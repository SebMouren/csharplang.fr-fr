---
ms.openlocfilehash: 3fc0f7d8db936d81a9419af15c495e9eeb456dd2
ms.sourcegitcommit: 7c44a62f9a639b8eb8ad8621d8577e90ea6f2afb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85196189"
---
# <a name="nullable-reference-types-specification"></a>Spécification des types référence Nullable

***Il s’agit d’un travail en cours : plusieurs parties sont manquantes ou incomplètes.***

## <a name="syntax"></a>Syntax

### <a name="nullable-reference-types"></a>Types références Nullables

Les types référence Nullable ont la même syntaxe `T?` que la forme abrégée des types valeur Nullable, mais n’ont pas de forme longue correspondante.

Dans le cadre de la spécification, la `nullable_type` production actuelle est renommée `nullable_value_type` et une `nullable_reference_type` production est ajoutée :

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

Le `non_nullable_reference_type` dans un `nullable_reference_type` doit être un type de référence non Nullable (classe, interface, délégué ou tableau) ou un paramètre de type qui est imposé comme étant un type de référence non Nullable (par le biais de la `class` contrainte ou d’une classe autre que `object` ).

Les types de référence Nullable ne peuvent pas se trouver aux emplacements suivants :

- comme une classe ou une interface de base
- en tant que récepteur d’un`member_access`
- comme `type` dans un`object_creation_expression`
- comme `delegate_type` dans un`delegate_creation_expression`
- comme `type` dans un `is_expression` , un `catch_clause` ou un`type_pattern`
- en tant que `interface` dans un nom de membre d’interface qualifié complet

Un avertissement est fourni sur un `nullable_reference_type` où le contexte d’annotation Nullable est désactivé.

### <a name="nullable-class-constraint"></a>Contrainte de classe Nullable

La `class` contrainte a un équivalent Nullable `class?` :

```antlr
primary_constraint
    : ...
    | 'class' '?'
    ;
```

### <a name="the-null-forgiving-operator"></a>Opérateur null-indulgent avec

L’opérateur de postconnexion `!` est appelé opérateur null-indulgent avec.

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

L’opérateur postfix n' `!` a aucun effet d’exécution, il prend la valeur du résultat de l’expression sous-jacente. Son seul rôle consiste à modifier l’État null de l’expression et à limiter les avertissements en fonction de son utilisation.

### <a name="nullable-implicitly-typed-local-variables"></a>variables locales implicitement typées Nullable

`var`déduit un type annoté pour les types référence.
Par exemple, dans `var s = "";` le `var` est déduit en tant que `string?` .

### <a name="nullable-compiler-directives"></a>Directives de compilateur Nullable

`#nullable`les directives contrôlent l’annotation Nullable et les contextes d’avertissement.

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

`#pragma warning`les directives sont développées pour permettre la modification du contexte d’avertissement Nullable et pour permettre l’activation des avertissements individuels sur même lorsqu’ils sont désactivés par défaut :

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

Notez que la nouvelle forme d' `pragma_warning_body` utilisations `nullable_action` , et non `warning_action` .

## <a name="nullable-contexts"></a>Contextes nullables

Chaque ligne de code source a un *contexte d’annotation Nullable* et un *contexte d’avertissement Nullable*. Ils contrôlent si les annotations Nullable ont un effet et si les avertissements de possibilité de valeur NULL sont fournis. Le contexte d’annotation d’une ligne donnée est *désactivé* ou *activé*. Le contexte d’avertissement d’une ligne donnée est *désactivé* ou *activé*.

