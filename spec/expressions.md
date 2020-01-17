---
ms.openlocfilehash: f61039abd6bd557ac0ea625e6aac1c8bafa57b02
ms.sourcegitcommit: e134bb7058e9848120b93b345f96d6ac0cb8c815
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "71704086"
---
# <a name="expressions"></a><span data-ttu-id="b0787-101">Expressions</span><span class="sxs-lookup"><span data-stu-id="b0787-101">Expressions</span></span>

<span data-ttu-id="b0787-102">Une expression est une séquence d'opérateurs et d'opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-102">An expression is a sequence of operators and operands.</span></span> <span data-ttu-id="b0787-103">Ce chapitre définit la syntaxe, l’ordre d’évaluation des opérandes et des opérateurs, ainsi que la signification des expressions.</span><span class="sxs-lookup"><span data-stu-id="b0787-103">This chapter defines the syntax, order of evaluation of operands and operators, and meaning of expressions.</span></span>

## <a name="expression-classifications"></a><span data-ttu-id="b0787-104">Classifications d’expressions</span><span class="sxs-lookup"><span data-stu-id="b0787-104">Expression classifications</span></span>

<span data-ttu-id="b0787-105">Une expression est classée comme l'un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-105">An expression is classified as one of the following:</span></span>

*  <span data-ttu-id="b0787-106">Valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-106">A value.</span></span> <span data-ttu-id="b0787-107">Chaque valeur possède un type associé.</span><span class="sxs-lookup"><span data-stu-id="b0787-107">Every value has an associated type.</span></span>
*  <span data-ttu-id="b0787-108">Variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-108">A variable.</span></span> <span data-ttu-id="b0787-109">Chaque variable a un type associé, à savoir le type déclaré de la variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-109">Every variable has an associated type, namely the declared type of the variable.</span></span>
*  <span data-ttu-id="b0787-110">Espace de noms.</span><span class="sxs-lookup"><span data-stu-id="b0787-110">A namespace.</span></span> <span data-ttu-id="b0787-111">Une expression avec cette classification ne peut apparaître qu’en tant que partie gauche d’un *member_access* ([accès aux membres](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="b0787-111">An expression with this classification can only appear as the left hand side of a *member_access* ([Member access](expressions.md#member-access)).</span></span> <span data-ttu-id="b0787-112">Dans tout autre contexte, une expression classée en tant qu’espace de noms provoque une erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-112">In any other context, an expression classified as a namespace causes a compile-time error.</span></span>
*  <span data-ttu-id="b0787-113">Type.</span><span class="sxs-lookup"><span data-stu-id="b0787-113">A type.</span></span> <span data-ttu-id="b0787-114">Une expression avec cette classification ne peut apparaître qu’en tant que partie gauche d’un *member_access* ([accès aux membres](expressions.md#member-access)), ou en tant qu’opérande pour l’opérateur `as` ([opérateur as](expressions.md#the-as-operator)), le `is` opérateur ([opérateur is](expressions.md#the-is-operator)) ou l’opérateur `typeof` ([opérateur typeof](expressions.md#the-typeof-operator)).</span><span class="sxs-lookup"><span data-stu-id="b0787-114">An expression with this classification can only appear as the left hand side of a *member_access* ([Member access](expressions.md#member-access)), or as an operand for the `as` operator ([The as operator](expressions.md#the-as-operator)), the `is` operator ([The is operator](expressions.md#the-is-operator)), or the `typeof` operator ([The typeof operator](expressions.md#the-typeof-operator)).</span></span> <span data-ttu-id="b0787-115">Dans tout autre contexte, une expression classifiée comme un type provoque une erreur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-115">In any other context, an expression classified as a type causes a compile-time error.</span></span>
*  <span data-ttu-id="b0787-116">Un groupe de méthodes, qui est un ensemble de méthodes surchargées résultant d’une recherche de membre ([recherche de membre](expressions.md#member-lookup)).</span><span class="sxs-lookup"><span data-stu-id="b0787-116">A method group, which is a set of overloaded methods resulting from a member lookup ([Member lookup](expressions.md#member-lookup)).</span></span> <span data-ttu-id="b0787-117">Un groupe de méthodes peut avoir une expression d’instance associée et une liste d’arguments de type associée.</span><span class="sxs-lookup"><span data-stu-id="b0787-117">A method group may have an associated instance expression and an associated type argument list.</span></span> <span data-ttu-id="b0787-118">Quand une méthode d’instance est appelée, le résultat de l’évaluation de l’expression d’instance devient l’instance représentée par `this` ([cet accès](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="b0787-118">When an instance method is invoked, the result of evaluating the instance expression becomes the instance represented by `this` ([This access](expressions.md#this-access)).</span></span> <span data-ttu-id="b0787-119">Un groupe de méthodes est autorisé dans un *invocation_expression* ([expressions d’appel](expressions.md#invocation-expressions)), un *delegate_creation_expression* (expressions de[création de délégué](expressions.md#delegate-creation-expressions)) et en tant que côté gauche d’un opérateur is et peut être converti implicitement en un type délégué compatible ([conversions de groupe de méthodes](conversions.md#method-group-conversions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-119">A method group is permitted in an *invocation_expression* ([Invocation expressions](expressions.md#invocation-expressions)) , a *delegate_creation_expression* ([Delegate creation expressions](expressions.md#delegate-creation-expressions)) and as the left hand side of an is operator, and can be implicitly converted to a compatible delegate type ([Method group conversions](conversions.md#method-group-conversions)).</span></span> <span data-ttu-id="b0787-120">Dans tout autre contexte, une expression classifiée comme un groupe de méthodes provoque une erreur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-120">In any other context, an expression classified as a method group causes a compile-time error.</span></span>
*  <span data-ttu-id="b0787-121">Littéral null.</span><span class="sxs-lookup"><span data-stu-id="b0787-121">A null literal.</span></span> <span data-ttu-id="b0787-122">Une expression avec cette classification peut être implicitement convertie en un type référence ou un type Nullable.</span><span class="sxs-lookup"><span data-stu-id="b0787-122">An expression with this classification can be implicitly converted to a reference type or nullable type.</span></span>
*  <span data-ttu-id="b0787-123">Fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="b0787-123">An anonymous function.</span></span> <span data-ttu-id="b0787-124">Une expression avec cette classification peut être convertie implicitement en un type délégué compatible ou un type d’arborescence d’expression.</span><span class="sxs-lookup"><span data-stu-id="b0787-124">An expression with this classification can be implicitly converted to a compatible delegate type or expression tree type.</span></span>
*  <span data-ttu-id="b0787-125">Accès à une propriété.</span><span class="sxs-lookup"><span data-stu-id="b0787-125">A property access.</span></span> <span data-ttu-id="b0787-126">Chaque accès à une propriété a un type associé, à savoir le type de la propriété.</span><span class="sxs-lookup"><span data-stu-id="b0787-126">Every property access has an associated type, namely the type of the property.</span></span> <span data-ttu-id="b0787-127">En outre, un accès à une propriété peut avoir une expression d’instance associée.</span><span class="sxs-lookup"><span data-stu-id="b0787-127">Furthermore, a property access may have an associated instance expression.</span></span> <span data-ttu-id="b0787-128">Lorsqu’un accesseur (bloc `get` ou `set`) d’un accès à une propriété d’instance est appelé, le résultat de l’évaluation de l’expression d’instance devient l’instance représentée par `this` ([cet accès](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="b0787-128">When an accessor (the `get` or `set` block) of an instance property access is invoked, the result of evaluating the instance expression becomes the instance represented by `this` ([This access](expressions.md#this-access)).</span></span>
*  <span data-ttu-id="b0787-129">Accès à un événement.</span><span class="sxs-lookup"><span data-stu-id="b0787-129">An event access.</span></span> <span data-ttu-id="b0787-130">Chaque accès à un événement a un type associé, à savoir le type de l’événement.</span><span class="sxs-lookup"><span data-stu-id="b0787-130">Every event access has an associated type, namely the type of the event.</span></span> <span data-ttu-id="b0787-131">En outre, un accès aux événements peut avoir une expression d’instance associée.</span><span class="sxs-lookup"><span data-stu-id="b0787-131">Furthermore, an event access may have an associated instance expression.</span></span> <span data-ttu-id="b0787-132">Un accès aux événements peut apparaître en tant qu’opérande gauche des opérateurs `+=` et `-=` ([assignation d’événement](expressions.md#event-assignment)).</span><span class="sxs-lookup"><span data-stu-id="b0787-132">An event access may appear as the left hand operand of the `+=` and `-=` operators ([Event assignment](expressions.md#event-assignment)).</span></span> <span data-ttu-id="b0787-133">Dans tout autre contexte, une expression classifiée comme un accès à un événement provoque une erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-133">In any other context, an expression classified as an event access causes a compile-time error.</span></span>
*  <span data-ttu-id="b0787-134">Un accès à un indexeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-134">An indexer access.</span></span> <span data-ttu-id="b0787-135">Chaque accès à l’indexeur a un type associé, à savoir le type d’élément de l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-135">Every indexer access has an associated type, namely the element type of the indexer.</span></span> <span data-ttu-id="b0787-136">En outre, un accès à un indexeur a une expression d’instance associée et une liste d’arguments associée.</span><span class="sxs-lookup"><span data-stu-id="b0787-136">Furthermore, an indexer access has an associated instance expression and an associated argument list.</span></span> <span data-ttu-id="b0787-137">Lorsqu’un accesseur (bloc `get` ou `set`) d’un accès à un indexeur est appelé, le résultat de l’évaluation de l’expression d’instance devient l’instance représentée par `this` ([cet accès](expressions.md#this-access)), et le résultat de l’évaluation de la liste d’arguments devient la liste de paramètres de l’appel.</span><span class="sxs-lookup"><span data-stu-id="b0787-137">When an accessor (the `get` or `set` block) of an indexer access is invoked, the result of evaluating the instance expression becomes the instance represented by `this` ([This access](expressions.md#this-access)), and the result of evaluating the argument list becomes the parameter list of the invocation.</span></span>
*  <span data-ttu-id="b0787-138">Rien.</span><span class="sxs-lookup"><span data-stu-id="b0787-138">Nothing.</span></span> <span data-ttu-id="b0787-139">Cela se produit lorsque l’expression est un appel d’une méthode avec un type de retour `void`.</span><span class="sxs-lookup"><span data-stu-id="b0787-139">This occurs when the expression is an invocation of a method with a return type of `void`.</span></span> <span data-ttu-id="b0787-140">Une expression classifiée comme Nothing n’est valide que dans le contexte d’une *statement_expression* ([instructions d’expression](statements.md#expression-statements)).</span><span class="sxs-lookup"><span data-stu-id="b0787-140">An expression classified as nothing is only valid in the context of a *statement_expression* ([Expression statements](statements.md#expression-statements)).</span></span>

<span data-ttu-id="b0787-141">Le résultat final d’une expression n’est jamais un espace de noms, un type, un groupe de méthodes ou un accès à un événement.</span><span class="sxs-lookup"><span data-stu-id="b0787-141">The final result of an expression is never a namespace, type, method group, or event access.</span></span> <span data-ttu-id="b0787-142">Au lieu de cela, comme indiqué ci-dessus, ces catégories d’expressions sont des constructions intermédiaires qui sont uniquement autorisées dans certains contextes.</span><span class="sxs-lookup"><span data-stu-id="b0787-142">Rather, as noted above, these categories of expressions are intermediate constructs that are only permitted in certain contexts.</span></span>

<span data-ttu-id="b0787-143">Un accès à une propriété ou un accès à un indexeur est toujours reclassifié comme valeur en effectuant un appel de l' *accesseur Get* ou de l' *accesseur Set*.</span><span class="sxs-lookup"><span data-stu-id="b0787-143">A property access or indexer access is always reclassified as a value by performing an invocation of the *get accessor* or the *set accessor*.</span></span> <span data-ttu-id="b0787-144">L’accesseur particulier est déterminé par le contexte de l’accès à la propriété ou à l’indexeur : si l’accès est la cible d’une assignation, l' *accesseur Set* est appelé pour assigner une nouvelle valeur ([assignation simple](expressions.md#simple-assignment)).</span><span class="sxs-lookup"><span data-stu-id="b0787-144">The particular accessor is determined by the context of the property or indexer access: If the access is the target of an assignment, the *set accessor* is invoked to assign a new value ([Simple assignment](expressions.md#simple-assignment)).</span></span> <span data-ttu-id="b0787-145">Dans le cas contraire, l' *accesseur Get* est appelé pour obtenir la valeur actuelle ([valeurs des expressions](expressions.md#values-of-expressions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-145">Otherwise, the *get accessor* is invoked to obtain the current value ([Values of expressions](expressions.md#values-of-expressions)).</span></span>

### <a name="values-of-expressions"></a><span data-ttu-id="b0787-146">Valeurs des expressions</span><span class="sxs-lookup"><span data-stu-id="b0787-146">Values of expressions</span></span>

<span data-ttu-id="b0787-147">La plupart des constructions qui impliquent une expression requièrent finalement que l’expression désigne une ***valeur***.</span><span class="sxs-lookup"><span data-stu-id="b0787-147">Most of the constructs that involve an expression ultimately require the expression to denote a ***value***.</span></span> <span data-ttu-id="b0787-148">Dans ce cas, si l’expression réelle désigne un espace de noms, un type, un groupe de méthodes ou rien, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-148">In such cases, if the actual expression denotes a namespace, a type, a method group, or nothing, a compile-time error occurs.</span></span> <span data-ttu-id="b0787-149">Toutefois, si l’expression désigne un accès à une propriété, un indexeur ou une variable, la valeur de la propriété, de l’indexeur ou de la variable est implicitement remplacée :</span><span class="sxs-lookup"><span data-stu-id="b0787-149">However, if the expression denotes a property access, an indexer access, or a variable, the value of the property, indexer, or variable is implicitly substituted:</span></span>

*  <span data-ttu-id="b0787-150">La valeur d’une variable est simplement la valeur actuellement stockée dans l’emplacement de stockage identifié par la variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-150">The value of a variable is simply the value currently stored in the storage location identified by the variable.</span></span> <span data-ttu-id="b0787-151">Une variable doit être considérée comme assignée de manière définitive ([assignation définie](variables.md#definite-assignment)) avant que sa valeur puisse être obtenue, sinon une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-151">A variable must be considered definitely assigned ([Definite assignment](variables.md#definite-assignment)) before its value can be obtained, or otherwise a compile-time error occurs.</span></span>
*  <span data-ttu-id="b0787-152">La valeur d’une expression d’accès à la propriété est obtenue en appelant l' *accesseur Get* de la propriété.</span><span class="sxs-lookup"><span data-stu-id="b0787-152">The value of a property access expression is obtained by invoking the *get accessor* of the property.</span></span> <span data-ttu-id="b0787-153">Si la propriété n’a pas d' *accesseur Get*, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-153">If the property has no *get accessor*, a compile-time error occurs.</span></span> <span data-ttu-id="b0787-154">Sinon, un appel de membre de fonction ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) est effectué et le résultat de l’appel devient la valeur de l’expression d’accès à la propriété.</span><span class="sxs-lookup"><span data-stu-id="b0787-154">Otherwise, a function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) is performed, and the result of the invocation becomes the value of the property access expression.</span></span>
*  <span data-ttu-id="b0787-155">La valeur d’une expression d’accès à l’indexeur est obtenue en appelant l' *accesseur Get* de l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-155">The value of an indexer access expression is obtained by invoking the *get accessor* of the indexer.</span></span> <span data-ttu-id="b0787-156">Si l’indexeur n’a pas d' *accesseur Get*, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-156">If the indexer has no *get accessor*, a compile-time error occurs.</span></span> <span data-ttu-id="b0787-157">Sinon, un appel de membre de fonction ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) est effectué avec la liste d’arguments associée à l’expression d’accès de l’indexeur, et le résultat de l’appel devient la valeur de l’expression d’accès de l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-157">Otherwise, a function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) is performed with the argument list associated with the indexer access expression, and the result of the invocation becomes the value of the indexer access expression.</span></span>

## <a name="static-and-dynamic-binding"></a><span data-ttu-id="b0787-158">Liaison statique et dynamique</span><span class="sxs-lookup"><span data-stu-id="b0787-158">Static and Dynamic Binding</span></span>

<span data-ttu-id="b0787-159">Le processus de détermination de la signification d’une opération en fonction du type ou de la valeur des expressions constitutives (arguments, opérandes, récepteurs) est souvent appelé ***liaison***.</span><span class="sxs-lookup"><span data-stu-id="b0787-159">The process of determining the meaning of an operation based on the type or value of constituent expressions (arguments, operands, receivers) is often referred to as ***binding***.</span></span> <span data-ttu-id="b0787-160">Par exemple, la signification d’un appel de méthode est déterminée en fonction du type du récepteur et des arguments.</span><span class="sxs-lookup"><span data-stu-id="b0787-160">For instance the meaning of a method call is determined based on the type of the receiver and arguments.</span></span> <span data-ttu-id="b0787-161">La signification d’un opérateur est déterminée en fonction du type de ses opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-161">The meaning of an operator is determined based on the type of its operands.</span></span>

<span data-ttu-id="b0787-162">C# Le sens d’une opération est généralement déterminé au moment de la compilation, en fonction du type au moment de la compilation des expressions qui le composent.</span><span class="sxs-lookup"><span data-stu-id="b0787-162">In C# the meaning of an operation is usually determined at compile-time, based on the compile-time type of its constituent expressions.</span></span> <span data-ttu-id="b0787-163">De même, si une expression contient une erreur, l’erreur est détectée et signalée par le compilateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-163">Likewise, if an expression contains an error, the error is detected and reported by the compiler.</span></span> <span data-ttu-id="b0787-164">Cette approche est connue sous le nom de ***liaison statique***.</span><span class="sxs-lookup"><span data-stu-id="b0787-164">This approach is known as ***static binding***.</span></span>

<span data-ttu-id="b0787-165">Toutefois, si une expression est une expression dynamique (par exemple, a le type `dynamic`), cela indique que toute liaison à laquelle elle participe doit être basée sur son type au moment de l’exécution (par exemple, le type réel de l’objet qu’elle désigne au moment de l’exécution) plutôt que le type qu’elle a au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-165">However, if an expression is a dynamic expression (i.e. has the type `dynamic`) this indicates that any binding that it participates in should be based on its run-time type (i.e. the actual type of the object it denotes at run-time) rather than the type it has at compile-time.</span></span> <span data-ttu-id="b0787-166">La liaison d’une telle opération est donc différée jusqu’au moment où l’opération doit être exécutée pendant l’exécution du programme.</span><span class="sxs-lookup"><span data-stu-id="b0787-166">The binding of such an operation is therefore deferred until the time where the operation is to be executed during the running of the program.</span></span> <span data-ttu-id="b0787-167">C’est ce que l’on appelle la ***liaison dynamique***.</span><span class="sxs-lookup"><span data-stu-id="b0787-167">This is referred to as ***dynamic binding***.</span></span>

<span data-ttu-id="b0787-168">Lorsqu’une opération est liée de manière dynamique, le compilateur n’effectue que peu ou pas de vérification.</span><span class="sxs-lookup"><span data-stu-id="b0787-168">When an operation is dynamically bound, little or no checking is performed by the compiler.</span></span> <span data-ttu-id="b0787-169">Au lieu de cela, si la liaison au moment de l’exécution échoue, les erreurs sont signalées en tant qu’exceptions au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b0787-169">Instead if the run-time binding fails, errors are reported as exceptions at run-time.</span></span>

<span data-ttu-id="b0787-170">Les opérations suivantes dans C# sont soumises à la liaison :</span><span class="sxs-lookup"><span data-stu-id="b0787-170">The following operations in C# are subject to binding:</span></span>

*  <span data-ttu-id="b0787-171">Accès au membre : `e.M`</span><span class="sxs-lookup"><span data-stu-id="b0787-171">Member access: `e.M`</span></span>
*  <span data-ttu-id="b0787-172">Appel de méthode : `e.M(e1, ..., eN)`</span><span class="sxs-lookup"><span data-stu-id="b0787-172">Method invocation: `e.M(e1, ..., eN)`</span></span>
*  <span data-ttu-id="b0787-173">Appel de délégué :`e(e1, ..., eN)`</span><span class="sxs-lookup"><span data-stu-id="b0787-173">Delegate invocation:`e(e1, ..., eN)`</span></span>
*  <span data-ttu-id="b0787-174">Accès aux éléments : `e[e1, ..., eN]`</span><span class="sxs-lookup"><span data-stu-id="b0787-174">Element access: `e[e1, ..., eN]`</span></span>
*  <span data-ttu-id="b0787-175">Création d’objets : `new C(e1, ..., eN)`</span><span class="sxs-lookup"><span data-stu-id="b0787-175">Object creation: `new C(e1, ..., eN)`</span></span>
*  <span data-ttu-id="b0787-176">Opérateurs unaires surchargés : `+`, `-`, `!`, `~`, `++`, `--`, `true`, `false`</span><span class="sxs-lookup"><span data-stu-id="b0787-176">Overloaded unary operators: `+`, `-`, `!`, `~`, `++`, `--`, `true`, `false`</span></span>
*  <span data-ttu-id="b0787-177">Opérateurs binaires surchargés : `+`, `-`, `*`, `/`, `%`, `&`, `&&`, `|`, `||`, `??`, `^`, `<<`, `>>`, `==`,`!=`, `>`, `<`, `>=`, `<=`</span><span class="sxs-lookup"><span data-stu-id="b0787-177">Overloaded binary operators: `+`, `-`, `*`, `/`, `%`, `&`, `&&`, `|`, `||`, `??`, `^`, `<<`, `>>`, `==`,`!=`, `>`, `<`, `>=`, `<=`</span></span>
*  <span data-ttu-id="b0787-178">Opérateurs d’assignation : `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, `>>=`</span><span class="sxs-lookup"><span data-stu-id="b0787-178">Assignment operators: `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, `>>=`</span></span>
*  <span data-ttu-id="b0787-179">Conversions implicites et explicites</span><span class="sxs-lookup"><span data-stu-id="b0787-179">Implicit and explicit conversions</span></span>

<span data-ttu-id="b0787-180">Quand aucune expression dynamique n’est impliquée, C# la valeur par défaut est la liaison statique, ce qui signifie que les types de compilation des expressions constitutives sont utilisés dans le processus de sélection.</span><span class="sxs-lookup"><span data-stu-id="b0787-180">When no dynamic expressions are involved, C# defaults to static binding, which means that the compile-time types of constituent expressions are used in the selection process.</span></span> <span data-ttu-id="b0787-181">Toutefois, lorsque l’une des expressions constitutives des opérations listées ci-dessus est une expression dynamique, l’opération est liée dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="b0787-181">However, when one of the constituent expressions in the operations listed above is a dynamic expression, the operation is instead dynamically bound.</span></span>

### <a name="binding-time"></a><span data-ttu-id="b0787-182">Liaison-heure</span><span class="sxs-lookup"><span data-stu-id="b0787-182">Binding-time</span></span>

<span data-ttu-id="b0787-183">La liaison statique a lieu au moment de la compilation, tandis que la liaison dynamique a lieu au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b0787-183">Static binding takes place at compile-time, whereas dynamic binding takes place at run-time.</span></span> <span data-ttu-id="b0787-184">Dans les sections suivantes, le terme « ***Binding-Time*** » fait référence à la compilation ou au moment de l’exécution, selon le moment où la liaison a lieu.</span><span class="sxs-lookup"><span data-stu-id="b0787-184">In the following sections, the term ***binding-time*** refers to either compile-time or run-time, depending on when the binding takes place.</span></span>

<span data-ttu-id="b0787-185">L’exemple suivant illustre les notions de liaison statique et dynamique et de l’heure de liaison :</span><span class="sxs-lookup"><span data-stu-id="b0787-185">The following example illustrates the notions of static and dynamic binding and of binding-time:</span></span>
```csharp
object  o = 5;
dynamic d = 5;

Console.WriteLine(5);  // static  binding to Console.WriteLine(int)
Console.WriteLine(o);  // static  binding to Console.WriteLine(object)
Console.WriteLine(d);  // dynamic binding to Console.WriteLine(int)
```

<span data-ttu-id="b0787-186">Les deux premiers appels sont liés statiquement : la surcharge de `Console.WriteLine` est choisie en fonction du type au moment de la compilation de leur argument.</span><span class="sxs-lookup"><span data-stu-id="b0787-186">The first two calls are statically bound: the overload of `Console.WriteLine` is picked based on the compile-time type of their argument.</span></span> <span data-ttu-id="b0787-187">Ainsi, la durée de la liaison est au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-187">Thus, the binding-time is compile-time.</span></span>

<span data-ttu-id="b0787-188">Le troisième appel est lié dynamiquement : la surcharge de `Console.WriteLine` est choisie en fonction du type au moment de l’exécution de son argument.</span><span class="sxs-lookup"><span data-stu-id="b0787-188">The third call is dynamically bound: the overload of `Console.WriteLine` is picked based on the run-time type of its argument.</span></span> <span data-ttu-id="b0787-189">Cela se produit parce que l’argument est une expression dynamique--son type au moment de la compilation est `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-189">This happens because the argument is a dynamic expression -- its compile-time type is `dynamic`.</span></span> <span data-ttu-id="b0787-190">Ainsi, le temps de liaison pour le troisième appel est au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b0787-190">Thus, the binding-time for the third call is run-time.</span></span>

### <a name="dynamic-binding"></a><span data-ttu-id="b0787-191">Liaison dynamique</span><span class="sxs-lookup"><span data-stu-id="b0787-191">Dynamic binding</span></span>

<span data-ttu-id="b0787-192">L’objectif de la liaison dynamique est de C# permettre aux programmes d’interagir avec des ***objets dynamiques***, c’est-à-dire des objets qui ne respectent pas les règles C# normales du système de type.</span><span class="sxs-lookup"><span data-stu-id="b0787-192">The purpose of dynamic binding is to allow C# programs to interact with ***dynamic objects***, i.e. objects that do not follow the normal rules of the C# type system.</span></span> <span data-ttu-id="b0787-193">Les objets dynamiques peuvent être des objets provenant d’autres langages de programmation avec des systèmes de types différents, ou des objets qui sont configurés par programme pour implémenter leur propre sémantique de liaison pour différentes opérations.</span><span class="sxs-lookup"><span data-stu-id="b0787-193">Dynamic objects may be objects from other programming languages with different types systems, or they may be objects that are programmatically setup to implement their own binding semantics for different operations.</span></span>

<span data-ttu-id="b0787-194">Le mécanisme par lequel un objet dynamique implémente sa propre sémantique est défini par l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="b0787-194">The mechanism by which a dynamic object implements its own semantics is implementation defined.</span></span> <span data-ttu-id="b0787-195">Une interface donnée, à nouveau définie, est implémentée par les objets dynamiques pour signaler au moment C# de l’exécution qu’ils ont une sémantique spéciale.</span><span class="sxs-lookup"><span data-stu-id="b0787-195">A given interface -- again implementation defined -- is implemented by dynamic objects to signal to the C# run-time that they have special semantics.</span></span> <span data-ttu-id="b0787-196">Par conséquent, chaque fois que des opérations sur un objet dynamique sont liées de manière dynamique, leur propre sémantique de liaison C# , plutôt que celles de telles qu’elles sont spécifiées dans ce document, prennent le contrôle.</span><span class="sxs-lookup"><span data-stu-id="b0787-196">Thus, whenever operations on a dynamic object are dynamically bound, their own binding semantics, rather than those of C# as specified in this document, take over.</span></span>

<span data-ttu-id="b0787-197">Bien que l’objectif de la liaison dynamique soit d’autoriser l’interopérabilité avec C# les objets dynamiques, autorise la liaison dynamique sur tous les objets, qu’ils soient dynamiques ou non.</span><span class="sxs-lookup"><span data-stu-id="b0787-197">While the purpose of dynamic binding is to allow interoperation with dynamic objects, C# allows dynamic binding on all objects, whether they are dynamic or not.</span></span> <span data-ttu-id="b0787-198">Cela permet une intégration plus fluide des objets dynamiques, car les résultats des opérations sur ceux-ci peuvent ne pas être eux-mêmes des objets dynamiques, mais ils sont toujours d’un type inconnu pour le programmeur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-198">This allows for a smoother integration of dynamic objects, as the results of operations on them may not themselves be dynamic objects, but are still of a type unknown to the programmer at compile-time.</span></span> <span data-ttu-id="b0787-199">En outre, la liaison dynamique permet d’éliminer le code basé sur la réflexion, susceptible d’engendrer des erreurs, même quand aucun objet impliqué n’est un objet dynamique.</span><span class="sxs-lookup"><span data-stu-id="b0787-199">Also dynamic binding can help eliminate error-prone reflection-based code even when no objects involved are dynamic objects.</span></span>

<span data-ttu-id="b0787-200">Les sections suivantes décrivent pour chaque construction dans le langage exactement quand la liaison dynamique est appliquée, la vérification du temps de compilation, le cas échéant, et le résultat de la classification des expressions et des résultats au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-200">The following sections describe for each construct in the language exactly when dynamic binding is applied, what compile time checking -- if any -- is applied, and what the compile-time result and expression classification is.</span></span>

### <a name="types-of-constituent-expressions"></a><span data-ttu-id="b0787-201">Types d’expressions constitutives</span><span class="sxs-lookup"><span data-stu-id="b0787-201">Types of constituent expressions</span></span>

<span data-ttu-id="b0787-202">Lorsqu’une opération est liée de manière statique, le type d’une expression de composant (par exemple, un récepteur, un argument, un index ou un opérande) est toujours considéré comme le type au moment de la compilation de cette expression.</span><span class="sxs-lookup"><span data-stu-id="b0787-202">When an operation is statically bound, the type of a constituent expression (e.g. a receiver, an argument, an index or an operand) is always considered to be the compile-time type of that expression.</span></span>

<span data-ttu-id="b0787-203">Lorsqu’une opération est liée de manière dynamique, le type d’une expression constitutive est déterminé de différentes manières selon le type de compilation de l’expression constitutive :</span><span class="sxs-lookup"><span data-stu-id="b0787-203">When an operation is dynamically bound, the type of a constituent expression is determined in different ways depending on the compile-time type of the constituent expression:</span></span>

*  <span data-ttu-id="b0787-204">Une expression constitutive du type `dynamic` au moment de la compilation est considérée comme ayant le type de la valeur réelle évaluée par l’expression au moment de l’exécution</span><span class="sxs-lookup"><span data-stu-id="b0787-204">A constituent expression of compile-time type `dynamic` is considered to have the type of the actual value that the expression evaluates to at runtime</span></span>
*  <span data-ttu-id="b0787-205">Une expression de composant dont le type au moment de la compilation est un paramètre de type est considérée comme ayant le type auquel le paramètre de type est lié au moment de l’exécution</span><span class="sxs-lookup"><span data-stu-id="b0787-205">A constituent expression whose compile-time type is a type parameter is considered to have the type which the type parameter is bound to at runtime</span></span>
*  <span data-ttu-id="b0787-206">Dans le cas contraire, l’expression constitutive est considérée comme ayant le type au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-206">Otherwise the constituent expression is considered to have its compile-time type.</span></span>

## <a name="operators"></a><span data-ttu-id="b0787-207">Opérateurs</span><span class="sxs-lookup"><span data-stu-id="b0787-207">Operators</span></span>

<span data-ttu-id="b0787-208">Les expressions sont construites à partir d' ***opérandes*** et d' ***opérateurs***.</span><span class="sxs-lookup"><span data-stu-id="b0787-208">Expressions are constructed from ***operands*** and ***operators***.</span></span> <span data-ttu-id="b0787-209">Les opérateurs d’une expression indiquent les opérations à appliquer aux opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-209">The operators of an expression indicate which operations to apply to the operands.</span></span> <span data-ttu-id="b0787-210">Parmi les exemples d’opérateurs figurent `+`, `-`, `*`, `/` et `new`.</span><span class="sxs-lookup"><span data-stu-id="b0787-210">Examples of operators include `+`, `-`, `*`, `/`, and `new`.</span></span> <span data-ttu-id="b0787-211">Les littéraux, les champs, les variables locales et les expressions sont des exemples d’opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-211">Examples of operands include literals, fields, local variables, and expressions.</span></span>

<span data-ttu-id="b0787-212">Il existe trois types d’opérateurs :</span><span class="sxs-lookup"><span data-stu-id="b0787-212">There are three kinds of operators:</span></span>

*  <span data-ttu-id="b0787-213">Les opérateurs unaires.</span><span class="sxs-lookup"><span data-stu-id="b0787-213">Unary operators.</span></span> <span data-ttu-id="b0787-214">Les opérateurs unaires acceptent un opérande et utilisent soit la notation de préfixe (par exemple `--x`), soit la notation suffixée (comme `x++`).</span><span class="sxs-lookup"><span data-stu-id="b0787-214">The unary operators take one operand and use either prefix notation (such as `--x`) or postfix notation (such as `x++`).</span></span>
*  <span data-ttu-id="b0787-215">Opérateurs binaires.</span><span class="sxs-lookup"><span data-stu-id="b0787-215">Binary operators.</span></span> <span data-ttu-id="b0787-216">Les opérateurs binaires prennent deux opérandes et utilisent tous la notation infixe (par exemple `x + y`).</span><span class="sxs-lookup"><span data-stu-id="b0787-216">The binary operators take two operands and all use infix notation (such as `x + y`).</span></span>
*  <span data-ttu-id="b0787-217">Opérateur ternaire.</span><span class="sxs-lookup"><span data-stu-id="b0787-217">Ternary operator.</span></span> <span data-ttu-id="b0787-218">Un seul opérateur ternaire, `?:`, existe ; Il prend trois opérandes et utilise la notation infixe (`c ? x : y`).</span><span class="sxs-lookup"><span data-stu-id="b0787-218">Only one ternary operator, `?:`, exists; it takes three operands and uses infix notation (`c ? x : y`).</span></span>

<span data-ttu-id="b0787-219">L’ordre d’évaluation des opérateurs dans une expression est déterminé par la ***priorité*** et l' ***associativité*** des opérateurs ([priorité et associativité](expressions.md#operator-precedence-and-associativity)des opérateurs).</span><span class="sxs-lookup"><span data-stu-id="b0787-219">The order of evaluation of operators in an expression is determined by the ***precedence*** and ***associativity*** of the operators ([Operator precedence and associativity](expressions.md#operator-precedence-and-associativity)).</span></span>

<span data-ttu-id="b0787-220">Les opérandes d’une expression sont évalués de gauche à droite.</span><span class="sxs-lookup"><span data-stu-id="b0787-220">Operands in an expression are evaluated from left to right.</span></span> <span data-ttu-id="b0787-221">Par exemple, dans `F(i) + G(i++) * H(i)`, la méthode `F` est appelée à l’aide de l’ancienne valeur de `i`, puis la méthode `G` est appelée avec l’ancienne valeur de `i`et, enfin, la méthode `H` est appelée avec la nouvelle valeur de `i`.</span><span class="sxs-lookup"><span data-stu-id="b0787-221">For example, in `F(i) + G(i++) * H(i)`, method `F` is called using the old value of `i`, then method `G` is called with the old value of `i`, and, finally, method `H` is called with the new value of `i`.</span></span> <span data-ttu-id="b0787-222">Cela est indépendant de et non lié à la priorité des opérateurs.</span><span class="sxs-lookup"><span data-stu-id="b0787-222">This is separate from and unrelated to operator precedence.</span></span>

<span data-ttu-id="b0787-223">Certains opérateurs peuvent être ***surchargés***.</span><span class="sxs-lookup"><span data-stu-id="b0787-223">Certain operators can be ***overloaded***.</span></span> <span data-ttu-id="b0787-224">La surcharge d’opérateur permet de spécifier des implémentations d’opérateur définies par l’utilisateur pour les opérations où l’un des opérandes ou les deux sont d’un type de classe ou de struct défini par l’utilisateur ([surcharge d’opérateur](expressions.md#operator-overloading)).</span><span class="sxs-lookup"><span data-stu-id="b0787-224">Operator overloading permits user-defined operator implementations to be specified for operations where one or both of the operands are of a user-defined class or struct type ([Operator overloading](expressions.md#operator-overloading)).</span></span>

### <a name="operator-precedence-and-associativity"></a><span data-ttu-id="b0787-225">Priorité des opérateurs et associativité</span><span class="sxs-lookup"><span data-stu-id="b0787-225">Operator precedence and associativity</span></span>

<span data-ttu-id="b0787-226">Quand une expression contient plusieurs opérateurs, la ***priorité*** des opérateurs contrôle l'ordre dans lequel les opérateurs individuels sont évalués.</span><span class="sxs-lookup"><span data-stu-id="b0787-226">When an expression contains multiple operators, the ***precedence*** of the operators controls the order in which the individual operators are evaluated.</span></span> <span data-ttu-id="b0787-227">Par exemple, l’expression `x + y * z` est évaluée comme `x + (y * z)`, car l’opérateur `*` a une priorité plus élevée que l’opérateur `+` binaire.</span><span class="sxs-lookup"><span data-stu-id="b0787-227">For example, the expression `x + y * z` is evaluated as `x + (y * z)` because the `*` operator has higher precedence than the binary `+` operator.</span></span> <span data-ttu-id="b0787-228">La priorité d’un opérateur est établie par la définition de sa production grammaticale associée.</span><span class="sxs-lookup"><span data-stu-id="b0787-228">The precedence of an operator is established by the definition of its associated grammar production.</span></span> <span data-ttu-id="b0787-229">Par exemple, un *additive_expression* se compose d’une séquence de *multiplicative_expression*s séparés par `+` ou `-` opérateurs, donnant ainsi aux opérateurs `+` et `-` une priorité plus faible que les opérateurs `*`, `/`et `%`.</span><span class="sxs-lookup"><span data-stu-id="b0787-229">For example, an *additive_expression* consists of a sequence of *multiplicative_expression*s separated by `+` or `-` operators, thus giving the `+` and `-` operators lower precedence than the `*`, `/`, and `%` operators.</span></span>

<span data-ttu-id="b0787-230">Le tableau suivant récapitule tous les opérateurs par ordre de priorité, du plus élevé au plus bas :</span><span class="sxs-lookup"><span data-stu-id="b0787-230">The following table summarizes all operators in order of precedence from highest to lowest:</span></span>

| <span data-ttu-id="b0787-231">__Section__</span><span class="sxs-lookup"><span data-stu-id="b0787-231">__Section__</span></span>                                                                                   | <span data-ttu-id="b0787-232">__Catégorie__</span><span class="sxs-lookup"><span data-stu-id="b0787-232">__Category__</span></span>                | <span data-ttu-id="b0787-233">__Opérateurs__</span><span class="sxs-lookup"><span data-stu-id="b0787-233">__Operators__</span></span> | 
|-----------------------------------------------------------------------------------------------|-----------------------------|---------------|
| [<span data-ttu-id="b0787-234">Expressions primaires</span><span class="sxs-lookup"><span data-stu-id="b0787-234">Primary expressions</span></span>](expressions.md#primary-expressions)                                     | <span data-ttu-id="b0787-235">Primary</span><span class="sxs-lookup"><span data-stu-id="b0787-235">Primary</span></span>                     | <span data-ttu-id="b0787-236">`x.y`  `f(x)`  `a[x]`  `x++`  `x--`  `new`  `typeof`  `default`  `checked`  `unchecked`  `delegate`</span><span class="sxs-lookup"><span data-stu-id="b0787-236">`x.y`  `f(x)`  `a[x]`  `x++`  `x--`  `new`  `typeof`  `default`  `checked`  `unchecked`  `delegate`</span></span> | 
| [<span data-ttu-id="b0787-237">Opérateurs unaires</span><span class="sxs-lookup"><span data-stu-id="b0787-237">Unary operators</span></span>](expressions.md#unary-operators)                                             | <span data-ttu-id="b0787-238">Unaire</span><span class="sxs-lookup"><span data-stu-id="b0787-238">Unary</span></span>                       | <span data-ttu-id="b0787-239">`+`  `-`  `!`  `~`  `++x`  `--x`  `(T)x`</span><span class="sxs-lookup"><span data-stu-id="b0787-239">`+`  `-`  `!`  `~`  `++x`  `--x`  `(T)x`</span></span> | 
| [<span data-ttu-id="b0787-240">Opérateurs arithmétiques</span><span class="sxs-lookup"><span data-stu-id="b0787-240">Arithmetic operators</span></span>](expressions.md#arithmetic-operators)                                   | <span data-ttu-id="b0787-241">Multiplication</span><span class="sxs-lookup"><span data-stu-id="b0787-241">Multiplicative</span></span>              | <span data-ttu-id="b0787-242">`*`  `/`  `%`</span><span class="sxs-lookup"><span data-stu-id="b0787-242">`*`  `/`  `%`</span></span> | 
| [<span data-ttu-id="b0787-243">Opérateurs arithmétiques</span><span class="sxs-lookup"><span data-stu-id="b0787-243">Arithmetic operators</span></span>](expressions.md#arithmetic-operators)                                   | <span data-ttu-id="b0787-244">Addition</span><span class="sxs-lookup"><span data-stu-id="b0787-244">Additive</span></span>                    | <span data-ttu-id="b0787-245">`+`  `-`</span><span class="sxs-lookup"><span data-stu-id="b0787-245">`+`  `-`</span></span>      | 
| [<span data-ttu-id="b0787-246">Opérateurs de décalage</span><span class="sxs-lookup"><span data-stu-id="b0787-246">Shift operators</span></span>](expressions.md#shift-operators)                                             | <span data-ttu-id="b0787-247">Shift</span><span class="sxs-lookup"><span data-stu-id="b0787-247">Shift</span></span>                       | <span data-ttu-id="b0787-248">`<<`  `>>`</span><span class="sxs-lookup"><span data-stu-id="b0787-248">`<<`  `>>`</span></span>    | 
| [<span data-ttu-id="b0787-249">Opérateurs relationnels et de test de type</span><span class="sxs-lookup"><span data-stu-id="b0787-249">Relational and type-testing operators</span></span>](expressions.md#relational-and-type-testing-operators) | <span data-ttu-id="b0787-250">Relations et test de type</span><span class="sxs-lookup"><span data-stu-id="b0787-250">Relational and type testing</span></span> | <span data-ttu-id="b0787-251">`<`  `>`  `<=`  `>=`  `is`  `as`</span><span class="sxs-lookup"><span data-stu-id="b0787-251">`<`  `>`  `<=`  `>=`  `is`  `as`</span></span> | 
| [<span data-ttu-id="b0787-252">Opérateurs relationnels et de test de type</span><span class="sxs-lookup"><span data-stu-id="b0787-252">Relational and type-testing operators</span></span>](expressions.md#relational-and-type-testing-operators) | <span data-ttu-id="b0787-253">Égalité</span><span class="sxs-lookup"><span data-stu-id="b0787-253">Equality</span></span>                    | <span data-ttu-id="b0787-254">`==`  `!=`</span><span class="sxs-lookup"><span data-stu-id="b0787-254">`==`  `!=`</span></span>    | 
| [<span data-ttu-id="b0787-255">Opérateurs logiques</span><span class="sxs-lookup"><span data-stu-id="b0787-255">Logical operators</span></span>](expressions.md#logical-operators)                                         | <span data-ttu-id="b0787-256">AND logique</span><span class="sxs-lookup"><span data-stu-id="b0787-256">Logical AND</span></span>                 | `&`           | 
| [<span data-ttu-id="b0787-257">Opérateurs logiques</span><span class="sxs-lookup"><span data-stu-id="b0787-257">Logical operators</span></span>](expressions.md#logical-operators)                                         | <span data-ttu-id="b0787-258">XOR logique</span><span class="sxs-lookup"><span data-stu-id="b0787-258">Logical XOR</span></span>                 | `^`           | 
| [<span data-ttu-id="b0787-259">Opérateurs logiques</span><span class="sxs-lookup"><span data-stu-id="b0787-259">Logical operators</span></span>](expressions.md#logical-operators)                                         | <span data-ttu-id="b0787-260">OR logique</span><span class="sxs-lookup"><span data-stu-id="b0787-260">Logical OR</span></span>                  | <code>&#124;</code>           |
| [<span data-ttu-id="b0787-261">Opérateurs logiques conditionnels</span><span class="sxs-lookup"><span data-stu-id="b0787-261">Conditional logical operators</span></span>](expressions.md#conditional-logical-operators)                 | <span data-ttu-id="b0787-262">AND conditionnel</span><span class="sxs-lookup"><span data-stu-id="b0787-262">Conditional AND</span></span>             | `&&`          | 
| [<span data-ttu-id="b0787-263">Opérateurs logiques conditionnels</span><span class="sxs-lookup"><span data-stu-id="b0787-263">Conditional logical operators</span></span>](expressions.md#conditional-logical-operators)                 | <span data-ttu-id="b0787-264">OR conditionnel</span><span class="sxs-lookup"><span data-stu-id="b0787-264">Conditional OR</span></span>              | <code>&#124;&#124;</code>          | 
| [<span data-ttu-id="b0787-265">Opérateur de fusion de Null</span><span class="sxs-lookup"><span data-stu-id="b0787-265">The null coalescing operator</span></span>](expressions.md#the-null-coalescing-operator)                   | <span data-ttu-id="b0787-266">Fusion de Null</span><span class="sxs-lookup"><span data-stu-id="b0787-266">Null coalescing</span></span>             | `??`          | 
| [<span data-ttu-id="b0787-267">Opérateur conditionnel</span><span class="sxs-lookup"><span data-stu-id="b0787-267">Conditional operator</span></span>](expressions.md#conditional-operator)                                   | <span data-ttu-id="b0787-268">Conditionnel</span><span class="sxs-lookup"><span data-stu-id="b0787-268">Conditional</span></span>                 | `?:`          | 
| <span data-ttu-id="b0787-269">[Opérateurs d’assignation](expressions.md#assignment-operators), [expressions de fonction anonymes](expressions.md#anonymous-function-expressions)</span><span class="sxs-lookup"><span data-stu-id="b0787-269">[Assignment operators](expressions.md#assignment-operators), [Anonymous function expressions](expressions.md#anonymous-function-expressions)</span></span>  | <span data-ttu-id="b0787-270">Assignation et expression lambda</span><span class="sxs-lookup"><span data-stu-id="b0787-270">Assignment and lambda expression</span></span> | <span data-ttu-id="b0787-271">`=`  `*=`  `/=`  `%=`  `+=`  `-=`  `<<=`  `>>=`  `&=`  `^=`  <code>&#124;=</code>  `=>`</span><span class="sxs-lookup"><span data-stu-id="b0787-271">`=`  `*=`  `/=`  `%=`  `+=`  `-=`  `<<=`  `>>=`  `&=`  `^=`  <code>&#124;=</code>  `=>`</span></span> | 

<span data-ttu-id="b0787-272">Lorsqu’un opérande se produit entre deux opérateurs ayant la même priorité, l’associativité des opérateurs contrôle l’ordre dans lequel les opérations sont effectuées :</span><span class="sxs-lookup"><span data-stu-id="b0787-272">When an operand occurs between two operators with the same precedence, the associativity of the operators controls the order in which the operations are performed:</span></span>

*  <span data-ttu-id="b0787-273">À l’exception des opérateurs d’assignation et de l’opérateur de fusion Null, tous les opérateurs binaires sont ***associatifs à gauche***, ce qui signifie que les opérations sont effectuées de gauche à droite.</span><span class="sxs-lookup"><span data-stu-id="b0787-273">Except for the assignment operators and the null coalescing operator, all binary operators are ***left-associative***, meaning that operations are performed from left to right.</span></span> <span data-ttu-id="b0787-274">Par exemple, `x + y + z` est évalué comme étant `(x + y) + z`.</span><span class="sxs-lookup"><span data-stu-id="b0787-274">For example, `x + y + z` is evaluated as `(x + y) + z`.</span></span>
*  <span data-ttu-id="b0787-275">Les opérateurs d’assignation, l’opérateur de fusion Null et l’opérateur conditionnel (`?:`) sont ***associatifs à droite***, ce qui signifie que les opérations sont exécutées de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-275">The assignment operators, the null coalescing operator and the conditional operator (`?:`) are ***right-associative***, meaning that operations are performed from right to left.</span></span> <span data-ttu-id="b0787-276">Par exemple, `x = y = z` est évalué comme étant `x = (y = z)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-276">For example, `x = y = z` is evaluated as `x = (y = z)`.</span></span>

<span data-ttu-id="b0787-277">La priorité et l’associativité peuvent être contrôlées à l’aide de parenthèses.</span><span class="sxs-lookup"><span data-stu-id="b0787-277">Precedence and associativity can be controlled using parentheses.</span></span> <span data-ttu-id="b0787-278">Par exemple, `x + y * z` multiplie d’abord `y` par `z`, puis ajoute le résultat à `x`, mais `(x + y) * z` ajoute d’abord `x` et `y`, puis multiplie le résultat par `z`.</span><span class="sxs-lookup"><span data-stu-id="b0787-278">For example, `x + y * z` first multiplies `y` by `z` and then adds the result to `x`, but `(x + y) * z` first adds `x` and `y` and then multiplies the result by `z`.</span></span>

### <a name="operator-overloading"></a><span data-ttu-id="b0787-279">Surcharge d’opérateur</span><span class="sxs-lookup"><span data-stu-id="b0787-279">Operator overloading</span></span>

<span data-ttu-id="b0787-280">Tous les opérateurs unaires et binaires ont des implémentations prédéfinies qui sont automatiquement disponibles dans toute expression.</span><span class="sxs-lookup"><span data-stu-id="b0787-280">All unary and binary operators have predefined implementations that are automatically available in any expression.</span></span> <span data-ttu-id="b0787-281">Outre les implémentations prédéfinies, les implémentations définies par l’utilisateur peuvent être introduites en incluant des déclarations de `operator` dans des classes et des structs ([opérateurs](classes.md#operators)).</span><span class="sxs-lookup"><span data-stu-id="b0787-281">In addition to the predefined implementations, user-defined implementations can be introduced by including `operator` declarations in classes and structs ([Operators](classes.md#operators)).</span></span> <span data-ttu-id="b0787-282">Les implémentations d’opérateur définies par l’utilisateur ont toujours priorité sur les implémentations d’opérateur prédéfinies : uniquement lorsqu’il n’existe aucune implémentation d’opérateur définie par l’utilisateur applicable, les implémentations d’opérateur prédéfinies sont prises en compte, comme décrit dans [résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution) et [résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="b0787-282">User-defined operator implementations always take precedence over predefined operator implementations: Only when no applicable user-defined operator implementations exist will the predefined operator implementations be considered, as described in [Unary operator overload resolution](expressions.md#unary-operator-overload-resolution) and [Binary operator overload resolution](expressions.md#binary-operator-overload-resolution).</span></span>

<span data-ttu-id="b0787-283">Les ***opérateurs unaires surchargeables*** sont :</span><span class="sxs-lookup"><span data-stu-id="b0787-283">The ***overloadable unary operators*** are:</span></span>
```csharp
+   -   !   ~   ++   --   true   false
```

<span data-ttu-id="b0787-284">Même si `true` et `false` ne sont pas utilisés explicitement dans les expressions (et ne sont donc pas inclus dans la table de précédence dans la [priorité d’opérateur et l’associativité](expressions.md#operator-precedence-and-associativity)), ils sont considérés comme des opérateurs, car ils sont appelés dans plusieurs contextes d’expression : des expressions booléennes ([expressions booléennes](expressions.md#boolean-expressions)) et des expressions impliquant l’opérateur conditionnel ([opérateur conditionnel](expressions.md#conditional-operator)[) et](expressions.md#conditional-logical-operators)conditionnelles</span><span class="sxs-lookup"><span data-stu-id="b0787-284">Although `true` and `false` are not used explicitly in expressions (and therefore are not included in the precedence table in [Operator precedence and associativity](expressions.md#operator-precedence-and-associativity)), they are considered operators because they are invoked in several expression contexts: boolean expressions ([Boolean expressions](expressions.md#boolean-expressions)) and expressions involving the conditional ([Conditional operator](expressions.md#conditional-operator)), and conditional logical operators ([Conditional logical operators](expressions.md#conditional-logical-operators)).</span></span>

<span data-ttu-id="b0787-285">Les ***opérateurs binaires surchargeables*** sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-285">The ***overloadable binary operators*** are:</span></span>
```csharp
+   -   *   /   %   &   |   ^   <<   >>   ==   !=   >   <   >=   <=
```

<span data-ttu-id="b0787-286">Seuls les opérateurs listés ci-dessus peuvent être surchargés.</span><span class="sxs-lookup"><span data-stu-id="b0787-286">Only the operators listed above can be overloaded.</span></span> <span data-ttu-id="b0787-287">En particulier, il n’est pas possible de surcharger l’accès aux membres, l’appel de méthode ou les `=`, `&&`, `||`, `??`, `?:`, `=>`, `checked`, `unchecked`, `new`, `typeof`, `default`, `as`et les opérateurs `is`.</span><span class="sxs-lookup"><span data-stu-id="b0787-287">In particular, it is not possible to overload member access, method invocation, or the `=`, `&&`, `||`, `??`, `?:`, `=>`, `checked`, `unchecked`, `new`, `typeof`, `default`, `as`, and `is` operators.</span></span>

<span data-ttu-id="b0787-288">Quand un opérateur binaire est surchargé, l’opérateur d’assignation correspondant, le cas échéant, est aussi implicitement surchargé.</span><span class="sxs-lookup"><span data-stu-id="b0787-288">When a binary operator is overloaded, the corresponding assignment operator, if any, is also implicitly overloaded.</span></span> <span data-ttu-id="b0787-289">Par exemple, une surcharge de l’opérateur `*` est également une surcharge de l’opérateur `*=`.</span><span class="sxs-lookup"><span data-stu-id="b0787-289">For example, an overload of operator `*` is also an overload of operator `*=`.</span></span> <span data-ttu-id="b0787-290">Cela est décrit plus en détail dans [assignation composée](expressions.md#compound-assignment).</span><span class="sxs-lookup"><span data-stu-id="b0787-290">This is described further in [Compound assignment](expressions.md#compound-assignment).</span></span> <span data-ttu-id="b0787-291">Notez que l’opérateur d’assignation lui-même (`=`) ne peut pas être surchargé.</span><span class="sxs-lookup"><span data-stu-id="b0787-291">Note that the assignment operator itself (`=`) cannot be overloaded.</span></span> <span data-ttu-id="b0787-292">Une assignation effectue toujours une simple copie bit-wise d’une valeur dans une variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-292">An assignment always performs a simple bit-wise copy of a value into a variable.</span></span>

<span data-ttu-id="b0787-293">Les opérations de cast, telles que `(T)x`, sont surchargées en fournissant des conversions définies par l’utilisateur ([conversions définies par l’utilisateur](conversions.md#user-defined-conversions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-293">Cast operations, such as `(T)x`, are overloaded by providing user-defined conversions ([User-defined conversions](conversions.md#user-defined-conversions)).</span></span>

<span data-ttu-id="b0787-294">L’accès aux éléments, tel que `a[x]`, n’est pas considéré comme un opérateur surchargeable.</span><span class="sxs-lookup"><span data-stu-id="b0787-294">Element access, such as `a[x]`, is not considered an overloadable operator.</span></span> <span data-ttu-id="b0787-295">À la place, l’indexation définie par l’utilisateur est prise en charge par le biais d’indexeurs ([indexeurs](classes.md#indexers)).</span><span class="sxs-lookup"><span data-stu-id="b0787-295">Instead, user-defined indexing is supported through indexers ([Indexers](classes.md#indexers)).</span></span>

<span data-ttu-id="b0787-296">Dans les expressions, les opérateurs sont référencés à l’aide de la notation opérateur, et dans les déclarations, les opérateurs sont référencés à l’aide de la notation fonctionnelle.</span><span class="sxs-lookup"><span data-stu-id="b0787-296">In expressions, operators are referenced using operator notation, and in declarations, operators are referenced using functional notation.</span></span> <span data-ttu-id="b0787-297">Le tableau suivant montre la relation entre les notations opérateur et opérateur pour les opérateurs unaires et binaires.</span><span class="sxs-lookup"><span data-stu-id="b0787-297">The following table shows the relationship between operator and functional notations for unary and binary operators.</span></span> <span data-ttu-id="b0787-298">Dans la première entrée, *op* dénote n’importe quel opérateur de préfixe unaire surchargeable.</span><span class="sxs-lookup"><span data-stu-id="b0787-298">In the first entry, *op* denotes any overloadable unary prefix operator.</span></span> <span data-ttu-id="b0787-299">Dans la deuxième entrée, *op* dénote le suffixe unaire `++` et `--` opérateurs.</span><span class="sxs-lookup"><span data-stu-id="b0787-299">In the second entry, *op* denotes the unary postfix `++` and `--` operators.</span></span> <span data-ttu-id="b0787-300">Dans la troisième entrée, *op* dénote tout opérateur binaire surchargeable.</span><span class="sxs-lookup"><span data-stu-id="b0787-300">In the third entry, *op* denotes any overloadable binary operator.</span></span>


| <span data-ttu-id="b0787-301">__Notation d’opérateur__</span><span class="sxs-lookup"><span data-stu-id="b0787-301">__Operator notation__</span></span> | <span data-ttu-id="b0787-302">__Notation fonctionnelle__</span><span class="sxs-lookup"><span data-stu-id="b0787-302">__Functional notation__</span></span> |
|-----------------------|-------------------------|
| `op x`                | `operator op(x)`        | 
| `x op`                | `operator op(x)`        | 
| `x op y`              | `operator op(x,y)`      | 

<span data-ttu-id="b0787-303">Les déclarations d’opérateur définies par l’utilisateur requièrent toujours qu’au moins un des paramètres soit du type de classe ou de struct qui contient la déclaration d’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-303">User-defined operator declarations always require at least one of the parameters to be of the class or struct type that contains the operator declaration.</span></span> <span data-ttu-id="b0787-304">Par conséquent, il n’est pas possible pour un opérateur défini par l’utilisateur d’avoir la même signature qu’un opérateur prédéfini.</span><span class="sxs-lookup"><span data-stu-id="b0787-304">Thus, it is not possible for a user-defined operator to have the same signature as a predefined operator.</span></span>

<span data-ttu-id="b0787-305">Les déclarations d’opérateur définies par l’utilisateur ne peuvent pas modifier la syntaxe, la priorité ou l’associativité d’un opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-305">User-defined operator declarations cannot modify the syntax, precedence, or associativity of an operator.</span></span> <span data-ttu-id="b0787-306">Par exemple, l’opérateur `/` est toujours un opérateur binaire, possède toujours le niveau de précédence spécifié dans [priorité et associativité des opérateurs](expressions.md#operator-precedence-and-associativity), et est toujours associatif à gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-306">For example, the `/` operator is always a binary operator, always has the precedence level specified in [Operator precedence and associativity](expressions.md#operator-precedence-and-associativity), and is always left-associative.</span></span>

<span data-ttu-id="b0787-307">Bien qu’il soit possible pour un opérateur défini par l’utilisateur d’effectuer n’importe quel calcul, les implémentations qui produisent des résultats autres que ceux qui sont intuitivement attendus sont fortement déconseillées.</span><span class="sxs-lookup"><span data-stu-id="b0787-307">While it is possible for a user-defined operator to perform any computation it pleases, implementations that produce results other than those that are intuitively expected are strongly discouraged.</span></span> <span data-ttu-id="b0787-308">Par exemple, une implémentation de `operator ==` doit comparer les deux opérandes pour déterminer leur égalité et retourner un résultat de `bool` approprié.</span><span class="sxs-lookup"><span data-stu-id="b0787-308">For example, an implementation of `operator ==` should compare the two operands for equality and return an appropriate `bool` result.</span></span>

<span data-ttu-id="b0787-309">Les descriptions des opérateurs individuels dans les [expressions primaires](expressions.md#primary-expressions) par le biais d' [opérateurs logiques conditionnels](expressions.md#conditional-logical-operators) spécifient les implémentations prédéfinies des opérateurs et toutes les règles supplémentaires qui s’appliquent à chaque opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-309">The descriptions of individual operators in [Primary expressions](expressions.md#primary-expressions) through [Conditional logical operators](expressions.md#conditional-logical-operators) specify the predefined implementations of the operators and any additional rules that apply to each operator.</span></span> <span data-ttu-id="b0787-310">Les descriptions utilisent les termes résolution de ***surcharge d’opérateur unaire***, ***résolution de surcharge d’opérateur binaire***et ***promotion numérique***, dont les définitions se trouvent dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="b0787-310">The descriptions make use of the terms ***unary operator overload resolution***, ***binary operator overload resolution***, and ***numeric promotion***, definitions of which are found in the following sections.</span></span>

### <a name="unary-operator-overload-resolution"></a><span data-ttu-id="b0787-311">Résolution de surcharge d’opérateur unaire</span><span class="sxs-lookup"><span data-stu-id="b0787-311">Unary operator overload resolution</span></span>

<span data-ttu-id="b0787-312">Opération de la forme `op x` ou `x op`, où `op` est un opérateur unaire surchargeable, et `x` est une expression de type `X`, est traité comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-312">An operation of the form `op x` or `x op`, where `op` is an overloadable unary operator, and `x` is an expression of type `X`, is processed as follows:</span></span>

*  <span data-ttu-id="b0787-313">Le jeu d’opérateurs candidats définis par l’utilisateur fourni par `X` pour l’opération `operator op(x)` est déterminé à l’aide des règles des [opérateurs candidats définis](expressions.md#candidate-user-defined-operators)par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-313">The set of candidate user-defined operators provided by `X` for the operation `operator op(x)` is determined using the rules of [Candidate user-defined operators](expressions.md#candidate-user-defined-operators).</span></span>
*  <span data-ttu-id="b0787-314">Si le jeu d’opérateurs candidats définis par l’utilisateur n’est pas vide, il devient alors l’ensemble des opérateurs candidats pour l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-314">If the set of candidate user-defined operators is not empty, then this becomes the set of candidate operators for the operation.</span></span> <span data-ttu-id="b0787-315">Sinon, les implémentations unaires prédéfinies de `operator op`, y compris leurs formulaires levés, deviennent l’ensemble des opérateurs candidats pour l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-315">Otherwise, the predefined unary `operator op` implementations, including their lifted forms, become the set of candidate operators for the operation.</span></span> <span data-ttu-id="b0787-316">Les implémentations prédéfinies d’un opérateur donné sont spécifiées dans la description de l’opérateur ([expressions primaires](expressions.md#primary-expressions) et [opérateurs unaires](expressions.md#unary-operators)).</span><span class="sxs-lookup"><span data-stu-id="b0787-316">The predefined implementations of a given operator are specified in the description of the operator ([Primary expressions](expressions.md#primary-expressions) and [Unary operators](expressions.md#unary-operators)).</span></span>
*  <span data-ttu-id="b0787-317">Les règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution) sont appliquées au jeu d’opérateurs candidats pour sélectionner le meilleur opérateur par rapport à la liste d’arguments `(x)`, et cet opérateur devient le résultat du processus de résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="b0787-317">The overload resolution rules of [Overload resolution](expressions.md#overload-resolution) are applied to the set of candidate operators to select the best operator with respect to the argument list `(x)`, and this operator becomes the result of the overload resolution process.</span></span> <span data-ttu-id="b0787-318">Si la résolution de surcharge ne peut pas sélectionner un seul opérateur Best, une erreur de liaison est générée.</span><span class="sxs-lookup"><span data-stu-id="b0787-318">If overload resolution fails to select a single best operator, a binding-time error occurs.</span></span>

### <a name="binary-operator-overload-resolution"></a><span data-ttu-id="b0787-319">Résolution de surcharge d’opérateur binaire</span><span class="sxs-lookup"><span data-stu-id="b0787-319">Binary operator overload resolution</span></span>

<span data-ttu-id="b0787-320">Une opération de la forme `x op y`, où `op` est un opérateur binaire surchargeable, `x` est une expression de type `X`, et `y` est une expression de type `Y`, est traité comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-320">An operation of the form `x op y`, where `op` is an overloadable binary operator, `x` is an expression of type `X`, and `y` is an expression of type `Y`, is processed as follows:</span></span>

*  <span data-ttu-id="b0787-321">Le jeu d’opérateurs candidats définis par l’utilisateur fourni par `X` et `Y` pour l’opération `operator op(x,y)` est déterminé.</span><span class="sxs-lookup"><span data-stu-id="b0787-321">The set of candidate user-defined operators provided by `X` and `Y` for the operation `operator op(x,y)` is determined.</span></span> <span data-ttu-id="b0787-322">L’ensemble se compose de l’Union des opérateurs candidats fournis par `X` et des opérateurs candidats fournis par `Y`, chacun déterminé à l’aide des règles des [opérateurs candidats définis](expressions.md#candidate-user-defined-operators)par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-322">The set consists of the union of the candidate operators provided by `X` and the candidate operators provided by `Y`, each determined using the rules of [Candidate user-defined operators](expressions.md#candidate-user-defined-operators).</span></span> <span data-ttu-id="b0787-323">Si `X` et `Y` sont du même type, ou si `X` et `Y` sont dérivés d’un type de base commun, les opérateurs candidats partagés se produisent uniquement dans le jeu combiné une fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-323">If `X` and `Y` are the same type, or if `X` and `Y` are derived from a common base type, then shared candidate operators only occur in the combined set once.</span></span>
*  <span data-ttu-id="b0787-324">Si le jeu d’opérateurs candidats définis par l’utilisateur n’est pas vide, il devient alors l’ensemble des opérateurs candidats pour l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-324">If the set of candidate user-defined operators is not empty, then this becomes the set of candidate operators for the operation.</span></span> <span data-ttu-id="b0787-325">Dans le cas contraire, les implémentations binaires prédéfinies de `operator op`, y compris leurs formulaires levés, deviennent l’ensemble des opérateurs candidats pour l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-325">Otherwise, the predefined binary `operator op` implementations, including their lifted forms,  become the set of candidate operators for the operation.</span></span> <span data-ttu-id="b0787-326">Les implémentations prédéfinies d’un opérateur donné sont spécifiées dans la description de l’opérateur ([opérateurs arithmétiques](expressions.md#arithmetic-operators) à l’aide d' [opérateurs logiques conditionnels](expressions.md#conditional-logical-operators)).</span><span class="sxs-lookup"><span data-stu-id="b0787-326">The predefined implementations of a given operator are specified in the description of the operator ([Arithmetic operators](expressions.md#arithmetic-operators) through [Conditional logical operators](expressions.md#conditional-logical-operators)).</span></span> <span data-ttu-id="b0787-327">Pour les opérateurs enum et Delegate prédéfinis, les seuls opérateurs pris en compte sont ceux définis par un type enum ou Delegate qui est le type de temps de liaison de l’un des opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-327">For predefined enum and delegate operators, the only operators considered are those defined by an enum or delegate type that is the binding-time type of one of the operands.</span></span>
*  <span data-ttu-id="b0787-328">Les règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution) sont appliquées au jeu d’opérateurs candidats pour sélectionner le meilleur opérateur par rapport à la liste d’arguments `(x,y)`, et cet opérateur devient le résultat du processus de résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="b0787-328">The overload resolution rules of [Overload resolution](expressions.md#overload-resolution) are applied to the set of candidate operators to select the best operator with respect to the argument list `(x,y)`, and this operator becomes the result of the overload resolution process.</span></span> <span data-ttu-id="b0787-329">Si la résolution de surcharge ne peut pas sélectionner un seul opérateur Best, une erreur de liaison est générée.</span><span class="sxs-lookup"><span data-stu-id="b0787-329">If overload resolution fails to select a single best operator, a binding-time error occurs.</span></span>

### <a name="candidate-user-defined-operators"></a><span data-ttu-id="b0787-330">Opérateurs candidats définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="b0787-330">Candidate user-defined operators</span></span>

<span data-ttu-id="b0787-331">Étant donné un type `T` et une opération `operator op(A)`, où `op` est un opérateur surchargeable et `A` est une liste d’arguments, l’ensemble des opérateurs candidats définis par l’utilisateur fournis par `T` pour `operator op(A)` est déterminé comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-331">Given a type `T` and an operation `operator op(A)`, where `op` is an overloadable operator and `A` is an argument list, the set of candidate user-defined operators provided by `T` for `operator op(A)` is determined as follows:</span></span>

*  <span data-ttu-id="b0787-332">Déterminez le type `T0`.</span><span class="sxs-lookup"><span data-stu-id="b0787-332">Determine the type `T0`.</span></span> <span data-ttu-id="b0787-333">Si `T` est un type Nullable, `T0` est son type sous-jacent, sinon `T0` est égal à `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-333">If `T` is a nullable type, `T0` is its underlying type, otherwise `T0` is equal to `T`.</span></span>
*  <span data-ttu-id="b0787-334">Pour toutes les déclarations de `operator op` dans `T0` et toutes les formes levées de tels opérateurs, si au moins un opérateur est applicable ([membre de fonction applicable](expressions.md#applicable-function-member)) par rapport à la liste d’arguments `A`, alors l’ensemble des opérateurs candidats se compose de tous les opérateurs applicables dans `T0`.</span><span class="sxs-lookup"><span data-stu-id="b0787-334">For all `operator op` declarations in `T0` and all lifted forms of such operators, if at least one operator is applicable ([Applicable function member](expressions.md#applicable-function-member)) with respect to the argument list `A`, then the set of candidate operators consists of all such applicable operators in `T0`.</span></span>
*  <span data-ttu-id="b0787-335">Sinon, si `T0` est `object`, l’ensemble des opérateurs candidats est vide.</span><span class="sxs-lookup"><span data-stu-id="b0787-335">Otherwise, if `T0` is `object`, the set of candidate operators is empty.</span></span>
*  <span data-ttu-id="b0787-336">Sinon, le jeu d’opérateurs candidats fourni par `T0` est l’ensemble des opérateurs candidats fournis par la classe de base directe de `T0`ou la classe de base effective de `T0` si `T0` est un paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="b0787-336">Otherwise, the set of candidate operators provided by `T0` is the set of candidate operators provided by the direct base class of `T0`, or the effective base class of `T0` if `T0` is a type parameter.</span></span>

### <a name="numeric-promotions"></a><span data-ttu-id="b0787-337">Promotions numériques</span><span class="sxs-lookup"><span data-stu-id="b0787-337">Numeric promotions</span></span>

<span data-ttu-id="b0787-338">La promotion numérique consiste à effectuer automatiquement certaines conversions implicites des opérandes des opérateurs numériques unaires et binaires prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="b0787-338">Numeric promotion consists of automatically performing certain implicit conversions of the operands of the predefined unary and binary numeric operators.</span></span> <span data-ttu-id="b0787-339">La promotion numérique n’est pas un mécanisme distinct, mais plutôt un effet de l’application de la résolution de surcharge aux opérateurs prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="b0787-339">Numeric promotion is not a distinct mechanism, but rather an effect of applying overload resolution to the predefined operators.</span></span> <span data-ttu-id="b0787-340">La promotion numérique n’affecte pas spécifiquement l’évaluation des opérateurs définis par l’utilisateur, bien que les opérateurs définis par l’utilisateur puissent être implémentés pour présenter des effets similaires.</span><span class="sxs-lookup"><span data-stu-id="b0787-340">Numeric promotion specifically does not affect evaluation of user-defined operators, although user-defined operators can be implemented to exhibit similar effects.</span></span>

<span data-ttu-id="b0787-341">À titre d’exemple de promotion numérique, prenez en compte les implémentations prédéfinies de l’opérateur de `*` binaire :</span><span class="sxs-lookup"><span data-stu-id="b0787-341">As an example of numeric promotion, consider the predefined implementations of the binary `*` operator:</span></span>

```csharp
int operator *(int x, int y);
uint operator *(uint x, uint y);
long operator *(long x, long y);
ulong operator *(ulong x, ulong y);
float operator *(float x, float y);
double operator *(double x, double y);
decimal operator *(decimal x, decimal y);
```

<span data-ttu-id="b0787-342">Lorsque des règles de résolution de surcharge ([résolution de surcharge](expressions.md#overload-resolution)) sont appliquées à cet ensemble d’opérateurs, l’effet est de sélectionner le premier des opérateurs pour lesquels des conversions implicites existent à partir des types d’opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-342">When overload resolution rules ([Overload resolution](expressions.md#overload-resolution)) are applied to this set of operators, the effect is to select the first of the operators for which implicit conversions exist from the operand types.</span></span> <span data-ttu-id="b0787-343">Par exemple, pour l’opération `b * s`, où `b` est un `byte` et `s` est un `short`, la résolution de surcharge sélectionne `operator *(int,int)` comme meilleur opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-343">For example, for the operation `b * s`, where `b` is a `byte` and `s` is a `short`, overload resolution selects `operator *(int,int)` as the best operator.</span></span> <span data-ttu-id="b0787-344">Par conséquent, l’effet est que `b` et `s` sont convertis en `int`et que le type du résultat est `int`.</span><span class="sxs-lookup"><span data-stu-id="b0787-344">Thus, the effect is that `b` and `s` are converted to `int`, and the type of the result is `int`.</span></span> <span data-ttu-id="b0787-345">De même, pour l’opération `i * d`, où `i` est un `int` et `d` est un `double`, la résolution de surcharge sélectionne `operator *(double,double)` comme meilleur opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-345">Likewise, for the operation `i * d`, where `i` is an `int` and `d` is a `double`, overload resolution selects `operator *(double,double)` as the best operator.</span></span>

#### <a name="unary-numeric-promotions"></a><span data-ttu-id="b0787-346">Promotions numériques unaires</span><span class="sxs-lookup"><span data-stu-id="b0787-346">Unary numeric promotions</span></span>

<span data-ttu-id="b0787-347">La promotion numérique unaire se produit pour les opérandes des opérateurs unaires `+`, `-`et `~` prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="b0787-347">Unary numeric promotion occurs for the operands of the predefined `+`, `-`, and `~` unary operators.</span></span> <span data-ttu-id="b0787-348">La promotion numérique unaire consiste simplement à convertir les opérandes de type `sbyte`, `byte`, `short`, `ushort`ou `char` en type `int`.</span><span class="sxs-lookup"><span data-stu-id="b0787-348">Unary numeric promotion simply consists of converting operands of type `sbyte`, `byte`, `short`, `ushort`, or `char` to type `int`.</span></span> <span data-ttu-id="b0787-349">En outre, pour l’opérateur unaire `-`, la promotion numérique unaire convertit les opérandes de type `uint` en type `long`.</span><span class="sxs-lookup"><span data-stu-id="b0787-349">Additionally, for the unary `-` operator, unary numeric promotion converts operands of type `uint` to type `long`.</span></span>

#### <a name="binary-numeric-promotions"></a><span data-ttu-id="b0787-350">Promotions numériques binaires</span><span class="sxs-lookup"><span data-stu-id="b0787-350">Binary numeric promotions</span></span>

<span data-ttu-id="b0787-351">La promotion numérique binaire se produit pour les opérandes des opérateurs binaires prédéfinis `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `==`, `!=`, `>`, `<`, `>=`et `<=`.</span><span class="sxs-lookup"><span data-stu-id="b0787-351">Binary numeric promotion occurs for the operands of the predefined `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `==`, `!=`, `>`, `<`, `>=`, and `<=` binary operators.</span></span> <span data-ttu-id="b0787-352">La promotion numérique binaire convertit implicitement les deux opérandes en un type commun qui, dans le cas des opérateurs non relationnels, devient également le type de résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-352">Binary numeric promotion implicitly converts both operands to a common type which, in case of the non-relational operators, also becomes the result type of the operation.</span></span> <span data-ttu-id="b0787-353">La promotion numérique binaire consiste à appliquer les règles suivantes, dans l’ordre où elles apparaissent ici :</span><span class="sxs-lookup"><span data-stu-id="b0787-353">Binary numeric promotion consists of applying the following rules, in the order they appear here:</span></span>

*  <span data-ttu-id="b0787-354">Si l’un des opérandes est de type `decimal`, l’autre opérande est converti en type `decimal`, ou une erreur de liaison se produit si l’autre opérande est de type `float` ou `double`.</span><span class="sxs-lookup"><span data-stu-id="b0787-354">If either operand is of type `decimal`, the other operand is converted to type `decimal`, or a binding-time error occurs if the other operand is of type `float` or `double`.</span></span>
*  <span data-ttu-id="b0787-355">Sinon, si l’un des opérandes est de type `double`, l’autre opérande est converti en type `double`.</span><span class="sxs-lookup"><span data-stu-id="b0787-355">Otherwise, if either operand is of type `double`, the other operand is converted to type `double`.</span></span>
*  <span data-ttu-id="b0787-356">Sinon, si l’un des opérandes est de type `float`, l’autre opérande est converti en type `float`.</span><span class="sxs-lookup"><span data-stu-id="b0787-356">Otherwise, if either operand is of type `float`, the other operand is converted to type `float`.</span></span>
*  <span data-ttu-id="b0787-357">Sinon, si l’un des opérandes est de type `ulong`, l’autre opérande est converti en type `ulong`, ou une erreur de liaison se produit si l’autre opérande est de type `sbyte`, `short`, `int`ou `long`.</span><span class="sxs-lookup"><span data-stu-id="b0787-357">Otherwise, if either operand is of type `ulong`, the other operand is converted to type `ulong`, or a binding-time error occurs if the other operand is of type `sbyte`, `short`, `int`, or `long`.</span></span>
*  <span data-ttu-id="b0787-358">Sinon, si l’un des opérandes est de type `long`, l’autre opérande est converti en type `long`.</span><span class="sxs-lookup"><span data-stu-id="b0787-358">Otherwise, if either operand is of type `long`, the other operand is converted to type `long`.</span></span>
*  <span data-ttu-id="b0787-359">Sinon, si l’un des opérandes est de type `uint` et que l’autre opérande est de type `sbyte`, `short`ou `int`, les deux opérandes sont convertis en type `long`.</span><span class="sxs-lookup"><span data-stu-id="b0787-359">Otherwise, if either operand is of type `uint` and the other operand is of type `sbyte`, `short`, or `int`, both operands are converted to type `long`.</span></span>
*  <span data-ttu-id="b0787-360">Sinon, si l’un des opérandes est de type `uint`, l’autre opérande est converti en type `uint`.</span><span class="sxs-lookup"><span data-stu-id="b0787-360">Otherwise, if either operand is of type `uint`, the other operand is converted to type `uint`.</span></span>
*  <span data-ttu-id="b0787-361">Sinon, les deux opérandes sont convertis en type `int`.</span><span class="sxs-lookup"><span data-stu-id="b0787-361">Otherwise, both operands are converted to type `int`.</span></span>

<span data-ttu-id="b0787-362">Notez que la première règle interdit toutes les opérations qui mélangent le type de `decimal` avec les types de `double` et de `float`.</span><span class="sxs-lookup"><span data-stu-id="b0787-362">Note that the first rule disallows any operations that mix the `decimal` type with the `double` and `float` types.</span></span> <span data-ttu-id="b0787-363">La règle suit du fait qu’il n’existe pas de conversion implicite entre le type de `decimal` et les types de `double` et de `float`.</span><span class="sxs-lookup"><span data-stu-id="b0787-363">The rule follows from the fact that there are no implicit conversions between the `decimal` type and the `double` and `float` types.</span></span>

<span data-ttu-id="b0787-364">Notez également qu’il n’est pas possible qu’un opérande soit de type `ulong` lorsque l’autre opérande est d’un type intégral signé.</span><span class="sxs-lookup"><span data-stu-id="b0787-364">Also note that it is not possible for an operand to be of type `ulong` when the other operand is of a signed integral type.</span></span> <span data-ttu-id="b0787-365">La raison est qu’il n’existe aucun type intégral qui peut représenter la plage complète de `ulong` ainsi que les types intégraux signés.</span><span class="sxs-lookup"><span data-stu-id="b0787-365">The reason is that no integral type exists that can represent the full range of `ulong` as well as the signed integral types.</span></span>

<span data-ttu-id="b0787-366">Dans les deux cas ci-dessus, une expression de cast peut être utilisée pour convertir explicitement un opérande en un type compatible avec l’autre opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-366">In both of the above cases, a cast expression can be used to explicitly convert one operand to a type that is compatible with the other operand.</span></span>

<span data-ttu-id="b0787-367">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-367">In the example</span></span>
```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (1.0 + percent / 100.0);
}
```
<span data-ttu-id="b0787-368">une erreur de liaison s’est produite, car une `decimal` ne peut pas être multipliée par un `double`.</span><span class="sxs-lookup"><span data-stu-id="b0787-368">a binding-time error occurs because a `decimal` cannot be multiplied by a `double`.</span></span> <span data-ttu-id="b0787-369">L’erreur est résolue par la conversion explicite du deuxième opérande en `decimal`, comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-369">The error is resolved by explicitly converting the second operand to `decimal`, as follows:</span></span>

```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (decimal)(1.0 + percent / 100.0);
}
```

### <a name="lifted-operators"></a><span data-ttu-id="b0787-370">Opérateurs levés</span><span class="sxs-lookup"><span data-stu-id="b0787-370">Lifted operators</span></span>

<span data-ttu-id="b0787-371">Les ***opérateurs levés*** permettent aux opérateurs prédéfinis et définis par l’utilisateur qui fonctionnent sur des types valeur non Nullable d’être également utilisés avec les formulaires Nullable de ces types.</span><span class="sxs-lookup"><span data-stu-id="b0787-371">***Lifted operators*** permit predefined and user-defined operators that operate on non-nullable value types to also be used with nullable forms of those types.</span></span> <span data-ttu-id="b0787-372">Les opérateurs levés sont construits à partir d’opérateurs prédéfinis et définis par l’utilisateur qui répondent à certaines exigences, comme décrit dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-372">Lifted operators are constructed from predefined and user-defined operators that meet certain requirements, as described in the following:</span></span>

*   <span data-ttu-id="b0787-373">Pour les opérateurs unaires</span><span class="sxs-lookup"><span data-stu-id="b0787-373">For the unary operators</span></span>

    ```csharp
    +  ++  -  --  !  ~
    ```

    <span data-ttu-id="b0787-374">une forme levée d’un opérateur existe si l’opérande et les types de résultats sont des types valeur non Nullable.</span><span class="sxs-lookup"><span data-stu-id="b0787-374">a lifted form of an operator exists if the operand and result types are both non-nullable value types.</span></span> <span data-ttu-id="b0787-375">Le formulaire levé est construit en ajoutant un modificateur de `?` unique à l’opérande et aux types de résultats.</span><span class="sxs-lookup"><span data-stu-id="b0787-375">The lifted form is constructed by adding a single `?` modifier to the operand and result types.</span></span> <span data-ttu-id="b0787-376">L’opérateur levé produit une valeur null si l’opérande a la valeur null.</span><span class="sxs-lookup"><span data-stu-id="b0787-376">The lifted operator produces a null value if the operand is null.</span></span> <span data-ttu-id="b0787-377">Dans le cas contraire, l’opérateur Levé désencapsule l’opérande, applique l’opérateur sous-jacent et encapsule le résultat.</span><span class="sxs-lookup"><span data-stu-id="b0787-377">Otherwise, the lifted operator unwraps the operand, applies the underlying operator, and wraps the result.</span></span>

*   <span data-ttu-id="b0787-378">Pour les opérateurs binaires</span><span class="sxs-lookup"><span data-stu-id="b0787-378">For the binary operators</span></span>

    ```csharp
    +  -  *  /  %  &  |  ^  <<  >>
    ```

    <span data-ttu-id="b0787-379">une forme levée d’un opérateur existe si l’opérande et les types de résultats sont tous des types valeur non Nullable.</span><span class="sxs-lookup"><span data-stu-id="b0787-379">a lifted form of an operator exists if the operand and result types are all non-nullable value types.</span></span> <span data-ttu-id="b0787-380">Le formulaire levé est construit en ajoutant un modificateur de `?` unique à chaque opérande et chaque type de résultat.</span><span class="sxs-lookup"><span data-stu-id="b0787-380">The lifted form is constructed by adding a single `?` modifier to each operand and result type.</span></span> <span data-ttu-id="b0787-381">L’opérateur levé produit une valeur null si l’un des opérandes ou les deux sont null (une exception étant le `&` et `|` opérateurs du type de `bool?`, comme décrit dans [opérateurs logiques booléens](expressions.md#boolean-logical-operators)).</span><span class="sxs-lookup"><span data-stu-id="b0787-381">The lifted operator produces a null value if one or both operands are null (an exception being the `&` and `|` operators of the `bool?` type, as described in [Boolean logical operators](expressions.md#boolean-logical-operators)).</span></span> <span data-ttu-id="b0787-382">Dans le cas contraire, l’opérateur Levé désencapsule les opérandes, applique l’opérateur sous-jacent et encapsule le résultat.</span><span class="sxs-lookup"><span data-stu-id="b0787-382">Otherwise, the lifted operator unwraps the operands, applies the underlying operator, and wraps the result.</span></span>

*   <span data-ttu-id="b0787-383">Pour les opérateurs d’égalité</span><span class="sxs-lookup"><span data-stu-id="b0787-383">For the equality operators</span></span>

    ```csharp
    ==  !=
    ```

    <span data-ttu-id="b0787-384">une forme levée d’un opérateur existe si les types d’opérandes sont des types valeur non Nullable et si le type de résultat est `bool`.</span><span class="sxs-lookup"><span data-stu-id="b0787-384">a lifted form of an operator exists if the operand types are both non-nullable value types and if the result type is `bool`.</span></span> <span data-ttu-id="b0787-385">Le formulaire levé est construit en ajoutant un modificateur de `?` unique à chaque type d’opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-385">The lifted form is constructed by adding a single `?` modifier to each operand type.</span></span> <span data-ttu-id="b0787-386">L’opérateur levé considère deux valeurs NULL comme étant égales et une valeur NULL n’est pas égale à une valeur non null.</span><span class="sxs-lookup"><span data-stu-id="b0787-386">The lifted operator considers two null values equal, and a null value unequal to any non-null value.</span></span> <span data-ttu-id="b0787-387">Si les deux opérandes n’ont pas la valeur null, l’opérateur Levé désencapsule les opérandes et applique l’opérateur sous-jacent pour produire le résultat `bool`.</span><span class="sxs-lookup"><span data-stu-id="b0787-387">If both operands are non-null, the lifted operator unwraps the operands and applies the underlying operator to produce the `bool` result.</span></span>

*   <span data-ttu-id="b0787-388">Pour les opérateurs relationnels</span><span class="sxs-lookup"><span data-stu-id="b0787-388">For the relational operators</span></span>

    ```csharp
    <  >  <=  >=
    ```

    <span data-ttu-id="b0787-389">une forme levée d’un opérateur existe si les types d’opérandes sont des types valeur non Nullable et si le type de résultat est `bool`.</span><span class="sxs-lookup"><span data-stu-id="b0787-389">a lifted form of an operator exists if the operand types are both non-nullable value types and if the result type is `bool`.</span></span> <span data-ttu-id="b0787-390">Le formulaire levé est construit en ajoutant un modificateur de `?` unique à chaque type d’opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-390">The lifted form is constructed by adding a single `?` modifier to each operand type.</span></span> <span data-ttu-id="b0787-391">L’opérateur levé produit la valeur `false` si l’un ou les deux opérandes ont la valeur null.</span><span class="sxs-lookup"><span data-stu-id="b0787-391">The lifted operator produces the value `false` if one or both operands are null.</span></span> <span data-ttu-id="b0787-392">Dans le cas contraire, l’opérateur Levé désencapsule les opérandes et applique l’opérateur sous-jacent pour produire le résultat `bool`.</span><span class="sxs-lookup"><span data-stu-id="b0787-392">Otherwise, the lifted operator unwraps the operands and applies the underlying operator to produce the `bool` result.</span></span>

## <a name="member-lookup"></a><span data-ttu-id="b0787-393">Recherche de membres</span><span class="sxs-lookup"><span data-stu-id="b0787-393">Member lookup</span></span>

<span data-ttu-id="b0787-394">Une recherche de membre est le processus par lequel la signification d’un nom dans le contexte d’un type est déterminée.</span><span class="sxs-lookup"><span data-stu-id="b0787-394">A member lookup is the process whereby the meaning of a name in the context of a type is determined.</span></span> <span data-ttu-id="b0787-395">Une recherche de membre peut se produire dans le cadre de l’évaluation d’un *simple_name* ([noms simples](expressions.md#simple-names)) ou d’un *member_access* ([accès aux membres](expressions.md#member-access)) dans une expression.</span><span class="sxs-lookup"><span data-stu-id="b0787-395">A member lookup can occur as part of evaluating a *simple_name* ([Simple names](expressions.md#simple-names)) or a *member_access* ([Member access](expressions.md#member-access)) in an expression.</span></span> <span data-ttu-id="b0787-396">Si le *simple_name* ou *member_access* se produit comme *primary_expression* d’un *invocation_expression* ([appels de méthode](expressions.md#method-invocations)), le membre est dit appelé.</span><span class="sxs-lookup"><span data-stu-id="b0787-396">If the *simple_name* or *member_access* occurs as the *primary_expression* of an *invocation_expression* ([Method invocations](expressions.md#method-invocations)), the member is said to be invoked.</span></span>

<span data-ttu-id="b0787-397">Si un membre est une méthode ou un événement, ou s’il s’agit d’une constante, d’un champ ou d’une propriété d’un type délégué ([délégués](delegates.md)) ou du type `dynamic` ([type dynamique](types.md#the-dynamic-type)), le membre est dit être *appelé.*</span><span class="sxs-lookup"><span data-stu-id="b0787-397">If a member is a method or event, or if it is a constant, field or property of either a delegate type ([Delegates](delegates.md)) or the type `dynamic` ([The dynamic type](types.md#the-dynamic-type)), then the member is said to be *invocable*.</span></span>

<span data-ttu-id="b0787-398">La recherche de membres prend en compte non seulement le nom d’un membre, mais également le nombre de paramètres de type que le membre possède et si le membre est accessible.</span><span class="sxs-lookup"><span data-stu-id="b0787-398">Member lookup considers not only the name of a member but also the number of type parameters the member has and whether the member is accessible.</span></span> <span data-ttu-id="b0787-399">Dans le cadre de la recherche de membres, les méthodes génériques et les types génériques imbriqués ont le nombre de paramètres de type indiqué dans leurs déclarations respectives et tous les autres membres ont des paramètres de type zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-399">For the purposes of member lookup, generic methods and nested generic types have the number of type parameters indicated in their respective declarations and all other members have zero type parameters.</span></span>

<span data-ttu-id="b0787-400">Une recherche de membre d’un nom `N` avec `K`paramètres de type  dans un type `T` est traitée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-400">A member lookup of a name `N` with `K` type parameters in a type `T` is processed as follows:</span></span>

*  <span data-ttu-id="b0787-401">Tout d’abord, un ensemble de membres accessibles nommés `N` est déterminé :</span><span class="sxs-lookup"><span data-stu-id="b0787-401">First, a set of accessible members named `N` is determined:</span></span>
    * <span data-ttu-id="b0787-402">Si `T` est un paramètre de type, le jeu est l’Union des ensembles de membres accessibles nommés `N` dans chacun des types spécifiés en tant que contrainte principale ou contrainte secondaire ([contraintes de paramètre de type](classes.md#type-parameter-constraints)) pour `T`, ainsi que l’ensemble des membres accessibles nommés `N` dans `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-402">If `T` is a type parameter, then the set is the union of the sets of accessible members named `N` in each of the types specified as a primary constraint or secondary constraint ([Type parameter constraints](classes.md#type-parameter-constraints)) for `T`, along with the set of accessible members named `N` in `object`.</span></span>
    * <span data-ttu-id="b0787-403">Dans le cas contraire, l’ensemble se compose de tous les membres accessibles ([accès aux membres](basic-concepts.md#member-access)) nommés `N` dans `T`, y compris les membres hérités et les membres accessibles nommés `N` dans `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-403">Otherwise, the set consists of all accessible ([Member access](basic-concepts.md#member-access)) members named `N` in `T`, including inherited members and the accessible members named `N` in `object`.</span></span> <span data-ttu-id="b0787-404">Si `T` est un type construit, l’ensemble des membres est obtenu en remplaçant les arguments de type comme décrit dans [membres des types construits](classes.md#members-of-constructed-types).</span><span class="sxs-lookup"><span data-stu-id="b0787-404">If `T` is a constructed type, the set of members is obtained by substituting type arguments as described in [Members of constructed types](classes.md#members-of-constructed-types).</span></span> <span data-ttu-id="b0787-405">Les membres qui incluent un modificateur `override` sont exclus de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="b0787-405">Members that include an `override` modifier are excluded from the set.</span></span>
*  <span data-ttu-id="b0787-406">Ensuite, si `K` est égal à zéro, tous les types imbriqués dont les déclarations incluent des paramètres de type sont supprimés.</span><span class="sxs-lookup"><span data-stu-id="b0787-406">Next, if `K` is zero, all nested types whose declarations include type parameters are removed.</span></span> <span data-ttu-id="b0787-407">Si `K` n’est pas égal à zéro, tous les membres avec un nombre différent de paramètres de type sont supprimés.</span><span class="sxs-lookup"><span data-stu-id="b0787-407">If `K` is not zero, all members with a different number of type parameters are removed.</span></span> <span data-ttu-id="b0787-408">Notez que lorsque `K` est égal à zéro, les méthodes ayant des paramètres de type ne sont pas supprimées, car le processus d’inférence de type ([inférence de type](expressions.md#type-inference)) peut être en mesure de déduire les arguments de type.</span><span class="sxs-lookup"><span data-stu-id="b0787-408">Note that when `K` is zero, methods having type parameters are not removed, since the type inference process ([Type inference](expressions.md#type-inference)) might be able to infer the type arguments.</span></span>
*  <span data-ttu-id="b0787-409">Ensuite, si le membre est *appelé*, tous les membres qui ne sont pas*invocables* sont supprimés du jeu.</span><span class="sxs-lookup"><span data-stu-id="b0787-409">Next, if the member is *invoked*, all non-*invocable* members are removed from the set.</span></span>
*  <span data-ttu-id="b0787-410">Ensuite, les membres qui sont masqués par d’autres membres sont supprimés du jeu.</span><span class="sxs-lookup"><span data-stu-id="b0787-410">Next, members that are hidden by other members are removed from the set.</span></span> <span data-ttu-id="b0787-411">Pour chaque membre `S.M` dans l’ensemble, où `S` est le type dans lequel le membre `M` est déclaré, les règles suivantes sont appliquées :</span><span class="sxs-lookup"><span data-stu-id="b0787-411">For every member `S.M` in the set, where `S` is the type in which the member `M` is declared, the following rules are applied:</span></span>
    * <span data-ttu-id="b0787-412">Si `M` est une constante, un champ, une propriété, un événement ou un membre de l’énumération, tous les membres déclarés dans un type de base de `S` sont supprimés du jeu.</span><span class="sxs-lookup"><span data-stu-id="b0787-412">If `M` is a constant, field, property, event, or enumeration member, then all members declared in a base type of `S` are removed from the set.</span></span>
    * <span data-ttu-id="b0787-413">Si `M` est une déclaration de type, tous les types non déclarés dans un type de base de `S` sont supprimés du jeu, et toutes les déclarations de type avec le même nombre de paramètres de type que `M` déclarés dans un type de base de `S` sont supprimées de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="b0787-413">If `M` is a type declaration, then all non-types declared in a base type of `S` are removed from the set, and all type declarations with the same number of type parameters as `M` declared in a base type of `S` are removed from the set.</span></span>
    * <span data-ttu-id="b0787-414">Si `M` est une méthode, tous les membres qui ne sont pas des méthodes déclarés dans un type de base de `S` sont supprimés du jeu.</span><span class="sxs-lookup"><span data-stu-id="b0787-414">If `M` is a method, then all non-method members declared in a base type of `S` are removed from the set.</span></span>
*  <span data-ttu-id="b0787-415">Ensuite, les membres d’interface qui sont masqués par les membres de classe sont supprimés du jeu.</span><span class="sxs-lookup"><span data-stu-id="b0787-415">Next, interface members that are hidden by class members are removed from the set.</span></span> <span data-ttu-id="b0787-416">Cette étape n’a d’effet que si `T` est un paramètre de type et `T` a une classe de base effective autre que `object` et un ensemble d’interfaces effectif non vide ([contraintes de paramètre de type](classes.md#type-parameter-constraints)).</span><span class="sxs-lookup"><span data-stu-id="b0787-416">This step only has an effect if `T` is a type parameter and `T` has both an effective base class other than `object` and a non-empty effective interface set ([Type parameter constraints](classes.md#type-parameter-constraints)).</span></span> <span data-ttu-id="b0787-417">Pour chaque membre `S.M` dans l’ensemble, où `S` est le type dans lequel l' `M` membre est déclaré, les règles suivantes sont appliquées si `S` est une déclaration de classe autre que `object`:</span><span class="sxs-lookup"><span data-stu-id="b0787-417">For every member `S.M` in the set, where `S` is the type in which the member `M` is declared, the following rules are applied if `S` is a class declaration other than `object`:</span></span>
    * <span data-ttu-id="b0787-418">Si `M` est un constante, un champ, une propriété, un événement, un membre de l’énumération ou une déclaration de type, tous les membres déclarés dans une déclaration d’interface sont supprimés du jeu.</span><span class="sxs-lookup"><span data-stu-id="b0787-418">If `M` is a constant, field, property, event, enumeration member, or type declaration, then all members declared in an interface declaration are removed from the set.</span></span>
    * <span data-ttu-id="b0787-419">Si `M` est une méthode, tous les membres qui ne sont pas des méthodes déclarés dans une déclaration d’interface sont supprimés du jeu, et toutes les méthodes ayant la même signature que `M` déclarées dans une déclaration d’interface sont supprimées de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="b0787-419">If `M` is a method, then all non-method members declared in an interface declaration are removed from the set, and all methods with the same signature as `M` declared in an interface declaration are removed from the set.</span></span>
*  <span data-ttu-id="b0787-420">Enfin, si vous avez supprimé des membres masqués, le résultat de la recherche est déterminé :</span><span class="sxs-lookup"><span data-stu-id="b0787-420">Finally, having removed hidden members, the result of the lookup is determined:</span></span>
    * <span data-ttu-id="b0787-421">Si l’ensemble se compose d’un seul membre qui n’est pas une méthode, ce membre est le résultat de la recherche.</span><span class="sxs-lookup"><span data-stu-id="b0787-421">If the set consists of a single member that is not a method, then this member is the result of the lookup.</span></span>
    * <span data-ttu-id="b0787-422">Sinon, si l’ensemble contient uniquement des méthodes, ce groupe de méthodes est le résultat de la recherche.</span><span class="sxs-lookup"><span data-stu-id="b0787-422">Otherwise, if the set contains only methods, then this group of methods is the result of the lookup.</span></span>
    * <span data-ttu-id="b0787-423">Dans le cas contraire, la recherche est ambiguë et une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-423">Otherwise, the lookup is ambiguous, and a binding-time error occurs.</span></span>

<span data-ttu-id="b0787-424">Pour les recherches de membres dans les types autres que les paramètres de type et les interfaces, et les recherches de membres dans les interfaces qui sont strictement à héritage simple (chaque interface dans la chaîne d’héritage a exactement zéro ou une interface de base directe), l’effet des règles de recherche est le suivant : simplement, les membres dérivés masquent les membres de base avec le même nom ou la même signature.</span><span class="sxs-lookup"><span data-stu-id="b0787-424">For member lookups in types other than type parameters and interfaces, and member lookups in interfaces that are strictly single-inheritance (each interface in the inheritance chain has exactly zero or one direct base interface), the effect of the lookup rules is simply that derived members hide base members with the same name or signature.</span></span> <span data-ttu-id="b0787-425">Ces recherches à héritage unique ne sont jamais ambiguës.</span><span class="sxs-lookup"><span data-stu-id="b0787-425">Such single-inheritance lookups are never ambiguous.</span></span> <span data-ttu-id="b0787-426">Les ambiguïtés qui peuvent se produire à partir de recherches de membres dans des interfaces à héritage multiple sont décrites dans [accès aux membres d’interface](interfaces.md#interface-member-access).</span><span class="sxs-lookup"><span data-stu-id="b0787-426">The ambiguities that can possibly arise from member lookups in multiple-inheritance interfaces are described in [Interface member access](interfaces.md#interface-member-access).</span></span>

### <a name="base-types"></a><span data-ttu-id="b0787-427">Types de base</span><span class="sxs-lookup"><span data-stu-id="b0787-427">Base types</span></span>

<span data-ttu-id="b0787-428">Dans le cadre de la recherche de membres, un type `T` est considéré comme ayant les types de base suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-428">For purposes of member lookup, a type `T` is considered to have the following base types:</span></span>

*  <span data-ttu-id="b0787-429">Si `T` est `object`, `T` n’a pas de type de base.</span><span class="sxs-lookup"><span data-stu-id="b0787-429">If `T` is `object`, then `T` has no base type.</span></span>
*  <span data-ttu-id="b0787-430">Si `T` est un *enum_type*, les types de base de `T` sont les types de classe `System.Enum`, `System.ValueType`et `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-430">If `T` is an *enum_type*, the base types of `T` are the class types `System.Enum`, `System.ValueType`, and `object`.</span></span>
*  <span data-ttu-id="b0787-431">Si `T` est un *struct_type*, les types de base de `T` sont les types de classe `System.ValueType` et `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-431">If `T` is a *struct_type*, the base types of `T` are the class types `System.ValueType` and `object`.</span></span>
*  <span data-ttu-id="b0787-432">Si `T` est un *class_type*, les types de base de `T` sont les classes de base de `T`, y compris le type de classe `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-432">If `T` is a *class_type*, the base types of `T` are the base classes of `T`, including the class type `object`.</span></span>
*  <span data-ttu-id="b0787-433">Si `T` est un *INTERFACE_TYPE*, les types de base de `T` sont les interfaces de base de `T` et le type de classe `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-433">If `T` is an *interface_type*, the base types of `T` are the base interfaces of `T` and the class type `object`.</span></span>
*  <span data-ttu-id="b0787-434">Si `T` est un *array_type*, les types de base de `T` sont les types de classe `System.Array` et `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-434">If `T` is an *array_type*, the base types of `T` are the class types `System.Array` and `object`.</span></span>
*  <span data-ttu-id="b0787-435">Si `T` est un *delegate_type*, les types de base de `T` sont les types de classe `System.Delegate` et `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-435">If `T` is a *delegate_type*, the base types of `T` are the class types `System.Delegate` and `object`.</span></span>

## <a name="function-members"></a><span data-ttu-id="b0787-436">Membres de fonction</span><span class="sxs-lookup"><span data-stu-id="b0787-436">Function members</span></span>

<span data-ttu-id="b0787-437">Les fonctions membres sont des membres qui contiennent des instructions exécutables.</span><span class="sxs-lookup"><span data-stu-id="b0787-437">Function members are members that contain executable statements.</span></span> <span data-ttu-id="b0787-438">Les membres de fonction sont toujours membres de types et ne peuvent pas être membres d’espaces de noms.</span><span class="sxs-lookup"><span data-stu-id="b0787-438">Function members are always members of types and cannot be members of namespaces.</span></span> <span data-ttu-id="b0787-439">C#définit les catégories suivantes de membres de fonction :</span><span class="sxs-lookup"><span data-stu-id="b0787-439">C# defines the following categories of function members:</span></span>

*  <span data-ttu-id="b0787-440">Méthodes</span><span class="sxs-lookup"><span data-stu-id="b0787-440">Methods</span></span>
*  <span data-ttu-id="b0787-441">Propriétés</span><span class="sxs-lookup"><span data-stu-id="b0787-441">Properties</span></span>
*  <span data-ttu-id="b0787-442">Events</span><span class="sxs-lookup"><span data-stu-id="b0787-442">Events</span></span>
*  <span data-ttu-id="b0787-443">Indexeurs</span><span class="sxs-lookup"><span data-stu-id="b0787-443">Indexers</span></span>
*  <span data-ttu-id="b0787-444">Opérateurs définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="b0787-444">User-defined operators</span></span>
*  <span data-ttu-id="b0787-445">Constructeurs d’instance</span><span class="sxs-lookup"><span data-stu-id="b0787-445">Instance constructors</span></span>
*  <span data-ttu-id="b0787-446">Constructeurs statiques</span><span class="sxs-lookup"><span data-stu-id="b0787-446">Static constructors</span></span>
*  <span data-ttu-id="b0787-447">Destructeurs</span><span class="sxs-lookup"><span data-stu-id="b0787-447">Destructors</span></span>

<span data-ttu-id="b0787-448">À l’exception des destructeurs et des constructeurs statiques (qui ne peuvent pas être appelés explicitement), les instructions contenues dans les fonctions membres sont exécutées par le biais d’appels de membres de fonction.</span><span class="sxs-lookup"><span data-stu-id="b0787-448">Except for destructors and static constructors (which cannot be invoked explicitly), the statements contained in function members are executed through function member invocations.</span></span> <span data-ttu-id="b0787-449">La syntaxe réelle pour l’écriture d’un appel de membre de fonction dépend de la catégorie de membre de fonction particulière.</span><span class="sxs-lookup"><span data-stu-id="b0787-449">The actual syntax for writing a function member invocation depends on the particular function member category.</span></span>

<span data-ttu-id="b0787-450">La liste d’arguments ([listes d’arguments](expressions.md#argument-lists)) d’un appel de membre de fonction fournit des valeurs réelles ou des références de variables pour les paramètres de la fonction membre.</span><span class="sxs-lookup"><span data-stu-id="b0787-450">The argument list ([Argument lists](expressions.md#argument-lists)) of a function member invocation provides actual values or variable references for the parameters of the function member.</span></span>

<span data-ttu-id="b0787-451">Les appels de méthodes génériques peuvent utiliser l’inférence de type pour déterminer le jeu d’arguments de type à passer à la méthode.</span><span class="sxs-lookup"><span data-stu-id="b0787-451">Invocations of generic methods may employ type inference to determine the set of type arguments to pass to the method.</span></span> <span data-ttu-id="b0787-452">Ce processus est décrit dans [inférence de type](expressions.md#type-inference).</span><span class="sxs-lookup"><span data-stu-id="b0787-452">This process is described in [Type inference](expressions.md#type-inference).</span></span>

<span data-ttu-id="b0787-453">Les appels de méthodes, d’indexeurs, d’opérateurs et de constructeurs d’instance utilisent la résolution de surcharge pour déterminer l’ensemble de candidats des membres de fonction à appeler.</span><span class="sxs-lookup"><span data-stu-id="b0787-453">Invocations of methods, indexers, operators and instance constructors employ overload resolution to determine which of a candidate set of function members to invoke.</span></span> <span data-ttu-id="b0787-454">Ce processus est décrit dans [résolution de surcharge](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="b0787-454">This process is described in [Overload resolution](expressions.md#overload-resolution).</span></span>

<span data-ttu-id="b0787-455">Une fois qu’un membre de fonction particulier a été identifié au moment de la liaison, éventuellement par la résolution de surcharge, le processus d’exécution réel de l’appel de la fonction membre est décrit dans [vérification de la résolution de surcharge dynamique au moment](expressions.md#compile-time-checking-of-dynamic-overload-resolution)de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-455">Once a particular function member has been identified at binding-time, possibly through overload resolution, the actual run-time process of invoking the function member is described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="b0787-456">Le tableau suivant résume le traitement qui a lieu dans les constructions impliquant les six catégories de fonctions membres qui peuvent être explicitement appelées.</span><span class="sxs-lookup"><span data-stu-id="b0787-456">The following table summarizes the processing that takes place in constructs involving the six categories of function members that can be explicitly invoked.</span></span> <span data-ttu-id="b0787-457">Dans le tableau, `e`, `x`, `y`et `value` indiquent des expressions classées comme variables ou valeurs, `T` indique une expression classée comme un type, `F` est le nom simple d’une méthode et `P` est le nom simple d’une propriété.</span><span class="sxs-lookup"><span data-stu-id="b0787-457">In the table, `e`, `x`, `y`, and `value` indicate expressions classified as variables or values, `T` indicates an expression classified as a type, `F` is the simple name of a method, and `P` is the simple name of a property.</span></span>


| <span data-ttu-id="b0787-458">__Composer__</span><span class="sxs-lookup"><span data-stu-id="b0787-458">__Construct__</span></span>     | <span data-ttu-id="b0787-459">__Exemple__</span><span class="sxs-lookup"><span data-stu-id="b0787-459">__Example__</span></span>    | <span data-ttu-id="b0787-460">__Description__</span><span class="sxs-lookup"><span data-stu-id="b0787-460">__Description__</span></span> |
|-------------------|----------------|-----------------|
| <span data-ttu-id="b0787-461">Appel de méthode</span><span class="sxs-lookup"><span data-stu-id="b0787-461">Method invocation</span></span> | `F(x,y)`       | <span data-ttu-id="b0787-462">La résolution de surcharge est appliquée pour sélectionner la meilleure méthode `F` dans la classe ou le struct conteneur.</span><span class="sxs-lookup"><span data-stu-id="b0787-462">Overload resolution is applied to select the best method `F` in the containing class or struct.</span></span> <span data-ttu-id="b0787-463">La méthode est appelée avec la liste d’arguments `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-463">The method is invoked with the argument list `(x,y)`.</span></span> <span data-ttu-id="b0787-464">Si la méthode n’est pas `static`, l’expression d’instance est `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-464">If the method is not `static`, the instance expression is `this`.</span></span> | 
|                   | `T.F(x,y)`     | <span data-ttu-id="b0787-465">La résolution de surcharge est appliquée pour sélectionner la meilleure méthode `F` dans la classe ou le struct `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-465">Overload resolution is applied to select the best method `F` in the class or struct `T`.</span></span> <span data-ttu-id="b0787-466">Une erreur de liaison au moment de la liaison se produit si la méthode n’est pas `static`.</span><span class="sxs-lookup"><span data-stu-id="b0787-466">A binding-time error occurs if the method is not `static`.</span></span> <span data-ttu-id="b0787-467">La méthode est appelée avec la liste d’arguments `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-467">The method is invoked with the argument list `(x,y)`.</span></span> | 
|                   | `e.F(x,y)`     | <span data-ttu-id="b0787-468">La résolution de surcharge est appliquée pour sélectionner la meilleure méthode F dans la classe, la structure ou l’interface donnée par le type de `e`.</span><span class="sxs-lookup"><span data-stu-id="b0787-468">Overload resolution is applied to select the best method F in the class, struct, or interface given by the type of `e`.</span></span> <span data-ttu-id="b0787-469">Une erreur de liaison au moment de la liaison se produit si la méthode est `static`.</span><span class="sxs-lookup"><span data-stu-id="b0787-469">A binding-time error occurs if the method is `static`.</span></span> <span data-ttu-id="b0787-470">La méthode est appelée avec l’expression d’instance `e` et la liste d’arguments `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-470">The method is invoked with the instance expression `e` and the argument list `(x,y)`.</span></span> | 
| <span data-ttu-id="b0787-471">Accès à la propriété</span><span class="sxs-lookup"><span data-stu-id="b0787-471">Property access</span></span>   | `P`            | <span data-ttu-id="b0787-472">L’accesseur `get` de la propriété `P` dans la classe ou le struct conteneur est appelé.</span><span class="sxs-lookup"><span data-stu-id="b0787-472">The `get` accessor of the property `P` in the containing class or struct is invoked.</span></span> <span data-ttu-id="b0787-473">Une erreur de compilation se produit si `P` est en écriture seule.</span><span class="sxs-lookup"><span data-stu-id="b0787-473">A compile-time error occurs if `P` is write-only.</span></span> <span data-ttu-id="b0787-474">Si `P` n’est pas `static`, l’expression d’instance est `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-474">If `P` is not `static`, the instance expression is `this`.</span></span> | 
|                   | `P = value`    | <span data-ttu-id="b0787-475">L’accesseur `set` de la propriété `P` dans la classe ou le struct conteneur est appelé avec la `(value)`de liste d’arguments.</span><span class="sxs-lookup"><span data-stu-id="b0787-475">The `set` accessor of the property `P` in the containing class or struct is invoked with the argument list `(value)`.</span></span> <span data-ttu-id="b0787-476">Une erreur de compilation se produit si `P` est en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="b0787-476">A compile-time error occurs if `P` is read-only.</span></span> <span data-ttu-id="b0787-477">Si `P` n’est pas `static`, l’expression d’instance est `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-477">If `P` is not `static`, the instance expression is `this`.</span></span> | 
|                   | `T.P`          | <span data-ttu-id="b0787-478">L’accesseur `get` de la propriété `P` dans la classe ou le struct `T` est appelé.</span><span class="sxs-lookup"><span data-stu-id="b0787-478">The `get` accessor of the property `P` in the class or struct `T` is invoked.</span></span> <span data-ttu-id="b0787-479">Une erreur de compilation se produit si `P` n’est pas `static` ou si `P` est en écriture seule.</span><span class="sxs-lookup"><span data-stu-id="b0787-479">A compile-time error occurs if `P` is not `static` or if `P` is write-only.</span></span> | 
|                   | `T.P = value`  | <span data-ttu-id="b0787-480">L’accesseur `set` de la propriété `P` dans la classe ou le struct `T` est appelé avec la `(value)`de la liste d’arguments.</span><span class="sxs-lookup"><span data-stu-id="b0787-480">The `set` accessor of the property `P` in the class or struct `T` is invoked with the argument list `(value)`.</span></span> <span data-ttu-id="b0787-481">Une erreur de compilation se produit si `P` n’est pas `static` ou si `P` est en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="b0787-481">A compile-time error occurs if `P` is not `static` or if `P` is read-only.</span></span> | 
|                   | `e.P`          | <span data-ttu-id="b0787-482">L’accesseur `get` de la propriété `P` dans la classe, la structure ou l’interface donnée par le type de `e` est appelé avec l’expression d’instance `e`.</span><span class="sxs-lookup"><span data-stu-id="b0787-482">The `get` accessor of the property `P` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`.</span></span> <span data-ttu-id="b0787-483">Une erreur de liaison s’est produite si `P` est `static` ou si `P` est en écriture seule.</span><span class="sxs-lookup"><span data-stu-id="b0787-483">A binding-time error occurs if `P` is `static` or if `P` is write-only.</span></span> | 
|                   | `e.P = value`  | <span data-ttu-id="b0787-484">L’accesseur `set` de la propriété `P` dans la classe, la structure ou l’interface donnée par le type de `e` est appelé avec l’expression d’instance `e` et la liste d’arguments `(value)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-484">The `set` accessor of the property `P` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e` and the argument list `(value)`.</span></span> <span data-ttu-id="b0787-485">Une erreur de liaison s’est produite si `P` est `static` ou si `P` est en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="b0787-485">A binding-time error occurs if `P` is `static` or if `P` is read-only.</span></span> | 
| <span data-ttu-id="b0787-486">Accès aux événements</span><span class="sxs-lookup"><span data-stu-id="b0787-486">Event access</span></span>      | `E += value`   | <span data-ttu-id="b0787-487">L’accesseur `add` de l’événement `E` dans la classe ou le struct conteneur est appelé.</span><span class="sxs-lookup"><span data-stu-id="b0787-487">The `add` accessor of the event `E` in the containing class or struct is invoked.</span></span> <span data-ttu-id="b0787-488">Si `E` n’est pas statique, l’expression d’instance est `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-488">If `E` is not static, the instance expression is `this`.</span></span> | 
|                   | `E -= value`   | <span data-ttu-id="b0787-489">L’accesseur `remove` de l’événement `E` dans la classe ou le struct conteneur est appelé.</span><span class="sxs-lookup"><span data-stu-id="b0787-489">The `remove` accessor of the event `E` in the containing class or struct is invoked.</span></span> <span data-ttu-id="b0787-490">Si `E` n’est pas statique, l’expression d’instance est `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-490">If `E` is not static, the instance expression is `this`.</span></span> | 
|                   | `T.E += value` | <span data-ttu-id="b0787-491">L’accesseur `add` de l’événement `E` dans la classe ou le struct `T` est appelé.</span><span class="sxs-lookup"><span data-stu-id="b0787-491">The `add` accessor of the event `E` in the class or struct `T` is invoked.</span></span> <span data-ttu-id="b0787-492">Une erreur de liaison au moment de la liaison se produit si `E` n’est pas statique.</span><span class="sxs-lookup"><span data-stu-id="b0787-492">A binding-time error occurs if `E` is not static.</span></span> | 
|                   | `T.E -= value` | <span data-ttu-id="b0787-493">L’accesseur `remove` de l’événement `E` dans la classe ou le struct `T` est appelé.</span><span class="sxs-lookup"><span data-stu-id="b0787-493">The `remove` accessor of the event `E` in the class or struct `T` is invoked.</span></span> <span data-ttu-id="b0787-494">Une erreur de liaison au moment de la liaison se produit si `E` n’est pas statique.</span><span class="sxs-lookup"><span data-stu-id="b0787-494">A binding-time error occurs if `E` is not static.</span></span> | 
|                   | `e.E += value` | <span data-ttu-id="b0787-495">L’accesseur `add` de l’événement `E` dans la classe, la structure ou l’interface donnée par le type de `e` est appelé avec l’expression d’instance `e`.</span><span class="sxs-lookup"><span data-stu-id="b0787-495">The `add` accessor of the event `E` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`.</span></span> <span data-ttu-id="b0787-496">Une erreur de liaison au moment de la liaison se produit si `E` est statique.</span><span class="sxs-lookup"><span data-stu-id="b0787-496">A binding-time error occurs if `E` is static.</span></span> | 
|                   | `e.E -= value` | <span data-ttu-id="b0787-497">L’accesseur `remove` de l’événement `E` dans la classe, la structure ou l’interface donnée par le type de `e` est appelé avec l’expression d’instance `e`.</span><span class="sxs-lookup"><span data-stu-id="b0787-497">The `remove` accessor of the event `E` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`.</span></span> <span data-ttu-id="b0787-498">Une erreur de liaison au moment de la liaison se produit si `E` est statique.</span><span class="sxs-lookup"><span data-stu-id="b0787-498">A binding-time error occurs if `E` is static.</span></span> | 
| <span data-ttu-id="b0787-499">Accès aux indexeurs</span><span class="sxs-lookup"><span data-stu-id="b0787-499">Indexer access</span></span>    | `e[x,y]`       | <span data-ttu-id="b0787-500">La résolution de surcharge est appliquée pour sélectionner le meilleur indexeur de la classe, du struct ou de l’interface donné par le type de e.</span><span class="sxs-lookup"><span data-stu-id="b0787-500">Overload resolution is applied to select the best indexer in the class, struct, or interface given by the type of e.</span></span> <span data-ttu-id="b0787-501">L’accesseur `get` de l’indexeur est appelé avec l’expression d’instance `e` et la liste d’arguments `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-501">The `get` accessor of the indexer is invoked with the instance expression `e` and the argument list `(x,y)`.</span></span> <span data-ttu-id="b0787-502">Une erreur de liaison au moment de la liaison se produit si l’indexeur est en écriture seule.</span><span class="sxs-lookup"><span data-stu-id="b0787-502">A binding-time error occurs if the indexer is write-only.</span></span> | 
|                   | `e[x,y] = value` | <span data-ttu-id="b0787-503">La résolution de surcharge est appliquée pour sélectionner le meilleur indexeur dans la classe, la structure ou l’interface spécifiée par le type de `e`.</span><span class="sxs-lookup"><span data-stu-id="b0787-503">Overload resolution is applied to select the best indexer in the class, struct, or interface given by the type of `e`.</span></span> <span data-ttu-id="b0787-504">L’accesseur `set` de l’indexeur est appelé avec l’expression d’instance `e` et la liste d’arguments `(x,y,value)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-504">The `set` accessor of the indexer is invoked with the instance expression `e` and the argument list `(x,y,value)`.</span></span> <span data-ttu-id="b0787-505">Une erreur de liaison s’est produite si l’indexeur est en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="b0787-505">A binding-time error occurs if the indexer is read-only.</span></span> | 
| <span data-ttu-id="b0787-506">Appel d’opérateur</span><span class="sxs-lookup"><span data-stu-id="b0787-506">Operator invocation</span></span> | `-x`         | <span data-ttu-id="b0787-507">La résolution de surcharge est appliquée pour sélectionner le meilleur opérateur unaire dans la classe ou le struct donné par le type de `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-507">Overload resolution is applied to select the best unary operator in the class or struct given by the type of `x`.</span></span> <span data-ttu-id="b0787-508">L’opérateur sélectionné est appelé avec la liste d’arguments `(x)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-508">The selected operator is invoked with the argument list `(x)`.</span></span> | 
|                     | `x + y`      | <span data-ttu-id="b0787-509">La résolution de surcharge est appliquée pour sélectionner le meilleur opérateur binaire dans les classes ou les structs spécifiés par les types d' `x` et de `y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-509">Overload resolution is applied to select the best binary operator in the classes or structs given by the types of `x` and `y`.</span></span> <span data-ttu-id="b0787-510">L’opérateur sélectionné est appelé avec la liste d’arguments `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-510">The selected operator is invoked with the argument list `(x,y)`.</span></span> | 
| <span data-ttu-id="b0787-511">Appel du constructeur d’instance</span><span class="sxs-lookup"><span data-stu-id="b0787-511">Instance constructor invocation</span></span> | `new T(x,y)` | <span data-ttu-id="b0787-512">La résolution de surcharge est appliquée pour sélectionner le constructeur d’instance le mieux adapté à la classe ou au struct `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-512">Overload resolution is applied to select the best instance constructor in the class or struct `T`.</span></span> <span data-ttu-id="b0787-513">Le constructeur d’instance est appelé avec la liste d’arguments `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-513">The instance constructor is invoked with the argument list `(x,y)`.</span></span> | 

### <a name="argument-lists"></a><span data-ttu-id="b0787-514">Listes d’arguments</span><span class="sxs-lookup"><span data-stu-id="b0787-514">Argument lists</span></span>

<span data-ttu-id="b0787-515">Chaque membre de fonction et appel de délégué inclut une liste d’arguments qui fournit des valeurs réelles ou des références de variables pour les paramètres de la fonction membre.</span><span class="sxs-lookup"><span data-stu-id="b0787-515">Every function member and delegate invocation includes an argument list which provides actual values or variable references for the parameters of the function member.</span></span> <span data-ttu-id="b0787-516">La syntaxe permettant de spécifier la liste d’arguments d’un appel de membre de fonction dépend de la catégorie de membre de fonction :</span><span class="sxs-lookup"><span data-stu-id="b0787-516">The syntax for specifying the argument list of a function member invocation depends on the function member category:</span></span>

*  <span data-ttu-id="b0787-517">Pour les constructeurs d’instance, les méthodes, les indexeurs et les délégués, les arguments sont spécifiés en tant que *argument_list*, comme décrit ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b0787-517">For instance constructors, methods, indexers and delegates, the arguments are specified as an *argument_list*, as described below.</span></span> <span data-ttu-id="b0787-518">Pour les indexeurs, lors de l’appel de l’accesseur `set`, la liste d’arguments comprend également l’expression spécifiée comme opérande droit de l’opérateur d’assignation.</span><span class="sxs-lookup"><span data-stu-id="b0787-518">For indexers, when invoking the `set` accessor, the argument list additionally includes the expression specified as the right operand of the assignment operator.</span></span>
*  <span data-ttu-id="b0787-519">Pour les propriétés, la liste d’arguments est vide lors de l’appel de l’accesseur `get`, et se compose de l’expression spécifiée comme opérande droit de l’opérateur d’assignation lors de l’appel de l’accesseur `set`.</span><span class="sxs-lookup"><span data-stu-id="b0787-519">For properties, the argument list is empty when invoking the `get` accessor, and consists of the expression specified as the right operand of the assignment operator when invoking the `set` accessor.</span></span>
*  <span data-ttu-id="b0787-520">Pour les événements, la liste d’arguments se compose de l’expression spécifiée comme opérande droit de l’opérateur `+=` ou `-=`.</span><span class="sxs-lookup"><span data-stu-id="b0787-520">For events, the argument list consists of the expression specified as the right operand of the `+=` or `-=` operator.</span></span>
*  <span data-ttu-id="b0787-521">Pour les opérateurs définis par l’utilisateur, la liste d’arguments se compose du seul opérande de l’opérateur unaire ou des deux opérandes de l’opérateur binaire.</span><span class="sxs-lookup"><span data-stu-id="b0787-521">For user-defined operators, the argument list consists of the single operand of the unary operator or the two operands of the binary operator.</span></span>

<span data-ttu-id="b0787-522">Les arguments des propriétés ([Propriétés](classes.md#properties)), événements ([événements](classes.md#events)) et opérateurs définis par l’utilisateur ([opérateurs](classes.md#operators)) sont toujours passés en tant que paramètres de valeur ([paramètres de valeur](classes.md#value-parameters)).</span><span class="sxs-lookup"><span data-stu-id="b0787-522">The arguments of properties ([Properties](classes.md#properties)), events ([Events](classes.md#events)), and user-defined operators ([Operators](classes.md#operators)) are always passed as value parameters ([Value parameters](classes.md#value-parameters)).</span></span> <span data-ttu-id="b0787-523">Les arguments des indexeurs ([indexeurs](classes.md#indexers)) sont toujours passés en tant que paramètres de valeur ([paramètres de valeur](classes.md#value-parameters)) ou tableaux de paramètres ([tableaux de paramètres](classes.md#parameter-arrays)).</span><span class="sxs-lookup"><span data-stu-id="b0787-523">The arguments of indexers ([Indexers](classes.md#indexers)) are always passed as value parameters ([Value parameters](classes.md#value-parameters)) or parameter arrays ([Parameter arrays](classes.md#parameter-arrays)).</span></span> <span data-ttu-id="b0787-524">Les paramètres de référence et de sortie ne sont pas pris en charge pour ces catégories de membres de fonction.</span><span class="sxs-lookup"><span data-stu-id="b0787-524">Reference and output parameters are not supported for these categories of function members.</span></span>

<span data-ttu-id="b0787-525">Les arguments d’un constructeur d’instance, d’une méthode, d’un indexeur ou d’un appel de délégué sont spécifiés en tant que *argument_list*:</span><span class="sxs-lookup"><span data-stu-id="b0787-525">The arguments of an instance constructor, method, indexer or delegate invocation are specified as an *argument_list*:</span></span>

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

<span data-ttu-id="b0787-526">Un *argument_list* se compose d’un ou plusieurs *arguments*, séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="b0787-526">An *argument_list* consists of one or more *argument*s, separated by commas.</span></span> <span data-ttu-id="b0787-527">Chaque argument se compose d’un *argument_name* facultatif suivi d’un *argument_value*.</span><span class="sxs-lookup"><span data-stu-id="b0787-527">Each argument consists of an optional  *argument_name* followed by an *argument_value*.</span></span> <span data-ttu-id="b0787-528">Un *argument* avec un *argument_name* est appelé ***argument nommé***, alors qu’un argument sans *argument_name* *est un* ***argument positionnel***.</span><span class="sxs-lookup"><span data-stu-id="b0787-528">An *argument* with an *argument_name* is referred to as a ***named argument***, whereas an *argument* without an *argument_name* is a ***positional argument***.</span></span> <span data-ttu-id="b0787-529">L’affichage d’un argument positionnel après un argument nommé dans un *argument_list*constitue une erreur.</span><span class="sxs-lookup"><span data-stu-id="b0787-529">It is an error for a positional argument to appear after a named argument in an *argument_list*.</span></span>

<span data-ttu-id="b0787-530">La *argument_value* peut prendre l’une des formes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-530">The *argument_value* can take one of the following forms:</span></span>

*  <span data-ttu-id="b0787-531">*Expression*indiquant que l’argument est passé en tant que paramètre de valeur ([paramètres de valeur](classes.md#value-parameters)).</span><span class="sxs-lookup"><span data-stu-id="b0787-531">An *expression*, indicating that the argument is passed as a value parameter ([Value parameters](classes.md#value-parameters)).</span></span>
*  <span data-ttu-id="b0787-532">Le mot clé `ref` suivi d’un *variable_reference* ([références de variable](variables.md#variable-references)), indiquant que l’argument est passé en tant que paramètre de référence (paramètres de[référence](classes.md#reference-parameters)).</span><span class="sxs-lookup"><span data-stu-id="b0787-532">The keyword `ref` followed by a *variable_reference* ([Variable references](variables.md#variable-references)), indicating that the argument is passed as a reference parameter ([Reference parameters](classes.md#reference-parameters)).</span></span> <span data-ttu-id="b0787-533">Une variable doit être assignée de manière définitive ([assignation définie](variables.md#definite-assignment)) avant de pouvoir être passée en tant que paramètre de référence.</span><span class="sxs-lookup"><span data-stu-id="b0787-533">A variable must be definitely assigned ([Definite assignment](variables.md#definite-assignment)) before it can be passed as a reference parameter.</span></span> <span data-ttu-id="b0787-534">Le mot clé `out` suivi d’un *variable_reference* ([références de variable](variables.md#variable-references)), indiquant que l’argument est passé en tant que paramètre de sortie (paramètres de[sortie](classes.md#output-parameters)).</span><span class="sxs-lookup"><span data-stu-id="b0787-534">The keyword `out` followed by a *variable_reference* ([Variable references](variables.md#variable-references)), indicating that the argument is passed as an output parameter ([Output parameters](classes.md#output-parameters)).</span></span> <span data-ttu-id="b0787-535">Une variable est considérée comme assignée définitivement ([assignation définie](variables.md#definite-assignment)) à la suite d’un appel de membre de fonction dans lequel la variable est passée comme paramètre de sortie.</span><span class="sxs-lookup"><span data-stu-id="b0787-535">A variable is considered definitely assigned ([Definite assignment](variables.md#definite-assignment)) following a function member invocation in which the variable is passed as an output parameter.</span></span>

#### <a name="corresponding-parameters"></a><span data-ttu-id="b0787-536">Paramètres correspondants</span><span class="sxs-lookup"><span data-stu-id="b0787-536">Corresponding parameters</span></span>

<span data-ttu-id="b0787-537">Pour chaque argument dans une liste d’arguments, il doit y avoir un paramètre correspondant dans le membre de fonction ou le délégué appelé.</span><span class="sxs-lookup"><span data-stu-id="b0787-537">For each argument in an argument list there has to be a corresponding parameter in the function member or delegate being invoked.</span></span>

<span data-ttu-id="b0787-538">La liste de paramètres utilisée dans les éléments suivants est déterminée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-538">The parameter list used in the following is determined as follows:</span></span>

*  <span data-ttu-id="b0787-539">Pour les méthodes virtuelles et les indexeurs définis dans les classes, la liste de paramètres est choisie à partir de la déclaration ou de la substitution la plus spécifique, en commençant par le type statique du récepteur, et en recherchant dans ses classes de base.</span><span class="sxs-lookup"><span data-stu-id="b0787-539">For virtual methods and indexers defined in classes, the parameter list is picked from the most specific declaration or override of the function member, starting with the static type of the receiver, and searching through its base classes.</span></span>
*  <span data-ttu-id="b0787-540">Pour les méthodes d’interface et les indexeurs, la liste de paramètres est choisie en fonction de la définition la plus spécifique du membre, en commençant par le type d’interface et en parcourant les interfaces de base.</span><span class="sxs-lookup"><span data-stu-id="b0787-540">For interface methods and indexers, the parameter list is picked form the most specific definition of the member, starting with the interface type and searching through the base interfaces.</span></span> <span data-ttu-id="b0787-541">Si aucune liste de paramètres unique n’est trouvée, une liste de paramètres avec des noms inaccessibles et aucun paramètre facultatif n’est construite, de sorte que les appels ne peuvent pas utiliser de paramètres nommés ou omettre des arguments facultatifs.</span><span class="sxs-lookup"><span data-stu-id="b0787-541">If no unique parameter list is found, a parameter list with inaccessible names and no optional parameters is constructed, so that invocations cannot use named parameters or omit optional arguments.</span></span>
*  <span data-ttu-id="b0787-542">Pour les méthodes partielles, la liste de paramètres de la déclaration de méthode partielle de définition est utilisée.</span><span class="sxs-lookup"><span data-stu-id="b0787-542">For partial methods, the parameter list of the defining partial method declaration is used.</span></span>
*  <span data-ttu-id="b0787-543">Pour tous les autres membres de fonction et délégués, il n’existe qu’une seule liste de paramètres, qui est celle utilisée.</span><span class="sxs-lookup"><span data-stu-id="b0787-543">For all other function members and delegates there is only a single parameter list, which is the one used.</span></span>

<span data-ttu-id="b0787-544">La position d’un argument ou d’un paramètre est définie en tant que nombre d’arguments ou de paramètres qui la précèdent dans la liste d’arguments ou la liste de paramètres.</span><span class="sxs-lookup"><span data-stu-id="b0787-544">The position of an argument or parameter is defined as the number of arguments or parameters preceding it in the argument list or parameter list.</span></span>

<span data-ttu-id="b0787-545">Les paramètres correspondants pour les arguments de membre de fonction sont établis comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-545">The corresponding parameters for function member arguments are established as follows:</span></span>

*  <span data-ttu-id="b0787-546">Arguments dans le *argument_list* de constructeurs d’instance, de méthodes, d’indexeurs et de délégués :</span><span class="sxs-lookup"><span data-stu-id="b0787-546">Arguments in the *argument_list* of instance constructors, methods, indexers and delegates:</span></span>
    * <span data-ttu-id="b0787-547">Un argument positionnel dans lequel un paramètre fixe se produit à la même position dans la liste de paramètres correspond à ce paramètre.</span><span class="sxs-lookup"><span data-stu-id="b0787-547">A positional argument where a fixed parameter occurs at the same position in the parameter list corresponds to that parameter.</span></span>
    * <span data-ttu-id="b0787-548">Un argument positionnel d’un membre de fonction avec un tableau de paramètres appelé dans sa forme normale correspond au tableau de paramètres, qui doit se trouver à la même position dans la liste de paramètres.</span><span class="sxs-lookup"><span data-stu-id="b0787-548">A positional argument of a function member with a parameter array invoked in its normal form corresponds to the parameter  array, which must occur at the same position in the parameter list.</span></span>
    * <span data-ttu-id="b0787-549">Argument positionnel d’un membre de fonction avec un tableau de paramètres appelé dans sa forme développée, où aucun paramètre fixe ne se trouve à la même position dans la liste de paramètres, correspond à un élément dans le tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="b0787-549">A positional argument of a function member with a parameter array invoked in its expanded form, where no fixed parameter occurs at the same position in the parameter list, corresponds to an element in the parameter array.</span></span>
    * <span data-ttu-id="b0787-550">Un argument nommé correspond au paramètre du même nom dans la liste de paramètres.</span><span class="sxs-lookup"><span data-stu-id="b0787-550">A named argument corresponds to the parameter of the same name in the parameter list.</span></span>
    * <span data-ttu-id="b0787-551">Pour les indexeurs, lors de l’appel de l’accesseur `set`, l’expression spécifiée comme opérande droit de l’opérateur d’assignation correspond au paramètre de `value` implicite de la déclaration d’accesseur `set`.</span><span class="sxs-lookup"><span data-stu-id="b0787-551">For indexers, when invoking the `set` accessor, the expression specified as the right operand of the assignment operator corresponds to the implicit `value` parameter of the `set` accessor declaration.</span></span>
*  <span data-ttu-id="b0787-552">Pour les propriétés, lors de l’appel de l’accesseur `get` il n’y a pas d’arguments.</span><span class="sxs-lookup"><span data-stu-id="b0787-552">For properties, when invoking the `get` accessor there are no arguments.</span></span> <span data-ttu-id="b0787-553">Lors de l’appel de l’accesseur `set`, l’expression spécifiée comme opérande droit de l’opérateur d’assignation correspond au paramètre de `value` implicite de la déclaration d’accesseur `set`.</span><span class="sxs-lookup"><span data-stu-id="b0787-553">When invoking the `set` accessor, the expression specified as the right operand of the assignment operator corresponds to the implicit `value` parameter of the `set` accessor declaration.</span></span>
*  <span data-ttu-id="b0787-554">Pour les opérateurs unaires définis par l’utilisateur (y compris les conversions), l’opérande unique correspond au paramètre unique de la déclaration d’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-554">For user-defined unary operators (including conversions), the single operand corresponds to the single parameter of the operator declaration.</span></span>
*  <span data-ttu-id="b0787-555">Pour les opérateurs binaires définis par l’utilisateur, l’opérande de gauche correspond au premier paramètre, tandis que l’opérande droit correspond au deuxième paramètre de la déclaration de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-555">For user-defined binary operators, the left operand corresponds to the first parameter, and the right operand corresponds to the second parameter of the operator declaration.</span></span>

#### <a name="run-time-evaluation-of-argument-lists"></a><span data-ttu-id="b0787-556">Évaluation au moment de l’exécution des listes d’arguments</span><span class="sxs-lookup"><span data-stu-id="b0787-556">Run-time evaluation of argument lists</span></span>

<span data-ttu-id="b0787-557">Pendant le traitement au moment de l’exécution d’un appel de membre de fonction ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), les expressions ou les références de variable d’une liste d’arguments sont évaluées dans l’ordre, de gauche à droite, comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-557">During the run-time processing of a function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), the expressions or variable references of an argument list are evaluated in order, from left to right, as follows:</span></span>

*  <span data-ttu-id="b0787-558">Pour un paramètre de valeur, l’expression d’argument est évaluée et une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) en type de paramètre correspondant est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b0787-558">For a value parameter, the argument expression is evaluated and an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) to the corresponding parameter type is performed.</span></span> <span data-ttu-id="b0787-559">La valeur résultante devient la valeur initiale du paramètre de valeur dans l’appel de membre de fonction.</span><span class="sxs-lookup"><span data-stu-id="b0787-559">The resulting value becomes the initial value of the value parameter in the function member invocation.</span></span>
*  <span data-ttu-id="b0787-560">Pour un paramètre de référence ou de sortie, la référence de variable est évaluée et l’emplacement de stockage résultant devient l’emplacement de stockage représenté par le paramètre dans l’appel de membre de fonction.</span><span class="sxs-lookup"><span data-stu-id="b0787-560">For a reference or output parameter, the variable reference is evaluated and the resulting storage location becomes the storage location represented by the parameter in the function member invocation.</span></span> <span data-ttu-id="b0787-561">Si la référence variable donnée en tant que paramètre de référence ou de sortie est un élément de tableau d’un *reference_type*, un contrôle au moment de l’exécution est effectué pour garantir que le type d’élément du tableau est identique au type du paramètre.</span><span class="sxs-lookup"><span data-stu-id="b0787-561">If the variable reference given as a reference or output parameter is an array element of a *reference_type*, a run-time check is performed to ensure that the element type of the array is identical to the type of the parameter.</span></span> <span data-ttu-id="b0787-562">En cas d’échec de cette vérification, une `System.ArrayTypeMismatchException` est levée.</span><span class="sxs-lookup"><span data-stu-id="b0787-562">If this check fails, a `System.ArrayTypeMismatchException` is thrown.</span></span>

<span data-ttu-id="b0787-563">Les méthodes, les indexeurs et les constructeurs d’instance peuvent déclarer leur paramètre le plus à droite comme un tableau de paramètres ([tableaux de paramètres](classes.md#parameter-arrays)).</span><span class="sxs-lookup"><span data-stu-id="b0787-563">Methods, indexers, and instance constructors may declare their right-most parameter to be a parameter array ([Parameter arrays](classes.md#parameter-arrays)).</span></span> <span data-ttu-id="b0787-564">Ces fonctions sont appelées soit sous leur forme normale, soit sous leur forme développée en fonction de ce qui est applicable ([fonction membre applicable](expressions.md#applicable-function-member)) :</span><span class="sxs-lookup"><span data-stu-id="b0787-564">Such function members are invoked either in their normal form or in their expanded form depending on which is applicable ([Applicable function member](expressions.md#applicable-function-member)):</span></span>

*  <span data-ttu-id="b0787-565">Lorsqu’un membre de fonction avec un tableau de paramètres est appelé dans sa forme normale, l’argument donné pour le tableau de paramètres doit être une expression unique qui est implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) en type de tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="b0787-565">When a function member with a parameter array is invoked in its normal form, the argument given for the parameter array must be a single expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the parameter array type.</span></span> <span data-ttu-id="b0787-566">Dans ce cas, le tableau de paramètres agit précisément comme un paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-566">In this case, the parameter array acts precisely like a value parameter.</span></span>
*  <span data-ttu-id="b0787-567">Lorsqu’un membre de fonction avec un tableau de paramètres est appelé dans sa forme développée, l’appel doit spécifier zéro, un ou plusieurs arguments positionnels pour le tableau de paramètres, où chaque argument est une expression implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) en type d’élément du tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="b0787-567">When a function member with a parameter array is invoked in its expanded form, the invocation must specify zero or more positional arguments for the parameter array, where each argument is an expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the element type of the parameter array.</span></span> <span data-ttu-id="b0787-568">Dans ce cas, l’appel crée une instance du type de tableau de paramètres avec une longueur correspondant au nombre d’arguments, initialise les éléments de l’instance de tableau avec les valeurs d’argument données et utilise l’instance de tableau nouvellement créée comme argument.</span><span class="sxs-lookup"><span data-stu-id="b0787-568">In this case, the invocation creates an instance of the parameter array type with a length corresponding to the number of arguments, initializes the elements of the array instance with the given argument values, and uses the newly created array instance as the actual argument.</span></span>

<span data-ttu-id="b0787-569">Les expressions d’une liste d’arguments sont toujours évaluées dans l’ordre dans lequel elles sont écrites.</span><span class="sxs-lookup"><span data-stu-id="b0787-569">The expressions of an argument list are always evaluated in the order they are written.</span></span> <span data-ttu-id="b0787-570">Ainsi, l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-570">Thus, the example</span></span>
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
<span data-ttu-id="b0787-571">génère la sortie</span><span class="sxs-lookup"><span data-stu-id="b0787-571">produces the output</span></span>
```console
x = 0, y = 1, z = 2
x = 4, y = -1, z = 3
```

<span data-ttu-id="b0787-572">Les règles de covariance de tableau ([covariance de tableau](arrays.md#array-covariance)) autorisent une valeur d’un type de tableau `A[]` être une référence à une instance d’un type de tableau `B[]`, à condition qu’il existe une conversion de référence implicite de `B` à `A`.</span><span class="sxs-lookup"><span data-stu-id="b0787-572">The array co-variance rules ([Array covariance](arrays.md#array-covariance)) permit a value of an array type `A[]` to be a reference to an instance of an array type `B[]`, provided an implicit reference conversion exists from `B` to `A`.</span></span> <span data-ttu-id="b0787-573">En raison de ces règles, lorsqu’un élément de tableau d’un *reference_type* est passé en tant que paramètre de référence ou de sortie, un contrôle au moment de l’exécution est nécessaire pour garantir que le type d’élément réel du tableau est identique à celui du paramètre.</span><span class="sxs-lookup"><span data-stu-id="b0787-573">Because of these rules, when an array element of a *reference_type* is passed as a reference or output parameter, a run-time check is required to ensure that the actual element type of the array is identical to that of the parameter.</span></span> <span data-ttu-id="b0787-574">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-574">In the example</span></span>
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
<span data-ttu-id="b0787-575">le deuxième appel de `F` entraîne la levée d’une `System.ArrayTypeMismatchException`, car le type d’élément réel de `b` est `string` et non `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-575">the second invocation of `F` causes a `System.ArrayTypeMismatchException` to be thrown because the actual element type of `b` is `string` and not `object`.</span></span>

<span data-ttu-id="b0787-576">Lorsqu’un membre de fonction avec un tableau de paramètres est appelé dans sa forme développée, l’appel est traité exactement comme si une expression de création de tableau avec un initialiseur de tableau ([expressions de création de tableau](expressions.md#array-creation-expressions)) a été insérée autour des paramètres développés.</span><span class="sxs-lookup"><span data-stu-id="b0787-576">When a function member with a parameter array is invoked in its expanded form, the invocation is processed exactly as if an array creation expression with an array initializer ([Array creation expressions](expressions.md#array-creation-expressions)) was inserted around the expanded parameters.</span></span> <span data-ttu-id="b0787-577">Par exemple, étant donné la déclaration</span><span class="sxs-lookup"><span data-stu-id="b0787-577">For example, given the declaration</span></span>
```csharp
void F(int x, int y, params object[] args);
```
<span data-ttu-id="b0787-578">les appels suivants de la forme développée de la méthode</span><span class="sxs-lookup"><span data-stu-id="b0787-578">the following invocations of the expanded form of the method</span></span>
```csharp
F(10, 20);
F(10, 20, 30, 40);
F(10, 20, 1, "hello", 3.0);
```
<span data-ttu-id="b0787-579">correspondre exactement à</span><span class="sxs-lookup"><span data-stu-id="b0787-579">correspond exactly to</span></span>
```csharp
F(10, 20, new object[] {});
F(10, 20, new object[] {30, 40});
F(10, 20, new object[] {1, "hello", 3.0});
```

<span data-ttu-id="b0787-580">En particulier, Notez qu’un tableau vide est créé lorsqu’il n’y a aucun argument donné pour le tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="b0787-580">In particular, note that an empty array is created when there are zero arguments given for the parameter array.</span></span>

<span data-ttu-id="b0787-581">Lorsque des arguments sont omis d’un membre de fonction avec des paramètres facultatifs correspondants, les arguments par défaut de la déclaration de membre de fonction sont passés implicitement.</span><span class="sxs-lookup"><span data-stu-id="b0787-581">When arguments are omitted from a function member with corresponding optional parameters, the default arguments of the function member declaration are implicitly passed.</span></span> <span data-ttu-id="b0787-582">Étant donné qu’elles sont toujours constantes, leur évaluation n’aura pas d’impact sur l’ordre d’évaluation des arguments restants.</span><span class="sxs-lookup"><span data-stu-id="b0787-582">Because these are always constant, their evaluation will not impact the evaluation order of the remaining arguments.</span></span>

### <a name="type-inference"></a><span data-ttu-id="b0787-583">Inférence de type</span><span class="sxs-lookup"><span data-stu-id="b0787-583">Type inference</span></span>

<span data-ttu-id="b0787-584">Quand une méthode générique est appelée sans spécifier d’arguments de type, un processus d' ***inférence de type*** tente de déduire des arguments de type pour l’appel.</span><span class="sxs-lookup"><span data-stu-id="b0787-584">When a generic method is called without specifying type arguments, a ***type inference*** process attempts to infer type arguments for the call.</span></span> <span data-ttu-id="b0787-585">La présence de l’inférence de type permet d’utiliser une syntaxe plus pratique pour appeler une méthode générique et permet au programmeur d’éviter de spécifier des informations de type redondantes.</span><span class="sxs-lookup"><span data-stu-id="b0787-585">The presence of type inference allows a more convenient syntax to be used for calling a generic method, and allows the programmer to avoid specifying redundant type information.</span></span> <span data-ttu-id="b0787-586">Par exemple, à partir de la déclaration de méthode :</span><span class="sxs-lookup"><span data-stu-id="b0787-586">For example, given the method declaration:</span></span>
```csharp
class Chooser
{
    static Random rand = new Random();

    public static T Choose<T>(T first, T second) {
        return (rand.Next(2) == 0)? first: second;
    }
}
```
<span data-ttu-id="b0787-587">Il est possible d’appeler la méthode `Choose` sans spécifier explicitement un argument de type :</span><span class="sxs-lookup"><span data-stu-id="b0787-587">it is possible to invoke the `Choose` method without explicitly specifying a type argument:</span></span>
```csharp
int i = Chooser.Choose(5, 213);                 // Calls Choose<int>

string s = Chooser.Choose("foo", "bar");        // Calls Choose<string>
```

<span data-ttu-id="b0787-588">Par le biais de l’inférence de type, les arguments de type `int` et `string` sont déterminés à partir des arguments à la méthode.</span><span class="sxs-lookup"><span data-stu-id="b0787-588">Through type inference, the type arguments `int` and `string` are determined from the arguments to the method.</span></span>

<span data-ttu-id="b0787-589">L’inférence de type se produit dans le cadre du traitement au moment de la liaison d’un appel de méthode ([appel de méthode](expressions.md#method-invocations)) et a lieu avant l’étape de résolution de surcharge de l’appel.</span><span class="sxs-lookup"><span data-stu-id="b0787-589">Type inference occurs as part of the binding-time processing of a method invocation ([Method invocations](expressions.md#method-invocations)) and takes place before the overload resolution step of the invocation.</span></span> <span data-ttu-id="b0787-590">Lorsqu’un groupe de méthodes particulier est spécifié dans un appel de méthode et qu’aucun argument de type n’est spécifié dans le cadre de l’appel de méthode, l’inférence de type est appliquée à chaque méthode générique dans le groupe de méthodes.</span><span class="sxs-lookup"><span data-stu-id="b0787-590">When a particular method group is specified in a method invocation, and no type arguments are specified as part of the method invocation, type inference is applied to each generic method in the method group.</span></span> <span data-ttu-id="b0787-591">Si l’inférence de type est réussie, les arguments de type déduits sont utilisés pour déterminer les types d’arguments pour la résolution de surcharge suivante.</span><span class="sxs-lookup"><span data-stu-id="b0787-591">If type inference succeeds, then the inferred type arguments are used to determine the types of arguments for subsequent overload resolution.</span></span> <span data-ttu-id="b0787-592">Si la résolution de surcharge choisit une méthode générique comme une méthode à appeler, les arguments de type inférés sont utilisés en tant qu’arguments de type réels pour l’appel.</span><span class="sxs-lookup"><span data-stu-id="b0787-592">If overload resolution chooses a generic method as the one to invoke, then the inferred type arguments are used as the actual type arguments for the invocation.</span></span> <span data-ttu-id="b0787-593">Si l’inférence de type pour une méthode particulière échoue, cette méthode ne participe pas à la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="b0787-593">If type inference for a particular method fails, that method does not participate in overload resolution.</span></span> <span data-ttu-id="b0787-594">L’échec de l’inférence de type, en soi-même, ne provoque pas d’erreur au moment de la liaison.</span><span class="sxs-lookup"><span data-stu-id="b0787-594">The failure of type inference, in and of itself, does not cause a binding-time error.</span></span> <span data-ttu-id="b0787-595">Toutefois, il arrive souvent à une erreur de liaison lorsque la résolution de surcharge ne parvient pas à trouver les méthodes applicables.</span><span class="sxs-lookup"><span data-stu-id="b0787-595">However, it often leads to a binding-time error when overload resolution then fails to find any applicable methods.</span></span>

<span data-ttu-id="b0787-596">Si le nombre d’arguments fourni est différent du nombre de paramètres dans la méthode, l’inférence échoue immédiatement.</span><span class="sxs-lookup"><span data-stu-id="b0787-596">If the supplied number of arguments is different than the number of parameters in the method, then inference immediately fails.</span></span> <span data-ttu-id="b0787-597">Dans le cas contraire, supposez que la méthode générique possède la signature suivante :</span><span class="sxs-lookup"><span data-stu-id="b0787-597">Otherwise, assume that the generic method has the following signature:</span></span>
```csharp
Tr M<X1,...,Xn>(T1 x1, ..., Tm xm)
```

<span data-ttu-id="b0787-598">Avec un appel de méthode de la forme `M(E1...Em)` la tâche d’inférence de type consiste à trouver des arguments de type uniques `S1...Sn` pour chacun des paramètres de type `X1...Xn` afin que l’appel `M<S1...Sn>(E1...Em)` devienne valide.</span><span class="sxs-lookup"><span data-stu-id="b0787-598">With a method call of the form `M(E1...Em)` the task of type inference is to find unique type arguments `S1...Sn` for each of the type parameters `X1...Xn` so that the call `M<S1...Sn>(E1...Em)` becomes valid.</span></span>

<span data-ttu-id="b0787-599">Pendant le processus d’inférence, chaque paramètre de type `Xi` est *fixé* à un type particulier `Si` ou non *résolu* avec un ensemble de *limites*associées.</span><span class="sxs-lookup"><span data-stu-id="b0787-599">During the process of inference each type parameter `Xi` is either *fixed* to a particular type `Si` or *unfixed* with an associated set of *bounds*.</span></span> <span data-ttu-id="b0787-600">Chaque limite est un type `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-600">Each of the bounds is some type `T`.</span></span> <span data-ttu-id="b0787-601">Initialement, chaque variable de type `Xi` n’est pas résolue avec un ensemble vide de limites.</span><span class="sxs-lookup"><span data-stu-id="b0787-601">Initially each type variable `Xi` is unfixed with an empty set of bounds.</span></span>

<span data-ttu-id="b0787-602">L’inférence de type a lieu en plusieurs phases.</span><span class="sxs-lookup"><span data-stu-id="b0787-602">Type inference takes place in phases.</span></span> <span data-ttu-id="b0787-603">Chaque phase essaiera de déduire les arguments de type pour d’autres variables de type en fonction des résultats de la phase précédente.</span><span class="sxs-lookup"><span data-stu-id="b0787-603">Each phase will try to infer type arguments for more type variables based on the findings of the previous phase.</span></span> <span data-ttu-id="b0787-604">La première phase effectue quelques inférences initiales des limites, tandis que la deuxième phase résout des variables de type à des types spécifiques et déduit d’autres limites.</span><span class="sxs-lookup"><span data-stu-id="b0787-604">The first phase makes some initial inferences of bounds, whereas the second phase fixes type variables to specific types and infers further bounds.</span></span> <span data-ttu-id="b0787-605">La deuxième phase peut être répétée plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-605">The second phase may have to be repeated a number of times.</span></span>

<span data-ttu-id="b0787-606">*Remarque :* L’inférence de type a lieu non seulement quand une méthode générique est appelée.</span><span class="sxs-lookup"><span data-stu-id="b0787-606">*Note:* Type inference takes place not only when a generic method is called.</span></span> <span data-ttu-id="b0787-607">L’inférence de type pour la conversion de groupes de méthodes est décrite dans [inférence de type pour la conversion de groupes de méthodes](expressions.md#type-inference-for-conversion-of-method-groups) et la recherche du meilleur type commun d’un ensemble d’expressions est décrite dans [recherche du meilleur type commun d’un ensemble d’expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions).</span><span class="sxs-lookup"><span data-stu-id="b0787-607">Type inference for conversion of method groups is described in [Type inference for conversion of method groups](expressions.md#type-inference-for-conversion-of-method-groups) and finding the best common type of a set of expressions is described in [Finding the best common type of a set of expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions).</span></span>

#### <a name="the-first-phase"></a><span data-ttu-id="b0787-608">La première phase</span><span class="sxs-lookup"><span data-stu-id="b0787-608">The first phase</span></span>

<span data-ttu-id="b0787-609">Pour chacun des arguments de méthode `Ei`:</span><span class="sxs-lookup"><span data-stu-id="b0787-609">For each of the method arguments `Ei`:</span></span>

*   <span data-ttu-id="b0787-610">Si `Ei` est une fonction anonyme, une *inférence de type de paramètre explicite* ([inférences de type de paramètre explicite](expressions.md#explicit-parameter-type-inferences)) est effectuée de `Ei` à `Ti`</span><span class="sxs-lookup"><span data-stu-id="b0787-610">If `Ei` is an anonymous function, an *explicit parameter type inference* ([Explicit parameter type inferences](expressions.md#explicit-parameter-type-inferences)) is made from `Ei` to `Ti`</span></span>
*   <span data-ttu-id="b0787-611">Sinon, si `Ei` a un type `U` et `xi` est un paramètre de valeur, une *inférence de liaison inférieure* est effectuée *de* `U` *à* `Ti`.</span><span class="sxs-lookup"><span data-stu-id="b0787-611">Otherwise, if `Ei` has a type `U` and `xi` is a value parameter then a *lower-bound inference* is made *from* `U` *to* `Ti`.</span></span>
*   <span data-ttu-id="b0787-612">Sinon, si `Ei` a un type `U` et `xi` est un paramètre `ref` ou `out`, une *inférence exacte* est effectuée *de* `U` *à* `Ti`.</span><span class="sxs-lookup"><span data-stu-id="b0787-612">Otherwise, if `Ei` has a type `U` and `xi` is a `ref` or `out` parameter then an *exact inference* is made *from* `U` *to* `Ti`.</span></span>
*   <span data-ttu-id="b0787-613">Sinon, aucune inférence n’est effectuée pour cet argument.</span><span class="sxs-lookup"><span data-stu-id="b0787-613">Otherwise, no inference is made for this argument.</span></span>


#### <a name="the-second-phase"></a><span data-ttu-id="b0787-614">La deuxième phase</span><span class="sxs-lookup"><span data-stu-id="b0787-614">The second phase</span></span>

<span data-ttu-id="b0787-615">La deuxième phase se déroule comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-615">The second phase proceeds as follows:</span></span>

*   <span data-ttu-id="b0787-616">Toutes les variables de type non *fixes* `Xi` qui ne *dépendent pas de* ([dépendance](expressions.md#dependence)) tout `Xj` sont fixes ([Correction](expressions.md#fixing)).</span><span class="sxs-lookup"><span data-stu-id="b0787-616">All *unfixed* type variables `Xi` which do not *depend on* ([Dependence](expressions.md#dependence)) any `Xj` are fixed ([Fixing](expressions.md#fixing)).</span></span>
*   <span data-ttu-id="b0787-617">S’il n’existe aucune variable de type de ce type, toutes les variables de type non *fixes* `Xi` sont *fixes* pour lesquelles tous les éléments suivants sont détenus :</span><span class="sxs-lookup"><span data-stu-id="b0787-617">If no such type variables exist, all *unfixed* type variables `Xi` are *fixed* for which all of the following hold:</span></span>
    *   <span data-ttu-id="b0787-618">Il existe au moins une variable de type `Xj` qui dépend de `Xi`</span><span class="sxs-lookup"><span data-stu-id="b0787-618">There is at least one type variable `Xj` that depends on `Xi`</span></span>
    *   <span data-ttu-id="b0787-619">`Xi` a un ensemble non vide de limites</span><span class="sxs-lookup"><span data-stu-id="b0787-619">`Xi` has a non-empty set of bounds</span></span>
*   <span data-ttu-id="b0787-620">S’il n’existe aucune variable de type de ce type et qu’il existe encore des variables de type non *fixes* , l’inférence de type échoue.</span><span class="sxs-lookup"><span data-stu-id="b0787-620">If no such type variables exist and there are still *unfixed* type variables, type inference fails.</span></span>
*   <span data-ttu-id="b0787-621">Sinon, s’il n’existe aucune variable de type non *fixe* , l’inférence de type est réussie.</span><span class="sxs-lookup"><span data-stu-id="b0787-621">Otherwise, if no further *unfixed* type variables exist, type inference succeeds.</span></span>
*   <span data-ttu-id="b0787-622">Sinon, pour tous les arguments `Ei` avec le type `Ti` de paramètre correspondant où les *types de sortie* ([types de sortie](expressions.md#output-types)) contiennent des variables `Xj` de type *non fixes*, mais pas les *types d’entrée* ([types d’entrée](expressions.md#input-types)), l’*inférence de type de sortie* ([inférences de type de sortie](expressions.md#output-type-inferences)) est effectuée *de* `Ei` *à* `Ti`.</span><span class="sxs-lookup"><span data-stu-id="b0787-622">Otherwise, for all arguments `Ei` with corresponding parameter type `Ti` where the *output types* ([Output types](expressions.md#output-types)) contain *unfixed* type variables `Xj` but the *input types* ([Input types](expressions.md#input-types)) do not, an *output type inference* ([Output type inferences](expressions.md#output-type-inferences)) is made *from* `Ei` *to* `Ti`.</span></span> <span data-ttu-id="b0787-623">La deuxième phase est ensuite répétée.</span><span class="sxs-lookup"><span data-stu-id="b0787-623">Then the second phase is repeated.</span></span>

#### <a name="input-types"></a><span data-ttu-id="b0787-624">Types d’entrée</span><span class="sxs-lookup"><span data-stu-id="b0787-624">Input types</span></span>

<span data-ttu-id="b0787-625">Si `E` est un groupe de méthodes ou une fonction anonyme implicitement typée et `T` est un type délégué ou un type d’arborescence d’expression, tous les types de paramètres de `T` sont des *types d’entrée* de `E` *avec le type* `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-625">If `E` is a method group or implicitly typed anonymous function and `T` is a delegate type or expression tree type then all the parameter types of `T` are *input types* of `E` *with type* `T`.</span></span>

####  <a name="output-types"></a><span data-ttu-id="b0787-626">Types de sorties</span><span class="sxs-lookup"><span data-stu-id="b0787-626">Output types</span></span>

<span data-ttu-id="b0787-627">Si `E` est un groupe de méthodes ou une fonction anonyme et `T` est un type délégué ou un type d’arborescence d’expression, le type de retour de `T` est un *type de sortie* `E` *avec le type* `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-627">If `E` is a method group or an anonymous function and `T` is a delegate type or expression tree type then the return type of `T` is an *output type of* `E` *with type* `T`.</span></span>

#### <a name="dependence"></a><span data-ttu-id="b0787-628">Dépendance</span><span class="sxs-lookup"><span data-stu-id="b0787-628">Dependence</span></span>

<span data-ttu-id="b0787-629">Une variable de type non *fixe* `Xi` *dépend directement* d’une variable de type non fixe `Xj` si, pour un argument, `Ek` avec le type `Tk` `Xj` se produit dans un *type d’entrée* de `Ek` avec le type `Tk` et `Xi` se produit dans un *type de sortie* `Ek` avec le type `Tk`.</span><span class="sxs-lookup"><span data-stu-id="b0787-629">An *unfixed* type variable `Xi` *depends directly on* an unfixed type variable `Xj` if for some argument `Ek` with type `Tk` `Xj` occurs in an *input type* of `Ek` with type `Tk` and `Xi` occurs in an *output type* of `Ek` with type `Tk`.</span></span>

<span data-ttu-id="b0787-630">`Xj` *dépend de* `Xi` si `Xj` *dépend directement* de `Xi` ou si `Xi` *dépend directement* de `Xk` et `Xk` *dépend de* `Xj`.</span><span class="sxs-lookup"><span data-stu-id="b0787-630">`Xj` *depends on* `Xi` if `Xj` *depends directly on* `Xi` or if `Xi` *depends directly on* `Xk` and `Xk` *depends on* `Xj`.</span></span> <span data-ttu-id="b0787-631">Par conséquent, « dépend de » est la fermeture transitive mais non réflexive de « dépend directement de ».</span><span class="sxs-lookup"><span data-stu-id="b0787-631">Thus "depends on" is the transitive but not reflexive closure of "depends directly on".</span></span>

#### <a name="output-type-inferences"></a><span data-ttu-id="b0787-632">Inférences de type de sortie</span><span class="sxs-lookup"><span data-stu-id="b0787-632">Output type inferences</span></span>

<span data-ttu-id="b0787-633">Une *inférence de type de sortie* est effectuée *à partir* d’une expression `E` *à* un type `T` de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="b0787-633">An *output type inference* is made *from* an expression `E` *to* a type `T` in the following way:</span></span>

*  <span data-ttu-id="b0787-634">Si `E` est une fonction anonyme avec un type de retour déduit `U`[(type de retour déduit](expressions.md#inferred-return-type)) et `T` est un type délégué ou un type d’arborescence d’expression avec le type de retour `Tb`, une *inférence à la limite inférieure* ([inférences à liaison inférieure](expressions.md#lower-bound-inferences)) est effectuée *de* `U` *à* `Tb`.</span><span class="sxs-lookup"><span data-stu-id="b0787-634">If `E` is an anonymous function with inferred return type  `U` ([Inferred return type](expressions.md#inferred-return-type)) and `T` is a delegate type or expression tree type with return type `Tb`, then a *lower-bound inference* ([Lower-bound inferences](expressions.md#lower-bound-inferences)) is made *from* `U` *to* `Tb`.</span></span>
*  <span data-ttu-id="b0787-635">Sinon, si `E` est un groupe de méthodes et que `T` est un type délégué ou un type d’arborescence d’expression avec des types de paramètres `T1...Tk` et `Tb`de type de retour, et que la résolution de surcharge de `E` avec les types `T1...Tk` produit une méthode unique avec le type de retour `U`, une *inférence de liaison inférieure* est effectuée *de* `U` *à* `Tb`.</span><span class="sxs-lookup"><span data-stu-id="b0787-635">Otherwise, if `E` is a method group and `T` is a delegate type or expression tree type with parameter types `T1...Tk` and return type `Tb`, and overload resolution of `E` with the types `T1...Tk` yields a single method with return type `U`, then a *lower-bound inference* is made *from* `U` *to* `Tb`.</span></span>
*  <span data-ttu-id="b0787-636">Sinon, si `E` est une expression de type `U`, une *inférence de liaison inférieure* est effectuée *de* `U` *à* `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-636">Otherwise, if `E` is an expression with type `U`, then a *lower-bound inference* is made *from* `U` *to* `T`.</span></span>
*  <span data-ttu-id="b0787-637">Dans le cas contraire, aucune inférence n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b0787-637">Otherwise, no inferences are made.</span></span>

#### <a name="explicit-parameter-type-inferences"></a><span data-ttu-id="b0787-638">Inférences de type de paramètre explicites</span><span class="sxs-lookup"><span data-stu-id="b0787-638">Explicit parameter type inferences</span></span>

<span data-ttu-id="b0787-639">Une *inférence de type de paramètre explicite* est effectuée *à partir* d’une expression `E` *à* un type `T` de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="b0787-639">An *explicit parameter type inference* is made *from* an expression `E` *to* a type `T` in the following way:</span></span>

*  <span data-ttu-id="b0787-640">Si `E` est une fonction anonyme explicitement typée avec des types de paramètres `U1...Uk` et `T` est un type délégué ou un type d’arborescence d’expression avec des types de paramètres `V1...Vk` puis pour chaque `Ui` une *inférence exacte* ([inférences exactes](expressions.md#exact-inferences)) est apportée *de* `Ui` *à* la `Vi`correspondante.</span><span class="sxs-lookup"><span data-stu-id="b0787-640">If `E` is an explicitly typed anonymous function with parameter types `U1...Uk` and `T` is a delegate type or expression tree type with parameter types `V1...Vk` then for each `Ui` an *exact inference* ([Exact inferences](expressions.md#exact-inferences)) is made *from* `Ui` *to* the corresponding `Vi`.</span></span>

#### <a name="exact-inferences"></a><span data-ttu-id="b0787-641">Inférences exactes</span><span class="sxs-lookup"><span data-stu-id="b0787-641">Exact inferences</span></span>

<span data-ttu-id="b0787-642">Une *inférence exacte* *d'* un type `U` *à* un type `V` est effectuée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-642">An *exact inference* *from* a type `U` *to* a type `V` is made as follows:</span></span>

*  <span data-ttu-id="b0787-643">Si `V` est l’un des `Xi` non *fixes* , `U` est ajouté au jeu de limites exactes pour `Xi`.</span><span class="sxs-lookup"><span data-stu-id="b0787-643">If `V` is one of the *unfixed* `Xi` then `U` is added to the set of exact bounds for `Xi`.</span></span>

*  <span data-ttu-id="b0787-644">Dans le cas contraire, définit `V1...Vk` et `U1...Uk` sont déterminés en vérifiant si l’un des cas suivants s’applique :</span><span class="sxs-lookup"><span data-stu-id="b0787-644">Otherwise, sets `V1...Vk` and `U1...Uk` are determined by checking if any of the following cases apply:</span></span>

   *  <span data-ttu-id="b0787-645">`V` est un type de tableau `V1[...]` et `U` est un type de tableau `U1[...]` du même rang</span><span class="sxs-lookup"><span data-stu-id="b0787-645">`V` is an array type `V1[...]` and `U` is an array type `U1[...]`  of the same rank</span></span>
   *  <span data-ttu-id="b0787-646">`V` est le type `V1?` et `U` est le type `U1?`</span><span class="sxs-lookup"><span data-stu-id="b0787-646">`V` is the type `V1?` and `U` is the type `U1?`</span></span>
   *  <span data-ttu-id="b0787-647">`V` est un type construit `C<V1...Vk>`et `U` est un type construit `C<U1...Uk>`</span><span class="sxs-lookup"><span data-stu-id="b0787-647">`V` is a constructed type `C<V1...Vk>`and `U` is a constructed type `C<U1...Uk>`</span></span>

   <span data-ttu-id="b0787-648">Si l’un de ces cas s’applique, une *inférence exacte* est effectuée *à partir de* chaque `Ui` *vers* le `Vi`correspondant.</span><span class="sxs-lookup"><span data-stu-id="b0787-648">If any of these cases apply then an *exact inference* is made *from* each `Ui` *to* the corresponding `Vi`.</span></span>

*  <span data-ttu-id="b0787-649">Sinon, aucune inférence n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b0787-649">Otherwise no inferences are made.</span></span>

#### <a name="lower-bound-inferences"></a><span data-ttu-id="b0787-650">Inférences à limite inférieure</span><span class="sxs-lookup"><span data-stu-id="b0787-650">Lower-bound inferences</span></span>

<span data-ttu-id="b0787-651">Une *inférence de liaison inférieure* *d'* un type `U` *à* un type `V` est effectuée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-651">A *lower-bound inference* *from* a type `U` *to* a type `V` is made as follows:</span></span>

*  <span data-ttu-id="b0787-652">Si `V` est l’un des `Xi` non *fixes* , `U` est ajouté au jeu de limites inférieures pour `Xi`.</span><span class="sxs-lookup"><span data-stu-id="b0787-652">If `V` is one of the *unfixed* `Xi` then `U` is added to the set of lower bounds for `Xi`.</span></span>
*  <span data-ttu-id="b0787-653">Sinon, si `V` est le type `V1?`et `U` est le type `U1?`, une inférence de limite inférieure est effectuée de `U1` à `V1`.</span><span class="sxs-lookup"><span data-stu-id="b0787-653">Otherwise, if `V` is the type `V1?`and `U` is the type `U1?` then a lower bound inference is made from `U1` to `V1`.</span></span>
*  <span data-ttu-id="b0787-654">Dans le cas contraire, définit `U1...Uk` et `V1...Vk` sont déterminés en vérifiant si l’un des cas suivants s’applique :</span><span class="sxs-lookup"><span data-stu-id="b0787-654">Otherwise, sets `U1...Uk` and `V1...Vk` are determined by checking if any of the following cases apply:</span></span>
   *  <span data-ttu-id="b0787-655">`V` est un type de tableau `V1[...]` et `U` est un type de tableau `U1[...]` (ou un paramètre de type dont le type de base effectif est `U1[...]`) du même rang</span><span class="sxs-lookup"><span data-stu-id="b0787-655">`V` is an array type `V1[...]` and `U` is an array type `U1[...]` (or a type parameter whose effective base type is `U1[...]`) of the same rank</span></span>
   *  <span data-ttu-id="b0787-656">`V` est l’un des `IEnumerable<V1>`, `ICollection<V1>` ou `IList<V1>` et `U` est un type tableau unidimensionnel `U1[]`(ou un paramètre de type dont le type de base effectif est `U1[]`)</span><span class="sxs-lookup"><span data-stu-id="b0787-656">`V` is one of `IEnumerable<V1>`, `ICollection<V1>` or `IList<V1>` and `U` is a one-dimensional array type `U1[]`(or a type parameter whose effective base type is `U1[]`)</span></span>
   *  <span data-ttu-id="b0787-657">`V` est une classe, un struct, une interface ou un type délégué construit `C<V1...Vk>` et il existe un type unique `C<U1...Uk>` tel que `U` (ou, si `U` est un paramètre de type, sa classe de base effective ou un membre de son ensemble d’interfaces effectif) est identique à, hérite de (directement ou indirectement) ou implémente (directement ou indirectement) `C<U1...Uk>`.</span><span class="sxs-lookup"><span data-stu-id="b0787-657">`V` is a constructed class, struct, interface or delegate type `C<V1...Vk>` and there is a unique type `C<U1...Uk>` such that `U` (or, if `U` is a type parameter, its effective base class or any member of its effective interface set) is identical to, inherits from (directly or indirectly), or implements (directly or indirectly) `C<U1...Uk>`.</span></span>

      <span data-ttu-id="b0787-658">(La restriction « unicité » signifie que, dans l’interface de cas `C<T> {} class U: C<X>, C<Y> {}`, aucune inférence n’est effectuée lors de l’inférence de `U` à `C<T>`, car `U1` peut être `X` ou `Y`.)</span><span class="sxs-lookup"><span data-stu-id="b0787-658">(The "uniqueness" restriction means that in the case interface `C<T> {} class U: C<X>, C<Y> {}`, then no inference is made when inferring from `U` to `C<T>` because `U1` could be `X` or `Y`.)</span></span>

   <span data-ttu-id="b0787-659">Si l’un de ces cas s’applique, une inférence est effectuée *à partir de* chaque `Ui` *vers* le `Vi` correspondant, comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-659">If any of these cases apply then an inference is made *from* each `Ui` *to* the corresponding `Vi` as follows:</span></span>

   *  <span data-ttu-id="b0787-660">Si `Ui` n’est pas connu comme étant un type référence, une *inférence exacte* est effectuée</span><span class="sxs-lookup"><span data-stu-id="b0787-660">If `Ui` is not known to be a reference type then an *exact inference* is made</span></span>
   *  <span data-ttu-id="b0787-661">Sinon, si `U` est un type tableau, une *inférence de liaison inférieure* est effectuée</span><span class="sxs-lookup"><span data-stu-id="b0787-661">Otherwise, if `U` is an array type then a *lower-bound inference* is made</span></span>
   *  <span data-ttu-id="b0787-662">Sinon, si `V` est `C<V1...Vk>`, l’inférence dépend du paramètre de type i-Th de `C`:</span><span class="sxs-lookup"><span data-stu-id="b0787-662">Otherwise, if `V` is `C<V1...Vk>` then inference depends on the i-th type parameter of `C`:</span></span>
      *  <span data-ttu-id="b0787-663">S’il s’agit d’un covariant, une *inférence de liaison inférieure* est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b0787-663">If it is covariant then a *lower-bound inference* is made.</span></span>
      *  <span data-ttu-id="b0787-664">S’il est contravariant, une *inférence de* la limite supérieure est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b0787-664">If it is contravariant then an *upper-bound inference* is made.</span></span>
      *  <span data-ttu-id="b0787-665">S’il s’agit d’un invariant, une *inférence exacte* est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b0787-665">If it is invariant then an *exact inference* is made.</span></span>
*  <span data-ttu-id="b0787-666">Dans le cas contraire, aucune inférence n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b0787-666">Otherwise, no inferences are made.</span></span>

#### <a name="upper-bound-inferences"></a><span data-ttu-id="b0787-667">Inférences à la limite supérieure</span><span class="sxs-lookup"><span data-stu-id="b0787-667">Upper-bound inferences</span></span>

<span data-ttu-id="b0787-668">Une *inférence à la limite supérieure* *d'* un type `U` *à* un type `V` est effectuée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-668">An *upper-bound inference* *from* a type `U` *to* a type `V` is made as follows:</span></span>

*  <span data-ttu-id="b0787-669">Si `V` est l’un des `Xi` non *fixes* , `U` est ajouté au jeu de limites supérieures pour `Xi`.</span><span class="sxs-lookup"><span data-stu-id="b0787-669">If `V` is one of the *unfixed* `Xi` then `U` is added to the set of upper bounds for `Xi`.</span></span>
*  <span data-ttu-id="b0787-670">Dans le cas contraire, définit `V1...Vk` et `U1...Uk` sont déterminés en vérifiant si l’un des cas suivants s’applique :</span><span class="sxs-lookup"><span data-stu-id="b0787-670">Otherwise, sets `V1...Vk` and `U1...Uk` are determined by checking if any of the following cases apply:</span></span>
   *  <span data-ttu-id="b0787-671">`U` est un type de tableau `U1[...]` et `V` est un type de tableau `V1[...]` du même rang</span><span class="sxs-lookup"><span data-stu-id="b0787-671">`U` is an array type `U1[...]` and `V` is an array type `V1[...]` of the same rank</span></span>
   *  <span data-ttu-id="b0787-672">`U` est l’un des `IEnumerable<Ue>`, `ICollection<Ue>` ou `IList<Ue>` et `V` est un type de tableau unidimensionnel `Ve[]`</span><span class="sxs-lookup"><span data-stu-id="b0787-672">`U` is one of `IEnumerable<Ue>`, `ICollection<Ue>` or `IList<Ue>` and `V` is a one-dimensional array type `Ve[]`</span></span>
   *  <span data-ttu-id="b0787-673">`U` est le type `U1?` et `V` est le type `V1?`</span><span class="sxs-lookup"><span data-stu-id="b0787-673">`U` is the type `U1?` and `V` is the type `V1?`</span></span>
   *  <span data-ttu-id="b0787-674">`U` est construit classe, struct, interface ou type délégué `C<U1...Uk>` et `V` est une classe, un struct, une interface ou un type délégué qui est identique à, hérite de (directement ou indirectement), ou implémente (directement ou indirectement) un type unique `C<V1...Vk>`</span><span class="sxs-lookup"><span data-stu-id="b0787-674">`U` is constructed class, struct, interface or delegate type `C<U1...Uk>` and `V` is a class, struct, interface or delegate type which is identical to, inherits from (directly or indirectly), or implements (directly or indirectly) a unique type `C<V1...Vk>`</span></span>

      <span data-ttu-id="b0787-675">(La restriction d’unicité signifie que si nous avons `interface C<T>{} class V<Z>: C<X<Z>>, C<Y<Z>>{}`, aucune inférence n’est effectuée lors de l’inférence de `C<U1>` à `V<Q>`.</span><span class="sxs-lookup"><span data-stu-id="b0787-675">(The "uniqueness" restriction means that if we have `interface C<T>{} class V<Z>: C<X<Z>>, C<Y<Z>>{}`, then no inference is made when inferring from `C<U1>` to `V<Q>`.</span></span> <span data-ttu-id="b0787-676">Les inférences ne sont pas faites de `U1` à `X<Q>` ou `Y<Q>`.)</span><span class="sxs-lookup"><span data-stu-id="b0787-676">Inferences are not made from `U1` to either `X<Q>` or `Y<Q>`.)</span></span>

   <span data-ttu-id="b0787-677">Si l’un de ces cas s’applique, une inférence est effectuée *à partir de* chaque `Ui` *vers* le `Vi` correspondant, comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-677">If any of these cases apply then an inference is made *from* each `Ui` *to* the corresponding `Vi` as follows:</span></span>
   *  <span data-ttu-id="b0787-678">Si `Ui` n’est pas connu comme étant un type référence, une *inférence exacte* est effectuée</span><span class="sxs-lookup"><span data-stu-id="b0787-678">If  `Ui` is not known to be a reference type then an *exact inference* is made</span></span>
   *  <span data-ttu-id="b0787-679">Sinon, si `V` est un type tableau, une *inférence à la limite supérieure* est effectuée</span><span class="sxs-lookup"><span data-stu-id="b0787-679">Otherwise, if `V` is an array type then an *upper-bound inference* is made</span></span>
   *  <span data-ttu-id="b0787-680">Sinon, si `U` est `C<U1...Uk>`, l’inférence dépend du paramètre de type i-Th de `C`:</span><span class="sxs-lookup"><span data-stu-id="b0787-680">Otherwise, if `U` is `C<U1...Uk>` then inference depends on the i-th type parameter of `C`:</span></span>
      *  <span data-ttu-id="b0787-681">S’il s’agit d’un covariant, une *inférence de* la limite supérieure est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b0787-681">If it is covariant then an *upper-bound inference* is made.</span></span>
      *  <span data-ttu-id="b0787-682">S’il est contravariant, une *inférence de liaison inférieure* est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b0787-682">If it is contravariant then a *lower-bound inference* is made.</span></span>
      *  <span data-ttu-id="b0787-683">S’il s’agit d’un invariant, une *inférence exacte* est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b0787-683">If it is invariant then an *exact inference* is made.</span></span>
*  <span data-ttu-id="b0787-684">Dans le cas contraire, aucune inférence n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b0787-684">Otherwise, no inferences are made.</span></span>   

#### <a name="fixing"></a><span data-ttu-id="b0787-685">Résolution</span><span class="sxs-lookup"><span data-stu-id="b0787-685">Fixing</span></span>

<span data-ttu-id="b0787-686">Une variable de type non *fixe* `Xi` avec un ensemble de limites est *fixe* comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-686">An *unfixed* type variable `Xi` with a set of bounds is *fixed* as follows:</span></span>

*  <span data-ttu-id="b0787-687">L’ensemble de *types candidats* `Uj` commence par le jeu de tous les types dans le jeu de limites de `Xi`.</span><span class="sxs-lookup"><span data-stu-id="b0787-687">The set of *candidate types* `Uj` starts out as the set of all types in the set of bounds for `Xi`.</span></span>
*  <span data-ttu-id="b0787-688">Nous examinons ensuite chaque limite pour `Xi` à son tour : pour chaque `U` à liaison exacte de `Xi` tous les types `Uj` qui ne sont pas identiques à `U` sont supprimés du jeu de candidats.</span><span class="sxs-lookup"><span data-stu-id="b0787-688">We then examine each bound for `Xi` in turn: For each exact bound `U` of `Xi` all types `Uj` which are not identical to `U` are removed from the candidate set.</span></span> <span data-ttu-id="b0787-689">Pour chaque `U` de limite inférieure de `Xi` tous les types `Uj` pour lesquels il n’existe *pas* de conversion implicite de `U` sont supprimés du jeu de candidats.</span><span class="sxs-lookup"><span data-stu-id="b0787-689">For each lower bound `U` of `Xi` all types `Uj` to which there is *not* an implicit conversion from `U` are removed from the candidate set.</span></span> <span data-ttu-id="b0787-690">Pour chaque `U` de la limite supérieure de `Xi` tous les types `Uj` à partir duquel il n’existe *pas* de conversion implicite en `U` sont supprimés du jeu de candidats.</span><span class="sxs-lookup"><span data-stu-id="b0787-690">For each upper bound `U` of `Xi` all types `Uj` from which there is *not* an implicit conversion to `U` are removed from the candidate set.</span></span>
*  <span data-ttu-id="b0787-691">Si parmi les types candidats restants `Uj` il existe un type unique `V` à partir duquel il existe une conversion implicite vers tous les autres types candidats, `Xi` est fixé à `V`.</span><span class="sxs-lookup"><span data-stu-id="b0787-691">If among the remaining candidate types `Uj` there is a unique type `V` from which there is an implicit conversion to all the other candidate types, then `Xi` is fixed to `V`.</span></span>
*  <span data-ttu-id="b0787-692">Sinon, l’inférence de type échoue.</span><span class="sxs-lookup"><span data-stu-id="b0787-692">Otherwise, type inference fails.</span></span>

#### <a name="inferred-return-type"></a><span data-ttu-id="b0787-693">Type de retour déduit</span><span class="sxs-lookup"><span data-stu-id="b0787-693">Inferred return type</span></span>

<span data-ttu-id="b0787-694">Le type de retour déduit d’une fonction anonyme `F` est utilisé pendant l’inférence de type et la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="b0787-694">The inferred return type of an anonymous function `F` is used during type inference and overload resolution.</span></span> <span data-ttu-id="b0787-695">Le type de retour déduit ne peut être déterminé que pour une fonction anonyme dans laquelle tous les types de paramètre sont connus, soit parce qu’ils sont explicitement donnés, fournis par le biais d’une conversion de fonction anonyme ou déduits pendant l’inférence de type sur un générique englobant appel de méthode.</span><span class="sxs-lookup"><span data-stu-id="b0787-695">The inferred return type can only be determined for an anonymous function where all parameter types are known, either because they are explicitly given, provided through an anonymous function conversion or inferred during type inference on an enclosing generic method invocation.</span></span>

<span data-ttu-id="b0787-696">Le ***type de résultat inféré*** est déterminé comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-696">The ***inferred result type*** is determined as follows:</span></span>

*  <span data-ttu-id="b0787-697">Si le corps de `F` est une *expression* qui a un type, le type de résultat déduit de `F` est le type de cette expression.</span><span class="sxs-lookup"><span data-stu-id="b0787-697">If the body of `F` is an *expression* that has a type, then the inferred result type of `F` is the type of that expression.</span></span>
*  <span data-ttu-id="b0787-698">Si le corps de `F` est un *bloc* et que l’ensemble d’expressions dans les instructions `return` du bloc a un type le mieux courant `T` ([recherche du meilleur type commun d’un ensemble d’expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)), le type de résultat déduit de `F` est `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-698">If the body of `F` is a *block* and the set of expressions in the block's `return` statements has a best common type `T` ([Finding the best common type of a set of expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)), then the inferred result type of `F` is `T`.</span></span>
*  <span data-ttu-id="b0787-699">Dans le cas contraire, un type de résultat ne peut pas être déduit pour `F`.</span><span class="sxs-lookup"><span data-stu-id="b0787-699">Otherwise, a result type cannot be inferred for `F`.</span></span>

<span data-ttu-id="b0787-700">Le ***type de retour déduit*** est déterminé comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-700">The ***inferred return type*** is determined as follows:</span></span>

*  <span data-ttu-id="b0787-701">Si `F` est Async et que le corps de `F` est une expression classifiée comme Nothing ([classifications d’expression](expressions.md#expression-classifications)) ou un bloc d’instructions où aucune instruction return n’a d’expressions, le type de retour déduit est `System.Threading.Tasks.Task`</span><span class="sxs-lookup"><span data-stu-id="b0787-701">If `F` is async and the body of `F` is either an expression classified as nothing ([Expression classifications](expressions.md#expression-classifications)), or a statement block where no return statements have expressions, the inferred return type is `System.Threading.Tasks.Task`</span></span>
*  <span data-ttu-id="b0787-702">Si `F` est asynchrone et a un type de résultat déduit `T`, le type de retour déduit est `System.Threading.Tasks.Task<T>`.</span><span class="sxs-lookup"><span data-stu-id="b0787-702">If `F` is async and has an inferred result type `T`, the inferred return type is `System.Threading.Tasks.Task<T>`.</span></span>
*  <span data-ttu-id="b0787-703">Si `F` est non asynchrone et a un type de résultat déduit `T`, le type de retour déduit est `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-703">If `F` is non-async and has an inferred result type `T`, the inferred return type is `T`.</span></span>
*  <span data-ttu-id="b0787-704">Dans le cas contraire, un type de retour ne peut pas être déduit pour `F`.</span><span class="sxs-lookup"><span data-stu-id="b0787-704">Otherwise a return type cannot be inferred for `F`.</span></span>

<span data-ttu-id="b0787-705">En guise d’exemple d’inférence de type impliquant des fonctions anonymes, considérez la méthode d’extension `Select` déclarée dans la classe `System.Linq.Enumerable` :</span><span class="sxs-lookup"><span data-stu-id="b0787-705">As an example of type inference involving anonymous functions, consider the `Select` extension method declared in the `System.Linq.Enumerable` class:</span></span>
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

<span data-ttu-id="b0787-706">En supposant que l’espace de noms `System.Linq` a été importé avec une clause `using` et une classe `Customer` avec une propriété `Name` de type `string`, la méthode `Select` peut être utilisée pour sélectionner les noms d’une liste de clients :</span><span class="sxs-lookup"><span data-stu-id="b0787-706">Assuming the `System.Linq` namespace was imported with a `using` clause, and given a class `Customer` with a `Name` property of type `string`, the `Select` method can be used to select the names of a list of customers:</span></span>
```csharp
List<Customer> customers = GetCustomerList();
IEnumerable<string> names = customers.Select(c => c.Name);
```

<span data-ttu-id="b0787-707">L’appel de méthode d’extension ([appel de méthode d’extension](expressions.md#extension-method-invocations)) de `Select` est traité en réécrivant l’appel à un appel de méthode statique :</span><span class="sxs-lookup"><span data-stu-id="b0787-707">The extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)) of `Select` is processed by rewriting the invocation to a static method invocation:</span></span>
```csharp
IEnumerable<string> names = Enumerable.Select(customers, c => c.Name);
```

<span data-ttu-id="b0787-708">Étant donné que les arguments de type n’ont pas été spécifiés explicitement, l’inférence de type est utilisée pour déduire les arguments de type.</span><span class="sxs-lookup"><span data-stu-id="b0787-708">Since type arguments were not explicitly specified, type inference is used to infer the type arguments.</span></span> <span data-ttu-id="b0787-709">Tout d’abord, l’argument `customers` est lié au paramètre `source`, ce qui déduit `T` à `Customer`.</span><span class="sxs-lookup"><span data-stu-id="b0787-709">First, the `customers` argument is related to the `source` parameter, inferring `T` to be `Customer`.</span></span> <span data-ttu-id="b0787-710">Ensuite, à l’aide du processus d’inférence de type de fonction anonyme décrit ci-dessus, `c` reçoit le type `Customer`, et l’expression `c.Name` est liée au type de retour du paramètre `selector`, en déduisant `S` à `string`.</span><span class="sxs-lookup"><span data-stu-id="b0787-710">Then, using the anonymous function type inference process described above, `c` is given type `Customer`, and the expression `c.Name` is related to the return type of the `selector` parameter, inferring `S` to be `string`.</span></span> <span data-ttu-id="b0787-711">Ainsi, l’appel est équivalent à</span><span class="sxs-lookup"><span data-stu-id="b0787-711">Thus, the invocation is equivalent to</span></span>
```csharp
Sequence.Select<Customer,string>(customers, (Customer c) => c.Name)
```
<span data-ttu-id="b0787-712">et le résultat est de type `IEnumerable<string>`.</span><span class="sxs-lookup"><span data-stu-id="b0787-712">and the result is of type `IEnumerable<string>`.</span></span>

<span data-ttu-id="b0787-713">L’exemple suivant montre comment l’inférence de type de fonction anonyme permet aux informations de type de « circuler » entre les arguments dans un appel de méthode générique.</span><span class="sxs-lookup"><span data-stu-id="b0787-713">The following example demonstrates how anonymous function type inference allows type information to "flow" between arguments in a generic method invocation.</span></span> <span data-ttu-id="b0787-714">À partir de la méthode :</span><span class="sxs-lookup"><span data-stu-id="b0787-714">Given the method:</span></span>
```csharp
static Z F<X,Y,Z>(X value, Func<X,Y> f1, Func<Y,Z> f2) {
    return f2(f1(value));
}
```

<span data-ttu-id="b0787-715">Inférence de type pour l’appel :</span><span class="sxs-lookup"><span data-stu-id="b0787-715">Type inference for the invocation:</span></span>
```csharp
double seconds = F("1:15:30", s => TimeSpan.Parse(s), t => t.TotalSeconds);
```
<span data-ttu-id="b0787-716">continue comme suit : tout d’abord, l’argument `"1:15:30"` est lié au paramètre `value`, en déduisant les `X` à `string`.</span><span class="sxs-lookup"><span data-stu-id="b0787-716">proceeds as follows: First, the argument `"1:15:30"` is related to the `value` parameter, inferring `X` to be `string`.</span></span> <span data-ttu-id="b0787-717">Ensuite, le paramètre de la première fonction anonyme, `s`, reçoit le type déduit `string`, et l’expression `TimeSpan.Parse(s)` est liée au type de retour de `f1`, en déduisant `Y` à `System.TimeSpan`.</span><span class="sxs-lookup"><span data-stu-id="b0787-717">Then, the parameter of the first anonymous function, `s`, is given the inferred type `string`, and the expression `TimeSpan.Parse(s)` is related to the return type of `f1`, inferring `Y` to be `System.TimeSpan`.</span></span> <span data-ttu-id="b0787-718">Enfin, le paramètre de la deuxième fonction anonyme, `t`, reçoit le type déduit `System.TimeSpan`, et l’expression `t.TotalSeconds` est liée au type de retour de `f2`, en déduisant `Z` à `double`.</span><span class="sxs-lookup"><span data-stu-id="b0787-718">Finally, the parameter of the second anonymous function, `t`, is given the inferred type `System.TimeSpan`, and the expression `t.TotalSeconds` is related to the return type of `f2`, inferring `Z` to be `double`.</span></span> <span data-ttu-id="b0787-719">Ainsi, le résultat de l’appel est de type `double`.</span><span class="sxs-lookup"><span data-stu-id="b0787-719">Thus, the result of the invocation is of type `double`.</span></span>

#### <a name="type-inference-for-conversion-of-method-groups"></a><span data-ttu-id="b0787-720">Inférence de type pour la conversion de groupes de méthodes</span><span class="sxs-lookup"><span data-stu-id="b0787-720">Type inference for conversion of method groups</span></span>

<span data-ttu-id="b0787-721">Comme pour les appels de méthodes génériques, l’inférence de type doit également être appliquée quand un groupe de méthodes `M` contenant une méthode générique est converti en un type délégué donné `D` ([conversions de groupe de méthodes](conversions.md#method-group-conversions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-721">Similar to calls of generic methods, type inference must also be applied when a method group `M` containing a generic method is converted to a given delegate type `D` ([Method group conversions](conversions.md#method-group-conversions)).</span></span> <span data-ttu-id="b0787-722">Pour une méthode donnée</span><span class="sxs-lookup"><span data-stu-id="b0787-722">Given a method</span></span>
```csharp
Tr M<X1...Xn>(T1 x1 ... Tm xm)
```
<span data-ttu-id="b0787-723">et le groupe de méthodes `M` assigné au type délégué `D` la tâche de l’inférence de type consiste à rechercher des arguments de type `S1...Sn` afin que l’expression :</span><span class="sxs-lookup"><span data-stu-id="b0787-723">and the method group `M` being assigned to the delegate type `D` the task of type inference is to find type arguments `S1...Sn` so that the expression:</span></span>
```csharp
M<S1...Sn>
```
<span data-ttu-id="b0787-724">devient compatible ([déclarations déléguées](delegates.md#delegate-declarations)) avec `D`.</span><span class="sxs-lookup"><span data-stu-id="b0787-724">becomes compatible ([Delegate declarations](delegates.md#delegate-declarations)) with `D`.</span></span>

<span data-ttu-id="b0787-725">Contrairement à l’algorithme d’inférence de type pour les appels de méthode générique, dans ce cas, il n’y a que des *types*d’arguments, aucune *expression*d’argument.</span><span class="sxs-lookup"><span data-stu-id="b0787-725">Unlike the type inference algorithm for generic method calls, in this case there are only argument *types*, no argument *expressions*.</span></span> <span data-ttu-id="b0787-726">En particulier, il n’y a pas de fonctions anonymes et, par conséquent, n’a pas besoin de plusieurs phases d’inférence.</span><span class="sxs-lookup"><span data-stu-id="b0787-726">In particular, there are no anonymous functions and hence no need for multiple phases of inference.</span></span>

<span data-ttu-id="b0787-727">Au lieu de cela, tous les `Xi` sont considérés comme non *résolus*et une *inférence de liaison inférieure* est effectuée *à partir* de chaque type d’argument `Uj` de `D` *au* type de paramètre correspondant `Tj` de `M`.</span><span class="sxs-lookup"><span data-stu-id="b0787-727">Instead, all `Xi` are considered *unfixed*, and a *lower-bound inference* is made *from* each argument type `Uj` of `D` *to* the corresponding parameter type `Tj` of `M`.</span></span> <span data-ttu-id="b0787-728">Si pour l’une des `Xi` aucune limite n’a été trouvée, l’inférence de type échoue.</span><span class="sxs-lookup"><span data-stu-id="b0787-728">If for any of the `Xi` no bounds were found, type inference fails.</span></span> <span data-ttu-id="b0787-729">Dans le cas contraire, toutes les `Xi` sont *fixes* à la `Si`correspondante, qui sont le résultat de l’inférence de type.</span><span class="sxs-lookup"><span data-stu-id="b0787-729">Otherwise, all `Xi` are *fixed* to corresponding `Si`, which are the result of type inference.</span></span>

#### <a name="finding-the-best-common-type-of-a-set-of-expressions"></a><span data-ttu-id="b0787-730">Recherche du meilleur type commun d’un ensemble d’expressions</span><span class="sxs-lookup"><span data-stu-id="b0787-730">Finding the best common type of a set of expressions</span></span>

<span data-ttu-id="b0787-731">Dans certains cas, un type commun doit être déduit pour un ensemble d’expressions.</span><span class="sxs-lookup"><span data-stu-id="b0787-731">In some cases, a common type needs to be inferred for a set of expressions.</span></span> <span data-ttu-id="b0787-732">En particulier, les types d’éléments des tableaux implicitement typés et les types de retour des fonctions anonymes avec des corps de *bloc* sont détectés de cette façon.</span><span class="sxs-lookup"><span data-stu-id="b0787-732">In particular, the element types of implicitly typed arrays and the return types of anonymous functions with *block* bodies are found in this way.</span></span>

<span data-ttu-id="b0787-733">Intuitivement, étant donné un ensemble d’expressions `E1...Em` cette inférence doit être équivalente à l’appel d’une méthode</span><span class="sxs-lookup"><span data-stu-id="b0787-733">Intuitively, given a set of expressions `E1...Em` this inference should be equivalent to calling a method</span></span>
```csharp
Tr M<X>(X x1 ... X xm)
```
<span data-ttu-id="b0787-734">avec les `Ei` comme arguments.</span><span class="sxs-lookup"><span data-stu-id="b0787-734">with the `Ei` as arguments.</span></span>

<span data-ttu-id="b0787-735">Plus précisément, l’inférence commence par une variable de type non *fixe* `X`.</span><span class="sxs-lookup"><span data-stu-id="b0787-735">More precisely, the inference starts out with an *unfixed* type variable `X`.</span></span> <span data-ttu-id="b0787-736">Les *inférences de type de sortie* sont ensuite effectuées *à partir de* chaque `Ei` *vers* `X`.</span><span class="sxs-lookup"><span data-stu-id="b0787-736">*Output type inferences* are then made *from* each `Ei` *to* `X`.</span></span> <span data-ttu-id="b0787-737">Enfin, `X` est *fixe* et, en cas de réussite, le type résultant `S` est le meilleur type commun qui en résulte pour les expressions.</span><span class="sxs-lookup"><span data-stu-id="b0787-737">Finally, `X` is *fixed* and, if successful, the resulting type `S` is the resulting best common type for the expressions.</span></span> <span data-ttu-id="b0787-738">Si ce `S` n’existe pas, les expressions n’ont pas le meilleur type commun.</span><span class="sxs-lookup"><span data-stu-id="b0787-738">If no such `S` exists, the expressions have no best common type.</span></span>

### <a name="overload-resolution"></a><span data-ttu-id="b0787-739">Résolution de surcharge</span><span class="sxs-lookup"><span data-stu-id="b0787-739">Overload resolution</span></span>

<span data-ttu-id="b0787-740">La résolution de surcharge est un mécanisme de liaison-temps permettant de sélectionner le meilleur membre de fonction à appeler à partir d’une liste d’arguments et d’un jeu de membres de fonction candidats.</span><span class="sxs-lookup"><span data-stu-id="b0787-740">Overload resolution is a binding-time mechanism for selecting the best function member to invoke given an argument list and a set of candidate function members.</span></span> <span data-ttu-id="b0787-741">La résolution de surcharge sélectionne la fonction membre à appeler dans les contextes suivants C#dans :</span><span class="sxs-lookup"><span data-stu-id="b0787-741">Overload resolution selects the function member to invoke in the following distinct contexts within C#:</span></span>

*  <span data-ttu-id="b0787-742">Appel d’une méthode nommée dans un *invocation_expression* ([appels de méthode](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="b0787-742">Invocation of a method named in an *invocation_expression* ([Method invocations](expressions.md#method-invocations)).</span></span>
*  <span data-ttu-id="b0787-743">Appel d’un constructeur d’instance nommé dans une *object_creation_expression* ([expressions de création d’objet](expressions.md#object-creation-expressions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-743">Invocation of an instance constructor named in an *object_creation_expression* ([Object creation expressions](expressions.md#object-creation-expressions)).</span></span>
*  <span data-ttu-id="b0787-744">Appel d’un accesseur d’indexeur via un *element_access* ([accès à l’élément](expressions.md#element-access)).</span><span class="sxs-lookup"><span data-stu-id="b0787-744">Invocation of an indexer accessor through an *element_access* ([Element access](expressions.md#element-access)).</span></span>
*  <span data-ttu-id="b0787-745">Appel d’un opérateur prédéfini ou défini par l’utilisateur référencé dans une expression (résolution de[surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution) et [résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="b0787-745">Invocation of a predefined or user-defined operator referenced in an expression ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution) and [Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)).</span></span>

<span data-ttu-id="b0787-746">Chacun de ces contextes définit l’ensemble des membres de fonction candidats et la liste d’arguments de manière unique, comme décrit en détail dans les sections répertoriées ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="b0787-746">Each of these contexts defines the set of candidate function members and the list of arguments in its own unique way, as described in detail in the sections listed above.</span></span> <span data-ttu-id="b0787-747">Par exemple, l’ensemble de candidats pour un appel de méthode n’inclut pas les méthodes marquées `override` ([recherche de membre](expressions.md#member-lookup)) et les méthodes d’une classe de base ne sont pas candidates si une méthode dans une classe dérivée est applicable ([appels de méthode](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="b0787-747">For example, the set of candidates for a method invocation does not include methods marked `override` ([Member lookup](expressions.md#member-lookup)), and methods in a base class are not candidates if any method in a derived class is applicable ([Method invocations](expressions.md#method-invocations)).</span></span>

<span data-ttu-id="b0787-748">Une fois que les membres de la fonction candidate et la liste d’arguments ont été identifiés, la sélection du meilleur membre de la fonction est la même dans tous les cas :</span><span class="sxs-lookup"><span data-stu-id="b0787-748">Once the candidate function members and the argument list have been identified, the selection of the best function member is the same in all cases:</span></span>

*  <span data-ttu-id="b0787-749">Compte tenu de l’ensemble des fonctions membres candidates applicables, le meilleur membre de fonction de cet ensemble est localisé.</span><span class="sxs-lookup"><span data-stu-id="b0787-749">Given the set of applicable candidate function members, the best function member in that set is located.</span></span> <span data-ttu-id="b0787-750">Si le jeu contient un seul membre de fonction, ce membre de fonction est le meilleur membre de fonction.</span><span class="sxs-lookup"><span data-stu-id="b0787-750">If the set contains only one function member, then that function member is the best function member.</span></span> <span data-ttu-id="b0787-751">Dans le cas contraire, le meilleur membre de fonction est le membre de fonction qui est mieux que tous les autres membres de fonction par rapport à la liste d’arguments donnée, à condition que chaque membre de fonction soit comparé à tous les autres membres de fonction en utilisant les règles dans un [meilleur membre](expressions.md#better-function-member)de fonction.</span><span class="sxs-lookup"><span data-stu-id="b0787-751">Otherwise, the best function member is the one function member that is better than all other function members with respect to the given argument list, provided that each function member is compared to all other function members using the rules in [Better function member](expressions.md#better-function-member).</span></span> <span data-ttu-id="b0787-752">S’il n’y a pas exactement un membre de fonction qui est mieux que tous les autres membres de fonction, l’appel de membre de fonction est ambigu et une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-752">If there is not exactly one function member that is better than all other function members, then the function member invocation is ambiguous and a binding-time error occurs.</span></span>

<span data-ttu-id="b0787-753">Les sections suivantes définissent les significations exactes des termes du ***membre de fonction applicable*** et du ***meilleur membre de fonction***.</span><span class="sxs-lookup"><span data-stu-id="b0787-753">The following sections define the exact meanings of the terms ***applicable function member*** and ***better function member***.</span></span>

#### <a name="applicable-function-member"></a><span data-ttu-id="b0787-754">Membre de fonction applicable</span><span class="sxs-lookup"><span data-stu-id="b0787-754">Applicable function member</span></span>

<span data-ttu-id="b0787-755">Un membre de fonction est considéré comme un ***membre de fonction applicable*** en ce qui concerne une liste d’arguments `A` lorsque toutes les conditions suivantes sont vraies :</span><span class="sxs-lookup"><span data-stu-id="b0787-755">A function member is said to be an ***applicable function member*** with respect to an argument list `A` when all of the following are true:</span></span>

*  <span data-ttu-id="b0787-756">Chaque argument de `A` correspond à un paramètre dans la déclaration de membre de fonction comme décrit dans les [paramètres correspondants](expressions.md#corresponding-parameters), et tout paramètre auquel aucun argument correspond est un paramètre facultatif.</span><span class="sxs-lookup"><span data-stu-id="b0787-756">Each argument in `A` corresponds to a parameter in the function member declaration as described in [Corresponding parameters](expressions.md#corresponding-parameters), and any parameter to which no argument corresponds is an optional parameter.</span></span>
*  <span data-ttu-id="b0787-757">Pour chaque argument dans `A`, le mode de passage de paramètre de l’argument (c’est-à-dire, value, `ref`ou `out`) est identique au mode de passage de paramètre du paramètre correspondant, et</span><span class="sxs-lookup"><span data-stu-id="b0787-757">For each argument in `A`, the parameter passing mode of the argument (i.e., value, `ref`, or `out`) is identical to the parameter passing mode of the corresponding parameter, and</span></span>
   *  <span data-ttu-id="b0787-758">pour un paramètre de valeur ou un tableau de paramètres, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir de l’argument vers le type du paramètre correspondant, ou</span><span class="sxs-lookup"><span data-stu-id="b0787-758">for a value parameter or a parameter array, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from the argument to the type of the corresponding parameter, or</span></span>
   *  <span data-ttu-id="b0787-759">pour un paramètre `ref` ou `out`, le type de l’argument est identique au type du paramètre correspondant.</span><span class="sxs-lookup"><span data-stu-id="b0787-759">for a `ref` or `out` parameter, the type of the argument is identical to the type of the corresponding parameter.</span></span> <span data-ttu-id="b0787-760">Après tout, un paramètre `ref` ou `out` est un alias pour l’argument passé.</span><span class="sxs-lookup"><span data-stu-id="b0787-760">After all, a `ref` or `out` parameter is an alias for the argument passed.</span></span>

<span data-ttu-id="b0787-761">Pour un membre de fonction qui comprend un tableau de paramètres, si le membre de fonction est applicable par les règles ci-dessus, il est dit s’il est applicable dans sa ***forme normale***.</span><span class="sxs-lookup"><span data-stu-id="b0787-761">For a function member that includes a parameter array, if the function member is applicable by the above rules, it is said to be applicable in its ***normal form***.</span></span> <span data-ttu-id="b0787-762">Si un membre de fonction qui comprend un tableau de paramètres n’est pas applicable dans sa forme normale, la fonction membre peut à la place être applicable dans sa ***forme développée***:</span><span class="sxs-lookup"><span data-stu-id="b0787-762">If a function member that includes a parameter array is not applicable in its normal form, the function member may instead be applicable in its ***expanded form***:</span></span>

*  <span data-ttu-id="b0787-763">La forme développée est construite en remplaçant le tableau de paramètres dans la déclaration de membre de fonction par zéro ou plusieurs paramètres de valeur du type d’élément du tableau de paramètres, de telle sorte que le nombre d’arguments dans la liste d’arguments `A` corresponde au nombre total de paramètres.</span><span class="sxs-lookup"><span data-stu-id="b0787-763">The expanded form is constructed by replacing the parameter array in the function member declaration with zero or more value parameters of the element type of the parameter array such that the number of arguments in the argument list `A` matches the total number of parameters.</span></span> <span data-ttu-id="b0787-764">Si `A` a moins d’arguments que le nombre de paramètres fixes dans la déclaration de membre de fonction, la forme développée de la fonction membre ne peut pas être construite et n’est donc pas applicable.</span><span class="sxs-lookup"><span data-stu-id="b0787-764">If `A` has fewer arguments than the number of fixed parameters in the function member declaration, the expanded form of the function member cannot be constructed and is thus not applicable.</span></span>
*  <span data-ttu-id="b0787-765">Dans le cas contraire, la forme développée s’applique si pour chaque argument dans `A` le mode de passage de paramètre de l’argument est identique au mode de passage de paramètre du paramètre correspondant, et</span><span class="sxs-lookup"><span data-stu-id="b0787-765">Otherwise, the expanded form is applicable if for each argument in `A` the parameter passing mode of the argument is identical to the parameter passing mode of the corresponding parameter, and</span></span>
   *  <span data-ttu-id="b0787-766">pour un paramètre de valeur fixe ou un paramètre de valeur créé par l’expansion, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir du type de l’argument vers le type du paramètre correspondant, ou</span><span class="sxs-lookup"><span data-stu-id="b0787-766">for a fixed value parameter or a value parameter created by the expansion, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from the type of the argument to the type of the corresponding parameter, or</span></span>
   *  <span data-ttu-id="b0787-767">pour un paramètre `ref` ou `out`, le type de l’argument est identique au type du paramètre correspondant.</span><span class="sxs-lookup"><span data-stu-id="b0787-767">for a `ref` or `out` parameter, the type of the argument is identical to the type of the corresponding parameter.</span></span>

#### <a name="better-function-member"></a><span data-ttu-id="b0787-768">Meilleure fonction membre</span><span class="sxs-lookup"><span data-stu-id="b0787-768">Better function member</span></span>

<span data-ttu-id="b0787-769">Pour déterminer le meilleur membre de la fonction, une liste d’arguments tronquée a est construite, contenant uniquement les expressions d’arguments, dans l’ordre dans lequel elles apparaissent dans la liste d’arguments d’origine.</span><span class="sxs-lookup"><span data-stu-id="b0787-769">For the purposes of determining the better function member, a stripped-down argument list A is constructed containing just the argument expressions themselves in the order they appear in the original argument list.</span></span>

<span data-ttu-id="b0787-770">Les listes de paramètres pour chacun des fonctions candidates sont construites de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="b0787-770">Parameter lists for each of the candidate function members are constructed in the following way:</span></span>

*  <span data-ttu-id="b0787-771">Le formulaire développé est utilisé si le membre de fonction ne s’applique qu’au formulaire développé.</span><span class="sxs-lookup"><span data-stu-id="b0787-771">The expanded form is used if the function member was applicable only in the expanded form.</span></span>
*  <span data-ttu-id="b0787-772">Les paramètres facultatifs sans arguments correspondants sont supprimés de la liste de paramètres</span><span class="sxs-lookup"><span data-stu-id="b0787-772">Optional parameters with no corresponding arguments are removed from the parameter list</span></span>
*  <span data-ttu-id="b0787-773">Les paramètres sont réorganisés afin qu’ils se produisent à la même position que l’argument correspondant dans la liste d’arguments.</span><span class="sxs-lookup"><span data-stu-id="b0787-773">The parameters are reordered so that they occur at the same position as the corresponding argument in the argument list.</span></span>

<span data-ttu-id="b0787-774">À partir d’une liste d’arguments `A` avec un ensemble d’expressions d’argument `{E1, E2, ..., En}` et deux membres de fonction applicables `Mp` et `Mq` avec des types de paramètres `{P1, P2, ..., Pn}` et `{Q1, Q2, ..., Qn}`, `Mp` est défini comme étant une ***meilleure fonction membre*** que `Mq` si</span><span class="sxs-lookup"><span data-stu-id="b0787-774">Given an argument list `A` with a set of argument expressions `{E1, E2, ..., En}` and two applicable function members `Mp` and `Mq` with parameter types `{P1, P2, ..., Pn}` and `{Q1, Q2, ..., Qn}`, `Mp` is defined to be a ***better function member*** than `Mq` if</span></span>

*  <span data-ttu-id="b0787-775">pour chaque argument, la conversion implicite de `Ex` en `Qx` n’est pas meilleure que la conversion implicite de `Ex` en `Px`, et</span><span class="sxs-lookup"><span data-stu-id="b0787-775">for each argument, the implicit conversion from `Ex` to `Qx` is not better than the implicit conversion from `Ex` to `Px`, and</span></span>
*  <span data-ttu-id="b0787-776">pour au moins un argument, la conversion de `Ex` en `Px` est meilleure que la conversion de `Ex` en `Qx`.</span><span class="sxs-lookup"><span data-stu-id="b0787-776">for at least one argument, the conversion from `Ex` to `Px` is better than the conversion from `Ex` to `Qx`.</span></span>

<span data-ttu-id="b0787-777">Lorsque vous effectuez cette évaluation, si `Mp` ou `Mq` est applicable dans sa forme développée, `Px` ou `Qx` fait référence à un paramètre dans la forme développée de la liste de paramètres.</span><span class="sxs-lookup"><span data-stu-id="b0787-777">When performing this evaluation, if `Mp` or `Mq` is applicable in its expanded form, then `Px` or `Qx` refers to a parameter in the expanded form of the parameter list.</span></span>

<span data-ttu-id="b0787-778">Si les séquences de type de paramètre `{P1, P2, ..., Pn}` et `{Q1, Q2, ..., Qn}` sont équivalentes (c’est-à-dire que chaque `Pi` a une conversion d’identité en `Qi`correspondante), les règles de rupture de liaison suivantes sont appliquées, dans l’ordre, pour déterminer le meilleur membre de fonction.</span><span class="sxs-lookup"><span data-stu-id="b0787-778">In case the parameter type sequences `{P1, P2, ..., Pn}` and `{Q1, Q2, ..., Qn}` are equivalent (i.e. each `Pi` has an identity conversion to the corresponding `Qi`), the following tie-breaking rules are applied, in order, to determine the better function member.</span></span>

*  <span data-ttu-id="b0787-779">Si `Mp` est une méthode non générique et que `Mq` est une méthode générique, `Mp` est mieux que `Mq`.</span><span class="sxs-lookup"><span data-stu-id="b0787-779">If `Mp` is a non-generic method and `Mq` is a generic method, then `Mp` is better than `Mq`.</span></span>
*  <span data-ttu-id="b0787-780">Dans le cas contraire, si `Mp` est applicable dans sa forme normale et `Mq` possède un tableau `params` et s’applique uniquement dans sa forme développée, `Mp` est préférable à `Mq`.</span><span class="sxs-lookup"><span data-stu-id="b0787-780">Otherwise, if `Mp` is applicable in its normal form and `Mq` has a `params` array and is applicable only in its expanded form, then `Mp` is better than `Mq`.</span></span>
*  <span data-ttu-id="b0787-781">Sinon, si `Mp` a plus de paramètres déclarés que `Mq`, `Mp` est mieux que `Mq`.</span><span class="sxs-lookup"><span data-stu-id="b0787-781">Otherwise, if `Mp` has more declared parameters than `Mq`, then `Mp` is better than `Mq`.</span></span> <span data-ttu-id="b0787-782">Cela peut se produire si les deux méthodes ont des `params` des tableaux et s’appliquent uniquement à leurs formes développées.</span><span class="sxs-lookup"><span data-stu-id="b0787-782">This can occur if both methods have `params` arrays and are applicable only in their expanded forms.</span></span>
*  <span data-ttu-id="b0787-783">Sinon, si tous les paramètres de `Mp` ont un argument correspondant, alors que les arguments par défaut doivent être remplacés par au moins un paramètre facultatif dans `Mq`, `Mp` est mieux que `Mq`.</span><span class="sxs-lookup"><span data-stu-id="b0787-783">Otherwise if all parameters of `Mp` have a corresponding argument whereas default arguments need to be substituted for at least one optional parameter in `Mq` then `Mp` is better than `Mq`.</span></span>
*  <span data-ttu-id="b0787-784">Sinon, si `Mp` a des types de paramètres plus spécifiques que `Mq`, `Mp` est mieux que `Mq`.</span><span class="sxs-lookup"><span data-stu-id="b0787-784">Otherwise, if `Mp` has more specific parameter types than `Mq`, then `Mp` is better than `Mq`.</span></span> <span data-ttu-id="b0787-785">Laissez `{R1, R2, ..., Rn}` et `{S1, S2, ..., Sn}` représenter les types de paramètres non instanciés et non développés de `Mp` et `Mq`.</span><span class="sxs-lookup"><span data-stu-id="b0787-785">Let `{R1, R2, ..., Rn}` and `{S1, S2, ..., Sn}` represent the uninstantiated and unexpanded parameter types of `Mp` and `Mq`.</span></span> <span data-ttu-id="b0787-786">les types de paramètres de `Mp`sont plus spécifiques que `Mq`si, pour chaque paramètre, `Rx` n’est pas moins spécifique que `Sx`, et, pour au moins un paramètre, `Rx` est plus spécifique que `Sx`:</span><span class="sxs-lookup"><span data-stu-id="b0787-786">`Mp`'s parameter types are more specific than `Mq`'s if, for each parameter, `Rx` is not less specific than `Sx`, and, for at least one parameter, `Rx` is more specific than `Sx`:</span></span>
   *  <span data-ttu-id="b0787-787">Un paramètre de type est moins spécifique qu’un paramètre de non-type.</span><span class="sxs-lookup"><span data-stu-id="b0787-787">A type parameter is less specific than a non-type parameter.</span></span>
   *  <span data-ttu-id="b0787-788">De manière récursive, un type construit est plus spécifique qu’un autre type construit (avec le même nombre d’arguments de type) si au moins un argument de type est plus spécifique et qu’aucun argument de type n’est moins spécifique que l’argument de type correspondant dans l’autre.</span><span class="sxs-lookup"><span data-stu-id="b0787-788">Recursively, a constructed type is more specific than another constructed type (with the same number of type arguments) if at least one type argument is more specific and no type argument is less specific than the corresponding type argument in the other.</span></span>
   *  <span data-ttu-id="b0787-789">Un type tableau est plus spécifique qu’un autre type tableau (avec le même nombre de dimensions) si le type d’élément du premier est plus spécifique que le type d’élément du second.</span><span class="sxs-lookup"><span data-stu-id="b0787-789">An array type is more specific than another array type (with the same number of dimensions) if the element type of the first is more specific than the element type of the second.</span></span>
*  <span data-ttu-id="b0787-790">Dans le cas contraire, si un membre est un opérateur non levé et que l’autre est un opérateur levé, la meilleure est la meilleure.</span><span class="sxs-lookup"><span data-stu-id="b0787-790">Otherwise if one member is a non-lifted operator and  the other is a lifted operator, the non-lifted one is better.</span></span>
*  <span data-ttu-id="b0787-791">Dans le cas contraire, aucune des fonctions membres n’est meilleure.</span><span class="sxs-lookup"><span data-stu-id="b0787-791">Otherwise, neither function member is better.</span></span>

#### <a name="better-conversion-from-expression"></a><span data-ttu-id="b0787-792">Meilleure conversion à partir de l’expression</span><span class="sxs-lookup"><span data-stu-id="b0787-792">Better conversion from expression</span></span>

<span data-ttu-id="b0787-793">Dans le cas d’une conversion implicite `C1` qui convertit une expression `E` en un type `T1`, et une `C2` de conversion implicite qui convertit une expression `E` en type `T2`, `C1` est une ***meilleure conversion*** que `C2` si `E` ne correspond pas exactement à `T2` et au moins l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-793">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>

* <span data-ttu-id="b0787-794">`E` correspond exactement à `T1` ([expression correspondant exactement](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="b0787-794">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
* <span data-ttu-id="b0787-795">`T1` est une meilleure cible de conversion que `T2` ([meilleure cible de conversion](expressions.md#better-conversion-target))</span><span class="sxs-lookup"><span data-stu-id="b0787-795">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target))</span></span>

#### <a name="exactly-matching-expression"></a><span data-ttu-id="b0787-796">Expression correspondant exactement</span><span class="sxs-lookup"><span data-stu-id="b0787-796">Exactly matching Expression</span></span>

<span data-ttu-id="b0787-797">À partir d’une expression `E` et d’un type `T`, `E` correspond exactement à `T` si l’un des éléments suivants est présent :</span><span class="sxs-lookup"><span data-stu-id="b0787-797">Given an expression `E` and a type `T`, `E` exactly matches `T` if one of the following holds:</span></span>

*  <span data-ttu-id="b0787-798">`E` a un type `S`et une conversion d’identité existe de `S` à `T`</span><span class="sxs-lookup"><span data-stu-id="b0787-798">`E` has a type `S`, and an identity conversion exists from `S` to `T`</span></span>
*  <span data-ttu-id="b0787-799">`E` est une fonction anonyme, `T` est soit un type délégué `D`, soit un type d’arborescence d’expression `Expression<D>` et l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-799">`E` is an anonymous function, `T` is either a delegate type `D` or an expression tree type `Expression<D>` and one of the following holds:</span></span>
   *  <span data-ttu-id="b0787-800">Un type de retour déduit `X` existe pour `E` dans le contexte de la liste de paramètres de `D` ([type de retour déduit](expressions.md#inferred-return-type)), et il existe une conversion d’identité de `X` vers le type de retour de `D`</span><span class="sxs-lookup"><span data-stu-id="b0787-800">An inferred return type `X` exists for `E` in the context of the parameter list of `D` ([Inferred return type](expressions.md#inferred-return-type)), and an identity conversion exists from `X` to the return type of `D`</span></span>
   *  <span data-ttu-id="b0787-801">`E` est non asynchrone et `D` a un type de retour `Y` ou `E` est Async et `D` a un type de retour `Task<Y>`et l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-801">Either `E` is non-async and `D` has a return type `Y` or `E` is async and `D` has a return type `Task<Y>`, and one of the following holds:</span></span>
      * <span data-ttu-id="b0787-802">Le corps de `E` est une expression qui correspond exactement à `Y`</span><span class="sxs-lookup"><span data-stu-id="b0787-802">The body of `E` is an expression that exactly matches `Y`</span></span>
      * <span data-ttu-id="b0787-803">Le corps de `E` est un bloc d’instruction où chaque instruction return retourne une expression qui correspond exactement à `Y`</span><span class="sxs-lookup"><span data-stu-id="b0787-803">The body of `E` is a statement block where every return statement returns an expression that exactly matches `Y`</span></span>

#### <a name="better-conversion-target"></a><span data-ttu-id="b0787-804">Meilleure cible de conversion</span><span class="sxs-lookup"><span data-stu-id="b0787-804">Better conversion target</span></span>

<span data-ttu-id="b0787-805">Étant donné deux types différents `T1` et `T2`, `T1` est une meilleure cible de conversion que `T2` si aucune conversion implicite de `T2` en `T1` n’existe, et au moins l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-805">Given two different types `T1` and `T2`, `T1` is a better conversion target than `T2` if no implicit conversion from `T2` to `T1` exists, and at least one of the following holds:</span></span>

*  <span data-ttu-id="b0787-806">Une conversion implicite d' `T1` en `T2` existe</span><span class="sxs-lookup"><span data-stu-id="b0787-806">An implicit conversion from `T1` to `T2` exists</span></span>
*  <span data-ttu-id="b0787-807">`T1` est un type délégué `D1` ou un type d’arborescence d’expression `Expression<D1>`, `T2` est soit un type délégué `D2`, soit un type d’arborescence d’expression `Expression<D2>`, `D1` a un type de retour `S1` et l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-807">`T1` is either a delegate type `D1` or an expression tree type `Expression<D1>`, `T2` is either a delegate type `D2` or an expression tree type `Expression<D2>`, `D1` has a return type `S1` and one of the following holds:</span></span>
   * <span data-ttu-id="b0787-808">le `D2` est un retour void</span><span class="sxs-lookup"><span data-stu-id="b0787-808">`D2` is void returning</span></span>
   * <span data-ttu-id="b0787-809">`D2` a un type de retour `S2`et `S1` est une meilleure cible de conversion que `S2`</span><span class="sxs-lookup"><span data-stu-id="b0787-809">`D2` has a return type `S2`, and `S1` is a better conversion target than `S2`</span></span>
*  <span data-ttu-id="b0787-810">`T1` est `Task<S1>`, `T2` est `Task<S2>`, et `S1` est une meilleure cible de conversion que `S2`</span><span class="sxs-lookup"><span data-stu-id="b0787-810">`T1` is `Task<S1>`, `T2` is `Task<S2>`, and `S1` is a better conversion target than `S2`</span></span>
*  <span data-ttu-id="b0787-811">`T1` est `S1` ou `S1?` où `S1` est un type intégral signé, et `T2` est `S2` ou `S2?`, où `S2` est un type intégral non signé.</span><span class="sxs-lookup"><span data-stu-id="b0787-811">`T1` is `S1` or `S1?` where `S1` is a signed integral type, and `T2` is `S2` or `S2?` where `S2` is an unsigned integral type.</span></span> <span data-ttu-id="b0787-812">Plus précisément :</span><span class="sxs-lookup"><span data-stu-id="b0787-812">Specifically:</span></span>
   * <span data-ttu-id="b0787-813">`S1` est `sbyte` et `S2` est `byte`, `ushort`, `uint`ou `ulong`</span><span class="sxs-lookup"><span data-stu-id="b0787-813">`S1` is `sbyte` and `S2` is `byte`, `ushort`, `uint`, or `ulong`</span></span>
   * <span data-ttu-id="b0787-814">`S1` est `short` et `S2` est `ushort`, `uint`ou `ulong`</span><span class="sxs-lookup"><span data-stu-id="b0787-814">`S1` is `short` and `S2` is `ushort`, `uint`, or `ulong`</span></span>
   * <span data-ttu-id="b0787-815">`S1` est `int` et `S2` est `uint`, ou `ulong`</span><span class="sxs-lookup"><span data-stu-id="b0787-815">`S1` is `int` and `S2` is `uint`, or `ulong`</span></span>
   * <span data-ttu-id="b0787-816">`S1` est `long` et `S2` est `ulong`</span><span class="sxs-lookup"><span data-stu-id="b0787-816">`S1` is `long` and `S2` is `ulong`</span></span>

#### <a name="overloading-in-generic-classes"></a><span data-ttu-id="b0787-817">Surcharge dans les classes génériques</span><span class="sxs-lookup"><span data-stu-id="b0787-817">Overloading in generic classes</span></span>

<span data-ttu-id="b0787-818">Tandis que les signatures telles qu’elles sont déclarées doivent être uniques, il est possible que la substitution des arguments de type génère des signatures identiques.</span><span class="sxs-lookup"><span data-stu-id="b0787-818">While signatures as declared must be unique, it is possible that substitution of type arguments results in identical signatures.</span></span> <span data-ttu-id="b0787-819">Dans ce cas, les règles de résolution de surcharge ci-dessus sélectionnent le membre le plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-819">In such cases, the tie-breaking rules of overload resolution above will pick the most specific member.</span></span>

<span data-ttu-id="b0787-820">Les exemples suivants montrent des surcharges qui sont valides et non valides conformément à cette règle :</span><span class="sxs-lookup"><span data-stu-id="b0787-820">The following examples show overloads that are valid and invalid according to this rule:</span></span>

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

### <a name="compile-time-checking-of-dynamic-overload-resolution"></a><span data-ttu-id="b0787-821">Vérification de la résolution de surcharge dynamique au moment de la compilation</span><span class="sxs-lookup"><span data-stu-id="b0787-821">Compile-time checking of dynamic overload resolution</span></span>

<span data-ttu-id="b0787-822">Pour la plupart des opérations liées de manière dynamique, l’ensemble des candidats possibles pour la résolution est inconnu au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-822">For most dynamically bound operations the set of possible candidates for resolution is unknown at compile-time.</span></span> <span data-ttu-id="b0787-823">Dans certains cas, toutefois, l’ensemble de candidats est connu au moment de la compilation :</span><span class="sxs-lookup"><span data-stu-id="b0787-823">In certain cases, however the candidate set is known at compile-time:</span></span>

*  <span data-ttu-id="b0787-824">Appels de méthode statiques avec des arguments dynamiques</span><span class="sxs-lookup"><span data-stu-id="b0787-824">Static method calls with dynamic arguments</span></span>
*  <span data-ttu-id="b0787-825">Appels de méthode d’instance où le récepteur n’est pas une expression dynamique</span><span class="sxs-lookup"><span data-stu-id="b0787-825">Instance method calls where the receiver is not a dynamic expression</span></span>
*  <span data-ttu-id="b0787-826">Appels de l’indexeur où le récepteur n’est pas une expression dynamique</span><span class="sxs-lookup"><span data-stu-id="b0787-826">Indexer calls where the receiver is not a dynamic expression</span></span>
*  <span data-ttu-id="b0787-827">Appels de constructeur avec des arguments dynamiques</span><span class="sxs-lookup"><span data-stu-id="b0787-827">Constructor calls with dynamic arguments</span></span>

<span data-ttu-id="b0787-828">Dans ces cas, un contrôle limité au moment de la compilation est effectué pour chaque candidat afin de déterminer si l’un d’eux peut éventuellement s’appliquer au moment de l’exécution. Cette vérification se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-828">In these cases a limited compile-time check is performed for each candidate to see if any of them could possibly apply at run-time.This check consists of the following steps:</span></span>

*  <span data-ttu-id="b0787-829">Inférence de type partiel : tout argument de type qui ne dépend pas directement ou indirectement sur un argument de type `dynamic` est déduit à l’aide des règles d' [inférence de type](expressions.md#type-inference).</span><span class="sxs-lookup"><span data-stu-id="b0787-829">Partial type inference: Any type argument that does not depend directly or indirectly on an argument of type `dynamic` is inferred using the rules of [Type inference](expressions.md#type-inference).</span></span> <span data-ttu-id="b0787-830">Les arguments de type restants sont inconnus.</span><span class="sxs-lookup"><span data-stu-id="b0787-830">The remaining type arguments are unknown.</span></span>
*  <span data-ttu-id="b0787-831">Vérification de l’applicabilité partielle : l’applicabilité est vérifiée en fonction du [membre de fonction applicable](expressions.md#applicable-function-member), mais les paramètres dont les types sont inconnus sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="b0787-831">Partial applicability check: Applicability is checked according to [Applicable function member](expressions.md#applicable-function-member), but ignoring parameters whose types are unknown.</span></span>
*  <span data-ttu-id="b0787-832">Si aucun candidat ne réussit ce test, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-832">If no candidate passes this test, a compile-time error occurs.</span></span>

### <a name="function-member-invocation"></a><span data-ttu-id="b0787-833">Appel de membre de fonction</span><span class="sxs-lookup"><span data-stu-id="b0787-833">Function member invocation</span></span>

<span data-ttu-id="b0787-834">Cette section décrit le processus qui a lieu au moment de l’exécution pour appeler un membre de fonction particulier.</span><span class="sxs-lookup"><span data-stu-id="b0787-834">This section describes the process that takes place at run-time to invoke a particular function member.</span></span> <span data-ttu-id="b0787-835">Il est supposé qu’un processus de liaison a déjà déterminé le membre particulier à appeler, éventuellement en appliquant la résolution de surcharge à un ensemble de membres de fonction candidats.</span><span class="sxs-lookup"><span data-stu-id="b0787-835">It is assumed that a binding-time process has already determined the particular member to invoke, possibly by applying overload resolution to a set of candidate function members.</span></span>

<span data-ttu-id="b0787-836">Dans le cadre de la description du processus d’appel, les membres de fonction sont divisés en deux catégories :</span><span class="sxs-lookup"><span data-stu-id="b0787-836">For purposes of describing the invocation process, function members are divided into two categories:</span></span>

*  <span data-ttu-id="b0787-837">Fonctions membres statiques.</span><span class="sxs-lookup"><span data-stu-id="b0787-837">Static function members.</span></span> <span data-ttu-id="b0787-838">Il s’agit des constructeurs d’instance, des méthodes statiques, des accesseurs de propriété statique et des opérateurs définis par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-838">These are instance constructors, static methods, static property accessors, and user-defined operators.</span></span> <span data-ttu-id="b0787-839">Les fonctions membres statiques sont toujours non virtuelles.</span><span class="sxs-lookup"><span data-stu-id="b0787-839">Static function members are always non-virtual.</span></span>
*  <span data-ttu-id="b0787-840">Membres de fonction d’instance.</span><span class="sxs-lookup"><span data-stu-id="b0787-840">Instance function members.</span></span> <span data-ttu-id="b0787-841">Il s’agit des méthodes d’instance, des accesseurs de propriété d’instance et des accesseurs d’indexeurs.</span><span class="sxs-lookup"><span data-stu-id="b0787-841">These are instance methods, instance property accessors, and indexer accessors.</span></span> <span data-ttu-id="b0787-842">Les fonctions membres de fonction d’instance sont non virtuelles ou virtuelles et sont toujours appelées sur une instance particulière.</span><span class="sxs-lookup"><span data-stu-id="b0787-842">Instance function members are either non-virtual or virtual, and are always invoked on a particular instance.</span></span> <span data-ttu-id="b0787-843">L’instance est calculée par une expression d’instance et devient accessible dans le membre de fonction en tant que `this` ([cet accès](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="b0787-843">The instance is computed by an instance expression, and it becomes accessible within the function member as `this` ([This access](expressions.md#this-access)).</span></span>

<span data-ttu-id="b0787-844">Le traitement au moment de l’exécution d’un appel de membre de fonction se compose des étapes suivantes, où `M` est le membre de fonction et, si `M` est un membre d’instance, `E` est l’expression d’instance :</span><span class="sxs-lookup"><span data-stu-id="b0787-844">The run-time processing of a function member invocation consists of the following steps, where `M` is the function member and, if `M` is an instance member, `E` is the instance expression:</span></span>

*  <span data-ttu-id="b0787-845">Si `M` est un membre de fonction statique :</span><span class="sxs-lookup"><span data-stu-id="b0787-845">If `M` is a static function member:</span></span>
   * <span data-ttu-id="b0787-846">La liste d’arguments est évaluée comme décrit dans [listes d’arguments](expressions.md#argument-lists).</span><span class="sxs-lookup"><span data-stu-id="b0787-846">The argument list is evaluated as described in [Argument lists](expressions.md#argument-lists).</span></span>
   * <span data-ttu-id="b0787-847">`M` est appelé.</span><span class="sxs-lookup"><span data-stu-id="b0787-847">`M` is invoked.</span></span>

*  <span data-ttu-id="b0787-848">Si `M` est un membre de fonction d’instance déclaré dans un *Value_type*:</span><span class="sxs-lookup"><span data-stu-id="b0787-848">If `M` is an instance function member declared in a *value_type*:</span></span>
   * <span data-ttu-id="b0787-849">`E` est évalué.</span><span class="sxs-lookup"><span data-stu-id="b0787-849">`E` is evaluated.</span></span> <span data-ttu-id="b0787-850">Si cette évaluation provoque une exception, aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-850">If this evaluation causes an exception, then no further steps are executed.</span></span>
   * <span data-ttu-id="b0787-851">Si `E` n’est pas classée en tant que variable, une variable locale temporaire du type de `E`est créée et la valeur de `E` est assignée à cette variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-851">If `E` is not classified as a variable, then a temporary local variable of `E`'s type is created and the value of `E` is assigned to that variable.</span></span> <span data-ttu-id="b0787-852">`E` est ensuite reclassifiée comme une référence à cette variable locale temporaire.</span><span class="sxs-lookup"><span data-stu-id="b0787-852">`E` is then reclassified as a reference to that temporary local variable.</span></span> <span data-ttu-id="b0787-853">La variable temporaire est accessible en tant que `this` dans `M`, mais pas de quelque manière que ce soit.</span><span class="sxs-lookup"><span data-stu-id="b0787-853">The temporary variable is accessible as `this` within `M`, but not in any other way.</span></span> <span data-ttu-id="b0787-854">Ainsi, uniquement lorsque `E` est une véritable variable, l’appelant peut observer les modifications apportées par `M` à `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-854">Thus, only when `E` is a true variable is it possible for the caller to observe the changes that `M` makes to `this`.</span></span>
   * <span data-ttu-id="b0787-855">La liste d’arguments est évaluée comme décrit dans [listes d’arguments](expressions.md#argument-lists).</span><span class="sxs-lookup"><span data-stu-id="b0787-855">The argument list is evaluated as described in [Argument lists](expressions.md#argument-lists).</span></span>
   * <span data-ttu-id="b0787-856">`M` est appelé.</span><span class="sxs-lookup"><span data-stu-id="b0787-856">`M` is invoked.</span></span> <span data-ttu-id="b0787-857">La variable référencée par `E` devient la variable référencée par `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-857">The variable referenced by `E` becomes the variable referenced by `this`.</span></span>

*  <span data-ttu-id="b0787-858">Si `M` est un membre de fonction d’instance déclaré dans un *reference_type*:</span><span class="sxs-lookup"><span data-stu-id="b0787-858">If `M` is an instance function member declared in a *reference_type*:</span></span>
   * <span data-ttu-id="b0787-859">`E` est évalué.</span><span class="sxs-lookup"><span data-stu-id="b0787-859">`E` is evaluated.</span></span> <span data-ttu-id="b0787-860">Si cette évaluation provoque une exception, aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-860">If this evaluation causes an exception, then no further steps are executed.</span></span>
   * <span data-ttu-id="b0787-861">La liste d’arguments est évaluée comme décrit dans [listes d’arguments](expressions.md#argument-lists).</span><span class="sxs-lookup"><span data-stu-id="b0787-861">The argument list is evaluated as described in [Argument lists](expressions.md#argument-lists).</span></span>
   * <span data-ttu-id="b0787-862">Si le type de `E` est un *Value_type*, une conversion boxing ([conversions boxing](types.md#boxing-conversions)) est effectuée pour convertir `E` en type `object`, et `E` est considéré comme étant de type `object` dans les étapes suivantes.</span><span class="sxs-lookup"><span data-stu-id="b0787-862">If the type of `E` is a *value_type*, a boxing conversion ([Boxing conversions](types.md#boxing-conversions)) is performed to convert `E` to type `object`, and `E` is considered to be of type `object` in the following steps.</span></span> <span data-ttu-id="b0787-863">Dans ce cas, `M` ne peut être qu’un membre de `System.Object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-863">In this case, `M` could only be a member of `System.Object`.</span></span>
   * <span data-ttu-id="b0787-864">La valeur de `E` est vérifiée comme valide.</span><span class="sxs-lookup"><span data-stu-id="b0787-864">The value of `E` is checked to be valid.</span></span> <span data-ttu-id="b0787-865">Si la valeur de `E` est `null`, une `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-865">If the value of `E` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
   * <span data-ttu-id="b0787-866">L’implémentation de la fonction membre à appeler est déterminée :</span><span class="sxs-lookup"><span data-stu-id="b0787-866">The function member implementation to invoke is determined:</span></span>
     * <span data-ttu-id="b0787-867">Si le type au moment de la liaison de `E` est une interface, la fonction membre à appeler est l’implémentation de `M` fournie par le type au moment de l’exécution de l’instance référencée par `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-867">If the binding-time type of `E` is an interface, the function member to invoke is the implementation of `M` provided by the run-time type of the instance referenced by `E`.</span></span> <span data-ttu-id="b0787-868">Cette fonction membre est déterminée en appliquant les règles de mappage d’interface ([mappage d’interface](interfaces.md#interface-mapping)) pour déterminer l’implémentation de `M` fournie par le type au moment de l’exécution de l’instance référencée par `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-868">This function member is determined by applying the interface mapping rules ([Interface mapping](interfaces.md#interface-mapping)) to determine the implementation of `M` provided by the run-time type of the instance referenced by `E`.</span></span>
     * <span data-ttu-id="b0787-869">Sinon, si `M` est un membre de fonction virtuelle, la fonction membre à appeler est l’implémentation de `M` fournie par le type au moment de l’exécution de l’instance référencée par `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-869">Otherwise, if `M` is a virtual function member, the function member to invoke is the implementation of `M` provided by the run-time type of the instance referenced by `E`.</span></span> <span data-ttu-id="b0787-870">Cette fonction membre est déterminée en appliquant les règles de détermination de l’implémentation la plus dérivée ([méthodes virtuelles](classes.md#virtual-methods)) de `M` en ce qui concerne le type au moment de l’exécution de l’instance référencée par `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-870">This function member is determined by applying the rules for determining the most derived implementation ([Virtual methods](classes.md#virtual-methods)) of `M` with respect to the run-time type of the instance referenced by `E`.</span></span>
     * <span data-ttu-id="b0787-871">Dans le cas contraire, `M` est un membre de fonction non virtuel, et la fonction membre à appeler est `M` elle-même.</span><span class="sxs-lookup"><span data-stu-id="b0787-871">Otherwise, `M` is a non-virtual function member, and the function member to invoke is `M` itself.</span></span>
   * <span data-ttu-id="b0787-872">L’implémentation de la fonction membre déterminée à l’étape ci-dessus est appelée.</span><span class="sxs-lookup"><span data-stu-id="b0787-872">The function member implementation determined in the step above is invoked.</span></span> <span data-ttu-id="b0787-873">L’objet référencé par `E` devient l’objet référencé par `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-873">The object referenced by `E` becomes the object referenced by `this`.</span></span>

#### <a name="invocations-on-boxed-instances"></a><span data-ttu-id="b0787-874">Appels sur les instances boxed</span><span class="sxs-lookup"><span data-stu-id="b0787-874">Invocations on boxed instances</span></span>

<span data-ttu-id="b0787-875">Un membre de fonction implémenté dans un *Value_type* peut être appelé via une instance boxed de qui *Value_type* dans les situations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-875">A function member implemented in a *value_type* can be invoked through a boxed instance of that *value_type* in the following situations:</span></span>

*  <span data-ttu-id="b0787-876">Quand le membre de fonction est un `override` d’une méthode héritée du type `object` et est appelé par le biais d’une expression d’instance de type `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-876">When the function member is an `override` of a method inherited from type `object` and is invoked through an instance expression of type `object`.</span></span>
*  <span data-ttu-id="b0787-877">Quand la fonction membre est une implémentation d’un membre de fonction d’interface et qu’elle est appelée par le biais d’une expression d’instance d’un *INTERFACE_TYPE*.</span><span class="sxs-lookup"><span data-stu-id="b0787-877">When the function member is an implementation of an interface function member and is invoked through an instance expression of an *interface_type*.</span></span>
*  <span data-ttu-id="b0787-878">Quand la fonction membre est appelée par l’intermédiaire d’un délégué.</span><span class="sxs-lookup"><span data-stu-id="b0787-878">When the function member is invoked through a delegate.</span></span>

<span data-ttu-id="b0787-879">Dans ces situations, l’instance boxed est considérée comme contenant une variable de la *Value_type*, et cette variable devient la variable référencée par `this` dans l’appel de membre de fonction.</span><span class="sxs-lookup"><span data-stu-id="b0787-879">In these situations, the boxed instance is considered to contain a variable of the *value_type*, and this variable becomes the variable referenced by `this` within the function member invocation.</span></span> <span data-ttu-id="b0787-880">En particulier, cela signifie que lorsqu’un membre de fonction est appelé sur une instance boxed, la fonction membre peut modifier la valeur contenue dans l’instance boxed.</span><span class="sxs-lookup"><span data-stu-id="b0787-880">In particular, this means that when a function member is invoked on a boxed instance, it is possible for the function member to modify the value contained in the boxed instance.</span></span>

## <a name="primary-expressions"></a><span data-ttu-id="b0787-881">Expressions primaires</span><span class="sxs-lookup"><span data-stu-id="b0787-881">Primary expressions</span></span>

<span data-ttu-id="b0787-882">Les expressions primaires incluent les formes les plus simples des expressions.</span><span class="sxs-lookup"><span data-stu-id="b0787-882">Primary expressions include the simplest forms of expressions.</span></span>

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

<span data-ttu-id="b0787-883">Les expressions primaires sont réparties entre *array_creation_expression*s et *primary_no_array_creation_expression*s.</span><span class="sxs-lookup"><span data-stu-id="b0787-883">Primary expressions are divided between *array_creation_expression*s and *primary_no_array_creation_expression*s.</span></span> <span data-ttu-id="b0787-884">Le traitement de l’expression de création de tableau de cette manière, plutôt que son affichage avec les autres formulaires d’expression simples, permet à la grammaire d’interdire le code potentiellement confus, tel que</span><span class="sxs-lookup"><span data-stu-id="b0787-884">Treating array-creation-expression in this way, rather than listing it along with the other simple expression forms, enables the grammar to disallow potentially confusing code such as</span></span>
```csharp
object o = new int[3][1];
```
<span data-ttu-id="b0787-885">qui, autrement, serait interprété comme</span><span class="sxs-lookup"><span data-stu-id="b0787-885">which would otherwise be interpreted as</span></span>
```csharp
object o = (new int[3])[1];
```

### <a name="literals"></a><span data-ttu-id="b0787-886">Littéraux</span><span class="sxs-lookup"><span data-stu-id="b0787-886">Literals</span></span>

<span data-ttu-id="b0787-887">Une *primary_expression* qui se compose d’un *littéral* ([littéraux](lexical-structure.md#literals)) est classée en tant que valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-887">A *primary_expression* that consists of a *literal* ([Literals](lexical-structure.md#literals)) is classified as a value.</span></span>


### <a name="interpolated-strings"></a><span data-ttu-id="b0787-888">Chaînes interpolées</span><span class="sxs-lookup"><span data-stu-id="b0787-888">Interpolated strings</span></span>

<span data-ttu-id="b0787-889">Un *interpolated_string_expression* se compose d’un signe `$` suivi d’un littéral de chaîne normal ou textuel, où les trous, délimités par `{` et `}`, encadrent les expressions et les spécifications de mise en forme.</span><span class="sxs-lookup"><span data-stu-id="b0787-889">An *interpolated_string_expression* consists of a `$` sign followed by a regular or verbatim string literal, wherein holes, delimited by `{` and `}`, enclose expressions and formatting specifications.</span></span> <span data-ttu-id="b0787-890">Une expression de chaîne interpolée est le résultat d’une *interpolated_string_literal* qui a été divisée en jetons individuels, comme décrit dans [littéraux de chaîne interpolés](lexical-structure.md#interpolated-string-literals).</span><span class="sxs-lookup"><span data-stu-id="b0787-890">An interpolated string expression is the result of an *interpolated_string_literal* that has been broken up into individual tokens, as described in [Interpolated string literals](lexical-structure.md#interpolated-string-literals).</span></span>

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

<span data-ttu-id="b0787-891">La *constant_expression* d’une interpolation doit avoir une conversion implicite en `int`.</span><span class="sxs-lookup"><span data-stu-id="b0787-891">The *constant_expression* in an interpolation must have an implicit conversion to `int`.</span></span>

<span data-ttu-id="b0787-892">Une *interpolated_string_expression* est classée en tant que valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-892">An *interpolated_string_expression* is classified as a value.</span></span> <span data-ttu-id="b0787-893">Si elle est immédiatement convertie en `System.IFormattable` ou `System.FormattableString` avec une conversion de chaîne interpolée implicite ([conversions de chaînes interpolées implicites](conversions.md#implicit-interpolated-string-conversions)), l’expression de chaîne interpolée a ce type.</span><span class="sxs-lookup"><span data-stu-id="b0787-893">If it is immediately converted to `System.IFormattable` or `System.FormattableString` with an implicit interpolated string conversion ([Implicit interpolated string conversions](conversions.md#implicit-interpolated-string-conversions)), the interpolated string expression has that type.</span></span> <span data-ttu-id="b0787-894">Dans le cas contraire, il a le type `string`.</span><span class="sxs-lookup"><span data-stu-id="b0787-894">Otherwise, it has the type `string`.</span></span>

<span data-ttu-id="b0787-895">Si le type d’une chaîne interpolée est `System.IFormattable` ou `System.FormattableString`, la signification est un appel à `System.Runtime.CompilerServices.FormattableStringFactory.Create`.</span><span class="sxs-lookup"><span data-stu-id="b0787-895">If the type of an interpolated string is `System.IFormattable` or `System.FormattableString`, the meaning is a call to `System.Runtime.CompilerServices.FormattableStringFactory.Create`.</span></span> <span data-ttu-id="b0787-896">Si le type est `string`, la signification de l’expression est un appel à `string.Format`.</span><span class="sxs-lookup"><span data-stu-id="b0787-896">If the type is `string`, the meaning of the expression is a call to `string.Format`.</span></span> <span data-ttu-id="b0787-897">Dans les deux cas, la liste d’arguments de l’appel se compose d’un littéral de chaîne de format avec des espaces réservés pour chaque interpolation, et d’un argument pour chaque expression qui correspond aux espaces réservés.</span><span class="sxs-lookup"><span data-stu-id="b0787-897">In both cases, the argument list of the call consists of a format string literal with placeholders for each interpolation, and an argument for each expression corresponding to the place holders.</span></span>

<span data-ttu-id="b0787-898">Le littéral de chaîne de format est construit comme suit, où `N` est le nombre d’interpolations dans le *interpolated_string_expression*:</span><span class="sxs-lookup"><span data-stu-id="b0787-898">The format string literal is constructed as follows, where `N` is the number of interpolations in the *interpolated_string_expression*:</span></span>

*  <span data-ttu-id="b0787-899">Si un *interpolated_regular_string_whole* ou un *interpolated_verbatim_string_whole* suit le signe `$`, le littéral de chaîne de format est ce jeton.</span><span class="sxs-lookup"><span data-stu-id="b0787-899">If an *interpolated_regular_string_whole* or an *interpolated_verbatim_string_whole* follows the `$` sign, then the format string literal is that token.</span></span>
*  <span data-ttu-id="b0787-900">Dans le cas contraire, le littéral de chaîne de format se compose des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-900">Otherwise, the format string literal consists of:</span></span> 
   *  <span data-ttu-id="b0787-901">Premier *interpolated_regular_string_start* ou *interpolated_verbatim_string_start*</span><span class="sxs-lookup"><span data-stu-id="b0787-901">First the *interpolated_regular_string_start* or *interpolated_verbatim_string_start*</span></span>
   *  <span data-ttu-id="b0787-902">Puis, pour chaque nombre `I` de `0` à `N-1`:</span><span class="sxs-lookup"><span data-stu-id="b0787-902">Then for each number `I` from `0` to `N-1`:</span></span> 
      * <span data-ttu-id="b0787-903">Représentation décimale de `I`</span><span class="sxs-lookup"><span data-stu-id="b0787-903">The decimal representation of `I`</span></span>
      * <span data-ttu-id="b0787-904">Ensuite, si l' *interpolation* correspondante a un *constant_expression*, un `,` (virgule) suivi de la représentation décimale de la valeur de la *constant_expression*</span><span class="sxs-lookup"><span data-stu-id="b0787-904">Then, if the corresponding *interpolation* has a *constant_expression*, a `,` (comma) followed by the decimal representation of the value of the *constant_expression*</span></span>
      * <span data-ttu-id="b0787-905">Ensuite, le *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_mid* ou *interpolated_verbatim_string_end* qui suit immédiatement l’interpolation correspondante.</span><span class="sxs-lookup"><span data-stu-id="b0787-905">Then the *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_mid* or *interpolated_verbatim_string_end* immediately following the corresponding interpolation.</span></span>

<span data-ttu-id="b0787-906">Les arguments suivants sont simplement les *expressions* des *interpolations* (le cas échéant), dans l’ordre.</span><span class="sxs-lookup"><span data-stu-id="b0787-906">The subsequent arguments are simply the *expressions* from the *interpolations* (if any), in order.</span></span>

<span data-ttu-id="b0787-907">TODO : exemples.</span><span class="sxs-lookup"><span data-stu-id="b0787-907">TODO: examples.</span></span>


### <a name="simple-names"></a><span data-ttu-id="b0787-908">Noms simples</span><span class="sxs-lookup"><span data-stu-id="b0787-908">Simple names</span></span>

<span data-ttu-id="b0787-909">Un *simple_name* se compose d’un identificateur, éventuellement suivi d’une liste d’arguments de type :</span><span class="sxs-lookup"><span data-stu-id="b0787-909">A *simple_name* consists of an identifier, optionally followed by a type argument list:</span></span>

```antlr
simple_name
    : identifier type_argument_list?
    ;
```

<span data-ttu-id="b0787-910">Une *simple_name* se présente sous la forme `I` ou sous la forme `I<A1,...,Ak>`, où `I` est un identificateur unique et `<A1,...,Ak>` est un *type_argument_list*facultatif.</span><span class="sxs-lookup"><span data-stu-id="b0787-910">A *simple_name* is either of the form `I` or of the form `I<A1,...,Ak>`, where `I` is a single identifier and `<A1,...,Ak>` is an optional *type_argument_list*.</span></span> <span data-ttu-id="b0787-911">Si aucun *type_argument_list* n’est spécifié, envisagez `K` à zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-911">When no *type_argument_list* is specified, consider `K` to be zero.</span></span> <span data-ttu-id="b0787-912">La *simple_name* est évaluée et classée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-912">The *simple_name* is evaluated and classified as follows:</span></span>

*  <span data-ttu-id="b0787-913">Si `K` est égal à zéro et que le *simple_name* apparaît dans un *bloc* et que l’espace de déclaration de variable locale ([déclarations](basic-concepts.md#declarations)) du *bloc*(ou du *bloc*englobant) contient une variable locale, un paramètre ou une constante portant le nom `I`, le *simple_name* fait référence à cette variable locale, ce paramètre ou cette constante et est classifié comme une variable ou une valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-913">If `K` is zero and the *simple_name* appears within a *block* and if the *block*'s (or an enclosing *block*'s) local variable declaration space ([Declarations](basic-concepts.md#declarations)) contains a local variable, parameter or constant with name `I`, then the *simple_name* refers to that local variable, parameter or constant and is classified as a variable or value.</span></span>
*  <span data-ttu-id="b0787-914">Si `K` est égal à zéro et que le *simple_name* apparaît dans le corps d’une déclaration de méthode générique et si cette déclaration comprend un paramètre de type portant le nom `I`, le *simple_name* fait référence à ce paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="b0787-914">If `K` is zero and the *simple_name* appears within the body of a generic method declaration and if that declaration includes a type parameter with name `I`, then the *simple_name* refers to that type parameter.</span></span>
*  <span data-ttu-id="b0787-915">Dans le cas contraire, pour chaque type d’instance `T` ([le type d’instance](classes.md#the-instance-type)), en commençant par le type d’instance de la déclaration de type englobante immédiate et en continuant avec le type d’instance de chaque déclaration de classe ou de struct englobante (le cas échéant) :</span><span class="sxs-lookup"><span data-stu-id="b0787-915">Otherwise, for each instance type `T` ([The instance type](classes.md#the-instance-type)), starting with the instance type of the immediately enclosing type declaration and continuing with the instance type of each enclosing class or struct declaration (if any):</span></span>
   *  <span data-ttu-id="b0787-916">Si `K` est égal à zéro et que la déclaration de `T` comprend un paramètre de type avec le nom `I`, le *simple_name* fait référence à ce paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="b0787-916">If `K` is zero and the declaration of `T` includes a type parameter with name `I`, then the *simple_name* refers to that type parameter.</span></span>
   *  <span data-ttu-id="b0787-917">Sinon, si une recherche de membre ([recherche de membre](expressions.md#member-lookup)) de `I` dans `T` avec `K`arguments de type  produit une correspondance :</span><span class="sxs-lookup"><span data-stu-id="b0787-917">Otherwise, if a member lookup ([Member lookup](expressions.md#member-lookup)) of `I` in `T` with `K` type arguments produces a match:</span></span>
      * <span data-ttu-id="b0787-918">Si `T` est le type d’instance du type de classe ou de struct immédiatement englobant et que la recherche identifie une ou plusieurs méthodes, le résultat est un groupe de méthodes avec une expression d’instance associée de `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-918">If `T` is the instance type of the immediately enclosing class or struct type and the lookup identifies one or more methods, the result is a method group with an associated instance expression of `this`.</span></span> <span data-ttu-id="b0787-919">Si une liste d’arguments de type a été spécifiée, elle est utilisée pour appeler une méthode générique ([appels de méthode](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="b0787-919">If a type argument list was specified, it is used in calling a generic method ([Method invocations](expressions.md#method-invocations)).</span></span>
      * <span data-ttu-id="b0787-920">Sinon, si `T` est le type d’instance du type de classe ou de struct immédiatement englobant, si la recherche identifie un membre d’instance et si la référence se produit dans le corps d’un constructeur d’instance, une méthode d’instance ou un accesseur d’instance, le résultat est le même qu’un accès de membre ([accès aux membres](expressions.md#member-access)) de la `this.I`de formulaire.</span><span class="sxs-lookup"><span data-stu-id="b0787-920">Otherwise, if `T` is the instance type of the immediately enclosing class or struct type, if the lookup identifies an instance member, and if the reference occurs within the body of an instance constructor, an instance method, or an instance accessor, the result is the same as a member access ([Member access](expressions.md#member-access)) of the form `this.I`.</span></span> <span data-ttu-id="b0787-921">Cela peut se produire uniquement lorsque `K` est égal à zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-921">This can only happen when `K` is zero.</span></span>
      * <span data-ttu-id="b0787-922">Dans le cas contraire, le résultat est le même qu’un accès de membre ([accès aux membres](expressions.md#member-access)) de la forme `T.I` ou `T.I<A1,...,Ak>`.</span><span class="sxs-lookup"><span data-stu-id="b0787-922">Otherwise, the result is the same as a member access ([Member access](expressions.md#member-access)) of the form `T.I` or `T.I<A1,...,Ak>`.</span></span> <span data-ttu-id="b0787-923">Dans ce cas, il s’agit d’une erreur de liaison au moment de la liaison pour que le *simple_name* fait référence à un membre d’instance.</span><span class="sxs-lookup"><span data-stu-id="b0787-923">In this case, it is a binding-time error for the *simple_name* to refer to an instance member.</span></span>

*  <span data-ttu-id="b0787-924">Sinon, pour chaque espace de noms `N`, en commençant par l’espace de noms dans lequel le *simple_name* se produit, en continuant avec chaque espace de noms englobant (le cas échéant) et en terminant par l’espace de noms global, les étapes suivantes sont évaluées jusqu’à ce qu’une entité soit localisée :</span><span class="sxs-lookup"><span data-stu-id="b0787-924">Otherwise, for each namespace `N`, starting with the namespace in which the *simple_name* occurs, continuing with each enclosing namespace (if any), and ending with the global namespace, the following steps are evaluated until an entity is located:</span></span>
   *  <span data-ttu-id="b0787-925">Si `K` est égal à zéro et que `I` est le nom d’un espace de noms dans `N`, alors :</span><span class="sxs-lookup"><span data-stu-id="b0787-925">If `K` is zero and `I` is the name of a namespace in `N`, then:</span></span>
      * <span data-ttu-id="b0787-926">Si l’emplacement où le *simple_name* se produit est entouré d’une déclaration d’espace de noms pour `N` et que la déclaration d’espace de noms contient un *extern_alias_directive* ou *using_alias_directive* qui associe le nom `I` à un espace de noms ou à un type, le *simple_name* est ambigu et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-926">If the location where the *simple_name* occurs is enclosed by a namespace declaration for `N` and the namespace declaration contains an *extern_alias_directive* or *using_alias_directive* that associates the name `I` with a namespace or type, then the *simple_name* is ambiguous and a compile-time error occurs.</span></span>
      * <span data-ttu-id="b0787-927">Dans le cas contraire, le *simple_name* fait référence à l’espace de noms nommé `I` dans `N`.</span><span class="sxs-lookup"><span data-stu-id="b0787-927">Otherwise, the *simple_name* refers to the namespace named `I` in `N`.</span></span>
   *  <span data-ttu-id="b0787-928">Dans le cas contraire, si `N` contient un type accessible dont le nom `I` et `K`paramètres de type  , alors :</span><span class="sxs-lookup"><span data-stu-id="b0787-928">Otherwise, if `N` contains an accessible type having name `I` and `K` type parameters, then:</span></span>
      * <span data-ttu-id="b0787-929">Si `K` est égal à zéro et que l’emplacement où le *simple_name* se produit est entouré d’une déclaration d’espace de noms pour `N` et que la déclaration d’espace de noms contient une *extern_alias_directive* ou une *using_alias_directive* qui associe le nom `I` à un espace de noms ou à un type, le *simple_name* est ambigu et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-929">If `K` is zero and the location where the *simple_name* occurs is enclosed by a namespace declaration for `N` and the namespace declaration contains an *extern_alias_directive* or *using_alias_directive* that associates the name `I` with a namespace or type, then the *simple_name* is ambiguous and a compile-time error occurs.</span></span>
      * <span data-ttu-id="b0787-930">Sinon, le *namespace_or_type_name* fait référence au type construit avec les arguments de type donnés.</span><span class="sxs-lookup"><span data-stu-id="b0787-930">Otherwise, the *namespace_or_type_name* refers to the type constructed with the given type arguments.</span></span>
   *  <span data-ttu-id="b0787-931">Sinon, si l’emplacement où le *simple_name* se produit est entouré d’une déclaration d’espace de noms pour `N`:</span><span class="sxs-lookup"><span data-stu-id="b0787-931">Otherwise, if the location where the *simple_name* occurs is enclosed by a namespace declaration for `N`:</span></span>
      * <span data-ttu-id="b0787-932">Si `K` est égal à zéro et que la déclaration d’espace de noms contient une *extern_alias_directive* ou *using_alias_directive* qui associe le nom `I` à un espace de noms ou un type importé, le *simple_name* fait référence à cet espace de noms ou type.</span><span class="sxs-lookup"><span data-stu-id="b0787-932">If `K` is zero and the namespace declaration contains an *extern_alias_directive* or *using_alias_directive* that associates the name `I` with an imported namespace or type, then the *simple_name* refers to that namespace or type.</span></span>
      * <span data-ttu-id="b0787-933">Sinon, si les espaces de noms et les déclarations de type importés par le *using_namespace_directive*s et *using_static_directive*s de la déclaration d’espace de noms contiennent exactement un type accessible ou un membre statique non-extension ayant le nom `I` et `K`paramètres de type  , le *simple_name* fait référence à ce type ou membre construit avec les arguments de type donnés.</span><span class="sxs-lookup"><span data-stu-id="b0787-933">Otherwise, if the namespaces and type declarations imported by the *using_namespace_directive*s and *using_static_directive*s of the namespace declaration contain exactly one accessible type or non-extension static member having name `I` and `K` type parameters, then the *simple_name* refers to that type or member constructed with the given type arguments.</span></span>
      * <span data-ttu-id="b0787-934">Dans le cas contraire, si les espaces de noms et les types importés par le *using_namespace_directive*s de la déclaration d’espace de noms contiennent plusieurs types accessibles ou des membres statiques de méthode de non-extension ayant un nom `I` et `K`paramètres de type  , le *simple_name* est ambigu et une erreur se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-934">Otherwise, if the namespaces and types imported by the *using_namespace_directive*s of the namespace declaration contain more than one accessible type or non-extension-method static member having name `I` and `K` type parameters, then the *simple_name* is ambiguous and an error occurs.</span></span>

   <span data-ttu-id="b0787-935">Notez que cette étape entière est exactement parallèle à l’étape correspondante dans le traitement d’un *namespace_or_type_name* ([espace de noms et noms de type](basic-concepts.md#namespace-and-type-names)).</span><span class="sxs-lookup"><span data-stu-id="b0787-935">Note that this entire step is exactly parallel to the corresponding step in the processing of a *namespace_or_type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)).</span></span>

*  <span data-ttu-id="b0787-936">Dans le cas contraire, la *simple_name* n’est pas définie et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-936">Otherwise, the *simple_name* is undefined and a compile-time error occurs.</span></span>


### <a name="parenthesized-expressions"></a><span data-ttu-id="b0787-937">Expressions entre parenthèses</span><span class="sxs-lookup"><span data-stu-id="b0787-937">Parenthesized expressions</span></span>

<span data-ttu-id="b0787-938">Une *parenthesized_expression* se compose d’une *expression* placée entre parenthèses.</span><span class="sxs-lookup"><span data-stu-id="b0787-938">A *parenthesized_expression* consists of an *expression* enclosed in parentheses.</span></span>

```antlr
parenthesized_expression
    : '(' expression ')'
    ;
```

<span data-ttu-id="b0787-939">Une *parenthesized_expression* est évaluée en évaluant l' *expression* entre parenthèses.</span><span class="sxs-lookup"><span data-stu-id="b0787-939">A *parenthesized_expression* is evaluated by evaluating the *expression* within the parentheses.</span></span> <span data-ttu-id="b0787-940">Si l' *expression* entre parenthèses désigne un espace de noms ou un type, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-940">If the *expression* within the parentheses denotes a namespace or type, a compile-time error occurs.</span></span> <span data-ttu-id="b0787-941">Dans le cas contraire, le résultat de l' *parenthesized_expression* est le résultat de l’évaluation de l' *expression*contenue.</span><span class="sxs-lookup"><span data-stu-id="b0787-941">Otherwise, the result of the *parenthesized_expression* is the result of the evaluation of the contained *expression*.</span></span>

### <a name="member-access"></a><span data-ttu-id="b0787-942">Accès au membre</span><span class="sxs-lookup"><span data-stu-id="b0787-942">Member access</span></span>

<span data-ttu-id="b0787-943">Un *member_access* se compose d’un *primary_expression*, d’un *predefined_type*ou d’un *qualified_alias_member*, suivi d’un jeton «`.`», suivi d’un *identificateur*, éventuellement suivi d’un *type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="b0787-943">A *member_access* consists of a *primary_expression*, a *predefined_type*, or a *qualified_alias_member*, followed by a "`.`" token, followed by an *identifier*, optionally followed by a *type_argument_list*.</span></span>

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

<span data-ttu-id="b0787-944">La *qualified_alias_member* production est définie dans les [qualificateurs d’alias d’espace de noms](namespaces.md#namespace-alias-qualifiers).</span><span class="sxs-lookup"><span data-stu-id="b0787-944">The *qualified_alias_member* production is defined in [Namespace alias qualifiers](namespaces.md#namespace-alias-qualifiers).</span></span>

<span data-ttu-id="b0787-945">Une *member_access* se présente sous la forme `E.I` ou sous la forme `E.I<A1, ..., Ak>`, où `E` est une expression primaire, `I` est un identificateur unique et `<A1, ..., Ak>` est un *type_argument_list*facultatif.</span><span class="sxs-lookup"><span data-stu-id="b0787-945">A *member_access* is either of the form `E.I` or of the form `E.I<A1, ..., Ak>`, where `E` is a primary-expression, `I` is a single identifier and `<A1, ..., Ak>` is an optional *type_argument_list*.</span></span> <span data-ttu-id="b0787-946">Si aucun *type_argument_list* n’est spécifié, envisagez `K` à zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-946">When no *type_argument_list* is specified, consider `K` to be zero.</span></span>

<span data-ttu-id="b0787-947">Un *member_access* avec un *primary_expression* de type `dynamic` est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="b0787-947">A *member_access* with a *primary_expression* of type `dynamic` is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="b0787-948">Dans ce cas, le compilateur classe l’accès au membre comme un accès à la propriété de type `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-948">In this case the compiler classifies the member access as a property access of type `dynamic`.</span></span> <span data-ttu-id="b0787-949">Les règles ci-dessous pour déterminer la signification de l' *member_access* sont ensuite appliquées au moment de l’exécution, à l’aide du type au moment de l’exécution et non du type de compilation du *primary_expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-949">The rules below to determine the meaning of the *member_access* are then applied at run-time, using the run-time type instead of the compile-time type of the *primary_expression*.</span></span> <span data-ttu-id="b0787-950">Si la classification au moment de l’exécution provoque un groupe de méthodes, l’accès au membre doit être le *primary_expression* d’un *invocation_expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-950">If this run-time classification leads to a method group, then the member access must be the *primary_expression* of an *invocation_expression*.</span></span>

<span data-ttu-id="b0787-951">La *member_access* est évaluée et classée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-951">The *member_access* is evaluated and classified as follows:</span></span>

*  <span data-ttu-id="b0787-952">Si `K` est égal à zéro et `E` est un espace de noms et `E` contient un espace de noms imbriqué avec le nom `I`, le résultat est l’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="b0787-952">If `K` is zero and `E` is a namespace and `E` contains a nested namespace with name `I`, then the result is that namespace.</span></span>
*  <span data-ttu-id="b0787-953">Sinon, si `E` est un espace de noms et `E` contient un type accessible avec le nom `I` et `K`paramètres de type  , le résultat est ce type construit avec les arguments de type donnés.</span><span class="sxs-lookup"><span data-stu-id="b0787-953">Otherwise, if `E` is a namespace and `E` contains an accessible type having name `I` and `K` type parameters, then the result is that type constructed with the given type arguments.</span></span>
*  <span data-ttu-id="b0787-954">Si `E` est un *predefined_type* ou un *primary_expression* classé comme type, si `E` n’est pas un paramètre de type, et si une recherche de membre ([recherche de membre](expressions.md#member-lookup)) de `I` dans `E` avec `K`paramètres de type  produit une correspondance, `E.I` est évalué et classé comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-954">If `E` is a *predefined_type* or a *primary_expression* classified as a type, if `E` is not a type parameter, and if a member lookup ([Member lookup](expressions.md#member-lookup)) of `I` in `E` with `K` type parameters produces a match, then `E.I` is evaluated and classified as follows:</span></span>
   *  <span data-ttu-id="b0787-955">Si `I` identifie un type, le résultat est ce type construit avec les arguments de type donnés.</span><span class="sxs-lookup"><span data-stu-id="b0787-955">If `I` identifies a type, then the result is that type constructed with the given type arguments.</span></span>
   *  <span data-ttu-id="b0787-956">Si `I` identifie une ou plusieurs méthodes, le résultat est un groupe de méthodes sans expression d’instance associée.</span><span class="sxs-lookup"><span data-stu-id="b0787-956">If `I` identifies one or more methods, then the result is a method group with no associated instance expression.</span></span> <span data-ttu-id="b0787-957">Si une liste d’arguments de type a été spécifiée, elle est utilisée pour appeler une méthode générique ([appels de méthode](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="b0787-957">If a type argument list was specified, it is used in calling a generic method ([Method invocations](expressions.md#method-invocations)).</span></span>
   *  <span data-ttu-id="b0787-958">Si `I` identifie une propriété `static`, le résultat est un accès à une propriété sans expression d’instance associée.</span><span class="sxs-lookup"><span data-stu-id="b0787-958">If `I` identifies a `static` property, then the result is a property access with no associated instance expression.</span></span>
   *  <span data-ttu-id="b0787-959">Si `I` identifie un champ de `static` :</span><span class="sxs-lookup"><span data-stu-id="b0787-959">If `I` identifies a `static` field:</span></span>
      * <span data-ttu-id="b0787-960">Si le champ est `readonly` et que la référence se produit à l’extérieur du constructeur statique de la classe ou du struct dans lequel le champ est déclaré, le résultat est une valeur, à savoir la valeur du champ statique `I` dans `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-960">If the field is `readonly` and the reference occurs outside the static constructor of the class or struct in which the field is declared, then the result is a value, namely the value of the static field `I` in `E`.</span></span>
      * <span data-ttu-id="b0787-961">Dans le cas contraire, le résultat est une variable, à savoir le champ statique `I` dans `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-961">Otherwise, the result is a variable, namely the static field `I` in `E`.</span></span>
   *  <span data-ttu-id="b0787-962">Si `I` identifie un événement `static` :</span><span class="sxs-lookup"><span data-stu-id="b0787-962">If `I` identifies a `static` event:</span></span>
      * <span data-ttu-id="b0787-963">Si la référence se produit dans la classe ou le struct dans lequel l’événement est déclaré, et si l’événement a été déclaré sans *event_accessor_declarations* ([événements](classes.md#events)), `E.I` est traité exactement comme si `I` était un champ statique.</span><span class="sxs-lookup"><span data-stu-id="b0787-963">If the reference occurs within the class or struct in which the event is declared, and the event was declared without *event_accessor_declarations* ([Events](classes.md#events)), then `E.I` is processed exactly as if `I` were a static field.</span></span>
      * <span data-ttu-id="b0787-964">Dans le cas contraire, le résultat est un accès à un événement sans expression d’instance associée.</span><span class="sxs-lookup"><span data-stu-id="b0787-964">Otherwise, the result is an event access with no associated instance expression.</span></span>
   *  <span data-ttu-id="b0787-965">Si `I` identifie une constante, le résultat est une valeur, à savoir la valeur de cette constante.</span><span class="sxs-lookup"><span data-stu-id="b0787-965">If `I` identifies a constant, then the result is a value, namely the value of that constant.</span></span>
    * <span data-ttu-id="b0787-966">Si `I` identifie un membre de l’énumération, le résultat est une valeur, à savoir la valeur de ce membre de l’énumération.</span><span class="sxs-lookup"><span data-stu-id="b0787-966">If `I` identifies an enumeration member, then the result is a value, namely the value of that enumeration member.</span></span>
    * <span data-ttu-id="b0787-967">Dans le cas contraire, `E.I` est une référence de membre non valide et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-967">Otherwise, `E.I` is an invalid member reference, and a compile-time error occurs.</span></span>
*  <span data-ttu-id="b0787-968">Si `E` est un accès à la propriété, un indexeur, une variable ou une valeur, le type de qui est `T`et une recherche de membre ([recherche de membre](expressions.md#member-lookup)) de `I` dans `T` avec `K`arguments de type  produit une correspondance, `E.I` est évalué et classé comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-968">If `E` is a property access, indexer access, variable, or value, the type of which is `T`, and a member lookup ([Member lookup](expressions.md#member-lookup)) of `I` in `T` with `K` type arguments produces a match, then `E.I` is evaluated and classified as follows:</span></span>
   *  <span data-ttu-id="b0787-969">Premièrement, si `E` est un accès à une propriété ou un indexeur, la valeur de la propriété ou de l’indexeur est obtenue ([valeurs des expressions](expressions.md#values-of-expressions)) et `E` est reclassifiée comme une valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-969">First, if `E` is a property or indexer access, then the value of the property or indexer access is obtained ([Values of expressions](expressions.md#values-of-expressions)) and `E` is reclassified as a value.</span></span>
   *  <span data-ttu-id="b0787-970">Si `I` identifie une ou plusieurs méthodes, le résultat est un groupe de méthodes avec une expression d’instance associée de `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-970">If `I` identifies one or more methods, then the result is a method group with an associated instance expression of `E`.</span></span> <span data-ttu-id="b0787-971">Si une liste d’arguments de type a été spécifiée, elle est utilisée pour appeler une méthode générique ([appels de méthode](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="b0787-971">If a type argument list was specified, it is used in calling a generic method ([Method invocations](expressions.md#method-invocations)).</span></span>
   *  <span data-ttu-id="b0787-972">Si `I` identifie une propriété d’instance,</span><span class="sxs-lookup"><span data-stu-id="b0787-972">If `I` identifies an instance property,</span></span>
      * <span data-ttu-id="b0787-973">Si `E` est `this`, `I` identifie une propriété implémentée automatiquement ([Propriétés implémentées automatiquement](classes.md#automatically-implemented-properties)) sans accesseur Set, et la référence se produit dans un constructeur d’instance pour un type de classe ou de struct `T`, le résultat est une variable, à savoir le champ de stockage masqué pour la propriété automatique fournie par `I` dans l’instance de `T` donnée par `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-973">If `E` is `this`, `I` identifies an automatically implemented property ([Automatically implemented properties](classes.md#automatically-implemented-properties)) without a setter, and the reference occurs within an instance constructor for a class or struct type `T`, then the result is a variable, namely the hidden backing field for the auto-property given by `I` in the instance of `T` given by `this`.</span></span>
      * <span data-ttu-id="b0787-974">Dans le cas contraire, le résultat est un accès à une propriété avec une expression d’instance associée de `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-974">Otherwise, the result is a property access with an associated instance expression of `E`.</span></span>
   *  <span data-ttu-id="b0787-975">Si `T` est un *class_type* et `I` identifie un champ d’instance de ce *class_type*:</span><span class="sxs-lookup"><span data-stu-id="b0787-975">If `T` is a *class_type* and `I` identifies an instance field of that *class_type*:</span></span>
      * <span data-ttu-id="b0787-976">Si la valeur de `E` est `null`, une `System.NullReferenceException` est levée.</span><span class="sxs-lookup"><span data-stu-id="b0787-976">If the value of `E` is `null`, then a `System.NullReferenceException` is thrown.</span></span>
      * <span data-ttu-id="b0787-977">Sinon, si le champ est `readonly` et que la référence se produit en dehors d’un constructeur d’instance de la classe dans laquelle le champ est déclaré, le résultat est une valeur, à savoir la valeur du champ `I` dans l’objet référencé par `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-977">Otherwise, if the field is `readonly` and the reference occurs outside an instance constructor of the class in which the field is declared, then the result is a value, namely the value of the field `I` in the object referenced by `E`.</span></span>
      * <span data-ttu-id="b0787-978">Dans le cas contraire, le résultat est une variable, à savoir le champ `I` dans l’objet référencé par `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-978">Otherwise, the result is a variable, namely the field `I` in the object referenced by `E`.</span></span>
   *  <span data-ttu-id="b0787-979">Si `T` est un *struct_type* et `I` identifie un champ d’instance de ce *struct_type*:</span><span class="sxs-lookup"><span data-stu-id="b0787-979">If `T` is a *struct_type* and `I` identifies an instance field of that *struct_type*:</span></span>
      * <span data-ttu-id="b0787-980">Si `E` est une valeur, ou si le champ est `readonly` et que la référence se produit en dehors d’un constructeur d’instance du struct dans lequel le champ est déclaré, le résultat est une valeur, à savoir la valeur du champ `I` dans l’instance de struct donnée par `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-980">If `E` is a value, or if the field is `readonly` and the reference occurs outside an instance constructor of the struct in which the field is declared, then the result is a value, namely the value of the field `I` in the struct instance given by `E`.</span></span>
      * <span data-ttu-id="b0787-981">Dans le cas contraire, le résultat est une variable, à savoir le champ `I` dans l’instance de struct donnée par `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-981">Otherwise, the result is a variable, namely the field `I` in the struct instance given by `E`.</span></span>
   *  <span data-ttu-id="b0787-982">Si `I` identifie un événement d’instance :</span><span class="sxs-lookup"><span data-stu-id="b0787-982">If `I` identifies an instance event:</span></span>
      * <span data-ttu-id="b0787-983">Si la référence se produit dans la classe ou le struct dans lequel l’événement est déclaré, et si l’événement a été déclaré sans *event_accessor_declarations* ([événements](classes.md#events)) et si la référence ne se produit pas comme la partie gauche d’un opérateur `+=` ou `-=`, `E.I` est traité exactement comme si `I` était un champ d’instance.</span><span class="sxs-lookup"><span data-stu-id="b0787-983">If the reference occurs within the class or struct in which the event is declared, and the event was declared without *event_accessor_declarations* ([Events](classes.md#events)), and the reference does not occur as the left-hand side of a `+=` or `-=` operator, then `E.I` is processed exactly as if `I` was an instance field.</span></span>
      * <span data-ttu-id="b0787-984">Dans le cas contraire, le résultat est un accès aux événements avec une expression d’instance associée de `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-984">Otherwise, the result is an event access with an associated instance expression of `E`.</span></span>
*  <span data-ttu-id="b0787-985">Sinon, une tentative est faite pour traiter `E.I` en tant qu’appel de méthode d’extension ([appels de méthode d’extension](expressions.md#extension-method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="b0787-985">Otherwise, an attempt is made to process `E.I` as an extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)).</span></span> <span data-ttu-id="b0787-986">En cas d’échec, `E.I` est une référence de membre non valide et une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-986">If this fails, `E.I` is an invalid member reference, and a binding-time error occurs.</span></span>

#### <a name="identical-simple-names-and-type-names"></a><span data-ttu-id="b0787-987">Noms simples et noms de types identiques</span><span class="sxs-lookup"><span data-stu-id="b0787-987">Identical simple names and type names</span></span>

<span data-ttu-id="b0787-988">Dans un accès aux membres de la forme `E.I`, si `E` est un identificateur unique et si la signification de `E` en tant que *simple_name* ([noms simples](expressions.md#simple-names)) est une constante, un champ, une propriété, une variable locale ou un paramètre avec le même type que la signification de `E` comme *type_name* ([espace de noms et noms de type](basic-concepts.md#namespace-and-type-names)), les deux significations possibles de `E` sont autorisées.</span><span class="sxs-lookup"><span data-stu-id="b0787-988">In a member access of the form `E.I`, if `E` is a single identifier, and if the meaning of `E` as a *simple_name* ([Simple names](expressions.md#simple-names)) is a constant, field, property, local variable, or parameter with the same type as the meaning of `E` as a *type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)), then both possible meanings of `E` are permitted.</span></span> <span data-ttu-id="b0787-989">Les deux significations possibles de `E.I` ne sont jamais ambiguës, car `I` doit nécessairement être un membre du type `E` dans les deux cas.</span><span class="sxs-lookup"><span data-stu-id="b0787-989">The two possible meanings of `E.I` are never ambiguous, since `I` must necessarily be a member of the type `E` in both cases.</span></span> <span data-ttu-id="b0787-990">En d’autres termes, la règle autorise simplement l’accès aux membres statiques et aux types imbriqués de `E` où une erreur de compilation se produirait autrement.</span><span class="sxs-lookup"><span data-stu-id="b0787-990">In other words, the rule simply permits access to the static members and nested types of `E` where a compile-time error would otherwise have occurred.</span></span> <span data-ttu-id="b0787-991">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b0787-991">For example:</span></span>
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

#### <a name="grammar-ambiguities"></a><span data-ttu-id="b0787-992">Ambiguïtés grammaticales</span><span class="sxs-lookup"><span data-stu-id="b0787-992">Grammar ambiguities</span></span>

<span data-ttu-id="b0787-993">Les productions pour *simple_name* ([noms simples](expressions.md#simple-names)) et *member_access* ([accès aux membres](expressions.md#member-access)) peuvent donner lieu à des ambiguïtés dans la grammaire des expressions.</span><span class="sxs-lookup"><span data-stu-id="b0787-993">The productions for *simple_name* ([Simple names](expressions.md#simple-names)) and *member_access* ([Member access](expressions.md#member-access)) can give rise to ambiguities in the grammar for expressions.</span></span> <span data-ttu-id="b0787-994">Par exemple, l’instruction suivante :</span><span class="sxs-lookup"><span data-stu-id="b0787-994">For example, the statement:</span></span>
```csharp
F(G<A,B>(7));
```
<span data-ttu-id="b0787-995">peut être interprété comme un appel à `F` avec deux arguments, `G < A` et `B > (7)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-995">could be interpreted as a call to `F` with two arguments, `G < A` and `B > (7)`.</span></span> <span data-ttu-id="b0787-996">Elle peut également être interprétée comme un appel à `F` avec un argument, qui est un appel à une méthode générique `G` avec deux arguments de type et un argument normal.</span><span class="sxs-lookup"><span data-stu-id="b0787-996">Alternatively, it could be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument.</span></span>

<span data-ttu-id="b0787-997">Si une séquence de jetons peut être analysée (en contexte) comme un *simple_name* ([noms simples](expressions.md#simple-names)), *member_access* ([accès aux membres](expressions.md#member-access)) ou *pointer_member_access* ([accès au membre pointeur](unsafe-code.md#pointer-member-access)) se terminant par un *type_argument_list* ([arguments de type](types.md#type-arguments)), le jeton qui suit immédiatement le jeton `>` de fermeture est examiné.</span><span class="sxs-lookup"><span data-stu-id="b0787-997">If a sequence of tokens can be parsed (in context) as a *simple_name* ([Simple names](expressions.md#simple-names)), *member_access* ([Member access](expressions.md#member-access)), or *pointer_member_access* ([Pointer member access](unsafe-code.md#pointer-member-access)) ending with a *type_argument_list* ([Type arguments](types.md#type-arguments)), the token immediately following the closing `>` token is examined.</span></span> <span data-ttu-id="b0787-998">S’il s’agit de l’un des</span><span class="sxs-lookup"><span data-stu-id="b0787-998">If it is one of</span></span>
```csharp
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
```
<span data-ttu-id="b0787-999">le *type_argument_list* est ensuite conservé dans le cadre du *simple_name*, *member_access* ou *pointer_member_access* et toute autre analyse possible de la séquence de jetons est ignorée.</span><span class="sxs-lookup"><span data-stu-id="b0787-999">then the *type_argument_list* is retained as part of the *simple_name*, *member_access* or *pointer_member_access* and any other possible parse of the sequence of tokens is discarded.</span></span> <span data-ttu-id="b0787-1000">Dans le cas contraire, le *type_argument_list* n’est pas considéré comme faisant partie du *simple_name*, *member_access* ou *pointer_member_access*, même s’il n’y a pas d’autre analyse possible de la séquence de jetons.</span><span class="sxs-lookup"><span data-stu-id="b0787-1000">Otherwise, the *type_argument_list* is not considered to be part of the *simple_name*, *member_access* or *pointer_member_access*, even if there is no other possible parse of the sequence of tokens.</span></span> <span data-ttu-id="b0787-1001">Notez que ces règles ne sont pas appliquées lors de l’analyse d’un *type_argument_list* dans un *namespace_or_type_name* ([noms d’espaces de noms et de types](basic-concepts.md#namespace-and-type-names)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1001">Note that these rules are not applied when parsing a *type_argument_list* in a *namespace_or_type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)).</span></span> <span data-ttu-id="b0787-1002">L'instruction</span><span class="sxs-lookup"><span data-stu-id="b0787-1002">The statement</span></span>
```csharp
F(G<A,B>(7));
```
<span data-ttu-id="b0787-1003">en fonction de cette règle, sera interprété comme un appel à `F` avec un argument, qui est un appel à une méthode générique `G` avec deux arguments de type et un argument normal.</span><span class="sxs-lookup"><span data-stu-id="b0787-1003">will, according to this rule, be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument.</span></span> <span data-ttu-id="b0787-1004">Les instructions</span><span class="sxs-lookup"><span data-stu-id="b0787-1004">The statements</span></span>
```csharp
F(G < A, B > 7);
F(G < A, B >> 7);
```
<span data-ttu-id="b0787-1005">est interprété comme un appel à `F` avec deux arguments.</span><span class="sxs-lookup"><span data-stu-id="b0787-1005">will each be interpreted as a call to `F` with two arguments.</span></span> <span data-ttu-id="b0787-1006">L'instruction</span><span class="sxs-lookup"><span data-stu-id="b0787-1006">The statement</span></span>
```csharp
x = F < A > +y;
```
<span data-ttu-id="b0787-1007">est interprété comme un opérateur inférieur à, supérieur à et opérateur unaire plus, comme si l’instruction avait été écrite `x = (F < A) > (+y)`, et non comme un *simple_name* avec un *type_argument_list* suivi d’un opérateur binaire plus.</span><span class="sxs-lookup"><span data-stu-id="b0787-1007">will be interpreted as a less than operator, greater than operator, and unary plus operator, as if the statement had been written `x = (F < A) > (+y)`, instead of as a *simple_name* with a *type_argument_list* followed by a binary plus operator.</span></span> <span data-ttu-id="b0787-1008">Dans l’instruction</span><span class="sxs-lookup"><span data-stu-id="b0787-1008">In the statement</span></span>
```csharp
x = y is C<T> + z;
```
<span data-ttu-id="b0787-1009">les jetons `C<T>` sont interprétés comme un *namespace_or_type_name* avec un *type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1009">the tokens `C<T>` are interpreted as a *namespace_or_type_name* with a *type_argument_list*.</span></span>

### <a name="invocation-expressions"></a><span data-ttu-id="b0787-1010">Appel d’expressions</span><span class="sxs-lookup"><span data-stu-id="b0787-1010">Invocation expressions</span></span>

<span data-ttu-id="b0787-1011">Un *invocation_expression* est utilisé pour appeler une méthode.</span><span class="sxs-lookup"><span data-stu-id="b0787-1011">An *invocation_expression* is used to invoke a method.</span></span>

```antlr
invocation_expression
    : primary_expression '(' argument_list? ')'
    ;
```

<span data-ttu-id="b0787-1012">Un *invocation_expression* est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)) si au moins l’un des éléments suivants est présent :</span><span class="sxs-lookup"><span data-stu-id="b0787-1012">An *invocation_expression* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)) if at least one of the following holds:</span></span>

* <span data-ttu-id="b0787-1013">Le *primary_expression* a des `dynamic`de type au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1013">The *primary_expression* has compile-time type `dynamic`.</span></span>
* <span data-ttu-id="b0787-1014">Au moins un argument de la *argument_list* facultative présente un `dynamic` de type au moment de la compilation et le *primary_expression* n’a pas de type délégué.</span><span class="sxs-lookup"><span data-stu-id="b0787-1014">At least one argument of the optional *argument_list* has compile-time type `dynamic` and the *primary_expression* does not have a delegate type.</span></span>

<span data-ttu-id="b0787-1015">Dans ce cas, le compilateur classe la *invocation_expression* comme une valeur de type `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1015">In this case the compiler classifies the *invocation_expression* as a value of type `dynamic`.</span></span> <span data-ttu-id="b0787-1016">Les règles ci-dessous pour déterminer la signification du *invocation_expression* sont ensuite appliquées au moment de l’exécution, à l’aide du type au moment de l’exécution au lieu du type de compilation de celles du *primary_expression* et des arguments dont le type au moment de la compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1016">The rules below to determine the meaning of the *invocation_expression* are then applied at run-time, using the run-time type instead of the compile-time type of those of the *primary_expression* and arguments which have the compile-time type `dynamic`.</span></span> <span data-ttu-id="b0787-1017">Si le *primary_expression* n’a pas de type `dynamic`au moment de la compilation, l’appel de la méthode subit une vérification limitée au moment de la compilation, comme décrit dans [contrôle de la résolution de surcharge dynamique au moment de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="b0787-1017">If the *primary_expression* does not have compile-time type `dynamic`, then the method invocation undergoes a limited compile time check as described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="b0787-1018">La *primary_expression* d’un *invocation_expression* doit être un groupe de méthodes ou une valeur d’un *delegate_type*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1018">The *primary_expression* of an *invocation_expression* must be a method group or a value of a *delegate_type*.</span></span> <span data-ttu-id="b0787-1019">Si le *primary_expression* est un groupe de méthodes, le *invocation_expression* est un appel de méthode ([appel de méthode](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1019">If the *primary_expression* is a method group, the *invocation_expression* is a method invocation ([Method invocations](expressions.md#method-invocations)).</span></span> <span data-ttu-id="b0787-1020">Si le *primary_expression* est une valeur d’un *delegate_type*, le *invocation_expression* est un appel de délégué ([appel de délégué](expressions.md#delegate-invocations)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1020">If the *primary_expression* is a value of a *delegate_type*, the *invocation_expression* is a delegate invocation ([Delegate invocations](expressions.md#delegate-invocations)).</span></span> <span data-ttu-id="b0787-1021">Si la *primary_expression* n’est ni un groupe de méthodes, ni une valeur d’un *delegate_type*, une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1021">If the *primary_expression* is neither a method group nor a value of a *delegate_type*, a binding-time error occurs.</span></span>

<span data-ttu-id="b0787-1022">La *argument_list* facultative ([listes d’arguments](expressions.md#argument-lists)) fournit des valeurs ou des références de variables pour les paramètres de la méthode.</span><span class="sxs-lookup"><span data-stu-id="b0787-1022">The optional *argument_list* ([Argument lists](expressions.md#argument-lists)) provides values or variable references for the parameters of the method.</span></span>

<span data-ttu-id="b0787-1023">Le résultat de l’évaluation d’un *invocation_expression* est classé comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-1023">The result of evaluating an *invocation_expression* is classified as follows:</span></span>

*  <span data-ttu-id="b0787-1024">Si le *invocation_expression* appelle une méthode ou un délégué qui retourne `void`, le résultat est Nothing.</span><span class="sxs-lookup"><span data-stu-id="b0787-1024">If the *invocation_expression* invokes a method or delegate that returns `void`, the result is nothing.</span></span> <span data-ttu-id="b0787-1025">Une expression classifiée comme Nothing n’est autorisée que dans le contexte d’une *statement_expression* ([instructions d’expression](statements.md#expression-statements)) ou en tant que corps d’une *lambda_expression* ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1025">An expression that is classified as nothing is permitted only in the context of a *statement_expression* ([Expression statements](statements.md#expression-statements)) or as the body of a *lambda_expression* ([Anonymous function expressions](expressions.md#anonymous-function-expressions)).</span></span> <span data-ttu-id="b0787-1026">Sinon, une erreur de temps de liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1026">Otherwise a binding-time error occurs.</span></span>
*  <span data-ttu-id="b0787-1027">Dans le cas contraire, le résultat est une valeur du type retourné par la méthode ou le délégué.</span><span class="sxs-lookup"><span data-stu-id="b0787-1027">Otherwise, the result is a value of the type returned by the method or delegate.</span></span>

#### <a name="method-invocations"></a><span data-ttu-id="b0787-1028">Appels de méthode</span><span class="sxs-lookup"><span data-stu-id="b0787-1028">Method invocations</span></span>

<span data-ttu-id="b0787-1029">Pour un appel de méthode, le *primary_expression* du *invocation_expression* doit être un groupe de méthodes.</span><span class="sxs-lookup"><span data-stu-id="b0787-1029">For a method invocation, the *primary_expression* of the *invocation_expression* must be a method group.</span></span> <span data-ttu-id="b0787-1030">Le groupe de méthodes identifie la méthode à appeler ou l’ensemble de méthodes surchargées à partir desquelles choisir une méthode spécifique à appeler.</span><span class="sxs-lookup"><span data-stu-id="b0787-1030">The method group identifies the one method to invoke or the set of overloaded methods from which to choose a specific method to invoke.</span></span> <span data-ttu-id="b0787-1031">Dans ce dernier cas, la détermination de la méthode spécifique à appeler est basée sur le contexte fourni par les types des arguments de la *argument_list*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1031">In the latter case, determination of the specific method to invoke is based on the context provided by the types of the arguments in the *argument_list*.</span></span>

<span data-ttu-id="b0787-1032">Le traitement au moment de la liaison d’un appel de méthode de la forme `M(A)`, où `M` est un groupe de méthodes (éventuellement avec un *type_argument_list*) et `A` est un *argument_list*facultatif, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-1032">The binding-time processing of a method invocation of the form `M(A)`, where `M` is a method group (possibly including a *type_argument_list*), and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="b0787-1033">L’ensemble des méthodes candidates pour l’appel de méthode est construit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1033">The set of candidate methods for the method invocation is constructed.</span></span> <span data-ttu-id="b0787-1034">Pour chaque méthode `F` associée au groupe de méthodes `M`:</span><span class="sxs-lookup"><span data-stu-id="b0787-1034">For each method `F` associated with the method group `M`:</span></span>
   *  <span data-ttu-id="b0787-1035">Si `F` n’est pas générique, `F` est candidate dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-1035">If `F` is non-generic, `F` is a candidate when:</span></span>
      * <span data-ttu-id="b0787-1036">`M` n’a pas de liste d’arguments de type, et</span><span class="sxs-lookup"><span data-stu-id="b0787-1036">`M` has no type argument list, and</span></span>
      * <span data-ttu-id="b0787-1037">`F` est applicable en ce qui concerne `A` ([fonction membre applicable](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1037">`F` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span>
   *  <span data-ttu-id="b0787-1038">Si `F` est générique et que `M` n’a pas de liste d’arguments de type, `F` est candidate dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-1038">If `F` is generic and `M` has no type argument list, `F` is a candidate when:</span></span>
      * <span data-ttu-id="b0787-1039">L’inférence de type ([inférence de type](expressions.md#type-inference)) est réussie, en déduisant une liste d’arguments de type pour l’appel, et</span><span class="sxs-lookup"><span data-stu-id="b0787-1039">Type inference ([Type inference](expressions.md#type-inference)) succeeds, inferring a list of type arguments for the call, and</span></span>
      * <span data-ttu-id="b0787-1040">Une fois que les arguments de type inférés sont substitués aux paramètres de type de méthode correspondants, tous les types construits dans la liste de paramètres de F répondent à leurs contraintes (qui[satisfont les contraintes](types.md#satisfying-constraints)) et la liste de paramètres de `F` s’applique à `A` ([fonction membre applicable](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1040">Once the inferred type arguments are substituted for the corresponding method type parameters, all constructed types in the parameter list of F satisfy their constraints ([Satisfying constraints](types.md#satisfying-constraints)), and the parameter list of `F` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span>
   *  <span data-ttu-id="b0787-1041">Si `F` est générique et que `M` comprend une liste d’arguments de type, `F` est candidate dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-1041">If `F` is generic and `M` includes a type argument list, `F` is a candidate when:</span></span>
      * <span data-ttu-id="b0787-1042">`F` a le même nombre de paramètres de type de méthode que ceux qui ont été fournis dans la liste d’arguments de type, et</span><span class="sxs-lookup"><span data-stu-id="b0787-1042">`F` has the same number of method type parameters as were supplied in the type argument list, and</span></span>
      * <span data-ttu-id="b0787-1043">Une fois que les arguments de type sont substitués aux paramètres de type de méthode correspondants, tous les types construits dans la liste de paramètres de F répondent à leurs contraintes (qui[satisfont les contraintes](types.md#satisfying-constraints)) et la liste de paramètres de `F` s’applique à `A` ([fonction membre applicable](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1043">Once the type arguments are substituted for the corresponding method type parameters, all constructed types in the parameter list of F satisfy their constraints ([Satisfying constraints](types.md#satisfying-constraints)), and the parameter list of `F` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span>
*  <span data-ttu-id="b0787-1044">L’ensemble des méthodes candidates est réduit pour contenir uniquement des méthodes des types les plus dérivés : pour chaque méthode `C.F` dans le jeu, où `C` est le type dans lequel la méthode `F` est déclarée, toutes les méthodes déclarées dans un type de base de `C` sont supprimées de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="b0787-1044">The set of candidate methods is reduced to contain only methods from the most derived types: For each method `C.F` in the set, where `C` is the type in which the method `F` is declared, all methods declared in a base type of `C` are removed from the set.</span></span> <span data-ttu-id="b0787-1045">En outre, si `C` est un type de classe autre que `object`, toutes les méthodes déclarées dans un type interface sont supprimées de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="b0787-1045">Furthermore, if `C` is a class type other than `object`, all methods declared in an interface type are removed from the set.</span></span> <span data-ttu-id="b0787-1046">(Cette dernière règle a un impact uniquement lorsque le groupe de méthodes était le résultat d’une recherche de membre sur un paramètre de type ayant une classe de base effective autre que Object et un ensemble d’interfaces effectif non vide.)</span><span class="sxs-lookup"><span data-stu-id="b0787-1046">(This latter rule only has affect when the method group was the result of a member lookup on a type parameter having an effective base class other than object and a non-empty effective interface set.)</span></span>
*  <span data-ttu-id="b0787-1047">Si le jeu résultant de méthodes candidates est vide, le traitement suivant des étapes suivantes est abandonné, et une tentative est effectuée pour traiter l’appel comme un appel de méthode d’extension ([appels de méthode d’extension](expressions.md#extension-method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1047">If the resulting set of candidate methods is empty, then further processing along the following steps are abandoned, and instead an attempt is made to process the invocation as an extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)).</span></span> <span data-ttu-id="b0787-1048">En cas d’échec, il n’existe aucune méthode applicable et une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1048">If this fails, then no applicable methods exist, and a binding-time error occurs.</span></span>
*  <span data-ttu-id="b0787-1049">La meilleure méthode de l’ensemble de méthodes candidates est identifiée à l’aide des règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="b0787-1049">The best method of the set of candidate methods is identified using the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="b0787-1050">Si une seule meilleure méthode ne peut pas être identifiée, l’appel de la méthode est ambigu et une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1050">If a single best method cannot be identified, the method invocation is ambiguous, and a binding-time error occurs.</span></span> <span data-ttu-id="b0787-1051">Lors de l’exécution de la résolution de surcharge, les paramètres d’une méthode générique sont pris en compte après avoir remplacé les arguments de type (fournis ou déduits) pour les paramètres de type de méthode correspondants.</span><span class="sxs-lookup"><span data-stu-id="b0787-1051">When performing overload resolution, the parameters of a generic method are considered after substituting the type arguments (supplied or inferred) for the corresponding method type parameters.</span></span>
*  <span data-ttu-id="b0787-1052">La validation finale de la meilleure méthode choisie est effectuée :</span><span class="sxs-lookup"><span data-stu-id="b0787-1052">Final validation of the chosen best method is performed:</span></span>
   * <span data-ttu-id="b0787-1053">La méthode est validée dans le contexte du groupe de méthodes : si la meilleure méthode est une méthode statique, le groupe de méthodes doit résulter d’un *simple_name* ou d’un *member_access* par le biais d’un type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1053">The method is validated in the context of the method group: If the best method is a static method, the method group must have resulted from a *simple_name* or a *member_access* through a type.</span></span> <span data-ttu-id="b0787-1054">Si la meilleure méthode est une méthode d’instance, le groupe de méthodes doit résulter d’une *simple_name*, d’une *member_access* par le biais d’une variable ou d’une valeur ou d’un *base_access*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1054">If the best method is an instance method, the method group must have resulted from a *simple_name*, a *member_access* through a variable or value, or a *base_access*.</span></span> <span data-ttu-id="b0787-1055">Si aucune de ces conditions n’est remplie, une erreur de liaison au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1055">If neither of these requirements is true, a binding-time error occurs.</span></span>
   * <span data-ttu-id="b0787-1056">Si la meilleure méthode est une méthode générique, les arguments de type (fournis ou déduits) sont vérifiés par rapport aux contraintes ([contraintes satisfaisantes](types.md#satisfying-constraints)) déclarées dans la méthode générique.</span><span class="sxs-lookup"><span data-stu-id="b0787-1056">If the best method is a generic method, the type arguments (supplied or inferred) are checked against the constraints ([Satisfying constraints](types.md#satisfying-constraints)) declared on the generic method.</span></span> <span data-ttu-id="b0787-1057">Si un argument de type ne satisfait pas la ou les contraintes correspondantes sur le paramètre de type, une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1057">If any type argument does not satisfy the corresponding constraint(s) on the type parameter, a binding-time error occurs.</span></span>

<span data-ttu-id="b0787-1058">Une fois qu’une méthode a été sélectionnée et validée au moment de la liaison par les étapes ci-dessus, l’appel réel au moment de l’exécution est traité en fonction des règles d’appel des membres de fonction décrites dans [vérification de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)par la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1058">Once a method has been selected and validated at binding-time by the above steps, the actual run-time invocation is processed according to the rules of function member invocation described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="b0787-1059">L’effet intuitif des règles de résolution décrites ci-dessus est le suivant : pour localiser la méthode particulière appelée par un appel de méthode, commencez avec le type indiqué par l’appel de méthode et remontez la chaîne d’héritage jusqu’à au moins un applicable, une déclaration de méthode accessible, non substituée a été trouvée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1059">The intuitive effect of the resolution rules described above is as follows: To locate the particular method invoked by a method invocation, start with the type indicated by the method invocation and proceed up the inheritance chain until at least one applicable, accessible, non-override method declaration is found.</span></span> <span data-ttu-id="b0787-1060">Effectuez ensuite l’inférence de type et la résolution de surcharge sur l’ensemble des méthodes applicables, accessibles et non substituées déclarées dans ce type et appelez la méthode sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1060">Then perform type inference and overload resolution on the set of applicable, accessible, non-override methods declared in that type and invoke the method thus selected.</span></span> <span data-ttu-id="b0787-1061">Si aucune méthode n’a été trouvée, essayez plutôt de traiter l’appel comme un appel de méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="b0787-1061">If no method was found, try instead to process the invocation as an extension method invocation.</span></span>

#### <a name="extension-method-invocations"></a><span data-ttu-id="b0787-1062">Appels de méthode d’extension</span><span class="sxs-lookup"><span data-stu-id="b0787-1062">Extension method invocations</span></span>

<span data-ttu-id="b0787-1063">Dans un appel de méthode ([appels sur des instances boxed](expressions.md#invocations-on-boxed-instances)) de l’un des formulaires</span><span class="sxs-lookup"><span data-stu-id="b0787-1063">In a method invocation ([Invocations on boxed instances](expressions.md#invocations-on-boxed-instances)) of one of the forms</span></span>
```csharp
expr . identifier ( )

expr . identifier ( args )

expr . identifier < typeargs > ( )

expr . identifier < typeargs > ( args )
```
<span data-ttu-id="b0787-1064">Si le traitement normal de l’appel ne trouve aucune méthode applicable, une tentative est faite pour traiter la construction comme un appel de méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="b0787-1064">if the normal processing of the invocation finds no applicable methods, an attempt is made to process the construct as an extension method invocation.</span></span> <span data-ttu-id="b0787-1065">Si *expr* ou l’un des *arguments* a le type au moment de la compilation `dynamic`, les méthodes d’extension ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="b0787-1065">If *expr* or any of the *args* has compile-time type `dynamic`, extension methods will not apply.</span></span>

<span data-ttu-id="b0787-1066">L’objectif est de trouver le meilleur *type_name* `C`, afin que l’appel de la méthode statique correspondante puisse avoir lieu :</span><span class="sxs-lookup"><span data-stu-id="b0787-1066">The objective is to find the best *type_name* `C`, so that the corresponding static method invocation can take place:</span></span>
```csharp
C . identifier ( expr )

C . identifier ( expr , args )

C . identifier < typeargs > ( expr )

C . identifier < typeargs > ( expr , args )
```

<span data-ttu-id="b0787-1067">Une méthode d’extension `Ci.Mj` est ***éligible*** dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-1067">An extension method `Ci.Mj` is ***eligible*** if:</span></span>

*  <span data-ttu-id="b0787-1068">`Ci` est une classe non imbriquée et non générique</span><span class="sxs-lookup"><span data-stu-id="b0787-1068">`Ci` is a non-generic, non-nested class</span></span>
*  <span data-ttu-id="b0787-1069">Le nom de `Mj` est *identifier*</span><span class="sxs-lookup"><span data-stu-id="b0787-1069">The name of `Mj` is *identifier*</span></span>
*  <span data-ttu-id="b0787-1070">`Mj` est accessible et applicable lorsqu’il est appliqué aux arguments en tant que méthode statique, comme indiqué ci-dessus</span><span class="sxs-lookup"><span data-stu-id="b0787-1070">`Mj` is accessible and applicable when applied to the arguments as a static method as shown above</span></span>
*  <span data-ttu-id="b0787-1071">Une identité, une référence ou une conversion boxing implicite existe entre *expr* et le type du premier paramètre de `Mj`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1071">An implicit identity, reference or boxing conversion exists from *expr* to the type of the first parameter of `Mj`.</span></span>

<span data-ttu-id="b0787-1072">La recherche de `C` se déroule comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-1072">The search for `C` proceeds as follows:</span></span>

*  <span data-ttu-id="b0787-1073">À partir de la déclaration d’espace de noms englobante la plus proche, en continuant avec chaque déclaration d’espace de noms englobant, et se terminant par l’unité de compilation conteneur, des tentatives successives sont effectuées pour trouver un ensemble candidat de méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="b0787-1073">Starting with the closest enclosing namespace declaration, continuing with each enclosing namespace declaration, and ending with the containing compilation unit, successive attempts are made to find a candidate set of extension methods:</span></span>
   * <span data-ttu-id="b0787-1074">Si l’espace de noms ou l’unité de compilation donné contient directement des déclarations de type non génériques `Ci` avec les méthodes d’extension éligibles `Mj`, l’ensemble de ces méthodes d’extension est l’ensemble de candidats.</span><span class="sxs-lookup"><span data-stu-id="b0787-1074">If the given namespace or compilation unit directly contains non-generic type declarations `Ci` with eligible extension methods `Mj`, then the set of those extension methods is the candidate set.</span></span>
   * <span data-ttu-id="b0787-1075">Si les types `Ci` importés par *using_static_declarations* et directement déclarés dans les espaces de noms importés par *using_namespace_directive*dans l’espace de noms ou l’unité de compilation donnés contiennent directement les méthodes d’extension éligibles `Mj`, l’ensemble de ces méthodes d’extension est l’ensemble de candidats.</span><span class="sxs-lookup"><span data-stu-id="b0787-1075">If types `Ci` imported by *using_static_declarations* and directly declared in namespaces imported by *using_namespace_directive*s in the given namespace or compilation unit directly contain eligible extension methods `Mj`, then the set of those extension methods is the candidate set.</span></span>
*  <span data-ttu-id="b0787-1076">Si aucun jeu de candidats n’est trouvé dans une déclaration d’espace de noms ou une unité de compilation englobante, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1076">If no candidate set is found in any enclosing namespace declaration or compilation unit, a compile-time error occurs.</span></span>
*  <span data-ttu-id="b0787-1077">Sinon, la résolution de surcharge est appliquée à l’ensemble de candidats comme décrit dans ([résolution de surcharge](expressions.md#overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1077">Otherwise, overload resolution is applied to the candidate set as described in ([Overload resolution](expressions.md#overload-resolution)).</span></span> <span data-ttu-id="b0787-1078">Si aucune meilleure méthode n’est trouvée, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1078">If no single best method is found, a compile-time error occurs.</span></span>
*  <span data-ttu-id="b0787-1079">`C` est le type dans lequel la meilleure méthode est déclarée en tant que méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="b0787-1079">`C` is the type within which the best method is declared as an extension method.</span></span>

<span data-ttu-id="b0787-1080">À l’aide de `C` en tant que cible, l’appel de méthode est ensuite traité comme un appel de méthode statique ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1080">Using `C` as a target, the method call is then processed as a static method invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span>

<span data-ttu-id="b0787-1081">Les règles précédentes signifient que les méthodes d’instance sont prioritaires sur les méthodes d’extension, que les méthodes d’extension disponibles dans les déclarations d’espace de noms interne ont la priorité sur les méthodes d’extension disponibles dans les déclarations d’espaces de noms externes, et cette extension les méthodes déclarées directement dans un espace de noms ont priorité sur les méthodes d’extension importées dans ce même espace de noms avec une directive d’espace de noms using.</span><span class="sxs-lookup"><span data-stu-id="b0787-1081">The preceding rules mean that instance methods take precedence over extension methods, that extension methods available in inner namespace declarations take precedence over extension methods available in outer namespace declarations, and that extension methods declared directly in a namespace take precedence over extension methods imported into that same namespace with a using namespace directive.</span></span> <span data-ttu-id="b0787-1082">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b0787-1082">For example:</span></span>
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

<span data-ttu-id="b0787-1083">Dans l’exemple, la méthode de `B`est prioritaire sur la première méthode d’extension, et la méthode de `C`est prioritaire sur les deux méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="b0787-1083">In the example, `B`'s method takes precedence over the first extension method, and `C`'s method takes precedence over both extension methods.</span></span>

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

<span data-ttu-id="b0787-1084">La sortie de cet exemple est la suivante :</span><span class="sxs-lookup"><span data-stu-id="b0787-1084">The output of this example is:</span></span>
```console
E.F(1)
D.G(2)
C.H(3)
```
<span data-ttu-id="b0787-1085">`D.G` a priorité sur `C.G`et `E.F` est prioritaire sur les `D.F` et les `C.F`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1085">`D.G` takes precedence over `C.G`, and `E.F` takes precedence over both `D.F` and `C.F`.</span></span>

#### <a name="delegate-invocations"></a><span data-ttu-id="b0787-1086">Appels délégués</span><span class="sxs-lookup"><span data-stu-id="b0787-1086">Delegate invocations</span></span>

<span data-ttu-id="b0787-1087">Pour un appel de délégué, le *primary_expression* du *invocation_expression* doit être une valeur d’un *delegate_type*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1087">For a delegate invocation, the *primary_expression* of the *invocation_expression* must be a value of a *delegate_type*.</span></span> <span data-ttu-id="b0787-1088">En outre, si l’on considère que le *delegate_type* être une fonction membre avec la même liste de paramètres que l' *delegate_type*, la *delegate_type* doit être applicable ([membre de fonction applicable](expressions.md#applicable-function-member)) en ce qui concerne la *argument_list* du *invocation_expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1088">Furthermore, considering the *delegate_type* to be a function member with the same parameter list as the *delegate_type*, the *delegate_type* must be applicable ([Applicable function member](expressions.md#applicable-function-member)) with respect to the *argument_list* of the *invocation_expression*.</span></span>

<span data-ttu-id="b0787-1089">Le traitement au moment de l’exécution d’un appel de délégué de la forme `D(A)`, où `D` est une *primary_expression* d’un *delegate_type* et `A` est un *argument_list*facultatif, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-1089">The run-time processing of a delegate invocation of the form `D(A)`, where `D` is a *primary_expression* of a *delegate_type* and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="b0787-1090">`D` est évalué.</span><span class="sxs-lookup"><span data-stu-id="b0787-1090">`D` is evaluated.</span></span> <span data-ttu-id="b0787-1091">Si cette évaluation provoque une exception, aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1091">If this evaluation causes an exception, no further steps are executed.</span></span>
*  <span data-ttu-id="b0787-1092">La valeur de `D` est vérifiée comme valide.</span><span class="sxs-lookup"><span data-stu-id="b0787-1092">The value of `D` is checked to be valid.</span></span> <span data-ttu-id="b0787-1093">Si la valeur de `D` est `null`, une `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1093">If the value of `D` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="b0787-1094">Dans le cas contraire, `D` est une référence à une instance de délégué.</span><span class="sxs-lookup"><span data-stu-id="b0787-1094">Otherwise, `D` is a reference to a delegate instance.</span></span> <span data-ttu-id="b0787-1095">Les appels de membre de fonction ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) sont exécutés sur chacune des entités pouvant être appelées dans la liste d’appel du délégué.</span><span class="sxs-lookup"><span data-stu-id="b0787-1095">Function member invocations ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) are performed on each of the callable entities in the invocation list of the delegate.</span></span> <span data-ttu-id="b0787-1096">Pour les entités pouvant être appelées constituées d’une méthode d’instance et d’instance, l’instance de l’appel est l’instance contenue dans l’entité pouvant être appelée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1096">For callable entities consisting of an instance and instance method, the instance for the invocation is the instance contained in the callable entity.</span></span>

### <a name="element-access"></a><span data-ttu-id="b0787-1097">Accès aux éléments</span><span class="sxs-lookup"><span data-stu-id="b0787-1097">Element access</span></span>

<span data-ttu-id="b0787-1098">Un *element_access* se compose d’un *primary_no_array_creation_expression*, suivi d’un jeton «`[`», suivi d’un *argument_list*, suivi d’un jeton «`]`».</span><span class="sxs-lookup"><span data-stu-id="b0787-1098">An *element_access* consists of a *primary_no_array_creation_expression*, followed by a "`[`" token, followed by an *argument_list*, followed by a "`]`" token.</span></span> <span data-ttu-id="b0787-1099">Le *argument_list* se compose d’un ou plusieurs *arguments*, séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="b0787-1099">The *argument_list* consists of one or more *argument*s, separated by commas.</span></span>

```antlr
element_access
    : primary_no_array_creation_expression '[' expression_list ']'
    ;
```

<span data-ttu-id="b0787-1100">La *argument_list* d’un *element_access* n’est pas autorisée à contenir des arguments `ref` ou `out`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1100">The *argument_list* of an *element_access* is not allowed to contain `ref` or `out` arguments.</span></span>

<span data-ttu-id="b0787-1101">Un *element_access* est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)) si au moins l’un des éléments suivants est présent :</span><span class="sxs-lookup"><span data-stu-id="b0787-1101">An *element_access* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)) if at least one of the following holds:</span></span>

* <span data-ttu-id="b0787-1102">Le *primary_no_array_creation_expression* a des `dynamic`de type au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1102">The *primary_no_array_creation_expression* has compile-time type `dynamic`.</span></span>
* <span data-ttu-id="b0787-1103">Au moins une expression de la *argument_list* a un type au moment de la compilation `dynamic` et le *primary_no_array_creation_expression* n’a pas de type tableau.</span><span class="sxs-lookup"><span data-stu-id="b0787-1103">At least one expression of the *argument_list* has compile-time type `dynamic` and the *primary_no_array_creation_expression* does not have an array type.</span></span>

<span data-ttu-id="b0787-1104">Dans ce cas, le compilateur classe la *element_access* comme une valeur de type `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1104">In this case the compiler classifies the *element_access* as a value of type `dynamic`.</span></span> <span data-ttu-id="b0787-1105">Les règles ci-dessous pour déterminer la signification du *element_access* sont ensuite appliquées au moment de l’exécution, à l’aide du type au moment de l’exécution au lieu du type de compilation de celles du *primary_no_array_creation_expression* et des expressions *argument_list* qui ont le type au moment de la compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1105">The rules below to determine the meaning of the *element_access* are then applied at run-time, using the run-time type instead of the compile-time type of those of the *primary_no_array_creation_expression* and *argument_list* expressions which have the compile-time type `dynamic`.</span></span> <span data-ttu-id="b0787-1106">Si le *primary_no_array_creation_expression* n’a pas de type `dynamic`au moment de la compilation, l’accès à l’élément subit une vérification limitée au moment de la compilation, comme décrit dans [vérification de la résolution de surcharge dynamique au moment de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="b0787-1106">If the *primary_no_array_creation_expression* does not have compile-time type `dynamic`, then the element access undergoes a limited compile time check as described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="b0787-1107">Si la *primary_no_array_creation_expression* d’un *element_access* est une valeur d’un *array_type*, l' *element_access* est un accès à un tableau ([accès au tableau](expressions.md#array-access)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1107">If the *primary_no_array_creation_expression* of an *element_access* is a value of an *array_type*, the *element_access* is an array access ([Array access](expressions.md#array-access)).</span></span> <span data-ttu-id="b0787-1108">Sinon, le *primary_no_array_creation_expression* doit être une variable ou une valeur d’un type de classe, de struct ou d’interface qui possède un ou plusieurs membres de l’indexeur, auquel cas le *element_access* est un accès à un indexeur ([accès à l’indexeur](expressions.md#indexer-access)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1108">Otherwise, the *primary_no_array_creation_expression* must be a variable or value of a class, struct, or interface type that has one or more indexer members, in which case the *element_access* is an indexer access ([Indexer access](expressions.md#indexer-access)).</span></span>

#### <a name="array-access"></a><span data-ttu-id="b0787-1109">Accès aux tableaux</span><span class="sxs-lookup"><span data-stu-id="b0787-1109">Array access</span></span>

<span data-ttu-id="b0787-1110">Pour un accès à un tableau, le *primary_no_array_creation_expression* du *element_access* doit être une valeur d’un *array_type*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1110">For an array access, the *primary_no_array_creation_expression* of the *element_access* must be a value of an *array_type*.</span></span> <span data-ttu-id="b0787-1111">En outre, le *argument_list* d’un accès à un tableau n’est pas autorisé à contenir des arguments nommés. Le nombre d’expressions dans le *argument_list* doit être le même que le rang du *array_type*, et chaque expression doit être de type `int`, `uint`, `long`, `ulong`, ou doit être implicitement convertible en un ou plusieurs de ces types.</span><span class="sxs-lookup"><span data-stu-id="b0787-1111">Furthermore, the *argument_list* of an array access is not allowed to contain named arguments.The number of expressions in the *argument_list* must be the same as the rank of the *array_type*, and each expression must be of type `int`, `uint`, `long`, `ulong`, or must be implicitly convertible to one or more of these types.</span></span>

<span data-ttu-id="b0787-1112">Le résultat de l’évaluation d’un accès à un tableau est une variable du type d’élément du tableau, à savoir l’élément de tableau sélectionné par la ou les valeurs de la ou des expressions de la *argument_list*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1112">The result of evaluating an array access is a variable of the element type of the array, namely the array element selected by the value(s) of the expression(s) in the *argument_list*.</span></span>

<span data-ttu-id="b0787-1113">Le traitement au moment de l’exécution d’un accès à un tableau de la forme `P[A]`, où `P` est un *primary_no_array_creation_expression* d’un *array_type* et `A` est un *argument_list*, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-1113">The run-time processing of an array access of the form `P[A]`, where `P` is a *primary_no_array_creation_expression* of an *array_type* and `A` is an *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="b0787-1114">`P` est évalué.</span><span class="sxs-lookup"><span data-stu-id="b0787-1114">`P` is evaluated.</span></span> <span data-ttu-id="b0787-1115">Si cette évaluation provoque une exception, aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1115">If this evaluation causes an exception, no further steps are executed.</span></span>
*  <span data-ttu-id="b0787-1116">Les expressions d’index des *argument_list* sont évaluées dans l’ordre, de gauche à droite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1116">The index expressions of the *argument_list* are evaluated in order, from left to right.</span></span> <span data-ttu-id="b0787-1117">Après l’évaluation de chaque expression d’index, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) vers l’un des types suivants est effectuée : `int`, `uint`, `long`, `ulong`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1117">Following evaluation of each index expression, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) to one of the following types is performed: `int`, `uint`, `long`, `ulong`.</span></span> <span data-ttu-id="b0787-1118">Le premier type de cette liste pour lequel une conversion implicite existe est choisi.</span><span class="sxs-lookup"><span data-stu-id="b0787-1118">The first type in this list for which an implicit conversion exists is chosen.</span></span> <span data-ttu-id="b0787-1119">Par exemple, si l’expression d’index est de type `short`, une conversion implicite vers `int` est effectuée, puisque les conversions implicites de `short` en `int` et de `short` à `long` sont possibles.</span><span class="sxs-lookup"><span data-stu-id="b0787-1119">For instance, if the index expression is of type `short` then an implicit conversion to `int` is performed, since implicit conversions from `short` to `int` and from `short` to `long` are possible.</span></span> <span data-ttu-id="b0787-1120">Si l’évaluation d’une expression d’index ou de la conversion implicite suivante provoque une exception, aucune autre expression d’index n’est évaluée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1120">If evaluation of an index expression or the subsequent implicit conversion causes an exception, then no further index expressions are evaluated and no further steps are executed.</span></span>
*  <span data-ttu-id="b0787-1121">La valeur de `P` est vérifiée comme valide.</span><span class="sxs-lookup"><span data-stu-id="b0787-1121">The value of `P` is checked to be valid.</span></span> <span data-ttu-id="b0787-1122">Si la valeur de `P` est `null`, une `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1122">If the value of `P` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="b0787-1123">La valeur de chaque expression dans le *argument_list* est vérifiée par rapport aux limites réelles de chaque dimension de l’instance de tableau référencée par `P`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1123">The value of each expression in the *argument_list* is checked against the actual bounds of each dimension of the array instance referenced by `P`.</span></span> <span data-ttu-id="b0787-1124">Si une ou plusieurs valeurs sont hors limites, une `System.IndexOutOfRangeException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1124">If one or more values are out of range, a `System.IndexOutOfRangeException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="b0787-1125">L’emplacement de l’élément de tableau donné par la ou les expressions d’index est calculé et cet emplacement devient le résultat de l’accès au tableau.</span><span class="sxs-lookup"><span data-stu-id="b0787-1125">The location of the array element given by the index expression(s) is computed, and this location becomes the result of the array access.</span></span>

#### <a name="indexer-access"></a><span data-ttu-id="b0787-1126">Accès aux indexeurs</span><span class="sxs-lookup"><span data-stu-id="b0787-1126">Indexer access</span></span>

<span data-ttu-id="b0787-1127">Pour un accès à un indexeur, le *primary_no_array_creation_expression* du *element_access* doit être une variable ou une valeur d’un type de classe, de struct ou d’interface, et ce type doit implémenter un ou plusieurs indexeurs applicables en ce qui concerne le *argument_list* du *element_access*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1127">For an indexer access, the *primary_no_array_creation_expression* of the *element_access* must be a variable or value of a class, struct, or interface type, and this type must implement one or more indexers that are applicable with respect to the *argument_list* of the *element_access*.</span></span>

<span data-ttu-id="b0787-1128">Le traitement au moment de la liaison d’un accès à un indexeur de la forme `P[A]`, où `P` est un *primary_no_array_creation_expression* d’un type de classe, de struct ou d’interface `T`, et `A` est un *argument_list*, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-1128">The binding-time processing of an indexer access of the form `P[A]`, where `P` is a *primary_no_array_creation_expression* of a class, struct, or interface type `T`, and `A` is an *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="b0787-1129">L’ensemble d’indexeurs fourni par `T` est construit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1129">The set of indexers provided by `T` is constructed.</span></span> <span data-ttu-id="b0787-1130">L’ensemble se compose de tous les indexeurs déclarés dans `T` ou d’un type de base de `T` qui ne sont pas des déclarations `override` et sont accessibles dans le contexte actuel ([accès aux membres](basic-concepts.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1130">The set consists of all indexers declared in `T` or a base type of `T` that are not `override` declarations and are accessible in the current context ([Member access](basic-concepts.md#member-access)).</span></span>
*  <span data-ttu-id="b0787-1131">Le jeu est réduit aux indexeurs applicables et non masqués par d’autres indexeurs.</span><span class="sxs-lookup"><span data-stu-id="b0787-1131">The set is reduced to those indexers that are applicable and not hidden by other indexers.</span></span> <span data-ttu-id="b0787-1132">Les règles suivantes sont appliquées à chaque indexeur `S.I` dans l’ensemble, où `S` est le type dans lequel l’indexeur `I` est déclaré :</span><span class="sxs-lookup"><span data-stu-id="b0787-1132">The following rules are applied to each indexer `S.I` in the set, where `S` is the type in which the indexer `I` is declared:</span></span>
   * <span data-ttu-id="b0787-1133">Si `I` n’est pas applicable en ce qui concerne `A` ([fonction membre applicable](expressions.md#applicable-function-member)), `I` est supprimé de l’ensemble.</span><span class="sxs-lookup"><span data-stu-id="b0787-1133">If `I` is not applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)), then `I` is removed from the set.</span></span>
   * <span data-ttu-id="b0787-1134">Si `I` est applicable en ce qui concerne `A` ([fonction membre applicable](expressions.md#applicable-function-member)), tous les indexeurs déclarés dans un type de base de `S` sont supprimés du jeu.</span><span class="sxs-lookup"><span data-stu-id="b0787-1134">If `I` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)), then all indexers declared in a base type of `S` are removed from the set.</span></span>
   * <span data-ttu-id="b0787-1135">Si `I` est applicable en ce qui concerne `A` ([fonction membre applicable](expressions.md#applicable-function-member)) et `S` est un type de classe autre que `object`, tous les indexeurs déclarés dans une interface sont supprimés du jeu.</span><span class="sxs-lookup"><span data-stu-id="b0787-1135">If `I` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)) and `S` is a class type other than `object`, all indexers declared in an interface are removed from the set.</span></span>
*  <span data-ttu-id="b0787-1136">Si le jeu d’indexeurs candidats obtenu est vide, aucun indexeur applicable n’existe et une erreur de liaison est générée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1136">If the resulting set of candidate indexers is empty, then no applicable indexers exist, and a binding-time error occurs.</span></span>
*  <span data-ttu-id="b0787-1137">Le meilleur indexeur de l’ensemble des indexeurs candidats est identifié à l’aide des règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="b0787-1137">The best indexer of the set of candidate indexers is identified using the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="b0787-1138">Si un seul meilleur indexeur ne peut pas être identifié, l’accès à l’indexeur est ambigu et une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1138">If a single best indexer cannot be identified, the indexer access is ambiguous, and a binding-time error occurs.</span></span>
*  <span data-ttu-id="b0787-1139">Les expressions d’index des *argument_list* sont évaluées dans l’ordre, de gauche à droite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1139">The index expressions of the *argument_list* are evaluated in order, from left to right.</span></span> <span data-ttu-id="b0787-1140">Le résultat du traitement de l’accès à l’indexeur est une expression classée comme un accès à un indexeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1140">The result of processing the indexer access is an expression classified as an indexer access.</span></span> <span data-ttu-id="b0787-1141">L’expression d’accès de l’indexeur fait référence à l’indexeur déterminé à l’étape ci-dessus et a une expression d’instance associée de `P` et une liste d’arguments associée de `A`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1141">The indexer access expression references the indexer determined in the step above, and has an associated instance expression of `P` and an associated argument list of `A`.</span></span>

<span data-ttu-id="b0787-1142">En fonction du contexte dans lequel il est utilisé, un accès à l’indexeur entraîne l’appel de l' *accesseur Get* ou de l' *accesseur Set* de l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1142">Depending on the context in which it is used, an indexer access causes invocation of either the *get accessor* or the *set accessor* of the indexer.</span></span> <span data-ttu-id="b0787-1143">Si l’accès à l’indexeur est la cible d’une assignation, l' *accesseur Set* est appelé pour assigner une nouvelle valeur ([assignation simple](expressions.md#simple-assignment)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1143">If the indexer access is the target of an assignment, the *set accessor* is invoked to assign a new value ([Simple assignment](expressions.md#simple-assignment)).</span></span> <span data-ttu-id="b0787-1144">Dans tous les autres cas, l' *accesseur Get* est appelé pour obtenir la valeur actuelle ([valeurs des expressions](expressions.md#values-of-expressions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1144">In all other cases, the *get accessor* is invoked to obtain the current value ([Values of expressions](expressions.md#values-of-expressions)).</span></span>

### <a name="this-access"></a><span data-ttu-id="b0787-1145">Cet accès</span><span class="sxs-lookup"><span data-stu-id="b0787-1145">This access</span></span>

<span data-ttu-id="b0787-1146">Un *This_Access* se compose du mot réservé `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1146">A *this_access* consists of the reserved word `this`.</span></span>

```antlr
this_access
    : 'this'
    ;
```

<span data-ttu-id="b0787-1147">Une *This_Access* est autorisée uniquement dans le *bloc* d’un constructeur d’instance, d’une méthode d’instance ou d’un accesseur d’instance.</span><span class="sxs-lookup"><span data-stu-id="b0787-1147">A *this_access* is permitted only in the *block* of an instance constructor, an instance method, or an instance accessor.</span></span> <span data-ttu-id="b0787-1148">Il a l’une des significations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-1148">It has one of the following meanings:</span></span>

*  <span data-ttu-id="b0787-1149">Lorsque `this` est utilisé dans un *primary_expression* au sein d’un constructeur d’instance d’une classe, il est classé comme une valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1149">When `this` is used in a *primary_expression* within an instance constructor of a class, it is classified as a value.</span></span> <span data-ttu-id="b0787-1150">Le type de la valeur est le type d’instance ([type d’instance](classes.md#the-instance-type)) de la classe dans laquelle l’utilisation se produit, et la valeur est une référence à l’objet en cours de construction.</span><span class="sxs-lookup"><span data-stu-id="b0787-1150">The type of the value is the instance type ([The instance type](classes.md#the-instance-type)) of the class within which the usage occurs, and the value is a reference to the object being constructed.</span></span>
*  <span data-ttu-id="b0787-1151">Lorsque `this` est utilisé dans une *primary_expression* dans une méthode d’instance ou un accesseur d’instance d’une classe, il est classé comme une valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1151">When `this` is used in a *primary_expression* within an instance method or instance accessor of a class, it is classified as a value.</span></span> <span data-ttu-id="b0787-1152">Le type de la valeur est le type d’instance ([type d’instance](classes.md#the-instance-type)) de la classe dans laquelle l’utilisation se produit, et la valeur est une référence à l’objet pour lequel la méthode ou l’accesseur a été appelé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1152">The type of the value is the instance type ([The instance type](classes.md#the-instance-type)) of the class within which the usage occurs, and the value is a reference to the object for which the method or accessor was invoked.</span></span>
*  <span data-ttu-id="b0787-1153">Lorsque `this` est utilisé dans un *primary_expression* au sein d’un constructeur d’instance d’un struct, il est classé en tant que variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-1153">When `this` is used in a *primary_expression* within an instance constructor of a struct, it is classified as a variable.</span></span> <span data-ttu-id="b0787-1154">Le type de la variable est le type d’instance ([type d’instance](classes.md#the-instance-type)) de la structure dans laquelle l’utilisation se produit, et la variable représente le struct en cours de construction.</span><span class="sxs-lookup"><span data-stu-id="b0787-1154">The type of the variable is the instance type ([The instance type](classes.md#the-instance-type)) of the struct within which the usage occurs, and the variable represents the struct being constructed.</span></span> <span data-ttu-id="b0787-1155">La variable `this` d’un constructeur d’instance d’un struct se comporte exactement comme un paramètre `out` du type struct (en particulier, cela signifie que la variable doit être assignée de manière définitive dans chaque chemin d’exécution du constructeur d’instance).</span><span class="sxs-lookup"><span data-stu-id="b0787-1155">The `this` variable of an instance constructor of a struct behaves exactly the same as an `out` parameter of the struct type—in particular, this means that the variable must be definitely assigned in every execution path of the instance constructor.</span></span>
*  <span data-ttu-id="b0787-1156">Lorsque `this` est utilisé dans une *primary_expression* dans une méthode d’instance ou un accesseur d’instance d’un struct, il est classé en tant que variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-1156">When `this` is used in a *primary_expression* within an instance method or instance accessor of a struct, it is classified as a variable.</span></span> <span data-ttu-id="b0787-1157">Le type de la variable est le type d’instance ([type d’instance](classes.md#the-instance-type)) de la structure dans laquelle l’utilisation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1157">The type of the variable is the instance type ([The instance type](classes.md#the-instance-type)) of the struct within which the usage occurs.</span></span>
   * <span data-ttu-id="b0787-1158">Si la méthode ou l’accesseur n’est pas un itérateur ([itérateurs](classes.md#iterators)), la variable `this` représente le struct pour lequel la méthode ou l’accesseur a été appelé, et se comporte exactement comme un paramètre `ref` du type struct.</span><span class="sxs-lookup"><span data-stu-id="b0787-1158">If the method or accessor is not an iterator ([Iterators](classes.md#iterators)), the `this` variable represents the struct for which the method or accessor was invoked, and behaves exactly the same as a `ref` parameter of the struct type.</span></span>
   * <span data-ttu-id="b0787-1159">Si la méthode ou l’accesseur est un itérateur, la variable `this` représente une copie du struct pour lequel la méthode ou l’accesseur a été appelé, et se comporte exactement comme un paramètre de valeur du type struct.</span><span class="sxs-lookup"><span data-stu-id="b0787-1159">If the method or accessor is an iterator, the `this` variable represents a copy of the struct for which the method or accessor was invoked, and behaves exactly the same as a value parameter of the struct type.</span></span>

<span data-ttu-id="b0787-1160">L’utilisation de `this` dans un *primary_expression* dans un contexte autre que ceux listés ci-dessus est une erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1160">Use of `this` in a *primary_expression* in a context other than the ones listed above is a compile-time error.</span></span> <span data-ttu-id="b0787-1161">En particulier, il n’est pas possible de faire référence à `this` dans une méthode statique, un accesseur de propriété statique ou dans une *variable_initializer* d’une déclaration de champ.</span><span class="sxs-lookup"><span data-stu-id="b0787-1161">In particular, it is not possible to refer to `this` in a static method, a static property accessor, or in a *variable_initializer* of a field declaration.</span></span>

### <a name="base-access"></a><span data-ttu-id="b0787-1162">Accès de base</span><span class="sxs-lookup"><span data-stu-id="b0787-1162">Base access</span></span>

<span data-ttu-id="b0787-1163">Un *base_access* se compose du mot réservé `base` suivi d’un jeton «`.`» et d’un identificateur ou d’un *argument_list* placé entre crochets :</span><span class="sxs-lookup"><span data-stu-id="b0787-1163">A *base_access* consists of the reserved word `base` followed by either a "`.`" token and an identifier or an *argument_list* enclosed in square brackets:</span></span>

```antlr
base_access
    : 'base' '.' identifier
    | 'base' '[' expression_list ']'
    ;
```

<span data-ttu-id="b0787-1164">Un *base_access* est utilisé pour accéder aux membres de la classe de base qui sont masqués par des membres portant le même nom dans la classe ou le struct actuel.</span><span class="sxs-lookup"><span data-stu-id="b0787-1164">A *base_access* is used to access base class members that are hidden by similarly named members in the current class or struct.</span></span> <span data-ttu-id="b0787-1165">Une *base_access* est autorisée uniquement dans le *bloc* d’un constructeur d’instance, d’une méthode d’instance ou d’un accesseur d’instance.</span><span class="sxs-lookup"><span data-stu-id="b0787-1165">A *base_access* is permitted only in the *block* of an instance constructor, an instance method, or an instance accessor.</span></span> <span data-ttu-id="b0787-1166">Quand `base.I` se produit dans une classe ou un struct, `I` doit désigner un membre de la classe de base de cette classe ou structure.</span><span class="sxs-lookup"><span data-stu-id="b0787-1166">When `base.I` occurs in a class or struct, `I` must denote a member of the base class of that class or struct.</span></span> <span data-ttu-id="b0787-1167">De même, quand `base[E]` se produit dans une classe, un indexeur applicable doit exister dans la classe de base.</span><span class="sxs-lookup"><span data-stu-id="b0787-1167">Likewise, when `base[E]` occurs in a class, an applicable indexer must exist in the base class.</span></span>

<span data-ttu-id="b0787-1168">Au moment de la liaison, *base_access* expressions de la forme `base.I` et `base[E]` sont évaluées exactement comme si elles étaient écrites `((B)this).I` et `((B)this)[E]`, où `B` est la classe de base de la classe ou du struct dans lequel la construction se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1168">At binding-time, *base_access* expressions of the form `base.I` and `base[E]` are evaluated exactly as if they were written `((B)this).I` and `((B)this)[E]`, where `B` is the base class of the class or struct in which the construct occurs.</span></span> <span data-ttu-id="b0787-1169">Ainsi, `base.I` et `base[E]` correspondent à `this.I` et `this[E]`, sauf que `this` est affiché en tant qu’instance de la classe de base.</span><span class="sxs-lookup"><span data-stu-id="b0787-1169">Thus, `base.I` and `base[E]` correspond to `this.I` and `this[E]`, except `this` is viewed as an instance of the base class.</span></span>

<span data-ttu-id="b0787-1170">Lorsqu’un *base_access* fait référence à un membre de fonction virtuelle (une méthode, une propriété ou un indexeur), la détermination du membre de fonction à appeler au moment de l’exécution ([vérification de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)au moment de la compilation) est modifiée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1170">When a *base_access* references a virtual function member (a method, property, or indexer), the determination of which function member to invoke at run-time ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) is changed.</span></span> <span data-ttu-id="b0787-1171">Le membre de fonction appelé est déterminé par la recherche de l’implémentation la plus dérivée ([méthodes virtuelles](classes.md#virtual-methods)) du membre de fonction par rapport à `B` (plutôt que par rapport au type d’exécution de `this`, comme normalement dans un accès non-base).</span><span class="sxs-lookup"><span data-stu-id="b0787-1171">The function member that is invoked is determined by finding the most derived implementation ([Virtual methods](classes.md#virtual-methods)) of the function member with respect to `B` (instead of with respect to the run-time type of `this`, as would be usual in a non-base access).</span></span> <span data-ttu-id="b0787-1172">Ainsi, dans un `override` d’un membre de fonction `virtual`, un *base_access* peut être utilisé pour appeler l’implémentation héritée du membre de fonction.</span><span class="sxs-lookup"><span data-stu-id="b0787-1172">Thus, within an `override` of a `virtual` function member, a *base_access* can be used to invoke the inherited implementation of the function member.</span></span> <span data-ttu-id="b0787-1173">Si le membre de fonction référencé par un *base_access* est abstrait, une erreur de liaison au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1173">If the function member referenced by a *base_access* is abstract, a binding-time error occurs.</span></span>

### <a name="postfix-increment-and-decrement-operators"></a><span data-ttu-id="b0787-1174">Opérateurs suffixés d’incrémentation et de décrémentation</span><span class="sxs-lookup"><span data-stu-id="b0787-1174">Postfix increment and decrement operators</span></span>

```antlr
post_increment_expression
    : primary_expression '++'
    ;

post_decrement_expression
    : primary_expression '--'
    ;
```

<span data-ttu-id="b0787-1175">L’opérande d’une opération d’incrémentation ou de décrémentation suffixée doit être une expression classifiée comme une variable, un accès à une propriété ou un accès à un indexeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1175">The operand of a postfix increment or decrement operation must be an expression classified as a variable, a property access, or an indexer access.</span></span> <span data-ttu-id="b0787-1176">Le résultat de l’opération est une valeur du même type que l’opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-1176">The result of the operation is a value of the same type as the operand.</span></span>

<span data-ttu-id="b0787-1177">Si le *primary_expression* a le type au moment de la compilation `dynamic` l’opérateur est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)), le *post_increment_expression* ou *post_decrement_expression* a le type au moment de la compilation `dynamic` et les règles suivantes sont appliquées au moment de l’exécution en utilisant le type d’exécution de l' *primary_expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1177">If the *primary_expression* has the compile-time type `dynamic` then the operator is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)), the *post_increment_expression* or *post_decrement_expression* has the compile-time type `dynamic` and the following rules are applied at run-time using the run-time type of the *primary_expression*.</span></span>

<span data-ttu-id="b0787-1178">Si l’opérande d’une opération d’incrémentation ou de décrémentation suffixée est un accès à une propriété ou un indexeur, la propriété ou l’indexeur doit avoir un `get` et un accesseur `set`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1178">If the operand of a postfix increment or decrement operation is a property or indexer access, the property or indexer must have both a `get` and a `set` accessor.</span></span> <span data-ttu-id="b0787-1179">Si ce n’est pas le cas, une erreur de temps de liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1179">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="b0787-1180">La résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-1180">Unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-1181">Les opérateurs `++` et `--` prédéfinis existent pour les types suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`et tout type enum.</span><span class="sxs-lookup"><span data-stu-id="b0787-1181">Predefined `++` and `--` operators exist for the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, and any enum type.</span></span> <span data-ttu-id="b0787-1182">Les opérateurs de `++` prédéfinis retournent la valeur produite par l’ajout de 1 à l’opérande, et les opérateurs de `--` prédéfinis retournent la valeur produite par la soustraction de 1 de l’opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-1182">The predefined `++` operators return the value produced by adding 1 to the operand, and the predefined `--` operators return the value produced by subtracting 1 from the operand.</span></span> <span data-ttu-id="b0787-1183">Dans un contexte de `checked`, si le résultat de l’addition ou de la soustraction est en dehors de la plage du type de résultat et que le type de résultat est un type intégral ou un type enum, une `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1183">In a `checked` context, if the result of this addition or subtraction is outside the range of the result type and the result type is an integral type or enum type, a `System.OverflowException` is thrown.</span></span>

<span data-ttu-id="b0787-1184">Le traitement au moment de l’exécution d’une opération d’incrémentation ou de décrémentation suffixée de la forme `x++` ou `x--` se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-1184">The run-time processing of a postfix increment or decrement operation of the form `x++` or `x--` consists of the following steps:</span></span>

*   <span data-ttu-id="b0787-1185">Si `x` est classée en tant que variable :</span><span class="sxs-lookup"><span data-stu-id="b0787-1185">If `x` is classified as a variable:</span></span>
    * <span data-ttu-id="b0787-1186">`x` est évaluée pour produire la variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-1186">`x` is evaluated to produce the variable.</span></span>
    * <span data-ttu-id="b0787-1187">La valeur de `x` est enregistrée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1187">The value of `x` is saved.</span></span>
    * <span data-ttu-id="b0787-1188">L’opérateur sélectionné est appelé avec la valeur enregistrée de `x` comme argument.</span><span class="sxs-lookup"><span data-stu-id="b0787-1188">The selected operator is invoked with the saved value of `x` as its argument.</span></span>
    * <span data-ttu-id="b0787-1189">La valeur retournée par l’opérateur est stockée dans l’emplacement donné par l’évaluation de `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1189">The value returned by the operator is stored in the location given by the evaluation of `x`.</span></span>
    * <span data-ttu-id="b0787-1190">La valeur enregistrée de `x` devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-1190">The saved value of `x` becomes the result of the operation.</span></span>
*   <span data-ttu-id="b0787-1191">Si `x` est classée comme un accès à une propriété ou un indexeur :</span><span class="sxs-lookup"><span data-stu-id="b0787-1191">If `x` is classified as a property or indexer access:</span></span>
    * <span data-ttu-id="b0787-1192">L’expression d’instance (si `x` n’est pas `static`) et la liste d’arguments (si `x` est un accès à un indexeur) associée à `x` sont évaluées, et les résultats sont utilisés dans les appels de `get` et d’accesseur `set` suivants.</span><span class="sxs-lookup"><span data-stu-id="b0787-1192">The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `get` and `set` accessor invocations.</span></span>
    * <span data-ttu-id="b0787-1193">L’accesseur `get` de `x` est appelé et la valeur retournée est enregistrée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1193">The `get` accessor of `x` is invoked and the returned value is saved.</span></span>
    * <span data-ttu-id="b0787-1194">L’opérateur sélectionné est appelé avec la valeur enregistrée de `x` comme argument.</span><span class="sxs-lookup"><span data-stu-id="b0787-1194">The selected operator is invoked with the saved value of `x` as its argument.</span></span>
    * <span data-ttu-id="b0787-1195">L’accesseur `set` de `x` est appelé avec la valeur retournée par l’opérateur comme son argument `value`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1195">The `set` accessor of `x` is invoked with the value returned by the operator as its `value` argument.</span></span>
    * <span data-ttu-id="b0787-1196">La valeur enregistrée de `x` devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-1196">The saved value of `x` becomes the result of the operation.</span></span>

<span data-ttu-id="b0787-1197">Les opérateurs `++` et `--` prennent également en charge la notation de préfixe ([opérateurs de préfixe d’incrémentation et de décrémentation](expressions.md#prefix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1197">The `++` and `--` operators also support prefix notation ([Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span> <span data-ttu-id="b0787-1198">En général, le résultat de `x++` ou `x--` est la valeur de `x` avant l’opération, alors que le résultat de `++x` ou `--x` est la valeur de `x` après l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-1198">Typically, the result of `x++` or `x--` is the value of `x` before the operation, whereas the result of `++x` or `--x` is the value of `x` after the operation.</span></span> <span data-ttu-id="b0787-1199">Dans les deux cas, `x` lui-même a la même valeur après l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-1199">In either case, `x` itself has the same value after the operation.</span></span>

<span data-ttu-id="b0787-1200">Une implémentation de `operator ++` ou `operator --` peut être appelée à l’aide de la notation suffixée ou de préfixe.</span><span class="sxs-lookup"><span data-stu-id="b0787-1200">An `operator ++` or `operator --` implementation can be invoked using either postfix or prefix notation.</span></span> <span data-ttu-id="b0787-1201">Il n’est pas possible d’avoir des implémentations d’opérateur distinctes pour les deux notations.</span><span class="sxs-lookup"><span data-stu-id="b0787-1201">It is not possible to have separate operator implementations for the two notations.</span></span>

### <a name="the-new-operator"></a><span data-ttu-id="b0787-1202">new, opérateur</span><span class="sxs-lookup"><span data-stu-id="b0787-1202">The new operator</span></span>

<span data-ttu-id="b0787-1203">L’opérateur `new` est utilisé pour créer de nouvelles instances de types.</span><span class="sxs-lookup"><span data-stu-id="b0787-1203">The `new` operator is used to create new instances of types.</span></span>

<span data-ttu-id="b0787-1204">Il existe trois formes d’expressions `new` :</span><span class="sxs-lookup"><span data-stu-id="b0787-1204">There are three forms of `new` expressions:</span></span>

*  <span data-ttu-id="b0787-1205">Les expressions de création d’objet sont utilisées pour créer des instances de types de classe et de valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1205">Object creation expressions are used to create new instances of class types and value types.</span></span>
*  <span data-ttu-id="b0787-1206">Les expressions de création de tableau sont utilisées pour créer de nouvelles instances de types tableau.</span><span class="sxs-lookup"><span data-stu-id="b0787-1206">Array creation expressions are used to create new instances of array types.</span></span>
*  <span data-ttu-id="b0787-1207">Les expressions de création de délégué sont utilisées pour créer de nouvelles instances de types délégués.</span><span class="sxs-lookup"><span data-stu-id="b0787-1207">Delegate creation expressions are used to create new instances of delegate types.</span></span>

<span data-ttu-id="b0787-1208">L’opérateur `new` implique la création d’une instance d’un type, mais n’implique pas nécessairement l’allocation dynamique de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="b0787-1208">The `new` operator implies creation of an instance of a type, but does not necessarily imply dynamic allocation of memory.</span></span> <span data-ttu-id="b0787-1209">En particulier, les instances de types valeur ne requièrent aucune mémoire supplémentaire au-delà des variables dans lesquelles elles résident et aucune allocation dynamique ne se produit lorsque `new` est utilisé pour créer des instances de types valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1209">In particular, instances of value types require no additional memory beyond the variables in which they reside, and no dynamic allocations occur when `new` is used to create instances of value types.</span></span>

#### <a name="object-creation-expressions"></a><span data-ttu-id="b0787-1210">Expressions de création d’objet</span><span class="sxs-lookup"><span data-stu-id="b0787-1210">Object creation expressions</span></span>

<span data-ttu-id="b0787-1211">Un *object_creation_expression* est utilisé pour créer une nouvelle instance d’un *class_type* ou d’un *Value_type*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1211">An *object_creation_expression* is used to create a new instance of a *class_type* or a *value_type*.</span></span>

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

<span data-ttu-id="b0787-1212">Le *type* d’un *object_creation_expression* doit être un *class_type*, un *Value_type* ou un *type_parameter*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1212">The *type* of an *object_creation_expression* must be a *class_type*, a *value_type* or a *type_parameter*.</span></span> <span data-ttu-id="b0787-1213">Le *type* ne peut pas être un *class_type*`abstract`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1213">The *type* cannot be an `abstract` *class_type*.</span></span>

<span data-ttu-id="b0787-1214">La *argument_list* facultative ([listes d’arguments](expressions.md#argument-lists)) est autorisée uniquement si le *type* est un *class_type* ou un *struct_type*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1214">The optional *argument_list* ([Argument lists](expressions.md#argument-lists)) is permitted only if the *type* is a *class_type* or a *struct_type*.</span></span>

<span data-ttu-id="b0787-1215">Une expression de création d’objet peut omettre la liste d’arguments de constructeur et les parenthèses englobantes fournies, elle comprend un initialiseur d’objet ou un initialiseur de collection.</span><span class="sxs-lookup"><span data-stu-id="b0787-1215">An object creation expression can omit the constructor argument list and enclosing parentheses provided it includes an object initializer or collection initializer.</span></span> <span data-ttu-id="b0787-1216">L’omission de la liste d’arguments du constructeur et des parenthèses englobantes équivaut à spécifier une liste d’arguments vide.</span><span class="sxs-lookup"><span data-stu-id="b0787-1216">Omitting the constructor argument list and enclosing parentheses is equivalent to specifying an empty argument list.</span></span>

<span data-ttu-id="b0787-1217">Le traitement d’une expression de création d’objet qui inclut un initialiseur d’objet ou un initialiseur de collection consiste à traiter en premier le constructeur d’instance, puis à traiter les initialisations de membre ou d’élément spécifiées par l’initialiseur d’objet ([Initialiseurs d’objets](expressions.md#object-initializers)) ou initialiseur de collection ([initialiseurs de collection](expressions.md#collection-initializers)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1217">Processing of an object creation expression that includes an object initializer or collection initializer consists of first processing the instance constructor and then processing the member or element initializations specified by the object initializer ([Object initializers](expressions.md#object-initializers)) or collection initializer ([Collection initializers](expressions.md#collection-initializers)).</span></span>

<span data-ttu-id="b0787-1218">Si l’un des arguments de la *argument_list* facultative a le type au moment de la compilation `dynamic` alors le *object_creation_expression* est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)) et les règles suivantes sont appliquées au moment de l’exécution à l’aide du type d’exécution des arguments de l' *argument_list* qui ont le type au moment de la compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1218">If any of the arguments in the optional *argument_list* has the compile-time type `dynamic` then the *object_creation_expression* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)) and the following rules are applied at run-time using the run-time type of those arguments of the *argument_list* that have the compile time type `dynamic`.</span></span> <span data-ttu-id="b0787-1219">Toutefois, la création d’objet subit une vérification limitée au moment de la compilation, comme décrit dans [vérification de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)par la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1219">However, the object creation undergoes a limited compile time check as described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="b0787-1220">Le traitement au moment de la liaison d’une *object_creation_expression* de la forme `new T(A)`, où `T` est un *class_type* ou un *value_type* et `A` est un *argument_list*facultatif, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-1220">The binding-time processing of an *object_creation_expression* of the form `new T(A)`, where `T` is a *class_type* or a *value_type* and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*   <span data-ttu-id="b0787-1221">Si `T` est un *Value_type* et `A` n’est pas présent :</span><span class="sxs-lookup"><span data-stu-id="b0787-1221">If `T` is a *value_type* and `A` is not present:</span></span>
    * <span data-ttu-id="b0787-1222">Le *object_creation_expression* est un appel de constructeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="b0787-1222">The *object_creation_expression* is a default constructor invocation.</span></span> <span data-ttu-id="b0787-1223">Le résultat de l' *object_creation_expression* est une valeur de type `T`, à savoir la valeur par défaut pour `T` telle que définie dans [le type System. ValueType](types.md#the-systemvaluetype-type).</span><span class="sxs-lookup"><span data-stu-id="b0787-1223">The result of the *object_creation_expression* is a value of type `T`, namely the default value for `T` as defined in [The System.ValueType type](types.md#the-systemvaluetype-type).</span></span>
*   <span data-ttu-id="b0787-1224">Sinon, si `T` est un *type_parameter* et `A` n’est pas présent :</span><span class="sxs-lookup"><span data-stu-id="b0787-1224">Otherwise, if `T` is a *type_parameter* and `A` is not present:</span></span>
    * <span data-ttu-id="b0787-1225">Si aucune contrainte de type valeur ou contrainte de constructeur ([contraintes de paramètre de type](classes.md#type-parameter-constraints)) n’a été spécifiée pour `T`, une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1225">If no value type constraint or constructor constraint ([Type parameter constraints](classes.md#type-parameter-constraints)) has been specified for `T`, a binding-time error occurs.</span></span>
    * <span data-ttu-id="b0787-1226">Le résultat de l' *object_creation_expression* est une valeur du type au moment de l’exécution auquel le paramètre de type a été lié, à savoir le résultat de l’appel du constructeur par défaut de ce type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1226">The result of the *object_creation_expression* is a value of the run-time type that the type parameter has been bound to, namely the result of invoking the default constructor of that type.</span></span> <span data-ttu-id="b0787-1227">Le type au moment de l’exécution peut être un type référence ou un type valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1227">The run-time type may be a reference type or a value type.</span></span>
*   <span data-ttu-id="b0787-1228">Sinon, si `T` est un *class_type* ou un *struct_type*:</span><span class="sxs-lookup"><span data-stu-id="b0787-1228">Otherwise, if `T` is a *class_type* or a *struct_type*:</span></span>
    * <span data-ttu-id="b0787-1229">Si `T` est un `abstract` *class_type*, une erreur se produit au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1229">If `T` is an `abstract` *class_type*, a compile-time error occurs.</span></span>
    * <span data-ttu-id="b0787-1230">Le constructeur d’instance à appeler est déterminé à l’aide des règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="b0787-1230">The instance constructor to invoke is determined using the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="b0787-1231">L’ensemble de constructeurs d’instance candidat se compose de tous les constructeurs d’instance accessibles déclarés dans `T` qui sont applicables à `A` ([fonction membre applicable](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1231">The set of candidate instance constructors consists of all accessible instance constructors declared in `T` which are applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span> <span data-ttu-id="b0787-1232">Si le jeu de constructeurs d’instance candidat est vide, ou si un seul constructeur d’instance unique ne peut pas être identifié, une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1232">If the set of candidate instance constructors is empty, or if a single best instance constructor cannot be identified, a binding-time error occurs.</span></span>
    * <span data-ttu-id="b0787-1233">Le résultat de l' *object_creation_expression* est une valeur de type `T`, à savoir la valeur produite par l’appel du constructeur d’instance déterminé à l’étape ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="b0787-1233">The result of the *object_creation_expression* is a value of type `T`, namely the value produced by invoking the instance constructor determined in the step above.</span></span>
*  <span data-ttu-id="b0787-1234">Sinon, la *object_creation_expression* n’est pas valide et une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1234">Otherwise, the *object_creation_expression* is invalid, and a binding-time error occurs.</span></span>

<span data-ttu-id="b0787-1235">Même si le *object_creation_expression* est lié dynamiquement, le type au moment de la compilation est toujours `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1235">Even if the *object_creation_expression* is dynamically bound, the compile-time type is still `T`.</span></span>

<span data-ttu-id="b0787-1236">Le traitement au moment de l’exécution d’une *object_creation_expression* de la forme `new T(A)`, où `T` est *class_type* ou un *struct_type* et `A` est un *argument_list*facultatif, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-1236">The run-time processing of an *object_creation_expression* of the form `new T(A)`, where `T` is *class_type* or a *struct_type* and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*   <span data-ttu-id="b0787-1237">Si `T` est un *class_type*:</span><span class="sxs-lookup"><span data-stu-id="b0787-1237">If `T` is a *class_type*:</span></span>
    * <span data-ttu-id="b0787-1238">Une nouvelle instance de la classe `T` est allouée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1238">A new instance of class `T` is allocated.</span></span> <span data-ttu-id="b0787-1239">Si la mémoire disponible est insuffisante pour allouer la nouvelle instance, une `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1239">If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.</span></span>
    * <span data-ttu-id="b0787-1240">Tous les champs de la nouvelle instance sont initialisés à leurs valeurs par défaut ([valeurs par défaut](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1240">All fields of the new instance are initialized to their default values ([Default values](variables.md#default-values)).</span></span>
    * <span data-ttu-id="b0787-1241">Le constructeur d’instance est appelé selon les règles d’appel de membre de fonction ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1241">The instance constructor is invoked according to the rules of function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span> <span data-ttu-id="b0787-1242">Une référence à l’instance qui vient d’être allouée est automatiquement passée au constructeur d’instance et l’instance est accessible à partir de ce constructeur en tant que `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1242">A reference to the newly allocated instance is automatically passed to the instance constructor and the instance can be accessed from within that constructor as `this`.</span></span>
*   <span data-ttu-id="b0787-1243">Si `T` est un *struct_type*:</span><span class="sxs-lookup"><span data-stu-id="b0787-1243">If `T` is a *struct_type*:</span></span>
    * <span data-ttu-id="b0787-1244">Une instance de type `T` est créée en allouant une variable locale temporaire.</span><span class="sxs-lookup"><span data-stu-id="b0787-1244">An instance of type `T` is created by allocating a temporary local variable.</span></span> <span data-ttu-id="b0787-1245">Étant donné qu’un constructeur d’instance d’un *struct_type* est requis pour assigner une valeur à chaque champ de l’instance en cours de création, aucune initialisation de la variable temporaire n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b0787-1245">Since an instance constructor of a *struct_type* is required to definitely assign a value to each field of the instance being created, no initialization of the temporary variable is necessary.</span></span>
    * <span data-ttu-id="b0787-1246">Le constructeur d’instance est appelé selon les règles d’appel de membre de fonction ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1246">The instance constructor is invoked according to the rules of function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span> <span data-ttu-id="b0787-1247">Une référence à l’instance qui vient d’être allouée est automatiquement passée au constructeur d’instance et l’instance est accessible à partir de ce constructeur en tant que `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1247">A reference to the newly allocated instance is automatically passed to the instance constructor and the instance can be accessed from within that constructor as `this`.</span></span>

#### <a name="object-initializers"></a><span data-ttu-id="b0787-1248">Initialiseurs d’objet</span><span class="sxs-lookup"><span data-stu-id="b0787-1248">Object initializers</span></span>

<span data-ttu-id="b0787-1249">Un ***initialiseur d’objet*** spécifie des valeurs pour zéro, un ou plusieurs champs, propriétés ou éléments indexés d’un objet.</span><span class="sxs-lookup"><span data-stu-id="b0787-1249">An ***object initializer*** specifies values for zero or more fields, properties or indexed elements of an object.</span></span>

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

<span data-ttu-id="b0787-1250">Un initialiseur d’objet se compose d’une séquence d’initialiseurs de membres, placée entre `{` et `}` des jetons et séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="b0787-1250">An object initializer consists of a sequence of member initializers, enclosed by `{` and `}` tokens and separated by commas.</span></span> <span data-ttu-id="b0787-1251">Chaque *member_initializer* désigne une cible pour l’initialisation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1251">Each *member_initializer* designates a target for the initialization.</span></span> <span data-ttu-id="b0787-1252">Un *identificateur* doit nommer un champ ou une propriété accessible de l’objet en cours d’initialisation, alors qu’un *argument_list* placé entre crochets doit spécifier des arguments pour un indexeur accessible sur l’objet en cours d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1252">An *identifier* must name an accessible field or property of the object being initialized, whereas an *argument_list* enclosed in square brackets must specify arguments for an accessible indexer on the object being initialized.</span></span> <span data-ttu-id="b0787-1253">Il y a une erreur pour qu’un initialiseur d’objet inclue plusieurs initialiseurs de membre pour le même champ ou la même propriété.</span><span class="sxs-lookup"><span data-stu-id="b0787-1253">It is an error for an object initializer to include more than one member initializer for the same field or property.</span></span>

<span data-ttu-id="b0787-1254">Chaque *initializer_target* est suivi d’un signe égal et d’une expression, d’un initialiseur d’objet ou d’un initialiseur de collection.</span><span class="sxs-lookup"><span data-stu-id="b0787-1254">Each *initializer_target* is followed by an equals sign and either an expression, an object initializer or a collection initializer.</span></span> <span data-ttu-id="b0787-1255">Les expressions dans l’initialiseur d’objet ne peuvent pas faire référence à l’objet nouvellement créé qu’il initialise.</span><span class="sxs-lookup"><span data-stu-id="b0787-1255">It is not possible for expressions within the object initializer to refer to the newly created object it is initializing.</span></span>

<span data-ttu-id="b0787-1256">Un initialiseur de membre qui spécifie une expression après que le signe égal est traité de la même façon qu’une assignation ([assignation simple](expressions.md#simple-assignment)) à la cible.</span><span class="sxs-lookup"><span data-stu-id="b0787-1256">A member initializer that specifies an expression after the equals sign is processed in the same way as an assignment ([Simple assignment](expressions.md#simple-assignment)) to the target.</span></span>

<span data-ttu-id="b0787-1257">Un initialiseur de membre qui spécifie un initialiseur d’objet après que le signe égal est un ***initialiseur d’objet imbriqué***, c.-à-d. une initialisation d’un objet incorporé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1257">A member initializer that specifies an object initializer after the equals sign is a ***nested object initializer***, i.e. an initialization of an embedded object.</span></span> <span data-ttu-id="b0787-1258">Au lieu d’assigner une nouvelle valeur au champ ou à la propriété, les assignations dans l’initialiseur d’objet imbriqué sont traitées comme des assignations aux membres du champ ou de la propriété.</span><span class="sxs-lookup"><span data-stu-id="b0787-1258">Instead of assigning a new value to the field or property, the assignments in the nested object initializer are treated as assignments to members of the field or property.</span></span> <span data-ttu-id="b0787-1259">Les initialiseurs d’objets imbriqués ne peuvent pas être appliqués à des propriétés avec un type valeur, ou à des champs en lecture seule avec un type valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1259">Nested object initializers cannot be applied to properties with a value type, or to read-only fields with a value type.</span></span>

<span data-ttu-id="b0787-1260">Un initialiseur de membre qui spécifie un initialiseur de collection après le signe égal est l’initialisation d’une collection incorporée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1260">A member initializer that specifies a collection initializer after the equals sign is an initialization of an embedded collection.</span></span> <span data-ttu-id="b0787-1261">Au lieu d’assigner une nouvelle collection au champ, à la propriété ou à l’indexeur cible, les éléments fournis dans l’initialiseur sont ajoutés à la collection référencée par la cible.</span><span class="sxs-lookup"><span data-stu-id="b0787-1261">Instead of assigning a new collection to the target field, property or indexer, the elements given in the initializer are added to the collection referenced by the target.</span></span> <span data-ttu-id="b0787-1262">La cible doit être d’un type de collection qui satisfait aux exigences spécifiées dans les [initialiseurs de collection](expressions.md#collection-initializers).</span><span class="sxs-lookup"><span data-stu-id="b0787-1262">The target must be of a collection type that satisfies the requirements specified in [Collection initializers](expressions.md#collection-initializers).</span></span>

<span data-ttu-id="b0787-1263">Les arguments d’un initialiseur d’index seront toujours évalués une seule fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-1263">The arguments to an index initializer will always be evaluated exactly once.</span></span> <span data-ttu-id="b0787-1264">Ainsi, même si les arguments finissent par ne jamais être utilisés (par exemple, en raison d’un initialiseur imbriqué vide), ils sont évalués pour leurs effets secondaires.</span><span class="sxs-lookup"><span data-stu-id="b0787-1264">Thus, even if the arguments end up never getting used (e.g. because of an empty nested initializer), they will be evaluated for their side effects.</span></span>

<span data-ttu-id="b0787-1265">La classe suivante représente un point avec deux coordonnées :</span><span class="sxs-lookup"><span data-stu-id="b0787-1265">The following class represents a point with two coordinates:</span></span>
```csharp
public class Point
{
    int x, y;

    public int X { get { return x; } set { x = value; } }
    public int Y { get { return y; } set { y = value; } }
}
```

<span data-ttu-id="b0787-1266">Une instance de `Point` peut être créée et initialisée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-1266">An instance of `Point` can be created and initialized as follows:</span></span>
```csharp
Point a = new Point { X = 0, Y = 1 };
```
<span data-ttu-id="b0787-1267">ce qui a le même effet que</span><span class="sxs-lookup"><span data-stu-id="b0787-1267">which has the same effect as</span></span>
```csharp
Point __a = new Point();
__a.X = 0;
__a.Y = 1; 
Point a = __a;
```
<span data-ttu-id="b0787-1268">où `__a` est une variable temporaire inaccessible et invisible.</span><span class="sxs-lookup"><span data-stu-id="b0787-1268">where `__a` is an otherwise invisible and inaccessible temporary variable.</span></span> <span data-ttu-id="b0787-1269">La classe suivante représente un rectangle créé à partir de deux points :</span><span class="sxs-lookup"><span data-stu-id="b0787-1269">The following class represents a rectangle created from two points:</span></span>
```csharp
public class Rectangle
{
    Point p1, p2;

    public Point P1 { get { return p1; } set { p1 = value; } }
    public Point P2 { get { return p2; } set { p2 = value; } }
}
```

<span data-ttu-id="b0787-1270">Une instance de `Rectangle` peut être créée et initialisée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-1270">An instance of `Rectangle` can be created and initialized as follows:</span></span>
```csharp
Rectangle r = new Rectangle {
    P1 = new Point { X = 0, Y = 1 },
    P2 = new Point { X = 2, Y = 3 }
};
```
<span data-ttu-id="b0787-1271">ce qui a le même effet que</span><span class="sxs-lookup"><span data-stu-id="b0787-1271">which has the same effect as</span></span>
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
<span data-ttu-id="b0787-1272">où `__r`, `__p1` et `__p2` sont des variables temporaires qui, sinon, sont inaccessibles et inaccessibles.</span><span class="sxs-lookup"><span data-stu-id="b0787-1272">where `__r`, `__p1` and `__p2` are temporary variables that are otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="b0787-1273">Si le constructeur de `Rectangle`alloue les deux instances de `Point` incorporées</span><span class="sxs-lookup"><span data-stu-id="b0787-1273">If `Rectangle`'s constructor allocates the two embedded `Point` instances</span></span>
```csharp
public class Rectangle
{
    Point p1 = new Point();
    Point p2 = new Point();

    public Point P1 { get { return p1; } }
    public Point P2 { get { return p2; } }
}
```
<span data-ttu-id="b0787-1274">la construction suivante peut être utilisée pour initialiser les instances de `Point` incorporées au lieu d’assigner de nouvelles instances :</span><span class="sxs-lookup"><span data-stu-id="b0787-1274">the following construct can be used to initialize the embedded `Point` instances instead of assigning new instances:</span></span>
```csharp
Rectangle r = new Rectangle {
    P1 = { X = 0, Y = 1 },
    P2 = { X = 2, Y = 3 }
};
```
<span data-ttu-id="b0787-1275">ce qui a le même effet que</span><span class="sxs-lookup"><span data-stu-id="b0787-1275">which has the same effect as</span></span>
```csharp
Rectangle __r = new Rectangle();
__r.P1.X = 0;
__r.P1.Y = 1;
__r.P2.X = 2;
__r.P2.Y = 3;
Rectangle r = __r;
```

<span data-ttu-id="b0787-1276">Dans le cas d’une définition appropriée de C, l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="b0787-1276">Given an appropriate definition of C, the following example:</span></span>
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
<span data-ttu-id="b0787-1277">équivaut à cette série d’affectations :</span><span class="sxs-lookup"><span data-stu-id="b0787-1277">is equivalent to this series of assignments:</span></span>
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
<span data-ttu-id="b0787-1278">où `__c`, etc., sont des variables générées qui sont invisibles et inaccessibles au code source.</span><span class="sxs-lookup"><span data-stu-id="b0787-1278">where `__c`, etc., are generated variables that are invisible and inaccessible to the source code.</span></span> <span data-ttu-id="b0787-1279">Notez que les arguments de `[0,0]` ne sont évalués qu’une seule fois, et les arguments de `[1,2]` sont évalués une seule fois, même s’ils ne sont jamais utilisés.</span><span class="sxs-lookup"><span data-stu-id="b0787-1279">Note that the arguments for `[0,0]` are evaluated only once, and the arguments for `[1,2]` are evaluated once even though they are never used.</span></span>

#### <a name="collection-initializers"></a><span data-ttu-id="b0787-1280">Initialiseurs de collection</span><span class="sxs-lookup"><span data-stu-id="b0787-1280">Collection initializers</span></span>

<span data-ttu-id="b0787-1281">Un initialiseur de collection spécifie les éléments d’une collection.</span><span class="sxs-lookup"><span data-stu-id="b0787-1281">A collection initializer specifies the elements of a collection.</span></span>

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

<span data-ttu-id="b0787-1282">Un initialiseur de collection se compose d’une séquence d’initialiseurs d’éléments, encadrés par `{` et `}` des jetons et séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="b0787-1282">A collection initializer consists of a sequence of element initializers, enclosed by `{` and `}` tokens and separated by commas.</span></span> <span data-ttu-id="b0787-1283">Chaque initialiseur d’élément spécifie un élément à ajouter à l’objet de collection qui est initialisé, et se compose d’une liste d’expressions encadrées par `{` et `}` des jetons et séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="b0787-1283">Each element initializer specifies an element to be added to the collection object being initialized, and consists of a list of expressions enclosed by `{` and `}` tokens and separated by commas.</span></span>  <span data-ttu-id="b0787-1284">Un initialiseur d’élément à une seule expression peut être écrit sans accolades, mais il ne peut pas être une expression d’assignation, afin d’éviter toute ambiguïté avec les initialiseurs de membres.</span><span class="sxs-lookup"><span data-stu-id="b0787-1284">A single-expression element initializer can be written without braces, but cannot then be an assignment expression, to avoid ambiguity with member initializers.</span></span> <span data-ttu-id="b0787-1285">La *non_assignment_expression* production est définie dans [expression](expressions.md#expression).</span><span class="sxs-lookup"><span data-stu-id="b0787-1285">The *non_assignment_expression* production is defined in [Expression](expressions.md#expression).</span></span>

<span data-ttu-id="b0787-1286">L’exemple suivant illustre une expression de création d’objet qui comprend un initialiseur de collection :</span><span class="sxs-lookup"><span data-stu-id="b0787-1286">The following is an example of an object creation expression that includes a collection initializer:</span></span>
```csharp
List<int> digits = new List<int> { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
```

<span data-ttu-id="b0787-1287">L’objet de collection auquel un initialiseur de collection est appliqué doit être d’un type qui implémente `System.Collections.IEnumerable` ou une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1287">The collection object to which a collection initializer is applied must be of a type that implements `System.Collections.IEnumerable` or a compile-time error occurs.</span></span> <span data-ttu-id="b0787-1288">Pour chaque élément spécifié dans l’ordre, l’initialiseur de collection appelle une méthode `Add` sur l’objet cible avec la liste d’expressions de l’initialiseur d’élément comme liste d’arguments, en appliquant une recherche de membre normale et une résolution de surcharge pour chaque appel.</span><span class="sxs-lookup"><span data-stu-id="b0787-1288">For each specified element in order, the collection initializer invokes an `Add` method on the target object with the expression list of the element initializer as argument list, applying normal member lookup and overload resolution for each invocation.</span></span> <span data-ttu-id="b0787-1289">Ainsi, l’objet de collection doit avoir une instance ou une méthode d’extension applicable avec le nom `Add` pour chaque initialiseur d’élément.</span><span class="sxs-lookup"><span data-stu-id="b0787-1289">Thus, the collection object must have an applicable instance or extension method with the name `Add` for each element initializer.</span></span>

<span data-ttu-id="b0787-1290">La classe suivante représente un contact avec un nom et une liste de numéros de téléphone :</span><span class="sxs-lookup"><span data-stu-id="b0787-1290">The following class represents a contact with a name and a list of phone numbers:</span></span>
```csharp
public class Contact
{
    string name;
    List<string> phoneNumbers = new List<string>();

    public string Name { get { return name; } set { name = value; } }

    public List<string> PhoneNumbers { get { return phoneNumbers; } }
}
```

<span data-ttu-id="b0787-1291">Vous pouvez créer et initialiser un `List<Contact>` comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-1291">A `List<Contact>` can be created and initialized as follows:</span></span>
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
<span data-ttu-id="b0787-1292">ce qui a le même effet que</span><span class="sxs-lookup"><span data-stu-id="b0787-1292">which has the same effect as</span></span>
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
<span data-ttu-id="b0787-1293">où `__clist`, `__c1` et `__c2` sont des variables temporaires qui, sinon, sont inaccessibles et inaccessibles.</span><span class="sxs-lookup"><span data-stu-id="b0787-1293">where `__clist`, `__c1` and `__c2` are temporary variables that are otherwise invisible and inaccessible.</span></span>

#### <a name="array-creation-expressions"></a><span data-ttu-id="b0787-1294">Expressions de création de tableau</span><span class="sxs-lookup"><span data-stu-id="b0787-1294">Array creation expressions</span></span>

<span data-ttu-id="b0787-1295">Un *array_creation_expression* est utilisé pour créer une nouvelle instance d’une *array_type*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1295">An *array_creation_expression* is used to create a new instance of an *array_type*.</span></span>

```antlr
array_creation_expression
    : 'new' non_array_type '[' expression_list ']' rank_specifier* array_initializer?
    | 'new' array_type array_initializer
    | 'new' rank_specifier array_initializer
    ;
```

<span data-ttu-id="b0787-1296">Une expression de création de tableau de la première forme alloue une instance de tableau du type qui résulte de la suppression de chacune des expressions individuelles de la liste d’expressions.</span><span class="sxs-lookup"><span data-stu-id="b0787-1296">An array creation expression of the first form allocates an array instance of the type that results from deleting each of the individual expressions from the expression list.</span></span> <span data-ttu-id="b0787-1297">Par exemple, l’expression de création de tableau `new int[10,20]` produit une instance de tableau de type `int[,]`, et l’expression de création de tableau `new int[10][,]` produit un tableau de type `int[][,]`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1297">For example, the array creation expression `new int[10,20]` produces an array instance of type `int[,]`, and the array creation expression `new int[10][,]` produces an array of type `int[][,]`.</span></span> <span data-ttu-id="b0787-1298">Chaque expression de la liste d’expressions doit être de type `int`, `uint`, `long`ou `ulong`, ou être implicitement convertible en un ou plusieurs de ces types.</span><span class="sxs-lookup"><span data-stu-id="b0787-1298">Each expression in the expression list must be of type `int`, `uint`, `long`, or `ulong`, or implicitly convertible to one or more of these types.</span></span> <span data-ttu-id="b0787-1299">La valeur de chaque expression détermine la longueur de la dimension correspondante dans l’instance de tableau qui vient d’être allouée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1299">The value of each expression determines the length of the corresponding dimension in the newly allocated array instance.</span></span> <span data-ttu-id="b0787-1300">Dans la mesure où la longueur d’une dimension de tableau ne doit pas être négative, il s’agit d’une erreur au moment de la compilation pour avoir un *constant_expression* avec une valeur négative dans la liste d’expressions.</span><span class="sxs-lookup"><span data-stu-id="b0787-1300">Since the length of an array dimension must be nonnegative, it is a compile-time error to have a *constant_expression* with a negative value in the expression list.</span></span>

<span data-ttu-id="b0787-1301">Sauf dans un contexte non sécurisé ([contextes non sécurisés](unsafe-code.md#unsafe-contexts)), la disposition des tableaux n’est pas spécifiée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1301">Except in an unsafe context ([Unsafe contexts](unsafe-code.md#unsafe-contexts)), the layout of arrays is unspecified.</span></span>

<span data-ttu-id="b0787-1302">Si une expression de création de tableau du premier formulaire comprend un initialiseur de tableau, chaque expression de la liste d’expressions doit être une constante et le rang et les longueurs de dimensions spécifiés par la liste d’expressions doivent correspondre à ceux de l’initialiseur de tableau.</span><span class="sxs-lookup"><span data-stu-id="b0787-1302">If an array creation expression of the first form includes an array initializer, each expression in the expression list must be a constant and the rank and dimension lengths specified by the expression list must match those of the array initializer.</span></span>

<span data-ttu-id="b0787-1303">Dans une expression de création de tableau de la deuxième ou de la troisième forme, le rang du type de tableau ou du spécificateur de rangs spécifié doit correspondre à celui de l’initialiseur de tableau.</span><span class="sxs-lookup"><span data-stu-id="b0787-1303">In an array creation expression of the second or third form, the rank of the specified array type or rank specifier must match that of the array initializer.</span></span> <span data-ttu-id="b0787-1304">Les longueurs de dimensions individuelles sont déduites du nombre d’éléments dans chacun des niveaux d’imbrication correspondants de l’initialiseur de tableau.</span><span class="sxs-lookup"><span data-stu-id="b0787-1304">The individual dimension lengths are inferred from the number of elements in each of the corresponding nesting levels of the array initializer.</span></span> <span data-ttu-id="b0787-1305">Ainsi, l’expression</span><span class="sxs-lookup"><span data-stu-id="b0787-1305">Thus, the expression</span></span>
```csharp
new int[,] {{0, 1}, {2, 3}, {4, 5}}
```
<span data-ttu-id="b0787-1306">correspond exactement à</span><span class="sxs-lookup"><span data-stu-id="b0787-1306">exactly corresponds to</span></span>
```csharp
new int[3, 2] {{0, 1}, {2, 3}, {4, 5}}
```

<span data-ttu-id="b0787-1307">Une expression de création de tableau de la troisième forme est appelée ***expression de création de tableau implicitement typée***.</span><span class="sxs-lookup"><span data-stu-id="b0787-1307">An array creation expression of the third form is referred to as an ***implicitly typed array creation expression***.</span></span> <span data-ttu-id="b0787-1308">Elle est similaire à la deuxième forme, à ceci près que le type d’élément du tableau n’est pas explicitement donné, mais déterminé comme le meilleur type commun ([recherche du meilleur type commun d’un ensemble d’expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)) du jeu d’expressions dans l’initialiseur de tableau.</span><span class="sxs-lookup"><span data-stu-id="b0787-1308">It is similar to the second form, except that the element type of the array is not explicitly given, but determined as the best common type ([Finding the best common type of a set of expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)) of the set of expressions in the array initializer.</span></span> <span data-ttu-id="b0787-1309">Dans le cas d’un tableau multidimensionnel, c’est-à-dire un où le *rank_specifier* contient au moins une virgule, cet ensemble comprend toutes les *expressions*qui se trouvent dans les *array_initializer*imbriqué s.</span><span class="sxs-lookup"><span data-stu-id="b0787-1309">For a multidimensional array, i.e., one where the *rank_specifier* contains at least one comma, this set comprises all *expression*s found in nested *array_initializer*s.</span></span>

<span data-ttu-id="b0787-1310">Les initialiseurs de tableau sont décrits plus en détail dans les [initialiseurs de tableau](arrays.md#array-initializers).</span><span class="sxs-lookup"><span data-stu-id="b0787-1310">Array initializers are described further in [Array initializers](arrays.md#array-initializers).</span></span>

<span data-ttu-id="b0787-1311">Le résultat de l’évaluation d’une expression de création de tableau est classé comme une valeur, à savoir une référence à l’instance de tableau nouvellement allouée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1311">The result of evaluating an array creation expression is classified as a value, namely a reference to the newly allocated array instance.</span></span> <span data-ttu-id="b0787-1312">Le traitement au moment de l’exécution d’une expression de création de tableau se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-1312">The run-time processing of an array creation expression consists of the following steps:</span></span>

*  <span data-ttu-id="b0787-1313">Les expressions de longueur de dimension des *expression_list* sont évaluées dans l’ordre, de gauche à droite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1313">The dimension length expressions of the *expression_list* are evaluated in order, from left to right.</span></span> <span data-ttu-id="b0787-1314">Après l’évaluation de chaque expression, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) vers l’un des types suivants est effectuée : `int`, `uint`, `long`, `ulong`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1314">Following evaluation of each expression, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) to one of the following types is performed: `int`, `uint`, `long`, `ulong`.</span></span> <span data-ttu-id="b0787-1315">Le premier type de cette liste pour lequel une conversion implicite existe est choisi.</span><span class="sxs-lookup"><span data-stu-id="b0787-1315">The first type in this list for which an implicit conversion exists is chosen.</span></span> <span data-ttu-id="b0787-1316">Si l’évaluation d’une expression ou de la conversion implicite suivante provoque une exception, aucune autre expression n’est évaluée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1316">If evaluation of an expression or the subsequent implicit conversion causes an exception, then no further expressions are evaluated and no further steps are executed.</span></span>
*  <span data-ttu-id="b0787-1317">Les valeurs calculées pour les longueurs de dimensions sont validées comme suit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1317">The computed values for the dimension lengths are validated as follows.</span></span> <span data-ttu-id="b0787-1318">Si une ou plusieurs valeurs sont inférieures à zéro, une `System.OverflowException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1318">If one or more of the values are less than zero, a `System.OverflowException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="b0787-1319">Une instance de tableau avec les longueurs de dimensions données est allouée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1319">An array instance with the given dimension lengths is allocated.</span></span> <span data-ttu-id="b0787-1320">Si la mémoire disponible est insuffisante pour allouer la nouvelle instance, une `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1320">If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="b0787-1321">Tous les éléments de la nouvelle instance de tableau sont initialisés à leurs valeurs par défaut ([valeurs par défaut](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1321">All elements of the new array instance are initialized to their default values ([Default values](variables.md#default-values)).</span></span>
*  <span data-ttu-id="b0787-1322">Si l’expression de création de tableau contient un initialiseur de tableau, chaque expression de l’initialiseur de tableau est évaluée et assignée à son élément de tableau correspondant.</span><span class="sxs-lookup"><span data-stu-id="b0787-1322">If the array creation expression contains an array initializer, then each expression in the array initializer is evaluated and assigned to its corresponding array element.</span></span> <span data-ttu-id="b0787-1323">Les évaluations et les assignations sont effectuées dans l’ordre dans lequel les expressions sont écrites dans l’initialiseur de tableau. en d’autres termes, les éléments sont initialisés dans l’ordre d’index de croissance, avec la dimension la plus à droite qui s’incrémente en premier.</span><span class="sxs-lookup"><span data-stu-id="b0787-1323">The evaluations and assignments are performed in the order the expressions are written in the array initializer—in other words, elements are initialized in increasing index order, with the rightmost dimension increasing first.</span></span> <span data-ttu-id="b0787-1324">Si l’évaluation d’une expression donnée ou de l’assignation suivante à l’élément de tableau correspondant provoque une exception, aucun autre élément n’est initialisé (et les éléments restants auront donc leurs valeurs par défaut).</span><span class="sxs-lookup"><span data-stu-id="b0787-1324">If evaluation of a given expression or the subsequent assignment to the corresponding array element causes an exception, then no further elements are initialized (and the remaining elements will thus have their default values).</span></span>

<span data-ttu-id="b0787-1325">Une expression de création de tableau autorise l’instanciation d’un tableau avec des éléments d’un type tableau, mais les éléments d’un tel tableau doivent être initialisés manuellement.</span><span class="sxs-lookup"><span data-stu-id="b0787-1325">An array creation expression permits instantiation of an array with elements of an array type, but the elements of such an array must be manually initialized.</span></span> <span data-ttu-id="b0787-1326">Par exemple, l’instruction</span><span class="sxs-lookup"><span data-stu-id="b0787-1326">For example, the statement</span></span>
```csharp
int[][] a = new int[100][];
```
<span data-ttu-id="b0787-1327">crée un tableau unidimensionnel avec 100 éléments de type `int[]`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1327">creates a single-dimensional array with 100 elements of type `int[]`.</span></span> <span data-ttu-id="b0787-1328">La valeur initiale de chaque élément est `null`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1328">The initial value of each element is `null`.</span></span> <span data-ttu-id="b0787-1329">Il n’est pas possible pour la même expression de création de tableau d’instancier également les sous-tableaux, et l’instruction</span><span class="sxs-lookup"><span data-stu-id="b0787-1329">It is not possible for the same array creation expression to also instantiate the sub-arrays, and the statement</span></span>
```csharp
int[][] a = new int[100][5];        // Error
```
<span data-ttu-id="b0787-1330">génère une erreur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1330">results in a compile-time error.</span></span> <span data-ttu-id="b0787-1331">L’instanciation des sous-tableaux doit plutôt être effectuée manuellement, comme dans</span><span class="sxs-lookup"><span data-stu-id="b0787-1331">Instantiation of the sub-arrays must instead be performed manually, as in</span></span>
```csharp
int[][] a = new int[100][];
for (int i = 0; i < 100; i++) a[i] = new int[5];
```

<span data-ttu-id="b0787-1332">Lorsqu’un tableau de tableaux a une forme « rectangulaire », c’est-à-dire lorsque les sous-tableaux sont tous de la même longueur, il est plus efficace d’utiliser un tableau multidimensionnel.</span><span class="sxs-lookup"><span data-stu-id="b0787-1332">When an array of arrays has a "rectangular" shape, that is when the sub-arrays are all of the same length, it is more efficient to use a multi-dimensional array.</span></span> <span data-ttu-id="b0787-1333">Dans l’exemple ci-dessus, l’instanciation du tableau de tableaux crée 101 objets, un tableau externe et des sous-tableaux 100.</span><span class="sxs-lookup"><span data-stu-id="b0787-1333">In the example above, instantiation of the array of arrays creates 101 objects—one outer array and 100 sub-arrays.</span></span> <span data-ttu-id="b0787-1334">En revanche,</span><span class="sxs-lookup"><span data-stu-id="b0787-1334">In contrast,</span></span>
```csharp
int[,] = new int[100, 5];
```
<span data-ttu-id="b0787-1335">crée un seul objet, un tableau à deux dimensions, et accomplit l’allocation dans une instruction unique.</span><span class="sxs-lookup"><span data-stu-id="b0787-1335">creates only a single object, a two-dimensional array, and accomplishes the allocation in a single statement.</span></span>

<span data-ttu-id="b0787-1336">Voici des exemples d’expressions de création de tableau implicitement typées :</span><span class="sxs-lookup"><span data-stu-id="b0787-1336">The following are examples of implicitly typed array creation expressions:</span></span>
```csharp
var a = new[] { 1, 10, 100, 1000 };                       // int[]

var b = new[] { 1, 1.5, 2, 2.5 };                         // double[]

var c = new[,] { { "hello", null }, { "world", "!" } };   // string[,]

var d = new[] { 1, "one", 2, "two" };                     // Error
```

<span data-ttu-id="b0787-1337">La dernière expression provoque une erreur au moment de la compilation, car ni `int` ni `string` n’est implicitement convertible en l’autre, et il n’y a donc pas de type commun optimal.</span><span class="sxs-lookup"><span data-stu-id="b0787-1337">The last expression causes a compile-time error because neither `int` nor `string` is implicitly convertible to the other, and so there is no best common type.</span></span> <span data-ttu-id="b0787-1338">Une expression de création de tableau explicitement typée doit être utilisée dans ce cas, par exemple en spécifiant le type à `object[]`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1338">An explicitly typed array creation expression must be used in this case, for example specifying the type to be `object[]`.</span></span> <span data-ttu-id="b0787-1339">L’un des éléments peut également être casté en un type de base commun, qui devient alors le type d’élément inféré.</span><span class="sxs-lookup"><span data-stu-id="b0787-1339">Alternatively, one of the elements can be cast to a common base type, which would then become the inferred element type.</span></span>

<span data-ttu-id="b0787-1340">Les expressions de création de tableau implicitement typées peuvent être combinées avec des initialiseurs d’objets anonymes ([expressions de création d’objets anonymes](expressions.md#anonymous-object-creation-expressions)) pour créer des structures de données typées anonymement.</span><span class="sxs-lookup"><span data-stu-id="b0787-1340">Implicitly typed array creation expressions can be combined with anonymous object initializers ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) to create anonymously typed data structures.</span></span> <span data-ttu-id="b0787-1341">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b0787-1341">For example:</span></span>
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

#### <a name="delegate-creation-expressions"></a><span data-ttu-id="b0787-1342">Expressions de création de délégué</span><span class="sxs-lookup"><span data-stu-id="b0787-1342">Delegate creation expressions</span></span>

<span data-ttu-id="b0787-1343">Un *delegate_creation_expression* est utilisé pour créer une nouvelle instance d’une *delegate_type*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1343">A *delegate_creation_expression* is used to create a new instance of a *delegate_type*.</span></span>

```antlr
delegate_creation_expression
    : 'new' delegate_type '(' expression ')'
    ;
```

<span data-ttu-id="b0787-1344">L’argument d’une expression de création de délégué doit être un groupe de méthodes, une fonction anonyme ou une valeur du type au moment de la compilation `dynamic` ou une *delegate_type*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1344">The argument of a delegate creation expression must be a method group, an anonymous function or a value of either the compile time type `dynamic` or a *delegate_type*.</span></span> <span data-ttu-id="b0787-1345">Si l’argument est un groupe de méthodes, il identifie la méthode et, pour une méthode d’instance, l’objet pour lequel créer un délégué.</span><span class="sxs-lookup"><span data-stu-id="b0787-1345">If the argument is a method group, it identifies the method and, for an instance method, the object for which to create a delegate.</span></span> <span data-ttu-id="b0787-1346">Si l’argument est une fonction anonyme, il définit directement les paramètres et le corps de la méthode de la cible du délégué.</span><span class="sxs-lookup"><span data-stu-id="b0787-1346">If the argument is an anonymous function it directly defines the parameters and method body of the delegate target.</span></span> <span data-ttu-id="b0787-1347">Si l’argument est une valeur, il identifie une instance de délégué de laquelle créer une copie.</span><span class="sxs-lookup"><span data-stu-id="b0787-1347">If the argument is a value it identifies a delegate instance of which to create a copy.</span></span>

<span data-ttu-id="b0787-1348">Si le type de l' *expression* est `dynamic`au moment de la compilation, le *delegate_creation_expression* est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)) et les règles ci-dessous sont appliquées au moment de l’exécution à l’aide du type d’exécution de l' *expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1348">If the *expression* has the compile-time type `dynamic`, the *delegate_creation_expression* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)), and the rules below are applied at run-time using the run-time type of the *expression*.</span></span> <span data-ttu-id="b0787-1349">Dans le cas contraire, les règles sont appliquées au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1349">Otherwise the rules are applied at compile-time.</span></span>

<span data-ttu-id="b0787-1350">Le traitement au moment de la liaison d’une *delegate_creation_expression* de la forme `new D(E)`, où `D` est un *delegate_type* et `E` est une *expression*, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-1350">The binding-time processing of a *delegate_creation_expression* of the form `new D(E)`, where `D` is a *delegate_type* and `E` is an *expression*, consists of the following steps:</span></span>

*  <span data-ttu-id="b0787-1351">Si `E` est un groupe de méthodes, l’expression de création de délégué est traitée de la même façon qu’une conversion de groupe de méthodes ([conversions de groupe de méthodes](conversions.md#method-group-conversions)) de `E` à `D`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1351">If `E` is a method group, the delegate creation expression is processed in the same way as a method group conversion ([Method group conversions](conversions.md#method-group-conversions)) from `E` to `D`.</span></span>
*  <span data-ttu-id="b0787-1352">Si `E` est une fonction anonyme, l’expression de création de délégué est traitée de la même façon qu’une conversion de fonction anonyme ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)) de `E` à `D`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1352">If `E` is an anonymous function, the delegate creation expression is processed in the same way as an anonymous function conversion ([Anonymous function conversions](conversions.md#anonymous-function-conversions)) from `E` to `D`.</span></span>
*  <span data-ttu-id="b0787-1353">Si `E` est une valeur, `E` doit être compatible ([déclarations de délégué](delegates.md#delegate-declarations)) avec `D`et le résultat est une référence à un délégué nouvellement créé de type `D` qui fait référence à la même liste d’appel que `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1353">If `E` is a value, `E` must be compatible ([Delegate declarations](delegates.md#delegate-declarations)) with `D`, and the result is a reference to a newly created delegate of type `D` that refers to the same invocation list as `E`.</span></span> <span data-ttu-id="b0787-1354">Si `E` n’est pas compatible avec `D`, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1354">If `E` is not compatible with `D`, a compile-time error occurs.</span></span>

<span data-ttu-id="b0787-1355">Le traitement au moment de l’exécution d’une *delegate_creation_expression* de la forme `new D(E)`, où `D` est un *delegate_type* et `E` est une *expression*, se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-1355">The run-time processing of a *delegate_creation_expression* of the form `new D(E)`, where `D` is a *delegate_type* and `E` is an *expression*, consists of the following steps:</span></span>

*   <span data-ttu-id="b0787-1356">Si `E` est un groupe de méthodes, l’expression de création de délégué est évaluée comme une conversion de groupe de méthodes ([conversions de groupe de méthodes](conversions.md#method-group-conversions)) de `E` à `D`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1356">If `E` is a method group, the delegate creation expression is evaluated as a method group conversion ([Method group conversions](conversions.md#method-group-conversions)) from `E` to `D`.</span></span>
*   <span data-ttu-id="b0787-1357">Si `E` est une fonction anonyme, la création du délégué est évaluée comme une conversion de fonction anonyme de `E` en `D` ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1357">If `E` is an anonymous function, the delegate creation is evaluated as an anonymous function conversion from `E` to `D` ([Anonymous function conversions](conversions.md#anonymous-function-conversions)).</span></span>
*   <span data-ttu-id="b0787-1358">Si `E` est une valeur d’un *delegate_type*:</span><span class="sxs-lookup"><span data-stu-id="b0787-1358">If `E` is a value of a *delegate_type*:</span></span>
    * <span data-ttu-id="b0787-1359">`E` est évalué.</span><span class="sxs-lookup"><span data-stu-id="b0787-1359">`E` is evaluated.</span></span> <span data-ttu-id="b0787-1360">Si cette évaluation provoque une exception, aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1360">If this evaluation causes an exception, no further steps are executed.</span></span>
    * <span data-ttu-id="b0787-1361">Si la valeur de `E` est `null`, une `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1361">If the value of `E` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
    * <span data-ttu-id="b0787-1362">Une nouvelle instance du type délégué `D` est allouée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1362">A new instance of the delegate type `D` is allocated.</span></span> <span data-ttu-id="b0787-1363">Si la mémoire disponible est insuffisante pour allouer la nouvelle instance, une `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1363">If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.</span></span>
    * <span data-ttu-id="b0787-1364">La nouvelle instance de délégué est initialisée avec la même liste d’appel que l’instance de délégué donnée par `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1364">The new delegate instance is initialized with the same invocation list as the delegate instance given by `E`.</span></span>

<span data-ttu-id="b0787-1365">La liste d’appel d’un délégué est déterminée lorsque le délégué est instancié, puis reste constante pendant toute la durée de vie du délégué.</span><span class="sxs-lookup"><span data-stu-id="b0787-1365">The invocation list of a delegate is determined when the delegate is instantiated and then remains constant for the entire lifetime of the delegate.</span></span> <span data-ttu-id="b0787-1366">En d’autres termes, il n’est pas possible de modifier les entités pouvant être appelées par Target d’un délégué une fois qu’il a été créé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1366">In other words, it is not possible to change the target callable entities of a delegate once it has been created.</span></span> <span data-ttu-id="b0787-1367">Quand deux délégués sont combinés ou s’ils sont supprimés d’un autre ([déclarations déléguées](delegates.md#delegate-declarations)), un nouveau délégué est obtenu ; aucun contenu n’a été modifié pour le délégué existant.</span><span class="sxs-lookup"><span data-stu-id="b0787-1367">When two delegates are combined or one is removed from another ([Delegate declarations](delegates.md#delegate-declarations)), a new delegate results; no existing delegate has its contents changed.</span></span>

<span data-ttu-id="b0787-1368">Il n’est pas possible de créer un délégué qui fait référence à une propriété, un indexeur, un opérateur défini par l’utilisateur, un constructeur d’instance, un destructeur ou un constructeur statique.</span><span class="sxs-lookup"><span data-stu-id="b0787-1368">It is not possible to create a delegate that refers to a property, indexer, user-defined operator, instance constructor, destructor, or static constructor.</span></span>

<span data-ttu-id="b0787-1369">Comme décrit ci-dessus, quand un délégué est créé à partir d’un groupe de méthodes, la liste de paramètres formels et le type de retour du délégué déterminent les méthodes surchargées à sélectionner.</span><span class="sxs-lookup"><span data-stu-id="b0787-1369">As described above, when a delegate is created from a method group, the formal parameter list and return type of the delegate determine which of the overloaded methods to select.</span></span> <span data-ttu-id="b0787-1370">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-1370">In the example</span></span>
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
<span data-ttu-id="b0787-1371">le champ `A.f` est initialisé avec un délégué qui fait référence à la deuxième méthode `Square`, car cette méthode correspond exactement à la liste de paramètres formels et au type de retour de `DoubleFunc`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1371">the `A.f` field is initialized with a delegate that refers to the second `Square` method because that method exactly matches the formal parameter list and return type of `DoubleFunc`.</span></span> <span data-ttu-id="b0787-1372">Si la deuxième méthode `Square` n’était pas présente, une erreur de compilation s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1372">Had the second `Square` method not been present, a compile-time error would have occurred.</span></span>

#### <a name="anonymous-object-creation-expressions"></a><span data-ttu-id="b0787-1373">Expressions de création d’objets anonymes</span><span class="sxs-lookup"><span data-stu-id="b0787-1373">Anonymous object creation expressions</span></span>

<span data-ttu-id="b0787-1374">Un *anonymous_object_creation_expression* est utilisé pour créer un objet d’un type anonyme.</span><span class="sxs-lookup"><span data-stu-id="b0787-1374">An *anonymous_object_creation_expression* is used to create an object of an anonymous type.</span></span>

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

<span data-ttu-id="b0787-1375">Un initialiseur d’objet anonyme déclare un type anonyme et retourne une instance de ce type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1375">An anonymous object initializer declares an anonymous type and returns an instance of that type.</span></span> <span data-ttu-id="b0787-1376">Un type anonyme est un type de classe sans type qui hérite directement de `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1376">An anonymous type is a nameless class type that inherits directly from `object`.</span></span> <span data-ttu-id="b0787-1377">Les membres d’un type anonyme sont une séquence de propriétés en lecture seule déduites de l’initialiseur d’objet anonyme utilisé pour créer une instance du type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1377">The members of an anonymous type are a sequence of read-only properties inferred from the anonymous object initializer used to create an instance of the type.</span></span> <span data-ttu-id="b0787-1378">Plus précisément, un initialiseur d’objet anonyme de la forme</span><span class="sxs-lookup"><span data-stu-id="b0787-1378">Specifically, an anonymous object initializer of the form</span></span>
```csharp
new { p1 = e1, p2 = e2, ..., pn = en }
```
<span data-ttu-id="b0787-1379">déclare un type anonyme de la forme</span><span class="sxs-lookup"><span data-stu-id="b0787-1379">declares an anonymous type of the form</span></span>
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
<span data-ttu-id="b0787-1380">où chaque `Tx` est le type de l’expression correspondante `ex`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1380">where each `Tx` is the type of the corresponding expression `ex`.</span></span> <span data-ttu-id="b0787-1381">L’expression utilisée dans un *member_declarator* doit avoir un type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1381">The expression used in a *member_declarator* must have a type.</span></span> <span data-ttu-id="b0787-1382">Par conséquent, il s’agit d’une erreur de compilation pour qu’une expression d’un *member_declarator* soit null ou une fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="b0787-1382">Thus, it is a compile-time error for an expression in a *member_declarator* to be null or an anonymous function.</span></span> <span data-ttu-id="b0787-1383">Il s’agit également d’une erreur au moment de la compilation pour que l’expression ait un type non sécurisé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1383">It is also a compile-time error for the expression to have an unsafe type.</span></span>

<span data-ttu-id="b0787-1384">Les noms d’un type anonyme et du paramètre de sa méthode `Equals` sont générés automatiquement par le compilateur et ne peuvent pas être référencés dans le texte du programme.</span><span class="sxs-lookup"><span data-stu-id="b0787-1384">The names of an anonymous type and of the parameter to its `Equals` method are automatically generated by the compiler and cannot be referenced in program text.</span></span>

<span data-ttu-id="b0787-1385">Dans le même programme, deux initialiseurs d’objets anonymes qui spécifient une séquence de propriétés des mêmes noms et types au moment de la compilation dans le même ordre produisent des instances du même type anonyme.</span><span class="sxs-lookup"><span data-stu-id="b0787-1385">Within the same program, two anonymous object initializers that specify a sequence of properties of the same names and compile-time types in the same order will produce instances of the same anonymous type.</span></span>

<span data-ttu-id="b0787-1386">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-1386">In the example</span></span>
```csharp
var p1 = new { Name = "Lawnmower", Price = 495.00 };
var p2 = new { Name = "Shovel", Price = 26.95 };
p1 = p2;
```
<span data-ttu-id="b0787-1387">l’assignation sur la dernière ligne est autorisée, car `p1` et `p2` sont du même type anonyme.</span><span class="sxs-lookup"><span data-stu-id="b0787-1387">the assignment on the last line is permitted because `p1` and `p2` are of the same anonymous type.</span></span>

<span data-ttu-id="b0787-1388">Les méthodes `Equals` et `GetHashcode` sur les types anonymes substituent les méthodes héritées de `object`, et sont définies en termes de `Equals` et de `GetHashcode` des propriétés, afin que deux instances du même type anonyme soient égales si et seulement si toutes leurs propriétés sont égales.</span><span class="sxs-lookup"><span data-stu-id="b0787-1388">The `Equals` and `GetHashcode` methods on anonymous types override the methods inherited from `object`, and are defined in terms of the `Equals` and `GetHashcode` of the properties, so that two instances of the same anonymous type are equal if and only if all their properties are equal.</span></span>

<span data-ttu-id="b0787-1389">Un déclarateur de membre peut être abrégé en un nom simple ([inférence de type](expressions.md#type-inference)), un accès de membre ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), un accès de base ([accès de base](expressions.md#base-access)) ou un accès de membre conditionnel null ([expressions conditionnelles NULL comme initialiseurs de projection](expressions.md#null-conditional-expressions-as-projection-initializers)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1389">A member declarator can be abbreviated to a simple name ([Type inference](expressions.md#type-inference)), a member access ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), a base access ([Base access](expressions.md#base-access)) or a null-conditional member access ([Null-conditional expressions as projection initializers](expressions.md#null-conditional-expressions-as-projection-initializers)).</span></span> <span data-ttu-id="b0787-1390">C’est ce qu’on appelle un ***initialiseur de projection*** et est un raccourci pour une déclaration et une assignation à une propriété du même nom.</span><span class="sxs-lookup"><span data-stu-id="b0787-1390">This is called a ***projection initializer*** and is shorthand for a declaration of and assignment to a property with the same name.</span></span> <span data-ttu-id="b0787-1391">Plus précisément, les déclarateurs de membres des formulaires</span><span class="sxs-lookup"><span data-stu-id="b0787-1391">Specifically, member declarators of the forms</span></span>
```csharp
identifier
expr.identifier
```
<span data-ttu-id="b0787-1392">sont exactement équivalents à ce qui suit, respectivement :</span><span class="sxs-lookup"><span data-stu-id="b0787-1392">are precisely equivalent to the following, respectively:</span></span>
```csharp
identifier = identifier
identifier = expr.identifier
```

<span data-ttu-id="b0787-1393">Ainsi, dans un initialiseur de projection, l' *identificateur* sélectionne à la fois la valeur et le champ ou la propriété auquel la valeur est assignée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1393">Thus, in a projection initializer the *identifier* selects both the value and the field or property to which the value is assigned.</span></span> <span data-ttu-id="b0787-1394">Intuitivement, un initialiseur de projection projette non seulement une valeur, mais également le nom de la valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1394">Intuitively, a projection initializer projects not just a value, but also the name of the value.</span></span>

### <a name="the-typeof-operator"></a><span data-ttu-id="b0787-1395">Opérateur typeof</span><span class="sxs-lookup"><span data-stu-id="b0787-1395">The typeof operator</span></span>

<span data-ttu-id="b0787-1396">L’opérateur `typeof` est utilisé pour obtenir l’objet `System.Type` pour un type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1396">The `typeof` operator is used to obtain the `System.Type` object for a type.</span></span>

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

<span data-ttu-id="b0787-1397">La première forme de *typeof_expression* se compose d’un mot clé `typeof` suivi d’un *type*entre parenthèses.</span><span class="sxs-lookup"><span data-stu-id="b0787-1397">The first form of *typeof_expression* consists of a `typeof` keyword followed by a parenthesized *type*.</span></span> <span data-ttu-id="b0787-1398">Le résultat d’une expression de ce formulaire est l’objet `System.Type` pour le type indiqué.</span><span class="sxs-lookup"><span data-stu-id="b0787-1398">The result of an expression of this form is the `System.Type` object for the indicated type.</span></span> <span data-ttu-id="b0787-1399">Il n’existe qu’un seul objet `System.Type` pour un type donné.</span><span class="sxs-lookup"><span data-stu-id="b0787-1399">There is only one `System.Type` object for any given type.</span></span> <span data-ttu-id="b0787-1400">Cela signifie que pour un type `T`, `typeof(T) == typeof(T)` a toujours la valeur true.</span><span class="sxs-lookup"><span data-stu-id="b0787-1400">This means that for a type `T`, `typeof(T) == typeof(T)` is always true.</span></span> <span data-ttu-id="b0787-1401">Le *type* ne peut pas être `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1401">The *type* cannot be `dynamic`.</span></span>

<span data-ttu-id="b0787-1402">La deuxième forme de *typeof_expression* se compose d’un mot clé `typeof` suivi d’un *unbound_type_name*entre parenthèses.</span><span class="sxs-lookup"><span data-stu-id="b0787-1402">The second form of *typeof_expression* consists of a `typeof` keyword followed by a parenthesized *unbound_type_name*.</span></span> <span data-ttu-id="b0787-1403">Un *unbound_type_name* est très similaire à un *type_name* ([nom de l’espace de noms et type](basic-concepts.md#namespace-and-type-names)), à ceci près qu’un *unbound_type_name* contient *generic_dimension_specifier*s où un *type_name* contient *type_argument_list*s.</span><span class="sxs-lookup"><span data-stu-id="b0787-1403">An *unbound_type_name* is very similar to a *type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)) except that an *unbound_type_name* contains *generic_dimension_specifier*s where a *type_name* contains *type_argument_list*s.</span></span> <span data-ttu-id="b0787-1404">Lorsque l’opérande d’un *typeof_expression* est une séquence de jetons qui satisfait les grammaires de *unbound_type_name* et *type_name*, à savoir lorsqu’il ne contient ni un *generic_dimension_specifier* ni un *type_argument_list*, la séquence de jetons est considérée comme un *type_name*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1404">When the operand of a *typeof_expression* is a sequence of tokens that satisfies the grammars of both *unbound_type_name* and *type_name*, namely when it contains neither a *generic_dimension_specifier* nor a *type_argument_list*, the sequence of tokens is considered to be a *type_name*.</span></span> <span data-ttu-id="b0787-1405">La signification d’un *unbound_type_name* est déterminée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-1405">The meaning of an *unbound_type_name* is determined as follows:</span></span>

*  <span data-ttu-id="b0787-1406">Convertissez la séquence de jetons en *type_name* en remplaçant chaque *generic_dimension_specifier* par un *type_argument_list* ayant le même nombre de virgules et le mot clé `object` comme chaque *type_argument*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1406">Convert the sequence of tokens to a *type_name* by replacing each *generic_dimension_specifier* with a *type_argument_list* having the same number of commas and the keyword `object` as each *type_argument*.</span></span>
*  <span data-ttu-id="b0787-1407">Évaluez le *type_name*résultant, en ignorant toutes les contraintes de paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1407">Evaluate the resulting *type_name*, while ignoring all type parameter constraints.</span></span>
*  <span data-ttu-id="b0787-1408">Le *unbound_type_name* est résolu en type générique indépendant associé au type construit résultant ([types liés et indépendants](types.md#bound-and-unbound-types)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1408">The *unbound_type_name* resolves to the unbound generic type associated with the resulting constructed type ([Bound and unbound types](types.md#bound-and-unbound-types)).</span></span>

<span data-ttu-id="b0787-1409">Le résultat de l' *typeof_expression* est l’objet `System.Type` pour le type générique indépendant qui en résulte.</span><span class="sxs-lookup"><span data-stu-id="b0787-1409">The result of the *typeof_expression* is the `System.Type` object for the resulting unbound generic type.</span></span>

<span data-ttu-id="b0787-1410">La troisième forme de *typeof_expression* se compose d’un mot clé `typeof` suivi d’un mot clé `void` entre parenthèses.</span><span class="sxs-lookup"><span data-stu-id="b0787-1410">The third form of *typeof_expression* consists of a `typeof` keyword followed by a parenthesized `void` keyword.</span></span> <span data-ttu-id="b0787-1411">Le résultat d’une expression de ce formulaire est l’objet `System.Type` qui représente l’absence d’un type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1411">The result of an expression of this form is the `System.Type` object that represents the absence of a type.</span></span> <span data-ttu-id="b0787-1412">L’objet de type retourné par `typeof(void)` est différent de l’objet de type retourné pour tout type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1412">The type object returned by `typeof(void)` is distinct from the type object returned for any type.</span></span> <span data-ttu-id="b0787-1413">Cet objet de type spécial est utile dans les bibliothèques de classes qui autorisent la réflexion sur les méthodes dans le langage, où ces méthodes souhaitent avoir un moyen de représenter le type de retour d’une méthode quelconque, y compris les méthodes void, avec une instance de `System.Type`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1413">This special type object is useful in class libraries that allow reflection onto methods in the language, where those methods wish to have a way to represent the return type of any method, including void methods, with an instance of `System.Type`.</span></span>

<span data-ttu-id="b0787-1414">L’opérateur `typeof` peut être utilisé sur un paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1414">The `typeof` operator can be used on a type parameter.</span></span> <span data-ttu-id="b0787-1415">Le résultat est l’objet `System.Type` pour le type au moment de l’exécution qui a été lié au paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1415">The result is the `System.Type` object for the run-time type that was bound to the type parameter.</span></span> <span data-ttu-id="b0787-1416">L’opérateur `typeof` peut également être utilisé sur un type construit ou un type générique indépendant ([types liés et indépendants](types.md#bound-and-unbound-types)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1416">The `typeof` operator can also be used on a constructed type or an unbound generic type ([Bound and unbound types](types.md#bound-and-unbound-types)).</span></span> <span data-ttu-id="b0787-1417">L’objet `System.Type` pour un type générique indépendant n’est pas le même que l’objet `System.Type` du type d’instance.</span><span class="sxs-lookup"><span data-stu-id="b0787-1417">The `System.Type` object for an unbound generic type is not the same as the `System.Type` object of the instance type.</span></span> <span data-ttu-id="b0787-1418">Le type d’instance est toujours un type construit fermé au moment de l’exécution, de sorte que son objet `System.Type` dépend des arguments de type au moment de l’exécution, tandis que le type générique indépendant n’a aucun argument de type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1418">The instance type is always a closed constructed type at run-time so its `System.Type` object depends on the run-time type arguments in use, while the unbound generic type has no type arguments.</span></span>

<span data-ttu-id="b0787-1419">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-1419">The example</span></span>
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
<span data-ttu-id="b0787-1420">produit la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="b0787-1420">produces the following output:</span></span>
```console
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

<span data-ttu-id="b0787-1421">Notez que `int` et `System.Int32` sont du même type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1421">Note that `int` and `System.Int32` are the same type.</span></span>

<span data-ttu-id="b0787-1422">Notez également que le résultat de `typeof(X<>)` ne dépend pas de l’argument de type, mais le résultat de `typeof(X<T>)` le fait.</span><span class="sxs-lookup"><span data-stu-id="b0787-1422">Also note that the result of `typeof(X<>)` does not depend on the type argument but the result of `typeof(X<T>)` does.</span></span>

### <a name="the-checked-and-unchecked-operators"></a><span data-ttu-id="b0787-1423">Opérateurs vérifiés et non vérifiés</span><span class="sxs-lookup"><span data-stu-id="b0787-1423">The checked and unchecked operators</span></span>

<span data-ttu-id="b0787-1424">Les opérateurs `checked` et `unchecked` sont utilisés pour contrôler le ***contexte*** de contrôle de dépassement de capacité pour les opérations arithmétiques de type intégral et les conversions.</span><span class="sxs-lookup"><span data-stu-id="b0787-1424">The `checked` and `unchecked` operators are used to control the ***overflow checking context*** for integral-type arithmetic operations and conversions.</span></span>

```antlr
checked_expression
    : 'checked' '(' expression ')'
    ;

unchecked_expression
    : 'unchecked' '(' expression ')'
    ;
```

<span data-ttu-id="b0787-1425">L’opérateur `checked` évalue l’expression contenue dans un contexte vérifié, tandis que l’opérateur `unchecked` évalue l’expression contenue dans un contexte non vérifié.</span><span class="sxs-lookup"><span data-stu-id="b0787-1425">The `checked` operator evaluates the contained expression in a checked context, and the `unchecked` operator evaluates the contained expression in an unchecked context.</span></span> <span data-ttu-id="b0787-1426">Un *checked_expression* ou *unchecked_expression* correspond exactement à un *parenthesized_expression* ([expressions entre parenthèses](expressions.md#parenthesized-expressions)), sauf que l’expression contenue est évaluée dans le contexte de contrôle de dépassement de capacité donné.</span><span class="sxs-lookup"><span data-stu-id="b0787-1426">A *checked_expression* or *unchecked_expression* corresponds exactly to a *parenthesized_expression* ([Parenthesized expressions](expressions.md#parenthesized-expressions)), except that the contained expression is evaluated in the given overflow checking context.</span></span>

<span data-ttu-id="b0787-1427">Le contexte de contrôle de dépassement de capacité peut également être contrôlé via les instructions `checked` et `unchecked` ([les instructions checked et unchecked](statements.md#the-checked-and-unchecked-statements)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1427">The overflow checking context can also be controlled through the `checked` and `unchecked` statements ([The checked and unchecked statements](statements.md#the-checked-and-unchecked-statements)).</span></span>

<span data-ttu-id="b0787-1428">Les opérations suivantes sont affectées par le contexte de contrôle de dépassement de capacité établi par le `checked` et `unchecked` les opérateurs et les instructions :</span><span class="sxs-lookup"><span data-stu-id="b0787-1428">The following operations are affected by the overflow checking context established by the `checked` and `unchecked` operators and statements:</span></span>

*  <span data-ttu-id="b0787-1429">Les opérateurs unaires `++` et `--` prédéfinis ([suffixe d’incrémentation et de décrémentation](expressions.md#postfix-increment-and-decrement-operators) et [opérateurs d’incrémentation et de décrémentation de préfixe](expressions.md#prefix-increment-and-decrement-operators)), lorsque l’opérande est de type intégral.</span><span class="sxs-lookup"><span data-stu-id="b0787-1429">The predefined `++` and `--` unary operators ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators) and [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)), when the operand is of an integral type.</span></span>
*  <span data-ttu-id="b0787-1430">Opérateur unaire `-` prédéfini ([opérateur moins unaire](expressions.md#unary-minus-operator)), lorsque l’opérande est de type intégral.</span><span class="sxs-lookup"><span data-stu-id="b0787-1430">The predefined `-` unary operator ([Unary minus operator](expressions.md#unary-minus-operator)), when the operand is of an integral type.</span></span>
*  <span data-ttu-id="b0787-1431">Les opérateurs binaires prédéfinis `+`, `-`, `*`et `/` ([opérateurs arithmétiques](expressions.md#arithmetic-operators)), lorsque les deux opérandes sont des types intégraux.</span><span class="sxs-lookup"><span data-stu-id="b0787-1431">The predefined `+`, `-`, `*`, and `/` binary operators ([Arithmetic operators](expressions.md#arithmetic-operators)), when both operands are of integral types.</span></span>
*  <span data-ttu-id="b0787-1432">Conversions numériques explicites ([conversions numériques explicites](conversions.md#explicit-numeric-conversions)) d’un type intégral vers un autre type intégral, ou de `float` ou `double` à un type intégral.</span><span class="sxs-lookup"><span data-stu-id="b0787-1432">Explicit numeric conversions ([Explicit numeric conversions](conversions.md#explicit-numeric-conversions)) from one integral type to another integral type, or from `float` or `double` to an integral type.</span></span>

<span data-ttu-id="b0787-1433">Lorsque l’une des opérations ci-dessus génère un résultat qui est trop grand pour être représenté dans le type de destination, le contexte dans lequel l’opération est effectuée contrôle le comportement résultant :</span><span class="sxs-lookup"><span data-stu-id="b0787-1433">When one of the above operations produce a result that is too large to represent in the destination type, the context in which the operation is performed controls the resulting behavior:</span></span>

*  <span data-ttu-id="b0787-1434">Dans un contexte de `checked`, si l’opération est une expression constante ([expressions constantes](expressions.md#constant-expressions)), une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1434">In a `checked` context, if the operation is a constant expression ([Constant expressions](expressions.md#constant-expressions)), a compile-time error occurs.</span></span> <span data-ttu-id="b0787-1435">Dans le cas contraire, lorsque l’opération est effectuée au moment de l’exécution, une `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1435">Otherwise, when the operation is performed at run-time, a `System.OverflowException` is thrown.</span></span>
*  <span data-ttu-id="b0787-1436">Dans un contexte de `unchecked`, le résultat est tronqué en ignorant les bits de poids fort qui ne tiennent pas dans le type de destination.</span><span class="sxs-lookup"><span data-stu-id="b0787-1436">In an `unchecked` context, the result is truncated by discarding any high-order bits that do not fit in the destination type.</span></span>

<span data-ttu-id="b0787-1437">Pour les expressions non constantes (expressions évaluées au moment de l’exécution) qui ne sont pas encadrées par des opérateurs ou des instructions `checked` ou `unchecked`, le contexte de contrôle de dépassement de capacité par défaut est `unchecked`, à moins que des facteurs externes (tels que les commutateurs de compilateur et la configuration de l’environnement d’exécution) appellent pour `checked` évaluation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1437">For non-constant expressions (expressions that are evaluated at run-time) that are not enclosed by any `checked` or `unchecked` operators or statements, the default overflow checking context is `unchecked` unless external factors (such as compiler switches and execution environment configuration) call for `checked` evaluation.</span></span>

<span data-ttu-id="b0787-1438">Pour les expressions constantes (expressions qui peuvent être entièrement évaluées au moment de la compilation), le contexte de contrôle de dépassement de capacité par défaut est toujours `checked`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1438">For constant expressions (expressions that can be fully evaluated at compile-time), the default overflow checking context is always `checked`.</span></span> <span data-ttu-id="b0787-1439">À moins qu’une expression constante soit explicitement placée dans un contexte de `unchecked`, les débordements qui se produisent pendant l’évaluation de l’expression au moment de la compilation provoquent toujours des erreurs au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1439">Unless a constant expression is explicitly placed in an `unchecked` context, overflows that occur during the compile-time evaluation of the expression always cause compile-time errors.</span></span>

<span data-ttu-id="b0787-1440">Le corps d’une fonction anonyme n’est pas affecté par `checked` ou `unchecked` contextes dans lesquels la fonction anonyme se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1440">The body of an anonymous function is not affected by `checked` or `unchecked` contexts in which the anonymous function occurs.</span></span>

<span data-ttu-id="b0787-1441">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-1441">In the example</span></span>
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
<span data-ttu-id="b0787-1442">aucune erreur de compilation n’est signalée, car aucune des expressions ne peut être évaluée au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1442">no compile-time errors are reported since neither of the expressions can be evaluated at compile-time.</span></span> <span data-ttu-id="b0787-1443">Au moment de l’exécution, la méthode `F` lève une `System.OverflowException`et la méthode `G` retourne-727379968 (les 32 de poids faible du résultat hors limites).</span><span class="sxs-lookup"><span data-stu-id="b0787-1443">At run-time, the `F` method throws a `System.OverflowException`, and the `G` method returns -727379968 (the lower 32 bits of the out-of-range result).</span></span> <span data-ttu-id="b0787-1444">Le comportement de la méthode `H` dépend du contexte de vérification de dépassement de capacité par défaut pour la compilation, mais est identique à `F` ou identique à `G`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1444">The behavior of the `H` method depends on the default overflow checking context for the compilation, but it is either the same as `F` or the same as `G`.</span></span>

<span data-ttu-id="b0787-1445">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-1445">In the example</span></span>
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
<span data-ttu-id="b0787-1446">les débordements qui se produisent lors de l’évaluation des expressions constantes dans `F` et `H` entraînent le signalement des erreurs de compilation, car les expressions sont évaluées dans un contexte de `checked`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1446">the overflows that occur when evaluating the constant expressions in `F` and `H` cause compile-time errors to be reported because the expressions are evaluated in a `checked` context.</span></span> <span data-ttu-id="b0787-1447">Un dépassement de capacité se produit également lors de l’évaluation de l’expression constante dans `G`, mais étant donné que l’évaluation a lieu dans un contexte de `unchecked`, le dépassement de capacité n’est pas signalé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1447">An overflow also occurs when evaluating the constant expression in `G`, but since the evaluation takes place in an `unchecked` context, the overflow is not reported.</span></span>

<span data-ttu-id="b0787-1448">Les opérateurs `checked` et `unchecked` affectent uniquement le contexte de contrôle de dépassement de capacité pour les opérations qui sont textuellement contenues dans les jetons «`(`» et «`)`».</span><span class="sxs-lookup"><span data-stu-id="b0787-1448">The `checked` and `unchecked` operators only affect the overflow checking context for those operations that are textually contained within the "`(`" and "`)`" tokens.</span></span> <span data-ttu-id="b0787-1449">Les opérateurs n’ont aucun effet sur les fonctions membres qui sont appelées suite à l’évaluation de l’expression contenue.</span><span class="sxs-lookup"><span data-stu-id="b0787-1449">The operators have no effect on function members that are invoked as a result of evaluating the contained expression.</span></span> <span data-ttu-id="b0787-1450">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-1450">In the example</span></span>
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
<span data-ttu-id="b0787-1451">l’utilisation de `checked` dans `F` n’affecte pas l’évaluation de `x * y` dans `Multiply`, donc `x * y` est évaluée dans le contexte de contrôle de dépassement de capacité par défaut.</span><span class="sxs-lookup"><span data-stu-id="b0787-1451">the use of `checked` in `F` does not affect the evaluation of `x * y` in `Multiply`, so `x * y` is evaluated in the default overflow checking context.</span></span>

<span data-ttu-id="b0787-1452">L’opérateur `unchecked` est pratique lors de l’écriture de constantes des types intégraux signés en notation hexadécimale.</span><span class="sxs-lookup"><span data-stu-id="b0787-1452">The `unchecked` operator is convenient when writing constants of the signed integral types in hexadecimal notation.</span></span> <span data-ttu-id="b0787-1453">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b0787-1453">For example:</span></span>
```csharp
class Test
{
    public const int AllBits = unchecked((int)0xFFFFFFFF);

    public const int HighBit = unchecked((int)0x80000000);
}
```

<span data-ttu-id="b0787-1454">Les deux constantes hexadécimales ci-dessus sont de type `uint`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1454">Both of the hexadecimal constants above are of type `uint`.</span></span> <span data-ttu-id="b0787-1455">Étant donné que les constantes se trouvent en dehors de la plage de `int`, sans l’opérateur `unchecked`, les casts en `int` produisent des erreurs de compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1455">Because the constants are outside the `int` range, without the `unchecked` operator, the casts to `int` would produce compile-time errors.</span></span>

<span data-ttu-id="b0787-1456">Les opérateurs et les instructions `checked` et `unchecked` permettent aux programmeurs de contrôler certains aspects de certains calculs numériques.</span><span class="sxs-lookup"><span data-stu-id="b0787-1456">The `checked` and `unchecked` operators and statements allow programmers to control certain aspects of some numeric calculations.</span></span> <span data-ttu-id="b0787-1457">Toutefois, le comportement de certains opérateurs numériques dépend des types de données des opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-1457">However, the behavior of some numeric operators depends on their operands' data types.</span></span> <span data-ttu-id="b0787-1458">Par exemple, la multiplication de deux décimales produit toujours une exception en cas de dépassement de capacité, même dans une construction de `unchecked` explicite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1458">For example, multiplying two decimals always results in an exception on overflow even within an explicitly `unchecked` construct.</span></span> <span data-ttu-id="b0787-1459">De même, la multiplication de deux valeurs float n’entraîne jamais d’exception en cas de dépassement de capacité, même dans une construction de `checked` explicite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1459">Similarly, multiplying two floats never results in an exception on overflow even within an explicitly `checked` construct.</span></span> <span data-ttu-id="b0787-1460">En outre, les autres opérateurs ne sont jamais affectés par le mode de vérification, qu’il s’agisse d’une valeur par défaut ou explicite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1460">In addition, other operators are never affected by the mode of checking, whether default or explicit.</span></span>

### <a name="default-value-expressions"></a><span data-ttu-id="b0787-1461">Expressions de valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="b0787-1461">Default value expressions</span></span>

<span data-ttu-id="b0787-1462">Une expression de valeur par défaut est utilisée pour obtenir la valeur par défaut ([valeurs par défaut](variables.md#default-values)) d’un type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1462">A default value expression is used to obtain the default value ([Default values](variables.md#default-values)) of a type.</span></span> <span data-ttu-id="b0787-1463">En général, une expression de valeur par défaut est utilisée pour les paramètres de type, car elle peut ne pas être connue si le paramètre de type est un type valeur ou un type référence.</span><span class="sxs-lookup"><span data-stu-id="b0787-1463">Typically a default value expression is used for type parameters, since it may not be known if the type parameter is a value type or a reference type.</span></span> <span data-ttu-id="b0787-1464">(Aucune conversion n’existe entre le littéral `null` et un paramètre de type, sauf si le paramètre de type est connu comme étant un type référence.)</span><span class="sxs-lookup"><span data-stu-id="b0787-1464">(No conversion exists from the `null` literal to a type parameter unless the type parameter is known to be a reference type.)</span></span>

```antlr
default_value_expression
    : 'default' '(' type ')'
    ;
```

<span data-ttu-id="b0787-1465">Si le *type* d’une *default_value_expression* évalue au moment de l’exécution sur un type référence, le résultat est `null` converti en ce type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1465">If the *type* in a *default_value_expression* evaluates at run-time to a reference type, the result is `null` converted to that type.</span></span> <span data-ttu-id="b0787-1466">Si le *type* d’une *default_value_expression* évalue au moment de l’exécution sur un type valeur, le résultat est la valeur par défaut de *Value_type*([constructeurs par défaut](types.md#default-constructors)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1466">If the *type* in a *default_value_expression* evaluates at run-time to a value type, the result is the *value_type*'s default value ([Default constructors](types.md#default-constructors)).</span></span>

<span data-ttu-id="b0787-1467">Une *default_value_expression* est une expression constante ([expressions constantes](expressions.md#constant-expressions)) si le type est un type référence ou un paramètre de type qui est connu comme étant un type référence ([contraintes de paramètre de type](classes.md#type-parameter-constraints)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1467">A *default_value_expression* is a constant expression ([Constant expressions](expressions.md#constant-expressions)) if the type is a reference type or a type parameter that is known to be a reference type ([Type parameter constraints](classes.md#type-parameter-constraints)).</span></span> <span data-ttu-id="b0787-1468">En outre, une *default_value_expression* est une expression constante si le type est l’un des types de valeurs suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`ou n’importe quel type énumération.</span><span class="sxs-lookup"><span data-stu-id="b0787-1468">In addition, a *default_value_expression* is a constant expression if the type is one of the following value types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, or any enumeration type.</span></span>


### <a name="nameof-expressions"></a><span data-ttu-id="b0787-1469">Expressions Nameof</span><span class="sxs-lookup"><span data-stu-id="b0787-1469">Nameof expressions</span></span>

<span data-ttu-id="b0787-1470">Un *nameof_expression* est utilisé pour obtenir le nom d’une entité de programme sous la forme d’une chaîne constante.</span><span class="sxs-lookup"><span data-stu-id="b0787-1470">A *nameof_expression* is used to obtain the name of a program entity as a constant string.</span></span>

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

<span data-ttu-id="b0787-1471">Par programmation, l’opérande *named_entity* est toujours une expression.</span><span class="sxs-lookup"><span data-stu-id="b0787-1471">Grammatically speaking, the *named_entity* operand is always an expression.</span></span> <span data-ttu-id="b0787-1472">Étant donné que `nameof` n’est pas un mot clé réservé, une expression nameof est toujours ambiguë syntaxiquement avec un appel du nom simple `nameof`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1472">Because `nameof` is not a reserved keyword, a nameof expression is always syntactically ambiguous with an invocation of the simple name `nameof`.</span></span> <span data-ttu-id="b0787-1473">Pour des raisons de compatibilité, si une recherche de nom ([noms simples](expressions.md#simple-names)) du nom `nameof` réussie, l’expression est traitée comme un *invocation_expression* , que l’appel soit légal ou non.</span><span class="sxs-lookup"><span data-stu-id="b0787-1473">For compatibility reasons, if a name lookup ([Simple names](expressions.md#simple-names)) of the name `nameof` succeeds, the expression is treated as an *invocation_expression* -- regardless of whether the invocation is legal.</span></span> <span data-ttu-id="b0787-1474">Dans le cas contraire, il s’agit d’un *nameof_expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1474">Otherwise it is a *nameof_expression*.</span></span>

<span data-ttu-id="b0787-1475">La signification de la *named_entity* d’une *nameof_expression* est la signification de celle-ci en tant qu’expression ; autrement dit, qu’il s’agisse d’un *simple_name*, d’un *base_access* ou d’un *member_access*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1475">The meaning of the *named_entity* of a *nameof_expression* is the meaning of it as an expression; that is, either as a *simple_name*, a *base_access* or a *member_access*.</span></span> <span data-ttu-id="b0787-1476">Toutefois, lorsque la recherche décrite dans [noms simples](expressions.md#simple-names) et [accès aux membres](expressions.md#member-access) génère une erreur parce qu’un membre d’instance a été trouvé dans un contexte statique, une *nameof_expression* ne produit aucune erreur de ce type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1476">However, where the lookup described in [Simple names](expressions.md#simple-names) and [Member access](expressions.md#member-access) results in an error because an instance member was found in a static context, a *nameof_expression* produces no such error.</span></span>

<span data-ttu-id="b0787-1477">Il s’agit d’une erreur de compilation pour un *named_entity* désignant un groupe de méthodes pour avoir une *type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1477">It is a compile-time error for a *named_entity* designating a method group to have a *type_argument_list*.</span></span> <span data-ttu-id="b0787-1478">Il s’agit d’une erreur au moment de la compilation pour qu’un *named_entity_target* ait le type `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1478">It is a compile time error for a *named_entity_target* to have the type `dynamic`.</span></span>

<span data-ttu-id="b0787-1479">Une *nameof_expression* est une expression constante de type `string`et n’a aucun effet au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b0787-1479">A *nameof_expression* is a constant expression of type `string`, and has no effect at runtime.</span></span> <span data-ttu-id="b0787-1480">En particulier, sa *named_entity* n’est pas évaluée et est ignorée dans le cadre de l’analyse de l’assignation définie ([règles générales pour les expressions simples](variables.md#general-rules-for-simple-expressions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1480">Specifically, its *named_entity* is not evaluated, and is ignored for the purposes of definite assignment analysis ([General rules for simple expressions](variables.md#general-rules-for-simple-expressions)).</span></span> <span data-ttu-id="b0787-1481">Sa valeur est le dernier identificateur de la *named_entity* avant le *type_argument_list*final facultatif, transformée de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="b0787-1481">Its value is the last identifier of the *named_entity* before the optional final *type_argument_list*, transformed in the following way:</span></span>

* <span data-ttu-id="b0787-1482">Le préfixe «`@`», s’il est utilisé, est supprimé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1482">The prefix "`@`", if used, is removed.</span></span>
* <span data-ttu-id="b0787-1483">Chaque *unicode_escape_sequence* est transformée en son caractère Unicode correspondant.</span><span class="sxs-lookup"><span data-stu-id="b0787-1483">Each *unicode_escape_sequence* is transformed into its corresponding Unicode character.</span></span>
* <span data-ttu-id="b0787-1484">Toutes les *formatting_characters* sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="b0787-1484">Any *formatting_characters* are removed.</span></span>

<span data-ttu-id="b0787-1485">Ce sont les mêmes transformations appliquées dans les [identificateurs](lexical-structure.md#identifiers) lors du test d’égalité entre les identificateurs.</span><span class="sxs-lookup"><span data-stu-id="b0787-1485">These are the same transformations applied in [Identifiers](lexical-structure.md#identifiers) when testing equality between identifiers.</span></span>

<span data-ttu-id="b0787-1486">TODO : exemples</span><span class="sxs-lookup"><span data-stu-id="b0787-1486">TODO: examples</span></span>

### <a name="anonymous-method-expressions"></a><span data-ttu-id="b0787-1487">Expressions de méthode anonymes</span><span class="sxs-lookup"><span data-stu-id="b0787-1487">Anonymous method expressions</span></span>

<span data-ttu-id="b0787-1488">Une *anonymous_method_expression* est l’une des deux façons de définir une fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="b0787-1488">An *anonymous_method_expression* is one of two ways of defining an anonymous function.</span></span> <span data-ttu-id="b0787-1489">Celles-ci sont décrites plus en détail dans [expressions de fonction anonymes](expressions.md#anonymous-function-expressions).</span><span class="sxs-lookup"><span data-stu-id="b0787-1489">These are further described in [Anonymous function expressions](expressions.md#anonymous-function-expressions).</span></span>

## <a name="unary-operators"></a><span data-ttu-id="b0787-1490">Les opérateurs unaires.</span><span class="sxs-lookup"><span data-stu-id="b0787-1490">Unary operators</span></span>

<span data-ttu-id="b0787-1491">Les opérateurs `?`, `+`, `-`, `!`, `~`, `++`, `--`, Cast et `await` sont appelés opérateurs unaires.</span><span class="sxs-lookup"><span data-stu-id="b0787-1491">The `?`, `+`, `-`, `!`, `~`, `++`, `--`, cast, and `await` operators are called the unary operators.</span></span>

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

<span data-ttu-id="b0787-1492">Si l’opérande d’un *unary_expression* a le type au moment de la compilation `dynamic`, il est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1492">If the operand of a *unary_expression* has the compile-time type `dynamic`, it is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="b0787-1493">Dans ce cas, le type au moment de la compilation de l' *unary_expression* est `dynamic`et la résolution décrite ci-après aura lieu au moment de l’exécution à l’aide du type d’exécution de l’opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-1493">In this case the compile-time type of the *unary_expression* is `dynamic`, and the resolution described below will take place at run-time using the run-time type of the operand.</span></span>

### <a name="null-conditional-operator"></a><span data-ttu-id="b0787-1494">Opérateur conditionnel null</span><span class="sxs-lookup"><span data-stu-id="b0787-1494">Null-conditional operator</span></span>

<span data-ttu-id="b0787-1495">L’opérateur conditionnel null applique une liste d’opérations à son opérande uniquement si cet opérande n’a pas la valeur null.</span><span class="sxs-lookup"><span data-stu-id="b0787-1495">The null-conditional operator applies a list of operations to its operand only if that operand is non-null.</span></span> <span data-ttu-id="b0787-1496">Sinon, le résultat de l’application de l’opérateur est `null`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1496">Otherwise the result of applying the operator is `null`.</span></span>

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

<span data-ttu-id="b0787-1497">La liste des opérations peut inclure l’accès au membre et les opérations d’accès aux éléments (qui peuvent eux-mêmes être conditionnels null), ainsi que l’appel.</span><span class="sxs-lookup"><span data-stu-id="b0787-1497">The list of operations can include member access and element access operations (which may themselves be null-conditional), as well as invocation.</span></span>

<span data-ttu-id="b0787-1498">Par exemple, l’expression `a.b?[0]?.c()` est une *null_conditional_expression* avec une `a.b` *primary_expression* et *null_conditional_operations* `?[0]` (accès aux éléments conditionnels null), `?.c` (accès aux membres conditionnels null) et `()` (appel).</span><span class="sxs-lookup"><span data-stu-id="b0787-1498">For example, the expression `a.b?[0]?.c()` is a *null_conditional_expression* with a *primary_expression* `a.b` and *null_conditional_operations* `?[0]` (null-conditional element access), `?.c` (null-conditional member access) and `()` (invocation).</span></span>

<span data-ttu-id="b0787-1499">Pour une *null_conditional_expression* `E` avec un `P`*primary_expression* , laissez `E0` être l’expression obtenue en supprimant textuellement la `?` de début de chaque *null_conditional_operations de `E`* qui en a une.</span><span class="sxs-lookup"><span data-stu-id="b0787-1499">For a *null_conditional_expression* `E` with a *primary_expression* `P`, let `E0` be the expression obtained by textually removing the leading `?` from each of the *null_conditional_operations* of `E` that have one.</span></span> <span data-ttu-id="b0787-1500">D’un point de vue conceptuel, `E0` est l’expression qui sera évaluée si aucune des vérifications null représentées par le `?`s ne trouve de `null`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1500">Conceptually, `E0` is the expression that will be evaluated if none of the null checks represented by the `?`s do find a `null`.</span></span>

<span data-ttu-id="b0787-1501">Par ailleurs, laissez `E1` être l’expression obtenue en supprimant textuellement le `?` de début de la première des *null_conditional_operations* dans `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1501">Also, let `E1` be the expression obtained by textually removing the leading `?` from just the first of the *null_conditional_operations* in `E`.</span></span> <span data-ttu-id="b0787-1502">Cela peut aboutir à une *expression primaire* (s’il n’y en a qu’une seule `?`) ou à une autre *null_conditional_expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1502">This may lead to a *primary-expression* (if there was just one `?`) or to another *null_conditional_expression*.</span></span>

<span data-ttu-id="b0787-1503">Par exemple, si `E` est l’expression `a.b?[0]?.c()`, `E0` est l’expression `a.b[0].c()` et `E1` est l’expression `a.b[0]?.c()`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1503">For example, if `E` is the expression `a.b?[0]?.c()`, then `E0` is the expression `a.b[0].c()` and `E1` is the expression `a.b[0]?.c()`.</span></span>

<span data-ttu-id="b0787-1504">Si `E0` est classé comme Nothing, `E` est classé comme Nothing.</span><span class="sxs-lookup"><span data-stu-id="b0787-1504">If `E0` is classified as nothing, then `E` is classified as nothing.</span></span> <span data-ttu-id="b0787-1505">Dans le cas contraire, E est classé comme une valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1505">Otherwise E is classified as a value.</span></span>

<span data-ttu-id="b0787-1506">`E0` et `E1` sont utilisés pour déterminer la signification des `E`:</span><span class="sxs-lookup"><span data-stu-id="b0787-1506">`E0` and `E1` are used to determine the meaning of `E`:</span></span>

*  <span data-ttu-id="b0787-1507">Si `E` se produit en tant que *statement_expression* la signification de `E` est identique à celle de l’instruction</span><span class="sxs-lookup"><span data-stu-id="b0787-1507">If `E` occurs as a *statement_expression* the meaning of `E` is the same as the statement</span></span>

   ```csharp
   if ((object)P != null) E1;
   ```

   <span data-ttu-id="b0787-1508">sauf que P n’est évalué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-1508">except that P is evaluated only once.</span></span>

*  <span data-ttu-id="b0787-1509">Sinon, si `E0` est classé comme rien, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1509">Otherwise, if `E0` is classified as nothing a compile-time error occurs.</span></span>

*  <span data-ttu-id="b0787-1510">Dans le cas contraire, laissez `T0` le type de `E0`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1510">Otherwise, let `T0` be the type of `E0`.</span></span>

   *  <span data-ttu-id="b0787-1511">Si `T0` est un paramètre de type qui n’est pas connu comme étant un type référence ou un type valeur n’acceptant pas les valeurs NULL, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1511">If `T0` is a type parameter that is not known to be a reference type or a non-nullable value type, a compile-time error occurs.</span></span>

   *  <span data-ttu-id="b0787-1512">Si `T0` est un type valeur qui n’autorise pas les valeurs NULL, le type de `E` est `T0?`, et la signification de `E` est identique à</span><span class="sxs-lookup"><span data-stu-id="b0787-1512">If `T0` is a non-nullable value type, then the type of `E` is `T0?`, and the meaning of `E` is the same as</span></span>

      ```csharp
      ((object)P == null) ? (T0?)null : E1
      ```

      <span data-ttu-id="b0787-1513">Hormis le fait que `P` n’est évalué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-1513">except that `P` is evaluated only once.</span></span>

   *  <span data-ttu-id="b0787-1514">Dans le cas contraire, le type de E est T0, et la signification de E est la même que</span><span class="sxs-lookup"><span data-stu-id="b0787-1514">Otherwise the type of E is T0, and the meaning of E is the same as</span></span>

      ```csharp
      ((object)P == null) ? null : E1
      ```

      <span data-ttu-id="b0787-1515">Hormis le fait que `P` n’est évalué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-1515">except that `P` is evaluated only once.</span></span>

<span data-ttu-id="b0787-1516">Si `E1` est lui-même une *null_conditional_expression*, ces règles sont à nouveau appliquées, en imbriquant les tests pour `null` jusqu’à ce qu’il n’y ait plus de `?`, et l’expression a été réduite jusqu’au `E0`de l’expression primaire.</span><span class="sxs-lookup"><span data-stu-id="b0787-1516">If `E1` is itself a *null_conditional_expression*, then these rules are applied again, nesting the tests for `null` until there are no further `?`'s, and the expression has been reduced all the way down to the primary-expression `E0`.</span></span>

<span data-ttu-id="b0787-1517">Par exemple, si l’expression `a.b?[0]?.c()` se présente sous la forme d’une instruction-expression, comme dans l’instruction :</span><span class="sxs-lookup"><span data-stu-id="b0787-1517">For example, if the expression `a.b?[0]?.c()` occurs as a statement-expression, as in the statement:</span></span>
```csharp
a.b?[0]?.c();
```
<span data-ttu-id="b0787-1518">son sens est équivalent à :</span><span class="sxs-lookup"><span data-stu-id="b0787-1518">its meaning is equivalent to:</span></span>
```csharp
if (a.b != null) a.b[0]?.c();
```
<span data-ttu-id="b0787-1519">ce qui équivaut à :</span><span class="sxs-lookup"><span data-stu-id="b0787-1519">which again is equivalent to:</span></span>
```csharp
if (a.b != null) if (a.b[0] != null) a.b[0].c();
```
<span data-ttu-id="b0787-1520">À ceci près que les `a.b` et les `a.b[0]` ne sont évalués qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-1520">Except that `a.b` and `a.b[0]` are evaluated only once.</span></span>

<span data-ttu-id="b0787-1521">Si elle se produit dans un contexte où sa valeur est utilisée, comme dans :</span><span class="sxs-lookup"><span data-stu-id="b0787-1521">If it occurs in a context where its value is used, as in:</span></span>
```csharp
var x = a.b?[0]?.c();
```
<span data-ttu-id="b0787-1522">en partant du principe que le type de l’appel final n’est pas un type valeur non Nullable, son sens est équivalent à :</span><span class="sxs-lookup"><span data-stu-id="b0787-1522">and assuming that the type of the final invocation is not a non-nullable value type, its meaning is equivalent to:</span></span>
```csharp
var x = (a.b == null) ? null : (a.b[0] == null) ? null : a.b[0].c();
```
<span data-ttu-id="b0787-1523">à ceci près que les `a.b` et les `a.b[0]` ne sont évalués qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-1523">except that `a.b` and `a.b[0]` are evaluated only once.</span></span>

#### <a name="null-conditional-expressions-as-projection-initializers"></a><span data-ttu-id="b0787-1524">Expressions conditionnelles null en tant qu’initialiseurs de projection</span><span class="sxs-lookup"><span data-stu-id="b0787-1524">Null-conditional expressions as projection initializers</span></span>

<span data-ttu-id="b0787-1525">Une expression conditionnelle null est uniquement autorisée en tant que *member_declarator* dans un *anonymous_object_creation_expression* ([expressions de création d’objet anonyme](expressions.md#anonymous-object-creation-expressions)) si elle se termine par un accès au membre (éventuellement nullement conditionnel).</span><span class="sxs-lookup"><span data-stu-id="b0787-1525">A null-conditional expression is only allowed as a *member_declarator* in an *anonymous_object_creation_expression* ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) if it ends with an (optionally null-conditional) member access.</span></span> <span data-ttu-id="b0787-1526">Par programmation, cette exigence peut être exprimée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-1526">Grammatically, this requirement can be expressed as:</span></span>

```antlr
null_conditional_member_access
    : primary_expression null_conditional_operations? '?' '.' identifier type_argument_list?
    | primary_expression null_conditional_operations '.' identifier type_argument_list?
    ;
```

<span data-ttu-id="b0787-1527">Il s’agit d’un cas particulier de la grammaire pour *null_conditional_expression* ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="b0787-1527">This is a special case of the grammar for *null_conditional_expression* above.</span></span> <span data-ttu-id="b0787-1528">La production pour *member_declarator* dans les [expressions de création d’objet anonyme](expressions.md#anonymous-object-creation-expressions) comprend alors uniquement *null_conditional_member_access*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1528">The production for *member_declarator* in [Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions) then includes only *null_conditional_member_access*.</span></span>

#### <a name="null-conditional-expressions-as-statement-expressions"></a><span data-ttu-id="b0787-1529">Expressions conditionnelles null en tant qu’expressions d’instruction</span><span class="sxs-lookup"><span data-stu-id="b0787-1529">Null-conditional expressions as statement expressions</span></span>

<span data-ttu-id="b0787-1530">Une expression conditionnelle null est uniquement autorisée en tant que *statement_expression* ([instructions d’expression](statements.md#expression-statements)) si elle se termine par un appel.</span><span class="sxs-lookup"><span data-stu-id="b0787-1530">A null-conditional expression is only allowed as a *statement_expression* ([Expression statements](statements.md#expression-statements)) if it ends with an invocation.</span></span> <span data-ttu-id="b0787-1531">Par programmation, cette exigence peut être exprimée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-1531">Grammatically, this requirement can be expressed as:</span></span>

```antlr
null_conditional_invocation_expression
    : primary_expression null_conditional_operations '(' argument_list? ')'
    ;
```

<span data-ttu-id="b0787-1532">Il s’agit d’un cas particulier de la grammaire pour *null_conditional_expression* ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="b0787-1532">This is a special case of the grammar for *null_conditional_expression* above.</span></span> <span data-ttu-id="b0787-1533">La production de *statement_expression* dans les [instructions d’expression](statements.md#expression-statements) comprend alors uniquement *null_conditional_invocation_expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1533">The production for *statement_expression* in [Expression statements](statements.md#expression-statements) then includes only *null_conditional_invocation_expression*.</span></span>


### <a name="unary-plus-operator"></a><span data-ttu-id="b0787-1534">Opérateur plus unaire</span><span class="sxs-lookup"><span data-stu-id="b0787-1534">Unary plus operator</span></span>

<span data-ttu-id="b0787-1535">Pour une opération de la forme `+x`, la résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-1535">For an operation of the form `+x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-1536">L’opérande est converti en type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1536">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="b0787-1537">Les opérateurs plus unaires prédéfinis sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-1537">The predefined unary plus operators are:</span></span>

```csharp
int operator +(int x);
uint operator +(uint x);
long operator +(long x);
ulong operator +(ulong x);
float operator +(float x);
double operator +(double x);
decimal operator +(decimal x);
```

<span data-ttu-id="b0787-1538">Pour chacun de ces opérateurs, le résultat est simplement la valeur de l’opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-1538">For each of these operators, the result is simply the value of the operand.</span></span>

### <a name="unary-minus-operator"></a><span data-ttu-id="b0787-1539">Opération moins unaire</span><span class="sxs-lookup"><span data-stu-id="b0787-1539">Unary minus operator</span></span>

<span data-ttu-id="b0787-1540">Pour une opération de la forme `-x`, la résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-1540">For an operation of the form `-x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-1541">L’opérande est converti en type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1541">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="b0787-1542">Les opérateurs de négation prédéfinis sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-1542">The predefined negation operators are:</span></span>

*  <span data-ttu-id="b0787-1543">Négation d’entier :</span><span class="sxs-lookup"><span data-stu-id="b0787-1543">Integer negation:</span></span>

   ```csharp
   int operator -(int x);
   long operator -(long x);
   ```

   <span data-ttu-id="b0787-1544">Le résultat est calculé en soustrayant `x` de zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-1544">The result is computed by subtracting `x` from zero.</span></span> <span data-ttu-id="b0787-1545">Si la valeur de `x` est la plus petite valeur représentable du type d’opérande (-2 ^ 31 pour `int` ou-2 ^ 63 pour `long`), la négation mathématique de `x` n’est pas représentable dans le type d’opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-1545">If the value of `x` is the smallest representable value of the operand type (-2^31 for `int` or -2^63 for `long`), then the mathematical negation of `x` is not representable within the operand type.</span></span> <span data-ttu-id="b0787-1546">Si cela se produit dans un contexte de `checked`, un `System.OverflowException` est levé ; Si elle se produit dans un contexte de `unchecked`, le résultat est la valeur de l’opérande et le dépassement de capacité n’est pas signalé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1546">If this occurs within a `checked` context, a `System.OverflowException` is thrown; if it occurs within an `unchecked` context, the result is the value of the operand and the overflow is not reported.</span></span>

   <span data-ttu-id="b0787-1547">Si l’opérande de l’opérateur de négation est de type `uint`, il est converti en type `long`et le type du résultat est `long`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1547">If the operand of the negation operator is of type `uint`, it is converted to type `long`, and the type of the result is `long`.</span></span> <span data-ttu-id="b0787-1548">Une exception est la règle qui autorise l’écriture de la valeur de `int`-2147483648 (-2 ^ 31) sous la forme d’un littéral d’entier décimal ([littéraux entiers](lexical-structure.md#integer-literals)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1548">An exception is the rule that permits the `int` value -2147483648 (-2^31) to be written as a decimal integer literal ([Integer literals](lexical-structure.md#integer-literals)).</span></span>

   <span data-ttu-id="b0787-1549">Si l’opérande de l’opérateur de négation est de type `ulong`, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1549">If the operand of the negation operator is of type `ulong`, a compile-time error occurs.</span></span> <span data-ttu-id="b0787-1550">Une exception est la règle qui autorise l’écriture de la valeur de `long`-9223372036854775808 (-2 ^ 63) sous la forme d’un littéral d’entier décimal ([littéraux entiers](lexical-structure.md#integer-literals)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1550">An exception is the rule that permits the `long` value -9223372036854775808 (-2^63) to be written as a decimal integer literal ([Integer literals](lexical-structure.md#integer-literals)).</span></span>

*  <span data-ttu-id="b0787-1551">Négation à virgule flottante :</span><span class="sxs-lookup"><span data-stu-id="b0787-1551">Floating-point negation:</span></span>

   ```csharp
   float operator -(float x);
   double operator -(double x);
   ```

   <span data-ttu-id="b0787-1552">Le résultat est la valeur de `x` avec son signe inversé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1552">The result is the value of `x` with its sign inverted.</span></span> <span data-ttu-id="b0787-1553">Si `x` est NaN, le résultat est également NaN.</span><span class="sxs-lookup"><span data-stu-id="b0787-1553">If `x` is NaN, the result is also NaN.</span></span>

*  <span data-ttu-id="b0787-1554">Négation décimale :</span><span class="sxs-lookup"><span data-stu-id="b0787-1554">Decimal negation:</span></span>

   ```csharp
   decimal operator -(decimal x);
   ```

   <span data-ttu-id="b0787-1555">Le résultat est calculé en soustrayant `x` de zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-1555">The result is computed by subtracting `x` from zero.</span></span> <span data-ttu-id="b0787-1556">La négation décimale équivaut à utiliser l’opérateur moins unaire de type `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1556">Decimal negation is equivalent to using the unary minus operator of type `System.Decimal`.</span></span>

### <a name="logical-negation-operator"></a><span data-ttu-id="b0787-1557">Opérateur de négation logique</span><span class="sxs-lookup"><span data-stu-id="b0787-1557">Logical negation operator</span></span>

<span data-ttu-id="b0787-1558">Pour une opération de la forme `!x`, la résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-1558">For an operation of the form `!x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-1559">L’opérande est converti en type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1559">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="b0787-1560">Il n’existe qu’un seul opérateur de négation logique prédéfini :</span><span class="sxs-lookup"><span data-stu-id="b0787-1560">Only one predefined logical negation operator exists:</span></span>
```csharp
bool operator !(bool x);
```

<span data-ttu-id="b0787-1561">Cet opérateur calcule la négation logique de l’opérande : si l’opérande est `true`, le résultat est `false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1561">This operator computes the logical negation of the operand: If the operand is `true`, the result is `false`.</span></span> <span data-ttu-id="b0787-1562">Si l’opérande est `false`, le résultat est `true`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1562">If the operand is `false`, the result is `true`.</span></span>

### <a name="bitwise-complement-operator"></a><span data-ttu-id="b0787-1563">Opérateur de complément de bits</span><span class="sxs-lookup"><span data-stu-id="b0787-1563">Bitwise complement operator</span></span>

<span data-ttu-id="b0787-1564">Pour une opération de la forme `~x`, la résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-1564">For an operation of the form `~x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-1565">L’opérande est converti en type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1565">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="b0787-1566">Les opérateurs de complément de bits prédéfinis sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-1566">The predefined bitwise complement operators are:</span></span>
```csharp
int operator ~(int x);
uint operator ~(uint x);
long operator ~(long x);
ulong operator ~(ulong x);
```

<span data-ttu-id="b0787-1567">Pour chacun de ces opérateurs, le résultat de l’opération est le complément de bits de `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1567">For each of these operators, the result of the operation is the bitwise complement of `x`.</span></span>

<span data-ttu-id="b0787-1568">Chaque type d’énumération `E` fournit implicitement l’opérateur de complément de bits suivant :</span><span class="sxs-lookup"><span data-stu-id="b0787-1568">Every enumeration type `E` implicitly provides the following bitwise complement operator:</span></span>

```csharp
E operator ~(E x);
```

<span data-ttu-id="b0787-1569">Le résultat de l’évaluation de `~x`, où `x` est une expression d’un type d’énumération `E` avec un type sous-jacent `U`, est exactement le même que l’évaluation de `(E)(~(U)x)`, à ceci près que la conversion en `E` est toujours effectuée comme si dans un contexte `unchecked` ([opérateurs activés et désactivés](expressions.md#the-checked-and-unchecked-operators)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1569">The result of evaluating `~x`, where `x` is an expression of an enumeration type `E` with an underlying type `U`, is exactly the same as evaluating `(E)(~(U)x)`, except that the conversion to `E` is always performed as if in an `unchecked` context ([The checked and unchecked operators](expressions.md#the-checked-and-unchecked-operators)).</span></span>

### <a name="prefix-increment-and-decrement-operators"></a><span data-ttu-id="b0787-1570">Opérateurs préfixés d’incrémentation et de décrémentation</span><span class="sxs-lookup"><span data-stu-id="b0787-1570">Prefix increment and decrement operators</span></span>

```antlr
pre_increment_expression
    : '++' unary_expression
    ;

pre_decrement_expression
    : '--' unary_expression
    ;
```

<span data-ttu-id="b0787-1571">L’opérande d’une opération d’incrémentation ou de décrémentation de préfixe doit être une expression classifiée comme une variable, un accès à une propriété ou un accès à un indexeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1571">The operand of a prefix increment or decrement operation must be an expression classified as a variable, a property access, or an indexer access.</span></span> <span data-ttu-id="b0787-1572">Le résultat de l’opération est une valeur du même type que l’opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-1572">The result of the operation is a value of the same type as the operand.</span></span>

<span data-ttu-id="b0787-1573">Si l’opérande d’une opération de préfixe d’incrémentation ou de décrémentation est un accès à une propriété ou un indexeur, la propriété ou l’indexeur doit avoir un `get` et un accesseur `set`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1573">If the operand of a prefix increment or decrement operation is a property or indexer access, the property or indexer must have both a `get` and a `set` accessor.</span></span> <span data-ttu-id="b0787-1574">Si ce n’est pas le cas, une erreur de temps de liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1574">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="b0787-1575">La résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-1575">Unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-1576">Les opérateurs `++` et `--` prédéfinis existent pour les types suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`et tout type enum.</span><span class="sxs-lookup"><span data-stu-id="b0787-1576">Predefined `++` and `--` operators exist for the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, and any enum type.</span></span> <span data-ttu-id="b0787-1577">Les opérateurs de `++` prédéfinis retournent la valeur produite par l’ajout de 1 à l’opérande, et les opérateurs de `--` prédéfinis retournent la valeur produite par la soustraction de 1 de l’opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-1577">The predefined `++` operators return the value produced by adding 1 to the operand, and the predefined `--` operators return the value produced by subtracting 1 from the operand.</span></span> <span data-ttu-id="b0787-1578">Dans un contexte de `checked`, si le résultat de l’addition ou de la soustraction est en dehors de la plage du type de résultat et que le type de résultat est un type intégral ou un type enum, une `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1578">In a `checked` context, if the result of this addition or subtraction is outside the range of the result type and the result type is an integral type or enum type, a `System.OverflowException` is thrown.</span></span>

<span data-ttu-id="b0787-1579">Le traitement au moment de l’exécution d’une opération de préfixe d’incrémentation ou de décrémentation de la forme `++x` ou `--x` se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-1579">The run-time processing of a prefix increment or decrement operation of the form `++x` or `--x` consists of the following steps:</span></span>

*   <span data-ttu-id="b0787-1580">Si `x` est classée en tant que variable :</span><span class="sxs-lookup"><span data-stu-id="b0787-1580">If `x` is classified as a variable:</span></span>
    * <span data-ttu-id="b0787-1581">`x` est évaluée pour produire la variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-1581">`x` is evaluated to produce the variable.</span></span>
    * <span data-ttu-id="b0787-1582">L’opérateur sélectionné est appelé avec la valeur de `x` comme argument.</span><span class="sxs-lookup"><span data-stu-id="b0787-1582">The selected operator is invoked with the value of `x` as its argument.</span></span>
    * <span data-ttu-id="b0787-1583">La valeur retournée par l’opérateur est stockée dans l’emplacement donné par l’évaluation de `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1583">The value returned by the operator is stored in the location given by the evaluation of `x`.</span></span>
    * <span data-ttu-id="b0787-1584">La valeur retournée par l’opérateur devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-1584">The value returned by the operator becomes the result of the operation.</span></span>
*   <span data-ttu-id="b0787-1585">Si `x` est classée comme un accès à une propriété ou un indexeur :</span><span class="sxs-lookup"><span data-stu-id="b0787-1585">If `x` is classified as a property or indexer access:</span></span>
    * <span data-ttu-id="b0787-1586">L’expression d’instance (si `x` n’est pas `static`) et la liste d’arguments (si `x` est un accès à un indexeur) associée à `x` sont évaluées, et les résultats sont utilisés dans les appels de `get` et d’accesseur `set` suivants.</span><span class="sxs-lookup"><span data-stu-id="b0787-1586">The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `get` and `set` accessor invocations.</span></span>
    * <span data-ttu-id="b0787-1587">L’accesseur `get` de `x` est appelé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1587">The `get` accessor of `x` is invoked.</span></span>
    * <span data-ttu-id="b0787-1588">L’opérateur sélectionné est appelé avec la valeur retournée par l’accesseur `get` comme argument.</span><span class="sxs-lookup"><span data-stu-id="b0787-1588">The selected operator is invoked with the value returned by the `get` accessor as its argument.</span></span>
    * <span data-ttu-id="b0787-1589">L’accesseur `set` de `x` est appelé avec la valeur retournée par l’opérateur comme son argument `value`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1589">The `set` accessor of `x` is invoked with the value returned by the operator as its `value` argument.</span></span>
    * <span data-ttu-id="b0787-1590">La valeur retournée par l’opérateur devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-1590">The value returned by the operator becomes the result of the operation.</span></span>

<span data-ttu-id="b0787-1591">Les opérateurs `++` et `--` prennent également en charge la notation suffixée ([opérateurs d’incrémentation et de décrémentation suffixés](expressions.md#postfix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1591">The `++` and `--` operators also support postfix notation ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators)).</span></span> <span data-ttu-id="b0787-1592">En général, le résultat de `x++` ou `x--` est la valeur de `x` avant l’opération, alors que le résultat de `++x` ou `--x` est la valeur de `x` après l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-1592">Typically, the result of `x++` or `x--` is the value of `x` before the operation, whereas the result of `++x` or `--x` is the value of `x` after the operation.</span></span> <span data-ttu-id="b0787-1593">Dans les deux cas, `x` lui-même a la même valeur après l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-1593">In either case, `x` itself has the same value after the operation.</span></span>

<span data-ttu-id="b0787-1594">Une implémentation de `operator++` ou `operator--` peut être appelée à l’aide de la notation suffixée ou de préfixe.</span><span class="sxs-lookup"><span data-stu-id="b0787-1594">An `operator++` or `operator--` implementation can be invoked using either postfix or prefix notation.</span></span> <span data-ttu-id="b0787-1595">Il n’est pas possible d’avoir des implémentations d’opérateur distinctes pour les deux notations.</span><span class="sxs-lookup"><span data-stu-id="b0787-1595">It is not possible to have separate operator implementations for the two notations.</span></span>

### <a name="cast-expressions"></a><span data-ttu-id="b0787-1596">Expressions de cast</span><span class="sxs-lookup"><span data-stu-id="b0787-1596">Cast expressions</span></span>

<span data-ttu-id="b0787-1597">Un *cast_expression* est utilisé pour convertir explicitement une expression en un type donné.</span><span class="sxs-lookup"><span data-stu-id="b0787-1597">A *cast_expression* is used to explicitly convert an expression to a given type.</span></span>

```antlr
cast_expression
    : '(' type ')' unary_expression
    ;
```

<span data-ttu-id="b0787-1598">*Cast_expression* de la forme `(T)E`, où `T` est un *type* et `E` est un *unary_expression*, effectue une conversion explicite ([conversions explicites](conversions.md#explicit-conversions)) de la valeur de `E` en type `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1598">A *cast_expression* of the form `(T)E`, where `T` is a *type* and `E` is a *unary_expression*, performs an explicit conversion ([Explicit conversions](conversions.md#explicit-conversions)) of the value of `E` to type `T`.</span></span> <span data-ttu-id="b0787-1599">S’il n’existe aucune conversion explicite de `E` à `T`, une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1599">If no explicit conversion exists from `E` to `T`, a binding-time error occurs.</span></span> <span data-ttu-id="b0787-1600">Dans le cas contraire, le résultat est la valeur produite par la conversion explicite.</span><span class="sxs-lookup"><span data-stu-id="b0787-1600">Otherwise, the result is the value produced by the explicit conversion.</span></span> <span data-ttu-id="b0787-1601">Le résultat est toujours classifié comme une valeur, même si `E` désigne une variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-1601">The result is always classified as a value, even if `E` denotes a variable.</span></span>

<span data-ttu-id="b0787-1602">La grammaire d’un *cast_expression* provoque certaines ambiguïtés syntaxiques.</span><span class="sxs-lookup"><span data-stu-id="b0787-1602">The grammar for a *cast_expression* leads to certain syntactic ambiguities.</span></span> <span data-ttu-id="b0787-1603">Par exemple, l’expression `(x)-y` peut être interprétée comme un *cast_expression* (un cast de `-y` en type `x`) ou en tant que *additive_expression* combiné avec un *parenthesized_expression* (qui calcule la valeur `x - y)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1603">For example, the expression `(x)-y` could either be interpreted as a *cast_expression* (a cast of `-y` to type `x`) or as an *additive_expression* combined with a *parenthesized_expression* (which computes the value `x - y)`.</span></span>

<span data-ttu-id="b0787-1604">Pour résoudre *cast_expression* ambiguïtés, la règle suivante existe : une séquence d’un ou plusieurs *jetons*([espace blanc](lexical-structure.md#white-space)) entre parenthèses est considérée comme le début d’un *cast_expression* uniquement si au moins l’une des conditions suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="b0787-1604">To resolve *cast_expression* ambiguities, the following rule exists: A sequence of one or more *token*s ([White space](lexical-structure.md#white-space)) enclosed in parentheses is considered the start of a *cast_expression* only if at least one of the following are true:</span></span>

*  <span data-ttu-id="b0787-1605">La séquence de jetons est une grammaire correcte pour un *type*, mais pas pour une *expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1605">The sequence of tokens is correct grammar for a *type*, but not for an *expression*.</span></span>
*  <span data-ttu-id="b0787-1606">La séquence de jetons est une grammaire correcte pour un *type*, et le jeton qui suit immédiatement les parenthèses fermantes est le jeton «`~`», le jeton «`!`», le jeton «`(`», un *identificateur* ([séquences d’échappement de caractères Unicode](lexical-structure.md#unicode-character-escape-sequences)), un *littéral* ([littéraux](lexical-structure.md#literals)) ou tout *mot clé* ([Mots clés](lexical-structure.md#keywords)) sauf `as` et `is`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1606">The sequence of tokens is correct grammar for a *type*, and the token immediately following the closing parentheses is the token "`~`", the token "`!`", the token "`(`", an *identifier* ([Unicode character escape sequences](lexical-structure.md#unicode-character-escape-sequences)), a *literal* ([Literals](lexical-structure.md#literals)), or any *keyword* ([Keywords](lexical-structure.md#keywords)) except `as` and `is`.</span></span>

<span data-ttu-id="b0787-1607">Le terme « grammaire correcte » ci-dessus signifie uniquement que la séquence de jetons doit se conformer à la production grammaticale particulière.</span><span class="sxs-lookup"><span data-stu-id="b0787-1607">The term "correct grammar" above means only that the sequence of tokens must conform to the particular grammatical production.</span></span> <span data-ttu-id="b0787-1608">Elle ne tient pas compte de la signification réelle des identificateurs constitutifs.</span><span class="sxs-lookup"><span data-stu-id="b0787-1608">It specifically does not consider the actual meaning of any constituent identifiers.</span></span> <span data-ttu-id="b0787-1609">Par exemple, si `x` et `y` sont des identificateurs, `x.y` est une grammaire correcte pour un type, même si `x.y` ne désigne pas un type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1609">For example, if `x` and `y` are identifiers, then `x.y` is correct grammar for a type, even if `x.y` doesn't actually denote a type.</span></span>

<span data-ttu-id="b0787-1610">À partir de la règle de désambiguïsation, il s’agit de savoir que, si `x` et `y` sont des identificateurs, `(x)y`, `(x)(y)`et `(x)(-y)` sont *cast_expression*s, mais `(x)-y` ne l’est pas, même si `x` identifie un type.</span><span class="sxs-lookup"><span data-stu-id="b0787-1610">From the disambiguation rule it follows that, if `x` and `y` are identifiers, `(x)y`, `(x)(y)`, and `(x)(-y)` are *cast_expression*s, but `(x)-y` is not, even if `x` identifies a type.</span></span> <span data-ttu-id="b0787-1611">Toutefois, si `x` est un mot clé qui identifie un type prédéfini (tel que `int`), les quatre formes sont *cast_expression*s (car ce mot clé n’est peut-être pas une expression par lui-même).</span><span class="sxs-lookup"><span data-stu-id="b0787-1611">However, if `x` is a keyword that identifies a predefined type (such as `int`), then all four forms are *cast_expression*s (because such a keyword could not possibly be an expression by itself).</span></span>

### <a name="await-expressions"></a><span data-ttu-id="b0787-1612">Expressions await</span><span class="sxs-lookup"><span data-stu-id="b0787-1612">Await expressions</span></span>

<span data-ttu-id="b0787-1613">L’opérateur await est utilisé pour interrompre l’évaluation de la fonction Async englobante jusqu’à ce que l’opération asynchrone représentée par l’opérande soit terminée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1613">The await operator is used to suspend evaluation of the enclosing async function until the asynchronous operation represented by the operand has completed.</span></span>

```antlr
await_expression
    : 'await' unary_expression
    ;
```

<span data-ttu-id="b0787-1614">Une *await_expression* est uniquement autorisée dans le corps d’une fonction Async ([itérateurs](classes.md#iterators)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1614">An *await_expression* is only allowed in the body of an async function ([Iterators](classes.md#iterators)).</span></span> <span data-ttu-id="b0787-1615">Dans la fonction Async englobante la plus proche, un *await_expression* peut ne pas se produire dans les emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-1615">Within the nearest enclosing async function, an *await_expression* may not occur in these places:</span></span>

*  <span data-ttu-id="b0787-1616">Dans une fonction anonyme imbriquée (non asynchrone)</span><span class="sxs-lookup"><span data-stu-id="b0787-1616">Inside a nested (non-async) anonymous function</span></span>
*  <span data-ttu-id="b0787-1617">À l’intérieur du bloc d’un *lock_statement*</span><span class="sxs-lookup"><span data-stu-id="b0787-1617">Inside the block of a *lock_statement*</span></span>
*  <span data-ttu-id="b0787-1618">Dans un contexte non sécurisé</span><span class="sxs-lookup"><span data-stu-id="b0787-1618">In an unsafe context</span></span>

<span data-ttu-id="b0787-1619">Notez qu’un *await_expression* ne peut pas se produire dans la plupart des cas au sein d’un *query_expression*, car ceux-ci sont convertis de façon syntaxique pour utiliser des expressions lambda non asynchrones.</span><span class="sxs-lookup"><span data-stu-id="b0787-1619">Note that an *await_expression* cannot occur in most places within a *query_expression*, because those are syntactically transformed to use non-async lambda expressions.</span></span>

<span data-ttu-id="b0787-1620">À l’intérieur d’une fonction Async, `await` ne peut pas être utilisé en tant qu’identificateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1620">Inside of an async function, `await` cannot be used as an identifier.</span></span> <span data-ttu-id="b0787-1621">Il n’y a donc pas d’ambiguïté syntaxique entre await-expressions et diverses expressions impliquant des identificateurs.</span><span class="sxs-lookup"><span data-stu-id="b0787-1621">There is therefore no syntactic ambiguity between await-expressions and various expressions involving identifiers.</span></span> <span data-ttu-id="b0787-1622">En dehors des fonctions Async, `await` agit comme un identificateur normal.</span><span class="sxs-lookup"><span data-stu-id="b0787-1622">Outside of async functions, `await` acts as a normal identifier.</span></span>

<span data-ttu-id="b0787-1623">L’opérande d’un *await_expression* est appelé la ***tâche***.</span><span class="sxs-lookup"><span data-stu-id="b0787-1623">The operand of an *await_expression* is called the ***task***.</span></span> <span data-ttu-id="b0787-1624">Il représente une opération asynchrone qui peut ou non se terminer au moment où le *await_expression* est évalué.</span><span class="sxs-lookup"><span data-stu-id="b0787-1624">It represents an asynchronous operation that may or may not be complete at the time the *await_expression* is evaluated.</span></span> <span data-ttu-id="b0787-1625">L’objectif de l’opérateur await est de suspendre l’exécution de la fonction Async englobante jusqu’à ce que la tâche attendue soit terminée, puis d’obtenir son résultat.</span><span class="sxs-lookup"><span data-stu-id="b0787-1625">The purpose of the await operator is to suspend execution of the enclosing async function until the awaited task is complete, and then obtain its outcome.</span></span>

#### <a name="awaitable-expressions"></a><span data-ttu-id="b0787-1626">Expressions await</span><span class="sxs-lookup"><span data-stu-id="b0787-1626">Awaitable expressions</span></span>

<span data-ttu-id="b0787-1627">La tâche d’une expression await doit être ***attendue***.</span><span class="sxs-lookup"><span data-stu-id="b0787-1627">The task of an await expression is required to be ***awaitable***.</span></span> <span data-ttu-id="b0787-1628">Une expression `t` est await si l’un des éléments suivants est attendu :</span><span class="sxs-lookup"><span data-stu-id="b0787-1628">An expression `t` is awaitable if one of the following holds:</span></span>

*  <span data-ttu-id="b0787-1629">`t` est de type au moment de la compilation `dynamic`</span><span class="sxs-lookup"><span data-stu-id="b0787-1629">`t` is of compile time type `dynamic`</span></span>
*  <span data-ttu-id="b0787-1630">`t` a une instance accessible ou une méthode d’extension appelée `GetAwaiter` sans paramètres ni aucun paramètre de type, et un type de retour `A` pour lequel tous les éléments suivants sont détenus :</span><span class="sxs-lookup"><span data-stu-id="b0787-1630">`t` has an accessible instance or extension method called `GetAwaiter` with no parameters and no type parameters, and a return type `A` for which all of the following hold:</span></span>
   * <span data-ttu-id="b0787-1631">`A` implémente l’interface `System.Runtime.CompilerServices.INotifyCompletion` (ci-après dénommées `INotifyCompletion` par souci de concision)</span><span class="sxs-lookup"><span data-stu-id="b0787-1631">`A` implements the interface `System.Runtime.CompilerServices.INotifyCompletion` (hereafter known as `INotifyCompletion` for brevity)</span></span>
   * <span data-ttu-id="b0787-1632">`A` a une propriété d’instance accessible accessible en lecture `IsCompleted` de type `bool`</span><span class="sxs-lookup"><span data-stu-id="b0787-1632">`A` has an accessible, readable instance property `IsCompleted` of type `bool`</span></span>
   * <span data-ttu-id="b0787-1633">`A` a une méthode d’instance accessible `GetResult` sans paramètres et sans paramètres de type</span><span class="sxs-lookup"><span data-stu-id="b0787-1633">`A` has an accessible instance method `GetResult` with no parameters and no type parameters</span></span>

<span data-ttu-id="b0787-1634">L’objectif de la méthode `GetAwaiter` est d’obtenir un ***await*** pour la tâche.</span><span class="sxs-lookup"><span data-stu-id="b0787-1634">The purpose of the `GetAwaiter` method is to obtain an ***awaiter*** for the task.</span></span> <span data-ttu-id="b0787-1635">Le type `A` est appelé le ***type d’attente*** pour l’expression await.</span><span class="sxs-lookup"><span data-stu-id="b0787-1635">The type `A` is called the ***awaiter type*** for the await expression.</span></span>

<span data-ttu-id="b0787-1636">L’objectif de la propriété `IsCompleted` est de déterminer si la tâche est déjà terminée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1636">The purpose of the `IsCompleted` property is to determine if the task is already complete.</span></span> <span data-ttu-id="b0787-1637">Dans ce cas, il n’est pas nécessaire d’interrompre l’évaluation.</span><span class="sxs-lookup"><span data-stu-id="b0787-1637">If so, there is no need to suspend evaluation.</span></span>

<span data-ttu-id="b0787-1638">L’objectif de la méthode `INotifyCompletion.OnCompleted` consiste à inscrire une « continuation » à la tâche ; c’est-à-dire un délégué (de type `System.Action`) qui sera appelé une fois la tâche terminée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1638">The purpose of the `INotifyCompletion.OnCompleted` method is to sign up a "continuation" to the task; i.e. a delegate (of type `System.Action`) that will be invoked once the task is complete.</span></span>

<span data-ttu-id="b0787-1639">L’objectif de la méthode `GetResult` est d’obtenir le résultat de la tâche une fois qu’elle est terminée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1639">The purpose of the `GetResult` method is to obtain the outcome of the task once it is complete.</span></span> <span data-ttu-id="b0787-1640">Ce résultat peut être terminé avec succès, éventuellement avec une valeur de résultat, ou il peut s’agir d’une exception levée par la méthode `GetResult`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1640">This outcome may be successful completion, possibly with a result value, or it may be an exception which is thrown by the `GetResult` method.</span></span>

#### <a name="classification-of-await-expressions"></a><span data-ttu-id="b0787-1641">Classification des expressions await</span><span class="sxs-lookup"><span data-stu-id="b0787-1641">Classification of await expressions</span></span>

<span data-ttu-id="b0787-1642">L’expression `await t` est classée de la même façon que l’expression `(t).GetAwaiter().GetResult()`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1642">The expression `await t` is classified the same way as the expression `(t).GetAwaiter().GetResult()`.</span></span> <span data-ttu-id="b0787-1643">Ainsi, si le type de retour de `GetResult` est `void`, le *await_expression* est classé comme Nothing.</span><span class="sxs-lookup"><span data-stu-id="b0787-1643">Thus, if the return type of `GetResult` is `void`, the *await_expression* is classified as nothing.</span></span> <span data-ttu-id="b0787-1644">S’il a un type de retour non void `T`, le *await_expression* est classé comme une valeur de type `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1644">If it has a non-void return type `T`, the *await_expression* is classified as a value of type `T`.</span></span>

#### <a name="runtime-evaluation-of-await-expressions"></a><span data-ttu-id="b0787-1645">Évaluation du runtime des expressions await</span><span class="sxs-lookup"><span data-stu-id="b0787-1645">Runtime evaluation of await expressions</span></span>

<span data-ttu-id="b0787-1646">Au moment de l’exécution, l’expression `await t` est évaluée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-1646">At runtime, the expression `await t` is evaluated as follows:</span></span>

*  <span data-ttu-id="b0787-1647">Un `a` await est obtenu en évaluant l’expression `(t).GetAwaiter()`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1647">An awaiter `a` is obtained by evaluating the expression `(t).GetAwaiter()`.</span></span>
*  <span data-ttu-id="b0787-1648">Une `b` `bool` est obtenue en évaluant l’expression `(a).IsCompleted`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1648">A `bool` `b` is obtained by evaluating the expression `(a).IsCompleted`.</span></span>
*  <span data-ttu-id="b0787-1649">Si `b` est `false`, l’évaluation varie selon que `a` implémente les `System.Runtime.CompilerServices.ICriticalNotifyCompletion` d’interface (ci-après appelées `ICriticalNotifyCompletion` par souci de concision).</span><span class="sxs-lookup"><span data-stu-id="b0787-1649">If `b` is `false` then evaluation depends on whether `a` implements the interface `System.Runtime.CompilerServices.ICriticalNotifyCompletion` (hereafter known as `ICriticalNotifyCompletion` for brevity).</span></span> <span data-ttu-id="b0787-1650">Cette vérification s’effectue au moment de la liaison. par exemple, lors de l’exécution, si `a` a le type au moment de la compilation `dynamic`, et au moment de la compilation, sinon.</span><span class="sxs-lookup"><span data-stu-id="b0787-1650">This check is done at binding time; i.e. at runtime if `a` has the compile time type `dynamic`, and at compile time otherwise.</span></span> <span data-ttu-id="b0787-1651">Laissez `r` désigner le délégué de reprise ([itérateurs](classes.md#iterators)) :</span><span class="sxs-lookup"><span data-stu-id="b0787-1651">Let `r` denote the resumption delegate ([Iterators](classes.md#iterators)):</span></span>
    * <span data-ttu-id="b0787-1652">Si `a` n’implémente pas `ICriticalNotifyCompletion`, l’expression `(a as (INotifyCompletion)).OnCompleted(r)` est évaluée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1652">If `a` does not implement `ICriticalNotifyCompletion`, then the expression `(a as (INotifyCompletion)).OnCompleted(r)` is evaluated.</span></span>
    * <span data-ttu-id="b0787-1653">Si `a` implémente `ICriticalNotifyCompletion`, l’expression `(a as (ICriticalNotifyCompletion)).UnsafeOnCompleted(r)` est évaluée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1653">If `a` does implement `ICriticalNotifyCompletion`, then the expression `(a as (ICriticalNotifyCompletion)).UnsafeOnCompleted(r)` is evaluated.</span></span>
    * <span data-ttu-id="b0787-1654">L’évaluation est ensuite interrompue et le contrôle est retourné à l’appelant actuel de la fonction Async.</span><span class="sxs-lookup"><span data-stu-id="b0787-1654">Evaluation is then suspended, and control is returned to the current caller of the async function.</span></span>
*  <span data-ttu-id="b0787-1655">Soit immédiatement après (si `b` a été `true`), soit lors de l’appel ultérieur du délégué de reprise (si `b` était `false`), l’expression `(a).GetResult()` est évaluée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1655">Either immediately after (if `b` was `true`), or upon later invocation of the resumption delegate (if `b` was `false`), the expression `(a).GetResult()` is evaluated.</span></span> <span data-ttu-id="b0787-1656">Si elle retourne une valeur, cette valeur est le résultat de l' *await_expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-1656">If it returns a value, that value is the result of the *await_expression*.</span></span> <span data-ttu-id="b0787-1657">Sinon, le résultat est Nothing.</span><span class="sxs-lookup"><span data-stu-id="b0787-1657">Otherwise the result is nothing.</span></span>

<span data-ttu-id="b0787-1658">L’implémentation de l’interface await des méthodes d’interface `INotifyCompletion.OnCompleted` et `ICriticalNotifyCompletion.UnsafeOnCompleted` doit entraîner l’appel de la `r` du délégué au plus une fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-1658">An awaiter's implementation of the interface methods `INotifyCompletion.OnCompleted` and `ICriticalNotifyCompletion.UnsafeOnCompleted` should cause the delegate `r` to be invoked at most once.</span></span> <span data-ttu-id="b0787-1659">Dans le cas contraire, le comportement de la fonction Async englobante n’est pas défini.</span><span class="sxs-lookup"><span data-stu-id="b0787-1659">Otherwise, the behavior of the enclosing async function is undefined.</span></span>

## <a name="arithmetic-operators"></a><span data-ttu-id="b0787-1660">Opérateurs arithmétiques</span><span class="sxs-lookup"><span data-stu-id="b0787-1660">Arithmetic operators</span></span>

<span data-ttu-id="b0787-1661">Les opérateurs `*`, `/`, `%`, `+`et `-` sont appelés opérateurs arithmétiques.</span><span class="sxs-lookup"><span data-stu-id="b0787-1661">The `*`, `/`, `%`, `+`, and `-` operators are called the arithmetic operators.</span></span>

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

<span data-ttu-id="b0787-1662">Si un opérande d’un opérateur arithmétique a le type au moment de la compilation `dynamic`, l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="b0787-1662">If an operand of an arithmetic operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="b0787-1663">Dans ce cas, le type au moment de la compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous a lieu au moment de l’exécution à l’aide du type d’exécution des opérandes qui ont le type au moment de la compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1663">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

### <a name="multiplication-operator"></a><span data-ttu-id="b0787-1664">Opérateur de multiplication</span><span class="sxs-lookup"><span data-stu-id="b0787-1664">Multiplication operator</span></span>

<span data-ttu-id="b0787-1665">Pour une opération de la forme `x * y`, la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-1665">For an operation of the form `x * y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-1666">Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1666">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="b0787-1667">Les opérateurs de multiplication prédéfinis sont répertoriés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b0787-1667">The predefined multiplication operators are listed below.</span></span> <span data-ttu-id="b0787-1668">Les opérateurs calculent tous le produit de `x` et `y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1668">The operators all compute the product of `x` and `y`.</span></span>

*  <span data-ttu-id="b0787-1669">Multiplication d’entiers :</span><span class="sxs-lookup"><span data-stu-id="b0787-1669">Integer multiplication:</span></span>

   ```csharp
   int operator *(int x, int y);
   uint operator *(uint x, uint y);
   long operator *(long x, long y);
   ulong operator *(ulong x, ulong y);
   ```

   <span data-ttu-id="b0787-1670">Dans un contexte de `checked`, si le produit est en dehors de la plage du type de résultat, un `System.OverflowException` est levé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1670">In a `checked` context, if the product is outside the range of the result type, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="b0787-1671">Dans un contexte de `unchecked`, les dépassements de capacité ne sont pas signalés et les bits de poids fort significatifs en dehors de la plage du type de résultat sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="b0787-1671">In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.</span></span>


*  <span data-ttu-id="b0787-1672">Multiplication à virgule flottante :</span><span class="sxs-lookup"><span data-stu-id="b0787-1672">Floating-point multiplication:</span></span>

   ```csharp
   float operator *(float x, float y);
   double operator *(double x, double y);
   ```

   <span data-ttu-id="b0787-1673">Le produit est calculé en fonction des règles de l’arithmétique IEEE 754.</span><span class="sxs-lookup"><span data-stu-id="b0787-1673">The product is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="b0787-1674">Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies différentes de zéro, de zéros, d’infinis et de NaN.</span><span class="sxs-lookup"><span data-stu-id="b0787-1674">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="b0787-1675">Dans le tableau, `x` et `y` sont des valeurs finies positives.</span><span class="sxs-lookup"><span data-stu-id="b0787-1675">In the table, `x` and `y` are positive finite values.</span></span> <span data-ttu-id="b0787-1676">`z` est le résultat de `x * y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1676">`z` is the result of `x * y`.</span></span> <span data-ttu-id="b0787-1677">Si le résultat est trop grand pour le type de destination, `z` est l’infini.</span><span class="sxs-lookup"><span data-stu-id="b0787-1677">If the result is too large for the destination type, `z` is infinity.</span></span> <span data-ttu-id="b0787-1678">Si le résultat est trop petit pour le type de destination, `z` est égal à zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-1678">If the result is too small for the destination type, `z` is zero.</span></span>

   |      |      |      |     |     |      |      |     |
   |:----:|-----:|:----:|:---:|:---:|:----:|:----:|:----|
   |      | <span data-ttu-id="b0787-1679">+y</span><span class="sxs-lookup"><span data-stu-id="b0787-1679">+y</span></span>   | <span data-ttu-id="b0787-1680">-y</span><span class="sxs-lookup"><span data-stu-id="b0787-1680">-y</span></span>   | <span data-ttu-id="b0787-1681">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1681">+0</span></span>  | <span data-ttu-id="b0787-1682">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1682">-0</span></span>  | <span data-ttu-id="b0787-1683">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1683">+inf</span></span> | <span data-ttu-id="b0787-1684">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1684">-inf</span></span> | <span data-ttu-id="b0787-1685">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1685">NaN</span></span> | 
   | <span data-ttu-id="b0787-1686">+x</span><span class="sxs-lookup"><span data-stu-id="b0787-1686">+x</span></span>   | <span data-ttu-id="b0787-1687">+z</span><span class="sxs-lookup"><span data-stu-id="b0787-1687">+z</span></span>   | <span data-ttu-id="b0787-1688">-z</span><span class="sxs-lookup"><span data-stu-id="b0787-1688">-z</span></span>   | <span data-ttu-id="b0787-1689">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1689">+0</span></span>  | <span data-ttu-id="b0787-1690">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1690">-0</span></span>  | <span data-ttu-id="b0787-1691">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1691">+inf</span></span> | <span data-ttu-id="b0787-1692">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1692">-inf</span></span> | <span data-ttu-id="b0787-1693">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1693">NaN</span></span> | 
   | <span data-ttu-id="b0787-1694">-x</span><span class="sxs-lookup"><span data-stu-id="b0787-1694">-x</span></span>   | <span data-ttu-id="b0787-1695">-z</span><span class="sxs-lookup"><span data-stu-id="b0787-1695">-z</span></span>   | <span data-ttu-id="b0787-1696">+z</span><span class="sxs-lookup"><span data-stu-id="b0787-1696">+z</span></span>   | <span data-ttu-id="b0787-1697">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1697">-0</span></span>  | <span data-ttu-id="b0787-1698">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1698">+0</span></span>  | <span data-ttu-id="b0787-1699">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1699">-inf</span></span> | <span data-ttu-id="b0787-1700">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1700">+inf</span></span> | <span data-ttu-id="b0787-1701">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1701">NaN</span></span> | 
   | <span data-ttu-id="b0787-1702">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1702">+0</span></span>   | <span data-ttu-id="b0787-1703">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1703">+0</span></span>   | <span data-ttu-id="b0787-1704">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1704">-0</span></span>   | <span data-ttu-id="b0787-1705">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1705">+0</span></span>  | <span data-ttu-id="b0787-1706">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1706">-0</span></span>  | <span data-ttu-id="b0787-1707">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1707">NaN</span></span>  | <span data-ttu-id="b0787-1708">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1708">NaN</span></span>  | <span data-ttu-id="b0787-1709">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1709">NaN</span></span> | 
   | <span data-ttu-id="b0787-1710">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1710">-0</span></span>   | <span data-ttu-id="b0787-1711">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1711">-0</span></span>   | <span data-ttu-id="b0787-1712">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1712">+0</span></span>   | <span data-ttu-id="b0787-1713">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1713">-0</span></span>  | <span data-ttu-id="b0787-1714">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1714">+0</span></span>  | <span data-ttu-id="b0787-1715">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1715">NaN</span></span>  | <span data-ttu-id="b0787-1716">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1716">NaN</span></span>  | <span data-ttu-id="b0787-1717">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1717">NaN</span></span> | 
   | <span data-ttu-id="b0787-1718">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1718">+inf</span></span> | <span data-ttu-id="b0787-1719">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1719">+inf</span></span> | <span data-ttu-id="b0787-1720">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1720">-inf</span></span> | <span data-ttu-id="b0787-1721">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1721">NaN</span></span> | <span data-ttu-id="b0787-1722">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1722">NaN</span></span> | <span data-ttu-id="b0787-1723">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1723">+inf</span></span> | <span data-ttu-id="b0787-1724">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1724">-inf</span></span> | <span data-ttu-id="b0787-1725">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1725">NaN</span></span> | 
   | <span data-ttu-id="b0787-1726">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1726">-inf</span></span> | <span data-ttu-id="b0787-1727">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1727">-inf</span></span> | <span data-ttu-id="b0787-1728">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1728">+inf</span></span> | <span data-ttu-id="b0787-1729">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1729">NaN</span></span> | <span data-ttu-id="b0787-1730">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1730">NaN</span></span> | <span data-ttu-id="b0787-1731">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1731">-inf</span></span> | <span data-ttu-id="b0787-1732">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1732">+inf</span></span> | <span data-ttu-id="b0787-1733">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1733">NaN</span></span> | 
   | <span data-ttu-id="b0787-1734">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1734">NaN</span></span>  | <span data-ttu-id="b0787-1735">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1735">NaN</span></span>  | <span data-ttu-id="b0787-1736">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1736">NaN</span></span>  | <span data-ttu-id="b0787-1737">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1737">NaN</span></span> | <span data-ttu-id="b0787-1738">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1738">NaN</span></span> | <span data-ttu-id="b0787-1739">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1739">NaN</span></span>  | <span data-ttu-id="b0787-1740">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1740">NaN</span></span>  | <span data-ttu-id="b0787-1741">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1741">NaN</span></span> | 

*  <span data-ttu-id="b0787-1742">Multiplication décimale :</span><span class="sxs-lookup"><span data-stu-id="b0787-1742">Decimal multiplication:</span></span>

   ```csharp
   decimal operator *(decimal x, decimal y);
   ```

   <span data-ttu-id="b0787-1743">Si la valeur résultante est trop grande pour être représentée au format `decimal`, un `System.OverflowException` est levé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1743">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="b0787-1744">Si la valeur de résultat est trop petite pour être représentée au format `decimal`, le résultat est égal à zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-1744">If the result value is too small to represent in the `decimal` format, the result is zero.</span></span> <span data-ttu-id="b0787-1745">L’échelle du résultat, avant tout arrondi, est la somme des échelles des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-1745">The scale of the result, before any rounding, is the sum of the scales of the two operands.</span></span>

   <span data-ttu-id="b0787-1746">La multiplication décimale équivaut à utiliser l’opérateur de multiplication de type `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1746">Decimal multiplication is equivalent to using the multiplication operator of type `System.Decimal`.</span></span>


### <a name="division-operator"></a><span data-ttu-id="b0787-1747">Opérateur de division</span><span class="sxs-lookup"><span data-stu-id="b0787-1747">Division operator</span></span>

<span data-ttu-id="b0787-1748">Pour une opération de la forme `x / y`, la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-1748">For an operation of the form `x / y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-1749">Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1749">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="b0787-1750">Les opérateurs de division prédéfinis sont répertoriés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b0787-1750">The predefined division operators are listed below.</span></span> <span data-ttu-id="b0787-1751">Les opérateurs calculent tous le quotient de `x` et `y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1751">The operators all compute the quotient of `x` and `y`.</span></span>

*  <span data-ttu-id="b0787-1752">Division d’entier :</span><span class="sxs-lookup"><span data-stu-id="b0787-1752">Integer division:</span></span>

   ```csharp
   int operator /(int x, int y);
   uint operator /(uint x, uint y);
   long operator /(long x, long y);
   ulong operator /(ulong x, ulong y);
   ```

   <span data-ttu-id="b0787-1753">Si la valeur de l’opérande de droite est égale à zéro, une `System.DivideByZeroException` est levée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1753">If the value of the right operand is zero, a `System.DivideByZeroException` is thrown.</span></span>

   <span data-ttu-id="b0787-1754">La Division arrondit le résultat vers zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-1754">The division rounds the result towards zero.</span></span> <span data-ttu-id="b0787-1755">Par conséquent, la valeur absolue du résultat est le plus grand entier possible qui est inférieur ou égal à la valeur absolue du quotient des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-1755">Thus the absolute value of the result is the largest possible integer that is less than or equal to the absolute value of the quotient of the two operands.</span></span> <span data-ttu-id="b0787-1756">Le résultat est zéro ou positif lorsque les deux opérandes ont le même signe et zéro ou une valeur négative lorsque les deux opérandes ont des signes opposés.</span><span class="sxs-lookup"><span data-stu-id="b0787-1756">The result is zero or positive when the two operands have the same sign and zero or negative when the two operands have opposite signs.</span></span>

   <span data-ttu-id="b0787-1757">Si l’opérande de gauche est le plus petit `int` représentable ou la même valeur de `long` et que l’opérande de droite est `-1`, un dépassement de capacité se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-1757">If the left operand is the smallest representable `int` or `long` value and the right operand is `-1`, an overflow occurs.</span></span> <span data-ttu-id="b0787-1758">Dans un contexte de `checked`, cela entraîne la levée d’une `System.ArithmeticException` (ou d’une sous-classe).</span><span class="sxs-lookup"><span data-stu-id="b0787-1758">In a `checked` context, this causes a `System.ArithmeticException` (or a subclass thereof) to be thrown.</span></span> <span data-ttu-id="b0787-1759">Dans un contexte de `unchecked`, il est défini par l’implémentation pour déterminer si une `System.ArithmeticException` (ou une sous-classe de celle-ci) est levée ou si le dépassement de capacité n’est pas signalé avec la valeur résultante qui est celle de l’opérande de gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-1759">In an `unchecked` context, it is implementation-defined as to whether a `System.ArithmeticException` (or a subclass thereof) is thrown or the overflow goes unreported with the resulting value being that of the left operand.</span></span>

*  <span data-ttu-id="b0787-1760">Division à virgule flottante :</span><span class="sxs-lookup"><span data-stu-id="b0787-1760">Floating-point division:</span></span>

   ```csharp
   float operator /(float x, float y);
   double operator /(double x, double y);
   ```

   <span data-ttu-id="b0787-1761">Le quotient est calculé en fonction des règles de l’arithmétique IEEE 754.</span><span class="sxs-lookup"><span data-stu-id="b0787-1761">The quotient is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="b0787-1762">Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies différentes de zéro, de zéros, d’infinis et de NaN.</span><span class="sxs-lookup"><span data-stu-id="b0787-1762">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="b0787-1763">Dans le tableau, `x` et `y` sont des valeurs finies positives.</span><span class="sxs-lookup"><span data-stu-id="b0787-1763">In the table, `x` and `y` are positive finite values.</span></span> <span data-ttu-id="b0787-1764">`z` est le résultat de `x / y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1764">`z` is the result of `x / y`.</span></span> <span data-ttu-id="b0787-1765">Si le résultat est trop grand pour le type de destination, `z` est l’infini.</span><span class="sxs-lookup"><span data-stu-id="b0787-1765">If the result is too large for the destination type, `z` is infinity.</span></span> <span data-ttu-id="b0787-1766">Si le résultat est trop petit pour le type de destination, `z` est égal à zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-1766">If the result is too small for the destination type, `z` is zero.</span></span>

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | <span data-ttu-id="b0787-1767">+y</span><span class="sxs-lookup"><span data-stu-id="b0787-1767">+y</span></span>   | <span data-ttu-id="b0787-1768">-y</span><span class="sxs-lookup"><span data-stu-id="b0787-1768">-y</span></span>   | <span data-ttu-id="b0787-1769">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1769">+0</span></span>   | <span data-ttu-id="b0787-1770">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1770">-0</span></span>   | <span data-ttu-id="b0787-1771">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1771">+inf</span></span> | <span data-ttu-id="b0787-1772">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1772">-inf</span></span> | <span data-ttu-id="b0787-1773">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1773">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1774">+x</span><span class="sxs-lookup"><span data-stu-id="b0787-1774">+x</span></span>   | <span data-ttu-id="b0787-1775">+z</span><span class="sxs-lookup"><span data-stu-id="b0787-1775">+z</span></span>   | <span data-ttu-id="b0787-1776">-z</span><span class="sxs-lookup"><span data-stu-id="b0787-1776">-z</span></span>   | <span data-ttu-id="b0787-1777">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1777">+inf</span></span> | <span data-ttu-id="b0787-1778">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1778">-inf</span></span> | <span data-ttu-id="b0787-1779">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1779">+0</span></span>   | <span data-ttu-id="b0787-1780">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1780">-0</span></span>   | <span data-ttu-id="b0787-1781">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1781">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1782">-x</span><span class="sxs-lookup"><span data-stu-id="b0787-1782">-x</span></span>   | <span data-ttu-id="b0787-1783">-z</span><span class="sxs-lookup"><span data-stu-id="b0787-1783">-z</span></span>   | <span data-ttu-id="b0787-1784">+z</span><span class="sxs-lookup"><span data-stu-id="b0787-1784">+z</span></span>   | <span data-ttu-id="b0787-1785">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1785">-inf</span></span> | <span data-ttu-id="b0787-1786">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1786">+inf</span></span> | <span data-ttu-id="b0787-1787">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1787">-0</span></span>   | <span data-ttu-id="b0787-1788">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1788">+0</span></span>   | <span data-ttu-id="b0787-1789">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1789">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1790">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1790">+0</span></span>   | <span data-ttu-id="b0787-1791">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1791">+0</span></span>   | <span data-ttu-id="b0787-1792">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1792">-0</span></span>   | <span data-ttu-id="b0787-1793">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1793">NaN</span></span>  | <span data-ttu-id="b0787-1794">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1794">NaN</span></span>  | <span data-ttu-id="b0787-1795">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1795">+0</span></span>   | <span data-ttu-id="b0787-1796">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1796">-0</span></span>   | <span data-ttu-id="b0787-1797">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1797">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1798">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1798">-0</span></span>   | <span data-ttu-id="b0787-1799">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1799">-0</span></span>   | <span data-ttu-id="b0787-1800">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1800">+0</span></span>   | <span data-ttu-id="b0787-1801">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1801">NaN</span></span>  | <span data-ttu-id="b0787-1802">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1802">NaN</span></span>  | <span data-ttu-id="b0787-1803">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1803">-0</span></span>   | <span data-ttu-id="b0787-1804">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1804">+0</span></span>   | <span data-ttu-id="b0787-1805">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1805">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1806">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1806">+inf</span></span> | <span data-ttu-id="b0787-1807">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1807">+inf</span></span> | <span data-ttu-id="b0787-1808">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1808">-inf</span></span> | <span data-ttu-id="b0787-1809">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1809">+inf</span></span> | <span data-ttu-id="b0787-1810">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1810">-inf</span></span> | <span data-ttu-id="b0787-1811">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1811">NaN</span></span>  | <span data-ttu-id="b0787-1812">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1812">NaN</span></span>  | <span data-ttu-id="b0787-1813">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1813">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1814">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1814">-inf</span></span> | <span data-ttu-id="b0787-1815">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1815">-inf</span></span> | <span data-ttu-id="b0787-1816">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1816">+inf</span></span> | <span data-ttu-id="b0787-1817">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1817">-inf</span></span> | <span data-ttu-id="b0787-1818">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1818">+inf</span></span> | <span data-ttu-id="b0787-1819">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1819">NaN</span></span>  | <span data-ttu-id="b0787-1820">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1820">NaN</span></span>  | <span data-ttu-id="b0787-1821">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1821">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1822">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1822">NaN</span></span>  | <span data-ttu-id="b0787-1823">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1823">NaN</span></span>  | <span data-ttu-id="b0787-1824">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1824">NaN</span></span>  | <span data-ttu-id="b0787-1825">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1825">NaN</span></span>  | <span data-ttu-id="b0787-1826">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1826">NaN</span></span>  | <span data-ttu-id="b0787-1827">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1827">NaN</span></span>  | <span data-ttu-id="b0787-1828">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1828">NaN</span></span>  | <span data-ttu-id="b0787-1829">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1829">NaN</span></span>  | 

*  <span data-ttu-id="b0787-1830">Division décimale :</span><span class="sxs-lookup"><span data-stu-id="b0787-1830">Decimal division:</span></span>

   ```csharp
   decimal operator /(decimal x, decimal y);
   ```

   <span data-ttu-id="b0787-1831">Si la valeur de l’opérande de droite est égale à zéro, une `System.DivideByZeroException` est levée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1831">If the value of the right operand is zero, a `System.DivideByZeroException` is thrown.</span></span> <span data-ttu-id="b0787-1832">Si la valeur résultante est trop grande pour être représentée au format `decimal`, un `System.OverflowException` est levé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1832">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="b0787-1833">Si la valeur de résultat est trop petite pour être représentée au format `decimal`, le résultat est égal à zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-1833">If the result value is too small to represent in the `decimal` format, the result is zero.</span></span> <span data-ttu-id="b0787-1834">L’échelle du résultat est la plus petite échelle qui conserve un résultat égal à la valeur décimale représentable la plus proche du résultat mathématique vrai.</span><span class="sxs-lookup"><span data-stu-id="b0787-1834">The scale of the result is the smallest scale that will preserve a result equal to the nearest representable decimal value to the true mathematical result.</span></span>

   <span data-ttu-id="b0787-1835">La Division décimale équivaut à utiliser l’opérateur de division de type `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1835">Decimal division is equivalent to using the division operator of type `System.Decimal`.</span></span>


### <a name="remainder-operator"></a><span data-ttu-id="b0787-1836">Opérateur de reste</span><span class="sxs-lookup"><span data-stu-id="b0787-1836">Remainder operator</span></span>

<span data-ttu-id="b0787-1837">Pour une opération de la forme `x % y`, la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-1837">For an operation of the form `x % y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-1838">Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1838">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="b0787-1839">Les opérateurs de reste prédéfinis sont répertoriés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b0787-1839">The predefined remainder operators are listed below.</span></span> <span data-ttu-id="b0787-1840">Les opérateurs calculent tous le reste de la division entre `x` et `y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1840">The operators all compute the remainder of the division between `x` and `y`.</span></span>

*  <span data-ttu-id="b0787-1841">Reste entier :</span><span class="sxs-lookup"><span data-stu-id="b0787-1841">Integer remainder:</span></span>

   ```csharp
   int operator %(int x, int y);
   uint operator %(uint x, uint y);
   long operator %(long x, long y);
   ulong operator %(ulong x, ulong y);
   ```

   <span data-ttu-id="b0787-1842">Le résultat de `x % y` est la valeur produite par `x - (x / y) * y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1842">The result of `x % y` is the value produced by `x - (x / y) * y`.</span></span> <span data-ttu-id="b0787-1843">Si `y` est égal à zéro, un `System.DivideByZeroException` est levé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1843">If `y` is zero, a `System.DivideByZeroException` is thrown.</span></span>

   <span data-ttu-id="b0787-1844">Si l’opérande de gauche est le plus petit `int` ou `long` valeur et que l’opérande de droite est `-1`, une `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1844">If the left operand is the smallest `int` or `long` value and the right operand is `-1`, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="b0787-1845">Dans le cas contraire, ne lève `x % y` d’exception lorsque `x / y` ne lèvera pas d’exception.</span><span class="sxs-lookup"><span data-stu-id="b0787-1845">In no case does `x % y` throw an exception where `x / y` would not throw an exception.</span></span>

*  <span data-ttu-id="b0787-1846">Reste à virgule flottante :</span><span class="sxs-lookup"><span data-stu-id="b0787-1846">Floating-point remainder:</span></span>

   ```csharp
   float operator %(float x, float y);
   double operator %(double x, double y);
   ```

   <span data-ttu-id="b0787-1847">Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies différentes de zéro, de zéros, d’infinis et de NaN.</span><span class="sxs-lookup"><span data-stu-id="b0787-1847">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="b0787-1848">Dans le tableau, `x` et `y` sont des valeurs finies positives.</span><span class="sxs-lookup"><span data-stu-id="b0787-1848">In the table, `x` and `y` are positive finite values.</span></span> <span data-ttu-id="b0787-1849">`z` est le résultat de `x % y` et est calculé comme `x - n * y`, où `n` est le plus grand entier possible qui est inférieur ou égal à `x / y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1849">`z` is the result of `x % y` and is computed as `x - n * y`, where `n` is the largest possible integer that is less than or equal to `x / y`.</span></span> <span data-ttu-id="b0787-1850">Cette méthode de calcul du reste est analogue à celle utilisée pour les opérandes entiers, mais diffère de la définition IEEE 754 (dans laquelle `n` est l’entier le plus proche de `x / y`).</span><span class="sxs-lookup"><span data-stu-id="b0787-1850">This method of computing the remainder is analogous to that used for integer operands, but differs from the IEEE 754 definition (in which `n` is the integer closest to `x / y`).</span></span>

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | <span data-ttu-id="b0787-1851">+y</span><span class="sxs-lookup"><span data-stu-id="b0787-1851">+y</span></span>   | <span data-ttu-id="b0787-1852">-y</span><span class="sxs-lookup"><span data-stu-id="b0787-1852">-y</span></span>   | <span data-ttu-id="b0787-1853">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1853">+0</span></span>   | <span data-ttu-id="b0787-1854">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1854">-0</span></span>   | <span data-ttu-id="b0787-1855">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1855">+inf</span></span> | <span data-ttu-id="b0787-1856">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1856">-inf</span></span> | <span data-ttu-id="b0787-1857">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1857">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1858">+x</span><span class="sxs-lookup"><span data-stu-id="b0787-1858">+x</span></span>   | <span data-ttu-id="b0787-1859">+z</span><span class="sxs-lookup"><span data-stu-id="b0787-1859">+z</span></span>   | <span data-ttu-id="b0787-1860">+z</span><span class="sxs-lookup"><span data-stu-id="b0787-1860">+z</span></span>   | <span data-ttu-id="b0787-1861">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1861">NaN</span></span>  | <span data-ttu-id="b0787-1862">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1862">NaN</span></span>  | <span data-ttu-id="b0787-1863">x</span><span class="sxs-lookup"><span data-stu-id="b0787-1863">x</span></span>    | <span data-ttu-id="b0787-1864">x</span><span class="sxs-lookup"><span data-stu-id="b0787-1864">x</span></span>    | <span data-ttu-id="b0787-1865">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1865">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1866">-x</span><span class="sxs-lookup"><span data-stu-id="b0787-1866">-x</span></span>   | <span data-ttu-id="b0787-1867">-z</span><span class="sxs-lookup"><span data-stu-id="b0787-1867">-z</span></span>   | <span data-ttu-id="b0787-1868">-z</span><span class="sxs-lookup"><span data-stu-id="b0787-1868">-z</span></span>   | <span data-ttu-id="b0787-1869">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1869">NaN</span></span>  | <span data-ttu-id="b0787-1870">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1870">NaN</span></span>  | <span data-ttu-id="b0787-1871">-x</span><span class="sxs-lookup"><span data-stu-id="b0787-1871">-x</span></span>   | <span data-ttu-id="b0787-1872">-x</span><span class="sxs-lookup"><span data-stu-id="b0787-1872">-x</span></span>   | <span data-ttu-id="b0787-1873">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1873">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1874">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1874">+0</span></span>   | <span data-ttu-id="b0787-1875">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1875">+0</span></span>   | <span data-ttu-id="b0787-1876">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1876">+0</span></span>   | <span data-ttu-id="b0787-1877">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1877">NaN</span></span>  | <span data-ttu-id="b0787-1878">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1878">NaN</span></span>  | <span data-ttu-id="b0787-1879">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1879">+0</span></span>   | <span data-ttu-id="b0787-1880">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1880">+0</span></span>   | <span data-ttu-id="b0787-1881">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1881">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1882">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1882">-0</span></span>   | <span data-ttu-id="b0787-1883">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1883">-0</span></span>   | <span data-ttu-id="b0787-1884">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1884">-0</span></span>   | <span data-ttu-id="b0787-1885">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1885">NaN</span></span>  | <span data-ttu-id="b0787-1886">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1886">NaN</span></span>  | <span data-ttu-id="b0787-1887">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1887">-0</span></span>   | <span data-ttu-id="b0787-1888">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1888">-0</span></span>   | <span data-ttu-id="b0787-1889">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1889">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1890">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1890">+inf</span></span> | <span data-ttu-id="b0787-1891">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1891">NaN</span></span>  | <span data-ttu-id="b0787-1892">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1892">NaN</span></span>  | <span data-ttu-id="b0787-1893">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1893">NaN</span></span>  | <span data-ttu-id="b0787-1894">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1894">NaN</span></span>  | <span data-ttu-id="b0787-1895">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1895">NaN</span></span>  | <span data-ttu-id="b0787-1896">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1896">NaN</span></span>  | <span data-ttu-id="b0787-1897">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1897">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1898">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1898">-inf</span></span> | <span data-ttu-id="b0787-1899">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1899">NaN</span></span>  | <span data-ttu-id="b0787-1900">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1900">NaN</span></span>  | <span data-ttu-id="b0787-1901">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1901">NaN</span></span>  | <span data-ttu-id="b0787-1902">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1902">NaN</span></span>  | <span data-ttu-id="b0787-1903">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1903">NaN</span></span>  | <span data-ttu-id="b0787-1904">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1904">NaN</span></span>  | <span data-ttu-id="b0787-1905">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1905">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1906">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1906">NaN</span></span>  | <span data-ttu-id="b0787-1907">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1907">NaN</span></span>  | <span data-ttu-id="b0787-1908">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1908">NaN</span></span>  | <span data-ttu-id="b0787-1909">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1909">NaN</span></span>  | <span data-ttu-id="b0787-1910">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1910">NaN</span></span>  | <span data-ttu-id="b0787-1911">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1911">NaN</span></span>  | <span data-ttu-id="b0787-1912">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1912">NaN</span></span>  | <span data-ttu-id="b0787-1913">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1913">NaN</span></span>  | 

*  <span data-ttu-id="b0787-1914">Reste décimal :</span><span class="sxs-lookup"><span data-stu-id="b0787-1914">Decimal remainder:</span></span>

   ```csharp
   decimal operator %(decimal x, decimal y);
   ```

   <span data-ttu-id="b0787-1915">Si la valeur de l’opérande de droite est égale à zéro, une `System.DivideByZeroException` est levée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1915">If the value of the right operand is zero, a `System.DivideByZeroException` is thrown.</span></span> <span data-ttu-id="b0787-1916">L’échelle du résultat, avant tout arrondi, est la plus grande des échelles des deux opérandes, et le signe du résultat, s’il est différent de zéro, est le même que celui de `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1916">The scale of the result, before any rounding, is the larger of the scales of the two operands, and the sign of the result, if non-zero, is the same as that of `x`.</span></span>

   <span data-ttu-id="b0787-1917">Le reste décimal est équivalent à l’utilisation de l’opérateur de reste de type `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1917">Decimal remainder is equivalent to using the remainder operator of type `System.Decimal`.</span></span>


### <a name="addition-operator"></a><span data-ttu-id="b0787-1918">Opérateur d’addition</span><span class="sxs-lookup"><span data-stu-id="b0787-1918">Addition operator</span></span>

<span data-ttu-id="b0787-1919">Pour une opération de la forme `x + y`, la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-1919">For an operation of the form `x + y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-1920">Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-1920">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="b0787-1921">Les opérateurs d’addition prédéfinis sont répertoriés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b0787-1921">The predefined addition operators are listed below.</span></span> <span data-ttu-id="b0787-1922">Pour les types numériques et d’énumération, les opérateurs d’addition prédéfinis calculent la somme des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-1922">For numeric and enumeration types, the predefined addition operators compute the sum of the two operands.</span></span> <span data-ttu-id="b0787-1923">Quand l’un des opérandes ou les deux sont de type chaîne, les opérateurs d’addition prédéfinis concatènent la représentation sous forme de chaîne des opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-1923">When one or both operands are of type string, the predefined addition operators concatenate the string representation of the operands.</span></span>

*  <span data-ttu-id="b0787-1924">Ajout d’un entier :</span><span class="sxs-lookup"><span data-stu-id="b0787-1924">Integer addition:</span></span>

   ```csharp
   int operator +(int x, int y);
   uint operator +(uint x, uint y);
   long operator +(long x, long y);
   ulong operator +(ulong x, ulong y);
   ```

   <span data-ttu-id="b0787-1925">Dans un contexte de `checked`, si la somme est en dehors de la plage du type de résultat, une `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1925">In a `checked` context, if the sum is outside the range of the result type, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="b0787-1926">Dans un contexte de `unchecked`, les dépassements de capacité ne sont pas signalés et les bits de poids fort significatifs en dehors de la plage du type de résultat sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="b0787-1926">In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.</span></span>

*  <span data-ttu-id="b0787-1927">Addition à virgule flottante :</span><span class="sxs-lookup"><span data-stu-id="b0787-1927">Floating-point addition:</span></span>

   ```csharp
   float operator +(float x, float y);
   double operator +(double x, double y);
   ```

   <span data-ttu-id="b0787-1928">La somme est calculée en fonction des règles de l’arithmétique IEEE 754.</span><span class="sxs-lookup"><span data-stu-id="b0787-1928">The sum is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="b0787-1929">Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies différentes de zéro, de zéros, d’infinis et de NaN.</span><span class="sxs-lookup"><span data-stu-id="b0787-1929">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="b0787-1930">Dans le tableau, `x` et `y` sont des valeurs finies non nulles, et `z` est le résultat de `x + y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1930">In the table, `x` and `y` are nonzero finite values, and `z` is the result of `x + y`.</span></span> <span data-ttu-id="b0787-1931">Si `x` et `y` ont la même magnitude mais des signes opposés, `z` est un zéro positif.</span><span class="sxs-lookup"><span data-stu-id="b0787-1931">If `x` and `y` have the same magnitude but opposite signs, `z` is positive zero.</span></span> <span data-ttu-id="b0787-1932">Si `x + y` est trop grand pour être représenté dans le type de destination, `z` est un infini avec le même signe que `x + y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1932">If `x + y` is too large to represent in the destination type, `z` is an infinity with the same sign as `x + y`.</span></span>

   |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | <span data-ttu-id="b0787-1933">o</span><span class="sxs-lookup"><span data-stu-id="b0787-1933">y</span></span>    | <span data-ttu-id="b0787-1934">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1934">+0</span></span>   | <span data-ttu-id="b0787-1935">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1935">-0</span></span>   | <span data-ttu-id="b0787-1936">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1936">+inf</span></span> | <span data-ttu-id="b0787-1937">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1937">-inf</span></span> | <span data-ttu-id="b0787-1938">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1938">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1939">x</span><span class="sxs-lookup"><span data-stu-id="b0787-1939">x</span></span>    | <span data-ttu-id="b0787-1940">e</span><span class="sxs-lookup"><span data-stu-id="b0787-1940">z</span></span>    | <span data-ttu-id="b0787-1941">x</span><span class="sxs-lookup"><span data-stu-id="b0787-1941">x</span></span>    | <span data-ttu-id="b0787-1942">x</span><span class="sxs-lookup"><span data-stu-id="b0787-1942">x</span></span>    | <span data-ttu-id="b0787-1943">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1943">+inf</span></span> | <span data-ttu-id="b0787-1944">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1944">-inf</span></span> | <span data-ttu-id="b0787-1945">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1945">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1946">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1946">+0</span></span>   | <span data-ttu-id="b0787-1947">o</span><span class="sxs-lookup"><span data-stu-id="b0787-1947">y</span></span>    | <span data-ttu-id="b0787-1948">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1948">+0</span></span>   | <span data-ttu-id="b0787-1949">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1949">+0</span></span>   | <span data-ttu-id="b0787-1950">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1950">+inf</span></span> | <span data-ttu-id="b0787-1951">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1951">-inf</span></span> | <span data-ttu-id="b0787-1952">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1952">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1953">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1953">-0</span></span>   | <span data-ttu-id="b0787-1954">o</span><span class="sxs-lookup"><span data-stu-id="b0787-1954">y</span></span>    | <span data-ttu-id="b0787-1955">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-1955">+0</span></span>   | <span data-ttu-id="b0787-1956">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-1956">-0</span></span>   | <span data-ttu-id="b0787-1957">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1957">+inf</span></span> | <span data-ttu-id="b0787-1958">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1958">-inf</span></span> | <span data-ttu-id="b0787-1959">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1959">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1960">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1960">+inf</span></span> | <span data-ttu-id="b0787-1961">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1961">+inf</span></span> | <span data-ttu-id="b0787-1962">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1962">+inf</span></span> | <span data-ttu-id="b0787-1963">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1963">+inf</span></span> | <span data-ttu-id="b0787-1964">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1964">+inf</span></span> | <span data-ttu-id="b0787-1965">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1965">NaN</span></span>  | <span data-ttu-id="b0787-1966">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1966">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1967">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1967">-inf</span></span> | <span data-ttu-id="b0787-1968">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1968">-inf</span></span> | <span data-ttu-id="b0787-1969">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1969">-inf</span></span> | <span data-ttu-id="b0787-1970">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1970">-inf</span></span> | <span data-ttu-id="b0787-1971">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1971">NaN</span></span>  | <span data-ttu-id="b0787-1972">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-1972">-inf</span></span> | <span data-ttu-id="b0787-1973">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1973">NaN</span></span>  | 
   | <span data-ttu-id="b0787-1974">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1974">NaN</span></span>  | <span data-ttu-id="b0787-1975">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1975">NaN</span></span>  | <span data-ttu-id="b0787-1976">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1976">NaN</span></span>  | <span data-ttu-id="b0787-1977">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1977">NaN</span></span>  | <span data-ttu-id="b0787-1978">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1978">NaN</span></span>  | <span data-ttu-id="b0787-1979">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1979">NaN</span></span>  | <span data-ttu-id="b0787-1980">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-1980">NaN</span></span>  | 

*  <span data-ttu-id="b0787-1981">Addition de nombres décimaux :</span><span class="sxs-lookup"><span data-stu-id="b0787-1981">Decimal addition:</span></span>

   ```csharp
   decimal operator +(decimal x, decimal y);
   ```

   <span data-ttu-id="b0787-1982">Si la valeur résultante est trop grande pour être représentée au format `decimal`, un `System.OverflowException` est levé.</span><span class="sxs-lookup"><span data-stu-id="b0787-1982">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="b0787-1983">L’échelle du résultat, avant tout arrondi, est la plus grande des échelles des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-1983">The scale of the result, before any rounding, is the larger of the scales of the two operands.</span></span>

   <span data-ttu-id="b0787-1984">L’addition décimale équivaut à utiliser l’opérateur d’addition de type `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1984">Decimal addition is equivalent to using the addition operator of type `System.Decimal`.</span></span>

*  <span data-ttu-id="b0787-1985">Addition d’énumération.</span><span class="sxs-lookup"><span data-stu-id="b0787-1985">Enumeration addition.</span></span> <span data-ttu-id="b0787-1986">Chaque type d’énumération fournit implicitement les opérateurs prédéfinis suivants, où `E` est le type enum, et `U` est le type sous-jacent de `E`:</span><span class="sxs-lookup"><span data-stu-id="b0787-1986">Every enumeration type implicitly provides the following predefined operators, where `E` is the enum type, and `U` is the underlying type of `E`:</span></span>

   ```csharp
   E operator +(E x, U y);
   E operator +(U x, E y);
   ```

   <span data-ttu-id="b0787-1987">Au moment de l’exécution, ces opérateurs sont évalués exactement comme `(E)((U)x + (U)y)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1987">At run-time these operators are evaluated exactly as `(E)((U)x + (U)y)`.</span></span>

*  <span data-ttu-id="b0787-1988">Concaténation de chaînes :</span><span class="sxs-lookup"><span data-stu-id="b0787-1988">String concatenation:</span></span>

   ```csharp
   string operator +(string x, string y);
   string operator +(string x, object y);
   string operator +(object x, string y);
   ```

   <span data-ttu-id="b0787-1989">Ces surcharges de l’opérateur binaire `+` effectuent la concaténation de chaînes.</span><span class="sxs-lookup"><span data-stu-id="b0787-1989">These overloads of the binary `+` operator perform string concatenation.</span></span> <span data-ttu-id="b0787-1990">Si un opérande de concaténation de chaîne est `null`, une chaîne vide est substituée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1990">If an operand of string concatenation is `null`, an empty string is substituted.</span></span> <span data-ttu-id="b0787-1991">Sinon, tout argument qui n’est pas une chaîne est converti en sa représentation sous forme de chaîne en appelant la méthode `ToString` virtuelle héritée du type `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1991">Otherwise, any non-string argument is converted to its string representation by invoking the virtual `ToString` method inherited from type `object`.</span></span> <span data-ttu-id="b0787-1992">Si `ToString` retourne `null`, une chaîne vide est substituée.</span><span class="sxs-lookup"><span data-stu-id="b0787-1992">If `ToString` returns `null`, an empty string is substituted.</span></span>

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

   Le résultat de l’opérateur de concaténation de chaînes est une chaîne qui se compose des caractères de l’opérande de gauche, suivis des caractères de l’opérande de droite. L’opérateur de concaténation de chaînes ne retourne jamais de valeur `null`. <span data-ttu-id="b0787-1995">Une `System.OutOfMemoryException` peut être levée si la mémoire disponible est insuffisante pour allouer la chaîne résultante.</span><span class="sxs-lookup"><span data-stu-id="b0787-1995">A `System.OutOfMemoryException` may be thrown if there is not enough memory available to allocate the resulting string.</span></span>

*  <span data-ttu-id="b0787-1996">Combinaison de délégués.</span><span class="sxs-lookup"><span data-stu-id="b0787-1996">Delegate combination.</span></span> <span data-ttu-id="b0787-1997">Chaque type délégué fournit implicitement l’opérateur prédéfini suivant, où `D` est le type délégué :</span><span class="sxs-lookup"><span data-stu-id="b0787-1997">Every delegate type implicitly provides the following predefined operator, where `D` is the delegate type:</span></span>

   ```csharp
   D operator +(D x, D y);
   ```

   <span data-ttu-id="b0787-1998">L’opérateur de `+` binaire effectue une combinaison de délégués lorsque les deux opérandes sont d’un type délégué `D`.</span><span class="sxs-lookup"><span data-stu-id="b0787-1998">The binary `+` operator performs delegate combination when both operands are of some delegate type `D`.</span></span> <span data-ttu-id="b0787-1999">(Si les opérandes ont des types délégués différents, une erreur de temps de liaison se produit.) Si le premier opérande est `null`, le résultat de l’opération est la valeur du deuxième opérande (même s’il est également `null`).</span><span class="sxs-lookup"><span data-stu-id="b0787-1999">(If the operands have different delegate types, a binding-time error occurs.) If the first operand is `null`, the result of the operation is the value of the second operand (even if that is also `null`).</span></span> <span data-ttu-id="b0787-2000">Sinon, si le second opérande est `null`, le résultat de l’opération est la valeur du premier opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-2000">Otherwise, if the second operand is `null`, then the result of the operation is the value of the first operand.</span></span> <span data-ttu-id="b0787-2001">Dans le cas contraire, le résultat de l’opération est une nouvelle instance de délégué qui, lorsqu’elle est appelée, appelle le premier opérande, puis appelle le second opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-2001">Otherwise, the result of the operation is a new delegate instance that, when invoked, invokes the first operand and then invokes the second operand.</span></span> <span data-ttu-id="b0787-2002">Pour obtenir des exemples de combinaison de délégués, consultez [opérateur de soustraction](expressions.md#subtraction-operator) et [appel de délégué](delegates.md#delegate-invocation).</span><span class="sxs-lookup"><span data-stu-id="b0787-2002">For examples of delegate combination, see [Subtraction operator](expressions.md#subtraction-operator) and [Delegate invocation](delegates.md#delegate-invocation).</span></span> <span data-ttu-id="b0787-2003">Étant donné que `System.Delegate` n’est pas un type délégué, `operator` `+` n’est pas défini pour celui-ci.</span><span class="sxs-lookup"><span data-stu-id="b0787-2003">Since `System.Delegate` is not a delegate type, `operator` `+` is not defined for it.</span></span>

### <a name="subtraction-operator"></a><span data-ttu-id="b0787-2004">Opérateur de soustraction</span><span class="sxs-lookup"><span data-stu-id="b0787-2004">Subtraction operator</span></span>

<span data-ttu-id="b0787-2005">Pour une opération de la forme `x - y`, la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-2005">For an operation of the form `x - y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-2006">Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2006">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="b0787-2007">Les opérateurs de soustraction prédéfinis sont répertoriés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b0787-2007">The predefined subtraction operators are listed below.</span></span> <span data-ttu-id="b0787-2008">Les opérateurs soustraitnt tous les `y` de `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2008">The operators all subtract `y` from `x`.</span></span>

*  <span data-ttu-id="b0787-2009">Soustraction d’entiers :</span><span class="sxs-lookup"><span data-stu-id="b0787-2009">Integer subtraction:</span></span>

   ```csharp
   int operator -(int x, int y);
   uint operator -(uint x, uint y);
   long operator -(long x, long y);
   ulong operator -(ulong x, ulong y);
   ```

   <span data-ttu-id="b0787-2010">Dans un contexte de `checked`, si la différence se situe en dehors de la plage du type de résultat, une `System.OverflowException` est levée.</span><span class="sxs-lookup"><span data-stu-id="b0787-2010">In a `checked` context, if the difference is outside the range of the result type, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="b0787-2011">Dans un contexte de `unchecked`, les dépassements de capacité ne sont pas signalés et les bits de poids fort significatifs en dehors de la plage du type de résultat sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="b0787-2011">In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.</span></span>

*  <span data-ttu-id="b0787-2012">Soustraction à virgule flottante :</span><span class="sxs-lookup"><span data-stu-id="b0787-2012">Floating-point subtraction:</span></span>

   ```csharp
   float operator -(float x, float y);
   double operator -(double x, double y);
   ```

   <span data-ttu-id="b0787-2013">La différence est calculée en fonction des règles de l’arithmétique IEEE 754.</span><span class="sxs-lookup"><span data-stu-id="b0787-2013">The difference is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="b0787-2014">Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies différentes de zéro, de zéros, d’infinis et de valeurs NaN.</span><span class="sxs-lookup"><span data-stu-id="b0787-2014">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaNs.</span></span> <span data-ttu-id="b0787-2015">Dans le tableau, `x` et `y` sont des valeurs finies non nulles, et `z` est le résultat de `x - y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2015">In the table, `x` and `y` are nonzero finite values, and `z` is the result of `x - y`.</span></span> <span data-ttu-id="b0787-2016">Si `x` et `y` sont égaux, `z` est un zéro positif.</span><span class="sxs-lookup"><span data-stu-id="b0787-2016">If `x` and `y` are equal, `z` is positive zero.</span></span> <span data-ttu-id="b0787-2017">Si `x - y` est trop grand pour être représenté dans le type de destination, `z` est un infini avec le même signe que `x - y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2017">If `x - y` is too large to represent in the destination type, `z` is an infinity with the same sign as `x - y`.</span></span>

   |      |      |      |      |      |      |     |
   |:----:|:----:|:----:|:----:|:----:|:----:|:---:|
   |      | <span data-ttu-id="b0787-2018">o</span><span class="sxs-lookup"><span data-stu-id="b0787-2018">y</span></span>    | <span data-ttu-id="b0787-2019">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-2019">+0</span></span>   | <span data-ttu-id="b0787-2020">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-2020">-0</span></span>   | <span data-ttu-id="b0787-2021">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2021">+inf</span></span> | <span data-ttu-id="b0787-2022">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2022">-inf</span></span> | <span data-ttu-id="b0787-2023">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2023">NaN</span></span> | 
   | <span data-ttu-id="b0787-2024">x</span><span class="sxs-lookup"><span data-stu-id="b0787-2024">x</span></span>    | <span data-ttu-id="b0787-2025">e</span><span class="sxs-lookup"><span data-stu-id="b0787-2025">z</span></span>    | <span data-ttu-id="b0787-2026">x</span><span class="sxs-lookup"><span data-stu-id="b0787-2026">x</span></span>    | <span data-ttu-id="b0787-2027">x</span><span class="sxs-lookup"><span data-stu-id="b0787-2027">x</span></span>    | <span data-ttu-id="b0787-2028">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2028">-inf</span></span> | <span data-ttu-id="b0787-2029">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2029">+inf</span></span> | <span data-ttu-id="b0787-2030">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2030">NaN</span></span> | 
   | <span data-ttu-id="b0787-2031">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-2031">+0</span></span>   | <span data-ttu-id="b0787-2032">-y</span><span class="sxs-lookup"><span data-stu-id="b0787-2032">-y</span></span>   | <span data-ttu-id="b0787-2033">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-2033">+0</span></span>   | <span data-ttu-id="b0787-2034">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-2034">+0</span></span>   | <span data-ttu-id="b0787-2035">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2035">-inf</span></span> | <span data-ttu-id="b0787-2036">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2036">+inf</span></span> | <span data-ttu-id="b0787-2037">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2037">NaN</span></span> | 
   | <span data-ttu-id="b0787-2038">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-2038">-0</span></span>   | <span data-ttu-id="b0787-2039">-y</span><span class="sxs-lookup"><span data-stu-id="b0787-2039">-y</span></span>   | <span data-ttu-id="b0787-2040">-0</span><span class="sxs-lookup"><span data-stu-id="b0787-2040">-0</span></span>   | <span data-ttu-id="b0787-2041">+0</span><span class="sxs-lookup"><span data-stu-id="b0787-2041">+0</span></span>   | <span data-ttu-id="b0787-2042">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2042">-inf</span></span> | <span data-ttu-id="b0787-2043">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2043">+inf</span></span> | <span data-ttu-id="b0787-2044">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2044">NaN</span></span> | 
   | <span data-ttu-id="b0787-2045">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2045">+inf</span></span> | <span data-ttu-id="b0787-2046">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2046">+inf</span></span> | <span data-ttu-id="b0787-2047">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2047">+inf</span></span> | <span data-ttu-id="b0787-2048">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2048">+inf</span></span> | <span data-ttu-id="b0787-2049">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2049">NaN</span></span>  | <span data-ttu-id="b0787-2050">+inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2050">+inf</span></span> | <span data-ttu-id="b0787-2051">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2051">NaN</span></span> | 
   | <span data-ttu-id="b0787-2052">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2052">-inf</span></span> | <span data-ttu-id="b0787-2053">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2053">-inf</span></span> | <span data-ttu-id="b0787-2054">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2054">-inf</span></span> | <span data-ttu-id="b0787-2055">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2055">-inf</span></span> | <span data-ttu-id="b0787-2056">-inf</span><span class="sxs-lookup"><span data-stu-id="b0787-2056">-inf</span></span> | <span data-ttu-id="b0787-2057">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2057">NaN</span></span>  | <span data-ttu-id="b0787-2058">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2058">NaN</span></span> | 
   | <span data-ttu-id="b0787-2059">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2059">NaN</span></span>  | <span data-ttu-id="b0787-2060">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2060">NaN</span></span>  | <span data-ttu-id="b0787-2061">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2061">NaN</span></span>  | <span data-ttu-id="b0787-2062">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2062">NaN</span></span>  | <span data-ttu-id="b0787-2063">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2063">NaN</span></span>  | <span data-ttu-id="b0787-2064">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2064">NaN</span></span>  | <span data-ttu-id="b0787-2065">NaN</span><span class="sxs-lookup"><span data-stu-id="b0787-2065">NaN</span></span> | 

*  <span data-ttu-id="b0787-2066">Soustraction de décimale :</span><span class="sxs-lookup"><span data-stu-id="b0787-2066">Decimal subtraction:</span></span>

   ```csharp
   decimal operator -(decimal x, decimal y);
   ```

   <span data-ttu-id="b0787-2067">Si la valeur résultante est trop grande pour être représentée au format `decimal`, un `System.OverflowException` est levé.</span><span class="sxs-lookup"><span data-stu-id="b0787-2067">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="b0787-2068">L’échelle du résultat, avant tout arrondi, est la plus grande des échelles des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2068">The scale of the result, before any rounding, is the larger of the scales of the two operands.</span></span>

   <span data-ttu-id="b0787-2069">La soustraction de décimale équivaut à utiliser l’opérateur de soustraction de type `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2069">Decimal subtraction is equivalent to using the subtraction operator of type `System.Decimal`.</span></span>

*  <span data-ttu-id="b0787-2070">Soustraction d’énumération.</span><span class="sxs-lookup"><span data-stu-id="b0787-2070">Enumeration subtraction.</span></span> <span data-ttu-id="b0787-2071">Chaque type d’énumération fournit implicitement l’opérateur prédéfini suivant, où `E` est le type enum, et `U` est le type sous-jacent de `E`:</span><span class="sxs-lookup"><span data-stu-id="b0787-2071">Every enumeration type implicitly provides the following predefined operator, where `E` is the enum type, and `U` is the underlying type of `E`:</span></span>

   ```csharp
   U operator -(E x, E y);
   ```

   <span data-ttu-id="b0787-2072">Cet opérateur est évalué exactement comme `(U)((U)x - (U)y)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2072">This operator is evaluated exactly as `(U)((U)x - (U)y)`.</span></span> <span data-ttu-id="b0787-2073">En d’autres termes, l’opérateur calcule la différence entre les valeurs ordinales de `x` et `y`, et le type du résultat est le type sous-jacent de l’énumération.</span><span class="sxs-lookup"><span data-stu-id="b0787-2073">In other words, the operator computes the difference between the ordinal values of `x` and `y`, and the type of the result is the underlying type of the enumeration.</span></span>

   ```csharp
   E operator -(E x, U y);
   ```

   <span data-ttu-id="b0787-2074">Cet opérateur est évalué exactement comme `(E)((U)x - y)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2074">This operator is evaluated exactly as `(E)((U)x - y)`.</span></span> <span data-ttu-id="b0787-2075">En d’autres termes, l’opérateur soustrait une valeur du type sous-jacent de l’énumération, ce qui produit une valeur de l’énumération.</span><span class="sxs-lookup"><span data-stu-id="b0787-2075">In other words, the operator subtracts a value from the underlying type of the enumeration, yielding a value of the enumeration.</span></span>

*  <span data-ttu-id="b0787-2076">Suppression du délégué.</span><span class="sxs-lookup"><span data-stu-id="b0787-2076">Delegate removal.</span></span> <span data-ttu-id="b0787-2077">Chaque type délégué fournit implicitement l’opérateur prédéfini suivant, où `D` est le type délégué :</span><span class="sxs-lookup"><span data-stu-id="b0787-2077">Every delegate type implicitly provides the following predefined operator, where `D` is the delegate type:</span></span>

   ```csharp
   D operator -(D x, D y);
   ```

   <span data-ttu-id="b0787-2078">L’opérateur de `-` binaire effectue une suppression de délégué lorsque les deux opérandes sont d’un type délégué `D`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2078">The binary `-` operator performs delegate removal when both operands are of some delegate type `D`.</span></span> <span data-ttu-id="b0787-2079">Si les opérandes ont des types délégués différents, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-2079">If the operands have different delegate types, a binding-time error occurs.</span></span> <span data-ttu-id="b0787-2080">Si le premier opérande a la valeur `null`, le résultat de l’opération est `null`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2080">If the first operand is `null`, the result of the operation is `null`.</span></span> <span data-ttu-id="b0787-2081">Sinon, si le second opérande est `null`, le résultat de l’opération est la valeur du premier opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-2081">Otherwise, if the second operand is `null`, then the result of the operation is the value of the first operand.</span></span> <span data-ttu-id="b0787-2082">Sinon, les deux opérandes représentent les listes d’appel ([déclarations de délégué](delegates.md#delegate-declarations)) ayant une ou plusieurs entrées, et le résultat est une nouvelle liste d’appel comprenant la liste de la première opérande avec les entrées du deuxième opérande supprimées, à condition que la liste du deuxième opérande soit une sous-liste contiguë correcte du premier.</span><span class="sxs-lookup"><span data-stu-id="b0787-2082">Otherwise, both operands represent invocation lists ([Delegate declarations](delegates.md#delegate-declarations)) having one or more entries, and the result is a new invocation list consisting of the first operand's list with the second operand's entries removed from it, provided the second operand's list is a proper contiguous sublist of the first's.</span></span>     <span data-ttu-id="b0787-2083">(Pour déterminer l’égalité des sous-listes, les entrées correspondantes sont comparées à celles de l’opérateur d’égalité de délégué ([opérateurs d’égalité de délégué](expressions.md#delegate-equality-operators)).) Dans le cas contraire, le résultat est la valeur de l’opérande gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-2083">(To determine sublist equality, corresponding entries are compared as for the delegate equality operator ([Delegate equality operators](expressions.md#delegate-equality-operators)).) Otherwise, the result is the value of the left operand.</span></span> <span data-ttu-id="b0787-2084">Aucune des listes d’opérandes n’est modifiée dans le processus.</span><span class="sxs-lookup"><span data-stu-id="b0787-2084">Neither of the operands' lists is changed in the process.</span></span> <span data-ttu-id="b0787-2085">Si la liste du deuxième opérande correspond à plusieurs sous-listes d’entrées contiguës dans la liste du premier opérande, la sous-liste correspondante la plus à droite des entrées contiguës est supprimée.</span><span class="sxs-lookup"><span data-stu-id="b0787-2085">If the second operand's list matches multiple sublists of contiguous entries in the first operand's list, the right-most matching sublist of contiguous entries is removed.</span></span> <span data-ttu-id="b0787-2086">Si la suppression aboutit à une liste vide, le résultat est `null`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2086">If removal results in an empty list, the result is `null`.</span></span> <span data-ttu-id="b0787-2087">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b0787-2087">For example:</span></span>

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

## <a name="shift-operators"></a><span data-ttu-id="b0787-2088">Opérateurs de décalage</span><span class="sxs-lookup"><span data-stu-id="b0787-2088">Shift operators</span></span>

<span data-ttu-id="b0787-2089">Les opérateurs `<<` et `>>` sont utilisés pour effectuer des opérations de décalage de bits.</span><span class="sxs-lookup"><span data-stu-id="b0787-2089">The `<<` and `>>` operators are used to perform bit shifting operations.</span></span>

```antlr
shift_expression
    : additive_expression
    | shift_expression '<<' additive_expression
    | shift_expression right_shift additive_expression
    ;
```

<span data-ttu-id="b0787-2090">Si un opérande d’un *shift_expression* a le type au moment de la compilation `dynamic`, l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2090">If an operand of a *shift_expression* has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="b0787-2091">Dans ce cas, le type au moment de la compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous a lieu au moment de l’exécution à l’aide du type d’exécution des opérandes qui ont le type au moment de la compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2091">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="b0787-2092">Pour une opération de la forme `x << count` ou `x >> count`, la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-2092">For an operation of the form `x << count` or `x >> count`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-2093">Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2093">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="b0787-2094">Lors de la déclaration d’un opérateur de décalage surchargé, le type du premier opérande doit toujours être la classe ou le struct contenant la déclaration d’opérateur, et le type du second opérande doit toujours être `int`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2094">When declaring an overloaded shift operator, the type of the first operand must always be the class or struct containing the operator declaration, and the type of the second operand must always be `int`.</span></span>

<span data-ttu-id="b0787-2095">Les opérateurs de décalage prédéfinis sont répertoriés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b0787-2095">The predefined shift operators are listed below.</span></span>

*  <span data-ttu-id="b0787-2096">Décalage vers la gauche :</span><span class="sxs-lookup"><span data-stu-id="b0787-2096">Shift left:</span></span>

   ```csharp
   int operator <<(int x, int count);
   uint operator <<(uint x, int count);
   long operator <<(long x, int count);
   ulong operator <<(ulong x, int count);
   ```

   <span data-ttu-id="b0787-2097">L’opérateur `<<` décale `x` vers la gauche d’un nombre de bits calculés comme décrit ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b0787-2097">The `<<` operator shifts `x` left by a number of bits computed as described below.</span></span>

   <span data-ttu-id="b0787-2098">Les bits de poids fort en dehors de la plage du type de résultat de `x` sont ignorés, les bits restants sont décalés vers la gauche et les positions de bits vides de poids faible sont définies sur zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-2098">The high-order bits outside the range of the result type of `x` are discarded, the remaining bits are shifted left, and the low-order empty bit positions are set to zero.</span></span>

*  <span data-ttu-id="b0787-2099">Décalage vers la droite :</span><span class="sxs-lookup"><span data-stu-id="b0787-2099">Shift right:</span></span>

   ```csharp
   int operator >>(int x, int count);
   uint operator >>(uint x, int count);
   long operator >>(long x, int count);
   ulong operator >>(ulong x, int count);
   ```

   <span data-ttu-id="b0787-2100">L’opérateur `>>` décale `x` vers la droite d’un certain nombre de bits calculés comme décrit ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b0787-2100">The `>>` operator shifts `x` right by a number of bits computed as described below.</span></span>

   <span data-ttu-id="b0787-2101">Lorsque `x` est de type `int` ou `long`, les bits de poids faible de `x` sont ignorés, les bits restants sont décalés vers la droite, et les positions de bits vides de poids fort ont la valeur zéro si `x` est non négative et défini sur un si `x` est négatif.</span><span class="sxs-lookup"><span data-stu-id="b0787-2101">When `x` is of type `int` or `long`, the low-order bits of `x` are discarded, the remaining bits are shifted right, and the high-order empty bit positions are set to zero if `x` is non-negative and set to one if `x` is negative.</span></span>

   <span data-ttu-id="b0787-2102">Lorsque `x` est de type `uint` ou `ulong`, les bits de poids faible de `x` sont ignorés, les bits restants sont décalés vers la droite, et les positions de bits vides de poids fort sont définies sur zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-2102">When `x` is of type `uint` or `ulong`, the low-order bits of `x` are discarded, the remaining bits are shifted right, and the high-order empty bit positions are set to zero.</span></span>

<span data-ttu-id="b0787-2103">Pour les opérateurs prédéfinis, le nombre de bits à décaler est calculé comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-2103">For the predefined operators, the number of bits to shift is computed as follows:</span></span>

*  <span data-ttu-id="b0787-2104">Lorsque le type de `x` est `int` ou `uint`, le nombre de décalages est donné par les cinq bits de poids faible de `count`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2104">When the type of `x` is `int` or `uint`, the shift count is given by the low-order five bits of `count`.</span></span> <span data-ttu-id="b0787-2105">En d’autres termes, le nombre de décalages est calculé à partir de `count & 0x1F`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2105">In other words, the shift count is computed from `count & 0x1F`.</span></span>
*  <span data-ttu-id="b0787-2106">Lorsque le type de `x` est `long` ou `ulong`, le nombre de décalages est donné par les six bits de poids faible de `count`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2106">When the type of `x` is `long` or `ulong`, the shift count is given by the low-order six bits of `count`.</span></span> <span data-ttu-id="b0787-2107">En d’autres termes, le nombre de décalages est calculé à partir de `count & 0x3F`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2107">In other words, the shift count is computed from `count & 0x3F`.</span></span>

<span data-ttu-id="b0787-2108">Si le nombre de décalages résultant est égal à zéro, les opérateurs de décalage retournent simplement la valeur de `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2108">If the resulting shift count is zero, the shift operators simply return the value of `x`.</span></span>

<span data-ttu-id="b0787-2109">Les opérations de décalage ne provoquent jamais de dépassements de capacité et produisent les mêmes résultats dans des contextes de `checked` et de `unchecked`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2109">Shift operations never cause overflows and produce the same results in `checked` and `unchecked` contexts.</span></span>

<span data-ttu-id="b0787-2110">Lorsque l’opérande gauche de l’opérateur `>>` est d’un type intégral signé, l’opérateur effectue un décalage arithmétique vers la droite, où la valeur du bit le plus significatif (le bit de signe) de l’opérande est propagée aux positions de bit vides de poids fort.</span><span class="sxs-lookup"><span data-stu-id="b0787-2110">When the left operand of the `>>` operator is of a signed integral type, the operator performs an arithmetic shift right wherein the value of the most significant bit (the sign bit) of the operand is propagated to the high-order empty bit positions.</span></span> <span data-ttu-id="b0787-2111">Lorsque l’opérande gauche de l’opérateur `>>` est d’un type intégral non signé, l’opérateur effectue un décalage logique vers la droite où les positions de bits vides d’ordre élevé ont toujours la valeur zéro.</span><span class="sxs-lookup"><span data-stu-id="b0787-2111">When the left operand of the `>>` operator is of an unsigned integral type, the operator performs a logical shift right wherein high-order empty bit positions are always set to zero.</span></span> <span data-ttu-id="b0787-2112">Pour effectuer l’opération inverse de celle déduite du type d’opérande, vous pouvez utiliser des casts explicites.</span><span class="sxs-lookup"><span data-stu-id="b0787-2112">To perform the opposite operation of that inferred from the operand type, explicit casts can be used.</span></span> <span data-ttu-id="b0787-2113">Par exemple, si `x` est une variable de type `int`, l’opération `unchecked((int)((uint)x >> y))` effectue un décalage logique à droite de `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2113">For example, if `x` is a variable of type `int`, the operation `unchecked((int)((uint)x >> y))` performs a logical shift right of `x`.</span></span>

## <a name="relational-and-type-testing-operators"></a><span data-ttu-id="b0787-2114">Opérateurs relationnels et de test de type</span><span class="sxs-lookup"><span data-stu-id="b0787-2114">Relational and type-testing operators</span></span>

<span data-ttu-id="b0787-2115">Les opérateurs `==`, `!=`, `<`, `>`, `<=`, `>=`, `is` et `as` sont appelés opérateurs relationnels et de test de type.</span><span class="sxs-lookup"><span data-stu-id="b0787-2115">The `==`, `!=`, `<`, `>`, `<=`, `>=`, `is` and `as` operators are called the relational and type-testing operators.</span></span>

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

<span data-ttu-id="b0787-2116">L’opérateur `is` est décrit dans [l’opérateur is](expressions.md#the-is-operator) et l’opérateur `as` est décrit dans [l’opérateur as](expressions.md#the-as-operator).</span><span class="sxs-lookup"><span data-stu-id="b0787-2116">The `is` operator is described in [The is operator](expressions.md#the-is-operator) and the `as` operator is described in [The as operator](expressions.md#the-as-operator).</span></span>

<span data-ttu-id="b0787-2117">Les opérateurs `==`, `!=`, `<`, `>`, `<=` et `>=` sont des ***opérateurs de comparaison***.</span><span class="sxs-lookup"><span data-stu-id="b0787-2117">The `==`, `!=`, `<`, `>`, `<=` and `>=` operators are ***comparison operators***.</span></span>

<span data-ttu-id="b0787-2118">Si un opérande d’un opérateur de comparaison a le type `dynamic`au moment de la compilation, l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2118">If an operand of a comparison operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="b0787-2119">Dans ce cas, le type au moment de la compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous a lieu au moment de l’exécution à l’aide du type d’exécution des opérandes qui ont le type au moment de la compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2119">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="b0787-2120">Pour une opération de la forme `x` *op* `y`, où *op* est un opérateur de comparaison, la résolution de surcharge ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-2120">For an operation of the form `x` *op* `y`, where *op* is a comparison operator, overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-2121">Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2121">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="b0787-2122">Les opérateurs de comparaison prédéfinis sont décrits dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2122">The predefined comparison operators are described in the following sections.</span></span> <span data-ttu-id="b0787-2123">Tous les opérateurs de comparaison prédéfinis retournent un résultat de type `bool`, comme décrit dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="b0787-2123">All predefined comparison operators return a result of type `bool`, as described in the following table.</span></span>


| <span data-ttu-id="b0787-2124">__Opération__</span><span class="sxs-lookup"><span data-stu-id="b0787-2124">__Operation__</span></span> | <span data-ttu-id="b0787-2125">__Résultat__</span><span class="sxs-lookup"><span data-stu-id="b0787-2125">__Result__</span></span>                                                       |
|---------------|------------------------------------------------------------------|
| `x == y`      | <span data-ttu-id="b0787-2126">`true` si `x` est égal à `y`, `false` dans le cas contraire</span><span class="sxs-lookup"><span data-stu-id="b0787-2126">`true` if `x` is equal to `y`, `false` otherwise</span></span>                 | 
| `x != y`      | <span data-ttu-id="b0787-2127">`true` si `x` n’est pas égal à `y`, `false` dans le cas contraire</span><span class="sxs-lookup"><span data-stu-id="b0787-2127">`true` if `x` is not equal to `y`, `false` otherwise</span></span>             | 
| `x < y`       | <span data-ttu-id="b0787-2128">`true` si `x` est inférieur à `y`, `false` dans le cas contraire</span><span class="sxs-lookup"><span data-stu-id="b0787-2128">`true` if `x` is less than `y`, `false` otherwise</span></span>                | 
| `x > y`       | <span data-ttu-id="b0787-2129">`true` si `x` est supérieur à `y`, `false` dans le cas contraire</span><span class="sxs-lookup"><span data-stu-id="b0787-2129">`true` if `x` is greater than `y`, `false` otherwise</span></span>             | 
| `x <= y`      | <span data-ttu-id="b0787-2130">`true` si `x` est inférieur ou égal à `y`, `false` dans le cas contraire</span><span class="sxs-lookup"><span data-stu-id="b0787-2130">`true` if `x` is less than or equal to `y`, `false` otherwise</span></span>    | 
| `x >= y`      | <span data-ttu-id="b0787-2131">`true` si `x` est supérieur ou égal à `y`, `false` sinon</span><span class="sxs-lookup"><span data-stu-id="b0787-2131">`true` if `x` is greater than or equal to `y`, `false` otherwise</span></span> | 

### <a name="integer-comparison-operators"></a><span data-ttu-id="b0787-2132">Opérateurs de comparaison d’entiers</span><span class="sxs-lookup"><span data-stu-id="b0787-2132">Integer comparison operators</span></span>

<span data-ttu-id="b0787-2133">Les opérateurs de comparaison d’entiers prédéfinis sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2133">The predefined integer comparison operators are:</span></span>
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

<span data-ttu-id="b0787-2134">Chacun de ces opérateurs compare les valeurs numériques des deux opérandes entiers et retourne une valeur `bool` qui indique si la relation particulière est `true` ou `false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2134">Each of these operators compares the numeric values of the two integer operands and returns a `bool` value that indicates whether the particular relation is `true` or `false`.</span></span>

### <a name="floating-point-comparison-operators"></a><span data-ttu-id="b0787-2135">Opérateurs de comparaison de nombres à virgule flottante</span><span class="sxs-lookup"><span data-stu-id="b0787-2135">Floating-point comparison operators</span></span>

<span data-ttu-id="b0787-2136">Les opérateurs de comparaison prédéfinis en virgule flottante sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2136">The predefined floating-point comparison operators are:</span></span>
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

<span data-ttu-id="b0787-2137">Les opérateurs comparent les opérandes en fonction des règles de la norme IEEE 754 :</span><span class="sxs-lookup"><span data-stu-id="b0787-2137">The operators compare the operands according to the rules of the IEEE 754 standard:</span></span>

*  <span data-ttu-id="b0787-2138">Si l’un des opérandes est NaN, le résultat est `false` pour tous les opérateurs, à l’exception des `!=`, pour lesquels le résultat est `true`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2138">If either operand is NaN, the result is `false` for all operators except `!=`, for which the result is `true`.</span></span> <span data-ttu-id="b0787-2139">Pour deux opérandes, `x != y` produit toujours le même résultat que `!(x == y)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2139">For any two operands, `x != y` always produces the same result as `!(x == y)`.</span></span> <span data-ttu-id="b0787-2140">Toutefois, lorsque l’un des opérandes ou les deux sont NaN, les opérateurs `<`, `>`, `<=`et `>=` ne produisent pas les mêmes résultats que la négation logique de l’opérateur opposé.</span><span class="sxs-lookup"><span data-stu-id="b0787-2140">However, when one or both operands are NaN, the `<`, `>`, `<=`, and `>=` operators do not produce the same results as the logical negation of the opposite operator.</span></span> <span data-ttu-id="b0787-2141">Par exemple, si l’un des `x` et `y` est NaN, `x < y` est `false`, mais `!(x >= y)` est `true`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2141">For example, if either of `x` and `y` is NaN, then `x < y` is `false`, but `!(x >= y)` is `true`.</span></span>
*  <span data-ttu-id="b0787-2142">Quand aucun des opérandes n’est NaN, les opérateurs comparent les valeurs des deux opérandes à virgule flottante en ce qui concerne le classement.</span><span class="sxs-lookup"><span data-stu-id="b0787-2142">When neither operand is NaN, the operators compare the values of the two floating-point operands with respect to the ordering</span></span>

   ```csharp
   -inf < -max < ... < -min < -0.0 == +0.0 < +min < ... < +max < +inf
   ```

   <span data-ttu-id="b0787-2143">où `min` et `max` sont les valeurs finies positives les plus petites et les plus grandes qui peuvent être représentées dans le format à virgule flottante donné.</span><span class="sxs-lookup"><span data-stu-id="b0787-2143">where `min` and `max` are the smallest and largest positive finite values that can be represented in the given floating-point format.</span></span> <span data-ttu-id="b0787-2144">Les effets notables de ce classement sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2144">Notable effects of this ordering are:</span></span>
   * <span data-ttu-id="b0787-2145">Les zéros positifs et négatifs sont considérés comme égaux.</span><span class="sxs-lookup"><span data-stu-id="b0787-2145">Negative and positive zeros are considered equal.</span></span>
   * <span data-ttu-id="b0787-2146">Un infini négatif est considéré comme inférieur à toutes les autres valeurs, mais égal à un autre infini négatif.</span><span class="sxs-lookup"><span data-stu-id="b0787-2146">A negative infinity is considered less than all other values, but equal to another negative infinity.</span></span>
   * <span data-ttu-id="b0787-2147">Un infini positif est considéré comme supérieur à toutes les autres valeurs, mais égal à un autre infini positif.</span><span class="sxs-lookup"><span data-stu-id="b0787-2147">A positive infinity is considered greater than all other values, but equal to another positive infinity.</span></span>

### <a name="decimal-comparison-operators"></a><span data-ttu-id="b0787-2148">Opérateurs de comparaison décimale</span><span class="sxs-lookup"><span data-stu-id="b0787-2148">Decimal comparison operators</span></span>

<span data-ttu-id="b0787-2149">Les opérateurs de comparaison décimaux prédéfinis sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2149">The predefined decimal comparison operators are:</span></span>
```csharp
bool operator ==(decimal x, decimal y);
bool operator !=(decimal x, decimal y);
bool operator <(decimal x, decimal y);
bool operator >(decimal x, decimal y);
bool operator <=(decimal x, decimal y);
bool operator >=(decimal x, decimal y);
```

<span data-ttu-id="b0787-2150">Chacun de ces opérateurs compare les valeurs numériques des deux opérandes décimaux et retourne une valeur `bool` qui indique si la relation particulière est `true` ou `false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2150">Each of these operators compares the numeric values of the two decimal operands and returns a `bool` value that indicates whether the particular relation is `true` or `false`.</span></span> <span data-ttu-id="b0787-2151">Chaque comparaison décimale équivaut à utiliser l’opérateur relationnel ou d’égalité correspondant de type `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2151">Each decimal comparison is equivalent to using the corresponding relational or equality operator of type `System.Decimal`.</span></span>

### <a name="boolean-equality-operators"></a><span data-ttu-id="b0787-2152">Opérateurs d’égalité booléennes</span><span class="sxs-lookup"><span data-stu-id="b0787-2152">Boolean equality operators</span></span>

<span data-ttu-id="b0787-2153">Les opérateurs d’égalité booléenne prédéfinis sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2153">The predefined boolean equality operators are:</span></span>
```csharp
bool operator ==(bool x, bool y);
bool operator !=(bool x, bool y);
```

<span data-ttu-id="b0787-2154">Le résultat de `==` est `true` si `x` et `y` sont `true` ou si `x` et `y` sont `false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2154">The result of `==` is `true` if both `x` and `y` are `true` or if both `x` and `y` are `false`.</span></span> <span data-ttu-id="b0787-2155">Sinon, le résultat est `false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2155">Otherwise, the result is `false`.</span></span>

<span data-ttu-id="b0787-2156">Le résultat de `!=` est `false` si `x` et `y` sont `true` ou si `x` et `y` sont `false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2156">The result of `!=` is `false` if both `x` and `y` are `true` or if both `x` and `y` are `false`.</span></span> <span data-ttu-id="b0787-2157">Sinon, le résultat est `true`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2157">Otherwise, the result is `true`.</span></span> <span data-ttu-id="b0787-2158">Quand les opérandes sont de type `bool`, l’opérateur `!=` produit le même résultat que l’opérateur `^`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2158">When the operands are of type `bool`, the `!=` operator produces the same result as the `^` operator.</span></span>

### <a name="enumeration-comparison-operators"></a><span data-ttu-id="b0787-2159">Opérateurs de comparaison d’énumération</span><span class="sxs-lookup"><span data-stu-id="b0787-2159">Enumeration comparison operators</span></span>

<span data-ttu-id="b0787-2160">Chaque type d’énumération fournit implicitement les opérateurs de comparaison prédéfinis suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2160">Every enumeration type implicitly provides the following predefined comparison operators:</span></span>
```csharp
bool operator ==(E x, E y);
bool operator !=(E x, E y);
bool operator <(E x, E y);
bool operator >(E x, E y);
bool operator <=(E x, E y);
bool operator >=(E x, E y);
```

<span data-ttu-id="b0787-2161">Le résultat de l’évaluation de `x op y`, où `x` et `y` sont des expressions d’un type d’énumération `E` avec un type sous-jacent `U`, et `op` est l’un des opérateurs de comparaison, est exactement le même que l’évaluation de `((U)x) op ((U)y)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2161">The result of evaluating `x op y`, where `x` and `y` are expressions of an enumeration type `E` with an underlying type `U`, and `op` is one of the comparison operators, is exactly the same as evaluating `((U)x) op ((U)y)`.</span></span> <span data-ttu-id="b0787-2162">En d’autres termes, les opérateurs de comparaison de type énumération comparent simplement les valeurs intégrales sous-jacentes des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2162">In other words, the enumeration type comparison operators simply compare the underlying integral values of the two operands.</span></span>

### <a name="reference-type-equality-operators"></a><span data-ttu-id="b0787-2163">Opérateurs d’égalité de type référence</span><span class="sxs-lookup"><span data-stu-id="b0787-2163">Reference type equality operators</span></span>

<span data-ttu-id="b0787-2164">Les opérateurs d’égalité de type référence prédéfinis sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2164">The predefined reference type equality operators are:</span></span>
```csharp
bool operator ==(object x, object y);
bool operator !=(object x, object y);
```

<span data-ttu-id="b0787-2165">Les opérateurs retournent le résultat de la comparaison des deux références pour l’égalité ou l’absence d’égalité.</span><span class="sxs-lookup"><span data-stu-id="b0787-2165">The operators return the result of comparing the two references for equality or non-equality.</span></span>

<span data-ttu-id="b0787-2166">Étant donné que les opérateurs d’égalité de type référence prédéfinis acceptent les opérandes de type `object`, ils s’appliquent à tous les types qui ne déclarent pas les membres `operator ==` et `operator !=` applicables.</span><span class="sxs-lookup"><span data-stu-id="b0787-2166">Since the predefined reference type equality operators accept operands of type `object`, they apply to all types that do not declare applicable `operator ==` and `operator !=` members.</span></span> <span data-ttu-id="b0787-2167">À l’inverse, tous les opérateurs d’égalité définis par l’utilisateur applicables masquent effectivement les opérateurs d’égalité de type référence prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="b0787-2167">Conversely, any applicable user-defined equality operators effectively hide the predefined reference type equality operators.</span></span>

<span data-ttu-id="b0787-2168">Les opérateurs d’égalité de type référence prédéfinis nécessitent l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2168">The predefined reference type equality operators require one of the following:</span></span>

*  <span data-ttu-id="b0787-2169">Les deux opérandes sont une valeur d’un type connu comme étant un *reference_type* ou le `null`littéral.</span><span class="sxs-lookup"><span data-stu-id="b0787-2169">Both operands are a value of a type known to be a *reference_type* or the literal `null`.</span></span> <span data-ttu-id="b0787-2170">En outre, une conversion de référence explicite ([conversions de références explicites](conversions.md#explicit-reference-conversions)) existe à partir du type de l’un des opérandes vers le type de l’autre opérande.</span><span class="sxs-lookup"><span data-stu-id="b0787-2170">Furthermore, an explicit reference conversion ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) exists from the type of either operand to the type of the other operand.</span></span>
*  <span data-ttu-id="b0787-2171">Un opérande est une valeur de type `T` où `T` est un *type_parameter* et l’autre opérande est le littéral `null`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2171">One operand is a value of type `T` where `T` is a *type_parameter* and the other operand is the literal `null`.</span></span> <span data-ttu-id="b0787-2172">En outre `T` n’a pas la contrainte de type valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2172">Furthermore `T` does not have the value type constraint.</span></span>

<span data-ttu-id="b0787-2173">À moins que l’une de ces conditions ne soit vraie, une erreur de liaison au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-2173">Unless one of these conditions are true, a binding-time error occurs.</span></span> <span data-ttu-id="b0787-2174">Les implications notables de ces règles sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-2174">Notable implications of these rules are:</span></span>

*  <span data-ttu-id="b0787-2175">Il s’agit d’une erreur de liaison au moment de la liaison pour utiliser les opérateurs d’égalité de type référence prédéfinis pour comparer deux références qui sont connues pour être différentes au moment de la liaison.</span><span class="sxs-lookup"><span data-stu-id="b0787-2175">It is a binding-time error to use the predefined reference type equality operators to compare two references that are known to be different at binding-time.</span></span> <span data-ttu-id="b0787-2176">Par exemple, si les types au moment de la liaison des opérandes sont deux types de classe `A` et `B`, et si ni `A` ni `B` dérivent de l’autre, il serait impossible pour les deux opérandes de référencer le même objet.</span><span class="sxs-lookup"><span data-stu-id="b0787-2176">For example, if the binding-time types of the operands are two class types `A` and `B`, and if neither `A` nor `B` derives from the other, then it would be impossible for the two operands to reference the same object.</span></span> <span data-ttu-id="b0787-2177">Ainsi, l’opération est considérée comme une erreur au moment de la liaison.</span><span class="sxs-lookup"><span data-stu-id="b0787-2177">Thus, the operation is considered a binding-time error.</span></span>
*  <span data-ttu-id="b0787-2178">Les opérateurs d’égalité de type référence prédéfinis n’autorisent pas la comparaison des opérandes de type valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2178">The predefined reference type equality operators do not permit value type operands to be compared.</span></span> <span data-ttu-id="b0787-2179">Par conséquent, à moins qu’un type struct ne déclare ses propres opérateurs d’égalité, il n’est pas possible de comparer les valeurs de ce type struct.</span><span class="sxs-lookup"><span data-stu-id="b0787-2179">Therefore, unless a struct type declares its own equality operators, it is not possible to compare values of that struct type.</span></span>
*  <span data-ttu-id="b0787-2180">Les opérateurs d’égalité de type référence prédéfinis n’entraînent jamais la levée des opérations de boxing pour leurs opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2180">The predefined reference type equality operators never cause boxing operations to occur for their operands.</span></span> <span data-ttu-id="b0787-2181">Il serait inutile d’effectuer ces opérations de boxing, car les références aux instances boxed nouvellement allouées seraient nécessairement différentes de toutes les autres références.</span><span class="sxs-lookup"><span data-stu-id="b0787-2181">It would be meaningless to perform such boxing operations, since references to the newly allocated boxed instances would necessarily differ from all other references.</span></span>
*  <span data-ttu-id="b0787-2182">Si un opérande d’un type de paramètre de type `T` est comparé à `null`et que le type de `T` au moment de l’exécution est un type valeur, le résultat de la comparaison est `false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2182">If an operand of a type parameter type `T` is compared to `null`, and the run-time type of `T` is a value type, the result of the comparison is `false`.</span></span>

<span data-ttu-id="b0787-2183">L’exemple suivant vérifie si un argument d’un type de paramètre de type sans contrainte est `null`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2183">The following example checks whether an argument of an unconstrained type parameter type is `null`.</span></span>
```csharp
class C<T>
{
    void F(T x) {
        if (x == null) throw new ArgumentNullException();
        ...
    }
}
```

<span data-ttu-id="b0787-2184">La construction `x == null` est autorisée même si `T` peut représenter un type valeur, et le résultat est simplement défini pour être `false` quand `T` est un type valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2184">The `x == null` construct is permitted even though `T` could represent a value type, and the result is simply defined to be `false` when `T` is a value type.</span></span>

<span data-ttu-id="b0787-2185">Pour une opération de la forme `x == y` ou `x != y`, s’il existe des `operator ==` ou des `operator !=` applicables, les règles de résolution de surcharge d’opérateur ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) sélectionnent cet opérateur au lieu de l’opérateur d’égalité de type référence prédéfini.</span><span class="sxs-lookup"><span data-stu-id="b0787-2185">For an operation of the form `x == y` or `x != y`, if any applicable `operator ==` or `operator !=` exists, the operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) rules will select that operator instead of the predefined reference type equality operator.</span></span> <span data-ttu-id="b0787-2186">Toutefois, il est toujours possible de sélectionner l’opérateur d’égalité de type référence prédéfini en effectuant un cast explicite de l’un des opérandes ou des deux en type `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2186">However, it is always possible to select the predefined reference type equality operator by explicitly casting one or both of the operands to type `object`.</span></span> <span data-ttu-id="b0787-2187">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2187">The example</span></span>
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
<span data-ttu-id="b0787-2188">génère la sortie</span><span class="sxs-lookup"><span data-stu-id="b0787-2188">produces the output</span></span>
```console
True
False
False
False
```

<span data-ttu-id="b0787-2189">Les variables `s` et `t` font référence à deux instances `string` distinctes contenant les mêmes caractères.</span><span class="sxs-lookup"><span data-stu-id="b0787-2189">The `s` and `t` variables refer to two distinct `string` instances containing the same characters.</span></span> <span data-ttu-id="b0787-2190">La première comparaison génère `True`, car l’opérateur d’égalité de chaînes prédéfini ([opérateurs d’égalité de chaînes](expressions.md#string-equality-operators)) est sélectionné lorsque les deux opérandes sont de type `string`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2190">The first comparison outputs `True` because the predefined string equality operator ([String equality operators](expressions.md#string-equality-operators)) is selected when both operands are of type `string`.</span></span> <span data-ttu-id="b0787-2191">Les autres comparaisons `False`nt toutes les sorties, car l’opérateur d’égalité de type référence prédéfini est sélectionné quand l’un des opérandes ou les deux sont de type `object`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2191">The remaining comparisons all output `False` because the predefined reference type equality operator is selected when one or both of the operands are of type `object`.</span></span>

<span data-ttu-id="b0787-2192">Notez que la technique ci-dessus n’est pas significative pour les types valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2192">Note that the above technique is not meaningful for value types.</span></span> <span data-ttu-id="b0787-2193">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2193">The example</span></span>
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
<span data-ttu-id="b0787-2194">génère `False`, car les casts créent des références à deux instances distinctes de valeurs de `int` boxed.</span><span class="sxs-lookup"><span data-stu-id="b0787-2194">outputs `False` because the casts create references to two separate instances of boxed `int` values.</span></span>

### <a name="string-equality-operators"></a><span data-ttu-id="b0787-2195">Opérateurs d’égalité de chaînes</span><span class="sxs-lookup"><span data-stu-id="b0787-2195">String equality operators</span></span>

<span data-ttu-id="b0787-2196">Les opérateurs d’égalité de chaînes prédéfinis sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2196">The predefined string equality operators are:</span></span>
```csharp
bool operator ==(string x, string y);
bool operator !=(string x, string y);
```

<span data-ttu-id="b0787-2197">Deux valeurs `string` sont considérées comme égales quand l’une des conditions suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="b0787-2197">Two `string` values are considered equal when one of the following is true:</span></span>

*  <span data-ttu-id="b0787-2198">Les deux valeurs sont `null`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2198">Both values are `null`.</span></span>
*  <span data-ttu-id="b0787-2199">Les deux valeurs sont des références non null aux instances de chaîne qui ont des longueurs identiques et des caractères identiques dans chaque position de caractère.</span><span class="sxs-lookup"><span data-stu-id="b0787-2199">Both values are non-null references to string instances that have identical lengths and identical characters in each character position.</span></span>

<span data-ttu-id="b0787-2200">Les opérateurs d’égalité de chaînes comparent les valeurs de chaîne plutôt que les références de chaîne.</span><span class="sxs-lookup"><span data-stu-id="b0787-2200">The string equality operators compare string values rather than string references.</span></span> <span data-ttu-id="b0787-2201">Lorsque deux instances de chaîne distinctes contiennent exactement la même séquence de caractères, les valeurs des chaînes sont égales, mais les références sont différentes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2201">When two separate string instances contain the exact same sequence of characters, the values of the strings are equal, but the references are different.</span></span> <span data-ttu-id="b0787-2202">Comme décrit dans [opérateurs d’égalité de type référence](expressions.md#reference-type-equality-operators), les opérateurs d’égalité de type référence peuvent être utilisés pour comparer des références de chaîne à la place de valeurs de chaîne.</span><span class="sxs-lookup"><span data-stu-id="b0787-2202">As described in [Reference type equality operators](expressions.md#reference-type-equality-operators), the reference type equality operators can be used to compare string references instead of string values.</span></span>

### <a name="delegate-equality-operators"></a><span data-ttu-id="b0787-2203">Opérateurs d’égalité de délégué</span><span class="sxs-lookup"><span data-stu-id="b0787-2203">Delegate equality operators</span></span>

<span data-ttu-id="b0787-2204">Chaque type délégué fournit implicitement les opérateurs de comparaison prédéfinis suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2204">Every delegate type implicitly provides the following predefined comparison operators:</span></span>

```csharp
bool operator ==(System.Delegate x, System.Delegate y);
bool operator !=(System.Delegate x, System.Delegate y);
```

<span data-ttu-id="b0787-2205">Deux instances de délégué sont considérées comme étant égales comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-2205">Two delegate instances are considered equal as follows:</span></span>

*  <span data-ttu-id="b0787-2206">Si l’une des instances de délégué est `null`, elles sont égales si et seulement si les deux sont `null`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2206">If either of the delegate instances is `null`, they are equal if and only if both are `null`.</span></span>
*  <span data-ttu-id="b0787-2207">Si les délégués ont un type au moment de l’exécution différent, ils ne sont jamais égaux.</span><span class="sxs-lookup"><span data-stu-id="b0787-2207">If the delegates have different run-time type they are never equal.</span></span>
*  <span data-ttu-id="b0787-2208">Si les deux instances de délégué ont une liste d’appel ([déclarations de délégué](delegates.md#delegate-declarations)), ces instances sont égales si et seulement si leurs listes d’appel sont de la même longueur, et que chaque entrée de la liste d’appel de l’une d’elles est égale (comme indiqué ci-dessous) à l’entrée correspondante, dans l’ordre, dans la liste d’appel de l’autre.</span><span class="sxs-lookup"><span data-stu-id="b0787-2208">If both of the delegate instances have an invocation list ([Delegate declarations](delegates.md#delegate-declarations)), those instances are equal if and only if their invocation lists are the same length, and each entry in one's invocation list is equal (as defined below) to the corresponding entry, in order, in the other's invocation list.</span></span>

<span data-ttu-id="b0787-2209">Les règles suivantes régissent l’égalité des entrées de la liste d’appel :</span><span class="sxs-lookup"><span data-stu-id="b0787-2209">The following rules govern the equality of invocation list entries:</span></span>

*  <span data-ttu-id="b0787-2210">Si deux entrées de liste d’appel font toutes deux référence à la même méthode statique, les entrées sont égales.</span><span class="sxs-lookup"><span data-stu-id="b0787-2210">If two invocation list entries both refer to the same static method then the entries are equal.</span></span>
*  <span data-ttu-id="b0787-2211">Si deux entrées de liste d’appel font toutes deux référence à la même méthode non statique sur le même objet cible (tel que défini par les opérateurs d’égalité de référence), les entrées sont égales.</span><span class="sxs-lookup"><span data-stu-id="b0787-2211">If two invocation list entries both refer to the same non-static method on the same target object (as defined by the reference equality operators) then the entries are equal.</span></span>
*  <span data-ttu-id="b0787-2212">Les entrées de la liste d’invocation générées à partir de l’évaluation d' *anonymous_method_expression*s ou d' *lambda_expression*s sémantiquement identiques avec le même jeu d’instances de variable externe capturées sont autorisées (mais pas obligatoires) pour être égales.</span><span class="sxs-lookup"><span data-stu-id="b0787-2212">Invocation list entries produced from evaluation of semantically identical *anonymous_method_expression*s or *lambda_expression*s with the same (possibly empty) set of captured outer variable instances are permitted (but not required) to be equal.</span></span>

### <a name="equality-operators-and-null"></a><span data-ttu-id="b0787-2213">Opérateurs d’égalité et null</span><span class="sxs-lookup"><span data-stu-id="b0787-2213">Equality operators and null</span></span>

<span data-ttu-id="b0787-2214">Les opérateurs `==` et `!=` permettent à un opérande d’être une valeur d’un type Nullable et l’autre est le littéral `null`, même si aucun opérateur prédéfini ou défini par l’utilisateur (sous forme non levée) n’existe pour l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-2214">The `==` and `!=` operators permit one operand to be a value of a nullable type and the other to be the `null` literal, even if no predefined or user-defined operator (in unlifted or lifted form) exists for the operation.</span></span>

<span data-ttu-id="b0787-2215">Pour une opération de l’une des formes</span><span class="sxs-lookup"><span data-stu-id="b0787-2215">For an operation of one of the forms</span></span>
```csharp
x == null
null == x
x != null
null != x
```
<span data-ttu-id="b0787-2216">où `x` est une expression d’un type Nullable, si la résolution de surcharge d’opérateur ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) ne parvient pas à trouver un opérateur applicable, le résultat est à la place calculé à partir de la propriété `HasValue` de `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2216">where `x` is an expression of a nullable type, if operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) fails to find an applicable operator, the result is instead computed from the `HasValue` property of `x`.</span></span> <span data-ttu-id="b0787-2217">Plus précisément, les deux premières formes sont converties en `!x.HasValue`, et les deux dernières formes sont traduites en `x.HasValue`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2217">Specifically, the first two forms are translated into `!x.HasValue`, and last two forms are translated into `x.HasValue`.</span></span>

### <a name="the-is-operator"></a><span data-ttu-id="b0787-2218">Opérateur is</span><span class="sxs-lookup"><span data-stu-id="b0787-2218">The is operator</span></span>

<span data-ttu-id="b0787-2219">L’opérateur `is` est utilisé pour vérifier dynamiquement si le type d’exécution d’un objet est compatible avec un type donné.</span><span class="sxs-lookup"><span data-stu-id="b0787-2219">The `is` operator is used to dynamically check if the run-time type of an object is compatible with a given type.</span></span> <span data-ttu-id="b0787-2220">Le résultat de l’opération `E is T`, où `E` est une expression et `T` est un type, est une valeur booléenne indiquant si `E` peut être converti en type `T` par une conversion de référence, une conversion boxing ou une conversion unboxing.</span><span class="sxs-lookup"><span data-stu-id="b0787-2220">The result of the operation `E is T`, where `E` is an expression and `T` is a type, is a boolean value indicating whether `E` can successfully be converted to type `T` by a reference conversion, a boxing conversion, or an unboxing conversion.</span></span> <span data-ttu-id="b0787-2221">L’opération est évaluée comme suit, après que les arguments de type ont été substitués à tous les paramètres de type :</span><span class="sxs-lookup"><span data-stu-id="b0787-2221">The operation is evaluated as follows, after type arguments have been substituted for all type parameters:</span></span>

*  <span data-ttu-id="b0787-2222">Si `E` est une fonction anonyme, une erreur se produit au moment de la compilation</span><span class="sxs-lookup"><span data-stu-id="b0787-2222">If `E` is an anonymous function, a compile-time error occurs</span></span>
*  <span data-ttu-id="b0787-2223">Si `E` est un groupe de méthodes ou le littéral `null`, de si le type de `E` est un type référence ou un type Nullable et que la valeur de `E` est null, le résultat est false.</span><span class="sxs-lookup"><span data-stu-id="b0787-2223">If `E` is a method group or the `null` literal, of if the type of `E` is a reference type or a nullable type and the value of `E` is null, the result is false.</span></span>
*  <span data-ttu-id="b0787-2224">Dans le cas contraire, laissez `D` représenter le type dynamique de `E` comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-2224">Otherwise, let `D` represent the dynamic type of `E` as follows:</span></span>
   * <span data-ttu-id="b0787-2225">Si le type de `E` est un type référence, `D` est le type au moment de l’exécution de la référence de l’instance par `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2225">If the type of `E` is a reference type, `D` is the run-time type of the instance reference by `E`.</span></span>
   * <span data-ttu-id="b0787-2226">Si le type de `E` est un type Nullable, `D` est le type sous-jacent de ce type Nullable.</span><span class="sxs-lookup"><span data-stu-id="b0787-2226">If the type of `E` is a nullable type, `D` is the underlying type of that nullable type.</span></span>
   * <span data-ttu-id="b0787-2227">Si le type de `E` est un type valeur qui n’autorise pas les valeurs NULL, `D` est le type de `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2227">If the type of `E` is a non-nullable value type, `D` is the type of `E`.</span></span>
*  <span data-ttu-id="b0787-2228">Le résultat de l’opération dépend `D` et `T` comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-2228">The result of the operation depends on `D` and `T` as follows:</span></span>
   * <span data-ttu-id="b0787-2229">Si `T` est un type référence, le résultat est true si `D` et `T` sont du même type, si `D` est un type référence et une conversion de référence implicite de `D` à `T` existe, ou si `D` est un type valeur et qu’une conversion boxing de `D` en `T` existe.</span><span class="sxs-lookup"><span data-stu-id="b0787-2229">If `T` is a reference type, the result is true if `D` and `T` are the same type, if `D` is a reference type and an implicit reference conversion from `D` to `T` exists, or if `D` is a value type and a boxing conversion from `D` to `T` exists.</span></span>
   * <span data-ttu-id="b0787-2230">Si `T` est un type Nullable, le résultat est true si `D` est le type sous-jacent de `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2230">If `T` is a nullable type, the result is true if `D` is the underlying type of `T`.</span></span>
   * <span data-ttu-id="b0787-2231">Si `T` est un type valeur qui n’autorise pas les valeurs NULL, le résultat est true si `D` et `T` sont du même type.</span><span class="sxs-lookup"><span data-stu-id="b0787-2231">If `T` is a non-nullable value type, the result is true if `D` and `T` are the same type.</span></span>
   * <span data-ttu-id="b0787-2232">Dans le cas contraire, le résultat est false.</span><span class="sxs-lookup"><span data-stu-id="b0787-2232">Otherwise, the result is false.</span></span>

<span data-ttu-id="b0787-2233">Notez que les conversions définies par l’utilisateur ne sont pas prises en compte par l’opérateur `is`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2233">Note that user defined conversions, are not considered by the `is` operator.</span></span>

### <a name="the-as-operator"></a><span data-ttu-id="b0787-2234">Opérateur as</span><span class="sxs-lookup"><span data-stu-id="b0787-2234">The as operator</span></span>

<span data-ttu-id="b0787-2235">L’opérateur `as` est utilisé pour convertir explicitement une valeur en un type référence ou un type Nullable donné.</span><span class="sxs-lookup"><span data-stu-id="b0787-2235">The `as` operator is used to explicitly convert a value to a given reference type or nullable type.</span></span> <span data-ttu-id="b0787-2236">Contrairement à une expression de cast ([expressions de cast](expressions.md#cast-expressions)), l’opérateur `as` ne lève jamais d’exception.</span><span class="sxs-lookup"><span data-stu-id="b0787-2236">Unlike a cast expression ([Cast expressions](expressions.md#cast-expressions)), the `as` operator never throws an exception.</span></span> <span data-ttu-id="b0787-2237">Au lieu de cela, si la conversion indiquée n’est pas possible, la valeur résultante est `null`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2237">Instead, if the indicated conversion is not possible, the resulting value is `null`.</span></span>

<span data-ttu-id="b0787-2238">Dans une opération de la forme `E as T`, `E` doit être une expression et `T` doit être un type référence, un paramètre de type connu comme étant un type référence ou un type Nullable.</span><span class="sxs-lookup"><span data-stu-id="b0787-2238">In an operation of the form `E as T`, `E` must be an expression and `T` must be a reference type, a type parameter known to be a reference type, or a nullable type.</span></span> <span data-ttu-id="b0787-2239">En outre, au moins l’une des conditions suivantes doit être vraie, sinon une erreur de compilation se produit :</span><span class="sxs-lookup"><span data-stu-id="b0787-2239">Furthermore, at least one of the following must be true, or otherwise a compile-time error occurs:</span></span>

*  <span data-ttu-id="b0787-2240">Une identité ([conversion d’identité](conversions.md#identity-conversion)), un Nullable implicite ([conversions implicites Nullable](conversions.md#implicit-nullable-conversions)), une référence implicite (conversions de[référence implicites](conversions.md#implicit-reference-conversions)), une conversion boxing ([conversions boxing](conversions.md#boxing-conversions)), un Nullable explicite ([conversions](conversions.md#explicit-nullable-conversions)explicites Nullable), une référence explicite ([conversions de références explicites](conversions.md#explicit-reference-conversions)) ou une `T``E` conversion unboxing ([conversions unboxing](conversions.md#unboxing-conversions)) est</span><span class="sxs-lookup"><span data-stu-id="b0787-2240">An identity ([Identity conversion](conversions.md#identity-conversion)), implicit nullable ([Implicit nullable conversions](conversions.md#implicit-nullable-conversions)), implicit reference ([Implicit reference conversions](conversions.md#implicit-reference-conversions)), boxing ([Boxing conversions](conversions.md#boxing-conversions)), explicit nullable ([Explicit nullable conversions](conversions.md#explicit-nullable-conversions)), explicit reference ([Explicit reference conversions](conversions.md#explicit-reference-conversions)), or unboxing ([Unboxing conversions](conversions.md#unboxing-conversions)) conversion exists from `E` to `T`.</span></span>
*  <span data-ttu-id="b0787-2241">Le type de `E` ou `T` est un type ouvert.</span><span class="sxs-lookup"><span data-stu-id="b0787-2241">The type of `E` or `T` is an open type.</span></span>
*  <span data-ttu-id="b0787-2242">`E` est le littéral `null`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2242">`E` is the `null` literal.</span></span>

<span data-ttu-id="b0787-2243">Si le type au moment de la compilation de `E` n’est pas `dynamic`, l’opération `E as T` produit le même résultat que</span><span class="sxs-lookup"><span data-stu-id="b0787-2243">If the compile-time type of `E` is not `dynamic`, the operation `E as T` produces the same result as</span></span>
```csharp
E is T ? (T)(E) : (T)null
```
<span data-ttu-id="b0787-2244">sauf que `E` n’est évalué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-2244">except that `E` is only evaluated once.</span></span> <span data-ttu-id="b0787-2245">Le compilateur peut être supposé optimiser `E as T` pour exécuter au plus un contrôle de type dynamique au lieu des deux contrôles de type dynamique qui sont implicites dans le cadre de l’expansion ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="b0787-2245">The compiler can be expected to optimize `E as T` to perform at most one dynamic type check as opposed to the two dynamic type checks implied by the expansion above.</span></span>

<span data-ttu-id="b0787-2246">Si le type de `E` au moment de la compilation est `dynamic`, contrairement à l’opérateur de cast, l’opérateur `as` n’est pas lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2246">If the compile-time type of `E` is `dynamic`, unlike the cast operator the `as` operator is not dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="b0787-2247">Par conséquent, dans ce cas, le développement est le suivant :</span><span class="sxs-lookup"><span data-stu-id="b0787-2247">Therefore the expansion in this case is:</span></span>
```csharp
E is T ? (T)(object)(E) : (T)null
```

<span data-ttu-id="b0787-2248">Notez que certaines conversions, telles que les conversions définies par l’utilisateur, ne sont pas possibles avec l’opérateur `as` et doivent plutôt être effectuées à l’aide d’expressions de cast.</span><span class="sxs-lookup"><span data-stu-id="b0787-2248">Note that some conversions, such as user defined conversions, are not possible with the `as` operator and should instead be performed using cast expressions.</span></span>

<span data-ttu-id="b0787-2249">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2249">In the example</span></span>
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
<span data-ttu-id="b0787-2250">le paramètre de type `T` de `G` est connu comme un type référence, car il a la contrainte de classe.</span><span class="sxs-lookup"><span data-stu-id="b0787-2250">the type parameter `T` of `G` is known to be a reference type, because it has the class constraint.</span></span> <span data-ttu-id="b0787-2251">Toutefois, le paramètre de type `U` de `H` n’est pas ; par conséquent, l’utilisation de l’opérateur `as` dans `H` n’est pas autorisée.</span><span class="sxs-lookup"><span data-stu-id="b0787-2251">The type parameter `U` of `H` is not however; hence the use of the `as` operator in `H` is disallowed.</span></span>

## <a name="logical-operators"></a><span data-ttu-id="b0787-2252">Opérateurs logiques.</span><span class="sxs-lookup"><span data-stu-id="b0787-2252">Logical operators</span></span>

<span data-ttu-id="b0787-2253">Les opérateurs `&`, `^`et `|` sont appelés opérateurs logiques.</span><span class="sxs-lookup"><span data-stu-id="b0787-2253">The `&`, `^`, and `|` operators are called the logical operators.</span></span>

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

<span data-ttu-id="b0787-2254">Si un opérande d’un opérateur logique a le type au moment de la compilation `dynamic`, l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2254">If an operand of a logical operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="b0787-2255">Dans ce cas, le type au moment de la compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous a lieu au moment de l’exécution à l’aide du type d’exécution des opérandes qui ont le type au moment de la compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2255">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="b0787-2256">Pour une opération de la forme `x op y`, où `op` est l’un des opérateurs logiques, la résolution de surcharge ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique.</span><span class="sxs-lookup"><span data-stu-id="b0787-2256">For an operation of the form `x op y`, where `op` is one of the logical operators, overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="b0787-2257">Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2257">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="b0787-2258">Les opérateurs logiques prédéfinis sont décrits dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2258">The predefined logical operators are described in the following sections.</span></span>

### <a name="integer-logical-operators"></a><span data-ttu-id="b0787-2259">Opérateurs logiques Integer</span><span class="sxs-lookup"><span data-stu-id="b0787-2259">Integer logical operators</span></span>

<span data-ttu-id="b0787-2260">Les opérateurs logiques d’entiers prédéfinis sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2260">The predefined integer logical operators are:</span></span>
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

<span data-ttu-id="b0787-2261">L’opérateur `&` calcule le `AND` logique au niveau du bit des deux opérandes, l’opérateur `|` calcule le `OR` logique au niveau du bit des deux opérandes, et l’opérateur `^` calcule la `OR` logique exclusive au niveau du bit des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2261">The `&` operator computes the bitwise logical `AND` of the two operands, the `|` operator computes the bitwise logical `OR` of the two operands, and the `^` operator computes the bitwise logical exclusive `OR` of the two operands.</span></span> <span data-ttu-id="b0787-2262">Aucun dépassement de capacité n’est possible à partir de ces opérations.</span><span class="sxs-lookup"><span data-stu-id="b0787-2262">No overflows are possible from these operations.</span></span>

### <a name="enumeration-logical-operators"></a><span data-ttu-id="b0787-2263">Opérateurs logiques d’énumération</span><span class="sxs-lookup"><span data-stu-id="b0787-2263">Enumeration logical operators</span></span>

<span data-ttu-id="b0787-2264">Chaque type d’énumération `E` fournit implicitement les opérateurs logiques prédéfinis suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2264">Every enumeration type `E` implicitly provides the following predefined logical operators:</span></span>

```csharp
E operator &(E x, E y);
E operator |(E x, E y);
E operator ^(E x, E y);
```

<span data-ttu-id="b0787-2265">Le résultat de l’évaluation de `x op y`, où `x` et `y` sont des expressions d’un type d’énumération `E` avec un type sous-jacent `U`, et `op` est l’un des opérateurs logiques, est exactement le même que l’évaluation de `(E)((U)x op (U)y)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2265">The result of evaluating `x op y`, where `x` and `y` are expressions of an enumeration type `E` with an underlying type `U`, and `op` is one of the logical operators, is exactly the same as evaluating `(E)((U)x op (U)y)`.</span></span> <span data-ttu-id="b0787-2266">En d’autres termes, les opérateurs logiques de type énumération effectuent simplement l’opération logique sur le type sous-jacent des deux opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2266">In other words, the enumeration type logical operators simply perform the logical operation on the underlying type of the two operands.</span></span>

### <a name="boolean-logical-operators"></a><span data-ttu-id="b0787-2267">Opérateurs logiques booléens</span><span class="sxs-lookup"><span data-stu-id="b0787-2267">Boolean logical operators</span></span>

<span data-ttu-id="b0787-2268">Les opérateurs logiques booléens prédéfinis sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2268">The predefined boolean logical operators are:</span></span>
```csharp
bool operator &(bool x, bool y);
bool operator |(bool x, bool y);
bool operator ^(bool x, bool y);
```

<span data-ttu-id="b0787-2269">Le résultat de `x & y` est `true` si `x` et `y` sont `true`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2269">The result of `x & y` is `true` if both `x` and `y` are `true`.</span></span> <span data-ttu-id="b0787-2270">Sinon, le résultat est `false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2270">Otherwise, the result is `false`.</span></span>

<span data-ttu-id="b0787-2271">Le résultat de `x | y` est `true` si `x` ou `y` est `true`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2271">The result of `x | y` is `true` if either `x` or `y` is `true`.</span></span> <span data-ttu-id="b0787-2272">Sinon, le résultat est `false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2272">Otherwise, the result is `false`.</span></span>

<span data-ttu-id="b0787-2273">Le résultat de `x ^ y` est `true` si `x` est `true` et `y` est `false`, ou `x` est `false` et `y` est `true`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2273">The result of `x ^ y` is `true` if `x` is `true` and `y` is `false`, or `x` is `false` and `y` is `true`.</span></span> <span data-ttu-id="b0787-2274">Sinon, le résultat est `false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2274">Otherwise, the result is `false`.</span></span> <span data-ttu-id="b0787-2275">Quand les opérandes sont de type `bool`, l’opérateur `^` calcule le même résultat que l’opérateur `!=`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2275">When the operands are of type `bool`, the `^` operator computes the same result as the `!=` operator.</span></span>

### <a name="nullable-boolean-logical-operators"></a><span data-ttu-id="b0787-2276">Opérateurs logiques booléens Nullable</span><span class="sxs-lookup"><span data-stu-id="b0787-2276">Nullable boolean logical operators</span></span>

<span data-ttu-id="b0787-2277">Le type booléen Nullable `bool?` peut représenter trois valeurs, `true`, `false`et `null`, et est conceptuellement similaire au type à trois valeurs utilisé pour les expressions booléennes dans SQL.</span><span class="sxs-lookup"><span data-stu-id="b0787-2277">The nullable boolean type `bool?` can represent three values, `true`, `false`, and `null`, and is conceptually similar to the three-valued type used for boolean expressions in SQL.</span></span> <span data-ttu-id="b0787-2278">Pour vous assurer que les résultats produits par les opérateurs `&` et `|` pour les opérandes `bool?` sont cohérents avec la logique à trois valeurs de SQL, les opérateurs prédéfinis suivants sont fournis :</span><span class="sxs-lookup"><span data-stu-id="b0787-2278">To ensure that the results produced by the `&` and `|` operators for `bool?` operands are consistent with SQL's three-valued logic, the following predefined operators are provided:</span></span>

```csharp
bool? operator &(bool? x, bool? y);
bool? operator |(bool? x, bool? y);
```

<span data-ttu-id="b0787-2279">Le tableau suivant répertorie les résultats produits par ces opérateurs pour toutes les combinaisons des valeurs `true`, `false`et `null`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2279">The following table lists the results produced by these operators for all combinations of the values `true`, `false`, and `null`.</span></span>

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

## <a name="conditional-logical-operators"></a><span data-ttu-id="b0787-2280">Opérateurs logiques conditionnels</span><span class="sxs-lookup"><span data-stu-id="b0787-2280">Conditional logical operators</span></span>

<span data-ttu-id="b0787-2281">Les opérateurs `&&` et `||` sont appelés opérateurs logiques conditionnels.</span><span class="sxs-lookup"><span data-stu-id="b0787-2281">The `&&` and `||` operators are called the conditional logical operators.</span></span> <span data-ttu-id="b0787-2282">Elles sont également appelées opérateurs logiques de « court-circuit ».</span><span class="sxs-lookup"><span data-stu-id="b0787-2282">They are also called the "short-circuiting" logical operators.</span></span>

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

<span data-ttu-id="b0787-2283">Les opérateurs `&&` et `||` sont des versions conditionnelles des opérateurs `&` et `|` :</span><span class="sxs-lookup"><span data-stu-id="b0787-2283">The `&&` and `||` operators are conditional versions of the `&` and `|` operators:</span></span>

*  <span data-ttu-id="b0787-2284">L’opération `x && y` correspond à l’opération `x & y`, sauf que `y` n’est évaluée que si `x` n’est pas `false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2284">The operation `x && y` corresponds to the operation `x & y`, except that `y` is evaluated only if `x` is not `false`.</span></span>
*  <span data-ttu-id="b0787-2285">L’opération `x || y` correspond à l’opération `x | y`, sauf que `y` n’est évaluée que si `x` n’est pas `true`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2285">The operation `x || y` corresponds to the operation `x | y`, except that `y` is evaluated only if `x` is not `true`.</span></span>

<span data-ttu-id="b0787-2286">Si un opérande d’un opérateur logique conditionnel a le type au moment de la compilation `dynamic`, l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2286">If an operand of a conditional logical operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="b0787-2287">Dans ce cas, le type au moment de la compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous a lieu au moment de l’exécution à l’aide du type d’exécution des opérandes qui ont le type au moment de la compilation `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2287">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="b0787-2288">Une opération de la forme `x && y` ou `x || y` est traitée en appliquant la résolution de surcharge ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) comme si l’opération avait été écrite `x & y` ou `x | y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2288">An operation of the form `x && y` or `x || y` is processed by applying overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) as if the operation was written `x & y` or `x | y`.</span></span> <span data-ttu-id="b0787-2289">Ainsi,</span><span class="sxs-lookup"><span data-stu-id="b0787-2289">Then,</span></span>

*  <span data-ttu-id="b0787-2290">Si la résolution de surcharge ne parvient pas à trouver un seul opérateur le mieux, ou si la résolution de surcharge sélectionne l’un des opérateurs logiques d’entiers prédéfinis, une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-2290">If overload resolution fails to find a single best operator, or if overload resolution selects one of the predefined integer logical operators, a binding-time error occurs.</span></span>
*  <span data-ttu-id="b0787-2291">Sinon, si l’opérateur sélectionné est l’un des opérateurs logiques booléens prédéfinis ([opérateurs logiques booléens](expressions.md#boolean-logical-operators)) ou des opérateurs logiques booléens Nullable ([opérateurs logiques booléens Nullable](expressions.md#nullable-boolean-logical-operators)), l’opération est traitée comme décrit dans [opérateurs logiques conditionnels booléens](expressions.md#boolean-conditional-logical-operators).</span><span class="sxs-lookup"><span data-stu-id="b0787-2291">Otherwise, if the selected operator is one of the predefined boolean logical operators ([Boolean logical operators](expressions.md#boolean-logical-operators)) or nullable boolean logical operators ([Nullable boolean logical operators](expressions.md#nullable-boolean-logical-operators)), the operation is processed as described in [Boolean conditional logical operators](expressions.md#boolean-conditional-logical-operators).</span></span>
*  <span data-ttu-id="b0787-2292">Dans le cas contraire, l’opérateur sélectionné est un opérateur défini par l’utilisateur et l’opération est traitée comme décrit dans [opérateurs logiques conditionnels définis par l’utilisateur](expressions.md#user-defined-conditional-logical-operators).</span><span class="sxs-lookup"><span data-stu-id="b0787-2292">Otherwise, the selected operator is a user-defined operator, and the operation is processed as described in [User-defined conditional logical operators](expressions.md#user-defined-conditional-logical-operators).</span></span>

<span data-ttu-id="b0787-2293">Il n’est pas possible de surcharger directement les opérateurs logiques conditionnels.</span><span class="sxs-lookup"><span data-stu-id="b0787-2293">It is not possible to directly overload the conditional logical operators.</span></span> <span data-ttu-id="b0787-2294">Toutefois, étant donné que les opérateurs logiques conditionnels sont évalués en termes d’opérateurs logiques standard, les surcharges des opérateurs logiques normaux sont, avec certaines restrictions, également considérées comme des surcharges des opérateurs logiques conditionnels.</span><span class="sxs-lookup"><span data-stu-id="b0787-2294">However, because the conditional logical operators are evaluated in terms of the regular logical operators, overloads of the regular logical operators are, with certain restrictions, also considered overloads of the conditional logical operators.</span></span> <span data-ttu-id="b0787-2295">Cela est décrit plus en détail dans [opérateurs logiques conditionnels définis par l’utilisateur](expressions.md#user-defined-conditional-logical-operators).</span><span class="sxs-lookup"><span data-stu-id="b0787-2295">This is described further in [User-defined conditional logical operators](expressions.md#user-defined-conditional-logical-operators).</span></span>

### <a name="boolean-conditional-logical-operators"></a><span data-ttu-id="b0787-2296">Opérateurs logiques conditionnels booléens</span><span class="sxs-lookup"><span data-stu-id="b0787-2296">Boolean conditional logical operators</span></span>

<span data-ttu-id="b0787-2297">Lorsque les opérandes de `&&` ou `||` sont de type `bool`, ou lorsque les opérandes sont de types qui ne définissent pas d' `operator &` ou de `operator |`applicables, mais qui définissent les conversions implicites en `bool`, l’opération est traitée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-2297">When the operands of `&&` or `||` are of type `bool`, or when the operands are of types that do not define an applicable `operator &` or `operator |`, but do define implicit conversions to `bool`, the operation is processed as follows:</span></span>

*  <span data-ttu-id="b0787-2298">L’opération `x && y` est évaluée comme `x ? y : false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2298">The operation `x && y` is evaluated as `x ? y : false`.</span></span> <span data-ttu-id="b0787-2299">En d’autres termes, `x` est d’abord évalué et converti en type `bool`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2299">In other words, `x` is first evaluated and converted to type `bool`.</span></span> <span data-ttu-id="b0787-2300">Ensuite, si `x` est `true`, `y` est évalué et converti en type `bool`, ce qui devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-2300">Then, if `x` is `true`, `y` is evaluated and converted to type `bool`, and this becomes the result of the operation.</span></span> <span data-ttu-id="b0787-2301">Dans le cas contraire, le résultat de l’opération est `false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2301">Otherwise, the result of the operation is `false`.</span></span>
*  <span data-ttu-id="b0787-2302">L’opération `x || y` est évaluée comme `x ? true : y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2302">The operation `x || y` is evaluated as `x ? true : y`.</span></span> <span data-ttu-id="b0787-2303">En d’autres termes, `x` est d’abord évalué et converti en type `bool`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2303">In other words, `x` is first evaluated and converted to type `bool`.</span></span> <span data-ttu-id="b0787-2304">Ensuite, si `x` est `true`, le résultat de l’opération est `true`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2304">Then, if `x` is `true`, the result of the operation is `true`.</span></span> <span data-ttu-id="b0787-2305">Dans le cas contraire, `y` est évalué et converti en type `bool`, ce qui devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-2305">Otherwise, `y` is evaluated and converted to type `bool`, and this becomes the result of the operation.</span></span>

### <a name="user-defined-conditional-logical-operators"></a><span data-ttu-id="b0787-2306">Opérateurs logiques conditionnels définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="b0787-2306">User-defined conditional logical operators</span></span>

<span data-ttu-id="b0787-2307">Lorsque les opérandes de `&&` ou `||` sont des types qui déclarent un `operator &` ou `operator |`défini par l’utilisateur applicable, les deux conditions suivantes doivent être vraies, où `T` est le type dans lequel l’opérateur sélectionné est déclaré :</span><span class="sxs-lookup"><span data-stu-id="b0787-2307">When the operands of `&&` or `||` are of types that declare an applicable user-defined `operator &` or `operator |`, both of the following must be true, where `T` is the type in which the selected operator is declared:</span></span>

*  <span data-ttu-id="b0787-2308">Le type de retour et le type de chaque paramètre de l’opérateur sélectionné doivent être `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2308">The return type and the type of each parameter of the selected operator must be `T`.</span></span> <span data-ttu-id="b0787-2309">En d’autres termes, l’opérateur doit calculer le `AND` logique ou le `OR` logique de deux opérandes de type `T`, et doit retourner un résultat de type `T`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2309">In other words, the operator must compute the logical `AND` or the logical `OR` of two operands of type `T`, and must return a result of type `T`.</span></span>
*  <span data-ttu-id="b0787-2310">`T` doit contenir des déclarations de `operator true` et de `operator false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2310">`T` must contain declarations of `operator true` and `operator false`.</span></span>

<span data-ttu-id="b0787-2311">Une erreur de liaison au moment de la liaison se produit si l’une de ces conditions n’est pas satisfaite.</span><span class="sxs-lookup"><span data-stu-id="b0787-2311">A binding-time error occurs if either of these requirements is not satisfied.</span></span> <span data-ttu-id="b0787-2312">Dans le cas contraire, l’opération `&&` ou `||` est évaluée en combinant le `operator true` défini par l’utilisateur ou `operator false` à l’opérateur défini par l’utilisateur sélectionné :</span><span class="sxs-lookup"><span data-stu-id="b0787-2312">Otherwise, the `&&` or `||` operation is evaluated by combining the user-defined `operator true` or `operator false` with the selected user-defined operator:</span></span>

*  <span data-ttu-id="b0787-2313">L’opération `x && y` est évaluée comme `T.false(x) ? x : T.&(x, y)`, où `T.false(x)` est un appel de l' `operator false` déclaré dans `T`, et `T.&(x, y)` est un appel de la `operator &`sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="b0787-2313">The operation `x && y` is evaluated as `T.false(x) ? x : T.&(x, y)`, where `T.false(x)` is an invocation of the `operator false` declared in `T`, and `T.&(x, y)` is an invocation of the selected `operator &`.</span></span> <span data-ttu-id="b0787-2314">En d’autres termes, `x` est évaluée en premier et `operator false` est appelé sur le résultat pour déterminer si `x` a la valeur false.</span><span class="sxs-lookup"><span data-stu-id="b0787-2314">In other words, `x` is first evaluated and `operator false` is invoked on the result to determine if `x` is definitely false.</span></span> <span data-ttu-id="b0787-2315">Ensuite, si `x` est définitivement false, le résultat de l’opération est la valeur précédemment calculée pour `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2315">Then, if `x` is definitely false, the result of the operation is the value previously computed for `x`.</span></span> <span data-ttu-id="b0787-2316">Dans le cas contraire, `y` est évalué et le `operator &` sélectionné est appelé sur la valeur précédemment calculée pour `x` et la valeur calculée pour `y` pour produire le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-2316">Otherwise, `y` is evaluated, and the selected `operator &` is invoked on the value previously computed for `x` and the value computed for `y` to produce the result of the operation.</span></span>
*  <span data-ttu-id="b0787-2317">L’opération `x || y` est évaluée comme `T.true(x) ? x : T.|(x, y)`, où `T.true(x)` est un appel de l' `operator true` déclaré dans `T`, et `T.|(x,y)` est un appel de la `operator|`sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="b0787-2317">The operation `x || y` is evaluated as `T.true(x) ? x : T.|(x, y)`, where `T.true(x)` is an invocation of the `operator true` declared in `T`, and `T.|(x,y)` is an invocation of the selected `operator|`.</span></span> <span data-ttu-id="b0787-2318">En d’autres termes, `x` est évaluée en premier et `operator true` est appelé sur le résultat pour déterminer si `x` est absolument true.</span><span class="sxs-lookup"><span data-stu-id="b0787-2318">In other words, `x` is first evaluated and `operator true` is invoked on the result to determine if `x` is definitely true.</span></span> <span data-ttu-id="b0787-2319">Ensuite, si `x` est défini sur true, le résultat de l’opération est la valeur précédemment calculée pour `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2319">Then, if `x` is definitely true, the result of the operation is the value previously computed for `x`.</span></span> <span data-ttu-id="b0787-2320">Dans le cas contraire, `y` est évalué et le `operator |` sélectionné est appelé sur la valeur précédemment calculée pour `x` et la valeur calculée pour `y` pour produire le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-2320">Otherwise, `y` is evaluated, and the selected `operator |` is invoked on the value previously computed for `x` and the value computed for `y` to produce the result of the operation.</span></span>

<span data-ttu-id="b0787-2321">Dans l’une ou l’autre de ces opérations, l’expression donnée par `x` n’est évaluée qu’une seule fois, et l’expression donnée par `y` n’est pas évaluée ou évaluée une seule fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-2321">In either of these operations, the expression given by `x` is only evaluated once, and the expression given by `y` is either not evaluated or evaluated exactly once.</span></span>

<span data-ttu-id="b0787-2322">Pour obtenir un exemple de type qui implémente `operator true` et `operator false`, consultez [Database Boolean type](structs.md#database-boolean-type).</span><span class="sxs-lookup"><span data-stu-id="b0787-2322">For an example of a type that implements `operator true` and `operator false`, see [Database boolean type](structs.md#database-boolean-type).</span></span>

## <a name="the-null-coalescing-operator"></a><span data-ttu-id="b0787-2323">Opérateur de fusion Null</span><span class="sxs-lookup"><span data-stu-id="b0787-2323">The null coalescing operator</span></span>

<span data-ttu-id="b0787-2324">L’opérateur `??` est appelé opérateur de fusion Null.</span><span class="sxs-lookup"><span data-stu-id="b0787-2324">The `??` operator is called the null coalescing operator.</span></span>

```antlr
null_coalescing_expression
    : conditional_or_expression
    | conditional_or_expression '??' null_coalescing_expression
    ;
```

<span data-ttu-id="b0787-2325">Une expression de fusion Null de la forme `a ?? b` nécessite que `a` soit un type Nullable ou un type référence.</span><span class="sxs-lookup"><span data-stu-id="b0787-2325">A null coalescing expression of the form `a ?? b` requires `a` to be of a nullable type or reference type.</span></span> <span data-ttu-id="b0787-2326">Si `a` est non null, le résultat de `a ?? b` est `a`; dans le cas contraire, le résultat est `b`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2326">If `a` is non-null, the result of `a ?? b` is `a`; otherwise, the result is `b`.</span></span> <span data-ttu-id="b0787-2327">L’opération évalue `b` uniquement si `a` a la valeur null.</span><span class="sxs-lookup"><span data-stu-id="b0787-2327">The operation evaluates `b` only if `a` is null.</span></span>

<span data-ttu-id="b0787-2328">L’opérateur de fusion Null est associatif à droite, ce qui signifie que les opérations sont regroupées de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-2328">The null coalescing operator is right-associative, meaning that operations are grouped from right to left.</span></span> <span data-ttu-id="b0787-2329">Par exemple, une expression de la forme `a ?? b ?? c` est évaluée comme `a ?? (b ?? c)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2329">For example, an expression of the form `a ?? b ?? c` is evaluated as `a ?? (b ?? c)`.</span></span> <span data-ttu-id="b0787-2330">En général, une expression de la forme `E1 ?? E2 ?? ... ?? En` retourne le premier des opérandes qui n’est pas null, ou null si tous les opérandes ont la valeur null.</span><span class="sxs-lookup"><span data-stu-id="b0787-2330">In general terms, an expression of the form `E1 ?? E2 ?? ... ?? En` returns the first of the operands that is non-null, or null if all operands are null.</span></span>

<span data-ttu-id="b0787-2331">Le type de l’expression `a ?? b` dépend des conversions implicites disponibles sur les opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2331">The type of the expression `a ?? b` depends on which implicit conversions are available on the operands.</span></span> <span data-ttu-id="b0787-2332">Par ordre de préférence, le type de `a ?? b` est `A0`, `A`ou `B`, où `A` est le type de `a` (à condition que `a` ait un type), `B` est le type de `b` (à condition que `b` ait un type) et `A0` est le type sous-jacent de `A` si `A` est un type Nullable, ou `A` dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="b0787-2332">In order of preference, the type of `a ?? b` is `A0`, `A`, or `B`, where `A` is the type of `a` (provided that `a` has a type), `B` is the type of `b` (provided that `b` has a type), and `A0` is the underlying type of `A` if `A` is a nullable type, or `A` otherwise.</span></span> <span data-ttu-id="b0787-2333">Plus précisément, `a ?? b` est traité comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-2333">Specifically, `a ?? b` is processed as follows:</span></span>

*  <span data-ttu-id="b0787-2334">Si `A` existe et qu’il ne s’agit pas d’un type Nullable ou d’un type référence, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-2334">If `A` exists and is not a nullable type or a reference type, a compile-time error occurs.</span></span>
*  <span data-ttu-id="b0787-2335">Si `b` est une expression dynamique, le type de résultat est `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2335">If `b` is a dynamic expression, the result type is `dynamic`.</span></span> <span data-ttu-id="b0787-2336">Au moment de l’exécution, `a` est évaluée pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-2336">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="b0787-2337">Si `a` n’a pas la valeur null, `a` est converti en type dynamique, ce qui devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="b0787-2337">If `a` is not null, `a` is converted to dynamic, and this becomes the result.</span></span> <span data-ttu-id="b0787-2338">Dans le cas contraire, `b` est évaluée et devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="b0787-2338">Otherwise, `b` is evaluated, and this becomes the result.</span></span>
*  <span data-ttu-id="b0787-2339">Sinon, si `A` existe et qu’il s’agit d’un type Nullable et qu’une conversion implicite existe de `b` à `A0`, le type de résultat est `A0`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2339">Otherwise, if `A` exists and is a nullable type and an implicit conversion exists from `b` to `A0`, the result type is `A0`.</span></span> <span data-ttu-id="b0787-2340">Au moment de l’exécution, `a` est évaluée pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-2340">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="b0787-2341">Si `a` n’a pas la valeur null, `a` est désencapsulée dans le type `A0`, ce qui devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="b0787-2341">If `a` is not null, `a` is unwrapped to type `A0`, and this becomes the result.</span></span> <span data-ttu-id="b0787-2342">Dans le cas contraire, `b` est évalué et converti en type `A0`, ce qui devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="b0787-2342">Otherwise, `b` is evaluated and converted to type `A0`, and this becomes the result.</span></span>
*  <span data-ttu-id="b0787-2343">Sinon, si `A` existe et qu’une conversion implicite existe de `b` à `A`, le type de résultat est `A`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2343">Otherwise, if `A` exists and an implicit conversion exists from `b` to `A`, the result type is `A`.</span></span> <span data-ttu-id="b0787-2344">Au moment de l’exécution, `a` est évaluée pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-2344">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="b0787-2345">Si `a` n’a pas la valeur null, `a` devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="b0787-2345">If `a` is not null, `a` becomes the result.</span></span> <span data-ttu-id="b0787-2346">Dans le cas contraire, `b` est évalué et converti en type `A`, ce qui devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="b0787-2346">Otherwise, `b` is evaluated and converted to type `A`, and this becomes the result.</span></span>
*  <span data-ttu-id="b0787-2347">Sinon, si `b` a un type `B` et qu’il existe une conversion implicite de `a` en `B`, le type de résultat est `B`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2347">Otherwise, if `b` has a type `B` and an implicit conversion exists from `a` to `B`, the result type is `B`.</span></span> <span data-ttu-id="b0787-2348">Au moment de l’exécution, `a` est évaluée pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-2348">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="b0787-2349">Si `a` n’a pas la valeur null, `a` est désencapsulée dans le type `A0` (si `A` existe et est Nullable) et converti en type `B`, et cela devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="b0787-2349">If `a` is not null, `a` is unwrapped to type `A0` (if `A` exists and is nullable) and converted to type `B`, and this becomes the result.</span></span> <span data-ttu-id="b0787-2350">Dans le cas contraire, `b` est évalué et devient le résultat.</span><span class="sxs-lookup"><span data-stu-id="b0787-2350">Otherwise, `b` is evaluated and becomes the result.</span></span>
*  <span data-ttu-id="b0787-2351">Sinon, les `a` et les `b` sont incompatibles, et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-2351">Otherwise, `a` and `b` are incompatible, and a compile-time error occurs.</span></span>

## <a name="conditional-operator"></a><span data-ttu-id="b0787-2352">Opérateur conditionnel</span><span class="sxs-lookup"><span data-stu-id="b0787-2352">Conditional operator</span></span>

<span data-ttu-id="b0787-2353">L’opérateur `?:` est appelé opérateur conditionnel.</span><span class="sxs-lookup"><span data-stu-id="b0787-2353">The `?:` operator is called the conditional operator.</span></span> <span data-ttu-id="b0787-2354">Il est parfois également appelé opérateur ternaire.</span><span class="sxs-lookup"><span data-stu-id="b0787-2354">It is at times also called the ternary operator.</span></span>

```antlr
conditional_expression
    : null_coalescing_expression
    | null_coalescing_expression '?' expression ':' expression
    ;
```

<span data-ttu-id="b0787-2355">Une expression conditionnelle de la forme `b ? x : y` évalue d’abord la condition `b`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2355">A conditional expression of the form `b ? x : y` first evaluates the condition `b`.</span></span> <span data-ttu-id="b0787-2356">Ensuite, si `b` est `true`, `x` est évaluée et devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-2356">Then, if `b` is `true`, `x` is evaluated and becomes the result of the operation.</span></span> <span data-ttu-id="b0787-2357">Dans le cas contraire, `y` est évalué et devient le résultat de l’opération.</span><span class="sxs-lookup"><span data-stu-id="b0787-2357">Otherwise, `y` is evaluated and becomes the result of the operation.</span></span> <span data-ttu-id="b0787-2358">Une expression conditionnelle n’évalue jamais à la fois `x` et `y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2358">A conditional expression never evaluates both `x` and `y`.</span></span>

<span data-ttu-id="b0787-2359">L’opérateur conditionnel est associatif à droite, ce qui signifie que les opérations sont regroupées de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-2359">The conditional operator is right-associative, meaning that operations are grouped from right to left.</span></span> <span data-ttu-id="b0787-2360">Par exemple, une expression de la forme `a ? b : c ? d : e` est évaluée comme `a ? b : (c ? d : e)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2360">For example, an expression of the form `a ? b : c ? d : e` is evaluated as `a ? b : (c ? d : e)`.</span></span>

<span data-ttu-id="b0787-2361">Le premier opérande de l’opérateur `?:` doit être une expression qui peut être implicitement convertie en `bool`ou une expression d’un type qui implémente `operator true`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2361">The first operand of the `?:` operator must be an expression that can be implicitly converted to `bool`, or an expression of a type that implements `operator true`.</span></span> <span data-ttu-id="b0787-2362">Si aucune de ces exigences n’est satisfaite, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-2362">If neither of these requirements is satisfied, a compile-time error occurs.</span></span>

<span data-ttu-id="b0787-2363">Le deuxième et le troisième opérandes, `x` et `y`de l’opérateur `?:` contrôlent le type de l’expression conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="b0787-2363">The second and third operands, `x` and `y`, of the `?:` operator control the type of the conditional expression.</span></span>

*  <span data-ttu-id="b0787-2364">Si `x` a le type `X` et `y` a le type `Y` puis</span><span class="sxs-lookup"><span data-stu-id="b0787-2364">If `x` has type `X` and `y` has type `Y` then</span></span>
   * <span data-ttu-id="b0787-2365">Si une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir de `X` vers `Y`, mais pas de `Y` à `X`, `Y` est le type de l’expression conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="b0787-2365">If an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from `X` to `Y`, but not from `Y` to `X`, then `Y` is the type of the conditional expression.</span></span>
   * <span data-ttu-id="b0787-2366">Si une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir de `Y` vers `X`, mais pas de `X` à `Y`, `X` est le type de l’expression conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="b0787-2366">If an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from `Y` to `X`, but not from `X` to `Y`, then `X` is the type of the conditional expression.</span></span>
   * <span data-ttu-id="b0787-2367">Dans le cas contraire, aucun type d’expression ne peut être déterminé et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-2367">Otherwise, no expression type can be determined, and a compile-time error occurs.</span></span>
*  <span data-ttu-id="b0787-2368">Si une seule des `x` et `y` a un type, et que `x` et `y`, de sont implicitement convertibles en ce type, alors il s’agit du type de l’expression conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="b0787-2368">If only one of `x` and `y` has a type, and both `x` and `y`, of are implicitly convertible to that type, then that is the type of the conditional expression.</span></span>
*  <span data-ttu-id="b0787-2369">Dans le cas contraire, aucun type d’expression ne peut être déterminé et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-2369">Otherwise, no expression type can be determined, and a compile-time error occurs.</span></span>

<span data-ttu-id="b0787-2370">Le traitement au moment de l’exécution d’une expression conditionnelle de la forme `b ? x : y` se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-2370">The run-time processing of a conditional expression of the form `b ? x : y` consists of the following steps:</span></span>

*  <span data-ttu-id="b0787-2371">Tout d’abord, `b` est évaluée et la valeur `bool` de `b` est déterminée :</span><span class="sxs-lookup"><span data-stu-id="b0787-2371">First, `b` is evaluated, and the `bool` value of `b` is determined:</span></span>
   * <span data-ttu-id="b0787-2372">Si une conversion implicite du type de `b` en `bool` existe, cette conversion implicite est effectuée pour produire une valeur de `bool`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2372">If an implicit conversion from the type of `b` to `bool` exists, then this implicit conversion is performed to produce a `bool` value.</span></span>
   * <span data-ttu-id="b0787-2373">Dans le cas contraire, le `operator true` défini par le type de `b` est appelé pour produire une valeur de `bool`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2373">Otherwise, the `operator true` defined by the type of `b` is invoked to produce a `bool` value.</span></span>
*  <span data-ttu-id="b0787-2374">Si la valeur `bool` produite par l’étape ci-dessus est `true`, `x` est évaluée et convertie en type de l’expression conditionnelle, et devient le résultat de l’expression conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="b0787-2374">If the `bool` value produced by the step above is `true`, then `x` is evaluated and converted to the type of the conditional expression, and this becomes the result of the conditional expression.</span></span>
*  <span data-ttu-id="b0787-2375">Dans le cas contraire, `y` est évalué et converti en type de l’expression conditionnelle, ce qui devient le résultat de l’expression conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="b0787-2375">Otherwise, `y` is evaluated and converted to the type of the conditional expression, and this becomes the result of the conditional expression.</span></span>

## <a name="anonymous-function-expressions"></a><span data-ttu-id="b0787-2376">Expressions de fonctions anonymes</span><span class="sxs-lookup"><span data-stu-id="b0787-2376">Anonymous function expressions</span></span>

<span data-ttu-id="b0787-2377">Une ***fonction anonyme*** est une expression qui représente une définition de méthode « in-line ».</span><span class="sxs-lookup"><span data-stu-id="b0787-2377">An ***anonymous function*** is an expression that represents an "in-line" method definition.</span></span> <span data-ttu-id="b0787-2378">Une fonction anonyme n’a pas de valeur ou de type dans et de elle-même, mais elle peut être convertie en un délégué compatible ou un type d’arborescence d’expression.</span><span class="sxs-lookup"><span data-stu-id="b0787-2378">An anonymous function does not have a value or type in and of itself, but is convertible to a compatible delegate or expression tree type.</span></span> <span data-ttu-id="b0787-2379">L’évaluation d’une conversion de fonction anonyme dépend du type de cible de la conversion : s’il s’agit d’un type délégué, la conversion correspond à une valeur de délégué qui référence la méthode définie par la fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="b0787-2379">The evaluation of an anonymous function conversion depends on the target type of the conversion: If it is a delegate type, the conversion evaluates to a delegate value referencing the method which the anonymous function defines.</span></span> <span data-ttu-id="b0787-2380">S’il s’agit d’un type d’arborescence d’expression, la conversion prend la valeur d’une arborescence d’expressions qui représente la structure de la méthode sous la forme d’une structure d’objet.</span><span class="sxs-lookup"><span data-stu-id="b0787-2380">If it is an expression tree type, the conversion evaluates to an expression tree which represents the structure of the method as an object structure.</span></span>

<span data-ttu-id="b0787-2381">Pour des raisons historiques, il existe deux versions syntaxiques des fonctions anonymes, à savoir *lambda_expression*s et *anonymous_method_expression*s.</span><span class="sxs-lookup"><span data-stu-id="b0787-2381">For historical reasons there are two syntactic flavors of anonymous functions, namely *lambda_expression*s and *anonymous_method_expression*s.</span></span> <span data-ttu-id="b0787-2382">Pour presque tous les besoins, *lambda_expression*s sont plus concises et plus expressifs que *anonymous_method_expression*s, qui restent dans le langage à des fins de compatibilité descendante.</span><span class="sxs-lookup"><span data-stu-id="b0787-2382">For almost all purposes, *lambda_expression*s are more concise and expressive than *anonymous_method_expression*s, which remain in the language for backwards compatibility.</span></span>

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

<span data-ttu-id="b0787-2383">L’opérateur `=>` a la même priorité que l’assignation (`=`) et est associatif à droite.</span><span class="sxs-lookup"><span data-stu-id="b0787-2383">The `=>` operator has the same precedence as assignment (`=`) and is right-associative.</span></span>

<span data-ttu-id="b0787-2384">Une fonction anonyme avec le modificateur `async` est une fonction Async et suit les règles décrites dans [itérateurs](classes.md#iterators).</span><span class="sxs-lookup"><span data-stu-id="b0787-2384">An anonymous function with the `async` modifier is an async function and follows the rules described in [Iterators](classes.md#iterators).</span></span>

<span data-ttu-id="b0787-2385">Les paramètres d’une fonction anonyme sous la forme d’un *lambda_expression* peuvent être explicitement ou implicitement typés.</span><span class="sxs-lookup"><span data-stu-id="b0787-2385">The parameters of an anonymous function in the form of a *lambda_expression* can be explicitly or implicitly typed.</span></span> <span data-ttu-id="b0787-2386">Dans une liste de paramètres typés explicitement, le type de chaque paramètre est défini explicitement.</span><span class="sxs-lookup"><span data-stu-id="b0787-2386">In an explicitly typed parameter list, the type of each parameter is explicitly stated.</span></span> <span data-ttu-id="b0787-2387">Dans une liste de paramètres typée implicitement, les types des paramètres sont déduits du contexte dans lequel la fonction anonyme se produit, en particulier lorsque la fonction anonyme est convertie en un type délégué compatible ou un type d’arborescence d’expression, ce type fournit les types de paramètres ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2387">In an implicitly typed parameter list, the types of the parameters are inferred from the context in which the anonymous function occurs—specifically, when the anonymous function is converted to a compatible delegate type or expression tree type, that type provides the parameter types ([Anonymous function conversions](conversions.md#anonymous-function-conversions)).</span></span>

<span data-ttu-id="b0787-2388">Dans une fonction anonyme avec un seul paramètre typé implicitement, les parenthèses peuvent être omises dans la liste de paramètres.</span><span class="sxs-lookup"><span data-stu-id="b0787-2388">In an anonymous function with a single, implicitly typed parameter, the parentheses may be omitted from the parameter list.</span></span> <span data-ttu-id="b0787-2389">En d’autres termes, une fonction anonyme de la forme</span><span class="sxs-lookup"><span data-stu-id="b0787-2389">In other words, an anonymous function of the form</span></span>
```csharp
( param ) => expr
```
<span data-ttu-id="b0787-2390">peut être abrégé en</span><span class="sxs-lookup"><span data-stu-id="b0787-2390">can be abbreviated to</span></span>
```csharp
param => expr
```

<span data-ttu-id="b0787-2391">La liste des paramètres d’une fonction anonyme sous la forme d’un *anonymous_method_expression* est facultative.</span><span class="sxs-lookup"><span data-stu-id="b0787-2391">The parameter list of an anonymous function in the form of an *anonymous_method_expression* is optional.</span></span> <span data-ttu-id="b0787-2392">Si elles sont spécifiées, les paramètres doivent être explicitement typés.</span><span class="sxs-lookup"><span data-stu-id="b0787-2392">If given, the parameters must be explicitly typed.</span></span> <span data-ttu-id="b0787-2393">Si ce n’est pas le cas, la fonction anonyme est convertible en un délégué avec une liste de paramètres qui ne contient pas de paramètres `out`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2393">If not, the anonymous function is convertible to a delegate with any parameter list not containing `out` parameters.</span></span>

<span data-ttu-id="b0787-2394">Un corps de *bloc* d’une fonction anonyme est accessible ([points de terminaison et accessibilité](statements.md#end-points-and-reachability)) sauf si la fonction anonyme se produit à l’intérieur d’une instruction inaccessible.</span><span class="sxs-lookup"><span data-stu-id="b0787-2394">A *block* body of an anonymous function is reachable ([End points and reachability](statements.md#end-points-and-reachability)) unless the anonymous function occurs inside an unreachable statement.</span></span>

<span data-ttu-id="b0787-2395">Voici quelques exemples de fonctions anonymes :</span><span class="sxs-lookup"><span data-stu-id="b0787-2395">Some examples of anonymous functions follow below:</span></span>

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

<span data-ttu-id="b0787-2396">Le comportement de *lambda_expression*s et *anonymous_method_expression*s est le même, à l’exception des points suivants :</span><span class="sxs-lookup"><span data-stu-id="b0787-2396">The behavior of *lambda_expression*s and *anonymous_method_expression*s is the same except for the following points:</span></span>

*  <span data-ttu-id="b0787-2397">*anonymous_method_expression*s permettent l’omission complète de la liste de paramètres, ce qui donne convertibilité aux types délégués d’une liste de paramètres de valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2397">*anonymous_method_expression*s permit the parameter list to be omitted entirely, yielding convertibility to delegate types of any list of value parameters.</span></span>
*  <span data-ttu-id="b0787-2398">*lambda_expression*s autorisent l’omission et la déduction des types de paramètres, tandis que les types de paramètres doivent être déclarés explicitement *anonymous_method_expression*s.</span><span class="sxs-lookup"><span data-stu-id="b0787-2398">*lambda_expression*s permit parameter types to be omitted and inferred whereas *anonymous_method_expression*s require parameter types to be explicitly stated.</span></span>
*  <span data-ttu-id="b0787-2399">Le corps d’un *lambda_expression* peut être une expression ou un bloc d’instructions, tandis que le corps d’un *anonymous_method_expression* doit être un bloc d’instructions.</span><span class="sxs-lookup"><span data-stu-id="b0787-2399">The body of a *lambda_expression* can be an expression or a statement block whereas the body of an *anonymous_method_expression* must be a statement block.</span></span>
*  <span data-ttu-id="b0787-2400">Seuls les *lambda_expression*s ont des conversions en types d’arborescences d’expressions compatibles ([types d’arborescence d’expression](types.md#expression-tree-types)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2400">Only *lambda_expression*s have conversions to compatible expression tree types ([Expression tree types](types.md#expression-tree-types)).</span></span>

### <a name="anonymous-function-signatures"></a><span data-ttu-id="b0787-2401">Signatures de fonctions anonymes</span><span class="sxs-lookup"><span data-stu-id="b0787-2401">Anonymous function signatures</span></span>

<span data-ttu-id="b0787-2402">La *anonymous_function_signature* facultative d’une fonction anonyme définit les noms et éventuellement les types des paramètres formels pour la fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="b0787-2402">The optional *anonymous_function_signature* of an anonymous function defines the names and optionally the types of the formal parameters for the anonymous function.</span></span> <span data-ttu-id="b0787-2403">La portée des paramètres de la fonction anonyme est le *anonymous_function_body*.</span><span class="sxs-lookup"><span data-stu-id="b0787-2403">The scope of the parameters of the anonymous function is the *anonymous_function_body*.</span></span> <span data-ttu-id="b0787-2404">([Étendues](basic-concepts.md#scopes)) Avec la liste de paramètres (si elle est donnée), le corps de la méthode anonyme constitue un espace de déclaration ([déclarations](basic-concepts.md#declarations)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2404">([Scopes](basic-concepts.md#scopes)) Together with the parameter list (if given) the anonymous-method-body constitutes a declaration space ([Declarations](basic-concepts.md#declarations)).</span></span> <span data-ttu-id="b0787-2405">Il s’agit donc d’une erreur de compilation pour le nom d’un paramètre de la fonction anonyme qui correspond au nom d’une variable locale, d’une constante locale ou d’un paramètre dont l’étendue comprend le *anonymous_method_expression* ou *lambda_expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-2405">It is thus a compile-time error for the name of a parameter of the anonymous function to match the name of a local variable, local constant or parameter whose scope includes the *anonymous_method_expression* or *lambda_expression*.</span></span>

<span data-ttu-id="b0787-2406">Si une fonction anonyme a un *explicit_anonymous_function_signature*, l’ensemble de types de délégués compatibles et de types d’arborescence d’expression est limité à ceux qui ont les mêmes types de paramètres et modificateurs dans le même ordre.</span><span class="sxs-lookup"><span data-stu-id="b0787-2406">If an anonymous function has an *explicit_anonymous_function_signature*, then the set of compatible delegate types and expression tree types is restricted to those that have the same parameter types and modifiers in the same order.</span></span> <span data-ttu-id="b0787-2407">Contrairement aux conversions de groupe de méthodes ([conversions de groupe de méthodes](conversions.md#method-group-conversions)), la contre-variance des types de paramètre de fonction anonyme n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="b0787-2407">In contrast to method group conversions ([Method group conversions](conversions.md#method-group-conversions)), contra-variance of anonymous function parameter types is not supported.</span></span> <span data-ttu-id="b0787-2408">Si une fonction anonyme n’a pas de *anonymous_function_signature*, l’ensemble de types de délégués compatibles et de types d’arborescence d’expression est limité à ceux qui n’ont pas de paramètres `out`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2408">If an anonymous function does not have an *anonymous_function_signature*, then the set of compatible delegate types and expression tree types is restricted to those that have no `out` parameters.</span></span>

<span data-ttu-id="b0787-2409">Notez qu’un *anonymous_function_signature* ne peut pas inclure d’attributs ou un tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="b0787-2409">Note that an *anonymous_function_signature* cannot include attributes or a parameter array.</span></span> <span data-ttu-id="b0787-2410">Toutefois, un *anonymous_function_signature* peut être compatible avec un type délégué dont la liste de paramètres contient un tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="b0787-2410">Nevertheless, an *anonymous_function_signature* may be compatible with a delegate type whose parameter list contains a parameter array.</span></span>

<span data-ttu-id="b0787-2411">Notez également que la conversion en un type d’arborescence d’expression, même si elle est compatible, peut encore échouer au moment de la compilation ([types d’arborescence d’expression](types.md#expression-tree-types)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2411">Note also that conversion to an expression tree type, even if compatible, may still fail at compile-time ([Expression tree types](types.md#expression-tree-types)).</span></span>

### <a name="anonymous-function-bodies"></a><span data-ttu-id="b0787-2412">Corps des fonctions anonymes</span><span class="sxs-lookup"><span data-stu-id="b0787-2412">Anonymous function bodies</span></span>

<span data-ttu-id="b0787-2413">Le corps (*expression* ou *bloc*) d’une fonction anonyme est soumis aux règles suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-2413">The body (*expression* or *block*) of an anonymous function is subject to the following rules:</span></span>

*  <span data-ttu-id="b0787-2414">Si la fonction anonyme comprend une signature, les paramètres spécifiés dans la signature sont disponibles dans le corps.</span><span class="sxs-lookup"><span data-stu-id="b0787-2414">If the anonymous function includes a signature, the parameters specified in the signature are available in the body.</span></span> <span data-ttu-id="b0787-2415">Si la fonction anonyme n’a pas de signature, elle peut être convertie en type délégué ou type d’expression ayant des paramètres ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)), mais les paramètres ne sont pas accessibles dans le corps.</span><span class="sxs-lookup"><span data-stu-id="b0787-2415">If the anonymous function has no signature it can be converted to a delegate type or expression type having parameters ([Anonymous function conversions](conversions.md#anonymous-function-conversions)), but the parameters cannot be accessed in the body.</span></span>
*  <span data-ttu-id="b0787-2416">À l’exception des paramètres `ref` ou `out` spécifiés dans la signature (le cas échéant) de la fonction anonyme englobante la plus proche, il s’agit d’une erreur au moment de la compilation pour que le corps accède à un paramètre `ref` ou `out`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2416">Except for `ref` or `out` parameters specified in the signature (if any) of the nearest enclosing anonymous function, it is a compile-time error for the body to access a `ref` or `out` parameter.</span></span>
*  <span data-ttu-id="b0787-2417">Lorsque le type de `this` est un type struct, il s’agit d’une erreur au moment de la compilation pour que le corps accède à `this`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2417">When the type of `this` is a struct type, it is a compile-time error for the body to access `this`.</span></span> <span data-ttu-id="b0787-2418">Cela est vrai, que l’accès soit explicite (comme dans `this.x`) ou implicite (comme dans `x` où `x` est un membre d’instance du struct).</span><span class="sxs-lookup"><span data-stu-id="b0787-2418">This is true whether the access is explicit (as in `this.x`) or implicit (as in `x` where `x` is an instance member of the struct).</span></span> <span data-ttu-id="b0787-2419">Cette règle interdit simplement cet accès et n’affecte pas si la recherche de membres est un membre du struct.</span><span class="sxs-lookup"><span data-stu-id="b0787-2419">This rule simply prohibits such access and does not affect whether member lookup results in a member of the struct.</span></span>
*  <span data-ttu-id="b0787-2420">Le corps a accès aux variables externes ([variables externes](expressions.md#outer-variables)) de la fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="b0787-2420">The body has access to the outer variables ([Outer variables](expressions.md#outer-variables)) of the anonymous function.</span></span> <span data-ttu-id="b0787-2421">L’accès à une variable externe fait référence à l’instance de la variable qui est active au moment où le *lambda_expression* ou *anonymous_method_expression* est évalué ([évaluation des expressions de fonction anonymes](expressions.md#evaluation-of-anonymous-function-expressions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2421">Access of an outer variable will reference the instance of the variable that is active at the time the *lambda_expression* or *anonymous_method_expression* is evaluated ([Evaluation of anonymous function expressions](expressions.md#evaluation-of-anonymous-function-expressions)).</span></span>
*  <span data-ttu-id="b0787-2422">Il s’agit d’une erreur au moment de la compilation pour que le corps contienne une instruction `goto`, une instruction `break` ou une instruction `continue` dont la cible est en dehors du corps ou dans le corps d’une fonction anonyme contenue.</span><span class="sxs-lookup"><span data-stu-id="b0787-2422">It is a compile-time error for the body to contain a `goto` statement, `break` statement, or `continue` statement whose target is outside the body or within the body of a contained anonymous function.</span></span>
*  <span data-ttu-id="b0787-2423">Une instruction `return` dans le corps retourne le contrôle à partir d’un appel de la fonction anonyme englobante la plus proche, et non du membre de la fonction englobante.</span><span class="sxs-lookup"><span data-stu-id="b0787-2423">A `return` statement in the body returns control from an invocation of the nearest enclosing anonymous function, not from the enclosing function member.</span></span> <span data-ttu-id="b0787-2424">Une expression spécifiée dans une instruction `return` doit être implicitement convertible en type de retour du type délégué ou du type d’arborescence d’expression vers lequel le *lambda_expression* ou le *anonymous_method_expression* englobant le plus proche est converti ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2424">An expression specified in a `return` statement must be implicitly convertible to the return type of the delegate type or expression tree type to which the nearest enclosing *lambda_expression* or *anonymous_method_expression* is converted ([Anonymous function conversions](conversions.md#anonymous-function-conversions)).</span></span>

<span data-ttu-id="b0787-2425">Elle n’est pas explicitement spécifiée, qu’il existe un moyen d’exécuter le bloc d’une fonction anonyme autre que par le biais de l’évaluation et de l’appel du *lambda_expression* ou *anonymous_method_expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-2425">It is explicitly unspecified whether there is any way to execute the block of an anonymous function other than through evaluation and invocation of the *lambda_expression* or *anonymous_method_expression*.</span></span> <span data-ttu-id="b0787-2426">En particulier, le compilateur peut choisir d’implémenter une fonction anonyme en synthétisant une ou plusieurs méthodes ou types nommés.</span><span class="sxs-lookup"><span data-stu-id="b0787-2426">In particular, the compiler may choose to implement an anonymous function by synthesizing one or more named methods or types.</span></span> <span data-ttu-id="b0787-2427">Les noms de ces éléments synthétisés doivent être d’une forme réservée à l’utilisation du compilateur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2427">The names of any such synthesized elements must be of a form reserved for compiler use.</span></span>

### <a name="overload-resolution-and-anonymous-functions"></a><span data-ttu-id="b0787-2428">Résolution de surcharge et fonctions anonymes</span><span class="sxs-lookup"><span data-stu-id="b0787-2428">Overload resolution and anonymous functions</span></span>

<span data-ttu-id="b0787-2429">Les fonctions anonymes dans une liste d’arguments participent à l’inférence de type et à la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="b0787-2429">Anonymous functions in an argument list participate in type inference and overload resolution.</span></span> <span data-ttu-id="b0787-2430">Reportez-vous à l' [inférence de type](expressions.md#type-inference) et à la [résolution de surcharge](expressions.md#overload-resolution) pour les règles exactes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2430">Please refer to [Type inference](expressions.md#type-inference) and [Overload resolution](expressions.md#overload-resolution) for the exact rules.</span></span>

<span data-ttu-id="b0787-2431">L’exemple suivant illustre l’effet des fonctions anonymes sur la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="b0787-2431">The following example illustrates the effect of anonymous functions on overload resolution.</span></span>

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

<span data-ttu-id="b0787-2432">La classe `ItemList<T>` a deux méthodes `Sum`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2432">The `ItemList<T>` class has two `Sum` methods.</span></span> <span data-ttu-id="b0787-2433">Chaque accepte un argument `selector`, qui extrait la valeur à additionner d’un élément de liste.</span><span class="sxs-lookup"><span data-stu-id="b0787-2433">Each takes a `selector` argument, which extracts the value to sum over from a list item.</span></span> <span data-ttu-id="b0787-2434">La valeur extraite peut être un `int` ou un `double` et la somme résultante est également un `int` ou un `double`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2434">The extracted value can be either an `int` or a `double` and the resulting sum is likewise either an `int` or a `double`.</span></span>

<span data-ttu-id="b0787-2435">Les méthodes de `Sum` peuvent par exemple être utilisées pour calculer des sommes à partir d’une liste de lignes de détails dans un ordre.</span><span class="sxs-lookup"><span data-stu-id="b0787-2435">The `Sum` methods could for example be used to compute sums from a list of detail lines in an order.</span></span>

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

<span data-ttu-id="b0787-2436">Lors du premier appel de `orderDetails.Sum`, les deux méthodes `Sum` sont applicables, car la fonction anonyme `d => d. UnitCount` est compatible avec `Func<Detail,int>` et `Func<Detail,double>`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2436">In the first invocation of `orderDetails.Sum`, both `Sum` methods are applicable because the anonymous function `d => d. UnitCount` is compatible with both `Func<Detail,int>` and `Func<Detail,double>`.</span></span> <span data-ttu-id="b0787-2437">Toutefois, la résolution de surcharge choisit la première méthode `Sum`, car la conversion en `Func<Detail,int>` est meilleure que la conversion en `Func<Detail,double>`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2437">However, overload resolution picks the first `Sum` method because the conversion to `Func<Detail,int>` is better than the conversion to `Func<Detail,double>`.</span></span>

<span data-ttu-id="b0787-2438">Dans le deuxième appel de `orderDetails.Sum`, seule la deuxième méthode `Sum` est applicable, car la fonction anonyme `d => d.UnitPrice * d.UnitCount` produit une valeur de type `double`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2438">In the second invocation of `orderDetails.Sum`, only the second `Sum` method is applicable because the anonymous function `d => d.UnitPrice * d.UnitCount` produces a value of type `double`.</span></span> <span data-ttu-id="b0787-2439">Par conséquent, la résolution de surcharge sélectionne la deuxième méthode `Sum` pour cet appel.</span><span class="sxs-lookup"><span data-stu-id="b0787-2439">Thus, overload resolution picks the second `Sum` method for that invocation.</span></span>

### <a name="anonymous-functions-and-dynamic-binding"></a><span data-ttu-id="b0787-2440">Fonctions anonymes et liaison dynamique</span><span class="sxs-lookup"><span data-stu-id="b0787-2440">Anonymous functions and dynamic binding</span></span>

<span data-ttu-id="b0787-2441">Une fonction anonyme ne peut pas être un récepteur, un argument ou un opérande d’une opération liée dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="b0787-2441">An anonymous function cannot be a receiver, argument or operand of a dynamically bound operation.</span></span>

### <a name="outer-variables"></a><span data-ttu-id="b0787-2442">Variables externes</span><span class="sxs-lookup"><span data-stu-id="b0787-2442">Outer variables</span></span>

<span data-ttu-id="b0787-2443">Toute variable locale, paramètre de valeur ou tableau de paramètres dont la portée comprend la *lambda_expression* ou *anonymous_method_expression* est appelée une ***variable externe*** de la fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="b0787-2443">Any local variable, value parameter, or parameter array whose scope includes the *lambda_expression* or *anonymous_method_expression* is called an ***outer variable*** of the anonymous function.</span></span> <span data-ttu-id="b0787-2444">Dans une fonction membre d’instance d’une classe, la valeur `this` est considérée comme un paramètre de valeur et est une variable externe de toute fonction anonyme contenue dans la fonction membre.</span><span class="sxs-lookup"><span data-stu-id="b0787-2444">In an instance function member of a class, the `this` value is considered a value parameter and is an outer variable of any anonymous function contained within the function member.</span></span>

#### <a name="captured-outer-variables"></a><span data-ttu-id="b0787-2445">Variables externes capturées</span><span class="sxs-lookup"><span data-stu-id="b0787-2445">Captured outer variables</span></span>

<span data-ttu-id="b0787-2446">Lorsqu’une variable externe est référencée par une fonction anonyme, la variable externe est dite ***capturée*** par la fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="b0787-2446">When an outer variable is referenced by an anonymous function, the outer variable is said to have been ***captured*** by the anonymous function.</span></span> <span data-ttu-id="b0787-2447">En règle générale, la durée de vie d’une variable locale est limitée à l’exécution du bloc ou de l’instruction à laquelle elle est associée ([variables locales](variables.md#local-variables)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2447">Ordinarily, the lifetime of a local variable is limited to execution of the block or statement with which it is associated ([Local variables](variables.md#local-variables)).</span></span> <span data-ttu-id="b0787-2448">Toutefois, la durée de vie d’une variable externe capturée est étendue au moins jusqu’à ce que le délégué ou l’arborescence d’expressions créé à partir de la fonction anonyme devienne éligible pour garbage collection.</span><span class="sxs-lookup"><span data-stu-id="b0787-2448">However, the lifetime of a captured outer variable is extended at least until the delegate or expression tree created from the anonymous function becomes eligible for garbage collection.</span></span>

<span data-ttu-id="b0787-2449">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2449">In the example</span></span>
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
<span data-ttu-id="b0787-2450">la variable locale `x` est capturée par la fonction anonyme, et la durée de vie de `x` est étendue au moins jusqu’à ce que le délégué retourné par `F` devienne éligible pour garbage collection (ce qui ne se produit pas jusqu’à la fin du programme).</span><span class="sxs-lookup"><span data-stu-id="b0787-2450">the local variable `x` is captured by the anonymous function, and the lifetime of `x` is extended at least until the delegate returned from `F` becomes eligible for garbage collection (which doesn't happen until the very end of the program).</span></span> <span data-ttu-id="b0787-2451">Étant donné que chaque appel de la fonction anonyme opère sur la même instance de `x`, la sortie de l’exemple est la suivante :</span><span class="sxs-lookup"><span data-stu-id="b0787-2451">Since each invocation of the anonymous function operates on the same instance of `x`, the output of the example is:</span></span>
```console
1
2
3
```

<span data-ttu-id="b0787-2452">Lorsqu’une variable locale ou un paramètre de valeur est capturé par une fonction anonyme, la variable locale ou le paramètre n’est plus considéré comme une variable fixe ([variables fixes et déplaçables](unsafe-code.md#fixed-and-moveable-variables)), mais est considéré comme une variable déplaçable.</span><span class="sxs-lookup"><span data-stu-id="b0787-2452">When a local variable or a value parameter is captured by an anonymous function, the local variable or parameter is no longer considered to be a fixed variable ([Fixed and moveable variables](unsafe-code.md#fixed-and-moveable-variables)), but is instead considered to be a moveable variable.</span></span> <span data-ttu-id="b0787-2453">Ainsi, tout `unsafe` code qui prend l’adresse d’une variable externe capturée doit d’abord utiliser l’instruction `fixed` pour corriger la variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-2453">Thus any `unsafe` code that takes the address of a captured outer variable must first use the `fixed` statement to fix the variable.</span></span>

<span data-ttu-id="b0787-2454">Notez que contrairement à une variable non capturée, une variable locale capturée peut être exposée simultanément à plusieurs threads d’exécution.</span><span class="sxs-lookup"><span data-stu-id="b0787-2454">Note that unlike an uncaptured variable, a captured local variable can be simultaneously exposed to multiple threads of execution.</span></span>

#### <a name="instantiation-of-local-variables"></a><span data-ttu-id="b0787-2455">Instanciation de variables locales</span><span class="sxs-lookup"><span data-stu-id="b0787-2455">Instantiation of local variables</span></span>

<span data-ttu-id="b0787-2456">Une variable locale est considérée comme ***instanciée*** lorsque l’exécution entre dans l’étendue de la variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-2456">A local variable is considered to be ***instantiated*** when execution enters the scope of the variable.</span></span> <span data-ttu-id="b0787-2457">Par exemple, lorsque la méthode suivante est appelée, la variable locale `x` est instanciée et initialisée trois fois, une fois pour chaque itération de la boucle.</span><span class="sxs-lookup"><span data-stu-id="b0787-2457">For example, when the following method is invoked, the local variable `x` is instantiated and initialized three times—once for each iteration of the loop.</span></span>

```csharp
static void F() {
    for (int i = 0; i < 3; i++) {
        int x = i * 2 + 1;
        ...
    }
}
```

<span data-ttu-id="b0787-2458">Toutefois, le déplacement de la déclaration de `x` en dehors de la boucle entraîne une seule instanciation de `x`:</span><span class="sxs-lookup"><span data-stu-id="b0787-2458">However, moving the declaration of `x` outside the loop results in a single instantiation of `x`:</span></span>
```csharp
static void F() {
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        ...
    }
}
```

<span data-ttu-id="b0787-2459">Lorsqu’elles ne sont pas capturées, il n’existe aucun moyen d’observer exactement la fréquence à laquelle une variable locale est instanciée. étant donné que les durées de vie des instanciations sont disjointes, il est possible que chaque instanciation utilise simplement le même emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="b0787-2459">When not captured, there is no way to observe exactly how often a local variable is instantiated—because the lifetimes of the instantiations are disjoint, it is possible for each instantiation to simply use the same storage location.</span></span> <span data-ttu-id="b0787-2460">Toutefois, lorsqu’une fonction anonyme capture une variable locale, les effets de l’instanciation deviennent évidents.</span><span class="sxs-lookup"><span data-stu-id="b0787-2460">However, when an anonymous function captures a local variable, the effects of instantiation become apparent.</span></span>

<span data-ttu-id="b0787-2461">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2461">The example</span></span>
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
<span data-ttu-id="b0787-2462">génère cette sortie :</span><span class="sxs-lookup"><span data-stu-id="b0787-2462">produces the output:</span></span>
```console
1
3
5
```

<span data-ttu-id="b0787-2463">Toutefois, lorsque la déclaration de `x` est déplacée en dehors de la boucle :</span><span class="sxs-lookup"><span data-stu-id="b0787-2463">However, when the declaration of `x` is moved outside the loop:</span></span>
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
<span data-ttu-id="b0787-2464">le résultat est :</span><span class="sxs-lookup"><span data-stu-id="b0787-2464">the output is:</span></span>
```console
5
5
5
```

<span data-ttu-id="b0787-2465">Si une boucle for déclare une variable d’itération, cette variable est considérée comme déclarée en dehors de la boucle.</span><span class="sxs-lookup"><span data-stu-id="b0787-2465">If a for-loop declares an iteration variable, that variable itself is considered to be declared outside of the loop.</span></span> <span data-ttu-id="b0787-2466">Ainsi, si l’exemple est modifié pour capturer la variable d’itération elle-même :</span><span class="sxs-lookup"><span data-stu-id="b0787-2466">Thus, if the example is changed to capture the iteration variable itself:</span></span>

```csharp
static D[] F() {
    D[] result = new D[3];
    for (int i = 0; i < 3; i++) {
        result[i] = () => { Console.WriteLine(i); };
    }
    return result;
}
```
<span data-ttu-id="b0787-2467">une seule instance de la variable d’itération est capturée, ce qui génère la sortie :</span><span class="sxs-lookup"><span data-stu-id="b0787-2467">only one instance of the iteration variable is captured, which produces the output:</span></span>
```console
3
3
3
```

<span data-ttu-id="b0787-2468">Il est possible que les délégués de fonction anonymes partagent des variables capturées, mais des instances distinctes d’autres.</span><span class="sxs-lookup"><span data-stu-id="b0787-2468">It is possible for anonymous function delegates to share some captured variables yet have separate instances of others.</span></span> <span data-ttu-id="b0787-2469">Par exemple, si `F` est remplacé par</span><span class="sxs-lookup"><span data-stu-id="b0787-2469">For example, if `F` is changed to</span></span>
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
<span data-ttu-id="b0787-2470">les trois délégués capturent la même instance de `x` mais des instances distinctes de `y`, et la sortie est :</span><span class="sxs-lookup"><span data-stu-id="b0787-2470">the three delegates capture the same instance of `x` but separate instances of `y`, and the output is:</span></span>
```console
1 1
2 1
3 1
```

<span data-ttu-id="b0787-2471">Des fonctions anonymes distinctes peuvent capturer la même instance d’une variable externe.</span><span class="sxs-lookup"><span data-stu-id="b0787-2471">Separate anonymous functions can capture the same instance of an outer variable.</span></span> <span data-ttu-id="b0787-2472">Dans l'exemple :</span><span class="sxs-lookup"><span data-stu-id="b0787-2472">In the example:</span></span>
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
<span data-ttu-id="b0787-2473">les deux fonctions anonymes capturent la même instance de la variable locale `x`, et elles peuvent donc « communiquer » par le biais de cette variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-2473">the two anonymous functions capture the same instance of the local variable `x`, and they can thus "communicate" through that variable.</span></span> <span data-ttu-id="b0787-2474">La sortie de l’exemple est la suivante :</span><span class="sxs-lookup"><span data-stu-id="b0787-2474">The output of the example is:</span></span>
```console
5
10
```

### <a name="evaluation-of-anonymous-function-expressions"></a><span data-ttu-id="b0787-2475">Évaluation des expressions de fonction anonymes</span><span class="sxs-lookup"><span data-stu-id="b0787-2475">Evaluation of anonymous function expressions</span></span>

<span data-ttu-id="b0787-2476">Une fonction anonyme `F` doit toujours être convertie en un type délégué `D` ou un type d’arborescence d’expression `E`, soit directement, soit par le biais de l’exécution d’une expression de création de délégué `new D(F)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2476">An anonymous function `F` must always be converted to a delegate type `D` or an expression tree type `E`, either directly or through the execution of a delegate creation expression `new D(F)`.</span></span> <span data-ttu-id="b0787-2477">Cette conversion détermine le résultat de la fonction anonyme, comme décrit dans [conversions de fonctions anonymes](conversions.md#anonymous-function-conversions).</span><span class="sxs-lookup"><span data-stu-id="b0787-2477">This conversion determines the result of the anonymous function, as described in [Anonymous function conversions](conversions.md#anonymous-function-conversions).</span></span>

## <a name="query-expressions"></a><span data-ttu-id="b0787-2478">Expressions de requête</span><span class="sxs-lookup"><span data-stu-id="b0787-2478">Query expressions</span></span>

<span data-ttu-id="b0787-2479">Les ***expressions de requête*** fournissent une syntaxe intégrée au langage pour les requêtes qui est semblable aux langages de requête relationnels et hiérarchiques tels que SQL et XQuery.</span><span class="sxs-lookup"><span data-stu-id="b0787-2479">***Query expressions*** provide a language integrated syntax for queries that is similar to relational and hierarchical query languages such as SQL and XQuery.</span></span>

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

<span data-ttu-id="b0787-2480">Une expression de requête commence par une clause `from` et se termine par une clause `select` ou `group`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2480">A query expression begins with a `from` clause and ends with either a `select` or `group` clause.</span></span> <span data-ttu-id="b0787-2481">La clause de `from` initiale peut être suivie par zéro, une ou plusieurs clauses `from`, `let`, `where`, `join` ou `orderby`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2481">The initial `from` clause can be followed by zero or more `from`, `let`, `where`, `join` or `orderby` clauses.</span></span> <span data-ttu-id="b0787-2482">Chaque clause `from` est un générateur qui introduit une ***variable de portée*** qui s’étend sur les éléments d’une ***séquence***.</span><span class="sxs-lookup"><span data-stu-id="b0787-2482">Each `from` clause is a generator introducing a ***range variable*** which ranges over the elements of a ***sequence***.</span></span> <span data-ttu-id="b0787-2483">Chaque clause `let` introduit une variable de portée représentant une valeur calculée au moyen de variables de plage précédentes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2483">Each `let` clause introduces a range variable representing a value computed by means of previous range variables.</span></span> <span data-ttu-id="b0787-2484">Chaque `where` clause est un filtre qui exclut des éléments du résultat.</span><span class="sxs-lookup"><span data-stu-id="b0787-2484">Each `where` clause is a filter that excludes items from the result.</span></span> <span data-ttu-id="b0787-2485">Chaque clause `join` compare les clés spécifiées de la séquence source avec les clés d’une autre séquence, ce qui génère des paires correspondantes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2485">Each `join` clause compares specified keys of the source sequence with keys of another sequence, yielding matching pairs.</span></span> <span data-ttu-id="b0787-2486">Chaque clause `orderby` réorganise les éléments en fonction des critères spécifiés. La dernière clause `select` ou `group` spécifie la forme du résultat en termes de variables de plage.</span><span class="sxs-lookup"><span data-stu-id="b0787-2486">Each `orderby` clause reorders items according to specified criteria.The final `select` or `group` clause specifies the shape of the result in terms of the range variables.</span></span> <span data-ttu-id="b0787-2487">Enfin, une clause `into` peut être utilisée pour « épisser » des requêtes en traitant les résultats d’une requête sous la forme d’un générateur dans une requête suivante.</span><span class="sxs-lookup"><span data-stu-id="b0787-2487">Finally, an `into` clause can be used to "splice" queries by treating the results of one query as a generator in a subsequent query.</span></span>

### <a name="ambiguities-in-query-expressions"></a><span data-ttu-id="b0787-2488">Ambiguïtés dans les expressions de requête</span><span class="sxs-lookup"><span data-stu-id="b0787-2488">Ambiguities in query expressions</span></span>

<span data-ttu-id="b0787-2489">Les expressions de requête contiennent un certain nombre de « mots-clés contextuels », c’est-à-dire des identificateurs qui ont une signification particulière dans un contexte donné.</span><span class="sxs-lookup"><span data-stu-id="b0787-2489">Query expressions contain a number of "contextual keywords", i.e., identifiers that have special meaning in a given context.</span></span> <span data-ttu-id="b0787-2490">En particulier, il s’agit de `from`, `where`, `join`, `on`, `equals`, `into`, `let`, `orderby`, `ascending`, `descending`, `select`, `group` et `by`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2490">Specifically these are `from`, `where`, `join`, `on`, `equals`, `into`, `let`, `orderby`, `ascending`, `descending`, `select`, `group` and `by`.</span></span> <span data-ttu-id="b0787-2491">Afin d’éviter les ambiguïtés dans les expressions de requête provoquées par l’utilisation mixte de ces identificateurs en tant que Mots clés ou noms simples, ces identificateurs sont considérés comme des mots clés lorsqu’ils se trouvent n’importe où dans une expression de requête.</span><span class="sxs-lookup"><span data-stu-id="b0787-2491">In order to avoid ambiguities in query expressions caused by mixed use of these identifiers as keywords or simple names, these identifiers are considered keywords when occurring anywhere within a query expression.</span></span>

<span data-ttu-id="b0787-2492">À cet effet, une expression de requête est une expression qui commence par «`from identifier`» suivi de n’importe quel jeton, à l’exception de «`;`», «`=`» ou «`,`».</span><span class="sxs-lookup"><span data-stu-id="b0787-2492">For this purpose, a query expression is any expression that starts with "`from identifier`" followed by any token except "`;`", "`=`" or "`,`".</span></span>

<span data-ttu-id="b0787-2493">Pour pouvoir utiliser ces mots comme identificateurs dans une expression de requête, ils peuvent être précédés de «`@`» ([identificateurs](lexical-structure.md#identifiers)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2493">In order to use these words as identifiers within a query expression, they can be prefixed with "`@`" ([Identifiers](lexical-structure.md#identifiers)).</span></span>

### <a name="query-expression-translation"></a><span data-ttu-id="b0787-2494">Traduction d’expression de requête</span><span class="sxs-lookup"><span data-stu-id="b0787-2494">Query expression translation</span></span>

<span data-ttu-id="b0787-2495">Le C# langage ne spécifie pas la sémantique d’exécution des expressions de requête.</span><span class="sxs-lookup"><span data-stu-id="b0787-2495">The C# language does not specify the execution semantics of query expressions.</span></span> <span data-ttu-id="b0787-2496">Au lieu de cela, les expressions de requête sont traduites en appels de méthodes qui adhèrent au *modèle d’expression de requête* ([le modèle d’expression de requête](expressions.md#the-query-expression-pattern)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2496">Rather, query expressions are translated into invocations of methods that adhere to the *query expression pattern* ([The query expression pattern](expressions.md#the-query-expression-pattern)).</span></span> <span data-ttu-id="b0787-2497">Plus précisément, les expressions de requête sont traduites en appels de méthodes nommées `Where`, `Select`, `SelectMany`, `Join`, `GroupJoin`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `GroupBy`et `Cast`. Ces méthodes sont supposées avoir des signatures et des types de résultats particuliers, comme décrit dans [le modèle d’expression de requête](expressions.md#the-query-expression-pattern).</span><span class="sxs-lookup"><span data-stu-id="b0787-2497">Specifically, query expressions are translated into invocations of methods named `Where`, `Select`, `SelectMany`, `Join`, `GroupJoin`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `GroupBy`, and `Cast`.These methods are expected to have particular signatures and result types, as described in [The query expression pattern](expressions.md#the-query-expression-pattern).</span></span> <span data-ttu-id="b0787-2498">Ces méthodes peuvent être des méthodes d’instance de l’objet interrogé ou des méthodes d’extension qui sont externes à l’objet, et elles implémentent l’exécution réelle de la requête.</span><span class="sxs-lookup"><span data-stu-id="b0787-2498">These methods can be instance methods of the object being queried or extension methods that are external to the object, and they implement the actual execution of the query.</span></span>

<span data-ttu-id="b0787-2499">La traduction des expressions de requête en appels de méthode est un mappage syntaxique qui se produit avant l’exécution d’une quelconque liaison de type ou de surcharge.</span><span class="sxs-lookup"><span data-stu-id="b0787-2499">The translation from query expressions to method invocations is a syntactic mapping that occurs before any type binding or overload resolution has been performed.</span></span> <span data-ttu-id="b0787-2500">La syntaxe de la traduction est garantie, mais il n’est pas garanti que la génération de code est C# correcte.</span><span class="sxs-lookup"><span data-stu-id="b0787-2500">The translation is guaranteed to be syntactically correct, but it is not guaranteed to produce semantically correct C# code.</span></span> <span data-ttu-id="b0787-2501">À la suite de la traduction des expressions de requête, les appels de méthode résultants sont traités comme des appels de méthode normaux, ce qui peut à son tour dévoiler les erreurs, par exemple si les méthodes n’existent pas, si les arguments ont des types incorrects, ou si les méthodes sont génériques et l’inférence de type échoue.</span><span class="sxs-lookup"><span data-stu-id="b0787-2501">Following translation of query expressions, the resulting method invocations are processed as regular method invocations, and this may in turn uncover errors, for example if the methods do not exist, if arguments have wrong types, or if the methods are generic and type inference fails.</span></span>

<span data-ttu-id="b0787-2502">Une expression de requête est traitée en appliquant à plusieurs reprises les traductions suivantes jusqu’à ce qu’aucune réduction supplémentaire ne soit possible.</span><span class="sxs-lookup"><span data-stu-id="b0787-2502">A query expression is processed by repeatedly applying the following translations until no further reductions are possible.</span></span> <span data-ttu-id="b0787-2503">Les traductions sont répertoriées dans l’ordre de l’application : chaque section suppose que les traductions dans les sections précédentes ont été effectuées de façon exhaustive, et une fois épuisées, une section n’est pas revisitée ultérieurement dans le traitement de la même expression de requête.</span><span class="sxs-lookup"><span data-stu-id="b0787-2503">The translations are listed in order of application: each section assumes that the translations in the preceding sections have been performed exhaustively, and once exhausted, a section will not later be revisited in the processing of the same query expression.</span></span>

<span data-ttu-id="b0787-2504">L’assignation à des variables de portée n’est pas autorisée dans les expressions de requête.</span><span class="sxs-lookup"><span data-stu-id="b0787-2504">Assignment to range variables is not allowed in query expressions.</span></span> <span data-ttu-id="b0787-2505">Toutefois, C# une implémentation est autorisée à ne pas toujours appliquer cette restriction, car cela peut parfois ne pas être possible avec le schéma de traduction syntaxique présenté ici.</span><span class="sxs-lookup"><span data-stu-id="b0787-2505">However a C# implementation is permitted to not always enforce this restriction, since this may sometimes not be possible with the syntactic translation scheme presented here.</span></span>

<span data-ttu-id="b0787-2506">Certaines traductions injectent des variables de portée avec des identificateurs transparents dénotés par `*`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2506">Certain translations inject range variables with transparent identifiers denoted by `*`.</span></span> <span data-ttu-id="b0787-2507">Les propriétés spéciales des identificateurs transparents sont décrites plus en détail dans [identificateurs transparents](expressions.md#transparent-identifiers).</span><span class="sxs-lookup"><span data-stu-id="b0787-2507">The special properties of transparent identifiers are discussed further in [Transparent identifiers](expressions.md#transparent-identifiers).</span></span>

#### <a name="select-and-groupby-clauses-with-continuations"></a><span data-ttu-id="b0787-2508">Clauses SELECT et GroupBy avec continuations</span><span class="sxs-lookup"><span data-stu-id="b0787-2508">Select and groupby clauses with continuations</span></span>

<span data-ttu-id="b0787-2509">Expression de requête avec une continuation</span><span class="sxs-lookup"><span data-stu-id="b0787-2509">A query expression with a continuation</span></span>
```csharp
from ... into x ...
```
<span data-ttu-id="b0787-2510">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2510">is translated into</span></span>
```csharp
from x in ( from ... ) ...
```

<span data-ttu-id="b0787-2511">Les traductions des sections suivantes supposent que les requêtes n’ont aucune continuation `into`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2511">The translations in the following sections assume that queries have no `into` continuations.</span></span>

<span data-ttu-id="b0787-2512">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2512">The example</span></span>
```csharp
from c in customers
group c by c.Country into g
select new { Country = g.Key, CustCount = g.Count() }
```
<span data-ttu-id="b0787-2513">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2513">is translated into</span></span>
```csharp
from g in
    from c in customers
    group c by c.Country
select new { Country = g.Key, CustCount = g.Count() }
```
<span data-ttu-id="b0787-2514">traduction finale de qui est</span><span class="sxs-lookup"><span data-stu-id="b0787-2514">the final translation of which is</span></span>
```csharp
customers.
GroupBy(c => c.Country).
Select(g => new { Country = g.Key, CustCount = g.Count() })
```

#### <a name="explicit-range-variable-types"></a><span data-ttu-id="b0787-2515">Types de variable de portée explicite</span><span class="sxs-lookup"><span data-stu-id="b0787-2515">Explicit range variable types</span></span>

<span data-ttu-id="b0787-2516">Une clause `from` qui spécifie explicitement un type de variable de portée</span><span class="sxs-lookup"><span data-stu-id="b0787-2516">A `from` clause that explicitly specifies a range variable type</span></span>
```csharp
from T x in e
```
<span data-ttu-id="b0787-2517">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2517">is translated into</span></span>
```csharp
from x in ( e ) . Cast < T > ( )
```

<span data-ttu-id="b0787-2518">Une clause `join` qui spécifie explicitement un type de variable de portée</span><span class="sxs-lookup"><span data-stu-id="b0787-2518">A `join` clause that explicitly specifies a range variable type</span></span>
```csharp
join T x in e on k1 equals k2
```
<span data-ttu-id="b0787-2519">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2519">is translated into</span></span>
```csharp
join x in ( e ) . Cast < T > ( ) on k1 equals k2
```

<span data-ttu-id="b0787-2520">Les traductions dans les sections suivantes supposent que les requêtes n’ont pas de types de variable de portée explicite.</span><span class="sxs-lookup"><span data-stu-id="b0787-2520">The translations in the following sections assume that queries have no explicit range variable types.</span></span>

<span data-ttu-id="b0787-2521">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2521">The example</span></span>
```csharp
from Customer c in customers
where c.City == "London"
select c
```
<span data-ttu-id="b0787-2522">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2522">is translated into</span></span>
```csharp
from c in customers.Cast<Customer>()
where c.City == "London"
select c
```
<span data-ttu-id="b0787-2523">traduction finale de qui est</span><span class="sxs-lookup"><span data-stu-id="b0787-2523">the final translation of which is</span></span>
```csharp
customers.
Cast<Customer>().
Where(c => c.City == "London")
```

<span data-ttu-id="b0787-2524">Les types de variable de portée explicite sont utiles pour interroger des collections qui implémentent l’interface `IEnumerable` non générique, mais pas l’interface de `IEnumerable<T>` générique.</span><span class="sxs-lookup"><span data-stu-id="b0787-2524">Explicit range variable types are useful for querying collections that implement the non-generic `IEnumerable` interface, but not the generic `IEnumerable<T>` interface.</span></span> <span data-ttu-id="b0787-2525">Dans l’exemple ci-dessus, ce serait le cas si `customers` était de type `ArrayList`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2525">In the example above, this would be the case if `customers` were of type `ArrayList`.</span></span>

#### <a name="degenerate-query-expressions"></a><span data-ttu-id="b0787-2526">Dégénérer des expressions de requête</span><span class="sxs-lookup"><span data-stu-id="b0787-2526">Degenerate query expressions</span></span>

<span data-ttu-id="b0787-2527">Expression de requête de la forme</span><span class="sxs-lookup"><span data-stu-id="b0787-2527">A query expression of the form</span></span>
```csharp
from x in e select x
```
<span data-ttu-id="b0787-2528">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2528">is translated into</span></span>
```csharp
( e ) . Select ( x => x )
```

<span data-ttu-id="b0787-2529">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2529">The example</span></span>
```csharp
from c in customers
select c
```
<span data-ttu-id="b0787-2530">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2530">is translated into</span></span>
```csharp
customers.Select(c => c)
```

<span data-ttu-id="b0787-2531">Une expression de requête dégénérée est une expression qui sélectionne de façon triviale les éléments de la source.</span><span class="sxs-lookup"><span data-stu-id="b0787-2531">A degenerate query expression is one that trivially selects the elements of the source.</span></span> <span data-ttu-id="b0787-2532">Une phase ultérieure de la traduction supprime les requêtes dégénérées introduites par d’autres étapes de traduction en les remplaçant par leur source.</span><span class="sxs-lookup"><span data-stu-id="b0787-2532">A later phase of the translation removes degenerate queries introduced by other translation steps by replacing them with their source.</span></span> <span data-ttu-id="b0787-2533">Toutefois, il est important de s’assurer que le résultat d’une expression de requête n’est jamais l’objet source lui-même, car cela révélerait le type et l’identité de la source au client de la requête.</span><span class="sxs-lookup"><span data-stu-id="b0787-2533">It is important however to ensure that the result of a query expression is never the source object itself, as that would reveal the type and identity of the source to the client of the query.</span></span> <span data-ttu-id="b0787-2534">Par conséquent, cette étape protège les requêtes dégénérées écrites directement dans le code source en appelant explicitement `Select` sur la source.</span><span class="sxs-lookup"><span data-stu-id="b0787-2534">Therefore this step protects degenerate queries written directly in source code by explicitly calling `Select` on the source.</span></span> <span data-ttu-id="b0787-2535">Il s’agit ensuite des implémenteurs de `Select` et d’autres opérateurs de requête pour garantir que ces méthodes ne retournent jamais l’objet source lui-même.</span><span class="sxs-lookup"><span data-stu-id="b0787-2535">It is then up to the implementers of `Select` and other query operators to ensure that these methods never return the source object itself.</span></span>

#### <a name="from-let-where-join-and-orderby-clauses"></a><span data-ttu-id="b0787-2536">Clauses from, Let, Where, Join et OrderBy</span><span class="sxs-lookup"><span data-stu-id="b0787-2536">From, let, where, join and orderby clauses</span></span>

<span data-ttu-id="b0787-2537">Expression de requête avec une deuxième clause `from` suivie d’une clause `select`</span><span class="sxs-lookup"><span data-stu-id="b0787-2537">A query expression with a second `from` clause followed by a `select` clause</span></span>
```csharp
from x1 in e1
from x2 in e2
select v
```
<span data-ttu-id="b0787-2538">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2538">is translated into</span></span>
```csharp
( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => v )
```

<span data-ttu-id="b0787-2539">Une expression de requête avec une deuxième clause `from` suivie d’autres éléments qu’une clause `select` :</span><span class="sxs-lookup"><span data-stu-id="b0787-2539">A query expression with a second `from` clause followed by something other than a `select` clause:</span></span>

```csharp
from x1 in e1
from x2 in e2
...
```
<span data-ttu-id="b0787-2540">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2540">is translated into</span></span>
```csharp
from * in ( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => new { x1 , x2 } )
...
```

<span data-ttu-id="b0787-2541">Expression de requête avec une clause `let`</span><span class="sxs-lookup"><span data-stu-id="b0787-2541">A query expression with a `let` clause</span></span>
```csharp
from x in e
let y = f
...
```
<span data-ttu-id="b0787-2542">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2542">is translated into</span></span>
```csharp
from * in ( e ) . Select ( x => new { x , y = f } )
...
```

<span data-ttu-id="b0787-2543">Expression de requête avec une clause `where`</span><span class="sxs-lookup"><span data-stu-id="b0787-2543">A query expression with a `where` clause</span></span>
```csharp
from x in e
where f
...
```
<span data-ttu-id="b0787-2544">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2544">is translated into</span></span>
```csharp
from x in ( e ) . Where ( x => f )
...
```

<span data-ttu-id="b0787-2545">Expression de requête avec une clause `join` sans `into` suivie d’une clause `select`</span><span class="sxs-lookup"><span data-stu-id="b0787-2545">A query expression with a `join` clause without an `into` followed by a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
select v
```
<span data-ttu-id="b0787-2546">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2546">is translated into</span></span>
```csharp
( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => v )
```

<span data-ttu-id="b0787-2547">Une expression de requête avec une clause `join` sans `into` suivie d’autres éléments qu’une clause `select`</span><span class="sxs-lookup"><span data-stu-id="b0787-2547">A query expression with a `join` clause without an `into` followed by something other than a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
...
```
<span data-ttu-id="b0787-2548">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2548">is translated into</span></span>
```csharp
from * in ( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => new { x1 , x2 })
...
```

<span data-ttu-id="b0787-2549">Expression de requête avec une clause `join` avec une `into` suivie d’une clause `select`</span><span class="sxs-lookup"><span data-stu-id="b0787-2549">A query expression with a `join` clause with an `into` followed by a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
select v
```
<span data-ttu-id="b0787-2550">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2550">is translated into</span></span>
```csharp
( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => v )
```

<span data-ttu-id="b0787-2551">Expression de requête avec une clause `join` avec un `into` suivi d’un autre nom qu’une clause `select`</span><span class="sxs-lookup"><span data-stu-id="b0787-2551">A query expression with a `join` clause with an `into` followed by something other than a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
...
```
<span data-ttu-id="b0787-2552">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2552">is translated into</span></span>
```csharp
from * in ( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => new { x1 , g })
...
```

<span data-ttu-id="b0787-2553">Expression de requête avec une clause `orderby`</span><span class="sxs-lookup"><span data-stu-id="b0787-2553">A query expression with an `orderby` clause</span></span>
```csharp
from x in e
orderby k1 , k2 , ..., kn
...
```
<span data-ttu-id="b0787-2554">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2554">is translated into</span></span>
```csharp
from x in ( e ) . 
OrderBy ( x => k1 ) . 
ThenBy ( x => k2 ) .
... .
ThenBy ( x => kn )
...
```

<span data-ttu-id="b0787-2555">Si une clause de classement spécifie un indicateur de direction `descending`, un appel de `OrderByDescending` ou `ThenByDescending` est généré à la place.</span><span class="sxs-lookup"><span data-stu-id="b0787-2555">If an ordering clause specifies a `descending` direction indicator, an invocation of `OrderByDescending` or `ThenByDescending` is produced instead.</span></span>

<span data-ttu-id="b0787-2556">Les traductions suivantes supposent qu’il n’y a pas de clauses `let`, `where`, `join` ou `orderby`, et pas plus que la seule clause `from` initiale dans chaque expression de requête.</span><span class="sxs-lookup"><span data-stu-id="b0787-2556">The following translations assume that there are no `let`, `where`, `join` or `orderby` clauses, and no more than the one initial `from` clause in each query expression.</span></span>

<span data-ttu-id="b0787-2557">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2557">The example</span></span>
```csharp
from c in customers
from o in c.Orders
select new { c.Name, o.OrderID, o.Total }
```
<span data-ttu-id="b0787-2558">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2558">is translated into</span></span>
```csharp
customers.
SelectMany(c => c.Orders,
     (c,o) => new { c.Name, o.OrderID, o.Total }
)
```

<span data-ttu-id="b0787-2559">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2559">The example</span></span>
```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```
<span data-ttu-id="b0787-2560">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2560">is translated into</span></span>
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```
<span data-ttu-id="b0787-2561">traduction finale de qui est</span><span class="sxs-lookup"><span data-stu-id="b0787-2561">the final translation of which is</span></span>
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.OrderID, x.o.Total })
```
<span data-ttu-id="b0787-2562">où `x` est un identificateur généré par le compilateur qui est autrement invisible et inaccessible.</span><span class="sxs-lookup"><span data-stu-id="b0787-2562">where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="b0787-2563">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2563">The example</span></span>
```csharp
from o in orders
let t = o.Details.Sum(d => d.UnitPrice * d.Quantity)
where t >= 1000
select new { o.OrderID, Total = t }
```
<span data-ttu-id="b0787-2564">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2564">is translated into</span></span>
```csharp
from * in orders.
    Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) })
where t >= 1000 
select new { o.OrderID, Total = t }
```
<span data-ttu-id="b0787-2565">traduction finale de qui est</span><span class="sxs-lookup"><span data-stu-id="b0787-2565">the final translation of which is</span></span>
```csharp
orders.
Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) }).
Where(x => x.t >= 1000).
Select(x => new { x.o.OrderID, Total = x.t })
```
<span data-ttu-id="b0787-2566">où `x` est un identificateur généré par le compilateur qui est autrement invisible et inaccessible.</span><span class="sxs-lookup"><span data-stu-id="b0787-2566">where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="b0787-2567">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2567">The example</span></span>
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
select new { c.Name, o.OrderDate, o.Total }
```
<span data-ttu-id="b0787-2568">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2568">is translated into</span></span>
```csharp
customers.Join(orders, c => c.CustomerID, o => o.CustomerID,
    (c, o) => new { c.Name, o.OrderDate, o.Total })
```

<span data-ttu-id="b0787-2569">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2569">The example</span></span>
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID into co
let n = co.Count()
where n >= 10
select new { c.Name, OrderCount = n }
```
<span data-ttu-id="b0787-2570">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2570">is translated into</span></span>
```csharp
from * in customers.
    GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
        (c, co) => new { c, co })
let n = co.Count()
where n >= 10 
select new { c.Name, OrderCount = n }
```
<span data-ttu-id="b0787-2571">traduction finale de qui est</span><span class="sxs-lookup"><span data-stu-id="b0787-2571">the final translation of which is</span></span>
```csharp
customers.
GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
    (c, co) => new { c, co }).
Select(x => new { x, n = x.co.Count() }).
Where(y => y.n >= 10).
Select(y => new { y.x.c.Name, OrderCount = y.n)
```
<span data-ttu-id="b0787-2572">où `x` et `y` sont des identificateurs générés par le compilateur qui sont autrement inaccessibles et inaccessibles.</span><span class="sxs-lookup"><span data-stu-id="b0787-2572">where `x` and `y` are compiler generated identifiers that are otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="b0787-2573">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2573">The example</span></span>
```csharp
from o in orders
orderby o.Customer.Name, o.Total descending
select o
```
<span data-ttu-id="b0787-2574">a la traduction finale</span><span class="sxs-lookup"><span data-stu-id="b0787-2574">has the final translation</span></span>
```csharp
orders.
OrderBy(o => o.Customer.Name).
ThenByDescending(o => o.Total)
```

#### <a name="select-clauses"></a><span data-ttu-id="b0787-2575">Clauses SELECT</span><span class="sxs-lookup"><span data-stu-id="b0787-2575">Select clauses</span></span>

<span data-ttu-id="b0787-2576">Expression de requête de la forme</span><span class="sxs-lookup"><span data-stu-id="b0787-2576">A query expression of the form</span></span>
```csharp
from x in e select v
```
<span data-ttu-id="b0787-2577">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2577">is translated into</span></span>
```csharp
( e ) . Select ( x => v )
```
<span data-ttu-id="b0787-2578">sauf lorsque v est l’identificateur x, la traduction est simplement</span><span class="sxs-lookup"><span data-stu-id="b0787-2578">except when v is the identifier x, the translation is simply</span></span>
```csharp
( e )
```

<span data-ttu-id="b0787-2579">Exemple :</span><span class="sxs-lookup"><span data-stu-id="b0787-2579">For example</span></span>
```csharp
from c in customers.Where(c => c.City == "London")
select c
```
<span data-ttu-id="b0787-2580">est simplement traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2580">is simply translated into</span></span>
```csharp
customers.Where(c => c.City == "London")
```

#### <a name="groupby-clauses"></a><span data-ttu-id="b0787-2581">Clauses GROUPBY</span><span class="sxs-lookup"><span data-stu-id="b0787-2581">Groupby clauses</span></span>

<span data-ttu-id="b0787-2582">Expression de requête de la forme</span><span class="sxs-lookup"><span data-stu-id="b0787-2582">A query expression of the form</span></span>
```csharp
from x in e group v by k
```
<span data-ttu-id="b0787-2583">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2583">is translated into</span></span>
```csharp
( e ) . GroupBy ( x => k , x => v )
```
<span data-ttu-id="b0787-2584">sauf lorsque v est l’identificateur x, la traduction est</span><span class="sxs-lookup"><span data-stu-id="b0787-2584">except when v is the identifier x, the translation is</span></span>
```csharp
( e ) . GroupBy ( x => k )
```

<span data-ttu-id="b0787-2585">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2585">The example</span></span>
```csharp
from c in customers
group c.Name by c.Country
```
<span data-ttu-id="b0787-2586">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2586">is translated into</span></span>
```csharp
customers.
GroupBy(c => c.Country, c => c.Name)
```

#### <a name="transparent-identifiers"></a><span data-ttu-id="b0787-2587">Identificateurs transparents</span><span class="sxs-lookup"><span data-stu-id="b0787-2587">Transparent identifiers</span></span>

<span data-ttu-id="b0787-2588">Certaines traductions injectent des variables de portée avec des ***identificateurs transparents*** dénotés par `*`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2588">Certain translations inject range variables with ***transparent identifiers*** denoted by `*`.</span></span> <span data-ttu-id="b0787-2589">Les identificateurs transparents ne sont pas une fonctionnalité de langage appropriée. ils existent uniquement comme étape intermédiaire dans le processus de traduction d’expression de requête.</span><span class="sxs-lookup"><span data-stu-id="b0787-2589">Transparent identifiers are not a proper language feature; they exist only as an intermediate step in the query expression translation process.</span></span>

<span data-ttu-id="b0787-2590">Quand une traduction de requête injecte un identificateur transparent, d’autres étapes de traduction propagent l’identificateur transparent dans des fonctions anonymes et des initialiseurs d’objets anonymes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2590">When a query translation injects a transparent identifier, further translation steps propagate the transparent identifier into anonymous functions and anonymous object initializers.</span></span> <span data-ttu-id="b0787-2591">Dans ces contextes, les identificateurs transparents ont le comportement suivant :</span><span class="sxs-lookup"><span data-stu-id="b0787-2591">In those contexts, transparent identifiers have the following behavior:</span></span>

*  <span data-ttu-id="b0787-2592">Lorsqu’un identificateur transparent se produit en tant que paramètre dans une fonction anonyme, les membres du type anonyme associé sont automatiquement dans la portée dans le corps de la fonction anonyme.</span><span class="sxs-lookup"><span data-stu-id="b0787-2592">When a transparent identifier occurs as a parameter in an anonymous function, the members of the associated anonymous type are automatically in scope in the body of the anonymous function.</span></span>
*  <span data-ttu-id="b0787-2593">Lorsqu’un membre avec un identificateur transparent est dans la portée, les membres de ce membre sont également dans la portée.</span><span class="sxs-lookup"><span data-stu-id="b0787-2593">When a member with a transparent identifier is in scope, the members of that member are in scope as well.</span></span>
*  <span data-ttu-id="b0787-2594">Lorsqu’un identificateur transparent se produit comme déclarateur de membre dans un initialiseur d’objet anonyme, il introduit un membre avec un identificateur transparent.</span><span class="sxs-lookup"><span data-stu-id="b0787-2594">When a transparent identifier occurs as a member declarator in an anonymous object initializer, it introduces a member with a transparent identifier.</span></span>
*  <span data-ttu-id="b0787-2595">Dans les étapes de traduction décrites ci-dessus, les identificateurs transparents sont toujours introduits en même temps que les types anonymes, dans le but de capturer plusieurs variables de portée en tant que membres d’un seul objet.</span><span class="sxs-lookup"><span data-stu-id="b0787-2595">In the translation steps described above, transparent identifiers are always introduced together with anonymous types, with the intent of capturing multiple range variables as members of a single object.</span></span> <span data-ttu-id="b0787-2596">Une implémentation de C# est autorisée à utiliser un autre mécanisme que les types anonymes pour regrouper plusieurs variables de portée.</span><span class="sxs-lookup"><span data-stu-id="b0787-2596">An implementation of C# is permitted to use a different mechanism than anonymous types to group together multiple range variables.</span></span> <span data-ttu-id="b0787-2597">Les exemples de traduction suivants supposent que les types anonymes sont utilisés et montrent comment les identificateurs transparents peuvent être traduits.</span><span class="sxs-lookup"><span data-stu-id="b0787-2597">The following translation examples assume that anonymous types are used, and show how transparent identifiers can be translated away.</span></span>

<span data-ttu-id="b0787-2598">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2598">The example</span></span>
```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.Total }
```
<span data-ttu-id="b0787-2599">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2599">is translated into</span></span>
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.Total }
```

<span data-ttu-id="b0787-2600">qui est ensuite traduite en</span><span class="sxs-lookup"><span data-stu-id="b0787-2600">which is further translated into</span></span>
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(* => o.Total).
Select(* => new { c.Name, o.Total })
```
<span data-ttu-id="b0787-2601">qui, lorsque les identificateurs transparents sont effacés, est équivalent à</span><span class="sxs-lookup"><span data-stu-id="b0787-2601">which, when transparent identifiers are erased, is equivalent to</span></span>
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.Total })
```
<span data-ttu-id="b0787-2602">où `x` est un identificateur généré par le compilateur qui est autrement invisible et inaccessible.</span><span class="sxs-lookup"><span data-stu-id="b0787-2602">where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="b0787-2603">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2603">The example</span></span>
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
<span data-ttu-id="b0787-2604">est traduit en</span><span class="sxs-lookup"><span data-stu-id="b0787-2604">is translated into</span></span>
```csharp
from * in customers.
    Join(orders, c => c.CustomerID, o => o.CustomerID, 
        (c, o) => new { c, o })
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
<span data-ttu-id="b0787-2605">ce qui est encore plus réduit à</span><span class="sxs-lookup"><span data-stu-id="b0787-2605">which is further reduced to</span></span>
```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID, (c, o) => new { c, o }).
Join(details, * => o.OrderID, d => d.OrderID, (*, d) => new { *, d }).
Join(products, * => d.ProductID, p => p.ProductID, (*, p) => new { *, p }).
Select(* => new { c.Name, o.OrderDate, p.ProductName })
```
<span data-ttu-id="b0787-2606">traduction finale de qui est</span><span class="sxs-lookup"><span data-stu-id="b0787-2606">the final translation of which is</span></span>
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
<span data-ttu-id="b0787-2607">où `x`, `y`et `z` sont des identificateurs générés par le compilateur qui, sinon, sont inaccessibles et inaccessibles.</span><span class="sxs-lookup"><span data-stu-id="b0787-2607">where `x`, `y`, and `z` are compiler generated identifiers that are otherwise invisible and inaccessible.</span></span>

### <a name="the-query-expression-pattern"></a><span data-ttu-id="b0787-2608">Modèle d’expression de requête</span><span class="sxs-lookup"><span data-stu-id="b0787-2608">The query expression pattern</span></span>

<span data-ttu-id="b0787-2609">Le ***modèle d’expression de requête*** établit un modèle de méthodes que les types peuvent implémenter pour prendre en charge les expressions de requête.</span><span class="sxs-lookup"><span data-stu-id="b0787-2609">The ***Query expression pattern*** establishes a pattern of methods that types can implement to support query expressions.</span></span> <span data-ttu-id="b0787-2610">Étant donné que les expressions de requête sont traduites en appels de méthode au moyen d’un mappage syntaxique, les types ont une flexibilité considérable dans la manière dont ils implémentent le modèle d’expression de requête.</span><span class="sxs-lookup"><span data-stu-id="b0787-2610">Because query expressions are translated to method invocations by means of a syntactic mapping, types have considerable flexibility in how they implement the query expression pattern.</span></span> <span data-ttu-id="b0787-2611">Par exemple, les méthodes du modèle peuvent être implémentées en tant que méthodes d’instance ou en tant que méthodes d’extension, car les deux ont la même syntaxe d’appel, et les méthodes peuvent demander des délégués ou des arborescences d’expressions, car les fonctions anonymes sont convertibles dans les deux.</span><span class="sxs-lookup"><span data-stu-id="b0787-2611">For example, the methods of the pattern can be implemented as instance methods or as extension methods because the two have the same invocation syntax, and the methods can request delegates or expression trees because anonymous functions are convertible to both.</span></span>

<span data-ttu-id="b0787-2612">La forme recommandée d’un type générique `C<T>` qui prend en charge le modèle d’expression de requête est illustrée ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b0787-2612">The recommended shape of a generic type `C<T>` that supports the query expression pattern is shown below.</span></span> <span data-ttu-id="b0787-2613">Un type générique est utilisé pour illustrer les relations appropriées entre les types de paramètres et de résultats, mais il est possible d’implémenter le modèle pour les types non génériques également.</span><span class="sxs-lookup"><span data-stu-id="b0787-2613">A generic type is used in order to illustrate the proper relationships between parameter and result types, but it is possible to implement the pattern for non-generic types as well.</span></span>

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

<span data-ttu-id="b0787-2614">Les méthodes ci-dessus utilisent les types délégués génériques `Func<T1,R>` et `Func<T1,T2,R>`, mais elles pouvaient tout aussi bien utiliser d’autres types de délégué ou d’arborescence d’expression avec les mêmes relations dans les types de paramètres et de résultats.</span><span class="sxs-lookup"><span data-stu-id="b0787-2614">The methods above use the generic delegate types `Func<T1,R>` and `Func<T1,T2,R>`, but they could equally well have used other delegate or expression tree types with the same relationships in parameter and result types.</span></span>

<span data-ttu-id="b0787-2615">Notez la relation recommandée entre `C<T>` et `O<T>` qui garantit que les méthodes `ThenBy` et `ThenByDescending` sont uniquement disponibles sur le résultat d’un `OrderBy` ou d’un `OrderByDescending`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2615">Notice the recommended relationship between `C<T>` and `O<T>` which ensures that the `ThenBy` and `ThenByDescending` methods are available only on the result of an `OrderBy` or `OrderByDescending`.</span></span> <span data-ttu-id="b0787-2616">Notez également la forme recommandée du résultat de `GroupBy`--une séquence de séquences, où chaque séquence interne a une propriété de `Key` supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="b0787-2616">Also notice the recommended shape of the result of `GroupBy` -- a sequence of sequences, where each inner sequence has an additional `Key` property.</span></span>

<span data-ttu-id="b0787-2617">L’espace de noms `System.Linq` fournit une implémentation du modèle d’opérateur de requête pour tout type qui implémente l’interface `System.Collections.Generic.IEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2617">The `System.Linq` namespace provides an implementation of the query operator pattern for any type that implements the `System.Collections.Generic.IEnumerable<T>` interface.</span></span>

## <a name="assignment-operators"></a><span data-ttu-id="b0787-2618">Opérateurs d’affectation</span><span class="sxs-lookup"><span data-stu-id="b0787-2618">Assignment operators</span></span>

<span data-ttu-id="b0787-2619">Les opérateurs d’assignation affectent une nouvelle valeur à une variable, une propriété, un événement ou un élément d’indexeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2619">The assignment operators assign a new value to a variable, a property, an event, or an indexer element.</span></span>

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

<span data-ttu-id="b0787-2620">L’opérande gauche d’une assignation doit être une expression classifiée comme une variable, un accès à une propriété, un accès à un indexeur ou un accès à un événement.</span><span class="sxs-lookup"><span data-stu-id="b0787-2620">The left operand of an assignment must be an expression classified as a variable, a property access, an indexer access, or an event access.</span></span>

<span data-ttu-id="b0787-2621">L’opérateur `=` est appelé ***opérateur d’assignation simple***.</span><span class="sxs-lookup"><span data-stu-id="b0787-2621">The `=` operator is called the ***simple assignment operator***.</span></span> <span data-ttu-id="b0787-2622">Elle assigne la valeur de l’opérande droit à la variable, à la propriété ou à l’élément d’indexeur donné par l’opérande gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-2622">It assigns the value of the right operand to the variable, property, or indexer element given by the left operand.</span></span> <span data-ttu-id="b0787-2623">L’opérande gauche de l’opérateur d’assignation simple ne peut pas être un accès à un événement (sauf s’il est décrit dans [événements de type champ](classes.md#field-like-events)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2623">The left operand of the simple assignment operator may not be an event access (except as described in [Field-like events](classes.md#field-like-events)).</span></span> <span data-ttu-id="b0787-2624">L’opérateur d’assignation simple est décrit dans [assignation simple](expressions.md#simple-assignment).</span><span class="sxs-lookup"><span data-stu-id="b0787-2624">The simple assignment operator is described in [Simple assignment](expressions.md#simple-assignment).</span></span>

<span data-ttu-id="b0787-2625">Les opérateurs d’assignation autres que l’opérateur `=` sont appelés des ***opérateurs d’assignation composée***.</span><span class="sxs-lookup"><span data-stu-id="b0787-2625">The assignment operators other than the `=` operator are called the ***compound assignment operators***.</span></span> <span data-ttu-id="b0787-2626">Ces opérateurs effectuent l’opération indiquée sur les deux opérandes, puis assignent la valeur résultante à la variable, à la propriété ou à l’élément d’indexeur donné par l’opérande gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-2626">These operators perform the indicated operation on the two operands, and then assign the resulting value to the variable, property, or indexer element given by the left operand.</span></span> <span data-ttu-id="b0787-2627">Les opérateurs d’assignation composée sont décrits dans [assignation composée](expressions.md#compound-assignment).</span><span class="sxs-lookup"><span data-stu-id="b0787-2627">The compound assignment operators are described in [Compound assignment](expressions.md#compound-assignment).</span></span>

<span data-ttu-id="b0787-2628">Les opérateurs `+=` et `-=` avec une expression d’accès à l’événement comme opérande gauche sont appelés *opérateurs d’assignation d’événement*.</span><span class="sxs-lookup"><span data-stu-id="b0787-2628">The `+=` and `-=` operators with an event access expression as the left operand are called the *event assignment operators*.</span></span> <span data-ttu-id="b0787-2629">Aucun autre opérateur d’assignation n’est valide avec un accès aux événements comme opérande de gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-2629">No other assignment operator is valid with an event access as the left operand.</span></span> <span data-ttu-id="b0787-2630">Les opérateurs d’assignation d’événement sont décrits dans [assignation d’événement](expressions.md#event-assignment).</span><span class="sxs-lookup"><span data-stu-id="b0787-2630">The event assignment operators are described in [Event assignment](expressions.md#event-assignment).</span></span>

<span data-ttu-id="b0787-2631">Les opérateurs d’assignation sont associatifs à droite, ce qui signifie que les opérations sont regroupées de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-2631">The assignment operators are right-associative, meaning that operations are grouped from right to left.</span></span> <span data-ttu-id="b0787-2632">Par exemple, une expression de la forme `a = b = c` est évaluée comme `a = (b = c)`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2632">For example, an expression of the form `a = b = c` is evaluated as `a = (b = c)`.</span></span>

### <a name="simple-assignment"></a><span data-ttu-id="b0787-2633">Assignation simple</span><span class="sxs-lookup"><span data-stu-id="b0787-2633">Simple assignment</span></span>

<span data-ttu-id="b0787-2634">L’opérateur `=` est appelé opérateur d’assignation simple.</span><span class="sxs-lookup"><span data-stu-id="b0787-2634">The `=` operator is called the simple assignment operator.</span></span>

<span data-ttu-id="b0787-2635">Si l’opérande gauche d’une assignation simple est de la forme `E.P` ou `E[Ei]` où `E` a le type au moment de la compilation `dynamic`, l’assignation est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2635">If the left operand of a simple assignment is of the form `E.P` or `E[Ei]` where `E` has the compile-time type `dynamic`, then the assignment is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="b0787-2636">Dans ce cas, le type au moment de la compilation de l’expression d’assignation est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution en fonction du type d’exécution de `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2636">In this case the compile-time type of the assignment expression is `dynamic`, and the resolution described below will take place at run-time based on the run-time type of `E`.</span></span>

<span data-ttu-id="b0787-2637">Dans une assignation simple, l’opérande de droite doit être une expression implicitement convertible en type de l’opérande de gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-2637">In a simple assignment, the right operand must be an expression that is implicitly convertible to the type of the left operand.</span></span> <span data-ttu-id="b0787-2638">L’opération assigne la valeur de l’opérande droit à la variable, à la propriété ou à l’élément d’indexeur donné par l’opérande gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-2638">The operation assigns the value of the right operand to the variable, property, or indexer element given by the left operand.</span></span>

<span data-ttu-id="b0787-2639">Le résultat d’une expression d’assignation simple est la valeur assignée à l’opérande de gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-2639">The result of a simple assignment expression is the value assigned to the left operand.</span></span> <span data-ttu-id="b0787-2640">Le résultat a le même type que l’opérande de gauche et est toujours classifié comme une valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2640">The result has the same type as the left operand and is always classified as a value.</span></span>

<span data-ttu-id="b0787-2641">Si l’opérande de gauche est un accès à une propriété ou un indexeur, la propriété ou l’indexeur doit avoir un accesseur `set`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2641">If the left operand is a property or indexer access, the property or indexer must have a `set` accessor.</span></span> <span data-ttu-id="b0787-2642">Si ce n’est pas le cas, une erreur de temps de liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-2642">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="b0787-2643">Le traitement au moment de l’exécution d’une assignation simple de la forme `x = y` se compose des étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-2643">The run-time processing of a simple assignment of the form `x = y` consists of the following steps:</span></span>

*  <span data-ttu-id="b0787-2644">Si `x` est classée en tant que variable :</span><span class="sxs-lookup"><span data-stu-id="b0787-2644">If `x` is classified as a variable:</span></span>
   * <span data-ttu-id="b0787-2645">`x` est évaluée pour produire la variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-2645">`x` is evaluated to produce the variable.</span></span>
   * <span data-ttu-id="b0787-2646">`y` est évaluée et, si nécessaire, convertie en type de `x` par le biais d’une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2646">`y` is evaluated and, if required, converted to the type of `x` through an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>
   * <span data-ttu-id="b0787-2647">Si la variable fournie par `x` est un élément de tableau d’un *reference_type*, une vérification au moment de l’exécution est effectuée pour s’assurer que la valeur calculée pour `y` est compatible avec l’instance de tableau de laquelle `x` est un élément.</span><span class="sxs-lookup"><span data-stu-id="b0787-2647">If the variable given by `x` is an array element of a *reference_type*, a run-time check is performed to ensure that the value computed for `y` is compatible with the array instance of which `x` is an element.</span></span> <span data-ttu-id="b0787-2648">La vérification est réussie si `y` est `null`ou si une conversion de référence implicite ([conversions de référence implicites](conversions.md#implicit-reference-conversions)) existe à partir du type réel de l’instance référencée par `y` vers le type d’élément réel de l’instance de tableau contenant `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2648">The check succeeds if `y` is `null`, or if an implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) exists from the actual type of the instance referenced by `y` to the actual element type of the array instance containing `x`.</span></span> <span data-ttu-id="b0787-2649">Sinon, une exception `System.ArrayTypeMismatchException` est levée.</span><span class="sxs-lookup"><span data-stu-id="b0787-2649">Otherwise, a `System.ArrayTypeMismatchException` is thrown.</span></span>
   * <span data-ttu-id="b0787-2650">La valeur résultant de l’évaluation et de la conversion de `y` est stockée dans l’emplacement donné par l’évaluation de `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2650">The value resulting from the evaluation and conversion of `y` is stored into the location given by the evaluation of `x`.</span></span>
*  <span data-ttu-id="b0787-2651">Si `x` est classée comme un accès à une propriété ou un indexeur :</span><span class="sxs-lookup"><span data-stu-id="b0787-2651">If `x` is classified as a property or indexer access:</span></span>
   * <span data-ttu-id="b0787-2652">L’expression d’instance (si `x` n’est pas `static`) et la liste d’arguments (si `x` est un accès à un indexeur) associée à `x` sont évaluées, et les résultats sont utilisés dans l’appel d’accesseur `set` suivant.</span><span class="sxs-lookup"><span data-stu-id="b0787-2652">The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `set` accessor invocation.</span></span>
   * <span data-ttu-id="b0787-2653">`y` est évaluée et, si nécessaire, convertie en type de `x` par le biais d’une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2653">`y` is evaluated and, if required, converted to the type of `x` through an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>
   * <span data-ttu-id="b0787-2654">L’accesseur `set` de `x` est appelé avec la valeur calculée pour `y` comme argument de `value`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2654">The `set` accessor of `x` is invoked with the value computed for `y` as its `value` argument.</span></span>

<span data-ttu-id="b0787-2655">Les règles de covariance de tableau ([covariance de tableau](arrays.md#array-covariance)) autorisent une valeur d’un type de tableau `A[]` être une référence à une instance d’un type de tableau `B[]`, à condition qu’il existe une conversion de référence implicite de `B` à `A`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2655">The array co-variance rules ([Array covariance](arrays.md#array-covariance)) permit a value of an array type `A[]` to be a reference to an instance of an array type `B[]`, provided an implicit reference conversion exists from `B` to `A`.</span></span> <span data-ttu-id="b0787-2656">En raison de ces règles, l’assignation à un élément de tableau d’un *reference_type* nécessite un contrôle à l’exécution pour garantir que la valeur assignée est compatible avec l’instance de tableau.</span><span class="sxs-lookup"><span data-stu-id="b0787-2656">Because of these rules, assignment to an array element of a *reference_type* requires a run-time check to ensure that the value being assigned is compatible with the array instance.</span></span> <span data-ttu-id="b0787-2657">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2657">In the example</span></span>
```csharp
string[] sa = new string[10];
object[] oa = sa;

oa[0] = null;               // Ok
oa[1] = "Hello";            // Ok
oa[2] = new ArrayList();    // ArrayTypeMismatchException
```
<span data-ttu-id="b0787-2658">la dernière assignation entraîne la levée d’une `System.ArrayTypeMismatchException`, car une instance de `ArrayList` ne peut pas être stockée dans un élément d’un `string[]`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2658">the last assignment causes a `System.ArrayTypeMismatchException` to be thrown because an instance of `ArrayList` cannot be stored in an element of a `string[]`.</span></span>

<span data-ttu-id="b0787-2659">Lorsqu’une propriété ou un indexeur déclaré dans un *struct_type* est la cible d’une assignation, l’expression d’instance associée à l’accès à la propriété ou à l’indexeur doit être classée en tant que variable.</span><span class="sxs-lookup"><span data-stu-id="b0787-2659">When a property or indexer declared in a *struct_type* is the target of an assignment, the instance expression associated with the property or indexer access must be classified as a variable.</span></span> <span data-ttu-id="b0787-2660">Si l’expression d’instance est classée en tant que valeur, une erreur de liaison au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-2660">If the instance expression is classified as a value, a binding-time error occurs.</span></span> <span data-ttu-id="b0787-2661">En raison de l' [accès aux membres](expressions.md#member-access), la même règle s’applique également aux champs.</span><span class="sxs-lookup"><span data-stu-id="b0787-2661">Because of [Member access](expressions.md#member-access), the same rule also applies to fields.</span></span>

<span data-ttu-id="b0787-2662">Compte tenu des déclarations :</span><span class="sxs-lookup"><span data-stu-id="b0787-2662">Given the declarations:</span></span>
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
<span data-ttu-id="b0787-2663">dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2663">in the example</span></span>
```csharp
Point p = new Point();
p.X = 100;
p.Y = 100;
Rectangle r = new Rectangle();
r.A = new Point(10, 10);
r.B = p;
```
<span data-ttu-id="b0787-2664">les assignations à `p.X`, `p.Y`, `r.A`et `r.B` sont autorisées, car `p` et `r` sont des variables.</span><span class="sxs-lookup"><span data-stu-id="b0787-2664">the assignments to `p.X`, `p.Y`, `r.A`, and `r.B` are permitted because `p` and `r` are variables.</span></span> <span data-ttu-id="b0787-2665">Toutefois, dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2665">However, in the example</span></span>
```csharp
Rectangle r = new Rectangle();
r.A.X = 10;
r.A.Y = 10;
r.B.X = 100;
r.B.Y = 100;
```
<span data-ttu-id="b0787-2666">les attributions ne sont pas valides, car `r.A` et `r.B` ne sont pas des variables.</span><span class="sxs-lookup"><span data-stu-id="b0787-2666">the assignments are all invalid, since `r.A` and `r.B` are not variables.</span></span>

### <a name="compound-assignment"></a><span data-ttu-id="b0787-2667">Assignation composée</span><span class="sxs-lookup"><span data-stu-id="b0787-2667">Compound assignment</span></span>

<span data-ttu-id="b0787-2668">Si l’opérande gauche d’une assignation composée se présente sous la forme `E.P` ou `E[Ei]` où `E` a le type au moment de la compilation `dynamic`, l’assignation est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2668">If the left operand of a compound assignment is of the form `E.P` or `E[Ei]` where `E` has the compile-time type `dynamic`, then the assignment is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="b0787-2669">Dans ce cas, le type au moment de la compilation de l’expression d’assignation est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution en fonction du type d’exécution de `E`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2669">In this case the compile-time type of the assignment expression is `dynamic`, and the resolution described below will take place at run-time based on the run-time type of `E`.</span></span>

<span data-ttu-id="b0787-2670">Une opération de la forme `x op= y` est traitée en appliquant la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) comme si l’opération avait été écrite `x op y`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2670">An operation of the form `x op= y` is processed by applying binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) as if the operation was written `x op y`.</span></span> <span data-ttu-id="b0787-2671">Ainsi,</span><span class="sxs-lookup"><span data-stu-id="b0787-2671">Then,</span></span>

*  <span data-ttu-id="b0787-2672">Si le type de retour de l’opérateur sélectionné est implicitement convertible en type de `x`, l’opération est évaluée comme `x = x op y`, sauf que `x` n’est évaluée qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-2672">If the return type of the selected operator is implicitly convertible to the type of `x`, the operation is evaluated as `x = x op y`, except that `x` is evaluated only once.</span></span>
*  <span data-ttu-id="b0787-2673">Sinon, si l’opérateur sélectionné est un opérateur prédéfini, si le type de retour de l’opérateur sélectionné est explicitement convertible en type de `x`, et si `y` est implicitement convertible en type de `x` ou si l’opérateur est un opérateur de décalage, l’opération est évaluée comme `x = (T)(x op y)`, où `T` est le type de `x`, sauf que `x` est évaluée une seule fois.</span><span class="sxs-lookup"><span data-stu-id="b0787-2673">Otherwise, if the selected operator is a predefined operator, if the return type of the selected operator is explicitly convertible to the type of `x`, and if `y` is implicitly convertible to the type of `x` or the operator is a shift operator, then the operation is evaluated as `x = (T)(x op y)`, where `T` is the type of `x`, except that `x` is evaluated only once.</span></span>
*  <span data-ttu-id="b0787-2674">Dans le cas contraire, l’assignation composée n’est pas valide et une erreur de liaison s’est produite.</span><span class="sxs-lookup"><span data-stu-id="b0787-2674">Otherwise, the compound assignment is invalid, and a binding-time error occurs.</span></span>

<span data-ttu-id="b0787-2675">Le terme « évalué une seule fois » signifie que, lors de l’évaluation de `x op y`, les résultats de toutes les expressions constitutives de `x` sont temporairement enregistrés et réutilisés lors de l’assignation à `x`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2675">The term "evaluated only once" means that in the evaluation of `x op y`, the results of any constituent expressions of `x` are temporarily saved and then reused when performing the assignment to `x`.</span></span> <span data-ttu-id="b0787-2676">Par exemple, dans l’assignation `A()[B()] += C()`, où `A` est une méthode qui retourne `int[]`, et `B` et `C` sont des méthodes qui retournent `int`, les méthodes sont appelées une seule fois, dans l’ordre `A`, `B``C`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2676">For example, in the assignment `A()[B()] += C()`, where `A` is a method returning `int[]`, and `B` and `C` are methods returning `int`, the methods are invoked only once, in the order `A`, `B`, `C`.</span></span>

<span data-ttu-id="b0787-2677">Lorsque l’opérande gauche d’une assignation composée est un accès à une propriété ou un indexeur, la propriété ou l’indexeur doit avoir un accesseur `get` et un accesseur `set`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2677">When the left operand of a compound assignment is a property access or indexer access, the property or indexer must have both a `get` accessor and a `set` accessor.</span></span> <span data-ttu-id="b0787-2678">Si ce n’est pas le cas, une erreur de temps de liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-2678">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="b0787-2679">La deuxième règle ci-dessus permet d’évaluer `x op= y` comme `x = (T)(x op y)` dans certains contextes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2679">The second rule above permits `x op= y` to be evaluated as `x = (T)(x op y)` in certain contexts.</span></span> <span data-ttu-id="b0787-2680">La règle existe de sorte que les opérateurs prédéfinis peuvent être utilisés comme opérateurs composés lorsque l’opérande de gauche est de type `sbyte`, `byte`, `short`, `ushort`ou `char`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2680">The rule exists such that the predefined operators can be used as compound operators when the left operand is of type `sbyte`, `byte`, `short`, `ushort`, or `char`.</span></span> <span data-ttu-id="b0787-2681">Même si les deux arguments sont de l’un de ces types, les opérateurs prédéfinis produisent un résultat de type `int`, comme décrit dans [promotions numériques binaires](expressions.md#binary-numeric-promotions).</span><span class="sxs-lookup"><span data-stu-id="b0787-2681">Even when both arguments are of one of those types, the predefined operators produce a result of type `int`, as described in [Binary numeric promotions](expressions.md#binary-numeric-promotions).</span></span> <span data-ttu-id="b0787-2682">Ainsi, sans cast, il n’est pas possible d’assigner le résultat à l’opérande gauche.</span><span class="sxs-lookup"><span data-stu-id="b0787-2682">Thus, without a cast it would not be possible to assign the result to the left operand.</span></span>

<span data-ttu-id="b0787-2683">L’effet intuitif de la règle pour les opérateurs prédéfinis est simplement que `x op= y` est autorisé si les `x op y` et `x = y` sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="b0787-2683">The intuitive effect of the rule for predefined operators is simply that `x op= y` is permitted if both of `x op y` and `x = y` are permitted.</span></span> <span data-ttu-id="b0787-2684">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2684">In the example</span></span>
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
<span data-ttu-id="b0787-2685">la raison la plus intuitive pour chaque erreur est qu’une assignation simple correspondante aurait également été une erreur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2685">the intuitive reason for each error is that a corresponding simple assignment would also have been an error.</span></span>

<span data-ttu-id="b0787-2686">Cela signifie également que les opérations d’assignation composée prennent en charge les opérations levées.</span><span class="sxs-lookup"><span data-stu-id="b0787-2686">This also means that compound assignment operations support lifted operations.</span></span> <span data-ttu-id="b0787-2687">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="b0787-2687">In the example</span></span>
```csharp
int? i = 0;
i += 1;             // Ok
```
<span data-ttu-id="b0787-2688">l’opérateur levé `+(int?,int?)` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="b0787-2688">the lifted operator `+(int?,int?)` is used.</span></span>

### <a name="event-assignment"></a><span data-ttu-id="b0787-2689">Assignation d’événement</span><span class="sxs-lookup"><span data-stu-id="b0787-2689">Event assignment</span></span>

<span data-ttu-id="b0787-2690">Si l’opérande gauche d’un opérateur `+=` ou `-=` est classé comme un accès aux événements, l’expression est évaluée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-2690">If the left operand of a `+=` or `-=` operator is classified as an event access, then the expression is evaluated as follows:</span></span>

*  <span data-ttu-id="b0787-2691">L’expression d’instance, le cas échéant, de l’accès à l’événement est évaluée.</span><span class="sxs-lookup"><span data-stu-id="b0787-2691">The instance expression, if any, of the event access is evaluated.</span></span>
*  <span data-ttu-id="b0787-2692">L’opérande droit de l’opérateur `+=` ou `-=` est évalué et, si nécessaire, est converti en type de l’opérande gauche via une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2692">The right operand of the `+=` or `-=` operator is evaluated, and, if required, converted to the type of the left operand through an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>
*  <span data-ttu-id="b0787-2693">Un accesseur d’événement de l’événement est appelé, avec la liste d’arguments composée de l’opérande de droite, après l’évaluation et, si nécessaire, la conversion.</span><span class="sxs-lookup"><span data-stu-id="b0787-2693">An event accessor of the event is invoked, with argument list consisting of the right operand, after evaluation and, if necessary, conversion.</span></span> <span data-ttu-id="b0787-2694">Si l’opérateur a été `+=`, l’accesseur `add` est appelé ; Si l’opérateur a été `-=`, l’accesseur `remove` est appelé.</span><span class="sxs-lookup"><span data-stu-id="b0787-2694">If the operator was `+=`, the `add` accessor is invoked; if the operator was `-=`, the `remove` accessor is invoked.</span></span>

<span data-ttu-id="b0787-2695">Une expression d’assignation d’événement ne produit pas de valeur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2695">An event assignment expression does not yield a value.</span></span> <span data-ttu-id="b0787-2696">Ainsi, une expression d’assignation d’événement est valide uniquement dans le contexte d’une *statement_expression* ([instructions d’expression](statements.md#expression-statements)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2696">Thus, an event assignment expression is valid only in the context of a *statement_expression* ([Expression statements](statements.md#expression-statements)).</span></span>

## <a name="expression"></a><span data-ttu-id="b0787-2697">Expression</span><span class="sxs-lookup"><span data-stu-id="b0787-2697">Expression</span></span>

<span data-ttu-id="b0787-2698">Une *expression* est soit une *non_assignment_expression* , soit une *assignation*.</span><span class="sxs-lookup"><span data-stu-id="b0787-2698">An *expression* is either a *non_assignment_expression* or an *assignment*.</span></span>

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

## <a name="constant-expressions"></a><span data-ttu-id="b0787-2699">Expressions de constantes</span><span class="sxs-lookup"><span data-stu-id="b0787-2699">Constant expressions</span></span>

<span data-ttu-id="b0787-2700">Une *constant_expression* est une expression qui peut être complètement évaluée au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-2700">A *constant_expression* is an expression that can be fully evaluated at compile-time.</span></span>

```antlr
constant_expression
    : expression
    ;
```

<span data-ttu-id="b0787-2701">Une expression constante doit être le `null` littéral ou une valeur avec l’un des types suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `object`, `string`ou n’importe quel type énumération.</span><span class="sxs-lookup"><span data-stu-id="b0787-2701">A constant expression must be the `null` literal or a value with one of  the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `object`, `string`, or any enumeration type.</span></span> <span data-ttu-id="b0787-2702">Seules les constructions suivantes sont autorisées dans les expressions constantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-2702">Only the following constructs are permitted in constant expressions:</span></span>

*  <span data-ttu-id="b0787-2703">Littéraux (y compris le littéral `null`).</span><span class="sxs-lookup"><span data-stu-id="b0787-2703">Literals (including the `null` literal).</span></span>
*  <span data-ttu-id="b0787-2704">Références à `const` membres de types classe et struct.</span><span class="sxs-lookup"><span data-stu-id="b0787-2704">References to `const` members of class and struct types.</span></span>
*  <span data-ttu-id="b0787-2705">Références aux membres de types énumération.</span><span class="sxs-lookup"><span data-stu-id="b0787-2705">References to members of enumeration types.</span></span>
*  <span data-ttu-id="b0787-2706">Références aux paramètres de `const` ou aux variables locales</span><span class="sxs-lookup"><span data-stu-id="b0787-2706">References to `const` parameters or local variables</span></span>
*  <span data-ttu-id="b0787-2707">Sous-expressions entre parenthèses, qui sont elles-mêmes des expressions constantes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2707">Parenthesized sub-expressions, which are themselves constant expressions.</span></span>
*  <span data-ttu-id="b0787-2708">Expressions de cast, à condition que le type cible soit l’un des types indiqués ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="b0787-2708">Cast expressions, provided the target type is one of the types listed above.</span></span>
*  <span data-ttu-id="b0787-2709">expressions `checked` et `unchecked`</span><span class="sxs-lookup"><span data-stu-id="b0787-2709">`checked` and `unchecked` expressions</span></span>
*  <span data-ttu-id="b0787-2710">Expressions de valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="b0787-2710">Default value expressions</span></span>
*  <span data-ttu-id="b0787-2711">Expressions Nameof</span><span class="sxs-lookup"><span data-stu-id="b0787-2711">Nameof expressions</span></span>
*  <span data-ttu-id="b0787-2712">Les opérateurs unaires `+`, `-`, `!`et `~` prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="b0787-2712">The predefined `+`, `-`, `!`, and `~` unary operators.</span></span>
*  <span data-ttu-id="b0787-2713">Les opérateurs binaires prédéfinis `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, `&&`, `||`, `==`, `!=`, `<`, `>`, `<=`et `>=`, à condition que chaque opérande soit d’un type indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="b0787-2713">The predefined `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, `&&`, `||`, `==`, `!=`, `<`, `>`, `<=`, and `>=` binary operators, provided each operand is of a type listed above.</span></span>
*  <span data-ttu-id="b0787-2714">Opérateur conditionnel `?:`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2714">The `?:` conditional operator.</span></span>

<span data-ttu-id="b0787-2715">Les conversions suivantes sont autorisées dans les expressions constantes :</span><span class="sxs-lookup"><span data-stu-id="b0787-2715">The following conversions are permitted in constant expressions:</span></span>

*  <span data-ttu-id="b0787-2716">Conversions d’identité</span><span class="sxs-lookup"><span data-stu-id="b0787-2716">Identity conversions</span></span>
*  <span data-ttu-id="b0787-2717">Conversions numériques</span><span class="sxs-lookup"><span data-stu-id="b0787-2717">Numeric conversions</span></span>
*  <span data-ttu-id="b0787-2718">Conversions d’énumération</span><span class="sxs-lookup"><span data-stu-id="b0787-2718">Enumeration conversions</span></span>
*  <span data-ttu-id="b0787-2719">Conversions d’expressions constantes</span><span class="sxs-lookup"><span data-stu-id="b0787-2719">Constant expression conversions</span></span>
*  <span data-ttu-id="b0787-2720">Conversions de référence implicites et explicites, à condition que la source des conversions soit une expression constante qui prend la valeur null.</span><span class="sxs-lookup"><span data-stu-id="b0787-2720">Implicit and explicit reference conversions, provided that the source of the conversions is a constant expression that evaluates to the null value.</span></span>

<span data-ttu-id="b0787-2721">Les autres conversions, y compris les conversions boxing, unboxing et implicites des valeurs non null, ne sont pas autorisées dans les expressions constantes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2721">Other conversions including boxing, unboxing and implicit reference conversions of non-null values are not permitted in constant expressions.</span></span> <span data-ttu-id="b0787-2722">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b0787-2722">For example:</span></span>
```csharp
class C {
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
<span data-ttu-id="b0787-2723">l’initialisation de i est une erreur, car une conversion boxing est requise.</span><span class="sxs-lookup"><span data-stu-id="b0787-2723">the initialization of i is an error because a boxing conversion is required.</span></span> <span data-ttu-id="b0787-2724">L’initialisation de str est une erreur, car une conversion de référence implicite à partir d’une valeur non null est requise.</span><span class="sxs-lookup"><span data-stu-id="b0787-2724">The initialization of str is an error because an implicit reference conversion from a non-null value is required.</span></span>

<span data-ttu-id="b0787-2725">Chaque fois qu’une expression répond aux spécifications mentionnées ci-dessus, l’expression est évaluée au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-2725">Whenever an expression fulfills the requirements listed above, the expression is evaluated at compile-time.</span></span> <span data-ttu-id="b0787-2726">Cela est vrai même si l’expression est une sous-expression d’une expression plus grande qui contient des constructions non constantes.</span><span class="sxs-lookup"><span data-stu-id="b0787-2726">This is true even if the expression is a sub-expression of a larger expression that contains non-constant constructs.</span></span>

<span data-ttu-id="b0787-2727">L’évaluation au moment de la compilation des expressions constantes utilise les mêmes règles que l’évaluation au moment de l’exécution des expressions non constantes, à la différence près que lorsque l’évaluation au moment de l’exécution a levé une exception, l’évaluation au moment de la compilation provoque une erreur au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-2727">The compile-time evaluation of constant expressions uses the same rules as run-time evaluation of non-constant expressions, except that where run-time evaluation would have thrown an exception, compile-time evaluation causes a compile-time error to occur.</span></span>

<span data-ttu-id="b0787-2728">À moins qu’une expression constante soit explicitement placée dans un contexte de `unchecked`, les dépassements qui se produisent dans les opérations arithmétiques de type intégral et les conversions pendant l’évaluation de l’expression au moment de la compilation provoquent toujours des erreurs au moment de la compilation ([expressions constantes](expressions.md#constant-expressions)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2728">Unless a constant expression is explicitly placed in an `unchecked` context, overflows that occur in integral-type arithmetic operations and conversions during the compile-time evaluation of the expression always cause compile-time errors ([Constant expressions](expressions.md#constant-expressions)).</span></span>

<span data-ttu-id="b0787-2729">Les expressions constantes se trouvent dans les contextes listés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b0787-2729">Constant expressions occur in the contexts listed below.</span></span> <span data-ttu-id="b0787-2730">Dans ces contextes, une erreur de compilation se produit si une expression ne peut pas être complètement évaluée au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0787-2730">In these contexts, a compile-time error occurs if an expression cannot be fully evaluated at compile-time.</span></span>

*  <span data-ttu-id="b0787-2731">Déclarations de constantes ([constantes](classes.md#constants)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2731">Constant declarations ([Constants](classes.md#constants)).</span></span>
*  <span data-ttu-id="b0787-2732">Déclarations de membre d’énumération ([membres enum](enums.md#enum-members)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2732">Enumeration member declarations ([Enum members](enums.md#enum-members)).</span></span>
*  <span data-ttu-id="b0787-2733">Arguments par défaut des listes de paramètres formels ([paramètres de méthode](classes.md#method-parameters))</span><span class="sxs-lookup"><span data-stu-id="b0787-2733">Default arguments of formal parameter lists ([Method parameters](classes.md#method-parameters))</span></span>
*  <span data-ttu-id="b0787-2734">`case` étiquettes d’une instruction `switch` ([instruction switch](statements.md#the-switch-statement)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2734">`case` labels of a `switch` statement ([The switch statement](statements.md#the-switch-statement)).</span></span>
*  <span data-ttu-id="b0787-2735">instructions `goto case` ([instruction goto](statements.md#the-goto-statement)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2735">`goto case` statements ([The goto statement](statements.md#the-goto-statement)).</span></span>
*  <span data-ttu-id="b0787-2736">Longueurs de dimension dans une expression de création de tableau ([expressions de création de tableau](expressions.md#array-creation-expressions)) qui comprend un initialiseur.</span><span class="sxs-lookup"><span data-stu-id="b0787-2736">Dimension lengths in an array creation expression ([Array creation expressions](expressions.md#array-creation-expressions)) that includes an initializer.</span></span>
*  <span data-ttu-id="b0787-2737">Attributs ([attributs](attributes.md)).</span><span class="sxs-lookup"><span data-stu-id="b0787-2737">Attributes ([Attributes](attributes.md)).</span></span>

<span data-ttu-id="b0787-2738">Une conversion d’expression constante implicite ([conversions d’expressions constantes implicites](conversions.md#implicit-constant-expression-conversions)) permet à une expression constante de type `int` d’être convertie en `sbyte`, `byte`, `short`, `ushort`, `uint`ou `ulong`, à condition que la valeur de l’expression constante soit comprise dans la plage du type de destination.</span><span class="sxs-lookup"><span data-stu-id="b0787-2738">An implicit constant expression conversion ([Implicit constant expression conversions](conversions.md#implicit-constant-expression-conversions)) permits a constant expression of type `int` to be converted to `sbyte`, `byte`, `short`, `ushort`, `uint`, or `ulong`, provided the value of the constant expression is within the range of the destination type.</span></span>

## <a name="boolean-expressions"></a><span data-ttu-id="b0787-2739">expressions booléennes</span><span class="sxs-lookup"><span data-stu-id="b0787-2739">Boolean expressions</span></span>

<span data-ttu-id="b0787-2740">Une *Boolean_expression* est une expression qui produit un résultat de type `bool`; soit directement, soit par le biais de l’application de `operator true` dans certains contextes, comme indiqué dans le code suivant.</span><span class="sxs-lookup"><span data-stu-id="b0787-2740">A *boolean_expression* is an expression that yields a result of type `bool`; either directly or through application of `operator true` in certain contexts as specified in the following.</span></span>

```antlr
boolean_expression
    : expression
    ;
```

<span data-ttu-id="b0787-2741">L’expression conditionnelle de contrôle d' *un if_statement* ([instruction if](statements.md#the-if-statement)), *while_statement* ([l’instruction while](statements.md#the-while-statement)) *, do_statement* ([instruction do](statements.md#the-do-statement)) ou *for_Statement* ([l’instruction for](statements.md#the-for-statement)) est un *Boolean_expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-2741">The controlling conditional expression of an *if_statement* ([The if statement](statements.md#the-if-statement)), *while_statement* ([The while statement](statements.md#the-while-statement)), *do_statement* ([The do statement](statements.md#the-do-statement)), or *for_statement* ([The for statement](statements.md#the-for-statement)) is a *boolean_expression*.</span></span> <span data-ttu-id="b0787-2742">L’expression conditionnelle de contrôle de l’opérateur `?:` ([opérateur conditionnel](expressions.md#conditional-operator)) suit les mêmes règles qu’un *Boolean_expression*, mais pour des raisons de priorité d’opérateur est classée en tant que *conditional_or_expression*.</span><span class="sxs-lookup"><span data-stu-id="b0787-2742">The controlling conditional expression of the `?:` operator ([Conditional operator](expressions.md#conditional-operator)) follows the same rules as a *boolean_expression*, but for reasons of operator precedence is classified as a *conditional_or_expression*.</span></span>

<span data-ttu-id="b0787-2743">Un *boolean_expression* `E` est requis pour pouvoir produire une valeur de type `bool`, comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0787-2743">A *boolean_expression* `E` is required to be able to produce a value of type `bool`, as follows:</span></span>

*  <span data-ttu-id="b0787-2744">Si `E` est implicitement convertible en `bool` alors, au moment de l’exécution, que la conversion implicite est appliquée.</span><span class="sxs-lookup"><span data-stu-id="b0787-2744">If `E` is implicitly convertible to `bool` then at runtime that implicit conversion is applied.</span></span>
*  <span data-ttu-id="b0787-2745">Sinon, la résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est utilisée pour rechercher une meilleure implémentation unique de l’opérateur `true` sur `E`, et cette implémentation est appliquée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b0787-2745">Otherwise, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is used to find a unique best implementation of operator `true` on `E`, and that implementation is applied at runtime.</span></span>
*  <span data-ttu-id="b0787-2746">Si aucun opérateur de ce type n’est trouvé, une erreur au moment de la liaison se produit.</span><span class="sxs-lookup"><span data-stu-id="b0787-2746">If no such operator is found, a binding-time error occurs.</span></span>

<span data-ttu-id="b0787-2747">Le type struct `DBBool` dans la [base de données de type booléen](structs.md#database-boolean-type) fournit un exemple de type qui implémente `operator true` et `operator false`.</span><span class="sxs-lookup"><span data-stu-id="b0787-2747">The `DBBool` struct type in [Database boolean type](structs.md#database-boolean-type) provides an example of a type that implements `operator true` and `operator false`.</span></span>
