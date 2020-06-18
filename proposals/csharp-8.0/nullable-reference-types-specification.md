---
ms.openlocfilehash: 060d3af7d06033b524e9e967a7f43c7577bc7965
ms.sourcegitcommit: 538df2e3c334d94cac1fac6a382ddfe15452ad96
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908315"
---
# <a name="nullable-reference-types-specification"></a><span data-ttu-id="b8c22-101">Spécification des types référence Nullable</span><span class="sxs-lookup"><span data-stu-id="b8c22-101">Nullable Reference Types Specification</span></span>

<span data-ttu-id="b8c22-102">***Il s’agit d’un travail en cours : plusieurs parties sont manquantes ou incomplètes.***</span><span class="sxs-lookup"><span data-stu-id="b8c22-102">***This is a work in progress - several parts are missing or incomplete.***</span></span>

## <a name="syntax"></a><span data-ttu-id="b8c22-103">Syntaxe</span><span class="sxs-lookup"><span data-stu-id="b8c22-103">Syntax</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="b8c22-104">Types références Nullables</span><span class="sxs-lookup"><span data-stu-id="b8c22-104">Nullable reference types</span></span>

<span data-ttu-id="b8c22-105">Les types référence Nullable ont la même syntaxe `T?` que la forme abrégée des types valeur Nullable, mais n’ont pas de forme longue correspondante.</span><span class="sxs-lookup"><span data-stu-id="b8c22-105">Nullable reference types have the same syntax `T?` as the short form of nullable value types, but do not have a corresponding long form.</span></span>

<span data-ttu-id="b8c22-106">Dans le cadre de la spécification, la `nullable_type` production actuelle est renommée `nullable_value_type` et une `nullable_reference_type` production est ajoutée :</span><span class="sxs-lookup"><span data-stu-id="b8c22-106">For the purposes of the specification, the current `nullable_type` production is renamed to `nullable_value_type`, and a `nullable_reference_type` production is added:</span></span>

```antlr
reference_type
    : ...
    | nullable_reference_type
    ;
    
nullable_reference_type
    : non_nullable_reference_type '?'
    ;
    
non_nullable_reference_type
    : type
    ;
```

<span data-ttu-id="b8c22-107">Le `non_nullable_reference_type` dans un `nullable_reference_type` doit être un type de référence non Nullable (classe, interface, délégué ou tableau) ou un paramètre de type qui est imposé comme étant un type de référence non Nullable (par le biais de la `class` contrainte ou d’une classe autre que `object` ).</span><span class="sxs-lookup"><span data-stu-id="b8c22-107">The `non_nullable_reference_type` in a `nullable_reference_type` must be a non-nullable reference type (class, interface, delegate or array), or a type parameter that is constrained to be a non-nullable reference type (through the `class` constraint, or a class other than `object`).</span></span>

<span data-ttu-id="b8c22-108">Les types de référence Nullable ne peuvent pas se trouver aux emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="b8c22-108">Nullable reference types cannot occur in the following positions:</span></span>

- <span data-ttu-id="b8c22-109">comme une classe ou une interface de base</span><span class="sxs-lookup"><span data-stu-id="b8c22-109">as a base class or interface</span></span>
- <span data-ttu-id="b8c22-110">en tant que récepteur d’un`member_access`</span><span class="sxs-lookup"><span data-stu-id="b8c22-110">as the receiver of a `member_access`</span></span>
- <span data-ttu-id="b8c22-111">comme `type` dans un`object_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="b8c22-111">as the `type` in an `object_creation_expression`</span></span>
- <span data-ttu-id="b8c22-112">comme `delegate_type` dans un`delegate_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="b8c22-112">as the `delegate_type` in a `delegate_creation_expression`</span></span>
- <span data-ttu-id="b8c22-113">comme `type` dans un `is_expression` , un `catch_clause` ou un`type_pattern`</span><span class="sxs-lookup"><span data-stu-id="b8c22-113">as the `type` in an `is_expression`, a `catch_clause` or a `type_pattern`</span></span>
- <span data-ttu-id="b8c22-114">en tant que `interface` dans un nom de membre d’interface qualifié complet</span><span class="sxs-lookup"><span data-stu-id="b8c22-114">as the `interface` in a fully qualified interface member name</span></span>

<span data-ttu-id="b8c22-115">Un avertissement est fourni sur un `nullable_reference_type` où le contexte d’annotation Nullable est désactivé.</span><span class="sxs-lookup"><span data-stu-id="b8c22-115">A warning is given on a `nullable_reference_type` where the nullable annotation context is disabled.</span></span>

### <a name="nullable-class-constraint"></a><span data-ttu-id="b8c22-116">Contrainte de classe Nullable</span><span class="sxs-lookup"><span data-stu-id="b8c22-116">Nullable class constraint</span></span>

<span data-ttu-id="b8c22-117">La `class` contrainte a un équivalent Nullable `class?` :</span><span class="sxs-lookup"><span data-stu-id="b8c22-117">The `class` constraint has a nullable counterpart `class?`:</span></span>

```antlr
primary_constraint
    : ...
    | 'class' '?'
    ;
```

### <a name="the-null-forgiving-operator"></a><span data-ttu-id="b8c22-118">Opérateur null-indulgent avec</span><span class="sxs-lookup"><span data-stu-id="b8c22-118">The null-forgiving operator</span></span>

<span data-ttu-id="b8c22-119">L’opérateur de postconnexion `!` est appelé opérateur null-indulgent avec.</span><span class="sxs-lookup"><span data-stu-id="b8c22-119">The post-fix `!` operator is called the null-forgiving operator.</span></span>

```antlr
primary_expression
    : ...
    | null_forgiving_expression
    ;
    
null_forgiving_expression
    : primary_expression '!'
    ;
```

<span data-ttu-id="b8c22-120">Le `primary_expression` doit être d’un type référence.</span><span class="sxs-lookup"><span data-stu-id="b8c22-120">The `primary_expression` must be of a reference type.</span></span>  

<span data-ttu-id="b8c22-121">L’opérateur postfix n' `!` a aucun effet d’exécution, il prend la valeur du résultat de l’expression sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="b8c22-121">The postfix `!` operator has no runtime effect - it evaluates to the result of the underlying expression.</span></span> <span data-ttu-id="b8c22-122">Son seul rôle consiste à modifier l’État null de l’expression et à limiter les avertissements en fonction de son utilisation.</span><span class="sxs-lookup"><span data-stu-id="b8c22-122">Its only role is to change the null state of the expression, and to limit warnings given on its use.</span></span>

### <a name="nullable-implicitly-typed-local-variables"></a><span data-ttu-id="b8c22-123">variables locales implicitement typées Nullable</span><span class="sxs-lookup"><span data-stu-id="b8c22-123">nullable implicitly typed local variables</span></span>

<span data-ttu-id="b8c22-124">`var`déduit un type annoté pour les types référence.</span><span class="sxs-lookup"><span data-stu-id="b8c22-124">`var` infers an annotated type for reference types.</span></span>
<span data-ttu-id="b8c22-125">Par exemple, dans `var s = "";` le `var` est déduit en tant que `string?` .</span><span class="sxs-lookup"><span data-stu-id="b8c22-125">For instance, in `var s = "";` the `var` is inferred as `string?`.</span></span>

### <a name="nullable-compiler-directives"></a><span data-ttu-id="b8c22-126">Directives de compilateur Nullable</span><span class="sxs-lookup"><span data-stu-id="b8c22-126">Nullable compiler directives</span></span>

<span data-ttu-id="b8c22-127">`#nullable`les directives contrôlent l’annotation Nullable et les contextes d’avertissement.</span><span class="sxs-lookup"><span data-stu-id="b8c22-127">`#nullable` directives control the nullable annotation and warning contexts.</span></span>

```antlr
pp_directive
    : ...
    | pp_nullable
    ;
    
pp_nullable
    : whitespace? '#' whitespace? 'nullable' whitespace nullable_action pp_new_line
    ;
    
nullable_action
    : 'disable'
    | 'enable'
    | 'restore'
    ;
```

