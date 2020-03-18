---
ms.openlocfilehash: 3df21c5816be90387a6cd9242e99ba11f43dfd1c
ms.sourcegitcommit: f61a06970fa0562d2e40363fae3948eb168624ca
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "79485167"
---
# <a name="pattern-matching-for-c-7"></a>Critères spéciaux pour C# 7

Les extensions de critères C# spéciaux pour permettre un grand nombre des avantages des types de données algébriques et des critères spéciaux des langages fonctionnels, mais d’une manière qui s’intègre parfaitement au langage sous-jacent. Les fonctionnalités de base sont : les [types d’enregistrements](https://github.com/dotnet/csharplang/blob/master/proposals/records.md), qui sont des types dont la signification sémantique est décrite par la forme des données ; et les critères spéciaux, qui sont un nouveau formulaire d’expression qui permet une décomposition à plusieurs niveaux extrêmement concise de ces types de données. Les éléments de cette approche sont inspirés par les fonctionnalités associées dans [F#](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/p29-syme.pdf "Critères spéciaux extensibles via un langage léger") les langages de programmation et [Scala](https://infoscience.epfl.ch/record/98468/files/MatchingObjectsWithPatterns-TR.pdf "Objets correspondants avec des modèles").

## <a name="is-expression"></a>Expression is

L’opérateur `is` est étendu pour tester une expression par rapport à un *modèle*.

```antlr
relational_expression
    : relational_expression 'is' pattern
    ;
```

Cette forme de *relational_expression* s’ajoute aux formulaires existants dans la C# spécification. Il s’agit d’une erreur au moment de la compilation si le *relational_expression* à gauche du jeton `is` ne désigne pas une valeur ou n’a pas de type.

Chaque *identificateur* du modèle introduit une nouvelle variable locale qui est *définitivement assignée* une fois que l’opérateur `is` est `true` (c’est-à-dire *affecté définitivement lorsque la valeur est true*).

> Remarque : il existe techniquement une ambiguïté entre le *type* d’une `is-expression` et *constant_pattern*, qui peuvent être une analyse valide d’un identificateur qualifié. Nous essayons de la lier en tant que type pour la compatibilité avec les versions précédentes du langage. en cas d’échec, nous allons le résoudre comme nous le faisons dans d’autres contextes, à la première chose trouvée (qui doit être une constante ou un type). Cette ambiguïté est uniquement présente sur le côté droit d’une expression `is`.

## <a name="patterns"></a>Modèles

Les modèles sont utilisés dans l’opérateur `is` et dans une *switch_Statement* pour exprimer la forme des données par rapport à laquelle les données entrantes doivent être comparées. Les modèles peuvent être récursifs afin que certaines parties des données puissent être comparées à des sous-modèles.

```antlr
pattern
    : declaration_pattern
    | constant_pattern
    | var_pattern
    ;

declaration_pattern
    : type simple_designation
    ;

constant_pattern
    : shift_expression
    ;

var_pattern
    : 'var' simple_designation
    ;
```

> Remarque : il existe techniquement une ambiguïté entre le *type* d’une `is-expression` et *constant_pattern*, qui peuvent être une analyse valide d’un identificateur qualifié. Nous essayons de la lier en tant que type pour la compatibilité avec les versions précédentes du langage. en cas d’échec, nous allons le résoudre comme nous le faisons dans d’autres contextes, à la première chose trouvée (qui doit être une constante ou un type). Cette ambiguïté est uniquement présente sur le côté droit d’une expression `is`.

### <a name="declaration-pattern"></a>Modèle de déclaration

Le *declaration_pattern* teste qu’une expression est d’un type donné et la convertit en ce type si le test a la valeur. Si le *simple_designation* est un identificateur, il introduit une variable locale du type donné nommé par l’identificateur donné. Cette variable locale est *assignée définitivement* lorsque le résultat de l’opération de correspondance de modèle est true.

```antlr
declaration_pattern
    : type simple_designation
    ;
```

