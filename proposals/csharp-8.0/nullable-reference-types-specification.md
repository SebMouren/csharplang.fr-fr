---
ms.openlocfilehash: aecbf4298053debdb479ad3aaf6e3db468918455
ms.sourcegitcommit: 02b535d712cc9e022290e14d9e0324508b28f231
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "79485209"
---
# <a name="nullable-reference-types-specification"></a>Spécification des types référence Nullable

Il s’agit d’un travail en cours : plusieurs parties sont manquantes ou incomplètes. ***

## <a name="syntax"></a>Syntaxe

### <a name="nullable-reference-types"></a>Types références Nullables

Les types de référence Nullable ont la même syntaxe `T?` que la forme abrégée des types valeur Nullable, mais n’ont pas de forme longue correspondante.

Dans le cadre de la spécification, le `nullable_type` de production actuel est renommé en `nullable_value_type`et un `nullable_reference_type` production est ajouté :

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

Le `non_nullable_reference_type` dans un `nullable_reference_type` doit être un type de référence non Nullable (classe, interface, délégué ou tableau), ou un paramètre de type qui est imposé comme étant un type de référence non Nullable (par le biais de la contrainte `class` ou d’une classe autre que `object`).

Les types de référence Nullable ne peuvent pas se trouver aux emplacements suivants :

- comme une classe ou une interface de base
- en tant que récepteur d’un `member_access`
- en tant que `type` dans un `object_creation_expression`
- en tant que `delegate_type` dans un `delegate_creation_expression`
- en tant que `type` dans un `is_expression`, un `catch_clause` ou un `type_pattern`
- en tant que `interface` dans un nom de membre d’interface qualifié complet

Un avertissement est fourni sur un `nullable_reference_type` où le contexte d’annotation Nullable est désactivé.

### <a name="nullable-class-constraint"></a>Contrainte de classe Nullable

La contrainte de `class` a un équivalent Nullable `class?`:

```antlr
primary_constraint
    : ...
    | 'class' '?'
    ;
```

### <a name="the-null-forgiving-operator"></a>Opérateur null-indulgent avec

L’opérateur de `!` après correction est appelé opérateur null-indulgent avec.

```antlr
primary_expression
    : ...
    | null_forgiving_expression
    ;
    
null_forgiving_expression
    : primary_expression '!'
    ;
```

Le `primary_expression` doit être d’un type référence.  

L’opérateur postfixé `!` n’a aucun effet d’exécution, il prend la valeur du résultat de l’expression sous-jacente. Son seul rôle consiste à modifier l’État null de l’expression et à limiter les avertissements en fonction de son utilisation.

### <a name="nullable-implicitly-typed-local-variables"></a>variables locales implicitement typées Nullable

`var` déduit un type annoté pour les types référence.
Par exemple, dans `var s = "";` la `var` est déduite comme `string?`.

### <a name="nullable-compiler-directives"></a>Directives de compilateur Nullable

les directives `#nullable` contrôlent l’annotation Nullable et les contextes d’avertissement.

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

les directives de `#pragma warning` sont étendues pour permettre la modification du contexte d’avertissement Nullable et permettre l’activation des avertissements individuels sur même lorsqu’ils sont désactivés par défaut :

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

Notez que la nouvelle forme de `pragma_warning_body` utilise `nullable_action`, et non `warning_action`.

## <a name="nullable-contexts"></a>Contextes nullables

Chaque ligne de code source a un *contexte d’annotation Nullable* et un *contexte d’avertissement Nullable*. Ils contrôlent si les annotations Nullable ont un effet et si les avertissements de possibilité de valeur NULL sont fournis. Le contexte d’annotation d’une ligne donnée est *désactivé* ou *activé*. Le contexte d’avertissement d’une ligne donnée est *désactivé* ou *activé*.