<span data-ttu-id="b8c22-128">`#pragma warning`les directives sont développées pour permettre la modification du contexte d’avertissement Nullable et pour permettre l’activation des avertissements individuels sur même lorsqu’ils sont désactivés par défaut :</span><span class="sxs-lookup"><span data-stu-id="b8c22-128">`#pragma warning` directives are expanded to allow changing the nullable warning context, and to allow individual warnings to be enabled on even when they're disabled by default:</span></span>

```antlr
pragma_warning_body
    : ...
    | 'warning' whitespace nullable_action whitespace 'nullable'
    ;

warning_action
    : ...
    | 'enable'
    ;
```

<span data-ttu-id="b8c22-129">Notez que la nouvelle forme d' `pragma_warning_body` utilisations `nullable_action` , et non `warning_action` .</span><span class="sxs-lookup"><span data-stu-id="b8c22-129">Note that the new form of `pragma_warning_body` uses `nullable_action`, not `warning_action`.</span></span>

## <a name="nullable-contexts"></a><span data-ttu-id="b8c22-130">Contextes nullables</span><span class="sxs-lookup"><span data-stu-id="b8c22-130">Nullable contexts</span></span>

<span data-ttu-id="b8c22-131">Chaque ligne de code source a un *contexte d’annotation Nullable* et un *contexte d’avertissement Nullable*.</span><span class="sxs-lookup"><span data-stu-id="b8c22-131">Every line of source code has a *nullable annotation context* and a *nullable warning context*.</span></span> <span data-ttu-id="b8c22-132">Ils contrôlent si les annotations Nullable ont un effet et si les avertissements de possibilité de valeur NULL sont fournis.</span><span class="sxs-lookup"><span data-stu-id="b8c22-132">These control whether nullable annotations have effect, and whether nullability warnings are given.</span></span> <span data-ttu-id="b8c22-133">Le contexte d’annotation d’une ligne donnée est *désactivé* ou *activé*.</span><span class="sxs-lookup"><span data-stu-id="b8c22-133">The annotation context of a given line is either *disabled* or *enabled*.</span></span> <span data-ttu-id="b8c22-134">Le contexte d’avertissement d’une ligne donnée est *désactivé* ou *activé*.</span><span class="sxs-lookup"><span data-stu-id="b8c22-134">The warning context of a given line is either *disabled* or *enabled*.</span></span>

