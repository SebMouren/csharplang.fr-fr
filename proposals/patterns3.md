---
ms.openlocfilehash: 57cdb682efd4cb169308e347d63e27c97b9f1b7a
ms.sourcegitcommit: 6901635c383801e4d177085587aaccadaa7b2f11
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641975"
---
# <a name="pattern-matching-changes-for-c-90"></a>Modifications correspondants aux modèles pour le C 9.0

Nous envisageons une petite poignée d’améliorations à l’appariement des motifs pour C 9.0 qui ont une synergie naturelle et fonctionnent bien pour résoudre un certain nombre de problèmes de programmation communs:
- https://github.com/dotnet/csharplang/issues/2925Types de type
- https://github.com/dotnet/csharplang/issues/1350Modèles parenthés pour faire respecter ou mettre l’accent sur la préséance des nouveaux combinateurs
- https://github.com/dotnet/csharplang/issues/1350Modèles conjonctifs `and` qui nécessitent deux modèles différents pour correspondre;
- https://github.com/dotnet/csharplang/issues/1350Modèles disjonctifs `or` qui nécessitent l’un ou l’autre des deux modèles différents pour correspondre;
- https://github.com/dotnet/csharplang/issues/1350Modèles `not` niés qui nécessitent un modèle donné pour *ne pas* correspondre; Et
- https://github.com/dotnet/csharplang/issues/812Modèles relationnels qui exigent que la valeur d’entrée soit inférieure, inférieure ou égale à, etc une constante donnée.

## <a name="parenthesized-patterns"></a>Modèles parenthésisés

Les motifs parenthésiens permettent au programmeur de mettre des parenthèses autour de n’importe quel modèle.  Ce n’est pas si utile avec les modèles existants dans C 8.0, mais les nouveaux combinateurs de modèle introduisent une priorité que le programmeur peut vouloir remplacer.

```antlr
primary_pattern
    : parenthesized_pattern
    | // all of the existing forms
    ;
parenthesized_pattern
    : '(' pattern ')'
    ;
```

## <a name="type-patterns"></a>Modèles de type

Nous permettons un type comme modèle :

``` antlr
primary_pattern
    : type-pattern
    | // all of the existing forms
    ;
type_pattern
    : type
    ;
```

Cela retcons l’expression existante *est-type-expression* d’être un *is-pattern-expression* dans laquelle le modèle est un *type-modèle,* bien que nous ne changerions pas l’arbre syntaxe produit par le compilateur.

Une question subtile de mise en œuvre est que cette grammaire est ambigu.  Une chaîne `a.b` telle que peut être analysée soit comme un nom qualifié (dans un contexte de type) ou une expression pointillée (dans un contexte d’expression).  Le compilateur est déjà capable de traiter un nom qualifié le même `e is Color.Red`qu’une expression pointillée afin de gérer quelque chose comme .  L’analyse sémantique du compilateur serait encore étendue pour être capable de lier un modèle constant (syntaxique) (par exemple une expression pointillée) comme un type afin de le traiter comme un modèle de type lié afin de soutenir cette construction.

Après ce changement, vous seriez en mesure d’écrire
```csharp
void M(object o1, object o2)
{
    var t = (o1, o2);
    if (t is (int, string)) {} // test if o1 is an int and o2 is a string
    switch (o1) {
        case int: break; // test if o1 is an int
        case System.String: break; // test if o1 is a string
    }
}
```

## <a name="relational-patterns"></a>Modèles relationnels

Les modèles relationnels permettent au programmeur d’exprimer qu’une valeur d’entrée doit satisfaire à une contrainte relationnelle par rapport à une valeur constante :

``` C#
    public static LifeStage LifeStageAtAge(int age) => age switch
    {
        < 0 =>  LifeStage.Prenatal,
        < 2 =>  LifeStage.Infant,
        < 4 =>  LifeStage.Toddler,
        < 6 =>  LifeStage.EarlyChild,
        < 12 => LifeStage.MiddleChild,
        < 20 => LifeStage.Adolescent,
        < 40 => LifeStage.EarlyAdult,
        < 65 => LifeStage.MiddleAdult,
        _ =>    LifeStage.LateAdult,
    };
```