Les deux contextes peuvent être spécifiés au niveau du projet ( C# en dehors du code source), ou n’importe où dans un fichier source via des directives de préprocesseur `#nullable`. Si aucun paramètre de niveau projet n’est fourni, la valeur par défaut est pour que les deux contextes soient *désactivés*.

La directive `#nullable` contrôle l’annotation et les contextes d’avertissement dans le texte source et a priorité sur les paramètres au niveau du projet.

Une directive définit le ou les contexte (s) qu’elle contrôle pour les lignes de code suivantes, jusqu’à ce qu’une autre directive la remplace, ou jusqu’à la fin du fichier source.

L’effet des directives est le suivant :

- `#nullable disable`: définit l’annotation Nullable et les contextes d’avertissement sur *désactivé*
- `#nullable enable`: définit l’annotation Nullable et les contextes d’avertissement sur *activé*
- `#nullable restore`: restaure l’annotation Nullable et les contextes d’avertissement dans les paramètres du projet
- `#nullable disable annotations`: définit le contexte d’annotation Nullable sur *désactivé*
- `#nullable enable annotations`: définit le contexte d’annotation Nullable sur *activé*
- `#nullable restore annotations`: restaure le contexte d’annotation Nullable aux paramètres du projet
- `#nullable disable warnings`: définit le contexte d’avertissement Nullable sur *désactivé*
- `#nullable enable warnings`: définit le contexte d’avertissement Nullable sur *activé*
- `#nullable restore warnings`: restaure le contexte d’avertissement Nullable aux paramètres du projet

## <a name="nullability-of-types"></a>Nullabilité des types

Un type donné peut avoir l’un des quatre nullabilities : *oublie*, non *null*, *Nullable* et *Unknown*. 

Les types non *null* et *inconnus* peuvent provoquer des avertissements si une valeur `null` potentielle leur est assignée. Toutefois, les types *oublie* et *Nullable* sont «*null-assignables*» et peuvent avoir des valeurs `null` qui leur sont assignées sans avertissements. 

Les types *oublie* et non *null* peuvent être déréférencés ou assignés sans avertissements. Toutefois, les valeurs de type *Nullable* et *Unknown* sont «*yield null*» et peuvent provoquer des avertissements lorsqu’ils sont déréférencés ou assignés sans vérification de valeur null correcte. 

L' *État NULL par défaut* d’un type qui génère un null est « peut-être null ». L’État NULL par défaut d’un type qui ne génère pas de valeur null est « not null ».

Le genre de type et le contexte d’annotation Nullable qu’il contient se trouvent dans déterminer sa possibilité de valeur NULL :

- Un type valeur non null `S` est toujours non *null*
- Un type valeur Nullable `S?` est toujours *Nullable*
- Un type référence non annoté `C` dans un contexte d’annotation *désactivé* est *oublie*
- Un type référence non annoté `C` dans un contexte d’annotation *activé* n’est pas *null*
- Un type de référence Nullable `C?` *accepte* la valeur null (mais un avertissement peut être généré dans un contexte d’annotation *désactivé* )

Les paramètres de type prennent également en compte les contraintes :

- Un paramètre de type `T` où toutes les contraintes (le cas échéant) sont des types qui génèrent des valeurs null (*Nullable* et *Unknown*) ou la contrainte de `class?` est *inconnue* .
- Un paramètre de type `T` où au moins une contrainte est soit *oublie* , soit non *null* , soit l’une des `struct` ou `class` contraintes est
    - *oublie* dans un contexte d’annotation *désactivé*
    - non *null* dans un contexte d’annotation *activé*
- Un paramètre de type Nullable `T?` où au moins l’une des contraintes de `T`est *oublie* ou non *null* , ou l’une des contraintes `struct` ou `class`, est
    - *Nullable* dans un contexte d’annotation *désactivé* (mais un avertissement est généré)
    - *Nullable* dans un contexte d’annotation *activé*

Pour un paramètre de type `T`, `T?` est autorisé uniquement si `T` est connu comme un type valeur ou s’il est connu comme étant un type référence.

### <a name="oblivious-vs-nonnullable"></a>Oublie et non null

Un `type` est considéré comme ayant lieu dans un contexte d’annotation donné lorsque le dernier jeton du type est dans ce contexte.

Si un type de référence donné `C` dans le code source est interprété comme oublie ou non null dépend du contexte d’annotation de ce code source. Mais une fois qu’elle est établie, elle est considérée comme faisant partie de ce type et « suit », par exemple, lors de la substitution des arguments de type générique. C’est comme s’il s’agissait d’une annotation comme `?` sur le type, mais invisible.

## <a name="constraints"></a>Contraintes

Les types de référence Nullable peuvent être utilisés en tant que contraintes génériques. En outre `object` est désormais valide en tant que contrainte explicite. L’absence d’une contrainte est maintenant équivalente à une contrainte de `object?` (au lieu de `object`), mais (contrairement à `object` avant) `object?` n’est pas interdite en tant que contrainte explicite.

`class?` est une nouvelle contrainte qui signale « peut-être un type de référence Nullable », tandis que `class` indique « type référence non null ».

La possibilité de valeur null d’un argument de type ou d’une contrainte n’a pas d’incidence sur la satisfaction de la contrainte par le type, sauf si ce n’est pas le cas aujourd’hui (les types valeur Nullable ne satisfont pas à la contrainte `struct`). Toutefois, si l’argument de type ne satisfait pas aux exigences de possibilité de valeur null de la contrainte, un avertissement peut être fourni.

## <a name="null-state-and-null-tracking"></a>État null et suivi null

Chaque expression d’un emplacement source donné a un *État null*, ce qui indique s’il est supposé avoir une valeur null. L’État null est « not null » ou « peut-être null ». L’État null est utilisé pour déterminer si un avertissement doit être fourni à propos des conversions et déréférencements non sécurisés par des valeurs NULL.

### <a name="null-tracking-for-variables"></a>Suivi de valeurs NULL pour les variables

Pour certaines expressions qui désignent des variables ou des propriétés, l’État null est suivi entre les occurrences, en fonction des assignations à celles-ci, les tests effectués sur ceux-ci et le workflow de contrôle entre eux. Cela est similaire à la façon dont l’assignation définie est suivie pour les variables. Les expressions suivies sont les suivantes :

```antlr
tracked_expression
    : simple_name
    | this
    | base
    | tracked_expression '.' identifier
    ;
```

Où les identificateurs désignent des champs ou des propriétés.

***Décrire les transitions d’État null similaires à l’assignation définie***

### <a name="null-state-for-expressions"></a>État null pour les expressions

L’État null d’une expression est dérivé de son formulaire et de son type, et de l’État null des variables qui y sont impliquées.

### <a name="literals"></a>Littéraux

L’État null d’un littéral de `null` est « peut-être null ». L’État null d’un `default` littéral qui est converti en un type qui est connu comme n’étant pas un type valeur non null est « peut-être null ». L’État null de tout autre littéral est « not null ».

### <a name="simple-names"></a>Noms simples

Si un `simple_name` n’est pas classifié comme une valeur, son état null est « not null ». Dans le cas contraire, il s’agit d’une expression suivie, et son état null est son état de suivi null à cet emplacement source.

### <a name="member-access"></a>Accès au membre

Si un `member_access` n’est pas classifié comme une valeur, son état null est « not null ». Sinon, s’il s’agit d’une expression suivie, son état null est son état de suivi null à cet emplacement source. Dans le cas contraire, son état null est l’État NULL par défaut de son type.

### <a name="invocation-expressions"></a>Appel d’expressions

Si un `invocation_expression` appelle un membre qui est déclaré avec un ou plusieurs attributs pour un comportement spécial Null, l’État null est déterminé par ces attributs. Dans le cas contraire, l’État null de l’expression est l’État NULL par défaut de son type.

### <a name="element-access"></a>Accès aux éléments

Si une `element_access` appelle un indexeur qui est déclaré avec un ou plusieurs attributs pour un comportement spécial Null, l’État null est déterminé par ces attributs. Dans le cas contraire, l’État null de l’expression est l’État NULL par défaut de son type.

### <a name="base-access"></a>Accès de base

Si `B` dénote le type de base du type englobant, `base.I` a le même État null que `((B)this).I` et `base[E]` a le même État null que `((B)this)[E]`.

### <a name="default-expressions"></a>Expressions par défaut

`default(T)` a l’État null « non null » si `T` est connu comme étant un type valeur non null. Dans le cas contraire, il a l’État NULL « peut-être null ».

### <a name="null-conditional-expressions"></a>Expressions conditionnelles null

Un `null_conditional_expression` a l’État NULL « peut-être null ».

### <a name="cast-expressions"></a>Expressions de cast

Si une expression de cast `(T)E` appelle une conversion définie par l’utilisateur, l’État null de l’expression est l’État NULL par défaut pour son type. Sinon, si `T` prend la valeur null (*Nullable* ou *Unknown*), l’État null est « peut-être null ». Sinon, l’État null est le même que l’État null de `E`.

### <a name="await-expressions"></a>Expressions await

L’État null de `await E` est l’État NULL par défaut de son type.

### <a name="the-as-operator"></a>Opérateur `as`

Une expression `as` a l’État NULL « peut-être null ».

### <a name="the-null-coalescing-operator"></a>Opérateur de fusion Null

`E1 ?? E2` a le même État null que `E2`

### <a name="the-conditional-operator"></a>l'opérateur conditionnel ;

L’État null de `E1 ? E2 : E3` est « not null » si l’État null de `E2` et `E3` sont « not null ». Dans le cas contraire, il s’agit de « peut-être null ».

### <a name="query-expressions"></a>Expressions de requête

L’État null d’une expression de requête est l’État NULL par défaut de son type.

### <a name="assignment-operators"></a>Opérateurs d'assignation

`E1 = E2` et `E1 op= E2` ont le même État null que `E2` après l’application de toutes les conversions implicites.

### <a name="unary-and-binary-operators"></a>Opérateurs unaires et binaires

Si un opérateur unaire ou binaire appelle un opérateur défini par l’utilisateur qui est déclaré avec un ou plusieurs attributs pour un comportement spécial Null, l’État null est déterminé par ces attributs. Dans le cas contraire, l’État null de l’expression est l’État NULL par défaut de son type.

***Quoi faire pour des `+` binaires sur des chaînes et des délégués ?***

### <a name="expressions-that-propagate-null-state"></a>Expressions qui propagent l’État null

`(E)`, `checked(E)` et `unchecked(E)` ont tous le même État null que `E`.

### <a name="expressions-that-are-never-null"></a>Expressions qui ne sont jamais null

L’État null des formulaires d’expression suivants est toujours « not null » :

- `this` Accès Transact-SQL
- chaînes interpolées
- expressions `new` (objets, délégués, objets anonymes et expressions de création de tableau)
- Expressions `typeof`
- Expressions `nameof`
- fonctions anonymes (méthodes anonymes et expressions lambda)
- expressions null-indulgent avec
- Expressions `is`

## <a name="type-inference"></a>Inférence de type

### <a name="type-inference-for-var"></a>Inférence de type pour `var`

Le type déduit pour les variables locales déclarées avec `var` est informé par l’État null de l’expression d’initialisation.

```csharp
var x = E;
```

Si le type de `E` est un type référence Nullable `C?` et que l’État null de `E` est « not null », le type déduit pour `x` est `C`. Dans le cas contraire, le type déduit est le type de `E`.

La possibilité de valeur null du type inféré pour `x` est déterminée comme décrit ci-dessus, en fonction du contexte d’annotation du `var`, comme si le type avait été donné explicitement à cette position.

### <a name="type-inference-for-var"></a>Inférence de type pour `var?`

Le type déduit pour les variables locales déclarées avec `var?` est indépendant de l’État null de l’expression d’initialisation.

```csharp
var? x = E;
```

Si le type `T` de `E` est un type valeur Nullable ou un type référence Nullable, le type déduit pour `x` est `T`. Sinon, si `T` est un type valeur non null `S` le type déduit est `S?`. Sinon, si `T` est un type référence qui n’est pas null `C` le type déduit est `C?`. Dans le cas contraire, la déclaration est illégale.

La possibilité de valeur null du type déduit pour `x` est toujours *Nullable*.

### <a name="generic-type-inference"></a>Inférence de type générique

L’inférence de type générique est améliorée pour déterminer si les types référence déduits doivent avoir la valeur null ou non. Il s’agit d’un meilleur effort, qui ne génère pas d’avertissements, mais peut entraîner des avertissements Nullable lorsque les types inférés de la surcharge sélectionnée sont appliqués aux arguments.

L’inférence de type ne repose pas sur le contexte d’annotation des types entrants. Au lieu de cela, une `type` est déduite, ce qui acquiert son propre contexte d’annotation là où elle aurait été définie explicitement. Cela souligne le rôle de l’inférence de type pour vous aider à écrire vous-même.

Plus précisément, le contexte d’annotation pour un argument de type inféré est le contexte du jeton qui aurait été suivi par la liste de paramètres de type `<...>`, avait-il eu un ; c’est-à-dire le nom de la méthode générique appelée. Pour les expressions de requête qui traduisent ces appels, le contexte est extrait du mot clé contextuel initial de la clause de requête à partir de laquelle l’appel est généré.

### <a name="the-first-phase"></a>La première phase

Les types référence Nullable circulent dans les limites des expressions initiales, comme décrit ci-dessous. En outre, deux nouveaux types de limites, à savoir `null` et `default` sont introduits. Leur objectif est de traiter les occurrences de `null` ou `default` dans les expressions d’entrée, ce qui peut entraîner la valeur null dans un type inféré, même si ce n’est pas le cas. Cela fonctionne même pour les types *valeur* Nullable, qui sont améliorés pour récupérer la « nullité » dans le processus d’inférence.

La détermination des limites à ajouter dans la première phase est améliorée comme suit :

Si un argument `Ei` a un type référence, le type `U` utilisé pour l’inférence dépend de l’État null de `Ei`, ainsi que de son type déclaré :
- Si le type déclaré est un type référence non null `U0` ou un type référence Nullable `U0?`
    - Si l’État null de `Ei` est « not null », `U` est `U0`
    - Si l’État null de `Ei` est « peut-être null », `U` est `U0?`
- Sinon, si `Ei` a un type déclaré, `U` est ce type
- Dans le cas contraire, si `Ei` est `null`, `U` est la limite spéciale `null`
- Dans le cas contraire, si `Ei` est `default`, `U` est la limite spéciale `default`
- Sinon, aucune inférence n’est effectuée.

### <a name="exact-upper-bound-and-lower-bound-inferences"></a>Inférences exactes, limites supérieures et limites inférieures

Dans les inférences *du* type `U` *au* type `V`, si `V` est un type référence Nullable `V0?`, `V0` est utilisé à la place de `V` dans les clauses suivantes.
- Si `V` est l’une des variables de type non fixed, `U` est ajouté comme limite exacte, supérieure ou inférieure comme avant
- Sinon, si `U` est `null` ou `default`, aucune inférence n’est effectuée
- Sinon, si `U` est un type référence Nullable `U0?`, `U0` est utilisé à la place de `U` dans les clauses suivantes.

L’essence est que la possibilité de valeur null liée directement à l’une des variables de type non Fixed est conservée dans ses limites. Pour les inférences qui parcourent davantage les types source et cible, en revanche, la possibilité de valeur null est ignorée. Il est possible qu’il ne corresponde pas, mais si ce n’est pas le cas, un avertissement est émis ultérieurement si la surcharge est choisie et appliquée.

### <a name="fixing"></a>Résolution

Actuellement, la spécification ne permet pas de décrire ce qui se produit lorsque plusieurs limites sont convertibles de l’identité les unes des autres, mais sont différentes. Cela peut se produire entre `object` et `dynamic`, entre les types tuple qui diffèrent uniquement dans les noms d’éléments, entre les types construits et maintenant entre `C` et `C?` pour les types référence.

En outre, nous devons propager la « valeur null » des expressions d’entrée vers le type de résultat. 

Pour gérer ces éléments, nous allons ajouter des phases à la résolution, ce qui est maintenant :

1. Rassemblez tous les types dans toutes les limites en tant que candidats, en supprimant `?` de tous les types de référence Nullable
2. Éliminez les candidats en fonction des exigences des limites exactes, inférieures et supérieures (en conservant les limites `null` et `default`)
3. Éliminer les candidats qui n’ont pas de conversion implicite vers tous les autres candidats
4. Si les candidats restants n’ont pas tous une conversion d’identité entre eux, l’inférence de type échoue
5. *Fusionnez* les candidats restants comme indiqué ci-dessous.
6. Si le candidat résultant est un type référence ou un type valeur non null et que *toutes* les limites exactes ou *une* des limites inférieures sont des types valeur Nullable, des types référence nullable, `null` ou `default`, `?` est ajouté au candidat résultant, ce qui en fait un type valeur Nullable ou un type référence.

La *fusion* est décrite entre deux types candidats. Elle est transitive et transmutative, de sorte que les candidats peuvent être fusionnés dans n’importe quel ordre avec le même résultat final. Elle n’est pas définie si les deux types candidats ne sont pas convertibles en identité.

La fonction *Merge* accepte deux types candidats et une direction ( *+* ou *-* ) :

- *Merge*(`T`, `T`, *d*) = t
- *Merge*(`S`, `T?`, *+* ) = *fusion*(`S?`, `T`, *+* ) = *Merge*(`S`, `T`, *+* )`?`
- *Merge*(`S`, `T?`, *-* ) = *fusion*(`S?`, `T`, *-* ) = *Merge*(`S`, `T`, *-* )
- *Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *+* ) = `C<`*fusion*(`S1`, `T1`, *d1*)`,...,`*fusion*(`Sn`, `Tn`, *DN*)`>`, *où*
    - `di` =  *+* si le `i`« th type » de `C<...>` est covariant
    - `di` =  *-* si le `i`« th type » du `C<...>` est un contre-ou un invariant