<span data-ttu-id="b8c22-135">Les deux contextes peuvent être spécifiés au niveau du projet (en dehors du code source C#), ou n’importe où dans un fichier source via des `#nullable` directives de préprocesseur.</span><span class="sxs-lookup"><span data-stu-id="b8c22-135">Both contexts can be specified at the project level (outside of C# source code), or anywhere within a source file via `#nullable` pre-processor directives.</span></span> <span data-ttu-id="b8c22-136">Si aucun paramètre de niveau projet n’est fourni, la valeur par défaut est pour que les deux contextes soient *désactivés*.</span><span class="sxs-lookup"><span data-stu-id="b8c22-136">If no project level settings are provided the default is for both contexts to be *disabled*.</span></span>

<span data-ttu-id="b8c22-137">La `#nullable` directive contrôle les contextes d’annotation et d’avertissement dans le texte source et a priorité sur les paramètres au niveau du projet.</span><span class="sxs-lookup"><span data-stu-id="b8c22-137">The `#nullable` directive controls the annotation and warning contexts within the source text, and take precedence over the project-level settings.</span></span>

<span data-ttu-id="b8c22-138">Une directive définit le ou les contexte (s) qu’elle contrôle pour les lignes de code suivantes, jusqu’à ce qu’une autre directive la remplace, ou jusqu’à la fin du fichier source.</span><span class="sxs-lookup"><span data-stu-id="b8c22-138">A directive sets the context(s) it controls for subsequent lines of code, until another directive overrides it, or until the end of the source file.</span></span>

<span data-ttu-id="b8c22-139">L’effet des directives est le suivant :</span><span class="sxs-lookup"><span data-stu-id="b8c22-139">The effect of the directives is as follows:</span></span>

- <span data-ttu-id="b8c22-140">`#nullable disable`: Définit l’annotation Nullable et les contextes d’avertissement sur *désactivé*</span><span class="sxs-lookup"><span data-stu-id="b8c22-140">`#nullable disable`: Sets the nullable annotation and warning contexts to *disabled*</span></span>
- <span data-ttu-id="b8c22-141">`#nullable enable`: Définit l’annotation Nullable et les contextes d’avertissement sur *activé*</span><span class="sxs-lookup"><span data-stu-id="b8c22-141">`#nullable enable`: Sets the nullable annotation and warning contexts to *enabled*</span></span>
- <span data-ttu-id="b8c22-142">`#nullable restore`: Restaure l’annotation Nullable et les contextes d’avertissement dans les paramètres du projet</span><span class="sxs-lookup"><span data-stu-id="b8c22-142">`#nullable restore`: Restores the nullable annotation and warning contexts to project settings</span></span>
- <span data-ttu-id="b8c22-143">`#nullable disable annotations`: Définit le contexte d’annotation Nullable sur *désactivé*</span><span class="sxs-lookup"><span data-stu-id="b8c22-143">`#nullable disable annotations`: Sets the nullable annotation context to *disabled*</span></span>
- <span data-ttu-id="b8c22-144">`#nullable enable annotations`: Définit le contexte d’annotation Nullable sur *activé*</span><span class="sxs-lookup"><span data-stu-id="b8c22-144">`#nullable enable annotations`: Sets the nullable annotation context to *enabled*</span></span>
- <span data-ttu-id="b8c22-145">`#nullable restore annotations`: Restaure le contexte d’annotation Nullable aux paramètres du projet</span><span class="sxs-lookup"><span data-stu-id="b8c22-145">`#nullable restore annotations`: Restores the nullable annotation context to project settings</span></span>
- <span data-ttu-id="b8c22-146">`#nullable disable warnings`: Définit le contexte d’avertissement Nullable sur *désactivé*</span><span class="sxs-lookup"><span data-stu-id="b8c22-146">`#nullable disable warnings`: Sets the nullable warning context to *disabled*</span></span>
- <span data-ttu-id="b8c22-147">`#nullable enable warnings`: Définit le contexte d’avertissement Nullable sur *activé*</span><span class="sxs-lookup"><span data-stu-id="b8c22-147">`#nullable enable warnings`: Sets the nullable warning context to *enabled*</span></span>
- <span data-ttu-id="b8c22-148">`#nullable restore warnings`: Restaure le contexte d’avertissement Nullable aux paramètres du projet</span><span class="sxs-lookup"><span data-stu-id="b8c22-148">`#nullable restore warnings`: Restores the nullable warning context to project settings</span></span>

## <a name="nullability-of-types"></a><span data-ttu-id="b8c22-149">Nullabilité des types</span><span class="sxs-lookup"><span data-stu-id="b8c22-149">Nullability of types</span></span>

<span data-ttu-id="b8c22-150">Un type donné peut avoir l’un des quatre nullabilities : *oublie*, non *null*, *Nullable* et *Unknown*.</span><span class="sxs-lookup"><span data-stu-id="b8c22-150">A given type can have one of four nullabilities: *Oblivious*, *nonnullable*, *nullable* and *unknown*.</span></span> 

<span data-ttu-id="b8c22-151">Les types non *null* et *inconnus* peuvent provoquer des avertissements si une `null` valeur potentielle leur est assignée.</span><span class="sxs-lookup"><span data-stu-id="b8c22-151">*Nonnullable* and *unknown* types may cause warnings if a potential `null` value is assigned to them.</span></span> <span data-ttu-id="b8c22-152">Toutefois, les types *oublie* et *Nullable* sont «*null-assignables*» et peuvent avoir des `null` valeurs qui leur sont assignées sans avertissements.</span><span class="sxs-lookup"><span data-stu-id="b8c22-152">*Oblivious* and *nullable* types, however, are "*null-assignable*" and can have `null` values assigned to them without warnings.</span></span> 

<span data-ttu-id="b8c22-153">Les types *oublie* et non *null* peuvent être déréférencés ou assignés sans avertissements.</span><span class="sxs-lookup"><span data-stu-id="b8c22-153">*Oblivious* and *nonnullable* types can be dereferenced or assigned without warnings.</span></span> <span data-ttu-id="b8c22-154">Toutefois, les valeurs de type *Nullable* et *Unknown* sont «*yield null*» et peuvent provoquer des avertissements lorsqu’ils sont déréférencés ou assignés sans vérification de valeur null correcte.</span><span class="sxs-lookup"><span data-stu-id="b8c22-154">Values of *nullable* and *unknown* types, however, are "*null-yielding*" and may cause warnings when dereferenced or assigned without proper null checking.</span></span> 

<span data-ttu-id="b8c22-155">L' *État NULL par défaut* d’un type qui génère un null est « peut-être null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-155">The *default null state* of a null-yielding type is "maybe null".</span></span> <span data-ttu-id="b8c22-156">L’État NULL par défaut d’un type qui ne génère pas de valeur null est « not null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-156">The default null state of a non-null-yielding type is "not null".</span></span>

<span data-ttu-id="b8c22-157">Le genre de type et le contexte d’annotation Nullable qu’il contient se trouvent dans déterminer sa possibilité de valeur NULL :</span><span class="sxs-lookup"><span data-stu-id="b8c22-157">The kind of type and the nullable annotation context it occurs in determine its nullability:</span></span>

- <span data-ttu-id="b8c22-158">Un type valeur `S` non null est toujours non *null*</span><span class="sxs-lookup"><span data-stu-id="b8c22-158">A nonnullable value type `S` is always *nonnullable*</span></span>
- <span data-ttu-id="b8c22-159">Un type valeur Nullable `S?` est toujours *Nullable*</span><span class="sxs-lookup"><span data-stu-id="b8c22-159">A nullable value type `S?` is always *nullable*</span></span>
- <span data-ttu-id="b8c22-160">Un type référence non annoté `C` dans un contexte d’annotation *désactivé* est *oublie*</span><span class="sxs-lookup"><span data-stu-id="b8c22-160">An unannotated reference type `C` in a *disabled* annotation context is *oblivious*</span></span>
- <span data-ttu-id="b8c22-161">Un type référence non annoté `C` dans un contexte d’annotation *activé* n’est pas *null*</span><span class="sxs-lookup"><span data-stu-id="b8c22-161">An unannotated reference type `C` in an *enabled* annotation context is *nonnullable*</span></span>
- <span data-ttu-id="b8c22-162">Un type de référence Nullable `C?` est *Nullable* (mais un avertissement peut être généré dans un contexte d’annotation *désactivé* )</span><span class="sxs-lookup"><span data-stu-id="b8c22-162">A nullable reference type `C?` is *nullable* (but a warning may be yielded in a *disabled* annotation context)</span></span>

<span data-ttu-id="b8c22-163">Les paramètres de type prennent également en compte les contraintes :</span><span class="sxs-lookup"><span data-stu-id="b8c22-163">Type parameters additionally take their constraints into account:</span></span>

- <span data-ttu-id="b8c22-164">Un paramètre de type `T` où toutes les contraintes (le cas échéant) sont des types qui génèrent des valeurs null (*Nullable* et *Unknown*) ou la `class?` contrainte est *inconnue* .</span><span class="sxs-lookup"><span data-stu-id="b8c22-164">A type parameter `T` where all constraints (if any) are either null-yielding types (*nullable* and *unknown*) or the `class?` constraint is *unknown*</span></span>
- <span data-ttu-id="b8c22-165">Un paramètre de type `T` dans lequel au moins une contrainte est *oublie* ou non *null* , ou l’une `struct` des `class` contraintes ou est</span><span class="sxs-lookup"><span data-stu-id="b8c22-165">A type parameter `T` where at least one constraint is either *oblivious* or *nonnullable* or one of the `struct` or `class` constraints is</span></span>
    - <span data-ttu-id="b8c22-166">*oublie* dans un contexte d’annotation *désactivé*</span><span class="sxs-lookup"><span data-stu-id="b8c22-166">*oblivious* in a *disabled* annotation context</span></span>
    - <span data-ttu-id="b8c22-167">non *null* dans un contexte d’annotation *activé*</span><span class="sxs-lookup"><span data-stu-id="b8c22-167">*nonnullable* in an *enabled* annotation context</span></span>
- <span data-ttu-id="b8c22-168">Un paramètre de type Nullable `T?` dans lequel au moins l’une des `T` contraintes de est *oublie* ou non *null* , ou l’une des `struct` `class` contraintes ou est</span><span class="sxs-lookup"><span data-stu-id="b8c22-168">A nullable type parameter `T?` where at least one of `T`'s constraints is *oblivious* or *nonnullable* or one of the `struct` or `class` constraints, is</span></span>
    - <span data-ttu-id="b8c22-169">*Nullable* dans un contexte d’annotation *désactivé* (mais un avertissement est généré)</span><span class="sxs-lookup"><span data-stu-id="b8c22-169">*nullable* in a *disabled* annotation context (but a warning is yielded)</span></span>
    - <span data-ttu-id="b8c22-170">*Nullable* dans un contexte d’annotation *activé*</span><span class="sxs-lookup"><span data-stu-id="b8c22-170">*nullable* in an *enabled* annotation context</span></span>

<span data-ttu-id="b8c22-171">Pour un paramètre de type `T` , `T?` est autorisé uniquement si `T` est connu comme étant un type valeur ou s’il est connu comme étant un type référence.</span><span class="sxs-lookup"><span data-stu-id="b8c22-171">For a type parameter `T`, `T?` is only allowed if `T` is known to be a value type or known to be a reference type.</span></span>

### <a name="oblivious-vs-nonnullable"></a><span data-ttu-id="b8c22-172">Oublie et non null</span><span class="sxs-lookup"><span data-stu-id="b8c22-172">Oblivious vs nonnullable</span></span>

<span data-ttu-id="b8c22-173">Un `type` est considéré comme ayant lieu dans un contexte d’annotation donné lorsque le dernier jeton du type est dans ce contexte.</span><span class="sxs-lookup"><span data-stu-id="b8c22-173">A `type` is deemed to occur in a given annotation context when the last token of the type is within that context.</span></span>

<span data-ttu-id="b8c22-174">Si un type de référence donné `C` dans le code source est interprété comme oublie ou non null, dépend du contexte d’annotation de ce code source.</span><span class="sxs-lookup"><span data-stu-id="b8c22-174">Whether a given reference type `C` in source code is interpreted as oblivious or nonnullable depends on the annotation context of that source code.</span></span> <span data-ttu-id="b8c22-175">Mais une fois qu’elle est établie, elle est considérée comme faisant partie de ce type et « suit », par exemple, lors de la substitution des arguments de type générique.</span><span class="sxs-lookup"><span data-stu-id="b8c22-175">But once established, it is considered part of that type, and "travels with it" e.g. during substitution of generic type arguments.</span></span> <span data-ttu-id="b8c22-176">C’est comme s’il s’agissait d’une annotation telle que `?` sur le type, mais invisible.</span><span class="sxs-lookup"><span data-stu-id="b8c22-176">It is as if there is an annotation like `?` on the type, but invisible.</span></span>

## <a name="constraints"></a><span data-ttu-id="b8c22-177">Contraintes</span><span class="sxs-lookup"><span data-stu-id="b8c22-177">Constraints</span></span>

<span data-ttu-id="b8c22-178">Les types de référence Nullable peuvent être utilisés en tant que contraintes génériques.</span><span class="sxs-lookup"><span data-stu-id="b8c22-178">Nullable reference types can be used as generic constraints.</span></span> <span data-ttu-id="b8c22-179">`object`En outre, est désormais valide en tant que contrainte explicite.</span><span class="sxs-lookup"><span data-stu-id="b8c22-179">Furthermore `object` is now valid as an explicit constraint.</span></span> <span data-ttu-id="b8c22-180">L’absence d’une contrainte est maintenant équivalente à une `object?` contrainte (au lieu de `object` ), mais (contrairement `object` à Before) `object?` n’est pas interdite en tant que contrainte explicite.</span><span class="sxs-lookup"><span data-stu-id="b8c22-180">Absence of a constraint is now equivalent to an `object?` constraint (instead of `object`), but (unlike `object` before) `object?` is not prohibited as an explicit constraint.</span></span>

<span data-ttu-id="b8c22-181">`class?`est une nouvelle contrainte qui signale « peut-être un type de référence Nullable », alors que `class` indique « type référence non null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-181">`class?` is a new constraint denoting "possibly nullable reference type", whereas `class` denotes "nonnullable reference type".</span></span>

<span data-ttu-id="b8c22-182">La possibilité de valeur null d’un argument de type ou d’une contrainte n’a pas d’incidence sur la satisfaction de la contrainte par le type, sauf si ce n’est pas le cas aujourd’hui (les types valeur Nullable ne répondent pas à la `struct` contrainte).</span><span class="sxs-lookup"><span data-stu-id="b8c22-182">The nullability of a type argument or of a constraint does not impact whether the type satisfies the constraint, except where that is already the case today (nullable value types do not satisfy the `struct` constraint).</span></span> <span data-ttu-id="b8c22-183">Toutefois, si l’argument de type ne satisfait pas aux exigences de possibilité de valeur null de la contrainte, un avertissement peut être fourni.</span><span class="sxs-lookup"><span data-stu-id="b8c22-183">However, if the type argument does not satisfy the nullability requirements of the constraint, a warning may be given.</span></span>

## <a name="null-state-and-null-tracking"></a><span data-ttu-id="b8c22-184">État null et suivi null</span><span class="sxs-lookup"><span data-stu-id="b8c22-184">Null state and null tracking</span></span>

<span data-ttu-id="b8c22-185">Chaque expression d’un emplacement source donné a un *État null*, ce qui indique s’il est supposé avoir une valeur null.</span><span class="sxs-lookup"><span data-stu-id="b8c22-185">Every expression in a given source location has a *null state*, which indicated whether it is believed to potentially evaluate to null.</span></span> <span data-ttu-id="b8c22-186">L’État null est « not null » ou « peut-être null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-186">The null state is either "not null" or "maybe null".</span></span> <span data-ttu-id="b8c22-187">L’État null est utilisé pour déterminer si un avertissement doit être fourni à propos des conversions et déréférencements non sécurisés par des valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="b8c22-187">The null state is used to determine whether a warning should be given about null-unsafe conversions and dereferences.</span></span>

### <a name="null-tracking-for-variables"></a><span data-ttu-id="b8c22-188">Suivi de valeurs NULL pour les variables</span><span class="sxs-lookup"><span data-stu-id="b8c22-188">Null tracking for variables</span></span>

<span data-ttu-id="b8c22-189">Pour certaines expressions qui désignent des variables ou des propriétés, l’État null est suivi entre les occurrences, en fonction des assignations à celles-ci, les tests effectués sur ceux-ci et le workflow de contrôle entre eux.</span><span class="sxs-lookup"><span data-stu-id="b8c22-189">For certain expressions denoting variables or properties, the null state is tracked between occurrences, based on assignments to them, tests performed on them and the control flow between them.</span></span> <span data-ttu-id="b8c22-190">Cela est similaire à la façon dont l’assignation définie est suivie pour les variables.</span><span class="sxs-lookup"><span data-stu-id="b8c22-190">This is similar to how definite assignment is tracked for variables.</span></span> <span data-ttu-id="b8c22-191">Les expressions suivies sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8c22-191">The tracked expressions are the ones of the following form:</span></span>

```antlr
tracked_expression
    : simple_name
    | this
    | base
    | tracked_expression '.' identifier
    ;
```

<span data-ttu-id="b8c22-192">Où les identificateurs désignent des champs ou des propriétés.</span><span class="sxs-lookup"><span data-stu-id="b8c22-192">Where the identifiers denote fields or properties.</span></span>

<span data-ttu-id="b8c22-193">***Décrire les transitions d’État null similaires à l’assignation définie***</span><span class="sxs-lookup"><span data-stu-id="b8c22-193">***Describe null state transitions similar to definite assignment***</span></span>

### <a name="null-state-for-expressions"></a><span data-ttu-id="b8c22-194">État null pour les expressions</span><span class="sxs-lookup"><span data-stu-id="b8c22-194">Null state for expressions</span></span>

<span data-ttu-id="b8c22-195">L’État null d’une expression est dérivé de son formulaire et de son type, et de l’État null des variables qui y sont impliquées.</span><span class="sxs-lookup"><span data-stu-id="b8c22-195">The null state of an expression is derived from its form and type, and from the null state of variables involved in it.</span></span>

### <a name="literals"></a><span data-ttu-id="b8c22-196">Littéraux</span><span class="sxs-lookup"><span data-stu-id="b8c22-196">Literals</span></span>

<span data-ttu-id="b8c22-197">L’État null d’un `null` littéral est « peut-être null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-197">The null state of a `null` literal is "maybe null".</span></span> <span data-ttu-id="b8c22-198">L’État null d’un `default` littéral qui est converti en un type qui est connu comme n’étant pas un type valeur non null est « peut-être null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-198">The null state of a `default` literal that is being converted to a type that is known not to be a nonnullable value type is "maybe null".</span></span> <span data-ttu-id="b8c22-199">L’État null de tout autre littéral est « not null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-199">The null state of any other literal is "not null".</span></span>

### <a name="simple-names"></a><span data-ttu-id="b8c22-200">Noms simples</span><span class="sxs-lookup"><span data-stu-id="b8c22-200">Simple names</span></span>

<span data-ttu-id="b8c22-201">Si un `simple_name` n’est pas classé comme une valeur, son état null est « not null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-201">If a `simple_name` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="b8c22-202">Dans le cas contraire, il s’agit d’une expression suivie, et son état null est son état de suivi null à cet emplacement source.</span><span class="sxs-lookup"><span data-stu-id="b8c22-202">Otherwise it is a tracked expression, and its null state is its tracked null state at this source location.</span></span>

### <a name="member-access"></a><span data-ttu-id="b8c22-203">Accès au membre</span><span class="sxs-lookup"><span data-stu-id="b8c22-203">Member access</span></span>

<span data-ttu-id="b8c22-204">Si un `member_access` n’est pas classé comme une valeur, son état null est « not null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-204">If a `member_access` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="b8c22-205">Sinon, s’il s’agit d’une expression suivie, son état null est son état de suivi null à cet emplacement source.</span><span class="sxs-lookup"><span data-stu-id="b8c22-205">Otherwise, if it is a tracked expression, its null state is its tracked null state at this source location.</span></span> <span data-ttu-id="b8c22-206">Dans le cas contraire, son état null est l’État NULL par défaut de son type.</span><span class="sxs-lookup"><span data-stu-id="b8c22-206">Otherwise its null state is the default null state of its type.</span></span>

### <a name="invocation-expressions"></a><span data-ttu-id="b8c22-207">Appel d’expressions</span><span class="sxs-lookup"><span data-stu-id="b8c22-207">Invocation expressions</span></span>

<span data-ttu-id="b8c22-208">Si un `invocation_expression` appelle un membre qui est déclaré avec un ou plusieurs attributs pour un comportement spécial Null, l’État null est déterminé par ces attributs.</span><span class="sxs-lookup"><span data-stu-id="b8c22-208">If an `invocation_expression` invokes a member that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="b8c22-209">Dans le cas contraire, l’État null de l’expression est l’État NULL par défaut de son type.</span><span class="sxs-lookup"><span data-stu-id="b8c22-209">Otherwise the null state of the expression is the default null state of its type.</span></span>

### <a name="element-access"></a><span data-ttu-id="b8c22-210">Accès aux éléments</span><span class="sxs-lookup"><span data-stu-id="b8c22-210">Element access</span></span>

<span data-ttu-id="b8c22-211">Si un `element_access` appelle un indexeur qui est déclaré avec un ou plusieurs attributs pour un comportement spécial Null, l’État null est déterminé par ces attributs.</span><span class="sxs-lookup"><span data-stu-id="b8c22-211">If an `element_access` invokes an indexer that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="b8c22-212">Dans le cas contraire, l’État null de l’expression est l’État NULL par défaut de son type.</span><span class="sxs-lookup"><span data-stu-id="b8c22-212">Otherwise the null state of the expression is the default null state of its type.</span></span>

### <a name="base-access"></a><span data-ttu-id="b8c22-213">Accès de base</span><span class="sxs-lookup"><span data-stu-id="b8c22-213">Base access</span></span>

<span data-ttu-id="b8c22-214">Si `B` indique le type de base du type englobant, `base.I` a le même État null que `((B)this).I` et `base[E]` a le même État null que `((B)this)[E]` .</span><span class="sxs-lookup"><span data-stu-id="b8c22-214">If `B` denotes the base type of the enclosing type, `base.I` has the same null state as `((B)this).I` and `base[E]` has the same null state as `((B)this)[E]`.</span></span>

### <a name="default-expressions"></a><span data-ttu-id="b8c22-215">Expressions par défaut</span><span class="sxs-lookup"><span data-stu-id="b8c22-215">Default expressions</span></span>

<span data-ttu-id="b8c22-216">`default(T)`a l’État null « non null » si `T` est connu pour être un type valeur non null.</span><span class="sxs-lookup"><span data-stu-id="b8c22-216">`default(T)` has the null state "non-null" if `T` is known to be a nonnullable value type.</span></span> <span data-ttu-id="b8c22-217">Dans le cas contraire, il a l’État NULL « peut-être null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-217">Otherwise it has the null state "maybe null".</span></span>

### <a name="null-conditional-expressions"></a><span data-ttu-id="b8c22-218">Expressions conditionnelles null</span><span class="sxs-lookup"><span data-stu-id="b8c22-218">Null-conditional expressions</span></span>

<span data-ttu-id="b8c22-219">Un `null_conditional_expression` a l’État NULL « peut-être null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-219">A `null_conditional_expression` has the null state "maybe null".</span></span>

### <a name="cast-expressions"></a><span data-ttu-id="b8c22-220">Expressions cast</span><span class="sxs-lookup"><span data-stu-id="b8c22-220">Cast expressions</span></span>

<span data-ttu-id="b8c22-221">Si une expression de Cast `(T)E` appelle une conversion définie par l’utilisateur, l’État null de l’expression est l’État NULL par défaut pour son type.</span><span class="sxs-lookup"><span data-stu-id="b8c22-221">If a cast expression `(T)E` invokes a user-defined conversion, then the null state of the expression is the default null state for its type.</span></span> <span data-ttu-id="b8c22-222">Sinon, si `T` a un rendement null (*Nullable* ou *inconnu*), l’État null est « peut-être null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-222">Otherwise, if `T` is null-yielding (*nullable* or *unknown*) then the null state is "maybe null".</span></span> <span data-ttu-id="b8c22-223">Sinon, l’État null est le même que l’État null de `E` .</span><span class="sxs-lookup"><span data-stu-id="b8c22-223">Otherwise the null state is the same as the null state of `E`.</span></span>

### <a name="await-expressions"></a><span data-ttu-id="b8c22-224">Expressions await</span><span class="sxs-lookup"><span data-stu-id="b8c22-224">Await expressions</span></span>

<span data-ttu-id="b8c22-225">L’État null de `await E` est l’État NULL par défaut de son type.</span><span class="sxs-lookup"><span data-stu-id="b8c22-225">The null state of `await E` is the default null state of its type.</span></span>

### <a name="the-as-operator"></a><span data-ttu-id="b8c22-226">`as`Opérateur</span><span class="sxs-lookup"><span data-stu-id="b8c22-226">The `as` operator</span></span>

<span data-ttu-id="b8c22-227">Une `as` expression a l’État NULL « peut-être null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-227">An `as` expression has the null state "maybe null".</span></span>

### <a name="the-null-coalescing-operator"></a><span data-ttu-id="b8c22-228">Opérateur de fusion Null</span><span class="sxs-lookup"><span data-stu-id="b8c22-228">The null-coalescing operator</span></span>

<span data-ttu-id="b8c22-229">`E1 ?? E2`a le même État null que`E2`</span><span class="sxs-lookup"><span data-stu-id="b8c22-229">`E1 ?? E2` has the same null state as `E2`</span></span>

### <a name="the-conditional-operator"></a><span data-ttu-id="b8c22-230">l'opérateur conditionnel ;</span><span class="sxs-lookup"><span data-stu-id="b8c22-230">The conditional operator</span></span>

<span data-ttu-id="b8c22-231">L’État null de `E1 ? E2 : E3` est « not null » si l’État null de `E2` et est `E3` « not null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-231">The null state of `E1 ? E2 : E3` is "not null" if the null state of both `E2` and `E3` are "not null".</span></span> <span data-ttu-id="b8c22-232">Dans le cas contraire, il s’agit de « peut-être null ».</span><span class="sxs-lookup"><span data-stu-id="b8c22-232">Otherwise it is "maybe null".</span></span>

### <a name="query-expressions"></a><span data-ttu-id="b8c22-233">Expressions de requête</span><span class="sxs-lookup"><span data-stu-id="b8c22-233">Query expressions</span></span>

<span data-ttu-id="b8c22-234">L’État null d’une expression de requête est l’État NULL par défaut de son type.</span><span class="sxs-lookup"><span data-stu-id="b8c22-234">The null state of a query expression is the default null state of its type.</span></span>

### <a name="assignment-operators"></a><span data-ttu-id="b8c22-235">Opérateurs d'assignation</span><span class="sxs-lookup"><span data-stu-id="b8c22-235">Assignment operators</span></span>

<span data-ttu-id="b8c22-236">`E1 = E2`et `E1 op= E2` ont le même État null qu' `E2` après l’application de toutes les conversions implicites.</span><span class="sxs-lookup"><span data-stu-id="b8c22-236">`E1 = E2` and `E1 op= E2` have the same null state as `E2` after any implicit conversions have been applied.</span></span>

### <a name="unary-and-binary-operators"></a><span data-ttu-id="b8c22-237">Opérateurs unaires et binaires</span><span class="sxs-lookup"><span data-stu-id="b8c22-237">Unary and binary operators</span></span>

<span data-ttu-id="b8c22-238">Si un opérateur unaire ou binaire appelle un opérateur défini par l’utilisateur qui est déclaré avec un ou plusieurs attributs pour un comportement spécial Null, l’État null est déterminé par ces attributs.</span><span class="sxs-lookup"><span data-stu-id="b8c22-238">If a unary or binary operator invokes an user-defined operator that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="b8c22-239">Dans le cas contraire, l’État null de l’expression est l’État NULL par défaut de son type.</span><span class="sxs-lookup"><span data-stu-id="b8c22-239">Otherwise the null state of the expression is the default null state of its type.</span></span>

<span data-ttu-id="b8c22-240">***Quoi faire pour les chaînes binaires `+` et les délégués ?***</span><span class="sxs-lookup"><span data-stu-id="b8c22-240">***Something special to do for binary `+` over strings and delegates?***</span></span>

### <a name="expressions-that-propagate-null-state"></a><span data-ttu-id="b8c22-241">Expressions qui propagent l’État null</span><span class="sxs-lookup"><span data-stu-id="b8c22-241">Expressions that propagate null state</span></span>

<span data-ttu-id="b8c22-242">`(E)`, `checked(E)` et `unchecked(E)` ont tous le même État null que `E` .</span><span class="sxs-lookup"><span data-stu-id="b8c22-242">`(E)`, `checked(E)` and `unchecked(E)` all have the same null state as `E`.</span></span>

### <a name="expressions-that-are-never-null"></a><span data-ttu-id="b8c22-243">Expressions qui ne sont jamais null</span><span class="sxs-lookup"><span data-stu-id="b8c22-243">Expressions that are never null</span></span>

<span data-ttu-id="b8c22-244">L’État null des formulaires d’expression suivants est toujours « not null » :</span><span class="sxs-lookup"><span data-stu-id="b8c22-244">The null state of the following expression forms is always "not null":</span></span>

- <span data-ttu-id="b8c22-245">`this` Accès Transact-SQL</span><span class="sxs-lookup"><span data-stu-id="b8c22-245">`this` access</span></span>
- <span data-ttu-id="b8c22-246">chaînes interpolées</span><span class="sxs-lookup"><span data-stu-id="b8c22-246">interpolated strings</span></span>
- <span data-ttu-id="b8c22-247">`new`expressions (objets, délégués, objets anonymes et expressions de création de tableau)</span><span class="sxs-lookup"><span data-stu-id="b8c22-247">`new` expressions (object, delegate, anonymous object and array creation expressions)</span></span>
- <span data-ttu-id="b8c22-248">Expressions `typeof`</span><span class="sxs-lookup"><span data-stu-id="b8c22-248">`typeof` expressions</span></span>
- <span data-ttu-id="b8c22-249">Expressions `nameof`</span><span class="sxs-lookup"><span data-stu-id="b8c22-249">`nameof` expressions</span></span>
- <span data-ttu-id="b8c22-250">fonctions anonymes (méthodes anonymes et expressions lambda)</span><span class="sxs-lookup"><span data-stu-id="b8c22-250">anonymous functions (anonymous methods and lambda expressions)</span></span>
- <span data-ttu-id="b8c22-251">expressions null-indulgent avec</span><span class="sxs-lookup"><span data-stu-id="b8c22-251">null-forgiving expressions</span></span>
- <span data-ttu-id="b8c22-252">Expressions `is`</span><span class="sxs-lookup"><span data-stu-id="b8c22-252">`is` expressions</span></span>

## <a name="type-inference"></a><span data-ttu-id="b8c22-253">Inférence de type</span><span class="sxs-lookup"><span data-stu-id="b8c22-253">Type inference</span></span>

### <a name="type-inference-for-var"></a><span data-ttu-id="b8c22-254">Inférence de type pour`var`</span><span class="sxs-lookup"><span data-stu-id="b8c22-254">Type inference for `var`</span></span>

<span data-ttu-id="b8c22-255">Le type déduit pour les variables locales déclarées avec `var` est informé par l’État null de l’expression d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="b8c22-255">The type inferred for local variables declared with `var` is informed by the null state of the initializing expression.</span></span>

```csharp
var x = E;
```

<span data-ttu-id="b8c22-256">Si le type de `E` est un type référence Nullable `C?` et si l’État null de `E` est « not null », le type déduit pour `x` est `C` .</span><span class="sxs-lookup"><span data-stu-id="b8c22-256">If the type of `E` is a nullable reference type `C?` and the null state of `E` is "not null" then the type inferred for `x` is `C`.</span></span> <span data-ttu-id="b8c22-257">Dans le cas contraire, le type déduit est le type de `E` .</span><span class="sxs-lookup"><span data-stu-id="b8c22-257">Otherwise, the inferred type is the type of `E`.</span></span>

<span data-ttu-id="b8c22-258">La possibilité de valeur null du type inféré pour `x` est déterminée comme décrit ci-dessus, en fonction du contexte d’annotation du `var` , comme si le type avait été donné explicitement à cette position.</span><span class="sxs-lookup"><span data-stu-id="b8c22-258">The nullability of the type inferred for `x` is determined as described above, based on the annotation context of the `var`, just as if the type had been given explicitly in that position.</span></span>

### <a name="type-inference-for-var"></a><span data-ttu-id="b8c22-259">Inférence de type pour`var?`</span><span class="sxs-lookup"><span data-stu-id="b8c22-259">Type inference for `var?`</span></span>

<span data-ttu-id="b8c22-260">Le type déduit pour les variables locales déclarées avec `var?` est indépendant de l’État null de l’expression d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="b8c22-260">The type inferred for local variables declared with `var?` is independent of the null state of the initializing expression.</span></span>

```csharp
var? x = E;
```

<span data-ttu-id="b8c22-261">Si le type `T` de `E` est un type valeur Nullable ou un type référence Nullable, le type déduit pour `x` est `T` .</span><span class="sxs-lookup"><span data-stu-id="b8c22-261">If the type `T` of `E` is a nullable value type or a nullable reference type then the type inferred for `x` is `T`.</span></span> <span data-ttu-id="b8c22-262">Sinon, si `T` est un type valeur non null, `S` le type déduit est `S?` .</span><span class="sxs-lookup"><span data-stu-id="b8c22-262">Otherwise, if `T` is a nonnullable value type `S` the type inferred is `S?`.</span></span> <span data-ttu-id="b8c22-263">Sinon, si `T` est un type référence qui n’est pas null, `C` le type déduit est `C?` .</span><span class="sxs-lookup"><span data-stu-id="b8c22-263">Otherwise, if `T` is a nonnullable reference type `C` the type inferred is `C?`.</span></span> <span data-ttu-id="b8c22-264">Dans le cas contraire, la déclaration est illégale.</span><span class="sxs-lookup"><span data-stu-id="b8c22-264">Otherwise, the declaration is illegal.</span></span>

<span data-ttu-id="b8c22-265">La possibilité de valeur null du type inféré pour `x` est toujours *Nullable*.</span><span class="sxs-lookup"><span data-stu-id="b8c22-265">The nullability of the type inferred for `x` is always *nullable*.</span></span>

### <a name="generic-type-inference"></a><span data-ttu-id="b8c22-266">Inférence de type générique</span><span class="sxs-lookup"><span data-stu-id="b8c22-266">Generic type inference</span></span>

<span data-ttu-id="b8c22-267">L’inférence de type générique est améliorée pour déterminer si les types référence déduits doivent avoir la valeur null ou non.</span><span class="sxs-lookup"><span data-stu-id="b8c22-267">Generic type inference is enhanced to help decide whether inferred reference types should be nullable or not.</span></span> <span data-ttu-id="b8c22-268">Il s’agit d’un meilleur effort, qui ne génère pas d’avertissements, mais peut entraîner des avertissements Nullable lorsque les types inférés de la surcharge sélectionnée sont appliqués aux arguments.</span><span class="sxs-lookup"><span data-stu-id="b8c22-268">This is a best effort, and does not in and of itself yield warnings, but may lead to nullable warnings when the inferred types of the selected overload are applied to the arguments.</span></span>

<span data-ttu-id="b8c22-269">L’inférence de type ne repose pas sur le contexte d’annotation des types entrants.</span><span class="sxs-lookup"><span data-stu-id="b8c22-269">The type inference does not rely on the annotation context of incoming types.</span></span> <span data-ttu-id="b8c22-270">Au lieu de cela, un `type` est déduit, ce qui acquiert son propre contexte d’annotation là où il a été exprimé de manière explicite.</span><span class="sxs-lookup"><span data-stu-id="b8c22-270">Instead a `type` is inferred which acquires its own annotation context from where it "would have been" if it had been expressed explicitly.</span></span> <span data-ttu-id="b8c22-271">Cela souligne le rôle de l’inférence de type pour vous aider à écrire vous-même.</span><span class="sxs-lookup"><span data-stu-id="b8c22-271">This underscores the role of type inference as a convenience for what you could have written yourself.</span></span>

<span data-ttu-id="b8c22-272">Plus précisément, le contexte d’annotation pour un argument de type inféré est le contexte du jeton qui aurait été suivi par la `<...>` liste de paramètres de type, avait-il eu un ; autrement dit, le nom de la méthode générique appelée.</span><span class="sxs-lookup"><span data-stu-id="b8c22-272">More precisely, the annotation context for an inferred type argument is the context of the token that would have been followed by the `<...>` type parameter list, had there been one; i.e. the name of the generic method being called.</span></span> <span data-ttu-id="b8c22-273">Pour les expressions de requête qui traduisent ces appels, le contexte est extrait du mot clé contextuel initial de la clause de requête à partir de laquelle l’appel est généré.</span><span class="sxs-lookup"><span data-stu-id="b8c22-273">For query expressions that translate to such calls, the context is taken from the initial contextual keyword of the query clause from which the call is generated.</span></span>

### <a name="the-first-phase"></a><span data-ttu-id="b8c22-274">La première phase</span><span class="sxs-lookup"><span data-stu-id="b8c22-274">The first phase</span></span>

<span data-ttu-id="b8c22-275">Les types référence Nullable circulent dans les limites des expressions initiales, comme décrit ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b8c22-275">Nullable reference types flow into the bounds from the initial expressions, as described below.</span></span> <span data-ttu-id="b8c22-276">En outre, deux nouveaux types de limites, à savoir `null` et `default` sont introduits.</span><span class="sxs-lookup"><span data-stu-id="b8c22-276">In addition, two new kinds of bounds, namely `null` and `default` are introduced.</span></span> <span data-ttu-id="b8c22-277">Leur but est de traverser des occurrences de `null` ou `default` dans les expressions d’entrée, ce qui peut entraîner la création d’une valeur null dans un type inféré, même si ce n’est pas le cas.</span><span class="sxs-lookup"><span data-stu-id="b8c22-277">Their purpose is to carry through occurrences of `null` or `default` in the input expressions, which may cause an inferred type to be nullable, even when it otherwise wouldn't.</span></span> <span data-ttu-id="b8c22-278">Cela fonctionne même pour les types *valeur* Nullable, qui sont améliorés pour récupérer la « nullité » dans le processus d’inférence.</span><span class="sxs-lookup"><span data-stu-id="b8c22-278">This works even for nullable *value* types, which are enhanced to pick up "nullness" in the inference process.</span></span>

<span data-ttu-id="b8c22-279">La détermination des limites à ajouter dans la première phase est améliorée comme suit :</span><span class="sxs-lookup"><span data-stu-id="b8c22-279">The determination of what bounds to add in the first phase are enhanced as follows:</span></span>

<span data-ttu-id="b8c22-280">Si un argument `Ei` a un type référence, le type `U` utilisé pour l’inférence dépend de l’État null de, ainsi `Ei` que de son type déclaré :</span><span class="sxs-lookup"><span data-stu-id="b8c22-280">If an argument `Ei` has a reference type, the type `U` used for inference depends on the null state of `Ei` as well as its declared type:</span></span>
- <span data-ttu-id="b8c22-281">Si le type déclaré est un type référence non null `U0` ou un type référence Nullable `U0?` , alors</span><span class="sxs-lookup"><span data-stu-id="b8c22-281">If the declared type is a nonnullable reference type `U0` or a nullable reference type `U0?` then</span></span>
    - <span data-ttu-id="b8c22-282">Si l’État null de `Ei` est « not null », alors `U` est`U0`</span><span class="sxs-lookup"><span data-stu-id="b8c22-282">if the null state of `Ei` is "not null" then `U` is `U0`</span></span>
    - <span data-ttu-id="b8c22-283">Si l’État null de `Ei` est « peut-être null », alors `U` est`U0?`</span><span class="sxs-lookup"><span data-stu-id="b8c22-283">if the null state of `Ei` is "maybe null" then `U` is `U0?`</span></span>
- <span data-ttu-id="b8c22-284">Sinon `Ei` , si a un type déclaré, `U` est ce type</span><span class="sxs-lookup"><span data-stu-id="b8c22-284">Otherwise if `Ei` has a declared type, `U` is that type</span></span>
- <span data-ttu-id="b8c22-285">Sinon, si `Ei` est `null` ensuite `U` la limite spéciale`null`</span><span class="sxs-lookup"><span data-stu-id="b8c22-285">Otherwise if `Ei` is `null` then `U` is the special bound `null`</span></span>
- <span data-ttu-id="b8c22-286">Sinon, si `Ei` est `default` ensuite `U` la limite spéciale`default`</span><span class="sxs-lookup"><span data-stu-id="b8c22-286">Otherwise if `Ei` is `default` then `U` is the special bound `default`</span></span>
- <span data-ttu-id="b8c22-287">Sinon, aucune inférence n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b8c22-287">Otherwise no inference is made.</span></span>

### <a name="exact-upper-bound-and-lower-bound-inferences"></a><span data-ttu-id="b8c22-288">Inférences exactes, limites supérieures et limites inférieures</span><span class="sxs-lookup"><span data-stu-id="b8c22-288">Exact, upper-bound and lower-bound inferences</span></span>

<span data-ttu-id="b8c22-289">Dans les inférences *du* type `U` *vers* le type `V` , si `V` est un type référence Nullable `V0?` , `V0` est utilisé à la place de `V` dans les clauses suivantes.</span><span class="sxs-lookup"><span data-stu-id="b8c22-289">In inferences *from* the type `U` *to* the type `V`, if `V` is a nullable reference type `V0?`, then `V0` is used instead of `V` in the following clauses.</span></span>
- <span data-ttu-id="b8c22-290">Si `V` est l’une des variables de type non fixed, `U` est ajouté comme limite exacte, supérieure ou inférieure comme avant</span><span class="sxs-lookup"><span data-stu-id="b8c22-290">If `V` is one of the unfixed type variables, `U` is added as an exact, upper or lower bound as before</span></span>
- <span data-ttu-id="b8c22-291">Sinon, si `U` est `null` ou `default` , aucune inférence n’est effectuée</span><span class="sxs-lookup"><span data-stu-id="b8c22-291">Otherwise, if `U` is `null` or `default`, no inference is made</span></span>
- <span data-ttu-id="b8c22-292">Sinon, si `U` est un type référence Nullable `U0?` , `U0` est utilisé `U` à la place de dans les clauses suivantes.</span><span class="sxs-lookup"><span data-stu-id="b8c22-292">Otherwise, if `U` is a nullable reference type `U0?`, then `U0` is used instead of `U` in the subsequent clauses.</span></span>

<span data-ttu-id="b8c22-293">L’essence est que la possibilité de valeur null liée directement à l’une des variables de type non Fixed est conservée dans ses limites.</span><span class="sxs-lookup"><span data-stu-id="b8c22-293">The essence is that nullability that pertains directly to one of the unfixed type variables is preserved into its bounds.</span></span> <span data-ttu-id="b8c22-294">Pour les inférences qui parcourent davantage les types source et cible, en revanche, la possibilité de valeur null est ignorée.</span><span class="sxs-lookup"><span data-stu-id="b8c22-294">For the inferences that recurse further into the source and target types, on the other hand, nullability is ignored.</span></span> <span data-ttu-id="b8c22-295">Il est possible qu’il ne corresponde pas, mais si ce n’est pas le cas, un avertissement est émis ultérieurement si la surcharge est choisie et appliquée.</span><span class="sxs-lookup"><span data-stu-id="b8c22-295">It may or may not match, but if it doesn't, a warning will be issued later if the overload is chosen and applied.</span></span>

### <a name="fixing"></a><span data-ttu-id="b8c22-296">Résolution </span><span class="sxs-lookup"><span data-stu-id="b8c22-296">Fixing</span></span>

<span data-ttu-id="b8c22-297">Actuellement, la spécification ne permet pas de décrire ce qui se produit lorsque plusieurs limites sont convertibles de l’identité les unes des autres, mais sont différentes.</span><span class="sxs-lookup"><span data-stu-id="b8c22-297">The spec currently does not do a good job of describing what happens when multiple bounds are identity convertible to each other, but are different.</span></span> <span data-ttu-id="b8c22-298">Cela peut se produire entre `object` et `dynamic` , entre les types tuple qui diffèrent uniquement dans les noms d’éléments, entre les types construits et maintenant entre `C` et `C?` pour les types référence.</span><span class="sxs-lookup"><span data-stu-id="b8c22-298">This may happen between `object` and `dynamic`, between tuple types that differ only in element names, between types constructed thereof and now also between `C` and `C?` for reference types.</span></span>

<span data-ttu-id="b8c22-299">En outre, nous devons propager la « valeur null » des expressions d’entrée vers le type de résultat.</span><span class="sxs-lookup"><span data-stu-id="b8c22-299">In addition we need to propagate "nullness" from the input expressions to the result type.</span></span> 

<span data-ttu-id="b8c22-300">Pour gérer ces éléments, nous allons ajouter des phases à la résolution, ce qui est maintenant :</span><span class="sxs-lookup"><span data-stu-id="b8c22-300">To handle these we add more phases to fixing, which is now:</span></span>

1. <span data-ttu-id="b8c22-301">Rassemblez tous les types dans toutes les limites en tant que candidats, en supprimant `?` de tous les types de référence Nullable</span><span class="sxs-lookup"><span data-stu-id="b8c22-301">Gather all the types in all the bounds as candidates, removing `?` from all that are nullable reference types</span></span>
2. <span data-ttu-id="b8c22-302">Éliminer les candidats en fonction des exigences relatives aux limites exactes, inférieures et supérieures (maintien `null` et `default` limites)</span><span class="sxs-lookup"><span data-stu-id="b8c22-302">Eliminate candidates based on requirements of exact, lower and upper bounds (keeping `null` and `default` bounds)</span></span>
3. <span data-ttu-id="b8c22-303">Éliminer les candidats qui n’ont pas de conversion implicite vers tous les autres candidats</span><span class="sxs-lookup"><span data-stu-id="b8c22-303">Eliminate candidates that do not have an implicit conversion to all the other candidates</span></span>
4. <span data-ttu-id="b8c22-304">Si les candidats restants n’ont pas tous une conversion d’identité entre eux, l’inférence de type échoue</span><span class="sxs-lookup"><span data-stu-id="b8c22-304">If the remaining candidates do not all have identity conversions to one another, then type inference fails</span></span>
5. <span data-ttu-id="b8c22-305">*Fusionnez* les candidats restants comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b8c22-305">*Merge* the remaining candidates as described below</span></span>
6. <span data-ttu-id="b8c22-306">Si le candidat résultant est un type référence ou un type valeur non null et que *toutes* les limites exactes ou *une* des limites inférieures sont des types valeur Nullable, des types référence Nullable ou `null` `default` , `?` est ajouté au candidat résultant, ce qui en fait un type valeur Nullable ou un type référence.</span><span class="sxs-lookup"><span data-stu-id="b8c22-306">If the resulting candidate is a reference type or a nonnullable value type and *all* of the exact bounds or *any* of the lower bounds are nullable value types, nullable reference types, `null` or `default`, then `?` is added to the resulting candidate, making it a nullable value type or reference type.</span></span>

<span data-ttu-id="b8c22-307">La *fusion* est décrite entre deux types candidats.</span><span class="sxs-lookup"><span data-stu-id="b8c22-307">*Merging* is described between two candidate types.</span></span> <span data-ttu-id="b8c22-308">Elle est transitive et transmutative, de sorte que les candidats peuvent être fusionnés dans n’importe quel ordre avec le même résultat final.</span><span class="sxs-lookup"><span data-stu-id="b8c22-308">It is transitive and commutative, so the candidates can be merged in any order with the same ultimate result.</span></span> <span data-ttu-id="b8c22-309">Elle n’est pas définie si les deux types candidats ne sont pas convertibles en identité.</span><span class="sxs-lookup"><span data-stu-id="b8c22-309">It is undefined if the two candidate types are not identity convertible to each other.</span></span>

<span data-ttu-id="b8c22-310">La fonction *Merge* accepte deux types candidats et une direction ( *+* ou *-* ) :</span><span class="sxs-lookup"><span data-stu-id="b8c22-310">The *Merge* function takes two candidate types and a direction (*+* or *-*):</span></span>

- <span data-ttu-id="b8c22-311">*Fusionner*( `T` , `T` , *d*) = T</span><span class="sxs-lookup"><span data-stu-id="b8c22-311">*Merge*(`T`, `T`, *d*) = T</span></span>
- <span data-ttu-id="b8c22-312">*Merge*( `S` , `T?` , *+* ) = *fusion*( `S?` , `T` , *+* ) = *Merge*( `S` , `T` , *+* )`?`</span><span class="sxs-lookup"><span data-stu-id="b8c22-312">*Merge*(`S`, `T?`, *+*) = *Merge*(`S?`, `T`, *+*) = *Merge*(`S`, `T`, *+*)`?`</span></span>
- <span data-ttu-id="b8c22-313">*Merge*( `S` , `T?` , *-* ) = *fusion*( `S?` , `T` , *-* ) = *Merge*( `S` , `T` , *-* )</span><span class="sxs-lookup"><span data-stu-id="b8c22-313">*Merge*(`S`, `T?`, *-*) = *Merge*(`S?`, `T`, *-*) = *Merge*(`S`, `T`, *-*)</span></span>
- <span data-ttu-id="b8c22-314">*Merge*( `C<S1,...,Sn>` , `C<T1,...,Tn>` , *+* ) = `C<` *fusion*( `S1` , `T1` *D1*) `,...,` *fusionner*( `Sn` , `Tn` , *DN*) `>` , *où*</span><span class="sxs-lookup"><span data-stu-id="b8c22-314">*Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *+*) = `C<`*Merge*(`S1`, `T1`, *d1*)`,...,`*Merge*(`Sn`, `Tn`, *dn*)`>`, *where*</span></span>
    - <span data-ttu-id="b8c22-315">`di` = *+* Si le `i` paramètre de type « TH » de `C<...>` est covariant</span><span class="sxs-lookup"><span data-stu-id="b8c22-315">`di` = *+* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="b8c22-316">`di` = *-* Si le `i` paramètre de type « TH » de `C<...>` est en contre-ou invariant</span><span class="sxs-lookup"><span data-stu-id="b8c22-316">`di` = *-* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="b8c22-317">*Merge*( `C<S1,...,Sn>` , `C<T1,...,Tn>` , *-* ) = `C<` *fusion*( `S1` , `T1` *D1*) `,...,` *fusionner*( `Sn` , `Tn` , *DN*) `>` , *où*</span><span class="sxs-lookup"><span data-stu-id="b8c22-317">*Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *-*) = `C<`*Merge*(`S1`, `T1`, *d1*)`,...,`*Merge*(`Sn`, `Tn`, *dn*)`>`, *where*</span></span>
    - <span data-ttu-id="b8c22-318">`di` = *-* Si le `i` paramètre de type « TH » de `C<...>` est covariant</span><span class="sxs-lookup"><span data-stu-id="b8c22-318">`di` = *-* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="b8c22-319">`di` = *+* Si le `i` paramètre de type « TH » de `C<...>` est en contre-ou invariant</span><span class="sxs-lookup"><span data-stu-id="b8c22-319">`di` = *+* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="b8c22-320">*Merge*( `(S1 s1,..., Sn sn)` , `(T1 t1,..., Tn tn)` , *d*) = `(` *fusion*( `S1` , `T1` , *d*) `n1,...,` *fusion*( `Sn` , `Tn` , *d*) `nn)` , *où*</span><span class="sxs-lookup"><span data-stu-id="b8c22-320">*Merge*(`(S1 s1,..., Sn sn)`, `(T1 t1,..., Tn tn)`, *d*) = `(`*Merge*(`S1`, `T1`, *d*)`n1,...,`*Merge*(`Sn`, `Tn`, *d*) `nn)`, *where*</span></span>
    - <span data-ttu-id="b8c22-321">`ni`est absent si `si` et `ti` diffère, ou si les deux sont absents</span><span class="sxs-lookup"><span data-stu-id="b8c22-321">`ni` is absent if `si` and `ti` differ, or if both are absent</span></span>
    - <span data-ttu-id="b8c22-322">`ni`est `si` si `si` et `ti` sont identiques</span><span class="sxs-lookup"><span data-stu-id="b8c22-322">`ni` is `si` if `si` and `ti` are the same</span></span>
