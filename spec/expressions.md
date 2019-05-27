---
ms.openlocfilehash: 130898a8b5a7b8eb986b314cb4cf78038e840b02
ms.sourcegitcommit: 09e0ddec3bb6aa99b7340158bbac86a5a8243b43
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/24/2019
ms.locfileid: "66193938"
---
# <a name="expressions"></a><span data-ttu-id="3d917-101">Expressions</span><span class="sxs-lookup"><span data-stu-id="3d917-101">Expressions</span></span>

<span data-ttu-id="3d917-102">Une expression est une séquence d’opérateurs et d’opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-102">An expression is a sequence of operators and operands.</span></span> <span data-ttu-id="3d917-103">Ce chapitre définit la syntaxe, l’ordre d’évaluation des opérandes et opérateurs et la signification des expressions.</span><span class="sxs-lookup"><span data-stu-id="3d917-103">This chapter defines the syntax, order of evaluation of operands and operators, and meaning of expressions.</span></span>

## <a name="expression-classifications"></a><span data-ttu-id="3d917-104">Classifications des expressions</span><span class="sxs-lookup"><span data-stu-id="3d917-104">Expression classifications</span></span>

<span data-ttu-id="3d917-105">Une expression est classée comme l'un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="3d917-105">An expression is classified as one of the following:</span></span>

*  <span data-ttu-id="3d917-106">Valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-106">A value.</span></span> <span data-ttu-id="3d917-107">Chaque valeur possède un type associé.</span><span class="sxs-lookup"><span data-stu-id="3d917-107">Every value has an associated type.</span></span>
*  <span data-ttu-id="3d917-108">Une variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-108">A variable.</span></span> <span data-ttu-id="3d917-109">Chaque variable possède un type associé, à savoir le type déclaré de la variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-109">Every variable has an associated type, namely the declared type of the variable.</span></span>
*  <span data-ttu-id="3d917-110">Espace de noms.</span><span class="sxs-lookup"><span data-stu-id="3d917-110">A namespace.</span></span> <span data-ttu-id="3d917-111">Une expression dotée de cette classification peut apparaître uniquement comme la partie gauche d’un *member_access* ([l’accès au membre](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="3d917-111">An expression with this classification can only appear as the left hand side of a *member_access* ([Member access](expressions.md#member-access)).</span></span> <span data-ttu-id="3d917-112">Dans un autre contexte, une expression classifiée comme un espace de noms provoque une erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-112">In any other context, an expression classified as a namespace causes a compile-time error.</span></span>
*  <span data-ttu-id="3d917-113">Type.</span><span class="sxs-lookup"><span data-stu-id="3d917-113">A type.</span></span> <span data-ttu-id="3d917-114">Une expression dotée de cette classification peut apparaître uniquement comme la partie gauche d’un *member_access* ([l’accès au membre](expressions.md#member-access)), ou en tant qu’opérande pour le `as` opérateur ([l’opérateur as ](expressions.md#the-as-operator)), la `is` opérateur ([l’opérateur](expressions.md#the-is-operator)), ou le `typeof` opérateur ([l’opérateur typeof](expressions.md#the-typeof-operator)).</span><span class="sxs-lookup"><span data-stu-id="3d917-114">An expression with this classification can only appear as the left hand side of a *member_access* ([Member access](expressions.md#member-access)), or as an operand for the `as` operator ([The as operator](expressions.md#the-as-operator)), the `is` operator ([The is operator](expressions.md#the-is-operator)), or the `typeof` operator ([The typeof operator](expressions.md#the-typeof-operator)).</span></span> <span data-ttu-id="3d917-115">Dans un autre contexte, une expression classifiée comme un type entraîne une erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-115">In any other context, an expression classified as a type causes a compile-time error.</span></span>
*  <span data-ttu-id="3d917-116">Un groupe de méthodes, qui est un ensemble de méthodes surchargées résultant d’une recherche de membres ([recherche de membres](expressions.md#member-lookup)).</span><span class="sxs-lookup"><span data-stu-id="3d917-116">A method group, which is a set of overloaded methods resulting from a member lookup ([Member lookup](expressions.md#member-lookup)).</span></span> <span data-ttu-id="3d917-117">Un groupe de méthodes peut-être avoir une expression d’instance associée et une liste d’arguments de type associé.</span><span class="sxs-lookup"><span data-stu-id="3d917-117">A method group may have an associated instance expression and an associated type argument list.</span></span> <span data-ttu-id="3d917-118">Lorsqu’une méthode d’instance est appelée, le résultat de l’évaluation de l’expression d’instance devient l’instance représentée par `this` ([cet accès](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="3d917-118">When an instance method is invoked, the result of evaluating the instance expression becomes the instance represented by `this` ([This access](expressions.md#this-access)).</span></span> <span data-ttu-id="3d917-119">Un groupe de méthodes est autorisé dans une *invocation_expression* ([expressions d’appel](expressions.md#invocation-expressions)), un *delegate_creation_expression* ([déléguer la création expressions](expressions.md#delegate-creation-expressions)) et en tant que le côté gauche d’un opérateur et peut être implicitement converti en un type délégué compatible ([conversions de groupes de méthode](conversions.md#method-group-conversions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-119">A method group is permitted in an *invocation_expression* ([Invocation expressions](expressions.md#invocation-expressions)) , a *delegate_creation_expression* ([Delegate creation expressions](expressions.md#delegate-creation-expressions)) and as the left hand side of an is operator, and can be implicitly converted to a compatible delegate type ([Method group conversions](conversions.md#method-group-conversions)).</span></span> <span data-ttu-id="3d917-120">Dans un autre contexte, une expression classifiée comme un groupe de méthodes provoque une erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-120">In any other context, an expression classified as a method group causes a compile-time error.</span></span>
*  <span data-ttu-id="3d917-121">Un littéral null.</span><span class="sxs-lookup"><span data-stu-id="3d917-121">A null literal.</span></span> <span data-ttu-id="3d917-122">Une expression dotée de cette classification peut être implicitement convertie en un type référence ou un type nullable.</span><span class="sxs-lookup"><span data-stu-id="3d917-122">An expression with this classification can be implicitly converted to a reference type or nullable type.</span></span>
*  <span data-ttu-id="3d917-123">Une fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="3d917-123">An anonymous function.</span></span> <span data-ttu-id="3d917-124">Une expression dotée de cette classification peut être convertie implicitement à un type délégué compatible ou le type arborescence d’expression.</span><span class="sxs-lookup"><span data-stu-id="3d917-124">An expression with this classification can be implicitly converted to a compatible delegate type or expression tree type.</span></span>
*  <span data-ttu-id="3d917-125">Un accès à la propriété.</span><span class="sxs-lookup"><span data-stu-id="3d917-125">A property access.</span></span> <span data-ttu-id="3d917-126">Chaque accès à la propriété possède un type associé, à savoir le type de la propriété.</span><span class="sxs-lookup"><span data-stu-id="3d917-126">Every property access has an associated type, namely the type of the property.</span></span> <span data-ttu-id="3d917-127">En outre, un accès à la propriété peut avoir une expression d’instance associée.</span><span class="sxs-lookup"><span data-stu-id="3d917-127">Furthermore, a property access may have an associated instance expression.</span></span> <span data-ttu-id="3d917-128">Lorsqu’un accesseur (le `get` ou `set` bloc) d’une instance d’accès à la propriété est appelée, le résultat de l’évaluation de l’expression d’instance devient l’instance représentée par `this` ([cet accès](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="3d917-128">When an accessor (the `get` or `set` block) of an instance property access is invoked, the result of evaluating the instance expression becomes the instance represented by `this` ([This access](expressions.md#this-access)).</span></span>
*  <span data-ttu-id="3d917-129">Accès à un événement.</span><span class="sxs-lookup"><span data-stu-id="3d917-129">An event access.</span></span> <span data-ttu-id="3d917-130">Chaque accès à l’événement a un type associé, à savoir le type de l’événement.</span><span class="sxs-lookup"><span data-stu-id="3d917-130">Every event access has an associated type, namely the type of the event.</span></span> <span data-ttu-id="3d917-131">En outre, un accès à l’événement peut avoir une expression d’instance associée.</span><span class="sxs-lookup"><span data-stu-id="3d917-131">Furthermore, an event access may have an associated instance expression.</span></span> <span data-ttu-id="3d917-132">Accès à un événement peut apparaître en tant que l’opérande de gauche de la `+=` et `-=` opérateurs ([assignation d’événement](expressions.md#event-assignment)).</span><span class="sxs-lookup"><span data-stu-id="3d917-132">An event access may appear as the left hand operand of the `+=` and `-=` operators ([Event assignment](expressions.md#event-assignment)).</span></span> <span data-ttu-id="3d917-133">Dans un autre contexte, une expression classifiée comme un accès à l’événement provoque une erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-133">In any other context, an expression classified as an event access causes a compile-time error.</span></span>
*  <span data-ttu-id="3d917-134">Accès à un indexeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-134">An indexer access.</span></span> <span data-ttu-id="3d917-135">Chaque accès à l’indexeur a un type associé, à savoir le type d’élément de l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-135">Every indexer access has an associated type, namely the element type of the indexer.</span></span> <span data-ttu-id="3d917-136">En outre, un accès à l’indexeur a une expression d’instance associée et une liste d’arguments.</span><span class="sxs-lookup"><span data-stu-id="3d917-136">Furthermore, an indexer access has an associated instance expression and an associated argument list.</span></span> <span data-ttu-id="3d917-137">Lorsqu’un accesseur (le `get` ou `set` bloc) d’un indexeur accès est appelé, le résultat de l’évaluation de l’expression d’instance devient l’instance représentée par `this` ([cet accès](expressions.md#this-access)) et le résultat de l’évaluation de la liste d’arguments devient la liste des paramètres de l’appel.</span><span class="sxs-lookup"><span data-stu-id="3d917-137">When an accessor (the `get` or `set` block) of an indexer access is invoked, the result of evaluating the instance expression becomes the instance represented by `this` ([This access](expressions.md#this-access)), and the result of evaluating the argument list becomes the parameter list of the invocation.</span></span>
*  <span data-ttu-id="3d917-138">rien.</span><span class="sxs-lookup"><span data-stu-id="3d917-138">Nothing.</span></span> <span data-ttu-id="3d917-139">Cela se produit lorsque l’expression est un appel d’une méthode avec un type de retour `void`.</span><span class="sxs-lookup"><span data-stu-id="3d917-139">This occurs when the expression is an invocation of a method with a return type of `void`.</span></span> <span data-ttu-id="3d917-140">Une expression classifiée rien n’est valide dans le contexte d’un *statement_expression* ([instructions d’Expression](statements.md#expression-statements)).</span><span class="sxs-lookup"><span data-stu-id="3d917-140">An expression classified as nothing is only valid in the context of a *statement_expression* ([Expression statements](statements.md#expression-statements)).</span></span>

<span data-ttu-id="3d917-141">Le résultat final d’une expression n’est jamais un espace de noms, un type, un groupe de méthodes ou un accès à l’événement.</span><span class="sxs-lookup"><span data-stu-id="3d917-141">The final result of an expression is never a namespace, type, method group, or event access.</span></span> <span data-ttu-id="3d917-142">Au lieu de cela, comme indiqué ci-dessus, ces catégories d’expressions sont des constructions intermédiaires qui ne sont autorisées que dans certains contextes.</span><span class="sxs-lookup"><span data-stu-id="3d917-142">Rather, as noted above, these categories of expressions are intermediate constructs that are only permitted in certain contexts.</span></span>

<span data-ttu-id="3d917-143">Un accès à la propriété ou indexeur est toujours reclassifié comme une valeur en effectuant un appel de la *accesseur get* ou *accesseur set*.</span><span class="sxs-lookup"><span data-stu-id="3d917-143">A property access or indexer access is always reclassified as a value by performing an invocation of the *get accessor* or the *set accessor*.</span></span> <span data-ttu-id="3d917-144">L’accesseur particulier est déterminé par le contexte de l’accès de propriété ou l’indexeur : Si l’accès est la cible d’une assignation, le *accesseur set* est appelé pour assigner une nouvelle valeur ([assignation Simple](expressions.md#simple-assignment)).</span><span class="sxs-lookup"><span data-stu-id="3d917-144">The particular accessor is determined by the context of the property or indexer access: If the access is the target of an assignment, the *set accessor* is invoked to assign a new value ([Simple assignment](expressions.md#simple-assignment)).</span></span> <span data-ttu-id="3d917-145">Sinon, le *accesseur get* est appelé pour obtenir la valeur actuelle ([valeurs des expressions](expressions.md#values-of-expressions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-145">Otherwise, the *get accessor* is invoked to obtain the current value ([Values of expressions](expressions.md#values-of-expressions)).</span></span>

### <a name="values-of-expressions"></a><span data-ttu-id="3d917-146">Valeurs des expressions</span><span class="sxs-lookup"><span data-stu-id="3d917-146">Values of expressions</span></span>

<span data-ttu-id="3d917-147">La plupart des constructions qui impliquent une expression nécessitent finalement l’expression dénote un ***valeur***.</span><span class="sxs-lookup"><span data-stu-id="3d917-147">Most of the constructs that involve an expression ultimately require the expression to denote a ***value***.</span></span> <span data-ttu-id="3d917-148">Dans ce cas, si l’expression réelle dénote un espace de noms, un type, un groupe de méthodes ou rien, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-148">In such cases, if the actual expression denotes a namespace, a type, a method group, or nothing, a compile-time error occurs.</span></span> <span data-ttu-id="3d917-149">Toutefois, si l’expression dénote un accès à la propriété, accès à un indexeur ou une variable, la valeur de la propriété, un indexeur ou une variable est implicitement substituée :</span><span class="sxs-lookup"><span data-stu-id="3d917-149">However, if the expression denotes a property access, an indexer access, or a variable, the value of the property, indexer, or variable is implicitly substituted:</span></span>

*  <span data-ttu-id="3d917-150">La valeur d’une variable est simplement la valeur actuellement stockée dans l’emplacement de stockage identifié par la variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-150">The value of a variable is simply the value currently stored in the storage location identified by the variable.</span></span> <span data-ttu-id="3d917-151">Une variable doit être considérée comme assignée définitivement ([assignation définie](variables.md#definite-assignment)) avant que sa valeur peut être obtenue ou sinon une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-151">A variable must be considered definitely assigned ([Definite assignment](variables.md#definite-assignment)) before its value can be obtained, or otherwise a compile-time error occurs.</span></span>
*  <span data-ttu-id="3d917-152">La valeur d’une expression d’accès de propriété est obtenue en appelant le *accesseur get* de la propriété.</span><span class="sxs-lookup"><span data-stu-id="3d917-152">The value of a property access expression is obtained by invoking the *get accessor* of the property.</span></span> <span data-ttu-id="3d917-153">Si la propriété n’a pas *accesseur get*, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-153">If the property has no *get accessor*, a compile-time error occurs.</span></span> <span data-ttu-id="3d917-154">Sinon, un appel de fonction membre ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) est effectuée, et le résultat de l’appel devient la valeur de l’expression d’accès de propriété.</span><span class="sxs-lookup"><span data-stu-id="3d917-154">Otherwise, a function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) is performed, and the result of the invocation becomes the value of the property access expression.</span></span>
*  <span data-ttu-id="3d917-155">La valeur d’une expression d’accès indexeur est obtenue en appelant le *accesseur get* de l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-155">The value of an indexer access expression is obtained by invoking the *get accessor* of the indexer.</span></span> <span data-ttu-id="3d917-156">Si l’indexeur n’a pas *accesseur get*, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-156">If the indexer has no *get accessor*, a compile-time error occurs.</span></span> <span data-ttu-id="3d917-157">Sinon, un appel de fonction membre ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) est effectuée avec l’argument liste associé à l’expression d’accès indexeur et le résultat de l’appel devient la valeur de l’expression d’accès indexeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-157">Otherwise, a function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) is performed with the argument list associated with the indexer access expression, and the result of the invocation becomes the value of the indexer access expression.</span></span>

## <a name="static-and-dynamic-binding"></a><span data-ttu-id="3d917-158">Liaison statique et dynamique</span><span class="sxs-lookup"><span data-stu-id="3d917-158">Static and Dynamic Binding</span></span>

<span data-ttu-id="3d917-159">Le processus de détermination de la signification d’une opération basée sur le type de valeur d’expressions qui le composent (arguments, opérandes, récepteurs) est souvent appelé ***liaison***.</span><span class="sxs-lookup"><span data-stu-id="3d917-159">The process of determining the meaning of an operation based on the type or value of constituent expressions (arguments, operands, receivers) is often referred to as ***binding***.</span></span> <span data-ttu-id="3d917-160">Par exemple la signification d’un appel de méthode est déterminée en fonction du type de récepteur et d’arguments.</span><span class="sxs-lookup"><span data-stu-id="3d917-160">For instance the meaning of a method call is determined based on the type of the receiver and arguments.</span></span> <span data-ttu-id="3d917-161">La signification d’un opérateur est déterminée en fonction du type de ses opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-161">The meaning of an operator is determined based on the type of its operands.</span></span>

<span data-ttu-id="3d917-162">En C# la signification d’une opération est généralement déterminée au moment de la compilation, en fonction du type de compilation de ses expressions qui le composent.</span><span class="sxs-lookup"><span data-stu-id="3d917-162">In C# the meaning of an operation is usually determined at compile-time, based on the compile-time type of its constituent expressions.</span></span> <span data-ttu-id="3d917-163">De même, si une expression contient une erreur, l’erreur est détecté et signalé par le compilateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-163">Likewise, if an expression contains an error, the error is detected and reported by the compiler.</span></span> <span data-ttu-id="3d917-164">Cette approche est appelée ***liaison statique***.</span><span class="sxs-lookup"><span data-stu-id="3d917-164">This approach is known as ***static binding***.</span></span>

<span data-ttu-id="3d917-165">Toutefois, si une expression est une expression dynamique (par exemple, a le type `dynamic`) cela indique que toute liaison il participe doit être basée sur son type au moment de l’exécution (par exemple, le type réel de l’objet, il indique au moment de l’exécution) plutôt que le type sur moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-165">However, if an expression is a dynamic expression (i.e. has the type `dynamic`) this indicates that any binding that it participates in should be based on its run-time type (i.e. the actual type of the object it denotes at run-time) rather than the type it has at compile-time.</span></span> <span data-ttu-id="3d917-166">Par conséquent, la liaison d’une telle opération est différée jusqu’au moment où l’opération doit être exécutée pendant l’exécution du programme.</span><span class="sxs-lookup"><span data-stu-id="3d917-166">The binding of such an operation is therefore deferred until the time where the operation is to be executed during the running of the program.</span></span> <span data-ttu-id="3d917-167">Cela est appelé ***liaison dynamique***.</span><span class="sxs-lookup"><span data-stu-id="3d917-167">This is referred to as ***dynamic binding***.</span></span>

<span data-ttu-id="3d917-168">Lorsqu’une opération est liée dynamiquement, peu ou aucune vérification est effectuée par le compilateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-168">When an operation is dynamically bound, little or no checking is performed by the compiler.</span></span> <span data-ttu-id="3d917-169">Au lieu de cela si la liaison de l’exécution échoue, les erreurs sont signalées en tant qu’exceptions au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="3d917-169">Instead if the run-time binding fails, errors are reported as exceptions at run-time.</span></span>

<span data-ttu-id="3d917-170">Les opérations suivantes en c# sont soumises à liaison :</span><span class="sxs-lookup"><span data-stu-id="3d917-170">The following operations in C# are subject to binding:</span></span>

*  <span data-ttu-id="3d917-171">Accès au membre : `e.M`</span><span class="sxs-lookup"><span data-stu-id="3d917-171">Member access: `e.M`</span></span>
*  <span data-ttu-id="3d917-172">Appel de méthode : `e.M(e1, ..., eN)`</span><span class="sxs-lookup"><span data-stu-id="3d917-172">Method invocation: `e.M(e1, ..., eN)`</span></span>
*  <span data-ttu-id="3d917-173">Appel de délégué :`e(e1, ..., eN)`</span><span class="sxs-lookup"><span data-stu-id="3d917-173">Delegate invocation:`e(e1, ..., eN)`</span></span>
*  <span data-ttu-id="3d917-174">Accès à un élément : `e[e1, ..., eN]`</span><span class="sxs-lookup"><span data-stu-id="3d917-174">Element access: `e[e1, ..., eN]`</span></span>
*  <span data-ttu-id="3d917-175">Création d’objets : `new C(e1, ..., eN)`</span><span class="sxs-lookup"><span data-stu-id="3d917-175">Object creation: `new C(e1, ..., eN)`</span></span>
*  <span data-ttu-id="3d917-176">Surcharge des opérateurs unaires : `+`, `-`, `!`, `~`, `++`, `--`, `true`, `false`</span><span class="sxs-lookup"><span data-stu-id="3d917-176">Overloaded unary operators: `+`, `-`, `!`, `~`, `++`, `--`, `true`, `false`</span></span>
*  <span data-ttu-id="3d917-177">Les opérateurs binaires surchargés : `+`, `-`, `*`, `/`, `%`, `&`, `&&`, `|`, `||`, `??`, `^`, `<<` , `>>`, `==`,`!=`, `>`, `<`, `>=`, `<=`</span><span class="sxs-lookup"><span data-stu-id="3d917-177">Overloaded binary operators: `+`, `-`, `*`, `/`, `%`, `&`, `&&`, `|`, `||`, `??`, `^`, `<<`, `>>`, `==`,`!=`, `>`, `<`, `>=`, `<=`</span></span>
*  <span data-ttu-id="3d917-178">Opérateurs d’assignation : `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, `>>=`</span><span class="sxs-lookup"><span data-stu-id="3d917-178">Assignment operators: `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, `>>=`</span></span>
*  <span data-ttu-id="3d917-179">Conversions implicites et explicites</span><span class="sxs-lookup"><span data-stu-id="3d917-179">Implicit and explicit conversions</span></span>

<span data-ttu-id="3d917-180">Si aucune expression dynamique n’est impliquées, c# par défaut est une liaison statique, ce qui signifie que les types de compilation d’expressions constitutifs sont utilisés dans le processus de sélection.</span><span class="sxs-lookup"><span data-stu-id="3d917-180">When no dynamic expressions are involved, C# defaults to static binding, which means that the compile-time types of constituent expressions are used in the selection process.</span></span> <span data-ttu-id="3d917-181">Toutefois, lorsque l’une des expressions qui le composent dans les opérations répertoriées ci-dessus est une expression dynamique, l’opération est liée à la place dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="3d917-181">However, when one of the constituent expressions in the operations listed above is a dynamic expression, the operation is instead dynamically bound.</span></span>

### <a name="binding-time"></a><span data-ttu-id="3d917-182">Moment de la liaison</span><span class="sxs-lookup"><span data-stu-id="3d917-182">Binding-time</span></span>

<span data-ttu-id="3d917-183">Liaison statique intervient au moment de la compilation, tandis que la liaison dynamique a lieu au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="3d917-183">Static binding takes place at compile-time, whereas dynamic binding takes place at run-time.</span></span> <span data-ttu-id="3d917-184">Dans les sections suivantes, le terme ***moment de la liaison*** fait référence au moment de la compilation ou d’exécution, en fonction de laquelle la liaison a lieu.</span><span class="sxs-lookup"><span data-stu-id="3d917-184">In the following sections, the term ***binding-time*** refers to either compile-time or run-time, depending on when the binding takes place.</span></span>

<span data-ttu-id="3d917-185">L’exemple suivant illustre les notions de liaison statique et dynamique et du moment de la liaison :</span><span class="sxs-lookup"><span data-stu-id="3d917-185">The following example illustrates the notions of static and dynamic binding and of binding-time:</span></span>
```csharp
object  o = 5;
dynamic d = 5;

Console.WriteLine(5);  // static  binding to Console.WriteLine(int)
Console.WriteLine(o);  // static  binding to Console.WriteLine(object)
Console.WriteLine(d);  // dynamic binding to Console.WriteLine(int)
```

<span data-ttu-id="3d917-186">Les deux premiers appels sont liées statiquement : la surcharge de `Console.WriteLine` est choisi en fonction du type de compilation de son argument.</span><span class="sxs-lookup"><span data-stu-id="3d917-186">The first two calls are statically bound: the overload of `Console.WriteLine` is picked based on the compile-time type of their argument.</span></span> <span data-ttu-id="3d917-187">Par conséquent, le moment de la liaison est lors de la compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-187">Thus, the binding-time is compile-time.</span></span>

<span data-ttu-id="3d917-188">Le troisième appel est dynamiquement lié : la surcharge de `Console.WriteLine` est choisi en fonction du type d’exécution de son argument.</span><span class="sxs-lookup"><span data-stu-id="3d917-188">The third call is dynamically bound: the overload of `Console.WriteLine` is picked based on the run-time type of its argument.</span></span> <span data-ttu-id="3d917-189">Cela se produit, car l’argument est une expression dynamique--son type de compilation est `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-189">This happens because the argument is a dynamic expression -- its compile-time type is `dynamic`.</span></span> <span data-ttu-id="3d917-190">Par conséquent, le temps de liaison pour le troisième appel est exécution.</span><span class="sxs-lookup"><span data-stu-id="3d917-190">Thus, the binding-time for the third call is run-time.</span></span>

### <a name="dynamic-binding"></a><span data-ttu-id="3d917-191">Liaison dynamique</span><span class="sxs-lookup"><span data-stu-id="3d917-191">Dynamic binding</span></span>

<span data-ttu-id="3d917-192">L’objectif de la liaison dynamique consiste à autoriser des programmes c# interagir avec ***objets dynamiques***, par exemple, les objets qui ne suivent pas les règles normales de C# système de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-192">The purpose of dynamic binding is to allow C# programs to interact with ***dynamic objects***, i.e. objects that do not follow the normal rules of the C# type system.</span></span> <span data-ttu-id="3d917-193">Objets dynamiques peuvent être des objets à partir d’autres langages de programmation avec les systèmes de types différents, ou ils peuvent être des objets qui sont par programmation le programme d’installation pour implémenter leur propre sémantique de liaison pour différentes opérations.</span><span class="sxs-lookup"><span data-stu-id="3d917-193">Dynamic objects may be objects from other programming languages with different types systems, or they may be objects that are programmatically setup to implement their own binding semantics for different operations.</span></span>

<span data-ttu-id="3d917-194">Le mécanisme par lequel un objet dynamique implémente sa propre sémantique est définie par l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="3d917-194">The mechanism by which a dynamic object implements its own semantics is implementation defined.</span></span> <span data-ttu-id="3d917-195">Une interface donnée--à nouveau définie par l’implémentation--est implémentée par les objets dynamiques afin de signaler à la c# durée d’exécution qu’ils ont une sémantique spéciale.</span><span class="sxs-lookup"><span data-stu-id="3d917-195">A given interface -- again implementation defined -- is implemented by dynamic objects to signal to the C# run-time that they have special semantics.</span></span> <span data-ttu-id="3d917-196">Par conséquent, chaque fois que les opérations sur un objet dynamique sont liées dynamiquement, leur propre sémantique de liaison, plutôt que ceux de c#, comme spécifié dans ce document, reprendre.</span><span class="sxs-lookup"><span data-stu-id="3d917-196">Thus, whenever operations on a dynamic object are dynamically bound, their own binding semantics, rather than those of C# as specified in this document, take over.</span></span>

<span data-ttu-id="3d917-197">Alors que la liaison dynamique vise à permettre l’interopérabilité avec les objets dynamiques, c# permet de liaison dynamique sur tous les objets, qu’ils soient dynamiques ou non.</span><span class="sxs-lookup"><span data-stu-id="3d917-197">While the purpose of dynamic binding is to allow interoperation with dynamic objects, C# allows dynamic binding on all objects, whether they are dynamic or not.</span></span> <span data-ttu-id="3d917-198">Cela permet une intégration plus lisse des objets dynamiques, comme les résultats des opérations sur ces derniers pas eux-mêmes peuvent être des objets dynamiques, mais sont toujours d’un type inconnu pour le programmeur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-198">This allows for a smoother integration of dynamic objects, as the results of operations on them may not themselves be dynamic objects, but are still of a type unknown to the programmer at compile-time.</span></span> <span data-ttu-id="3d917-199">Liaison dynamique peut également aider à éliminer le code de basée sur reflection sujette aux erreurs même quand aucune objets impliqués ne sont des objets dynamiques.</span><span class="sxs-lookup"><span data-stu-id="3d917-199">Also dynamic binding can help eliminate error-prone reflection-based code even when no objects involved are dynamic objects.</span></span>

<span data-ttu-id="3d917-200">Les sections suivantes décrivent chaque construction dans le langage for exactement lorsque liaison dynamique est appliqué, ce qui compile la vérification au moment--si toute--est appliquée, et quelles la compilation résultat et expression classification est.</span><span class="sxs-lookup"><span data-stu-id="3d917-200">The following sections describe for each construct in the language exactly when dynamic binding is applied, what compile time checking -- if any -- is applied, and what the compile-time result and expression classification is.</span></span>

### <a name="types-of-constituent-expressions"></a><span data-ttu-id="3d917-201">Types d’expressions qui le composent</span><span class="sxs-lookup"><span data-stu-id="3d917-201">Types of constituent expressions</span></span>

<span data-ttu-id="3d917-202">Lorsqu’une opération est liée statiquement, le type d’une expression qui le composent (par exemple, un récepteur, un argument, un index ou un opérande) est toujours considéré comme le type de compilation de cette expression.</span><span class="sxs-lookup"><span data-stu-id="3d917-202">When an operation is statically bound, the type of a constituent expression (e.g. a receiver, an argument, an index or an operand) is always considered to be the compile-time type of that expression.</span></span>

<span data-ttu-id="3d917-203">Lorsqu’une opération est dynamiquement liée, le type d’une expression qui le composent est déterminé de différentes manières selon le type de compilation de l’expression qui le composent :</span><span class="sxs-lookup"><span data-stu-id="3d917-203">When an operation is dynamically bound, the type of a constituent expression is determined in different ways depending on the compile-time type of the constituent expression:</span></span>

*  <span data-ttu-id="3d917-204">Une expression de type au moment de la compilation de constitutif `dynamic` est considérée comme ayant le type de la valeur réelle que l’expression prend la valeur lors de l’exécution</span><span class="sxs-lookup"><span data-stu-id="3d917-204">A constituent expression of compile-time type `dynamic` is considered to have the type of the actual value that the expression evaluates to at runtime</span></span>
*  <span data-ttu-id="3d917-205">Une expression qui le composent, dont le type de compilation est un paramètre de type est considéré comme ayant le type auquel est lié le paramètre de type lors de l’exécution</span><span class="sxs-lookup"><span data-stu-id="3d917-205">A constituent expression whose compile-time type is a type parameter is considered to have the type which the type parameter is bound to at runtime</span></span>
*  <span data-ttu-id="3d917-206">Sinon l’expression qui le composent est considérée comme ayant le type de sa compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-206">Otherwise the constituent expression is considered to have its compile-time type.</span></span>

## <a name="operators"></a><span data-ttu-id="3d917-207">Opérateurs</span><span class="sxs-lookup"><span data-stu-id="3d917-207">Operators</span></span>

<span data-ttu-id="3d917-208">Les expressions sont construites à partir ***opérandes*** et ***opérateurs***.</span><span class="sxs-lookup"><span data-stu-id="3d917-208">Expressions are constructed from ***operands*** and ***operators***.</span></span> <span data-ttu-id="3d917-209">Les opérateurs d’une expression indiquent les opérations à appliquer aux opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-209">The operators of an expression indicate which operations to apply to the operands.</span></span> <span data-ttu-id="3d917-210">Parmi les exemples d’opérateurs figurent `+`, `-`, `*`, `/` et `new`.</span><span class="sxs-lookup"><span data-stu-id="3d917-210">Examples of operators include `+`, `-`, `*`, `/`, and `new`.</span></span> <span data-ttu-id="3d917-211">Les littéraux, les champs, les variables locales et les expressions sont des exemples d’opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-211">Examples of operands include literals, fields, local variables, and expressions.</span></span>

<span data-ttu-id="3d917-212">Il existe trois types d’opérateurs :</span><span class="sxs-lookup"><span data-stu-id="3d917-212">There are three kinds of operators:</span></span>

*  <span data-ttu-id="3d917-213">Les opérateurs unaires.</span><span class="sxs-lookup"><span data-stu-id="3d917-213">Unary operators.</span></span> <span data-ttu-id="3d917-214">Les opérateurs unaires prennent un opérande et utiliser une notation de préfixe (tel que `--x`) ou un suffixe de notation (tel que `x++`).</span><span class="sxs-lookup"><span data-stu-id="3d917-214">The unary operators take one operand and use either prefix notation (such as `--x`) or postfix notation (such as `x++`).</span></span>
*  <span data-ttu-id="3d917-215">Opérateurs binaires.</span><span class="sxs-lookup"><span data-stu-id="3d917-215">Binary operators.</span></span> <span data-ttu-id="3d917-216">Les opérateurs binaires prennent deux opérandes et utilisent toutes la notation infixe (tel que `x + y`).</span><span class="sxs-lookup"><span data-stu-id="3d917-216">The binary operators take two operands and all use infix notation (such as `x + y`).</span></span>
*  <span data-ttu-id="3d917-217">opérateur ternaire.</span><span class="sxs-lookup"><span data-stu-id="3d917-217">Ternary operator.</span></span> <span data-ttu-id="3d917-218">Qu’un seul opérateur ternaire, `?:`, existe ; il prend trois opérandes et emploie la notation infix (`c ? x : y`).</span><span class="sxs-lookup"><span data-stu-id="3d917-218">Only one ternary operator, `?:`, exists; it takes three operands and uses infix notation (`c ? x : y`).</span></span>

<span data-ttu-id="3d917-219">L’ordre d’évaluation des opérateurs dans une expression est déterminé par le ***priorité*** et ***associativité*** des opérateurs ([priorité et associativité](expressions.md#operator-precedence-and-associativity)) .</span><span class="sxs-lookup"><span data-stu-id="3d917-219">The order of evaluation of operators in an expression is determined by the ***precedence*** and ***associativity*** of the operators ([Operator precedence and associativity](expressions.md#operator-precedence-and-associativity)).</span></span>

<span data-ttu-id="3d917-220">Les opérandes dans une expression sont évalués de gauche à droite.</span><span class="sxs-lookup"><span data-stu-id="3d917-220">Operands in an expression are evaluated from left to right.</span></span> <span data-ttu-id="3d917-221">Par exemple, dans `F(i) + G(i++) * H(i)`, méthode `F` est appelée à l’aide de l’ancienne valeur de `i`, méthode then `G` est appelée avec l’ancienne valeur de `i`et, enfin, méthode `H` est appelée avec la nouvelle valeur de `i`.</span><span class="sxs-lookup"><span data-stu-id="3d917-221">For example, in `F(i) + G(i++) * H(i)`, method `F` is called using the old value of `i`, then method `G` is called with the old value of `i`, and, finally, method `H` is called with the new value of `i`.</span></span> <span data-ttu-id="3d917-222">Cela est distincte et non liées à la priorité des opérateurs.</span><span class="sxs-lookup"><span data-stu-id="3d917-222">This is separate from and unrelated to operator precedence.</span></span>

<span data-ttu-id="3d917-223">Certains opérateurs peuvent être ***surchargé***.</span><span class="sxs-lookup"><span data-stu-id="3d917-223">Certain operators can be ***overloaded***.</span></span> <span data-ttu-id="3d917-224">Surcharge d’opérateur autorise des implémentations de l’opérateur défini par l’utilisateur à être spécifié pour les opérations où un ou les deux opérandes sont d’un type classe ou struct défini par l’utilisateur ([la surcharge d’opérateur](expressions.md#operator-overloading)).</span><span class="sxs-lookup"><span data-stu-id="3d917-224">Operator overloading permits user-defined operator implementations to be specified for operations where one or both of the operands are of a user-defined class or struct type ([Operator overloading](expressions.md#operator-overloading)).</span></span>

### <a name="operator-precedence-and-associativity"></a><span data-ttu-id="3d917-225">Priorité des opérateurs et associativité</span><span class="sxs-lookup"><span data-stu-id="3d917-225">Operator precedence and associativity</span></span>

<span data-ttu-id="3d917-226">Quand une expression contient plusieurs opérateurs, la ***priorité*** des opérateurs contrôle l'ordre dans lequel les opérateurs individuels sont évalués.</span><span class="sxs-lookup"><span data-stu-id="3d917-226">When an expression contains multiple operators, the ***precedence*** of the operators controls the order in which the individual operators are evaluated.</span></span> <span data-ttu-id="3d917-227">Par exemple, l’expression `x + y * z` est évalué comme `x + (y * z)` , car le `*` opérateur a une priorité plus élevée que le fichier binaire `+` opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-227">For example, the expression `x + y * z` is evaluated as `x + (y * z)` because the `*` operator has higher precedence than the binary `+` operator.</span></span> <span data-ttu-id="3d917-228">La priorité d’un opérateur est établie par la définition de sa production grammaticale associée.</span><span class="sxs-lookup"><span data-stu-id="3d917-228">The precedence of an operator is established by the definition of its associated grammar production.</span></span> <span data-ttu-id="3d917-229">Par exemple, un *additive_expression* se compose d’une séquence de *multiplicative_expression*s séparés par `+` ou `-` opérateurs, donnant ainsi la `+` et `-` opérateurs diminuer la priorité que la `*`, `/`, et `%` opérateurs.</span><span class="sxs-lookup"><span data-stu-id="3d917-229">For example, an *additive_expression* consists of a sequence of *multiplicative_expression*s separated by `+` or `-` operators, thus giving the `+` and `-` operators lower precedence than the `*`, `/`, and `%` operators.</span></span>

<span data-ttu-id="3d917-230">Le tableau suivant récapitule tous les opérateurs dans l’ordre de priorité, du plus élevé au plus bas :</span><span class="sxs-lookup"><span data-stu-id="3d917-230">The following table summarizes all operators in order of precedence from highest to lowest:</span></span>

| <span data-ttu-id="3d917-231">__Section__</span><span class="sxs-lookup"><span data-stu-id="3d917-231">__Section__</span></span>                                                                                   | <span data-ttu-id="3d917-232">__Catégorie__</span><span class="sxs-lookup"><span data-stu-id="3d917-232">__Category__</span></span>                | <span data-ttu-id="3d917-233">__Opérateurs__</span><span class="sxs-lookup"><span data-stu-id="3d917-233">__Operators__</span></span> | 
|-----------------------------------------------------------------------------------------------|-----------------------------|---------------|
| [<span data-ttu-id="3d917-234">Expressions primaires</span><span class="sxs-lookup"><span data-stu-id="3d917-234">Primary expressions</span></span>](expressions.md#primary-expressions)                                     | <span data-ttu-id="3d917-235">Principale</span><span class="sxs-lookup"><span data-stu-id="3d917-235">Primary</span></span>                     | <span data-ttu-id="3d917-236">`x.y`  `f(x)`  `a[x]`  `x++`  `x--`  `new`  `typeof`  `default`  `checked`  `unchecked`  `delegate`</span><span class="sxs-lookup"><span data-stu-id="3d917-236">`x.y`  `f(x)`  `a[x]`  `x++`  `x--`  `new`  `typeof`  `default`  `checked`  `unchecked`  `delegate`</span></span> | 
| [<span data-ttu-id="3d917-237">Opérateurs unaires</span><span class="sxs-lookup"><span data-stu-id="3d917-237">Unary operators</span></span>](expressions.md#unary-operators)                                             | <span data-ttu-id="3d917-238">Unaire</span><span class="sxs-lookup"><span data-stu-id="3d917-238">Unary</span></span>                       | <span data-ttu-id="3d917-239">`+`  `-`  `!`  `~`  `++x`  `--x`  `(T)x`</span><span class="sxs-lookup"><span data-stu-id="3d917-239">`+`  `-`  `!`  `~`  `++x`  `--x`  `(T)x`</span></span> | 
| [<span data-ttu-id="3d917-240">Opérateurs arithmétiques</span><span class="sxs-lookup"><span data-stu-id="3d917-240">Arithmetic operators</span></span>](expressions.md#arithmetic-operators)                                   | <span data-ttu-id="3d917-241">Multiplication</span><span class="sxs-lookup"><span data-stu-id="3d917-241">Multiplicative</span></span>              | <span data-ttu-id="3d917-242">`*`  `/`  `%`</span><span class="sxs-lookup"><span data-stu-id="3d917-242">`*`  `/`  `%`</span></span> | 
| [<span data-ttu-id="3d917-243">Opérateurs arithmétiques</span><span class="sxs-lookup"><span data-stu-id="3d917-243">Arithmetic operators</span></span>](expressions.md#arithmetic-operators)                                   | <span data-ttu-id="3d917-244">Addition</span><span class="sxs-lookup"><span data-stu-id="3d917-244">Additive</span></span>                    | <span data-ttu-id="3d917-245">`+`  `-`</span><span class="sxs-lookup"><span data-stu-id="3d917-245">`+`  `-`</span></span>      | 
| [<span data-ttu-id="3d917-246">Opérateurs de décalage</span><span class="sxs-lookup"><span data-stu-id="3d917-246">Shift operators</span></span>](expressions.md#shift-operators)                                             | <span data-ttu-id="3d917-247">Shift</span><span class="sxs-lookup"><span data-stu-id="3d917-247">Shift</span></span>                       | <span data-ttu-id="3d917-248">`<<`  `>>`</span><span class="sxs-lookup"><span data-stu-id="3d917-248">`<<`  `>>`</span></span>    | 
| [<span data-ttu-id="3d917-249">Opérateurs relationnels et de test de type</span><span class="sxs-lookup"><span data-stu-id="3d917-249">Relational and type-testing operators</span></span>](expressions.md#relational-and-type-testing-operators) | <span data-ttu-id="3d917-250">Relations et test de type</span><span class="sxs-lookup"><span data-stu-id="3d917-250">Relational and type testing</span></span> | <span data-ttu-id="3d917-251">`<`  `>`  `<=`  `>=`  `is`  `as`</span><span class="sxs-lookup"><span data-stu-id="3d917-251">`<`  `>`  `<=`  `>=`  `is`  `as`</span></span> | 
| [<span data-ttu-id="3d917-252">Opérateurs relationnels et de test de type</span><span class="sxs-lookup"><span data-stu-id="3d917-252">Relational and type-testing operators</span></span>](expressions.md#relational-and-type-testing-operators) | <span data-ttu-id="3d917-253">Égalité</span><span class="sxs-lookup"><span data-stu-id="3d917-253">Equality</span></span>                    | <span data-ttu-id="3d917-254">`==`  `!=`</span><span class="sxs-lookup"><span data-stu-id="3d917-254">`==`  `!=`</span></span>    | 
| [<span data-ttu-id="3d917-255">Opérateurs logiques</span><span class="sxs-lookup"><span data-stu-id="3d917-255">Logical operators</span></span>](expressions.md#logical-operators)                                         | <span data-ttu-id="3d917-256">AND logique</span><span class="sxs-lookup"><span data-stu-id="3d917-256">Logical AND</span></span>                 | `&`           | 
| [<span data-ttu-id="3d917-257">Opérateurs logiques</span><span class="sxs-lookup"><span data-stu-id="3d917-257">Logical operators</span></span>](expressions.md#logical-operators)                                         | <span data-ttu-id="3d917-258">XOR logique</span><span class="sxs-lookup"><span data-stu-id="3d917-258">Logical XOR</span></span>                 | `^`           | 
| [<span data-ttu-id="3d917-259">Opérateurs logiques</span><span class="sxs-lookup"><span data-stu-id="3d917-259">Logical operators</span></span>](expressions.md#logical-operators)                                         | <span data-ttu-id="3d917-260">OR logique</span><span class="sxs-lookup"><span data-stu-id="3d917-260">Logical OR</span></span>                  | <code>&#124;</code>           |
| [<span data-ttu-id="3d917-261">Opérateurs logiques conditionnels</span><span class="sxs-lookup"><span data-stu-id="3d917-261">Conditional logical operators</span></span>](expressions.md#conditional-logical-operators)                 | <span data-ttu-id="3d917-262">AND conditionnel</span><span class="sxs-lookup"><span data-stu-id="3d917-262">Conditional AND</span></span>             | `&&`          | 
| [<span data-ttu-id="3d917-263">Opérateurs logiques conditionnels</span><span class="sxs-lookup"><span data-stu-id="3d917-263">Conditional logical operators</span></span>](expressions.md#conditional-logical-operators)                 | <span data-ttu-id="3d917-264">OR conditionnel</span><span class="sxs-lookup"><span data-stu-id="3d917-264">Conditional OR</span></span>              | <code>&#124;&#124;</code>          | 
| [<span data-ttu-id="3d917-265">Opérateur de fusion de Null</span><span class="sxs-lookup"><span data-stu-id="3d917-265">The null coalescing operator</span></span>](expressions.md#the-null-coalescing-operator)                   | <span data-ttu-id="3d917-266">Fusion de Null</span><span class="sxs-lookup"><span data-stu-id="3d917-266">Null coalescing</span></span>             | `??`          | 
| [<span data-ttu-id="3d917-267">Opérateur conditionnel</span><span class="sxs-lookup"><span data-stu-id="3d917-267">Conditional operator</span></span>](expressions.md#conditional-operator)                                   | <span data-ttu-id="3d917-268">Conditionnel</span><span class="sxs-lookup"><span data-stu-id="3d917-268">Conditional</span></span>                 | `?:`          | 
| <span data-ttu-id="3d917-269">[Opérateurs d’assignation](expressions.md#assignment-operators), [des expressions de fonction anonyme](expressions.md#anonymous-function-expressions)</span><span class="sxs-lookup"><span data-stu-id="3d917-269">[Assignment operators](expressions.md#assignment-operators), [Anonymous function expressions](expressions.md#anonymous-function-expressions)</span></span>  | <span data-ttu-id="3d917-270">Assignation et expression lambda</span><span class="sxs-lookup"><span data-stu-id="3d917-270">Assignment and lambda expression</span></span> | <span data-ttu-id="3d917-271">`=`  `*=`  `/=`  `%=`  `+=`  `-=`  `<<=`  `>>=`  `&=`  `^=`  <code>&#124;=</code>  `=>`</span><span class="sxs-lookup"><span data-stu-id="3d917-271">`=`  `*=`  `/=`  `%=`  `+=`  `-=`  `<<=`  `>>=`  `&=`  `^=`  <code>&#124;=</code>  `=>`</span></span> | 

<span data-ttu-id="3d917-272">Lorsqu’un opérande se produit entre deux opérateurs de même priorité, l’associativité des opérateurs détermine l’ordre dans lequel les opérations sont effectuées :</span><span class="sxs-lookup"><span data-stu-id="3d917-272">When an operand occurs between two operators with the same precedence, the associativity of the operators controls the order in which the operations are performed:</span></span>

*  <span data-ttu-id="3d917-273">Sauf pour les opérateurs d’assignation et l’opérateur de fusion null, tous les opérateurs binaires sont ***associatifs sur leur gauche***, ce qui signifie que les opérations sont effectuées de gauche à droite.</span><span class="sxs-lookup"><span data-stu-id="3d917-273">Except for the assignment operators and the null coalescing operator, all binary operators are ***left-associative***, meaning that operations are performed from left to right.</span></span> <span data-ttu-id="3d917-274">Par exemple, `x + y + z` est évalué comme étant `(x + y) + z`.</span><span class="sxs-lookup"><span data-stu-id="3d917-274">For example, `x + y + z` is evaluated as `(x + y) + z`.</span></span>
*  <span data-ttu-id="3d917-275">Les opérateurs d’assignation, l’opérateur de fusion null et l’opérateur conditionnel (`?:`) sont ***associatif à droite***, ce qui signifie que les opérations sont effectuées de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-275">The assignment operators, the null coalescing operator and the conditional operator (`?:`) are ***right-associative***, meaning that operations are performed from right to left.</span></span> <span data-ttu-id="3d917-276">Par exemple, `x = y = z` est évalué comme étant `x = (y = z)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-276">For example, `x = y = z` is evaluated as `x = (y = z)`.</span></span>

<span data-ttu-id="3d917-277">La priorité et l’associativité peuvent être contrôlées à l’aide de parenthèses.</span><span class="sxs-lookup"><span data-stu-id="3d917-277">Precedence and associativity can be controlled using parentheses.</span></span> <span data-ttu-id="3d917-278">Par exemple, `x + y * z` multiplie d’abord `y` par `z`, puis ajoute le résultat à `x`, mais `(x + y) * z` ajoute d’abord `x` et `y`, puis multiplie le résultat par `z`.</span><span class="sxs-lookup"><span data-stu-id="3d917-278">For example, `x + y * z` first multiplies `y` by `z` and then adds the result to `x`, but `(x + y) * z` first adds `x` and `y` and then multiplies the result by `z`.</span></span>

### <a name="operator-overloading"></a><span data-ttu-id="3d917-279">Surcharge d’opérateur</span><span class="sxs-lookup"><span data-stu-id="3d917-279">Operator overloading</span></span>

<span data-ttu-id="3d917-280">Tous les opérateurs unaires et binaires ont des implémentations prédéfinies qui sont automatiquement disponibles dans n’importe quelle expression.</span><span class="sxs-lookup"><span data-stu-id="3d917-280">All unary and binary operators have predefined implementations that are automatically available in any expression.</span></span> <span data-ttu-id="3d917-281">En plus des implémentations prédéfinies, les implémentations définies par l’utilisateur peuvent être introduites en incluant `operator` déclarations dans les classes et structs ([opérateurs](classes.md#operators)).</span><span class="sxs-lookup"><span data-stu-id="3d917-281">In addition to the predefined implementations, user-defined implementations can be introduced by including `operator` declarations in classes and structs ([Operators](classes.md#operators)).</span></span> <span data-ttu-id="3d917-282">Implémentations d’opérateurs définis par l’utilisateur ont toujours priorité sur les implémentations d’opérateurs prédéfinis : Uniquement lorsqu’il n’existe aucune implémentation d’opérateur définie par l’utilisateur applicable implémentations considérée, comme décrit dans [opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution) et [surcharge d’opérateur binaire résolution](expressions.md#binary-operator-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="3d917-282">User-defined operator implementations always take precedence over predefined operator implementations: Only when no applicable user-defined operator implementations exist will the predefined operator implementations be considered, as described in [Unary operator overload resolution](expressions.md#unary-operator-overload-resolution) and [Binary operator overload resolution](expressions.md#binary-operator-overload-resolution).</span></span>

<span data-ttu-id="3d917-283">Le ***opérateurs surchargeables unaires*** sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-283">The ***overloadable unary operators*** are:</span></span>
```csharp
+   -   !   ~   ++   --   true   false
```

<span data-ttu-id="3d917-284">Bien que `true` et `false` ne sont pas explicitement utilisés dans les expressions (et par conséquent ne sont pas inclus dans le tableau de priorité dans [priorité et associativité](expressions.md#operator-precedence-and-associativity)), ils sont considérés comme les opérateurs, car ils sont appelé dans plusieurs contextes d’expression : expressions booléennes ([expressions booléennes](expressions.md#boolean-expressions)) et les expressions impliquant l’instruction conditionnelle ([opérateur conditionnel](expressions.md#conditional-operator)) et conditionnel logique opérateurs ([opérateurs logiques conditionnels](expressions.md#conditional-logical-operators)).</span><span class="sxs-lookup"><span data-stu-id="3d917-284">Although `true` and `false` are not used explicitly in expressions (and therefore are not included in the precedence table in [Operator precedence and associativity](expressions.md#operator-precedence-and-associativity)), they are considered operators because they are invoked in several expression contexts: boolean expressions ([Boolean expressions](expressions.md#boolean-expressions)) and expressions involving the conditional ([Conditional operator](expressions.md#conditional-operator)), and conditional logical operators ([Conditional logical operators](expressions.md#conditional-logical-operators)).</span></span>

<span data-ttu-id="3d917-285">Le ***surchargeables opérateurs binaires*** sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-285">The ***overloadable binary operators*** are:</span></span>
```csharp
+   -   *   /   %   &   |   ^   <<   >>   ==   !=   >   <   >=   <=
```

<span data-ttu-id="3d917-286">Seuls les opérateurs répertoriés ci-dessus peuvent être surchargés.</span><span class="sxs-lookup"><span data-stu-id="3d917-286">Only the operators listed above can be overloaded.</span></span> <span data-ttu-id="3d917-287">En particulier, il n’est pas possible de surcharger l’accès au membre, l’appel de méthode, ou le `=`, `&&`, `||`, `??`, `?:`, `=>`, `checked`, `unchecked`, `new`, `typeof`, `default`, `as`, et `is` opérateurs.</span><span class="sxs-lookup"><span data-stu-id="3d917-287">In particular, it is not possible to overload member access, method invocation, or the `=`, `&&`, `||`, `??`, `?:`, `=>`, `checked`, `unchecked`, `new`, `typeof`, `default`, `as`, and `is` operators.</span></span>

<span data-ttu-id="3d917-288">Quand un opérateur binaire est surchargé, l’opérateur d’assignation correspondant, le cas échéant, est aussi implicitement surchargé.</span><span class="sxs-lookup"><span data-stu-id="3d917-288">When a binary operator is overloaded, the corresponding assignment operator, if any, is also implicitly overloaded.</span></span> <span data-ttu-id="3d917-289">Par exemple, une surcharge d’opérateur `*` est également une surcharge d’opérateur `*=`.</span><span class="sxs-lookup"><span data-stu-id="3d917-289">For example, an overload of operator `*` is also an overload of operator `*=`.</span></span> <span data-ttu-id="3d917-290">Cela est décrite plus loin dans [assignation composée](expressions.md#compound-assignment).</span><span class="sxs-lookup"><span data-stu-id="3d917-290">This is described further in [Compound assignment](expressions.md#compound-assignment).</span></span> <span data-ttu-id="3d917-291">Notez que l’opérateur d’assignation (`=`) ne peut pas être surchargé.</span><span class="sxs-lookup"><span data-stu-id="3d917-291">Note that the assignment operator itself (`=`) cannot be overloaded.</span></span> <span data-ttu-id="3d917-292">Une assignation effectue toujours une copie bit par bit simple d’une valeur dans une variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-292">An assignment always performs a simple bit-wise copy of a value into a variable.</span></span>

<span data-ttu-id="3d917-293">Effectuer un cast des opérations, telles que `(T)x`, sont surchargées en fournissant des conversions définies par l’utilisateur ([conversions définies par l’utilisateur](conversions.md#user-defined-conversions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-293">Cast operations, such as `(T)x`, are overloaded by providing user-defined conversions ([User-defined conversions](conversions.md#user-defined-conversions)).</span></span>

<span data-ttu-id="3d917-294">Élément d’accès, tel que `a[x]`, n’est pas considéré comme un opérateur surchargeable.</span><span class="sxs-lookup"><span data-stu-id="3d917-294">Element access, such as `a[x]`, is not considered an overloadable operator.</span></span> <span data-ttu-id="3d917-295">Au lieu de cela, défini par l’utilisateur de l’indexation est prise en charge via des indexeurs ([indexeurs](classes.md#indexers)).</span><span class="sxs-lookup"><span data-stu-id="3d917-295">Instead, user-defined indexing is supported through indexers ([Indexers](classes.md#indexers)).</span></span>

<span data-ttu-id="3d917-296">Dans les expressions, opérateurs sont référencées à l’aide de la notation de l’opérateur et dans les déclarations, les opérateurs sont référencées à l’aide de la notation fonctionnelle.</span><span class="sxs-lookup"><span data-stu-id="3d917-296">In expressions, operators are referenced using operator notation, and in declarations, operators are referenced using functional notation.</span></span> <span data-ttu-id="3d917-297">Le tableau suivant montre la relation entre l’opérateur et les notations fonctionnelles pour les opérateurs unaires et binaires.</span><span class="sxs-lookup"><span data-stu-id="3d917-297">The following table shows the relationship between operator and functional notations for unary and binary operators.</span></span> <span data-ttu-id="3d917-298">Dans la première entrée, *op* désigne n’importe quel opérateur de préfixe unaire surchargeable.</span><span class="sxs-lookup"><span data-stu-id="3d917-298">In the first entry, *op* denotes any overloadable unary prefix operator.</span></span> <span data-ttu-id="3d917-299">Dans la deuxième entrée, *op* désigne le suffixe unaire `++` et `--` opérateurs.</span><span class="sxs-lookup"><span data-stu-id="3d917-299">In the second entry, *op* denotes the unary postfix `++` and `--` operators.</span></span> <span data-ttu-id="3d917-300">Dans la troisième entrée *op* désigne tout opérateur binaire surchargeable.</span><span class="sxs-lookup"><span data-stu-id="3d917-300">In the third entry, *op* denotes any overloadable binary operator.</span></span>


| <span data-ttu-id="3d917-301">__Notation de l’opérateur__</span><span class="sxs-lookup"><span data-stu-id="3d917-301">__Operator notation__</span></span> | <span data-ttu-id="3d917-302">__Notation fonctionnelle__</span><span class="sxs-lookup"><span data-stu-id="3d917-302">__Functional notation__</span></span> |
|-----------------------|-------------------------|
| `op x`                | `operator op(x)`        | 
| `x op`                | `operator op(x)`        | 
| `x op y`              | `operator op(x,y)`      | 

<span data-ttu-id="3d917-303">Déclarations d’opérateur défini par l’utilisateur nécessitent toujours au moins un des paramètres de type classe ou struct qui contient la déclaration d’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-303">User-defined operator declarations always require at least one of the parameters to be of the class or struct type that contains the operator declaration.</span></span> <span data-ttu-id="3d917-304">Par conséquent, il n’est pas possible pour un opérateur défini par l’utilisateur d’avoir la même signature qu’un opérateur prédéfini.</span><span class="sxs-lookup"><span data-stu-id="3d917-304">Thus, it is not possible for a user-defined operator to have the same signature as a predefined operator.</span></span>

<span data-ttu-id="3d917-305">Déclarations d’opérateur défini par l’utilisateur ne peut pas modifier la syntaxe, priorité ou associativité d’un opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-305">User-defined operator declarations cannot modify the syntax, precedence, or associativity of an operator.</span></span> <span data-ttu-id="3d917-306">Par exemple, le `/` opérateur est toujours un opérateur binaire, a toujours le niveau de priorité spécifié dans [priorité et associativité](expressions.md#operator-precedence-and-associativity)et est toujours associatifs sur leur gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-306">For example, the `/` operator is always a binary operator, always has the precedence level specified in [Operator precedence and associativity](expressions.md#operator-precedence-and-associativity), and is always left-associative.</span></span>

<span data-ttu-id="3d917-307">S’il est possible pour un opérateur défini par l’utilisateur effectuer des calculs qu’il souhaite, les implémentations qui produisent des résultats autres que ceux qui sont intuitivement attendus sont fortement déconseillées.</span><span class="sxs-lookup"><span data-stu-id="3d917-307">While it is possible for a user-defined operator to perform any computation it pleases, implementations that produce results other than those that are intuitively expected are strongly discouraged.</span></span> <span data-ttu-id="3d917-308">Par exemple, une implémentation de `operator ==` doit comparer les deux opérandes sont égaux et renvoyer un approprié `bool` résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-308">For example, an implementation of `operator ==` should compare the two operands for equality and return an appropriate `bool` result.</span></span>

<span data-ttu-id="3d917-309">Les descriptions de chacun des opérateurs dans [expressions primaires](expressions.md#primary-expressions) via [opérateurs logiques conditionnels](expressions.md#conditional-logical-operators) spécifient les implémentations prédéfinies des opérateurs et des règles supplémentaires qui s’appliquent à chaque opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-309">The descriptions of individual operators in [Primary expressions](expressions.md#primary-expressions) through [Conditional logical operators](expressions.md#conditional-logical-operators) specify the predefined implementations of the operators and any additional rules that apply to each operator.</span></span> <span data-ttu-id="3d917-310">Les descriptions utiliser des termes du contrat ***opérateur unaire de résolution de surcharge***, ***opérateur binaire de résolution de surcharge***, et ***promotion numérique***, dont les définitions trouvé dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="3d917-310">The descriptions make use of the terms ***unary operator overload resolution***, ***binary operator overload resolution***, and ***numeric promotion***, definitions of which are found in the following sections.</span></span>

### <a name="unary-operator-overload-resolution"></a><span data-ttu-id="3d917-311">Résolution de surcharge d’opérateur unaire</span><span class="sxs-lookup"><span data-stu-id="3d917-311">Unary operator overload resolution</span></span>

<span data-ttu-id="3d917-312">Une opération de la forme `op x` ou `x op`, où `op` est un opérateur unaire surchargeable, et `x` est une expression de type `X`, est traitée comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-312">An operation of the form `op x` or `x op`, where `op` is an overloadable unary operator, and `x` is an expression of type `X`, is processed as follows:</span></span>

*  <span data-ttu-id="3d917-313">L’ensemble des opérateurs de défini par l’utilisateur candidats fourni par `X` pour l’opération `operator op(x)` est déterminée en utilisant les règles de [opérateurs définis par l’utilisateur candidats](expressions.md#candidate-user-defined-operators).</span><span class="sxs-lookup"><span data-stu-id="3d917-313">The set of candidate user-defined operators provided by `X` for the operation `operator op(x)` is determined using the rules of [Candidate user-defined operators](expressions.md#candidate-user-defined-operators).</span></span>
*  <span data-ttu-id="3d917-314">Si l’ensemble des opérateurs définis par l’utilisateur de candidat n’est pas vide, il devient alors l’ensemble des opérateurs candidats pour l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-314">If the set of candidate user-defined operators is not empty, then this becomes the set of candidate operators for the operation.</span></span> <span data-ttu-id="3d917-315">Sinon, l’unaire prédéfinie `operator op` implémentations, y compris leurs formes levés, devient l’ensemble des opérateurs candidats pour l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-315">Otherwise, the predefined unary `operator op` implementations, including their lifted forms, become the set of candidate operators for the operation.</span></span> <span data-ttu-id="3d917-316">Les implémentations prédéfinies d’un opérateur donné sont spécifiées dans la description de l’opérateur ([expressions primaires](expressions.md#primary-expressions) et [opérateurs unaires](expressions.md#unary-operators)).</span><span class="sxs-lookup"><span data-stu-id="3d917-316">The predefined implementations of a given operator are specified in the description of the operator ([Primary expressions](expressions.md#primary-expressions) and [Unary operators](expressions.md#unary-operators)).</span></span>
*  <span data-ttu-id="3d917-317">Les règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution) sont appliquées à l’ensemble des opérateurs candidats pour sélectionner le meilleur opérateur en ce qui concerne la liste d’arguments `(x)`, et cet opérateur devient le résultat de la surcharge processus de résolution.</span><span class="sxs-lookup"><span data-stu-id="3d917-317">The overload resolution rules of [Overload resolution](expressions.md#overload-resolution) are applied to the set of candidate operators to select the best operator with respect to the argument list `(x)`, and this operator becomes the result of the overload resolution process.</span></span> <span data-ttu-id="3d917-318">Si la résolution de surcharge ne parvient pas à sélectionner un meilleur opérateur unique, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-318">If overload resolution fails to select a single best operator, a binding-time error occurs.</span></span>

### <a name="binary-operator-overload-resolution"></a><span data-ttu-id="3d917-319">Résolution de surcharge d’opérateur binaire</span><span class="sxs-lookup"><span data-stu-id="3d917-319">Binary operator overload resolution</span></span>

<span data-ttu-id="3d917-320">Une opération de la forme `x op y`, où `op` est un opérateur binaire surchargeable, `x` est une expression de type `X`, et `y` est une expression de type `Y`, est traitée comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-320">An operation of the form `x op y`, where `op` is an overloadable binary operator, `x` is an expression of type `X`, and `y` is an expression of type `Y`, is processed as follows:</span></span>

*  <span data-ttu-id="3d917-321">L’ensemble des opérateurs de défini par l’utilisateur candidats fourni par `X` et `Y` pour l’opération `operator op(x,y)` est déterminée.</span><span class="sxs-lookup"><span data-stu-id="3d917-321">The set of candidate user-defined operators provided by `X` and `Y` for the operation `operator op(x,y)` is determined.</span></span> <span data-ttu-id="3d917-322">Le jeu se compose de l’union des opérateurs candidats fournis par `X` et opérateurs candidats fournis par `Y`, chaque déterminée en utilisant les règles de [opérateurs définis par l’utilisateur candidats](expressions.md#candidate-user-defined-operators).</span><span class="sxs-lookup"><span data-stu-id="3d917-322">The set consists of the union of the candidate operators provided by `X` and the candidate operators provided by `Y`, each determined using the rules of [Candidate user-defined operators](expressions.md#candidate-user-defined-operators).</span></span> <span data-ttu-id="3d917-323">Si `X` et `Y` sont du même type, ou si `X` et `Y` sont dérivées à partir d’un type de base commun, puis les opérateurs candidats partagés se produisent uniquement dans l’ensemble combiné qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="3d917-323">If `X` and `Y` are the same type, or if `X` and `Y` are derived from a common base type, then shared candidate operators only occur in the combined set once.</span></span>
*  <span data-ttu-id="3d917-324">Si l’ensemble des opérateurs définis par l’utilisateur de candidat n’est pas vide, il devient alors l’ensemble des opérateurs candidats pour l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-324">If the set of candidate user-defined operators is not empty, then this becomes the set of candidate operators for the operation.</span></span> <span data-ttu-id="3d917-325">Sinon, le fichier binaire prédéfini `operator op` implémentations, y compris leurs formes levés, devient l’ensemble des opérateurs candidats pour l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-325">Otherwise, the predefined binary `operator op` implementations, including their lifted forms,  become the set of candidate operators for the operation.</span></span> <span data-ttu-id="3d917-326">Les implémentations prédéfinies d’un opérateur donné sont spécifiées dans la description de l’opérateur ([opérateurs arithmétiques](expressions.md#arithmetic-operators) via [opérateurs logiques conditionnels](expressions.md#conditional-logical-operators)).</span><span class="sxs-lookup"><span data-stu-id="3d917-326">The predefined implementations of a given operator are specified in the description of the operator ([Arithmetic operators](expressions.md#arithmetic-operators) through [Conditional logical operators](expressions.md#conditional-logical-operators)).</span></span> <span data-ttu-id="3d917-327">Pour les opérateurs d’enum et le délégué prédéfinis, seuls les opérateurs considéré comme sont celles définies par un type enum ou un délégué qui est le type au moment de la liaison d’un des opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-327">For predefined enum and delegate operators, the only operators considered are those defined by an enum or delegate type that is the binding-time type of one of the operands.</span></span>
*  <span data-ttu-id="3d917-328">Les règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution) sont appliquées à l’ensemble des opérateurs candidats pour sélectionner le meilleur opérateur en ce qui concerne la liste d’arguments `(x,y)`, et cet opérateur devient le résultat de la surcharge processus de résolution.</span><span class="sxs-lookup"><span data-stu-id="3d917-328">The overload resolution rules of [Overload resolution](expressions.md#overload-resolution) are applied to the set of candidate operators to select the best operator with respect to the argument list `(x,y)`, and this operator becomes the result of the overload resolution process.</span></span> <span data-ttu-id="3d917-329">Si la résolution de surcharge ne parvient pas à sélectionner un meilleur opérateur unique, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-329">If overload resolution fails to select a single best operator, a binding-time error occurs.</span></span>

### <a name="candidate-user-defined-operators"></a><span data-ttu-id="3d917-330">Opérateurs définis par l’utilisateur candidats</span><span class="sxs-lookup"><span data-stu-id="3d917-330">Candidate user-defined operators</span></span>

<span data-ttu-id="3d917-331">Un type donné `T` et une opération `operator op(A)`, où `op` est un opérateur surchargeable et `A` est une liste d’arguments, l’ensemble des candidats les opérateurs définis par l’utilisateur fournis par `T` pour `operator op(A)` est déterminé comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-331">Given a type `T` and an operation `operator op(A)`, where `op` is an overloadable operator and `A` is an argument list, the set of candidate user-defined operators provided by `T` for `operator op(A)` is determined as follows:</span></span>

*  <span data-ttu-id="3d917-332">Déterminer le type `T0`.</span><span class="sxs-lookup"><span data-stu-id="3d917-332">Determine the type `T0`.</span></span> <span data-ttu-id="3d917-333">Si `T` est un type nullable, `T0` est son type sous-jacent, sinon `T0` est égal à `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-333">If `T` is a nullable type, `T0` is its underlying type, otherwise `T0` is equal to `T`.</span></span>
*  <span data-ttu-id="3d917-334">Pour toutes les `operator op` déclarations dans `T0` et tous les levé ces opérateurs, si au moins un opérateur n’est applicable ([membre de fonction Applicable](expressions.md#applicable-function-member)) par rapport à la liste d’arguments `A`, puis l’ensemble des opérateurs candidats se compose de tous les opérateurs qui font l’objet de ce type applicables dans `T0`.</span><span class="sxs-lookup"><span data-stu-id="3d917-334">For all `operator op` declarations in `T0` and all lifted forms of such operators, if at least one operator is applicable ([Applicable function member](expressions.md#applicable-function-member)) with respect to the argument list `A`, then the set of candidate operators consists of all such applicable operators in `T0`.</span></span>
*  <span data-ttu-id="3d917-335">Sinon, si `T0` est `object`, l’ensemble des opérateurs candidats est vide.</span><span class="sxs-lookup"><span data-stu-id="3d917-335">Otherwise, if `T0` is `object`, the set of candidate operators is empty.</span></span>
*  <span data-ttu-id="3d917-336">Sinon, l’ensemble des opérateurs candidats fourni par `T0` est l’ensemble des opérateurs candidats fournis par la classe de base directe de `T0`, ou la classe de base efficace de `T0` si `T0` est un paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-336">Otherwise, the set of candidate operators provided by `T0` is the set of candidate operators provided by the direct base class of `T0`, or the effective base class of `T0` if `T0` is a type parameter.</span></span>

### <a name="numeric-promotions"></a><span data-ttu-id="3d917-337">Promotions numériques</span><span class="sxs-lookup"><span data-stu-id="3d917-337">Numeric promotions</span></span>

<span data-ttu-id="3d917-338">La promotion numérique consiste à effectuer automatiquement certaines conversions implicites des opérandes de le prédéfinis opérateurs unaires et binaires numériques.</span><span class="sxs-lookup"><span data-stu-id="3d917-338">Numeric promotion consists of automatically performing certain implicit conversions of the operands of the predefined unary and binary numeric operators.</span></span> <span data-ttu-id="3d917-339">La promotion numérique n’est pas un mécanisme distinct, mais plutôt un effet de l’application de la résolution de surcharge pour les opérateurs prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="3d917-339">Numeric promotion is not a distinct mechanism, but rather an effect of applying overload resolution to the predefined operators.</span></span> <span data-ttu-id="3d917-340">La promotion numérique n’affecte pas, en particulier d’évaluation des opérateurs définis par l’utilisateur, bien que les opérateurs définis par l’utilisateur peuvent être implémentées pour présenter des effets similaires.</span><span class="sxs-lookup"><span data-stu-id="3d917-340">Numeric promotion specifically does not affect evaluation of user-defined operators, although user-defined operators can be implemented to exhibit similar effects.</span></span>

<span data-ttu-id="3d917-341">Comme exemple de promotion numérique, examinons les implémentations prédéfinies du binaire `*` opérateur :</span><span class="sxs-lookup"><span data-stu-id="3d917-341">As an example of numeric promotion, consider the predefined implementations of the binary `*` operator:</span></span>

```csharp
int operator *(int x, int y);
uint operator *(uint x, uint y);
long operator *(long x, long y);
ulong operator *(ulong x, ulong y);
float operator *(float x, float y);
double operator *(double x, double y);
decimal operator *(decimal x, decimal y);
```

<span data-ttu-id="3d917-342">Lorsque les règles de résolution de surcharge ([la résolution de surcharge](expressions.md#overload-resolution)) sont appliquées à cet ensemble d’opérateurs, l’effet consiste à sélectionner le premier des opérateurs pour lesquels existent des conversions implicites à partir de types d’opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-342">When overload resolution rules ([Overload resolution](expressions.md#overload-resolution)) are applied to this set of operators, the effect is to select the first of the operators for which implicit conversions exist from the operand types.</span></span> <span data-ttu-id="3d917-343">Par exemple, pour l’opération `b * s`, où `b` est un `byte` et `s` est un `short`, sélectionne de résolution de surcharge `operator *(int,int)` comme meilleur opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-343">For example, for the operation `b * s`, where `b` is a `byte` and `s` is a `short`, overload resolution selects `operator *(int,int)` as the best operator.</span></span> <span data-ttu-id="3d917-344">Par conséquent, le résultat est que `b` et `s` sont convertis en `int`, et le type du résultat est `int`.</span><span class="sxs-lookup"><span data-stu-id="3d917-344">Thus, the effect is that `b` and `s` are converted to `int`, and the type of the result is `int`.</span></span> <span data-ttu-id="3d917-345">De même, pour l’opération `i * d`, où `i` est un `int` et `d` est un `double`, sélectionne de résolution de surcharge `operator *(double,double)` comme meilleur opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-345">Likewise, for the operation `i * d`, where `i` is an `int` and `d` is a `double`, overload resolution selects `operator *(double,double)` as the best operator.</span></span>

#### <a name="unary-numeric-promotions"></a><span data-ttu-id="3d917-346">Promotions numériques unaires</span><span class="sxs-lookup"><span data-stu-id="3d917-346">Unary numeric promotions</span></span>

<span data-ttu-id="3d917-347">La promotion numérique unaire se produit pour les opérandes de prédéfinis `+`, `-`, et `~` opérateurs unaires.</span><span class="sxs-lookup"><span data-stu-id="3d917-347">Unary numeric promotion occurs for the operands of the predefined `+`, `-`, and `~` unary operators.</span></span> <span data-ttu-id="3d917-348">La promotion numérique unaire se compose simplement de la conversion des opérandes de type `sbyte`, `byte`, `short`, `ushort`, ou `char` à taper `int`.</span><span class="sxs-lookup"><span data-stu-id="3d917-348">Unary numeric promotion simply consists of converting operands of type `sbyte`, `byte`, `short`, `ushort`, or `char` to type `int`.</span></span> <span data-ttu-id="3d917-349">En outre, dans le cas de `-` opérateur, la promotion numérique unaire convertit les opérandes de type `uint` à taper `long`.</span><span class="sxs-lookup"><span data-stu-id="3d917-349">Additionally, for the unary `-` operator, unary numeric promotion converts operands of type `uint` to type `long`.</span></span>

#### <a name="binary-numeric-promotions"></a><span data-ttu-id="3d917-350">Promotions numériques binaires</span><span class="sxs-lookup"><span data-stu-id="3d917-350">Binary numeric promotions</span></span>

<span data-ttu-id="3d917-351">La promotion numérique binaire se produit pour les opérandes de prédéfinis `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `==`, `!=`, `>`, `<`, `>=`, et `<=` opérateurs binaires.</span><span class="sxs-lookup"><span data-stu-id="3d917-351">Binary numeric promotion occurs for the operands of the predefined `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `==`, `!=`, `>`, `<`, `>=`, and `<=` binary operators.</span></span> <span data-ttu-id="3d917-352">La promotion numérique binaire convertit implicitement les deux opérandes en un type commun qui, dans le cas des opérateurs non relationnels, devient également le type de résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-352">Binary numeric promotion implicitly converts both operands to a common type which, in case of the non-relational operators, also becomes the result type of the operation.</span></span> <span data-ttu-id="3d917-353">La promotion numérique binaire consiste à appliquer les règles suivantes, dans l’ordre de qu'apparition ici :</span><span class="sxs-lookup"><span data-stu-id="3d917-353">Binary numeric promotion consists of applying the following rules, in the order they appear here:</span></span>

*  <span data-ttu-id="3d917-354">Si des opérandes est de type `decimal`, l’autre opérande est converti en type `decimal`, ou une erreur au moment de la liaison se produit si l’autre opérande est de type `float` ou `double`.</span><span class="sxs-lookup"><span data-stu-id="3d917-354">If either operand is of type `decimal`, the other operand is converted to type `decimal`, or a binding-time error occurs if the other operand is of type `float` or `double`.</span></span>
*  <span data-ttu-id="3d917-355">Sinon, si des opérandes est de type `double`, l’autre opérande est converti en type `double`.</span><span class="sxs-lookup"><span data-stu-id="3d917-355">Otherwise, if either operand is of type `double`, the other operand is converted to type `double`.</span></span>
*  <span data-ttu-id="3d917-356">Sinon, si des opérandes est de type `float`, l’autre opérande est converti en type `float`.</span><span class="sxs-lookup"><span data-stu-id="3d917-356">Otherwise, if either operand is of type `float`, the other operand is converted to type `float`.</span></span>
*  <span data-ttu-id="3d917-357">Sinon, si des opérandes est de type `ulong`, l’autre opérande est converti en type `ulong`, ou une erreur au moment de la liaison se produit si l’autre opérande est de type `sbyte`, `short`, `int`, ou `long`.</span><span class="sxs-lookup"><span data-stu-id="3d917-357">Otherwise, if either operand is of type `ulong`, the other operand is converted to type `ulong`, or a binding-time error occurs if the other operand is of type `sbyte`, `short`, `int`, or `long`.</span></span>
*  <span data-ttu-id="3d917-358">Sinon, si des opérandes est de type `long`, l’autre opérande est converti en type `long`.</span><span class="sxs-lookup"><span data-stu-id="3d917-358">Otherwise, if either operand is of type `long`, the other operand is converted to type `long`.</span></span>
*  <span data-ttu-id="3d917-359">Sinon, si des opérandes est de type `uint` et l’autre opérande est de type `sbyte`, `short`, ou `int`, les deux opérandes sont convertis en type `long`.</span><span class="sxs-lookup"><span data-stu-id="3d917-359">Otherwise, if either operand is of type `uint` and the other operand is of type `sbyte`, `short`, or `int`, both operands are converted to type `long`.</span></span>
*  <span data-ttu-id="3d917-360">Sinon, si des opérandes est de type `uint`, l’autre opérande est converti en type `uint`.</span><span class="sxs-lookup"><span data-stu-id="3d917-360">Otherwise, if either operand is of type `uint`, the other operand is converted to type `uint`.</span></span>
*  <span data-ttu-id="3d917-361">Sinon, les deux opérandes sont convertis en type `int`.</span><span class="sxs-lookup"><span data-stu-id="3d917-361">Otherwise, both operands are converted to type `int`.</span></span>

<span data-ttu-id="3d917-362">Notez que la première règle n’autorise pas toutes les opérations qui combinent le `decimal` type avec le `double` et `float` types.</span><span class="sxs-lookup"><span data-stu-id="3d917-362">Note that the first rule disallows any operations that mix the `decimal` type with the `double` and `float` types.</span></span> <span data-ttu-id="3d917-363">La règle résulte du fait qu’aucune conversion implicite entre le `decimal` type et le `double` et `float` types.</span><span class="sxs-lookup"><span data-stu-id="3d917-363">The rule follows from the fact that there are no implicit conversions between the `decimal` type and the `double` and `float` types.</span></span>

<span data-ttu-id="3d917-364">Notez également qu’il n’est pas possible pour un opérande de type `ulong` lorsque l’autre opérande est d’un type intégral signé.</span><span class="sxs-lookup"><span data-stu-id="3d917-364">Also note that it is not possible for an operand to be of type `ulong` when the other operand is of a signed integral type.</span></span> <span data-ttu-id="3d917-365">La raison est qu’il existe aucun type intégral qui peut représenter la gamme complète des `ulong` , ainsi que les types intégraux signés.</span><span class="sxs-lookup"><span data-stu-id="3d917-365">The reason is that no integral type exists that can represent the full range of `ulong` as well as the signed integral types.</span></span>

<span data-ttu-id="3d917-366">Dans les deux cas ci-dessus, une expression de cast peut être utilisée pour convertir explicitement un opérande en un type qui est compatible avec l’autre opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-366">In both of the above cases, a cast expression can be used to explicitly convert one operand to a type that is compatible with the other operand.</span></span>

<span data-ttu-id="3d917-367">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-367">In the example</span></span>
```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (1.0 + percent / 100.0);
}
```
<span data-ttu-id="3d917-368">une erreur au moment de la liaison se produit, car un `decimal` ne peut pas être multiplié par un `double`.</span><span class="sxs-lookup"><span data-stu-id="3d917-368">a binding-time error occurs because a `decimal` cannot be multiplied by a `double`.</span></span> <span data-ttu-id="3d917-369">L’erreur est résolue en la convertissant explicitement le deuxième opérande à `decimal`, comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-369">The error is resolved by explicitly converting the second operand to `decimal`, as follows:</span></span>

```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (decimal)(1.0 + percent / 100.0);
}
```

### <a name="lifted-operators"></a><span data-ttu-id="3d917-370">Opérateurs levés</span><span class="sxs-lookup"><span data-stu-id="3d917-370">Lifted operators</span></span>

<span data-ttu-id="3d917-371">***Levé opérateurs*** autoriser les opérateurs prédéfinis et définis par l’utilisateur qui fonctionnent sur des types valeur non nullable à utiliser avec les formulaires nullables de ces types.</span><span class="sxs-lookup"><span data-stu-id="3d917-371">***Lifted operators*** permit predefined and user-defined operators that operate on non-nullable value types to also be used with nullable forms of those types.</span></span> <span data-ttu-id="3d917-372">Opérateurs capturées sont construites à partir d’opérateurs prédéfinis et définis par l’utilisateur qui répondent à certaines exigences, comme décrit dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="3d917-372">Lifted operators are constructed from predefined and user-defined operators that meet certain requirements, as described in the following:</span></span>

*   <span data-ttu-id="3d917-373">Pour les opérateurs unaires</span><span class="sxs-lookup"><span data-stu-id="3d917-373">For the unary operators</span></span>

    ```csharp
    +  ++  -  --  !  ~
    ```

    <span data-ttu-id="3d917-374">Il existe un formulaire levé d’un opérateur si les types d’opérande et le résultat sont les deux types de valeur non nullable.</span><span class="sxs-lookup"><span data-stu-id="3d917-374">a lifted form of an operator exists if the operand and result types are both non-nullable value types.</span></span> <span data-ttu-id="3d917-375">Le formulaire levé est construit en ajoutant un seul `?` modificateur aux types d’opérande et le résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-375">The lifted form is constructed by adding a single `?` modifier to the operand and result types.</span></span> <span data-ttu-id="3d917-376">L’opérateur levé produit une valeur null si l’opérande a la valeur null.</span><span class="sxs-lookup"><span data-stu-id="3d917-376">The lifted operator produces a null value if the operand is null.</span></span> <span data-ttu-id="3d917-377">Sinon, l’opérateur levé Désencapsule l’opérande s’applique l’opérateur sous-jacent et encapsule le résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-377">Otherwise, the lifted operator unwraps the operand, applies the underlying operator, and wraps the result.</span></span>

*   <span data-ttu-id="3d917-378">Pour les opérateurs binaires</span><span class="sxs-lookup"><span data-stu-id="3d917-378">For the binary operators</span></span>

    ```csharp
    +  -  *  /  %  &  |  ^  <<  >>
    ```

    <span data-ttu-id="3d917-379">Il existe un formulaire levé d’un opérateur si les types d’opérande et le résultat sont tous les types valeur non nullable.</span><span class="sxs-lookup"><span data-stu-id="3d917-379">a lifted form of an operator exists if the operand and result types are all non-nullable value types.</span></span> <span data-ttu-id="3d917-380">Le formulaire levé est construit en ajoutant un seul `?` modificateur à chaque type d’opérande et le résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-380">The lifted form is constructed by adding a single `?` modifier to each operand and result type.</span></span> <span data-ttu-id="3d917-381">L’opérateur levé produit une valeur null si un ou les deux opérandes ont la valeur null (une exception qui est la `&` et `|` opérateurs de la `bool?` type, comme décrit dans [booléennes opérateurs logiques](expressions.md#boolean-logical-operators)).</span><span class="sxs-lookup"><span data-stu-id="3d917-381">The lifted operator produces a null value if one or both operands are null (an exception being the `&` and `|` operators of the `bool?` type, as described in [Boolean logical operators](expressions.md#boolean-logical-operators)).</span></span> <span data-ttu-id="3d917-382">Sinon, l’opérateur levé désencapsule les opérandes s’applique l’opérateur sous-jacent et encapsule le résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-382">Otherwise, the lifted operator unwraps the operands, applies the underlying operator, and wraps the result.</span></span>

*   <span data-ttu-id="3d917-383">Pour les opérateurs d’égalité</span><span class="sxs-lookup"><span data-stu-id="3d917-383">For the equality operators</span></span>

    ```csharp
    ==  !=
    ```

    <span data-ttu-id="3d917-384">Il existe un formulaire levé d’un opérateur si les types d’opérandes sont des types valeur non nullable et si le type de résultat est `bool`.</span><span class="sxs-lookup"><span data-stu-id="3d917-384">a lifted form of an operator exists if the operand types are both non-nullable value types and if the result type is `bool`.</span></span> <span data-ttu-id="3d917-385">Le formulaire levé est construit en ajoutant un seul `?` modificateur à chaque type d’opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-385">The lifted form is constructed by adding a single `?` modifier to each operand type.</span></span> <span data-ttu-id="3d917-386">L’opérateur levé considère qu’égal de deux valeurs null et une valeur null inégal à n’importe quelle valeur non null.</span><span class="sxs-lookup"><span data-stu-id="3d917-386">The lifted operator considers two null values equal, and a null value unequal to any non-null value.</span></span> <span data-ttu-id="3d917-387">Si les deux opérandes sont non null, l’opérateur levé désencapsule les opérandes et applique l’opérateur sous-jacente pour produire le `bool` résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-387">If both operands are non-null, the lifted operator unwraps the operands and applies the underlying operator to produce the `bool` result.</span></span>

*   <span data-ttu-id="3d917-388">Pour les opérateurs relationnels</span><span class="sxs-lookup"><span data-stu-id="3d917-388">For the relational operators</span></span>

    ```csharp
    <  >  <=  >=
    ```

    <span data-ttu-id="3d917-389">Il existe un formulaire levé d’un opérateur si les types d’opérandes sont des types valeur non nullable et si le type de résultat est `bool`.</span><span class="sxs-lookup"><span data-stu-id="3d917-389">a lifted form of an operator exists if the operand types are both non-nullable value types and if the result type is `bool`.</span></span> <span data-ttu-id="3d917-390">Le formulaire levé est construit en ajoutant un seul `?` modificateur à chaque type d’opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-390">The lifted form is constructed by adding a single `?` modifier to each operand type.</span></span> <span data-ttu-id="3d917-391">L’opérateur levé produit la valeur `false` si un ou deux opérandes sont null.</span><span class="sxs-lookup"><span data-stu-id="3d917-391">The lifted operator produces the value `false` if one or both operands are null.</span></span> <span data-ttu-id="3d917-392">Sinon, l’opérateur levé désencapsule les opérandes et applique l’opérateur sous-jacente pour produire le `bool` résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-392">Otherwise, the lifted operator unwraps the operands and applies the underlying operator to produce the `bool` result.</span></span>

## <a name="member-lookup"></a><span data-ttu-id="3d917-393">Recherche de membres</span><span class="sxs-lookup"><span data-stu-id="3d917-393">Member lookup</span></span>

<span data-ttu-id="3d917-394">Recherche d’un membre est le processus par lequel la signification d’un nom dans le contexte d’un type est déterminée.</span><span class="sxs-lookup"><span data-stu-id="3d917-394">A member lookup is the process whereby the meaning of a name in the context of a type is determined.</span></span> <span data-ttu-id="3d917-395">Recherche d’un membre peut se produire dans le cadre de l’évaluation d’un *simple_name* ([noms simples](expressions.md#simple-names)) ou un *member_access* ([l’accès au membre](expressions.md#member-access)) dans un expression.</span><span class="sxs-lookup"><span data-stu-id="3d917-395">A member lookup can occur as part of evaluating a *simple_name* ([Simple names](expressions.md#simple-names)) or a *member_access* ([Member access](expressions.md#member-access)) in an expression.</span></span> <span data-ttu-id="3d917-396">Si le *simple_name* ou *member_access* se produit en tant que le *primary_expression* d’un *invocation_expression* ([ Appels de méthode](expressions.md#method-invocations)), le membre est dit à appeler.</span><span class="sxs-lookup"><span data-stu-id="3d917-396">If the *simple_name* or *member_access* occurs as the *primary_expression* of an *invocation_expression* ([Method invocations](expressions.md#method-invocations)), the member is said to be invoked.</span></span>

<span data-ttu-id="3d917-397">Si un membre est une méthode ou un événement, ou s’il s’agit d’une constante, le champ ou la propriété de type délégué ([délégués](delegates.md)) ou le type `dynamic` ([du type dynamic](types.md#the-dynamic-type)), le membre est dit être *invocable*.</span><span class="sxs-lookup"><span data-stu-id="3d917-397">If a member is a method or event, or if it is a constant, field or property of either a delegate type ([Delegates](delegates.md)) or the type `dynamic` ([The dynamic type](types.md#the-dynamic-type)), then the member is said to be *invocable*.</span></span>

<span data-ttu-id="3d917-398">Recherche de membres considère que non seulement le nom d’un membre, mais également le nombre de paramètres de type pour le membre et si le membre est accessible.</span><span class="sxs-lookup"><span data-stu-id="3d917-398">Member lookup considers not only the name of a member but also the number of type parameters the member has and whether the member is accessible.</span></span> <span data-ttu-id="3d917-399">Dans le cadre de la recherche de membres, les méthodes génériques et des types génériques imbriqués ont le nombre de paramètres de type indiqué dans leurs déclarations respectifs et tous les autres membres ont aucun paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-399">For the purposes of member lookup, generic methods and nested generic types have the number of type parameters indicated in their respective declarations and all other members have zero type parameters.</span></span>

<span data-ttu-id="3d917-400">Recherche d’un membre d’un nom `N` avec `K`  paramètres de type dans un type `T` est traité comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-400">A member lookup of a name `N` with `K` type parameters in a type `T` is processed as follows:</span></span>

*  <span data-ttu-id="3d917-401">Tout d’abord, un ensemble de membres accessibles nommé `N` est déterminé :</span><span class="sxs-lookup"><span data-stu-id="3d917-401">First, a set of accessible members named `N` is determined:</span></span>
    * <span data-ttu-id="3d917-402">Si `T` est un paramètre de type, le jeu est l’union des jeux de membres accessibles nommés `N` dans chacun des types spécifiés en tant que contrainte primaire ou secondaire contrainte ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)) pour  `T`, ainsi que l’ensemble des membres accessibles nommé `N` dans `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-402">If `T` is a type parameter, then the set is the union of the sets of accessible members named `N` in each of the types specified as a primary constraint or secondary constraint ([Type parameter constraints](classes.md#type-parameter-constraints)) for `T`, along with the set of accessible members named `N` in `object`.</span></span>
    * <span data-ttu-id="3d917-403">Sinon, le jeu est composé de tous accessibles ([l’accès au membre](basic-concepts.md#member-access)) membres nommés `N` dans `T`, y compris les membres hérités et les membres accessibles nommés `N` dans `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-403">Otherwise, the set consists of all accessible ([Member access](basic-concepts.md#member-access)) members named `N` in `T`, including inherited members and the accessible members named `N` in `object`.</span></span> <span data-ttu-id="3d917-404">Si `T` est un type construit, le jeu de membres est obtenu en remplaçant les arguments de type comme décrit dans [membres des types construits](classes.md#members-of-constructed-types).</span><span class="sxs-lookup"><span data-stu-id="3d917-404">If `T` is a constructed type, the set of members is obtained by substituting type arguments as described in [Members of constructed types](classes.md#members-of-constructed-types).</span></span> <span data-ttu-id="3d917-405">Les membres qui incluent un `override` modificateur sont exclus de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="3d917-405">Members that include an `override` modifier are excluded from the set.</span></span>
*  <span data-ttu-id="3d917-406">Ensuite, si `K` est égal à zéro, imbriquée de tous les types dont les déclarations incluent des paramètres de type sont supprimés.</span><span class="sxs-lookup"><span data-stu-id="3d917-406">Next, if `K` is zero, all nested types whose declarations include type parameters are removed.</span></span> <span data-ttu-id="3d917-407">Si `K` est ne pas égal à zéro, tous les membres avec un nombre différent de type de paramètres sont supprimés.</span><span class="sxs-lookup"><span data-stu-id="3d917-407">If `K` is not zero, all members with a different number of type parameters are removed.</span></span> <span data-ttu-id="3d917-408">Notez que lorsque `K` sont zéro, les méthodes ayant le type de paramètres ne sont pas supprimées, depuis le processus d’inférence de type ([l’inférence de Type](expressions.md#type-inference)) peut être en mesure de déduire les arguments de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-408">Note that when `K` is zero, methods having type parameters are not removed, since the type inference process ([Type inference](expressions.md#type-inference)) might be able to infer the type arguments.</span></span>
*  <span data-ttu-id="3d917-409">Ensuite, si le membre est *appelé*, tous les éléments non-*invocable* membres sont supprimés de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="3d917-409">Next, if the member is *invoked*, all non-*invocable* members are removed from the set.</span></span>
*  <span data-ttu-id="3d917-410">Ensuite, les membres qui sont masqués par d’autres membres sont supprimés de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="3d917-410">Next, members that are hidden by other members are removed from the set.</span></span> <span data-ttu-id="3d917-411">Pour chaque membre `S.M` dans l’ensemble, où `S` est le type dans lequel le membre `M` est déclaré, les règles suivantes sont appliquées :</span><span class="sxs-lookup"><span data-stu-id="3d917-411">For every member `S.M` in the set, where `S` is the type in which the member `M` is declared, the following rules are applied:</span></span>
    * <span data-ttu-id="3d917-412">Si `M` est constante, champ, propriété, événement ou membre d’énumération, puis tous les membres déclarés dans un type de base de `S` sont supprimés de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="3d917-412">If `M` is a constant, field, property, event, or enumeration member, then all members declared in a base type of `S` are removed from the set.</span></span>
    * <span data-ttu-id="3d917-413">Si `M` est une déclaration de type, puis tous les non-types déclarés dans un type de base de `S` sont supprimés de l’ensemble, et toutes les déclarations avec le même nombre de paramètres de type en tant que de type `M` déclaré dans un type de base de `S` sont supprimés à partir du jeu.</span><span class="sxs-lookup"><span data-stu-id="3d917-413">If `M` is a type declaration, then all non-types declared in a base type of `S` are removed from the set, and all type declarations with the same number of type parameters as `M` declared in a base type of `S` are removed from the set.</span></span>
    * <span data-ttu-id="3d917-414">Si `M` est une méthode, puis tous les membres non méthode déclarée dans un type de base de `S` sont supprimés de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="3d917-414">If `M` is a method, then all non-method members declared in a base type of `S` are removed from the set.</span></span>
*  <span data-ttu-id="3d917-415">Ensuite, les membres d’interface qui sont masqués par les membres de classe sont supprimés de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="3d917-415">Next, interface members that are hidden by class members are removed from the set.</span></span> <span data-ttu-id="3d917-416">Cette étape n’a d’effet que si `T` est un paramètre de type et `T` a à la fois une classe de base efficace autre que `object` et une interface efficace vide définie ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)).</span><span class="sxs-lookup"><span data-stu-id="3d917-416">This step only has an effect if `T` is a type parameter and `T` has both an effective base class other than `object` and a non-empty effective interface set ([Type parameter constraints](classes.md#type-parameter-constraints)).</span></span> <span data-ttu-id="3d917-417">Pour chaque membre `S.M` dans l’ensemble, où `S` est le type dans lequel le membre `M` est déclaré, les règles suivantes sont appliquées si `S` est une déclaration de classe autre que `object`:</span><span class="sxs-lookup"><span data-stu-id="3d917-417">For every member `S.M` in the set, where `S` is the type in which the member `M` is declared, the following rules are applied if `S` is a class declaration other than `object`:</span></span>
    * <span data-ttu-id="3d917-418">Si `M` est une constante, champ, propriété, événement, membre d’énumération ou déclaration de type, puis tous les membres déclarés dans une déclaration d’interface sont supprimés de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="3d917-418">If `M` is a constant, field, property, event, enumeration member, or type declaration, then all members declared in an interface declaration are removed from the set.</span></span>
    * <span data-ttu-id="3d917-419">Si `M` est une méthode, puis tous les membres non méthode déclarées dans une déclaration d’interface sont supprimés de l’ensemble et toutes les méthodes avec la même signature que `M` déclaré dans une interface déclaration sont supprimés de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="3d917-419">If `M` is a method, then all non-method members declared in an interface declaration are removed from the set, and all methods with the same signature as `M` declared in an interface declaration are removed from the set.</span></span>
*  <span data-ttu-id="3d917-420">Enfin, le résultat de la recherche d’avoir supprimé les membres masqués, est déterminé :</span><span class="sxs-lookup"><span data-stu-id="3d917-420">Finally, having removed hidden members, the result of the lookup is determined:</span></span>
    * <span data-ttu-id="3d917-421">Si le jeu se compose d’un seul membre qui n’est pas une méthode, ce membre est le résultat de la recherche.</span><span class="sxs-lookup"><span data-stu-id="3d917-421">If the set consists of a single member that is not a method, then this member is the result of the lookup.</span></span>
    * <span data-ttu-id="3d917-422">Sinon, si le jeu contient uniquement des méthodes, ce groupe de méthodes est le résultat de la recherche.</span><span class="sxs-lookup"><span data-stu-id="3d917-422">Otherwise, if the set contains only methods, then this group of methods is the result of the lookup.</span></span>
    * <span data-ttu-id="3d917-423">Sinon, la recherche est ambiguë et une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-423">Otherwise, the lookup is ambiguous, and a binding-time error occurs.</span></span>

<span data-ttu-id="3d917-424">Pour les recherches de membres dans les types autres que les paramètres de type et les interfaces et les recherches de membres dans les interfaces qui sont strictement à héritage unique (chaque interface dans la chaîne d’héritage possède exactement zéro ou une interface de base directe), est l’effet des règles de recherche simplement qui dérivée des membres de base de masquer les membres avec le même nom ou la même signature.</span><span class="sxs-lookup"><span data-stu-id="3d917-424">For member lookups in types other than type parameters and interfaces, and member lookups in interfaces that are strictly single-inheritance (each interface in the inheritance chain has exactly zero or one direct base interface), the effect of the lookup rules is simply that derived members hide base members with the same name or signature.</span></span> <span data-ttu-id="3d917-425">Ces recherches à héritage unique ne sont jamais ambiguës.</span><span class="sxs-lookup"><span data-stu-id="3d917-425">Such single-inheritance lookups are never ambiguous.</span></span> <span data-ttu-id="3d917-426">Les ambiguïtés qui peuvent éventuellement résulter des recherches dans les interfaces de l’héritage multiple sont décrites dans [accès aux membres de l’Interface](interfaces.md#interface-member-access).</span><span class="sxs-lookup"><span data-stu-id="3d917-426">The ambiguities that can possibly arise from member lookups in multiple-inheritance interfaces are described in [Interface member access](interfaces.md#interface-member-access).</span></span>

### <a name="base-types"></a><span data-ttu-id="3d917-427">Types de base</span><span class="sxs-lookup"><span data-stu-id="3d917-427">Base types</span></span>

<span data-ttu-id="3d917-428">À des fins de recherche de membres, un type `T` est considérée comme ayant les types de base suivants :</span><span class="sxs-lookup"><span data-stu-id="3d917-428">For purposes of member lookup, a type `T` is considered to have the following base types:</span></span>

*  <span data-ttu-id="3d917-429">Si `T` est `object`, puis `T` n’a aucun type de base.</span><span class="sxs-lookup"><span data-stu-id="3d917-429">If `T` is `object`, then `T` has no base type.</span></span>
*  <span data-ttu-id="3d917-430">Si `T` est un *type_de_liste*, les types de base de `T` sont les types de classe `System.Enum`, `System.ValueType`, et `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-430">If `T` is an *enum_type*, the base types of `T` are the class types `System.Enum`, `System.ValueType`, and `object`.</span></span>
*  <span data-ttu-id="3d917-431">Si `T` est un *struct_type*, les types de base de `T` sont les types de classe `System.ValueType` et `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-431">If `T` is a *struct_type*, the base types of `T` are the class types `System.ValueType` and `object`.</span></span>
*  <span data-ttu-id="3d917-432">Si `T` est un *class_type*, les types de base de `T` sont des classes de base de `T`, y compris le type de classe `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-432">If `T` is a *class_type*, the base types of `T` are the base classes of `T`, including the class type `object`.</span></span>
*  <span data-ttu-id="3d917-433">Si `T` est un *interface_type*, les types de base de `T` sont les interfaces de base de `T` et le type de classe `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-433">If `T` is an *interface_type*, the base types of `T` are the base interfaces of `T` and the class type `object`.</span></span>
*  <span data-ttu-id="3d917-434">Si `T` est un *array_type*, les types de base de `T` sont les types de classe `System.Array` et `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-434">If `T` is an *array_type*, the base types of `T` are the class types `System.Array` and `object`.</span></span>
*  <span data-ttu-id="3d917-435">Si `T` est un *delegate_type*, les types de base de `T` sont les types de classe `System.Delegate` et `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-435">If `T` is a *delegate_type*, the base types of `T` are the class types `System.Delegate` and `object`.</span></span>

## <a name="function-members"></a><span data-ttu-id="3d917-436">Fonctions membres</span><span class="sxs-lookup"><span data-stu-id="3d917-436">Function members</span></span>

<span data-ttu-id="3d917-437">Les fonctions membres sont des membres qui contiennent des instructions exécutables.</span><span class="sxs-lookup"><span data-stu-id="3d917-437">Function members are members that contain executable statements.</span></span> <span data-ttu-id="3d917-438">Fonctions membres sont toujours membres de types et ne peut pas être membres d’espaces de noms.</span><span class="sxs-lookup"><span data-stu-id="3d917-438">Function members are always members of types and cannot be members of namespaces.</span></span> <span data-ttu-id="3d917-439">C# définit les catégories de fonctions membres suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-439">C# defines the following categories of function members:</span></span>

*  <span data-ttu-id="3d917-440">Méthodes</span><span class="sxs-lookup"><span data-stu-id="3d917-440">Methods</span></span>
*  <span data-ttu-id="3d917-441">Propriétés</span><span class="sxs-lookup"><span data-stu-id="3d917-441">Properties</span></span>
*  <span data-ttu-id="3d917-442">Événements</span><span class="sxs-lookup"><span data-stu-id="3d917-442">Events</span></span>
*  <span data-ttu-id="3d917-443">Indexeurs</span><span class="sxs-lookup"><span data-stu-id="3d917-443">Indexers</span></span>
*  <span data-ttu-id="3d917-444">Opérateurs définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="3d917-444">User-defined operators</span></span>
*  <span data-ttu-id="3d917-445">Constructeurs d’instance</span><span class="sxs-lookup"><span data-stu-id="3d917-445">Instance constructors</span></span>
*  <span data-ttu-id="3d917-446">Constructeurs statiques</span><span class="sxs-lookup"><span data-stu-id="3d917-446">Static constructors</span></span>
*  <span data-ttu-id="3d917-447">Destructeurs</span><span class="sxs-lookup"><span data-stu-id="3d917-447">Destructors</span></span>

<span data-ttu-id="3d917-448">À l’exception des destructeurs et des constructeurs statiques (qui ne peut pas être appelés explicitement), les instructions contenues dans les fonctions membres sont exécutées via des appels de fonctions membres.</span><span class="sxs-lookup"><span data-stu-id="3d917-448">Except for destructors and static constructors (which cannot be invoked explicitly), the statements contained in function members are executed through function member invocations.</span></span> <span data-ttu-id="3d917-449">La syntaxe réelle pour l’écriture d’un appel de fonction membre dépend de la catégorie de membre de fonction particulière.</span><span class="sxs-lookup"><span data-stu-id="3d917-449">The actual syntax for writing a function member invocation depends on the particular function member category.</span></span>

<span data-ttu-id="3d917-450">La liste d’arguments ([listes d’arguments](expressions.md#argument-lists)) d’une fonction membre appel fournit des valeurs réelles ou des références variables pour les paramètres de la fonction membre.</span><span class="sxs-lookup"><span data-stu-id="3d917-450">The argument list ([Argument lists](expressions.md#argument-lists)) of a function member invocation provides actual values or variable references for the parameters of the function member.</span></span>

<span data-ttu-id="3d917-451">Appels de méthodes génériques peuvent utiliser l’inférence de type pour déterminer le jeu d’arguments de type à passer à la méthode.</span><span class="sxs-lookup"><span data-stu-id="3d917-451">Invocations of generic methods may employ type inference to determine the set of type arguments to pass to the method.</span></span> <span data-ttu-id="3d917-452">Ce processus est décrit dans [l’inférence de Type](expressions.md#type-inference).</span><span class="sxs-lookup"><span data-stu-id="3d917-452">This process is described in [Type inference](expressions.md#type-inference).</span></span>

<span data-ttu-id="3d917-453">Les appels de méthodes, indexeurs, opérateurs et constructeurs d’instance emploient la résolution de surcharge pour déterminer parmi un ensemble de candidats de membres de fonction à appeler.</span><span class="sxs-lookup"><span data-stu-id="3d917-453">Invocations of methods, indexers, operators and instance constructors employ overload resolution to determine which of a candidate set of function members to invoke.</span></span> <span data-ttu-id="3d917-454">Ce processus est décrit dans [la résolution de surcharge](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="3d917-454">This process is described in [Overload resolution](expressions.md#overload-resolution).</span></span>

<span data-ttu-id="3d917-455">Une fois qu’une fonction membre particulière a été identifié au moment de la liaison, éventuellement via la résolution de surcharge, le processus d’exécution réel de l’appel de la fonction membre est décrit dans [la vérification de la résolution de surcharge dynamiquelorsdelacompilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="3d917-455">Once a particular function member has been identified at binding-time, possibly through overload resolution, the actual run-time process of invoking the function member is described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="3d917-456">Le tableau suivant récapitule le traitement qui a lieu dans les constructions qui impliquent les six catégories de fonctions membres qui peuvent être appelés explicitement.</span><span class="sxs-lookup"><span data-stu-id="3d917-456">The following table summarizes the processing that takes place in constructs involving the six categories of function members that can be explicitly invoked.</span></span> <span data-ttu-id="3d917-457">Dans la table, `e`, `x`, `y`, et `value` indiquent les expressions classifiées en tant que variables ou des valeurs, `T` indique une expression classifiée comme un type, `F` est le nom simple d’une méthode, `P` est le nom simple d’une propriété.</span><span class="sxs-lookup"><span data-stu-id="3d917-457">In the table, `e`, `x`, `y`, and `value` indicate expressions classified as variables or values, `T` indicates an expression classified as a type, `F` is the simple name of a method, and `P` is the simple name of a property.</span></span>


| <span data-ttu-id="3d917-458">__Construction__</span><span class="sxs-lookup"><span data-stu-id="3d917-458">__Construct__</span></span>     | <span data-ttu-id="3d917-459">__Exemple__</span><span class="sxs-lookup"><span data-stu-id="3d917-459">__Example__</span></span>    | <span data-ttu-id="3d917-460">__Description__</span><span class="sxs-lookup"><span data-stu-id="3d917-460">__Description__</span></span> |
|-------------------|----------------|-----------------|
| <span data-ttu-id="3d917-461">Appel de méthode</span><span class="sxs-lookup"><span data-stu-id="3d917-461">Method invocation</span></span> | `F(x,y)`       | <span data-ttu-id="3d917-462">Résolution de surcharge est appliquée pour sélectionner la meilleure méthode `F` dans la classe conteneur ou un struct.</span><span class="sxs-lookup"><span data-stu-id="3d917-462">Overload resolution is applied to select the best method `F` in the containing class or struct.</span></span> <span data-ttu-id="3d917-463">La méthode est appelée avec la liste d’arguments `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-463">The method is invoked with the argument list `(x,y)`.</span></span> <span data-ttu-id="3d917-464">Si la méthode n’est pas `static`, l’expression d’instance est `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-464">If the method is not `static`, the instance expression is `this`.</span></span> | 
|                   | `T.F(x,y)`     | <span data-ttu-id="3d917-465">Résolution de surcharge est appliquée pour sélectionner la meilleure méthode `F` dans la classe ou structure `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-465">Overload resolution is applied to select the best method `F` in the class or struct `T`.</span></span> <span data-ttu-id="3d917-466">Une erreur au moment de la liaison se produit si la méthode n’est pas `static`.</span><span class="sxs-lookup"><span data-stu-id="3d917-466">A binding-time error occurs if the method is not `static`.</span></span> <span data-ttu-id="3d917-467">La méthode est appelée avec la liste d’arguments `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-467">The method is invoked with the argument list `(x,y)`.</span></span> | 
|                   | `e.F(x,y)`     | <span data-ttu-id="3d917-468">Résolution de surcharge est appliquée pour sélectionner la meilleure méthode F dans la classe, un struct ou une interface donnée par le type de `e`.</span><span class="sxs-lookup"><span data-stu-id="3d917-468">Overload resolution is applied to select the best method F in the class, struct, or interface given by the type of `e`.</span></span> <span data-ttu-id="3d917-469">Une erreur au moment de la liaison se produit si la méthode est `static`.</span><span class="sxs-lookup"><span data-stu-id="3d917-469">A binding-time error occurs if the method is `static`.</span></span> <span data-ttu-id="3d917-470">La méthode est appelée avec l’expression d’instance `e` et la liste d’arguments `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-470">The method is invoked with the instance expression `e` and the argument list `(x,y)`.</span></span> | 
| <span data-ttu-id="3d917-471">Accès à la propriété</span><span class="sxs-lookup"><span data-stu-id="3d917-471">Property access</span></span>   | `P`            | <span data-ttu-id="3d917-472">Le `get` accesseur de la propriété `P` dans la classe conteneur ou un struct est appelé.</span><span class="sxs-lookup"><span data-stu-id="3d917-472">The `get` accessor of the property `P` in the containing class or struct is invoked.</span></span> <span data-ttu-id="3d917-473">Une erreur de compilation se produit si `P` est en écriture seule.</span><span class="sxs-lookup"><span data-stu-id="3d917-473">A compile-time error occurs if `P` is write-only.</span></span> <span data-ttu-id="3d917-474">Si `P` n’est pas `static`, l’expression d’instance est `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-474">If `P` is not `static`, the instance expression is `this`.</span></span> | 
|                   | `P = value`    | <span data-ttu-id="3d917-475">Le `set` accesseur de la propriété `P` dans la classe conteneur ou un struct est appelé avec la liste d’arguments `(value)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-475">The `set` accessor of the property `P` in the containing class or struct is invoked with the argument list `(value)`.</span></span> <span data-ttu-id="3d917-476">Une erreur de compilation se produit si `P` est en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="3d917-476">A compile-time error occurs if `P` is read-only.</span></span> <span data-ttu-id="3d917-477">Si `P` n’est pas `static`, l’expression d’instance est `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-477">If `P` is not `static`, the instance expression is `this`.</span></span> | 
|                   | `T.P`          | <span data-ttu-id="3d917-478">Le `get` accesseur de la propriété `P` dans la classe ou structure `T` est appelé.</span><span class="sxs-lookup"><span data-stu-id="3d917-478">The `get` accessor of the property `P` in the class or struct `T` is invoked.</span></span> <span data-ttu-id="3d917-479">Une erreur de compilation se produit si `P` n’est pas `static` ou si `P` est en écriture seule.</span><span class="sxs-lookup"><span data-stu-id="3d917-479">A compile-time error occurs if `P` is not `static` or if `P` is write-only.</span></span> | 
|                   | `T.P = value`  | <span data-ttu-id="3d917-480">Le `set` accesseur de la propriété `P` dans la classe ou structure `T` est appelé avec la liste d’arguments `(value)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-480">The `set` accessor of the property `P` in the class or struct `T` is invoked with the argument list `(value)`.</span></span> <span data-ttu-id="3d917-481">Une erreur de compilation se produit si `P` n’est pas `static` ou si `P` est en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="3d917-481">A compile-time error occurs if `P` is not `static` or if `P` is read-only.</span></span> | 
|                   | `e.P`          | <span data-ttu-id="3d917-482">Le `get` accesseur de la propriété `P` dans la classe, un struct ou une interface donnée par le type de `e` est appelé avec l’expression d’instance `e`.</span><span class="sxs-lookup"><span data-stu-id="3d917-482">The `get` accessor of the property `P` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`.</span></span> <span data-ttu-id="3d917-483">Une erreur au moment de la liaison se produit si `P` est `static` ou si `P` est en écriture seule.</span><span class="sxs-lookup"><span data-stu-id="3d917-483">A binding-time error occurs if `P` is `static` or if `P` is write-only.</span></span> | 
|                   | `e.P = value`  | <span data-ttu-id="3d917-484">Le `set` accesseur de la propriété `P` dans la classe, un struct ou une interface donnée par le type de `e` est appelé avec l’expression d’instance `e` et la liste d’arguments `(value)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-484">The `set` accessor of the property `P` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e` and the argument list `(value)`.</span></span> <span data-ttu-id="3d917-485">Une erreur au moment de la liaison se produit si `P` est `static` ou si `P` est en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="3d917-485">A binding-time error occurs if `P` is `static` or if `P` is read-only.</span></span> | 
| <span data-ttu-id="3d917-486">Accès à l’événement</span><span class="sxs-lookup"><span data-stu-id="3d917-486">Event access</span></span>      | `E += value`   | <span data-ttu-id="3d917-487">Le `add` accesseur de l’événement `E` dans la classe conteneur ou un struct est appelé.</span><span class="sxs-lookup"><span data-stu-id="3d917-487">The `add` accessor of the event `E` in the containing class or struct is invoked.</span></span> <span data-ttu-id="3d917-488">Si `E` est non statique, l’expression d’instance est `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-488">If `E` is not static, the instance expression is `this`.</span></span> | 
|                   | `E -= value`   | <span data-ttu-id="3d917-489">Le `remove` accesseur de l’événement `E` dans la classe conteneur ou un struct est appelé.</span><span class="sxs-lookup"><span data-stu-id="3d917-489">The `remove` accessor of the event `E` in the containing class or struct is invoked.</span></span> <span data-ttu-id="3d917-490">Si `E` est non statique, l’expression d’instance est `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-490">If `E` is not static, the instance expression is `this`.</span></span> | 
|                   | `T.E += value` | <span data-ttu-id="3d917-491">Le `add` accesseur de l’événement `E` dans la classe ou structure `T` est appelé.</span><span class="sxs-lookup"><span data-stu-id="3d917-491">The `add` accessor of the event `E` in the class or struct `T` is invoked.</span></span> <span data-ttu-id="3d917-492">Une erreur au moment de la liaison se produit si `E` n’est pas statique.</span><span class="sxs-lookup"><span data-stu-id="3d917-492">A binding-time error occurs if `E` is not static.</span></span> | 
|                   | `T.E -= value` | <span data-ttu-id="3d917-493">Le `remove` accesseur de l’événement `E` dans la classe ou structure `T` est appelé.</span><span class="sxs-lookup"><span data-stu-id="3d917-493">The `remove` accessor of the event `E` in the class or struct `T` is invoked.</span></span> <span data-ttu-id="3d917-494">Une erreur au moment de la liaison se produit si `E` n’est pas statique.</span><span class="sxs-lookup"><span data-stu-id="3d917-494">A binding-time error occurs if `E` is not static.</span></span> | 
|                   | `e.E += value` | <span data-ttu-id="3d917-495">Le `add` accesseur de l’événement `E` dans la classe, un struct ou une interface donnée par le type de `e` est appelé avec l’expression d’instance `e`.</span><span class="sxs-lookup"><span data-stu-id="3d917-495">The `add` accessor of the event `E` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`.</span></span> <span data-ttu-id="3d917-496">Une erreur au moment de la liaison se produit si `E` est statique.</span><span class="sxs-lookup"><span data-stu-id="3d917-496">A binding-time error occurs if `E` is static.</span></span> | 
|                   | `e.E -= value` | <span data-ttu-id="3d917-497">Le `remove` accesseur de l’événement `E` dans la classe, un struct ou une interface donnée par le type de `e` est appelé avec l’expression d’instance `e`.</span><span class="sxs-lookup"><span data-stu-id="3d917-497">The `remove` accessor of the event `E` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`.</span></span> <span data-ttu-id="3d917-498">Une erreur au moment de la liaison se produit si `E` est statique.</span><span class="sxs-lookup"><span data-stu-id="3d917-498">A binding-time error occurs if `E` is static.</span></span> | 
| <span data-ttu-id="3d917-499">Accès aux indexeurs</span><span class="sxs-lookup"><span data-stu-id="3d917-499">Indexer access</span></span>    | `e[x,y]`       | <span data-ttu-id="3d917-500">Résolution de surcharge est appliquée pour sélectionner le meilleur indexeur dans la classe, un struct ou une interface donné par le type de e.</span><span class="sxs-lookup"><span data-stu-id="3d917-500">Overload resolution is applied to select the best indexer in the class, struct, or interface given by the type of e.</span></span> <span data-ttu-id="3d917-501">Le `get` accesseur de l’indexeur est appelé avec l’expression d’instance `e` et la liste d’arguments `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-501">The `get` accessor of the indexer is invoked with the instance expression `e` and the argument list `(x,y)`.</span></span> <span data-ttu-id="3d917-502">Une erreur au moment de la liaison se produit si l’indexeur est en écriture seule.</span><span class="sxs-lookup"><span data-stu-id="3d917-502">A binding-time error occurs if the indexer is write-only.</span></span> | 
|                   | `e[x,y] = value` | <span data-ttu-id="3d917-503">Résolution de surcharge est appliquée pour sélectionner le meilleur indexeur dans la classe, un struct ou une interface donnée par le type de `e`.</span><span class="sxs-lookup"><span data-stu-id="3d917-503">Overload resolution is applied to select the best indexer in the class, struct, or interface given by the type of `e`.</span></span> <span data-ttu-id="3d917-504">Le `set` accesseur de l’indexeur est appelé avec l’expression d’instance `e` et la liste d’arguments `(x,y,value)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-504">The `set` accessor of the indexer is invoked with the instance expression `e` and the argument list `(x,y,value)`.</span></span> <span data-ttu-id="3d917-505">Une erreur au moment de la liaison se produit si l’indexeur est en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="3d917-505">A binding-time error occurs if the indexer is read-only.</span></span> | 
| <span data-ttu-id="3d917-506">Appel d’opérateur</span><span class="sxs-lookup"><span data-stu-id="3d917-506">Operator invocation</span></span> | `-x`         | <span data-ttu-id="3d917-507">Résolution de surcharge est appliquée pour sélectionner le meilleur opérateur unaire dans la classe ou le struct donné par le type de `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-507">Overload resolution is applied to select the best unary operator in the class or struct given by the type of `x`.</span></span> <span data-ttu-id="3d917-508">L’opérateur sélectionné est appelé avec la liste d’arguments `(x)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-508">The selected operator is invoked with the argument list `(x)`.</span></span> | 
|                     | `x + y`      | <span data-ttu-id="3d917-509">Résolution de surcharge est appliquée pour sélectionner le meilleur opérateur binaire dans les classes ou les structs donnés par les types de `x` et `y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-509">Overload resolution is applied to select the best binary operator in the classes or structs given by the types of `x` and `y`.</span></span> <span data-ttu-id="3d917-510">L’opérateur sélectionné est appelé avec la liste d’arguments `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-510">The selected operator is invoked with the argument list `(x,y)`.</span></span> | 
| <span data-ttu-id="3d917-511">Appel de constructeur d’instance</span><span class="sxs-lookup"><span data-stu-id="3d917-511">Instance constructor invocation</span></span> | `new T(x,y)` | <span data-ttu-id="3d917-512">Résolution de surcharge est appliquée pour sélectionner le meilleur constructeur d’instance dans la classe ou structure `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-512">Overload resolution is applied to select the best instance constructor in the class or struct `T`.</span></span> <span data-ttu-id="3d917-513">Le constructeur d’instance est appelé avec la liste d’arguments `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-513">The instance constructor is invoked with the argument list `(x,y)`.</span></span> | 

### <a name="argument-lists"></a><span data-ttu-id="3d917-514">Listes d’arguments</span><span class="sxs-lookup"><span data-stu-id="3d917-514">Argument lists</span></span>

<span data-ttu-id="3d917-515">Chaque fonction membre et appel de délégué inclut une liste d’arguments qui fournit les valeurs réelles ou des références variables pour les paramètres de la fonction membre.</span><span class="sxs-lookup"><span data-stu-id="3d917-515">Every function member and delegate invocation includes an argument list which provides actual values or variable references for the parameters of the function member.</span></span> <span data-ttu-id="3d917-516">La syntaxe de spécification de la liste d’arguments d’un appel de fonction membre dépend de la catégorie de membre de fonction :</span><span class="sxs-lookup"><span data-stu-id="3d917-516">The syntax for specifying the argument list of a function member invocation depends on the function member category:</span></span>

*  <span data-ttu-id="3d917-517">Par exemple, les constructeurs, méthodes, indexeurs et délégués, les arguments sont spécifiés comme un *argument_list*, comme décrit ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3d917-517">For instance constructors, methods, indexers and delegates, the arguments are specified as an *argument_list*, as described below.</span></span> <span data-ttu-id="3d917-518">Pour les indexeurs, lors de l’appel le `set` accesseur, la liste d’arguments inclut en outre l’expression spécifiée comme opérande de droite de l’opérateur d’assignation.</span><span class="sxs-lookup"><span data-stu-id="3d917-518">For indexers, when invoking the `set` accessor, the argument list additionally includes the expression specified as the right operand of the assignment operator.</span></span>
*  <span data-ttu-id="3d917-519">Pour les propriétés, la liste d’arguments est vide lors de l’appel le `get` accesseur et se compose de l’expression spécifiée en tant qu’opérande de droite de l’opérateur d’assignation lors de l’appel le `set` accesseur.</span><span class="sxs-lookup"><span data-stu-id="3d917-519">For properties, the argument list is empty when invoking the `get` accessor, and consists of the expression specified as the right operand of the assignment operator when invoking the `set` accessor.</span></span>
*  <span data-ttu-id="3d917-520">Pour les événements, la liste d’arguments se compose de l’expression spécifiée en tant qu’opérande de droite de la `+=` ou `-=` opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-520">For events, the argument list consists of the expression specified as the right operand of the `+=` or `-=` operator.</span></span>
*  <span data-ttu-id="3d917-521">Opérateurs définis par l’utilisateur, la liste d’arguments se compose de l’opérande unique de l’opérateur unaire ou les deux opérandes de l’opérateur binaire.</span><span class="sxs-lookup"><span data-stu-id="3d917-521">For user-defined operators, the argument list consists of the single operand of the unary operator or the two operands of the binary operator.</span></span>

<span data-ttu-id="3d917-522">Les arguments des propriétés ([propriétés](classes.md#properties)), événements ([événements](classes.md#events)) et les opérateurs définis par l’utilisateur ([opérateurs](classes.md#operators)) sont toujours transmis en tant que paramètres de valeur ([ Paramètres de valeur](classes.md#value-parameters)).</span><span class="sxs-lookup"><span data-stu-id="3d917-522">The arguments of properties ([Properties](classes.md#properties)), events ([Events](classes.md#events)), and user-defined operators ([Operators](classes.md#operators)) are always passed as value parameters ([Value parameters](classes.md#value-parameters)).</span></span> <span data-ttu-id="3d917-523">Les arguments des indexeurs ([indexeurs](classes.md#indexers)) sont toujours transmis en tant que paramètres de valeur ([paramètres de valeur](classes.md#value-parameters)) ou des tableaux de paramètres ([tableaux de paramètres](classes.md#parameter-arrays)).</span><span class="sxs-lookup"><span data-stu-id="3d917-523">The arguments of indexers ([Indexers](classes.md#indexers)) are always passed as value parameters ([Value parameters](classes.md#value-parameters)) or parameter arrays ([Parameter arrays](classes.md#parameter-arrays)).</span></span> <span data-ttu-id="3d917-524">Paramètres de référence et de sortie ne sont pas pris en charge pour ces catégories de fonctions membres.</span><span class="sxs-lookup"><span data-stu-id="3d917-524">Reference and output parameters are not supported for these categories of function members.</span></span>

<span data-ttu-id="3d917-525">Les arguments d’un appel de constructeur, de méthode, indexeur ou délégué instance sont spécifiés comme un *argument_list*:</span><span class="sxs-lookup"><span data-stu-id="3d917-525">The arguments of an instance constructor, method, indexer or delegate invocation are specified as an *argument_list*:</span></span>

```antlr
argument_list
    : argument (',' argument)*
    ;

argument
    : argument_name? argument_value
    ;

argument_name
    : identifier ':'
    ;

argument_value
    : expression
    | 'ref' variable_reference
    | 'out' variable_reference
    ;
```

<span data-ttu-id="3d917-526">Un *argument_list* se compose d’une ou plusieurs *argument*s, séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="3d917-526">An *argument_list* consists of one or more *argument*s, separated by commas.</span></span> <span data-ttu-id="3d917-527">Chaque argument se compose d’un texte facultatif *nom_argument* suivie d’une *argument_value*.</span><span class="sxs-lookup"><span data-stu-id="3d917-527">Each argument consists of an optional  *argument_name* followed by an *argument_value*.</span></span> <span data-ttu-id="3d917-528">Un *argument* avec un *nom_argument* est appelé un ***argument nommé***, tandis qu’un *argument* sans un  *nom_argument* est un ***argument positionnel***.</span><span class="sxs-lookup"><span data-stu-id="3d917-528">An *argument* with an *argument_name* is referred to as a ***named argument***, whereas an *argument* without an *argument_name* is a ***positional argument***.</span></span> <span data-ttu-id="3d917-529">C’est une erreur pour un argument positionnel à apparaître après un argument nommé dans une *argument_list*.</span><span class="sxs-lookup"><span data-stu-id="3d917-529">It is an error for a positional argument to appear after a named argument in an *argument_list*.</span></span>

<span data-ttu-id="3d917-530">Le *argument_value* peut prendre l’une des formes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-530">The *argument_value* can take one of the following forms:</span></span>

*  <span data-ttu-id="3d917-531">Un *expression*, ce qui indique que l’argument est passé comme un paramètre de valeur ([paramètres de valeur](classes.md#value-parameters)).</span><span class="sxs-lookup"><span data-stu-id="3d917-531">An *expression*, indicating that the argument is passed as a value parameter ([Value parameters](classes.md#value-parameters)).</span></span>
*  <span data-ttu-id="3d917-532">Le mot clé `ref` suivie d’un *variable_reference* ([références de Variable](variables.md#variable-references)), ce qui indique que l’argument est passé comme paramètre de référence ([paramètres de référence ](classes.md#reference-parameters)).</span><span class="sxs-lookup"><span data-stu-id="3d917-532">The keyword `ref` followed by a *variable_reference* ([Variable references](variables.md#variable-references)), indicating that the argument is passed as a reference parameter ([Reference parameters](classes.md#reference-parameters)).</span></span> <span data-ttu-id="3d917-533">Une variable doit absolument être assignée ([assignation définie](variables.md#definite-assignment)) avant de pouvoir être passée comme paramètre de référence.</span><span class="sxs-lookup"><span data-stu-id="3d917-533">A variable must be definitely assigned ([Definite assignment](variables.md#definite-assignment)) before it can be passed as a reference parameter.</span></span> <span data-ttu-id="3d917-534">Le mot clé `out` suivie d’un *variable_reference* ([références de Variable](variables.md#variable-references)), ce qui indique que l’argument est passé comme paramètre de sortie ([deparamètresdesortie](classes.md#output-parameters)).</span><span class="sxs-lookup"><span data-stu-id="3d917-534">The keyword `out` followed by a *variable_reference* ([Variable references](variables.md#variable-references)), indicating that the argument is passed as an output parameter ([Output parameters](classes.md#output-parameters)).</span></span> <span data-ttu-id="3d917-535">Une variable est considéré comme définitivement assignée ([assignation définie](variables.md#definite-assignment)) après l’appel d’une fonction membre dans lequel la variable est passée comme paramètre de sortie.</span><span class="sxs-lookup"><span data-stu-id="3d917-535">A variable is considered definitely assigned ([Definite assignment](variables.md#definite-assignment)) following a function member invocation in which the variable is passed as an output parameter.</span></span>

#### <a name="corresponding-parameters"></a><span data-ttu-id="3d917-536">Paramètres correspondants</span><span class="sxs-lookup"><span data-stu-id="3d917-536">Corresponding parameters</span></span>

<span data-ttu-id="3d917-537">Pour chaque argument dans une liste d’arguments il doit être un paramètre correspondant dans le membre de fonction ou un délégué appelé.</span><span class="sxs-lookup"><span data-stu-id="3d917-537">For each argument in an argument list there has to be a corresponding parameter in the function member or delegate being invoked.</span></span>

<span data-ttu-id="3d917-538">La liste des paramètres utilisée dans le code suivant est déterminée comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-538">The parameter list used in the following is determined as follows:</span></span>

*  <span data-ttu-id="3d917-539">Pour les méthodes virtuelles et des indexeurs définis dans les classes, la liste de paramètres est récupéré à partir de la déclaration de la plus spécifique ou la substitution de la fonction membre, en commençant par le type statique du récepteur et la recherche dans ses classes de base.</span><span class="sxs-lookup"><span data-stu-id="3d917-539">For virtual methods and indexers defined in classes, the parameter list is picked from the most specific declaration or override of the function member, starting with the static type of the receiver, and searching through its base classes.</span></span>
*  <span data-ttu-id="3d917-540">Pour les méthodes d’interface et indexeurs, la liste de paramètres est prélevée forment la définition la plus spécifique du membre, en commençant par le type d’interface et la recherche dans les interfaces de base.</span><span class="sxs-lookup"><span data-stu-id="3d917-540">For interface methods and indexers, the parameter list is picked form the most specific definition of the member, starting with the interface type and searching through the base interfaces.</span></span> <span data-ttu-id="3d917-541">Si aucune liste de paramètres unique n’est trouvé, une liste de paramètres avec les noms sont inaccessibles et aucun paramètre facultatif est construite, afin que les appels ne peut pas utiliser des paramètres nommés ou omettre les arguments facultatifs.</span><span class="sxs-lookup"><span data-stu-id="3d917-541">If no unique parameter list is found, a parameter list with inaccessible names and no optional parameters is constructed, so that invocations cannot use named parameters or omit optional arguments.</span></span>
*  <span data-ttu-id="3d917-542">Pour les méthodes partielles, la liste des paramètres de la déclaration de méthode partielle de définition est utilisée.</span><span class="sxs-lookup"><span data-stu-id="3d917-542">For partial methods, the parameter list of the defining partial method declaration is used.</span></span>
*  <span data-ttu-id="3d917-543">Pour toutes les autres fonctions membres et les délégués, il existe uniquement une liste de paramètres unique, qui est celui utilisé.</span><span class="sxs-lookup"><span data-stu-id="3d917-543">For all other function members and delegates there is only a single parameter list, which is the one used.</span></span>

<span data-ttu-id="3d917-544">La position d’un argument ou un paramètre est définie comme le nombre d’arguments ou paramètres précèdent dans la liste d’arguments ou d’une liste de paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-544">The position of an argument or parameter is defined as the number of arguments or parameters preceding it in the argument list or parameter list.</span></span>

<span data-ttu-id="3d917-545">Les paramètres correspondants pour les arguments de fonction membre sont établies comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-545">The corresponding parameters for function member arguments are established as follows:</span></span>

*  <span data-ttu-id="3d917-546">Arguments dans le *argument_list* de constructeurs d’instance, méthodes, indexeurs et délégués :</span><span class="sxs-lookup"><span data-stu-id="3d917-546">Arguments in the *argument_list* of instance constructors, methods, indexers and delegates:</span></span>
    * <span data-ttu-id="3d917-547">Un argument positionnel où un paramètre fixe se produit à la même position dans la liste de paramètres correspond à ce paramètre.</span><span class="sxs-lookup"><span data-stu-id="3d917-547">A positional argument where a fixed parameter occurs at the same position in the parameter list corresponds to that parameter.</span></span>
    * <span data-ttu-id="3d917-548">Un argument positionnel d’une fonction membre avec un tableau de paramètres appelé dans sa forme normale correspond au tableau de paramètres, qui doit se produire à la même position dans la liste de paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-548">A positional argument of a function member with a parameter array invoked in its normal form corresponds to the parameter  array, which must occur at the same position in the parameter list.</span></span>
    * <span data-ttu-id="3d917-549">Un argument positionnel d’une fonction membre avec un tableau de paramètres appelé dans sa forme étendue, où aucun paramètre fixe se produit à la même position dans la liste de paramètres, correspond à un élément dans le tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-549">A positional argument of a function member with a parameter array invoked in its expanded form, where no fixed parameter occurs at the same position in the parameter list, corresponds to an element in the parameter array.</span></span>
    * <span data-ttu-id="3d917-550">Un argument nommé correspond au paramètre du même nom dans la liste de paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-550">A named argument corresponds to the parameter of the same name in the parameter list.</span></span>
    * <span data-ttu-id="3d917-551">Pour les indexeurs, lors de l’appel le `set` accesseur, l’expression spécifiée comme opérande de droite de l’opérateur d’assignation correspond à l’implicite `value` paramètre de la `set` déclaration d’accesseur.</span><span class="sxs-lookup"><span data-stu-id="3d917-551">For indexers, when invoking the `set` accessor, the expression specified as the right operand of the assignment operator corresponds to the implicit `value` parameter of the `set` accessor declaration.</span></span>
*  <span data-ttu-id="3d917-552">Pour les propriétés, lors de l’appel le `get` accesseur il y a aucun argument.</span><span class="sxs-lookup"><span data-stu-id="3d917-552">For properties, when invoking the `get` accessor there are no arguments.</span></span> <span data-ttu-id="3d917-553">Lors de l’appel le `set` accesseur, l’expression spécifiée comme opérande de droite de l’opérateur d’assignation correspond à l’implicite `value` paramètre de la `set` déclaration d’accesseur.</span><span class="sxs-lookup"><span data-stu-id="3d917-553">When invoking the `set` accessor, the expression specified as the right operand of the assignment operator corresponds to the implicit `value` parameter of the `set` accessor declaration.</span></span>
*  <span data-ttu-id="3d917-554">Pour les opérateurs unaire défini par l’utilisateur (y compris les conversions), l’opérande unique correspond au paramètre unique de la déclaration d’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-554">For user-defined unary operators (including conversions), the single operand corresponds to the single parameter of the operator declaration.</span></span>
*  <span data-ttu-id="3d917-555">Pour les opérateurs binaires définis par l’utilisateur, l’opérande gauche correspond au premier paramètre, et l’opérande de droite correspond au second paramètre de la déclaration d’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-555">For user-defined binary operators, the left operand corresponds to the first parameter, and the right operand corresponds to the second parameter of the operator declaration.</span></span>

#### <a name="run-time-evaluation-of-argument-lists"></a><span data-ttu-id="3d917-556">Version d’évaluation de l’exécution de listes d’arguments</span><span class="sxs-lookup"><span data-stu-id="3d917-556">Run-time evaluation of argument lists</span></span>

<span data-ttu-id="3d917-557">Pendant le traitement de l’exécution d’un appel de fonction membre ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), les expressions ou les références de variable d’une liste d’arguments sont évalués dans l’ordre, de gauche à droite, en tant que suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-557">During the run-time processing of a function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), the expressions or variable references of an argument list are evaluated in order, from left to right, as follows:</span></span>

*  <span data-ttu-id="3d917-558">Pour un paramètre de valeur, l’expression d’argument est évaluée et une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) au paramètre correspondant type est effectué.</span><span class="sxs-lookup"><span data-stu-id="3d917-558">For a value parameter, the argument expression is evaluated and an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) to the corresponding parameter type is performed.</span></span> <span data-ttu-id="3d917-559">La valeur résultante devient la valeur initiale du paramètre de valeur dans l’appel de fonction membre.</span><span class="sxs-lookup"><span data-stu-id="3d917-559">The resulting value becomes the initial value of the value parameter in the function member invocation.</span></span>
*  <span data-ttu-id="3d917-560">Pour un paramètre de référence ou de sortie, la référence variable est évaluée et l’emplacement de stockage résultant devient l’emplacement de stockage représenté par le paramètre dans l’appel de fonction membre.</span><span class="sxs-lookup"><span data-stu-id="3d917-560">For a reference or output parameter, the variable reference is evaluated and the resulting storage location becomes the storage location represented by the parameter in the function member invocation.</span></span> <span data-ttu-id="3d917-561">Si la référence de variable donnée comme un paramètre de référence ou de sortie est un élément d’un *reference_type*, une vérification de l’exécution est effectuée pour vous assurer que le type d’élément du tableau est identique au type du paramètre.</span><span class="sxs-lookup"><span data-stu-id="3d917-561">If the variable reference given as a reference or output parameter is an array element of a *reference_type*, a run-time check is performed to ensure that the element type of the array is identical to the type of the parameter.</span></span> <span data-ttu-id="3d917-562">Si cette vérification échoue, un `System.ArrayTypeMismatchException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-562">If this check fails, a `System.ArrayTypeMismatchException` is thrown.</span></span>

<span data-ttu-id="3d917-563">Méthodes, les indexeurs et les constructeurs d’instance peuvent déclarer leur paramètre le plus à droite pour être un tableau de paramètres ([tableaux de paramètres](classes.md#parameter-arrays)).</span><span class="sxs-lookup"><span data-stu-id="3d917-563">Methods, indexers, and instance constructors may declare their right-most parameter to be a parameter array ([Parameter arrays](classes.md#parameter-arrays)).</span></span> <span data-ttu-id="3d917-564">Ces fonctions membres sont appelés dans leur forme normale ou dans leur forme étendue selon laquelle s’applique ([membre de fonction Applicable](expressions.md#applicable-function-member)) :</span><span class="sxs-lookup"><span data-stu-id="3d917-564">Such function members are invoked either in their normal form or in their expanded form depending on which is applicable ([Applicable function member](expressions.md#applicable-function-member)):</span></span>

*  <span data-ttu-id="3d917-565">Lorsqu’une fonction membre avec un tableau de paramètres est appelée dans sa forme normale, l’argument fourni pour le tableau de paramètres doit être une expression unique qui est implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) pour le type de tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-565">When a function member with a parameter array is invoked in its normal form, the argument given for the parameter array must be a single expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the parameter array type.</span></span> <span data-ttu-id="3d917-566">Dans ce cas, le tableau de paramètres opère précisément comme un paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-566">In this case, the parameter array acts precisely like a value parameter.</span></span>
*  <span data-ttu-id="3d917-567">Lorsqu’une fonction membre avec un tableau de paramètres est appelée dans sa forme étendue, l’appel doit spécifier zéro ou plusieurs arguments de position pour le tableau de paramètres, où chaque argument est une expression qui est implicitement convertible ([implicite conversions](conversions.md#implicit-conversions)) pour le type d’élément de tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-567">When a function member with a parameter array is invoked in its expanded form, the invocation must specify zero or more positional arguments for the parameter array, where each argument is an expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the element type of the parameter array.</span></span> <span data-ttu-id="3d917-568">Dans ce cas, l’appel crée une instance de type tableau de paramètres d’une longueur correspondant au nombre d’arguments, initialise les éléments de l’instance de tableau avec les valeurs d’argument donné et utilise l’instance de tableau qui vient d’être créé en tant que le texte réel argument.</span><span class="sxs-lookup"><span data-stu-id="3d917-568">In this case, the invocation creates an instance of the parameter array type with a length corresponding to the number of arguments, initializes the elements of the array instance with the given argument values, and uses the newly created array instance as the actual argument.</span></span>

<span data-ttu-id="3d917-569">Les expressions d’une liste d’arguments sont toujours évaluées dans l’ordre qu’ils sont écrits.</span><span class="sxs-lookup"><span data-stu-id="3d917-569">The expressions of an argument list are always evaluated in the order they are written.</span></span> <span data-ttu-id="3d917-570">Par conséquent, l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-570">Thus, the example</span></span>
```csharp
class Test
{
    static void F(int x, int y = -1, int z = -2) {
        System.Console.WriteLine("x = {0}, y = {1}, z = {2}", x, y, z);
    }

    static void Main() {
        int i = 0;
        F(i++, i++, i++);
        F(z: i++, x: i++);
    }
}
```
<span data-ttu-id="3d917-571">génère la sortie</span><span class="sxs-lookup"><span data-stu-id="3d917-571">produces the output</span></span>
```
x = 0, y = 1, z = 2
x = 4, y = -1, z = 3
```

<span data-ttu-id="3d917-572">Les règles de variance co tableau ([covariance de tableau](arrays.md#array-covariance)) permettent à une valeur d’un type tableau `A[]` soit une référence à une instance d’un type tableau `B[]`, à condition qu’existe une conversion de référence implicite de `B` à `A`.</span><span class="sxs-lookup"><span data-stu-id="3d917-572">The array co-variance rules ([Array covariance](arrays.md#array-covariance)) permit a value of an array type `A[]` to be a reference to an instance of an array type `B[]`, provided an implicit reference conversion exists from `B` to `A`.</span></span> <span data-ttu-id="3d917-573">En raison de ces règles, lorsqu’elles sont un élément d’un *reference_type* est passé comme paramètre de référence ou de sortie, une vérification de l’exécution est nécessaire pour garantir que le type réel des éléments du tableau est identique à celui du paramètre.</span><span class="sxs-lookup"><span data-stu-id="3d917-573">Because of these rules, when an array element of a *reference_type* is passed as a reference or output parameter, a run-time check is required to ensure that the actual element type of the array is identical to that of the parameter.</span></span> <span data-ttu-id="3d917-574">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-574">In the example</span></span>
```csharp
class Test
{
    static void F(ref object x) {...}

    static void Main() {
        object[] a = new object[10];
        object[] b = new string[10];
        F(ref a[0]);        // Ok
        F(ref b[1]);        // ArrayTypeMismatchException
    }
}
```
<span data-ttu-id="3d917-575">le deuxième appel de `F` provoque un `System.ArrayTypeMismatchException` levée, car le type de l’élément réel de `b` est `string` et non `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-575">the second invocation of `F` causes a `System.ArrayTypeMismatchException` to be thrown because the actual element type of `b` is `string` and not `object`.</span></span>

<span data-ttu-id="3d917-576">Lorsqu’une fonction membre avec un tableau de paramètres est appelée dans sa forme étendue, l’appel est traité exactement comme si une expression de création de tableau avec un initialiseur de tableau ([expressions de création de tableau](expressions.md#array-creation-expressions)) a été inséré autour de la paramètres de l’étendue.</span><span class="sxs-lookup"><span data-stu-id="3d917-576">When a function member with a parameter array is invoked in its expanded form, the invocation is processed exactly as if an array creation expression with an array initializer ([Array creation expressions](expressions.md#array-creation-expressions)) was inserted around the expanded parameters.</span></span> <span data-ttu-id="3d917-577">Par exemple, prenons la déclaration</span><span class="sxs-lookup"><span data-stu-id="3d917-577">For example, given the declaration</span></span>
```csharp
void F(int x, int y, params object[] args);
```
<span data-ttu-id="3d917-578">les appels suivants de la forme étendue de la méthode</span><span class="sxs-lookup"><span data-stu-id="3d917-578">the following invocations of the expanded form of the method</span></span>
```csharp
F(10, 20);
F(10, 20, 30, 40);
F(10, 20, 1, "hello", 3.0);
```
<span data-ttu-id="3d917-579">correspondent exactement à</span><span class="sxs-lookup"><span data-stu-id="3d917-579">correspond exactly to</span></span>
```csharp
F(10, 20, new object[] {});
F(10, 20, new object[] {30, 40});
F(10, 20, new object[] {1, "hello", 3.0});
```

<span data-ttu-id="3d917-580">En particulier, notez qu’un tableau vide est créé lorsqu’il y a aucun argument spécifié pour le tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-580">In particular, note that an empty array is created when there are zero arguments given for the parameter array.</span></span>

<span data-ttu-id="3d917-581">Lorsque les arguments sont omis à partir d’une fonction membre avec correspondante des paramètres facultatifs, les arguments par défaut de la déclaration de membre de fonction sont implicitement passés.</span><span class="sxs-lookup"><span data-stu-id="3d917-581">When arguments are omitted from a function member with corresponding optional parameters, the default arguments of the function member declaration are implicitly passed.</span></span> <span data-ttu-id="3d917-582">Car il s’agit toujours constante, leur évaluation n’affecte pas l’ordre d’évaluation des autres arguments.</span><span class="sxs-lookup"><span data-stu-id="3d917-582">Because these are always constant, their evaluation will not impact the evaluation order of the remaining arguments.</span></span>

### <a name="type-inference"></a><span data-ttu-id="3d917-583">Inférence de type</span><span class="sxs-lookup"><span data-stu-id="3d917-583">Type inference</span></span>

<span data-ttu-id="3d917-584">Lorsqu’une méthode générique est appelée sans spécifier les arguments de type, un ***l’inférence de type*** processus tente de déduire les arguments de type pour l’appel.</span><span class="sxs-lookup"><span data-stu-id="3d917-584">When a generic method is called without specifying type arguments, a ***type inference*** process attempts to infer type arguments for the call.</span></span> <span data-ttu-id="3d917-585">La présence de l’inférence de type permet une syntaxe plus pratique à utiliser pour appeler une méthode générique et permet au programmeur d’éviter de spécifier des informations de type redondant.</span><span class="sxs-lookup"><span data-stu-id="3d917-585">The presence of type inference allows a more convenient syntax to be used for calling a generic method, and allows the programmer to avoid specifying redundant type information.</span></span> <span data-ttu-id="3d917-586">Par exemple, étant donné la déclaration de méthode :</span><span class="sxs-lookup"><span data-stu-id="3d917-586">For example, given the method declaration:</span></span>
```csharp
class Chooser
{
    static Random rand = new Random();

    public static T Choose<T>(T first, T second) {
        return (rand.Next(2) == 0)? first: second;
    }
}
```
<span data-ttu-id="3d917-587">Il est possible d’appeler le `Choose` méthode sans spécifier explicitement un argument de type :</span><span class="sxs-lookup"><span data-stu-id="3d917-587">it is possible to invoke the `Choose` method without explicitly specifying a type argument:</span></span>
```csharp
int i = Chooser.Choose(5, 213);                 // Calls Choose<int>

string s = Chooser.Choose("foo", "bar");        // Calls Choose<string>
```

<span data-ttu-id="3d917-588">Via l’inférence de type, les arguments de type `int` et `string` sont déterminées à partir des arguments à la méthode.</span><span class="sxs-lookup"><span data-stu-id="3d917-588">Through type inference, the type arguments `int` and `string` are determined from the arguments to the method.</span></span>

<span data-ttu-id="3d917-589">Inférence de type se produit dans le cadre du traitement du moment de la liaison d’un appel de méthode ([appels de méthode](expressions.md#method-invocations)) et intervient avant l’étape de résolution de surcharge de l’appel.</span><span class="sxs-lookup"><span data-stu-id="3d917-589">Type inference occurs as part of the binding-time processing of a method invocation ([Method invocations](expressions.md#method-invocations)) and takes place before the overload resolution step of the invocation.</span></span> <span data-ttu-id="3d917-590">Quand un groupe de méthode particulière est spécifié dans un appel de méthode et sans arguments de type sont spécifiés dans le cadre de l’appel de méthode, l’inférence de type est appliquée à chaque méthode générique, dans le groupe de méthodes.</span><span class="sxs-lookup"><span data-stu-id="3d917-590">When a particular method group is specified in a method invocation, and no type arguments are specified as part of the method invocation, type inference is applied to each generic method in the method group.</span></span> <span data-ttu-id="3d917-591">Si l’inférence de type réussit, les arguments de type déduits sont utilisés pour déterminer les types d’arguments pour la résolution de surcharge suivantes.</span><span class="sxs-lookup"><span data-stu-id="3d917-591">If type inference succeeds, then the inferred type arguments are used to determine the types of arguments for subsequent overload resolution.</span></span> <span data-ttu-id="3d917-592">Si la résolution de surcharge choisit une méthode générique que celui à appeler, les arguments de type déduits sont utilisés comme arguments de type réel pour l’appel.</span><span class="sxs-lookup"><span data-stu-id="3d917-592">If overload resolution chooses a generic method as the one to invoke, then the inferred type arguments are used as the actual type arguments for the invocation.</span></span> <span data-ttu-id="3d917-593">Cas d’échec de l’inférence de type pour une méthode particulière, cette méthode ne participe pas la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="3d917-593">If type inference for a particular method fails, that method does not participate in overload resolution.</span></span> <span data-ttu-id="3d917-594">L’échec de l’inférence de type elle-même, n’entraîne pas une erreur au moment de la liaison.</span><span class="sxs-lookup"><span data-stu-id="3d917-594">The failure of type inference, in and of itself, does not cause a binding-time error.</span></span> <span data-ttu-id="3d917-595">Toutefois, il conduit souvent à une erreur de liaison lors de la résolution de surcharge puis ne trouve pas de toutes les méthodes applicables.</span><span class="sxs-lookup"><span data-stu-id="3d917-595">However, it often leads to a binding-time error when overload resolution then fails to find any applicable methods.</span></span>

<span data-ttu-id="3d917-596">Si le nombre d’arguments fournis est différent du nombre de paramètres dans la méthode, l’inférence échoue immédiatement.</span><span class="sxs-lookup"><span data-stu-id="3d917-596">If the supplied number of arguments is different than the number of parameters in the method, then inference immediately fails.</span></span> <span data-ttu-id="3d917-597">Sinon, on suppose que la méthode générique a la signature suivante :</span><span class="sxs-lookup"><span data-stu-id="3d917-597">Otherwise, assume that the generic method has the following signature:</span></span>
```csharp
Tr M<X1,...,Xn>(T1 x1, ..., Tm xm)
```

<span data-ttu-id="3d917-598">Avec un appel de méthode sous la forme `M(E1...Em)` la tâche d’inférence de type consiste à trouver les arguments de type unique `S1...Sn` pour chacun des paramètres de type `X1...Xn` afin que l’appel `M<S1...Sn>(E1...Em)` devient valide.</span><span class="sxs-lookup"><span data-stu-id="3d917-598">With a method call of the form `M(E1...Em)` the task of type inference is to find unique type arguments `S1...Sn` for each of the type parameters `X1...Xn` so that the call `M<S1...Sn>(E1...Em)` becomes valid.</span></span>

<span data-ttu-id="3d917-599">Pendant le processus d’inférence de chaque paramètre de type `Xi` est soit *fixe* à un type particulier `Si` ou *non fixed* avec un ensemble associé de *limites*.</span><span class="sxs-lookup"><span data-stu-id="3d917-599">During the process of inference each type parameter `Xi` is either *fixed* to a particular type `Si` or *unfixed* with an associated set of *bounds*.</span></span> <span data-ttu-id="3d917-600">Chacune des limites est un type `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-600">Each of the bounds is some type `T`.</span></span> <span data-ttu-id="3d917-601">Chaque variable de type initialement `Xi` est corrigé avec un ensemble de limites vide.</span><span class="sxs-lookup"><span data-stu-id="3d917-601">Initially each type variable `Xi` is unfixed with an empty set of bounds.</span></span>

<span data-ttu-id="3d917-602">Inférence de type a lieu en plusieurs phases.</span><span class="sxs-lookup"><span data-stu-id="3d917-602">Type inference takes place in phases.</span></span> <span data-ttu-id="3d917-603">Chaque phase essaiera de déduire les arguments de type pour les variables de type supplémentaires en fonction des résultats de la phase précédente.</span><span class="sxs-lookup"><span data-stu-id="3d917-603">Each phase will try to infer type arguments for more type variables based on the findings of the previous phase.</span></span> <span data-ttu-id="3d917-604">La première phase rend certains des inférences initiales des limites, tandis que la deuxième phase résout les variables de type pour des types spécifiques et déduit davantage de limites.</span><span class="sxs-lookup"><span data-stu-id="3d917-604">The first phase makes some initial inferences of bounds, whereas the second phase fixes type variables to specific types and infers further bounds.</span></span> <span data-ttu-id="3d917-605">La deuxième phase peut-être être répétée un nombre de fois.</span><span class="sxs-lookup"><span data-stu-id="3d917-605">The second phase may have to be repeated a number of times.</span></span>

<span data-ttu-id="3d917-606">*Remarque :* Inférence type intervient non seulement quand une méthode générique est appelée.</span><span class="sxs-lookup"><span data-stu-id="3d917-606">*Note:* Type inference takes place not only when a generic method is called.</span></span> <span data-ttu-id="3d917-607">Inférence de type pour la conversion de groupes de méthodes est décrite dans [inférence pour la conversion de groupes de méthodes de Type](expressions.md#type-inference-for-conversion-of-method-groups) et recherche le meilleur type courantes d’un ensemble d’expressions est décrite dans [recherche le meilleur type courantes d’un jeu des expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions).</span><span class="sxs-lookup"><span data-stu-id="3d917-607">Type inference for conversion of method groups is described in [Type inference for conversion of method groups](expressions.md#type-inference-for-conversion-of-method-groups) and finding the best common type of a set of expressions is described in [Finding the best common type of a set of expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions).</span></span>

#### <a name="the-first-phase"></a><span data-ttu-id="3d917-608">La première phase</span><span class="sxs-lookup"><span data-stu-id="3d917-608">The first phase</span></span>

<span data-ttu-id="3d917-609">Pour chacun des arguments de la méthode `Ei`:</span><span class="sxs-lookup"><span data-stu-id="3d917-609">For each of the method arguments `Ei`:</span></span>

*   <span data-ttu-id="3d917-610">Si `Ei` est une fonction anonyme, un *inférence de type de paramètre explicite* ([des inférences de type de paramètre explicite](expressions.md#explicit-parameter-type-inferences)) est effectuée à partir de `Ei` à `Ti`</span><span class="sxs-lookup"><span data-stu-id="3d917-610">If `Ei` is an anonymous function, an *explicit parameter type inference* ([Explicit parameter type inferences](expressions.md#explicit-parameter-type-inferences)) is made from `Ei` to `Ti`</span></span>
*   <span data-ttu-id="3d917-611">Sinon, si `Ei` a un type `U` et `xi` est un paramètre de valeur un *inférence de limite inférieure* est effectuée *de* `U` *à* `Ti`.</span><span class="sxs-lookup"><span data-stu-id="3d917-611">Otherwise, if `Ei` has a type `U` and `xi` is a value parameter then a *lower-bound inference* is made *from* `U` *to* `Ti`.</span></span>
*   <span data-ttu-id="3d917-612">Sinon, si `Ei` a un type `U` et `xi` est un `ref` ou `out` paramètre une *exacte inférence* est effectuée *à partir de* `U` *à* `Ti`.</span><span class="sxs-lookup"><span data-stu-id="3d917-612">Otherwise, if `Ei` has a type `U` and `xi` is a `ref` or `out` parameter then an *exact inference* is made *from* `U` *to* `Ti`.</span></span>
*   <span data-ttu-id="3d917-613">Sinon, aucune inférence est effectuée pour cet argument.</span><span class="sxs-lookup"><span data-stu-id="3d917-613">Otherwise, no inference is made for this argument.</span></span>


#### <a name="the-second-phase"></a><span data-ttu-id="3d917-614">La deuxième phase</span><span class="sxs-lookup"><span data-stu-id="3d917-614">The second phase</span></span>

<span data-ttu-id="3d917-615">La deuxième phase se déroule comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-615">The second phase proceeds as follows:</span></span>

*   <span data-ttu-id="3d917-616">Tous les *non fixed* variables de type `Xi` qui n’est pas le cas *dépendent* ([dépendance](expressions.md#dependence)) n’importe quel `Xj` sont fixes ([fixation](expressions.md#fixing)).</span><span class="sxs-lookup"><span data-stu-id="3d917-616">All *unfixed* type variables `Xi` which do not *depend on* ([Dependence](expressions.md#dependence)) any `Xj` are fixed ([Fixing](expressions.md#fixing)).</span></span>
*   <span data-ttu-id="3d917-617">Si aucune variable de type ce type n’existe, tous les *non fixed* variables de type `Xi` sont *fixe* pour lequel toutes les conditions suivantes contiennent :</span><span class="sxs-lookup"><span data-stu-id="3d917-617">If no such type variables exist, all *unfixed* type variables `Xi` are *fixed* for which all of the following hold:</span></span>
    *   <span data-ttu-id="3d917-618">Il existe au moins une variable de type `Xj` qui dépend de `Xi`</span><span class="sxs-lookup"><span data-stu-id="3d917-618">There is at least one type variable `Xj` that depends on `Xi`</span></span>
    *   <span data-ttu-id="3d917-619">`Xi` possède un ensemble non vide de limites</span><span class="sxs-lookup"><span data-stu-id="3d917-619">`Xi` has a non-empty set of bounds</span></span>
*   <span data-ttu-id="3d917-620">Si aucun ces variables de type n’existent et sont toujours *non fixed* variables de type, l’échec de l’inférence de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-620">If no such type variables exist and there are still *unfixed* type variables, type inference fails.</span></span>
*   <span data-ttu-id="3d917-621">Sinon, si aucune autre *non fixed* les variables de type existent, l’inférence de type réussit.</span><span class="sxs-lookup"><span data-stu-id="3d917-621">Otherwise, if no further *unfixed* type variables exist, type inference succeeds.</span></span>
*   <span data-ttu-id="3d917-622">Sinon, pour tous les arguments `Ei` avec le type de paramètre correspondant `Ti` où le *types de sortie* ([types de sortie](expressions.md#output-types)) contiennent *non fixed* variables de type `Xj` mais le *de types d’entrées* ([de types d’entrées](expressions.md#input-types)) ne le faites pas, un *l’inférence de type de sortie* ([des inférences de type de sortie ](expressions.md#output-type-inferences)) est effectuée *de* `Ei` *à* `Ti`.</span><span class="sxs-lookup"><span data-stu-id="3d917-622">Otherwise, for all arguments `Ei` with corresponding parameter type `Ti` where the *output types* ([Output types](expressions.md#output-types)) contain *unfixed* type variables `Xj` but the *input types* ([Input types](expressions.md#input-types)) do not, an *output type inference* ([Output type inferences](expressions.md#output-type-inferences)) is made *from* `Ei` *to* `Ti`.</span></span> <span data-ttu-id="3d917-623">Puis la deuxième phase est répétée.</span><span class="sxs-lookup"><span data-stu-id="3d917-623">Then the second phase is repeated.</span></span>

#### <a name="input-types"></a><span data-ttu-id="3d917-624">Types d’entrée</span><span class="sxs-lookup"><span data-stu-id="3d917-624">Input types</span></span>

<span data-ttu-id="3d917-625">Si `E` est un groupe de méthodes ou implicitement typé fonction anonyme et `T` est un délégué de type ou de type arborescence d’expression puis de tous les types de paramètres de `T` sont *de types d’entrées* de `E` *avec type* `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-625">If `E` is a method group or implicitly typed anonymous function and `T` is a delegate type or expression tree type then all the parameter types of `T` are *input types* of `E` *with type* `T`.</span></span>

####  <a name="output-types"></a><span data-ttu-id="3d917-626">Types de sortie</span><span class="sxs-lookup"><span data-stu-id="3d917-626">Output types</span></span>

<span data-ttu-id="3d917-627">Si `E` est un groupe de méthodes ou une fonction anonyme et `T` est un délégué de type ou de type arborescence d’expression puis le type de retour de `T` est un *type de sortie* `E` *avec type*  `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-627">If `E` is a method group or an anonymous function and `T` is a delegate type or expression tree type then the return type of `T` is an *output type of* `E` *with type* `T`.</span></span>

#### <a name="dependence"></a><span data-ttu-id="3d917-628">Dépendance</span><span class="sxs-lookup"><span data-stu-id="3d917-628">Dependence</span></span>

<span data-ttu-id="3d917-629">Un *non fixed* variable de type `Xi` *dépend directement de* une variable de type non fixed `Xj` if pour certains arguments `Ek` avec type `Tk` `Xj` se produit dans un *type d’entrée* de `Ek` avec type `Tk` et `Xi` se produit dans un *type de sortie* de `Ek` avec type `Tk`.</span><span class="sxs-lookup"><span data-stu-id="3d917-629">An *unfixed* type variable `Xi` *depends directly on* an unfixed type variable `Xj` if for some argument `Ek` with type `Tk` `Xj` occurs in an *input type* of `Ek` with type `Tk` and `Xi` occurs in an *output type* of `Ek` with type `Tk`.</span></span>

<span data-ttu-id="3d917-630">`Xj` *dépend de* `Xi` si `Xj` *dépend directement de* `Xi` ou si `Xi` *dépend directement de* `Xk` et `Xk` *dépend* `Xj`.</span><span class="sxs-lookup"><span data-stu-id="3d917-630">`Xj` *depends on* `Xi` if `Xj` *depends directly on* `Xi` or if `Xi` *depends directly on* `Xk` and `Xk` *depends on* `Xj`.</span></span> <span data-ttu-id="3d917-631">Par conséquent, « dépend » est la fermeture transitive mais pas réflexive de « dépend directement ».</span><span class="sxs-lookup"><span data-stu-id="3d917-631">Thus "depends on" is the transitive but not reflexive closure of "depends directly on".</span></span>

#### <a name="output-type-inferences"></a><span data-ttu-id="3d917-632">Inférences de type de sortie</span><span class="sxs-lookup"><span data-stu-id="3d917-632">Output type inferences</span></span>

<span data-ttu-id="3d917-633">Un *l’inférence de type de sortie* est effectuée *de* une expression `E` *à* un type `T` de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="3d917-633">An *output type inference* is made *from* an expression `E` *to* a type `T` in the following way:</span></span>

*  <span data-ttu-id="3d917-634">Si `E` est une fonction anonyme avec le type de retour déduit `U` ([type de retour déduit](expressions.md#inferred-return-type)) et `T` est un type délégué ou type arborescence d’expression avec un type de retour `Tb`, puis un *inférence de limite inférieure* ([des inférences de limite inférieure](expressions.md#lower-bound-inferences)) est effectuée *de* `U` *à* `Tb`.</span><span class="sxs-lookup"><span data-stu-id="3d917-634">If `E` is an anonymous function with inferred return type  `U` ([Inferred return type](expressions.md#inferred-return-type)) and `T` is a delegate type or expression tree type with return type `Tb`, then a *lower-bound inference* ([Lower-bound inferences](expressions.md#lower-bound-inferences)) is made *from* `U` *to* `Tb`.</span></span>
*  <span data-ttu-id="3d917-635">Sinon, si `E` est un groupe de méthodes et `T` est un type délégué ou l’expression type d’arborescence avec les types de paramètres `T1...Tk` et type de retour `Tb`et la résolution de surcharge des `E` avec les types `T1...Tk` génère un méthode avec le type de retour unique `U`, puis un *inférence de limite inférieure* est effectuée *à partir de* `U` *à* `Tb`.</span><span class="sxs-lookup"><span data-stu-id="3d917-635">Otherwise, if `E` is a method group and `T` is a delegate type or expression tree type with parameter types `T1...Tk` and return type `Tb`, and overload resolution of `E` with the types `T1...Tk` yields a single method with return type `U`, then a *lower-bound inference* is made *from* `U` *to* `Tb`.</span></span>
*  <span data-ttu-id="3d917-636">Sinon, si `E` est une expression avec type `U`, puis un *inférence de limite inférieure* est effectuée *à partir de* `U` *à* `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-636">Otherwise, if `E` is an expression with type `U`, then a *lower-bound inference* is made *from* `U` *to* `T`.</span></span>
*  <span data-ttu-id="3d917-637">Sinon, aucune des inférences ne sont effectuées.</span><span class="sxs-lookup"><span data-stu-id="3d917-637">Otherwise, no inferences are made.</span></span>

#### <a name="explicit-parameter-type-inferences"></a><span data-ttu-id="3d917-638">Inférences de type de paramètre explicite</span><span class="sxs-lookup"><span data-stu-id="3d917-638">Explicit parameter type inferences</span></span>

<span data-ttu-id="3d917-639">Un *inférence de type de paramètre explicite* est effectuée *de* une expression `E` *à* un type `T` de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="3d917-639">An *explicit parameter type inference* is made *from* an expression `E` *to* a type `T` in the following way:</span></span>

*  <span data-ttu-id="3d917-640">Si `E` est une fonction anonyme explicitement typée avec les types de paramètres `U1...Uk` et `T` est un type délégué ou l’expression type d’arborescence avec les types de paramètres `V1...Vk` puis pour chaque `Ui` un *exacte inférence* ([exacte des inférences](expressions.md#exact-inferences)) est effectuée *de* `Ui` *à* correspondant `Vi`.</span><span class="sxs-lookup"><span data-stu-id="3d917-640">If `E` is an explicitly typed anonymous function with parameter types `U1...Uk` and `T` is a delegate type or expression tree type with parameter types `V1...Vk` then for each `Ui` an *exact inference* ([Exact inferences](expressions.md#exact-inferences)) is made *from* `Ui` *to* the corresponding `Vi`.</span></span>

#### <a name="exact-inferences"></a><span data-ttu-id="3d917-641">Inférences exactes</span><span class="sxs-lookup"><span data-stu-id="3d917-641">Exact inferences</span></span>

<span data-ttu-id="3d917-642">Un *exacte inférence* *à partir de* un type `U` *à* un type `V` est effectué comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-642">An *exact inference* *from* a type `U` *to* a type `V` is made as follows:</span></span>

*  <span data-ttu-id="3d917-643">Si `V` est un de la *non fixed* `Xi` puis `U` est ajouté à l’ensemble des limites exactes pour `Xi`.</span><span class="sxs-lookup"><span data-stu-id="3d917-643">If `V` is one of the *unfixed* `Xi` then `U` is added to the set of exact bounds for `Xi`.</span></span>

*  <span data-ttu-id="3d917-644">Sinon, définit `V1...Vk` et `U1...Uk` sont déterminées en vérifiant si un des cas suivants s’applique :</span><span class="sxs-lookup"><span data-stu-id="3d917-644">Otherwise, sets `V1...Vk` and `U1...Uk` are determined by checking if any of the following cases apply:</span></span>

   *  <span data-ttu-id="3d917-645">`V` est un type tableau `V1[...]` et `U` est un type tableau `U1[...]` du même rang</span><span class="sxs-lookup"><span data-stu-id="3d917-645">`V` is an array type `V1[...]` and `U` is an array type `U1[...]`  of the same rank</span></span>
   *  <span data-ttu-id="3d917-646">`V` est le type `V1?` et `U` est le type `U1?`</span><span class="sxs-lookup"><span data-stu-id="3d917-646">`V` is the type `V1?` and `U` is the type `U1?`</span></span>
   *  <span data-ttu-id="3d917-647">`V` est un type construit `C<V1...Vk>`et `U` est un type construit `C<U1...Uk>`</span><span class="sxs-lookup"><span data-stu-id="3d917-647">`V` is a constructed type `C<V1...Vk>`and `U` is a constructed type `C<U1...Uk>`</span></span>

   <span data-ttu-id="3d917-648">Si un de ces cas s’applique ensuite une *exacte inférence* est effectuée *à partir de* chaque `Ui` *à* correspondant `Vi`.</span><span class="sxs-lookup"><span data-stu-id="3d917-648">If any of these cases apply then an *exact inference* is made *from* each `Ui` *to* the corresponding `Vi`.</span></span>

*  <span data-ttu-id="3d917-649">Sinon, aucune des inférences ne sont effectuées.</span><span class="sxs-lookup"><span data-stu-id="3d917-649">Otherwise no inferences are made.</span></span>

#### <a name="lower-bound-inferences"></a><span data-ttu-id="3d917-650">Limite inférieure inférences</span><span class="sxs-lookup"><span data-stu-id="3d917-650">Lower-bound inferences</span></span>

<span data-ttu-id="3d917-651">A *inférence de limite inférieure* *de* un type `U` *à* un type `V` est effectué comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-651">A *lower-bound inference* *from* a type `U` *to* a type `V` is made as follows:</span></span>

*  <span data-ttu-id="3d917-652">Si `V` fait partie de la *non fixed* `Xi` puis `U` est ajouté à l’ensemble des limites inférieures `Xi`.</span><span class="sxs-lookup"><span data-stu-id="3d917-652">If `V` is one of the *unfixed* `Xi` then `U` is added to the set of lower bounds for `Xi`.</span></span>
*  <span data-ttu-id="3d917-653">Sinon, si `V` est le type `V1?`et `U` est le type `U1?` une inférence de limite inférieure est effectuée à partir de `U1` à `V1`.</span><span class="sxs-lookup"><span data-stu-id="3d917-653">Otherwise, if `V` is the type `V1?`and `U` is the type `U1?` then a lower bound inference is made from `U1` to `V1`.</span></span>
*  <span data-ttu-id="3d917-654">Sinon, définit `U1...Uk` et `V1...Vk` sont déterminées en vérifiant si un des cas suivants s’applique :</span><span class="sxs-lookup"><span data-stu-id="3d917-654">Otherwise, sets `U1...Uk` and `V1...Vk` are determined by checking if any of the following cases apply:</span></span>
   *  <span data-ttu-id="3d917-655">`V` est un type tableau `V1[...]` et `U` est un type tableau `U1[...]` (ou un paramètre de type dont le type de base efficace est `U1[...]`) du même rang</span><span class="sxs-lookup"><span data-stu-id="3d917-655">`V` is an array type `V1[...]` and `U` is an array type `U1[...]` (or a type parameter whose effective base type is `U1[...]`) of the same rank</span></span>
   *  <span data-ttu-id="3d917-656">`V` est un des `IEnumerable<V1>`, `ICollection<V1>` ou `IList<V1>` et `U` est un tableau unidimensionnel de type `U1[]`(ou un paramètre de type dont le type de base efficace est `U1[]`)</span><span class="sxs-lookup"><span data-stu-id="3d917-656">`V` is one of `IEnumerable<V1>`, `ICollection<V1>` or `IList<V1>` and `U` is a one-dimensional array type `U1[]`(or a type parameter whose effective base type is `U1[]`)</span></span>
   *  <span data-ttu-id="3d917-657">`V` est un type classe, struct, interface ou un délégué construit `C<V1...Vk>` et il existe un type unique `C<U1...Uk>` telles que `U` (ou, si `U` est un paramètre de type, sa classe de base efficace ou n’importe quel membre de son set interface efficace) est identique à, hérite (directement ou indirectement), ou implémente (directement ou indirectement) `C<U1...Uk>`.</span><span class="sxs-lookup"><span data-stu-id="3d917-657">`V` is a constructed class, struct, interface or delegate type `C<V1...Vk>` and there is a unique type `C<U1...Uk>` such that `U` (or, if `U` is a type parameter, its effective base class or any member of its effective interface set) is identical to, inherits from (directly or indirectly), or implements (directly or indirectly) `C<U1...Uk>`.</span></span>

      <span data-ttu-id="3d917-658">(La restriction « unicité » signifie que dans l’interface cas `C<T> {} class U: C<X>, C<Y> {}`, aucune inférence est effectuée lors de l’inférence de `U` à `C<T>` car `U1` peut être `X` ou `Y`.)</span><span class="sxs-lookup"><span data-stu-id="3d917-658">(The "uniqueness" restriction means that in the case interface `C<T> {} class U: C<X>, C<Y> {}`, then no inference is made when inferring from `U` to `C<T>` because `U1` could be `X` or `Y`.)</span></span>

   <span data-ttu-id="3d917-659">Si un de ces cas s’applique une inférence est effectuée *de* chaque `Ui` *à* correspondant `Vi` comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-659">If any of these cases apply then an inference is made *from* each `Ui` *to* the corresponding `Vi` as follows:</span></span>

   *  <span data-ttu-id="3d917-660">Si `Ui` n’est pas connu pour être un type référence une *exacte inférence* est effectuée</span><span class="sxs-lookup"><span data-stu-id="3d917-660">If `Ui` is not known to be a reference type then an *exact inference* is made</span></span>
   *  <span data-ttu-id="3d917-661">Sinon, si `U` est un type tableau un *limite inférieure inférence* est effectuée</span><span class="sxs-lookup"><span data-stu-id="3d917-661">Otherwise, if `U` is an array type then a *lower-bound inference* is made</span></span>
   *  <span data-ttu-id="3d917-662">Sinon, si `V` est `C<V1...Vk>` puis inférence varie selon le paramètre de type i-ème `C`:</span><span class="sxs-lookup"><span data-stu-id="3d917-662">Otherwise, if `V` is `C<V1...Vk>` then inference depends on the i-th type parameter of `C`:</span></span>
      *  <span data-ttu-id="3d917-663">S’il est covariant un *limite inférieure inférence* est effectuée.</span><span class="sxs-lookup"><span data-stu-id="3d917-663">If it is covariant then a *lower-bound inference* is made.</span></span>
      *  <span data-ttu-id="3d917-664">S’il est contravariant une *supérieures inférence* est effectuée.</span><span class="sxs-lookup"><span data-stu-id="3d917-664">If it is contravariant then an *upper-bound inference* is made.</span></span>
      *  <span data-ttu-id="3d917-665">Si elle est invariante une *exacte inférence* est effectuée.</span><span class="sxs-lookup"><span data-stu-id="3d917-665">If it is invariant then an *exact inference* is made.</span></span>
*  <span data-ttu-id="3d917-666">Sinon, aucune des inférences ne sont effectuées.</span><span class="sxs-lookup"><span data-stu-id="3d917-666">Otherwise, no inferences are made.</span></span>

#### <a name="upper-bound-inferences"></a><span data-ttu-id="3d917-667">Inférences de limite supérieure</span><span class="sxs-lookup"><span data-stu-id="3d917-667">Upper-bound inferences</span></span>

<span data-ttu-id="3d917-668">Un *inférence de limite supérieure* *de* un type `U` *à* un type `V` est effectué comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-668">An *upper-bound inference* *from* a type `U` *to* a type `V` is made as follows:</span></span>

*  <span data-ttu-id="3d917-669">Si `V` fait partie de la *non fixed* `Xi` puis `U` est ajouté à l’ensemble des limites supérieures pour `Xi`.</span><span class="sxs-lookup"><span data-stu-id="3d917-669">If `V` is one of the *unfixed* `Xi` then `U` is added to the set of upper bounds for `Xi`.</span></span>
*  <span data-ttu-id="3d917-670">Sinon, définit `V1...Vk` et `U1...Uk` sont déterminées en vérifiant si un des cas suivants s’applique :</span><span class="sxs-lookup"><span data-stu-id="3d917-670">Otherwise, sets `V1...Vk` and `U1...Uk` are determined by checking if any of the following cases apply:</span></span>
   *  <span data-ttu-id="3d917-671">`U` est un type tableau `U1[...]` et `V` est un type tableau `V1[...]` du même rang</span><span class="sxs-lookup"><span data-stu-id="3d917-671">`U` is an array type `U1[...]` and `V` is an array type `V1[...]` of the same rank</span></span>
   *  <span data-ttu-id="3d917-672">`U` est un des `IEnumerable<Ue>`, `ICollection<Ue>` ou `IList<Ue>` et `V` est un tableau unidimensionnel de type `Ve[]`</span><span class="sxs-lookup"><span data-stu-id="3d917-672">`U` is one of `IEnumerable<Ue>`, `ICollection<Ue>` or `IList<Ue>` and `V` is a one-dimensional array type `Ve[]`</span></span>
   *  <span data-ttu-id="3d917-673">`U` est le type `U1?` et `V` est le type `V1?`</span><span class="sxs-lookup"><span data-stu-id="3d917-673">`U` is the type `U1?` and `V` is the type `V1?`</span></span>
   *  <span data-ttu-id="3d917-674">`U` est la classe construite, struct, interface ou un type délégué `C<U1...Uk>` et `V` est une classe, struct, interface ou un type délégué qui est identique à hérite (directement ou indirectement) ou implémente (directement ou indirectement) un type unique `C<V1...Vk>`</span><span class="sxs-lookup"><span data-stu-id="3d917-674">`U` is constructed class, struct, interface or delegate type `C<U1...Uk>` and `V` is a class, struct, interface or delegate type which is identical to, inherits from (directly or indirectly), or implements (directly or indirectly) a unique type `C<V1...Vk>`</span></span>

      <span data-ttu-id="3d917-675">(La restriction « unicité » signifie que si nous avons `interface C<T>{} class V<Z>: C<X<Z>>, C<Y<Z>>{}`, aucune inférence est effectuée lors de l’inférence de `C<U1>` à `V<Q>`.</span><span class="sxs-lookup"><span data-stu-id="3d917-675">(The "uniqueness" restriction means that if we have `interface C<T>{} class V<Z>: C<X<Z>>, C<Y<Z>>{}`, then no inference is made when inferring from `C<U1>` to `V<Q>`.</span></span> <span data-ttu-id="3d917-676">Des inférences ne sont pas effectuées à partir de `U1` soit `X<Q>` ou `Y<Q>`.)</span><span class="sxs-lookup"><span data-stu-id="3d917-676">Inferences are not made from `U1` to either `X<Q>` or `Y<Q>`.)</span></span>

   <span data-ttu-id="3d917-677">Si un de ces cas s’applique une inférence est effectuée *de* chaque `Ui` *à* correspondant `Vi` comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-677">If any of these cases apply then an inference is made *from* each `Ui` *to* the corresponding `Vi` as follows:</span></span>
   *  <span data-ttu-id="3d917-678">Si `Ui` n’est pas connu pour être un type référence une *exacte inférence* est effectuée</span><span class="sxs-lookup"><span data-stu-id="3d917-678">If  `Ui` is not known to be a reference type then an *exact inference* is made</span></span>
   *  <span data-ttu-id="3d917-679">Sinon, si `V` est un type tableau, une *supérieures inférence* est effectuée</span><span class="sxs-lookup"><span data-stu-id="3d917-679">Otherwise, if `V` is an array type then an *upper-bound inference* is made</span></span>
   *  <span data-ttu-id="3d917-680">Sinon, si `U` est `C<U1...Uk>` puis inférence varie selon le paramètre de type i-ème `C`:</span><span class="sxs-lookup"><span data-stu-id="3d917-680">Otherwise, if `U` is `C<U1...Uk>` then inference depends on the i-th type parameter of `C`:</span></span>
      *  <span data-ttu-id="3d917-681">S’il est covariant une *supérieures inférence* est effectuée.</span><span class="sxs-lookup"><span data-stu-id="3d917-681">If it is covariant then an *upper-bound inference* is made.</span></span>
      *  <span data-ttu-id="3d917-682">S’il est contravariant un *limite inférieure inférence* est effectuée.</span><span class="sxs-lookup"><span data-stu-id="3d917-682">If it is contravariant then a *lower-bound inference* is made.</span></span>
      *  <span data-ttu-id="3d917-683">Si elle est invariante une *exacte inférence* est effectuée.</span><span class="sxs-lookup"><span data-stu-id="3d917-683">If it is invariant then an *exact inference* is made.</span></span>
*  <span data-ttu-id="3d917-684">Sinon, aucune des inférences ne sont effectuées.</span><span class="sxs-lookup"><span data-stu-id="3d917-684">Otherwise, no inferences are made.</span></span>   

#### <a name="fixing"></a><span data-ttu-id="3d917-685">Résolution</span><span class="sxs-lookup"><span data-stu-id="3d917-685">Fixing</span></span>

<span data-ttu-id="3d917-686">Un *non fixed* variable de type `Xi` avec un ensemble de limites est *fixe* comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-686">An *unfixed* type variable `Xi` with a set of bounds is *fixed* as follows:</span></span>

*  <span data-ttu-id="3d917-687">L’ensemble de *types candidats* `Uj` démarre en tant que l’ensemble de tous les types dans l’ensemble de limites pour `Xi`.</span><span class="sxs-lookup"><span data-stu-id="3d917-687">The set of *candidate types* `Uj` starts out as the set of all types in the set of bounds for `Xi`.</span></span>
*  <span data-ttu-id="3d917-688">Nous examinons ensuite chaque limite pour `Xi` à son tour : Pour chaque limite exacte `U` de `Xi` tous les types `Uj` qui ne sont pas identique à `U` sont supprimés de l’ensemble de candidats.</span><span class="sxs-lookup"><span data-stu-id="3d917-688">We then examine each bound for `Xi` in turn: For each exact bound `U` of `Xi` all types `Uj` which are not identical to `U` are removed from the candidate set.</span></span> <span data-ttu-id="3d917-689">Pour chaque limite inférieure `U` de `Xi` tous les types `Uj` pour lequel il est *pas* une conversion implicite de `U` sont supprimés de l’ensemble de candidats.</span><span class="sxs-lookup"><span data-stu-id="3d917-689">For each lower bound `U` of `Xi` all types `Uj` to which there is *not* an implicit conversion from `U` are removed from the candidate set.</span></span> <span data-ttu-id="3d917-690">Pour chaque limite supérieure `U` de `Xi` tous les types `Uj` à partir de laquelle est *pas* une conversion implicite vers `U` sont supprimés de l’ensemble de candidats.</span><span class="sxs-lookup"><span data-stu-id="3d917-690">For each upper bound `U` of `Xi` all types `Uj` from which there is *not* an implicit conversion to `U` are removed from the candidate set.</span></span>
*  <span data-ttu-id="3d917-691">If parmi les types de candidats restants `Uj` est un type unique `V` à partir de laquelle il existe une conversion implicite pour tous les autres types candidats, puis `Xi` est fixé à `V`.</span><span class="sxs-lookup"><span data-stu-id="3d917-691">If among the remaining candidate types `Uj` there is a unique type `V` from which there is an implicit conversion to all the other candidate types, then `Xi` is fixed to `V`.</span></span>
*  <span data-ttu-id="3d917-692">Sinon, l’inférence de type échoue.</span><span class="sxs-lookup"><span data-stu-id="3d917-692">Otherwise, type inference fails.</span></span>

#### <a name="inferred-return-type"></a><span data-ttu-id="3d917-693">Type de retour déduit</span><span class="sxs-lookup"><span data-stu-id="3d917-693">Inferred return type</span></span>

<span data-ttu-id="3d917-694">Type d’une fonction anonyme de retour le déduit `F` est utilisé lors de la résolution de surcharge et de l’inférence de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-694">The inferred return type of an anonymous function `F` is used during type inference and overload resolution.</span></span> <span data-ttu-id="3d917-695">Le type de retour déduit peut uniquement être déterminé pour une fonction anonyme, où le paramètre tous les types sont connus, soit, car ils sont fournis explicitement, fournies via une conversion de la fonction anonyme ou déduit lors de l’inférence de type sur une englobante est générique appel de méthode.</span><span class="sxs-lookup"><span data-stu-id="3d917-695">The inferred return type can only be determined for an anonymous function where all parameter types are known, either because they are explicitly given, provided through an anonymous function conversion or inferred during type inference on an enclosing generic method invocation.</span></span>

<span data-ttu-id="3d917-696">Le ***déduit le type de résultat*** est déterminée comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-696">The ***inferred result type*** is determined as follows:</span></span>

*  <span data-ttu-id="3d917-697">Si le corps de `F` est un *expression* qui a un type, alors que le type de résultat déduit de `F` est le type de cette expression.</span><span class="sxs-lookup"><span data-stu-id="3d917-697">If the body of `F` is an *expression* that has a type, then the inferred result type of `F` is the type of that expression.</span></span>
*  <span data-ttu-id="3d917-698">Si le corps de `F` est un *bloc* et l’ensemble d’expressions dans le bloc `return` instructions a un type commun meilleures `T` ([recherche le meilleur type courantes d’un ensemble d’expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)), puis le type de résultat déduit de `F` est `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-698">If the body of `F` is a *block* and the set of expressions in the block's `return` statements has a best common type `T` ([Finding the best common type of a set of expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)), then the inferred result type of `F` is `T`.</span></span>
*  <span data-ttu-id="3d917-699">Sinon, un type de résultat ne peut pas être déduit pour `F`.</span><span class="sxs-lookup"><span data-stu-id="3d917-699">Otherwise, a result type cannot be inferred for `F`.</span></span>

<span data-ttu-id="3d917-700">Le ***déduit le type de retour*** est déterminée comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-700">The ***inferred return type*** is determined as follows:</span></span>

*  <span data-ttu-id="3d917-701">Si `F` est asynchrone et le corps de `F` est soit une expression classée nothing ([classifications des expressions](expressions.md#expression-classifications)), ou un bloc d’instructions où aucune instruction de retour n’ont des expressions, le déduit type de retour est `System.Threading.Tasks.Task`</span><span class="sxs-lookup"><span data-stu-id="3d917-701">If `F` is async and the body of `F` is either an expression classified as nothing ([Expression classifications](expressions.md#expression-classifications)), or a statement block where no return statements have expressions, the inferred return type is `System.Threading.Tasks.Task`</span></span>
*  <span data-ttu-id="3d917-702">Si `F` est asynchrone et a un type de résultat déduit `T`, le déduit type de retour est `System.Threading.Tasks.Task<T>`.</span><span class="sxs-lookup"><span data-stu-id="3d917-702">If `F` is async and has an inferred result type `T`, the inferred return type is `System.Threading.Tasks.Task<T>`.</span></span>
*  <span data-ttu-id="3d917-703">Si `F` est non asynchrone et a un type de résultat déduit `T`, le déduit type de retour est `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-703">If `F` is non-async and has an inferred result type `T`, the inferred return type is `T`.</span></span>
*  <span data-ttu-id="3d917-704">Sinon, un type de retour ne peut pas être déduit pour `F`.</span><span class="sxs-lookup"><span data-stu-id="3d917-704">Otherwise a return type cannot be inferred for `F`.</span></span>

<span data-ttu-id="3d917-705">Prenons un exemple d’inférence de type impliquant des fonctions anonymes, le `Select` méthode d’extension déclarée dans la `System.Linq.Enumerable` classe :</span><span class="sxs-lookup"><span data-stu-id="3d917-705">As an example of type inference involving anonymous functions, consider the `Select` extension method declared in the `System.Linq.Enumerable` class:</span></span>
```csharp
namespace System.Linq
{
    public static class Enumerable
    {
        public static IEnumerable<TResult> Select<TSource,TResult>(
            this IEnumerable<TSource> source,
            Func<TSource,TResult> selector)
        {
            foreach (TSource element in source) yield return selector(element);
        }
    }
}
```

<span data-ttu-id="3d917-706">En supposant que le `System.Linq` espace de noms a été importé avec un `using` clause et une classe `Customer` avec un `Name` propriété de type `string`, le `Select` méthode peut être utilisée pour sélectionner les noms d’une liste de clients :</span><span class="sxs-lookup"><span data-stu-id="3d917-706">Assuming the `System.Linq` namespace was imported with a `using` clause, and given a class `Customer` with a `Name` property of type `string`, the `Select` method can be used to select the names of a list of customers:</span></span>
```csharp
List<Customer> customers = GetCustomerList();
IEnumerable<string> names = customers.Select(c => c.Name);
```

<span data-ttu-id="3d917-707">L’appel de méthode d’extension ([appels de méthode d’Extension](expressions.md#extension-method-invocations)) de `Select` est traité par la réécriture de l’appel à un appel de méthode statique :</span><span class="sxs-lookup"><span data-stu-id="3d917-707">The extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)) of `Select` is processed by rewriting the invocation to a static method invocation:</span></span>
```csharp
IEnumerable<string> names = Enumerable.Select(customers, c => c.Name);
```

<span data-ttu-id="3d917-708">Dans la mesure où les arguments de type n’étaient pas explicitement spécifiés, l’inférence de type est utilisé pour déduire les arguments de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-708">Since type arguments were not explicitly specified, type inference is used to infer the type arguments.</span></span> <span data-ttu-id="3d917-709">Tout d’abord, le `customers` argument est lié à la `source` paramètre, déduction `T` être `Customer`.</span><span class="sxs-lookup"><span data-stu-id="3d917-709">First, the `customers` argument is related to the `source` parameter, inferring `T` to be `Customer`.</span></span> <span data-ttu-id="3d917-710">Ensuite, à l’aide de la fonction anonyme tapez des processus d’inférence décrit ci-dessus, `c` est de type donné `Customer`et l’expression `c.Name` concerne le type de retour de la `selector` paramètre, déduction `S` être `string`.</span><span class="sxs-lookup"><span data-stu-id="3d917-710">Then, using the anonymous function type inference process described above, `c` is given type `Customer`, and the expression `c.Name` is related to the return type of the `selector` parameter, inferring `S` to be `string`.</span></span> <span data-ttu-id="3d917-711">Par conséquent, l’appel est équivalent à</span><span class="sxs-lookup"><span data-stu-id="3d917-711">Thus, the invocation is equivalent to</span></span>
```csharp
Sequence.Select<Customer,string>(customers, (Customer c) => c.Name)
```
<span data-ttu-id="3d917-712">et le résultat est de type `IEnumerable<string>`.</span><span class="sxs-lookup"><span data-stu-id="3d917-712">and the result is of type `IEnumerable<string>`.</span></span>

<span data-ttu-id="3d917-713">L’exemple suivant montre comment anonyme type fonction inférence permet des informations de type « flux » entre les arguments dans un appel de méthode générique.</span><span class="sxs-lookup"><span data-stu-id="3d917-713">The following example demonstrates how anonymous function type inference allows type information to "flow" between arguments in a generic method invocation.</span></span> <span data-ttu-id="3d917-714">Étant donné la méthode :</span><span class="sxs-lookup"><span data-stu-id="3d917-714">Given the method:</span></span>
```csharp
static Z F<X,Y,Z>(X value, Func<X,Y> f1, Func<Y,Z> f2) {
    return f2(f1(value));
}
```

<span data-ttu-id="3d917-715">Inférence de type pour l’appel :</span><span class="sxs-lookup"><span data-stu-id="3d917-715">Type inference for the invocation:</span></span>
```csharp
double seconds = F("1:15:30", s => TimeSpan.Parse(s), t => t.TotalSeconds);
```
<span data-ttu-id="3d917-716">procède comme suit : Tout d’abord, l’argument `"1:15:30"` est liée à la `value` paramètre, déduction `X` être `string`.</span><span class="sxs-lookup"><span data-stu-id="3d917-716">proceeds as follows: First, the argument `"1:15:30"` is related to the `value` parameter, inferring `X` to be `string`.</span></span> <span data-ttu-id="3d917-717">Ensuite, le paramètre de la première fonction anonyme, `s`, reçoit le type déduit `string`et l’expression `TimeSpan.Parse(s)` concerne le type de retour de `f1`, inférence `Y` être `System.TimeSpan`.</span><span class="sxs-lookup"><span data-stu-id="3d917-717">Then, the parameter of the first anonymous function, `s`, is given the inferred type `string`, and the expression `TimeSpan.Parse(s)` is related to the return type of `f1`, inferring `Y` to be `System.TimeSpan`.</span></span> <span data-ttu-id="3d917-718">Enfin, le paramètre de la deuxième fonction anonyme, `t`, reçoit le type déduit `System.TimeSpan`et l’expression `t.TotalSeconds` concerne le type de retour de `f2`, inférence `Z` être `double`.</span><span class="sxs-lookup"><span data-stu-id="3d917-718">Finally, the parameter of the second anonymous function, `t`, is given the inferred type `System.TimeSpan`, and the expression `t.TotalSeconds` is related to the return type of `f2`, inferring `Z` to be `double`.</span></span> <span data-ttu-id="3d917-719">Par conséquent, le résultat de l’appel est de type `double`.</span><span class="sxs-lookup"><span data-stu-id="3d917-719">Thus, the result of the invocation is of type `double`.</span></span>

#### <a name="type-inference-for-conversion-of-method-groups"></a><span data-ttu-id="3d917-720">Inférence de type pour la conversion de groupes de méthodes</span><span class="sxs-lookup"><span data-stu-id="3d917-720">Type inference for conversion of method groups</span></span>

<span data-ttu-id="3d917-721">Comme pour les appels de méthodes génériques, inférence de type doit également être appliquée lorsqu’un groupe de méthodes `M` contenant une méthode générique est converti en un type délégué donné `D` ([conversions de groupes de méthode](conversions.md#method-group-conversions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-721">Similar to calls of generic methods, type inference must also be applied when a method group `M` containing a generic method is converted to a given delegate type `D` ([Method group conversions](conversions.md#method-group-conversions)).</span></span> <span data-ttu-id="3d917-722">Soit une méthode</span><span class="sxs-lookup"><span data-stu-id="3d917-722">Given a method</span></span>
```csharp
Tr M<X1...Xn>(T1 x1 ... Tm xm)
```
<span data-ttu-id="3d917-723">et le groupe de méthodes `M` assignée au type délégué `D` la tâche d’inférence de type consiste à trouver les arguments de type `S1...Sn` afin que l’expression :</span><span class="sxs-lookup"><span data-stu-id="3d917-723">and the method group `M` being assigned to the delegate type `D` the task of type inference is to find type arguments `S1...Sn` so that the expression:</span></span>
```csharp
M<S1...Sn>
```
<span data-ttu-id="3d917-724">devient compatible ([déclarations Delegate](delegates.md#delegate-declarations)) avec `D`.</span><span class="sxs-lookup"><span data-stu-id="3d917-724">becomes compatible ([Delegate declarations](delegates.md#delegate-declarations)) with `D`.</span></span>

<span data-ttu-id="3d917-725">Contrairement à l’algorithme d’inférence de type pour les appels de méthode générique, dans ce cas il existe uniquement argument *types*, aucun argument *expressions*.</span><span class="sxs-lookup"><span data-stu-id="3d917-725">Unlike the type inference algorithm for generic method calls, in this case there are only argument *types*, no argument *expressions*.</span></span> <span data-ttu-id="3d917-726">En particulier, il n’existe aucune fonction anonyme et donc pas besoin de plusieurs phases d’inférence.</span><span class="sxs-lookup"><span data-stu-id="3d917-726">In particular, there are no anonymous functions and hence no need for multiple phases of inference.</span></span>

<span data-ttu-id="3d917-727">Au lieu de cela, tous les `Xi` sont considérés comme *non fixed*et un *inférence de limite inférieure* est effectuée *de* chaque type d’argument `Uj` de `D` *à* le type de paramètre correspondant `Tj` de `M`.</span><span class="sxs-lookup"><span data-stu-id="3d917-727">Instead, all `Xi` are considered *unfixed*, and a *lower-bound inference* is made *from* each argument type `Uj` of `D` *to* the corresponding parameter type `Tj` of `M`.</span></span> <span data-ttu-id="3d917-728">If pour toutes les `Xi` pas de limites ont été trouvés, l’inférence de type échoue.</span><span class="sxs-lookup"><span data-stu-id="3d917-728">If for any of the `Xi` no bounds were found, type inference fails.</span></span> <span data-ttu-id="3d917-729">Sinon, tous les `Xi` sont *fixe* correspondant `Si`, qui sont le résultat de l’inférence de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-729">Otherwise, all `Xi` are *fixed* to corresponding `Si`, which are the result of type inference.</span></span>

#### <a name="finding-the-best-common-type-of-a-set-of-expressions"></a><span data-ttu-id="3d917-730">Recherche le meilleur type courantes d’un ensemble d’expressions</span><span class="sxs-lookup"><span data-stu-id="3d917-730">Finding the best common type of a set of expressions</span></span>

<span data-ttu-id="3d917-731">Dans certains cas, un type commun doit être déduite pour un ensemble d’expressions.</span><span class="sxs-lookup"><span data-stu-id="3d917-731">In some cases, a common type needs to be inferred for a set of expressions.</span></span> <span data-ttu-id="3d917-732">En particulier, les types d’éléments des tableaux implicitement typés et les types de retour de fonctions anonymes avec *bloc* corps sont trouvent de cette façon.</span><span class="sxs-lookup"><span data-stu-id="3d917-732">In particular, the element types of implicitly typed arrays and the return types of anonymous functions with *block* bodies are found in this way.</span></span>

<span data-ttu-id="3d917-733">Intuitivement, étant donné un ensemble d’expressions `E1...Em` cette inférence doit être équivalente à l’appel d’une méthode</span><span class="sxs-lookup"><span data-stu-id="3d917-733">Intuitively, given a set of expressions `E1...Em` this inference should be equivalent to calling a method</span></span>
```csharp
Tr M<X>(X x1 ... X xm)
```
<span data-ttu-id="3d917-734">avec la `Ei` en tant qu’arguments.</span><span class="sxs-lookup"><span data-stu-id="3d917-734">with the `Ei` as arguments.</span></span>

<span data-ttu-id="3d917-735">Plus précisément, l’inférence démarre avec un *non fixed* variable de type `X`.</span><span class="sxs-lookup"><span data-stu-id="3d917-735">More precisely, the inference starts out with an *unfixed* type variable `X`.</span></span> <span data-ttu-id="3d917-736">*Sortie des inférences de type* sont ensuite effectuées *à partir de* chaque `Ei` *à* `X`.</span><span class="sxs-lookup"><span data-stu-id="3d917-736">*Output type inferences* are then made *from* each `Ei` *to* `X`.</span></span> <span data-ttu-id="3d917-737">Enfin, `X` est *fixe* et, en cas de réussite, le résultat type `S` est le meilleur type commun qui en résulte pour les expressions.</span><span class="sxs-lookup"><span data-stu-id="3d917-737">Finally, `X` is *fixed* and, if successful, the resulting type `S` is the resulting best common type for the expressions.</span></span> <span data-ttu-id="3d917-738">Si aucun `S` existe, les expressions n’ont aucun meilleur type commun.</span><span class="sxs-lookup"><span data-stu-id="3d917-738">If no such `S` exists, the expressions have no best common type.</span></span>

### <a name="overload-resolution"></a><span data-ttu-id="3d917-739">Résolution de surcharge</span><span class="sxs-lookup"><span data-stu-id="3d917-739">Overload resolution</span></span>

<span data-ttu-id="3d917-740">Résolution de surcharge est un mécanisme de liaison-heure pour sélectionner la meilleure fonction membre à appeler étant donnés une liste d’arguments et un ensemble de fonctions membres candidates.</span><span class="sxs-lookup"><span data-stu-id="3d917-740">Overload resolution is a binding-time mechanism for selecting the best function member to invoke given an argument list and a set of candidate function members.</span></span> <span data-ttu-id="3d917-741">Résolution de surcharge sélectionne la fonction membre à appeler dans les différents contextes au sein de c# suivants :</span><span class="sxs-lookup"><span data-stu-id="3d917-741">Overload resolution selects the function member to invoke in the following distinct contexts within C#:</span></span>

*  <span data-ttu-id="3d917-742">Appel d’une méthode nommée dans une *invocation_expression* ([appels de méthode](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="3d917-742">Invocation of a method named in an *invocation_expression* ([Method invocations](expressions.md#method-invocations)).</span></span>
*  <span data-ttu-id="3d917-743">Appel d’un constructeur d’instance nommé dans un *object_creation_expression* ([des expressions de la création d’objet](expressions.md#object-creation-expressions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-743">Invocation of an instance constructor named in an *object_creation_expression* ([Object creation expressions](expressions.md#object-creation-expressions)).</span></span>
*  <span data-ttu-id="3d917-744">Appel d’un accesseur d’indexeur via un *element_access* ([accès à un élément](expressions.md#element-access)).</span><span class="sxs-lookup"><span data-stu-id="3d917-744">Invocation of an indexer accessor through an *element_access* ([Element access](expressions.md#element-access)).</span></span>
*  <span data-ttu-id="3d917-745">Appel d’un opérateur prédéfini ou définies par l’utilisateur référencé dans une expression ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution) et [opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="3d917-745">Invocation of a predefined or user-defined operator referenced in an expression ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution) and [Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)).</span></span>

<span data-ttu-id="3d917-746">Chacun de ces contextes définit l’ensemble des fonctions membres candidates et la liste d’arguments dans sa propre méthode unique, comme décrit en détail dans les sections ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="3d917-746">Each of these contexts defines the set of candidate function members and the list of arguments in its own unique way, as described in detail in the sections listed above.</span></span> <span data-ttu-id="3d917-747">Par exemple, l’ensemble de candidats pour un appel de méthode n’inclut pas les méthodes marquées `override` ([recherche de membres](expressions.md#member-lookup)), et les méthodes dans une classe de base ne sont pas des candidats si n’importe quelle méthode dans une classe dérivée est applicable ([ Appels de méthode](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="3d917-747">For example, the set of candidates for a method invocation does not include methods marked `override` ([Member lookup](expressions.md#member-lookup)), and methods in a base class are not candidates if any method in a derived class is applicable ([Method invocations](expressions.md#method-invocations)).</span></span>

<span data-ttu-id="3d917-748">Une fois que les fonctions membres candidates et la liste d’arguments ont été identifiés, la sélection de la meilleure fonction membre est la même dans tous les cas :</span><span class="sxs-lookup"><span data-stu-id="3d917-748">Once the candidate function members and the argument list have been identified, the selection of the best function member is the same in all cases:</span></span>

*  <span data-ttu-id="3d917-749">Compte tenu du jeu de membres de fonctions candidats applicables, la meilleure fonction membre dans la mesure ensemble se trouve.</span><span class="sxs-lookup"><span data-stu-id="3d917-749">Given the set of applicable candidate function members, the best function member in that set is located.</span></span> <span data-ttu-id="3d917-750">Si le jeu ne contient qu’un seul membre de fonction, cette fonction membre est la meilleure fonction membre.</span><span class="sxs-lookup"><span data-stu-id="3d917-750">If the set contains only one function member, then that function member is the best function member.</span></span> <span data-ttu-id="3d917-751">Sinon, la meilleure fonction membre est le membre d’une fonction qui est mieux que tous les autres membres de fonction en ce qui concerne la liste d’arguments donné, à condition que chaque fonction membre est comparée à toutes les autres fonctions membres en utilisant les règles dans [ Meilleur membre de fonction](expressions.md#better-function-member).</span><span class="sxs-lookup"><span data-stu-id="3d917-751">Otherwise, the best function member is the one function member that is better than all other function members with respect to the given argument list, provided that each function member is compared to all other function members using the rules in [Better function member](expressions.md#better-function-member).</span></span> <span data-ttu-id="3d917-752">S’il n’est pas exactement une seule fonction membre qui est meilleure que toutes les autres fonctions membres, puis l’appel de fonction membre est ambigu et une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-752">If there is not exactly one function member that is better than all other function members, then the function member invocation is ambiguous and a binding-time error occurs.</span></span>

<span data-ttu-id="3d917-753">Les sections suivantes définissent la signification exacte des termes du contrat ***membre de fonction applicable*** et ***meilleur membre de fonction***.</span><span class="sxs-lookup"><span data-stu-id="3d917-753">The following sections define the exact meanings of the terms ***applicable function member*** and ***better function member***.</span></span>

#### <a name="applicable-function-member"></a><span data-ttu-id="3d917-754">Membre de fonction applicable</span><span class="sxs-lookup"><span data-stu-id="3d917-754">Applicable function member</span></span>

<span data-ttu-id="3d917-755">Une fonction membre est dite un ***membre de fonction applicable*** par rapport à une liste d’arguments `A` lorsque toutes les conditions suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="3d917-755">A function member is said to be an ***applicable function member*** with respect to an argument list `A` when all of the following are true:</span></span>

*  <span data-ttu-id="3d917-756">Chaque argument dans `A` correspond à un paramètre dans la déclaration de membre de fonction, comme décrit dans [les paramètres correspondants](expressions.md#corresponding-parameters), et n’importe quel paramètre auquel ne correspond aucun argument est un paramètre facultatif.</span><span class="sxs-lookup"><span data-stu-id="3d917-756">Each argument in `A` corresponds to a parameter in the function member declaration as described in [Corresponding parameters](expressions.md#corresponding-parameters), and any parameter to which no argument corresponds is an optional parameter.</span></span>
*  <span data-ttu-id="3d917-757">Pour chaque argument dans `A`, le paramètre en passant du mode de l’argument (par exemple, la valeur, `ref`, ou `out`) est identique au mode de transmission du paramètre correspondant, et</span><span class="sxs-lookup"><span data-stu-id="3d917-757">For each argument in `A`, the parameter passing mode of the argument (i.e., value, `ref`, or `out`) is identical to the parameter passing mode of the corresponding parameter, and</span></span>
   *  <span data-ttu-id="3d917-758">pour un paramètre de valeur ou un tableau de paramètres, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir de l’argument vers le type du paramètre correspondant, ou</span><span class="sxs-lookup"><span data-stu-id="3d917-758">for a value parameter or a parameter array, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from the argument to the type of the corresponding parameter, or</span></span>
   *  <span data-ttu-id="3d917-759">pour un `ref` ou `out` paramètre, le type de l’argument est identique au type du paramètre correspondant.</span><span class="sxs-lookup"><span data-stu-id="3d917-759">for a `ref` or `out` parameter, the type of the argument is identical to the type of the corresponding parameter.</span></span> <span data-ttu-id="3d917-760">Après tout, un `ref` ou `out` paramètre est un alias pour l’argument passé.</span><span class="sxs-lookup"><span data-stu-id="3d917-760">After all, a `ref` or `out` parameter is an alias for the argument passed.</span></span>

<span data-ttu-id="3d917-761">Pour une fonction membre qui inclut un tableau de paramètres, si la fonction membre est applicable par les règles ci-dessus, elle est dite applicable dans son ***forme normale***.</span><span class="sxs-lookup"><span data-stu-id="3d917-761">For a function member that includes a parameter array, if the function member is applicable by the above rules, it is said to be applicable in its ***normal form***.</span></span> <span data-ttu-id="3d917-762">Si une fonction membre qui inclut un tableau de paramètres n’est pas applicable dans sa forme normale, la fonction membre peut alors être applicable dans son ***développé formulaire***:</span><span class="sxs-lookup"><span data-stu-id="3d917-762">If a function member that includes a parameter array is not applicable in its normal form, the function member may instead be applicable in its ***expanded form***:</span></span>

*  <span data-ttu-id="3d917-763">La forme étendue est construite en remplaçant le tableau de paramètres dans la déclaration de fonction membre par zéro ou plusieurs paramètres de valeur du type d’élément du paramètre de tableau telles que le nombre d’arguments dans la liste d’arguments `A` correspond au total nombre de paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-763">The expanded form is constructed by replacing the parameter array in the function member declaration with zero or more value parameters of the element type of the parameter array such that the number of arguments in the argument list `A` matches the total number of parameters.</span></span> <span data-ttu-id="3d917-764">Si `A` a moins d’arguments que le nombre de paramètres fixes dans la déclaration de fonction membre, la forme étendue de la fonction membre ne peut pas être construite et par conséquent, n’est pas applicable.</span><span class="sxs-lookup"><span data-stu-id="3d917-764">If `A` has fewer arguments than the number of fixed parameters in the function member declaration, the expanded form of the function member cannot be constructed and is thus not applicable.</span></span>
*  <span data-ttu-id="3d917-765">Sinon, la forme étendue est applicable si, pour chaque argument dans `A` le mode de passage de paramètre de l’argument est identique au mode de transmission du paramètre correspondant, et</span><span class="sxs-lookup"><span data-stu-id="3d917-765">Otherwise, the expanded form is applicable if for each argument in `A` the parameter passing mode of the argument is identical to the parameter passing mode of the corresponding parameter, and</span></span>
   *  <span data-ttu-id="3d917-766">pour un paramètre de valeur fixe ou un paramètre de valeur créé par l’expansion, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir du type de l’argument vers le type du paramètre correspondant, ou</span><span class="sxs-lookup"><span data-stu-id="3d917-766">for a fixed value parameter or a value parameter created by the expansion, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from the type of the argument to the type of the corresponding parameter, or</span></span>
   *  <span data-ttu-id="3d917-767">pour un `ref` ou `out` paramètre, le type de l’argument est identique au type du paramètre correspondant.</span><span class="sxs-lookup"><span data-stu-id="3d917-767">for a `ref` or `out` parameter, the type of the argument is identical to the type of the corresponding parameter.</span></span>

#### <a name="better-function-member"></a><span data-ttu-id="3d917-768">Meilleur membre de fonction</span><span class="sxs-lookup"><span data-stu-id="3d917-768">Better function member</span></span>

<span data-ttu-id="3d917-769">Dans le cadre de déterminer le meilleur membre de fonction, une liste d’arguments épurée A est construite, contenant uniquement les expressions d’arguments eux-mêmes dans l’ordre de qu'apparition dans la liste d’arguments d’origine.</span><span class="sxs-lookup"><span data-stu-id="3d917-769">For the purposes of determining the better function member, a stripped-down argument list A is constructed containing just the argument expressions themselves in the order they appear in the original argument list.</span></span>

<span data-ttu-id="3d917-770">Listes des paramètres pour chacun de la fonction candidate membres sont construits de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="3d917-770">Parameter lists for each of the candidate function members are constructed in the following way:</span></span>

*  <span data-ttu-id="3d917-771">La forme étendue est utilisée si la fonction membre est applicable uniquement dans la forme étendue.</span><span class="sxs-lookup"><span data-stu-id="3d917-771">The expanded form is used if the function member was applicable only in the expanded form.</span></span>
*  <span data-ttu-id="3d917-772">Paramètres facultatifs avec aucun argument correspondant sont supprimés de la liste de paramètres</span><span class="sxs-lookup"><span data-stu-id="3d917-772">Optional parameters with no corresponding arguments are removed from the parameter list</span></span>
*  <span data-ttu-id="3d917-773">Les paramètres sont réorganisés afin qu’elles se produisent à la même position que l’argument correspondant dans la liste d’arguments.</span><span class="sxs-lookup"><span data-stu-id="3d917-773">The parameters are reordered so that they occur at the same position as the corresponding argument in the argument list.</span></span>

<span data-ttu-id="3d917-774">Étant donné une liste d’arguments `A` avec un ensemble d’expressions d’arguments `{E1, E2, ..., En}` et deux membres de fonction applicable `Mp` et `Mq` avec les types de paramètres `{P1, P2, ..., Pn}` et `{Q1, Q2, ..., Qn}`, `Mp` est défini comme étant un ***meilleur membre de fonction*** à `Mq` si</span><span class="sxs-lookup"><span data-stu-id="3d917-774">Given an argument list `A` with a set of argument expressions `{E1, E2, ..., En}` and two applicable function members `Mp` and `Mq` with parameter types `{P1, P2, ..., Pn}` and `{Q1, Q2, ..., Qn}`, `Mp` is defined to be a ***better function member*** than `Mq` if</span></span>

*  <span data-ttu-id="3d917-775">pour chaque argument, la conversion implicite de `Ex` à `Qx` n’est pas meilleur que la conversion implicite de `Ex` à `Px`, et</span><span class="sxs-lookup"><span data-stu-id="3d917-775">for each argument, the implicit conversion from `Ex` to `Qx` is not better than the implicit conversion from `Ex` to `Px`, and</span></span>
*  <span data-ttu-id="3d917-776">au moins un argument, la conversion de `Ex` à `Px` est meilleure que la conversion de `Ex` à `Qx`.</span><span class="sxs-lookup"><span data-stu-id="3d917-776">for at least one argument, the conversion from `Ex` to `Px` is better than the conversion from `Ex` to `Qx`.</span></span>

<span data-ttu-id="3d917-777">Lorsque vous effectuez cette évaluation, si `Mp` ou `Mq` est applicable dans sa forme étendue, puis `Px` ou `Qx` fait référence à un paramètre dans la forme étendue de la liste de paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-777">When performing this evaluation, if `Mp` or `Mq` is applicable in its expanded form, then `Px` or `Qx` refers to a parameter in the expanded form of the parameter list.</span></span>

<span data-ttu-id="3d917-778">Dans le cas où le type de paramètre séquences `{P1, P2, ..., Pn}` et `{Q1, Q2, ..., Qn}` sont équivalentes (autrement dit, chaque `Pi` a une conversion d’identité correspondant `Qi`), les règles de saut de lien suivantes sont appliquées, dans l’ordre, pour déterminer la meilleure fonction membre.</span><span class="sxs-lookup"><span data-stu-id="3d917-778">In case the parameter type sequences `{P1, P2, ..., Pn}` and `{Q1, Q2, ..., Qn}` are equivalent (i.e. each `Pi` has an identity conversion to the corresponding `Qi`), the following tie-breaking rules are applied, in order, to determine the better function member.</span></span>

*  <span data-ttu-id="3d917-779">Si `Mp` est une méthode non générique et `Mq` est une méthode générique, puis `Mp` est meilleure que `Mq`.</span><span class="sxs-lookup"><span data-stu-id="3d917-779">If `Mp` is a non-generic method and `Mq` is a generic method, then `Mp` is better than `Mq`.</span></span>
*  <span data-ttu-id="3d917-780">Sinon, si `Mp` est applicable dans sa forme normale et `Mq` a un `params` de tableau et est applicable uniquement dans sa forme étendue, puis `Mp` est meilleure que `Mq`.</span><span class="sxs-lookup"><span data-stu-id="3d917-780">Otherwise, if `Mp` is applicable in its normal form and `Mq` has a `params` array and is applicable only in its expanded form, then `Mp` is better than `Mq`.</span></span>
*  <span data-ttu-id="3d917-781">Sinon, si `Mp` a plusieurs déclarés de paramètres que `Mq`, puis `Mp` est meilleure que `Mq`.</span><span class="sxs-lookup"><span data-stu-id="3d917-781">Otherwise, if `Mp` has more declared parameters than `Mq`, then `Mp` is better than `Mq`.</span></span> <span data-ttu-id="3d917-782">Cela peut se produire si les deux méthodes ont `params` tableaux et sont applicables uniquement dans leurs formes développées.</span><span class="sxs-lookup"><span data-stu-id="3d917-782">This can occur if both methods have `params` arrays and are applicable only in their expanded forms.</span></span>
*  <span data-ttu-id="3d917-783">Sinon, si tous les paramètres de `Mp` ont un argument correspondant, tandis que les arguments par défaut doivent être substitué au moins un paramètre facultatif dans `Mq` puis `Mp` est meilleure que `Mq`.</span><span class="sxs-lookup"><span data-stu-id="3d917-783">Otherwise if all parameters of `Mp` have a corresponding argument whereas default arguments need to be substituted for at least one optional parameter in `Mq` then `Mp` is better than `Mq`.</span></span>
*  <span data-ttu-id="3d917-784">Sinon, si `Mp` a des types de paramètres plus spécifiques que `Mq`, puis `Mp` est meilleure que `Mq`.</span><span class="sxs-lookup"><span data-stu-id="3d917-784">Otherwise, if `Mp` has more specific parameter types than `Mq`, then `Mp` is better than `Mq`.</span></span> <span data-ttu-id="3d917-785">Laisser `{R1, R2, ..., Rn}` et `{S1, S2, ..., Sn}` représentent les types de paramètre non instancié et non développées de `Mp` et `Mq`.</span><span class="sxs-lookup"><span data-stu-id="3d917-785">Let `{R1, R2, ..., Rn}` and `{S1, S2, ..., Sn}` represent the uninstantiated and unexpanded parameter types of `Mp` and `Mq`.</span></span> <span data-ttu-id="3d917-786">`Mp`de types de paramètres sont plus précises que `Mq`d’if, pour chaque paramètre, `Rx` n’est pas moins spécifiques que `Sx`et, pour au moins un paramètre, `Rx` est plus spécifique que `Sx`:</span><span class="sxs-lookup"><span data-stu-id="3d917-786">`Mp`'s parameter types are more specific than `Mq`'s if, for each parameter, `Rx` is not less specific than `Sx`, and, for at least one parameter, `Rx` is more specific than `Sx`:</span></span>
   *  <span data-ttu-id="3d917-787">Un paramètre de type est moins spécifique à un paramètre sans type.</span><span class="sxs-lookup"><span data-stu-id="3d917-787">A type parameter is less specific than a non-type parameter.</span></span>
   *  <span data-ttu-id="3d917-788">Récursive, un type construit est plus spécifique à un autre type construit (avec le même nombre d’arguments de type) si au moins un argument de type est plus spécifique et aucun argument de type n’est moins spécifique que l’argument de type correspondant dans l’autre.</span><span class="sxs-lookup"><span data-stu-id="3d917-788">Recursively, a constructed type is more specific than another constructed type (with the same number of type arguments) if at least one type argument is more specific and no type argument is less specific than the corresponding type argument in the other.</span></span>
   *  <span data-ttu-id="3d917-789">Un type tableau est plus spécifique qu’un autre type de tableau (avec le même nombre de dimensions) si le type d’élément de la première est plus spécifique que le type d’élément de la deuxième.</span><span class="sxs-lookup"><span data-stu-id="3d917-789">An array type is more specific than another array type (with the same number of dimensions) if the element type of the first is more specific than the element type of the second.</span></span>
*  <span data-ttu-id="3d917-790">Sinon, si un membre est un opérateur non levé et l’autre est un opérateur levé, non levé celui qui est préférable.</span><span class="sxs-lookup"><span data-stu-id="3d917-790">Otherwise if one member is a non-lifted operator and  the other is a lifted operator, the non-lifted one is better.</span></span>
*  <span data-ttu-id="3d917-791">Sinon, aucune fonction membre est préférable.</span><span class="sxs-lookup"><span data-stu-id="3d917-791">Otherwise, neither function member is better.</span></span>

#### <a name="better-conversion-from-expression"></a><span data-ttu-id="3d917-792">Meilleure conversion à partir de l’expression</span><span class="sxs-lookup"><span data-stu-id="3d917-792">Better conversion from expression</span></span>

<span data-ttu-id="3d917-793">Étant donné une conversion implicite `C1` qui convertit à partir d’une expression `E` à un type `T1`et une conversion implicite `C2` qui convertit à partir d’une expression `E` à un type `T2`, `C1` est un ***une meilleure conversion*** à `C2` si `E` ne correspond pas exactement à `T2` et au moins une des opérations suivantes conserve :</span><span class="sxs-lookup"><span data-stu-id="3d917-793">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>

* <span data-ttu-id="3d917-794">`E` correspond exactement à `T1` ([correspondant exactement à Expression](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="3d917-794">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
* <span data-ttu-id="3d917-795">`T1` est une cible de conversion mieux que `T2` ([mieux cibler conversion](expressions.md#better-conversion-target))</span><span class="sxs-lookup"><span data-stu-id="3d917-795">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target))</span></span>

#### <a name="exactly-matching-expression"></a><span data-ttu-id="3d917-796">Expression de correspondance exacte</span><span class="sxs-lookup"><span data-stu-id="3d917-796">Exactly matching Expression</span></span>

<span data-ttu-id="3d917-797">Étant donné une expression `E` et un type `T`, `E` correspond exactement à `T` si dont l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="3d917-797">Given an expression `E` and a type `T`, `E` exactly matches `T` if one of the following holds:</span></span>

*  <span data-ttu-id="3d917-798">`E` a un type `S`, et il existe une conversion d’identité à partir de `S` à `T`</span><span class="sxs-lookup"><span data-stu-id="3d917-798">`E` has a type `S`, and an identity conversion exists from `S` to `T`</span></span>
*  <span data-ttu-id="3d917-799">`E` est une fonction anonyme, `T` est soit un type de délégué `D` ou un type d’arborescence expression `Expression<D>` et dont l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="3d917-799">`E` is an anonymous function, `T` is either a delegate type `D` or an expression tree type `Expression<D>` and one of the following holds:</span></span>
   *  <span data-ttu-id="3d917-800">Un type de retour déduit `X` existe pour `E` dans le contexte de la liste des paramètres `D` ([type de retour déduit](expressions.md#inferred-return-type)), et il existe une conversion d’identité à partir de `X` pour le type de retour `D`</span><span class="sxs-lookup"><span data-stu-id="3d917-800">An inferred return type `X` exists for `E` in the context of the parameter list of `D` ([Inferred return type](expressions.md#inferred-return-type)), and an identity conversion exists from `X` to the return type of `D`</span></span>
   *  <span data-ttu-id="3d917-801">Soit `E` est non asynchrone et `D` a un type de retour `Y` ou `E` est asynchrone et `D` a un type de retour `Task<Y>`, et dont l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="3d917-801">Either `E` is non-async and `D` has a return type `Y` or `E` is async and `D` has a return type `Task<Y>`, and one of the following holds:</span></span>
      * <span data-ttu-id="3d917-802">Le corps de `E` est une expression correspondant exactement à `Y`</span><span class="sxs-lookup"><span data-stu-id="3d917-802">The body of `E` is an expression that exactly matches `Y`</span></span>
      * <span data-ttu-id="3d917-803">Le corps de `E` est un bloc d’instructions où chaque instruction return renvoie une expression correspondant exactement à `Y`</span><span class="sxs-lookup"><span data-stu-id="3d917-803">The body of `E` is a statement block where every return statement returns an expression that exactly matches `Y`</span></span>

#### <a name="better-conversion-target"></a><span data-ttu-id="3d917-804">Mieux cibler de conversion</span><span class="sxs-lookup"><span data-stu-id="3d917-804">Better conversion target</span></span>

<span data-ttu-id="3d917-805">Étant donné deux types différents `T1` et `T2`, `T1` est une cible de conversion mieux que `T2` si aucune conversion implicite de `T2` à `T1` existe, et au moins une des opérations suivantes conserve :</span><span class="sxs-lookup"><span data-stu-id="3d917-805">Given two different types `T1` and `T2`, `T1` is a better conversion target than `T2` if no implicit conversion from `T2` to `T1` exists, and at least one of the following holds:</span></span>

*  <span data-ttu-id="3d917-806">Une conversion implicite de `T1` à `T2` existe</span><span class="sxs-lookup"><span data-stu-id="3d917-806">An implicit conversion from `T1` to `T2` exists</span></span>
*  <span data-ttu-id="3d917-807">`T1` est soit un type de délégué `D1` ou un type d’arborescence expression `Expression<D1>`, `T2` est soit un type de délégué `D2` ou un type d’arborescence expression `Expression<D2>`, `D1` a un type de retour `S1` et l’une du blocages suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-807">`T1` is either a delegate type `D1` or an expression tree type `Expression<D1>`, `T2` is either a delegate type `D2` or an expression tree type `Expression<D2>`, `D1` has a return type `S1` and one of the following holds:</span></span>
   * <span data-ttu-id="3d917-808">`D2` void retourne</span><span class="sxs-lookup"><span data-stu-id="3d917-808">`D2` is void returning</span></span>
   * <span data-ttu-id="3d917-809">`D2` a un type de retour `S2`, et `S1` est une cible de conversion mieux que `S2`</span><span class="sxs-lookup"><span data-stu-id="3d917-809">`D2` has a return type `S2`, and `S1` is a better conversion target than `S2`</span></span>
*  <span data-ttu-id="3d917-810">`T1` est `Task<S1>`, `T2` est `Task<S2>`, et `S1` est une cible de conversion mieux que `S2`</span><span class="sxs-lookup"><span data-stu-id="3d917-810">`T1` is `Task<S1>`, `T2` is `Task<S2>`, and `S1` is a better conversion target than `S2`</span></span>
*  <span data-ttu-id="3d917-811">`T1` est `S1` ou `S1?` où `S1` est un type intégral signé, et `T2` est `S2` ou `S2?` où `S2` est un type intégral non signé.</span><span class="sxs-lookup"><span data-stu-id="3d917-811">`T1` is `S1` or `S1?` where `S1` is a signed integral type, and `T2` is `S2` or `S2?` where `S2` is an unsigned integral type.</span></span> <span data-ttu-id="3d917-812">Plus précisément :</span><span class="sxs-lookup"><span data-stu-id="3d917-812">Specifically:</span></span>
   * <span data-ttu-id="3d917-813">`S1` est `sbyte` et `S2` est `byte`, `ushort`, `uint`, ou `ulong`</span><span class="sxs-lookup"><span data-stu-id="3d917-813">`S1` is `sbyte` and `S2` is `byte`, `ushort`, `uint`, or `ulong`</span></span>
   * <span data-ttu-id="3d917-814">`S1` est `short` et `S2` est `ushort`, `uint`, ou `ulong`</span><span class="sxs-lookup"><span data-stu-id="3d917-814">`S1` is `short` and `S2` is `ushort`, `uint`, or `ulong`</span></span>
   * <span data-ttu-id="3d917-815">`S1` est `int` et `S2` est `uint`, ou `ulong`</span><span class="sxs-lookup"><span data-stu-id="3d917-815">`S1` is `int` and `S2` is `uint`, or `ulong`</span></span>
   * <span data-ttu-id="3d917-816">`S1` est `long` et `S2` est `ulong`</span><span class="sxs-lookup"><span data-stu-id="3d917-816">`S1` is `long` and `S2` is `ulong`</span></span>

#### <a name="overloading-in-generic-classes"></a><span data-ttu-id="3d917-817">La surcharge dans les classes génériques</span><span class="sxs-lookup"><span data-stu-id="3d917-817">Overloading in generic classes</span></span>

<span data-ttu-id="3d917-818">Tandis que les signatures tel que déclaré doivent être uniques, il est possible que la substitution des arguments de type génère des signatures identiques.</span><span class="sxs-lookup"><span data-stu-id="3d917-818">While signatures as declared must be unique, it is possible that substitution of type arguments results in identical signatures.</span></span> <span data-ttu-id="3d917-819">Dans ce cas, les règles de saut de lien de résolution de surcharge ci-dessus sélectionnera le membre plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-819">In such cases, the tie-breaking rules of overload resolution above will pick the most specific member.</span></span>

<span data-ttu-id="3d917-820">Les exemples suivants montrent des surcharges qui sont valides et non valides en fonction de cette règle :</span><span class="sxs-lookup"><span data-stu-id="3d917-820">The following examples show overloads that are valid and invalid according to this rule:</span></span>

```csharp
interface I1<T> {...}

interface I2<T> {...}

class G1<U>
{
    int F1(U u);                  // Overload resolution for G<int>.F1
    int F1(int i);                // will pick non-generic

    void F2(I1<U> a);             // Valid overload
    void F2(I2<U> a);
}

class G2<U,V>
{
    void F3(U u, V v);            // Valid, but overload resolution for
    void F3(V v, U u);            // G2<int,int>.F3 will fail

    void F4(U u, I1<V> v);        // Valid, but overload resolution for    
    void F4(I1<V> v, U u);        // G2<I1<int>,int>.F4 will fail

    void F5(U u1, I1<V> v2);      // Valid overload
    void F5(V v1, U u2);

    void F6(ref U u);             // valid overload
    void F6(out V v);
}
```

### <a name="compile-time-checking-of-dynamic-overload-resolution"></a><span data-ttu-id="3d917-821">La vérification de la résolution de surcharge dynamique lors de la compilation</span><span class="sxs-lookup"><span data-stu-id="3d917-821">Compile-time checking of dynamic overload resolution</span></span>

<span data-ttu-id="3d917-822">Pour les opérations dépendantes plus dynamiquement l’ensemble de candidats possibles pour la résolution est inconnu au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-822">For most dynamically bound operations the set of possible candidates for resolution is unknown at compile-time.</span></span> <span data-ttu-id="3d917-823">Dans certains cas, toutefois l’ensemble de candidats est connu au moment de la compilation :</span><span class="sxs-lookup"><span data-stu-id="3d917-823">In certain cases, however the candidate set is known at compile-time:</span></span>

*  <span data-ttu-id="3d917-824">Appels de méthode statique avec des arguments dynamiques</span><span class="sxs-lookup"><span data-stu-id="3d917-824">Static method calls with dynamic arguments</span></span>
*  <span data-ttu-id="3d917-825">Appels de méthode d’instance où le destinataire n’est pas une expression dynamique</span><span class="sxs-lookup"><span data-stu-id="3d917-825">Instance method calls where the receiver is not a dynamic expression</span></span>
*  <span data-ttu-id="3d917-826">Appels d’indexeur où le destinataire n’est pas une expression dynamique</span><span class="sxs-lookup"><span data-stu-id="3d917-826">Indexer calls where the receiver is not a dynamic expression</span></span>
*  <span data-ttu-id="3d917-827">Appels de constructeur avec des arguments dynamiques</span><span class="sxs-lookup"><span data-stu-id="3d917-827">Constructor calls with dynamic arguments</span></span>

<span data-ttu-id="3d917-828">Dans ce cas, une vérification limitée de la compilation est effectuée pour chaque candidat voir si un d’eux peut éventuellement s’appliquent au moment de l’exécution. Cette vérification se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-828">In these cases a limited compile-time check is performed for each candidate to see if any of them could possibly apply at run-time.This check consists of the following steps:</span></span>

*  <span data-ttu-id="3d917-829">Inférence de type partiel : Tout type d’argument qui ne dépende pas directement ou indirectement un argument de type `dynamic` est déduit à l’aide des règles de [l’inférence de Type](expressions.md#type-inference).</span><span class="sxs-lookup"><span data-stu-id="3d917-829">Partial type inference: Any type argument that does not depend directly or indirectly on an argument of type `dynamic` is inferred using the rules of [Type inference](expressions.md#type-inference).</span></span> <span data-ttu-id="3d917-830">Les autres arguments de type sont inconnues.</span><span class="sxs-lookup"><span data-stu-id="3d917-830">The remaining type arguments are unknown.</span></span>
*  <span data-ttu-id="3d917-831">Vérification de la mise en application partielle : Mise en application est vérifiée en fonction de [membre de fonction Applicable](expressions.md#applicable-function-member), mais en ignorant les paramètres dont les types sont inconnus.</span><span class="sxs-lookup"><span data-stu-id="3d917-831">Partial applicability check: Applicability is checked according to [Applicable function member](expressions.md#applicable-function-member), but ignoring parameters whose types are unknown.</span></span>
*  <span data-ttu-id="3d917-832">Si aucun candidat ne réussit ce test, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-832">If no candidate passes this test, a compile-time error occurs.</span></span>

### <a name="function-member-invocation"></a><span data-ttu-id="3d917-833">Appel de fonction membre</span><span class="sxs-lookup"><span data-stu-id="3d917-833">Function member invocation</span></span>

<span data-ttu-id="3d917-834">Cette section décrit le processus qui a lieu au moment de l’exécution pour appeler une fonction membre particulière.</span><span class="sxs-lookup"><span data-stu-id="3d917-834">This section describes the process that takes place at run-time to invoke a particular function member.</span></span> <span data-ttu-id="3d917-835">Il est supposé qu’un processus du moment de la liaison a déjà déterminé le membre particulier à appeler, éventuellement en appliquant résolution de surcharge à un ensemble de fonctions membres candidates.</span><span class="sxs-lookup"><span data-stu-id="3d917-835">It is assumed that a binding-time process has already determined the particular member to invoke, possibly by applying overload resolution to a set of candidate function members.</span></span>

<span data-ttu-id="3d917-836">Pour les besoins de description du processus d’appel, les fonctions membres sont divisées en deux catégories :</span><span class="sxs-lookup"><span data-stu-id="3d917-836">For purposes of describing the invocation process, function members are divided into two categories:</span></span>

*  <span data-ttu-id="3d917-837">Les fonctions membres static.</span><span class="sxs-lookup"><span data-stu-id="3d917-837">Static function members.</span></span> <span data-ttu-id="3d917-838">Il s’agit de constructeurs d’instance, les méthodes statiques, les accesseurs de propriété statique et les opérateurs définis par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-838">These are instance constructors, static methods, static property accessors, and user-defined operators.</span></span> <span data-ttu-id="3d917-839">Les fonctions membres static sont toujours non virtuelle.</span><span class="sxs-lookup"><span data-stu-id="3d917-839">Static function members are always non-virtual.</span></span>
*  <span data-ttu-id="3d917-840">Fonctions membres d’instance.</span><span class="sxs-lookup"><span data-stu-id="3d917-840">Instance function members.</span></span> <span data-ttu-id="3d917-841">Il s’agit de méthodes d’instance, les accesseurs de propriété d’instance et accesseurs des indexeurs.</span><span class="sxs-lookup"><span data-stu-id="3d917-841">These are instance methods, instance property accessors, and indexer accessors.</span></span> <span data-ttu-id="3d917-842">Fonctions membres d’instance sont non virtuel ou virtuel et sont toujours appelées sur une instance particulière.</span><span class="sxs-lookup"><span data-stu-id="3d917-842">Instance function members are either non-virtual or virtual, and are always invoked on a particular instance.</span></span> <span data-ttu-id="3d917-843">L’instance est calculée par une expression d’instance, et il devienne accessible au sein de la fonction membre en tant que `this` ([cet accès](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="3d917-843">The instance is computed by an instance expression, and it becomes accessible within the function member as `this` ([This access](expressions.md#this-access)).</span></span>

<span data-ttu-id="3d917-844">Le traitement de l’exécution d’un appel de fonction membre se compose des étapes suivantes, où `M` est la fonction membre et, si `M` est un membre d’instance, `E` est l’expression d’instance :</span><span class="sxs-lookup"><span data-stu-id="3d917-844">The run-time processing of a function member invocation consists of the following steps, where `M` is the function member and, if `M` is an instance member, `E` is the instance expression:</span></span>

*  <span data-ttu-id="3d917-845">Si `M` est une fonction membre statique :</span><span class="sxs-lookup"><span data-stu-id="3d917-845">If `M` is a static function member:</span></span>
   * <span data-ttu-id="3d917-846">La liste d’arguments est évaluée comme décrit dans [listes d’arguments](expressions.md#argument-lists).</span><span class="sxs-lookup"><span data-stu-id="3d917-846">The argument list is evaluated as described in [Argument lists](expressions.md#argument-lists).</span></span>
   * <span data-ttu-id="3d917-847">`M` est appelé.</span><span class="sxs-lookup"><span data-stu-id="3d917-847">`M` is invoked.</span></span>

*  <span data-ttu-id="3d917-848">Si `M` est une fonction membre d’instance déclarée dans un *value_type*:</span><span class="sxs-lookup"><span data-stu-id="3d917-848">If `M` is an instance function member declared in a *value_type*:</span></span>
   * <span data-ttu-id="3d917-849">`E` est évalué.</span><span class="sxs-lookup"><span data-stu-id="3d917-849">`E` is evaluated.</span></span> <span data-ttu-id="3d917-850">Si cette version d’évaluation provoque une exception, aucune étape supplémentaire n’est exécutées.</span><span class="sxs-lookup"><span data-stu-id="3d917-850">If this evaluation causes an exception, then no further steps are executed.</span></span>
   * <span data-ttu-id="3d917-851">Si `E` n’est pas classé comme une variable, puis une variable locale temporaire du `E`du type est créé et la valeur de `E` est affecté à cette variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-851">If `E` is not classified as a variable, then a temporary local variable of `E`'s type is created and the value of `E` is assigned to that variable.</span></span> <span data-ttu-id="3d917-852">`E` est ensuite reclassé en tant que référence à cette variable locale temporaire.</span><span class="sxs-lookup"><span data-stu-id="3d917-852">`E` is then reclassified as a reference to that temporary local variable.</span></span> <span data-ttu-id="3d917-853">La variable temporaire est accessible en tant que `this` dans `M`, mais pas de quelque autre manière.</span><span class="sxs-lookup"><span data-stu-id="3d917-853">The temporary variable is accessible as `this` within `M`, but not in any other way.</span></span> <span data-ttu-id="3d917-854">Par conséquent, uniquement lorsque `E` est une véritable variable est-il permet à l’appelant afin d’observer les modifications qui `M` apporte à `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-854">Thus, only when `E` is a true variable is it possible for the caller to observe the changes that `M` makes to `this`.</span></span>
   * <span data-ttu-id="3d917-855">La liste d’arguments est évaluée comme décrit dans [listes d’arguments](expressions.md#argument-lists).</span><span class="sxs-lookup"><span data-stu-id="3d917-855">The argument list is evaluated as described in [Argument lists](expressions.md#argument-lists).</span></span>
   * <span data-ttu-id="3d917-856">`M` est appelé.</span><span class="sxs-lookup"><span data-stu-id="3d917-856">`M` is invoked.</span></span> <span data-ttu-id="3d917-857">La variable référencée par `E` devient la variable référencée par `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-857">The variable referenced by `E` becomes the variable referenced by `this`.</span></span>

*  <span data-ttu-id="3d917-858">Si `M` est une fonction membre d’instance déclarée dans un *reference_type*:</span><span class="sxs-lookup"><span data-stu-id="3d917-858">If `M` is an instance function member declared in a *reference_type*:</span></span>
   * <span data-ttu-id="3d917-859">`E` est évalué.</span><span class="sxs-lookup"><span data-stu-id="3d917-859">`E` is evaluated.</span></span> <span data-ttu-id="3d917-860">Si cette version d’évaluation provoque une exception, aucune étape supplémentaire n’est exécutées.</span><span class="sxs-lookup"><span data-stu-id="3d917-860">If this evaluation causes an exception, then no further steps are executed.</span></span>
   * <span data-ttu-id="3d917-861">La liste d’arguments est évaluée comme décrit dans [listes d’arguments](expressions.md#argument-lists).</span><span class="sxs-lookup"><span data-stu-id="3d917-861">The argument list is evaluated as described in [Argument lists](expressions.md#argument-lists).</span></span>
   * <span data-ttu-id="3d917-862">Si le type de `E` est un *value_type*, une conversion boxing ([conversions Boxing](types.md#boxing-conversions)) est effectuée pour convertir `E` à taper `object`, et `E` est considéré comme pour être de type `object` dans les étapes suivantes.</span><span class="sxs-lookup"><span data-stu-id="3d917-862">If the type of `E` is a *value_type*, a boxing conversion ([Boxing conversions](types.md#boxing-conversions)) is performed to convert `E` to type `object`, and `E` is considered to be of type `object` in the following steps.</span></span> <span data-ttu-id="3d917-863">Dans ce cas, `M` peut uniquement appartenir à de `System.Object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-863">In this case, `M` could only be a member of `System.Object`.</span></span>
   * <span data-ttu-id="3d917-864">La valeur de `E` est vérifié pour être valide.</span><span class="sxs-lookup"><span data-stu-id="3d917-864">The value of `E` is checked to be valid.</span></span> <span data-ttu-id="3d917-865">Si la valeur de `E` est `null`, un `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="3d917-865">If the value of `E` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
   * <span data-ttu-id="3d917-866">L’implémentation de fonction membre à appeler est déterminée :</span><span class="sxs-lookup"><span data-stu-id="3d917-866">The function member implementation to invoke is determined:</span></span>
     * <span data-ttu-id="3d917-867">Si le type de la liaison et l’heure de `E` est une interface, la fonction membre à appeler est l’implémentation de `M` fournies par le type au moment de l’exécution de l’instance référencée par `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-867">If the binding-time type of `E` is an interface, the function member to invoke is the implementation of `M` provided by the run-time type of the instance referenced by `E`.</span></span> <span data-ttu-id="3d917-868">Cette fonction membre est déterminée en appliquant les règles de mappage d’interface ([mappage d’Interface](interfaces.md#interface-mapping)) pour déterminer l’implémentation de `M` fournies par le type au moment de l’exécution de l’instance référencée par `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-868">This function member is determined by applying the interface mapping rules ([Interface mapping](interfaces.md#interface-mapping)) to determine the implementation of `M` provided by the run-time type of the instance referenced by `E`.</span></span>
     * <span data-ttu-id="3d917-869">Sinon, si `M` est une fonction membre virtuelle, la fonction membre à appeler est l’implémentation de `M` fournies par le type au moment de l’exécution de l’instance référencée par `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-869">Otherwise, if `M` is a virtual function member, the function member to invoke is the implementation of `M` provided by the run-time type of the instance referenced by `E`.</span></span> <span data-ttu-id="3d917-870">Cette fonction membre est déterminée en appliquant les règles pour déterminer l’implémentation la plus dérivée ([les méthodes virtuelles](classes.md#virtual-methods)) de `M` en ce qui concerne le type au moment de l’exécution de l’instance référencée par `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-870">This function member is determined by applying the rules for determining the most derived implementation ([Virtual methods](classes.md#virtual-methods)) of `M` with respect to the run-time type of the instance referenced by `E`.</span></span>
     * <span data-ttu-id="3d917-871">Sinon, `M` est une fonction membre non virtuelle, et la fonction membre à appeler est `M` lui-même.</span><span class="sxs-lookup"><span data-stu-id="3d917-871">Otherwise, `M` is a non-virtual function member, and the function member to invoke is `M` itself.</span></span>
   * <span data-ttu-id="3d917-872">L’implémentation de fonction membre déterminée à l’étape précédente est appelée.</span><span class="sxs-lookup"><span data-stu-id="3d917-872">The function member implementation determined in the step above is invoked.</span></span> <span data-ttu-id="3d917-873">L’objet référencé par `E` devient l’objet référencé par `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-873">The object referenced by `E` becomes the object referenced by `this`.</span></span>

#### <a name="invocations-on-boxed-instances"></a><span data-ttu-id="3d917-874">Appels sur les instances</span><span class="sxs-lookup"><span data-stu-id="3d917-874">Invocations on boxed instances</span></span>

<span data-ttu-id="3d917-875">Une fonction membre implémenté dans un *value_type* peut être appelée via une instance boxed qui *value_type* dans les situations suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-875">A function member implemented in a *value_type* can be invoked through a boxed instance of that *value_type* in the following situations:</span></span>

*  <span data-ttu-id="3d917-876">Lorsque la fonction membre est un `override` d’une méthode héritée de type `object` et est appelée via une expression d’instance de type `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-876">When the function member is an `override` of a method inherited from type `object` and is invoked through an instance expression of type `object`.</span></span>
*  <span data-ttu-id="3d917-877">Lorsque la fonction membre est une implémentation d’une fonction membre d’interface et est appelé via une expression d’instance d’un *interface_type*.</span><span class="sxs-lookup"><span data-stu-id="3d917-877">When the function member is an implementation of an interface function member and is invoked through an instance expression of an *interface_type*.</span></span>
*  <span data-ttu-id="3d917-878">Lorsque la fonction membre est appelé via un délégué.</span><span class="sxs-lookup"><span data-stu-id="3d917-878">When the function member is invoked through a delegate.</span></span>

<span data-ttu-id="3d917-879">Dans ces situations, l’instance boxed est considérée comme contenant une variable de la *value_type*, et cette variable devient la variable référencée par `this` au sein de l’appel de fonction membre.</span><span class="sxs-lookup"><span data-stu-id="3d917-879">In these situations, the boxed instance is considered to contain a variable of the *value_type*, and this variable becomes the variable referenced by `this` within the function member invocation.</span></span> <span data-ttu-id="3d917-880">En particulier, cela signifie que lorsqu’une fonction membre est appelée sur une instance boxed, il est possible pour le membre de fonction Modifier la valeur contenue dans l’instance boxed.</span><span class="sxs-lookup"><span data-stu-id="3d917-880">In particular, this means that when a function member is invoked on a boxed instance, it is possible for the function member to modify the value contained in the boxed instance.</span></span>

## <a name="primary-expressions"></a><span data-ttu-id="3d917-881">Expressions primaires</span><span class="sxs-lookup"><span data-stu-id="3d917-881">Primary expressions</span></span>

<span data-ttu-id="3d917-882">Expressions primaires incluent les formes les plus simples d’expressions.</span><span class="sxs-lookup"><span data-stu-id="3d917-882">Primary expressions include the simplest forms of expressions.</span></span>

```antlr
primary_expression
    : primary_no_array_creation_expression
    | array_creation_expression
    ;

primary_no_array_creation_expression
    : literal
    | interpolated_string_expression
    | simple_name
    | parenthesized_expression
    | member_access
    | invocation_expression
    | element_access
    | this_access
    | base_access
    | post_increment_expression
    | post_decrement_expression
    | object_creation_expression
    | delegate_creation_expression
    | anonymous_object_creation_expression
    | typeof_expression
    | checked_expression
    | unchecked_expression
    | default_value_expression
    | nameof_expression
    | anonymous_method_expression
    | primary_no_array_creation_expression_unsafe
    ;
```

<span data-ttu-id="3d917-883">Expressions primaires sont réparties entre *array_creation_expression*s et *primary_no_array_creation_expression*s.</span><span class="sxs-lookup"><span data-stu-id="3d917-883">Primary expressions are divided between *array_creation_expression*s and *primary_no_array_creation_expression*s.</span></span> <span data-ttu-id="3d917-884">Traitement d’expression de création de tableau de cette façon, au lieu de répertorier, ainsi que d’autres formes d’expression simple, permet à la grammaire interdire pouvant prêter à confusion code tel que</span><span class="sxs-lookup"><span data-stu-id="3d917-884">Treating array-creation-expression in this way, rather than listing it along with the other simple expression forms, enables the grammar to disallow potentially confusing code such as</span></span>
```csharp
object o = new int[3][1];
```
<span data-ttu-id="3d917-885">ce qui pourrait être interprété en tant que</span><span class="sxs-lookup"><span data-stu-id="3d917-885">which would otherwise be interpreted as</span></span>
```csharp
object o = (new int[3])[1];
```

### <a name="literals"></a><span data-ttu-id="3d917-886">Littéraux</span><span class="sxs-lookup"><span data-stu-id="3d917-886">Literals</span></span>

<span data-ttu-id="3d917-887">Un *primary_expression* qui se compose d’un *littéral* ([littéraux](lexical-structure.md#literals)) est classé en tant que valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-887">A *primary_expression* that consists of a *literal* ([Literals](lexical-structure.md#literals)) is classified as a value.</span></span>


### <a name="interpolated-strings"></a><span data-ttu-id="3d917-888">Chaînes interpolées</span><span class="sxs-lookup"><span data-stu-id="3d917-888">Interpolated strings</span></span>

<span data-ttu-id="3d917-889">Un *interpolated_string_expression* se compose d’un `$` connexion suivies d’une chaîne régulier ou textuelle literal, où les trous, délimitées par `{` et `}`, placez les expressions et mise en forme spécifications.</span><span class="sxs-lookup"><span data-stu-id="3d917-889">An *interpolated_string_expression* consists of a `$` sign followed by a regular or verbatim string literal, wherein holes, delimited by `{` and `}`, enclose expressions and formatting specifications.</span></span> <span data-ttu-id="3d917-890">Une expression de chaîne interpolée est le résultat d’une *interpolated_string_literal* qui a été divisé en jetons individuels, comme décrit dans [interpolées littéraux de chaîne](lexical-structure.md#interpolated-string-literals).</span><span class="sxs-lookup"><span data-stu-id="3d917-890">An interpolated string expression is the result of an *interpolated_string_literal* that has been broken up into individual tokens, as described in [Interpolated string literals](lexical-structure.md#interpolated-string-literals).</span></span>

```antlr
interpolated_string_expression
    : '$' interpolated_regular_string
    | '$' interpolated_verbatim_string
    ;

interpolated_regular_string
    : interpolated_regular_string_whole
    | interpolated_regular_string_start interpolated_regular_string_body interpolated_regular_string_end
    ;

interpolated_regular_string_body
    : interpolation (interpolated_regular_string_mid interpolation)*
    ;

interpolation
    : expression
    | expression ',' constant_expression
    ;

interpolated_verbatim_string
    : interpolated_verbatim_string_whole
    | interpolated_verbatim_string_start interpolated_verbatim_string_body interpolated_verbatim_string_end
    ;

interpolated_verbatim_string_body
    : interpolation (interpolated_verbatim_string_mid interpolation)+
    ;
```

<span data-ttu-id="3d917-891">Le *constant_expression* une interpolation doit avoir une conversion implicite vers `int`.</span><span class="sxs-lookup"><span data-stu-id="3d917-891">The *constant_expression* in an interpolation must have an implicit conversion to `int`.</span></span>

<span data-ttu-id="3d917-892">Un *interpolated_string_expression* est classé en tant que valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-892">An *interpolated_string_expression* is classified as a value.</span></span> <span data-ttu-id="3d917-893">Si elle est convertie immédiatement en `System.IFormattable` ou `System.FormattableString` avec une conversion implicite de chaîne interpolée ([implicite interpolées conversions de chaînes](conversions.md#implicit-interpolated-string-conversions)), l’expression de chaîne interpolée est du même type.</span><span class="sxs-lookup"><span data-stu-id="3d917-893">If it is immediately converted to `System.IFormattable` or `System.FormattableString` with an implicit interpolated string conversion ([Implicit interpolated string conversions](conversions.md#implicit-interpolated-string-conversions)), the interpolated string expression has that type.</span></span> <span data-ttu-id="3d917-894">Sinon, elle a le type `string`.</span><span class="sxs-lookup"><span data-stu-id="3d917-894">Otherwise, it has the type `string`.</span></span>

<span data-ttu-id="3d917-895">Si le type d’une chaîne interpolée est `System.IFormattable` ou `System.FormattableString`, la signification est un appel à `System.Runtime.CompilerServices.FormattableStringFactory.Create`.</span><span class="sxs-lookup"><span data-stu-id="3d917-895">If the type of an interpolated string is `System.IFormattable` or `System.FormattableString`, the meaning is a call to `System.Runtime.CompilerServices.FormattableStringFactory.Create`.</span></span> <span data-ttu-id="3d917-896">Si le type est `string`, la signification de l’expression est un appel à `string.Format`.</span><span class="sxs-lookup"><span data-stu-id="3d917-896">If the type is `string`, the meaning of the expression is a call to `string.Format`.</span></span> <span data-ttu-id="3d917-897">Dans les deux cas, la liste d’arguments de l’appel se compose d’une chaîne de format littérale avec des espaces réservés pour chaque interpolation et un argument pour chaque expression correspondant pour les espaces réservés.</span><span class="sxs-lookup"><span data-stu-id="3d917-897">In both cases, the argument list of the call consists of a format string literal with placeholders for each interpolation, and an argument for each expression corresponding to the place holders.</span></span>

<span data-ttu-id="3d917-898">Le littéral de chaîne de format est construit comme suit, où `N` est le nombre d’interpolations dans le *interpolated_string_expression*:</span><span class="sxs-lookup"><span data-stu-id="3d917-898">The format string literal is constructed as follows, where `N` is the number of interpolations in the *interpolated_string_expression*:</span></span>

*  <span data-ttu-id="3d917-899">Si un *interpolated_regular_string_whole* ou un *interpolated_verbatim_string_whole* suit le `$` signer, puis le littéral de chaîne de format est ce jeton.</span><span class="sxs-lookup"><span data-stu-id="3d917-899">If an *interpolated_regular_string_whole* or an *interpolated_verbatim_string_whole* follows the `$` sign, then the format string literal is that token.</span></span>
*  <span data-ttu-id="3d917-900">Sinon, le littéral de chaîne de format se compose de :</span><span class="sxs-lookup"><span data-stu-id="3d917-900">Otherwise, the format string literal consists of:</span></span> 
   *  <span data-ttu-id="3d917-901">Première le *interpolated_regular_string_start* ou *interpolated_verbatim_string_start*</span><span class="sxs-lookup"><span data-stu-id="3d917-901">First the *interpolated_regular_string_start* or *interpolated_verbatim_string_start*</span></span>
   *  <span data-ttu-id="3d917-902">Puis pour chaque numéro `I` de `0` à `N-1`:</span><span class="sxs-lookup"><span data-stu-id="3d917-902">Then for each number `I` from `0` to `N-1`:</span></span> 
      * <span data-ttu-id="3d917-903">Représentation décimale de `I`</span><span class="sxs-lookup"><span data-stu-id="3d917-903">The decimal representation of `I`</span></span>
      * <span data-ttu-id="3d917-904">Puis, si le correspondant *interpolation* a un *constant_expression*, un `,` (virgule), suivie par la représentation décimale de la valeur de la *constant_expression*</span><span class="sxs-lookup"><span data-stu-id="3d917-904">Then, if the corresponding *interpolation* has a *constant_expression*, a `,` (comma) followed by the decimal representation of the value of the *constant_expression*</span></span>
      * <span data-ttu-id="3d917-905">Le *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_mid* ou *interpolated_ verbatim_string_end* qui suit immédiatement l’interpolation correspondante.</span><span class="sxs-lookup"><span data-stu-id="3d917-905">Then the *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_mid* or *interpolated_verbatim_string_end* immediately following the corresponding interpolation.</span></span>

<span data-ttu-id="3d917-906">Les arguments suivants sont tout simplement le *expressions* à partir de la *interpolations* (le cas échéant), dans l’ordre.</span><span class="sxs-lookup"><span data-stu-id="3d917-906">The subsequent arguments are simply the *expressions* from the *interpolations* (if any), in order.</span></span>

<span data-ttu-id="3d917-907">TODO : exemples.</span><span class="sxs-lookup"><span data-stu-id="3d917-907">TODO: examples.</span></span>


### <a name="simple-names"></a><span data-ttu-id="3d917-908">Noms simples</span><span class="sxs-lookup"><span data-stu-id="3d917-908">Simple names</span></span>

<span data-ttu-id="3d917-909">Un *simple_name* se compose d’un identificateur, éventuellement suivi d’une liste d’arguments de type :</span><span class="sxs-lookup"><span data-stu-id="3d917-909">A *simple_name* consists of an identifier, optionally followed by a type argument list:</span></span>

```antlr
simple_name
    : identifier type_argument_list?
    ;
```

<span data-ttu-id="3d917-910">Un *simple_name* est soit sous la forme `I` ou sous la forme `I<A1,...,Ak>`, où `I` est un identificateur unique et `<A1,...,Ak>` est facultative *type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="3d917-910">A *simple_name* is either of the form `I` or of the form `I<A1,...,Ak>`, where `I` is a single identifier and `<A1,...,Ak>` is an optional *type_argument_list*.</span></span> <span data-ttu-id="3d917-911">En cas de non *type_argument_list* est spécifié, envisagez `K` à zéro.</span><span class="sxs-lookup"><span data-stu-id="3d917-911">When no *type_argument_list* is specified, consider `K` to be zero.</span></span> <span data-ttu-id="3d917-912">Le *simple_name* est évalué et classé comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-912">The *simple_name* is evaluated and classified as follows:</span></span>

*  <span data-ttu-id="3d917-913">Si `K` est égal à zéro et le *simple_name* apparaît dans un *bloc* et si le *bloc*du (ou une englobante *bloc*du) local espace de déclaration de variable ([déclarations](basic-concepts.md#declarations)) contient une variable locale, paramètre ou une constante avec le nom `I`, puis le *simple_name* fait référence à cette variable locale, paramètre ou une constante et est considéré comme une variable ou une valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-913">If `K` is zero and the *simple_name* appears within a *block* and if the *block*'s (or an enclosing *block*'s) local variable declaration space ([Declarations](basic-concepts.md#declarations)) contains a local variable, parameter or constant with name `I`, then the *simple_name* refers to that local variable, parameter or constant and is classified as a variable or value.</span></span>
*  <span data-ttu-id="3d917-914">Si `K` est égal à zéro et le *simple_name* s’affiche dans le corps d’une déclaration de méthode générique et si cette déclaration inclut un paramètre de type avec nom `I`, puis le *simple_name*fait référence à ce paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-914">If `K` is zero and the *simple_name* appears within the body of a generic method declaration and if that declaration includes a type parameter with name `I`, then the *simple_name* refers to that type parameter.</span></span>
*  <span data-ttu-id="3d917-915">Sinon, pour chaque type d’instance `T` ([le type d’instance](classes.md#the-instance-type)), en commençant par le type d’instance de la déclaration de type immédiatement englobante et en continuant avec le type d’instance de chaque classe ou struct englobant déclaration (le cas échéant) :</span><span class="sxs-lookup"><span data-stu-id="3d917-915">Otherwise, for each instance type `T` ([The instance type](classes.md#the-instance-type)), starting with the instance type of the immediately enclosing type declaration and continuing with the instance type of each enclosing class or struct declaration (if any):</span></span>
   *  <span data-ttu-id="3d917-916">Si `K` est zéro et la déclaration de `T` inclut un paramètre de type avec nom `I`, puis le *simple_name* fait référence à ce paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-916">If `K` is zero and the declaration of `T` includes a type parameter with name `I`, then the *simple_name* refers to that type parameter.</span></span>
   *  <span data-ttu-id="3d917-917">Sinon, si une recherche de membres ([recherche de membres](expressions.md#member-lookup)) de `I` dans `T` avec `K`  arguments de type génère une correspondance :</span><span class="sxs-lookup"><span data-stu-id="3d917-917">Otherwise, if a member lookup ([Member lookup](expressions.md#member-lookup)) of `I` in `T` with `K` type arguments produces a match:</span></span>
      * <span data-ttu-id="3d917-918">Si `T` est le type d’instance du type classe ou struct englobant immédiatement et la recherche indique qu’une ou plusieurs méthodes, le résultat est un groupe de méthodes avec une expression d’instance associée de `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-918">If `T` is the instance type of the immediately enclosing class or struct type and the lookup identifies one or more methods, the result is a method group with an associated instance expression of `this`.</span></span> <span data-ttu-id="3d917-919">Si une liste d’arguments de type a été spécifiée, il est utilisé dans l’appel d’une méthode générique ([appels de méthode](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="3d917-919">If a type argument list was specified, it is used in calling a generic method ([Method invocations](expressions.md#method-invocations)).</span></span>
      * <span data-ttu-id="3d917-920">Sinon, si `T` est le type d’instance du type classe ou struct immédiatement englobant, si la recherche identifie un membre d’instance, et si la référence se produit dans le corps d’un constructeur d’instance, une méthode d’instance ou un accesseur d’instance, le Il en résulte le même qu’un accès de membre ([l’accès au membre](expressions.md#member-access)) sous la forme `this.I`.</span><span class="sxs-lookup"><span data-stu-id="3d917-920">Otherwise, if `T` is the instance type of the immediately enclosing class or struct type, if the lookup identifies an instance member, and if the reference occurs within the body of an instance constructor, an instance method, or an instance accessor, the result is the same as a member access ([Member access](expressions.md#member-access)) of the form `this.I`.</span></span> <span data-ttu-id="3d917-921">Cela peut uniquement se produire lorsque `K` est égal à zéro.</span><span class="sxs-lookup"><span data-stu-id="3d917-921">This can only happen when `K` is zero.</span></span>
      * <span data-ttu-id="3d917-922">Sinon, le résultat est identique à un accès au membre ([l’accès au membre](expressions.md#member-access)) sous la forme `T.I` ou `T.I<A1,...,Ak>`.</span><span class="sxs-lookup"><span data-stu-id="3d917-922">Otherwise, the result is the same as a member access ([Member access](expressions.md#member-access)) of the form `T.I` or `T.I<A1,...,Ak>`.</span></span> <span data-ttu-id="3d917-923">Dans ce cas, il est une erreur au moment de la liaison pour le *simple_name* pour faire référence à un membre d’instance.</span><span class="sxs-lookup"><span data-stu-id="3d917-923">In this case, it is a binding-time error for the *simple_name* to refer to an instance member.</span></span>

*  <span data-ttu-id="3d917-924">Sinon, pour chaque espace de noms `N`, en commençant par l’espace de noms dans lequel le *simple_name* se produit, en continuant avec chaque espace de noms (le cas échéant) englobant et se terminant par l’espace de noms global, les étapes suivantes sont évaluée jusqu'à ce qu’une entité se trouve :</span><span class="sxs-lookup"><span data-stu-id="3d917-924">Otherwise, for each namespace `N`, starting with the namespace in which the *simple_name* occurs, continuing with each enclosing namespace (if any), and ending with the global namespace, the following steps are evaluated until an entity is located:</span></span>
   *  <span data-ttu-id="3d917-925">Si `K` est égal à zéro et `I` est le nom d’un espace de noms `N`, puis :</span><span class="sxs-lookup"><span data-stu-id="3d917-925">If `K` is zero and `I` is the name of a namespace in `N`, then:</span></span>
      * <span data-ttu-id="3d917-926">Si l’emplacement où le *simple_name* se produit est délimitée par une déclaration d’espace de noms pour `N` et la déclaration d’espace de noms contient un *extern_alias_directive* ou  *using_alias_directive* qui associe le nom `I` avec un espace de noms ou un type, puis le *simple_name* est ambigu et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-926">If the location where the *simple_name* occurs is enclosed by a namespace declaration for `N` and the namespace declaration contains an *extern_alias_directive* or *using_alias_directive* that associates the name `I` with a namespace or type, then the *simple_name* is ambiguous and a compile-time error occurs.</span></span>
      * <span data-ttu-id="3d917-927">Sinon, le *simple_name* fait référence à l’espace de noms nommé `I` dans `N`.</span><span class="sxs-lookup"><span data-stu-id="3d917-927">Otherwise, the *simple_name* refers to the namespace named `I` in `N`.</span></span>
   *  <span data-ttu-id="3d917-928">Sinon, si `N` contient un nom de type accessible `I` et `K`  paramètres de type, puis :</span><span class="sxs-lookup"><span data-stu-id="3d917-928">Otherwise, if `N` contains an accessible type having name `I` and `K` type parameters, then:</span></span>
      * <span data-ttu-id="3d917-929">Si `K` est égal à zéro et l’emplacement où le *simple_name* se produit est délimitée par une déclaration d’espace de noms pour `N` et la déclaration d’espace de noms contient un *extern_alias_directive*ou *using_alias_directive* qui associe le nom `I` avec un espace de noms ou un type, puis le *simple_name* est ambigu et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-929">If `K` is zero and the location where the *simple_name* occurs is enclosed by a namespace declaration for `N` and the namespace declaration contains an *extern_alias_directive* or *using_alias_directive* that associates the name `I` with a namespace or type, then the *simple_name* is ambiguous and a compile-time error occurs.</span></span>
      * <span data-ttu-id="3d917-930">Sinon, le *namespace_or_type_name* fait référence au type construit avec les arguments de type donné.</span><span class="sxs-lookup"><span data-stu-id="3d917-930">Otherwise, the *namespace_or_type_name* refers to the type constructed with the given type arguments.</span></span>
   *  <span data-ttu-id="3d917-931">Sinon, si l’emplacement où le *simple_name* se produit est délimitée par une déclaration d’espace de noms pour `N`:</span><span class="sxs-lookup"><span data-stu-id="3d917-931">Otherwise, if the location where the *simple_name* occurs is enclosed by a namespace declaration for `N`:</span></span>
      * <span data-ttu-id="3d917-932">Si `K` est égal à zéro et la déclaration d’espace de noms contient un *extern_alias_directive* ou *using_alias_directive* qui associe le nom `I` avec un espace de noms importé ou type, puis le *simple_name* fait référence à cet espace de noms ou un type.</span><span class="sxs-lookup"><span data-stu-id="3d917-932">If `K` is zero and the namespace declaration contains an *extern_alias_directive* or *using_alias_directive* that associates the name `I` with an imported namespace or type, then the *simple_name* refers to that namespace or type.</span></span>
      * <span data-ttu-id="3d917-933">Sinon, si les déclarations d’espaces de noms et le type importé par le *using_namespace_directive*s et *using_static_directive*s de la déclaration d’espace de noms contiennent exactement un type accessible ou membre statique sans extension ayant le nom `I` et `K`  paramètres de type, puis le *simple_name* fait référence à ce type ou membre construit avec les arguments de type donné.</span><span class="sxs-lookup"><span data-stu-id="3d917-933">Otherwise, if the namespaces and type declarations imported by the *using_namespace_directive*s and *using_static_directive*s of the namespace declaration contain exactly one accessible type or non-extension static member having name `I` and `K` type parameters, then the *simple_name* refers to that type or member constructed with the given type arguments.</span></span>
      * <span data-ttu-id="3d917-934">Sinon, si les espaces de noms et les types importés par le *using_namespace_directive*s de la déclaration d’espace de noms contiennent plus d’un type accessible ou non--méthode d’extension de membre statique ayant le nom `I` et `K`  paramètres de type, puis le *simple_name* est ambigu et une erreur se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-934">Otherwise, if the namespaces and types imported by the *using_namespace_directive*s of the namespace declaration contain more than one accessible type or non-extension-method static member having name `I` and `K` type parameters, then the *simple_name* is ambiguous and an error occurs.</span></span>

   <span data-ttu-id="3d917-935">Notez que cette étape est exactement parallèlement à l’étape correspondante dans le traitement d’un *namespace_or_type_name* ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)).</span><span class="sxs-lookup"><span data-stu-id="3d917-935">Note that this entire step is exactly parallel to the corresponding step in the processing of a *namespace_or_type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)).</span></span>

*  <span data-ttu-id="3d917-936">Sinon, le *simple_name* est non défini et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-936">Otherwise, the *simple_name* is undefined and a compile-time error occurs.</span></span>


### <a name="parenthesized-expressions"></a><span data-ttu-id="3d917-937">Expressions entre parenthèses</span><span class="sxs-lookup"><span data-stu-id="3d917-937">Parenthesized expressions</span></span>

<span data-ttu-id="3d917-938">Un *parenthesized_expression* se compose d’un *expression* placés entre parenthèses.</span><span class="sxs-lookup"><span data-stu-id="3d917-938">A *parenthesized_expression* consists of an *expression* enclosed in parentheses.</span></span>

```antlr
parenthesized_expression
    : '(' expression ')'
    ;
```

<span data-ttu-id="3d917-939">Un *parenthesized_expression* est évaluée en évaluant le *expression* entre parenthèses.</span><span class="sxs-lookup"><span data-stu-id="3d917-939">A *parenthesized_expression* is evaluated by evaluating the *expression* within the parentheses.</span></span> <span data-ttu-id="3d917-940">Si le *expression* entre les parenthèses qui désigne un espace de noms ou un type, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-940">If the *expression* within the parentheses denotes a namespace or type, a compile-time error occurs.</span></span> <span data-ttu-id="3d917-941">Sinon, le résultat de la *parenthesized_expression* est le résultat de l’évaluation de la relation contenant-contenu *expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-941">Otherwise, the result of the *parenthesized_expression* is the result of the evaluation of the contained *expression*.</span></span>

### <a name="member-access"></a><span data-ttu-id="3d917-942">Accès au membre</span><span class="sxs-lookup"><span data-stu-id="3d917-942">Member access</span></span>

<span data-ttu-id="3d917-943">Un *member_access* se compose d’un *primary_expression*, un *predefined_type*, ou un *qualified_alias_member*, suivi par un»`.`« jeton, suivi par un *identificateur*, suivie éventuellement un *type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="3d917-943">A *member_access* consists of a *primary_expression*, a *predefined_type*, or a *qualified_alias_member*, followed by a "`.`" token, followed by an *identifier*, optionally followed by a *type_argument_list*.</span></span>

```antlr
member_access
    : primary_expression '.' identifier type_argument_list?
    | predefined_type '.' identifier type_argument_list?
    | qualified_alias_member '.' identifier
    ;

predefined_type
    : 'bool'   | 'byte'  | 'char'  | 'decimal' | 'double' | 'float' | 'int' | 'long'
    | 'object' | 'sbyte' | 'short' | 'string'  | 'uint'   | 'ulong' | 'ushort'
    ;
```

<span data-ttu-id="3d917-944">Le *qualified_alias_member* production est définie dans [qualificateurs d’alias Namespace](namespaces.md#namespace-alias-qualifiers).</span><span class="sxs-lookup"><span data-stu-id="3d917-944">The *qualified_alias_member* production is defined in [Namespace alias qualifiers](namespaces.md#namespace-alias-qualifiers).</span></span>

<span data-ttu-id="3d917-945">Un *member_access* est soit sous la forme `E.I` ou sous la forme `E.I<A1, ..., Ak>`, où `E` est une expression primaire, `I` est un identificateur unique et `<A1, ..., Ak>` est facultatif  *type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="3d917-945">A *member_access* is either of the form `E.I` or of the form `E.I<A1, ..., Ak>`, where `E` is a primary-expression, `I` is a single identifier and `<A1, ..., Ak>` is an optional *type_argument_list*.</span></span> <span data-ttu-id="3d917-946">En cas de non *type_argument_list* est spécifié, envisagez `K` à zéro.</span><span class="sxs-lookup"><span data-stu-id="3d917-946">When no *type_argument_list* is specified, consider `K` to be zero.</span></span>

<span data-ttu-id="3d917-947">Un *member_access* avec un *primary_expression* de type `dynamic` lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="3d917-947">A *member_access* with a *primary_expression* of type `dynamic` is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="3d917-948">Dans ce cas le compilateur classifie l’accès au membre en tant qu’un accès à la propriété de type `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-948">In this case the compiler classifies the member access as a property access of type `dynamic`.</span></span> <span data-ttu-id="3d917-949">Les règles ci-dessous pour déterminer la signification de la *member_access* sont ensuite appliquées au moment de l’exécution, à l’aide du type au moment de l’exécution au lieu du type de compilation de la *primary_expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-949">The rules below to determine the meaning of the *member_access* are then applied at run-time, using the run-time type instead of the compile-time type of the *primary_expression*.</span></span> <span data-ttu-id="3d917-950">Si cette classification exécution mène à un groupe de méthodes, l’accès au membre doit être le *primary_expression* d’un *invocation_expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-950">If this run-time classification leads to a method group, then the member access must be the *primary_expression* of an *invocation_expression*.</span></span>

<span data-ttu-id="3d917-951">Le *member_access* est évalué et classé comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-951">The *member_access* is evaluated and classified as follows:</span></span>

*  <span data-ttu-id="3d917-952">Si `K` est égal à zéro et `E` est un espace de noms et `E` contient un espace de noms imbriqué avec le nom `I`, le résultat est de cet espace de noms.</span><span class="sxs-lookup"><span data-stu-id="3d917-952">If `K` is zero and `E` is a namespace and `E` contains a nested namespace with name `I`, then the result is that namespace.</span></span>
*  <span data-ttu-id="3d917-953">Sinon, si `E` est un espace de noms et `E` contient un nom de type accessible `I` et `K`  paramètres de type, le résultat est ce type construit avec les arguments de type donné.</span><span class="sxs-lookup"><span data-stu-id="3d917-953">Otherwise, if `E` is a namespace and `E` contains an accessible type having name `I` and `K` type parameters, then the result is that type constructed with the given type arguments.</span></span>
*  <span data-ttu-id="3d917-954">Si `E` est un *predefined_type* ou un *primary_expression* classés en tant que type, si `E` n’est pas un paramètre de type et si une recherche de membres ([recherche de membres](expressions.md#member-lookup)) de `I` dans `E` avec `K`  paramètres de type génère une correspondance, puis `E.I` est évalué et classé comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-954">If `E` is a *predefined_type* or a *primary_expression* classified as a type, if `E` is not a type parameter, and if a member lookup ([Member lookup](expressions.md#member-lookup)) of `I` in `E` with `K` type parameters produces a match, then `E.I` is evaluated and classified as follows:</span></span>
   *  <span data-ttu-id="3d917-955">Si `I` identifie un type, le résultat est ce type construit avec les arguments de type donné.</span><span class="sxs-lookup"><span data-stu-id="3d917-955">If `I` identifies a type, then the result is that type constructed with the given type arguments.</span></span>
   *  <span data-ttu-id="3d917-956">Si `I` identifie une ou plusieurs méthodes, le résultat est un groupe de méthodes sans expression d’instance associée.</span><span class="sxs-lookup"><span data-stu-id="3d917-956">If `I` identifies one or more methods, then the result is a method group with no associated instance expression.</span></span> <span data-ttu-id="3d917-957">Si une liste d’arguments de type a été spécifiée, il est utilisé dans l’appel d’une méthode générique ([appels de méthode](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="3d917-957">If a type argument list was specified, it is used in calling a generic method ([Method invocations](expressions.md#method-invocations)).</span></span>
   *  <span data-ttu-id="3d917-958">Si `I` identifie un `static` propriété, le résultat est un accès à la propriété sans expression d’instance associée.</span><span class="sxs-lookup"><span data-stu-id="3d917-958">If `I` identifies a `static` property, then the result is a property access with no associated instance expression.</span></span>
   *  <span data-ttu-id="3d917-959">Si `I` identifie un `static` champ :</span><span class="sxs-lookup"><span data-stu-id="3d917-959">If `I` identifies a `static` field:</span></span>
      * <span data-ttu-id="3d917-960">Si le champ est `readonly` et la référence se produit en dehors du constructeur statique de la classe ou structure dans laquelle le champ est déclaré, le résultat est une valeur, à savoir la valeur du champ static `I` dans `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-960">If the field is `readonly` and the reference occurs outside the static constructor of the class or struct in which the field is declared, then the result is a value, namely the value of the static field `I` in `E`.</span></span>
      * <span data-ttu-id="3d917-961">Sinon, le résultat est une variable, à savoir le champ statique `I` dans `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-961">Otherwise, the result is a variable, namely the static field `I` in `E`.</span></span>
   *  <span data-ttu-id="3d917-962">Si `I` identifie un `static` événement :</span><span class="sxs-lookup"><span data-stu-id="3d917-962">If `I` identifies a `static` event:</span></span>
      * <span data-ttu-id="3d917-963">Si la référence se produit au sein de la classe ou structure dans laquelle l’événement est déclaré, et l’événement a été déclaré sans *event_accessor_declarations* ([événements](classes.md#events)), puis `E.I` est traitée exactement comme si `I` ont un champ statique.</span><span class="sxs-lookup"><span data-stu-id="3d917-963">If the reference occurs within the class or struct in which the event is declared, and the event was declared without *event_accessor_declarations* ([Events](classes.md#events)), then `E.I` is processed exactly as if `I` were a static field.</span></span>
      * <span data-ttu-id="3d917-964">Sinon, le résultat est un accès à l’événement sans expression d’instance associée.</span><span class="sxs-lookup"><span data-stu-id="3d917-964">Otherwise, the result is an event access with no associated instance expression.</span></span>
   *  <span data-ttu-id="3d917-965">Si `I` identifie une constante, le résultat est une valeur, à savoir la valeur de cette constante.</span><span class="sxs-lookup"><span data-stu-id="3d917-965">If `I` identifies a constant, then the result is a value, namely the value of that constant.</span></span>
    * <span data-ttu-id="3d917-966">Si `I` identifie un membre d’énumération, le résultat est une valeur, à savoir la valeur de ce membre d’énumération.</span><span class="sxs-lookup"><span data-stu-id="3d917-966">If `I` identifies an enumeration member, then the result is a value, namely the value of that enumeration member.</span></span>
    * <span data-ttu-id="3d917-967">Sinon, `E.I` est une référence de membre non valide et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-967">Otherwise, `E.I` is an invalid member reference, and a compile-time error occurs.</span></span>
*  <span data-ttu-id="3d917-968">Si `E` est un accès à la propriété, un accès à un indexeur, une variable ou une valeur, dont le type est `T`et une recherche de membres ([recherche de membres](expressions.md#member-lookup)) de `I` dans `T` avec `K`  arguments de type génère une correspondance, puis `E.I` est évalué et classé comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-968">If `E` is a property access, indexer access, variable, or value, the type of which is `T`, and a member lookup ([Member lookup](expressions.md#member-lookup)) of `I` in `T` with `K` type arguments produces a match, then `E.I` is evaluated and classified as follows:</span></span>
   *  <span data-ttu-id="3d917-969">Tout d’abord, if `E` est une propriété ou indexeur, la valeur de la propriété ou l’accès à l’indexeur est obtenue ([valeurs des expressions](expressions.md#values-of-expressions)) et `E` est reclassé en tant que valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-969">First, if `E` is a property or indexer access, then the value of the property or indexer access is obtained ([Values of expressions](expressions.md#values-of-expressions)) and `E` is reclassified as a value.</span></span>
   *  <span data-ttu-id="3d917-970">Si `I` identifie une ou plusieurs méthodes, le résultat est un groupe de méthodes avec une expression d’instance associée de `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-970">If `I` identifies one or more methods, then the result is a method group with an associated instance expression of `E`.</span></span> <span data-ttu-id="3d917-971">Si une liste d’arguments de type a été spécifiée, il est utilisé dans l’appel d’une méthode générique ([appels de méthode](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="3d917-971">If a type argument list was specified, it is used in calling a generic method ([Method invocations](expressions.md#method-invocations)).</span></span>
   *  <span data-ttu-id="3d917-972">Si `I` identifie une propriété d’instance,</span><span class="sxs-lookup"><span data-stu-id="3d917-972">If `I` identifies an instance property,</span></span>
      * <span data-ttu-id="3d917-973">Si `E` est `this`, `I` identifie une propriété implémentée automatiquement ([implémenté automatiquement les propriétés](classes.md#automatically-implemented-properties)) sans un accesseur Set et la référence se produit au sein d’un constructeur d’instance pour un type de classe ou struct `T`, le résultat est une variable, à savoir le champ de stockage masqué pour la propriété automatique fournie par `I` dans l’instance de `T` donné par `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-973">If `E` is `this`, `I` identifies an automatically implemented property ([Automatically implemented properties](classes.md#automatically-implemented-properties)) without a setter, and the reference occurs within an instance constructor for a class or struct type `T`, then the result is a variable, namely the hidden backing field for the auto-property given by `I` in the instance of `T` given by `this`.</span></span>
      * <span data-ttu-id="3d917-974">Sinon, le résultat est un accès à la propriété avec une expression d’instance associée de `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-974">Otherwise, the result is a property access with an associated instance expression of `E`.</span></span>
   *  <span data-ttu-id="3d917-975">Si `T` est un *class_type* et `I` identifie un champ d’instance de qui *class_type*:</span><span class="sxs-lookup"><span data-stu-id="3d917-975">If `T` is a *class_type* and `I` identifies an instance field of that *class_type*:</span></span>
      * <span data-ttu-id="3d917-976">Si la valeur de `E` est `null`, puis un `System.NullReferenceException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-976">If the value of `E` is `null`, then a `System.NullReferenceException` is thrown.</span></span>
      * <span data-ttu-id="3d917-977">Sinon, si le champ est `readonly` et la référence se produit en dehors d’un constructeur d’instance de la classe dans laquelle le champ est déclaré, le résultat est une valeur, à savoir la valeur du champ `I` dans l’objet référencé par `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-977">Otherwise, if the field is `readonly` and the reference occurs outside an instance constructor of the class in which the field is declared, then the result is a value, namely the value of the field `I` in the object referenced by `E`.</span></span>
      * <span data-ttu-id="3d917-978">Sinon, le résultat est une variable, à savoir le champ `I` dans l’objet référencé par `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-978">Otherwise, the result is a variable, namely the field `I` in the object referenced by `E`.</span></span>
   *  <span data-ttu-id="3d917-979">Si `T` est un *struct_type* et `I` identifie un champ d’instance de qui *struct_type*:</span><span class="sxs-lookup"><span data-stu-id="3d917-979">If `T` is a *struct_type* and `I` identifies an instance field of that *struct_type*:</span></span>
      * <span data-ttu-id="3d917-980">Si `E` est une valeur, ou si le champ est `readonly` et la référence se produit en dehors d’un constructeur d’instance du struct dans lequel le champ est déclaré, le résultat est une valeur, à savoir la valeur du champ `I` dans l’instance de struct donnée par  `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-980">If `E` is a value, or if the field is `readonly` and the reference occurs outside an instance constructor of the struct in which the field is declared, then the result is a value, namely the value of the field `I` in the struct instance given by `E`.</span></span>
      * <span data-ttu-id="3d917-981">Sinon, le résultat est une variable, à savoir le champ `I` dans l’instance de struct donnée par `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-981">Otherwise, the result is a variable, namely the field `I` in the struct instance given by `E`.</span></span>
   *  <span data-ttu-id="3d917-982">Si `I` identifie un événement d’instance :</span><span class="sxs-lookup"><span data-stu-id="3d917-982">If `I` identifies an instance event:</span></span>
      * <span data-ttu-id="3d917-983">Si la référence se produit au sein de la classe ou structure dans laquelle l’événement est déclaré, et l’événement a été déclaré sans *event_accessor_declarations* ([événements](classes.md#events)), et la référence ne se produit pas en tant que le côté gauche d’un `+=` ou `-=` opérateur, puis `E.I` est traitée exactement comme si `I` était un champ d’instance.</span><span class="sxs-lookup"><span data-stu-id="3d917-983">If the reference occurs within the class or struct in which the event is declared, and the event was declared without *event_accessor_declarations* ([Events](classes.md#events)), and the reference does not occur as the left-hand side of a `+=` or `-=` operator, then `E.I` is processed exactly as if `I` was an instance field.</span></span>
      * <span data-ttu-id="3d917-984">Sinon, le résultat est un accès à l’événement avec une expression d’instance associée de `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-984">Otherwise, the result is an event access with an associated instance expression of `E`.</span></span>
*  <span data-ttu-id="3d917-985">Sinon, une tentative est effectuée pour traiter les `E.I` comme un appel de méthode d’extension ([appels de méthode d’Extension](expressions.md#extension-method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="3d917-985">Otherwise, an attempt is made to process `E.I` as an extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)).</span></span> <span data-ttu-id="3d917-986">En cas d’échec, `E.I` est une référence de membre non valide, et se produit une erreur au moment de la liaison.</span><span class="sxs-lookup"><span data-stu-id="3d917-986">If this fails, `E.I` is an invalid member reference, and a binding-time error occurs.</span></span>

#### <a name="identical-simple-names-and-type-names"></a><span data-ttu-id="3d917-987">Des noms identiques simples et les noms de types</span><span class="sxs-lookup"><span data-stu-id="3d917-987">Identical simple names and type names</span></span>

<span data-ttu-id="3d917-988">Dans un accès de membre de la forme `E.I`, si `E` est un identificateur unique et si la signification de `E` comme un *simple_name* ([noms simples](expressions.md#simple-names)) est une constante, le champ, la propriété, variable locale ou un paramètre avec le même type que la signification de `E` comme un *type_name* ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)), puis les deux significations possibles de `E` sont autorisé.</span><span class="sxs-lookup"><span data-stu-id="3d917-988">In a member access of the form `E.I`, if `E` is a single identifier, and if the meaning of `E` as a *simple_name* ([Simple names](expressions.md#simple-names)) is a constant, field, property, local variable, or parameter with the same type as the meaning of `E` as a *type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)), then both possible meanings of `E` are permitted.</span></span> <span data-ttu-id="3d917-989">Les deux significations possibles de `E.I` ne sont jamais ambiguës, depuis `I` doit être nécessairement un membre du type `E` dans les deux cas.</span><span class="sxs-lookup"><span data-stu-id="3d917-989">The two possible meanings of `E.I` are never ambiguous, since `I` must necessarily be a member of the type `E` in both cases.</span></span> <span data-ttu-id="3d917-990">En d’autres termes, la règle permet simplement d’accéder aux membres statiques et les types imbriqués de `E` où une erreur de compilation se serait produite.</span><span class="sxs-lookup"><span data-stu-id="3d917-990">In other words, the rule simply permits access to the static members and nested types of `E` where a compile-time error would otherwise have occurred.</span></span> <span data-ttu-id="3d917-991">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3d917-991">For example:</span></span>
```csharp
struct Color
{
    public static readonly Color White = new Color(...);
    public static readonly Color Black = new Color(...);

    public Color Complement() {...}
}

class A
{
    public Color Color;                // Field Color of type Color

    void F() {
        Color = Color.Black;           // References Color.Black static member
        Color = Color.Complement();    // Invokes Complement() on Color field
    }

    static void G() {
        Color c = Color.White;         // References Color.White static member
    }
}
```

#### <a name="grammar-ambiguities"></a><span data-ttu-id="3d917-992">Ambiguïtés au niveau de grammaire</span><span class="sxs-lookup"><span data-stu-id="3d917-992">Grammar ambiguities</span></span>

<span data-ttu-id="3d917-993">Les productions pour *simple_name* ([noms simples](expressions.md#simple-names)) et *member_access* ([l’accès au membre](expressions.md#member-access)) peut donner lieu à des ambiguïtés dans le Grammaire des expressions.</span><span class="sxs-lookup"><span data-stu-id="3d917-993">The productions for *simple_name* ([Simple names](expressions.md#simple-names)) and *member_access* ([Member access](expressions.md#member-access)) can give rise to ambiguities in the grammar for expressions.</span></span> <span data-ttu-id="3d917-994">Par exemple, l’instruction :</span><span class="sxs-lookup"><span data-stu-id="3d917-994">For example, the statement:</span></span>
```
F(G<A,B>(7));
```
<span data-ttu-id="3d917-995">peut être interprétée comme un appel à `F` avec deux arguments, `G < A` et `B > (7)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-995">could be interpreted as a call to `F` with two arguments, `G < A` and `B > (7)`.</span></span> <span data-ttu-id="3d917-996">Sinon, elle peut être interprétée comme un appel à `F` avec un seul argument, qui est un appel à une méthode générique `G` avec deux arguments de type et un seul argument régulière.</span><span class="sxs-lookup"><span data-stu-id="3d917-996">Alternatively, it could be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument.</span></span>

<span data-ttu-id="3d917-997">Si une séquence de jetons peut être analysée (dans le contexte) comme un *simple_name* ([noms simples](expressions.md#simple-names)), *member_access* ([l’accès au membre](expressions.md#member-access)), ou *pointer_member_access* ([accès des membres pointeurs](unsafe-code.md#pointer-member-access)) se terminant par un *type_argument_list* ([arguments de Type](types.md#type-arguments)), le jeton immédiatement après la fermeture `>` jeton est examiné.</span><span class="sxs-lookup"><span data-stu-id="3d917-997">If a sequence of tokens can be parsed (in context) as a *simple_name* ([Simple names](expressions.md#simple-names)), *member_access* ([Member access](expressions.md#member-access)), or *pointer_member_access* ([Pointer member access](unsafe-code.md#pointer-member-access)) ending with a *type_argument_list* ([Type arguments](types.md#type-arguments)), the token immediately following the closing `>` token is examined.</span></span> <span data-ttu-id="3d917-998">S’il s’agit d’un des</span><span class="sxs-lookup"><span data-stu-id="3d917-998">If it is one of</span></span>
```csharp
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
```
<span data-ttu-id="3d917-999">le *type_argument_list* est conservé dans le cadre de la *simple_name*, *member_access* ou *pointer_member_access* et n’importe quel autres analyse possible de la séquence de jetons est ignoré.</span><span class="sxs-lookup"><span data-stu-id="3d917-999">then the *type_argument_list* is retained as part of the *simple_name*, *member_access* or *pointer_member_access* and any other possible parse of the sequence of tokens is discarded.</span></span> <span data-ttu-id="3d917-1000">Sinon, le *type_argument_list* n’est pas considéré comme faisant partie de la *simple_name*, *member_access* ou *pointer_member_access*, même s’il n’existe aucune autre analyse possible de la séquence de jetons.</span><span class="sxs-lookup"><span data-stu-id="3d917-1000">Otherwise, the *type_argument_list* is not considered to be part of the *simple_name*, *member_access* or *pointer_member_access*, even if there is no other possible parse of the sequence of tokens.</span></span> <span data-ttu-id="3d917-1001">Notez que ces règles ne sont pas appliquées lors de l’analyse un *type_argument_list* dans un *namespace_or_type_name* ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1001">Note that these rules are not applied when parsing a *type_argument_list* in a *namespace_or_type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)).</span></span> <span data-ttu-id="3d917-1002">L'instruction</span><span class="sxs-lookup"><span data-stu-id="3d917-1002">The statement</span></span>
```csharp
F(G<A,B>(7));
```
<span data-ttu-id="3d917-1003">être interprété en fonction de cette règle, comme un appel à `F` avec un seul argument, qui est un appel à une méthode générique `G` avec deux arguments de type et un seul argument régulière.</span><span class="sxs-lookup"><span data-stu-id="3d917-1003">will, according to this rule, be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument.</span></span> <span data-ttu-id="3d917-1004">Les instructions</span><span class="sxs-lookup"><span data-stu-id="3d917-1004">The statements</span></span>
```csharp
F(G < A, B > 7);
F(G < A, B >> 7);
```
<span data-ttu-id="3d917-1005">chaque interprété comme un appel à `F` avec deux arguments.</span><span class="sxs-lookup"><span data-stu-id="3d917-1005">will each be interpreted as a call to `F` with two arguments.</span></span> <span data-ttu-id="3d917-1006">L'instruction</span><span class="sxs-lookup"><span data-stu-id="3d917-1006">The statement</span></span>
```csharp
x = F < A > +y;
```
<span data-ttu-id="3d917-1007">sera interprété comme un opérateur inférieur à, supérieur à (opérateur) et opérateur plus unaire, comme si l’instruction avait été écrit `x = (F < A) > (+y)`, au lieu de comme un *simple_name* avec un *type_argument_list* suivi d’un opérateur plus binaire.</span><span class="sxs-lookup"><span data-stu-id="3d917-1007">will be interpreted as a less than operator, greater than operator, and unary plus operator, as if the statement had been written `x = (F < A) > (+y)`, instead of as a *simple_name* with a *type_argument_list* followed by a binary plus operator.</span></span> <span data-ttu-id="3d917-1008">Dans l’instruction</span><span class="sxs-lookup"><span data-stu-id="3d917-1008">In the statement</span></span>
```csharp
x = y is C<T> + z;
```
<span data-ttu-id="3d917-1009">les jetons `C<T>` sont interprétés comme un *namespace_or_type_name* avec un *type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1009">the tokens `C<T>` are interpreted as a *namespace_or_type_name* with a *type_argument_list*.</span></span>

### <a name="invocation-expressions"></a><span data-ttu-id="3d917-1010">Appel d’expressions</span><span class="sxs-lookup"><span data-stu-id="3d917-1010">Invocation expressions</span></span>

<span data-ttu-id="3d917-1011">Un *invocation_expression* est utilisé pour appeler une méthode.</span><span class="sxs-lookup"><span data-stu-id="3d917-1011">An *invocation_expression* is used to invoke a method.</span></span>

```antlr
invocation_expression
    : primary_expression '(' argument_list? ')'
    ;
```

<span data-ttu-id="3d917-1012">Un *invocation_expression* lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)) si au moins une des opérations suivantes conserve :</span><span class="sxs-lookup"><span data-stu-id="3d917-1012">An *invocation_expression* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)) if at least one of the following holds:</span></span>

* <span data-ttu-id="3d917-1013">Le *primary_expression* a le type de compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1013">The *primary_expression* has compile-time type `dynamic`.</span></span>
* <span data-ttu-id="3d917-1014">Au moins un argument de l’élément facultatif *argument_list* a le type de compilation `dynamic` et *primary_expression* n’a pas un type délégué.</span><span class="sxs-lookup"><span data-stu-id="3d917-1014">At least one argument of the optional *argument_list* has compile-time type `dynamic` and the *primary_expression* does not have a delegate type.</span></span>

<span data-ttu-id="3d917-1015">Dans ce cas le compilateur classifie les *invocation_expression* en tant que valeur de type `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1015">In this case the compiler classifies the *invocation_expression* as a value of type `dynamic`.</span></span> <span data-ttu-id="3d917-1016">Les règles ci-dessous pour déterminer la signification de la *invocation_expression* sont ensuite appliquées au moment de l’exécution, à l’aide du type au moment de l’exécution au lieu du type de compilation de celles de la *primary_expression* et arguments qui ont le type de compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1016">The rules below to determine the meaning of the *invocation_expression* are then applied at run-time, using the run-time type instead of the compile-time type of those of the *primary_expression* and arguments which have the compile-time type `dynamic`.</span></span> <span data-ttu-id="3d917-1017">Si le *primary_expression* n’a pas de type au moment de la compilation `dynamic`, puis l’appel de méthode fait l’objet d’une vérification de temps de compilation limité comme décrit dans [la vérification de la résolution de surcharge dynamique lors de la compilation ](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="3d917-1017">If the *primary_expression* does not have compile-time type `dynamic`, then the method invocation undergoes a limited compile time check as described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="3d917-1018">Le *primary_expression* d’un *invocation_expression* doit être un groupe de méthodes ou une valeur d’un *delegate_type*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1018">The *primary_expression* of an *invocation_expression* must be a method group or a value of a *delegate_type*.</span></span> <span data-ttu-id="3d917-1019">Si le *primary_expression* est un groupe de méthodes, les *invocation_expression* est un appel de méthode ([appels de méthode](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1019">If the *primary_expression* is a method group, the *invocation_expression* is a method invocation ([Method invocations](expressions.md#method-invocations)).</span></span> <span data-ttu-id="3d917-1020">Si le *primary_expression* est une valeur d’un *delegate_type*, le *invocation_expression* est un appel de délégué ([d’appelsdedélégué](expressions.md#delegate-invocations)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1020">If the *primary_expression* is a value of a *delegate_type*, the *invocation_expression* is a delegate invocation ([Delegate invocations](expressions.md#delegate-invocations)).</span></span> <span data-ttu-id="3d917-1021">Si le *primary_expression* n’est ni un groupe de méthodes ni une valeur d’un *delegate_type*, se produit une erreur au moment de la liaison.</span><span class="sxs-lookup"><span data-stu-id="3d917-1021">If the *primary_expression* is neither a method group nor a value of a *delegate_type*, a binding-time error occurs.</span></span>

<span data-ttu-id="3d917-1022">Le paramètre facultatif *argument_list* ([listes d’arguments](expressions.md#argument-lists)) fournit des valeurs ou des références variables pour les paramètres de la méthode.</span><span class="sxs-lookup"><span data-stu-id="3d917-1022">The optional *argument_list* ([Argument lists](expressions.md#argument-lists)) provides values or variable references for the parameters of the method.</span></span>

<span data-ttu-id="3d917-1023">Le résultat de l’évaluation d’une *invocation_expression* est classé comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-1023">The result of evaluating an *invocation_expression* is classified as follows:</span></span>

*  <span data-ttu-id="3d917-1024">Si le *invocation_expression* appelle une méthode ou un délégué qui retourne `void`, le résultat est nothing.</span><span class="sxs-lookup"><span data-stu-id="3d917-1024">If the *invocation_expression* invokes a method or delegate that returns `void`, the result is nothing.</span></span> <span data-ttu-id="3d917-1025">Une expression est classée comme rien n’est autorisé uniquement dans le contexte d’un *statement_expression* ([instructions d’Expression](statements.md#expression-statements)) ou en tant que le corps d’un *lambda_expression*([Expressions de fonction anonyme](expressions.md#anonymous-function-expressions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1025">An expression that is classified as nothing is permitted only in the context of a *statement_expression* ([Expression statements](statements.md#expression-statements)) or as the body of a *lambda_expression* ([Anonymous function expressions](expressions.md#anonymous-function-expressions)).</span></span> <span data-ttu-id="3d917-1026">Sinon, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1026">Otherwise a binding-time error occurs.</span></span>
*  <span data-ttu-id="3d917-1027">Sinon, le résultat est une valeur du type retourné par la méthode ou le délégué.</span><span class="sxs-lookup"><span data-stu-id="3d917-1027">Otherwise, the result is a value of the type returned by the method or delegate.</span></span>

#### <a name="method-invocations"></a><span data-ttu-id="3d917-1028">Appels de méthode</span><span class="sxs-lookup"><span data-stu-id="3d917-1028">Method invocations</span></span>

<span data-ttu-id="3d917-1029">Appel d’une méthode, le *primary_expression* de la *invocation_expression* doit être un groupe de méthodes.</span><span class="sxs-lookup"><span data-stu-id="3d917-1029">For a method invocation, the *primary_expression* of the *invocation_expression* must be a method group.</span></span> <span data-ttu-id="3d917-1030">Le groupe de méthodes identifie la méthode à appeler ou l’ensemble de méthodes surchargées parmi lesquelles choisir une méthode spécifique à appeler.</span><span class="sxs-lookup"><span data-stu-id="3d917-1030">The method group identifies the one method to invoke or the set of overloaded methods from which to choose a specific method to invoke.</span></span> <span data-ttu-id="3d917-1031">Dans ce cas, la détermination de la méthode spécifique à appeler est basée sur le contexte fourni par les types des arguments de la *argument_list*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1031">In the latter case, determination of the specific method to invoke is based on the context provided by the types of the arguments in the *argument_list*.</span></span>

<span data-ttu-id="3d917-1032">Le traitement au moment de la liaison d’un appel de méthode sous la forme `M(A)`, où `M` est un groupe de méthodes (y compris éventuellement un *type_argument_list*), et `A` est facultative *argument_ liste*, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-1032">The binding-time processing of a method invocation of the form `M(A)`, where `M` is a method group (possibly including a *type_argument_list*), and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="3d917-1033">L’ensemble des méthodes candidates pour l’appel de méthode est construit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1033">The set of candidate methods for the method invocation is constructed.</span></span> <span data-ttu-id="3d917-1034">Pour chaque méthode `F` associé au groupe de la méthode `M`:</span><span class="sxs-lookup"><span data-stu-id="3d917-1034">For each method `F` associated with the method group `M`:</span></span>
   *  <span data-ttu-id="3d917-1035">Si `F` n’est pas générique, `F` est un candidat lorsque :</span><span class="sxs-lookup"><span data-stu-id="3d917-1035">If `F` is non-generic, `F` is a candidate when:</span></span>
      * <span data-ttu-id="3d917-1036">`M` n’a aucune liste d’arguments de type, et</span><span class="sxs-lookup"><span data-stu-id="3d917-1036">`M` has no type argument list, and</span></span>
      * <span data-ttu-id="3d917-1037">`F` s’applique au niveau `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1037">`F` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span>
   *  <span data-ttu-id="3d917-1038">Si `F` est générique et `M` n’a aucune liste d’arguments de type, `F` est un candidat lorsque :</span><span class="sxs-lookup"><span data-stu-id="3d917-1038">If `F` is generic and `M` has no type argument list, `F` is a candidate when:</span></span>
      * <span data-ttu-id="3d917-1039">Inférence de type ([l’inférence de Type](expressions.md#type-inference)) réussit, la déduction d’une liste d’arguments de type pour l’appel, et</span><span class="sxs-lookup"><span data-stu-id="3d917-1039">Type inference ([Type inference](expressions.md#type-inference)) succeeds, inferring a list of type arguments for the call, and</span></span>
      * <span data-ttu-id="3d917-1040">Une fois que les arguments de type déduits sont substitués pour les paramètres de type de méthode correspondante, tous les types construits dans la liste des paramètres de F satisfont leurs contraintes ([qui satisfait aux contraintes](types.md#satisfying-constraints)) et la liste des paramètres de `F` est applicable par rapport à `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1040">Once the inferred type arguments are substituted for the corresponding method type parameters, all constructed types in the parameter list of F satisfy their constraints ([Satisfying constraints](types.md#satisfying-constraints)), and the parameter list of `F` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span>
   *  <span data-ttu-id="3d917-1041">Si `F` est générique et `M` inclut une liste d’arguments de type, `F` est un candidat lorsque :</span><span class="sxs-lookup"><span data-stu-id="3d917-1041">If `F` is generic and `M` includes a type argument list, `F` is a candidate when:</span></span>
      * <span data-ttu-id="3d917-1042">`F` a le même nombre de paramètres de type de méthode comme ont été fournies dans la liste d’arguments de type, et</span><span class="sxs-lookup"><span data-stu-id="3d917-1042">`F` has the same number of method type parameters as were supplied in the type argument list, and</span></span>
      * <span data-ttu-id="3d917-1043">Une fois que les arguments de type sont substitués pour les paramètres de type de méthode correspondante, tous les types construits dans la liste des paramètres de F satisfont leurs contraintes ([qui satisfait aux contraintes](types.md#satisfying-constraints)) et la liste des paramètres `F` s’applique au niveau `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1043">Once the type arguments are substituted for the corresponding method type parameters, all constructed types in the parameter list of F satisfy their constraints ([Satisfying constraints](types.md#satisfying-constraints)), and the parameter list of `F` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span>
*  <span data-ttu-id="3d917-1044">L’ensemble des méthodes candidates est réduit pour qu’il contienne uniquement les méthodes à partir des types plus dérivés : Pour chaque méthode `C.F` dans l’ensemble, où `C` est le type dans lequel la méthode `F` est déclaré, toutes les méthodes déclarées dans un type de base de `C` sont supprimés de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="3d917-1044">The set of candidate methods is reduced to contain only methods from the most derived types: For each method `C.F` in the set, where `C` is the type in which the method `F` is declared, all methods declared in a base type of `C` are removed from the set.</span></span> <span data-ttu-id="3d917-1045">En outre, si `C` est un type de classe autre que `object`, toutes les méthodes déclarées dans un type d’interface sont supprimés de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="3d917-1045">Furthermore, if `C` is a class type other than `object`, all methods declared in an interface type are removed from the set.</span></span> <span data-ttu-id="3d917-1046">(Cette règle de ce dernier n’a effet lorsque le groupe de méthodes était le résultat d’une recherche de membres sur un paramètre de type ayant une classe de base efficace autre que de l’objet et une interface efficace vide définie.)</span><span class="sxs-lookup"><span data-stu-id="3d917-1046">(This latter rule only has affect when the method group was the result of a member lookup on a type parameter having an effective base class other than object and a non-empty effective interface set.)</span></span>
*  <span data-ttu-id="3d917-1047">Si le jeu résultant des méthodes candidates est vide, puis un traitement supplémentaire sur les étapes suivantes sont abandonnés et à la place une tentative est effectuée pour traiter l’appel comme un appel de méthode d’extension ([appels de méthode d’Extension](expressions.md#extension-method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1047">If the resulting set of candidate methods is empty, then further processing along the following steps are abandoned, and instead an attempt is made to process the invocation as an extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)).</span></span> <span data-ttu-id="3d917-1048">En cas d’échec, n’existe aucune méthode applicable et une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1048">If this fails, then no applicable methods exist, and a binding-time error occurs.</span></span>
*  <span data-ttu-id="3d917-1049">La meilleure méthode de l’ensemble de méthodes de candidat est identifiée à l’aide de règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="3d917-1049">The best method of the set of candidate methods is identified using the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="3d917-1050">Si une meilleure méthode unique ne peut pas être identifiée, l’appel de méthode est ambigu et une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1050">If a single best method cannot be identified, the method invocation is ambiguous, and a binding-time error occurs.</span></span> <span data-ttu-id="3d917-1051">Lorsque vous effectuez la résolution de surcharge, les paramètres d’une méthode générique sont considérés comme après la substitution des arguments de type (fourni ou déduit) pour les paramètres de type de méthode correspondante.</span><span class="sxs-lookup"><span data-stu-id="3d917-1051">When performing overload resolution, the parameters of a generic method are considered after substituting the type arguments (supplied or inferred) for the corresponding method type parameters.</span></span>
*  <span data-ttu-id="3d917-1052">La validation finale de la meilleure méthode choisie est effectuée :</span><span class="sxs-lookup"><span data-stu-id="3d917-1052">Final validation of the chosen best method is performed:</span></span>
   * <span data-ttu-id="3d917-1053">La méthode est validée dans le contexte du groupe de méthodes : Si la meilleure méthode est une méthode statique, le groupe de méthodes doit résulter d’un *simple_name* ou un *member_access* via un type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1053">The method is validated in the context of the method group: If the best method is a static method, the method group must have resulted from a *simple_name* or a *member_access* through a type.</span></span> <span data-ttu-id="3d917-1054">Si la meilleure méthode est une méthode d’instance, le groupe de méthodes doit résulter d’un *simple_name*, un *member_access* via une variable ou une valeur, ou un *base_access*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1054">If the best method is an instance method, the method group must have resulted from a *simple_name*, a *member_access* through a variable or value, or a *base_access*.</span></span> <span data-ttu-id="3d917-1055">Si aucune de ces conditions est true, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1055">If neither of these requirements is true, a binding-time error occurs.</span></span>
   * <span data-ttu-id="3d917-1056">Si la meilleure méthode est une méthode générique, les arguments de type (fourni ou déduit) sont vérifiées sur les contraintes ([qui satisfait aux contraintes](types.md#satisfying-constraints)) déclaré dans la méthode générique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1056">If the best method is a generic method, the type arguments (supplied or inferred) are checked against the constraints ([Satisfying constraints](types.md#satisfying-constraints)) declared on the generic method.</span></span> <span data-ttu-id="3d917-1057">Si tout argument de type ne satisfait pas les contraintes correspondante sur le paramètre de type, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1057">If any type argument does not satisfy the corresponding constraint(s) on the type parameter, a binding-time error occurs.</span></span>

<span data-ttu-id="3d917-1058">Une fois qu’une méthode a été sélectionnée et validée au moment de la liaison par les étapes ci-dessus, l’appel d’exécution réel est traité selon les règles de l’appel de fonction membre décrit dans [la vérification de la résolution de surcharge dynamique lors de la compilation ](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="3d917-1058">Once a method has been selected and validated at binding-time by the above steps, the actual run-time invocation is processed according to the rules of function member invocation described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="3d917-1059">L’effet intuitif des règles de résolution décrites ci-dessus est comme suit : Pour localiser la méthode particulière appelée par un appel de méthode, commencez avec le type indiqué par l’appel de méthode et remontez à la chaîne d’héritage jusqu'à ce que la déclaration d’au moins une méthode applicable, accessible et non-override est trouvée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1059">The intuitive effect of the resolution rules described above is as follows: To locate the particular method invoked by a method invocation, start with the type indicated by the method invocation and proceed up the inheritance chain until at least one applicable, accessible, non-override method declaration is found.</span></span> <span data-ttu-id="3d917-1060">Effectuer l’inférence de type et sur l’ensemble de méthodes applicables, accessibles et non-override déclaré dans ce type de la résolution de surcharge, puis appeler la méthode donc sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1060">Then perform type inference and overload resolution on the set of applicable, accessible, non-override methods declared in that type and invoke the method thus selected.</span></span> <span data-ttu-id="3d917-1061">Si aucune méthode n’a été trouvée, essayez plutôt traiter l’appel comme un appel de méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="3d917-1061">If no method was found, try instead to process the invocation as an extension method invocation.</span></span>

#### <a name="extension-method-invocations"></a><span data-ttu-id="3d917-1062">Appels de méthode d’extension</span><span class="sxs-lookup"><span data-stu-id="3d917-1062">Extension method invocations</span></span>

<span data-ttu-id="3d917-1063">Dans un appel de méthode ([appels sur les instances](expressions.md#invocations-on-boxed-instances)) d’une des formes</span><span class="sxs-lookup"><span data-stu-id="3d917-1063">In a method invocation ([Invocations on boxed instances](expressions.md#invocations-on-boxed-instances)) of one of the forms</span></span>
```csharp
expr . identifier ( )

expr . identifier ( args )

expr . identifier < typeargs > ( )

expr . identifier < typeargs > ( args )
```
<span data-ttu-id="3d917-1064">Si le traitement normal de l’appel ne trouve aucune méthode applicable, une tentative est effectuée pour traiter la construction comme un appel de méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="3d917-1064">if the normal processing of the invocation finds no applicable methods, an attempt is made to process the construct as an extension method invocation.</span></span> <span data-ttu-id="3d917-1065">Si *expr* ou l’un de le *args* a le type de compilation `dynamic`, méthodes d’extension ne s’applique pas.</span><span class="sxs-lookup"><span data-stu-id="3d917-1065">If *expr* or any of the *args* has compile-time type `dynamic`, extension methods will not apply.</span></span>

<span data-ttu-id="3d917-1066">L’objectif est de trouver le meilleur *type_name* `C`, de sorte que l’appel de méthode statique correspondante peut avoir lieu :</span><span class="sxs-lookup"><span data-stu-id="3d917-1066">The objective is to find the best *type_name* `C`, so that the corresponding static method invocation can take place:</span></span>
```csharp
C . identifier ( expr )

C . identifier ( expr , args )

C . identifier < typeargs > ( expr )

C . identifier < typeargs > ( expr , args )
```

<span data-ttu-id="3d917-1067">Une méthode d’extension `Ci.Mj` est ***éligibles*** si :</span><span class="sxs-lookup"><span data-stu-id="3d917-1067">An extension method `Ci.Mj` is ***eligible*** if:</span></span>

*  <span data-ttu-id="3d917-1068">`Ci` est une classe non générique, non imbriquées</span><span class="sxs-lookup"><span data-stu-id="3d917-1068">`Ci` is a non-generic, non-nested class</span></span>
*  <span data-ttu-id="3d917-1069">Le nom de `Mj` est *identificateur*</span><span class="sxs-lookup"><span data-stu-id="3d917-1069">The name of `Mj` is *identifier*</span></span>
*  <span data-ttu-id="3d917-1070">`Mj` est accessible et applicables lorsqu’il est appliqué aux arguments comme une méthode statique, comme indiqué ci-dessus</span><span class="sxs-lookup"><span data-stu-id="3d917-1070">`Mj` is accessible and applicable when applied to the arguments as a static method as shown above</span></span>
*  <span data-ttu-id="3d917-1071">Il existe une référence, une conversion boxing ou un identité implicites à partir de *expr* pour le type du premier paramètre de `Mj`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1071">An implicit identity, reference or boxing conversion exists from *expr* to the type of the first parameter of `Mj`.</span></span>

<span data-ttu-id="3d917-1072">La recherche de `C` procède comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-1072">The search for `C` proceeds as follows:</span></span>

*  <span data-ttu-id="3d917-1073">En commençant par la déclaration espace de noms englobant le plus proche, poursuivre chaque déclaration d’espace de noms englobant et se terminant par l’unité de compilation qui le contient, les tentatives consécutives sont apportées pour rechercher un jeu de candidat de méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="3d917-1073">Starting with the closest enclosing namespace declaration, continuing with each enclosing namespace declaration, and ending with the containing compilation unit, successive attempts are made to find a candidate set of extension methods:</span></span>
   * <span data-ttu-id="3d917-1074">Si l’unité de compilation ou d’espace de noms donnée contient directement des déclarations de type non générique `Ci` avec les méthodes d’extension éligibles `Mj`, puis l’ensemble de ces méthodes d’extension est l’ensemble de candidats.</span><span class="sxs-lookup"><span data-stu-id="3d917-1074">If the given namespace or compilation unit directly contains non-generic type declarations `Ci` with eligible extension methods `Mj`, then the set of those extension methods is the candidate set.</span></span>
   * <span data-ttu-id="3d917-1075">Si les types `Ci` importé par *using_static_declarations* et sont directement déclarées dans les espaces de noms importés par *using_namespace_directive*s dans l’unité donnée de compilation ou d’espace de noms directement contient des méthodes d’extension éligibles `Mj`, puis l’ensemble de ces méthodes d’extension est l’ensemble de candidats.</span><span class="sxs-lookup"><span data-stu-id="3d917-1075">If types `Ci` imported by *using_static_declarations* and directly declared in namespaces imported by *using_namespace_directive*s in the given namespace or compilation unit directly contain eligible extension methods `Mj`, then the set of those extension methods is the candidate set.</span></span>
*  <span data-ttu-id="3d917-1076">Si aucun ensemble de candidats n’est trouvée dans n’importe quelle unité de compilation ou de la déclaration d’espace de noms englobante, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1076">If no candidate set is found in any enclosing namespace declaration or compilation unit, a compile-time error occurs.</span></span>
*  <span data-ttu-id="3d917-1077">Sinon, la résolution de surcharge est appliquée au candidat configuré comme décrit dans ([la résolution de surcharge](expressions.md#overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1077">Otherwise, overload resolution is applied to the candidate set as described in ([Overload resolution](expressions.md#overload-resolution)).</span></span> <span data-ttu-id="3d917-1078">Si aucune méthode meilleures unique n’est trouvée, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1078">If no single best method is found, a compile-time error occurs.</span></span>
*  <span data-ttu-id="3d917-1079">`C` est le type dans lequel la meilleure méthode est déclarée comme méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="3d917-1079">`C` is the type within which the best method is declared as an extension method.</span></span>

<span data-ttu-id="3d917-1080">À l’aide de `C` en tant que cible, l’appel de méthode est ensuite traité comme un appel de méthode statique ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1080">Using `C` as a target, the method call is then processed as a static method invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span>

<span data-ttu-id="3d917-1081">Les règles précédentes signifient que les méthodes d’instance sont prioritaires sur les méthodes d’extension, que les méthodes d’extension disponibles dans les déclarations d’espace de noms interne sont prioritaires sur les méthodes d’extension disponibles dans les déclarations d’espace de noms externe et cette extension les méthodes déclarées directement dans un espace de noms sont prioritaires sur les méthodes d’extension importés dans ce même espace de noms avec un à l’aide de la directive d’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="3d917-1081">The preceding rules mean that instance methods take precedence over extension methods, that extension methods available in inner namespace declarations take precedence over extension methods available in outer namespace declarations, and that extension methods declared directly in a namespace take precedence over extension methods imported into that same namespace with a using namespace directive.</span></span> <span data-ttu-id="3d917-1082">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3d917-1082">For example:</span></span>
```csharp
public static class E
{
    public static void F(this object obj, int i) { }

    public static void F(this object obj, string s) { }
}

class A { }

class B
{
    public void F(int i) { }
}

class C
{
    public void F(object obj) { }
}

class X
{
    static void Test(A a, B b, C c) {
        a.F(1);              // E.F(object, int)
        a.F("hello");        // E.F(object, string)

        b.F(1);              // B.F(int)
        b.F("hello");        // E.F(object, string)

        c.F(1);              // C.F(object)
        c.F("hello");        // C.F(object)
    }
}
```

<span data-ttu-id="3d917-1083">Dans l’exemple, `B`de méthode est prioritaire sur la première méthode d’extension, et `C`de méthode est prioritaire sur les deux méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="3d917-1083">In the example, `B`'s method takes precedence over the first extension method, and `C`'s method takes precedence over both extension methods.</span></span>

```csharp
public static class C
{
    public static void F(this int i) { Console.WriteLine("C.F({0})", i); }
    public static void G(this int i) { Console.WriteLine("C.G({0})", i); }
    public static void H(this int i) { Console.WriteLine("C.H({0})", i); }
}

namespace N1
{
    public static class D
    {
        public static void F(this int i) { Console.WriteLine("D.F({0})", i); }
        public static void G(this int i) { Console.WriteLine("D.G({0})", i); }
    }
}

namespace N2
{
    using N1;

    public static class E
    {
        public static void F(this int i) { Console.WriteLine("E.F({0})", i); }
    }

    class Test
    {
        static void Main(string[] args)
        {
            1.F();
            2.G();
            3.H();
        }
    }
}
```

<span data-ttu-id="3d917-1084">La sortie de cet exemple est :</span><span class="sxs-lookup"><span data-stu-id="3d917-1084">The output of this example is:</span></span>
```
E.F(1)
D.G(2)
C.H(3)
```
<span data-ttu-id="3d917-1085">`D.G` est prioritaire sur `C.G`, et `E.F` est prioritaire sur les deux `D.F` et `C.F`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1085">`D.G` takes precedence over `C.G`, and `E.F` takes precedence over both `D.F` and `C.F`.</span></span>

#### <a name="delegate-invocations"></a><span data-ttu-id="3d917-1086">Appels de délégué</span><span class="sxs-lookup"><span data-stu-id="3d917-1086">Delegate invocations</span></span>

<span data-ttu-id="3d917-1087">Pour un appel de délégué, le *primary_expression* de la *invocation_expression* doit être une valeur d’un *delegate_type*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1087">For a delegate invocation, the *primary_expression* of the *invocation_expression* must be a value of a *delegate_type*.</span></span> <span data-ttu-id="3d917-1088">En outre, envisagez la *delegate_type* pour être une fonction membre avec la même liste de paramètres en tant que le *delegate_type*, le *delegate_type* doit être applicable ( [Membre de fonction applicable](expressions.md#applicable-function-member)) par rapport à la *argument_list* de la *invocation_expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1088">Furthermore, considering the *delegate_type* to be a function member with the same parameter list as the *delegate_type*, the *delegate_type* must be applicable ([Applicable function member](expressions.md#applicable-function-member)) with respect to the *argument_list* of the *invocation_expression*.</span></span>

<span data-ttu-id="3d917-1089">Le traitement de l’exécution d’un appel de délégué sous la forme `D(A)`, où `D` est un *primary_expression* d’un *delegate_type* et `A` est une option *argument_list*, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-1089">The run-time processing of a delegate invocation of the form `D(A)`, where `D` is a *primary_expression* of a *delegate_type* and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="3d917-1090">`D` est évalué.</span><span class="sxs-lookup"><span data-stu-id="3d917-1090">`D` is evaluated.</span></span> <span data-ttu-id="3d917-1091">Si cette version d’évaluation provoque une exception, aucune étape supplémentaire n’est exécutées.</span><span class="sxs-lookup"><span data-stu-id="3d917-1091">If this evaluation causes an exception, no further steps are executed.</span></span>
*  <span data-ttu-id="3d917-1092">La valeur de `D` est vérifié pour être valide.</span><span class="sxs-lookup"><span data-stu-id="3d917-1092">The value of `D` is checked to be valid.</span></span> <span data-ttu-id="3d917-1093">Si la valeur de `D` est `null`, un `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1093">If the value of `D` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="3d917-1094">Sinon, `D` est une référence à une instance de délégué.</span><span class="sxs-lookup"><span data-stu-id="3d917-1094">Otherwise, `D` is a reference to a delegate instance.</span></span> <span data-ttu-id="3d917-1095">Appels de membre de fonction ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) sont effectuées sur chacune des entités pouvant être appelées dans la liste d’appel du délégué.</span><span class="sxs-lookup"><span data-stu-id="3d917-1095">Function member invocations ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) are performed on each of the callable entities in the invocation list of the delegate.</span></span> <span data-ttu-id="3d917-1096">Pour les entités pouvant être appelées consistant en une instance et une méthode d’instance, l’instance pour l’appel est l’instance contenue dans l’entité pouvant être appelée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1096">For callable entities consisting of an instance and instance method, the instance for the invocation is the instance contained in the callable entity.</span></span>

### <a name="element-access"></a><span data-ttu-id="3d917-1097">Accès à un élément</span><span class="sxs-lookup"><span data-stu-id="3d917-1097">Element access</span></span>

<span data-ttu-id="3d917-1098">Un *element_access* se compose d’un *primary_no_array_creation_expression*, suivie une »`[`» jeton, suivie une *argument_list*, suivi par un » `]`« token.</span><span class="sxs-lookup"><span data-stu-id="3d917-1098">An *element_access* consists of a *primary_no_array_creation_expression*, followed by a "`[`" token, followed by an *argument_list*, followed by a "`]`" token.</span></span> <span data-ttu-id="3d917-1099">Le *argument_list* se compose d’une ou plusieurs *argument*s, séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="3d917-1099">The *argument_list* consists of one or more *argument*s, separated by commas.</span></span>

```antlr
element_access
    : primary_no_array_creation_expression '[' expression_list ']'
    ;
```

<span data-ttu-id="3d917-1100">Le *argument_list* d’un *element_access* n’est pas autorisé à contenir `ref` ou `out` arguments.</span><span class="sxs-lookup"><span data-stu-id="3d917-1100">The *argument_list* of an *element_access* is not allowed to contain `ref` or `out` arguments.</span></span>

<span data-ttu-id="3d917-1101">Un *element_access* lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)) si au moins une des opérations suivantes conserve :</span><span class="sxs-lookup"><span data-stu-id="3d917-1101">An *element_access* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)) if at least one of the following holds:</span></span>

* <span data-ttu-id="3d917-1102">Le *primary_no_array_creation_expression* a le type de compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1102">The *primary_no_array_creation_expression* has compile-time type `dynamic`.</span></span>
* <span data-ttu-id="3d917-1103">Au moins une expression de la *argument_list* a le type de compilation `dynamic` et *primary_no_array_creation_expression* n’a pas un type tableau.</span><span class="sxs-lookup"><span data-stu-id="3d917-1103">At least one expression of the *argument_list* has compile-time type `dynamic` and the *primary_no_array_creation_expression* does not have an array type.</span></span>

<span data-ttu-id="3d917-1104">Dans ce cas le compilateur classifie les *element_access* en tant que valeur de type `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1104">In this case the compiler classifies the *element_access* as a value of type `dynamic`.</span></span> <span data-ttu-id="3d917-1105">Les règles ci-dessous pour déterminer la signification de la *element_access* sont ensuite appliquées au moment de l’exécution, à l’aide du type au moment de l’exécution au lieu du type de compilation de celles de la *primary_no_array_creation_expression*et *argument_list* expressions qui ont le type de compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1105">The rules below to determine the meaning of the *element_access* are then applied at run-time, using the run-time type instead of the compile-time type of those of the *primary_no_array_creation_expression* and *argument_list* expressions which have the compile-time type `dynamic`.</span></span> <span data-ttu-id="3d917-1106">Si le *primary_no_array_creation_expression* n’a pas de type au moment de la compilation `dynamic`, puis l’accès à un élément fait l’objet d’une vérification de temps de compilation limité comme décrit dans [vérification dynamique lors de la compilation résolution de surcharge](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="3d917-1106">If the *primary_no_array_creation_expression* does not have compile-time type `dynamic`, then the element access undergoes a limited compile time check as described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="3d917-1107">Si le *primary_no_array_creation_expression* d’un *element_access* est une valeur d’un *array_type*, le *element_access* est un tableau d’accès ([tableau accès](expressions.md#array-access)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1107">If the *primary_no_array_creation_expression* of an *element_access* is a value of an *array_type*, the *element_access* is an array access ([Array access](expressions.md#array-access)).</span></span> <span data-ttu-id="3d917-1108">Sinon, le *primary_no_array_creation_expression* doit être une variable ou une valeur d’une classe, un struct ou un type d’interface qui a un ou plusieurs membres d’indexeur, auquel cas la *element_access* est un accès à l’indexeur ([accès indexeur](expressions.md#indexer-access)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1108">Otherwise, the *primary_no_array_creation_expression* must be a variable or value of a class, struct, or interface type that has one or more indexer members, in which case the *element_access* is an indexer access ([Indexer access](expressions.md#indexer-access)).</span></span>

#### <a name="array-access"></a><span data-ttu-id="3d917-1109">Accès aux tableaux</span><span class="sxs-lookup"><span data-stu-id="3d917-1109">Array access</span></span>

<span data-ttu-id="3d917-1110">Pour l’accès au tableau, le *primary_no_array_creation_expression* de la *element_access* doit être une valeur d’un *array_type*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1110">For an array access, the *primary_no_array_creation_expression* of the *element_access* must be a value of an *array_type*.</span></span> <span data-ttu-id="3d917-1111">En outre, le *argument_list* d’un tableau accès n’est pas autorisé à contenir des arguments nommés. Le nombre d’expressions dans le *argument_list* doit être le même que le rang de la *array_type*, et chaque expression doit être de type `int`, `uint`, `long`, `ulong`, ou doit être implicitement convertible en un ou plusieurs de ces types.</span><span class="sxs-lookup"><span data-stu-id="3d917-1111">Furthermore, the *argument_list* of an array access is not allowed to contain named arguments.The number of expressions in the *argument_list* must be the same as the rank of the *array_type*, and each expression must be of type `int`, `uint`, `long`, `ulong`, or must be implicitly convertible to one or more of these types.</span></span>

<span data-ttu-id="3d917-1112">Le résultat de l’évaluation de l’accès au tableau est une variable du type d’élément du tableau, à savoir l’élément de tableau sélectionné par les valeurs des expressions de la *argument_list*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1112">The result of evaluating an array access is a variable of the element type of the array, namely the array element selected by the value(s) of the expression(s) in the *argument_list*.</span></span>

<span data-ttu-id="3d917-1113">Le traitement de l’exécution de l’accès à un tableau sous la forme `P[A]`, où `P` est un *primary_no_array_creation_expression* d’un *array_type* et `A` est un *argument_list*, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-1113">The run-time processing of an array access of the form `P[A]`, where `P` is a *primary_no_array_creation_expression* of an *array_type* and `A` is an *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="3d917-1114">`P` est évalué.</span><span class="sxs-lookup"><span data-stu-id="3d917-1114">`P` is evaluated.</span></span> <span data-ttu-id="3d917-1115">Si cette version d’évaluation provoque une exception, aucune étape supplémentaire n’est exécutées.</span><span class="sxs-lookup"><span data-stu-id="3d917-1115">If this evaluation causes an exception, no further steps are executed.</span></span>
*  <span data-ttu-id="3d917-1116">Les expressions d’index de la *argument_list* sont évaluées dans l’ordre, de gauche à droite.</span><span class="sxs-lookup"><span data-stu-id="3d917-1116">The index expressions of the *argument_list* are evaluated in order, from left to right.</span></span> <span data-ttu-id="3d917-1117">Suite de l’évaluation de chaque expression d’index, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) à un des types suivants est effectuée : `int`, `uint`, `long`, `ulong`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1117">Following evaluation of each index expression, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) to one of the following types is performed: `int`, `uint`, `long`, `ulong`.</span></span> <span data-ttu-id="3d917-1118">Le premier type dans cette liste pour laquelle il existe une conversion implicite est choisi.</span><span class="sxs-lookup"><span data-stu-id="3d917-1118">The first type in this list for which an implicit conversion exists is chosen.</span></span> <span data-ttu-id="3d917-1119">Par exemple, si l’expression d’index est de type `short` ensuite une conversion implicite vers `int` est effectuée, car les conversions implicites à partir de `short` à `int` et à partir de `short` à `long` sont possibles.</span><span class="sxs-lookup"><span data-stu-id="3d917-1119">For instance, if the index expression is of type `short` then an implicit conversion to `int` is performed, since implicit conversions from `short` to `int` and from `short` to `long` are possible.</span></span> <span data-ttu-id="3d917-1120">Si l’évaluation d’une expression d’index ou la conversion implicite suite provoque une exception, puis aucune nouvelle expression d’index n’est évaluées et aucune autre procédure est exécutée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1120">If evaluation of an index expression or the subsequent implicit conversion causes an exception, then no further index expressions are evaluated and no further steps are executed.</span></span>
*  <span data-ttu-id="3d917-1121">La valeur de `P` est vérifié pour être valide.</span><span class="sxs-lookup"><span data-stu-id="3d917-1121">The value of `P` is checked to be valid.</span></span> <span data-ttu-id="3d917-1122">Si la valeur de `P` est `null`, un `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1122">If the value of `P` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="3d917-1123">La valeur de chaque expression contenue dans le *argument_list* est comparée aux limites réelles de chaque dimension de l’instance de tableau référencé par `P`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1123">The value of each expression in the *argument_list* is checked against the actual bounds of each dimension of the array instance referenced by `P`.</span></span> <span data-ttu-id="3d917-1124">Si une ou plusieurs valeurs sont hors limites, un `System.IndexOutOfRangeException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1124">If one or more values are out of range, a `System.IndexOutOfRangeException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="3d917-1125">L’emplacement de l’élément de tableau donné par les expressions d’index est calculé et cet emplacement devient le résultat de l’accès.</span><span class="sxs-lookup"><span data-stu-id="3d917-1125">The location of the array element given by the index expression(s) is computed, and this location becomes the result of the array access.</span></span>

#### <a name="indexer-access"></a><span data-ttu-id="3d917-1126">Accès aux indexeurs</span><span class="sxs-lookup"><span data-stu-id="3d917-1126">Indexer access</span></span>

<span data-ttu-id="3d917-1127">Pour l’accès à un indexeur, le *primary_no_array_creation_expression* de la *element_access* doit être une variable ou une valeur d’une classe, un struct ou un type d’interface, et ce type doit implémenter un ou plusieurs indexeurs qui sont appliquent au niveau du *argument_list* de la *element_access*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1127">For an indexer access, the *primary_no_array_creation_expression* of the *element_access* must be a variable or value of a class, struct, or interface type, and this type must implement one or more indexers that are applicable with respect to the *argument_list* of the *element_access*.</span></span>

<span data-ttu-id="3d917-1128">Le traitement au moment de la liaison d’un accès d’indexeur de la forme `P[A]`, où `P` est un *primary_no_array_creation_expression* d’une classe, un struct ou un type d’interface `T`, et `A` est un *argument_list*, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-1128">The binding-time processing of an indexer access of the form `P[A]`, where `P` is a *primary_no_array_creation_expression* of a class, struct, or interface type `T`, and `A` is an *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="3d917-1129">L’ensemble des indexeurs fournis par `T` est construit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1129">The set of indexers provided by `T` is constructed.</span></span> <span data-ttu-id="3d917-1130">Le jeu est composé de tous les indexeurs déclarés dans `T` ou un type de base de `T` qui ne sont pas `override` déclarations et ils sont accessibles dans le contexte actuel ([l’accès au membre](basic-concepts.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1130">The set consists of all indexers declared in `T` or a base type of `T` that are not `override` declarations and are accessible in the current context ([Member access](basic-concepts.md#member-access)).</span></span>
*  <span data-ttu-id="3d917-1131">L’ensemble est réduit aux indexeurs qui sont applicables et non masqués par d’autres indexeurs.</span><span class="sxs-lookup"><span data-stu-id="3d917-1131">The set is reduced to those indexers that are applicable and not hidden by other indexers.</span></span> <span data-ttu-id="3d917-1132">Les règles suivantes sont appliquées à chaque indexeur `S.I` dans l’ensemble, où `S` est le type dans lequel l’indexeur `I` est déclarée :</span><span class="sxs-lookup"><span data-stu-id="3d917-1132">The following rules are applied to each indexer `S.I` in the set, where `S` is the type in which the indexer `I` is declared:</span></span>
   * <span data-ttu-id="3d917-1133">Si `I` n’est pas applicable au niveau `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)), puis `I` est supprimée du jeu.</span><span class="sxs-lookup"><span data-stu-id="3d917-1133">If `I` is not applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)), then `I` is removed from the set.</span></span>
   * <span data-ttu-id="3d917-1134">Si `I` est applicable par rapport à `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)), puis tous les indexeurs déclarés dans un type de base de `S` sont supprimés de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="3d917-1134">If `I` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)), then all indexers declared in a base type of `S` are removed from the set.</span></span>
   * <span data-ttu-id="3d917-1135">Si `I` est applicable par rapport à `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)) et `S` est un type de classe autre que `object`, tous les indexeurs déclarés dans une interface sont supprimés de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="3d917-1135">If `I` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)) and `S` is a class type other than `object`, all indexers declared in an interface are removed from the set.</span></span>
*  <span data-ttu-id="3d917-1136">Si le jeu résultant des indexeurs candidats est vide, n’existe aucun indexeur applicable et une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1136">If the resulting set of candidate indexers is empty, then no applicable indexers exist, and a binding-time error occurs.</span></span>
*  <span data-ttu-id="3d917-1137">Le meilleur indexeur de l’ensemble des indexeurs candidats est identifié à l’aide de règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="3d917-1137">The best indexer of the set of candidate indexers is identified using the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="3d917-1138">Si un seul meilleur indexeur ne peut pas être identifié, l’accès à l’indexeur est ambigu et une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1138">If a single best indexer cannot be identified, the indexer access is ambiguous, and a binding-time error occurs.</span></span>
*  <span data-ttu-id="3d917-1139">Les expressions d’index de la *argument_list* sont évaluées dans l’ordre, de gauche à droite.</span><span class="sxs-lookup"><span data-stu-id="3d917-1139">The index expressions of the *argument_list* are evaluated in order, from left to right.</span></span> <span data-ttu-id="3d917-1140">Le résultat du traitement de l’accès à l’indexeur d’est une expression classifiée comme accès à un indexeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1140">The result of processing the indexer access is an expression classified as an indexer access.</span></span> <span data-ttu-id="3d917-1141">L’expression d’accès indexeur fait référence à l’indexeur déterminé à l’étape précédente et possède une expression d’instance associée de `P` et une liste d’arguments de `A`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1141">The indexer access expression references the indexer determined in the step above, and has an associated instance expression of `P` and an associated argument list of `A`.</span></span>

<span data-ttu-id="3d917-1142">En fonction du contexte dans lequel elle est utilisée, accès à un indexeur provoque l’appel d’un le *accesseur get* ou *accesseur set* de l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1142">Depending on the context in which it is used, an indexer access causes invocation of either the *get accessor* or the *set accessor* of the indexer.</span></span> <span data-ttu-id="3d917-1143">Si l’accès à l’indexeur est la cible d’une assignation, le *accesseur set* est appelé pour assigner une nouvelle valeur ([assignation Simple](expressions.md#simple-assignment)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1143">If the indexer access is the target of an assignment, the *set accessor* is invoked to assign a new value ([Simple assignment](expressions.md#simple-assignment)).</span></span> <span data-ttu-id="3d917-1144">Dans tous les autres cas, le *accesseur get* est appelé pour obtenir la valeur actuelle ([valeurs des expressions](expressions.md#values-of-expressions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1144">In all other cases, the *get accessor* is invoked to obtain the current value ([Values of expressions](expressions.md#values-of-expressions)).</span></span>

### <a name="this-access"></a><span data-ttu-id="3d917-1145">Cet accès</span><span class="sxs-lookup"><span data-stu-id="3d917-1145">This access</span></span>

<span data-ttu-id="3d917-1146">Un *this_access* se compose d’un mot réservé `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1146">A *this_access* consists of the reserved word `this`.</span></span>

```antlr
this_access
    : 'this'
    ;
```

<span data-ttu-id="3d917-1147">Un *this_access* est autorisée uniquement dans le *bloc* d’un constructeur d’instance, une méthode d’instance ou un accesseur d’instance.</span><span class="sxs-lookup"><span data-stu-id="3d917-1147">A *this_access* is permitted only in the *block* of an instance constructor, an instance method, or an instance accessor.</span></span> <span data-ttu-id="3d917-1148">Il possède une des significations suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-1148">It has one of the following meanings:</span></span>

*  <span data-ttu-id="3d917-1149">Lorsque `this` est utilisée dans un *primary_expression* au sein d’un constructeur d’instance d’une classe, il est considéré comme une valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1149">When `this` is used in a *primary_expression* within an instance constructor of a class, it is classified as a value.</span></span> <span data-ttu-id="3d917-1150">Le type de la valeur est le type d’instance ([le type d’instance](classes.md#the-instance-type)) de la classe dans laquelle a lieu l’utilisation et la valeur est une référence à l’objet en cours de construction.</span><span class="sxs-lookup"><span data-stu-id="3d917-1150">The type of the value is the instance type ([The instance type](classes.md#the-instance-type)) of the class within which the usage occurs, and the value is a reference to the object being constructed.</span></span>
*  <span data-ttu-id="3d917-1151">Lorsque `this` est utilisée dans un *primary_expression* au sein d’une méthode d’instance ou un accesseur d’instance d’une classe, il est considéré comme une valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1151">When `this` is used in a *primary_expression* within an instance method or instance accessor of a class, it is classified as a value.</span></span> <span data-ttu-id="3d917-1152">Le type de la valeur est le type d’instance ([le type d’instance](classes.md#the-instance-type)) de la classe dans laquelle a lieu l’utilisation et la valeur est une référence à l’objet pour lequel la méthode ou l’accesseur a été appelée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1152">The type of the value is the instance type ([The instance type](classes.md#the-instance-type)) of the class within which the usage occurs, and the value is a reference to the object for which the method or accessor was invoked.</span></span>
*  <span data-ttu-id="3d917-1153">Lorsque `this` est utilisée dans un *primary_expression* au sein d’un constructeur d’instance d’un struct, il est classé en tant que variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-1153">When `this` is used in a *primary_expression* within an instance constructor of a struct, it is classified as a variable.</span></span> <span data-ttu-id="3d917-1154">Le type de la variable est le type d’instance ([le type d’instance](classes.md#the-instance-type)) du struct dans lequel a lieu l’utilisation et la variable représente la structure en cours de construction.</span><span class="sxs-lookup"><span data-stu-id="3d917-1154">The type of the variable is the instance type ([The instance type](classes.md#the-instance-type)) of the struct within which the usage occurs, and the variable represents the struct being constructed.</span></span> <span data-ttu-id="3d917-1155">Le `this` variable d’un constructeur d’instance d’un struct comporte exactement comme un `out` paramètre du type struct, en particulier, cela signifie que la variable doit absolument être assignée dans chaque chemin d’accès de l’exécution de l’instance constructeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1155">The `this` variable of an instance constructor of a struct behaves exactly the same as an `out` parameter of the struct type—in particular, this means that the variable must be definitely assigned in every execution path of the instance constructor.</span></span>
*  <span data-ttu-id="3d917-1156">Lorsque `this` est utilisée dans un *primary_expression* au sein d’une méthode d’instance ou un accesseur d’instance d’un struct, il est classé en tant que variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-1156">When `this` is used in a *primary_expression* within an instance method or instance accessor of a struct, it is classified as a variable.</span></span> <span data-ttu-id="3d917-1157">Le type de la variable est le type d’instance ([le type d’instance](classes.md#the-instance-type)) du struct dans lequel se déroule l’utilisation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1157">The type of the variable is the instance type ([The instance type](classes.md#the-instance-type)) of the struct within which the usage occurs.</span></span>
   * <span data-ttu-id="3d917-1158">Si la méthode ou l’accesseur n’est pas un itérateur ([itérateurs](classes.md#iterators)), la `this` variable représente le struct pour lequel la méthode ou l’accesseur a été appelée et se comporte exactement comme un `ref` paramètre du type struct.</span><span class="sxs-lookup"><span data-stu-id="3d917-1158">If the method or accessor is not an iterator ([Iterators](classes.md#iterators)), the `this` variable represents the struct for which the method or accessor was invoked, and behaves exactly the same as a `ref` parameter of the struct type.</span></span>
   * <span data-ttu-id="3d917-1159">Si la méthode ou l’accesseur est un itérateur, le `this` variable représente une copie du struct pour lequel la méthode ou l’accesseur a été appelée et se comporte exactement comme un paramètre de valeur du type struct.</span><span class="sxs-lookup"><span data-stu-id="3d917-1159">If the method or accessor is an iterator, the `this` variable represents a copy of the struct for which the method or accessor was invoked, and behaves exactly the same as a value parameter of the struct type.</span></span>

<span data-ttu-id="3d917-1160">Utilisation de `this` dans un *primary_expression* dans un contexte autre que ceux répertoriés ci-dessus est une erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1160">Use of `this` in a *primary_expression* in a context other than the ones listed above is a compile-time error.</span></span> <span data-ttu-id="3d917-1161">En particulier, il n’est pas possible de faire référence à `this` dans une méthode statique, un accesseur de propriété statique, ou dans un *variable_initializer* d’une déclaration de champ.</span><span class="sxs-lookup"><span data-stu-id="3d917-1161">In particular, it is not possible to refer to `this` in a static method, a static property accessor, or in a *variable_initializer* of a field declaration.</span></span>

### <a name="base-access"></a><span data-ttu-id="3d917-1162">Accès de base</span><span class="sxs-lookup"><span data-stu-id="3d917-1162">Base access</span></span>

<span data-ttu-id="3d917-1163">Un *base_access* se compose d’un mot réservé `base` suivie par un «`.`« jeton et un identificateur ou un *argument_list* entre crochets :</span><span class="sxs-lookup"><span data-stu-id="3d917-1163">A *base_access* consists of the reserved word `base` followed by either a "`.`" token and an identifier or an *argument_list* enclosed in square brackets:</span></span>

```antlr
base_access
    : 'base' '.' identifier
    | 'base' '[' expression_list ']'
    ;
```

<span data-ttu-id="3d917-1164">Un *base_access* est utilisé pour accéder aux membres de classe de base qui sont masqués par les membres de même nom dans la classe ou structure actuelle.</span><span class="sxs-lookup"><span data-stu-id="3d917-1164">A *base_access* is used to access base class members that are hidden by similarly named members in the current class or struct.</span></span> <span data-ttu-id="3d917-1165">Un *base_access* est autorisée uniquement dans le *bloc* d’un constructeur d’instance, une méthode d’instance ou un accesseur d’instance.</span><span class="sxs-lookup"><span data-stu-id="3d917-1165">A *base_access* is permitted only in the *block* of an instance constructor, an instance method, or an instance accessor.</span></span> <span data-ttu-id="3d917-1166">Lorsque `base.I` se produit dans une classe ou un struct, `I` doit désigner un membre de la classe de base de cette classe ou un struct.</span><span class="sxs-lookup"><span data-stu-id="3d917-1166">When `base.I` occurs in a class or struct, `I` must denote a member of the base class of that class or struct.</span></span> <span data-ttu-id="3d917-1167">De même, quand `base[E]` se produit dans une classe, un indexeur applicable doit exister dans la classe de base.</span><span class="sxs-lookup"><span data-stu-id="3d917-1167">Likewise, when `base[E]` occurs in a class, an applicable indexer must exist in the base class.</span></span>

<span data-ttu-id="3d917-1168">Au moment de la liaison, *base_access* expressions de la forme `base.I` et `base[E]` sont évaluées comme si elles étaient écrites `((B)this).I` et `((B)this)[E]`, où `B` est la classe de base de la classe ou un struct dans lequel la construction se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1168">At binding-time, *base_access* expressions of the form `base.I` and `base[E]` are evaluated exactly as if they were written `((B)this).I` and `((B)this)[E]`, where `B` is the base class of the class or struct in which the construct occurs.</span></span> <span data-ttu-id="3d917-1169">Par conséquent, `base.I` et `base[E]` correspondent aux `this.I` et `this[E]`, à l’exception `this` est considéré comme une instance de la classe de base.</span><span class="sxs-lookup"><span data-stu-id="3d917-1169">Thus, `base.I` and `base[E]` correspond to `this.I` and `this[E]`, except `this` is viewed as an instance of the base class.</span></span>

<span data-ttu-id="3d917-1170">Quand un *base_access* fait référence à une fonction membre virtuelle (méthode, propriété ou indexeur), la détermination de qui fonction membre à appeler au moment de l’exécution ([la vérification de la résolution de surcharge dynamique lors de la compilation ](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) est modifié.</span><span class="sxs-lookup"><span data-stu-id="3d917-1170">When a *base_access* references a virtual function member (a method, property, or indexer), the determination of which function member to invoke at run-time ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) is changed.</span></span> <span data-ttu-id="3d917-1171">La fonction membre qui est appelée est déterminée en recherchant l’implémentation la plus dérivée ([les méthodes virtuelles](classes.md#virtual-methods)) de la fonction membre au niveau `B` (au lieu d’en ce qui concerne le type au moment de l’exécution de `this`, en tant que serait un accès de base autre).</span><span class="sxs-lookup"><span data-stu-id="3d917-1171">The function member that is invoked is determined by finding the most derived implementation ([Virtual methods](classes.md#virtual-methods)) of the function member with respect to `B` (instead of with respect to the run-time type of `this`, as would be usual in a non-base access).</span></span> <span data-ttu-id="3d917-1172">Par conséquent, au sein d’un `override` d’un `virtual` fonction membre, un *base_access* peut être utilisée pour appeler l’implémentation héritée de la fonction membre.</span><span class="sxs-lookup"><span data-stu-id="3d917-1172">Thus, within an `override` of a `virtual` function member, a *base_access* can be used to invoke the inherited implementation of the function member.</span></span> <span data-ttu-id="3d917-1173">Si la fonction membre référencé par un *base_access* est abstrait, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1173">If the function member referenced by a *base_access* is abstract, a binding-time error occurs.</span></span>

### <a name="postfix-increment-and-decrement-operators"></a><span data-ttu-id="3d917-1174">Opérateurs suffixés d’incrémentation et de décrémentation</span><span class="sxs-lookup"><span data-stu-id="3d917-1174">Postfix increment and decrement operators</span></span>

```antlr
post_increment_expression
    : primary_expression '++'
    ;

post_decrement_expression
    : primary_expression '--'
    ;
```

<span data-ttu-id="3d917-1175">L’opérande d’une incrémentation ou de décrémentation opération doit être une expression classifiée comme une variable, un accès à la propriété ou un indexeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1175">The operand of a postfix increment or decrement operation must be an expression classified as a variable, a property access, or an indexer access.</span></span> <span data-ttu-id="3d917-1176">Le résultat de l’opération est une valeur du même type que l’opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-1176">The result of the operation is a value of the same type as the operand.</span></span>

<span data-ttu-id="3d917-1177">Si le *primary_expression* a le type de compilation `dynamic` l’opérateur est dynamiquement lié ([liaison dynamique](expressions.md#dynamic-binding)), la *post_increment_expression*ou *post_decrement_expression* a le type de compilation `dynamic` et les règles suivantes sont appliquées au moment de l’exécution à l’aide du type au moment de l’exécution de la *primary_expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1177">If the *primary_expression* has the compile-time type `dynamic` then the operator is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)), the *post_increment_expression* or *post_decrement_expression* has the compile-time type `dynamic` and the following rules are applied at run-time using the run-time type of the *primary_expression*.</span></span>

<span data-ttu-id="3d917-1178">Si l’opérande d’un suffixe incrémente ou opération de décrémentation est une propriété ou indexeur, la propriété ou l’indexeur doit avoir à la fois un `get` et un `set` accesseur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1178">If the operand of a postfix increment or decrement operation is a property or indexer access, the property or indexer must have both a `get` and a `set` accessor.</span></span> <span data-ttu-id="3d917-1179">Si ce n’est pas le cas, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1179">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="3d917-1180">Opérateur unaire de résolution de surcharge ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1180">Unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-1181">Prédéfinies `++` et `--` opérateurs existent pour les types suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char` , `float`, `double`, `decimal`et tout type enum.</span><span class="sxs-lookup"><span data-stu-id="3d917-1181">Predefined `++` and `--` operators exist for the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, and any enum type.</span></span> <span data-ttu-id="3d917-1182">Prédéfinis `++` opérateurs retournent la valeur produite en ajoutant 1 à l’opérande et sur les `--` opérateurs retournent la valeur produite en soustrayant 1 de l’opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-1182">The predefined `++` operators return the value produced by adding 1 to the operand, and the predefined `--` operators return the value produced by subtracting 1 from the operand.</span></span> <span data-ttu-id="3d917-1183">Dans un `checked` contexte, si le résultat de l’addition ou la soustraction est en dehors de la plage du type du résultat et le type de résultat est un type intégral ou enum, une `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1183">In a `checked` context, if the result of this addition or subtraction is outside the range of the result type and the result type is an integral type or enum type, a `System.OverflowException` is thrown.</span></span>

<span data-ttu-id="3d917-1184">Le traitement de l’exécution d’une incrémentation ou décrémentation opération sous la forme `x++` ou `x--` se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-1184">The run-time processing of a postfix increment or decrement operation of the form `x++` or `x--` consists of the following steps:</span></span>

*   <span data-ttu-id="3d917-1185">Si `x` est classé en tant que variable :</span><span class="sxs-lookup"><span data-stu-id="3d917-1185">If `x` is classified as a variable:</span></span>
    * <span data-ttu-id="3d917-1186">`x` est évalué pour produire la variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-1186">`x` is evaluated to produce the variable.</span></span>
    * <span data-ttu-id="3d917-1187">La valeur de `x` est enregistré.</span><span class="sxs-lookup"><span data-stu-id="3d917-1187">The value of `x` is saved.</span></span>
    * <span data-ttu-id="3d917-1188">L’opérateur sélectionné est appelé avec la valeur enregistrée de `x` comme argument.</span><span class="sxs-lookup"><span data-stu-id="3d917-1188">The selected operator is invoked with the saved value of `x` as its argument.</span></span>
    * <span data-ttu-id="3d917-1189">La valeur retournée par l’opérateur est stockée dans l’emplacement indiqué par la version d’évaluation de `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1189">The value returned by the operator is stored in the location given by the evaluation of `x`.</span></span>
    * <span data-ttu-id="3d917-1190">La valeur enregistrée de `x` devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-1190">The saved value of `x` becomes the result of the operation.</span></span>
*   <span data-ttu-id="3d917-1191">Si `x` est classé comme un accès de propriété ou l’indexeur :</span><span class="sxs-lookup"><span data-stu-id="3d917-1191">If `x` is classified as a property or indexer access:</span></span>
    * <span data-ttu-id="3d917-1192">L’expression d’instance (si `x` n’est pas `static`) et la liste d’arguments (si `x` est l’accès à un indexeur) associé `x` sont évaluées, et les résultats sont utilisés dans l’ultérieure `get` et `set` appels d’accesseur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1192">The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `get` and `set` accessor invocations.</span></span>
    * <span data-ttu-id="3d917-1193">Le `get` l’accesseur de `x` est appelée et la valeur retournée est enregistrée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1193">The `get` accessor of `x` is invoked and the returned value is saved.</span></span>
    * <span data-ttu-id="3d917-1194">L’opérateur sélectionné est appelé avec la valeur enregistrée de `x` comme argument.</span><span class="sxs-lookup"><span data-stu-id="3d917-1194">The selected operator is invoked with the saved value of `x` as its argument.</span></span>
    * <span data-ttu-id="3d917-1195">Le `set` l’accesseur de `x` est appelé avec la valeur retournée par l’opérateur en tant que son `value` argument.</span><span class="sxs-lookup"><span data-stu-id="3d917-1195">The `set` accessor of `x` is invoked with the value returned by the operator as its `value` argument.</span></span>
    * <span data-ttu-id="3d917-1196">La valeur enregistrée de `x` devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-1196">The saved value of `x` becomes the result of the operation.</span></span>

<span data-ttu-id="3d917-1197">Le `++` et `--` opérateurs prennent également en charge la notation de préfixe ([Incrément préfixé et opérateurs de décrémentation](expressions.md#prefix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1197">The `++` and `--` operators also support prefix notation ([Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span> <span data-ttu-id="3d917-1198">En règle générale, le résultat de `x++` ou `x--` est la valeur de `x` avant l’opération, tandis que le résultat de `++x` ou `--x` est la valeur de `x` après l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-1198">Typically, the result of `x++` or `x--` is the value of `x` before the operation, whereas the result of `++x` or `--x` is the value of `x` after the operation.</span></span> <span data-ttu-id="3d917-1199">Dans les deux cas, `x` lui-même a la même valeur après l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-1199">In either case, `x` itself has the same value after the operation.</span></span>

<span data-ttu-id="3d917-1200">Un `operator ++` ou `operator --` implémentation peut être appelée à l’aide de la notation de préfixe ou de suffixe.</span><span class="sxs-lookup"><span data-stu-id="3d917-1200">An `operator ++` or `operator --` implementation can be invoked using either postfix or prefix notation.</span></span> <span data-ttu-id="3d917-1201">Il n’est pas possible d’avoir des implémentations d’opérateurs distincts pour les deux notations.</span><span class="sxs-lookup"><span data-stu-id="3d917-1201">It is not possible to have separate operator implementations for the two notations.</span></span>

### <a name="the-new-operator"></a><span data-ttu-id="3d917-1202">new, opérateur</span><span class="sxs-lookup"><span data-stu-id="3d917-1202">The new operator</span></span>

<span data-ttu-id="3d917-1203">Le `new` opérateur est utilisé pour créer des instances de types.</span><span class="sxs-lookup"><span data-stu-id="3d917-1203">The `new` operator is used to create new instances of types.</span></span>

<span data-ttu-id="3d917-1204">Il existe trois formes de `new` expressions :</span><span class="sxs-lookup"><span data-stu-id="3d917-1204">There are three forms of `new` expressions:</span></span>

*  <span data-ttu-id="3d917-1205">Expressions de la création d’objet sont utilisées pour créer des instances des types classe et des types valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1205">Object creation expressions are used to create new instances of class types and value types.</span></span>
*  <span data-ttu-id="3d917-1206">Expressions de création de tableau sont utilisées pour créer des instances de types de tableau.</span><span class="sxs-lookup"><span data-stu-id="3d917-1206">Array creation expressions are used to create new instances of array types.</span></span>
*  <span data-ttu-id="3d917-1207">Expressions de création de délégué sont utilisées pour créer des instances de délégué types.</span><span class="sxs-lookup"><span data-stu-id="3d917-1207">Delegate creation expressions are used to create new instances of delegate types.</span></span>

<span data-ttu-id="3d917-1208">Le `new` opérateur implique la création d’une instance d’un type, mais n’implique pas nécessairement l’allocation dynamique de mémoire.</span><span class="sxs-lookup"><span data-stu-id="3d917-1208">The `new` operator implies creation of an instance of a type, but does not necessarily imply dynamic allocation of memory.</span></span> <span data-ttu-id="3d917-1209">En particulier, les instances de types valeur ne requièrent aucune mémoire supplémentaire au-delà des variables dans lequel elles résident, et aucune allocation dynamique se produire lorsque `new` est utilisé pour créer des instances de types valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1209">In particular, instances of value types require no additional memory beyond the variables in which they reside, and no dynamic allocations occur when `new` is used to create instances of value types.</span></span>

#### <a name="object-creation-expressions"></a><span data-ttu-id="3d917-1210">Expressions de la création d’objet</span><span class="sxs-lookup"><span data-stu-id="3d917-1210">Object creation expressions</span></span>

<span data-ttu-id="3d917-1211">Un *object_creation_expression* est utilisé pour créer une nouvelle instance d’un *class_type* ou un *value_type*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1211">An *object_creation_expression* is used to create a new instance of a *class_type* or a *value_type*.</span></span>

```antlr
object_creation_expression
    : 'new' type '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    ;

object_or_collection_initializer
    : object_initializer
    | collection_initializer
    ;
```

<span data-ttu-id="3d917-1212">Le *type* d’un *object_creation_expression* doit être un *class_type*, un *value_type* ou un *type_parameter* .</span><span class="sxs-lookup"><span data-stu-id="3d917-1212">The *type* of an *object_creation_expression* must be a *class_type*, a *value_type* or a *type_parameter*.</span></span> <span data-ttu-id="3d917-1213">Le *type* ne peut pas être un `abstract` *class_type*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1213">The *type* cannot be an `abstract` *class_type*.</span></span>

<span data-ttu-id="3d917-1214">Le paramètre facultatif *argument_list* ([listes d’arguments](expressions.md#argument-lists)) est autorisée uniquement si le *type* est un *class_type* ou un *struct_ type*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1214">The optional *argument_list* ([Argument lists](expressions.md#argument-lists)) is permitted only if the *type* is a *class_type* or a *struct_type*.</span></span>

<span data-ttu-id="3d917-1215">Une expression de création d’objet peut omettre la liste d’arguments de constructeur et englobante parenthèses fourni que contient un initialiseur d’objet ou un initialiseur de collection.</span><span class="sxs-lookup"><span data-stu-id="3d917-1215">An object creation expression can omit the constructor argument list and enclosing parentheses provided it includes an object initializer or collection initializer.</span></span> <span data-ttu-id="3d917-1216">L’omission de la liste d’arguments de constructeur et en les plaçant entre parenthèses sont équivalente à la spécification d’une liste d’arguments vide.</span><span class="sxs-lookup"><span data-stu-id="3d917-1216">Omitting the constructor argument list and enclosing parentheses is equivalent to specifying an empty argument list.</span></span>

<span data-ttu-id="3d917-1217">Traitement d’une expression de création d’objet qui inclut un initialiseur d’objet ou un initialiseur de collection se compose de traitement tout d’abord le constructeur d’instance, puis en traitant les initialisations de membre ou l’élément spécifiées par l’initialiseur d’objet ([ Initialiseurs d’objets](expressions.md#object-initializers)) ou un initialiseur de collection ([initialiseurs de Collection](expressions.md#collection-initializers)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1217">Processing of an object creation expression that includes an object initializer or collection initializer consists of first processing the instance constructor and then processing the member or element initializations specified by the object initializer ([Object initializers](expressions.md#object-initializers)) or collection initializer ([Collection initializers](expressions.md#collection-initializers)).</span></span>

<span data-ttu-id="3d917-1218">Si un des arguments de l’élément facultatif *argument_list* a le type de compilation `dynamic` le *object_creation_expression* lié dynamiquement ([deliaisondynamique](expressions.md#dynamic-binding)) et les règles suivantes sont appliquées au moment de l’exécution à l’aide du type au moment de l’exécution de ces arguments de la *argument_list* qui ont le type de temps de compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1218">If any of the arguments in the optional *argument_list* has the compile-time type `dynamic` then the *object_creation_expression* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)) and the following rules are applied at run-time using the run-time type of those arguments of the *argument_list* that have the compile time type `dynamic`.</span></span> <span data-ttu-id="3d917-1219">Toutefois, la création de l’objet fait l’objet d’une vérification de temps de compilation limité comme décrit dans [compilation la vérification de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="3d917-1219">However, the object creation undergoes a limited compile time check as described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="3d917-1220">Le traitement au moment de la liaison d’un *object_creation_expression* du formulaire `new T(A)`, où `T` est un *class_type* ou un *value_type* et `A` est facultative *argument_list*, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-1220">The binding-time processing of an *object_creation_expression* of the form `new T(A)`, where `T` is a *class_type* or a *value_type* and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*   <span data-ttu-id="3d917-1221">Si `T` est un *value_type* et `A` n’est pas présent :</span><span class="sxs-lookup"><span data-stu-id="3d917-1221">If `T` is a *value_type* and `A` is not present:</span></span>
    * <span data-ttu-id="3d917-1222">Le *object_creation_expression* est un appel de constructeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="3d917-1222">The *object_creation_expression* is a default constructor invocation.</span></span> <span data-ttu-id="3d917-1223">Le résultat de la *object_creation_expression* est une valeur de type `T`, à savoir la valeur par défaut `T` tel que défini dans [System.ValueType le type](types.md#the-systemvaluetype-type).</span><span class="sxs-lookup"><span data-stu-id="3d917-1223">The result of the *object_creation_expression* is a value of type `T`, namely the default value for `T` as defined in [The System.ValueType type](types.md#the-systemvaluetype-type).</span></span>
*   <span data-ttu-id="3d917-1224">Sinon, si `T` est un *type_parameter* et `A` n’est pas présent :</span><span class="sxs-lookup"><span data-stu-id="3d917-1224">Otherwise, if `T` is a *type_parameter* and `A` is not present:</span></span>
    * <span data-ttu-id="3d917-1225">Si aucune contrainte de type valeur ou une contrainte de constructeur ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)) a été spécifiée pour `T`, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1225">If no value type constraint or constructor constraint ([Type parameter constraints](classes.md#type-parameter-constraints)) has been specified for `T`, a binding-time error occurs.</span></span>
    * <span data-ttu-id="3d917-1226">Le résultat de la *object_creation_expression* est une valeur du type d’exécution auquel le paramètre de type a été lié, à savoir le résultat de l’appel du constructeur par défaut de ce type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1226">The result of the *object_creation_expression* is a value of the run-time type that the type parameter has been bound to, namely the result of invoking the default constructor of that type.</span></span> <span data-ttu-id="3d917-1227">Le type d’exécution peut être un type référence ou un type valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1227">The run-time type may be a reference type or a value type.</span></span>
*   <span data-ttu-id="3d917-1228">Sinon, si `T` est un *class_type* ou un *struct_type*:</span><span class="sxs-lookup"><span data-stu-id="3d917-1228">Otherwise, if `T` is a *class_type* or a *struct_type*:</span></span>
    * <span data-ttu-id="3d917-1229">Si `T` est un `abstract` *class_type*, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1229">If `T` is an `abstract` *class_type*, a compile-time error occurs.</span></span>
    * <span data-ttu-id="3d917-1230">Le constructeur d’instance à appeler est déterminé en utilisant les règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="3d917-1230">The instance constructor to invoke is determined using the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="3d917-1231">L’ensemble de constructeurs d’instance candidats se compose de tous les constructeurs d’instance accessibles déclarés dans `T` qui sont applicables au niveau `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1231">The set of candidate instance constructors consists of all accessible instance constructors declared in `T` which are applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span> <span data-ttu-id="3d917-1232">Si l’ensemble de constructeurs d’instance candidats est vide, ou si un seul constructeur d’instance optimal ne peut pas être identifié, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1232">If the set of candidate instance constructors is empty, or if a single best instance constructor cannot be identified, a binding-time error occurs.</span></span>
    * <span data-ttu-id="3d917-1233">Le résultat de la *object_creation_expression* est une valeur de type `T`, à savoir la valeur produite en appelant le constructeur d’instance déterminé à l’étape précédente.</span><span class="sxs-lookup"><span data-stu-id="3d917-1233">The result of the *object_creation_expression* is a value of type `T`, namely the value produced by invoking the instance constructor determined in the step above.</span></span>
*  <span data-ttu-id="3d917-1234">Sinon, le *object_creation_expression* n’est pas valide, et se produit une erreur au moment de la liaison.</span><span class="sxs-lookup"><span data-stu-id="3d917-1234">Otherwise, the *object_creation_expression* is invalid, and a binding-time error occurs.</span></span>

<span data-ttu-id="3d917-1235">Même si le *object_creation_expression* est dynamiquement lié, le type de compilation est toujours `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1235">Even if the *object_creation_expression* is dynamically bound, the compile-time type is still `T`.</span></span>

<span data-ttu-id="3d917-1236">L’exécution du traitement d’un *object_creation_expression* du formulaire `new T(A)`, où `T` est *class_type* ou un *struct_type* et `A` est facultative *argument_list*, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-1236">The run-time processing of an *object_creation_expression* of the form `new T(A)`, where `T` is *class_type* or a *struct_type* and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*   <span data-ttu-id="3d917-1237">Si `T` est un *class_type*:</span><span class="sxs-lookup"><span data-stu-id="3d917-1237">If `T` is a *class_type*:</span></span>
    * <span data-ttu-id="3d917-1238">Une nouvelle instance de la classe `T` est allouée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1238">A new instance of class `T` is allocated.</span></span> <span data-ttu-id="3d917-1239">Si vous ne comporte pas assez de mémoire disponible pour allouer la nouvelle instance, un `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1239">If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.</span></span>
    * <span data-ttu-id="3d917-1240">Tous les champs de la nouvelle instance sont initialisés à leurs valeurs par défaut ([valeurs par défaut](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1240">All fields of the new instance are initialized to their default values ([Default values](variables.md#default-values)).</span></span>
    * <span data-ttu-id="3d917-1241">Le constructeur d’instance est appelé conformément aux règles d’appel de fonction membre ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1241">The instance constructor is invoked according to the rules of function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span> <span data-ttu-id="3d917-1242">Une référence à l’instance nouvellement allouée est automatiquement passée au constructeur d’instance et l’instance est accessible à partir de ce constructeur en tant que `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1242">A reference to the newly allocated instance is automatically passed to the instance constructor and the instance can be accessed from within that constructor as `this`.</span></span>
*   <span data-ttu-id="3d917-1243">Si `T` est un *struct_type*:</span><span class="sxs-lookup"><span data-stu-id="3d917-1243">If `T` is a *struct_type*:</span></span>
    * <span data-ttu-id="3d917-1244">Une instance du type `T` est créée en allouant une variable locale temporaire.</span><span class="sxs-lookup"><span data-stu-id="3d917-1244">An instance of type `T` is created by allocating a temporary local variable.</span></span> <span data-ttu-id="3d917-1245">Depuis un constructeur d’instance d’un *struct_type* est nécessaire à affecter définitivement une valeur pour chaque champ de l’instance en cours de création, aucune initialisation de la variable temporaire est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="3d917-1245">Since an instance constructor of a *struct_type* is required to definitely assign a value to each field of the instance being created, no initialization of the temporary variable is necessary.</span></span>
    * <span data-ttu-id="3d917-1246">Le constructeur d’instance est appelé conformément aux règles d’appel de fonction membre ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1246">The instance constructor is invoked according to the rules of function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span> <span data-ttu-id="3d917-1247">Une référence à l’instance nouvellement allouée est automatiquement passée au constructeur d’instance et l’instance est accessible à partir de ce constructeur en tant que `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1247">A reference to the newly allocated instance is automatically passed to the instance constructor and the instance can be accessed from within that constructor as `this`.</span></span>

#### <a name="object-initializers"></a><span data-ttu-id="3d917-1248">Initialiseurs d’objet</span><span class="sxs-lookup"><span data-stu-id="3d917-1248">Object initializers</span></span>

<span data-ttu-id="3d917-1249">Un ***initialiseur d’objet*** spécifie les valeurs de zéro ou plusieurs champs, des propriétés ou des éléments indexés d’un objet.</span><span class="sxs-lookup"><span data-stu-id="3d917-1249">An ***object initializer*** specifies values for zero or more fields, properties or indexed elements of an object.</span></span>

```antlr
object_initializer
    : '{' member_initializer_list? '}'
    | '{' member_initializer_list ',' '}'
    ;

member_initializer_list
    : member_initializer (',' member_initializer)*
    ;

member_initializer
    : initializer_target '=' initializer_value
    ;

initializer_target
    : identifier
    | '[' argument_list ']'
    ;

initializer_value
    : expression
    | object_or_collection_initializer
    ;
```

<span data-ttu-id="3d917-1250">Un initialiseur d’objet se compose d’une séquence d’initialiseurs de membres, encadré par `{` et `}` des jetons et séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="3d917-1250">An object initializer consists of a sequence of member initializers, enclosed by `{` and `}` tokens and separated by commas.</span></span> <span data-ttu-id="3d917-1251">Chaque *member_initializer* désigne une cible pour l’initialisation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1251">Each *member_initializer* designates a target for the initialization.</span></span> <span data-ttu-id="3d917-1252">Un *identificateur* doit nommer un champ accessible ou une propriété de l’objet en cours d’initialisation, tandis qu’un *argument_list* placé entre crochets doit spécifier des arguments pour un indexeur accessible sur le objet en cours d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1252">An *identifier* must name an accessible field or property of the object being initialized, whereas an *argument_list* enclosed in square brackets must specify arguments for an accessible indexer on the object being initialized.</span></span> <span data-ttu-id="3d917-1253">C’est une erreur pour un initialiseur d’objet inclure plus d’un initialiseur de membre pour le même champ ou propriété.</span><span class="sxs-lookup"><span data-stu-id="3d917-1253">It is an error for an object initializer to include more than one member initializer for the same field or property.</span></span>

<span data-ttu-id="3d917-1254">Chaque *initializer_target* est suivi par un signe égal et une expression, un initialiseur d’objet ou un initialiseur de collection.</span><span class="sxs-lookup"><span data-stu-id="3d917-1254">Each *initializer_target* is followed by an equals sign and either an expression, an object initializer or a collection initializer.</span></span> <span data-ttu-id="3d917-1255">Il n’est pas possible pour les expressions au sein de l’initialiseur d’objet pour faire référence à l’objet nouvellement créé, qu'il est en cours d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1255">It is not possible for expressions within the object initializer to refer to the newly created object it is initializing.</span></span>

<span data-ttu-id="3d917-1256">Un initialiseur de membre qui spécifie une expression après le signe égal est traité de la même façon qu’une affectation ([assignation Simple](expressions.md#simple-assignment)) à la cible.</span><span class="sxs-lookup"><span data-stu-id="3d917-1256">A member initializer that specifies an expression after the equals sign is processed in the same way as an assignment ([Simple assignment](expressions.md#simple-assignment)) to the target.</span></span>

<span data-ttu-id="3d917-1257">Un initialiseur de membre qui spécifie un initialiseur d’objet après le signe égal est un ***initialiseur d’objet imbriqués***, c'est-à-dire une initialisation d’un objet incorporé.</span><span class="sxs-lookup"><span data-stu-id="3d917-1257">A member initializer that specifies an object initializer after the equals sign is a ***nested object initializer***, i.e. an initialization of an embedded object.</span></span> <span data-ttu-id="3d917-1258">Au lieu d’affecter une nouvelle valeur pour le champ ou la propriété, les attributions dans l’initialiseur d’objet imbriqués sont traitées comme affectations aux membres du champ ou de propriété.</span><span class="sxs-lookup"><span data-stu-id="3d917-1258">Instead of assigning a new value to the field or property, the assignments in the nested object initializer are treated as assignments to members of the field or property.</span></span> <span data-ttu-id="3d917-1259">Initialiseurs d’objets imbriqués ne peut pas être appliqués aux propriétés avec un type valeur, ou à des champs en lecture seule avec un type valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1259">Nested object initializers cannot be applied to properties with a value type, or to read-only fields with a value type.</span></span>

<span data-ttu-id="3d917-1260">Un initialiseur de membre qui spécifie un initialiseur de collection après le signe égal est une initialisation d’une collection incorporée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1260">A member initializer that specifies a collection initializer after the equals sign is an initialization of an embedded collection.</span></span> <span data-ttu-id="3d917-1261">Au lieu d’affecter une nouvelle collection pour le champ cible, la propriété ou l’indexeur, les éléments contenus dans l’initialiseur sont ajoutés à la collection référencée par la cible.</span><span class="sxs-lookup"><span data-stu-id="3d917-1261">Instead of assigning a new collection to the target field, property or indexer, the elements given in the initializer are added to the collection referenced by the target.</span></span> <span data-ttu-id="3d917-1262">La cible doit être d’un type de collection qui satisfait aux exigences spécifiées dans [initialiseurs de collections](expressions.md#collection-initializers).</span><span class="sxs-lookup"><span data-stu-id="3d917-1262">The target must be of a collection type that satisfies the requirements specified in [Collection initializers](expressions.md#collection-initializers).</span></span>

<span data-ttu-id="3d917-1263">Les arguments à un initialiseur de l’index seront toujours évaluées une seule fois.</span><span class="sxs-lookup"><span data-stu-id="3d917-1263">The arguments to an index initializer will always be evaluated exactly once.</span></span> <span data-ttu-id="3d917-1264">Par conséquent, même si les arguments se retrouvent jamais utilisé (par exemple, en raison d’un initialiseur vide imbriqué), ils sont évalués pour leurs effets.</span><span class="sxs-lookup"><span data-stu-id="3d917-1264">Thus, even if the arguments end up never getting used (e.g. because of an empty nested initializer), they will be evaluated for their side effects.</span></span>

<span data-ttu-id="3d917-1265">La classe suivante représente un point avec deux coordonnées :</span><span class="sxs-lookup"><span data-stu-id="3d917-1265">The following class represents a point with two coordinates:</span></span>
```csharp
public class Point
{
    int x, y;

    public int X { get { return x; } set { x = value; } }
    public int Y { get { return y; } set { y = value; } }
}
```

<span data-ttu-id="3d917-1266">Une instance de `Point` peut être créé et initialisé comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-1266">An instance of `Point` can be created and initialized as follows:</span></span>
```csharp
Point a = new Point { X = 0, Y = 1 };
```
<span data-ttu-id="3d917-1267">qui a le même effet que</span><span class="sxs-lookup"><span data-stu-id="3d917-1267">which has the same effect as</span></span>
```csharp
Point __a = new Point();
__a.X = 0;
__a.Y = 1; 
Point a = __a;
```
<span data-ttu-id="3d917-1268">où `__a` est une variable temporaire sinon invisible et inaccessible.</span><span class="sxs-lookup"><span data-stu-id="3d917-1268">where `__a` is an otherwise invisible and inaccessible temporary variable.</span></span> <span data-ttu-id="3d917-1269">La classe suivante représente un rectangle créé à partir de deux points :</span><span class="sxs-lookup"><span data-stu-id="3d917-1269">The following class represents a rectangle created from two points:</span></span>
```csharp
public class Rectangle
{
    Point p1, p2;

    public Point P1 { get { return p1; } set { p1 = value; } }
    public Point P2 { get { return p2; } set { p2 = value; } }
}
```

<span data-ttu-id="3d917-1270">Une instance de `Rectangle` peut être créé et initialisé comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-1270">An instance of `Rectangle` can be created and initialized as follows:</span></span>
```csharp
Rectangle r = new Rectangle {
    P1 = new Point { X = 0, Y = 1 },
    P2 = new Point { X = 2, Y = 3 }
};
```
<span data-ttu-id="3d917-1271">qui a le même effet que</span><span class="sxs-lookup"><span data-stu-id="3d917-1271">which has the same effect as</span></span>
```csharp
Rectangle __r = new Rectangle();
Point __p1 = new Point();
__p1.X = 0;
__p1.Y = 1;
__r.P1 = __p1;
Point __p2 = new Point();
__p2.X = 2;
__p2.Y = 3;
__r.P2 = __p2; 
Rectangle r = __r;
```
<span data-ttu-id="3d917-1272">où `__r`, `__p1` et `__p2` sont des variables temporaires qui sont sinon invisible et inaccessible.</span><span class="sxs-lookup"><span data-stu-id="3d917-1272">where `__r`, `__p1` and `__p2` are temporary variables that are otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="3d917-1273">Si `Rectangle`du constructeur alloue les deux incorporés `Point` instances</span><span class="sxs-lookup"><span data-stu-id="3d917-1273">If `Rectangle`'s constructor allocates the two embedded `Point` instances</span></span>
```csharp
public class Rectangle
{
    Point p1 = new Point();
    Point p2 = new Point();

    public Point P1 { get { return p1; } }
    public Point P2 { get { return p2; } }
}
```
<span data-ttu-id="3d917-1274">la construction suivante peut être utilisée pour initialiser l’embedded `Point` instances au lieu d’affecter de nouvelles instances :</span><span class="sxs-lookup"><span data-stu-id="3d917-1274">the following construct can be used to initialize the embedded `Point` instances instead of assigning new instances:</span></span>
```csharp
Rectangle r = new Rectangle {
    P1 = { X = 0, Y = 1 },
    P2 = { X = 2, Y = 3 }
};
```
<span data-ttu-id="3d917-1275">qui a le même effet que</span><span class="sxs-lookup"><span data-stu-id="3d917-1275">which has the same effect as</span></span>
```csharp
Rectangle __r = new Rectangle();
__r.P1.X = 0;
__r.P1.Y = 1;
__r.P2.X = 2;
__r.P2.Y = 3;
Rectangle r = __r;
```

<span data-ttu-id="3d917-1276">Étant donné une définition appropriée de C, l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="3d917-1276">Given an appropriate definition of C, the following example:</span></span>
```csharp
var c = new C {
    x = true,
    y = { a = "Hello" },
    z = { 1, 2, 3 },
    ["x"] = 5,
    [0,0] = { "a", "b" },
    [1,2] = {}
};
```
<span data-ttu-id="3d917-1277">est équivalente à cette série d’affectations :</span><span class="sxs-lookup"><span data-stu-id="3d917-1277">is equivalent to this series of assignments:</span></span>
```csharp
C __c = new C();
__c.x = true;
__c.y.a = "Hello";
__c.z.Add(1); 
__c.z.Add(2);
__c.z.Add(3);
string __i1 = "x";
__c[__i1] = 5;
int __i2 = 0, __i3 = 0;
__c[__i2,__i3].Add("a");
__c[__i2,__i3].Add("b");
int __i4 = 1, __i5 = 2;
var c = __c;
```
<span data-ttu-id="3d917-1278">où `__c`, etc., sont des variables générées sont invisibles et inaccessibles au code source.</span><span class="sxs-lookup"><span data-stu-id="3d917-1278">where `__c`, etc., are generated variables that are invisible and inaccessible to the source code.</span></span> <span data-ttu-id="3d917-1279">Notez que les arguments pour `[0,0]` sont évaluée qu’une seule fois et les arguments pour `[1,2]` sont évaluées qu’une seule fois, même si elles ne sont jamais utilisées.</span><span class="sxs-lookup"><span data-stu-id="3d917-1279">Note that the arguments for `[0,0]` are evaluated only once, and the arguments for `[1,2]` are evaluated once even though they are never used.</span></span>

#### <a name="collection-initializers"></a><span data-ttu-id="3d917-1280">Initialiseurs de collection</span><span class="sxs-lookup"><span data-stu-id="3d917-1280">Collection initializers</span></span>

<span data-ttu-id="3d917-1281">Un initialiseur de collection spécifie les éléments d’une collection.</span><span class="sxs-lookup"><span data-stu-id="3d917-1281">A collection initializer specifies the elements of a collection.</span></span>

```antlr
collection_initializer
    : '{' element_initializer_list '}'
    | '{' element_initializer_list ',' '}'
    ;

element_initializer_list
    : element_initializer (',' element_initializer)*
    ;

element_initializer
    : non_assignment_expression
    | '{' expression_list '}'
    ;

expression_list
    : expression (',' expression)*
    ;
```

<span data-ttu-id="3d917-1282">Un initialiseur de collection se compose d’une séquence d’initialiseurs d’éléments, encadré par `{` et `}` des jetons et séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="3d917-1282">A collection initializer consists of a sequence of element initializers, enclosed by `{` and `}` tokens and separated by commas.</span></span> <span data-ttu-id="3d917-1283">Chaque initialiseur d’élément spécifie un élément à ajouter à la collection d’objets en cours d’initialisation et se compose d’une liste d’expressions délimitée par `{` et `}` des jetons et séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="3d917-1283">Each element initializer specifies an element to be added to the collection object being initialized, and consists of a list of expressions enclosed by `{` and `}` tokens and separated by commas.</span></span>  <span data-ttu-id="3d917-1284">Un initialiseur d’élément de contenant une seule expression peut être écrite sans accolades, mais ne peut pas être ensuite une expression d’assignation, pour éviter toute ambiguïté avec initialiseurs de membres.</span><span class="sxs-lookup"><span data-stu-id="3d917-1284">A single-expression element initializer can be written without braces, but cannot then be an assignment expression, to avoid ambiguity with member initializers.</span></span> <span data-ttu-id="3d917-1285">Le *non_assignment_expression* production est définie dans [Expression](expressions.md#expression).</span><span class="sxs-lookup"><span data-stu-id="3d917-1285">The *non_assignment_expression* production is defined in [Expression](expressions.md#expression).</span></span>

<span data-ttu-id="3d917-1286">Voici un exemple d’une expression de création d’objet qui inclut un initialiseur de collection :</span><span class="sxs-lookup"><span data-stu-id="3d917-1286">The following is an example of an object creation expression that includes a collection initializer:</span></span>
```csharp
List<int> digits = new List<int> { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
```

<span data-ttu-id="3d917-1287">L’objet de collection à laquelle est appliqué un initialiseur de collection doit être d’un type qui implémente `System.Collections.IEnumerable` ou une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1287">The collection object to which a collection initializer is applied must be of a type that implements `System.Collections.IEnumerable` or a compile-time error occurs.</span></span> <span data-ttu-id="3d917-1288">Pour chaque élément est spécifié dans l’ordre, l’initialiseur de collection appelle un `Add` méthode sur la cible de l’objet avec la liste d’expressions de l’initialiseur d’élément en tant que liste d’arguments, application de recherche de membres normales et pour chaque appel de la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="3d917-1288">For each specified element in order, the collection initializer invokes an `Add` method on the target object with the expression list of the element initializer as argument list, applying normal member lookup and overload resolution for each invocation.</span></span> <span data-ttu-id="3d917-1289">Par conséquent, l’objet de collection doit avoir une méthode d’extension ou d’instance applicable avec le nom `Add` pour chaque initialiseur d’élément.</span><span class="sxs-lookup"><span data-stu-id="3d917-1289">Thus, the collection object must have an applicable instance or extension method with the name `Add` for each element initializer.</span></span>

<span data-ttu-id="3d917-1290">La classe suivante représente un contact avec un nom et une liste de numéros de téléphone :</span><span class="sxs-lookup"><span data-stu-id="3d917-1290">The following class represents a contact with a name and a list of phone numbers:</span></span>
```csharp
public class Contact
{
    string name;
    List<string> phoneNumbers = new List<string>();

    public string Name { get { return name; } set { name = value; } }

    public List<string> PhoneNumbers { get { return phoneNumbers; } }
}
```

<span data-ttu-id="3d917-1291">Un `List<Contact>` peut être créé et initialisé comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-1291">A `List<Contact>` can be created and initialized as follows:</span></span>
```csharp
var contacts = new List<Contact> {
    new Contact {
        Name = "Chris Smith",
        PhoneNumbers = { "206-555-0101", "425-882-8080" }
    },
    new Contact {
        Name = "Bob Harris",
        PhoneNumbers = { "650-555-0199" }
    }
};
```
<span data-ttu-id="3d917-1292">qui a le même effet que</span><span class="sxs-lookup"><span data-stu-id="3d917-1292">which has the same effect as</span></span>
```csharp
var __clist = new List<Contact>();
Contact __c1 = new Contact();
__c1.Name = "Chris Smith";
__c1.PhoneNumbers.Add("206-555-0101");
__c1.PhoneNumbers.Add("425-882-8080");
__clist.Add(__c1);
Contact __c2 = new Contact();
__c2.Name = "Bob Harris";
__c2.PhoneNumbers.Add("650-555-0199");
__clist.Add(__c2);
var contacts = __clist;
```
<span data-ttu-id="3d917-1293">où `__clist`, `__c1` et `__c2` sont des variables temporaires qui sont sinon invisible et inaccessible.</span><span class="sxs-lookup"><span data-stu-id="3d917-1293">where `__clist`, `__c1` and `__c2` are temporary variables that are otherwise invisible and inaccessible.</span></span>

#### <a name="array-creation-expressions"></a><span data-ttu-id="3d917-1294">Expressions de création de tableau</span><span class="sxs-lookup"><span data-stu-id="3d917-1294">Array creation expressions</span></span>

<span data-ttu-id="3d917-1295">Un *array_creation_expression* est utilisé pour créer une nouvelle instance d’un *array_type*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1295">An *array_creation_expression* is used to create a new instance of an *array_type*.</span></span>

```antlr
array_creation_expression
    : 'new' non_array_type '[' expression_list ']' rank_specifier* array_initializer?
    | 'new' array_type array_initializer
    | 'new' rank_specifier array_initializer
    ;
```

<span data-ttu-id="3d917-1296">Une expression de création de tableau de la première forme alloue une instance de tableau du type qui résulte de la suppression de chacune des expressions individuelles à partir de la liste d’expressions.</span><span class="sxs-lookup"><span data-stu-id="3d917-1296">An array creation expression of the first form allocates an array instance of the type that results from deleting each of the individual expressions from the expression list.</span></span> <span data-ttu-id="3d917-1297">Par exemple, l’expression de création de tableau `new int[10,20]` génère une instance de tableau de type `int[,]`et l’expression de création de tableau `new int[10][,]` génère un tableau de type `int[][,]`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1297">For example, the array creation expression `new int[10,20]` produces an array instance of type `int[,]`, and the array creation expression `new int[10][,]` produces an array of type `int[][,]`.</span></span> <span data-ttu-id="3d917-1298">Chaque expression contenue dans la liste d’expressions doit être de type `int`, `uint`, `long`, ou `ulong`, ou implicitement convertible en un ou plusieurs de ces types.</span><span class="sxs-lookup"><span data-stu-id="3d917-1298">Each expression in the expression list must be of type `int`, `uint`, `long`, or `ulong`, or implicitly convertible to one or more of these types.</span></span> <span data-ttu-id="3d917-1299">La valeur de chaque expression détermine la longueur de la dimension correspondante dans l’instance de tableau nouvellement allouée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1299">The value of each expression determines the length of the corresponding dimension in the newly allocated array instance.</span></span> <span data-ttu-id="3d917-1300">Étant donné que la longueur d’une dimension de tableau doit être non négative, il est une erreur de compilation d’avoir un *constant_expression* avec une valeur négative dans la liste d’expressions.</span><span class="sxs-lookup"><span data-stu-id="3d917-1300">Since the length of an array dimension must be nonnegative, it is a compile-time error to have a *constant_expression* with a negative value in the expression list.</span></span>

<span data-ttu-id="3d917-1301">Sauf dans un contexte unsafe ([contextes Unsafe](unsafe-code.md#unsafe-contexts)), la disposition des tableaux n’est pas spécifiée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1301">Except in an unsafe context ([Unsafe contexts](unsafe-code.md#unsafe-contexts)), the layout of arrays is unspecified.</span></span>

<span data-ttu-id="3d917-1302">Si une expression de création de tableau de la première forme comprend un initialiseur de tableau, chaque expression dans la liste d’expressions doit être une constante et les longueurs de classement et de dimension spécifiés par la liste de l’expression doivent correspondre à ceux de l’initialiseur de tableau.</span><span class="sxs-lookup"><span data-stu-id="3d917-1302">If an array creation expression of the first form includes an array initializer, each expression in the expression list must be a constant and the rank and dimension lengths specified by the expression list must match those of the array initializer.</span></span>

<span data-ttu-id="3d917-1303">Dans une expression de création de tableau sous la forme deuxième ou troisième, le rang du spécificateur de type ou le rang de tableau spécifié doit correspondre à celui de l’initialiseur de tableau.</span><span class="sxs-lookup"><span data-stu-id="3d917-1303">In an array creation expression of the second or third form, the rank of the specified array type or rank specifier must match that of the array initializer.</span></span> <span data-ttu-id="3d917-1304">Les longueurs de dimensions individuelles sont déduits à partir du nombre d’éléments dans chacun des niveaux d’imbrication correspondants de l’initialiseur de tableau.</span><span class="sxs-lookup"><span data-stu-id="3d917-1304">The individual dimension lengths are inferred from the number of elements in each of the corresponding nesting levels of the array initializer.</span></span> <span data-ttu-id="3d917-1305">Par conséquent, l’expression</span><span class="sxs-lookup"><span data-stu-id="3d917-1305">Thus, the expression</span></span>
```csharp
new int[,] {{0, 1}, {2, 3}, {4, 5}}
```
<span data-ttu-id="3d917-1306">correspond exactement à</span><span class="sxs-lookup"><span data-stu-id="3d917-1306">exactly corresponds to</span></span>
```csharp
new int[3, 2] {{0, 1}, {2, 3}, {4, 5}}
```

<span data-ttu-id="3d917-1307">Une expression de création de tableau de la troisième forme est appelée un ***implicitement typées expression de création de tableau***.</span><span class="sxs-lookup"><span data-stu-id="3d917-1307">An array creation expression of the third form is referred to as an ***implicitly typed array creation expression***.</span></span> <span data-ttu-id="3d917-1308">Elle est similaire à la seconde forme, à ceci près que le type d’élément du tableau n’est explicitement indiqué, déterminé, mais le type commun mieux ([recherche le meilleur type courantes d’un ensemble d’expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)) de l’ensemble d’expressions dans le tableau initialiseur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1308">It is similar to the second form, except that the element type of the array is not explicitly given, but determined as the best common type ([Finding the best common type of a set of expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)) of the set of expressions in the array initializer.</span></span> <span data-ttu-id="3d917-1309">Pour un tableau multidimensionnel, c'est-à-dire celui dans lequel le *rank_specifier* contient au moins une virgule, ce jeu comprend l’ensemble *expression*s trouvé dans imbriqués *array_initializer*s.</span><span class="sxs-lookup"><span data-stu-id="3d917-1309">For a multidimensional array, i.e., one where the *rank_specifier* contains at least one comma, this set comprises all *expression*s found in nested *array_initializer*s.</span></span>

<span data-ttu-id="3d917-1310">Les initialiseurs de tableau sont décrites dans [les initialiseurs de tableaux](arrays.md#array-initializers).</span><span class="sxs-lookup"><span data-stu-id="3d917-1310">Array initializers are described further in [Array initializers](arrays.md#array-initializers).</span></span>

<span data-ttu-id="3d917-1311">Le résultat de l’évaluation d’une expression de création de tableau est classé en tant que valeur, à savoir une référence à l’instance de tableau nouvellement allouée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1311">The result of evaluating an array creation expression is classified as a value, namely a reference to the newly allocated array instance.</span></span> <span data-ttu-id="3d917-1312">Le traitement de l’exécution d’une expression de création de tableau se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-1312">The run-time processing of an array creation expression consists of the following steps:</span></span>

*  <span data-ttu-id="3d917-1313">Les expressions de longueur de dimension de la *liste d’expressions* sont évaluées dans l’ordre, de gauche à droite.</span><span class="sxs-lookup"><span data-stu-id="3d917-1313">The dimension length expressions of the *expression_list* are evaluated in order, from left to right.</span></span> <span data-ttu-id="3d917-1314">Suite de l’évaluation de chaque expression, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) à un des types suivants est effectuée : `int`, `uint`, `long`, `ulong`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1314">Following evaluation of each expression, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) to one of the following types is performed: `int`, `uint`, `long`, `ulong`.</span></span> <span data-ttu-id="3d917-1315">Le premier type dans cette liste pour laquelle il existe une conversion implicite est choisi.</span><span class="sxs-lookup"><span data-stu-id="3d917-1315">The first type in this list for which an implicit conversion exists is chosen.</span></span> <span data-ttu-id="3d917-1316">Si l’évaluation d’une expression ou la conversion implicite suite provoque une exception, puis aucune nouvelle expression n’est évaluées et aucune autre étape n’est exécutés.</span><span class="sxs-lookup"><span data-stu-id="3d917-1316">If evaluation of an expression or the subsequent implicit conversion causes an exception, then no further expressions are evaluated and no further steps are executed.</span></span>
*  <span data-ttu-id="3d917-1317">Les valeurs calculées pour les longueurs de dimensions sont validées comme suit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1317">The computed values for the dimension lengths are validated as follows.</span></span> <span data-ttu-id="3d917-1318">Si un ou plusieurs des valeurs sont inférieures à zéro, un `System.OverflowException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1318">If one or more of the values are less than zero, a `System.OverflowException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="3d917-1319">Une instance de tableau avec les longueurs de dimensions donné est allouée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1319">An array instance with the given dimension lengths is allocated.</span></span> <span data-ttu-id="3d917-1320">Si vous ne comporte pas assez de mémoire disponible pour allouer la nouvelle instance, un `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1320">If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="3d917-1321">Tous les éléments de la nouvelle instance de tableau sont initialisés à leurs valeurs par défaut ([valeurs par défaut](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1321">All elements of the new array instance are initialized to their default values ([Default values](variables.md#default-values)).</span></span>
*  <span data-ttu-id="3d917-1322">Si l’expression de création de tableau contient un initialiseur de tableau, chaque expression dans l’initialiseur de tableau est évaluée et assignée à l’élément de tableau correspondant.</span><span class="sxs-lookup"><span data-stu-id="3d917-1322">If the array creation expression contains an array initializer, then each expression in the array initializer is evaluated and assigned to its corresponding array element.</span></span> <span data-ttu-id="3d917-1323">Les évaluations et les affectations sont effectuées dans l’ordre les expressions sont écrites dans l’initialiseur de tableau, en d’autres termes, les éléments sont initialisés dans l’index l’ordre croissant, avec la dimension à l’extrême droite tout d’abord l’augmentation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1323">The evaluations and assignments are performed in the order the expressions are written in the array initializer—in other words, elements are initialized in increasing index order, with the rightmost dimension increasing first.</span></span> <span data-ttu-id="3d917-1324">Si l’évaluation d’une expression donnée ou l’assignation consécutive de l’élément de tableau correspondant provoque une exception, aucun autre élément n’est initialisés (et les éléments restants ainsi auront leurs valeurs par défaut).</span><span class="sxs-lookup"><span data-stu-id="3d917-1324">If evaluation of a given expression or the subsequent assignment to the corresponding array element causes an exception, then no further elements are initialized (and the remaining elements will thus have their default values).</span></span>

<span data-ttu-id="3d917-1325">Une expression de création de tableau permet l’instanciation d’un tableau avec des éléments d’un type tableau, mais les éléments d’un tel tableau doivent être initialisés manuellement.</span><span class="sxs-lookup"><span data-stu-id="3d917-1325">An array creation expression permits instantiation of an array with elements of an array type, but the elements of such an array must be manually initialized.</span></span> <span data-ttu-id="3d917-1326">Par exemple, l’instruction</span><span class="sxs-lookup"><span data-stu-id="3d917-1326">For example, the statement</span></span>
```csharp
int[][] a = new int[100][];
```
<span data-ttu-id="3d917-1327">Crée un tableau unidimensionnel avec 100 éléments de type `int[]`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1327">creates a single-dimensional array with 100 elements of type `int[]`.</span></span> <span data-ttu-id="3d917-1328">La valeur initiale de chaque élément est `null`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1328">The initial value of each element is `null`.</span></span> <span data-ttu-id="3d917-1329">Il n’est pas possible pour la même expression de création de tableau instancier également les sous-tableaux et l’instruction</span><span class="sxs-lookup"><span data-stu-id="3d917-1329">It is not possible for the same array creation expression to also instantiate the sub-arrays, and the statement</span></span>
```csharp
int[][] a = new int[100][5];        // Error
```
<span data-ttu-id="3d917-1330">génère une erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1330">results in a compile-time error.</span></span> <span data-ttu-id="3d917-1331">Instanciation des sous-tableaux doit plutôt être effectuée manuellement, comme dans</span><span class="sxs-lookup"><span data-stu-id="3d917-1331">Instantiation of the sub-arrays must instead be performed manually, as in</span></span>
```csharp
int[][] a = new int[100][];
for (int i = 0; i < 100; i++) a[i] = new int[5];
```

<span data-ttu-id="3d917-1332">Quand un tableau de tableaux a une forme « rectangulaire », c'est-à-dire lorsque les sous-tableaux ont tous la même longueur, il est plus efficace d’utiliser un tableau multidimensionnel.</span><span class="sxs-lookup"><span data-stu-id="3d917-1332">When an array of arrays has a "rectangular" shape, that is when the sub-arrays are all of the same length, it is more efficient to use a multi-dimensional array.</span></span> <span data-ttu-id="3d917-1333">Dans l’exemple ci-dessus, l’instanciation du tableau de tableaux crée 101 objets — un tableau extérieur et 100 sous-tableaux.</span><span class="sxs-lookup"><span data-stu-id="3d917-1333">In the example above, instantiation of the array of arrays creates 101 objects—one outer array and 100 sub-arrays.</span></span> <span data-ttu-id="3d917-1334">En revanche,</span><span class="sxs-lookup"><span data-stu-id="3d917-1334">In contrast,</span></span>
```csharp
int[,] = new int[100, 5];
```
<span data-ttu-id="3d917-1335">Crée un seul objet, un tableau à deux dimensions et réalise l’allocation dans une seule instruction.</span><span class="sxs-lookup"><span data-stu-id="3d917-1335">creates only a single object, a two-dimensional array, and accomplishes the allocation in a single statement.</span></span>

<span data-ttu-id="3d917-1336">Voici quelques exemples d’expressions de la création de tableau implicitement typé :</span><span class="sxs-lookup"><span data-stu-id="3d917-1336">The following are examples of implicitly typed array creation expressions:</span></span>
```csharp
var a = new[] { 1, 10, 100, 1000 };                       // int[]

var b = new[] { 1, 1.5, 2, 2.5 };                         // double[]

var c = new[,] { { "hello", null }, { "world", "!" } };   // string[,]

var d = new[] { 1, "one", 2, "two" };                     // Error
```

<span data-ttu-id="3d917-1337">La dernière expression provoque une erreur de compilation, car ni `int` ni `string` est implicitement convertible à l’autre, puis tapez et il n’est pas mieux courants.</span><span class="sxs-lookup"><span data-stu-id="3d917-1337">The last expression causes a compile-time error because neither `int` nor `string` is implicitly convertible to the other, and so there is no best common type.</span></span> <span data-ttu-id="3d917-1338">Une expression de création de tableau explicitement typée doit être utilisée dans ce cas, par exemple en spécifiant le type doit être `object[]`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1338">An explicitly typed array creation expression must be used in this case, for example specifying the type to be `object[]`.</span></span> <span data-ttu-id="3d917-1339">Vous pouvez également l’un des éléments pouvant être casté en un type de base commun, qui deviendrait ensuite le type d’élément déduits.</span><span class="sxs-lookup"><span data-stu-id="3d917-1339">Alternatively, one of the elements can be cast to a common base type, which would then become the inferred element type.</span></span>

<span data-ttu-id="3d917-1340">Expressions de création de tableau implicitement typé peuvent être combinées avec des initialiseurs d’objet ([expressions de création d’objet anonyme](expressions.md#anonymous-object-creation-expressions)) pour créer de manière anonyme tapé des structures de données.</span><span class="sxs-lookup"><span data-stu-id="3d917-1340">Implicitly typed array creation expressions can be combined with anonymous object initializers ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) to create anonymously typed data structures.</span></span> <span data-ttu-id="3d917-1341">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3d917-1341">For example:</span></span>
```csharp
var contacts = new[] {
    new {
        Name = "Chris Smith",
        PhoneNumbers = new[] { "206-555-0101", "425-882-8080" }
    },
    new {
        Name = "Bob Harris",
        PhoneNumbers = new[] { "650-555-0199" }
    }
};
```

#### <a name="delegate-creation-expressions"></a><span data-ttu-id="3d917-1342">Expressions de création de délégué</span><span class="sxs-lookup"><span data-stu-id="3d917-1342">Delegate creation expressions</span></span>

<span data-ttu-id="3d917-1343">Un *delegate_creation_expression* est utilisé pour créer une nouvelle instance d’un *delegate_type*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1343">A *delegate_creation_expression* is used to create a new instance of a *delegate_type*.</span></span>

```antlr
delegate_creation_expression
    : 'new' delegate_type '(' expression ')'
    ;
```

<span data-ttu-id="3d917-1344">L’argument d’une expression de création de délégué doit être un groupe de méthodes, une fonction anonyme ou une valeur du type de temps de compilation `dynamic` ou un *delegate_type*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1344">The argument of a delegate creation expression must be a method group, an anonymous function or a value of either the compile time type `dynamic` or a *delegate_type*.</span></span> <span data-ttu-id="3d917-1345">Si l’argument est un groupe de méthodes, il identifie la méthode et, pour une méthode d’instance, l’objet pour lequel créer un délégué.</span><span class="sxs-lookup"><span data-stu-id="3d917-1345">If the argument is a method group, it identifies the method and, for an instance method, the object for which to create a delegate.</span></span> <span data-ttu-id="3d917-1346">Si l’argument est une fonction anonyme définit directement les paramètres et le corps de la méthode de la cible du délégué.</span><span class="sxs-lookup"><span data-stu-id="3d917-1346">If the argument is an anonymous function it directly defines the parameters and method body of the delegate target.</span></span> <span data-ttu-id="3d917-1347">Si l’argument est une valeur, il identifie une instance de délégué de laquelle créer une copie.</span><span class="sxs-lookup"><span data-stu-id="3d917-1347">If the argument is a value it identifies a delegate instance of which to create a copy.</span></span>

<span data-ttu-id="3d917-1348">Si le *expression* a le type de compilation `dynamic`, le *delegate_creation_expression* lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)), ainsi que les règles ci-dessous sont appliquées au moment de l’exécution à l’aide du type au moment de l’exécution de la *expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1348">If the *expression* has the compile-time type `dynamic`, the *delegate_creation_expression* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)), and the rules below are applied at run-time using the run-time type of the *expression*.</span></span> <span data-ttu-id="3d917-1349">Sinon, les règles sont appliquées au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1349">Otherwise the rules are applied at compile-time.</span></span>

<span data-ttu-id="3d917-1350">Le traitement au moment de la liaison d’un *delegate_creation_expression* du formulaire `new D(E)`, où `D` est un *delegate_type* et `E` est un *expression* , se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-1350">The binding-time processing of a *delegate_creation_expression* of the form `new D(E)`, where `D` is a *delegate_type* and `E` is an *expression*, consists of the following steps:</span></span>

*  <span data-ttu-id="3d917-1351">Si `E` est un groupe de méthodes, l’expression de création de délégué est traitée de la même façon en tant qu’une conversion de groupe (méthode) ([conversions de groupes de méthode](conversions.md#method-group-conversions)) à partir de `E` à `D`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1351">If `E` is a method group, the delegate creation expression is processed in the same way as a method group conversion ([Method group conversions](conversions.md#method-group-conversions)) from `E` to `D`.</span></span>
*  <span data-ttu-id="3d917-1352">Si `E` est une fonction anonyme, l’expression de création de délégué est traitée de la même façon en tant qu’une conversion de la fonction anonyme ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)) à partir de `E` à `D`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1352">If `E` is an anonymous function, the delegate creation expression is processed in the same way as an anonymous function conversion ([Anonymous function conversions](conversions.md#anonymous-function-conversions)) from `E` to `D`.</span></span>
*  <span data-ttu-id="3d917-1353">Si `E` est une valeur, `E` doivent être compatibles ([déclarations Delegate](delegates.md#delegate-declarations)) avec `D`, et le résultat est une référence à un nouveau délégué de type `D` qui fait référence à l’appel de la même liste en tant que `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1353">If `E` is a value, `E` must be compatible ([Delegate declarations](delegates.md#delegate-declarations)) with `D`, and the result is a reference to a newly created delegate of type `D` that refers to the same invocation list as `E`.</span></span> <span data-ttu-id="3d917-1354">Si `E` n’est pas compatible avec `D`, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1354">If `E` is not compatible with `D`, a compile-time error occurs.</span></span>

<span data-ttu-id="3d917-1355">L’exécution du traitement d’un *delegate_creation_expression* du formulaire `new D(E)`, où `D` est un *delegate_type* et `E` est un *expression* , se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-1355">The run-time processing of a *delegate_creation_expression* of the form `new D(E)`, where `D` is a *delegate_type* and `E` is an *expression*, consists of the following steps:</span></span>

*   <span data-ttu-id="3d917-1356">Si `E` est un groupe de méthodes, l’expression de création de délégué est évaluée comme une conversion de groupe (méthode) ([conversions de groupes de méthode](conversions.md#method-group-conversions)) à partir de `E` à `D`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1356">If `E` is a method group, the delegate creation expression is evaluated as a method group conversion ([Method group conversions](conversions.md#method-group-conversions)) from `E` to `D`.</span></span>
*   <span data-ttu-id="3d917-1357">Si `E` est une fonction anonyme, la création de délégué est évaluée comme une conversion d’une fonction anonyme de `E` à `D` ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1357">If `E` is an anonymous function, the delegate creation is evaluated as an anonymous function conversion from `E` to `D` ([Anonymous function conversions](conversions.md#anonymous-function-conversions)).</span></span>
*   <span data-ttu-id="3d917-1358">Si `E` est une valeur d’un *delegate_type*:</span><span class="sxs-lookup"><span data-stu-id="3d917-1358">If `E` is a value of a *delegate_type*:</span></span>
    * <span data-ttu-id="3d917-1359">`E` est évalué.</span><span class="sxs-lookup"><span data-stu-id="3d917-1359">`E` is evaluated.</span></span> <span data-ttu-id="3d917-1360">Si cette version d’évaluation provoque une exception, aucune étape supplémentaire n’est exécutées.</span><span class="sxs-lookup"><span data-stu-id="3d917-1360">If this evaluation causes an exception, no further steps are executed.</span></span>
    * <span data-ttu-id="3d917-1361">Si la valeur de `E` est `null`, un `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1361">If the value of `E` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
    * <span data-ttu-id="3d917-1362">Une nouvelle instance du type délégué `D` est allouée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1362">A new instance of the delegate type `D` is allocated.</span></span> <span data-ttu-id="3d917-1363">Si vous ne comporte pas assez de mémoire disponible pour allouer la nouvelle instance, un `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1363">If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.</span></span>
    * <span data-ttu-id="3d917-1364">La nouvelle instance de délégué est initialisée avec la même liste d’appel que l’instance de délégué fourni par `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1364">The new delegate instance is initialized with the same invocation list as the delegate instance given by `E`.</span></span>

<span data-ttu-id="3d917-1365">La liste d’appel d’un délégué est déterminée lorsque le délégué est instancié et reste constant pendant toute la durée de vie du délégué.</span><span class="sxs-lookup"><span data-stu-id="3d917-1365">The invocation list of a delegate is determined when the delegate is instantiated and then remains constant for the entire lifetime of the delegate.</span></span> <span data-ttu-id="3d917-1366">En d’autres termes, il n’est pas possible de modifier les entités pouvant être appelé cible d’un délégué une fois qu’il a été créé.</span><span class="sxs-lookup"><span data-stu-id="3d917-1366">In other words, it is not possible to change the target callable entities of a delegate once it has been created.</span></span> <span data-ttu-id="3d917-1367">Lorsque deux délégués sont combinés ou un est supprimé d’un autre ([déclarations Delegate](delegates.md#delegate-declarations)), entraîne un nouveau délégué ; aucun délégué existant n’a son contenu modifié.</span><span class="sxs-lookup"><span data-stu-id="3d917-1367">When two delegates are combined or one is removed from another ([Delegate declarations](delegates.md#delegate-declarations)), a new delegate results; no existing delegate has its contents changed.</span></span>

<span data-ttu-id="3d917-1368">Il n’est pas possible de créer un délégué qui fait référence à une propriété, l’indexeur, l’opérateur défini par l’utilisateur, constructeur d’instance, destructeur ou constructeur statique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1368">It is not possible to create a delegate that refers to a property, indexer, user-defined operator, instance constructor, destructor, or static constructor.</span></span>

<span data-ttu-id="3d917-1369">Comme décrit ci-dessus, quand un délégué est créé à partir d’un groupe de méthodes, la liste de paramètres formels et type de retour du délégué déterminer laquelle des méthodes surchargées à sélectionner.</span><span class="sxs-lookup"><span data-stu-id="3d917-1369">As described above, when a delegate is created from a method group, the formal parameter list and return type of the delegate determine which of the overloaded methods to select.</span></span> <span data-ttu-id="3d917-1370">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-1370">In the example</span></span>
```csharp
delegate double DoubleFunc(double x);

class A
{
    DoubleFunc f = new DoubleFunc(Square);

    static float Square(float x) {
        return x * x;
    }

    static double Square(double x) {
        return x * x;
    }
}
```
<span data-ttu-id="3d917-1371">le `A.f` champ est initialisé avec un délégué qui fait référence à la seconde `Square` (méthode), car cette méthode correspond exactement à la liste de paramètres formels et le type de retour de `DoubleFunc`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1371">the `A.f` field is initialized with a delegate that refers to the second `Square` method because that method exactly matches the formal parameter list and return type of `DoubleFunc`.</span></span> <span data-ttu-id="3d917-1372">Les deuxièmes `Square` méthode était pas présent, une erreur de compilation aurait été obtenu.</span><span class="sxs-lookup"><span data-stu-id="3d917-1372">Had the second `Square` method not been present, a compile-time error would have occurred.</span></span>

#### <a name="anonymous-object-creation-expressions"></a><span data-ttu-id="3d917-1373">Expressions de création d’objet anonyme</span><span class="sxs-lookup"><span data-stu-id="3d917-1373">Anonymous object creation expressions</span></span>

<span data-ttu-id="3d917-1374">Un *anonymous_object_creation_expression* est utilisé pour créer un objet d’un type anonyme.</span><span class="sxs-lookup"><span data-stu-id="3d917-1374">An *anonymous_object_creation_expression* is used to create an object of an anonymous type.</span></span>

```antlr
anonymous_object_creation_expression
    : 'new' anonymous_object_initializer
    ;

anonymous_object_initializer
    : '{' member_declarator_list? '}'
    | '{' member_declarator_list ',' '}'
    ;

member_declarator_list
    : member_declarator (',' member_declarator)*
    ;

member_declarator
    : simple_name
    | member_access
    | base_access
    | null_conditional_member_access
    | identifier '=' expression
    ;
```

<span data-ttu-id="3d917-1375">Un initialiseur d’objet anonyme déclare un type anonyme et retourne une instance de ce type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1375">An anonymous object initializer declares an anonymous type and returns an instance of that type.</span></span> <span data-ttu-id="3d917-1376">Un type anonyme est un type de classe sans nom qui hérite directement de `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1376">An anonymous type is a nameless class type that inherits directly from `object`.</span></span> <span data-ttu-id="3d917-1377">Les membres d’un type anonyme sont une série de propriétés en lecture seule déduit à partir de l’initialiseur d’objet anonyme utilisé pour créer une instance du type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1377">The members of an anonymous type are a sequence of read-only properties inferred from the anonymous object initializer used to create an instance of the type.</span></span> <span data-ttu-id="3d917-1378">Plus précisément, un initialiseur d’objet anonyme du formulaire</span><span class="sxs-lookup"><span data-stu-id="3d917-1378">Specifically, an anonymous object initializer of the form</span></span>
```csharp
new { p1 = e1, p2 = e2, ..., pn = en }
```
<span data-ttu-id="3d917-1379">déclare un type anonyme du formulaire</span><span class="sxs-lookup"><span data-stu-id="3d917-1379">declares an anonymous type of the form</span></span>
```csharp
class __Anonymous1
{
    private readonly T1 f1;
    private readonly T2 f2;
    ...
    private readonly Tn fn;

    public __Anonymous1(T1 a1, T2 a2, ..., Tn an) {
        f1 = a1;
        f2 = a2;
        ...
        fn = an;
    }

    public T1 p1 { get { return f1; } }
    public T2 p2 { get { return f2; } }
    ...
    public Tn pn { get { return fn; } }

    public override bool Equals(object __o) { ... }
    public override int GetHashCode() { ... }
}
```
<span data-ttu-id="3d917-1380">où chaque `Tx` est le type de l’expression correspondante `ex`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1380">where each `Tx` is the type of the corresponding expression `ex`.</span></span> <span data-ttu-id="3d917-1381">L’expression utilisée dans un *member_declarator* doit avoir un type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1381">The expression used in a *member_declarator* must have a type.</span></span> <span data-ttu-id="3d917-1382">Par conséquent, il est une erreur de compilation pour une expression dans une *member_declarator* doit être null ou une fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="3d917-1382">Thus, it is a compile-time error for an expression in a *member_declarator* to be null or an anonymous function.</span></span> <span data-ttu-id="3d917-1383">Il est également une erreur de compilation pour l’expression avoir un type non sécurisé.</span><span class="sxs-lookup"><span data-stu-id="3d917-1383">It is also a compile-time error for the expression to have an unsafe type.</span></span>

<span data-ttu-id="3d917-1384">Les noms d’un type anonyme et du paramètre à son `Equals` méthode sont générées automatiquement par le compilateur et ne peut pas être référencé dans le texte de programme.</span><span class="sxs-lookup"><span data-stu-id="3d917-1384">The names of an anonymous type and of the parameter to its `Equals` method are automatically generated by the compiler and cannot be referenced in program text.</span></span>

<span data-ttu-id="3d917-1385">Dans le même programme, les deux initialiseurs d’objet anonymes qui spécifient une séquence de propriétés des mêmes noms et types de compilation dans le même ordre sont produisent des instances du même type anonyme.</span><span class="sxs-lookup"><span data-stu-id="3d917-1385">Within the same program, two anonymous object initializers that specify a sequence of properties of the same names and compile-time types in the same order will produce instances of the same anonymous type.</span></span>

<span data-ttu-id="3d917-1386">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-1386">In the example</span></span>
```csharp
var p1 = new { Name = "Lawnmower", Price = 495.00 };
var p2 = new { Name = "Shovel", Price = 26.95 };
p1 = p2;
```
<span data-ttu-id="3d917-1387">l’attribution de la dernière ligne est autorisée car `p1` et `p2` sont du même type anonyme.</span><span class="sxs-lookup"><span data-stu-id="3d917-1387">the assignment on the last line is permitted because `p1` and `p2` are of the same anonymous type.</span></span>

<span data-ttu-id="3d917-1388">Le `Equals` et `GetHashcode` méthodes sur des types anonymes substituer les méthodes héritées de `object`et sont définis en termes de la `Equals` et `GetHashcode` des propriétés, afin que les deux instances du même type anonyme sont égales si et uniquement si toutes leurs propriétés sont égales.</span><span class="sxs-lookup"><span data-stu-id="3d917-1388">The `Equals` and `GetHashcode` methods on anonymous types override the methods inherited from `object`, and are defined in terms of the `Equals` and `GetHashcode` of the properties, so that two instances of the same anonymous type are equal if and only if all their properties are equal.</span></span>

<span data-ttu-id="3d917-1389">Un déclarateur de membre peut être abrégé en un nom simple ([l’inférence de Type](expressions.md#type-inference)), un accès au membre ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), un accès de base ([d’accèsdeBase](expressions.md#base-access)) ou un accès aux membres conditionnels null ([des expressions de condition Null en tant qu’initialiseurs de projection](expressions.md#null-conditional-expressions-as-projection-initializers)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1389">A member declarator can be abbreviated to a simple name ([Type inference](expressions.md#type-inference)), a member access ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), a base access ([Base access](expressions.md#base-access)) or a null-conditional member access ([Null-conditional expressions as projection initializers](expressions.md#null-conditional-expressions-as-projection-initializers)).</span></span> <span data-ttu-id="3d917-1390">Il s’agit une ***initialiseurs de projection*** et est un raccourci pour une déclaration d’et l’attribution à une propriété portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="3d917-1390">This is called a ***projection initializer*** and is shorthand for a declaration of and assignment to a property with the same name.</span></span> <span data-ttu-id="3d917-1391">Plus précisément, les déclarateurs de membres des formes</span><span class="sxs-lookup"><span data-stu-id="3d917-1391">Specifically, member declarators of the forms</span></span>
```csharp
identifier
expr.identifier
```
<span data-ttu-id="3d917-1392">sont précisément équivalentes à ce qui suit, respectivement :</span><span class="sxs-lookup"><span data-stu-id="3d917-1392">are precisely equivalent to the following, respectively:</span></span>
```csharp
identifier = identifier
identifier = expr.identifier
```

<span data-ttu-id="3d917-1393">Par conséquent, dans un initialiseur de projection la *identificateur* sélectionne la valeur et le champ ou propriété à laquelle la valeur est affectée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1393">Thus, in a projection initializer the *identifier* selects both the value and the field or property to which the value is assigned.</span></span> <span data-ttu-id="3d917-1394">Intuitivement, un initialiseur de projection projette pas seulement une valeur, mais également le nom de la valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1394">Intuitively, a projection initializer projects not just a value, but also the name of the value.</span></span>

### <a name="the-typeof-operator"></a><span data-ttu-id="3d917-1395">L’opérateur typeof</span><span class="sxs-lookup"><span data-stu-id="3d917-1395">The typeof operator</span></span>

<span data-ttu-id="3d917-1396">Le `typeof` opérateur permet d’obtenir le `System.Type` objet pour un type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1396">The `typeof` operator is used to obtain the `System.Type` object for a type.</span></span>

```antlr
typeof_expression
    : 'typeof' '(' type ')'
    | 'typeof' '(' unbound_type_name ')'
    | 'typeof' '(' 'void' ')'
    ;

unbound_type_name
    : identifier generic_dimension_specifier?
    | identifier '::' identifier generic_dimension_specifier?
    | unbound_type_name '.' identifier generic_dimension_specifier?
    ;

generic_dimension_specifier
    : '<' comma* '>'
    ;

comma
    : ','
    ;
```

<span data-ttu-id="3d917-1397">La première forme de *typeof_expression* se compose d’un `typeof` mot clé suivi par un entre parenthèses *type*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1397">The first form of *typeof_expression* consists of a `typeof` keyword followed by a parenthesized *type*.</span></span> <span data-ttu-id="3d917-1398">Le résultat d’une expression de cette forme est la `System.Type` objet pour le type indiqué.</span><span class="sxs-lookup"><span data-stu-id="3d917-1398">The result of an expression of this form is the `System.Type` object for the indicated type.</span></span> <span data-ttu-id="3d917-1399">Il existe un seul `System.Type` objet pour un type donné.</span><span class="sxs-lookup"><span data-stu-id="3d917-1399">There is only one `System.Type` object for any given type.</span></span> <span data-ttu-id="3d917-1400">Cela signifie que pour un type `T`, `typeof(T) == typeof(T)` est toujours true.</span><span class="sxs-lookup"><span data-stu-id="3d917-1400">This means that for a type `T`, `typeof(T) == typeof(T)` is always true.</span></span> <span data-ttu-id="3d917-1401">Le *type* ne peut pas être `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1401">The *type* cannot be `dynamic`.</span></span>

<span data-ttu-id="3d917-1402">La deuxième forme de *typeof_expression* se compose d’un `typeof` mot clé suivi par un entre parenthèses *unbound_type_name*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1402">The second form of *typeof_expression* consists of a `typeof` keyword followed by a parenthesized *unbound_type_name*.</span></span> <span data-ttu-id="3d917-1403">Un *unbound_type_name* est très similaire à un *type_name* ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)), sauf qu’un *unbound_type_name* contient *generic_dimension_specifier*s où une *type_name* contient *type_argument_list*s.</span><span class="sxs-lookup"><span data-stu-id="3d917-1403">An *unbound_type_name* is very similar to a *type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)) except that an *unbound_type_name* contains *generic_dimension_specifier*s where a *type_name* contains *type_argument_list*s.</span></span> <span data-ttu-id="3d917-1404">Lorsque l’opérande d’un *typeof_expression* est une séquence de jetons qui satisfait les grammaires des deux *unbound_type_name* et *type_name*, à savoir lorsqu’il contient ni un *generic_dimension_specifier* ni un *type_argument_list*, la séquence de jetons est considéré comme un *type_name*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1404">When the operand of a *typeof_expression* is a sequence of tokens that satisfies the grammars of both *unbound_type_name* and *type_name*, namely when it contains neither a *generic_dimension_specifier* nor a *type_argument_list*, the sequence of tokens is considered to be a *type_name*.</span></span> <span data-ttu-id="3d917-1405">La signification d’un *unbound_type_name* est déterminée comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-1405">The meaning of an *unbound_type_name* is determined as follows:</span></span>

*  <span data-ttu-id="3d917-1406">Convertir la séquence de jetons à un *type_name* en remplaçant chaque *generic_dimension_specifier* avec un *type_argument_list* ayant le même nombre de virgules et le mot clé `object` que chaque *type_argument*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1406">Convert the sequence of tokens to a *type_name* by replacing each *generic_dimension_specifier* with a *type_argument_list* having the same number of commas and the keyword `object` as each *type_argument*.</span></span>
*  <span data-ttu-id="3d917-1407">Évaluer résultant *type_name*, tout en ignorant toutes les contraintes de paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1407">Evaluate the resulting *type_name*, while ignoring all type parameter constraints.</span></span>
*  <span data-ttu-id="3d917-1408">Le *unbound_type_name* correspond au type générique indépendant associé avec le type construit résultant ([liés et indépendants des types](types.md#bound-and-unbound-types)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1408">The *unbound_type_name* resolves to the unbound generic type associated with the resulting constructed type ([Bound and unbound types](types.md#bound-and-unbound-types)).</span></span>

<span data-ttu-id="3d917-1409">Le résultat de la *typeof_expression* est le `System.Type` objet résultant indépendants de type générique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1409">The result of the *typeof_expression* is the `System.Type` object for the resulting unbound generic type.</span></span>

<span data-ttu-id="3d917-1410">La troisième forme de *typeof_expression* se compose d’un `typeof` mot clé suivi par un entre parenthèses `void` mot clé.</span><span class="sxs-lookup"><span data-stu-id="3d917-1410">The third form of *typeof_expression* consists of a `typeof` keyword followed by a parenthesized `void` keyword.</span></span> <span data-ttu-id="3d917-1411">Le résultat d’une expression de cette forme est la `System.Type` objet qui représente l’absence d’un type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1411">The result of an expression of this form is the `System.Type` object that represents the absence of a type.</span></span> <span data-ttu-id="3d917-1412">L’objet de type retourné par `typeof(void)` est distincte de l’objet de type retourné pour n’importe quel type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1412">The type object returned by `typeof(void)` is distinct from the type object returned for any type.</span></span> <span data-ttu-id="3d917-1413">Cet objet de type spécial est utile dans les bibliothèques de classes qui permettent la réflexion sur des méthodes dans le langage, lorsque ces méthodes souhaitent disposer d’un moyen pour représenter le type de retour de toute méthode, y compris les méthodes void, avec une instance de `System.Type`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1413">This special type object is useful in class libraries that allow reflection onto methods in the language, where those methods wish to have a way to represent the return type of any method, including void methods, with an instance of `System.Type`.</span></span>

<span data-ttu-id="3d917-1414">Le `typeof` opérateur peut être utilisé sur un paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1414">The `typeof` operator can be used on a type parameter.</span></span> <span data-ttu-id="3d917-1415">Le résultat est le `System.Type` objet pour le type d’exécution qui a été lié au paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1415">The result is the `System.Type` object for the run-time type that was bound to the type parameter.</span></span> <span data-ttu-id="3d917-1416">Le `typeof` opérateur peut également être utilisé sur un type construit ou un type générique indépendant ([liés et indépendants des types](types.md#bound-and-unbound-types)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1416">The `typeof` operator can also be used on a constructed type or an unbound generic type ([Bound and unbound types](types.md#bound-and-unbound-types)).</span></span> <span data-ttu-id="3d917-1417">Le `System.Type` de l’objet pour un type générique indépendant n’est pas le même que le `System.Type` objet du type d’instance.</span><span class="sxs-lookup"><span data-stu-id="3d917-1417">The `System.Type` object for an unbound generic type is not the same as the `System.Type` object of the instance type.</span></span> <span data-ttu-id="3d917-1418">Le type d’instance est toujours un type construit fermé au moment de l’exécution pour son `System.Type` dépend de l’objet sur les arguments de type au moment de l’exécution en cours d’utilisation, tandis que le type générique indépendant ne comporte aucun argument de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1418">The instance type is always a closed constructed type at run-time so its `System.Type` object depends on the run-time type arguments in use, while the unbound generic type has no type arguments.</span></span>

<span data-ttu-id="3d917-1419">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-1419">The example</span></span>
```csharp
using System;

class X<T>
{
    public static void PrintTypes() {
        Type[] t = {
            typeof(int),
            typeof(System.Int32),
            typeof(string),
            typeof(double[]),
            typeof(void),
            typeof(T),
            typeof(X<T>),
            typeof(X<X<T>>),
            typeof(X<>)
        };
        for (int i = 0; i < t.Length; i++) {
            Console.WriteLine(t[i]);
        }
    }
}

class Test
{
    static void Main() {
        X<int>.PrintTypes();
    }
}
```
<span data-ttu-id="3d917-1420">génère la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="3d917-1420">produces the following output:</span></span>
```
System.Int32
System.Int32
System.String
System.Double[]
System.Void
System.Int32
X`1[System.Int32]
X`1[X`1[System.Int32]]
X`1[T]
```

<span data-ttu-id="3d917-1421">Notez que `int` et `System.Int32` sont du même type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1421">Note that `int` and `System.Int32` are the same type.</span></span>

<span data-ttu-id="3d917-1422">Notez également que le résultat de `typeof(X<>)` ne dépend pas de l’argument de type, mais le résultat de `typeof(X<T>)` est.</span><span class="sxs-lookup"><span data-stu-id="3d917-1422">Also note that the result of `typeof(X<>)` does not depend on the type argument but the result of `typeof(X<T>)` does.</span></span>

### <a name="the-checked-and-unchecked-operators"></a><span data-ttu-id="3d917-1423">Opérateurs vérifiés et non vérifiés</span><span class="sxs-lookup"><span data-stu-id="3d917-1423">The checked and unchecked operators</span></span>

<span data-ttu-id="3d917-1424">Le `checked` et `unchecked` opérateurs sont utilisés pour contrôler la ***contexte de vérification de dépassement de capacité*** pour les conversions et les opérations arithmétiques de type intégral.</span><span class="sxs-lookup"><span data-stu-id="3d917-1424">The `checked` and `unchecked` operators are used to control the ***overflow checking context*** for integral-type arithmetic operations and conversions.</span></span>

```antlr
checked_expression
    : 'checked' '(' expression ')'
    ;

unchecked_expression
    : 'unchecked' '(' expression ')'
    ;
```

<span data-ttu-id="3d917-1425">Le `checked` opérateur évalue l’expression contenue dans un contexte vérifié et le `unchecked` opérateur évalue l’expression contenue dans un contexte non vérifié.</span><span class="sxs-lookup"><span data-stu-id="3d917-1425">The `checked` operator evaluates the contained expression in a checked context, and the `unchecked` operator evaluates the contained expression in an unchecked context.</span></span> <span data-ttu-id="3d917-1426">Un *checked_expression* ou *unchecked_expression* correspond exactement à un *parenthesized_expression* ([expressions entre parenthèses](expressions.md#parenthesized-expressions)), sauf que l’expression de relation contenant-contenue est évaluée dans le contexte de contrôle de dépassement donné.</span><span class="sxs-lookup"><span data-stu-id="3d917-1426">A *checked_expression* or *unchecked_expression* corresponds exactly to a *parenthesized_expression* ([Parenthesized expressions](expressions.md#parenthesized-expressions)), except that the contained expression is evaluated in the given overflow checking context.</span></span>

<span data-ttu-id="3d917-1427">Le contexte de vérification de dépassement de capacité peut également être contrôlé via la `checked` et `unchecked` instructions ([les instructions checked et unchecked](statements.md#the-checked-and-unchecked-statements)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1427">The overflow checking context can also be controlled through the `checked` and `unchecked` statements ([The checked and unchecked statements](statements.md#the-checked-and-unchecked-statements)).</span></span>

<span data-ttu-id="3d917-1428">Les opérations suivantes sont affectées par le dépassement de la vérification du contexte établi par le `checked` et `unchecked` opérateurs et instructions :</span><span class="sxs-lookup"><span data-stu-id="3d917-1428">The following operations are affected by the overflow checking context established by the `checked` and `unchecked` operators and statements:</span></span>

*  <span data-ttu-id="3d917-1429">Prédéfinis `++` et `--` opérateurs unaires ([d’incrémentation et décrémentation opérateurs](expressions.md#postfix-increment-and-decrement-operators) et [Incrément préfixé et opérateurs de décrémentation](expressions.md#prefix-increment-and-decrement-operators)), lorsque l’opérande est d’un type intégral type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1429">The predefined `++` and `--` unary operators ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators) and [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)), when the operand is of an integral type.</span></span>
*  <span data-ttu-id="3d917-1430">Prédéfinis `-` opérateur unaire ([opérateur moins unaire](expressions.md#unary-minus-operator)), lorsque l’opérande est de type intégral.</span><span class="sxs-lookup"><span data-stu-id="3d917-1430">The predefined `-` unary operator ([Unary minus operator](expressions.md#unary-minus-operator)), when the operand is of an integral type.</span></span>
*  <span data-ttu-id="3d917-1431">Prédéfinis `+`, `-`, `*`, et `/` opérateurs binaires ([opérateurs arithmétiques](expressions.md#arithmetic-operators)), lorsque les deux opérandes sont des types intégraux.</span><span class="sxs-lookup"><span data-stu-id="3d917-1431">The predefined `+`, `-`, `*`, and `/` binary operators ([Arithmetic operators](expressions.md#arithmetic-operators)), when both operands are of integral types.</span></span>
*  <span data-ttu-id="3d917-1432">Conversions numériques explicites ([conversions numériques explicites](conversions.md#explicit-numeric-conversions)) à partir d’un type intégral vers un autre type intégral, ou de `float` ou `double` vers un type intégral.</span><span class="sxs-lookup"><span data-stu-id="3d917-1432">Explicit numeric conversions ([Explicit numeric conversions](conversions.md#explicit-numeric-conversions)) from one integral type to another integral type, or from `float` or `double` to an integral type.</span></span>

<span data-ttu-id="3d917-1433">Lorsque l’une des opérations ci-dessus produit un résultat qui est trop grand pour représenter le type de destination, le contexte dans lequel l’opération est effectuée contrôle le comportement résultant :</span><span class="sxs-lookup"><span data-stu-id="3d917-1433">When one of the above operations produce a result that is too large to represent in the destination type, the context in which the operation is performed controls the resulting behavior:</span></span>

*  <span data-ttu-id="3d917-1434">Dans un `checked` contexte, si l’opération est une expression constante ([expressions constantes](expressions.md#constant-expressions)), une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1434">In a `checked` context, if the operation is a constant expression ([Constant expressions](expressions.md#constant-expressions)), a compile-time error occurs.</span></span> <span data-ttu-id="3d917-1435">Sinon, lorsque l’opération est effectuée au moment de l’exécution, un `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1435">Otherwise, when the operation is performed at run-time, a `System.OverflowException` is thrown.</span></span>
*  <span data-ttu-id="3d917-1436">Dans un `unchecked` contexte, le résultat est tronqué en supprimant tous les bits de poids fort qui ne tiennent pas dans le type de destination.</span><span class="sxs-lookup"><span data-stu-id="3d917-1436">In an `unchecked` context, the result is truncated by discarding any high-order bits that do not fit in the destination type.</span></span>

<span data-ttu-id="3d917-1437">Pour les expressions non constantes (expressions sont évaluées au moment de l’exécution) qui ne sont pas entourées de `checked` ou `unchecked` opérateurs ou instructions, le contexte de contrôle de dépassement de par défaut est `unchecked` , sauf si des facteurs externes (tels que le compilateur commutateurs et configuration de l’environnement d’exécution) appellent pour `checked` évaluation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1437">For non-constant expressions (expressions that are evaluated at run-time) that are not enclosed by any `checked` or `unchecked` operators or statements, the default overflow checking context is `unchecked` unless external factors (such as compiler switches and execution environment configuration) call for `checked` evaluation.</span></span>

<span data-ttu-id="3d917-1438">Pour les expressions constantes (expressions qui peuvent être complètement évaluées au moment de la compilation), le dépassement de capacité par défaut la vérification du contexte est toujours `checked`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1438">For constant expressions (expressions that can be fully evaluated at compile-time), the default overflow checking context is always `checked`.</span></span> <span data-ttu-id="3d917-1439">Sauf si une expression constante est explicitement placée dans un `unchecked` contexte, les dépassements de capacité qui se produisent pendant l’évaluation de la compilation de l’expression toujours entraînent des erreurs de compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1439">Unless a constant expression is explicitly placed in an `unchecked` context, overflows that occur during the compile-time evaluation of the expression always cause compile-time errors.</span></span>

<span data-ttu-id="3d917-1440">Le corps d’une fonction anonyme n’est pas affecté par `checked` ou `unchecked` contextes dans lesquels la fonction anonyme se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1440">The body of an anonymous function is not affected by `checked` or `unchecked` contexts in which the anonymous function occurs.</span></span>

<span data-ttu-id="3d917-1441">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-1441">In the example</span></span>
```csharp
class Test
{
    static readonly int x = 1000000;
    static readonly int y = 1000000;

    static int F() {
        return checked(x * y);      // Throws OverflowException
    }

    static int G() {
        return unchecked(x * y);    // Returns -727379968
    }

    static int H() {
        return x * y;               // Depends on default
    }
}
```
<span data-ttu-id="3d917-1442">aucune erreur de compilation n’est signalées dans la mesure où aucune des expressions pouvant être évaluées au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1442">no compile-time errors are reported since neither of the expressions can be evaluated at compile-time.</span></span> <span data-ttu-id="3d917-1443">Au moment de l’exécution, le `F` méthode lève un `System.OverflowException`et le `G` méthode retourne-727379968 (les 32 bits du résultat out-of-range).</span><span class="sxs-lookup"><span data-stu-id="3d917-1443">At run-time, the `F` method throws a `System.OverflowException`, and the `G` method returns -727379968 (the lower 32 bits of the out-of-range result).</span></span> <span data-ttu-id="3d917-1444">Le comportement de la `H` méthode varie selon le contexte pour la compilation de contrôle de dépassement par défaut, mais il est identique à celui `F` ou identique à `G`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1444">The behavior of the `H` method depends on the default overflow checking context for the compilation, but it is either the same as `F` or the same as `G`.</span></span>

<span data-ttu-id="3d917-1445">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-1445">In the example</span></span>
```csharp
class Test
{
    const int x = 1000000;
    const int y = 1000000;

    static int F() {
        return checked(x * y);      // Compile error, overflow
    }

    static int G() {
        return unchecked(x * y);    // Returns -727379968
    }

    static int H() {
        return x * y;               // Compile error, overflow
    }
}
```
<span data-ttu-id="3d917-1446">le dépassement de capacité qui se produire lors de l’évaluation des expressions de constante dans `F` et `H` provoquer des erreurs de compilation, car les expressions sont évaluées dans un `checked` contexte.</span><span class="sxs-lookup"><span data-stu-id="3d917-1446">the overflows that occur when evaluating the constant expressions in `F` and `H` cause compile-time errors to be reported because the expressions are evaluated in a `checked` context.</span></span> <span data-ttu-id="3d917-1447">Un dépassement de capacité se produit également lors de l’évaluation de l’expression constante dans `G`, mais étant donné que la version d’évaluation a lieu un `unchecked` contexte, le dépassement de capacité n’est pas signalée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1447">An overflow also occurs when evaluating the constant expression in `G`, but since the evaluation takes place in an `unchecked` context, the overflow is not reported.</span></span>

<span data-ttu-id="3d917-1448">Le `checked` et `unchecked` opérateurs affectent uniquement le dépassement de la vérification de contexte pour les opérations qui sont contenus textuellement dans le «`(`« et »`)`« jetons.</span><span class="sxs-lookup"><span data-stu-id="3d917-1448">The `checked` and `unchecked` operators only affect the overflow checking context for those operations that are textually contained within the "`(`" and "`)`" tokens.</span></span> <span data-ttu-id="3d917-1449">Les opérateurs n’ont aucun effet sur les fonctions membres qui sont appelés en évaluant l’expression de relation contenant-contenue.</span><span class="sxs-lookup"><span data-stu-id="3d917-1449">The operators have no effect on function members that are invoked as a result of evaluating the contained expression.</span></span> <span data-ttu-id="3d917-1450">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-1450">In the example</span></span>
```csharp
class Test
{
    static int Multiply(int x, int y) {
        return x * y;
    }

    static int F() {
        return checked(Multiply(1000000, 1000000));
    }
}
```
<span data-ttu-id="3d917-1451">l’utilisation de `checked` dans `F` n’affecte pas la version d’évaluation de `x * y` dans `Multiply`, de sorte que `x * y` est évaluée dans le contexte de contrôle de dépassement par défaut.</span><span class="sxs-lookup"><span data-stu-id="3d917-1451">the use of `checked` in `F` does not affect the evaluation of `x * y` in `Multiply`, so `x * y` is evaluated in the default overflow checking context.</span></span>

<span data-ttu-id="3d917-1452">Le `unchecked` opérateur est pratique lorsque vous écrivez des constantes des types intégraux signés en notation hexadécimale.</span><span class="sxs-lookup"><span data-stu-id="3d917-1452">The `unchecked` operator is convenient when writing constants of the signed integral types in hexadecimal notation.</span></span> <span data-ttu-id="3d917-1453">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3d917-1453">For example:</span></span>
```csharp
class Test
{
    public const int AllBits = unchecked((int)0xFFFFFFFF);

    public const int HighBit = unchecked((int)0x80000000);
}
```

<span data-ttu-id="3d917-1454">Les deux constantes hexadécimales ci-dessus sont de type `uint`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1454">Both of the hexadecimal constants above are of type `uint`.</span></span> <span data-ttu-id="3d917-1455">Étant donné que les constantes sont en dehors de la `int` de plage, sans le `unchecked` opérateur, les casts à `int` produiraient des erreurs de compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1455">Because the constants are outside the `int` range, without the `unchecked` operator, the casts to `int` would produce compile-time errors.</span></span>

<span data-ttu-id="3d917-1456">Le `checked` et `unchecked` opérateurs et instructions permettent aux programmeurs de contrôler certains aspects des calculs numériques.</span><span class="sxs-lookup"><span data-stu-id="3d917-1456">The `checked` and `unchecked` operators and statements allow programmers to control certain aspects of some numeric calculations.</span></span> <span data-ttu-id="3d917-1457">Toutefois, le comportement de certains opérateurs numériques dépend des types de données de leurs opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-1457">However, the behavior of some numeric operators depends on their operands' data types.</span></span> <span data-ttu-id="3d917-1458">Par exemple, toujours la multiplication de deux décimales génère une exception de dépassement de capacité même dans une explicitement `unchecked` construire.</span><span class="sxs-lookup"><span data-stu-id="3d917-1458">For example, multiplying two decimals always results in an exception on overflow even within an explicitly `unchecked` construct.</span></span> <span data-ttu-id="3d917-1459">De même, la multiplication de deux flotte jamais résultats dans une exception de dépassement de capacité même dans une explicitement `checked` construire.</span><span class="sxs-lookup"><span data-stu-id="3d917-1459">Similarly, multiplying two floats never results in an exception on overflow even within an explicitly `checked` construct.</span></span> <span data-ttu-id="3d917-1460">En outre, les autres opérateurs ne sont jamais affectés par le mode de contrôle, si la valeur par défaut ou explicite.</span><span class="sxs-lookup"><span data-stu-id="3d917-1460">In addition, other operators are never affected by the mode of checking, whether default or explicit.</span></span>

### <a name="default-value-expressions"></a><span data-ttu-id="3d917-1461">Expressions de valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="3d917-1461">Default value expressions</span></span>

<span data-ttu-id="3d917-1462">Une expression de valeur par défaut est utilisée pour obtenir la valeur par défaut ([valeurs par défaut](variables.md#default-values)) d’un type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1462">A default value expression is used to obtain the default value ([Default values](variables.md#default-values)) of a type.</span></span> <span data-ttu-id="3d917-1463">En général, une expression de valeur par défaut est utilisée pour les paramètres de type, car il ne peut pas connue si le paramètre de type est un type valeur ou un type référence.</span><span class="sxs-lookup"><span data-stu-id="3d917-1463">Typically a default value expression is used for type parameters, since it may not be known if the type parameter is a value type or a reference type.</span></span> <span data-ttu-id="3d917-1464">(Il n’existe aucune conversion à partir de la `null` littéral à un paramètre de type, sauf si le paramètre de type est connu pour être un type référence.)</span><span class="sxs-lookup"><span data-stu-id="3d917-1464">(No conversion exists from the `null` literal to a type parameter unless the type parameter is known to be a reference type.)</span></span>

```antlr
default_value_expression
    : 'default' '(' type ')'
    ;
```

<span data-ttu-id="3d917-1465">Si le *type* dans un *default_value_expression* prend la valeur au moment de l’exécution à un type référence, le résultat est `null` convertie en ce type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1465">If the *type* in a *default_value_expression* evaluates at run-time to a reference type, the result is `null` converted to that type.</span></span> <span data-ttu-id="3d917-1466">Si le *type* dans un *default_value_expression* prend la valeur au moment de l’exécution à un type valeur, le résultat est le *value_type*de valeur par défaut ([par défaut constructeurs](types.md#default-constructors)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1466">If the *type* in a *default_value_expression* evaluates at run-time to a value type, the result is the *value_type*'s default value ([Default constructors](types.md#default-constructors)).</span></span>

<span data-ttu-id="3d917-1467">Un *default_value_expression* est une expression constante ([expressions constantes](expressions.md#constant-expressions)) si le type est un type référence ou un paramètre de type est connu pour être un type référence ([paramètre de Type contraintes](classes.md#type-parameter-constraints)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1467">A *default_value_expression* is a constant expression ([Constant expressions](expressions.md#constant-expressions)) if the type is a reference type or a type parameter that is known to be a reference type ([Type parameter constraints](classes.md#type-parameter-constraints)).</span></span> <span data-ttu-id="3d917-1468">En outre, un *default_value_expression* est une expression constante si le type est un des types de valeurs suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, ou tout type d’énumération.</span><span class="sxs-lookup"><span data-stu-id="3d917-1468">In addition, a *default_value_expression* is a constant expression if the type is one of the following value types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, or any enumeration type.</span></span>


### <a name="nameof-expressions"></a><span data-ttu-id="3d917-1469">Expressions Nameof</span><span class="sxs-lookup"><span data-stu-id="3d917-1469">Nameof expressions</span></span>

<span data-ttu-id="3d917-1470">Un *nameof_expression* est utilisé pour obtenir le nom d’une entité de programme sous forme de chaîne constante.</span><span class="sxs-lookup"><span data-stu-id="3d917-1470">A *nameof_expression* is used to obtain the name of a program entity as a constant string.</span></span>

```antlr
nameof_expression
    : 'nameof' '(' named_entity ')'
    ;

named_entity
    : simple_name
    | named_entity_target '.' identifier type_argument_list?
    ;

named_entity_target
    : 'this'
    | 'base'
    | named_entity 
    | predefined_type 
    | qualified_alias_member
    ;
```

<span data-ttu-id="3d917-1471">Grammaticalement parlant, les *named_entity* opérande est toujours une expression.</span><span class="sxs-lookup"><span data-stu-id="3d917-1471">Grammatically speaking, the *named_entity* operand is always an expression.</span></span> <span data-ttu-id="3d917-1472">Étant donné que `nameof` n’est pas un mot clé réservé, une expression nameof est toujours syntaxiquement ambiguë dans un appel de nom simple `nameof`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1472">Because `nameof` is not a reserved keyword, a nameof expression is always syntactically ambiguous with an invocation of the simple name `nameof`.</span></span> <span data-ttu-id="3d917-1473">Pour des raisons de compatibilité, si une recherche de nom ([noms simples](expressions.md#simple-names)) du nom `nameof` réussit, l’expression est traitée comme un *invocation_expression* --que si l’appel soit juridique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1473">For compatibility reasons, if a name lookup ([Simple names](expressions.md#simple-names)) of the name `nameof` succeeds, the expression is treated as an *invocation_expression* -- regardless of whether the invocation is legal.</span></span> <span data-ttu-id="3d917-1474">Sinon, il est un *nameof_expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1474">Otherwise it is a *nameof_expression*.</span></span>

<span data-ttu-id="3d917-1475">La signification de la *named_entity* d’un *nameof_expression* est la signification de celle-ci en tant qu’expression ; autrement dit, soit en tant qu’un *simple_name*, un *base_access*  ou un *member_access*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1475">The meaning of the *named_entity* of a *nameof_expression* is the meaning of it as an expression; that is, either as a *simple_name*, a *base_access* or a *member_access*.</span></span> <span data-ttu-id="3d917-1476">Toutefois, dans lequel la recherche est décrit dans [noms simples](expressions.md#simple-names) et [l’accès au membre](expressions.md#member-access) génère une erreur, car un membre d’instance a été trouvé dans un contexte statique, un *nameof_expression*ne produit aucune erreur de ce type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1476">However, where the lookup described in [Simple names](expressions.md#simple-names) and [Member access](expressions.md#member-access) results in an error because an instance member was found in a static context, a *nameof_expression* produces no such error.</span></span>

<span data-ttu-id="3d917-1477">Il s’agit d’une erreur lors de la compilation pour un *named_entity* désignant un groupe de méthodes pour avoir un *type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1477">It is a compile-time error for a *named_entity* designating a method group to have a *type_argument_list*.</span></span> <span data-ttu-id="3d917-1478">Il est une erreur de compilation pour un *named_entity_target* comme ayant le type `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1478">It is a compile time error for a *named_entity_target* to have the type `dynamic`.</span></span>

<span data-ttu-id="3d917-1479">Un *nameof_expression* est une expression constante de type `string`, et n’a aucun effet lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="3d917-1479">A *nameof_expression* is a constant expression of type `string`, and has no effect at runtime.</span></span> <span data-ttu-id="3d917-1480">Plus précisément, sa *named_entity* n’est pas évaluée et est ignoré dans le cadre de l’analyse d’assignation ([règles générales pour les expressions simples](variables.md#general-rules-for-simple-expressions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1480">Specifically, its *named_entity* is not evaluated, and is ignored for the purposes of definite assignment analysis ([General rules for simple expressions](variables.md#general-rules-for-simple-expressions)).</span></span> <span data-ttu-id="3d917-1481">Sa valeur est le dernier identificateur de la *named_entity* avant la dernière facultative *type_argument_list*, transformées de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="3d917-1481">Its value is the last identifier of the *named_entity* before the optional final *type_argument_list*, transformed in the following way:</span></span>

* <span data-ttu-id="3d917-1482">Le préfixe «`@`», le cas échéant, est supprimé.</span><span class="sxs-lookup"><span data-stu-id="3d917-1482">The prefix "`@`", if used, is removed.</span></span>
* <span data-ttu-id="3d917-1483">Chaque *unicode_escape_sequence* est transformée en son caractère Unicode correspondant.</span><span class="sxs-lookup"><span data-stu-id="3d917-1483">Each *unicode_escape_sequence* is transformed into its corresponding Unicode character.</span></span>
* <span data-ttu-id="3d917-1484">N’importe quel *formatting_characters* sont supprimés.</span><span class="sxs-lookup"><span data-stu-id="3d917-1484">Any *formatting_characters* are removed.</span></span>

<span data-ttu-id="3d917-1485">Ce sont les mêmes transformations appliquées dans [identificateurs](lexical-structure.md#identifiers) lors du test d’égalité entre les identificateurs.</span><span class="sxs-lookup"><span data-stu-id="3d917-1485">These are the same transformations applied in [Identifiers](lexical-structure.md#identifiers) when testing equality between identifiers.</span></span>

<span data-ttu-id="3d917-1486">TODO : exemples</span><span class="sxs-lookup"><span data-stu-id="3d917-1486">TODO: examples</span></span>

### <a name="anonymous-method-expressions"></a><span data-ttu-id="3d917-1487">Expressions de méthode anonyme</span><span class="sxs-lookup"><span data-stu-id="3d917-1487">Anonymous method expressions</span></span>

<span data-ttu-id="3d917-1488">Un *anonymous_method_expression* est une des deux façons de définir une fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="3d917-1488">An *anonymous_method_expression* is one of two ways of defining an anonymous function.</span></span> <span data-ttu-id="3d917-1489">Celles-ci sont décrites en détail dans [expressions de fonction anonyme](expressions.md#anonymous-function-expressions).</span><span class="sxs-lookup"><span data-stu-id="3d917-1489">These are further described in [Anonymous function expressions](expressions.md#anonymous-function-expressions).</span></span>

## <a name="unary-operators"></a><span data-ttu-id="3d917-1490">Les opérateurs unaires.</span><span class="sxs-lookup"><span data-stu-id="3d917-1490">Unary operators</span></span>

<span data-ttu-id="3d917-1491">Le `?`, `+`, `-`, `!`, `~`, `++`, `--`, effectuez un cast, et `await` sont appelés les opérateurs unaires.</span><span class="sxs-lookup"><span data-stu-id="3d917-1491">The `?`, `+`, `-`, `!`, `~`, `++`, `--`, cast, and `await` operators are called the unary operators.</span></span>

```antlr
unary_expression
    : primary_expression
    | null_conditional_expression
    | '+' unary_expression
    | '-' unary_expression
    | '!' unary_expression
    | '~' unary_expression
    | pre_increment_expression
    | pre_decrement_expression
    | cast_expression
    | await_expression
    | unary_expression_unsafe
    ;
```

<span data-ttu-id="3d917-1492">Si l’opérande d’un *unary_expression* a le type de compilation `dynamic`, elle est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1492">If the operand of a *unary_expression* has the compile-time type `dynamic`, it is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="3d917-1493">Dans ce cas de type de la compilation de la *unary_expression* est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution à l’aide du type au moment de l’exécution de l’opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-1493">In this case the compile-time type of the *unary_expression* is `dynamic`, and the resolution described below will take place at run-time using the run-time type of the operand.</span></span>

### <a name="null-conditional-operator"></a><span data-ttu-id="3d917-1494">Opérateur conditionnel null</span><span class="sxs-lookup"><span data-stu-id="3d917-1494">Null-conditional operator</span></span>

<span data-ttu-id="3d917-1495">L’opérateur conditionnel null s’applique à une liste des opérations à son opérande uniquement si l’opérande est non null.</span><span class="sxs-lookup"><span data-stu-id="3d917-1495">The null-conditional operator applies a list of operations to its operand only if that operand is non-null.</span></span> <span data-ttu-id="3d917-1496">Sinon, le résultat de l’application de l’opérateur est `null`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1496">Otherwise the result of applying the operator is `null`.</span></span>

```antlr
null_conditional_expression
    : primary_expression null_conditional_operations
    ;

null_conditional_operations
    : null_conditional_operations? '?' '.' identifier type_argument_list?
    | null_conditional_operations? '?' '[' argument_list ']'
    | null_conditional_operations '.' identifier type_argument_list?
    | null_conditional_operations '[' argument_list ']'
    | null_conditional_operations '(' argument_list? ')'
    ;
```

<span data-ttu-id="3d917-1497">La liste des opérations peut inclure l’accès au membre et les opérations d’accès élément (qui peuvent se trouver conditionnels null), ainsi que les appel.</span><span class="sxs-lookup"><span data-stu-id="3d917-1497">The list of operations can include member access and element access operations (which may themselves be null-conditional), as well as invocation.</span></span>

<span data-ttu-id="3d917-1498">Par exemple, l’expression `a.b?[0]?.c()` est un *null_conditional_expression* avec un *primary_expression* `a.b` et *null_conditional_operations* `?[0]` (accès de l’élément de condition null), `?.c` (accès membres conditionnels null) et `()` (appel).</span><span class="sxs-lookup"><span data-stu-id="3d917-1498">For example, the expression `a.b?[0]?.c()` is a *null_conditional_expression* with a *primary_expression* `a.b` and *null_conditional_operations* `?[0]` (null-conditional element access), `?.c` (null-conditional member access) and `()` (invocation).</span></span>

<span data-ttu-id="3d917-1499">Pour un *null_conditional_expression* `E` avec un *primary_expression* `P`, let `E0` être l’expression obtenue en supprimant textuellement l’interligne `?`à partir de chaque le *null_conditional_operations* de `E` qui ont un.</span><span class="sxs-lookup"><span data-stu-id="3d917-1499">For a *null_conditional_expression* `E` with a *primary_expression* `P`, let `E0` be the expression obtained by textually removing the leading `?` from each of the *null_conditional_operations* of `E` that have one.</span></span> <span data-ttu-id="3d917-1500">Conceptuellement, `E0` est l’expression sera évaluée si aucune des vérifications null représenté par le `?`s trouvez un `null`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1500">Conceptually, `E0` is the expression that will be evaluated if none of the null checks represented by the `?`s do find a `null`.</span></span>

<span data-ttu-id="3d917-1501">Aussi, laisser `E1` être l’expression obtenue en supprimant textuellement le caractère de début `?` de simplement le premier de la *null_conditional_operations* dans `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1501">Also, let `E1` be the expression obtained by textually removing the leading `?` from just the first of the *null_conditional_operations* in `E`.</span></span> <span data-ttu-id="3d917-1502">Cela peut conduire à une *expression primaire* (cas échéant simplement `?`) ou à un autre *null_conditional_expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1502">This may lead to a *primary-expression* (if there was just one `?`) or to another *null_conditional_expression*.</span></span>

<span data-ttu-id="3d917-1503">Par exemple, si `E` est l’expression `a.b?[0]?.c()`, puis `E0` est l’expression `a.b[0].c()` et `E1` est l’expression `a.b[0]?.c()`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1503">For example, if `E` is the expression `a.b?[0]?.c()`, then `E0` is the expression `a.b[0].c()` and `E1` is the expression `a.b[0]?.c()`.</span></span>

<span data-ttu-id="3d917-1504">Si `E0` est classé comme rien, puis `E` est classé comme rien.</span><span class="sxs-lookup"><span data-stu-id="3d917-1504">If `E0` is classified as nothing, then `E` is classified as nothing.</span></span> <span data-ttu-id="3d917-1505">Sinon, E est classé en tant que valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1505">Otherwise E is classified as a value.</span></span>

<span data-ttu-id="3d917-1506">`E0` et `E1` servent à déterminer la signification de `E`:</span><span class="sxs-lookup"><span data-stu-id="3d917-1506">`E0` and `E1` are used to determine the meaning of `E`:</span></span>

*  <span data-ttu-id="3d917-1507">Si `E` se produit comme un *statement_expression* la signification de `E` est identique à l’instruction</span><span class="sxs-lookup"><span data-stu-id="3d917-1507">If `E` occurs as a *statement_expression* the meaning of `E` is the same as the statement</span></span>

   ```csharp
   if ((object)P != null) E1;
   ```

   <span data-ttu-id="3d917-1508">à ceci près que P est évaluée une seule fois.</span><span class="sxs-lookup"><span data-stu-id="3d917-1508">except that P is evaluated only once.</span></span>

*  <span data-ttu-id="3d917-1509">Sinon, si `E0` est classé en tant que rien ne se produit une erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1509">Otherwise, if `E0` is classified as nothing a compile-time error occurs.</span></span>

*  <span data-ttu-id="3d917-1510">Sinon, laissez le `T0` être du type de `E0`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1510">Otherwise, let `T0` be the type of `E0`.</span></span>

   *  <span data-ttu-id="3d917-1511">Si `T0` est un paramètre de type qui n’est pas connu pour être un type référence ou un type valeur non nullable, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1511">If `T0` is a type parameter that is not known to be a reference type or a non-nullable value type, a compile-time error occurs.</span></span>

   *  <span data-ttu-id="3d917-1512">Si `T0` est un type valeur non nullable, le type de `E` est `T0?`et la signification de `E` est identique à</span><span class="sxs-lookup"><span data-stu-id="3d917-1512">If `T0` is a non-nullable value type, then the type of `E` is `T0?`, and the meaning of `E` is the same as</span></span>

      ```csharp
      ((object)P == null) ? (T0?)null : E1
      ```

      <span data-ttu-id="3d917-1513">à ceci près que `P` est évaluée une seule fois.</span><span class="sxs-lookup"><span data-stu-id="3d917-1513">except that `P` is evaluated only once.</span></span>

   *  <span data-ttu-id="3d917-1514">Sinon, le type de E est T0, et la signification de E est identique à</span><span class="sxs-lookup"><span data-stu-id="3d917-1514">Otherwise the type of E is T0, and the meaning of E is the same as</span></span>

      ```csharp
      ((object)P == null) ? null : E1
      ```

      <span data-ttu-id="3d917-1515">à ceci près que `P` est évaluée une seule fois.</span><span class="sxs-lookup"><span data-stu-id="3d917-1515">except that `P` is evaluated only once.</span></span>

<span data-ttu-id="3d917-1516">Si `E1` est lui-même un *null_conditional_expression*, puis ces règles sont appliquées à nouveau, l’imbrication les tests pour `null` jusqu'à ce qu’il y a aucune autre `?`de, et l’expression a été réduite tout en bas à l’expression primaire `E0`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1516">If `E1` is itself a *null_conditional_expression*, then these rules are applied again, nesting the tests for `null` until there are no further `?`'s, and the expression has been reduced all the way down to the primary-expression `E0`.</span></span>

<span data-ttu-id="3d917-1517">Par exemple, si l’expression `a.b?[0]?.c()` se produit comme une expression d’instruction, comme dans l’instruction :</span><span class="sxs-lookup"><span data-stu-id="3d917-1517">For example, if the expression `a.b?[0]?.c()` occurs as a statement-expression, as in the statement:</span></span>
```csharp
a.b?[0]?.c();
```
<span data-ttu-id="3d917-1518">sa signification est équivalente à :</span><span class="sxs-lookup"><span data-stu-id="3d917-1518">its meaning is equivalent to:</span></span>
```csharp
if (a.b != null) a.b[0]?.c();
```
<span data-ttu-id="3d917-1519">qui est à nouveau équivalent à :</span><span class="sxs-lookup"><span data-stu-id="3d917-1519">which again is equivalent to:</span></span>
```csharp
if (a.b != null) if (a.b[0] != null) a.b[0].c();
```
<span data-ttu-id="3d917-1520">À ceci près que `a.b` et `a.b[0]` sont évaluées qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="3d917-1520">Except that `a.b` and `a.b[0]` are evaluated only once.</span></span>

<span data-ttu-id="3d917-1521">S’il se produit dans un contexte où sa valeur est utilisée, comme dans :</span><span class="sxs-lookup"><span data-stu-id="3d917-1521">If it occurs in a context where its value is used, as in:</span></span>
```csharp
var x = a.b?[0]?.c();
```
<span data-ttu-id="3d917-1522">et, en supposant que le type de l’appel final n’est pas un type valeur non nullable, sa signification est équivalente à :</span><span class="sxs-lookup"><span data-stu-id="3d917-1522">and assuming that the type of the final invocation is not a non-nullable value type, its meaning is equivalent to:</span></span>
```csharp
var x = (a.b == null) ? null : (a.b[0] == null) ? null : a.b[0].c();
```
<span data-ttu-id="3d917-1523">À ceci près que `a.b` et `a.b[0]` sont évaluées qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="3d917-1523">except that `a.b` and `a.b[0]` are evaluated only once.</span></span>

#### <a name="null-conditional-expressions-as-projection-initializers"></a><span data-ttu-id="3d917-1524">Expressions de condition NULL en tant qu’initialiseurs de projection</span><span class="sxs-lookup"><span data-stu-id="3d917-1524">Null-conditional expressions as projection initializers</span></span>

<span data-ttu-id="3d917-1525">Une expression de condition null est uniquement autorisée dans un *member_declarator* dans un *anonymous_object_creation_expression* ([expressions de création d’objet anonyme](expressions.md#anonymous-object-creation-expressions)) si elle se termine avec un accès de membres (éventuellement conditionnels null).</span><span class="sxs-lookup"><span data-stu-id="3d917-1525">A null-conditional expression is only allowed as a *member_declarator* in an *anonymous_object_creation_expression* ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) if it ends with an (optionally null-conditional) member access.</span></span> <span data-ttu-id="3d917-1526">Grammaticalement, cette exigence peut être exprimée en tant que :</span><span class="sxs-lookup"><span data-stu-id="3d917-1526">Grammatically, this requirement can be expressed as:</span></span>

```antlr
null_conditional_member_access
    : primary_expression null_conditional_operations? '?' '.' identifier type_argument_list?
    | primary_expression null_conditional_operations '.' identifier type_argument_list?
    ;
```

<span data-ttu-id="3d917-1527">Il s’agit d’un cas spécial de la grammaire pour *null_conditional_expression* ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="3d917-1527">This is a special case of the grammar for *null_conditional_expression* above.</span></span> <span data-ttu-id="3d917-1528">La production de *member_declarator* dans [expressions de création d’objet anonyme](expressions.md#anonymous-object-creation-expressions) puis inclut uniquement *null_conditional_member_access*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1528">The production for *member_declarator* in [Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions) then includes only *null_conditional_member_access*.</span></span>

#### <a name="null-conditional-expressions-as-statement-expressions"></a><span data-ttu-id="3d917-1529">Expressions de condition NULL en tant qu’expressions d’instruction</span><span class="sxs-lookup"><span data-stu-id="3d917-1529">Null-conditional expressions as statement expressions</span></span>

<span data-ttu-id="3d917-1530">Une expression de condition null est uniquement autorisée dans un *statement_expression* ([instructions d’Expression](statements.md#expression-statements)) si elle se termine par un appel.</span><span class="sxs-lookup"><span data-stu-id="3d917-1530">A null-conditional expression is only allowed as a *statement_expression* ([Expression statements](statements.md#expression-statements)) if it ends with an invocation.</span></span> <span data-ttu-id="3d917-1531">Grammaticalement, cette exigence peut être exprimée en tant que :</span><span class="sxs-lookup"><span data-stu-id="3d917-1531">Grammatically, this requirement can be expressed as:</span></span>

```antlr
null_conditional_invocation_expression
    : primary_expression null_conditional_operations '(' argument_list? ')'
    ;
```

<span data-ttu-id="3d917-1532">Il s’agit d’un cas spécial de la grammaire pour *null_conditional_expression* ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="3d917-1532">This is a special case of the grammar for *null_conditional_expression* above.</span></span> <span data-ttu-id="3d917-1533">La production de *statement_expression* dans [instructions d’Expression](statements.md#expression-statements) puis inclut uniquement *null_conditional_invocation_expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1533">The production for *statement_expression* in [Expression statements](statements.md#expression-statements) then includes only *null_conditional_invocation_expression*.</span></span>


### <a name="unary-plus-operator"></a><span data-ttu-id="3d917-1534">Opérateur plus unaire</span><span class="sxs-lookup"><span data-stu-id="3d917-1534">Unary plus operator</span></span>

<span data-ttu-id="3d917-1535">Pour une opération du formulaire `+x`, la résolution de surcharge opérateur unaire ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1535">For an operation of the form `+x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-1536">L’opérande est converti au type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1536">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="3d917-1537">Les opérateurs plus unaire prédéfinie sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-1537">The predefined unary plus operators are:</span></span>

```csharp
int operator +(int x);
uint operator +(uint x);
long operator +(long x);
ulong operator +(ulong x);
float operator +(float x);
double operator +(double x);
decimal operator +(decimal x);
```

<span data-ttu-id="3d917-1538">Pour chacun de ces opérateurs, le résultat est simplement la valeur de l’opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-1538">For each of these operators, the result is simply the value of the operand.</span></span>

### <a name="unary-minus-operator"></a><span data-ttu-id="3d917-1539">Opération moins unaire</span><span class="sxs-lookup"><span data-stu-id="3d917-1539">Unary minus operator</span></span>

<span data-ttu-id="3d917-1540">Pour une opération du formulaire `-x`, la résolution de surcharge opérateur unaire ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1540">For an operation of the form `-x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-1541">L’opérande est converti au type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1541">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="3d917-1542">Les opérateurs de négation prédéfinis sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-1542">The predefined negation operators are:</span></span>

*  <span data-ttu-id="3d917-1543">Négation d’entier :</span><span class="sxs-lookup"><span data-stu-id="3d917-1543">Integer negation:</span></span>

   ```csharp
   int operator -(int x);
   long operator -(long x);
   ```

   <span data-ttu-id="3d917-1544">Le résultat est calculé en soustrayant `x` à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="3d917-1544">The result is computed by subtracting `x` from zero.</span></span> <span data-ttu-id="3d917-1545">Si la valeur de `x` est la plus petite valeur représentable du type d’opérande (-2 ^ 31 pour `int` ou -2 ^ 63 pour `long`), puis la négation mathématique de `x` n’est pas représentable dans le type d’opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-1545">If the value of `x` is the smallest representable value of the operand type (-2^31 for `int` or -2^63 for `long`), then the mathematical negation of `x` is not representable within the operand type.</span></span> <span data-ttu-id="3d917-1546">Si cela se produit dans un `checked` contexte, un `System.OverflowException` est levée ; si elle se produit dans un `unchecked` contexte, le résultat est la valeur de l’opérande et le dépassement de capacité n’est pas signalée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1546">If this occurs within a `checked` context, a `System.OverflowException` is thrown; if it occurs within an `unchecked` context, the result is the value of the operand and the overflow is not reported.</span></span>

   <span data-ttu-id="3d917-1547">Si l’opérande de l’opérateur de négation est de type `uint`, il est converti en type `long`, et le type du résultat est `long`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1547">If the operand of the negation operator is of type `uint`, it is converted to type `long`, and the type of the result is `long`.</span></span> <span data-ttu-id="3d917-1548">Une exception est la règle qui autorise le `int` valeur -2147483648 (-2 ^ 31) sous la forme d’un littéral d’entier décimal ([littéraux entiers](lexical-structure.md#integer-literals)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1548">An exception is the rule that permits the `int` value -2147483648 (-2^31) to be written as a decimal integer literal ([Integer literals](lexical-structure.md#integer-literals)).</span></span>

   <span data-ttu-id="3d917-1549">Si l’opérande de l’opérateur de négation est de type `ulong`, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1549">If the operand of the negation operator is of type `ulong`, a compile-time error occurs.</span></span> <span data-ttu-id="3d917-1550">Une exception est la règle qui autorise le `long` valeur -9223372036854775808 (-2 ^ 63) sous la forme d’un littéral d’entier décimal ([littéraux entiers](lexical-structure.md#integer-literals)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1550">An exception is the rule that permits the `long` value -9223372036854775808 (-2^63) to be written as a decimal integer literal ([Integer literals](lexical-structure.md#integer-literals)).</span></span>

*  <span data-ttu-id="3d917-1551">Négation de virgule flottante :</span><span class="sxs-lookup"><span data-stu-id="3d917-1551">Floating-point negation:</span></span>

   ```csharp
   float operator -(float x);
   double operator -(double x);
   ```

   <span data-ttu-id="3d917-1552">Le résultat est la valeur de `x` avec son signe inversé.</span><span class="sxs-lookup"><span data-stu-id="3d917-1552">The result is the value of `x` with its sign inverted.</span></span> <span data-ttu-id="3d917-1553">Si `x` est NaN, le résultat est également NaN.</span><span class="sxs-lookup"><span data-stu-id="3d917-1553">If `x` is NaN, the result is also NaN.</span></span>

*  <span data-ttu-id="3d917-1554">Négation de décimal :</span><span class="sxs-lookup"><span data-stu-id="3d917-1554">Decimal negation:</span></span>

   ```csharp
   decimal operator -(decimal x);
   ```

   <span data-ttu-id="3d917-1555">Le résultat est calculé en soustrayant `x` à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="3d917-1555">The result is computed by subtracting `x` from zero.</span></span> <span data-ttu-id="3d917-1556">Négation de décimal est équivalente à l’aide de l’opérateur moins unaire du type `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1556">Decimal negation is equivalent to using the unary minus operator of type `System.Decimal`.</span></span>

### <a name="logical-negation-operator"></a><span data-ttu-id="3d917-1557">Opérateur de négation logique</span><span class="sxs-lookup"><span data-stu-id="3d917-1557">Logical negation operator</span></span>

<span data-ttu-id="3d917-1558">Pour une opération du formulaire `!x`, la résolution de surcharge opérateur unaire ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1558">For an operation of the form `!x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-1559">L’opérande est converti au type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1559">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="3d917-1560">Il existe qu’un seul opérateur de négation logique prédéfini :</span><span class="sxs-lookup"><span data-stu-id="3d917-1560">Only one predefined logical negation operator exists:</span></span>
```csharp
bool operator !(bool x);
```

<span data-ttu-id="3d917-1561">Cet opérateur calcule la négation logique de l’opérande : Si l’opérande est `true`, le résultat est `false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1561">This operator computes the logical negation of the operand: If the operand is `true`, the result is `false`.</span></span> <span data-ttu-id="3d917-1562">Si l’opérande est `false`, le résultat est `true`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1562">If the operand is `false`, the result is `true`.</span></span>

### <a name="bitwise-complement-operator"></a><span data-ttu-id="3d917-1563">Opérateur de complément de bits</span><span class="sxs-lookup"><span data-stu-id="3d917-1563">Bitwise complement operator</span></span>

<span data-ttu-id="3d917-1564">Pour une opération du formulaire `~x`, la résolution de surcharge opérateur unaire ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1564">For an operation of the form `~x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-1565">L’opérande est converti au type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1565">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="3d917-1566">Les opérateurs de complément de bits prédéfinis sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-1566">The predefined bitwise complement operators are:</span></span>
```csharp
int operator ~(int x);
uint operator ~(uint x);
long operator ~(long x);
ulong operator ~(ulong x);
```

<span data-ttu-id="3d917-1567">Pour chacun de ces opérateurs, le résultat de l’opération est le complément de bits de `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1567">For each of these operators, the result of the operation is the bitwise complement of `x`.</span></span>

<span data-ttu-id="3d917-1568">Chaque type d’énumération `E` implicitement fournit l’opérateur de complément de bits suivant :</span><span class="sxs-lookup"><span data-stu-id="3d917-1568">Every enumeration type `E` implicitly provides the following bitwise complement operator:</span></span>

```csharp
E operator ~(E x);
```

<span data-ttu-id="3d917-1569">Le résultat de l’évaluation `~x`, où `x` est une expression d’un type énumération `E` avec un type sous-jacent `U`, est exactement le même que l’évaluation `(E)(~(U)x)`, sauf que la conversion en `E` est toujours effectuée comme dans le cas une `unchecked` contexte ([les opérateurs checked et unchecked](expressions.md#the-checked-and-unchecked-operators)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1569">The result of evaluating `~x`, where `x` is an expression of an enumeration type `E` with an underlying type `U`, is exactly the same as evaluating `(E)(~(U)x)`, except that the conversion to `E` is always performed as if in an `unchecked` context ([The checked and unchecked operators](expressions.md#the-checked-and-unchecked-operators)).</span></span>

### <a name="prefix-increment-and-decrement-operators"></a><span data-ttu-id="3d917-1570">Opérateurs préfixés d’incrémentation et de décrémentation</span><span class="sxs-lookup"><span data-stu-id="3d917-1570">Prefix increment and decrement operators</span></span>

```antlr
pre_increment_expression
    : '++' unary_expression
    ;

pre_decrement_expression
    : '--' unary_expression
    ;
```

<span data-ttu-id="3d917-1571">L’opérande d’une incrémentation de préfixe ou de décrémentation opération doit être une expression classifiée comme une variable, un accès à la propriété ou un indexeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1571">The operand of a prefix increment or decrement operation must be an expression classified as a variable, a property access, or an indexer access.</span></span> <span data-ttu-id="3d917-1572">Le résultat de l’opération est une valeur du même type que l’opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-1572">The result of the operation is a value of the same type as the operand.</span></span>

<span data-ttu-id="3d917-1573">Si l’opérande d’un préfixe d’incrémente ou opération de décrémentation est une propriété ou indexeur, la propriété ou l’indexeur doit avoir à la fois un `get` et un `set` accesseur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1573">If the operand of a prefix increment or decrement operation is a property or indexer access, the property or indexer must have both a `get` and a `set` accessor.</span></span> <span data-ttu-id="3d917-1574">Si ce n’est pas le cas, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1574">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="3d917-1575">Opérateur unaire de résolution de surcharge ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1575">Unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-1576">Prédéfinies `++` et `--` opérateurs existent pour les types suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char` , `float`, `double`, `decimal`et tout type enum.</span><span class="sxs-lookup"><span data-stu-id="3d917-1576">Predefined `++` and `--` operators exist for the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, and any enum type.</span></span> <span data-ttu-id="3d917-1577">Prédéfinis `++` opérateurs retournent la valeur produite en ajoutant 1 à l’opérande et sur les `--` opérateurs retournent la valeur produite en soustrayant 1 de l’opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-1577">The predefined `++` operators return the value produced by adding 1 to the operand, and the predefined `--` operators return the value produced by subtracting 1 from the operand.</span></span> <span data-ttu-id="3d917-1578">Dans un `checked` contexte, si le résultat de l’addition ou la soustraction est en dehors de la plage du type du résultat et le type de résultat est un type intégral ou enum, une `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1578">In a `checked` context, if the result of this addition or subtraction is outside the range of the result type and the result type is an integral type or enum type, a `System.OverflowException` is thrown.</span></span>

<span data-ttu-id="3d917-1579">Le traitement de l’exécution d’un incrément de préfixe ou de l’opération sous la forme de décrémentation `++x` ou `--x` se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-1579">The run-time processing of a prefix increment or decrement operation of the form `++x` or `--x` consists of the following steps:</span></span>

*   <span data-ttu-id="3d917-1580">Si `x` est classé en tant que variable :</span><span class="sxs-lookup"><span data-stu-id="3d917-1580">If `x` is classified as a variable:</span></span>
    * <span data-ttu-id="3d917-1581">`x` est évalué pour produire la variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-1581">`x` is evaluated to produce the variable.</span></span>
    * <span data-ttu-id="3d917-1582">L’opérateur sélectionné est appelé avec la valeur de `x` comme argument.</span><span class="sxs-lookup"><span data-stu-id="3d917-1582">The selected operator is invoked with the value of `x` as its argument.</span></span>
    * <span data-ttu-id="3d917-1583">La valeur retournée par l’opérateur est stockée dans l’emplacement indiqué par la version d’évaluation de `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1583">The value returned by the operator is stored in the location given by the evaluation of `x`.</span></span>
    * <span data-ttu-id="3d917-1584">La valeur retournée par l’opérateur devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-1584">The value returned by the operator becomes the result of the operation.</span></span>
*   <span data-ttu-id="3d917-1585">Si `x` est classé comme un accès de propriété ou l’indexeur :</span><span class="sxs-lookup"><span data-stu-id="3d917-1585">If `x` is classified as a property or indexer access:</span></span>
    * <span data-ttu-id="3d917-1586">L’expression d’instance (si `x` n’est pas `static`) et la liste d’arguments (si `x` est l’accès à un indexeur) associé `x` sont évaluées, et les résultats sont utilisés dans l’ultérieure `get` et `set` appels d’accesseur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1586">The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `get` and `set` accessor invocations.</span></span>
    * <span data-ttu-id="3d917-1587">Le `get` l’accesseur de `x` est appelé.</span><span class="sxs-lookup"><span data-stu-id="3d917-1587">The `get` accessor of `x` is invoked.</span></span>
    * <span data-ttu-id="3d917-1588">L’opérateur sélectionné est appelé avec la valeur retournée par la `get` accesseur comme argument.</span><span class="sxs-lookup"><span data-stu-id="3d917-1588">The selected operator is invoked with the value returned by the `get` accessor as its argument.</span></span>
    * <span data-ttu-id="3d917-1589">Le `set` l’accesseur de `x` est appelé avec la valeur retournée par l’opérateur en tant que son `value` argument.</span><span class="sxs-lookup"><span data-stu-id="3d917-1589">The `set` accessor of `x` is invoked with the value returned by the operator as its `value` argument.</span></span>
    * <span data-ttu-id="3d917-1590">La valeur retournée par l’opérateur devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-1590">The value returned by the operator becomes the result of the operation.</span></span>

<span data-ttu-id="3d917-1591">Le `++` et `--` opérateurs prennent également en charge postfix notation ([d’incrémentation et décrémentation opérateurs](expressions.md#postfix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1591">The `++` and `--` operators also support postfix notation ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators)).</span></span> <span data-ttu-id="3d917-1592">En règle générale, le résultat de `x++` ou `x--` est la valeur de `x` avant l’opération, tandis que le résultat de `++x` ou `--x` est la valeur de `x` après l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-1592">Typically, the result of `x++` or `x--` is the value of `x` before the operation, whereas the result of `++x` or `--x` is the value of `x` after the operation.</span></span> <span data-ttu-id="3d917-1593">Dans les deux cas, `x` lui-même a la même valeur après l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-1593">In either case, `x` itself has the same value after the operation.</span></span>

<span data-ttu-id="3d917-1594">Un `operator++` ou `operator--` implémentation peut être appelée à l’aide de la notation de préfixe ou de suffixe.</span><span class="sxs-lookup"><span data-stu-id="3d917-1594">An `operator++` or `operator--` implementation can be invoked using either postfix or prefix notation.</span></span> <span data-ttu-id="3d917-1595">Il n’est pas possible d’avoir des implémentations d’opérateurs distincts pour les deux notations.</span><span class="sxs-lookup"><span data-stu-id="3d917-1595">It is not possible to have separate operator implementations for the two notations.</span></span>

### <a name="cast-expressions"></a><span data-ttu-id="3d917-1596">Expressions de cast</span><span class="sxs-lookup"><span data-stu-id="3d917-1596">Cast expressions</span></span>

<span data-ttu-id="3d917-1597">Un *cast_expression* est utilisé pour convertir explicitement une expression en un type donné.</span><span class="sxs-lookup"><span data-stu-id="3d917-1597">A *cast_expression* is used to explicitly convert an expression to a given type.</span></span>

```antlr
cast_expression
    : '(' type ')' unary_expression
    ;
```

<span data-ttu-id="3d917-1598">Un *cast_expression* du formulaire `(T)E`, où `T` est un *type* et `E` est un *unary_expression*, effectue une explicite conversion ([conversions explicites](conversions.md#explicit-conversions)) de la valeur de `E` à taper `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1598">A *cast_expression* of the form `(T)E`, where `T` is a *type* and `E` is a *unary_expression*, performs an explicit conversion ([Explicit conversions](conversions.md#explicit-conversions)) of the value of `E` to type `T`.</span></span> <span data-ttu-id="3d917-1599">Si aucune conversion explicite `E` à `T`, se produit une erreur au moment de la liaison.</span><span class="sxs-lookup"><span data-stu-id="3d917-1599">If no explicit conversion exists from `E` to `T`, a binding-time error occurs.</span></span> <span data-ttu-id="3d917-1600">Sinon, le résultat est la valeur produite par la conversion explicite.</span><span class="sxs-lookup"><span data-stu-id="3d917-1600">Otherwise, the result is the value produced by the explicit conversion.</span></span> <span data-ttu-id="3d917-1601">Le résultat est toujours classifié en tant que valeur, même si `E` désigne une variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-1601">The result is always classified as a value, even if `E` denotes a variable.</span></span>

<span data-ttu-id="3d917-1602">La grammaire d’un *cast_expression* donne lieu à certaines ambiguïtés syntaxiques.</span><span class="sxs-lookup"><span data-stu-id="3d917-1602">The grammar for a *cast_expression* leads to certain syntactic ambiguities.</span></span> <span data-ttu-id="3d917-1603">Par exemple, l’expression `(x)-y` peut soit être interprétée comme un *cast_expression* (un cast de `-y` vers le type `x`) ou en tant qu’un *additive_expression* combiné avec un *parenthesized_expression* (qui calcule la valeur `x - y)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1603">For example, the expression `(x)-y` could either be interpreted as a *cast_expression* (a cast of `-y` to type `x`) or as an *additive_expression* combined with a *parenthesized_expression* (which computes the value `x - y)`.</span></span>

<span data-ttu-id="3d917-1604">Pour résoudre *cast_expression* ambiguïtés, la règle suivante existe : Une séquence d’un ou plusieurs *jeton*s ([espace blanc](lexical-structure.md#white-space)) placé entre parenthèses est considéré comme le début d’un *cast_expression* uniquement si au moins une des opérations suivantes est remplie :</span><span class="sxs-lookup"><span data-stu-id="3d917-1604">To resolve *cast_expression* ambiguities, the following rule exists: A sequence of one or more *token*s ([White space](lexical-structure.md#white-space)) enclosed in parentheses is considered the start of a *cast_expression* only if at least one of the following are true:</span></span>

*  <span data-ttu-id="3d917-1605">La séquence de jetons est la grammaire correcte pour un *type*, mais pas pour un *expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1605">The sequence of tokens is correct grammar for a *type*, but not for an *expression*.</span></span>
*  <span data-ttu-id="3d917-1606">La séquence de jetons est la grammaire correcte pour un *type*, et le jeton qui suit immédiatement la parenthèse fermante est le jeton «`~`», le jeton «`!`», le jeton «`(`», un  *identificateur* ([séquences d’échappement de caractère Unicode](lexical-structure.md#unicode-character-escape-sequences)), un *littéral* ([littéraux](lexical-structure.md#literals)), ou n’importe quel *mot clé*([Mots clés](lexical-structure.md#keywords)) à l’exception `as` et `is`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1606">The sequence of tokens is correct grammar for a *type*, and the token immediately following the closing parentheses is the token "`~`", the token "`!`", the token "`(`", an *identifier* ([Unicode character escape sequences](lexical-structure.md#unicode-character-escape-sequences)), a *literal* ([Literals](lexical-structure.md#literals)), or any *keyword* ([Keywords](lexical-structure.md#keywords)) except `as` and `is`.</span></span>

<span data-ttu-id="3d917-1607">Le terme « grammaire correcte » ci-dessus indique seulement que la séquence de jetons doit être conforme à la production grammaticale particulière.</span><span class="sxs-lookup"><span data-stu-id="3d917-1607">The term "correct grammar" above means only that the sequence of tokens must conform to the particular grammatical production.</span></span> <span data-ttu-id="3d917-1608">Plus précisément, il ne considère pas la signification réelle de tous les identificateurs qui le composent.</span><span class="sxs-lookup"><span data-stu-id="3d917-1608">It specifically does not consider the actual meaning of any constituent identifiers.</span></span> <span data-ttu-id="3d917-1609">Par exemple, si `x` et `y` sont des identificateurs, puis `x.y` est la grammaire correcte pour un type, même si `x.y` ne désigne en réalité un type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1609">For example, if `x` and `y` are identifiers, then `x.y` is correct grammar for a type, even if `x.y` doesn't actually denote a type.</span></span>

<span data-ttu-id="3d917-1610">À partir de la règle de suppression qui, si `x` et `y` sont des identificateurs, `(x)y`, `(x)(y)`, et `(x)(-y)` sont *cast_expression*s, mais `(x)-y` n’est pas, même si `x` identifie un type.</span><span class="sxs-lookup"><span data-stu-id="3d917-1610">From the disambiguation rule it follows that, if `x` and `y` are identifiers, `(x)y`, `(x)(y)`, and `(x)(-y)` are *cast_expression*s, but `(x)-y` is not, even if `x` identifies a type.</span></span> <span data-ttu-id="3d917-1611">Toutefois, si `x` est un mot clé qui identifie un type prédéfini (tel que `int`), puis les quatre formes sont *cast_expression*s (car un tel mot clé ne peut pas être une expression en lui-même).</span><span class="sxs-lookup"><span data-stu-id="3d917-1611">However, if `x` is a keyword that identifies a predefined type (such as `int`), then all four forms are *cast_expression*s (because such a keyword could not possibly be an expression by itself).</span></span>

### <a name="await-expressions"></a><span data-ttu-id="3d917-1612">Expressions await</span><span class="sxs-lookup"><span data-stu-id="3d917-1612">Await expressions</span></span>

<span data-ttu-id="3d917-1613">L’opérateur await est utilisé pour suspendre l’évaluation de la fonction async englobante jusqu'à ce que l’opération asynchrone représentée par l’opérande est terminée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1613">The await operator is used to suspend evaluation of the enclosing async function until the asynchronous operation represented by the operand has completed.</span></span>

```antlr
await_expression
    : 'await' unary_expression
    ;
```

<span data-ttu-id="3d917-1614">Un *await_expression* est autorisée uniquement dans le corps d’une fonction asynchrone ([itérateurs](classes.md#iterators)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1614">An *await_expression* is only allowed in the body of an async function ([Iterators](classes.md#iterators)).</span></span> <span data-ttu-id="3d917-1615">Dans l’englobante la plus proche fonction asynchrone, un *await_expression* ne peut pas se produire dans ces emplacements :</span><span class="sxs-lookup"><span data-stu-id="3d917-1615">Within the nearest enclosing async function, an *await_expression* may not occur in these places:</span></span>

*  <span data-ttu-id="3d917-1616">À l’intérieur d’une fonction anonyme imbriquées (non asynchrone)</span><span class="sxs-lookup"><span data-stu-id="3d917-1616">Inside a nested (non-async) anonymous function</span></span>
*  <span data-ttu-id="3d917-1617">Dans le bloc d’un *lock_statement*</span><span class="sxs-lookup"><span data-stu-id="3d917-1617">Inside the block of a *lock_statement*</span></span>
*  <span data-ttu-id="3d917-1618">Dans un contexte unsafe</span><span class="sxs-lookup"><span data-stu-id="3d917-1618">In an unsafe context</span></span>

<span data-ttu-id="3d917-1619">Notez qu’un *await_expression* ne peut pas apparaître à la plupart des emplacements au sein d’un *query_expression*, car celles sont syntaxiquement transformés pour utiliser des expressions lambda non asynchrone.</span><span class="sxs-lookup"><span data-stu-id="3d917-1619">Note that an *await_expression* cannot occur in most places within a *query_expression*, because those are syntactically transformed to use non-async lambda expressions.</span></span>

<span data-ttu-id="3d917-1620">À l’intérieur d’une fonction async, `await` ne peut pas être utilisé en tant qu’identificateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1620">Inside of an async function, `await` cannot be used as an identifier.</span></span> <span data-ttu-id="3d917-1621">Par conséquent, il n’existe aucune ambiguïté syntaxique entre diverses expressions impliquant des identificateurs et les expressions await.</span><span class="sxs-lookup"><span data-stu-id="3d917-1621">There is therefore no syntactic ambiguity between await-expressions and various expressions involving identifiers.</span></span> <span data-ttu-id="3d917-1622">En dehors des fonctions asynchrones, `await` agit comme un identificateur normal.</span><span class="sxs-lookup"><span data-stu-id="3d917-1622">Outside of async functions, `await` acts as a normal identifier.</span></span>

<span data-ttu-id="3d917-1623">L’opérande d’un *await_expression* est appelé le ***tâche***.</span><span class="sxs-lookup"><span data-stu-id="3d917-1623">The operand of an *await_expression* is called the ***task***.</span></span> <span data-ttu-id="3d917-1624">Il représente une opération asynchrone qui peut être ou non complète au moment où le *await_expression* est évaluée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1624">It represents an asynchronous operation that may or may not be complete at the time the *await_expression* is evaluated.</span></span> <span data-ttu-id="3d917-1625">L’objectif de l’opérateur await est pour suspendre l’exécution de la fonction async englobante jusqu'à ce que la tâche attendue soit terminée et pour obtenir son résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-1625">The purpose of the await operator is to suspend execution of the enclosing async function until the awaited task is complete, and then obtain its outcome.</span></span>

#### <a name="awaitable-expressions"></a><span data-ttu-id="3d917-1626">Expressions await</span><span class="sxs-lookup"><span data-stu-id="3d917-1626">Awaitable expressions</span></span>

<span data-ttu-id="3d917-1627">La tâche d’une expression await est nécessaire pour être ***awaitable***.</span><span class="sxs-lookup"><span data-stu-id="3d917-1627">The task of an await expression is required to be ***awaitable***.</span></span> <span data-ttu-id="3d917-1628">Une expression `t` peut être attendu si dont l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="3d917-1628">An expression `t` is awaitable if one of the following holds:</span></span>

*  <span data-ttu-id="3d917-1629">`t` est de type au moment de compilation `dynamic`</span><span class="sxs-lookup"><span data-stu-id="3d917-1629">`t` is of compile time type `dynamic`</span></span>
*  <span data-ttu-id="3d917-1630">`t` a une méthode d’extension ou d’instance accessible appelée `GetAwaiter` sans paramètres et aucun paramètre de type et un type de retour `A` pour lequel toutes les conditions suivantes contiennent :</span><span class="sxs-lookup"><span data-stu-id="3d917-1630">`t` has an accessible instance or extension method called `GetAwaiter` with no parameters and no type parameters, and a return type `A` for which all of the following hold:</span></span>
   * <span data-ttu-id="3d917-1631">`A` implémente l’interface `System.Runtime.CompilerServices.INotifyCompletion` (ci-après appelée `INotifyCompletion` par souci de concision)</span><span class="sxs-lookup"><span data-stu-id="3d917-1631">`A` implements the interface `System.Runtime.CompilerServices.INotifyCompletion` (hereafter known as `INotifyCompletion` for brevity)</span></span>
   * <span data-ttu-id="3d917-1632">`A` a une propriété d’instance accessible et lisible `IsCompleted` de type `bool`</span><span class="sxs-lookup"><span data-stu-id="3d917-1632">`A` has an accessible, readable instance property `IsCompleted` of type `bool`</span></span>
   * <span data-ttu-id="3d917-1633">`A` a une méthode d’instance accessible `GetResult` sans paramètres et aucun paramètre de type</span><span class="sxs-lookup"><span data-stu-id="3d917-1633">`A` has an accessible instance method `GetResult` with no parameters and no type parameters</span></span>

<span data-ttu-id="3d917-1634">L’objectif de la `GetAwaiter` méthode consiste à obtenir un ***awaiter*** pour la tâche.</span><span class="sxs-lookup"><span data-stu-id="3d917-1634">The purpose of the `GetAwaiter` method is to obtain an ***awaiter*** for the task.</span></span> <span data-ttu-id="3d917-1635">Le type `A` est appelé le ***type d’entité awaiter*** pour l’expression await.</span><span class="sxs-lookup"><span data-stu-id="3d917-1635">The type `A` is called the ***awaiter type*** for the await expression.</span></span>

<span data-ttu-id="3d917-1636">L’objectif de la `IsCompleted` propriété consiste à déterminer si la tâche est déjà terminée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1636">The purpose of the `IsCompleted` property is to determine if the task is already complete.</span></span> <span data-ttu-id="3d917-1637">Dans ce cas, il n’est pas nécessaire de suspendre l’évaluation.</span><span class="sxs-lookup"><span data-stu-id="3d917-1637">If so, there is no need to suspend evaluation.</span></span>

<span data-ttu-id="3d917-1638">L’objectif de la `INotifyCompletion.OnCompleted` méthode consiste à créer une « continuation » à la tâche ; par exemple, un délégué (de type `System.Action`) qui sera appelé une fois que la tâche est terminée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1638">The purpose of the `INotifyCompletion.OnCompleted` method is to sign up a "continuation" to the task; i.e. a delegate (of type `System.Action`) that will be invoked once the task is complete.</span></span>

<span data-ttu-id="3d917-1639">L’objectif de la `GetResult` méthode consiste à obtenir le résultat de la tâche une fois celle-ci terminée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1639">The purpose of the `GetResult` method is to obtain the outcome of the task once it is complete.</span></span> <span data-ttu-id="3d917-1640">Ce résultat peut être réussite, éventuellement avec une valeur de résultat, ou il peut être une exception qui est levée par le `GetResult` (méthode).</span><span class="sxs-lookup"><span data-stu-id="3d917-1640">This outcome may be successful completion, possibly with a result value, or it may be an exception which is thrown by the `GetResult` method.</span></span>

#### <a name="classification-of-await-expressions"></a><span data-ttu-id="3d917-1641">Classification des expressions await</span><span class="sxs-lookup"><span data-stu-id="3d917-1641">Classification of await expressions</span></span>

<span data-ttu-id="3d917-1642">L’expression `await t` est classé de la même façon que l’expression `(t).GetAwaiter().GetResult()`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1642">The expression `await t` is classified the same way as the expression `(t).GetAwaiter().GetResult()`.</span></span> <span data-ttu-id="3d917-1643">Par conséquent, si le type de retour de `GetResult` est `void`, le *await_expression* est classé comme rien.</span><span class="sxs-lookup"><span data-stu-id="3d917-1643">Thus, if the return type of `GetResult` is `void`, the *await_expression* is classified as nothing.</span></span> <span data-ttu-id="3d917-1644">S’il a un type de retour non void `T`, le *await_expression* est classé en tant que valeur de type `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1644">If it has a non-void return type `T`, the *await_expression* is classified as a value of type `T`.</span></span>

#### <a name="runtime-evaluation-of-await-expressions"></a><span data-ttu-id="3d917-1645">Évaluation de runtime des expressions await</span><span class="sxs-lookup"><span data-stu-id="3d917-1645">Runtime evaluation of await expressions</span></span>

<span data-ttu-id="3d917-1646">Lors de l’exécution, l’expression `await t` est évaluée comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-1646">At runtime, the expression `await t` is evaluated as follows:</span></span>

*  <span data-ttu-id="3d917-1647">Un élément awaiter `a` est obtenue en évaluant l’expression `(t).GetAwaiter()`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1647">An awaiter `a` is obtained by evaluating the expression `(t).GetAwaiter()`.</span></span>
*  <span data-ttu-id="3d917-1648">Un `bool` `b` est obtenue en évaluant l’expression `(a).IsCompleted`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1648">A `bool` `b` is obtained by evaluating the expression `(a).IsCompleted`.</span></span>
*  <span data-ttu-id="3d917-1649">Si `b` est `false` puis évaluation varie selon que `a` implémente l’interface `System.Runtime.CompilerServices.ICriticalNotifyCompletion` (ci-après appelée `ICriticalNotifyCompletion` par souci de concision).</span><span class="sxs-lookup"><span data-stu-id="3d917-1649">If `b` is `false` then evaluation depends on whether `a` implements the interface `System.Runtime.CompilerServices.ICriticalNotifyCompletion` (hereafter known as `ICriticalNotifyCompletion` for brevity).</span></span> <span data-ttu-id="3d917-1650">Cette vérification est effectuée lors de la liaison de temps ; par exemple, lors de l’exécution si `a` a le type de temps de compilation `dynamic`et au moment de la compilation dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="3d917-1650">This check is done at binding time; i.e. at runtime if `a` has the compile time type `dynamic`, and at compile time otherwise.</span></span> <span data-ttu-id="3d917-1651">Laisser `r` désignent le délégué de reprise ([itérateurs](classes.md#iterators)) :</span><span class="sxs-lookup"><span data-stu-id="3d917-1651">Let `r` denote the resumption delegate ([Iterators](classes.md#iterators)):</span></span>
    * <span data-ttu-id="3d917-1652">Si `a` n’implémente pas `ICriticalNotifyCompletion`, puis l’expression `(a as (INotifyCompletion)).OnCompleted(r)` est évaluée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1652">If `a` does not implement `ICriticalNotifyCompletion`, then the expression `(a as (INotifyCompletion)).OnCompleted(r)` is evaluated.</span></span>
    * <span data-ttu-id="3d917-1653">Si `a` implémente `ICriticalNotifyCompletion`, puis l’expression `(a as (ICriticalNotifyCompletion)).UnsafeOnCompleted(r)` est évaluée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1653">If `a` does implement `ICriticalNotifyCompletion`, then the expression `(a as (ICriticalNotifyCompletion)).UnsafeOnCompleted(r)` is evaluated.</span></span>
    * <span data-ttu-id="3d917-1654">Version d’évaluation est interrompue puis et contrôle est retourné à l’appelant actuel de la fonction async.</span><span class="sxs-lookup"><span data-stu-id="3d917-1654">Evaluation is then suspended, and control is returned to the current caller of the async function.</span></span>
*  <span data-ttu-id="3d917-1655">Soit immédiatement après (si `b` a été `true`), ou en cas d’un appel ultérieur du délégué de reprise (si `b` a été `false`), l’expression `(a).GetResult()` est évaluée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1655">Either immediately after (if `b` was `true`), or upon later invocation of the resumption delegate (if `b` was `false`), the expression `(a).GetResult()` is evaluated.</span></span> <span data-ttu-id="3d917-1656">Si elle retourne une valeur, cette valeur est le résultat de la *await_expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-1656">If it returns a value, that value is the result of the *await_expression*.</span></span> <span data-ttu-id="3d917-1657">Sinon, le résultat est nothing.</span><span class="sxs-lookup"><span data-stu-id="3d917-1657">Otherwise the result is nothing.</span></span>

<span data-ttu-id="3d917-1658">Implémentation d’un élément awaiter méthodes d’interface `INotifyCompletion.OnCompleted` et `ICriticalNotifyCompletion.UnsafeOnCompleted` doit provoquer le délégué `r` à appeler au maximum une fois.</span><span class="sxs-lookup"><span data-stu-id="3d917-1658">An awaiter's implementation of the interface methods `INotifyCompletion.OnCompleted` and `ICriticalNotifyCompletion.UnsafeOnCompleted` should cause the delegate `r` to be invoked at most once.</span></span> <span data-ttu-id="3d917-1659">Sinon, le comportement de la fonction async englobante est indéfini.</span><span class="sxs-lookup"><span data-stu-id="3d917-1659">Otherwise, the behavior of the enclosing async function is undefined.</span></span>

## <a name="arithmetic-operators"></a><span data-ttu-id="3d917-1660">Opérateurs arithmétiques</span><span class="sxs-lookup"><span data-stu-id="3d917-1660">Arithmetic operators</span></span>

<span data-ttu-id="3d917-1661">Le `*`, `/`, `%`, `+`, et `-` sont appelés les opérateurs arithmétiques.</span><span class="sxs-lookup"><span data-stu-id="3d917-1661">The `*`, `/`, `%`, `+`, and `-` operators are called the arithmetic operators.</span></span>

```antlr
multiplicative_expression
    : unary_expression
    | multiplicative_expression '*' unary_expression
    | multiplicative_expression '/' unary_expression
    | multiplicative_expression '%' unary_expression
    ;

additive_expression
    : multiplicative_expression
    | additive_expression '+' multiplicative_expression
    | additive_expression '-' multiplicative_expression
    ;
```

<span data-ttu-id="3d917-1662">Si un opérande d’un opérateur arithmétique a le type de compilation `dynamic`, puis l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="3d917-1662">If an operand of an arithmetic operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="3d917-1663">Dans ce cas le type de compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution à l’aide du type au moment de l’exécution de ces opérandes qui ont le type de compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1663">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

### <a name="multiplication-operator"></a><span data-ttu-id="3d917-1664">Opérateur de multiplication</span><span class="sxs-lookup"><span data-stu-id="3d917-1664">Multiplication operator</span></span>

<span data-ttu-id="3d917-1665">Pour une opération du formulaire `x * y`, opérateur binaire de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1665">For an operation of the form `x * y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-1666">Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1666">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="3d917-1667">Les opérateurs de multiplication prédéfinis sont répertoriés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3d917-1667">The predefined multiplication operators are listed below.</span></span> <span data-ttu-id="3d917-1668">Tous les opérateurs calculent le produit des `x` et `y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1668">The operators all compute the product of `x` and `y`.</span></span>

*  <span data-ttu-id="3d917-1669">Multiplication d’entiers :</span><span class="sxs-lookup"><span data-stu-id="3d917-1669">Integer multiplication:</span></span>

   ```csharp
   int operator *(int x, int y);
   uint operator *(uint x, uint y);
   long operator *(long x, long y);
   ulong operator *(ulong x, ulong y);
   ```

   <span data-ttu-id="3d917-1670">Dans un `checked` contexte, si le produit est en dehors de la plage du type du résultat, un `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1670">In a `checked` context, if the product is outside the range of the result type, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="3d917-1671">Dans un `unchecked` contexte, dépassements de capacité ne sont pas signalés et n’importe quel bits de poids fort significatifs en dehors de la plage du type du résultat sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="3d917-1671">In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.</span></span>


*  <span data-ttu-id="3d917-1672">Multiplication à virgule flottante :</span><span class="sxs-lookup"><span data-stu-id="3d917-1672">Floating-point multiplication:</span></span>

   ```csharp
   float operator *(float x, float y);
   double operator *(double x, double y);
   ```

   <span data-ttu-id="3d917-1673">Le produit est calculé selon les règles de la norme IEEE 754 arithmétique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1673">The product is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="3d917-1674">Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies, les zéros non significatifs, les valeurs infinies et NaN.</span><span class="sxs-lookup"><span data-stu-id="3d917-1674">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="3d917-1675">Dans la table, `x` et `y` sont des valeurs finies positives.</span><span class="sxs-lookup"><span data-stu-id="3d917-1675">In the table, `x` and `y` are positive finite values.</span></span> <span data-ttu-id="3d917-1676">`z` est le résultat de `x * y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1676">`z` is the result of `x * y`.</span></span> <span data-ttu-id="3d917-1677">Si le résultat est trop grand pour le type de destination, `z` est l’infini.</span><span class="sxs-lookup"><span data-stu-id="3d917-1677">If the result is too large for the destination type, `z` is infinity.</span></span> <span data-ttu-id="3d917-1678">Si le résultat est trop petit pour le type de destination, `z` est égal à zéro.</span><span class="sxs-lookup"><span data-stu-id="3d917-1678">If the result is too small for the destination type, `z` is zero.</span></span>

   |      |      |      |     |     |      |      |     |
   |:----:|-----:|:----:|:---:|:---:|:----:|:----:|:----|
   |      | <span data-ttu-id="3d917-1679">+y</span><span class="sxs-lookup"><span data-stu-id="3d917-1679">+y</span></span>   | <span data-ttu-id="3d917-1680">-y</span><span class="sxs-lookup"><span data-stu-id="3d917-1680">-y</span></span>   | <span data-ttu-id="3d917-1681">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1681">+0</span></span>  | <span data-ttu-id="3d917-1682">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1682">-0</span></span>  | <span data-ttu-id="3d917-1683">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1683">+inf</span></span> | <span data-ttu-id="3d917-1684">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1684">-inf</span></span> | <span data-ttu-id="3d917-1685">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1685">NaN</span></span> | 
   | <span data-ttu-id="3d917-1686">+x</span><span class="sxs-lookup"><span data-stu-id="3d917-1686">+x</span></span>   | <span data-ttu-id="3d917-1687">+z</span><span class="sxs-lookup"><span data-stu-id="3d917-1687">+z</span></span>   | <span data-ttu-id="3d917-1688">-z</span><span class="sxs-lookup"><span data-stu-id="3d917-1688">-z</span></span>   | <span data-ttu-id="3d917-1689">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1689">+0</span></span>  | <span data-ttu-id="3d917-1690">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1690">-0</span></span>  | <span data-ttu-id="3d917-1691">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1691">+inf</span></span> | <span data-ttu-id="3d917-1692">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1692">-inf</span></span> | <span data-ttu-id="3d917-1693">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1693">NaN</span></span> | 
   | <span data-ttu-id="3d917-1694">-x</span><span class="sxs-lookup"><span data-stu-id="3d917-1694">-x</span></span>   | <span data-ttu-id="3d917-1695">-z</span><span class="sxs-lookup"><span data-stu-id="3d917-1695">-z</span></span>   | <span data-ttu-id="3d917-1696">+z</span><span class="sxs-lookup"><span data-stu-id="3d917-1696">+z</span></span>   | <span data-ttu-id="3d917-1697">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1697">-0</span></span>  | <span data-ttu-id="3d917-1698">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1698">+0</span></span>  | <span data-ttu-id="3d917-1699">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1699">-inf</span></span> | <span data-ttu-id="3d917-1700">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1700">+inf</span></span> | <span data-ttu-id="3d917-1701">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1701">NaN</span></span> | 
   | <span data-ttu-id="3d917-1702">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1702">+0</span></span>   | <span data-ttu-id="3d917-1703">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1703">+0</span></span>   | <span data-ttu-id="3d917-1704">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1704">-0</span></span>   | <span data-ttu-id="3d917-1705">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1705">+0</span></span>  | <span data-ttu-id="3d917-1706">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1706">-0</span></span>  | <span data-ttu-id="3d917-1707">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1707">NaN</span></span>  | <span data-ttu-id="3d917-1708">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1708">NaN</span></span>  | <span data-ttu-id="3d917-1709">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1709">NaN</span></span> | 
   | <span data-ttu-id="3d917-1710">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1710">-0</span></span>   | <span data-ttu-id="3d917-1711">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1711">-0</span></span>   | <span data-ttu-id="3d917-1712">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1712">+0</span></span>   | <span data-ttu-id="3d917-1713">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1713">-0</span></span>  | <span data-ttu-id="3d917-1714">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1714">+0</span></span>  | <span data-ttu-id="3d917-1715">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1715">NaN</span></span>  | <span data-ttu-id="3d917-1716">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1716">NaN</span></span>  | <span data-ttu-id="3d917-1717">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1717">NaN</span></span> | 
   | <span data-ttu-id="3d917-1718">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1718">+inf</span></span> | <span data-ttu-id="3d917-1719">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1719">+inf</span></span> | <span data-ttu-id="3d917-1720">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1720">-inf</span></span> | <span data-ttu-id="3d917-1721">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1721">NaN</span></span> | <span data-ttu-id="3d917-1722">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1722">NaN</span></span> | <span data-ttu-id="3d917-1723">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1723">+inf</span></span> | <span data-ttu-id="3d917-1724">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1724">-inf</span></span> | <span data-ttu-id="3d917-1725">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1725">NaN</span></span> | 
   | <span data-ttu-id="3d917-1726">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1726">-inf</span></span> | <span data-ttu-id="3d917-1727">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1727">-inf</span></span> | <span data-ttu-id="3d917-1728">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1728">+inf</span></span> | <span data-ttu-id="3d917-1729">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1729">NaN</span></span> | <span data-ttu-id="3d917-1730">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1730">NaN</span></span> | <span data-ttu-id="3d917-1731">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1731">-inf</span></span> | <span data-ttu-id="3d917-1732">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1732">+inf</span></span> | <span data-ttu-id="3d917-1733">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1733">NaN</span></span> | 
   | <span data-ttu-id="3d917-1734">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1734">NaN</span></span>  | <span data-ttu-id="3d917-1735">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1735">NaN</span></span>  | <span data-ttu-id="3d917-1736">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1736">NaN</span></span>  | <span data-ttu-id="3d917-1737">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1737">NaN</span></span> | <span data-ttu-id="3d917-1738">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1738">NaN</span></span> | <span data-ttu-id="3d917-1739">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1739">NaN</span></span>  | <span data-ttu-id="3d917-1740">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1740">NaN</span></span>  | <span data-ttu-id="3d917-1741">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1741">NaN</span></span> | 

*  <span data-ttu-id="3d917-1742">Multiplication de nombres décimaux :</span><span class="sxs-lookup"><span data-stu-id="3d917-1742">Decimal multiplication:</span></span>

   ```csharp
   decimal operator *(decimal x, decimal y);
   ```

   <span data-ttu-id="3d917-1743">Si la valeur résultante est trop grande pour être représenté dans le `decimal` format, un `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1743">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="3d917-1744">Si la valeur de résultat est trop petite pour être représenté dans le `decimal` format, le résultat est égal à zéro.</span><span class="sxs-lookup"><span data-stu-id="3d917-1744">If the result value is too small to represent in the `decimal` format, the result is zero.</span></span> <span data-ttu-id="3d917-1745">La mise à l’échelle du résultat, avant tout arrondi, est la somme des échelles des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-1745">The scale of the result, before any rounding, is the sum of the scales of the two operands.</span></span>

   <span data-ttu-id="3d917-1746">Multiplication de nombres décimaux est équivalente à l’aide de l’opérateur de multiplication du type `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1746">Decimal multiplication is equivalent to using the multiplication operator of type `System.Decimal`.</span></span>


### <a name="division-operator"></a><span data-ttu-id="3d917-1747">Opérateur de division</span><span class="sxs-lookup"><span data-stu-id="3d917-1747">Division operator</span></span>

<span data-ttu-id="3d917-1748">Pour une opération du formulaire `x / y`, opérateur binaire de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1748">For an operation of the form `x / y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-1749">Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1749">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="3d917-1750">Les opérateurs de division prédéfinis sont répertoriés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3d917-1750">The predefined division operators are listed below.</span></span> <span data-ttu-id="3d917-1751">Tous les opérateurs calculent le quotient de `x` et `y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1751">The operators all compute the quotient of `x` and `y`.</span></span>

*  <span data-ttu-id="3d917-1752">Division d’entier :</span><span class="sxs-lookup"><span data-stu-id="3d917-1752">Integer division:</span></span>

   ```csharp
   int operator /(int x, int y);
   uint operator /(uint x, uint y);
   long operator /(long x, long y);
   ulong operator /(ulong x, ulong y);
   ```

   <span data-ttu-id="3d917-1753">Si la valeur de l’opérande droite est zéro, un `System.DivideByZeroException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1753">If the value of the right operand is zero, a `System.DivideByZeroException` is thrown.</span></span>

   <span data-ttu-id="3d917-1754">La division arrondit le résultat vers zéro.</span><span class="sxs-lookup"><span data-stu-id="3d917-1754">The division rounds the result towards zero.</span></span> <span data-ttu-id="3d917-1755">Par conséquent, la valeur absolue du résultat est le plus grand entier possible qui est inférieur ou égal à la valeur absolue du quotient de deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-1755">Thus the absolute value of the result is the largest possible integer that is less than or equal to the absolute value of the quotient of the two operands.</span></span> <span data-ttu-id="3d917-1756">Le résultat est zéro ou une valeur positive lorsque les deux opérandes ont le même signe et zéro ou négatif lorsque les deux opérandes ont des signes opposés.</span><span class="sxs-lookup"><span data-stu-id="3d917-1756">The result is zero or positive when the two operands have the same sign and zero or negative when the two operands have opposite signs.</span></span>

   <span data-ttu-id="3d917-1757">Si l’opérande gauche est le plus petit qui peut être représenté `int` ou `long` valeur et l’opérande de droite est `-1`, un dépassement de capacité se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-1757">If the left operand is the smallest representable `int` or `long` value and the right operand is `-1`, an overflow occurs.</span></span> <span data-ttu-id="3d917-1758">Dans un `checked` contexte, cela entraîne un `System.ArithmeticException` (ou une sous-classe) devant être levé.</span><span class="sxs-lookup"><span data-stu-id="3d917-1758">In a `checked` context, this causes a `System.ArithmeticException` (or a subclass thereof) to be thrown.</span></span> <span data-ttu-id="3d917-1759">Dans un `unchecked` contexte, il est défini par l’implémentation pour savoir si un `System.ArithmeticException` (ou une sous-classe) est levée ou le dépassement de capacité n’est pas rapporté par la valeur obtenue en cours de l’opérande gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-1759">In an `unchecked` context, it is implementation-defined as to whether a `System.ArithmeticException` (or a subclass thereof) is thrown or the overflow goes unreported with the resulting value being that of the left operand.</span></span>

*  <span data-ttu-id="3d917-1760">Division à virgule flottante :</span><span class="sxs-lookup"><span data-stu-id="3d917-1760">Floating-point division:</span></span>

   ```csharp
   float operator /(float x, float y);
   double operator /(double x, double y);
   ```

   <span data-ttu-id="3d917-1761">Le quotient est calculé selon les règles de la norme IEEE 754 arithmétique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1761">The quotient is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="3d917-1762">Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies, les zéros non significatifs, les valeurs infinies et NaN.</span><span class="sxs-lookup"><span data-stu-id="3d917-1762">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="3d917-1763">Dans la table, `x` et `y` sont des valeurs finies positives.</span><span class="sxs-lookup"><span data-stu-id="3d917-1763">In the table, `x` and `y` are positive finite values.</span></span> <span data-ttu-id="3d917-1764">`z` est le résultat de `x / y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1764">`z` is the result of `x / y`.</span></span> <span data-ttu-id="3d917-1765">Si le résultat est trop grand pour le type de destination, `z` est l’infini.</span><span class="sxs-lookup"><span data-stu-id="3d917-1765">If the result is too large for the destination type, `z` is infinity.</span></span> <span data-ttu-id="3d917-1766">Si le résultat est trop petit pour le type de destination, `z` est égal à zéro.</span><span class="sxs-lookup"><span data-stu-id="3d917-1766">If the result is too small for the destination type, `z` is zero.</span></span>

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | <span data-ttu-id="3d917-1767">+y</span><span class="sxs-lookup"><span data-stu-id="3d917-1767">+y</span></span>   | <span data-ttu-id="3d917-1768">-y</span><span class="sxs-lookup"><span data-stu-id="3d917-1768">-y</span></span>   | <span data-ttu-id="3d917-1769">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1769">+0</span></span>   | <span data-ttu-id="3d917-1770">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1770">-0</span></span>   | <span data-ttu-id="3d917-1771">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1771">+inf</span></span> | <span data-ttu-id="3d917-1772">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1772">-inf</span></span> | <span data-ttu-id="3d917-1773">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1773">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1774">+x</span><span class="sxs-lookup"><span data-stu-id="3d917-1774">+x</span></span>   | <span data-ttu-id="3d917-1775">+z</span><span class="sxs-lookup"><span data-stu-id="3d917-1775">+z</span></span>   | <span data-ttu-id="3d917-1776">-z</span><span class="sxs-lookup"><span data-stu-id="3d917-1776">-z</span></span>   | <span data-ttu-id="3d917-1777">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1777">+inf</span></span> | <span data-ttu-id="3d917-1778">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1778">-inf</span></span> | <span data-ttu-id="3d917-1779">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1779">+0</span></span>   | <span data-ttu-id="3d917-1780">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1780">-0</span></span>   | <span data-ttu-id="3d917-1781">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1781">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1782">-x</span><span class="sxs-lookup"><span data-stu-id="3d917-1782">-x</span></span>   | <span data-ttu-id="3d917-1783">-z</span><span class="sxs-lookup"><span data-stu-id="3d917-1783">-z</span></span>   | <span data-ttu-id="3d917-1784">+z</span><span class="sxs-lookup"><span data-stu-id="3d917-1784">+z</span></span>   | <span data-ttu-id="3d917-1785">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1785">-inf</span></span> | <span data-ttu-id="3d917-1786">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1786">+inf</span></span> | <span data-ttu-id="3d917-1787">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1787">-0</span></span>   | <span data-ttu-id="3d917-1788">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1788">+0</span></span>   | <span data-ttu-id="3d917-1789">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1789">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1790">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1790">+0</span></span>   | <span data-ttu-id="3d917-1791">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1791">+0</span></span>   | <span data-ttu-id="3d917-1792">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1792">-0</span></span>   | <span data-ttu-id="3d917-1793">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1793">NaN</span></span>  | <span data-ttu-id="3d917-1794">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1794">NaN</span></span>  | <span data-ttu-id="3d917-1795">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1795">+0</span></span>   | <span data-ttu-id="3d917-1796">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1796">-0</span></span>   | <span data-ttu-id="3d917-1797">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1797">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1798">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1798">-0</span></span>   | <span data-ttu-id="3d917-1799">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1799">-0</span></span>   | <span data-ttu-id="3d917-1800">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1800">+0</span></span>   | <span data-ttu-id="3d917-1801">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1801">NaN</span></span>  | <span data-ttu-id="3d917-1802">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1802">NaN</span></span>  | <span data-ttu-id="3d917-1803">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1803">-0</span></span>   | <span data-ttu-id="3d917-1804">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1804">+0</span></span>   | <span data-ttu-id="3d917-1805">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1805">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1806">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1806">+inf</span></span> | <span data-ttu-id="3d917-1807">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1807">+inf</span></span> | <span data-ttu-id="3d917-1808">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1808">-inf</span></span> | <span data-ttu-id="3d917-1809">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1809">+inf</span></span> | <span data-ttu-id="3d917-1810">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1810">-inf</span></span> | <span data-ttu-id="3d917-1811">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1811">NaN</span></span>  | <span data-ttu-id="3d917-1812">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1812">NaN</span></span>  | <span data-ttu-id="3d917-1813">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1813">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1814">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1814">-inf</span></span> | <span data-ttu-id="3d917-1815">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1815">-inf</span></span> | <span data-ttu-id="3d917-1816">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1816">+inf</span></span> | <span data-ttu-id="3d917-1817">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1817">-inf</span></span> | <span data-ttu-id="3d917-1818">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1818">+inf</span></span> | <span data-ttu-id="3d917-1819">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1819">NaN</span></span>  | <span data-ttu-id="3d917-1820">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1820">NaN</span></span>  | <span data-ttu-id="3d917-1821">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1821">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1822">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1822">NaN</span></span>  | <span data-ttu-id="3d917-1823">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1823">NaN</span></span>  | <span data-ttu-id="3d917-1824">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1824">NaN</span></span>  | <span data-ttu-id="3d917-1825">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1825">NaN</span></span>  | <span data-ttu-id="3d917-1826">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1826">NaN</span></span>  | <span data-ttu-id="3d917-1827">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1827">NaN</span></span>  | <span data-ttu-id="3d917-1828">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1828">NaN</span></span>  | <span data-ttu-id="3d917-1829">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1829">NaN</span></span>  | 

*  <span data-ttu-id="3d917-1830">Division décimale :</span><span class="sxs-lookup"><span data-stu-id="3d917-1830">Decimal division:</span></span>

   ```csharp
   decimal operator /(decimal x, decimal y);
   ```

   <span data-ttu-id="3d917-1831">Si la valeur de l’opérande droite est zéro, un `System.DivideByZeroException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1831">If the value of the right operand is zero, a `System.DivideByZeroException` is thrown.</span></span> <span data-ttu-id="3d917-1832">Si la valeur résultante est trop grande pour être représenté dans le `decimal` format, un `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1832">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="3d917-1833">Si la valeur de résultat est trop petite pour être représenté dans le `decimal` format, le résultat est égal à zéro.</span><span class="sxs-lookup"><span data-stu-id="3d917-1833">If the result value is too small to represent in the `decimal` format, the result is zero.</span></span> <span data-ttu-id="3d917-1834">La mise à l’échelle du résultat est la plus petite qui conservera un résultat égal à la plus proche de la valeur décimale représentable le résultat mathématique true.</span><span class="sxs-lookup"><span data-stu-id="3d917-1834">The scale of the result is the smallest scale that will preserve a result equal to the nearest representable decimal value to the true mathematical result.</span></span>

   <span data-ttu-id="3d917-1835">La division décimale équivaut à l’utilisation de l’opérateur de division du type `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1835">Decimal division is equivalent to using the division operator of type `System.Decimal`.</span></span>


### <a name="remainder-operator"></a><span data-ttu-id="3d917-1836">Opérateur de reste</span><span class="sxs-lookup"><span data-stu-id="3d917-1836">Remainder operator</span></span>

<span data-ttu-id="3d917-1837">Pour une opération du formulaire `x % y`, opérateur binaire de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1837">For an operation of the form `x % y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-1838">Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1838">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="3d917-1839">Les opérateurs de reste prédéfinis sont répertoriés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3d917-1839">The predefined remainder operators are listed below.</span></span> <span data-ttu-id="3d917-1840">Tous les opérateurs de calcul le reste de la division entre `x` et `y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1840">The operators all compute the remainder of the division between `x` and `y`.</span></span>

*  <span data-ttu-id="3d917-1841">Reste entier :</span><span class="sxs-lookup"><span data-stu-id="3d917-1841">Integer remainder:</span></span>

   ```csharp
   int operator %(int x, int y);
   uint operator %(uint x, uint y);
   long operator %(long x, long y);
   ulong operator %(ulong x, ulong y);
   ```

   <span data-ttu-id="3d917-1842">Le résultat de `x % y` est la valeur produite par `x - (x / y) * y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1842">The result of `x % y` is the value produced by `x - (x / y) * y`.</span></span> <span data-ttu-id="3d917-1843">Si `y` est égal à zéro, un `System.DivideByZeroException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1843">If `y` is zero, a `System.DivideByZeroException` is thrown.</span></span>

   <span data-ttu-id="3d917-1844">Si l’opérande gauche est le plus petit `int` ou `long` valeur et l’opérande de droite est `-1`, un `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1844">If the left operand is the smallest `int` or `long` value and the right operand is `-1`, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="3d917-1845">En aucun cas ne `x % y` lever une exception où `x / y` pas lève une exception.</span><span class="sxs-lookup"><span data-stu-id="3d917-1845">In no case does `x % y` throw an exception where `x / y` would not throw an exception.</span></span>

*  <span data-ttu-id="3d917-1846">Reste à virgule flottante :</span><span class="sxs-lookup"><span data-stu-id="3d917-1846">Floating-point remainder:</span></span>

   ```csharp
   float operator %(float x, float y);
   double operator %(double x, double y);
   ```

   <span data-ttu-id="3d917-1847">Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies, les zéros non significatifs, les valeurs infinies et NaN.</span><span class="sxs-lookup"><span data-stu-id="3d917-1847">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="3d917-1848">Dans la table, `x` et `y` sont des valeurs finies positives.</span><span class="sxs-lookup"><span data-stu-id="3d917-1848">In the table, `x` and `y` are positive finite values.</span></span> <span data-ttu-id="3d917-1849">`z` est le résultat de `x % y` et est calculée en tant que `x - n * y`, où `n` est le plus grand entier possible qui est inférieur ou égal à `x / y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1849">`z` is the result of `x % y` and is computed as `x - n * y`, where `n` is the largest possible integer that is less than or equal to `x / y`.</span></span> <span data-ttu-id="3d917-1850">Cette méthode de calcul du reste est analogue à celle utilisée pour les opérandes entiers, mais diffère de la définition de la norme IEEE 754 (dans lequel `n` est l’entier le plus proche `x / y`).</span><span class="sxs-lookup"><span data-stu-id="3d917-1850">This method of computing the remainder is analogous to that used for integer operands, but differs from the IEEE 754 definition (in which `n` is the integer closest to `x / y`).</span></span>

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | <span data-ttu-id="3d917-1851">+y</span><span class="sxs-lookup"><span data-stu-id="3d917-1851">+y</span></span>   | <span data-ttu-id="3d917-1852">-y</span><span class="sxs-lookup"><span data-stu-id="3d917-1852">-y</span></span>   | <span data-ttu-id="3d917-1853">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1853">+0</span></span>   | <span data-ttu-id="3d917-1854">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1854">-0</span></span>   | <span data-ttu-id="3d917-1855">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1855">+inf</span></span> | <span data-ttu-id="3d917-1856">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1856">-inf</span></span> | <span data-ttu-id="3d917-1857">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1857">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1858">+x</span><span class="sxs-lookup"><span data-stu-id="3d917-1858">+x</span></span>   | <span data-ttu-id="3d917-1859">+z</span><span class="sxs-lookup"><span data-stu-id="3d917-1859">+z</span></span>   | <span data-ttu-id="3d917-1860">+z</span><span class="sxs-lookup"><span data-stu-id="3d917-1860">+z</span></span>   | <span data-ttu-id="3d917-1861">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1861">NaN</span></span>  | <span data-ttu-id="3d917-1862">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1862">NaN</span></span>  | <span data-ttu-id="3d917-1863">x</span><span class="sxs-lookup"><span data-stu-id="3d917-1863">x</span></span>    | <span data-ttu-id="3d917-1864">x</span><span class="sxs-lookup"><span data-stu-id="3d917-1864">x</span></span>    | <span data-ttu-id="3d917-1865">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1865">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1866">-x</span><span class="sxs-lookup"><span data-stu-id="3d917-1866">-x</span></span>   | <span data-ttu-id="3d917-1867">-z</span><span class="sxs-lookup"><span data-stu-id="3d917-1867">-z</span></span>   | <span data-ttu-id="3d917-1868">-z</span><span class="sxs-lookup"><span data-stu-id="3d917-1868">-z</span></span>   | <span data-ttu-id="3d917-1869">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1869">NaN</span></span>  | <span data-ttu-id="3d917-1870">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1870">NaN</span></span>  | <span data-ttu-id="3d917-1871">-x</span><span class="sxs-lookup"><span data-stu-id="3d917-1871">-x</span></span>   | <span data-ttu-id="3d917-1872">-x</span><span class="sxs-lookup"><span data-stu-id="3d917-1872">-x</span></span>   | <span data-ttu-id="3d917-1873">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1873">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1874">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1874">+0</span></span>   | <span data-ttu-id="3d917-1875">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1875">+0</span></span>   | <span data-ttu-id="3d917-1876">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1876">+0</span></span>   | <span data-ttu-id="3d917-1877">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1877">NaN</span></span>  | <span data-ttu-id="3d917-1878">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1878">NaN</span></span>  | <span data-ttu-id="3d917-1879">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1879">+0</span></span>   | <span data-ttu-id="3d917-1880">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1880">+0</span></span>   | <span data-ttu-id="3d917-1881">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1881">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1882">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1882">-0</span></span>   | <span data-ttu-id="3d917-1883">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1883">-0</span></span>   | <span data-ttu-id="3d917-1884">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1884">-0</span></span>   | <span data-ttu-id="3d917-1885">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1885">NaN</span></span>  | <span data-ttu-id="3d917-1886">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1886">NaN</span></span>  | <span data-ttu-id="3d917-1887">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1887">-0</span></span>   | <span data-ttu-id="3d917-1888">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1888">-0</span></span>   | <span data-ttu-id="3d917-1889">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1889">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1890">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1890">+inf</span></span> | <span data-ttu-id="3d917-1891">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1891">NaN</span></span>  | <span data-ttu-id="3d917-1892">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1892">NaN</span></span>  | <span data-ttu-id="3d917-1893">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1893">NaN</span></span>  | <span data-ttu-id="3d917-1894">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1894">NaN</span></span>  | <span data-ttu-id="3d917-1895">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1895">NaN</span></span>  | <span data-ttu-id="3d917-1896">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1896">NaN</span></span>  | <span data-ttu-id="3d917-1897">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1897">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1898">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1898">-inf</span></span> | <span data-ttu-id="3d917-1899">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1899">NaN</span></span>  | <span data-ttu-id="3d917-1900">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1900">NaN</span></span>  | <span data-ttu-id="3d917-1901">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1901">NaN</span></span>  | <span data-ttu-id="3d917-1902">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1902">NaN</span></span>  | <span data-ttu-id="3d917-1903">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1903">NaN</span></span>  | <span data-ttu-id="3d917-1904">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1904">NaN</span></span>  | <span data-ttu-id="3d917-1905">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1905">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1906">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1906">NaN</span></span>  | <span data-ttu-id="3d917-1907">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1907">NaN</span></span>  | <span data-ttu-id="3d917-1908">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1908">NaN</span></span>  | <span data-ttu-id="3d917-1909">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1909">NaN</span></span>  | <span data-ttu-id="3d917-1910">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1910">NaN</span></span>  | <span data-ttu-id="3d917-1911">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1911">NaN</span></span>  | <span data-ttu-id="3d917-1912">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1912">NaN</span></span>  | <span data-ttu-id="3d917-1913">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1913">NaN</span></span>  | 

*  <span data-ttu-id="3d917-1914">Reste de nombres décimaux :</span><span class="sxs-lookup"><span data-stu-id="3d917-1914">Decimal remainder:</span></span>

   ```csharp
   decimal operator %(decimal x, decimal y);
   ```

   <span data-ttu-id="3d917-1915">Si la valeur de l’opérande droite est zéro, un `System.DivideByZeroException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1915">If the value of the right operand is zero, a `System.DivideByZeroException` is thrown.</span></span> <span data-ttu-id="3d917-1916">La mise à l’échelle du résultat, avant tout arrondi, est la plus grande des échelles des deux opérandes, et le signe du résultat, si différente de zéro, est identique à celui de `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1916">The scale of the result, before any rounding, is the larger of the scales of the two operands, and the sign of the result, if non-zero, is the same as that of `x`.</span></span>

   <span data-ttu-id="3d917-1917">Reste de nombres décimaux est équivalente à l’aide de l’opérateur de reste du type `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1917">Decimal remainder is equivalent to using the remainder operator of type `System.Decimal`.</span></span>


### <a name="addition-operator"></a><span data-ttu-id="3d917-1918">Opérateur d’addition</span><span class="sxs-lookup"><span data-stu-id="3d917-1918">Addition operator</span></span>

<span data-ttu-id="3d917-1919">Pour une opération du formulaire `x + y`, opérateur binaire de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1919">For an operation of the form `x + y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-1920">Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-1920">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="3d917-1921">Les opérateurs d’addition prédéfinis sont répertoriées ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3d917-1921">The predefined addition operators are listed below.</span></span> <span data-ttu-id="3d917-1922">Pour les types numériques et d’énumération, les opérateurs d’addition prédéfinis calculent la somme des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-1922">For numeric and enumeration types, the predefined addition operators compute the sum of the two operands.</span></span> <span data-ttu-id="3d917-1923">Lorsqu’un ou deux opérandes sont de type chaîne, les opérateurs d’addition prédéfinis concaténer la représentation sous forme de chaîne des opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-1923">When one or both operands are of type string, the predefined addition operators concatenate the string representation of the operands.</span></span>

*  <span data-ttu-id="3d917-1924">Ajout de l’entier :</span><span class="sxs-lookup"><span data-stu-id="3d917-1924">Integer addition:</span></span>

   ```csharp
   int operator +(int x, int y);
   uint operator +(uint x, uint y);
   long operator +(long x, long y);
   ulong operator +(ulong x, ulong y);
   ```

   <span data-ttu-id="3d917-1925">Dans un `checked` contexte, si la somme est en dehors de la plage du type du résultat, un `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1925">In a `checked` context, if the sum is outside the range of the result type, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="3d917-1926">Dans un `unchecked` contexte, dépassements de capacité ne sont pas signalés et n’importe quel bits de poids fort significatifs en dehors de la plage du type du résultat sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="3d917-1926">In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.</span></span>

*  <span data-ttu-id="3d917-1927">Addition à virgule flottante :</span><span class="sxs-lookup"><span data-stu-id="3d917-1927">Floating-point addition:</span></span>

   ```csharp
   float operator +(float x, float y);
   double operator +(double x, double y);
   ```

   <span data-ttu-id="3d917-1928">La somme est calculée selon les règles de la norme IEEE 754 arithmétique.</span><span class="sxs-lookup"><span data-stu-id="3d917-1928">The sum is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="3d917-1929">Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies, les zéros non significatifs, les valeurs infinies et NaN.</span><span class="sxs-lookup"><span data-stu-id="3d917-1929">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="3d917-1930">Dans la table, `x` et `y` sont des valeurs finies, et `z` est le résultat de `x + y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1930">In the table, `x` and `y` are nonzero finite values, and `z` is the result of `x + y`.</span></span> <span data-ttu-id="3d917-1931">Si `x` et `y` ont la même grandeur, mais l’opposé de signes, `z` est un zéro positif.</span><span class="sxs-lookup"><span data-stu-id="3d917-1931">If `x` and `y` have the same magnitude but opposite signs, `z` is positive zero.</span></span> <span data-ttu-id="3d917-1932">Si `x + y` est trop grand pour être représenté dans le type de destination, `z` est un infini ayant le même signe que `x + y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1932">If `x + y` is too large to represent in the destination type, `z` is an infinity with the same sign as `x + y`.</span></span>

   |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | <span data-ttu-id="3d917-1933">o</span><span class="sxs-lookup"><span data-stu-id="3d917-1933">y</span></span>    | <span data-ttu-id="3d917-1934">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1934">+0</span></span>   | <span data-ttu-id="3d917-1935">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1935">-0</span></span>   | <span data-ttu-id="3d917-1936">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1936">+inf</span></span> | <span data-ttu-id="3d917-1937">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1937">-inf</span></span> | <span data-ttu-id="3d917-1938">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1938">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1939">x</span><span class="sxs-lookup"><span data-stu-id="3d917-1939">x</span></span>    | <span data-ttu-id="3d917-1940">e</span><span class="sxs-lookup"><span data-stu-id="3d917-1940">z</span></span>    | <span data-ttu-id="3d917-1941">x</span><span class="sxs-lookup"><span data-stu-id="3d917-1941">x</span></span>    | <span data-ttu-id="3d917-1942">x</span><span class="sxs-lookup"><span data-stu-id="3d917-1942">x</span></span>    | <span data-ttu-id="3d917-1943">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1943">+inf</span></span> | <span data-ttu-id="3d917-1944">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1944">-inf</span></span> | <span data-ttu-id="3d917-1945">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1945">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1946">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1946">+0</span></span>   | <span data-ttu-id="3d917-1947">o</span><span class="sxs-lookup"><span data-stu-id="3d917-1947">y</span></span>    | <span data-ttu-id="3d917-1948">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1948">+0</span></span>   | <span data-ttu-id="3d917-1949">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1949">+0</span></span>   | <span data-ttu-id="3d917-1950">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1950">+inf</span></span> | <span data-ttu-id="3d917-1951">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1951">-inf</span></span> | <span data-ttu-id="3d917-1952">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1952">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1953">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1953">-0</span></span>   | <span data-ttu-id="3d917-1954">o</span><span class="sxs-lookup"><span data-stu-id="3d917-1954">y</span></span>    | <span data-ttu-id="3d917-1955">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-1955">+0</span></span>   | <span data-ttu-id="3d917-1956">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-1956">-0</span></span>   | <span data-ttu-id="3d917-1957">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1957">+inf</span></span> | <span data-ttu-id="3d917-1958">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1958">-inf</span></span> | <span data-ttu-id="3d917-1959">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1959">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1960">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1960">+inf</span></span> | <span data-ttu-id="3d917-1961">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1961">+inf</span></span> | <span data-ttu-id="3d917-1962">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1962">+inf</span></span> | <span data-ttu-id="3d917-1963">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1963">+inf</span></span> | <span data-ttu-id="3d917-1964">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1964">+inf</span></span> | <span data-ttu-id="3d917-1965">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1965">NaN</span></span>  | <span data-ttu-id="3d917-1966">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1966">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1967">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1967">-inf</span></span> | <span data-ttu-id="3d917-1968">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1968">-inf</span></span> | <span data-ttu-id="3d917-1969">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1969">-inf</span></span> | <span data-ttu-id="3d917-1970">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1970">-inf</span></span> | <span data-ttu-id="3d917-1971">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1971">NaN</span></span>  | <span data-ttu-id="3d917-1972">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-1972">-inf</span></span> | <span data-ttu-id="3d917-1973">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1973">NaN</span></span>  | 
   | <span data-ttu-id="3d917-1974">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1974">NaN</span></span>  | <span data-ttu-id="3d917-1975">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1975">NaN</span></span>  | <span data-ttu-id="3d917-1976">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1976">NaN</span></span>  | <span data-ttu-id="3d917-1977">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1977">NaN</span></span>  | <span data-ttu-id="3d917-1978">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1978">NaN</span></span>  | <span data-ttu-id="3d917-1979">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1979">NaN</span></span>  | <span data-ttu-id="3d917-1980">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-1980">NaN</span></span>  | 

*  <span data-ttu-id="3d917-1981">Addition de nombres décimaux :</span><span class="sxs-lookup"><span data-stu-id="3d917-1981">Decimal addition:</span></span>

   ```csharp
   decimal operator +(decimal x, decimal y);
   ```

   <span data-ttu-id="3d917-1982">Si la valeur résultante est trop grande pour être représenté dans le `decimal` format, un `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1982">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="3d917-1983">L’échelle du résultat, avant tout arrondi, est la plus grande des échelles des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-1983">The scale of the result, before any rounding, is the larger of the scales of the two operands.</span></span>

   <span data-ttu-id="3d917-1984">Addition de nombres décimaux est équivalente à l’aide de l’opérateur d’addition de type `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1984">Decimal addition is equivalent to using the addition operator of type `System.Decimal`.</span></span>

*  <span data-ttu-id="3d917-1985">Ajout de l’énumération.</span><span class="sxs-lookup"><span data-stu-id="3d917-1985">Enumeration addition.</span></span> <span data-ttu-id="3d917-1986">Chaque type énumération fournit implicitement suivantes des opérateurs, prédéfinis où `E` est le type enum, et `U` est le type sous-jacent de `E`:</span><span class="sxs-lookup"><span data-stu-id="3d917-1986">Every enumeration type implicitly provides the following predefined operators, where `E` is the enum type, and `U` is the underlying type of `E`:</span></span>

   ```csharp
   E operator +(E x, U y);
   E operator +(U x, E y);
   ```

   <span data-ttu-id="3d917-1987">Au moment de l’exécution, ces opérateurs sont évalués exactement comme `(E)((U)x + (U)y)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1987">At run-time these operators are evaluated exactly as `(E)((U)x + (U)y)`.</span></span>

*  <span data-ttu-id="3d917-1988">Concaténation de chaînes :</span><span class="sxs-lookup"><span data-stu-id="3d917-1988">String concatenation:</span></span>

   ```csharp
   string operator +(string x, string y);
   string operator +(string x, object y);
   string operator +(object x, string y);
   ```

   <span data-ttu-id="3d917-1989">Ces surcharges de la ressource binaire `+` opérateur effectuer la concaténation de chaînes.</span><span class="sxs-lookup"><span data-stu-id="3d917-1989">These overloads of the binary `+` operator perform string concatenation.</span></span> <span data-ttu-id="3d917-1990">Si un opérande de concaténation de chaînes est `null`, une chaîne vide est substituée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1990">If an operand of string concatenation is `null`, an empty string is substituted.</span></span> <span data-ttu-id="3d917-1991">Sinon, n’importe quel argument autre qu’une chaîne est converti en sa représentation de chaîne en appelant virtuel `ToString` méthode héritée du type `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1991">Otherwise, any non-string argument is converted to its string representation by invoking the virtual `ToString` method inherited from type `object`.</span></span> <span data-ttu-id="3d917-1992">Si `ToString` retourne `null`, une chaîne vide est substituée.</span><span class="sxs-lookup"><span data-stu-id="3d917-1992">If `ToString` returns `null`, an empty string is substituted.</span></span>

   ```csharp
   using System;
   
   class Test
   {
       static void Main() {
           string s = null;
           Console.WriteLine("s = >" + s + "<");        // displays s = ><
           int i = 1;
           Console.WriteLine("i = " + i);               // displays i = 1
           float f = 1.2300E+15F;
           Console.WriteLine("f = " + f);               // displays f = 1.23E+15
           decimal d = 2.900m;
           Console.WriteLine("d = " + d);               // displays d = 2.900
       }
   }
   ```

   Le résultat de l’opérateur de concaténation de chaîne est une chaîne qui se compose des caractères de l’opérande gauche suivi par les caractères de l’opérande de droite. L’opérateur de concaténation de chaîne ne retourne jamais un `null` valeur. <span data-ttu-id="3d917-1995">Un `System.OutOfMemoryException` peut être levée si n’est pas suffisamment de mémoire disponible pour l’allocation de la chaîne résultante.</span><span class="sxs-lookup"><span data-stu-id="3d917-1995">A `System.OutOfMemoryException` may be thrown if there is not enough memory available to allocate the resulting string.</span></span>

*  <span data-ttu-id="3d917-1996">Combinaison de délégués.</span><span class="sxs-lookup"><span data-stu-id="3d917-1996">Delegate combination.</span></span> <span data-ttu-id="3d917-1997">Chaque type délégué fournit implicitement l’opérateur prédéfini suivant, où `D` est le type délégué :</span><span class="sxs-lookup"><span data-stu-id="3d917-1997">Every delegate type implicitly provides the following predefined operator, where `D` is the delegate type:</span></span>

   ```csharp
   D operator +(D x, D y);
   ```

   <span data-ttu-id="3d917-1998">Le fichier binaire `+` opérateur effectue la combinaison de délégués lorsque les deux opérandes sont d’un type délégué `D`.</span><span class="sxs-lookup"><span data-stu-id="3d917-1998">The binary `+` operator performs delegate combination when both operands are of some delegate type `D`.</span></span> <span data-ttu-id="3d917-1999">(Si les opérandes sont de types délégués différents, une erreur au moment de la liaison se produit.) Si le premier opérande est `null`, le résultat de l’opération est la valeur du deuxième opérande (même si c’est également `null`).</span><span class="sxs-lookup"><span data-stu-id="3d917-1999">(If the operands have different delegate types, a binding-time error occurs.) If the first operand is `null`, the result of the operation is the value of the second operand (even if that is also `null`).</span></span> <span data-ttu-id="3d917-2000">Sinon, si le second opérande est `null`, le résultat de l’opération est la valeur du premier opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-2000">Otherwise, if the second operand is `null`, then the result of the operation is the value of the first operand.</span></span> <span data-ttu-id="3d917-2001">Sinon, le résultat de l’opération est un nouvelle instance de délégué qui, lors de l’appelé, appelle le premier opérande et appelle ensuite le second opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-2001">Otherwise, the result of the operation is a new delegate instance that, when invoked, invokes the first operand and then invokes the second operand.</span></span> <span data-ttu-id="3d917-2002">Pour obtenir des exemples de combinaison de délégués, consultez [opérateur de soustraction](expressions.md#subtraction-operator) et [appel de délégué](delegates.md#delegate-invocation).</span><span class="sxs-lookup"><span data-stu-id="3d917-2002">For examples of delegate combination, see [Subtraction operator](expressions.md#subtraction-operator) and [Delegate invocation](delegates.md#delegate-invocation).</span></span> <span data-ttu-id="3d917-2003">Dans la mesure où `System.Delegate` n’est pas un type délégué, `operator`  `+` n’est pas défini pour elle.</span><span class="sxs-lookup"><span data-stu-id="3d917-2003">Since `System.Delegate` is not a delegate type, `operator` `+` is not defined for it.</span></span>

### <a name="subtraction-operator"></a><span data-ttu-id="3d917-2004">Opérateur de soustraction</span><span class="sxs-lookup"><span data-stu-id="3d917-2004">Subtraction operator</span></span>

<span data-ttu-id="3d917-2005">Pour une opération du formulaire `x - y`, opérateur binaire de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-2005">For an operation of the form `x - y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-2006">Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2006">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="3d917-2007">Les opérateurs de soustraction prédéfinis sont répertoriés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3d917-2007">The predefined subtraction operators are listed below.</span></span> <span data-ttu-id="3d917-2008">Les opérateurs tous soustraire `y` de `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2008">The operators all subtract `y` from `x`.</span></span>

*  <span data-ttu-id="3d917-2009">Soustraction de nombres entiers :</span><span class="sxs-lookup"><span data-stu-id="3d917-2009">Integer subtraction:</span></span>

   ```csharp
   int operator -(int x, int y);
   uint operator -(uint x, uint y);
   long operator -(long x, long y);
   ulong operator -(ulong x, ulong y);
   ```

   <span data-ttu-id="3d917-2010">Dans un `checked` contexte, si la différence est en dehors de la plage du type du résultat, un `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-2010">In a `checked` context, if the difference is outside the range of the result type, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="3d917-2011">Dans un `unchecked` contexte, dépassements de capacité ne sont pas signalés et n’importe quel bits de poids fort significatifs en dehors de la plage du type du résultat sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="3d917-2011">In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.</span></span>

*  <span data-ttu-id="3d917-2012">Soustraction de nombres à virgule flottante :</span><span class="sxs-lookup"><span data-stu-id="3d917-2012">Floating-point subtraction:</span></span>

   ```csharp
   float operator -(float x, float y);
   double operator -(double x, double y);
   ```

   <span data-ttu-id="3d917-2013">La différence est calculée selon les règles de la norme IEEE 754 arithmétique.</span><span class="sxs-lookup"><span data-stu-id="3d917-2013">The difference is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="3d917-2014">Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies, les zéros non significatifs, les valeurs infinies et NaN.</span><span class="sxs-lookup"><span data-stu-id="3d917-2014">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaNs.</span></span> <span data-ttu-id="3d917-2015">Dans la table, `x` et `y` sont des valeurs finies, et `z` est le résultat de `x - y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2015">In the table, `x` and `y` are nonzero finite values, and `z` is the result of `x - y`.</span></span> <span data-ttu-id="3d917-2016">Si `x` et `y` sont égales, `z` est un zéro positif.</span><span class="sxs-lookup"><span data-stu-id="3d917-2016">If `x` and `y` are equal, `z` is positive zero.</span></span> <span data-ttu-id="3d917-2017">Si `x - y` est trop grand pour être représenté dans le type de destination, `z` est un infini ayant le même signe que `x - y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2017">If `x - y` is too large to represent in the destination type, `z` is an infinity with the same sign as `x - y`.</span></span>

   |      |      |      |      |      |      |     |
   |:----:|:----:|:----:|:----:|:----:|:----:|:---:|
   |      | <span data-ttu-id="3d917-2018">o</span><span class="sxs-lookup"><span data-stu-id="3d917-2018">y</span></span>    | <span data-ttu-id="3d917-2019">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-2019">+0</span></span>   | <span data-ttu-id="3d917-2020">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-2020">-0</span></span>   | <span data-ttu-id="3d917-2021">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2021">+inf</span></span> | <span data-ttu-id="3d917-2022">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2022">-inf</span></span> | <span data-ttu-id="3d917-2023">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2023">NaN</span></span> | 
   | <span data-ttu-id="3d917-2024">x</span><span class="sxs-lookup"><span data-stu-id="3d917-2024">x</span></span>    | <span data-ttu-id="3d917-2025">e</span><span class="sxs-lookup"><span data-stu-id="3d917-2025">z</span></span>    | <span data-ttu-id="3d917-2026">x</span><span class="sxs-lookup"><span data-stu-id="3d917-2026">x</span></span>    | <span data-ttu-id="3d917-2027">x</span><span class="sxs-lookup"><span data-stu-id="3d917-2027">x</span></span>    | <span data-ttu-id="3d917-2028">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2028">-inf</span></span> | <span data-ttu-id="3d917-2029">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2029">+inf</span></span> | <span data-ttu-id="3d917-2030">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2030">NaN</span></span> | 
   | <span data-ttu-id="3d917-2031">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-2031">+0</span></span>   | <span data-ttu-id="3d917-2032">-y</span><span class="sxs-lookup"><span data-stu-id="3d917-2032">-y</span></span>   | <span data-ttu-id="3d917-2033">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-2033">+0</span></span>   | <span data-ttu-id="3d917-2034">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-2034">+0</span></span>   | <span data-ttu-id="3d917-2035">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2035">-inf</span></span> | <span data-ttu-id="3d917-2036">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2036">+inf</span></span> | <span data-ttu-id="3d917-2037">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2037">NaN</span></span> | 
   | <span data-ttu-id="3d917-2038">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-2038">-0</span></span>   | <span data-ttu-id="3d917-2039">-y</span><span class="sxs-lookup"><span data-stu-id="3d917-2039">-y</span></span>   | <span data-ttu-id="3d917-2040">-0</span><span class="sxs-lookup"><span data-stu-id="3d917-2040">-0</span></span>   | <span data-ttu-id="3d917-2041">+0</span><span class="sxs-lookup"><span data-stu-id="3d917-2041">+0</span></span>   | <span data-ttu-id="3d917-2042">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2042">-inf</span></span> | <span data-ttu-id="3d917-2043">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2043">+inf</span></span> | <span data-ttu-id="3d917-2044">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2044">NaN</span></span> | 
   | <span data-ttu-id="3d917-2045">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2045">+inf</span></span> | <span data-ttu-id="3d917-2046">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2046">+inf</span></span> | <span data-ttu-id="3d917-2047">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2047">+inf</span></span> | <span data-ttu-id="3d917-2048">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2048">+inf</span></span> | <span data-ttu-id="3d917-2049">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2049">NaN</span></span>  | <span data-ttu-id="3d917-2050">+inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2050">+inf</span></span> | <span data-ttu-id="3d917-2051">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2051">NaN</span></span> | 
   | <span data-ttu-id="3d917-2052">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2052">-inf</span></span> | <span data-ttu-id="3d917-2053">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2053">-inf</span></span> | <span data-ttu-id="3d917-2054">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2054">-inf</span></span> | <span data-ttu-id="3d917-2055">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2055">-inf</span></span> | <span data-ttu-id="3d917-2056">-inf</span><span class="sxs-lookup"><span data-stu-id="3d917-2056">-inf</span></span> | <span data-ttu-id="3d917-2057">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2057">NaN</span></span>  | <span data-ttu-id="3d917-2058">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2058">NaN</span></span> | 
   | <span data-ttu-id="3d917-2059">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2059">NaN</span></span>  | <span data-ttu-id="3d917-2060">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2060">NaN</span></span>  | <span data-ttu-id="3d917-2061">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2061">NaN</span></span>  | <span data-ttu-id="3d917-2062">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2062">NaN</span></span>  | <span data-ttu-id="3d917-2063">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2063">NaN</span></span>  | <span data-ttu-id="3d917-2064">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2064">NaN</span></span>  | <span data-ttu-id="3d917-2065">NaN</span><span class="sxs-lookup"><span data-stu-id="3d917-2065">NaN</span></span> | 

*  <span data-ttu-id="3d917-2066">Soustraction de nombres décimaux :</span><span class="sxs-lookup"><span data-stu-id="3d917-2066">Decimal subtraction:</span></span>

   ```csharp
   decimal operator -(decimal x, decimal y);
   ```

   <span data-ttu-id="3d917-2067">Si la valeur résultante est trop grande pour être représenté dans le `decimal` format, un `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-2067">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="3d917-2068">L’échelle du résultat, avant tout arrondi, est la plus grande des échelles des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2068">The scale of the result, before any rounding, is the larger of the scales of the two operands.</span></span>

   <span data-ttu-id="3d917-2069">Soustraction de nombres décimaux est équivalente à l’aide de l’opérateur de soustraction de type `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2069">Decimal subtraction is equivalent to using the subtraction operator of type `System.Decimal`.</span></span>

*  <span data-ttu-id="3d917-2070">Soustraction de l’énumération.</span><span class="sxs-lookup"><span data-stu-id="3d917-2070">Enumeration subtraction.</span></span> <span data-ttu-id="3d917-2071">Chaque type énumération fournit implicitement l’opérateur prédéfini suivant, où `E` est le type enum, et `U` est le type sous-jacent de `E`:</span><span class="sxs-lookup"><span data-stu-id="3d917-2071">Every enumeration type implicitly provides the following predefined operator, where `E` is the enum type, and `U` is the underlying type of `E`:</span></span>

   ```csharp
   U operator -(E x, E y);
   ```

   <span data-ttu-id="3d917-2072">Cet opérateur est évalué exactement comme `(U)((U)x - (U)y)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2072">This operator is evaluated exactly as `(U)((U)x - (U)y)`.</span></span> <span data-ttu-id="3d917-2073">En d’autres termes, l’opérateur calcule la différence entre des valeurs ordinales des `x` et `y`, et le type du résultat est le type sous-jacent de l’énumération.</span><span class="sxs-lookup"><span data-stu-id="3d917-2073">In other words, the operator computes the difference between the ordinal values of `x` and `y`, and the type of the result is the underlying type of the enumeration.</span></span>

   ```csharp
   E operator -(E x, U y);
   ```

   <span data-ttu-id="3d917-2074">Cet opérateur est évalué exactement comme `(E)((U)x - y)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2074">This operator is evaluated exactly as `(E)((U)x - y)`.</span></span> <span data-ttu-id="3d917-2075">En d’autres termes, l’opérateur soustrait une valeur à partir du type sous-jacent de l’énumération, on obtient une valeur de l’énumération.</span><span class="sxs-lookup"><span data-stu-id="3d917-2075">In other words, the operator subtracts a value from the underlying type of the enumeration, yielding a value of the enumeration.</span></span>

*  <span data-ttu-id="3d917-2076">Suppression de délégué.</span><span class="sxs-lookup"><span data-stu-id="3d917-2076">Delegate removal.</span></span> <span data-ttu-id="3d917-2077">Chaque type délégué fournit implicitement l’opérateur prédéfini suivant, où `D` est le type délégué :</span><span class="sxs-lookup"><span data-stu-id="3d917-2077">Every delegate type implicitly provides the following predefined operator, where `D` is the delegate type:</span></span>

   ```csharp
   D operator -(D x, D y);
   ```

   <span data-ttu-id="3d917-2078">Le fichier binaire `-` opérateur effectue la suppression de délégué lorsque les deux opérandes sont d’un type délégué `D`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2078">The binary `-` operator performs delegate removal when both operands are of some delegate type `D`.</span></span> <span data-ttu-id="3d917-2079">Si les opérandes ont des types délégués différents, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-2079">If the operands have different delegate types, a binding-time error occurs.</span></span> <span data-ttu-id="3d917-2080">Si le premier opérande est `null`, le résultat de l’opération est `null`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2080">If the first operand is `null`, the result of the operation is `null`.</span></span> <span data-ttu-id="3d917-2081">Sinon, si le second opérande est `null`, le résultat de l’opération est la valeur du premier opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-2081">Otherwise, if the second operand is `null`, then the result of the operation is the value of the first operand.</span></span> <span data-ttu-id="3d917-2082">Sinon, les deux opérandes représentent des listes d’appel ([déclarations Delegate](delegates.md#delegate-declarations)) ayant une ou plusieurs entrées et le résultat est une nouvelle liste d’appel qui se compose de la liste du premier opérande avec les entrées du deuxième opérande retirées Il, fourni la liste du deuxième opérande est une sous-liste contiguë correcte de la première.</span><span class="sxs-lookup"><span data-stu-id="3d917-2082">Otherwise, both operands represent invocation lists ([Delegate declarations](delegates.md#delegate-declarations)) having one or more entries, and the result is a new invocation list consisting of the first operand's list with the second operand's entries removed from it, provided the second operand's list is a proper contiguous sublist of the first's.</span></span>     <span data-ttu-id="3d917-2083">(Pour déterminer l’égalité sous-liste, les entrées correspondantes sont comparées comme pour l’opérateur d’égalité de délégué ([déléguer des opérateurs d’égalité](expressions.md#delegate-equality-operators)).) Sinon, le résultat est la valeur de l’opérande gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-2083">(To determine sublist equality, corresponding entries are compared as for the delegate equality operator ([Delegate equality operators](expressions.md#delegate-equality-operators)).) Otherwise, the result is the value of the left operand.</span></span> <span data-ttu-id="3d917-2084">Aucune des listes des opérandes est modifiée dans le processus.</span><span class="sxs-lookup"><span data-stu-id="3d917-2084">Neither of the operands' lists is changed in the process.</span></span> <span data-ttu-id="3d917-2085">Si la liste du deuxième opérande correspond à plusieurs sous-listes d’entrées contiguës dans la liste du premier opérande, la sous-liste correspondante la plus à droite d’entrées contiguës est supprimée.</span><span class="sxs-lookup"><span data-stu-id="3d917-2085">If the second operand's list matches multiple sublists of contiguous entries in the first operand's list, the right-most matching sublist of contiguous entries is removed.</span></span> <span data-ttu-id="3d917-2086">Si les résultats de la suppression d’une liste vide, le résultat est `null`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2086">If removal results in an empty list, the result is `null`.</span></span> <span data-ttu-id="3d917-2087">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3d917-2087">For example:</span></span>

   ```csharp
   delegate void D(int x);
   
   class C
   {
       public static void M1(int i) { /* ... */ }
       public static void M2(int i) { /* ... */ }
   }

   class Test
   {
       static void Main() { 
           D cd1 = new D(C.M1);
           D cd2 = new D(C.M2);
           D cd3 = cd1 + cd2 + cd2 + cd1;   // M1 + M2 + M2 + M1
           cd3 -= cd1;                      // => M1 + M2 + M2
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd1 + cd2;                // => M2 + M1
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd2 + cd2;                // => M1 + M1
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd2 + cd1;                // => M1 + M2
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd1 + cd1;                // => M1 + M2 + M2 + M1
       }
   }
   ```

## <a name="shift-operators"></a><span data-ttu-id="3d917-2088">Opérateurs de décalage</span><span class="sxs-lookup"><span data-stu-id="3d917-2088">Shift operators</span></span>

<span data-ttu-id="3d917-2089">Le `<<` et `>>` opérateurs sont utilisés pour effectuer des opérations de décalage de bits.</span><span class="sxs-lookup"><span data-stu-id="3d917-2089">The `<<` and `>>` operators are used to perform bit shifting operations.</span></span>

```antlr
shift_expression
    : additive_expression
    | shift_expression '<<' additive_expression
    | shift_expression right_shift additive_expression
    ;
```

<span data-ttu-id="3d917-2090">Si un opérande d’un *shift_expression* a le type de compilation `dynamic`, puis l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2090">If an operand of a *shift_expression* has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="3d917-2091">Dans ce cas le type de compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution à l’aide du type au moment de l’exécution de ces opérandes qui ont le type de compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2091">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="3d917-2092">Pour une opération du formulaire `x << count` ou `x >> count`, opérateur binaire de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-2092">For an operation of the form `x << count` or `x >> count`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-2093">Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2093">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="3d917-2094">Lorsque vous déclarez un opérateur de décalage surchargé, le type du premier opérande doit toujours être la classe ou structure qui contient la déclaration d’opérateur et le type du second opérande doit toujours être `int`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2094">When declaring an overloaded shift operator, the type of the first operand must always be the class or struct containing the operator declaration, and the type of the second operand must always be `int`.</span></span>

<span data-ttu-id="3d917-2095">Les opérateurs de décalage prédéfinis sont répertoriés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3d917-2095">The predefined shift operators are listed below.</span></span>

*  <span data-ttu-id="3d917-2096">Déplacer vers la gauche :</span><span class="sxs-lookup"><span data-stu-id="3d917-2096">Shift left:</span></span>

   ```csharp
   int operator <<(int x, int count);
   uint operator <<(uint x, int count);
   long operator <<(long x, int count);
   ulong operator <<(ulong x, int count);
   ```

   <span data-ttu-id="3d917-2097">Le `<<` opérateur shifts `x` gauche d’un nombre de bits calculé comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3d917-2097">The `<<` operator shifts `x` left by a number of bits computed as described below.</span></span>

   <span data-ttu-id="3d917-2098">Les bits de poids fort en dehors de la plage du type de résultat de `x` sont ignorées, les bits restants sont décalés à gauche, et les positions de bits vides de poids faible sont définies à zéro.</span><span class="sxs-lookup"><span data-stu-id="3d917-2098">The high-order bits outside the range of the result type of `x` are discarded, the remaining bits are shifted left, and the low-order empty bit positions are set to zero.</span></span>

*  <span data-ttu-id="3d917-2099">Décalage à droite :</span><span class="sxs-lookup"><span data-stu-id="3d917-2099">Shift right:</span></span>

   ```csharp
   int operator >>(int x, int count);
   uint operator >>(uint x, int count);
   long operator >>(long x, int count);
   ulong operator >>(ulong x, int count);
   ```

   <span data-ttu-id="3d917-2100">Le `>>` opérateur équipes `x` droite d’un nombre de bits calculé comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3d917-2100">The `>>` operator shifts `x` right by a number of bits computed as described below.</span></span>

   <span data-ttu-id="3d917-2101">Lorsque `x` est de type `int` ou `long`, les bits de poids faible de `x` sont ignorés, les bits restants sont décalés vers la droite, et les positions de bits vides de poids fort sont définies sur zéro si `x` est négative et défini 1 se `x` est un nombre négatif.</span><span class="sxs-lookup"><span data-stu-id="3d917-2101">When `x` is of type `int` or `long`, the low-order bits of `x` are discarded, the remaining bits are shifted right, and the high-order empty bit positions are set to zero if `x` is non-negative and set to one if `x` is negative.</span></span>

   <span data-ttu-id="3d917-2102">Lorsque `x` est de type `uint` ou `ulong`, les bits de poids faible de `x` sont ignorées, les bits restants sont décalés vers la droite, et les positions de bits vides de poids fort sont définies à zéro.</span><span class="sxs-lookup"><span data-stu-id="3d917-2102">When `x` is of type `uint` or `ulong`, the low-order bits of `x` are discarded, the remaining bits are shifted right, and the high-order empty bit positions are set to zero.</span></span>

<span data-ttu-id="3d917-2103">Pour les opérateurs prédéfinis, le nombre de bits de décalage est calculé comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-2103">For the predefined operators, the number of bits to shift is computed as follows:</span></span>

*  <span data-ttu-id="3d917-2104">Lorsque le type de `x` est `int` ou `uint`, la valeur du décalage est donnée par les cinq bits de poids faible de `count`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2104">When the type of `x` is `int` or `uint`, the shift count is given by the low-order five bits of `count`.</span></span> <span data-ttu-id="3d917-2105">En d’autres termes, la valeur du décalage est calculée à partir de `count & 0x1F`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2105">In other words, the shift count is computed from `count & 0x1F`.</span></span>
*  <span data-ttu-id="3d917-2106">Lorsque le type de `x` est `long` ou `ulong`, la valeur du décalage est donnée par les six bits de poids faible de `count`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2106">When the type of `x` is `long` or `ulong`, the shift count is given by the low-order six bits of `count`.</span></span> <span data-ttu-id="3d917-2107">En d’autres termes, la valeur du décalage est calculée à partir de `count & 0x3F`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2107">In other words, the shift count is computed from `count & 0x3F`.</span></span>

<span data-ttu-id="3d917-2108">Si le compteur de décalage est égal à zéro, les opérateurs de décalage doit simplement retournent la valeur de `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2108">If the resulting shift count is zero, the shift operators simply return the value of `x`.</span></span>

<span data-ttu-id="3d917-2109">Opérations de décalage jamais provoquer des dépassements de capacité et produisent les mêmes résultats dans `checked` et `unchecked` contextes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2109">Shift operations never cause overflows and produce the same results in `checked` and `unchecked` contexts.</span></span>

<span data-ttu-id="3d917-2110">Lorsque l’opérande gauche de la `>>` opérateur est d’un type intégral signé, l’opérateur effectue un décalage arithmétique vers la droite dans laquelle la valeur de bit le plus significatif (le bit de signe) de l’opérande est propagée vers les positions de bits vides de poids fort.</span><span class="sxs-lookup"><span data-stu-id="3d917-2110">When the left operand of the `>>` operator is of a signed integral type, the operator performs an arithmetic shift right wherein the value of the most significant bit (the sign bit) of the operand is propagated to the high-order empty bit positions.</span></span> <span data-ttu-id="3d917-2111">Lorsque l’opérande gauche de la `>>` opérateur est de type intégral non signé, l’opérateur effectue un décalage logique droite fort les positions des bits vides de poids fort sont toujours mises à zéro.</span><span class="sxs-lookup"><span data-stu-id="3d917-2111">When the left operand of the `>>` operator is of an unsigned integral type, the operator performs a logical shift right wherein high-order empty bit positions are always set to zero.</span></span> <span data-ttu-id="3d917-2112">Pour effectuer l’opération inverse de qui déduit le type d’opérande, des casts explicites peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="3d917-2112">To perform the opposite operation of that inferred from the operand type, explicit casts can be used.</span></span> <span data-ttu-id="3d917-2113">Par exemple, si `x` est une variable de type `int`, l’opération `unchecked((int)((uint)x >> y))` effectue un décalage logique droite de `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2113">For example, if `x` is a variable of type `int`, the operation `unchecked((int)((uint)x >> y))` performs a logical shift right of `x`.</span></span>

## <a name="relational-and-type-testing-operators"></a><span data-ttu-id="3d917-2114">Opérateurs relationnels et de test de type</span><span class="sxs-lookup"><span data-stu-id="3d917-2114">Relational and type-testing operators</span></span>

<span data-ttu-id="3d917-2115">Le `==`, `!=`, `<`, `>`, `<=`, `>=`, `is` et `as` sont appelés les opérateurs relationnels et de test de type.</span><span class="sxs-lookup"><span data-stu-id="3d917-2115">The `==`, `!=`, `<`, `>`, `<=`, `>=`, `is` and `as` operators are called the relational and type-testing operators.</span></span>

```antlr
relational_expression
    : shift_expression
    | relational_expression '<' shift_expression
    | relational_expression '>' shift_expression
    | relational_expression '<=' shift_expression
    | relational_expression '>=' shift_expression
    | relational_expression 'is' type
    | relational_expression 'as' type
    ;

equality_expression
    : relational_expression
    | equality_expression '==' relational_expression
    | equality_expression '!=' relational_expression
    ;
```

<span data-ttu-id="3d917-2116">Le `is` opérateur est décrit dans [l’opérateur n’est](expressions.md#the-is-operator) et le `as` opérateur est décrit dans [l’opérateur as](expressions.md#the-as-operator).</span><span class="sxs-lookup"><span data-stu-id="3d917-2116">The `is` operator is described in [The is operator](expressions.md#the-is-operator) and the `as` operator is described in [The as operator](expressions.md#the-as-operator).</span></span>

<span data-ttu-id="3d917-2117">Le `==`, `!=`, `<`, `>`, `<=` et `>=` opérateurs sont ***opérateurs de comparaison***.</span><span class="sxs-lookup"><span data-stu-id="3d917-2117">The `==`, `!=`, `<`, `>`, `<=` and `>=` operators are ***comparison operators***.</span></span>

<span data-ttu-id="3d917-2118">Si un opérande d’un opérateur de comparaison a le type de compilation `dynamic`, puis l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2118">If an operand of a comparison operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="3d917-2119">Dans ce cas le type de compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution à l’aide du type au moment de l’exécution de ces opérandes qui ont le type de compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2119">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="3d917-2120">Pour une opération du formulaire `x` *op* `y`, où *op* est un opérateur de comparaison, la résolution de surcharge ([opérateur binaire surcharge résolution](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-2120">For an operation of the form `x` *op* `y`, where *op* is a comparison operator, overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-2121">Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2121">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="3d917-2122">Les opérateurs de comparaison prédéfinis sont décrits dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2122">The predefined comparison operators are described in the following sections.</span></span> <span data-ttu-id="3d917-2123">Tous les opérateurs de comparaison prédéfinis retournent un résultat de type `bool`, comme décrit dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="3d917-2123">All predefined comparison operators return a result of type `bool`, as described in the following table.</span></span>


| <span data-ttu-id="3d917-2124">__Opération__</span><span class="sxs-lookup"><span data-stu-id="3d917-2124">__Operation__</span></span> | <span data-ttu-id="3d917-2125">__Résultat__</span><span class="sxs-lookup"><span data-stu-id="3d917-2125">__Result__</span></span>                                                       |
|---------------|------------------------------------------------------------------|
| `x == y`      | <span data-ttu-id="3d917-2126">`true` Si `x` est égal à `y`, `false` sinon</span><span class="sxs-lookup"><span data-stu-id="3d917-2126">`true` if `x` is equal to `y`, `false` otherwise</span></span>                 | 
| `x != y`      | <span data-ttu-id="3d917-2127">`true` Si `x` n’est pas égal à `y`, `false` sinon</span><span class="sxs-lookup"><span data-stu-id="3d917-2127">`true` if `x` is not equal to `y`, `false` otherwise</span></span>             | 
| `x < y`       | <span data-ttu-id="3d917-2128">`true` Si `x` est inférieure à `y`, `false` sinon</span><span class="sxs-lookup"><span data-stu-id="3d917-2128">`true` if `x` is less than `y`, `false` otherwise</span></span>                | 
| `x > y`       | <span data-ttu-id="3d917-2129">`true` Si `x` est supérieur à `y`, `false` sinon</span><span class="sxs-lookup"><span data-stu-id="3d917-2129">`true` if `x` is greater than `y`, `false` otherwise</span></span>             | 
| `x <= y`      | <span data-ttu-id="3d917-2130">`true` Si `x` est inférieure ou égale à `y`, `false` sinon</span><span class="sxs-lookup"><span data-stu-id="3d917-2130">`true` if `x` is less than or equal to `y`, `false` otherwise</span></span>    | 
| `x >= y`      | <span data-ttu-id="3d917-2131">`true` Si `x` est supérieur ou égal à `y`, `false` sinon</span><span class="sxs-lookup"><span data-stu-id="3d917-2131">`true` if `x` is greater than or equal to `y`, `false` otherwise</span></span> | 

### <a name="integer-comparison-operators"></a><span data-ttu-id="3d917-2132">Opérateurs de comparaison d’entiers</span><span class="sxs-lookup"><span data-stu-id="3d917-2132">Integer comparison operators</span></span>

<span data-ttu-id="3d917-2133">Les opérateurs de comparaison d’entiers prédéfinis sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-2133">The predefined integer comparison operators are:</span></span>
```csharp
bool operator ==(int x, int y);
bool operator ==(uint x, uint y);
bool operator ==(long x, long y);
bool operator ==(ulong x, ulong y);

bool operator !=(int x, int y);
bool operator !=(uint x, uint y);
bool operator !=(long x, long y);
bool operator !=(ulong x, ulong y);

bool operator <(int x, int y);
bool operator <(uint x, uint y);
bool operator <(long x, long y);
bool operator <(ulong x, ulong y);

bool operator >(int x, int y);
bool operator >(uint x, uint y);
bool operator >(long x, long y);
bool operator >(ulong x, ulong y);

bool operator <=(int x, int y);
bool operator <=(uint x, uint y);
bool operator <=(long x, long y);
bool operator <=(ulong x, ulong y);

bool operator >=(int x, int y);
bool operator >=(uint x, uint y);
bool operator >=(long x, long y);
bool operator >=(ulong x, ulong y);
```

<span data-ttu-id="3d917-2134">Chacun de ces opérateurs compare les valeurs numériques des deux opérandes entiers et retourne un `bool` valeur qui indique si la relation particulière est `true` ou `false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2134">Each of these operators compares the numeric values of the two integer operands and returns a `bool` value that indicates whether the particular relation is `true` or `false`.</span></span>

### <a name="floating-point-comparison-operators"></a><span data-ttu-id="3d917-2135">Opérateurs de comparaison à virgule flottante</span><span class="sxs-lookup"><span data-stu-id="3d917-2135">Floating-point comparison operators</span></span>

<span data-ttu-id="3d917-2136">Les opérateurs de comparaison à virgule flottante prédéfinis sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-2136">The predefined floating-point comparison operators are:</span></span>
```csharp
bool operator ==(float x, float y);
bool operator ==(double x, double y);

bool operator !=(float x, float y);
bool operator !=(double x, double y);

bool operator <(float x, float y);
bool operator <(double x, double y);

bool operator >(float x, float y);
bool operator >(double x, double y);

bool operator <=(float x, float y);
bool operator <=(double x, double y);

bool operator >=(float x, float y);
bool operator >=(double x, double y);
```

<span data-ttu-id="3d917-2137">Les opérateurs comparent les opérandes selon les règles de la norme IEEE 754 :</span><span class="sxs-lookup"><span data-stu-id="3d917-2137">The operators compare the operands according to the rules of the IEEE 754 standard:</span></span>

*  <span data-ttu-id="3d917-2138">Si des opérandes sont une NaN, le résultat est `false` pour tous les opérateurs à l’exception `!=`, pour lequel le résultat est `true`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2138">If either operand is NaN, the result is `false` for all operators except `!=`, for which the result is `true`.</span></span> <span data-ttu-id="3d917-2139">Pour les deux opérandes, `x != y` produit toujours le même résultat que `!(x == y)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2139">For any two operands, `x != y` always produces the same result as `!(x == y)`.</span></span> <span data-ttu-id="3d917-2140">Toutefois, quand un ou deux opérandes sont des NaN, la `<`, `>`, `<=`, et `>=` opérateurs ne produisent pas les mêmes résultats que la négation logique de l’opérateur opposé.</span><span class="sxs-lookup"><span data-stu-id="3d917-2140">However, when one or both operands are NaN, the `<`, `>`, `<=`, and `>=` operators do not produce the same results as the logical negation of the opposite operator.</span></span> <span data-ttu-id="3d917-2141">Par exemple, si un de `x` et `y` est NaN, puis `x < y` est `false`, mais `!(x >= y)` est `true`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2141">For example, if either of `x` and `y` is NaN, then `x < y` is `false`, but `!(x >= y)` is `true`.</span></span>
*  <span data-ttu-id="3d917-2142">Lorsque aucun des opérandes sont une NaN, les opérateurs comparent les valeurs des deux opérandes à virgule flottante en ce qui concerne l’ordre</span><span class="sxs-lookup"><span data-stu-id="3d917-2142">When neither operand is NaN, the operators compare the values of the two floating-point operands with respect to the ordering</span></span>

   ```
   -inf < -max < ... < -min < -0.0 == +0.0 < +min < ... < +max < +inf
   ```

   <span data-ttu-id="3d917-2143">où `min` et `max` sont les plus grandes et positif finis valeurs qui peuvent être représentés dans le format à virgule flottante donné.</span><span class="sxs-lookup"><span data-stu-id="3d917-2143">where `min` and `max` are the smallest and largest positive finite values that can be represented in the given floating-point format.</span></span> <span data-ttu-id="3d917-2144">Les effets notables de cet ordre sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-2144">Notable effects of this ordering are:</span></span>
   * <span data-ttu-id="3d917-2145">Les zéros non significatifs positifs et négatifs sont considérées comme égales.</span><span class="sxs-lookup"><span data-stu-id="3d917-2145">Negative and positive zeros are considered equal.</span></span>
   * <span data-ttu-id="3d917-2146">Un infini négatif est considéré comme inférieur à toutes les autres valeurs, mais égal à un autre infini négatif.</span><span class="sxs-lookup"><span data-stu-id="3d917-2146">A negative infinity is considered less than all other values, but equal to another negative infinity.</span></span>
   * <span data-ttu-id="3d917-2147">Un infini positif est considéré comme supérieur à toutes les autres valeurs, mais il est égal à un autre infini positif.</span><span class="sxs-lookup"><span data-stu-id="3d917-2147">A positive infinity is considered greater than all other values, but equal to another positive infinity.</span></span>

### <a name="decimal-comparison-operators"></a><span data-ttu-id="3d917-2148">Opérateurs de comparaison de nombres décimaux</span><span class="sxs-lookup"><span data-stu-id="3d917-2148">Decimal comparison operators</span></span>

<span data-ttu-id="3d917-2149">Les opérateurs de comparaison de décimaux prédéfinis sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-2149">The predefined decimal comparison operators are:</span></span>
```csharp
bool operator ==(decimal x, decimal y);
bool operator !=(decimal x, decimal y);
bool operator <(decimal x, decimal y);
bool operator >(decimal x, decimal y);
bool operator <=(decimal x, decimal y);
bool operator >=(decimal x, decimal y);
```

<span data-ttu-id="3d917-2150">Chacun de ces opérateurs compare les valeurs numériques des deux opérandes décimales et retourne un `bool` valeur qui indique si la relation particulière est `true` ou `false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2150">Each of these operators compares the numeric values of the two decimal operands and returns a `bool` value that indicates whether the particular relation is `true` or `false`.</span></span> <span data-ttu-id="3d917-2151">Chaque comparaison de nombres décimaux est équivalente à l’aide de l’opérateur d’égalité de type ou relationnel correspondant `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2151">Each decimal comparison is equivalent to using the corresponding relational or equality operator of type `System.Decimal`.</span></span>

### <a name="boolean-equality-operators"></a><span data-ttu-id="3d917-2152">Opérateurs d’égalité booléenne</span><span class="sxs-lookup"><span data-stu-id="3d917-2152">Boolean equality operators</span></span>

<span data-ttu-id="3d917-2153">Les opérateurs d’égalité booléenne prédéfinis sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-2153">The predefined boolean equality operators are:</span></span>
```csharp
bool operator ==(bool x, bool y);
bool operator !=(bool x, bool y);
```

<span data-ttu-id="3d917-2154">Le résultat de `==` est `true` si les deux `x` et `y` sont `true` ou si les deux `x` et `y` sont `false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2154">The result of `==` is `true` if both `x` and `y` are `true` or if both `x` and `y` are `false`.</span></span> <span data-ttu-id="3d917-2155">Sinon, le résultat est `false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2155">Otherwise, the result is `false`.</span></span>

<span data-ttu-id="3d917-2156">Le résultat de `!=` est `false` si les deux `x` et `y` sont `true` ou si les deux `x` et `y` sont `false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2156">The result of `!=` is `false` if both `x` and `y` are `true` or if both `x` and `y` are `false`.</span></span> <span data-ttu-id="3d917-2157">Sinon, le résultat est `true`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2157">Otherwise, the result is `true`.</span></span> <span data-ttu-id="3d917-2158">Lorsque les opérandes sont de type `bool`, le `!=` opérateur génère le même résultat que la `^` opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2158">When the operands are of type `bool`, the `!=` operator produces the same result as the `^` operator.</span></span>

### <a name="enumeration-comparison-operators"></a><span data-ttu-id="3d917-2159">Opérateurs de comparaison d’énumération</span><span class="sxs-lookup"><span data-stu-id="3d917-2159">Enumeration comparison operators</span></span>

<span data-ttu-id="3d917-2160">Chaque type énumération fournit implicitement les opérateurs de comparaison prédéfinis suivants :</span><span class="sxs-lookup"><span data-stu-id="3d917-2160">Every enumeration type implicitly provides the following predefined comparison operators:</span></span>
```csharp
bool operator ==(E x, E y);
bool operator !=(E x, E y);
bool operator <(E x, E y);
bool operator >(E x, E y);
bool operator <=(E x, E y);
bool operator >=(E x, E y);
```

<span data-ttu-id="3d917-2161">Le résultat de l’évaluation `x op y`, où `x` et `y` sont des expressions d’un type énumération `E` avec un type sous-jacent `U`, et `op` est un des opérateurs de comparaison, est exactement identique à l’évaluation `((U)x) op ((U)y)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2161">The result of evaluating `x op y`, where `x` and `y` are expressions of an enumeration type `E` with an underlying type `U`, and `op` is one of the comparison operators, is exactly the same as evaluating `((U)x) op ((U)y)`.</span></span> <span data-ttu-id="3d917-2162">En d’autres termes, les opérateurs de comparaison de type énumération simplement comparent les valeurs intégrales sous-jacentes des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2162">In other words, the enumeration type comparison operators simply compare the underlying integral values of the two operands.</span></span>

### <a name="reference-type-equality-operators"></a><span data-ttu-id="3d917-2163">Opérateurs d’égalité de type référence</span><span class="sxs-lookup"><span data-stu-id="3d917-2163">Reference type equality operators</span></span>

<span data-ttu-id="3d917-2164">Les opérateurs d’égalité de type référence prédéfinis sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-2164">The predefined reference type equality operators are:</span></span>
```csharp
bool operator ==(object x, object y);
bool operator !=(object x, object y);
```

<span data-ttu-id="3d917-2165">Les opérateurs retournent le résultat de la comparaison des deux références pour l’égalité ou non égalité.</span><span class="sxs-lookup"><span data-stu-id="3d917-2165">The operators return the result of comparing the two references for equality or non-equality.</span></span>

<span data-ttu-id="3d917-2166">Étant donné que les opérateurs d’égalité de type référence prédéfinis acceptent les opérandes de type `object`, elles s’appliquent à tous les types qui ne déclarent pas applicables `operator ==` et `operator !=` membres.</span><span class="sxs-lookup"><span data-stu-id="3d917-2166">Since the predefined reference type equality operators accept operands of type `object`, they apply to all types that do not declare applicable `operator ==` and `operator !=` members.</span></span> <span data-ttu-id="3d917-2167">À l’inverse, tous les opérateurs d’égalité de défini par l’utilisateur applicable masque effectivement les opérateurs d’égalité de type référence prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="3d917-2167">Conversely, any applicable user-defined equality operators effectively hide the predefined reference type equality operators.</span></span>

<span data-ttu-id="3d917-2168">Les opérateurs d’égalité de type référence prédéfinis nécessitent une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-2168">The predefined reference type equality operators require one of the following:</span></span>

*  <span data-ttu-id="3d917-2169">Les deux opérandes sont une valeur d’un type connu pour être un *reference_type* ou le littéral `null`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2169">Both operands are a value of a type known to be a *reference_type* or the literal `null`.</span></span> <span data-ttu-id="3d917-2170">En outre, une conversion de référence explicite ([conversions de référence explicite](conversions.md#explicit-reference-conversions)) existe à partir du type de des opérandes vers le type de l’autre opérande.</span><span class="sxs-lookup"><span data-stu-id="3d917-2170">Furthermore, an explicit reference conversion ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) exists from the type of either operand to the type of the other operand.</span></span>
*  <span data-ttu-id="3d917-2171">Un opérande est une valeur de type `T` où `T` est un *type_parameter* et l’autre opérande est le littéral `null`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2171">One operand is a value of type `T` where `T` is a *type_parameter* and the other operand is the literal `null`.</span></span> <span data-ttu-id="3d917-2172">En outre `T` n’a pas la contrainte de type valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2172">Furthermore `T` does not have the value type constraint.</span></span>

<span data-ttu-id="3d917-2173">Sauf si une des conditions suivantes sont remplies, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-2173">Unless one of these conditions are true, a binding-time error occurs.</span></span> <span data-ttu-id="3d917-2174">Les implications notables de ces règles sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-2174">Notable implications of these rules are:</span></span>

*  <span data-ttu-id="3d917-2175">Il est une erreur au moment de la liaison à utiliser les opérateurs d’égalité de type référence prédéfinis pour comparer deux références qui sont connues pour être différentes au moment de la liaison.</span><span class="sxs-lookup"><span data-stu-id="3d917-2175">It is a binding-time error to use the predefined reference type equality operators to compare two references that are known to be different at binding-time.</span></span> <span data-ttu-id="3d917-2176">Par exemple, si les types de liaison-heure des opérandes sont deux types de classe `A` et `B`et si ni `A` ni `B` dérive de l’autre, puis il serait impossible pour les deux opérandes référencer le même objet.</span><span class="sxs-lookup"><span data-stu-id="3d917-2176">For example, if the binding-time types of the operands are two class types `A` and `B`, and if neither `A` nor `B` derives from the other, then it would be impossible for the two operands to reference the same object.</span></span> <span data-ttu-id="3d917-2177">Par conséquent, l’opération est considérée comme une erreur au moment de la liaison.</span><span class="sxs-lookup"><span data-stu-id="3d917-2177">Thus, the operation is considered a binding-time error.</span></span>
*  <span data-ttu-id="3d917-2178">Les opérateurs d’égalité de type référence prédéfinis ne permettent pas de valeur des opérandes de type à comparer.</span><span class="sxs-lookup"><span data-stu-id="3d917-2178">The predefined reference type equality operators do not permit value type operands to be compared.</span></span> <span data-ttu-id="3d917-2179">Par conséquent, sauf si un type struct déclare ses propres opérateurs d’égalité, il n’est pas possible de comparer les valeurs de ce type struct.</span><span class="sxs-lookup"><span data-stu-id="3d917-2179">Therefore, unless a struct type declares its own equality operators, it is not possible to compare values of that struct type.</span></span>
*  <span data-ttu-id="3d917-2180">Les opérateurs d’égalité de type référence prédéfinis provoquent jamais les opérations de boxing pour leurs opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2180">The predefined reference type equality operators never cause boxing operations to occur for their operands.</span></span> <span data-ttu-id="3d917-2181">Il est inutile d’effectuer ces opérations de boxing, étant donné que les références aux instances boxed nouvellement allouées seraient nécessairement différentes de toutes les autres références.</span><span class="sxs-lookup"><span data-stu-id="3d917-2181">It would be meaningless to perform such boxing operations, since references to the newly allocated boxed instances would necessarily differ from all other references.</span></span>
*  <span data-ttu-id="3d917-2182">Si un opérande d’un type de paramètre de type `T` est comparé à `null`et le type au moment de l’exécution de `T` est un type valeur, le résultat de la comparaison est `false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2182">If an operand of a type parameter type `T` is compared to `null`, and the run-time type of `T` is a value type, the result of the comparison is `false`.</span></span>

<span data-ttu-id="3d917-2183">L’exemple suivant vérifie si un argument d’un type de paramètre de type sans contrainte est `null`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2183">The following example checks whether an argument of an unconstrained type parameter type is `null`.</span></span>
```csharp
class C<T>
{
    void F(T x) {
        if (x == null) throw new ArgumentNullException();
        ...
    }
}
```

<span data-ttu-id="3d917-2184">Le `x == null` construction est autorisée même si `T` pourrait représenter un type valeur, et le résultat est simplement défini pour être `false` lorsque `T` est un type valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2184">The `x == null` construct is permitted even though `T` could represent a value type, and the result is simply defined to be `false` when `T` is a value type.</span></span>

<span data-ttu-id="3d917-2185">Pour une opération du formulaire `x == y` ou `x != y`, le cas échéant toute `operator ==` ou `operator !=` existe, la résolution de surcharge d’opérateur ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) règles qui sélectionnera opérateur au lieu de l’opérateur d’égalité de type référence prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="3d917-2185">For an operation of the form `x == y` or `x != y`, if any applicable `operator ==` or `operator !=` exists, the operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) rules will select that operator instead of the predefined reference type equality operator.</span></span> <span data-ttu-id="3d917-2186">Toutefois, il est toujours possible de sélectionner l’opérateur d’égalité de type référence prédéfinis en effectuant un cast explicite d’une ou les deux opérandes en type `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2186">However, it is always possible to select the predefined reference type equality operator by explicitly casting one or both of the operands to type `object`.</span></span> <span data-ttu-id="3d917-2187">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2187">The example</span></span>
```csharp
using System;

class Test
{
    static void Main() {
        string s = "Test";
        string t = string.Copy(s);
        Console.WriteLine(s == t);
        Console.WriteLine((object)s == t);
        Console.WriteLine(s == (object)t);
        Console.WriteLine((object)s == (object)t);
    }
}
```
<span data-ttu-id="3d917-2188">génère la sortie</span><span class="sxs-lookup"><span data-stu-id="3d917-2188">produces the output</span></span>
```
True
False
False
False
```

<span data-ttu-id="3d917-2189">Le `s` et `t` variables font référence à deux distinctes `string` instances contenant les mêmes caractères.</span><span class="sxs-lookup"><span data-stu-id="3d917-2189">The `s` and `t` variables refer to two distinct `string` instances containing the same characters.</span></span> <span data-ttu-id="3d917-2190">La première comparaison génère `True` , car l’opérateur d’égalité de chaîne prédéfinie ([opérateurs d’égalité de chaîne](expressions.md#string-equality-operators)) est sélectionné lorsque les deux opérandes sont de type `string`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2190">The first comparison outputs `True` because the predefined string equality operator ([String equality operators](expressions.md#string-equality-operators)) is selected when both operands are of type `string`.</span></span> <span data-ttu-id="3d917-2191">Les autres comparaisons donnent toutes `False` , car l’opérateur d’égalité de type référence prédéfinis est sélectionné quand un ou les deux opérandes sont de type `object`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2191">The remaining comparisons all output `False` because the predefined reference type equality operator is selected when one or both of the operands are of type `object`.</span></span>

<span data-ttu-id="3d917-2192">Notez que la technique ci-dessus n’est pas significative pour les types de valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2192">Note that the above technique is not meaningful for value types.</span></span> <span data-ttu-id="3d917-2193">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2193">The example</span></span>
```csharp
class Test
{
    static void Main() {
        int i = 123;
        int j = 123;
        System.Console.WriteLine((object)i == (object)j);
    }
}
```
<span data-ttu-id="3d917-2194">sorties `False` converti (boxed), car les casts créent des références à deux instances distinctes de `int` valeurs.</span><span class="sxs-lookup"><span data-stu-id="3d917-2194">outputs `False` because the casts create references to two separate instances of boxed `int` values.</span></span>

### <a name="string-equality-operators"></a><span data-ttu-id="3d917-2195">Opérateurs d’égalité</span><span class="sxs-lookup"><span data-stu-id="3d917-2195">String equality operators</span></span>

<span data-ttu-id="3d917-2196">Les opérateurs d’égalité de chaîne prédéfinies sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-2196">The predefined string equality operators are:</span></span>
```csharp
bool operator ==(string x, string y);
bool operator !=(string x, string y);
```

<span data-ttu-id="3d917-2197">Deux `string` valeurs sont considérées comme égales lorsqu’une des opérations suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="3d917-2197">Two `string` values are considered equal when one of the following is true:</span></span>

*  <span data-ttu-id="3d917-2198">Les deux valeurs sont `null`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2198">Both values are `null`.</span></span>
*  <span data-ttu-id="3d917-2199">Les deux valeurs sont des références non-null à des instances de chaîne qui ont des longueurs identiques et des caractères identiques dans chaque position de caractère.</span><span class="sxs-lookup"><span data-stu-id="3d917-2199">Both values are non-null references to string instances that have identical lengths and identical characters in each character position.</span></span>

<span data-ttu-id="3d917-2200">Les opérateurs d’égalité de chaîne comparent des valeurs de chaîne plutôt que des références de chaîne.</span><span class="sxs-lookup"><span data-stu-id="3d917-2200">The string equality operators compare string values rather than string references.</span></span> <span data-ttu-id="3d917-2201">Lorsque deux instances de chaîne séparées contiennent exactement la même séquence de caractères, les valeurs des chaînes sont égales, mais les références sont différentes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2201">When two separate string instances contain the exact same sequence of characters, the values of the strings are equal, but the references are different.</span></span> <span data-ttu-id="3d917-2202">Comme décrit dans [opérateurs d’égalité de type référence](expressions.md#reference-type-equality-operators), les opérateurs d’égalité de type référence peuvent être utilisés pour comparer les références de chaîne au lieu de valeurs de chaîne.</span><span class="sxs-lookup"><span data-stu-id="3d917-2202">As described in [Reference type equality operators](expressions.md#reference-type-equality-operators), the reference type equality operators can be used to compare string references instead of string values.</span></span>

### <a name="delegate-equality-operators"></a><span data-ttu-id="3d917-2203">Opérateurs d’égalité de délégué</span><span class="sxs-lookup"><span data-stu-id="3d917-2203">Delegate equality operators</span></span>

<span data-ttu-id="3d917-2204">Chaque type de délégué fournit implicitement les opérateurs de comparaison prédéfinis suivants :</span><span class="sxs-lookup"><span data-stu-id="3d917-2204">Every delegate type implicitly provides the following predefined comparison operators:</span></span>

```csharp
bool operator ==(System.Delegate x, System.Delegate y);
bool operator !=(System.Delegate x, System.Delegate y);
```

<span data-ttu-id="3d917-2205">Délégué de deux instances est considérées comme égales comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-2205">Two delegate instances are considered equal as follows:</span></span>

*  <span data-ttu-id="3d917-2206">Si une des instances de délégué est `null`, elles sont égales si et seulement si les deux sont `null`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2206">If either of the delegate instances is `null`, they are equal if and only if both are `null`.</span></span>
*  <span data-ttu-id="3d917-2207">Si les délégués ont un autre type d’exécution qu’ils ne sont jamais égaux.</span><span class="sxs-lookup"><span data-stu-id="3d917-2207">If the delegates have different run-time type they are never equal.</span></span>
*  <span data-ttu-id="3d917-2208">Si les deux instances de délégué ont une liste d’appel ([déclarations Delegate](delegates.md#delegate-declarations)), ces instances sont égales si et seulement si leurs listes d’appel sont de même longueur, et chaque entrée dans sa liste d’appel est égale (tel que défini ci-dessous) à l’entrée correspondante dans l’ordre, dans la liste d’appel pour l’autre.</span><span class="sxs-lookup"><span data-stu-id="3d917-2208">If both of the delegate instances have an invocation list ([Delegate declarations](delegates.md#delegate-declarations)), those instances are equal if and only if their invocation lists are the same length, and each entry in one's invocation list is equal (as defined below) to the corresponding entry, in order, in the other's invocation list.</span></span>

<span data-ttu-id="3d917-2209">Les règles suivantes régissent l’égalité des entrées de liste d’appel :</span><span class="sxs-lookup"><span data-stu-id="3d917-2209">The following rules govern the equality of invocation list entries:</span></span>

*  <span data-ttu-id="3d917-2210">Si deux appel entrées de la liste les deux référence au même statique (méthode), puis les entrées sont égale.</span><span class="sxs-lookup"><span data-stu-id="3d917-2210">If two invocation list entries both refer to the same static method then the entries are equal.</span></span>
*  <span data-ttu-id="3d917-2211">Si deux appel entrées de la liste les deux font référence à la même méthode non statique sur le même objet cible (comme défini par les opérateurs d’égalité de référence) les entrées sont égale.</span><span class="sxs-lookup"><span data-stu-id="3d917-2211">If two invocation list entries both refer to the same non-static method on the same target object (as defined by the reference equality operators) then the entries are equal.</span></span>
*  <span data-ttu-id="3d917-2212">Entrées de liste d’appel provenant à partir de la version d’évaluation de sémantiquement identiques *anonymous_method_expression*s ou *lambda_expression*s avec le même jeu (éventuellement vide) de la variable externe capturée instances sont autorisés (mais pas obligatoires) sont égales.</span><span class="sxs-lookup"><span data-stu-id="3d917-2212">Invocation list entries produced from evaluation of semantically identical *anonymous_method_expression*s or *lambda_expression*s with the same (possibly empty) set of captured outer variable instances are permitted (but not required) to be equal.</span></span>

### <a name="equality-operators-and-null"></a><span data-ttu-id="3d917-2213">NULL et les opérateurs d’égalité</span><span class="sxs-lookup"><span data-stu-id="3d917-2213">Equality operators and null</span></span>

<span data-ttu-id="3d917-2214">Le `==` et `!=` opérateurs permettent un opérande soit une valeur d’un type nullable et l’autre pour être le `null` littéral, même si aucun opérateur prédéfinie ou définies par l’utilisateur (dans unlifted ou levé formulaire) n’existe pour l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-2214">The `==` and `!=` operators permit one operand to be a value of a nullable type and the other to be the `null` literal, even if no predefined or user-defined operator (in unlifted or lifted form) exists for the operation.</span></span>

<span data-ttu-id="3d917-2215">Pour une opération d’une des formes</span><span class="sxs-lookup"><span data-stu-id="3d917-2215">For an operation of one of the forms</span></span>
```csharp
x == null
null == x
x != null
null != x
```
<span data-ttu-id="3d917-2216">où `x` est une expression d’un type nullable, si l’opérateur de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) ne parvient pas à trouver un opérateur applicable, le résultat est calculé à la place à partir du `HasValue` propriété de `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2216">where `x` is an expression of a nullable type, if operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) fails to find an applicable operator, the result is instead computed from the `HasValue` property of `x`.</span></span> <span data-ttu-id="3d917-2217">Plus précisément, les deux premiers formulaires sont traduites en `!x.HasValue`, et les deux derniers formulaires sont traduites en `x.HasValue`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2217">Specifically, the first two forms are translated into `!x.HasValue`, and last two forms are translated into `x.HasValue`.</span></span>

### <a name="the-is-operator"></a><span data-ttu-id="3d917-2218">L’opérateur is</span><span class="sxs-lookup"><span data-stu-id="3d917-2218">The is operator</span></span>

<span data-ttu-id="3d917-2219">Le `is` opérateur est utilisé pour contrôler de manière dynamique si le type au moment de l’exécution d’un objet est compatible avec un type donné.</span><span class="sxs-lookup"><span data-stu-id="3d917-2219">The `is` operator is used to dynamically check if the run-time type of an object is compatible with a given type.</span></span> <span data-ttu-id="3d917-2220">Le résultat de l’opération `E is T`, où `E` est une expression et `T` est un type, est une valeur booléenne valeur indiquant si `E` peut être converti en type `T` par une conversion de référence, une conversion boxing conversion, ou une conversion unboxing.</span><span class="sxs-lookup"><span data-stu-id="3d917-2220">The result of the operation `E is T`, where `E` is an expression and `T` is a type, is a boolean value indicating whether `E` can successfully be converted to type `T` by a reference conversion, a boxing conversion, or an unboxing conversion.</span></span> <span data-ttu-id="3d917-2221">L’opération est évaluée comme suit, après que les arguments de type ont été substitués à tous les paramètres de type :</span><span class="sxs-lookup"><span data-stu-id="3d917-2221">The operation is evaluated as follows, after type arguments have been substituted for all type parameters:</span></span>

*  <span data-ttu-id="3d917-2222">Si `E` est une fonction anonyme, une erreur de compilation se produit</span><span class="sxs-lookup"><span data-stu-id="3d917-2222">If `E` is an anonymous function, a compile-time error occurs</span></span>
*  <span data-ttu-id="3d917-2223">Si `E` est un groupe de méthodes ou les `null` littéral of si le type de `E` est un type référence ou un type nullable et la valeur de `E` est null, le résultat est false.</span><span class="sxs-lookup"><span data-stu-id="3d917-2223">If `E` is a method group or the `null` literal, of if the type of `E` is a reference type or a nullable type and the value of `E` is null, the result is false.</span></span>
*  <span data-ttu-id="3d917-2224">Sinon, laissez le `D` représentent le type dynamique de `E` comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-2224">Otherwise, let `D` represent the dynamic type of `E` as follows:</span></span>
   * <span data-ttu-id="3d917-2225">Si le type de `E` est un type référence, `D` est le type au moment de l’exécution de la référence d’instance par `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2225">If the type of `E` is a reference type, `D` is the run-time type of the instance reference by `E`.</span></span>
   * <span data-ttu-id="3d917-2226">Si le type de `E` est un type nullable, `D` est le type sous-jacent de ce type nullable.</span><span class="sxs-lookup"><span data-stu-id="3d917-2226">If the type of `E` is a nullable type, `D` is the underlying type of that nullable type.</span></span>
   * <span data-ttu-id="3d917-2227">Si le type de `E` est un type valeur non nullable, `D` est le type de `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2227">If the type of `E` is a non-nullable value type, `D` is the type of `E`.</span></span>
*  <span data-ttu-id="3d917-2228">Le résultat de l’opération dépend `D` et `T` comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-2228">The result of the operation depends on `D` and `T` as follows:</span></span>
   * <span data-ttu-id="3d917-2229">Si `T` est un type référence, le résultat est true si `D` et `T` sont le même type, si `D` est un type référence et une conversion de référence implicite à partir de `D` à `T` existe, ou si `D` est un type valeur et une conversion boxing de `D` à `T` existe.</span><span class="sxs-lookup"><span data-stu-id="3d917-2229">If `T` is a reference type, the result is true if `D` and `T` are the same type, if `D` is a reference type and an implicit reference conversion from `D` to `T` exists, or if `D` is a value type and a boxing conversion from `D` to `T` exists.</span></span>
   * <span data-ttu-id="3d917-2230">Si `T` est un type nullable, le résultat est true si `D` est le type sous-jacent de `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2230">If `T` is a nullable type, the result is true if `D` is the underlying type of `T`.</span></span>
   * <span data-ttu-id="3d917-2231">Si `T` est un type valeur non nullable, le résultat est true si `D` et `T` sont du même type.</span><span class="sxs-lookup"><span data-stu-id="3d917-2231">If `T` is a non-nullable value type, the result is true if `D` and `T` are the same type.</span></span>
   * <span data-ttu-id="3d917-2232">Sinon, le résultat est false.</span><span class="sxs-lookup"><span data-stu-id="3d917-2232">Otherwise, the result is false.</span></span>

<span data-ttu-id="3d917-2233">Notez que les conversions définies par l’utilisateur, ne sont pas envisagés par le `is` opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2233">Note that user defined conversions, are not considered by the `is` operator.</span></span>

### <a name="the-as-operator"></a><span data-ttu-id="3d917-2234">L’opérateur as</span><span class="sxs-lookup"><span data-stu-id="3d917-2234">The as operator</span></span>

<span data-ttu-id="3d917-2235">Le `as` opérateur est utilisé pour convertir explicitement une valeur à un type de référence donnée ou d’un type nullable.</span><span class="sxs-lookup"><span data-stu-id="3d917-2235">The `as` operator is used to explicitly convert a value to a given reference type or nullable type.</span></span> <span data-ttu-id="3d917-2236">Contrairement à une expression de cast ([les expressions de Cast](expressions.md#cast-expressions)), le `as` opérateur lève jamais d’exception.</span><span class="sxs-lookup"><span data-stu-id="3d917-2236">Unlike a cast expression ([Cast expressions](expressions.md#cast-expressions)), the `as` operator never throws an exception.</span></span> <span data-ttu-id="3d917-2237">Au lieu de cela, si la conversion indiquée n’est pas possible, la valeur résultante est `null`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2237">Instead, if the indicated conversion is not possible, the resulting value is `null`.</span></span>

<span data-ttu-id="3d917-2238">Dans une opération de la forme `E as T`, `E` doit être une expression et `T` doit être un type référence, un paramètre de type connu pour être un type référence ou un type nullable.</span><span class="sxs-lookup"><span data-stu-id="3d917-2238">In an operation of the form `E as T`, `E` must be an expression and `T` must be a reference type, a type parameter known to be a reference type, or a nullable type.</span></span> <span data-ttu-id="3d917-2239">En outre, au moins un des éléments suivants doit être remplie, ou sinon une erreur de compilation se produit :</span><span class="sxs-lookup"><span data-stu-id="3d917-2239">Furthermore, at least one of the following must be true, or otherwise a compile-time error occurs:</span></span>

*  <span data-ttu-id="3d917-2240">Une identité ([conversion d’identité](conversions.md#identity-conversion)), implicites nullable ([les conversions implicites nullables](conversions.md#implicit-nullable-conversions)), référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions)), le boxing ([ Les conversions boxing](conversions.md#boxing-conversions)) explicite nullable ([conversions nullables explicites](conversions.md#explicit-nullable-conversions)), référence explicite ([conversions de référence explicite](conversions.md#explicit-reference-conversions)), ou unboxing ([Les conversions Unboxing](conversions.md#unboxing-conversions)) conversion existe entre `E` à `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2240">An identity ([Identity conversion](conversions.md#identity-conversion)), implicit nullable ([Implicit nullable conversions](conversions.md#implicit-nullable-conversions)), implicit reference ([Implicit reference conversions](conversions.md#implicit-reference-conversions)), boxing ([Boxing conversions](conversions.md#boxing-conversions)), explicit nullable ([Explicit nullable conversions](conversions.md#explicit-nullable-conversions)), explicit reference ([Explicit reference conversions](conversions.md#explicit-reference-conversions)), or unboxing ([Unboxing conversions](conversions.md#unboxing-conversions)) conversion exists from `E` to `T`.</span></span>
*  <span data-ttu-id="3d917-2241">Le type de `E` ou `T` est un type ouvert.</span><span class="sxs-lookup"><span data-stu-id="3d917-2241">The type of `E` or `T` is an open type.</span></span>
*  <span data-ttu-id="3d917-2242">`E` est le `null` littéral.</span><span class="sxs-lookup"><span data-stu-id="3d917-2242">`E` is the `null` literal.</span></span>

<span data-ttu-id="3d917-2243">Si le type de la compilation de `E` n’est pas `dynamic`, l’opération `E as T` produit le même résultat en tant que</span><span class="sxs-lookup"><span data-stu-id="3d917-2243">If the compile-time type of `E` is not `dynamic`, the operation `E as T` produces the same result as</span></span>
```csharp
E is T ? (T)(E) : (T)null
```
<span data-ttu-id="3d917-2244">sauf que `E` n’est évalué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="3d917-2244">except that `E` is only evaluated once.</span></span> <span data-ttu-id="3d917-2245">Le compilateur peut s’attendre à optimiser `E as T` à effectuer au maximum une vérification de type dynamique par opposition à deux contrôles de type dynamique impliquée par l’expansion ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="3d917-2245">The compiler can be expected to optimize `E as T` to perform at most one dynamic type check as opposed to the two dynamic type checks implied by the expansion above.</span></span>

<span data-ttu-id="3d917-2246">Si le type de la compilation de `E` est `dynamic`, contrairement à l’opérateur de cast le `as` opérateur n’est pas lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2246">If the compile-time type of `E` is `dynamic`, unlike the cast operator the `as` operator is not dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="3d917-2247">Par conséquent, l’expansion est dans ce cas :</span><span class="sxs-lookup"><span data-stu-id="3d917-2247">Therefore the expansion in this case is:</span></span>
```csharp
E is T ? (T)(object)(E) : (T)null
```

<span data-ttu-id="3d917-2248">Notez que certaines conversions, telles que les conversions définies par l’utilisateur, ne sont pas possibles avec la `as` opérateur et doivent être effectuées à l’aide d’expressions de cast.</span><span class="sxs-lookup"><span data-stu-id="3d917-2248">Note that some conversions, such as user defined conversions, are not possible with the `as` operator and should instead be performed using cast expressions.</span></span>

<span data-ttu-id="3d917-2249">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2249">In the example</span></span>
```csharp
class X
{

    public string F(object o) {
        return o as string;        // OK, string is a reference type
    }

    public T G<T>(object o) where T: Attribute {
        return o as T;             // Ok, T has a class constraint
    }

    public U H<U>(object o) {
        return o as U;             // Error, U is unconstrained 
    }
}
```
<span data-ttu-id="3d917-2250">le paramètre de type `T` de `G` est censée être un type référence, car il a la contrainte de classe.</span><span class="sxs-lookup"><span data-stu-id="3d917-2250">the type parameter `T` of `G` is known to be a reference type, because it has the class constraint.</span></span> <span data-ttu-id="3d917-2251">Le paramètre de type `U` de `H` n’est pas toutefois ; par conséquent, l’utilisation de la `as` opérateur dans `H` n’est pas autorisée.</span><span class="sxs-lookup"><span data-stu-id="3d917-2251">The type parameter `U` of `H` is not however; hence the use of the `as` operator in `H` is disallowed.</span></span>

## <a name="logical-operators"></a><span data-ttu-id="3d917-2252">Opérateurs logiques</span><span class="sxs-lookup"><span data-stu-id="3d917-2252">Logical operators</span></span>

<span data-ttu-id="3d917-2253">Le `&`, `^`, et `|` sont appelés les opérateurs logiques.</span><span class="sxs-lookup"><span data-stu-id="3d917-2253">The `&`, `^`, and `|` operators are called the logical operators.</span></span>

```antlr
and_expression
    : equality_expression
    | and_expression '&' equality_expression
    ;

exclusive_or_expression
    : and_expression
    | exclusive_or_expression '^' and_expression
    ;

inclusive_or_expression
    : exclusive_or_expression
    | inclusive_or_expression '|' exclusive_or_expression
    ;
```

<span data-ttu-id="3d917-2254">Si un opérande d’un opérateur logique a le type de compilation `dynamic`, puis l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2254">If an operand of a logical operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="3d917-2255">Dans ce cas le type de compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution à l’aide du type au moment de l’exécution de ces opérandes qui ont le type de compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2255">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="3d917-2256">Pour une opération du formulaire `x op y`, où `op` est un des opérateurs logiques, la résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="3d917-2256">For an operation of the form `x op y`, where `op` is one of the logical operators, overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="3d917-2257">Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2257">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="3d917-2258">Les opérateurs logiques prédéfinis sont décrits dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2258">The predefined logical operators are described in the following sections.</span></span>

### <a name="integer-logical-operators"></a><span data-ttu-id="3d917-2259">Opérateurs logiques d’entiers</span><span class="sxs-lookup"><span data-stu-id="3d917-2259">Integer logical operators</span></span>

<span data-ttu-id="3d917-2260">Les opérateurs logiques d’entiers prédéfinis sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-2260">The predefined integer logical operators are:</span></span>
```csharp
int operator &(int x, int y);
uint operator &(uint x, uint y);
long operator &(long x, long y);
ulong operator &(ulong x, ulong y);

int operator |(int x, int y);
uint operator |(uint x, uint y);
long operator |(long x, long y);
ulong operator |(ulong x, ulong y);

int operator ^(int x, int y);
uint operator ^(uint x, uint y);
long operator ^(long x, long y);
ulong operator ^(ulong x, ulong y);
```

<span data-ttu-id="3d917-2261">Le `&` opérateur calcule l’opérateur de bits logiques `AND` des deux opérandes, le `|` opérateur calcule l’opérateur de bits logiques `OR` des deux opérandes et le `^` opérateur calcule l’OR exclusif logique `OR` des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2261">The `&` operator computes the bitwise logical `AND` of the two operands, the `|` operator computes the bitwise logical `OR` of the two operands, and the `^` operator computes the bitwise logical exclusive `OR` of the two operands.</span></span> <span data-ttu-id="3d917-2262">Aucun dépassements de capacité ne sont possibles à partir de ces opérations.</span><span class="sxs-lookup"><span data-stu-id="3d917-2262">No overflows are possible from these operations.</span></span>

### <a name="enumeration-logical-operators"></a><span data-ttu-id="3d917-2263">Opérateurs logiques d’énumération</span><span class="sxs-lookup"><span data-stu-id="3d917-2263">Enumeration logical operators</span></span>

<span data-ttu-id="3d917-2264">Chaque type d’énumération `E` implicitement fournit les éléments suivants prédéfinies des opérateurs logiques :</span><span class="sxs-lookup"><span data-stu-id="3d917-2264">Every enumeration type `E` implicitly provides the following predefined logical operators:</span></span>

```csharp
E operator &(E x, E y);
E operator |(E x, E y);
E operator ^(E x, E y);
```

<span data-ttu-id="3d917-2265">Le résultat de l’évaluation `x op y`, où `x` et `y` sont des expressions d’un type énumération `E` avec un type sous-jacent `U`, et `op` est un des opérateurs logiques, est exactement identique à l’évaluation `(E)((U)x op (U)y)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2265">The result of evaluating `x op y`, where `x` and `y` are expressions of an enumeration type `E` with an underlying type `U`, and `op` is one of the logical operators, is exactly the same as evaluating `(E)((U)x op (U)y)`.</span></span> <span data-ttu-id="3d917-2266">En d’autres termes, les opérateurs logiques de type énumération simplement effectuent l’opération logique sur le type sous-jacent des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2266">In other words, the enumeration type logical operators simply perform the logical operation on the underlying type of the two operands.</span></span>

### <a name="boolean-logical-operators"></a><span data-ttu-id="3d917-2267">Opérateurs logiques booléens</span><span class="sxs-lookup"><span data-stu-id="3d917-2267">Boolean logical operators</span></span>

<span data-ttu-id="3d917-2268">Les opérateurs logiques booléens prédéfinis sont :</span><span class="sxs-lookup"><span data-stu-id="3d917-2268">The predefined boolean logical operators are:</span></span>
```csharp
bool operator &(bool x, bool y);
bool operator |(bool x, bool y);
bool operator ^(bool x, bool y);
```

<span data-ttu-id="3d917-2269">Le résultat de `x & y` est `true` si `x` et `y` sont `true`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2269">The result of `x & y` is `true` if both `x` and `y` are `true`.</span></span> <span data-ttu-id="3d917-2270">Sinon, le résultat est `false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2270">Otherwise, the result is `false`.</span></span>

<span data-ttu-id="3d917-2271">Le résultat de `x | y` est `true` si `x` ou `y` est `true`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2271">The result of `x | y` is `true` if either `x` or `y` is `true`.</span></span> <span data-ttu-id="3d917-2272">Sinon, le résultat est `false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2272">Otherwise, the result is `false`.</span></span>

<span data-ttu-id="3d917-2273">Le résultat de `x ^ y` est `true` si `x` est `true` et `y` est `false`, ou `x` est `false` et `y` est `true`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2273">The result of `x ^ y` is `true` if `x` is `true` and `y` is `false`, or `x` is `false` and `y` is `true`.</span></span> <span data-ttu-id="3d917-2274">Sinon, le résultat est `false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2274">Otherwise, the result is `false`.</span></span> <span data-ttu-id="3d917-2275">Lorsque les opérandes sont de type `bool`, le `^` opérateur calcule le même résultat que la `!=` opérateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2275">When the operands are of type `bool`, the `^` operator computes the same result as the `!=` operator.</span></span>

### <a name="nullable-boolean-logical-operators"></a><span data-ttu-id="3d917-2276">Opérateurs logiques de type boolean nullables</span><span class="sxs-lookup"><span data-stu-id="3d917-2276">Nullable boolean logical operators</span></span>

<span data-ttu-id="3d917-2277">Le type boolean nullable `bool?` peut représenter les trois valeurs, `true`, `false`, et `null`et est conceptuellement semblable au type à trois valeurs utilisé pour les expressions booléennes dans SQL.</span><span class="sxs-lookup"><span data-stu-id="3d917-2277">The nullable boolean type `bool?` can represent three values, `true`, `false`, and `null`, and is conceptually similar to the three-valued type used for boolean expressions in SQL.</span></span> <span data-ttu-id="3d917-2278">Pour vous assurer que les résultats générés par le `&` et `|` opérateurs pour `bool?` opérandes sont cohérentes avec une logique à trois valeurs de SQL, les opérateurs prédéfinis suivants sont fournis :</span><span class="sxs-lookup"><span data-stu-id="3d917-2278">To ensure that the results produced by the `&` and `|` operators for `bool?` operands are consistent with SQL's three-valued logic, the following predefined operators are provided:</span></span>

```csharp
bool? operator &(bool? x, bool? y);
bool? operator |(bool? x, bool? y);
```

<span data-ttu-id="3d917-2279">Le tableau suivant répertorie les résultats générés par ces opérateurs pour toutes les combinaisons des valeurs `true`, `false`, et `null`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2279">The following table lists the results produced by these operators for all combinations of the values `true`, `false`, and `null`.</span></span>

| `x`     | `y`     | `x & y` | <code>x &#124; y</code> |
|:-------:|:-------:|:-------:|:-------:|
| `true`  | `true`  | `true`  | `true`  | 
| `true`  | `false` | `false` | `true`  | 
| `true`  | `null`  | `null`  | `true`  | 
| `false` | `true`  | `false` | `true`  | 
| `false` | `false` | `false` | `false` | 
| `false` | `null`  | `false` | `null`  | 
| `null`  | `true`  | `null`  | `true`  | 
| `null`  | `false` | `false` | `null`  | 
| `null`  | `null`  | `null`  | `null`  | 

## <a name="conditional-logical-operators"></a><span data-ttu-id="3d917-2280">Opérateurs logiques conditionnels</span><span class="sxs-lookup"><span data-stu-id="3d917-2280">Conditional logical operators</span></span>

<span data-ttu-id="3d917-2281">Le `&&` et `||` sont appelés des opérateurs logiques conditionnels.</span><span class="sxs-lookup"><span data-stu-id="3d917-2281">The `&&` and `||` operators are called the conditional logical operators.</span></span> <span data-ttu-id="3d917-2282">Ils sont également appelés les opérateurs logiques « court-circuit ».</span><span class="sxs-lookup"><span data-stu-id="3d917-2282">They are also called the "short-circuiting" logical operators.</span></span>

```antlr
conditional_and_expression
    : inclusive_or_expression
    | conditional_and_expression '&&' inclusive_or_expression
    ;

conditional_or_expression
    : conditional_and_expression
    | conditional_or_expression '||' conditional_and_expression
    ;
```

<span data-ttu-id="3d917-2283">Le `&&` et `||` opérateurs sont des versions conditionnelles de la `&` et `|` opérateurs :</span><span class="sxs-lookup"><span data-stu-id="3d917-2283">The `&&` and `||` operators are conditional versions of the `&` and `|` operators:</span></span>

*  <span data-ttu-id="3d917-2284">L’opération `x && y` correspond à l’opération `x & y`, sauf que `y` est évaluée uniquement si `x` n’est pas `false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2284">The operation `x && y` corresponds to the operation `x & y`, except that `y` is evaluated only if `x` is not `false`.</span></span>
*  <span data-ttu-id="3d917-2285">L’opération `x || y` correspond à l’opération `x | y`, sauf que `y` est évaluée uniquement si `x` n’est pas `true`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2285">The operation `x || y` corresponds to the operation `x | y`, except that `y` is evaluated only if `x` is not `true`.</span></span>

<span data-ttu-id="3d917-2286">Si un opérande d’un opérateur logique conditionnel a le type de compilation `dynamic`, puis l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2286">If an operand of a conditional logical operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="3d917-2287">Dans ce cas le type de compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution à l’aide du type au moment de l’exécution de ces opérandes qui ont le type de compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2287">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="3d917-2288">Une opération de la forme `x && y` ou `x || y` est traitée en appliquant la résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) comme si l’opération a été écrite `x & y` ou `x | y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2288">An operation of the form `x && y` or `x || y` is processed by applying overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) as if the operation was written `x & y` or `x | y`.</span></span> <span data-ttu-id="3d917-2289">Ensuite,</span><span class="sxs-lookup"><span data-stu-id="3d917-2289">Then,</span></span>

*  <span data-ttu-id="3d917-2290">Si la résolution de surcharge ne parvient pas à trouver un seul meilleur opérateur, ou si la résolution de surcharge sélectionne l’un des opérateurs logiques d’entiers prédéfinis, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-2290">If overload resolution fails to find a single best operator, or if overload resolution selects one of the predefined integer logical operators, a binding-time error occurs.</span></span>
*  <span data-ttu-id="3d917-2291">Sinon, si l’opérateur sélectionné est un des opérateurs logiques booléens prédéfinis ([des opérateurs logiques booléens](expressions.md#boolean-logical-operators)) ou des opérateurs logiques booléens nullables ([des opérateurs logiques booléens Nullable](expressions.md#nullable-boolean-logical-operators)), le opération est traitée comme décrit dans [booléennes opérateurs logiques conditionnels](expressions.md#boolean-conditional-logical-operators).</span><span class="sxs-lookup"><span data-stu-id="3d917-2291">Otherwise, if the selected operator is one of the predefined boolean logical operators ([Boolean logical operators](expressions.md#boolean-logical-operators)) or nullable boolean logical operators ([Nullable boolean logical operators](expressions.md#nullable-boolean-logical-operators)), the operation is processed as described in [Boolean conditional logical operators](expressions.md#boolean-conditional-logical-operators).</span></span>
*  <span data-ttu-id="3d917-2292">Sinon, l’opérateur sélectionné est un opérateur défini par l’utilisateur, et l’opération est traitée comme décrit dans [opérateurs logiques conditionnels définis par l’utilisateur](expressions.md#user-defined-conditional-logical-operators).</span><span class="sxs-lookup"><span data-stu-id="3d917-2292">Otherwise, the selected operator is a user-defined operator, and the operation is processed as described in [User-defined conditional logical operators](expressions.md#user-defined-conditional-logical-operators).</span></span>

<span data-ttu-id="3d917-2293">Il n’est pas possible de surcharger directement les opérateurs logiques conditionnels.</span><span class="sxs-lookup"><span data-stu-id="3d917-2293">It is not possible to directly overload the conditional logical operators.</span></span> <span data-ttu-id="3d917-2294">Toutefois, étant donné que les opérateurs logiques conditionnels sont évaluées en termes d’opérateurs logiques normaux, les surcharges des opérateurs logiques normaux sont, avec certaines restrictions, considérées comme des surcharges des opérateurs logiques conditionnels.</span><span class="sxs-lookup"><span data-stu-id="3d917-2294">However, because the conditional logical operators are evaluated in terms of the regular logical operators, overloads of the regular logical operators are, with certain restrictions, also considered overloads of the conditional logical operators.</span></span> <span data-ttu-id="3d917-2295">Cela est décrite plus loin dans [opérateurs logiques conditionnels définis par l’utilisateur](expressions.md#user-defined-conditional-logical-operators).</span><span class="sxs-lookup"><span data-stu-id="3d917-2295">This is described further in [User-defined conditional logical operators](expressions.md#user-defined-conditional-logical-operators).</span></span>

### <a name="boolean-conditional-logical-operators"></a><span data-ttu-id="3d917-2296">Opérateurs logiques conditionnels booléens</span><span class="sxs-lookup"><span data-stu-id="3d917-2296">Boolean conditional logical operators</span></span>

<span data-ttu-id="3d917-2297">Lorsque les opérandes de `&&` ou `||` sont de type `bool`, ou lorsque les opérandes sont des types qui ne définissent pas un applicable `operator &` ou `operator |`, mais ne définissent pas de conversions implicites vers `bool`, l’opération est traitées comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-2297">When the operands of `&&` or `||` are of type `bool`, or when the operands are of types that do not define an applicable `operator &` or `operator |`, but do define implicit conversions to `bool`, the operation is processed as follows:</span></span>

*  <span data-ttu-id="3d917-2298">L’opération `x && y` est évalué comme `x ? y : false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2298">The operation `x && y` is evaluated as `x ? y : false`.</span></span> <span data-ttu-id="3d917-2299">En d’autres termes, `x` est tout d’abord évaluées et converties en type `bool`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2299">In other words, `x` is first evaluated and converted to type `bool`.</span></span> <span data-ttu-id="3d917-2300">Ensuite, si `x` est `true`, `y` est évaluée et converti en type `bool`, et cela devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-2300">Then, if `x` is `true`, `y` is evaluated and converted to type `bool`, and this becomes the result of the operation.</span></span> <span data-ttu-id="3d917-2301">Sinon, le résultat de l’opération est `false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2301">Otherwise, the result of the operation is `false`.</span></span>
*  <span data-ttu-id="3d917-2302">L’opération `x || y` est évalué comme `x ? true : y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2302">The operation `x || y` is evaluated as `x ? true : y`.</span></span> <span data-ttu-id="3d917-2303">En d’autres termes, `x` est tout d’abord évaluées et converties en type `bool`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2303">In other words, `x` is first evaluated and converted to type `bool`.</span></span> <span data-ttu-id="3d917-2304">Ensuite, si `x` est `true`, le résultat de l’opération est `true`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2304">Then, if `x` is `true`, the result of the operation is `true`.</span></span> <span data-ttu-id="3d917-2305">Sinon, `y` est évaluée et converti en type `bool`, et cela devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-2305">Otherwise, `y` is evaluated and converted to type `bool`, and this becomes the result of the operation.</span></span>

### <a name="user-defined-conditional-logical-operators"></a><span data-ttu-id="3d917-2306">Opérateurs logiques conditionnels définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="3d917-2306">User-defined conditional logical operators</span></span>

<span data-ttu-id="3d917-2307">Lorsque les opérandes de `&&` ou `||` sont des types qui déclarent un applicables définies par l’utilisateur `operator &` ou `operator |`, des opérations suivantes doivent être remplies, où `T` est le type dans lequel l’opérateur sélectionné est déclarée :</span><span class="sxs-lookup"><span data-stu-id="3d917-2307">When the operands of `&&` or `||` are of types that declare an applicable user-defined `operator &` or `operator |`, both of the following must be true, where `T` is the type in which the selected operator is declared:</span></span>

*  <span data-ttu-id="3d917-2308">Le type de retour et le type de chaque paramètre de l’opérateur sélectionné doivent être `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2308">The return type and the type of each parameter of the selected operator must be `T`.</span></span> <span data-ttu-id="3d917-2309">En d’autres termes, l’opérateur doit calculer la logique `AND` ou l’opérateur logique `OR` des deux opérandes de type `T`et doit retourner un résultat de type `T`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2309">In other words, the operator must compute the logical `AND` or the logical `OR` of two operands of type `T`, and must return a result of type `T`.</span></span>
*  <span data-ttu-id="3d917-2310">`T` doit contenir les déclarations de `operator true` et `operator false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2310">`T` must contain declarations of `operator true` and `operator false`.</span></span>

<span data-ttu-id="3d917-2311">Une erreur au moment de la liaison se produit si une de ces conditions n’est pas satisfaite.</span><span class="sxs-lookup"><span data-stu-id="3d917-2311">A binding-time error occurs if either of these requirements is not satisfied.</span></span> <span data-ttu-id="3d917-2312">Sinon, le `&&` ou `||` est évaluée en combinant le défini par l’utilisateur `operator true` ou `operator false` avec l’opérateur défini par l’utilisateur sélectionné :</span><span class="sxs-lookup"><span data-stu-id="3d917-2312">Otherwise, the `&&` or `||` operation is evaluated by combining the user-defined `operator true` or `operator false` with the selected user-defined operator:</span></span>

*  <span data-ttu-id="3d917-2313">L’opération `x && y` est évalué comme `T.false(x) ? x : T.&(x, y)`, où `T.false(x)` est un appel de la `operator false` déclaré dans `T`, et `T.&(x, y)` est un appel de sélectionné `operator &`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2313">The operation `x && y` is evaluated as `T.false(x) ? x : T.&(x, y)`, where `T.false(x)` is an invocation of the `operator false` declared in `T`, and `T.&(x, y)` is an invocation of the selected `operator &`.</span></span> <span data-ttu-id="3d917-2314">En d’autres termes, `x` est évalué en premier et `operator false` est appelée sur le résultat pour déterminer si `x` est définitivement false.</span><span class="sxs-lookup"><span data-stu-id="3d917-2314">In other words, `x` is first evaluated and `operator false` is invoked on the result to determine if `x` is definitely false.</span></span> <span data-ttu-id="3d917-2315">Ensuite, si `x` est définitivement false, le résultat de l’opération est la valeur précédemment calculée pour `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2315">Then, if `x` is definitely false, the result of the operation is the value previously computed for `x`.</span></span> <span data-ttu-id="3d917-2316">Sinon, `y` est évaluée et le texte sélectionné `operator &` est appelée sur la valeur précédemment calculée pour `x` et la valeur calculée pour `y` pour produire le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-2316">Otherwise, `y` is evaluated, and the selected `operator &` is invoked on the value previously computed for `x` and the value computed for `y` to produce the result of the operation.</span></span>
*  <span data-ttu-id="3d917-2317">L’opération `x || y` est évalué comme `T.true(x) ? x : T.|(x, y)`, où `T.true(x)` est un appel de la `operator true` déclaré dans `T`, et `T.|(x,y)` est un appel de sélectionné `operator|`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2317">The operation `x || y` is evaluated as `T.true(x) ? x : T.|(x, y)`, where `T.true(x)` is an invocation of the `operator true` declared in `T`, and `T.|(x,y)` is an invocation of the selected `operator|`.</span></span> <span data-ttu-id="3d917-2318">En d’autres termes, `x` est évalué en premier et `operator true` est appelée sur le résultat pour déterminer si `x` vaut sans aucun doute.</span><span class="sxs-lookup"><span data-stu-id="3d917-2318">In other words, `x` is first evaluated and `operator true` is invoked on the result to determine if `x` is definitely true.</span></span> <span data-ttu-id="3d917-2319">Ensuite, si `x` vaut sans aucun doute, le résultat de l’opération est la valeur précédemment calculée pour `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2319">Then, if `x` is definitely true, the result of the operation is the value previously computed for `x`.</span></span> <span data-ttu-id="3d917-2320">Sinon, `y` est évaluée et le texte sélectionné `operator |` est appelée sur la valeur précédemment calculée pour `x` et la valeur calculée pour `y` pour produire le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-2320">Otherwise, `y` is evaluated, and the selected `operator |` is invoked on the value previously computed for `x` and the value computed for `y` to produce the result of the operation.</span></span>

<span data-ttu-id="3d917-2321">Dans une de ces opérations, l’expression donnée par `x` est évaluée une seule fois et l’expression donnée par `y` est pas évalué, ni évaluée une seule fois.</span><span class="sxs-lookup"><span data-stu-id="3d917-2321">In either of these operations, the expression given by `x` is only evaluated once, and the expression given by `y` is either not evaluated or evaluated exactly once.</span></span>

<span data-ttu-id="3d917-2322">Pour obtenir un exemple d’un type qui implémente `operator true` et `operator false`, consultez [de base de données de type booléen](structs.md#database-boolean-type).</span><span class="sxs-lookup"><span data-stu-id="3d917-2322">For an example of a type that implements `operator true` and `operator false`, see [Database boolean type](structs.md#database-boolean-type).</span></span>

## <a name="the-null-coalescing-operator"></a><span data-ttu-id="3d917-2323">L’opérateur de fusion null</span><span class="sxs-lookup"><span data-stu-id="3d917-2323">The null coalescing operator</span></span>

<span data-ttu-id="3d917-2324">Le `??` opérateur est appelé l’opérateur de fusion null.</span><span class="sxs-lookup"><span data-stu-id="3d917-2324">The `??` operator is called the null coalescing operator.</span></span>

```antlr
null_coalescing_expression
    : conditional_or_expression
    | conditional_or_expression '??' null_coalescing_expression
    ;
```

<span data-ttu-id="3d917-2325">Une expression de fusion null sous la forme `a ?? b` nécessite `a` pour être d’un type référence ou de type nullable.</span><span class="sxs-lookup"><span data-stu-id="3d917-2325">A null coalescing expression of the form `a ?? b` requires `a` to be of a nullable type or reference type.</span></span> <span data-ttu-id="3d917-2326">Si `a` n’est pas null, le résultat de `a ?? b` est `a`; sinon, le résultat est `b`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2326">If `a` is non-null, the result of `a ?? b` is `a`; otherwise, the result is `b`.</span></span> <span data-ttu-id="3d917-2327">L’opération a la valeur `b` uniquement si `a` a la valeur null.</span><span class="sxs-lookup"><span data-stu-id="3d917-2327">The operation evaluates `b` only if `a` is null.</span></span>

<span data-ttu-id="3d917-2328">L’opérateur de fusion null est associatif à droite, ce qui signifie que les opérations sont groupées de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-2328">The null coalescing operator is right-associative, meaning that operations are grouped from right to left.</span></span> <span data-ttu-id="3d917-2329">Par exemple, une expression sous la forme `a ?? b ?? c` est évalué comme `a ?? (b ?? c)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2329">For example, an expression of the form `a ?? b ?? c` is evaluated as `a ?? (b ?? c)`.</span></span> <span data-ttu-id="3d917-2330">Dans de manière générale, une expression sous la forme `E1 ?? E2 ?? ... ?? En` retourne le premier des opérandes est non null, ou null si tous les opérandes sont null.</span><span class="sxs-lookup"><span data-stu-id="3d917-2330">In general terms, an expression of the form `E1 ?? E2 ?? ... ?? En` returns the first of the operands that is non-null, or null if all operands are null.</span></span>

<span data-ttu-id="3d917-2331">Le type de l’expression `a ?? b` varie selon les conversions implicites sont disponibles sur les opérandes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2331">The type of the expression `a ?? b` depends on which implicit conversions are available on the operands.</span></span> <span data-ttu-id="3d917-2332">Dans l’ordre de préférence, le type de `a ?? b` est `A0`, `A`, ou `B`, où `A` est le type de `a` (sous réserve que `a` a un type), `B` est le type de `b` () condition que `b` a un type), et `A0` est le type sous-jacent de `A` si `A` est un type nullable, ou `A` dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="3d917-2332">In order of preference, the type of `a ?? b` is `A0`, `A`, or `B`, where `A` is the type of `a` (provided that `a` has a type), `B` is the type of `b` (provided that `b` has a type), and `A0` is the underlying type of `A` if `A` is a nullable type, or `A` otherwise.</span></span> <span data-ttu-id="3d917-2333">Plus précisément, `a ?? b` est traité comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-2333">Specifically, `a ?? b` is processed as follows:</span></span>

*  <span data-ttu-id="3d917-2334">Si `A` existe et n’est pas un type nullable ou un type référence, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-2334">If `A` exists and is not a nullable type or a reference type, a compile-time error occurs.</span></span>
*  <span data-ttu-id="3d917-2335">Si `b` est une expression dynamique, le type de résultat est `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2335">If `b` is a dynamic expression, the result type is `dynamic`.</span></span> <span data-ttu-id="3d917-2336">Au moment de l’exécution, `a` est évalué en premier.</span><span class="sxs-lookup"><span data-stu-id="3d917-2336">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="3d917-2337">Si `a` n’est pas null, `a` est converti en dynamique, et cela devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-2337">If `a` is not null, `a` is converted to dynamic, and this becomes the result.</span></span> <span data-ttu-id="3d917-2338">Sinon, `b` est évaluée, et cela devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-2338">Otherwise, `b` is evaluated, and this becomes the result.</span></span>
*  <span data-ttu-id="3d917-2339">Sinon, si `A` existe et est un type nullable et existe une conversion implicite de `b` à `A0`, le type de résultat est `A0`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2339">Otherwise, if `A` exists and is a nullable type and an implicit conversion exists from `b` to `A0`, the result type is `A0`.</span></span> <span data-ttu-id="3d917-2340">Au moment de l’exécution, `a` est évalué en premier.</span><span class="sxs-lookup"><span data-stu-id="3d917-2340">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="3d917-2341">Si `a` n’est pas null, `a` est désencapsulé pour taper `A0`, et cela devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-2341">If `a` is not null, `a` is unwrapped to type `A0`, and this becomes the result.</span></span> <span data-ttu-id="3d917-2342">Sinon, `b` est évaluée et converti en type `A0`, et cela devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-2342">Otherwise, `b` is evaluated and converted to type `A0`, and this becomes the result.</span></span>
*  <span data-ttu-id="3d917-2343">Sinon, si `A` existe et qu’une conversion implicite existe à partir de `b` à `A`, le type de résultat est `A`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2343">Otherwise, if `A` exists and an implicit conversion exists from `b` to `A`, the result type is `A`.</span></span> <span data-ttu-id="3d917-2344">Au moment de l’exécution, `a` est évalué en premier.</span><span class="sxs-lookup"><span data-stu-id="3d917-2344">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="3d917-2345">Si `a` n’est pas null, `a` devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-2345">If `a` is not null, `a` becomes the result.</span></span> <span data-ttu-id="3d917-2346">Sinon, `b` est évaluée et converti en type `A`, et cela devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-2346">Otherwise, `b` is evaluated and converted to type `A`, and this becomes the result.</span></span>
*  <span data-ttu-id="3d917-2347">Sinon, si `b` a un type `B` et existe une conversion implicite de `a` à `B`, le type de résultat est `B`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2347">Otherwise, if `b` has a type `B` and an implicit conversion exists from `a` to `B`, the result type is `B`.</span></span> <span data-ttu-id="3d917-2348">Au moment de l’exécution, `a` est évalué en premier.</span><span class="sxs-lookup"><span data-stu-id="3d917-2348">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="3d917-2349">Si `a` n’est pas null, `a` est désencapsulé pour taper `A0` (si `A` existe et est nullable) et converti en type `B`, et cela devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-2349">If `a` is not null, `a` is unwrapped to type `A0` (if `A` exists and is nullable) and converted to type `B`, and this becomes the result.</span></span> <span data-ttu-id="3d917-2350">Sinon, `b` est évaluée et devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-2350">Otherwise, `b` is evaluated and becomes the result.</span></span>
*  <span data-ttu-id="3d917-2351">Sinon, `a` et `b` sont incompatibles et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-2351">Otherwise, `a` and `b` are incompatible, and a compile-time error occurs.</span></span>

## <a name="conditional-operator"></a><span data-ttu-id="3d917-2352">Opérateur conditionnel</span><span class="sxs-lookup"><span data-stu-id="3d917-2352">Conditional operator</span></span>

<span data-ttu-id="3d917-2353">Le `?:` opérateur est appelé l’opérateur conditionnel.</span><span class="sxs-lookup"><span data-stu-id="3d917-2353">The `?:` operator is called the conditional operator.</span></span> <span data-ttu-id="3d917-2354">Il est parfois également appelé l’opérateur ternaire.</span><span class="sxs-lookup"><span data-stu-id="3d917-2354">It is at times also called the ternary operator.</span></span>

```antlr
conditional_expression
    : null_coalescing_expression
    | null_coalescing_expression '?' expression ':' expression
    ;
```

<span data-ttu-id="3d917-2355">Une expression conditionnelle de la forme `b ? x : y` évalue la condition d’abord `b`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2355">A conditional expression of the form `b ? x : y` first evaluates the condition `b`.</span></span> <span data-ttu-id="3d917-2356">Ensuite, si `b` est `true`, `x` est évaluée et devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-2356">Then, if `b` is `true`, `x` is evaluated and becomes the result of the operation.</span></span> <span data-ttu-id="3d917-2357">Sinon, `y` est évaluée et devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="3d917-2357">Otherwise, `y` is evaluated and becomes the result of the operation.</span></span> <span data-ttu-id="3d917-2358">Une expression conditionnelle évalue jamais les deux `x` et `y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2358">A conditional expression never evaluates both `x` and `y`.</span></span>

<span data-ttu-id="3d917-2359">L’opérateur conditionnel est associatif à droite, ce qui signifie que les opérations sont groupées de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-2359">The conditional operator is right-associative, meaning that operations are grouped from right to left.</span></span> <span data-ttu-id="3d917-2360">Par exemple, une expression sous la forme `a ? b : c ? d : e` est évalué comme `a ? b : (c ? d : e)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2360">For example, an expression of the form `a ? b : c ? d : e` is evaluated as `a ? b : (c ? d : e)`.</span></span>

<span data-ttu-id="3d917-2361">Le premier opérande de le `?:` opérateur doit être une expression qui peut être implicitement convertie en `bool`, ou une expression d’un type qui implémente `operator true`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2361">The first operand of the `?:` operator must be an expression that can be implicitly converted to `bool`, or an expression of a type that implements `operator true`.</span></span> <span data-ttu-id="3d917-2362">Si aucune de ces conditions n’est remplie, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-2362">If neither of these requirements is satisfied, a compile-time error occurs.</span></span>

<span data-ttu-id="3d917-2363">Les deuxième et troisième opérandes `x` et `y`, de la `?:` opérateur contrôler le type de l’expression conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="3d917-2363">The second and third operands, `x` and `y`, of the `?:` operator control the type of the conditional expression.</span></span>

*  <span data-ttu-id="3d917-2364">Si `x` a type `X` et `y` a type `Y` puis</span><span class="sxs-lookup"><span data-stu-id="3d917-2364">If `x` has type `X` and `y` has type `Y` then</span></span>
   * <span data-ttu-id="3d917-2365">Si une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir de `X` à `Y`, mais pas de `Y` à `X`, puis `Y` est le type de l’expression conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="3d917-2365">If an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from `X` to `Y`, but not from `Y` to `X`, then `Y` is the type of the conditional expression.</span></span>
   * <span data-ttu-id="3d917-2366">Si une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir de `Y` à `X`, mais pas de `X` à `Y`, puis `X` est le type de l’expression conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="3d917-2366">If an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from `Y` to `X`, but not from `X` to `Y`, then `X` is the type of the conditional expression.</span></span>
   * <span data-ttu-id="3d917-2367">Sinon, aucun type de l’expression ne peut être déterminé, et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-2367">Otherwise, no expression type can be determined, and a compile-time error occurs.</span></span>
*  <span data-ttu-id="3d917-2368">Si seul un des `x` et `y` a un type et les deux `x` et `y`, de sont implicitement convertible vers ce type, qui est le type de l’expression conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="3d917-2368">If only one of `x` and `y` has a type, and both `x` and `y`, of are implicitly convertible to that type, then that is the type of the conditional expression.</span></span>
*  <span data-ttu-id="3d917-2369">Sinon, aucun type de l’expression ne peut être déterminé, et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-2369">Otherwise, no expression type can be determined, and a compile-time error occurs.</span></span>

<span data-ttu-id="3d917-2370">Le traitement de l’exécution d’une expression conditionnelle de la forme `b ? x : y` se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-2370">The run-time processing of a conditional expression of the form `b ? x : y` consists of the following steps:</span></span>

*  <span data-ttu-id="3d917-2371">Tout d’abord, `b` est évaluée et le `bool` valeur `b` est déterminé :</span><span class="sxs-lookup"><span data-stu-id="3d917-2371">First, `b` is evaluated, and the `bool` value of `b` is determined:</span></span>
   * <span data-ttu-id="3d917-2372">Si une conversion implicite du type de `b` à `bool` existe, cette conversion implicite est effectuée pour produire un `bool` valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2372">If an implicit conversion from the type of `b` to `bool` exists, then this implicit conversion is performed to produce a `bool` value.</span></span>
   * <span data-ttu-id="3d917-2373">Sinon, le `operator true` défini par le type de `b` est appelé pour produire un `bool` valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2373">Otherwise, the `operator true` defined by the type of `b` is invoked to produce a `bool` value.</span></span>
*  <span data-ttu-id="3d917-2374">Si le `bool` valeur produite par l’étape précédente est `true`, puis `x` est évaluée et convertie vers le type de l’expression conditionnelle, et cela devient le résultat de l’expression conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="3d917-2374">If the `bool` value produced by the step above is `true`, then `x` is evaluated and converted to the type of the conditional expression, and this becomes the result of the conditional expression.</span></span>
*  <span data-ttu-id="3d917-2375">Sinon, `y` est évaluée et convertie vers le type de l’expression conditionnelle, et cela devient le résultat de l’expression conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="3d917-2375">Otherwise, `y` is evaluated and converted to the type of the conditional expression, and this becomes the result of the conditional expression.</span></span>

## <a name="anonymous-function-expressions"></a><span data-ttu-id="3d917-2376">Expressions de fonction anonyme</span><span class="sxs-lookup"><span data-stu-id="3d917-2376">Anonymous function expressions</span></span>

<span data-ttu-id="3d917-2377">Un ***fonction anonyme*** est une expression qui représente une définition de méthode de « en ligne ».</span><span class="sxs-lookup"><span data-stu-id="3d917-2377">An ***anonymous function*** is an expression that represents an "in-line" method definition.</span></span> <span data-ttu-id="3d917-2378">Une fonction anonyme n’a pas un type ou la valeur elle-même, mais est convertible en un type d’arborescence délégué ou une expression compatible.</span><span class="sxs-lookup"><span data-stu-id="3d917-2378">An anonymous function does not have a value or type in and of itself, but is convertible to a compatible delegate or expression tree type.</span></span> <span data-ttu-id="3d917-2379">L’évaluation d’une conversion de la fonction anonyme varie selon le type de cible de la conversion : Dans le cas d’un type délégué, la conversion prend une valeur de délégué faisant référence à la méthode qui définit la fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="3d917-2379">The evaluation of an anonymous function conversion depends on the target type of the conversion: If it is a delegate type, the conversion evaluates to a delegate value referencing the method which the anonymous function defines.</span></span> <span data-ttu-id="3d917-2380">Dans le cas d’un type arborescence d’expression, la conversion évalue à une arborescence d’expression qui représente la structure de la méthode comme une structure d’objet.</span><span class="sxs-lookup"><span data-stu-id="3d917-2380">If it is an expression tree type, the conversion evaluates to an expression tree which represents the structure of the method as an object structure.</span></span>

<span data-ttu-id="3d917-2381">Pour des raisons historiques il existe deux variantes syntaxiques de fonctions anonymes, à savoir *lambda_expression*s et *anonymous_method_expression*s.</span><span class="sxs-lookup"><span data-stu-id="3d917-2381">For historical reasons there are two syntactic flavors of anonymous functions, namely *lambda_expression*s and *anonymous_method_expression*s.</span></span> <span data-ttu-id="3d917-2382">À des fins de presque tous les, *lambda_expression*s sont plus concises et plus expressif que *anonymous_method_expression*s, qui restent dans la langue pour des raisons de compatibilité descendante.</span><span class="sxs-lookup"><span data-stu-id="3d917-2382">For almost all purposes, *lambda_expression*s are more concise and expressive than *anonymous_method_expression*s, which remain in the language for backwards compatibility.</span></span>

```antlr
lambda_expression
    : anonymous_function_signature '=>' anonymous_function_body
    ;

anonymous_method_expression
    : 'delegate' explicit_anonymous_function_signature? block
    ;

anonymous_function_signature
    : explicit_anonymous_function_signature
    | implicit_anonymous_function_signature
    ;

explicit_anonymous_function_signature
    : '(' explicit_anonymous_function_parameter_list? ')'
    ;

explicit_anonymous_function_parameter_list
    : explicit_anonymous_function_parameter (',' explicit_anonymous_function_parameter)*
    ;

explicit_anonymous_function_parameter
    : anonymous_function_parameter_modifier? type identifier
    ;

anonymous_function_parameter_modifier
    : 'ref'
    | 'out'
    ;

implicit_anonymous_function_signature
    : '(' implicit_anonymous_function_parameter_list? ')'
    | implicit_anonymous_function_parameter
    ;

implicit_anonymous_function_parameter_list
    : implicit_anonymous_function_parameter (',' implicit_anonymous_function_parameter)*
    ;

implicit_anonymous_function_parameter
    : identifier
    ;

anonymous_function_body
    : expression
    | block
    ;
```

<span data-ttu-id="3d917-2383">Le `=>` a la même priorité que l’assignation (`=`) et est associatif à droite.</span><span class="sxs-lookup"><span data-stu-id="3d917-2383">The `=>` operator has the same precedence as assignment (`=`) and is right-associative.</span></span>

<span data-ttu-id="3d917-2384">Une fonction anonyme avec le `async` modificateur est une fonction async et suit les règles décrites dans [itérateurs](classes.md#iterators).</span><span class="sxs-lookup"><span data-stu-id="3d917-2384">An anonymous function with the `async` modifier is an async function and follows the rules described in [Iterators](classes.md#iterators).</span></span>

<span data-ttu-id="3d917-2385">Les paramètres d’une fonction anonyme sous la forme d’un *lambda_expression* peut être explicitement ou implicitement typé.</span><span class="sxs-lookup"><span data-stu-id="3d917-2385">The parameters of an anonymous function in the form of a *lambda_expression* can be explicitly or implicitly typed.</span></span> <span data-ttu-id="3d917-2386">Dans une liste de paramètres explicitement typée, le type de chaque paramètre est défini explicitement.</span><span class="sxs-lookup"><span data-stu-id="3d917-2386">In an explicitly typed parameter list, the type of each parameter is explicitly stated.</span></span> <span data-ttu-id="3d917-2387">Dans une liste de paramètres implicitement typé, les types des paramètres sont déduits à partir du contexte dans lequel la fonction anonyme se produit, en particulier, lorsque la fonction anonyme est convertie en type délégué compatible ou type arborescence d’expression, qui fournit de type les types de paramètres ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2387">In an implicitly typed parameter list, the types of the parameters are inferred from the context in which the anonymous function occurs—specifically, when the anonymous function is converted to a compatible delegate type or expression tree type, that type provides the parameter types ([Anonymous function conversions](conversions.md#anonymous-function-conversions)).</span></span>

<span data-ttu-id="3d917-2388">Dans une fonction anonyme avec un paramètre unique, implicitement typé, les parenthèses peuvent être omises de la liste de paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-2388">In an anonymous function with a single, implicitly typed parameter, the parentheses may be omitted from the parameter list.</span></span> <span data-ttu-id="3d917-2389">En d’autres termes, une fonction anonyme du formulaire</span><span class="sxs-lookup"><span data-stu-id="3d917-2389">In other words, an anonymous function of the form</span></span>
```csharp
( param ) => expr
```
<span data-ttu-id="3d917-2390">peut être abrégé en</span><span class="sxs-lookup"><span data-stu-id="3d917-2390">can be abbreviated to</span></span>
```csharp
param => expr
```

<span data-ttu-id="3d917-2391">La liste des paramètres d’une fonction anonyme sous la forme d’un *anonymous_method_expression* est facultatif.</span><span class="sxs-lookup"><span data-stu-id="3d917-2391">The parameter list of an anonymous function in the form of an *anonymous_method_expression* is optional.</span></span> <span data-ttu-id="3d917-2392">Si spécifié, les paramètres doivent être explicitement typées.</span><span class="sxs-lookup"><span data-stu-id="3d917-2392">If given, the parameters must be explicitly typed.</span></span> <span data-ttu-id="3d917-2393">Sinon, la fonction anonyme est convertible en un délégué avec n’importe quel paramètre de liste ne contenant ne pas `out` paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-2393">If not, the anonymous function is convertible to a delegate with any parameter list not containing `out` parameters.</span></span>

<span data-ttu-id="3d917-2394">Un *bloc* corps d’une fonction anonyme est accessible ([points de terminaison et accessibilité](statements.md#end-points-and-reachability)), sauf si la fonction anonyme se produit à l’intérieur d’une instruction inaccessible.</span><span class="sxs-lookup"><span data-stu-id="3d917-2394">A *block* body of an anonymous function is reachable ([End points and reachability](statements.md#end-points-and-reachability)) unless the anonymous function occurs inside an unreachable statement.</span></span>

<span data-ttu-id="3d917-2395">Suivent les quelques exemples de fonctions anonymes ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="3d917-2395">Some examples of anonymous functions follow below:</span></span>

```csharp
x => x + 1                              // Implicitly typed, expression body
x => { return x + 1; }                  // Implicitly typed, statement body
(int x) => x + 1                        // Explicitly typed, expression body
(int x) => { return x + 1; }            // Explicitly typed, statement body
(x, y) => x * y                         // Multiple parameters
() => Console.WriteLine()               // No parameters
async (t1,t2) => await t1 + await t2    // Async
delegate (int x) { return x + 1; }      // Anonymous method expression
delegate { return 1 + 1; }              // Parameter list omitted
```

<span data-ttu-id="3d917-2396">Le comportement de *lambda_expression*s et *anonymous_method_expression*s est identique à l’exception des points suivants :</span><span class="sxs-lookup"><span data-stu-id="3d917-2396">The behavior of *lambda_expression*s and *anonymous_method_expression*s is the same except for the following points:</span></span>

*  <span data-ttu-id="3d917-2397">*anonymous_method_expression*s autoriser la liste de paramètres pour être omis, produisant la convertibilité de variance pour déléguer les types de n’importe quelle liste de paramètres de valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2397">*anonymous_method_expression*s permit the parameter list to be omitted entirely, yielding convertibility to delegate types of any list of value parameters.</span></span>
*  <span data-ttu-id="3d917-2398">*lambda_expression*s autoriser à être omis et déduit alors que les types de paramètres *anonymous_method_expression*s requièrent des types de paramètre d’être explicitement spécifié.</span><span class="sxs-lookup"><span data-stu-id="3d917-2398">*lambda_expression*s permit parameter types to be omitted and inferred whereas *anonymous_method_expression*s require parameter types to be explicitly stated.</span></span>
*  <span data-ttu-id="3d917-2399">Le corps d’un *lambda_expression* peut être une expression ou un bloc d’instructions tandis que le corps d’un *anonymous_method_expression* doit être un bloc d’instructions.</span><span class="sxs-lookup"><span data-stu-id="3d917-2399">The body of a *lambda_expression* can be an expression or a statement block whereas the body of an *anonymous_method_expression* must be a statement block.</span></span>
*  <span data-ttu-id="3d917-2400">Uniquement *lambda_expression*s ont des conversions de types d’arborescence expression compatible ([types arborescence d’Expression](types.md#expression-tree-types)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2400">Only *lambda_expression*s have conversions to compatible expression tree types ([Expression tree types](types.md#expression-tree-types)).</span></span>

### <a name="anonymous-function-signatures"></a><span data-ttu-id="3d917-2401">Signatures de fonction anonyme</span><span class="sxs-lookup"><span data-stu-id="3d917-2401">Anonymous function signatures</span></span>

<span data-ttu-id="3d917-2402">Le paramètre facultatif *anonymous_function_signature* d’une fonction anonyme définit les noms et éventuellement les types des paramètres formels de la fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="3d917-2402">The optional *anonymous_function_signature* of an anonymous function defines the names and optionally the types of the formal parameters for the anonymous function.</span></span> <span data-ttu-id="3d917-2403">L’étendue des paramètres de la fonction anonyme est la *anonymous_function_body*.</span><span class="sxs-lookup"><span data-stu-id="3d917-2403">The scope of the parameters of the anonymous function is the *anonymous_function_body*.</span></span> <span data-ttu-id="3d917-2404">([Étendues](basic-concepts.md#scopes)) ainsi que la liste de paramètres (si fournie) anonymes-méthode-body constitue un espace de déclaration ([déclarations](basic-concepts.md#declarations)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2404">([Scopes](basic-concepts.md#scopes)) Together with the parameter list (if given) the anonymous-method-body constitutes a declaration space ([Declarations](basic-concepts.md#declarations)).</span></span> <span data-ttu-id="3d917-2405">Il est donc une erreur de compilation pour le nom d’un paramètre de la fonction anonyme pour correspondre au nom d’une variable locale, la constante locale ou le paramètre dont l’étendue inclut la *anonymous_method_expression* ou *lambda_ expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-2405">It is thus a compile-time error for the name of a parameter of the anonymous function to match the name of a local variable, local constant or parameter whose scope includes the *anonymous_method_expression* or *lambda_expression*.</span></span>

<span data-ttu-id="3d917-2406">Si une fonction anonyme a une *explicit_anonymous_function_signature*, puis l’ensemble des types délégués compatibles et types arborescence d’expression est limitée à ceux qui ont les mêmes types de paramètres et les modificateurs dans le même ordre.</span><span class="sxs-lookup"><span data-stu-id="3d917-2406">If an anonymous function has an *explicit_anonymous_function_signature*, then the set of compatible delegate types and expression tree types is restricted to those that have the same parameter types and modifiers in the same order.</span></span> <span data-ttu-id="3d917-2407">Contrairement aux conversions de groupe (méthode) ([conversions de groupes de méthode](conversions.md#method-group-conversions)), contra-variance des types de paramètres de fonction anonyme n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="3d917-2407">In contrast to method group conversions ([Method group conversions](conversions.md#method-group-conversions)), contra-variance of anonymous function parameter types is not supported.</span></span> <span data-ttu-id="3d917-2408">Si une fonction anonyme n’est pas un *anonymous_function_signature*, puis l’ensemble des types délégués compatibles et types arborescence d’expression est limitée à ceux qui n’ont pas `out` paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-2408">If an anonymous function does not have an *anonymous_function_signature*, then the set of compatible delegate types and expression tree types is restricted to those that have no `out` parameters.</span></span>

<span data-ttu-id="3d917-2409">Notez qu’un *anonymous_function_signature* ne peut pas inclure des attributs ou un tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-2409">Note that an *anonymous_function_signature* cannot include attributes or a parameter array.</span></span> <span data-ttu-id="3d917-2410">Néanmoins, une *anonymous_function_signature* peut être compatible avec un type de délégué dont la liste paramètre contient un tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="3d917-2410">Nevertheless, an *anonymous_function_signature* may be compatible with a delegate type whose parameter list contains a parameter array.</span></span>

<span data-ttu-id="3d917-2411">Notez également que la conversion vers un type arborescence d’expression, même si compatible, peut encore échouer au moment de la compilation ([types arborescence d’Expression](types.md#expression-tree-types)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2411">Note also that conversion to an expression tree type, even if compatible, may still fail at compile-time ([Expression tree types](types.md#expression-tree-types)).</span></span>

### <a name="anonymous-function-bodies"></a><span data-ttu-id="3d917-2412">Corps de fonction anonyme</span><span class="sxs-lookup"><span data-stu-id="3d917-2412">Anonymous function bodies</span></span>

<span data-ttu-id="3d917-2413">Le corps (*expression* ou *bloc*) d’une fonction anonyme est soumis aux règles suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-2413">The body (*expression* or *block*) of an anonymous function is subject to the following rules:</span></span>

*  <span data-ttu-id="3d917-2414">Si la fonction anonyme inclut une signature, les paramètres spécifiés dans la signature sont disponibles dans le corps.</span><span class="sxs-lookup"><span data-stu-id="3d917-2414">If the anonymous function includes a signature, the parameters specified in the signature are available in the body.</span></span> <span data-ttu-id="3d917-2415">Si la fonction anonyme n’a aucune signature elle peut être convertie à un type de délégué ou d’un type d’expression ayant des paramètres ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)), mais les paramètres ne sont pas accessibles dans le corps.</span><span class="sxs-lookup"><span data-stu-id="3d917-2415">If the anonymous function has no signature it can be converted to a delegate type or expression type having parameters ([Anonymous function conversions](conversions.md#anonymous-function-conversions)), but the parameters cannot be accessed in the body.</span></span>
*  <span data-ttu-id="3d917-2416">À l’exception de `ref` ou `out` paramètres spécifiés dans la signature (le cas échéant) de l’englobante la plus proche fonction anonyme, il est une erreur de compilation pour le corps d’accéder à un `ref` ou `out` paramètre.</span><span class="sxs-lookup"><span data-stu-id="3d917-2416">Except for `ref` or `out` parameters specified in the signature (if any) of the nearest enclosing anonymous function, it is a compile-time error for the body to access a `ref` or `out` parameter.</span></span>
*  <span data-ttu-id="3d917-2417">Lorsque le type de `this` est un type struct, c’est une erreur de compilation pour le corps d’accéder à `this`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2417">When the type of `this` is a struct type, it is a compile-time error for the body to access `this`.</span></span> <span data-ttu-id="3d917-2418">Cela est vrai si l’accès est explicite (comme dans `this.x`) ou implicites (comme dans `x` où `x` est un membre d’instance du struct).</span><span class="sxs-lookup"><span data-stu-id="3d917-2418">This is true whether the access is explicit (as in `this.x`) or implicit (as in `x` where `x` is an instance member of the struct).</span></span> <span data-ttu-id="3d917-2419">Cette règle simplement interdit l’accès et n’affecte pas si les résultats de recherche de membres dans un membre du struct.</span><span class="sxs-lookup"><span data-stu-id="3d917-2419">This rule simply prohibits such access and does not affect whether member lookup results in a member of the struct.</span></span>
*  <span data-ttu-id="3d917-2420">Le corps a accès aux variables externes ([Outer variables](expressions.md#outer-variables)) de la fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="3d917-2420">The body has access to the outer variables ([Outer variables](expressions.md#outer-variables)) of the anonymous function.</span></span> <span data-ttu-id="3d917-2421">Accès d’une variable externe fait référence à l’instance de la variable qui est active au moment où le *lambda_expression* ou *anonymous_method_expression* est évaluée ([version d’évaluation de expressions de fonction anonyme](expressions.md#evaluation-of-anonymous-function-expressions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2421">Access of an outer variable will reference the instance of the variable that is active at the time the *lambda_expression* or *anonymous_method_expression* is evaluated ([Evaluation of anonymous function expressions](expressions.md#evaluation-of-anonymous-function-expressions)).</span></span>
*  <span data-ttu-id="3d917-2422">C’est une erreur de compilation pour le corps contienne un `goto` instruction, `break` instruction, ou `continue` instruction dont la cible est en dehors du corps ou dans le corps d’une fonction anonyme contenue.</span><span class="sxs-lookup"><span data-stu-id="3d917-2422">It is a compile-time error for the body to contain a `goto` statement, `break` statement, or `continue` statement whose target is outside the body or within the body of a contained anonymous function.</span></span>
*  <span data-ttu-id="3d917-2423">Un `return` retourne le contrôle à partir d’un appel à l’englobante la plus proche instruction dans le corps de la fonction anonyme, pas à partir de la fonction membre englobante.</span><span class="sxs-lookup"><span data-stu-id="3d917-2423">A `return` statement in the body returns control from an invocation of the nearest enclosing anonymous function, not from the enclosing function member.</span></span> <span data-ttu-id="3d917-2424">Une expression spécifiée dans un `return` instruction doit être implicitement convertible au type de retour du type de délégué ou de type arborescence d’expression à laquelle l’englobante la plus proche *lambda_expression* ou *anonymous_ method_expression* est converti ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2424">An expression specified in a `return` statement must be implicitly convertible to the return type of the delegate type or expression tree type to which the nearest enclosing *lambda_expression* or *anonymous_method_expression* is converted ([Anonymous function conversions](conversions.md#anonymous-function-conversions)).</span></span>

<span data-ttu-id="3d917-2425">Il est explicitement non spécifiée s’il existe un moyen d’exécuter le bloc d’une fonction anonyme autre que par le biais d’évaluation et l’appel de la *lambda_expression* ou *anonymous_method_expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-2425">It is explicitly unspecified whether there is any way to execute the block of an anonymous function other than through evaluation and invocation of the *lambda_expression* or *anonymous_method_expression*.</span></span> <span data-ttu-id="3d917-2426">En particulier, le compilateur peut choisir d’implémenter une fonction anonyme en synthétisant une ou plusieurs méthodes ou des types.</span><span class="sxs-lookup"><span data-stu-id="3d917-2426">In particular, the compiler may choose to implement an anonymous function by synthesizing one or more named methods or types.</span></span> <span data-ttu-id="3d917-2427">Les noms de ces éléments synthétisées doivent être d’un formulaire réservé à l’utilisation par le compilateur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2427">The names of any such synthesized elements must be of a form reserved for compiler use.</span></span>

### <a name="overload-resolution-and-anonymous-functions"></a><span data-ttu-id="3d917-2428">Résolution de surcharge et les fonctions anonymes</span><span class="sxs-lookup"><span data-stu-id="3d917-2428">Overload resolution and anonymous functions</span></span>

<span data-ttu-id="3d917-2429">Les fonctions anonymes dans une liste d’arguments participent à l’inférence de type et la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="3d917-2429">Anonymous functions in an argument list participate in type inference and overload resolution.</span></span> <span data-ttu-id="3d917-2430">Reportez-vous à [l’inférence de Type](expressions.md#type-inference) et [la résolution de surcharge](expressions.md#overload-resolution) pour connaître les règles exactes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2430">Please refer to [Type inference](expressions.md#type-inference) and [Overload resolution](expressions.md#overload-resolution) for the exact rules.</span></span>

<span data-ttu-id="3d917-2431">L’exemple suivant illustre l’effet de fonctions anonymes sur la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="3d917-2431">The following example illustrates the effect of anonymous functions on overload resolution.</span></span>

```csharp
class ItemList<T>: List<T>
{
    public int Sum(Func<T,int> selector) {
        int sum = 0;
        foreach (T item in this) sum += selector(item);
        return sum;
    }

    public double Sum(Func<T,double> selector) {
        double sum = 0;
        foreach (T item in this) sum += selector(item);
        return sum;
    }
}
```

<span data-ttu-id="3d917-2432">Le `ItemList<T>` classe a deux `Sum` méthodes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2432">The `ItemList<T>` class has two `Sum` methods.</span></span> <span data-ttu-id="3d917-2433">Chacun prend un `selector` argument, qui extrait la valeur à la somme sur un élément de liste.</span><span class="sxs-lookup"><span data-stu-id="3d917-2433">Each takes a `selector` argument, which extracts the value to sum over from a list item.</span></span> <span data-ttu-id="3d917-2434">La valeur extraite peut être soit un `int` ou un `double` et la somme résultante est de même un `int` ou un `double`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2434">The extracted value can be either an `int` or a `double` and the resulting sum is likewise either an `int` or a `double`.</span></span>

<span data-ttu-id="3d917-2435">Le `Sum` méthodes pourraient par exemple être utilisées pour calculer la somme à partir d’une liste des lignes de détails dans un ordre.</span><span class="sxs-lookup"><span data-stu-id="3d917-2435">The `Sum` methods could for example be used to compute sums from a list of detail lines in an order.</span></span>

```csharp
class Detail
{
    public int UnitCount;
    public double UnitPrice;
    ...
}

void ComputeSums() {
    ItemList<Detail> orderDetails = GetOrderDetails(...);
    int totalUnits = orderDetails.Sum(d => d.UnitCount);
    double orderTotal = orderDetails.Sum(d => d.UnitPrice * d.UnitCount);
    ...
}
```

<span data-ttu-id="3d917-2436">Dans la première invocation de `orderDetails.Sum`, à la fois `Sum` méthodes sont applicables, car la fonction anonyme `d => d. UnitCount` est compatible avec les deux `Func<Detail,int>` et `Func<Detail,double>`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2436">In the first invocation of `orderDetails.Sum`, both `Sum` methods are applicable because the anonymous function `d => d. UnitCount` is compatible with both `Func<Detail,int>` and `Func<Detail,double>`.</span></span> <span data-ttu-id="3d917-2437">Toutefois, la résolution de surcharge sélectionne la première `Sum` (méthode), car la conversion en `Func<Detail,int>` est meilleure que la conversion en `Func<Detail,double>`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2437">However, overload resolution picks the first `Sum` method because the conversion to `Func<Detail,int>` is better than the conversion to `Func<Detail,double>`.</span></span>

<span data-ttu-id="3d917-2438">Dans le deuxième appel de `orderDetails.Sum`, seul le deuxième `Sum` méthode s’applique, car la fonction anonyme `d => d.UnitPrice * d.UnitCount` produit une valeur de type `double`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2438">In the second invocation of `orderDetails.Sum`, only the second `Sum` method is applicable because the anonymous function `d => d.UnitPrice * d.UnitCount` produces a value of type `double`.</span></span> <span data-ttu-id="3d917-2439">Par conséquent, la surcharge résolution choisit la seconde `Sum` méthode pour cet appel.</span><span class="sxs-lookup"><span data-stu-id="3d917-2439">Thus, overload resolution picks the second `Sum` method for that invocation.</span></span>

### <a name="anonymous-functions-and-dynamic-binding"></a><span data-ttu-id="3d917-2440">Liaison dynamique et fonctions anonymes</span><span class="sxs-lookup"><span data-stu-id="3d917-2440">Anonymous functions and dynamic binding</span></span>

<span data-ttu-id="3d917-2441">Une fonction anonyme ne peut pas être un destinataire, un argument ou un opérande d’une opération dynamique liée.</span><span class="sxs-lookup"><span data-stu-id="3d917-2441">An anonymous function cannot be a receiver, argument or operand of a dynamically bound operation.</span></span>

### <a name="outer-variables"></a><span data-ttu-id="3d917-2442">Variables externes</span><span class="sxs-lookup"><span data-stu-id="3d917-2442">Outer variables</span></span>

<span data-ttu-id="3d917-2443">Toute variable locale, un paramètre de valeur ou un tableau de paramètres dont l’étendue inclut la *lambda_expression* ou *anonymous_method_expression* est appelée un ***variable externe*** de la fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="3d917-2443">Any local variable, value parameter, or parameter array whose scope includes the *lambda_expression* or *anonymous_method_expression* is called an ***outer variable*** of the anonymous function.</span></span> <span data-ttu-id="3d917-2444">Dans une fonction membre d’instance d’une classe, le `this` valeur est considérée comme un paramètre de valeur et est une variable externe d’une fonction anonyme contenue dans la fonction membre.</span><span class="sxs-lookup"><span data-stu-id="3d917-2444">In an instance function member of a class, the `this` value is considered a value parameter and is an outer variable of any anonymous function contained within the function member.</span></span>

#### <a name="captured-outer-variables"></a><span data-ttu-id="3d917-2445">Capture des variables externes</span><span class="sxs-lookup"><span data-stu-id="3d917-2445">Captured outer variables</span></span>

<span data-ttu-id="3d917-2446">Lorsqu’une variable externe est référencée par une fonction anonyme, la variable externe est dite avoir été ***capturées*** par la fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="3d917-2446">When an outer variable is referenced by an anonymous function, the outer variable is said to have been ***captured*** by the anonymous function.</span></span> <span data-ttu-id="3d917-2447">En règle générale, la durée de vie d’une variable locale est limitée à l’exécution de l’instruction auquel il est associé ou le bloc ([variables locales](variables.md#local-variables)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2447">Ordinarily, the lifetime of a local variable is limited to execution of the block or statement with which it is associated ([Local variables](variables.md#local-variables)).</span></span> <span data-ttu-id="3d917-2448">Toutefois, la durée de vie d’une variable externe capturée est étendue au moins jusqu'à ce que le délégué ou arborescence de l’expression créée à partir de la fonction anonyme devient éligible pour le garbage collection.</span><span class="sxs-lookup"><span data-stu-id="3d917-2448">However, the lifetime of a captured outer variable is extended at least until the delegate or expression tree created from the anonymous function becomes eligible for garbage collection.</span></span>

<span data-ttu-id="3d917-2449">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2449">In the example</span></span>
```csharp
using System;

delegate int D();

class Test
{
    static D F() {
        int x = 0;
        D result = () => ++x;
        return result;
    }

    static void Main() {
        D d = F();
        Console.WriteLine(d());
        Console.WriteLine(d());
        Console.WriteLine(d());
    }
}
```
<span data-ttu-id="3d917-2450">la variable locale `x` est capturé par la fonction anonyme et la durée de vie de `x` est étendue au moins jusqu'à ce que le délégué retourné par `F` devient éligible pour le garbage collection (qui ne se produit pas jusqu'à la fin de le programme).</span><span class="sxs-lookup"><span data-stu-id="3d917-2450">the local variable `x` is captured by the anonymous function, and the lifetime of `x` is extended at least until the delegate returned from `F` becomes eligible for garbage collection (which doesn't happen until the very end of the program).</span></span> <span data-ttu-id="3d917-2451">Étant donné que chaque appel de la fonction anonyme opère sur la même instance de `x`, la sortie de l’exemple est :</span><span class="sxs-lookup"><span data-stu-id="3d917-2451">Since each invocation of the anonymous function operates on the same instance of `x`, the output of the example is:</span></span>
```
1
2
3
```

<span data-ttu-id="3d917-2452">Lorsqu’une variable locale ou un paramètre de valeur est capturé par une fonction anonyme, la variable locale ou du paramètre est n’est plus considéré comme une variable fixe ([variables fixes et déplaçables](unsafe-code.md#fixed-and-moveable-variables)), mais est considéré à la place comme un moveable variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-2452">When a local variable or a value parameter is captured by an anonymous function, the local variable or parameter is no longer considered to be a fixed variable ([Fixed and moveable variables](unsafe-code.md#fixed-and-moveable-variables)), but is instead considered to be a moveable variable.</span></span> <span data-ttu-id="3d917-2453">Par conséquent, les `unsafe` le code qui prend l’adresse d’une variable capturée externe doit tout d’abord utiliser la `fixed` instruction pour corriger la variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-2453">Thus any `unsafe` code that takes the address of a captured outer variable must first use the `fixed` statement to fix the variable.</span></span>

<span data-ttu-id="3d917-2454">Notez que contrairement à une variable de rejet, une variable locale capturée peut être exposée simultanément à plusieurs threads d’exécution.</span><span class="sxs-lookup"><span data-stu-id="3d917-2454">Note that unlike an uncaptured variable, a captured local variable can be simultaneously exposed to multiple threads of execution.</span></span>

#### <a name="instantiation-of-local-variables"></a><span data-ttu-id="3d917-2455">Instanciation des variables locales</span><span class="sxs-lookup"><span data-stu-id="3d917-2455">Instantiation of local variables</span></span>

<span data-ttu-id="3d917-2456">Une variable locale est considéré comme étant ***instancié*** lorsque l’exécution entre l’étendue de la variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-2456">A local variable is considered to be ***instantiated*** when execution enters the scope of the variable.</span></span> <span data-ttu-id="3d917-2457">Par exemple, quand la méthode suivante est appelée, la variable locale `x` est instancié et initialisé trois fois, une fois pour chaque itération de la boucle.</span><span class="sxs-lookup"><span data-stu-id="3d917-2457">For example, when the following method is invoked, the local variable `x` is instantiated and initialized three times—once for each iteration of the loop.</span></span>

```csharp
static void F() {
    for (int i = 0; i < 3; i++) {
        int x = i * 2 + 1;
        ...
    }
}
```

<span data-ttu-id="3d917-2458">Toutefois, déplacer la déclaration de `x` à l’extérieur les résultats de la boucle dans une instanciation unique de `x`:</span><span class="sxs-lookup"><span data-stu-id="3d917-2458">However, moving the declaration of `x` outside the loop results in a single instantiation of `x`:</span></span>
```csharp
static void F() {
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        ...
    }
}
```

<span data-ttu-id="3d917-2459">Lorsque l’élément ne pas capturé, il n’existe aucun moyen d’observer exactement la fréquence à laquelle une variable locale est instanciée, car les durées de vie des instanciations sont disjointes, il est possible pour chaque instanciation simplement d’utiliser le même emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="3d917-2459">When not captured, there is no way to observe exactly how often a local variable is instantiated—because the lifetimes of the instantiations are disjoint, it is possible for each instantiation to simply use the same storage location.</span></span> <span data-ttu-id="3d917-2460">Toutefois, lorsqu’une fonction anonyme capture une variable locale, les effets de l’instanciation apparu.</span><span class="sxs-lookup"><span data-stu-id="3d917-2460">However, when an anonymous function captures a local variable, the effects of instantiation become apparent.</span></span>

<span data-ttu-id="3d917-2461">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2461">The example</span></span>
```csharp
using System;

delegate void D();

class Test
{
    static D[] F() {
        D[] result = new D[3];
        for (int i = 0; i < 3; i++) {
            int x = i * 2 + 1;
            result[i] = () => { Console.WriteLine(x); };
        }
        return result;
    }

    static void Main() {
        foreach (D d in F()) d();
    }
}
```
<span data-ttu-id="3d917-2462">génère cette sortie :</span><span class="sxs-lookup"><span data-stu-id="3d917-2462">produces the output:</span></span>
```
1
3
5
```

<span data-ttu-id="3d917-2463">Toutefois, lorsque la déclaration de `x` est déplacé en dehors de la boucle :</span><span class="sxs-lookup"><span data-stu-id="3d917-2463">However, when the declaration of `x` is moved outside the loop:</span></span>
```csharp
static D[] F() {
    D[] result = new D[3];
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        result[i] = () => { Console.WriteLine(x); };
    }
    return result;
}
```
<span data-ttu-id="3d917-2464">La sortie est :</span><span class="sxs-lookup"><span data-stu-id="3d917-2464">the output is:</span></span>
```
5
5
5
```

<span data-ttu-id="3d917-2465">Si une boucle for déclare une variable d’itération, cette variable lui-même est considéré comme déclaré en dehors de la boucle.</span><span class="sxs-lookup"><span data-stu-id="3d917-2465">If a for-loop declares an iteration variable, that variable itself is considered to be declared outside of the loop.</span></span> <span data-ttu-id="3d917-2466">Par conséquent, si l’exemple est modifié pour capturer la variable d’itération :</span><span class="sxs-lookup"><span data-stu-id="3d917-2466">Thus, if the example is changed to capture the iteration variable itself:</span></span>

```csharp
static D[] F() {
    D[] result = new D[3];
    for (int i = 0; i < 3; i++) {
        result[i] = () => { Console.WriteLine(i); };
    }
    return result;
}
```
<span data-ttu-id="3d917-2467">seule une instance de la variable d’itération est capturée, ce qui génère la sortie :</span><span class="sxs-lookup"><span data-stu-id="3d917-2467">only one instance of the iteration variable is captured, which produces the output:</span></span>
```
3
3
3
```

<span data-ttu-id="3d917-2468">Il est possible pour les délégués de fonction anonyme à partager certaines variables capturées encore avoir des instances distinctes d’autres personnes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2468">It is possible for anonymous function delegates to share some captured variables yet have separate instances of others.</span></span> <span data-ttu-id="3d917-2469">Par exemple, si `F` est remplacée par</span><span class="sxs-lookup"><span data-stu-id="3d917-2469">For example, if `F` is changed to</span></span>
```csharp
static D[] F() {
    D[] result = new D[3];
    int x = 0;
    for (int i = 0; i < 3; i++) {
        int y = 0;
        result[i] = () => { Console.WriteLine("{0} {1}", ++x, ++y); };
    }
    return result;
}
```
<span data-ttu-id="3d917-2470">les trois délégués capturer la même instance de `x` , mais des instances distinctes de `y`, et la sortie est :</span><span class="sxs-lookup"><span data-stu-id="3d917-2470">the three delegates capture the same instance of `x` but separate instances of `y`, and the output is:</span></span>
```
1 1
2 1
3 1
```

<span data-ttu-id="3d917-2471">Séparer les fonctions anonymes peuvent capturer la même instance d’une variable externe.</span><span class="sxs-lookup"><span data-stu-id="3d917-2471">Separate anonymous functions can capture the same instance of an outer variable.</span></span> <span data-ttu-id="3d917-2472">Dans l'exemple :</span><span class="sxs-lookup"><span data-stu-id="3d917-2472">In the example:</span></span>
```csharp
using System;

delegate void Setter(int value);

delegate int Getter();

class Test
{
    static void Main() {
        int x = 0;
        Setter s = (int value) => { x = value; };
        Getter g = () => { return x; };
        s(5);
        Console.WriteLine(g());
        s(10);
        Console.WriteLine(g());
    }
}
```
<span data-ttu-id="3d917-2473">les deux fonctions anonymes capturer la même instance de la variable locale `x`, et ils peuvent ainsi « communiquer » via cette variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-2473">the two anonymous functions capture the same instance of the local variable `x`, and they can thus "communicate" through that variable.</span></span> <span data-ttu-id="3d917-2474">La sortie de l’exemple est :</span><span class="sxs-lookup"><span data-stu-id="3d917-2474">The output of the example is:</span></span>
```
5
10
```

### <a name="evaluation-of-anonymous-function-expressions"></a><span data-ttu-id="3d917-2475">Évaluation des expressions de fonction anonyme</span><span class="sxs-lookup"><span data-stu-id="3d917-2475">Evaluation of anonymous function expressions</span></span>

<span data-ttu-id="3d917-2476">Une fonction anonyme `F` doit toujours être convertie en un type délégué `D` ou un type d’arborescence expression `E`, directement ou via l’exécution d’une expression de création de délégué `new D(F)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2476">An anonymous function `F` must always be converted to a delegate type `D` or an expression tree type `E`, either directly or through the execution of a delegate creation expression `new D(F)`.</span></span> <span data-ttu-id="3d917-2477">Cette conversion détermine le résultat de la fonction anonyme, comme décrit dans [conversions de fonctions anonymes](conversions.md#anonymous-function-conversions).</span><span class="sxs-lookup"><span data-stu-id="3d917-2477">This conversion determines the result of the anonymous function, as described in [Anonymous function conversions](conversions.md#anonymous-function-conversions).</span></span>

## <a name="query-expressions"></a><span data-ttu-id="3d917-2478">Expressions de requête</span><span class="sxs-lookup"><span data-stu-id="3d917-2478">Query expressions</span></span>

<span data-ttu-id="3d917-2479">***Expressions de requête*** fournissent une syntaxe de langage intégrée pour les requêtes qui est similaire aux langages de requête hiérarchiques et relationnelles telles que SQL ou XQuery.</span><span class="sxs-lookup"><span data-stu-id="3d917-2479">***Query expressions*** provide a language integrated syntax for queries that is similar to relational and hierarchical query languages such as SQL and XQuery.</span></span>

```antlr
query_expression
    : from_clause query_body
    ;

from_clause
    : 'from' type? identifier 'in' expression
    ;

query_body
    : query_body_clauses? select_or_group_clause query_continuation?
    ;

query_body_clauses
    : query_body_clause
    | query_body_clauses query_body_clause
    ;

query_body_clause
    : from_clause
    | let_clause
    | where_clause
    | join_clause
    | join_into_clause
    | orderby_clause
    ;

let_clause
    : 'let' identifier '=' expression
    ;

where_clause
    : 'where' boolean_expression
    ;

join_clause
    : 'join' type? identifier 'in' expression 'on' expression 'equals' expression
    ;

join_into_clause
    : 'join' type? identifier 'in' expression 'on' expression 'equals' expression 'into' identifier
    ;

orderby_clause
    : 'orderby' orderings
    ;

orderings
    : ordering (',' ordering)*
    ;

ordering
    : expression ordering_direction?
    ;

ordering_direction
    : 'ascending'
    | 'descending'
    ;

select_or_group_clause
    : select_clause
    | group_clause
    ;

select_clause
    : 'select' expression
    ;

group_clause
    : 'group' expression 'by' expression
    ;

query_continuation
    : 'into' identifier query_body
    ;
```

<span data-ttu-id="3d917-2480">Une expression de requête commence par un `from` clause et se termine par soit un `select` ou `group` clause.</span><span class="sxs-lookup"><span data-stu-id="3d917-2480">A query expression begins with a `from` clause and ends with either a `select` or `group` clause.</span></span> <span data-ttu-id="3d917-2481">Initial `from` clause peut être suivie de zéro ou plusieurs `from`, `let`, `where`, `join` ou `orderby` clauses.</span><span class="sxs-lookup"><span data-stu-id="3d917-2481">The initial `from` clause can be followed by zero or more `from`, `let`, `where`, `join` or `orderby` clauses.</span></span> <span data-ttu-id="3d917-2482">Chaque `from` clause est un générateur de présentation un ***variable de portée*** qui couvre les éléments d’un ***séquence***.</span><span class="sxs-lookup"><span data-stu-id="3d917-2482">Each `from` clause is a generator introducing a ***range variable*** which ranges over the elements of a ***sequence***.</span></span> <span data-ttu-id="3d917-2483">Chaque `let` clause introduit une variable de portée qui représente une valeur calculée au moyen de variables de portée précédente.</span><span class="sxs-lookup"><span data-stu-id="3d917-2483">Each `let` clause introduces a range variable representing a value computed by means of previous range variables.</span></span> <span data-ttu-id="3d917-2484">Chaque `where` clause est un filtre qui exclut des éléments à partir du résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-2484">Each `where` clause is a filter that excludes items from the result.</span></span> <span data-ttu-id="3d917-2485">Chaque `join` clause compare les clés spécifiées de la séquence source avec des clés d’une autre séquence, produisant des paires correspondantes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2485">Each `join` clause compares specified keys of the source sequence with keys of another sequence, yielding matching pairs.</span></span> <span data-ttu-id="3d917-2486">Chaque `orderby` clause réorganise les éléments en fonction de critères spécifiés. La dernière `select` ou `group` clause spécifie la forme du résultat en termes des variables de plage.</span><span class="sxs-lookup"><span data-stu-id="3d917-2486">Each `orderby` clause reorders items according to specified criteria.The final `select` or `group` clause specifies the shape of the result in terms of the range variables.</span></span> <span data-ttu-id="3d917-2487">Enfin, un `into` clause peut être utilisée pour « splice « requêtes en traitant les résultats d’une requête en tant que générateur dans une requête suivante.</span><span class="sxs-lookup"><span data-stu-id="3d917-2487">Finally, an `into` clause can be used to "splice" queries by treating the results of one query as a generator in a subsequent query.</span></span>

### <a name="ambiguities-in-query-expressions"></a><span data-ttu-id="3d917-2488">Ambiguïtés dans les expressions de requête</span><span class="sxs-lookup"><span data-stu-id="3d917-2488">Ambiguities in query expressions</span></span>

<span data-ttu-id="3d917-2489">Expressions de requête contiennent un nombre de « mots clés contextuels », par exemple, les identificateurs qui ont une signification spéciale dans un contexte donné.</span><span class="sxs-lookup"><span data-stu-id="3d917-2489">Query expressions contain a number of "contextual keywords", i.e., identifiers that have special meaning in a given context.</span></span> <span data-ttu-id="3d917-2490">Plus précisément, il s’agit de `from`, `where`, `join`, `on`, `equals`, `into`, `let`, `orderby`, `ascending`, `descending`, `select`, `group` et `by`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2490">Specifically these are `from`, `where`, `join`, `on`, `equals`, `into`, `let`, `orderby`, `ascending`, `descending`, `select`, `group` and `by`.</span></span> <span data-ttu-id="3d917-2491">Afin d’éviter les ambiguïtés dans les expressions de requête causées par une utilisation mixte de ces identificateurs en tant que mots clés ou des noms simples, ces identificateurs sont considérés comme mots clés lors de n’importe où dans une expression de requête.</span><span class="sxs-lookup"><span data-stu-id="3d917-2491">In order to avoid ambiguities in query expressions caused by mixed use of these identifiers as keywords or simple names, these identifiers are considered keywords when occurring anywhere within a query expression.</span></span>

<span data-ttu-id="3d917-2492">À cet effet, une expression de requête est toute expression qui commence par «`from identifier`« suivie de n’importe quel jeton à l’exception »`;`«, »`=`« ou »`,`».</span><span class="sxs-lookup"><span data-stu-id="3d917-2492">For this purpose, a query expression is any expression that starts with "`from identifier`" followed by any token except "`;`", "`=`" or "`,`".</span></span>

<span data-ttu-id="3d917-2493">Pour pouvoir utiliser ces mots comme identificateurs dans une expression de requête, ils peuvent être le préfixe «`@`» ([identificateurs](lexical-structure.md#identifiers)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2493">In order to use these words as identifiers within a query expression, they can be prefixed with "`@`" ([Identifiers](lexical-structure.md#identifiers)).</span></span>

### <a name="query-expression-translation"></a><span data-ttu-id="3d917-2494">Traduction des expressions de requête</span><span class="sxs-lookup"><span data-stu-id="3d917-2494">Query expression translation</span></span>

<span data-ttu-id="3d917-2495">Le langage c# ne spécifie pas la sémantique d’exécution d’expressions de requête.</span><span class="sxs-lookup"><span data-stu-id="3d917-2495">The C# language does not specify the execution semantics of query expressions.</span></span> <span data-ttu-id="3d917-2496">Au lieu de cela, les expressions de requête sont traduites en appels de méthodes qui respectent le *modèle d’expression de requête* ([le modèle d’expression de requête](expressions.md#the-query-expression-pattern)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2496">Rather, query expressions are translated into invocations of methods that adhere to the *query expression pattern* ([The query expression pattern](expressions.md#the-query-expression-pattern)).</span></span> <span data-ttu-id="3d917-2497">Plus précisément, les expressions de requête sont traduites en appels de méthodes nommées `Where`, `Select`, `SelectMany`, `Join`, `GroupJoin`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `GroupBy`, et `Cast`. Ces méthodes sont supposées comporter des signatures particuliers et les types de résultats, comme décrit dans [le modèle d’expression de requête](expressions.md#the-query-expression-pattern).</span><span class="sxs-lookup"><span data-stu-id="3d917-2497">Specifically, query expressions are translated into invocations of methods named `Where`, `Select`, `SelectMany`, `Join`, `GroupJoin`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `GroupBy`, and `Cast`.These methods are expected to have particular signatures and result types, as described in [The query expression pattern](expressions.md#the-query-expression-pattern).</span></span> <span data-ttu-id="3d917-2498">Ces méthodes peuvent être des méthodes d’instance de l’objet en cours d’interrogation ou des méthodes d’extension qui sont externes à l’objet, et qu’ils implémentent l’exécution réelle de la requête.</span><span class="sxs-lookup"><span data-stu-id="3d917-2498">These methods can be instance methods of the object being queried or extension methods that are external to the object, and they implement the actual execution of the query.</span></span>

<span data-ttu-id="3d917-2499">La traduction à partir d’expressions de requête aux appels de méthode est un mappage syntaxique qui se produit avant la liaison de n’importe quel type ou la résolution de surcharge a été effectuée.</span><span class="sxs-lookup"><span data-stu-id="3d917-2499">The translation from query expressions to method invocations is a syntactic mapping that occurs before any type binding or overload resolution has been performed.</span></span> <span data-ttu-id="3d917-2500">La traduction est garantie est syntaxiquement correct, mais il n’est pas garanti pour produire le code c# sémantiquement correct.</span><span class="sxs-lookup"><span data-stu-id="3d917-2500">The translation is guaranteed to be syntactically correct, but it is not guaranteed to produce semantically correct C# code.</span></span> <span data-ttu-id="3d917-2501">Après la traduction d’expressions de requête, les appels de méthode qui en résulte sont traités comme des appels de méthode normal et cela peut révéler à son tour les erreurs, par exemple si les méthodes n’existent pas, si les arguments ont des types incorrects, ou si les méthodes sont génériques et inférence de type échoue.</span><span class="sxs-lookup"><span data-stu-id="3d917-2501">Following translation of query expressions, the resulting method invocations are processed as regular method invocations, and this may in turn uncover errors, for example if the methods do not exist, if arguments have wrong types, or if the methods are generic and type inference fails.</span></span>

<span data-ttu-id="3d917-2502">Une expression de requête est traitée par l’application à plusieurs reprises les traductions suivantes jusqu'à ce qu’aucune réduction supplémentaire n’est possibles.</span><span class="sxs-lookup"><span data-stu-id="3d917-2502">A query expression is processed by repeatedly applying the following translations until no further reductions are possible.</span></span> <span data-ttu-id="3d917-2503">Les traductions sont répertoriées dans l’ordre d’application : chaque section part du principe que les traductions dans les sections précédentes ont été effectuées de manière exhaustive, et une fois atteint, une section ne sera pas plus tard être revisitée lors du traitement de la même expression de requête.</span><span class="sxs-lookup"><span data-stu-id="3d917-2503">The translations are listed in order of application: each section assumes that the translations in the preceding sections have been performed exhaustively, and once exhausted, a section will not later be revisited in the processing of the same query expression.</span></span>

<span data-ttu-id="3d917-2504">Affectation à des variables de plage n’est pas autorisée dans les expressions de requête.</span><span class="sxs-lookup"><span data-stu-id="3d917-2504">Assignment to range variables is not allowed in query expressions.</span></span> <span data-ttu-id="3d917-2505">Toutefois une implémentation c# est autorisée à n'appliquer pas toujours cette restriction, dans la mesure où cela est parfois pas possible avec le schéma de conversion syntaxique présenté ici.</span><span class="sxs-lookup"><span data-stu-id="3d917-2505">However a C# implementation is permitted to not always enforce this restriction, since this may sometimes not be possible with the syntactic translation scheme presented here.</span></span>

<span data-ttu-id="3d917-2506">Certaines traductions injectent des variables de portée avec des identificateurs transparents dénotés par `*`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2506">Certain translations inject range variables with transparent identifiers denoted by `*`.</span></span> <span data-ttu-id="3d917-2507">Les propriétés spéciales d’identificateurs transparents sont abordées plus en détail dans [identificateurs Transparent](expressions.md#transparent-identifiers).</span><span class="sxs-lookup"><span data-stu-id="3d917-2507">The special properties of transparent identifiers are discussed further in [Transparent identifiers](expressions.md#transparent-identifiers).</span></span>

#### <a name="select-and-groupby-clauses-with-continuations"></a><span data-ttu-id="3d917-2508">Clauses SELECT et groupby avec des continuations</span><span class="sxs-lookup"><span data-stu-id="3d917-2508">Select and groupby clauses with continuations</span></span>

<span data-ttu-id="3d917-2509">Une expression de requête avec une continuation</span><span class="sxs-lookup"><span data-stu-id="3d917-2509">A query expression with a continuation</span></span>
```csharp
from ... into x ...
```
<span data-ttu-id="3d917-2510">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2510">is translated into</span></span>
```csharp
from x in ( from ... ) ...
```

<span data-ttu-id="3d917-2511">Les traductions dans les sections suivantes supposent que les requêtes n’ont pas `into` continuations.</span><span class="sxs-lookup"><span data-stu-id="3d917-2511">The translations in the following sections assume that queries have no `into` continuations.</span></span>

<span data-ttu-id="3d917-2512">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2512">The example</span></span>
```csharp
from c in customers
group c by c.Country into g
select new { Country = g.Key, CustCount = g.Count() }
```
<span data-ttu-id="3d917-2513">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2513">is translated into</span></span>
```csharp
from g in
    from c in customers
    group c by c.Country
select new { Country = g.Key, CustCount = g.Count() }
```
<span data-ttu-id="3d917-2514">la traduction finale qui est</span><span class="sxs-lookup"><span data-stu-id="3d917-2514">the final translation of which is</span></span>
```csharp
customers.
GroupBy(c => c.Country).
Select(g => new { Country = g.Key, CustCount = g.Count() })
```

#### <a name="explicit-range-variable-types"></a><span data-ttu-id="3d917-2515">Types de variables de plage explicite</span><span class="sxs-lookup"><span data-stu-id="3d917-2515">Explicit range variable types</span></span>

<span data-ttu-id="3d917-2516">Un `from` clause qui spécifie explicitement un type de variable de plage</span><span class="sxs-lookup"><span data-stu-id="3d917-2516">A `from` clause that explicitly specifies a range variable type</span></span>
```csharp
from T x in e
```
<span data-ttu-id="3d917-2517">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2517">is translated into</span></span>
```csharp
from x in ( e ) . Cast < T > ( )
```

<span data-ttu-id="3d917-2518">Un `join` clause qui spécifie explicitement un type de variable de plage</span><span class="sxs-lookup"><span data-stu-id="3d917-2518">A `join` clause that explicitly specifies a range variable type</span></span>
```
join T x in e on k1 equals k2
```
<span data-ttu-id="3d917-2519">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2519">is translated into</span></span>
```
join x in ( e ) . Cast < T > ( ) on k1 equals k2
```

<span data-ttu-id="3d917-2520">Les traductions dans les sections suivantes supposent que les requêtes ne disposent aucun type de variable de portée explicite.</span><span class="sxs-lookup"><span data-stu-id="3d917-2520">The translations in the following sections assume that queries have no explicit range variable types.</span></span>

<span data-ttu-id="3d917-2521">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2521">The example</span></span>
```csharp
from Customer c in customers
where c.City == "London"
select c
```
<span data-ttu-id="3d917-2522">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2522">is translated into</span></span>
```csharp
from c in customers.Cast<Customer>()
where c.City == "London"
select c
```
<span data-ttu-id="3d917-2523">la traduction finale qui est</span><span class="sxs-lookup"><span data-stu-id="3d917-2523">the final translation of which is</span></span>
```csharp
customers.
Cast<Customer>().
Where(c => c.City == "London")
```

<span data-ttu-id="3d917-2524">Types de variables de plage explicites sont utiles pour l’interrogation des collections qui implémentent le non générique `IEnumerable` interface, mais pas le générique `IEnumerable<T>` interface.</span><span class="sxs-lookup"><span data-stu-id="3d917-2524">Explicit range variable types are useful for querying collections that implement the non-generic `IEnumerable` interface, but not the generic `IEnumerable<T>` interface.</span></span> <span data-ttu-id="3d917-2525">Dans l’exemple ci-dessus, ce serait le cas si `customers` étaient de type `ArrayList`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2525">In the example above, this would be the case if `customers` were of type `ArrayList`.</span></span>

#### <a name="degenerate-query-expressions"></a><span data-ttu-id="3d917-2526">Expressions de requête dégénérée</span><span class="sxs-lookup"><span data-stu-id="3d917-2526">Degenerate query expressions</span></span>

<span data-ttu-id="3d917-2527">Une expression de requête sous la forme</span><span class="sxs-lookup"><span data-stu-id="3d917-2527">A query expression of the form</span></span>
```csharp
from x in e select x
```
<span data-ttu-id="3d917-2528">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2528">is translated into</span></span>
```csharp
( e ) . Select ( x => x )
```

<span data-ttu-id="3d917-2529">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2529">The example</span></span>
```csharp
from c in customers
select c
```
<span data-ttu-id="3d917-2530">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2530">is translated into</span></span>
```csharp
customers.Select(c => c)
```

<span data-ttu-id="3d917-2531">Une expression de requête dégénérée est une façon triviale sélectionne les éléments de la source.</span><span class="sxs-lookup"><span data-stu-id="3d917-2531">A degenerate query expression is one that trivially selects the elements of the source.</span></span> <span data-ttu-id="3d917-2532">Une étape ultérieure de la traduction supprime les requêtes dégénérées introduites dans les autres étapes de traduction en les remplaçant par leur source.</span><span class="sxs-lookup"><span data-stu-id="3d917-2532">A later phase of the translation removes degenerate queries introduced by other translation steps by replacing them with their source.</span></span> <span data-ttu-id="3d917-2533">Il convient toutefois, pour vous assurer que le résultat d’une requête expression n’est jamais l’objet de source lui-même, qui serait révèlent le type et l’identité de la source au client de la requête.</span><span class="sxs-lookup"><span data-stu-id="3d917-2533">It is important however to ensure that the result of a query expression is never the source object itself, as that would reveal the type and identity of the source to the client of the query.</span></span> <span data-ttu-id="3d917-2534">Par conséquent, cette étape protège dégénérées requêtes écrites directement dans le code source en appelant explicitement `Select` sur la source.</span><span class="sxs-lookup"><span data-stu-id="3d917-2534">Therefore this step protects degenerate queries written directly in source code by explicitly calling `Select` on the source.</span></span> <span data-ttu-id="3d917-2535">Il revient ensuite les implémenteurs de `Select` et d’autres opérateurs de requête pour vous assurer que ces méthodes retournent jamais l’objet de source lui-même.</span><span class="sxs-lookup"><span data-stu-id="3d917-2535">It is then up to the implementers of `Select` and other query operators to ensure that these methods never return the source object itself.</span></span>

#### <a name="from-let-where-join-and-orderby-clauses"></a><span data-ttu-id="3d917-2536">À partir, where, join et orderby clauses let</span><span class="sxs-lookup"><span data-stu-id="3d917-2536">From, let, where, join and orderby clauses</span></span>

<span data-ttu-id="3d917-2537">Une expression de requête avec un second `from` clause suivie d’un `select` clause</span><span class="sxs-lookup"><span data-stu-id="3d917-2537">A query expression with a second `from` clause followed by a `select` clause</span></span>
```csharp
from x1 in e1
from x2 in e2
select v
```
<span data-ttu-id="3d917-2538">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2538">is translated into</span></span>
```csharp
( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => v )
```

<span data-ttu-id="3d917-2539">Une expression de requête avec un second `from` clause suivie d’un élément autre qu’un `select` clause :</span><span class="sxs-lookup"><span data-stu-id="3d917-2539">A query expression with a second `from` clause followed by something other than a `select` clause:</span></span>

```csharp
from x1 in e1
from x2 in e2
...
```
<span data-ttu-id="3d917-2540">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2540">is translated into</span></span>
```csharp
from * in ( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => new { x1 , x2 } )
...
```

<span data-ttu-id="3d917-2541">Une expression de requête avec un `let` clause</span><span class="sxs-lookup"><span data-stu-id="3d917-2541">A query expression with a `let` clause</span></span>
```csharp
from x in e
let y = f
...
```
<span data-ttu-id="3d917-2542">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2542">is translated into</span></span>
```csharp
from * in ( e ) . Select ( x => new { x , y = f } )
...
```

<span data-ttu-id="3d917-2543">Une expression de requête avec un `where` clause</span><span class="sxs-lookup"><span data-stu-id="3d917-2543">A query expression with a `where` clause</span></span>
```csharp
from x in e
where f
...
```
<span data-ttu-id="3d917-2544">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2544">is translated into</span></span>
```csharp
from x in ( e ) . Where ( x => f )
...
```

<span data-ttu-id="3d917-2545">Une expression de requête avec un `join` clause sans un `into` suivie d’un `select` clause</span><span class="sxs-lookup"><span data-stu-id="3d917-2545">A query expression with a `join` clause without an `into` followed by a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
select v
```
<span data-ttu-id="3d917-2546">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2546">is translated into</span></span>
```csharp
( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => v )
```

<span data-ttu-id="3d917-2547">Une expression de requête avec un `join` clause sans un `into` suivie d’un élément autre qu’un `select` clause</span><span class="sxs-lookup"><span data-stu-id="3d917-2547">A query expression with a `join` clause without an `into` followed by something other than a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
...
```
<span data-ttu-id="3d917-2548">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2548">is translated into</span></span>
```csharp
from * in ( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => new { x1 , x2 })
...
```

<span data-ttu-id="3d917-2549">Une expression de requête avec un `join` clause avec un `into` suivie d’un `select` clause</span><span class="sxs-lookup"><span data-stu-id="3d917-2549">A query expression with a `join` clause with an `into` followed by a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
select v
```
<span data-ttu-id="3d917-2550">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2550">is translated into</span></span>
```csharp
( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => v )
```

<span data-ttu-id="3d917-2551">Une expression de requête avec un `join` clause avec un `into` suivie d’un élément autre qu’un `select` clause</span><span class="sxs-lookup"><span data-stu-id="3d917-2551">A query expression with a `join` clause with an `into` followed by something other than a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
...
```
<span data-ttu-id="3d917-2552">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2552">is translated into</span></span>
```csharp
from * in ( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => new { x1 , g })
...
```

<span data-ttu-id="3d917-2553">Une expression de requête avec un `orderby` clause</span><span class="sxs-lookup"><span data-stu-id="3d917-2553">A query expression with an `orderby` clause</span></span>
```csharp
from x in e
orderby k1 , k2 , ..., kn
...
```
<span data-ttu-id="3d917-2554">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2554">is translated into</span></span>
```csharp
from x in ( e ) . 
OrderBy ( x => k1 ) . 
ThenBy ( x => k2 ) .
... .
ThenBy ( x => kn )
...
```

<span data-ttu-id="3d917-2555">Si un classement spécifie un `descending` indicateur de direction, un appel à `OrderByDescending` ou `ThenByDescending` est généré à la place.</span><span class="sxs-lookup"><span data-stu-id="3d917-2555">If an ordering clause specifies a `descending` direction indicator, an invocation of `OrderByDescending` or `ThenByDescending` is produced instead.</span></span>

<span data-ttu-id="3d917-2556">Les traductions suivantes supposent qu’il n’y aucun `let`, `where`, `join` ou `orderby` clauses et pas plus de celui qui initial `from` clause dans chaque expression de requête.</span><span class="sxs-lookup"><span data-stu-id="3d917-2556">The following translations assume that there are no `let`, `where`, `join` or `orderby` clauses, and no more than the one initial `from` clause in each query expression.</span></span>

<span data-ttu-id="3d917-2557">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2557">The example</span></span>
```csharp
from c in customers
from o in c.Orders
select new { c.Name, o.OrderID, o.Total }
```
<span data-ttu-id="3d917-2558">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2558">is translated into</span></span>
```csharp
customers.
SelectMany(c => c.Orders,
     (c,o) => new { c.Name, o.OrderID, o.Total }
)
```

<span data-ttu-id="3d917-2559">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2559">The example</span></span>
```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```
<span data-ttu-id="3d917-2560">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2560">is translated into</span></span>
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```
<span data-ttu-id="3d917-2561">la traduction finale qui est</span><span class="sxs-lookup"><span data-stu-id="3d917-2561">the final translation of which is</span></span>
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.OrderID, x.o.Total })
```
<span data-ttu-id="3d917-2562">où `x` est un identificateur généré par le compilateur qui est sinon invisible et inaccessible.</span><span class="sxs-lookup"><span data-stu-id="3d917-2562">where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="3d917-2563">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2563">The example</span></span>
```csharp
from o in orders
let t = o.Details.Sum(d => d.UnitPrice * d.Quantity)
where t >= 1000
select new { o.OrderID, Total = t }
```
<span data-ttu-id="3d917-2564">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2564">is translated into</span></span>
```csharp
from * in orders.
    Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) })
where t >= 1000 
select new { o.OrderID, Total = t }
```
<span data-ttu-id="3d917-2565">la traduction finale qui est</span><span class="sxs-lookup"><span data-stu-id="3d917-2565">the final translation of which is</span></span>
```csharp
orders.
Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) }).
Where(x => x.t >= 1000).
Select(x => new { x.o.OrderID, Total = x.t })
```
<span data-ttu-id="3d917-2566">où `x` est un identificateur généré par le compilateur qui est sinon invisible et inaccessible.</span><span class="sxs-lookup"><span data-stu-id="3d917-2566">where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="3d917-2567">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2567">The example</span></span>
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
select new { c.Name, o.OrderDate, o.Total }
```
<span data-ttu-id="3d917-2568">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2568">is translated into</span></span>
```csharp
customers.Join(orders, c => c.CustomerID, o => o.CustomerID,
    (c, o) => new { c.Name, o.OrderDate, o.Total })
```

<span data-ttu-id="3d917-2569">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2569">The example</span></span>
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID into co
let n = co.Count()
where n >= 10
select new { c.Name, OrderCount = n }
```
<span data-ttu-id="3d917-2570">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2570">is translated into</span></span>
```csharp
from * in customers.
    GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
        (c, co) => new { c, co })
let n = co.Count()
where n >= 10 
select new { c.Name, OrderCount = n }
```
<span data-ttu-id="3d917-2571">la traduction finale qui est</span><span class="sxs-lookup"><span data-stu-id="3d917-2571">the final translation of which is</span></span>
```csharp
customers.
GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
    (c, co) => new { c, co }).
Select(x => new { x, n = x.co.Count() }).
Where(y => y.n >= 10).
Select(y => new { y.x.c.Name, OrderCount = y.n)
```
<span data-ttu-id="3d917-2572">où `x` et `y` sont des identificateurs générés par le compilateur qui ne sont autrement invisibles et inaccessible.</span><span class="sxs-lookup"><span data-stu-id="3d917-2572">where `x` and `y` are compiler generated identifiers that are otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="3d917-2573">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2573">The example</span></span>
```csharp
from o in orders
orderby o.Customer.Name, o.Total descending
select o
```
<span data-ttu-id="3d917-2574">a la traduction finale</span><span class="sxs-lookup"><span data-stu-id="3d917-2574">has the final translation</span></span>
```csharp
orders.
OrderBy(o => o.Customer.Name).
ThenByDescending(o => o.Total)
```

#### <a name="select-clauses"></a><span data-ttu-id="3d917-2575">Clauses SELECT</span><span class="sxs-lookup"><span data-stu-id="3d917-2575">Select clauses</span></span>

<span data-ttu-id="3d917-2576">Une expression de requête sous la forme</span><span class="sxs-lookup"><span data-stu-id="3d917-2576">A query expression of the form</span></span>
```csharp
from x in e select v
```
<span data-ttu-id="3d917-2577">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2577">is translated into</span></span>
```csharp
( e ) . Select ( x => v )
```
<span data-ttu-id="3d917-2578">sauf lorsque v est l’identificateur x, la traduction est simplement</span><span class="sxs-lookup"><span data-stu-id="3d917-2578">except when v is the identifier x, the translation is simply</span></span>
```csharp
( e )
```

<span data-ttu-id="3d917-2579">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3d917-2579">For example</span></span>
```csharp
from c in customers.Where(c => c.City == "London")
select c
```
<span data-ttu-id="3d917-2580">est simplement traduit</span><span class="sxs-lookup"><span data-stu-id="3d917-2580">is simply translated into</span></span>
```csharp
customers.Where(c => c.City == "London")
```

#### <a name="groupby-clauses"></a><span data-ttu-id="3d917-2581">Clauses GroupBy</span><span class="sxs-lookup"><span data-stu-id="3d917-2581">Groupby clauses</span></span>

<span data-ttu-id="3d917-2582">Une expression de requête sous la forme</span><span class="sxs-lookup"><span data-stu-id="3d917-2582">A query expression of the form</span></span>
```csharp
from x in e group v by k
```
<span data-ttu-id="3d917-2583">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2583">is translated into</span></span>
```csharp
( e ) . GroupBy ( x => k , x => v )
```
<span data-ttu-id="3d917-2584">sauf lorsque v est l’identificateur x, la traduction est</span><span class="sxs-lookup"><span data-stu-id="3d917-2584">except when v is the identifier x, the translation is</span></span>
```csharp
( e ) . GroupBy ( x => k )
```

<span data-ttu-id="3d917-2585">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2585">The example</span></span>
```csharp
from c in customers
group c.Name by c.Country
```
<span data-ttu-id="3d917-2586">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2586">is translated into</span></span>
```csharp
customers.
GroupBy(c => c.Country, c => c.Name)
```

#### <a name="transparent-identifiers"></a><span data-ttu-id="3d917-2587">Identificateurs transparents</span><span class="sxs-lookup"><span data-stu-id="3d917-2587">Transparent identifiers</span></span>

<span data-ttu-id="3d917-2588">Certaines traductions injectent des variables de portée avec ***identificateurs transparents*** dénoté par `*`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2588">Certain translations inject range variables with ***transparent identifiers*** denoted by `*`.</span></span> <span data-ttu-id="3d917-2589">Identificateurs transparents ne sont pas une fonctionnalité de langage approprié ; ils existent uniquement comme une étape intermédiaire dans le processus de traduction d’expression de requête.</span><span class="sxs-lookup"><span data-stu-id="3d917-2589">Transparent identifiers are not a proper language feature; they exist only as an intermediate step in the query expression translation process.</span></span>

<span data-ttu-id="3d917-2590">Lorsqu’une traduction de requête injecte un identificateur transparent, étapes de traduction propagent davantage l’identificateur transparent dans les fonctions anonymes et les initialiseurs d’objet.</span><span class="sxs-lookup"><span data-stu-id="3d917-2590">When a query translation injects a transparent identifier, further translation steps propagate the transparent identifier into anonymous functions and anonymous object initializers.</span></span> <span data-ttu-id="3d917-2591">Dans ces contextes, identificateurs transparents ont le comportement suivant :</span><span class="sxs-lookup"><span data-stu-id="3d917-2591">In those contexts, transparent identifiers have the following behavior:</span></span>

*  <span data-ttu-id="3d917-2592">Lorsqu’un identificateur transparent se produit en tant que paramètre dans une fonction anonyme, les membres du type anonyme associé sont automatiquement dans la portée dans le corps de la fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="3d917-2592">When a transparent identifier occurs as a parameter in an anonymous function, the members of the associated anonymous type are automatically in scope in the body of the anonymous function.</span></span>
*  <span data-ttu-id="3d917-2593">Lorsqu’un membre avec un identificateur transparent est dans la portée, les membres de ce membre sont dans l’étendue ainsi.</span><span class="sxs-lookup"><span data-stu-id="3d917-2593">When a member with a transparent identifier is in scope, the members of that member are in scope as well.</span></span>
*  <span data-ttu-id="3d917-2594">En cas d’un identificateur transparent comme un déclarateur de membre dans un initialiseur d’objet anonyme, elle introduit un membre avec un identificateur transparent.</span><span class="sxs-lookup"><span data-stu-id="3d917-2594">When a transparent identifier occurs as a member declarator in an anonymous object initializer, it introduces a member with a transparent identifier.</span></span>
*  <span data-ttu-id="3d917-2595">Dans les étapes de traduction décrites ci-dessus, les identificateurs transparents sont toujours introduits avec des types anonymes, avec l’intention de capturer plusieurs variables de plage en tant que membres d’un objet unique.</span><span class="sxs-lookup"><span data-stu-id="3d917-2595">In the translation steps described above, transparent identifiers are always introduced together with anonymous types, with the intent of capturing multiple range variables as members of a single object.</span></span> <span data-ttu-id="3d917-2596">Une implémentation du langage c# est autorisée à utiliser un autre mécanisme que les types anonymes pour regrouper plusieurs variables de plage.</span><span class="sxs-lookup"><span data-stu-id="3d917-2596">An implementation of C# is permitted to use a different mechanism than anonymous types to group together multiple range variables.</span></span> <span data-ttu-id="3d917-2597">Les exemples de traduction suivants supposent que les types anonymes sont utilisés et affichent le degré de transparence identificateurs peuvent être traduites de suite.</span><span class="sxs-lookup"><span data-stu-id="3d917-2597">The following translation examples assume that anonymous types are used, and show how transparent identifiers can be translated away.</span></span>

<span data-ttu-id="3d917-2598">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2598">The example</span></span>
```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.Total }
```
<span data-ttu-id="3d917-2599">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2599">is translated into</span></span>
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.Total }
```

<span data-ttu-id="3d917-2600">qui est davantage de traduire</span><span class="sxs-lookup"><span data-stu-id="3d917-2600">which is further translated into</span></span>
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(* => o.Total).
Select(* => new { c.Name, o.Total })
```
<span data-ttu-id="3d917-2601">ce qui, lorsque les identificateurs transparents sont effacés, équivaut à</span><span class="sxs-lookup"><span data-stu-id="3d917-2601">which, when transparent identifiers are erased, is equivalent to</span></span>
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.Total })
```
<span data-ttu-id="3d917-2602">où `x` est un identificateur généré par le compilateur qui est sinon invisible et inaccessible.</span><span class="sxs-lookup"><span data-stu-id="3d917-2602">where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="3d917-2603">L’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2603">The example</span></span>
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
<span data-ttu-id="3d917-2604">est traduite en</span><span class="sxs-lookup"><span data-stu-id="3d917-2604">is translated into</span></span>
```csharp
from * in customers.
    Join(orders, c => c.CustomerID, o => o.CustomerID, 
        (c, o) => new { c, o })
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
<span data-ttu-id="3d917-2605">qui est davantage réduite à</span><span class="sxs-lookup"><span data-stu-id="3d917-2605">which is further reduced to</span></span>
```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID, (c, o) => new { c, o }).
Join(details, * => o.OrderID, d => d.OrderID, (*, d) => new { *, d }).
Join(products, * => d.ProductID, p => p.ProductID, (*, p) => new { *, p }).
Select(* => new { c.Name, o.OrderDate, p.ProductName })
```
<span data-ttu-id="3d917-2606">la traduction finale qui est</span><span class="sxs-lookup"><span data-stu-id="3d917-2606">the final translation of which is</span></span>
```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID,
    (c, o) => new { c, o }).
Join(details, x => x.o.OrderID, d => d.OrderID,
    (x, d) => new { x, d }).
Join(products, y => y.d.ProductID, p => p.ProductID,
    (y, p) => new { y, p }).
Select(z => new { z.y.x.c.Name, z.y.x.o.OrderDate, z.p.ProductName })
```
<span data-ttu-id="3d917-2607">où `x`, `y`, et `z` sont des identificateurs générés par le compilateur qui ne sont autrement invisibles et inaccessible.</span><span class="sxs-lookup"><span data-stu-id="3d917-2607">where `x`, `y`, and `z` are compiler generated identifiers that are otherwise invisible and inaccessible.</span></span>

### <a name="the-query-expression-pattern"></a><span data-ttu-id="3d917-2608">Le modèle d’expression de requête</span><span class="sxs-lookup"><span data-stu-id="3d917-2608">The query expression pattern</span></span>

<span data-ttu-id="3d917-2609">Le ***modèle d’expression de requête*** établit un modèle de méthodes types peuvent implémenter pour prendre en charge les expressions de requête.</span><span class="sxs-lookup"><span data-stu-id="3d917-2609">The ***Query expression pattern*** establishes a pattern of methods that types can implement to support query expressions.</span></span> <span data-ttu-id="3d917-2610">Étant donné que les expressions de requête sont converties en appels de méthode au moyen d’un mappage syntaxique, les types ont une flexibilité considérable dans la manière dont elles implémentent le modèle d’expression de requête.</span><span class="sxs-lookup"><span data-stu-id="3d917-2610">Because query expressions are translated to method invocations by means of a syntactic mapping, types have considerable flexibility in how they implement the query expression pattern.</span></span> <span data-ttu-id="3d917-2611">Par exemple, les méthodes du modèle peuvent être implémentés en tant que méthodes d’instance ou en tant que méthodes d’extension, car les deux ont la même syntaxe d’appel et les méthodes peuvent demander des délégués ou les arborescences d’expression, car les fonctions anonymes sont convertibles vers les deux.</span><span class="sxs-lookup"><span data-stu-id="3d917-2611">For example, the methods of the pattern can be implemented as instance methods or as extension methods because the two have the same invocation syntax, and the methods can request delegates or expression trees because anonymous functions are convertible to both.</span></span>

<span data-ttu-id="3d917-2612">La forme recommandée d’un type générique `C<T>` que prend en charge le modèle d’expression de requête est indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3d917-2612">The recommended shape of a generic type `C<T>` that supports the query expression pattern is shown below.</span></span> <span data-ttu-id="3d917-2613">Un type générique est utilisé afin d’illustrer les relations appropriées entre les types de paramètre et le résultat, mais il est possible d’implémenter le modèle pour également les types non génériques.</span><span class="sxs-lookup"><span data-stu-id="3d917-2613">A generic type is used in order to illustrate the proper relationships between parameter and result types, but it is possible to implement the pattern for non-generic types as well.</span></span>

```csharp
delegate R Func<T1,R>(T1 arg1);

delegate R Func<T1,T2,R>(T1 arg1, T2 arg2);

class C
{
    public C<T> Cast<T>();
}

class C<T> : C
{
    public C<T> Where(Func<T,bool> predicate);

    public C<U> Select<U>(Func<T,U> selector);

    public C<V> SelectMany<U,V>(Func<T,C<U>> selector,
        Func<T,U,V> resultSelector);

    public C<V> Join<U,K,V>(C<U> inner, Func<T,K> outerKeySelector,
        Func<U,K> innerKeySelector, Func<T,U,V> resultSelector);

    public C<V> GroupJoin<U,K,V>(C<U> inner, Func<T,K> outerKeySelector,
        Func<U,K> innerKeySelector, Func<T,C<U>,V> resultSelector);

    public O<T> OrderBy<K>(Func<T,K> keySelector);

    public O<T> OrderByDescending<K>(Func<T,K> keySelector);

    public C<G<K,T>> GroupBy<K>(Func<T,K> keySelector);

    public C<G<K,E>> GroupBy<K,E>(Func<T,K> keySelector,
        Func<T,E> elementSelector);
}

class O<T> : C<T>
{
    public O<T> ThenBy<K>(Func<T,K> keySelector);

    public O<T> ThenByDescending<K>(Func<T,K> keySelector);
}

class G<K,T> : C<T>
{
    public K Key { get; }
}
```

<span data-ttu-id="3d917-2614">Les méthodes ci-dessus utilisent les types délégués génériques `Func<T1,R>` et `Func<T1,T2,R>`, mais pourrait tout aussi bien avoir utilisées autres types d’arborescence délégué ou une expression avec les mêmes relations dans les types de paramètre et le résultat.</span><span class="sxs-lookup"><span data-stu-id="3d917-2614">The methods above use the generic delegate types `Func<T1,R>` and `Func<T1,T2,R>`, but they could equally well have used other delegate or expression tree types with the same relationships in parameter and result types.</span></span>

<span data-ttu-id="3d917-2615">Notez que la relation recommandée entre `C<T>` et `O<T>` qui garantit que le `ThenBy` et `ThenByDescending` méthodes sont disponibles uniquement sur le résultat d’une `OrderBy` ou `OrderByDescending`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2615">Notice the recommended relationship between `C<T>` and `O<T>` which ensures that the `ThenBy` and `ThenByDescending` methods are available only on the result of an `OrderBy` or `OrderByDescending`.</span></span> <span data-ttu-id="3d917-2616">Notez également la forme recommandée du résultat de `GroupBy` --une séquence de séquences, où chaque séquence interne a un autre `Key` propriété.</span><span class="sxs-lookup"><span data-stu-id="3d917-2616">Also notice the recommended shape of the result of `GroupBy` -- a sequence of sequences, where each inner sequence has an additional `Key` property.</span></span>

<span data-ttu-id="3d917-2617">Le `System.Linq` espace de noms fournit une implémentation du modèle d’opérateur de requête pour n’importe quel type qui implémente le `System.Collections.Generic.IEnumerable<T>` interface.</span><span class="sxs-lookup"><span data-stu-id="3d917-2617">The `System.Linq` namespace provides an implementation of the query operator pattern for any type that implements the `System.Collections.Generic.IEnumerable<T>` interface.</span></span>

## <a name="assignment-operators"></a><span data-ttu-id="3d917-2618">Opérateurs d'assignation</span><span class="sxs-lookup"><span data-stu-id="3d917-2618">Assignment operators</span></span>

<span data-ttu-id="3d917-2619">Les opérateurs d’assignation affecter une nouvelle valeur à une variable, une propriété, un événement ou un élément d’indexeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2619">The assignment operators assign a new value to a variable, a property, an event, or an indexer element.</span></span>

```antlr
assignment
    : unary_expression assignment_operator expression
    ;

assignment_operator
    : '='
    | '+='
    | '-='
    | '*='
    | '/='
    | '%='
    | '&='
    | '|='
    | '^='
    | '<<='
    | right_shift_assignment
    ;
```

<span data-ttu-id="3d917-2620">L’opérande gauche d’une assignation doit être une expression classifiée comme une variable, un accès à la propriété, accès à un indexeur ou un accès à l’événement.</span><span class="sxs-lookup"><span data-stu-id="3d917-2620">The left operand of an assignment must be an expression classified as a variable, a property access, an indexer access, or an event access.</span></span>

<span data-ttu-id="3d917-2621">Le `=` opérateur est appelé le ***opérateur d’assignation simple***.</span><span class="sxs-lookup"><span data-stu-id="3d917-2621">The `=` operator is called the ***simple assignment operator***.</span></span> <span data-ttu-id="3d917-2622">Il attribue la valeur de l’opérande droit à l’élément de variable, propriété ou indexeur indiqué par l’opérande de gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-2622">It assigns the value of the right operand to the variable, property, or indexer element given by the left operand.</span></span> <span data-ttu-id="3d917-2623">L’opérande gauche de l’opérateur d’assignation simple est peut-être pas accès à un événement (sauf indication contraire dans [les événements de type champ](classes.md#field-like-events)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2623">The left operand of the simple assignment operator may not be an event access (except as described in [Field-like events](classes.md#field-like-events)).</span></span> <span data-ttu-id="3d917-2624">L’opérateur d’assignation simple est décrit dans [assignation Simple](expressions.md#simple-assignment).</span><span class="sxs-lookup"><span data-stu-id="3d917-2624">The simple assignment operator is described in [Simple assignment](expressions.md#simple-assignment).</span></span>

<span data-ttu-id="3d917-2625">Les opérateurs d’assignation autres que le `=` opérateur sont appelés les ***opérateurs d’assignation composée***.</span><span class="sxs-lookup"><span data-stu-id="3d917-2625">The assignment operators other than the `=` operator are called the ***compound assignment operators***.</span></span> <span data-ttu-id="3d917-2626">Ces opérateurs effectuent l’opération indiquée sur les deux opérandes et puis affectez la valeur obtenue à l’élément de variable, propriété ou indexeur indiqué par l’opérande de gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-2626">These operators perform the indicated operation on the two operands, and then assign the resulting value to the variable, property, or indexer element given by the left operand.</span></span> <span data-ttu-id="3d917-2627">Les opérateurs d’assignation composée sont décrits dans [assignation composée](expressions.md#compound-assignment).</span><span class="sxs-lookup"><span data-stu-id="3d917-2627">The compound assignment operators are described in [Compound assignment](expressions.md#compound-assignment).</span></span>

<span data-ttu-id="3d917-2628">Le `+=` et `-=` avec une expression d’accès événement en tant que l’opérande de gauche sont appelés les *opérateurs d’assignation événement*.</span><span class="sxs-lookup"><span data-stu-id="3d917-2628">The `+=` and `-=` operators with an event access expression as the left operand are called the *event assignment operators*.</span></span> <span data-ttu-id="3d917-2629">Aucun autre opérateur d’assignation n’est valide avec un accès à l’événement en tant que l’opérande de gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-2629">No other assignment operator is valid with an event access as the left operand.</span></span> <span data-ttu-id="3d917-2630">Les opérateurs d’assignation événement sont décrites dans [assignation d’événement](expressions.md#event-assignment).</span><span class="sxs-lookup"><span data-stu-id="3d917-2630">The event assignment operators are described in [Event assignment](expressions.md#event-assignment).</span></span>

<span data-ttu-id="3d917-2631">Les opérateurs d’assignation sont associatifs sur leur droite, ce qui signifie que les opérations sont groupées de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-2631">The assignment operators are right-associative, meaning that operations are grouped from right to left.</span></span> <span data-ttu-id="3d917-2632">Par exemple, une expression sous la forme `a = b = c` est évalué comme `a = (b = c)`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2632">For example, an expression of the form `a = b = c` is evaluated as `a = (b = c)`.</span></span>

### <a name="simple-assignment"></a><span data-ttu-id="3d917-2633">Assignation simple</span><span class="sxs-lookup"><span data-stu-id="3d917-2633">Simple assignment</span></span>

<span data-ttu-id="3d917-2634">Le `=` opérateur est appelé l’opérateur d’assignation simple.</span><span class="sxs-lookup"><span data-stu-id="3d917-2634">The `=` operator is called the simple assignment operator.</span></span>

<span data-ttu-id="3d917-2635">Si l’opérande gauche d’une assignation simple est au format `E.P` ou `E[Ei]` où `E` a le type de compilation `dynamic`, l’attribution est dynamiquement lié ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2635">If the left operand of a simple assignment is of the form `E.P` or `E[Ei]` where `E` has the compile-time type `dynamic`, then the assignment is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="3d917-2636">Dans ce cas le type de compilation de l’expression d’assignation est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution en fonction du type d’exécution de `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2636">In this case the compile-time type of the assignment expression is `dynamic`, and the resolution described below will take place at run-time based on the run-time type of `E`.</span></span>

<span data-ttu-id="3d917-2637">Dans une assignation simple, l’opérande de droite doit être une expression qui est implicitement convertible dans le type de l’opérande gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-2637">In a simple assignment, the right operand must be an expression that is implicitly convertible to the type of the left operand.</span></span> <span data-ttu-id="3d917-2638">L’opération assigne la valeur de l’opérande droit à l’élément de variable, propriété ou indexeur indiqué par l’opérande de gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-2638">The operation assigns the value of the right operand to the variable, property, or indexer element given by the left operand.</span></span>

<span data-ttu-id="3d917-2639">Le résultat d’une expression d’assignation simple est la valeur assignée à l’opérande de gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-2639">The result of a simple assignment expression is the value assigned to the left operand.</span></span> <span data-ttu-id="3d917-2640">Le résultat a le même type que l’opérande de gauche et est toujours considéré comme une valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2640">The result has the same type as the left operand and is always classified as a value.</span></span>

<span data-ttu-id="3d917-2641">Si l’opérande gauche est un accès de propriété ou un indexeur, la propriété ou l’indexeur doit avoir un `set` accesseur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2641">If the left operand is a property or indexer access, the property or indexer must have a `set` accessor.</span></span> <span data-ttu-id="3d917-2642">Si ce n’est pas le cas, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-2642">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="3d917-2643">Le traitement de l’exécution d’une assignation simple sous la forme `x = y` se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-2643">The run-time processing of a simple assignment of the form `x = y` consists of the following steps:</span></span>

*  <span data-ttu-id="3d917-2644">Si `x` est classé en tant que variable :</span><span class="sxs-lookup"><span data-stu-id="3d917-2644">If `x` is classified as a variable:</span></span>
   * <span data-ttu-id="3d917-2645">`x` est évalué pour produire la variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-2645">`x` is evaluated to produce the variable.</span></span>
   * <span data-ttu-id="3d917-2646">`y` est évaluée et, si nécessaire, converti vers le type de `x` via une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2646">`y` is evaluated and, if required, converted to the type of `x` through an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>
   * <span data-ttu-id="3d917-2647">Si la variable donnée par `x` est un élément d’un *reference_type*, une vérification de l’exécution est effectuée pour vous assurer que la valeur calculée pour `y` est compatible avec l’instance de tableau dont `x` est un élément.</span><span class="sxs-lookup"><span data-stu-id="3d917-2647">If the variable given by `x` is an array element of a *reference_type*, a run-time check is performed to ensure that the value computed for `y` is compatible with the array instance of which `x` is an element.</span></span> <span data-ttu-id="3d917-2648">La vérification réussit si `y` est `null`, ou si une conversion de référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions)) existe dans le type réel de l’instance référencée par `y` au type d’élément réel de l’instance de tableau contenant `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2648">The check succeeds if `y` is `null`, or if an implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) exists from the actual type of the instance referenced by `y` to the actual element type of the array instance containing `x`.</span></span> <span data-ttu-id="3d917-2649">Sinon, une exception `System.ArrayTypeMismatchException` est levée.</span><span class="sxs-lookup"><span data-stu-id="3d917-2649">Otherwise, a `System.ArrayTypeMismatchException` is thrown.</span></span>
   * <span data-ttu-id="3d917-2650">La valeur résultant de l’évaluation et la conversion de `y` est stocké dans l’emplacement indiqué par l’évaluation de `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2650">The value resulting from the evaluation and conversion of `y` is stored into the location given by the evaluation of `x`.</span></span>
*  <span data-ttu-id="3d917-2651">Si `x` est classé comme un accès de propriété ou l’indexeur :</span><span class="sxs-lookup"><span data-stu-id="3d917-2651">If `x` is classified as a property or indexer access:</span></span>
   * <span data-ttu-id="3d917-2652">L’expression d’instance (si `x` n’est pas `static`) et la liste d’arguments (si `x` est l’accès à un indexeur) associé à `x` sont évaluées, et les résultats sont utilisés dans l’ultérieure `set` invocation de l’accesseur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2652">The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `set` accessor invocation.</span></span>
   * <span data-ttu-id="3d917-2653">`y` est évaluée et, si nécessaire, converti vers le type de `x` via une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2653">`y` is evaluated and, if required, converted to the type of `x` through an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>
   * <span data-ttu-id="3d917-2654">Le `set` l’accesseur de `x` est appelé avec la valeur calculée de `y` en tant que son `value` argument.</span><span class="sxs-lookup"><span data-stu-id="3d917-2654">The `set` accessor of `x` is invoked with the value computed for `y` as its `value` argument.</span></span>

<span data-ttu-id="3d917-2655">Les règles de variance co tableau ([covariance de tableau](arrays.md#array-covariance)) permettent à une valeur d’un type tableau `A[]` soit une référence à une instance d’un type tableau `B[]`, à condition qu’existe une conversion de référence implicite de `B` à `A`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2655">The array co-variance rules ([Array covariance](arrays.md#array-covariance)) permit a value of an array type `A[]` to be a reference to an instance of an array type `B[]`, provided an implicit reference conversion exists from `B` to `A`.</span></span> <span data-ttu-id="3d917-2656">En raison de ces règles, l’assignation à un élément d’un *reference_type* requiert une vérification de l’exécution pour vous assurer que la valeur assignée est compatible avec l’instance de tableau.</span><span class="sxs-lookup"><span data-stu-id="3d917-2656">Because of these rules, assignment to an array element of a *reference_type* requires a run-time check to ensure that the value being assigned is compatible with the array instance.</span></span> <span data-ttu-id="3d917-2657">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2657">In the example</span></span>
```csharp
string[] sa = new string[10];
object[] oa = sa;

oa[0] = null;               // Ok
oa[1] = "Hello";            // Ok
oa[2] = new ArrayList();    // ArrayTypeMismatchException
```
<span data-ttu-id="3d917-2658">la dernière assignation entraîne un `System.ArrayTypeMismatchException` levée, car une instance de `ArrayList` ne peut pas être stocké dans un élément d’un `string[]`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2658">the last assignment causes a `System.ArrayTypeMismatchException` to be thrown because an instance of `ArrayList` cannot be stored in an element of a `string[]`.</span></span>

<span data-ttu-id="3d917-2659">Lorsqu’une propriété ou un indexeur déclaré dans un *struct_type* est la cible d’une assignation, l’expression d’instance associée de la propriété ou l’accès à l’indexeur doit être classée en tant que variable.</span><span class="sxs-lookup"><span data-stu-id="3d917-2659">When a property or indexer declared in a *struct_type* is the target of an assignment, the instance expression associated with the property or indexer access must be classified as a variable.</span></span> <span data-ttu-id="3d917-2660">Si l’expression d’instance est classifiée en tant que valeur, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-2660">If the instance expression is classified as a value, a binding-time error occurs.</span></span> <span data-ttu-id="3d917-2661">Raison de [l’accès au membre](expressions.md#member-access), la même règle s’applique également aux champs.</span><span class="sxs-lookup"><span data-stu-id="3d917-2661">Because of [Member access](expressions.md#member-access), the same rule also applies to fields.</span></span>

<span data-ttu-id="3d917-2662">Compte tenu des déclarations :</span><span class="sxs-lookup"><span data-stu-id="3d917-2662">Given the declarations:</span></span>
```csharp
struct Point
{
    int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int X {
        get { return x; }
        set { x = value; }
    }

    public int Y {
        get { return y; }
        set { y = value; }
    }
}

struct Rectangle
{
    Point a, b;

    public Rectangle(Point a, Point b) {
        this.a = a;
        this.b = b;
    }

    public Point A {
        get { return a; }
        set { a = value; }
    }

    public Point B {
        get { return b; }
        set { b = value; }
    }
}
```
<span data-ttu-id="3d917-2663">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2663">in the example</span></span>
```csharp
Point p = new Point();
p.X = 100;
p.Y = 100;
Rectangle r = new Rectangle();
r.A = new Point(10, 10);
r.B = p;
```
<span data-ttu-id="3d917-2664">les affectations à `p.X`, `p.Y`, `r.A`, et `r.B` sont autorisées car `p` et `r` sont des variables.</span><span class="sxs-lookup"><span data-stu-id="3d917-2664">the assignments to `p.X`, `p.Y`, `r.A`, and `r.B` are permitted because `p` and `r` are variables.</span></span> <span data-ttu-id="3d917-2665">Toutefois, dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2665">However, in the example</span></span>
```csharp
Rectangle r = new Rectangle();
r.A.X = 10;
r.A.Y = 10;
r.B.X = 100;
r.B.Y = 100;
```
<span data-ttu-id="3d917-2666">les affectations sont non valides depuis `r.A` et `r.B` ne sont pas des variables.</span><span class="sxs-lookup"><span data-stu-id="3d917-2666">the assignments are all invalid, since `r.A` and `r.B` are not variables.</span></span>

### <a name="compound-assignment"></a><span data-ttu-id="3d917-2667">Assignation composée</span><span class="sxs-lookup"><span data-stu-id="3d917-2667">Compound assignment</span></span>

<span data-ttu-id="3d917-2668">Si l’opérande gauche d’une assignation composée est au format `E.P` ou `E[Ei]` où `E` a le type de compilation `dynamic`, l’attribution est dynamiquement lié ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2668">If the left operand of a compound assignment is of the form `E.P` or `E[Ei]` where `E` has the compile-time type `dynamic`, then the assignment is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="3d917-2669">Dans ce cas le type de compilation de l’expression d’assignation est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution en fonction du type d’exécution de `E`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2669">In this case the compile-time type of the assignment expression is `dynamic`, and the resolution described below will take place at run-time based on the run-time type of `E`.</span></span>

<span data-ttu-id="3d917-2670">Une opération de la forme `x op= y` est traitée en appliquant la résolution de surcharge d’opérateur binaire ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) comme si l’opération a été écrit `x op y`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2670">An operation of the form `x op= y` is processed by applying binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) as if the operation was written `x op y`.</span></span> <span data-ttu-id="3d917-2671">Ensuite,</span><span class="sxs-lookup"><span data-stu-id="3d917-2671">Then,</span></span>

*  <span data-ttu-id="3d917-2672">Si le type de retour de l’opérateur sélectionné est implicitement convertible au type de `x`, l’opération est évaluée en tant que `x = x op y`, sauf que `x` est évaluée une seule fois.</span><span class="sxs-lookup"><span data-stu-id="3d917-2672">If the return type of the selected operator is implicitly convertible to the type of `x`, the operation is evaluated as `x = x op y`, except that `x` is evaluated only once.</span></span>
*  <span data-ttu-id="3d917-2673">Sinon, si l’opérateur sélectionné est un opérateur prédéfini, si le type de retour de l’opérateur sélectionné est explicitement convertible au type de `x`et si `y` est implicitement convertible au type de `x` ou l’opérateur est un Shift, opérateur, puis l’opération est évaluée en tant que `x = (T)(x op y)`, où `T` est le type de `x`, sauf que `x` est évaluée une seule fois.</span><span class="sxs-lookup"><span data-stu-id="3d917-2673">Otherwise, if the selected operator is a predefined operator, if the return type of the selected operator is explicitly convertible to the type of `x`, and if `y` is implicitly convertible to the type of `x` or the operator is a shift operator, then the operation is evaluated as `x = (T)(x op y)`, where `T` is the type of `x`, except that `x` is evaluated only once.</span></span>
*  <span data-ttu-id="3d917-2674">Sinon, l’assignation composée n’est pas valide, et se produit une erreur au moment de la liaison.</span><span class="sxs-lookup"><span data-stu-id="3d917-2674">Otherwise, the compound assignment is invalid, and a binding-time error occurs.</span></span>

<span data-ttu-id="3d917-2675">Le terme « évalué une seule fois » signifie que dans la version d’évaluation de `x op y`, les résultats de toutes les expressions constitutifs de `x` sont temporairement enregistrés et puis réutilisées lors de l’exécution de l’assignation à `x`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2675">The term "evaluated only once" means that in the evaluation of `x op y`, the results of any constituent expressions of `x` are temporarily saved and then reused when performing the assignment to `x`.</span></span> <span data-ttu-id="3d917-2676">Par exemple, dans l’assignation `A()[B()] += C()`, où `A` est une méthode retournant `int[]`, et `B` et `C` sont des méthodes qui retournent `int`, les méthodes sont appelées une seule fois, dans l’ordre `A`, `B`, `C`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2676">For example, in the assignment `A()[B()] += C()`, where `A` is a method returning `int[]`, and `B` and `C` are methods returning `int`, the methods are invoked only once, in the order `A`, `B`, `C`.</span></span>

<span data-ttu-id="3d917-2677">Lorsque l’opérande gauche d’une assignation composée est un accès à la propriété ou indexeur, la propriété ou l’indexeur doit avoir un à la fois un `get` accesseur et un `set` accesseur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2677">When the left operand of a compound assignment is a property access or indexer access, the property or indexer must have both a `get` accessor and a `set` accessor.</span></span> <span data-ttu-id="3d917-2678">Si ce n’est pas le cas, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-2678">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="3d917-2679">La deuxième règle ci-dessus permet `x op= y` soit évaluée comme `x = (T)(x op y)` dans certains contextes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2679">The second rule above permits `x op= y` to be evaluated as `x = (T)(x op y)` in certain contexts.</span></span> <span data-ttu-id="3d917-2680">La règle existe telles que les opérateurs prédéfinis peuvent être utilisés comme opérateurs composés lorsque l’opérande gauche est de type `sbyte`, `byte`, `short`, `ushort`, ou `char`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2680">The rule exists such that the predefined operators can be used as compound operators when the left operand is of type `sbyte`, `byte`, `short`, `ushort`, or `char`.</span></span> <span data-ttu-id="3d917-2681">Même lorsque les deux arguments sont d’un de ces types, les opérateurs prédéfinis produisent un résultat de type `int`, comme décrit dans [promotions numériques binaires](expressions.md#binary-numeric-promotions).</span><span class="sxs-lookup"><span data-stu-id="3d917-2681">Even when both arguments are of one of those types, the predefined operators produce a result of type `int`, as described in [Binary numeric promotions](expressions.md#binary-numeric-promotions).</span></span> <span data-ttu-id="3d917-2682">Par conséquent, sans un cast il pas serait possible d’affecter le résultat à l’opérande de gauche.</span><span class="sxs-lookup"><span data-stu-id="3d917-2682">Thus, without a cast it would not be possible to assign the result to the left operand.</span></span>

<span data-ttu-id="3d917-2683">L’effet intuitif de la règle pour les opérateurs prédéfinis est simplement que `x op= y` est autorisé si les deux de `x op y` et `x = y` sont autorisées.</span><span class="sxs-lookup"><span data-stu-id="3d917-2683">The intuitive effect of the rule for predefined operators is simply that `x op= y` is permitted if both of `x op y` and `x = y` are permitted.</span></span> <span data-ttu-id="3d917-2684">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2684">In the example</span></span>
```csharp
byte b = 0;
char ch = '\0';
int i = 0;

b += 1;             // Ok
b += 1000;          // Error, b = 1000 not permitted
b += i;             // Error, b = i not permitted
b += (byte)i;       // Ok

ch += 1;            // Error, ch = 1 not permitted
ch += (char)1;      // Ok
```
<span data-ttu-id="3d917-2685">la raison intuitive pour chaque erreur est qu’une assignation simple correspondante aurait également été une erreur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2685">the intuitive reason for each error is that a corresponding simple assignment would also have been an error.</span></span>

<span data-ttu-id="3d917-2686">Cela signifie également que les opérations prennent en charge l’assignation composée levé operations.</span><span class="sxs-lookup"><span data-stu-id="3d917-2686">This also means that compound assignment operations support lifted operations.</span></span> <span data-ttu-id="3d917-2687">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="3d917-2687">In the example</span></span>
```csharp
int? i = 0;
i += 1;             // Ok
```
<span data-ttu-id="3d917-2688">l’opérateur levé `+(int?,int?)` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="3d917-2688">the lifted operator `+(int?,int?)` is used.</span></span>

### <a name="event-assignment"></a><span data-ttu-id="3d917-2689">Assignation d’événement</span><span class="sxs-lookup"><span data-stu-id="3d917-2689">Event assignment</span></span>

<span data-ttu-id="3d917-2690">Si l’opérande de gauche d’un `+=` ou `-=` opérateur est classé comme un accès à l’événement, puis l’expression est évaluée comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-2690">If the left operand of a `+=` or `-=` operator is classified as an event access, then the expression is evaluated as follows:</span></span>

*  <span data-ttu-id="3d917-2691">L’expression d’instance, le cas échéant, de l’accès à l’événement est évaluée.</span><span class="sxs-lookup"><span data-stu-id="3d917-2691">The instance expression, if any, of the event access is evaluated.</span></span>
*  <span data-ttu-id="3d917-2692">L’opérande de droite de la `+=` ou `-=` opérateur est évalué et, si nécessaire, converti vers le type de l’opérande gauche via une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2692">The right operand of the `+=` or `-=` operator is evaluated, and, if required, converted to the type of the left operand through an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>
*  <span data-ttu-id="3d917-2693">Un accesseur d’événement de l’événement est appelé, avec la liste d’arguments composée de l’opérande de droite, après évaluation et, si nécessaire, conversion.</span><span class="sxs-lookup"><span data-stu-id="3d917-2693">An event accessor of the event is invoked, with argument list consisting of the right operand, after evaluation and, if necessary, conversion.</span></span> <span data-ttu-id="3d917-2694">Si l’opérateur a été `+=`, le `add` accesseur est appelé ; si l’opérateur a été `-=`, le `remove` accesseur est appelé.</span><span class="sxs-lookup"><span data-stu-id="3d917-2694">If the operator was `+=`, the `add` accessor is invoked; if the operator was `-=`, the `remove` accessor is invoked.</span></span>

<span data-ttu-id="3d917-2695">Une expression d’assignation événement ne produit pas de valeur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2695">An event assignment expression does not yield a value.</span></span> <span data-ttu-id="3d917-2696">Par conséquent, une expression d’assignation événement est valide uniquement dans le contexte d’un *statement_expression* ([instructions d’Expression](statements.md#expression-statements)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2696">Thus, an event assignment expression is valid only in the context of a *statement_expression* ([Expression statements](statements.md#expression-statements)).</span></span>

## <a name="expression"></a><span data-ttu-id="3d917-2697">Expression</span><span class="sxs-lookup"><span data-stu-id="3d917-2697">Expression</span></span>

<span data-ttu-id="3d917-2698">Un *expression* est soit un *non_assignment_expression* ou un *attribution*.</span><span class="sxs-lookup"><span data-stu-id="3d917-2698">An *expression* is either a *non_assignment_expression* or an *assignment*.</span></span>

```antlr
expression
    : non_assignment_expression
    | assignment
    ;

non_assignment_expression
    : conditional_expression
    | lambda_expression
    | query_expression
    ;
```

## <a name="constant-expressions"></a><span data-ttu-id="3d917-2699">Expressions constantes</span><span class="sxs-lookup"><span data-stu-id="3d917-2699">Constant expressions</span></span>

<span data-ttu-id="3d917-2700">Un *constant_expression* est une expression qui peut être complètement évaluée au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-2700">A *constant_expression* is an expression that can be fully evaluated at compile-time.</span></span>

```antlr
constant_expression
    : expression
    ;
```

<span data-ttu-id="3d917-2701">Une expression constante doit être le `null` littéral ou une valeur avec l’un des types suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char` , `float`, `double`, `decimal`, `bool`, `object`, `string`, ou tout type d’énumération.</span><span class="sxs-lookup"><span data-stu-id="3d917-2701">A constant expression must be the `null` literal or a value with one of  the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `object`, `string`, or any enumeration type.</span></span> <span data-ttu-id="3d917-2702">Uniquement les constructions suivantes sont autorisées dans les expressions constantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-2702">Only the following constructs are permitted in constant expressions:</span></span>

*  <span data-ttu-id="3d917-2703">Littéraux (y compris le `null` littérale).</span><span class="sxs-lookup"><span data-stu-id="3d917-2703">Literals (including the `null` literal).</span></span>
*  <span data-ttu-id="3d917-2704">Références à `const` membres de classe et les types struct.</span><span class="sxs-lookup"><span data-stu-id="3d917-2704">References to `const` members of class and struct types.</span></span>
*  <span data-ttu-id="3d917-2705">Références aux membres des types énumération.</span><span class="sxs-lookup"><span data-stu-id="3d917-2705">References to members of enumeration types.</span></span>
*  <span data-ttu-id="3d917-2706">Références à `const` paramètres ou variables locales</span><span class="sxs-lookup"><span data-stu-id="3d917-2706">References to `const` parameters or local variables</span></span>
*  <span data-ttu-id="3d917-2707">Entre parenthèses sous-expressions, qui sont eux-mêmes des expressions constantes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2707">Parenthesized sub-expressions, which are themselves constant expressions.</span></span>
*  <span data-ttu-id="3d917-2708">Expressions de cast, à condition que le type cible est un des types mentionnés ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="3d917-2708">Cast expressions, provided the target type is one of the types listed above.</span></span>
*  <span data-ttu-id="3d917-2709">`checked` et `unchecked` expressions</span><span class="sxs-lookup"><span data-stu-id="3d917-2709">`checked` and `unchecked` expressions</span></span>
*  <span data-ttu-id="3d917-2710">Expressions de valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="3d917-2710">Default value expressions</span></span>
*  <span data-ttu-id="3d917-2711">Expressions Nameof</span><span class="sxs-lookup"><span data-stu-id="3d917-2711">Nameof expressions</span></span>
*  <span data-ttu-id="3d917-2712">Prédéfinis `+`, `-`, `!`, et `~` opérateurs unaires.</span><span class="sxs-lookup"><span data-stu-id="3d917-2712">The predefined `+`, `-`, `!`, and `~` unary operators.</span></span>
*  <span data-ttu-id="3d917-2713">Prédéfinis `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, `&&`, `||`, `==`, `!=`, `<`, `>`, `<=`, et `>=` fourni d’opérateurs binaires, chaque opérande est d’un type répertorié ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="3d917-2713">The predefined `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, `&&`, `||`, `==`, `!=`, `<`, `>`, `<=`, and `>=` binary operators, provided each operand is of a type listed above.</span></span>
*  <span data-ttu-id="3d917-2714">Le `?:` opérateur conditionnel.</span><span class="sxs-lookup"><span data-stu-id="3d917-2714">The `?:` conditional operator.</span></span>

<span data-ttu-id="3d917-2715">Les conversions suivantes sont autorisées dans les expressions constantes :</span><span class="sxs-lookup"><span data-stu-id="3d917-2715">The following conversions are permitted in constant expressions:</span></span>

*  <span data-ttu-id="3d917-2716">Conversions d’identité</span><span class="sxs-lookup"><span data-stu-id="3d917-2716">Identity conversions</span></span>
*  <span data-ttu-id="3d917-2717">Conversions numériques</span><span class="sxs-lookup"><span data-stu-id="3d917-2717">Numeric conversions</span></span>
*  <span data-ttu-id="3d917-2718">Conversions d’énumération</span><span class="sxs-lookup"><span data-stu-id="3d917-2718">Enumeration conversions</span></span>
*  <span data-ttu-id="3d917-2719">Conversions d’expression constante</span><span class="sxs-lookup"><span data-stu-id="3d917-2719">Constant expression conversions</span></span>
*  <span data-ttu-id="3d917-2720">Conversions de référence implicites et explicites, fournies que la source des conversions est une expression constante qui correspond à la valeur null.</span><span class="sxs-lookup"><span data-stu-id="3d917-2720">Implicit and explicit reference conversions, provided that the source of the conversions is a constant expression that evaluates to the null value.</span></span>

<span data-ttu-id="3d917-2721">D’autres conversions, y compris le boxing, les conversions de référence unboxing et implicites des valeurs non null ne sont pas autorisées dans les expressions constantes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2721">Other conversions including boxing, unboxing and implicit reference conversions of non-null values are not permitted in constant expressions.</span></span> <span data-ttu-id="3d917-2722">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3d917-2722">For example:</span></span>
```csharp
class C {
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
<span data-ttu-id="3d917-2723">l’initialisation de i est une erreur car une conversion boxing est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="3d917-2723">the initialization of i is an error because a boxing conversion is required.</span></span> <span data-ttu-id="3d917-2724">L’initialisation de str est une erreur car une conversion de référence implicite à partir d’une valeur non null est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="3d917-2724">The initialization of str is an error because an implicit reference conversion from a non-null value is required.</span></span>

<span data-ttu-id="3d917-2725">Chaque fois qu’une expression satisfait les spécifications répertoriées ci-dessus, l’expression est évaluée au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-2725">Whenever an expression fulfills the requirements listed above, the expression is evaluated at compile-time.</span></span> <span data-ttu-id="3d917-2726">Cela est vrai même si l’expression est une sous-expression d’une expression plus longue qui contient des constructions non constantes.</span><span class="sxs-lookup"><span data-stu-id="3d917-2726">This is true even if the expression is a sub-expression of a larger expression that contains non-constant constructs.</span></span>

<span data-ttu-id="3d917-2727">L’évaluation de la compilation d’expressions constantes utilise les mêmes règles que l’évaluation de l’exécution des expressions non constantes, sauf que là où l’évaluation au moment de l’exécution aurait levé une exception, l’évaluation de la compilation provoque une erreur de compilation se produisent.</span><span class="sxs-lookup"><span data-stu-id="3d917-2727">The compile-time evaluation of constant expressions uses the same rules as run-time evaluation of non-constant expressions, except that where run-time evaluation would have thrown an exception, compile-time evaluation causes a compile-time error to occur.</span></span>

<span data-ttu-id="3d917-2728">Sauf si une expression constante est explicitement placée dans un `unchecked` contexte, les dépassements de capacité qui se produisent dans les opérations arithmétiques de type intégral et les conversions lors de l’évaluation de la compilation de l’expression toujours provoquer des erreurs de compilation ([Expressions constantes](expressions.md#constant-expressions)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2728">Unless a constant expression is explicitly placed in an `unchecked` context, overflows that occur in integral-type arithmetic operations and conversions during the compile-time evaluation of the expression always cause compile-time errors ([Constant expressions](expressions.md#constant-expressions)).</span></span>

<span data-ttu-id="3d917-2729">Expressions constantes se produisent dans les contextes répertoriés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3d917-2729">Constant expressions occur in the contexts listed below.</span></span> <span data-ttu-id="3d917-2730">Dans ces contextes, une erreur de compilation se produit si une expression ne peut pas être complètement évaluée au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="3d917-2730">In these contexts, a compile-time error occurs if an expression cannot be fully evaluated at compile-time.</span></span>

*  <span data-ttu-id="3d917-2731">Déclarations de constante ([constantes](classes.md#constants)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2731">Constant declarations ([Constants](classes.md#constants)).</span></span>
*  <span data-ttu-id="3d917-2732">Déclarations de membre d’énumération ([membres Enum](enums.md#enum-members)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2732">Enumeration member declarations ([Enum members](enums.md#enum-members)).</span></span>
*  <span data-ttu-id="3d917-2733">Arguments de listes de paramètres formels par défaut ([paramètres de méthode](classes.md#method-parameters))</span><span class="sxs-lookup"><span data-stu-id="3d917-2733">Default arguments of formal parameter lists ([Method parameters](classes.md#method-parameters))</span></span>
*  <span data-ttu-id="3d917-2734">`case` étiquettes d’un `switch` instruction ([l’instruction switch](statements.md#the-switch-statement)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2734">`case` labels of a `switch` statement ([The switch statement](statements.md#the-switch-statement)).</span></span>
*  <span data-ttu-id="3d917-2735">`goto case` instructions ([l’instruction goto](statements.md#the-goto-statement)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2735">`goto case` statements ([The goto statement](statements.md#the-goto-statement)).</span></span>
*  <span data-ttu-id="3d917-2736">Longueurs des dimensions dans une expression de création de tableau ([expressions de création de tableau](expressions.md#array-creation-expressions)) qui comprend un initialiseur.</span><span class="sxs-lookup"><span data-stu-id="3d917-2736">Dimension lengths in an array creation expression ([Array creation expressions](expressions.md#array-creation-expressions)) that includes an initializer.</span></span>
*  <span data-ttu-id="3d917-2737">Attributs ([attributs](attributes.md)).</span><span class="sxs-lookup"><span data-stu-id="3d917-2737">Attributes ([Attributes](attributes.md)).</span></span>

<span data-ttu-id="3d917-2738">Une conversion implicite expression constante ([conversions d’expression constante implicites](conversions.md#implicit-constant-expression-conversions)) autorise une expression constante de type `int` à convertir en `sbyte`, `byte`, `short`, `ushort`, `uint`, ou `ulong`, à condition que la valeur de l’expression constante se trouve dans la plage du type de destination.</span><span class="sxs-lookup"><span data-stu-id="3d917-2738">An implicit constant expression conversion ([Implicit constant expression conversions](conversions.md#implicit-constant-expression-conversions)) permits a constant expression of type `int` to be converted to `sbyte`, `byte`, `short`, `ushort`, `uint`, or `ulong`, provided the value of the constant expression is within the range of the destination type.</span></span>

## <a name="boolean-expressions"></a><span data-ttu-id="3d917-2739">expressions booléennes</span><span class="sxs-lookup"><span data-stu-id="3d917-2739">Boolean expressions</span></span>

<span data-ttu-id="3d917-2740">Un *boolean_expression* est une expression qui produit un résultat de type `bool`; soit directement ou via l’application de `operator true` dans certains contextes, comme indiqué dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="3d917-2740">A *boolean_expression* is an expression that yields a result of type `bool`; either directly or through application of `operator true` in certain contexts as specified in the following.</span></span>

```antlr
boolean_expression
    : expression
    ;
```

<span data-ttu-id="3d917-2741">Expression conditionnelle de contrôle d’un *if_statement* ([if instruction](statements.md#the-if-statement)), *while_statement* ([l’instruction while](statements.md#the-while-statement)), *do_statement* ([l’instruction do](statements.md#the-do-statement)), ou *for_statement* ([l’instruction for](statements.md#the-for-statement)) est un *boolean_ expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-2741">The controlling conditional expression of an *if_statement* ([The if statement](statements.md#the-if-statement)), *while_statement* ([The while statement](statements.md#the-while-statement)), *do_statement* ([The do statement](statements.md#the-do-statement)), or *for_statement* ([The for statement](statements.md#the-for-statement)) is a *boolean_expression*.</span></span> <span data-ttu-id="3d917-2742">L’expression conditionnelle de contrôle de la `?:` opérateur ([opérateur conditionnel](expressions.md#conditional-operator)) suit les mêmes règles qu’un *boolean_expression*, mais pour des raisons de l’opérateur de priorité est classifiée comme un *conditional_or_expression*.</span><span class="sxs-lookup"><span data-stu-id="3d917-2742">The controlling conditional expression of the `?:` operator ([Conditional operator](expressions.md#conditional-operator)) follows the same rules as a *boolean_expression*, but for reasons of operator precedence is classified as a *conditional_or_expression*.</span></span>

<span data-ttu-id="3d917-2743">Un *boolean_expression* `E` est nécessaire pour pouvoir produire une valeur de type `bool`, comme suit :</span><span class="sxs-lookup"><span data-stu-id="3d917-2743">A *boolean_expression* `E` is required to be able to produce a value of type `bool`, as follows:</span></span>

*  <span data-ttu-id="3d917-2744">Si `E` est implicitement convertible en `bool` lors de l’exécution par la conversion implicite est appliquée.</span><span class="sxs-lookup"><span data-stu-id="3d917-2744">If `E` is implicitly convertible to `bool` then at runtime that implicit conversion is applied.</span></span>
*  <span data-ttu-id="3d917-2745">Sinon, la résolution de surcharge opérateur unaire ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est utilisé pour trouver une meilleure implémentation unique de l’opérateur `true` sur `E`, et que l’implémentation est appliquée lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="3d917-2745">Otherwise, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is used to find a unique best implementation of operator `true` on `E`, and that implementation is applied at runtime.</span></span>
*  <span data-ttu-id="3d917-2746">Si aucun opérateur n’est trouvée, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="3d917-2746">If no such operator is found, a binding-time error occurs.</span></span>

<span data-ttu-id="3d917-2747">Le `DBBool` type struct dans [de base de données de type booléen](structs.md#database-boolean-type) fournit un exemple d’un type qui implémente `operator true` et `operator false`.</span><span class="sxs-lookup"><span data-stu-id="3d917-2747">The `DBBool` struct type in [Database boolean type](structs.md#database-boolean-type) provides an example of a type that implements `operator true` and `operator false`.</span></span>