Les deux contextes peuvent être spécifiés au niveau du projet (en dehors du code source C#), ou n’importe où dans un fichier source via des `#nullable` directives de préprocesseur. Si aucun paramètre de niveau projet n’est fourni, la valeur par défaut est pour que les deux contextes soient *désactivés*.

La `#nullable` directive contrôle les contextes d’annotation et d’avertissement dans le texte source et a priorité sur les paramètres au niveau du projet.

Une directive définit le ou les contexte (s) qu’elle contrôle pour les lignes de code suivantes, jusqu’à ce qu’une autre directive la remplace, ou jusqu’à la fin du fichier source.

L’effet des directives est le suivant :

- `#nullable disable`: Définit l’annotation Nullable et les contextes d’avertissement sur *désactivé*
- `#nullable enable`: Définit l’annotation Nullable et les contextes d’avertissement sur *activé*
- `#nullable restore`: Restaure l’annotation Nullable et les contextes d’avertissement dans les paramètres du projet
- `#nullable disable annotations`: Définit le contexte d’annotation Nullable sur *désactivé*
- `#nullable enable annotations`: Définit le contexte d’annotation Nullable sur *activé*
- `#nullable restore annotations`: Restaure le contexte d’annotation Nullable aux paramètres du projet
- `#nullable disable warnings`: Définit le contexte d’avertissement Nullable sur *désactivé*
- `#nullable enable warnings`: Définit le contexte d’avertissement Nullable sur *activé*
- `#nullable restore warnings`: Restaure le contexte d’avertissement Nullable aux paramètres du projet

## <a name="nullability-of-types"></a>Nullabilité des types

Un type donné peut avoir l’un des quatre nullabilities : *oublie*, non *null*, *Nullable* et *Unknown*. 

Les types non *null* et *inconnus* peuvent provoquer des avertissements si une `null` valeur potentielle leur est assignée. Toutefois, les types *oublie* et *Nullable* sont «*null-assignables*» et peuvent avoir des `null` valeurs qui leur sont assignées sans avertissements. 

Les types *oublie* et non *null* peuvent être déréférencés ou assignés sans avertissements. Toutefois, les valeurs de type *Nullable* et *Unknown* sont «*yield null*» et peuvent provoquer des avertissements lorsqu’ils sont déréférencés ou assignés sans vérification de valeur null correcte. 

L' *État NULL par défaut* d’un type qui génère un null est « peut-être null ». L’État NULL par défaut d’un type qui ne génère pas de valeur null est « not null ».

Le genre de type et le contexte d’annotation Nullable qu’il contient se trouvent dans déterminer sa possibilité de valeur NULL :

- Un type valeur `S` non null est toujours non *null*
- Un type valeur Nullable `S?` est toujours *Nullable*
- Un type référence non annoté `C` dans un contexte d’annotation *désactivé* est *oublie*
- Un type référence non annoté `C` dans un contexte d’annotation *activé* n’est pas *null*
- Un type de référence Nullable `C?` est *Nullable* (mais un avertissement peut être généré dans un contexte d’annotation *désactivé* )

Les paramètres de type prennent également en compte les contraintes :

- Un paramètre de type `T` où toutes les contraintes (le cas échéant) sont des types qui génèrent des valeurs null (*Nullable* et *Unknown*) ou la `class?` contrainte est *inconnue* .
- Un paramètre de type `T` dans lequel au moins une contrainte est *oublie* ou non *null* , ou l’une `struct` des `class` contraintes ou est
    - *oublie* dans un contexte d’annotation *désactivé*
    - non *null* dans un contexte d’annotation *activé*
- Un paramètre de type Nullable `T?` dans lequel au moins l’une des `T` contraintes de est *oublie* ou non *null* , ou l’une des `struct` `class` contraintes ou est
    - *Nullable* dans un contexte d’annotation *désactivé* (mais un avertissement est généré)
    - *Nullable* dans un contexte d’annotation *activé*

Pour un paramètre de type `T` , `T?` est autorisé uniquement si `T` est connu comme étant un type valeur ou s’il est connu comme étant un type référence.

### <a name="nested-functions"></a>Fonctions imbriquées

Les fonctions imbriquées (expressions lambda et fonctions locales) sont traitées comme des méthodes, sauf en ce qui concerne leurs variables capturées.
L’État par défaut d’une variable capturée à l’intérieur d’une fonction lambda ou locale est l’intersection de l’État Nullable de la variable à toutes les « utilisations » de cette fonction imbriquée. L’utilisation d’une fonction est un appel à cette fonction ou l’endroit où elle est convertie en délégué.

### <a name="oblivious-vs-nonnullable"></a>Oublie et non null

Un `type` est considéré comme ayant lieu dans un contexte d’annotation donné lorsque le dernier jeton du type est dans ce contexte.

Si un type de référence donné `C` dans le code source est interprété comme oublie ou non null, dépend du contexte d’annotation de ce code source. Mais une fois qu’elle est établie, elle est considérée comme faisant partie de ce type et « suit », par exemple, lors de la substitution des arguments de type générique. C’est comme s’il s’agissait d’une annotation telle que `?` sur le type, mais invisible.

## <a name="constraints"></a>Contraintes

Les types de référence Nullable peuvent être utilisés en tant que contraintes génériques. `object`En outre, est désormais valide en tant que contrainte explicite. L’absence d’une contrainte est maintenant équivalente à une `object?` contrainte (au lieu de `object` ), mais (contrairement `object` à Before) `object?` n’est pas interdite en tant que contrainte explicite.

`class?`est une nouvelle contrainte qui signale « peut-être un type de référence Nullable », alors que `class` indique « type référence non null ».

La possibilité de valeur null d’un argument de type ou d’une contrainte n’a pas d’incidence sur la satisfaction de la contrainte par le type, sauf si ce n’est pas le cas aujourd’hui (les types valeur Nullable ne répondent pas à la `struct` contrainte). Toutefois, si l’argument de type ne satisfait pas aux exigences de possibilité de valeur null de la contrainte, un avertissement peut être fourni.

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

L’État null d’un `null` littéral est « peut-être null ». L’État null d’un `default` littéral qui est converti en un type qui est connu comme n’étant pas un type valeur non null est « peut-être null ». L’État null de tout autre littéral est « not null ».

### <a name="simple-names"></a>Noms simples

Si un `simple_name` n’est pas classé comme une valeur, son état null est « not null ». Dans le cas contraire, il s’agit d’une expression suivie, et son état null est son état de suivi null à cet emplacement source.

### <a name="member-access"></a>Accès au membre

Si un `member_access` n’est pas classé comme une valeur, son état null est « not null ». Sinon, s’il s’agit d’une expression suivie, son état null est son état de suivi null à cet emplacement source. Dans le cas contraire, son état null est l’État NULL par défaut de son type.

### <a name="invocation-expressions"></a>Appel d’expressions

Si un `invocation_expression` appelle un membre qui est déclaré avec un ou plusieurs attributs pour un comportement spécial Null, l’État null est déterminé par ces attributs. Dans le cas contraire, l’État null de l’expression est l’État NULL par défaut de son type.

### <a name="element-access"></a>Accès aux éléments

Si un `element_access` appelle un indexeur qui est déclaré avec un ou plusieurs attributs pour un comportement spécial Null, l’État null est déterminé par ces attributs. Dans le cas contraire, l’État null de l’expression est l’État NULL par défaut de son type.

### <a name="base-access"></a>Accès de base

Si `B` indique le type de base du type englobant, `base.I` a le même État null que `((B)this).I` et `base[E]` a le même État null que `((B)this)[E]` .

### <a name="default-expressions"></a>Expressions par défaut

`default(T)`a l’État null « non null » si `T` est connu pour être un type valeur non null. Dans le cas contraire, il a l’État NULL « peut-être null ».

### <a name="null-conditional-expressions"></a>Expressions conditionnelles null

Un `null_conditional_expression` a l’État NULL « peut-être null ».

### <a name="cast-expressions"></a>Expressions cast

Si une expression de Cast `(T)E` appelle une conversion définie par l’utilisateur, l’État null de l’expression est l’État NULL par défaut pour son type. Sinon, si `T` a un rendement null (*Nullable* ou *inconnu*), l’État null est « peut-être null ». Sinon, l’État null est le même que l’État null de `E` .

### <a name="await-expressions"></a>Expressions await

L’État null de `await E` est l’État NULL par défaut de son type.

### <a name="the-as-operator"></a>`as`Opérateur

Une `as` expression a l’État NULL « peut-être null ».

### <a name="the-null-coalescing-operator"></a>Opérateur de fusion Null

`E1 ?? E2`a le même État null que`E2`

### <a name="the-conditional-operator"></a>l'opérateur conditionnel ;

L’État null de `E1 ? E2 : E3` est « not null » si l’État null de `E2` et est `E3` « not null ». Dans le cas contraire, il s’agit de « peut-être null ».

### <a name="query-expressions"></a>Expressions de requête

L’État null d’une expression de requête est l’État NULL par défaut de son type.

### <a name="assignment-operators"></a>Opérateurs d'assignation

`E1 = E2`et `E1 op= E2` ont le même État null qu' `E2` après l’application de toutes les conversions implicites.

### <a name="unary-and-binary-operators"></a>Opérateurs unaires et binaires

Si un opérateur unaire ou binaire appelle un opérateur défini par l’utilisateur qui est déclaré avec un ou plusieurs attributs pour un comportement spécial Null, l’État null est déterminé par ces attributs. Dans le cas contraire, l’État null de l’expression est l’État NULL par défaut de son type.

***Quoi faire pour les chaînes binaires `+` et les délégués ?***

### <a name="expressions-that-propagate-null-state"></a>Expressions qui propagent l’État null

`(E)`, `checked(E)` et `unchecked(E)` ont tous le même État null que `E` .

### <a name="expressions-that-are-never-null"></a>Expressions qui ne sont jamais null

L’État null des formulaires d’expression suivants est toujours « not null » :

- `this` Accès Transact-SQL
- chaînes interpolées
- `new`expressions (objets, délégués, objets anonymes et expressions de création de tableau)
- Expressions `typeof`
- Expressions `nameof`
- fonctions anonymes (méthodes anonymes et expressions lambda)
- expressions null-indulgent avec
- Expressions `is`

## <a name="type-inference"></a>Inférence de type

### <a name="type-inference-for-var"></a>Inférence de type pour`var`

Le type déduit pour les variables locales déclarées avec `var` est informé par l’État null de l’expression d’initialisation.

```csharp
var x = E;
```

Si le type de `E` est un type référence Nullable `C?` et si l’État null de `E` est « not null », le type déduit pour `x` est `C` . Dans le cas contraire, le type déduit est le type de `E` .

La possibilité de valeur null du type inféré pour `x` est déterminée comme décrit ci-dessus, en fonction du contexte d’annotation du `var` , comme si le type avait été donné explicitement à cette position.

### <a name="type-inference-for-var"></a>Inférence de type pour`var?`

Le type déduit pour les variables locales déclarées avec `var?` est indépendant de l’État null de l’expression d’initialisation.

```csharp
var? x = E;
```

Si le type `T` de `E` est un type valeur Nullable ou un type référence Nullable, le type déduit pour `x` est `T` . Sinon, si `T` est un type valeur non null, `S` le type déduit est `S?` . Sinon, si `T` est un type référence qui n’est pas null, `C` le type déduit est `C?` . Dans le cas contraire, la déclaration est illégale.

La possibilité de valeur null du type inféré pour `x` est toujours *Nullable*.

### <a name="generic-type-inference"></a>Inférence de type générique

L’inférence de type générique est améliorée pour déterminer si les types référence déduits doivent avoir la valeur null ou non. Il s’agit d’un meilleur effort, qui ne génère pas d’avertissements, mais peut entraîner des avertissements Nullable lorsque les types inférés de la surcharge sélectionnée sont appliqués aux arguments.

L’inférence de type ne repose pas sur le contexte d’annotation des types entrants. Au lieu de cela, un `type` est déduit, ce qui acquiert son propre contexte d’annotation là où il a été exprimé de manière explicite. Cela souligne le rôle de l’inférence de type pour vous aider à écrire vous-même.

Plus précisément, le contexte d’annotation pour un argument de type inféré est le contexte du jeton qui aurait été suivi par la `<...>` liste de paramètres de type, avait-il eu un ; autrement dit, le nom de la méthode générique appelée. Pour les expressions de requête qui traduisent ces appels, le contexte est extrait du mot clé contextuel initial de la clause de requête à partir de laquelle l’appel est généré.

### <a name="the-first-phase"></a>La première phase

Les types référence Nullable circulent dans les limites des expressions initiales, comme décrit ci-dessous. En outre, deux nouveaux types de limites, à savoir `null` et `default` sont introduits. Leur but est de traverser des occurrences de `null` ou `default` dans les expressions d’entrée, ce qui peut entraîner la création d’une valeur null dans un type inféré, même si ce n’est pas le cas. Cela fonctionne même pour les types *valeur* Nullable, qui sont améliorés pour récupérer la « nullité » dans le processus d’inférence.

La détermination des limites à ajouter dans la première phase est améliorée comme suit :

Si un argument `Ei` a un type référence, le type `U` utilisé pour l’inférence dépend de l’État null de, ainsi `Ei` que de son type déclaré :
- Si le type déclaré est un type référence non null `U0` ou un type référence Nullable `U0?` , alors
    - Si l’État null de `Ei` est « not null », alors `U` est`U0`
    - Si l’État null de `Ei` est « peut-être null », alors `U` est`U0?`
- Sinon `Ei` , si a un type déclaré, `U` est ce type
- Sinon, si `Ei` est `null` ensuite `U` la limite spéciale`null`
- Sinon, si `Ei` est `default` ensuite `U` la limite spéciale`default`
- Sinon, aucune inférence n’est effectuée.

### <a name="exact-upper-bound-and-lower-bound-inferences"></a>Inférences exactes, limites supérieures et limites inférieures

Dans les inférences *du* type `U` *vers* le type `V` , si `V` est un type référence Nullable `V0?` , `V0` est utilisé à la place de `V` dans les clauses suivantes.
- Si `V` est l’une des variables de type non fixed, `U` est ajouté comme limite exacte, supérieure ou inférieure comme avant
- Sinon, si `U` est `null` ou `default` , aucune inférence n’est effectuée
- Sinon, si `U` est un type référence Nullable `U0?` , `U0` est utilisé `U` à la place de dans les clauses suivantes.

L’essence est que la possibilité de valeur null liée directement à l’une des variables de type non Fixed est conservée dans ses limites. Pour les inférences qui parcourent davantage les types source et cible, en revanche, la possibilité de valeur null est ignorée. Il est possible qu’il ne corresponde pas, mais si ce n’est pas le cas, un avertissement est émis ultérieurement si la surcharge est choisie et appliquée.

### <a name="fixing"></a>Résolution 

Actuellement, la spécification ne permet pas de décrire ce qui se produit lorsque plusieurs limites sont convertibles de l’identité les unes des autres, mais sont différentes. Cela peut se produire entre `object` et `dynamic` , entre les types tuple qui diffèrent uniquement dans les noms d’éléments, entre les types construits et maintenant entre `C` et `C?` pour les types référence.

En outre, nous devons propager la « valeur null » des expressions d’entrée vers le type de résultat. 

Pour gérer ces éléments, nous allons ajouter des phases à la résolution, ce qui est maintenant :

1. Rassemblez tous les types dans toutes les limites en tant que candidats, en supprimant `?` de tous les types de référence Nullable
2. Éliminer les candidats en fonction des exigences relatives aux limites exactes, inférieures et supérieures (maintien `null` et `default` limites)
3. Éliminer les candidats qui n’ont pas de conversion implicite vers tous les autres candidats
4. Si les candidats restants n’ont pas tous une conversion d’identité entre eux, l’inférence de type échoue
5. *Fusionnez* les candidats restants comme indiqué ci-dessous.
6. Si le candidat résultant est un type référence ou un type valeur non null et que *toutes* les limites exactes ou *une* des limites inférieures sont des types valeur Nullable, des types référence Nullable ou `null` `default` , `?` est ajouté au candidat résultant, ce qui en fait un type valeur Nullable ou un type référence.

La *fusion* est décrite entre deux types candidats. Elle est transitive et transmutative, de sorte que les candidats peuvent être fusionnés dans n’importe quel ordre avec le même résultat final. Elle n’est pas définie si les deux types candidats ne sont pas convertibles en identité.

La fonction *Merge* accepte deux types candidats et une direction ( *+* ou *-* ) :

- *Fusionner*( `T` , `T` , *d*) = T
- *Merge*( `S` , `T?` , *+* ) = *fusion*( `S?` , `T` , *+* ) = *Merge*( `S` , `T` , *+* )`?`
- *Merge*( `S` , `T?` , *-* ) = *fusion*( `S?` , `T` , *-* ) = *Merge*( `S` , `T` , *-* )
- *Merge*( `C<S1,...,Sn>` , `C<T1,...,Tn>` , *+* ) = `C<` *fusion*( `S1` , `T1` *D1*) `,...,` *fusionner*( `Sn` , `Tn` , *DN*) `>` , *où*
    - `di` = *+* Si le `i` paramètre de type « TH » de `C<...>` est covariant
    - `di` = *-* Si le `i` paramètre de type « TH » de `C<...>` est en contre-ou invariant
- *Merge*( `C<S1,...,Sn>` , `C<T1,...,Tn>` , *-* ) = `C<` *fusion*( `S1` , `T1` *D1*) `,...,` *fusionner*( `Sn` , `Tn` , *DN*) `>` , *où*
    - `di` = *-* Si le `i` paramètre de type « TH » de `C<...>` est covariant
    - `di` = *+* Si le `i` paramètre de type « TH » de `C<...>` est en contre-ou invariant
- *Merge*( `(S1 s1,..., Sn sn)` , `(T1 t1,..., Tn tn)` , *d*) = `(` *fusion*( `S1` , `T1` , *d*) `n1,...,` *fusion*( `Sn` , `Tn` , *d*) `nn)` , *où*
    - `ni`est absent si `si` et `ti` diffère, ou si les deux sont absents
    - `ni`est `si` si `si` et `ti` sont identiques
- *Merge*( `object` , `dynamic` ) = *fusion*( `dynamic` , `object` ) =`dynamic`

## <a name="warnings"></a>Avertissements

### <a name="potential-null-assignment"></a>Attribution de valeur null potentielle

### <a name="potential-null-dereference"></a>Déréférencement null potentiel

### <a name="constraint-nullability-mismatch"></a>Incompatibilité des valeurs null de contrainte

### <a name="nullable-types-in-disabled-annotation-context"></a>Types Nullable dans un contexte d’annotation désactivé

## <a name="attributes-for-special-null-behavior"></a>Attributs pour le comportement de la valeur null spéciale