- <span data-ttu-id="b8c22-323">*Merge*( `object` , `dynamic` ) = *fusion*( `dynamic` , `object` ) =`dynamic`</span><span class="sxs-lookup"><span data-stu-id="b8c22-323">*Merge*(`object`, `dynamic`) = *Merge*(`dynamic`, `object`) = `dynamic`</span></span>

## <a name="warnings"></a><span data-ttu-id="b8c22-324">Avertissements</span><span class="sxs-lookup"><span data-stu-id="b8c22-324">Warnings</span></span>

### <a name="potential-null-assignment"></a><span data-ttu-id="b8c22-325">Attribution de valeur null potentielle</span><span class="sxs-lookup"><span data-stu-id="b8c22-325">Potential null assignment</span></span>

### <a name="potential-null-dereference"></a><span data-ttu-id="b8c22-326">Déréférencement null potentiel</span><span class="sxs-lookup"><span data-stu-id="b8c22-326">Potential null dereference</span></span>

### <a name="constraint-nullability-mismatch"></a><span data-ttu-id="b8c22-327">Incompatibilité des valeurs null de contrainte</span><span class="sxs-lookup"><span data-stu-id="b8c22-327">Constraint nullability mismatch</span></span>

### <a name="nullable-types-in-disabled-annotation-context"></a><span data-ttu-id="b8c22-328">Types Nullable dans un contexte d’annotation désactivé</span><span class="sxs-lookup"><span data-stu-id="b8c22-328">Nullable types in disabled annotation context</span></span>

## <a name="attributes-for-special-null-behavior"></a><span data-ttu-id="b8c22-329">Attributs pour le comportement de la valeur null spéciale</span><span class="sxs-lookup"><span data-stu-id="b8c22-329">Attributes for special null behavior</span></span>