Les modèles relationnels `<`soutiennent `<=` `>`les `>=` opérateurs relationnels, , , et sur tous les types intégrés qui prennent en charge ces opérateurs relationnels binaires avec deux opérandes du même type dans une expression. Spécifiquement, nous soutenons tous ces `sbyte`modèles `byte`relationnels pour `char`, `float` `double`, `decimal` `nint` `short`, `ushort`, `int` `uint`, `long`, , `ulong`, , , , , , , , , , et `nuint`.

```antlr
primary_pattern
    : relational_pattern
    ;
relational_pattern
    : '<' relational_expression
    | '<=' relational_expression
    | '>' relational_expression
    | '>=' relational_expression
    ;
```

L’expression est nécessaire pour évaluer à une valeur constante.  C’est une erreur si `double.NaN` `float.NaN`cette valeur constante est ou .  C’est une erreur si l’expression est une constante nulle.

Lorsque l’entrée est un type pour lequel un opérateur relationnel binaire intégré approprié est défini qui est applicable avec l’entrée comme son opératment gauche et la constante donnée comme son opéra droit, l’évaluation de cet opérateur est prise comme la signification du modèle relationnel.  Sinon, nous convertissons l’entrée au type d’expression à l’aide d’une conversion nulle ou déballage explicite.  Il s’agit d’une erreur de compilation-temps si aucune conversion de ce genre n’existe.  Le modèle est considéré comme ne correspondant pas si la conversion échoue.  Si la conversion réussit, le résultat de l’opération de `e OP v` jumelage des motifs est le résultat de l’évaluation de l’expression où `e` se trouve l’entrée convertie, `OP` est l’opérateur relationnel, et `v` est l’expression constante.

## <a name="pattern-combinators"></a>Combinateurs de modèle

Les *combinateurs* de modèle permettent `and` d’apparier les deux modèles différents utilisant `and`(ceci peut être `or` étendu à n’importe quel nombre de modèles par l’utilisation répétée de ), soit de deux modèles différents utilisant (ditto), ou la *négation* d’un modèle utilisant `not`.

Une utilisation commune d’un combinateur sera l’idiome

``` c#
if (e is not null) ...
```

Plus lisible que l’idiome `e is object`actuel, ce modèle exprime clairement que l’on vérifie une valeur non nulle.

Les `and` `or` combinateurs et les combinateurs seront utiles pour tester des gammes de valeurs

``` c#
bool IsLetter(char c) => c is >= 'a' and <= 'z' or >= 'A' and <= 'Z';
```

Cet exemple illustre `and` que la priorité d’analyse sera plus élevée (c’est-à-dire qu’elle se liera plus étroitement) que `or`.  Le programmeur peut utiliser le *modèle parenthèse* pour rendre la préséance explicite :

``` c#
bool IsLetter(char c) => c is (>= 'a' and <= 'z') or (>= 'A' and <= 'Z');
```

Comme tous les modèles, ces combinateurs peuvent être utilisés dans n’importe quel contexte dans lequel un modèle est attendu, y compris les modèles imbriqués, *l’expression est-modèle,* *l’expression de commutateur,* et le modèle de l’étiquette de cas d’une déclaration de commutateur.

```antlr
pattern
    : disjunctive_pattern
    ;
disjunctive_pattern
    : disjunctive_pattern 'or' conjunctive_pattern
    | conjunctive_pattern
    ;
conjunctive_pattern
    : conjunctive_pattern 'and' negated_pattern
    | negated_pattern
    ;
negated_pattern
    : 'not' negated_pattern
    | primary_pattern
    ;
primary_pattern
    : // all of the patterns forms previously defined
    ;
```

## <a name="open-issues-with-proposed-changes"></a>Questions ouvertes avec les changements proposés

### <a name="syntax-for-relational-operators"></a>Syntaxe pour les opérateurs relationnels

Sont `and` `or`, `not` , et une sorte de mot-clé contextuel?  Si c’est le cas, y *a-t-il*un changement de rupture (par exemple par rapport à leur utilisation en tant que désignateur dans un modèle de déclaration).

### <a name="semantics-eg-type-for-relational-operators"></a>Sémantique (p. ex. type) pour les opérateurs relationnels

Nous nous attendons à soutenir tous les types primitifs qui peuvent être comparés dans une expression à l’aide d’un opérateur relationnel.  Le sens dans les cas simples est clair

``` c#
bool IsValidPercentage(int x) => x is >= 0 and <= 100;
```

Mais lorsque l’entrée n’est pas un type aussi primitif, à quel type essayons-nous de le convertir?