- *Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *-* ) = `C<`*fusion*(`S1`, `T1`, *d1*)`,...,`*fusion*(`Sn`, `Tn`, *DN*)`>`, *où*
    - `di` =  *-* si le `i`« th type » de `C<...>` est covariant
    - `di` =  *+* si le `i`« th type » du `C<...>` est un contre-ou un invariant
- *Merge*(`(S1 s1,..., Sn sn)`, `(T1 t1,..., Tn tn)`, *d*) = `(`*fusion*(`S1`, `T1`, *d*)`n1,...,`*fusion*(`Sn`, `Tn`, *d*) `nn)`, *où*
    - `ni` est absent si `si` et `ti` diffèrent, ou si les deux sont absents
    - `ni` est `si` si `si` et `ti` sont identiques
- *Merge*(`object`, `dynamic`) = *fusion*(`dynamic`, `object`) = `dynamic`

## <a name="warnings"></a>Avertissements

### <a name="potential-null-assignment"></a>Attribution de valeur null potentielle

### <a name="potential-null-dereference"></a>Déréférencement null potentiel

### <a name="constraint-nullability-mismatch"></a>Incompatibilité des valeurs null de contrainte

### <a name="nullable-types-in-disabled-annotation-context"></a>Types Nullable dans un contexte d’annotation désactivé

## <a name="attributes-for-special-null-behavior"></a>Attributs pour le comportement de la valeur null spéciale