La sémantique d’exécution de cette expression est qu’elle teste le type de Runtime de l’opérande de *relational_expression* gauche par rapport au *type* dans le modèle. S’il s’agit d’un type de Runtime (ou d’un sous-type), le résultat de l' `is operator` est `true`. Elle déclare une nouvelle variable locale nommée par l' *identificateur* auquel est affectée la valeur de l’opérande de gauche lorsque le résultat est `true`.

Certaines combinaisons de type statique du côté gauche et du type donné sont considérées comme incompatibles et entraînent une erreur au moment de la compilation. Une valeur de type statique `E` est dite *compatible* avec le type `T` s’il existe une conversion d’identité, une conversion de référence implicite, une conversion boxing, une conversion de référence explicite ou une conversion unboxing de `E` en `T`. Il s’agit d’une erreur de compilation si une expression de type `E` n’est pas compatible avec le type dans un modèle de type auquel elle est associée.

> Remarque : [dans C# 7,1, nous étendons cela](../csharp-7.1/generics-pattern-match.md) pour permettre une opération de mise en correspondance de modèle si le type d’entrée ou le type `T` est un type ouvert. Ce paragraphe est remplacé par ce qui suit :
> 
> Certaines combinaisons de type statique du côté gauche et du type donné sont considérées comme incompatibles et entraînent une erreur au moment de la compilation. Une valeur de type statique `E` est dite *compatible* avec le type `T` s’il existe une conversion d’identité, une conversion de référence implicite, une conversion boxing, une conversion de référence explicite ou une conversion unboxing de `E` en `T`, **ou si `E` ou `T` est un type ouvert**. Il s’agit d’une erreur de compilation si une expression de type `E` n’est pas compatible avec le type dans un modèle de type auquel elle est associée.

Le modèle de déclaration est utile pour effectuer des tests de type au moment de l’exécution de types référence et remplace l’idiome

```csharp
var v = expr as Type;
if (v != null) { // code using v }
```

Avec un peu plus concis

```csharp
if (expr is Type v) { // code using v }
```

Il s’agit d’une erreur si le *type* est un type valeur Nullable.

Le modèle de déclaration peut être utilisé pour tester des valeurs de types Nullable : une valeur de type `Nullable<T>` (ou un `T`boxed) correspond à un modèle de type `T2 id` si la valeur n’est pas null et que le type de `T2` est `T`, ou un type de base ou une interface de `T`. Par exemple, dans le fragment de code

```csharp
int? x = 3;
if (x is int v) { // code using v }
```

La condition de l’instruction `if` est `true` au moment de l’exécution et la variable `v` contient la valeur `3` de type `int` à l’intérieur du bloc.

### <a name="constant-pattern"></a>Modèle de constante

```antlr
constant_pattern
    : shift_expression
    ;
```

Un modèle de constante teste la valeur d’une expression par rapport à une valeur constante. La constante peut être une expression constante, telle qu’un littéral, le nom d’une variable de `const` déclarée, une constante d’énumération ou une expression de `typeof`.

Si les deux *e* et *c* sont des types intégraux, le modèle est considéré comme mis en correspondance si le résultat de l’expression `e == c` est `true`.

Dans le cas contraire, le modèle est considéré comme correspondant si `object.Equals(e, c)` retourne `true`. Dans ce cas, il s’agit d’une erreur au moment de la compilation si le type statique de *e* n’est pas *compatible* avec le type de la constante.

### <a name="var-pattern"></a>Modèle var

```antlr
var_pattern
    : 'var' simple_designation
    ;
```

Une expression *e* correspond à un *var_pattern* toujours. En d’autres termes, une correspondance avec un *modèle var* est toujours réussie. Si le *simple_designation* est un identificateur, alors, au moment de l’exécution, la valeur de *e* est liée à une variable locale nouvellement introduite. Le type de la variable locale est le type statique de *e*.

Il s’agit d’une erreur si le nom `var` est lié à un type.

## <a name="switch-statement"></a>Instruction switch

L’instruction `switch` est étendue pour sélectionner pour l’exécution le premier bloc ayant un modèle associé qui correspond à l' *expression de switch*.

```antlr
switch_label
    : 'case' complex_pattern case_guard? ':'
    | 'case' constant_expression case_guard? ':'
    | 'default' ':'
    ;

case_guard
    : 'when' expression
    ;
```

L’ordre dans lequel les modèles sont mis en correspondance n’est pas défini. Un compilateur est autorisé à faire correspondre les modèles dans le désordre et à réutiliser les résultats des modèles déjà associés pour calculer le résultat de la correspondance d’autres modèles.

Si une *protection de casse* est présente, son expression est de type `bool`. Elle est évaluée comme une condition supplémentaire qui doit être satisfaite pour que le cas soit considéré comme respecté.

Il s’agit d’une erreur si un *switch_label* peut n’avoir aucun effet au moment de l’exécution, car son modèle est inclus dans les cas précédents. [TODO : nous devrions être plus précis concernant les techniques que le compilateur est tenu d’utiliser pour atteindre ce jugement.]

Une variable de modèle déclarée dans un *switch_label* est assignée de manière définitive dans son bloc cas si et uniquement si ce bloc de cas contient précisément une *switch_label*.

[TODO : nous devons spécifier quand un *bloc switch* est accessible.]

### <a name="scope-of-pattern-variables"></a>Étendue des variables de modèle

La portée d’une variable déclarée dans un modèle est la suivante :

- Si le modèle est une étiquette case, alors l’étendue de la variable est le *bloc case*.

Dans le cas contraire, la variable est déclarée dans une expression *is_pattern* , et sa portée est basée sur la construction qui englobe immédiatement l’expression contenant l’expression *is_pattern* comme suit :

- Si l’expression se trouve dans une expression lambda avec une expression, son étendue est le corps de l’expression lambda.
- Si l’expression se trouve dans une méthode ou une propriété à expression nulle, son étendue est le corps de la méthode ou de la propriété.
- Si l’expression se trouve dans une clause `when` d’une clause `catch`, sa portée correspond à cette clause `catch`.
- Si l’expression se trouve dans un *iteration_statement*, son étendue est simplement cette instruction.
- Sinon, si l’expression est dans une autre forme d’instruction, son étendue est l’étendue contenant l’instruction.

Dans le but de déterminer l’étendue, un *embedded_statement* est considéré comme étant dans sa propre étendue. Par exemple, la grammaire d’un *if_statement* est

``` antlr
if_statement
    : 'if' '(' boolean_expression ')' embedded_statement
    | 'if' '(' boolean_expression ')' embedded_statement 'else' embedded_statement
    ;
```

Par conséquent, si l’instruction contrôlée d’un *if_statement* déclare une variable de modèle, sa portée est limitée à ce *embedded_statement*:

```csharp
if (x) M(y is var z);
```

Dans ce cas, la portée de `z` est l’instruction incorporée `M(y is var z);`.

D’autres cas sont des erreurs pour d’autres raisons (par exemple, dans la valeur par défaut d’un paramètre ou un attribut, les deux étant une erreur parce que ces contextes nécessitent une expression constante).

> [Dans C# 7,3, nous avons ajouté les contextes suivants](../csharp-7.3/expression-variables-in-initializers.md) dans lesquels une variable de modèle peut être déclarée :
> - Si l’expression se trouve dans un *initialiseur de constructeur*, sa portée est l' *initialiseur de constructeur* et le corps du constructeur.
> - Si l’expression se trouve dans un initialiseur de champ, son étendue est le *equals_value_clause* dans lequel elle apparaît.
> - Si l’expression se trouve dans une clause de requête spécifiée pour être convertie dans le corps d’une expression lambda, son étendue est uniquement cette expression.

## <a name="changes-to-syntactic-disambiguation"></a>Modifications apportées à la désambiguïsation syntaxique

Dans C# certaines situations, les génériques sont ambigus, et la spécification du langage indique comment résoudre ces ambiguïtés :

> #### <a name="7652-grammar-ambiguities"></a>ambiguïtés de la grammaire 7.6.5.2
> Les productions pour le *nom simple* (§ 7.6.3) et l' *accès aux membres* (§ 7.6.5) peuvent donner lieu à des ambiguïtés dans la grammaire des expressions. Par exemple, l’instruction suivante :
> ```csharp
> F(G<A,B>(7));
> ```
> peut être interprété comme un appel à `F` avec deux arguments, `G < A` et `B > (7)`. Elle peut également être interprétée comme un appel à `F` avec un argument, qui est un appel à une méthode générique `G` avec deux arguments de type et un argument normal.

> Si une séquence de jetons peut être analysée (en contexte) en tant que *nom simple* (§ 7.6.3), *accès aux membres* (§ 7.6.5) ou *accès aux membres de pointeurs* (§ 18.5.2) se terminant par un *type-argument-List* (§ 4.4.1), le jeton qui suit immédiatement le jeton `>` de fermeture est examiné. S’il s’agit de l’un des
> ```none
> (  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
> ```
> le *type-argument-List* est conservé dans le cadre du *simple-name*, *member-Access* ou *pointer-Member-Access* et toute autre analyse possible de la séquence de jetons est ignorée. Dans le cas contraire, la *liste d’arguments de type* n’est pas considérée comme faisant partie du *simple-name*, *member-Access* ou > *pointer-Member-Access*, même s’il n’y a aucune autre analyse possible de la séquence de jetons. Notez que ces règles ne sont pas appliquées lors de l’analyse d’un *type-argument-List* dans un *espace de noms ou un nom de type* (§ 3,8). L'instruction
> ```csharp
> F(G<A,B>(7));
> ```
> en fonction de cette règle, sera interprété comme un appel à `F` avec un argument, qui est un appel à une méthode générique `G` avec deux arguments de type et un argument normal. Les instructions
> ```csharp
> F(G < A, B > 7);
> F(G < A, B >> 7);
> ```
> est interprété comme un appel à `F` avec deux arguments. L'instruction
> ```csharp
> x = F < A > +y;
> ```
> est interprété comme un opérateur inférieur à, supérieur à et opérateur unaire, comme si l’instruction avait été écrite `x = (F < A) > (+y)`, et non comme un *simple-name* avec un *type-argument-List* suivi d’un opérateur binaire plus. Dans l’instruction
> ```csharp
> x = y is C<T> + z;
> ```
> les jetons `C<T>` sont interprétés comme un *espace de noms ou un nom* de type avec un *type-argument-List*.

Il y a un certain nombre de modifications introduites dans C# 7 qui rendent ces règles de désambiguïsation n’étant plus suffisantes pour gérer la complexité du langage.

### <a name="out-variable-declarations"></a>Déclarations de variables out

Il est désormais possible de déclarer une variable dans un argument out :

```csharp
M(out Type name);
```

Toutefois, le type peut être générique : 

```csharp
M(out A<B> name);
```

Étant donné que la grammaire de la langue de l’argument utilise l' *expression*, ce contexte est soumis à la règle de désambiguïsation. Dans ce cas, le `>` de fermeture est suivi d’un *identificateur*, qui n’est pas l’un des jetons qui lui permet d’être traité comme un *type-argument-List*. Je propose donc d' **Ajouter l' *identificateur* à l’ensemble de jetons qui déclenche la désambiguïsation pour une liste d’arguments de *type*.**

### <a name="tuples-and-deconstruction-declarations"></a>Déclarations de tuples et de déconstruction

Un littéral de tuple s’exécute exactement dans le même problème. Considérer l’expression de Tuple

```csharp
(A < B, C > D, E < F, G > H)
```

Dans les 6 C# anciennes règles d’analyse d’une liste d’arguments, celle-ci est interprétée comme un tuple avec quatre éléments, en commençant par `A < B` comme premier. Toutefois, lorsque cela apparaît à gauche d’une déconstruction, nous voulons que la désambiguation soit déclenchée par le jeton d' *identificateur* comme décrit ci-dessus :

```csharp
(A<B,C> D, E<F,G> H) = e;
```

Il s’agit d’une déclaration de déconstruction qui déclare deux variables, la première étant de type `A<B,C>` et nommée `D`. En d’autres termes, le littéral de tuple contient deux expressions, chacune d’elles étant une expression de déclaration.

Pour simplifier la spécification et le compilateur, je propose que ce littéral de tuple soit analysé comme un tuple à deux éléments à chaque fois qu’il apparaît (qu’il apparaisse ou non sur le côté gauche d’une assignation). Il s’agit d’un résultat naturel de la désambiguïsation qui est décrite dans la section précédente.

### <a name="pattern-matching"></a>Critères spéciaux

Les critères spéciaux présentent un nouveau contexte dans lequel l’ambiguïté de type expression se produit. Précédemment, le côté droit d’un opérateur de `is` était un type. À présent, il peut s’agir d’un type ou d’une expression, et s’il s’agit d’un type, il peut être suivi d’un identificateur. Cela peut, techniquement, modifier la signification du code existant :

```csharp
var x = e is T < A > B;
```

Cela peut être analysé dans les règles C# 6 comme

```csharp
var x = ((e is T) < A) > B;
```

mais sous les règles C# 7 (avec la désambiguïsation proposée ci-dessus) sont analysées comme

```csharp
var x = e is T<A> B;
```

qui déclare une variable `B` de type `T<A>`. Heureusement, les compilateurs natif et Roslyn ont un bogue qui leur donne une erreur de syntaxe sur le code C# 6. Par conséquent, cette modification avec rupture particulière n’est pas un problème.

La mise en correspondance de modèle introduit des jetons supplémentaires qui doivent conduire la résolution d’ambiguïté à la sélection d’un type. Les exemples suivants de code C# 6 valide existant seraient rompus sans règles de désambiguïsation supplémentaires :

```csharp
var x = e is A<B> && f;            // &&
var x = e is A<B> || f;            // ||
var x = e is A<B> & f;             // &
var x = e is A<B>[];               // [
```

### <a name="proposed-change-to-the-disambiguation-rule"></a>Proposition de modification de la règle de désambiguïsation

Je propose de modifier la spécification pour modifier la liste des jetons disambiguating à partir de

>
```none
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
```

to

>
```none
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^  &&  ||  &  [
```

Et, dans certains contextes, nous traitons l' *identificateur* en tant que jeton disambiguating. Ces contextes sont là où la séquence de jetons enlevée est immédiatement précédée de l’un des mots clés `is`, `case`ou `out`, ou se présente lors de l’analyse du premier élément d’un littéral de Tuple (auquel cas les jetons sont précédés de `(` ou `:` et l’identificateur est suivi d’un `,`) ou d’un élément suivant d’un littéral de Tuple.

### <a name="modified-disambiguation-rule"></a>Règle de désambiguïté modifiée

La règle de désambiguïté révisée serait semblable à celle-ci

> Si une séquence de jetons peut être analysée (en contexte) comme un *nom simple* (§ 7.6.3), un *accès aux membres* (§ 7.6.5) ou un *accès aux membres de pointeurs* (§ 18.5.2) se terminant par un *type-argument-List* (§ 4.4.1), le jeton qui suit immédiatement le jeton de `>` de fermeture est examiné pour voir s’il est
> - L’une des `(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^  &&  ||  &  [`; ni
> - L’un des opérateurs relationnels `<  >  <=  >=  is as`; ni
> - Mot clé de requête contextuelle apparaissant à l’intérieur d’une expression de requête ; ni
> - Dans certains contextes, nous traitons l' *identificateur* en tant que jeton disambiguating. Ces contextes sont là où la séquence de jetons enlevée est immédiatement précédée de l’un des mots clés `is`, `case` ou `out`, ou se présente lors de l’analyse du premier élément d’un littéral de Tuple (auquel cas les jetons sont précédés de `(` ou `:` et l’identificateur est suivi d’un `,`) ou d’un élément suivant d’un littéral de Tuple.
> 
> Si le jeton suivant figure parmi cette liste, ou un identificateur dans un tel contexte, le *type-argument-List* est conservé dans le cadre du *simple-name*, de l' *accès aux membres* ou du *pointeur-Member Access* , et toute autre analyse possible de la séquence de jetons est ignorée.  Dans le cas contraire, la *liste d’arguments de type* n’est pas considérée comme faisant partie du *simple-name*, d' *accès aux membres* ou d' *accès aux membres du pointeur*, même s’il n’y a pas d’autre analyse possible de la séquence de jetons. Notez que ces règles ne sont pas appliquées lors de l’analyse d’un *type-argument-List* dans un *espace de noms ou un nom de type* (§ 3,8).

### <a name="breaking-changes-due-to-this-proposal"></a>Modifications avec rupture en raison de cette proposition

Aucune modification avec rupture n’est connue en raison de cette règle de désambiguïsation d’ambiguïté proposée.

### <a name="interesting-examples"></a>Exemples intéressants

Voici quelques résultats intéressants de ces règles de désambiguïsation :

L’expression `(A < B, C > D)` est un tuple avec deux éléments, chacun étant une comparaison.

L’expression `(A<B,C> D, E)` est un tuple avec deux éléments, le premier étant une expression de déclaration.

L’appel de `M(A < B, C > D, E)` a trois arguments.

L’appel de `M(out A<B,C> D, E)` a deux arguments, le premier étant une déclaration de `out`.

L’expression `e is A<B> C` utilise une expression de déclaration.

L’étiquette case `case A<B> C:` utilise une expression de déclaration.

## <a name="some-examples-of-pattern-matching"></a>Exemples de critères spéciaux

### <a name="is-as"></a>Est de

Nous pouvons remplacer l’idiome

```csharp
var v = expr as Type;   
if (v != null) {
    // code using v
}
```

Avec un peu plus concis et direct

```csharp
if (expr is Type v) {
    // code using v
}
```

### <a name="testing-nullable"></a>Tester la valeur null

Nous pouvons remplacer l’idiome

```csharp
Type? v = x?.y?.z;
if (v.HasValue) {
    var value = v.GetValueOrDefault();
    // code using value
}
```

Avec un peu plus concis et direct

```csharp
if (x?.y?.z is Type value) {
    // code using value
}
```

### <a name="arithmetic-simplification"></a>Simplification arithmétique

Supposons que nous définissons un ensemble de types récursifs pour représenter des expressions (selon une proposition distincte) :

```csharp
abstract class Expr;
class X() : Expr;
class Const(double Value) : Expr;
class Add(Expr Left, Expr Right) : Expr;
class Mult(Expr Left, Expr Right) : Expr;
class Neg(Expr Value) : Expr;
```

Nous pouvons maintenant définir une fonction pour calculer la dérivée (non réduite) d’une expression :

```csharp
Expr Deriv(Expr e)
{
  switch (e) {
    case X(): return Const(1);
    case Const(*): return Const(0);
    case Add(var Left, var Right):
      return Add(Deriv(Left), Deriv(Right));
    case Mult(var Left, var Right):
      return Add(Mult(Deriv(Left), Right), Mult(Left, Deriv(Right)));
    case Neg(var Value):
      return Neg(Deriv(Value));
  }
}
```

Une expression simplifie les modèles positionnels :

```csharp
Expr Simplify(Expr e)
{
  switch (e) {
    case Mult(Const(0), *): return Const(0);
    case Mult(*, Const(0)): return Const(0);
    case Mult(Const(1), var x): return Simplify(x);
    case Mult(var x, Const(1)): return Simplify(x);
    case Mult(Const(var l), Const(var r)): return Const(l*r);
    case Add(Const(0), var x): return Simplify(x);
    case Add(var x, Const(0)): return Simplify(x);
    case Add(Const(var l), Const(var r)): return Const(l+r);
    case Neg(Const(var k)): return Const(-k);
    default: return e;
  }
}
```