``` c#
bool IsValidPercentage(object x) => x is >= 0 and <= 100;
```

Nous avons proposé que lorsque le type d’entrée est déjà un primitif comparable, c’est le type de comparaison. Cependant, lorsque l’entrée n’est pas un primitif comparable, nous traitons le relationnel comme incluant un test de type implicite au type de la constante sur le côté droit de la relationnelle.  Si le programmeur a l’intention de prendre en charge plus d’un type d’entrée, cela doit être fait explicitement :

``` c#
bool IsValidPercentage(object x) => x is
    >= 0 and <= 100 or    // integer tests
    >= 0F and <= 100F or  // float tests
    >= 0D and <= 100D;    // double tests
```

### <a name="flowing-type-information-from-the-left-to-the-right-of-and"></a>Informations de type fluide de la gauche à droite de`and`

Il a été suggéré que `and` lorsque vous écrivez un combinateur, les informations de type apprises à gauche sur le type de haut niveau pourraient circuler vers la droite.  Par exemple

```csharp
bool isSmallByte(object o) => o is byte and < 100;
```

Ici, le *type d’entrée* au deuxième modèle est réduit par `and`le type de *rétrécissement* des exigences de gauche de la .  Nous définirions la sémantique de rétrécissement de type pour tous les modèles comme suit.  Le *type rétréci* d’un modèle `P` est défini comme suit :
1. S’il `P` s’agit d’un modèle de type, le *type rétréci* est le type du type de modèle.
2. S’il `P` s’agit d’un modèle de déclaration, le *type rétréci* est le type du type de modèle de déclaration.
3. S’il `P` s’agit d’un modèle récursif qui donne un type explicite, le *type rétréci* est ce type.
4. Si `P` est un modèle constant où la constante n’est pas la constante nulle et où l’expression n’a pas *de conversion d’expression constante* au type *d’entrée,* le *type rétréci* est le type de la constante.
5. S’il `P` s’agit d’un modèle relationnel où l’expression constante n’a pas *de conversion d’expression constante* au type *d’entrée,* le *type rétréci* est le type de la constante.
6. `P` S’il `or` s’agit d’un modèle, le *type rétréci* est le type commun du *type rétréci* des sous-délinquants si un tel type commun existe. À cette fin, l’algorithme de type commun ne considère que l’identité, la boxe `or` et les conversions implicites de référence, et il considère tous les sous-délinquants d’une séquence de modèles (ignorant les modèles parenthés).
7. `P` S’il `and` s’agit d’un modèle, le *type rétréci* est le *type rétréci* du bon modèle. En outre, le *type rétréci* du modèle gauche est le type *d’entrée* du modèle droit.
8. Sinon, le type `P` `P` *rétréci* est 'type d’entrée.

### <a name="variable-definitions-and-definite-assignment"></a>Définitions variables et affectation définitive

L’ajout `or` `not` et les modèles crée de nouveaux problèmes intéressants autour des variables de modèle et de l’affectation définie.  Étant donné que les variables peuvent normalement être déclarées tout au `or` plus une fois, il semblerait que toute variable de modèle déclarée d’un côté d’un modèle ne serait pas définitivement attribuée lorsque le modèle correspond.  De même, on `not` ne s’attendrait pas à ce qu’une variable déclarée à l’intérieur d’un modèle soit définitivement attribuée lorsque le modèle correspond.  La façon la plus simple d’y remédier est d’interdire de déclarer des variables de modèle dans ces contextes.  Toutefois, cela peut être trop restrictif.  Il y a d’autres approches à considérer.

Un scénario qui mérite d’être envisagé est le

``` csharp
if (e is not int i) return;
M(i); // is i definitely assigned here?
```

Cela ne fonctionne pas aujourd’hui parce que, pour un *est-modèle-expression,* les variables de modèle sont considérés *définitivement attribués* seulement où *l’expression est-modèle* est vrai ("définitivement attribué quand vrai").

Soutenir cela serait plus simple (du point de vue du programmeur) `if` que d’ajouter un soutien à une déclaration de condition niée.  Même si nous ajoutons un tel soutien, les programmeurs se demanderaient pourquoi l’extrait ci-dessus ne fonctionne pas.  D’autre part, le même `switch` scénario dans un est moins logique, car il n’y a pas de point correspondant dans le programme où *certainement attribué quand faux* serait significatif.  Permettrons-nous cela dans une *expression de modèle,* mais pas dans d’autres contextes où les modèles sont autorisés?  Cela semble irrégulier.

