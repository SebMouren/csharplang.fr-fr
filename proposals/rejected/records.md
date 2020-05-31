---
ms.openlocfilehash: 49720d62c496ff904eacacb31ae29ef97a5aefaa
ms.sourcegitcommit: ae114131069ca76e4a1ec8149b7bab81fce8965c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2020
ms.locfileid: "84228217"
---
# <a name="records"></a><span data-ttu-id="f3a9c-101">enregistrements</span><span class="sxs-lookup"><span data-stu-id="f3a9c-101">records</span></span>

* <span data-ttu-id="f3a9c-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="f3a9c-102">[x] Proposed</span></span>
* <span data-ttu-id="f3a9c-103">[] Prototype : [terminé](https://github.com/PROTOTYPE_OWNER/roslyn/BRANCH_NAME)</span><span class="sxs-lookup"><span data-stu-id="f3a9c-103">[ ] Prototype: [Complete](https://github.com/PROTOTYPE_OWNER/roslyn/BRANCH_NAME)</span></span>
* <span data-ttu-id="f3a9c-104">[] Implémentation : [en cours](https://github.com/dotnet/roslyn/BRANCH_NAME)</span><span class="sxs-lookup"><span data-stu-id="f3a9c-104">[ ] Implementation: [In Progress](https://github.com/dotnet/roslyn/BRANCH_NAME)</span></span>
* <span data-ttu-id="f3a9c-105">[] Spécification : spécification de brouillons encadrée</span><span class="sxs-lookup"><span data-stu-id="f3a9c-105">[ ] Specification: Draft specification enclosed</span></span>

## <a name="summary"></a><span data-ttu-id="f3a9c-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="f3a9c-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="f3a9c-107">Les enregistrements sont une nouvelle forme de déclaration simplifiée pour les types de classe et de struct C# qui combinent les avantages d’un certain nombre de fonctionnalités plus simples.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-107">Records are a new, simplified declaration form for C# class and struct types that combine the benefits of a number of simpler features.</span></span> <span data-ttu-id="f3a9c-108">Nous décrivons les nouvelles fonctionnalités (paramètres du récepteur de l’appelant et *with-expression*s), indiquons la syntaxe et la sémantique des déclarations d’enregistrement, puis fournissons des exemples.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-108">We describe the new features (caller-receiver parameters and *with-expression*s), give the syntax and semantics for record declarations, and then provide some examples.</span></span>


## <a name="motivation"></a><span data-ttu-id="f3a9c-109">Motivation</span><span class="sxs-lookup"><span data-stu-id="f3a9c-109">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="f3a9c-110">Un nombre significatif de déclarations de type en C# n’est guère plus que les collections d’agrégats de données typées.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-110">A significant number of type declarations in C# are little more than aggregate collections of typed data.</span></span> <span data-ttu-id="f3a9c-111">Malheureusement, la déclaration de tels types nécessite une grande quantité de code réutilisable.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-111">Unfortunately, declaring such types requires a great deal of boilerplate code.</span></span> <span data-ttu-id="f3a9c-112">Les *enregistrements* fournissent un mécanisme permettant de déclarer un type de données en décrivant les membres de l’agrégat ainsi que le code supplémentaire ou les écarts par rapport à l’usage habituel, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-112">*Records* provide a mechanism for declaring a datatype by describing the members of the aggregate along with additional code or deviations from the usual boilerplate, if any.</span></span>

<span data-ttu-id="f3a9c-113">Consultez les [exemples](#examples)ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-113">See [Examples](#examples), below.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="f3a9c-114">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="f3a9c-114">Detailed design</span></span>
[design]: #detailed-design

### <a name="caller-receiver-parameters"></a><span data-ttu-id="f3a9c-115">paramètres Caller-Receiver</span><span class="sxs-lookup"><span data-stu-id="f3a9c-115">caller-receiver parameters</span></span>

<span data-ttu-id="f3a9c-116">Actuellement *, l’argument par défaut* d’un paramètre de méthode doit être</span><span class="sxs-lookup"><span data-stu-id="f3a9c-116">Currently a method parameter's *default-argument* must be</span></span> 
- <span data-ttu-id="f3a9c-117">*expression constante*; ni</span><span class="sxs-lookup"><span data-stu-id="f3a9c-117">a *constant-expression*; or</span></span>
- <span data-ttu-id="f3a9c-118">expression de la forme `new S()` où `S` est un type valeur ; ou</span><span class="sxs-lookup"><span data-stu-id="f3a9c-118">an expression of the form `new S()` where `S` is a value type; or</span></span>
- <span data-ttu-id="f3a9c-119">expression de la forme `default(S)` où `S` est un type valeur</span><span class="sxs-lookup"><span data-stu-id="f3a9c-119">an expression of the form `default(S)` where `S` is a value type</span></span>

<span data-ttu-id="f3a9c-120">Nous étendons ceci pour ajouter les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="f3a9c-120">We extend this to add the following</span></span>
- <span data-ttu-id="f3a9c-121">une expression de la forme`this.Identifier`</span><span class="sxs-lookup"><span data-stu-id="f3a9c-121">an expression of the form `this.Identifier`</span></span>

<span data-ttu-id="f3a9c-122">Ce nouveau formulaire est appelé *argument par défaut du récepteur*de l’appelant et n’est autorisé que si toutes les conditions suivantes sont satisfaites</span><span class="sxs-lookup"><span data-stu-id="f3a9c-122">This new form is called a *caller-receiver default-argument*, and is allowed only if all of the following are satisfied</span></span>
- <span data-ttu-id="f3a9c-123">La méthode dans laquelle il apparaît est une méthode d’instance ; les</span><span class="sxs-lookup"><span data-stu-id="f3a9c-123">The method in which it appears is an instance method; and</span></span>
- <span data-ttu-id="f3a9c-124">L’expression `this.Identifier` est liée à un membre d’instance du type englobant, qui doit être un champ ou une propriété ; et</span><span class="sxs-lookup"><span data-stu-id="f3a9c-124">The expression `this.Identifier` binds to an instance member of the enclosing type, which must be either a field or a property; and</span></span>
- <span data-ttu-id="f3a9c-125">Le membre auquel il est lié (et l' `get` accesseur, s’il s’agit d’une propriété) est au moins aussi accessible que la méthode ; et</span><span class="sxs-lookup"><span data-stu-id="f3a9c-125">The member to which it binds (and the `get` accessor, if it is a property) is at least as accessible as the method; and</span></span>
- <span data-ttu-id="f3a9c-126">Le type de `this.Identifier` est implicitement convertible à l’aide d’une conversion d’identité ou de valeurs NULL dans le type du paramètre (il s’agit d’une contrainte existante sur un *argument par défaut*).</span><span class="sxs-lookup"><span data-stu-id="f3a9c-126">The type of `this.Identifier` is implicitly convertible by an identity or nullable conversion to the type of the parameter (this is an existing constraint on *default-argument*).</span></span>

<span data-ttu-id="f3a9c-127">Quand un argument est omis d’un appel d’un membre de fonction pour un paramètre facultatif correspondant avec un *argument par défaut de récepteur appelant*, la valeur du membre du destinataire est transmise de manière implicite.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-127">When an argument is omitted from an invocation of a function member for a corresponding optional parameter with a *caller-receiver default-argument*, the value of the receiver's member is implicitly passed.</span></span> 

> <span data-ttu-id="f3a9c-128">**Design Notes**: la raison principale du paramètre Caller-Receiver est de prendre en charge l' *expression with*.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-128">**Design Notes**: the main reason for the caller-receiver parameter is to support the *with-expression*.</span></span> <span data-ttu-id="f3a9c-129">L’idée est que vous pouvez déclarer une méthode comme celle-ci</span><span class="sxs-lookup"><span data-stu-id="f3a9c-129">The idea is that you can declare a method like this</span></span>
> ```cs
> class Point
> {
>     public readonly int X;
>     public readonly int Y;
>     public Point With(int x = this.X, int y = this.Y) => new Point(x, y);
>     // etc
> }
> ```
> <span data-ttu-id="f3a9c-130">puis l’utiliser comme suit</span><span class="sxs-lookup"><span data-stu-id="f3a9c-130">and then use it like this</span></span>
> ```cs
>     Point p = new Point(3, 4);
>     p = p.With(x: 1);
> ```
> <span data-ttu-id="f3a9c-131">Pour créer un nouveau `Point` comme un existant `Point` , mais avec la valeur `X` modifiée.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-131">To create a new `Point` just like an existing `Point` but with the value of `X` changed.</span></span>
> 
> <span data-ttu-id="f3a9c-132">Il s’agit d’une question ouverte que la forme syntaxique de l' *expression with-expression* mérite d’être ajoutée une fois que nous aurons pris en charge les paramètres du récepteur de l’appelant. il est donc possible de le faire *au lieu de* plutôt qu' *en plus* de l' *expression with*.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-132">It is an open question whether or not the syntactic form of the *with-expression* is worth adding once we have support for caller-receiver parameters, so it is possible we would do this *instead of* rather than *in addition to* the *with-expression*.</span></span>

- <span data-ttu-id="f3a9c-133">[] **Problème d’ouverture**: quel est l’ordre dans lequel un *argument par défaut du récepteur appelant* est évalué par rapport à d’autres arguments ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-133">[ ] **Open issue**: What is the order in which a *caller-receiver default-argument* is evaluated with respect to other arguments?</span></span> <span data-ttu-id="f3a9c-134">Dois-je indiquer qu’il n’est pas spécifié ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-134">Should we say that it is unspecified?</span></span>

### <a name="with-expressions"></a><span data-ttu-id="f3a9c-135">expressions with</span><span class="sxs-lookup"><span data-stu-id="f3a9c-135">with-expressions</span></span>

<span data-ttu-id="f3a9c-136">Un nouveau formulaire d’expression est proposé :</span><span class="sxs-lookup"><span data-stu-id="f3a9c-136">A new expression form is proposed:</span></span>

```antlr
primary_expression
    : with_expression
    ;

with_expression
    : primary_expression 'with' '{' with_initializer_list '}'
    ;

with_initializer_list
    : with_initializer
    | with_initializer ',' with_initializer_list
    ;

with_initializer
    : identifier '=' expression
    ;
```

<span data-ttu-id="f3a9c-137">Le jeton `with` est un nouveau mot clé contextuel.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-137">The token `with` is a new context-sensitive keyword.</span></span>

<span data-ttu-id="f3a9c-138">Chaque *identificateur* à gauche d’un *with_initializer* doit être lié à un champ ou une propriété d’instance accessible du type de la *primary_expression* du *with_expression*.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-138">Each *identifier* on the left of a *with_initializer* must bind to an accessible instance field or property of the type of the *primary_expression* of the *with_expression*.</span></span> <span data-ttu-id="f3a9c-139">Il se peut qu’il n’y ait pas de nom dupliqué parmi ces identificateurs d’un *with_expression*donné.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-139">There may be no duplicated name among these identifiers of a given *with_expression*.</span></span>

<span data-ttu-id="f3a9c-140">*With_expression* de la forme</span><span class="sxs-lookup"><span data-stu-id="f3a9c-140">A *with_expression* of the form</span></span>

> <span data-ttu-id="f3a9c-141">*E1* `with` `{` *identificateur*  =  *E2*,...`}`</span><span class="sxs-lookup"><span data-stu-id="f3a9c-141">*e1* `with` `{` *identifier* = *e2*, ... `}`</span></span>

<span data-ttu-id="f3a9c-142">est traité comme un appel de la forme</span><span class="sxs-lookup"><span data-stu-id="f3a9c-142">is treated as an invocation of the form</span></span>

> <span data-ttu-id="f3a9c-143">*E1* `.With(` *identificateur2* `:` *E2*,...`)`</span><span class="sxs-lookup"><span data-stu-id="f3a9c-143">*e1*`.With(`*identifier2*`:` *e2*, ...`)`</span></span>

<span data-ttu-id="f3a9c-144">Où, pour chaque méthode nommée `With` qui est un membre d’instance accessible de *E1*, nous sélectionnons *identificateur2* comme nom du premier paramètre de cette méthode qui a un paramètre de récepteur appelant qui est le même membre que le champ d’instance ou la propriété liée à l' *identificateur*.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-144">Where, for each method named `With` that is an accessible instance member of *e1*, we select *identifier2* as the name of the first parameter in that method that has a caller-receiver parameter that is the same member as the instance field or property bound to *identifier*.</span></span> <span data-ttu-id="f3a9c-145">Si aucun paramètre de ce type ne peut être identifié, la méthode n’est pas prise en compte.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-145">If no such parameter can be identified that method is eliminated from consideration.</span></span> <span data-ttu-id="f3a9c-146">La méthode à appeler est sélectionnée parmi les candidats restants par la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-146">The method to be invoked is selected from among the remaining candidates by overload resolution.</span></span>

> <span data-ttu-id="f3a9c-147">**Remarques**sur la conception : les paramètres de récepteur de l’appelant sont fournis, un grand nombre des avantages de l' *expression with-expression* sont disponibles sans cette forme de syntaxe spéciale.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-147">**Design Notes**: Given caller-receiver parameters, many of the benefits of the *with-expression* are available without this special syntax form.</span></span> <span data-ttu-id="f3a9c-148">Nous envisageons donc si cela est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-148">We are therefore considering whether or not it is needed.</span></span> <span data-ttu-id="f3a9c-149">Son principal avantage est de permettre à de programmer en termes de noms de champs et de propriétés, plutôt qu’en termes de noms de paramètres.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-149">Its main benefit is allowing one to program in terms of the names of fields and properties, rather than in terms of the names of parameters.</span></span> <span data-ttu-id="f3a9c-150">De cette façon, nous améliorons la lisibilité et la qualité des outils (par exemple, Go-to-Definition sur l’identificateur d’un *with_expression* naviguerait vers la propriété plutôt que vers un paramètre de méthode).</span><span class="sxs-lookup"><span data-stu-id="f3a9c-150">In this way we improve both readability and the quality of tooling (e.g. go-to-definition on the identifier of a *with_expression* would navigate to the property rather than to a method parameter).</span></span>

- <span data-ttu-id="f3a9c-151">[] **Problème d’ouverture**: cette description doit être modifiée pour prendre en charge les méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-151">[ ] **Open issue**: This description should be modified to support extension methods.</span></span>

### <a name="pattern-matching"></a><span data-ttu-id="f3a9c-152">correspondance au modèle</span><span class="sxs-lookup"><span data-stu-id="f3a9c-152">pattern-matching</span></span>

<span data-ttu-id="f3a9c-153">Consultez la [spécification de critères spéciaux](csharp-8.0/patterns.md#positional-pattern) pour une spécification de `Deconstruct` et sa relation à la mise en correspondance de modèle.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-153">See the [Pattern Matching Specification](csharp-8.0/patterns.md#positional-pattern) for a specification of `Deconstruct` and its relationship to pattern-matching.</span></span>

> <span data-ttu-id="f3a9c-154">**Remarques**sur la conception : en vertu du compilateur, `Deconstruct` comme indiqué dans le présent document, et de la spécification de la mise en correspondance des modèles, une déclaration d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="f3a9c-154">**Design Notes**: By virtue of the compiler-generated `Deconstruct` as specified herein, and the specification for pattern-matching, a record declaration</span></span>
> ```cs
> public class Point(int X, int Y);
> ```
> <span data-ttu-id="f3a9c-155">prend en charge la mise en correspondance des modèles positionnels comme suit</span><span class="sxs-lookup"><span data-stu-id="f3a9c-155">will support positional pattern-matching as follows</span></span>
> ```cs
> Point p = new Point(3, 4);
> if (p is Point(3, var y)) { // if X is 3
>     Console.WriteLine(y);   // print Y
> }
> ```

### <a name="record-type-declarations"></a><span data-ttu-id="f3a9c-156">déclarations de type d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="f3a9c-156">record type declarations</span></span>

<span data-ttu-id="f3a9c-157">La syntaxe d’une `class` `struct` déclaration ou est étendue pour prendre en charge les paramètres de valeur ; les paramètres deviennent des propriétés du type :</span><span class="sxs-lookup"><span data-stu-id="f3a9c-157">The syntax for a `class` or `struct` declaration is extended to support value parameters; the parameters become properties of the type:</span></span>

```antlr
class_declaration
    : attributes? class_modifiers? 'partial'? 'class' identifier type_parameter_list?
      record_parameters? record_class_base? type_parameter_constraints_clauses? class_body
    ;

struct_declaration
    : attributes? struct_modifiers? 'partial'? 'struct' identifier type_parameter_list?
      record_parameters? struct_interfaces? type_parameter_constraints_clauses? struct_body
    ;

record_class_base
    : class_type record_base_arguments?
    | interface_type_list
    | class_type record_base_arguments? ',' interface_type_list
    ;

record_base_arguments
    : '(' argument_list? ')'
    ;

record_parameters
    : '(' record_parameter_list? ')'
    ;

record_parameter_list
    : record_parameter
    | record_parameter ',' record_parameter_list
    ;

record_parameter
    : attributes? type identifier record_property_name? default_argument?
    ;

record_property_name
    : ':' identifier
    ;

class_body
    : '{' class_member_declarations? '}'
    | ';'
    ;

struct_body
    : '{' struct_members_declarations? '}'
    | ';'
    ;
```

> <span data-ttu-id="f3a9c-158">**Design Notes**: étant donné que les types d’enregistrements sont souvent utiles sans qu’aucun membre n’ait besoin d’être explicitement déclaré dans un corps de classe, nous modifions la syntaxe de la déclaration pour permettre à un corps d’être simplement un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-158">**Design Notes**: Because record types are often useful without the need for any members explicitly declared in a class-body, we modify the syntax of the declaration to allow a body to be simply a semicolon.</span></span>

<span data-ttu-id="f3a9c-159">Une classe (struct) déclarée avec *Record-Parameters* est appelée *classe d’enregistrement* (struct d'*enregistrement*), soit un *type d’enregistrement*.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-159">A class (struct) declared with the *record-parameters* is called a *record class* (*record struct*), either of which is a *record type*.</span></span>

- <span data-ttu-id="f3a9c-160">[] **Problème d’ouverture**: nous devons inclure *primary_constructor_body* dans la grammaire pour qu’elle puisse apparaître dans une déclaration de type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-160">[ ] **Open issue**: We need to include *primary_constructor_body* in the grammar so that it can appear inside a record type declaration.</span></span>
- <span data-ttu-id="f3a9c-161">[] **Problème d’ouverture**: Quelles sont les règles de conflit de noms pour les noms de paramètres ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-161">[ ] **Open issue**: What are the name conflict rules for the parameter names?</span></span> <span data-ttu-id="f3a9c-162">Vraisemblablement, il n’est pas possible d’entrer en conflit avec un paramètre de type ou un autre *paramètre d’enregistrement*.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-162">Presumably one is not allowed to conflict with a type parameter or another *record-parameter*.</span></span>
- <span data-ttu-id="f3a9c-163">[] **Problème d’ouverture**: nous devons spécifier la portée des paramètres d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-163">[ ] **Open issue**: We need to specify the scope of the record-parameters.</span></span> <span data-ttu-id="f3a9c-164">Où peut-il être utilisé ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-164">Where can they be used?</span></span> <span data-ttu-id="f3a9c-165">Vraisemblablement dans les initialiseurs de champ d’instance et *primary_constructor_body* au moins.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-165">Presumably within instance field initializers and *primary_constructor_body* at least.</span></span>
- <span data-ttu-id="f3a9c-166">[] **Problème d’ouverture**: une déclaration de type d’enregistrement peut-elle être partielle ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-166">[ ] **Open issue**: Can a record type declaration be partial?</span></span> <span data-ttu-id="f3a9c-167">Dans ce cas, les paramètres doivent-ils être répétés sur chaque composant ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-167">If so, must the parameters be repeated on each part?</span></span>

#### <a name="members-of-a-record-type"></a><span data-ttu-id="f3a9c-168">Membres d’un type d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="f3a9c-168">Members of a record type</span></span>

<span data-ttu-id="f3a9c-169">En plus des membres déclarés dans le *corps*de la classe, un type d’enregistrement a les membres supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="f3a9c-169">In addition to the members declared in the *class-body*, a record type has the following additional members:</span></span>

##### <a name="primary-constructor"></a><span data-ttu-id="f3a9c-170">Constructeur principal</span><span class="sxs-lookup"><span data-stu-id="f3a9c-170">Primary Constructor</span></span>

<span data-ttu-id="f3a9c-171">Un type d’enregistrement a un `public` constructeur dont la signature correspond aux paramètres de valeur de la déclaration de type.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-171">A record type has a `public` constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="f3a9c-172">C’est ce que l’on appelle le *constructeur principal* pour le type et entraîne la suppression du *constructeur par défaut* déclaré implicitement.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-172">This is called the *primary constructor* for the type, and causes the implicitly declared *default constructor* to be suppressed.</span></span>

<span data-ttu-id="f3a9c-173">Au moment de l’exécution, le constructeur principal</span><span class="sxs-lookup"><span data-stu-id="f3a9c-173">At runtime the primary constructor</span></span>

* <span data-ttu-id="f3a9c-174">Initialise les champs de stockage générés par le compilateur pour les propriétés correspondant aux paramètres de valeur (si ces propriétés sont fournies par le compilateur ; [voir 1.1.2](#1.1.2)); Cliquez</span><span class="sxs-lookup"><span data-stu-id="f3a9c-174">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided; [see 1.1.2](#1.1.2)); then</span></span>
* <span data-ttu-id="f3a9c-175">exécute les initialiseurs de champ d’instance qui apparaissent dans le corps de la *classe*; Et puis</span><span class="sxs-lookup"><span data-stu-id="f3a9c-175">executes the instance field initializers appearing in the *class-body*; and then</span></span>
* <span data-ttu-id="f3a9c-176">appelle un constructeur de classe de base :</span><span class="sxs-lookup"><span data-stu-id="f3a9c-176">invokes a base class constructor:</span></span>
    * <span data-ttu-id="f3a9c-177">Si le *record_base_arguments*contient des arguments, un constructeur de base sélectionné par la résolution de surcharge avec ces arguments est appelé ;</span><span class="sxs-lookup"><span data-stu-id="f3a9c-177">If there are arguments in the *record_base_arguments*, a base constructor selected by overload resolution with these arguments is invoked;</span></span>
    * <span data-ttu-id="f3a9c-178">Dans le cas contraire, un constructeur de base est appelé sans argument.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-178">Otherwise a base constructor is invoked with no arguments.</span></span>
* <span data-ttu-id="f3a9c-179">exécute le corps de chaque *primary_constructor_body*, le cas échéant, dans l’ordre de la source.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-179">executes the body of each *primary_constructor_body*, if any, in source order.</span></span>

- <span data-ttu-id="f3a9c-180">[] **Problème d’ouverture**: nous devons spécifier cet ordre, particulièrement entre les unités de compilation pour les éléments partiels.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-180">[ ] **Open issue**: We need to specify that order, particularly across compilation units for partials.</span></span>
- <span data-ttu-id="f3a9c-181">[] **Problème d’ouverture**: nous devons spécifier que chaque constructeur explicitement déclaré doit être chaîné au constructeur principal.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-181">[ ] **Open Issue**: We need to specify that every explicitly declared constructor must chain to the primary constructor.</span></span>
- <span data-ttu-id="f3a9c-182">[] **Problème d’ouverture**: doit-il être autorisé à modifier le modificateur d’accès sur le constructeur principal ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-182">[ ] **Open issue**: Should it be allowed to change the access modifier on the primary constructor?</span></span>
- <span data-ttu-id="f3a9c-183">[] **Problème d’ouverture**: dans un struct d’enregistrement, il s’agit d’une erreur pour qu’il n’y ait pas de paramètres d’enregistrement ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-183">[ ] **Open issue**: In a record struct, it is an error for there to be no record parameters?</span></span>

##### <a name="primary-constructor-body"></a><span data-ttu-id="f3a9c-184">Corps du constructeur principal</span><span class="sxs-lookup"><span data-stu-id="f3a9c-184">Primary constructor body</span></span>

```antlr
primary_constructor_body
    : attributes? constructor_modifiers? identifier block
    ;
```

<span data-ttu-id="f3a9c-185">Un *primary_constructor_body* peut uniquement être utilisé dans une déclaration de type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-185">A *primary_constructor_body* may only be used within a record type declaration.</span></span> <span data-ttu-id="f3a9c-186">L' *identificateur* d’un *primary_constructor_body* doit nommer le type d’enregistrement dans lequel il est déclaré.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-186">The *identifier* of a *primary_constructor_body* shall name the record type in which it is declared.</span></span>

<span data-ttu-id="f3a9c-187">Le *primary_constructor_body* ne déclare pas un membre seul, mais est un moyen pour le programmeur de fournir des attributs pour et de spécifier l’accès de, le constructeur principal d’un type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-187">The *primary_constructor_body* does not declare a member on its own, but is a way for the programmer to provide attributes for, and specify the access of, a record type's primary constructor.</span></span> <span data-ttu-id="f3a9c-188">Elle permet également au programmeur de fournir du code supplémentaire qui est exécuté lorsqu’une instance du type d’enregistrement est construite.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-188">It also enables the programmer to provide additional code that will be executed when an instance of the record type is constructed.</span></span>

- <span data-ttu-id="f3a9c-189">[] **Problème d’ouverture**: nous devrions noter qu’un constructeur par défaut de struct ignore cela.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-189">[ ] **Open issue**: We should note that a struct default constructor bypasses this.</span></span>
- <span data-ttu-id="f3a9c-190">[] **Problème d’ouverture**: nous devons spécifier l’ordre d’exécution de l’initialisation.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-190">[ ] **Open issue**: We should specify the execution order of initialization.</span></span>
- <span data-ttu-id="f3a9c-191">[] **Problème d’ouverture**: devons-nous autoriser un *primary_constructor_body* (vraisemblablement sans attributs et modificateurs) dans une déclaration de type non-enregistrement et le considérer comme le code d’un initialiseur de champ d’instance ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-191">[ ] **Open issue**: Should we allow something like a *primary_constructor_body* (presumably without attributes and modifiers) in a non-record type declaration, and treat it like we would the code of an instance field initializer?</span></span>

##### <a name="properties"></a><span data-ttu-id="f3a9c-192">Propriétés</span><span class="sxs-lookup"><span data-stu-id="f3a9c-192">Properties</span></span>

<span data-ttu-id="f3a9c-193">Pour chaque paramètre d’enregistrement d’une déclaration de type d’enregistrement, il existe un `public` membre de propriété correspondant dont le nom et le type sont extraits de la déclaration de paramètre de valeur.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-193">For each record parameter of a record type declaration there is a corresponding `public` property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="f3a9c-194">Son nom est l' *identificateur* de l' *record_property_name*, s’il est présent, ou l' *identificateur* du *record_parameter* dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-194">Its name is the *identifier* of the *record_property_name*, if present, or the *identifier* of the *record_parameter* otherwise.</span></span> <span data-ttu-id="f3a9c-195">Si aucune propriété publique concrète (c’est-à-dire non abstraite) avec un `get` accesseur et avec ce nom et ce type n’est explicitement déclarée ou héritée, elle est produite par le compilateur comme suit :</span><span class="sxs-lookup"><span data-stu-id="f3a9c-195">If no concrete (i.e. non-abstract) public property with a `get` accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

* <span data-ttu-id="f3a9c-196">Pour un *struct d’enregistrement* ou une `sealed` *classe d’enregistrement*:</span><span class="sxs-lookup"><span data-stu-id="f3a9c-196">For a *record struct* or a `sealed` *record class*:</span></span>
 * <span data-ttu-id="f3a9c-197">Un `private` `readonly` champ est produit sous la forme d’un champ de stockage pour une `readonly` propriété.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-197">A `private` `readonly` field is produced as a backing field for a `readonly` property.</span></span> <span data-ttu-id="f3a9c-198">Sa valeur est initialisée pendant la construction avec la valeur du paramètre de constructeur principal correspondant.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-198">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span>
 * <span data-ttu-id="f3a9c-199">L’accesseur de la propriété `get` est implémenté pour retourner la valeur du champ de stockage.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-199">The property's `get` accessor is implemented to return the value of the backing field.</span></span>
 * <span data-ttu-id="f3a9c-200">Chaque accesseur de propriété virtuelle héritée « correspondant » `get` est substitué.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-200">Each "matching" inherited virtual property's `get` accessor is overridden.</span></span>

> <span data-ttu-id="f3a9c-201">**Design Notes**: en d’autres termes, si vous étendez une classe de base ou implémentez une interface qui déclare une propriété abstraite publique avec le même nom et le même type qu’un paramètre d’enregistrement, cette propriété est substituée ou implémentée.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-201">**Design notes**: In other words, if you extend a base class or implement an interface that declares a public abstract property with the same name and type as a record parameter, that property is overridden or implemented.</span></span>

- <span data-ttu-id="f3a9c-202">[] **Problème d’ouverture**: est-il possible de modifier le modificateur d’accès d’une propriété lorsqu’elle est déclarée explicitement ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-202">[ ] **Open issue**: Should it be possible to change the access modifier on a property when it is explicitly declared?</span></span>
- <span data-ttu-id="f3a9c-203">[] **Problème d’ouverture**: est-il possible de remplacer un champ pour une propriété ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-203">[ ] **Open issue**: Should it be possible to substitute a field for a property?</span></span>

##### <a name="object-methods"></a><span data-ttu-id="f3a9c-204">Méthodes d'objets</span><span class="sxs-lookup"><span data-stu-id="f3a9c-204">Object Methods</span></span>

<span data-ttu-id="f3a9c-205">Pour un *struct d’enregistrement* ou une `sealed` *classe d’enregistrement*, les implémentations des méthodes `object.GetHashCode()` et `object.Equals(object)` sont produits par le compilateur, sauf s’ils sont fournis par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-205">For a *record struct* or a `sealed` *record class*, implementations of the methods `object.GetHashCode()` and `object.Equals(object)` are produced by the compiler unless provided by the user.</span></span>

- <span data-ttu-id="f3a9c-206">[] **Problème d’ouverture**: nous devons spécifier précisément leur implémentation.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-206">[ ] **Open issue**: We should precisely specify their implementation.</span></span>
- <span data-ttu-id="f3a9c-207">[] **Problème d’ouverture**: nous devons également ajouter l’interface `IEquatable<T>` pour le type d’enregistrement et spécifier que les implémentations sont fournies.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-207">[ ] **Open issue**: We should also add the interface `IEquatable<T>` for the record type and specify that implementations are provided.</span></span>
- <span data-ttu-id="f3a9c-208">[] **Problème d’ouverture**: nous devons également spécifier que nous implémentons tous les `IEquatable<T>.Equals` .</span><span class="sxs-lookup"><span data-stu-id="f3a9c-208">[ ] **Open issue**: We should also specify that we implement every `IEquatable<T>.Equals`.</span></span>
- <span data-ttu-id="f3a9c-209">[] **Problème d’ouverture**: nous devons spécifier précisément comment nous résolvons le problème des valeurs égales en termes d’héritage d’enregistrement : plus précisément comment nous générons des méthodes d’égalité de manière à ce qu’elles soient symétriques, transitives, réflexes, etc.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-209">[ ] **Open issue**: We should specify precisely how we solve the problem of Equals in the face of record inheritance: specifically how we generate equality methods such that they are symmetric, transitive, reflexive, etc.</span></span>
- <span data-ttu-id="f3a9c-210">[] **Problème d’ouverture**: il a été proposé de mettre en œuvre `operator ==` et `operator !=` pour les types d’enregistrements.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-210">[ ] **Open issue**: It has been proposed that we implement `operator ==` and `operator !=` for record types.</span></span>
- <span data-ttu-id="f3a9c-211">[] **Problème d’ouverture**: dois-je générer automatiquement une implémentation de `object.ToString` ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-211">[ ] **Open issue**: Should we auto-generate an implementation of `object.ToString`?</span></span>

##### `Deconstruct`

<span data-ttu-id="f3a9c-212">Un type d’enregistrement a une méthode générée `public` `void Deconstruct` par le compilateur, sauf si une signature est fournie par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-212">A record type has a compiler-generated `public` method `void Deconstruct` unless one with any signature is provided by the user.</span></span> <span data-ttu-id="f3a9c-213">Chaque paramètre est un `out` paramètre du même nom et du même type que le paramètre correspondant du type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-213">Each parameter is an `out` parameter of the same name and type as the corresponding parameter of the record type.</span></span> <span data-ttu-id="f3a9c-214">L’implémentation fournie par le compilateur de cette méthode doit assigner `out` à chaque paramètre la valeur de la propriété correspondante.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-214">The compiler-provided implementation of this method shall assign each `out` parameter with the value of the corresponding property.</span></span>

<span data-ttu-id="f3a9c-215">Consultez [la spécification de critères spéciaux](csharp-8.0/patterns.md#positional-pattern) pour la sémantique de `Deconstruct` .</span><span class="sxs-lookup"><span data-stu-id="f3a9c-215">See [the pattern-matching specification](csharp-8.0/patterns.md#positional-pattern) for the semantics of `Deconstruct`.</span></span>

##### <a name="with-method"></a><span data-ttu-id="f3a9c-216">Méthode `With`</span><span class="sxs-lookup"><span data-stu-id="f3a9c-216">`With` method</span></span>

<span data-ttu-id="f3a9c-217">À moins qu’un membre déclaré par l’utilisateur ne soit nommé `With` déclaré, un type d’enregistrement a une méthode fournie par le compilateur nommée `With` dont le type de retour est le type d’enregistrement lui-même et qui contient un paramètre de valeur correspondant à chaque *paramètre d’enregistrement* dans le même ordre que ces paramètres apparaissent dans la déclaration de type d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-217">Unless there is a user-declared member named `With` declared, a record type has a compiler-provided method named `With` whose return type is the record type itself, and containing one value parameter corresponding to each *record-parameter* in the same order that these parameters appear in the record type declaration.</span></span> <span data-ttu-id="f3a9c-218">Chaque paramètre doit avoir un *argument par défaut du récepteur* de l’appelant de la propriété correspondante.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-218">Each parameter shall have a *caller-receiver default-argument* of the corresponding property.</span></span>

<span data-ttu-id="f3a9c-219">Dans une `abstract` classe d’enregistrement, la méthode fournie par le compilateur `With` est abstraite.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-219">In an `abstract` record class, the compiler-provided `With` method is abstract.</span></span> <span data-ttu-id="f3a9c-220">Dans un struct d’enregistrement ou une classe d’enregistrement sealed, la méthode fournie par le compilateur `With` est `sealed` .</span><span class="sxs-lookup"><span data-stu-id="f3a9c-220">In a record struct, or a sealed record class, the compiler-provided `With` method is `sealed`.</span></span> <span data-ttu-id="f3a9c-221">Sinon, la méthode fournie `With` par le compilateur est’Virtual et son implémentation retourne une nouvelle instance produite en appelant le constructeur principal avec les paramètres comme arguments pour créer une nouvelle instance à partir des paramètres et retourner cette nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-221">Otherwise the compiler-provided `With` method is \`virtual and its implementation shall return a new instance produced by invoking the primary constructor with the parameters as arguments to create a new instance from the parameters, and return that new instance.</span></span>

- <span data-ttu-id="f3a9c-222">[] **Problème d’ouverture**: nous devons également spécifier dans quelles conditions nous remplaçons ou implémentons `With` des méthodes ou des méthodes virtuelles héritées `With` à partir d’interfaces implémentées.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-222">[ ] **Open issue**: We should also specify under what conditions we override or implement inherited virtual `With` methods or `With` methods from implemented interfaces.</span></span>
- <span data-ttu-id="f3a9c-223">[] **Problème d’ouverture**: nous devrions indiquer ce qui se passe lorsque nous héritons d’une méthode non virtuelle `With` .</span><span class="sxs-lookup"><span data-stu-id="f3a9c-223">[ ] **Open issue**: We should say what happens when we inherit a non-virtual `With` method.</span></span>

> <span data-ttu-id="f3a9c-224">**Design Notes**: étant donné que les types d’enregistrements sont par défaut immuables, la `With` méthode permet de créer une nouvelle instance qui est la même qu’une instance existante, mais avec les propriétés sélectionnées en fonction de nouvelles valeurs.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-224">**Design notes**: Because record types are by default immutable, the `With` method provides a way of creating a new instance that is the same as an existing instance but with selected properties given new values.</span></span> <span data-ttu-id="f3a9c-225">Par exemple, donné</span><span class="sxs-lookup"><span data-stu-id="f3a9c-225">For example, given</span></span>
> ```cs
> public class Point(int X, int Y);
> ```
> <span data-ttu-id="f3a9c-226">Il existe un membre fourni par le compilateur</span><span class="sxs-lookup"><span data-stu-id="f3a9c-226">there is a compiler-provided member</span></span>
> ```cs
>     public virtual Point With(int X = this.X, int Y = this.Y) => new Point(X, Y);
> ```
> <span data-ttu-id="f3a9c-227">Qui active une variable du type d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="f3a9c-227">Which enables an variable of the record type</span></span>
> ```cs
> var p = new Point(3, 4);
> ```
> <span data-ttu-id="f3a9c-228">à remplacer par une instance qui a une ou plusieurs propriétés différentes</span><span class="sxs-lookup"><span data-stu-id="f3a9c-228">to be replaced with an instance that has one or more properties different</span></span>
> ```cs
>     p = p.With(X: 5);
> ```
> <span data-ttu-id="f3a9c-229">Cela peut également être exprimé à l’aide de la *with_expression*:</span><span class="sxs-lookup"><span data-stu-id="f3a9c-229">This can also be expressed using the *with_expression*:</span></span>
> ```cs
>     p = p with { X = 5 };
> ```

### <a name="examples"></a><span data-ttu-id="f3a9c-230">Exemples</span><span class="sxs-lookup"><span data-stu-id="f3a9c-230">Examples</span></span>

#### <a name="compatibility-of-record-types"></a><span data-ttu-id="f3a9c-231">Compatibilité des types d’enregistrements</span><span class="sxs-lookup"><span data-stu-id="f3a9c-231">Compatibility of record types</span></span>

<span data-ttu-id="f3a9c-232">Étant donné que le programmeur peut ajouter des membres à une déclaration de type d’enregistrement, il est souvent possible de modifier le jeu d’éléments d’enregistrement sans affecter les clients existants.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-232">Because the programmer can add members to a record type declaration, it is often possible to change the set of record elements without affecting existing clients.</span></span> <span data-ttu-id="f3a9c-233">Par exemple, en fonction d’une version initiale d’un type d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="f3a9c-233">For example, given an initial version of a record type</span></span>

```cs
// v1
public class Person(string Name, DateTime DateOfBirth);
```

<span data-ttu-id="f3a9c-234">Un nouvel élément du type d’enregistrement peut être compatible avec la prochaine révision du type sans affecter la compatibilité binaire ou source :</span><span class="sxs-lookup"><span data-stu-id="f3a9c-234">A new element of the record type can be compatibly added in the next revision of the type without affecting binary or source compatibility:</span></span>

```cs
// v2
public class Person(string Name, DateTime DateOfBirth, string HomeTown)
{
    // Note: below operations added to retain binary compatibility with v1
    public Person(string Name, DateTime DateOfBirth) : this(Name, DateOfBirth, string.Empty) {}
    public static void operator is(Person self, out string Name, out DateTime DateOfBirth)
        { Name = self.Name; DateOfBirth = self.DateOfBirth; }
    public Person With(string Name, DateTime DateOfBirth) => new Person(Name, DateOfBirth);
}
```

#### <a name="record-struct-example"></a><span data-ttu-id="f3a9c-235">exemple d’enregistrement d’un struct</span><span class="sxs-lookup"><span data-stu-id="f3a9c-235">record struct example</span></span>

<span data-ttu-id="f3a9c-236">Ce struct d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="f3a9c-236">This record struct</span></span>

```cs
public struct Pair(object First, object Second);
```

<span data-ttu-id="f3a9c-237">est traduit dans ce code</span><span class="sxs-lookup"><span data-stu-id="f3a9c-237">is translated to this code</span></span>

```cs
public struct Pair : IEquatable<Pair>
{
    public object First { get; }
    public object Second { get; }
    public Pair(object First, object Second)
    {
        this.First = First;
        this.Second = Second;
    }
    public bool Equals(Pair other) // for IEquatable<Pair>
    {
        return Equals(First, other.First) && Equals(Second, other.Second);
    }
    public override bool Equals(object other)
    {
        return (other as Pair)?.Equals(this) == true;
    }
    public override int GetHashCode()
    {
        return (First?.GetHashCode()*17 + Second?.GetHashCode()).GetValueOrDefault();
    }
    public Pair With(object First = this.First, object Second = this.Second) => new Pair(First, Second);
    public void Deconstruct(out object First, out object Second)
    {
        First = self.First;
        Second = self.Second;
    }
}
```

- <span data-ttu-id="f3a9c-238">[] **Problème d’ouverture**: l’implémentation de Equals (paire autre) doit-elle être un membre public de pair ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-238">[ ] **Open issue**: should the implementation of Equals(Pair other) be a public member of Pair?</span></span>
- <span data-ttu-id="f3a9c-239">[] **Problème d’ouverture**: cette implémentation de `Equals` n’est pas symétrique dans le cas de l’héritage.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-239">[ ] **Open issue**: This implementation of `Equals` is not symmetric in the face of inheritance.</span></span>
- <span data-ttu-id="f3a9c-240">[] **Problème d’ouverture**: un enregistrement doit-il être déclaré `operator ==` et `operator !=` ?</span><span class="sxs-lookup"><span data-stu-id="f3a9c-240">[ ] **Open issue**: Should a record declare `operator ==` and `operator !=`?</span></span>

> <span data-ttu-id="f3a9c-241">**Design Notes**: étant donné qu’un seul type d’enregistrement peut hériter d’un autre, et que cette implémentation de n’est `Equals` pas symétrique dans ce cas, il n’est pas correct.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-241">**Design notes**: Because one record type can inherit from another, and this implementation of `Equals` would not be symmetric in that case, it is not correct.</span></span> <span data-ttu-id="f3a9c-242">Nous proposons de mettre en œuvre l’égalité de la manière suivante :</span><span class="sxs-lookup"><span data-stu-id="f3a9c-242">We propose to implement equality this way:</span></span>
> ```cs
>     public bool Equals(Pair other) // for IEquatable<Pair>
>     {
>         return other != null && EqualityContract == other.EqualityContract &&
>             Equals(First, other.First) && Equals(Second, other.Second);
>     }
>     protected virtual Type EqualityContract => typeof(Pair);
> ```
> <span data-ttu-id="f3a9c-243">Les enregistrements dérivés le seraient `override EqualityContract` .</span><span class="sxs-lookup"><span data-stu-id="f3a9c-243">Derived records would `override EqualityContract`.</span></span> <span data-ttu-id="f3a9c-244">L’alternative la moins attrayante est de restreindre l’héritage.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-244">The less attractive alternative is to restrict inheritance.</span></span>

#### <a name="sealed-record-example"></a><span data-ttu-id="f3a9c-245">exemple d’enregistrement sealed</span><span class="sxs-lookup"><span data-stu-id="f3a9c-245">sealed record example</span></span>

<span data-ttu-id="f3a9c-246">Cette classe d’enregistrement sealed</span><span class="sxs-lookup"><span data-stu-id="f3a9c-246">This sealed record class</span></span>

```cs
public sealed class Student(string Name, decimal Gpa);
```

<span data-ttu-id="f3a9c-247">est traduit dans ce code</span><span class="sxs-lookup"><span data-stu-id="f3a9c-247">is translated into this code</span></span>

```cs
public sealed class Student : IEquatable<Student>
{
    public string Name { get; }
    public decimal Gpa { get; }
    public Student(string Name, decimal Gpa)
    {
        this.Name = Name;
        this.Gpa = Gpa;
    }
    public bool Equals(Student other) // for IEquatable<Student>
    {
        return other != null && Equals(Name, other.Name) && Equals(Gpa, other.Gpa);
    }
    public override bool Equals(object other)
    {
        return this.Equals(other as Student);
    }
    public override int GetHashCode()
    {
        return (Name?.GetHashCode()*17 + Gpa?.GetHashCode()).GetValueOrDefault();
    }
    public Student With(string Name = this.Name, decimal Gpa = this.Gpa) => new Student(Name, Gpa);
    public void Deconstruct(out string Name, out decimal Gpa)
    {
        Name = self.Name;
        Gpa = self.Gpa;
    }
}
```

#### <a name="abstract-record-class-example"></a><span data-ttu-id="f3a9c-248">exemple de classe d’enregistrement abstraite</span><span class="sxs-lookup"><span data-stu-id="f3a9c-248">abstract record class example</span></span>

<span data-ttu-id="f3a9c-249">Cette classe d’enregistrement abstraite</span><span class="sxs-lookup"><span data-stu-id="f3a9c-249">This abstract record class</span></span>

```cs
public abstract class Person(string Name);
```

<span data-ttu-id="f3a9c-250">est traduit dans ce code</span><span class="sxs-lookup"><span data-stu-id="f3a9c-250">is translated into this code</span></span>

```cs
public abstract class Person : IEquatable<Person>
{
    public string Name { get; }
    public Person(string Name)
    {
        this.Name = Name;
    }
    public bool Equals(Person other)
    {
        return other != null && Equals(Name, other.Name);
    }
    public override bool Equals(object other)
    {
        return Equals(other as Person);
    }
    public override int GetHashCode()
    {
        return (Name?.GetHashCode()).GetValueOrDefault();
    }
    public abstract Person With(string Name = this.Name);
    public void Deconstruct(Person self, out string Name)
    {
        Name = self.Name;
    }
}
```

#### <a name="combining-abstract-and-sealed-records"></a><span data-ttu-id="f3a9c-251">combinaison d’enregistrements abstraits et sealed</span><span class="sxs-lookup"><span data-stu-id="f3a9c-251">combining abstract and sealed records</span></span>

<span data-ttu-id="f3a9c-252">À partir de la classe d’enregistrement abstraite `Person` ci-dessus, cette classe d’enregistrement sealed</span><span class="sxs-lookup"><span data-stu-id="f3a9c-252">Given the abstract record class `Person` above, this sealed record class</span></span>

```cs
public sealed class Student(string Name, decimal Gpa) : Person(Name);
```

<span data-ttu-id="f3a9c-253">est traduit dans ce code</span><span class="sxs-lookup"><span data-stu-id="f3a9c-253">is translated into this code</span></span>

```cs
public sealed class Student : Person, IEquatable<Student>
{
    public override string Name { get; }
    public decimal Gpa { get; }
    public Student(string Name, decimal Gpa) : base(Name)
    {
        this.Name = Name;
        this.Gpa = Gpa;
    }
    public override bool Equals(Student other) // for IEquatable<Student>
    {
        return Equals(Name, other.Name) && Equals(Gpa, other.Gpa);
    }
    public bool Equals(Person other) // for IEquatable<Person>
    {
        return (other as Student)?.Equals(this) == true;
    }
    public override bool Equals(object other)
    {
        return (other as Student)?.Equals(this) == true;
    }
    public override int GetHashCode()
    {
        return (Name?.GetHashCode()*17 + Gpa.GetHashCode()).GetValueOrDefault();
    }
    public Student With(string Name = this.Name, decimal Gpa = this.Gpa) => new Student(Name, Gpa);
    public override Person With(string Name = this.Name) => new Student(Name, Gpa);
    public void Deconstruct(Student self, out string Name, out decimal Gpa)
    {
        Name = self.Name;
        Gpa = self.Gpa;
    }
}
```

## <a name="drawbacks"></a><span data-ttu-id="f3a9c-254">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="f3a9c-254">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="f3a9c-255">Comme pour toutes les fonctionnalités de langage, nous devons déterminer si la complexité supplémentaire de la langue est remboursée dans la clarté supplémentaire offerte par le corps des programmes C# qui tireraient parti de la fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-255">As with any language feature, we must question whether the additional complexity to the language is repaid in the additional clarity offered to the body of C# programs that would benefit from the feature.</span></span>

## <a name="alternatives"></a><span data-ttu-id="f3a9c-256">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="f3a9c-256">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="f3a9c-257">Nous avons envisagé d’ajouter des *constructeurs principaux* en C# 6.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-257">We considered adding *primary constructors* in C# 6.</span></span> <span data-ttu-id="f3a9c-258">Bien qu’ils occupent la même surface syntaxique que cette proposition, nous avons constaté qu’ils ne relevaient pas des avantages offerts par les enregistrements.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-258">Although they occupy the same syntactic surface as this proposal, we found that they fell short of the advantages offered by records.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="f3a9c-259">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="f3a9c-259">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="f3a9c-260">Les questions ouvertes s’affichent dans le corps de la proposition.</span><span class="sxs-lookup"><span data-stu-id="f3a9c-260">Open questions appear in the body of the proposal.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="f3a9c-261">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="f3a9c-261">Design meetings</span></span>

<span data-ttu-id="f3a9c-262">TBD</span><span class="sxs-lookup"><span data-stu-id="f3a9c-262">TBD</span></span>