A cela s’inttûment le problème de l’affectation définitive dans un *modèle disjonctif*.

```csharp
if (e is 0 or int i)
{
    M(i); // is i definitely assigned here?
}
```

Nous ne `i` nous attendrions à être définitivement affectés lorsque l’entrée n’est pas nulle.  Mais puisque nous ne savons pas si l’entrée `i` est nulle ou non à l’intérieur du bloc, n’est pas définitivement attribué.  Cependant, que se `i` passe-t-il si nous permettons d’être déclarés dans différents modèles mutuellement exclusifs?

```csharp
if ((e1, e2) is (0, int i) or (int i, 0))
{
    M(i);
}
```

Ici, la `i` variable est définitivement assignée à l’intérieur du bloc, et prend la valeur de l’autre élément du tuple quand un élément zéro est trouvé.

Il a également été suggéré de permettre que les variables soient (multipliées) définies dans tous les cas d’un bloc de cas :

```csharp
    case (0, int x):
    case (int x, 0):
        Console.WriteLine(x);
```

Pour que tout cela fonctionne, nous devrons définir soigneusement où de telles définitions multiples sont autorisées et dans quelles conditions une telle variable est considérée comme définitivement attribuée.

Si nous décidons de reporter ce travail à plus tard (ce que je conseille), nous pourrions dire dans C 9
- sous `not` un `or`ou , variables de modèle ne peuvent pas être déclarés.

Ensuite, nous aurions le temps de développer une expérience qui donnerait un aperçu de la valeur possible de se détendre plus tard.

### <a name="diagnostics-subsumption-and-exhaustiveness"></a>Diagnostics, sous-ssumption et exhaustivité

Ces nouveaux formulaires de modèle introduisent de nombreuses nouvelles possibilités d’erreur de programmeur diagnostique.  Nous devrons décider quels types d’erreurs nous diagnostiquerons et comment le faire.  Voici quelques exemples :

``` csharp
case >= 0 and <= 100D:
```

Ce cas ne peut jamais correspondre (parce que l’entrée ne peut pas être à la fois un `int` et un `double`).  Nous avons déjà une erreur lorsque nous détectons un cas qui ne peut jamais correspondre, mais son libellé ("Le boîtier de commutateur a déjà été traité par un cas précédent" et "Le modèle a déjà été manipulé par un bras précédent de l’expression de commutateur") peut être trompeur dans de nouveaux scénarios.  Nous devrons peut-être modifier le libellé pour simplement dire que le modèle ne correspondra jamais à l’entrée.

``` csharp
case 1 and 2:
```

De même, ce serait une erreur `1` parce `2`qu’une valeur ne peut pas être à la fois et .

``` csharp
case 1 or 2 or 3 or 1:
```

Ce cas est possible de `or 1` correspondre, mais à la fin n’ajoute aucun sens au modèle.  Je suggère que nous devrions viser à produire une erreur chaque fois qu’une certaine conjonction ou disjonction d’un modèle composé ne définit pas une variable de modèle ou n’affecte pas l’ensemble des valeurs assorties.

``` csharp
case < 2: break;
case 0 or 1 or 2 or 3 or 4 or 5: break;
```

En `0 or 1 or` l’espèce, rien n’ajoute à la deuxième affaire, car ces valeurs auraient été traitées par le premier cas.  Cela mérite aussi une erreur.

``` csharp
byte b = ...;
int x = b switch { <100 => 0, 100 => 1, 101 => 2, >101 => 3 };
```

Une expression de commutateur comme celle-ci doit être considérée comme *exhaustive* (elle gère toutes les valeurs d’entrée possibles).

Dans C 8.0, une expression de `byte` commutateur avec une entrée de type n’est considérée comme exhaustive que si elle contient un bras final dont le motif correspond à tout (un *modèle de rejet* ou *var-modèle).*  Même une expression de commutateur qui `byte` a un bras pour chaque valeur distincte n’est pas considérée comme exhaustive dans C 8.  Afin de gérer correctement l’exhaustivité des modèles relationnels, nous devrons traiter ce cas aussi.  Il s’agira techniquement d’un changement de rupture, mais aucun utilisateur n’est susceptible de remarquer.
