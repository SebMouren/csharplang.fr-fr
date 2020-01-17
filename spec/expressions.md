---
ms.openlocfilehash: f61039abd6bd557ac0ea625e6aac1c8bafa57b02
ms.sourcegitcommit: e134bb7058e9848120b93b345f96d6ac0cb8c815
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "71704086"
---
# <a name="expressions"></a>Expressions

Une expression est une séquence d'opérateurs et d'opérandes. Ce chapitre définit la syntaxe, l’ordre d’évaluation des opérandes et des opérateurs, ainsi que la signification des expressions.

## <a name="expression-classifications"></a>Classifications d’expressions

Une expression est classée comme l'un des éléments suivants :

*  Valeur. Chaque valeur possède un type associé.
*  Variable. Chaque variable a un type associé, à savoir le type déclaré de la variable.
*  Espace de noms. Une expression avec cette classification ne peut apparaître qu’en tant que partie gauche d’un *member_access* ([accès aux membres](expressions.md#member-access)). Dans tout autre contexte, une expression classée en tant qu’espace de noms provoque une erreur de compilation.
*  Type. Une expression avec cette classification ne peut apparaître qu’en tant que partie gauche d’un *member_access* ([accès aux membres](expressions.md#member-access)), ou en tant qu’opérande pour l’opérateur `as` ([opérateur as](expressions.md#the-as-operator)), le `is` opérateur ([opérateur is](expressions.md#the-is-operator)) ou l’opérateur `typeof` ([opérateur typeof](expressions.md#the-typeof-operator)). Dans tout autre contexte, une expression classifiée comme un type provoque une erreur au moment de la compilation.
*  Un groupe de méthodes, qui est un ensemble de méthodes surchargées résultant d’une recherche de membre ([recherche de membre](expressions.md#member-lookup)). Un groupe de méthodes peut avoir une expression d’instance associée et une liste d’arguments de type associée. Quand une méthode d’instance est appelée, le résultat de l’évaluation de l’expression d’instance devient l’instance représentée par `this` ([cet accès](expressions.md#this-access)). Un groupe de méthodes est autorisé dans un *invocation_expression* ([expressions d’appel](expressions.md#invocation-expressions)), un *delegate_creation_expression* (expressions de[création de délégué](expressions.md#delegate-creation-expressions)) et en tant que côté gauche d’un opérateur is et peut être converti implicitement en un type délégué compatible ([conversions de groupe de méthodes](conversions.md#method-group-conversions)). Dans tout autre contexte, une expression classifiée comme un groupe de méthodes provoque une erreur au moment de la compilation.
*  Littéral null. Une expression avec cette classification peut être implicitement convertie en un type référence ou un type Nullable.
*  Fonction anonyme. Une expression avec cette classification peut être convertie implicitement en un type délégué compatible ou un type d’arborescence d’expression.
*  Accès à une propriété. Chaque accès à une propriété a un type associé, à savoir le type de la propriété. En outre, un accès à une propriété peut avoir une expression d’instance associée. Lorsqu’un accesseur (bloc `get` ou `set`) d’un accès à une propriété d’instance est appelé, le résultat de l’évaluation de l’expression d’instance devient l’instance représentée par `this` ([cet accès](expressions.md#this-access)).
*  Accès à un événement. Chaque accès à un événement a un type associé, à savoir le type de l’événement. En outre, un accès aux événements peut avoir une expression d’instance associée. Un accès aux événements peut apparaître en tant qu’opérande gauche des opérateurs `+=` et `-=` ([assignation d’événement](expressions.md#event-assignment)). Dans tout autre contexte, une expression classifiée comme un accès à un événement provoque une erreur de compilation.
*  Un accès à un indexeur. Chaque accès à l’indexeur a un type associé, à savoir le type d’élément de l’indexeur. En outre, un accès à un indexeur a une expression d’instance associée et une liste d’arguments associée. Lorsqu’un accesseur (bloc `get` ou `set`) d’un accès à un indexeur est appelé, le résultat de l’évaluation de l’expression d’instance devient l’instance représentée par `this` ([cet accès](expressions.md#this-access)), et le résultat de l’évaluation de la liste d’arguments devient la liste de paramètres de l’appel.
*  Rien. Cela se produit lorsque l’expression est un appel d’une méthode avec un type de retour `void`. Une expression classifiée comme Nothing n’est valide que dans le contexte d’une *statement_expression* ([instructions d’expression](statements.md#expression-statements)).

Le résultat final d’une expression n’est jamais un espace de noms, un type, un groupe de méthodes ou un accès à un événement. Au lieu de cela, comme indiqué ci-dessus, ces catégories d’expressions sont des constructions intermédiaires qui sont uniquement autorisées dans certains contextes.

Un accès à une propriété ou un accès à un indexeur est toujours reclassifié comme valeur en effectuant un appel de l' *accesseur Get* ou de l' *accesseur Set*. L’accesseur particulier est déterminé par le contexte de l’accès à la propriété ou à l’indexeur : si l’accès est la cible d’une assignation, l' *accesseur Set* est appelé pour assigner une nouvelle valeur ([assignation simple](expressions.md#simple-assignment)). Dans le cas contraire, l' *accesseur Get* est appelé pour obtenir la valeur actuelle ([valeurs des expressions](expressions.md#values-of-expressions)).

### <a name="values-of-expressions"></a>Valeurs des expressions

La plupart des constructions qui impliquent une expression requièrent finalement que l’expression désigne une ***valeur***. Dans ce cas, si l’expression réelle désigne un espace de noms, un type, un groupe de méthodes ou rien, une erreur de compilation se produit. Toutefois, si l’expression désigne un accès à une propriété, un indexeur ou une variable, la valeur de la propriété, de l’indexeur ou de la variable est implicitement remplacée :

*  La valeur d’une variable est simplement la valeur actuellement stockée dans l’emplacement de stockage identifié par la variable. Une variable doit être considérée comme assignée de manière définitive ([assignation définie](variables.md#definite-assignment)) avant que sa valeur puisse être obtenue, sinon une erreur de compilation se produit.
*  La valeur d’une expression d’accès à la propriété est obtenue en appelant l' *accesseur Get* de la propriété. Si la propriété n’a pas d' *accesseur Get*, une erreur de compilation se produit. Sinon, un appel de membre de fonction ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) est effectué et le résultat de l’appel devient la valeur de l’expression d’accès à la propriété.
*  La valeur d’une expression d’accès à l’indexeur est obtenue en appelant l' *accesseur Get* de l’indexeur. Si l’indexeur n’a pas d' *accesseur Get*, une erreur de compilation se produit. Sinon, un appel de membre de fonction ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) est effectué avec la liste d’arguments associée à l’expression d’accès de l’indexeur, et le résultat de l’appel devient la valeur de l’expression d’accès de l’indexeur.

## <a name="static-and-dynamic-binding"></a>Liaison statique et dynamique

Le processus de détermination de la signification d’une opération en fonction du type ou de la valeur des expressions constitutives (arguments, opérandes, récepteurs) est souvent appelé ***liaison***. Par exemple, la signification d’un appel de méthode est déterminée en fonction du type du récepteur et des arguments. La signification d’un opérateur est déterminée en fonction du type de ses opérandes.

C# Le sens d’une opération est généralement déterminé au moment de la compilation, en fonction du type au moment de la compilation des expressions qui le composent. De même, si une expression contient une erreur, l’erreur est détectée et signalée par le compilateur. Cette approche est connue sous le nom de ***liaison statique***.

Toutefois, si une expression est une expression dynamique (par exemple, a le type `dynamic`), cela indique que toute liaison à laquelle elle participe doit être basée sur son type au moment de l’exécution (par exemple, le type réel de l’objet qu’elle désigne au moment de l’exécution) plutôt que le type qu’elle a au moment de la compilation. La liaison d’une telle opération est donc différée jusqu’au moment où l’opération doit être exécutée pendant l’exécution du programme. C’est ce que l’on appelle la ***liaison dynamique***.

Lorsqu’une opération est liée de manière dynamique, le compilateur n’effectue que peu ou pas de vérification. Au lieu de cela, si la liaison au moment de l’exécution échoue, les erreurs sont signalées en tant qu’exceptions au moment de l’exécution.

Les opérations suivantes dans C# sont soumises à la liaison :

*  Accès au membre : `e.M`
*  Appel de méthode : `e.M(e1, ..., eN)`
*  Appel de délégué :`e(e1, ..., eN)`
*  Accès aux éléments : `e[e1, ..., eN]`
*  Création d’objets : `new C(e1, ..., eN)`
*  Opérateurs unaires surchargés : `+`, `-`, `!`, `~`, `++`, `--`, `true`, `false`
*  Opérateurs binaires surchargés : `+`, `-`, `*`, `/`, `%`, `&`, `&&`, `|`, `||`, `??`, `^`, `<<`, `>>`, `==`,`!=`, `>`, `<`, `>=`, `<=`
*  Opérateurs d’assignation : `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, `>>=`
*  Conversions implicites et explicites

Quand aucune expression dynamique n’est impliquée, C# la valeur par défaut est la liaison statique, ce qui signifie que les types de compilation des expressions constitutives sont utilisés dans le processus de sélection. Toutefois, lorsque l’une des expressions constitutives des opérations listées ci-dessus est une expression dynamique, l’opération est liée dynamiquement.

### <a name="binding-time"></a>Liaison-heure

La liaison statique a lieu au moment de la compilation, tandis que la liaison dynamique a lieu au moment de l’exécution. Dans les sections suivantes, le terme « ***Binding-Time*** » fait référence à la compilation ou au moment de l’exécution, selon le moment où la liaison a lieu.

L’exemple suivant illustre les notions de liaison statique et dynamique et de l’heure de liaison :
```csharp
object  o = 5;
dynamic d = 5;

Console.WriteLine(5);  // static  binding to Console.WriteLine(int)
Console.WriteLine(o);  // static  binding to Console.WriteLine(object)
Console.WriteLine(d);  // dynamic binding to Console.WriteLine(int)
```

Les deux premiers appels sont liés statiquement : la surcharge de `Console.WriteLine` est choisie en fonction du type au moment de la compilation de leur argument. Ainsi, la durée de la liaison est au moment de la compilation.

Le troisième appel est lié dynamiquement : la surcharge de `Console.WriteLine` est choisie en fonction du type au moment de l’exécution de son argument. Cela se produit parce que l’argument est une expression dynamique--son type au moment de la compilation est `dynamic`. Ainsi, le temps de liaison pour le troisième appel est au moment de l’exécution.

### <a name="dynamic-binding"></a>Liaison dynamique

L’objectif de la liaison dynamique est de C# permettre aux programmes d’interagir avec des ***objets dynamiques***, c’est-à-dire des objets qui ne respectent pas les règles C# normales du système de type. Les objets dynamiques peuvent être des objets provenant d’autres langages de programmation avec des systèmes de types différents, ou des objets qui sont configurés par programme pour implémenter leur propre sémantique de liaison pour différentes opérations.

Le mécanisme par lequel un objet dynamique implémente sa propre sémantique est défini par l’implémentation. Une interface donnée, à nouveau définie, est implémentée par les objets dynamiques pour signaler au moment C# de l’exécution qu’ils ont une sémantique spéciale. Par conséquent, chaque fois que des opérations sur un objet dynamique sont liées de manière dynamique, leur propre sémantique de liaison C# , plutôt que celles de telles qu’elles sont spécifiées dans ce document, prennent le contrôle.

Bien que l’objectif de la liaison dynamique soit d’autoriser l’interopérabilité avec C# les objets dynamiques, autorise la liaison dynamique sur tous les objets, qu’ils soient dynamiques ou non. Cela permet une intégration plus fluide des objets dynamiques, car les résultats des opérations sur ceux-ci peuvent ne pas être eux-mêmes des objets dynamiques, mais ils sont toujours d’un type inconnu pour le programmeur au moment de la compilation. En outre, la liaison dynamique permet d’éliminer le code basé sur la réflexion, susceptible d’engendrer des erreurs, même quand aucun objet impliqué n’est un objet dynamique.

Les sections suivantes décrivent pour chaque construction dans le langage exactement quand la liaison dynamique est appliquée, la vérification du temps de compilation, le cas échéant, et le résultat de la classification des expressions et des résultats au moment de la compilation.

### <a name="types-of-constituent-expressions"></a>Types d’expressions constitutives

Lorsqu’une opération est liée de manière statique, le type d’une expression de composant (par exemple, un récepteur, un argument, un index ou un opérande) est toujours considéré comme le type au moment de la compilation de cette expression.

Lorsqu’une opération est liée de manière dynamique, le type d’une expression constitutive est déterminé de différentes manières selon le type de compilation de l’expression constitutive :

*  Une expression constitutive du type `dynamic` au moment de la compilation est considérée comme ayant le type de la valeur réelle évaluée par l’expression au moment de l’exécution
*  Une expression de composant dont le type au moment de la compilation est un paramètre de type est considérée comme ayant le type auquel le paramètre de type est lié au moment de l’exécution
*  Dans le cas contraire, l’expression constitutive est considérée comme ayant le type au moment de la compilation.

## <a name="operators"></a>Opérateurs

Les expressions sont construites à partir d' ***opérandes*** et d' ***opérateurs***. Les opérateurs d’une expression indiquent les opérations à appliquer aux opérandes. Parmi les exemples d’opérateurs figurent `+`, `-`, `*`, `/` et `new`. Les littéraux, les champs, les variables locales et les expressions sont des exemples d’opérandes.

Il existe trois types d’opérateurs :

*  Les opérateurs unaires. Les opérateurs unaires acceptent un opérande et utilisent soit la notation de préfixe (par exemple `--x`), soit la notation suffixée (comme `x++`).
*  Opérateurs binaires. Les opérateurs binaires prennent deux opérandes et utilisent tous la notation infixe (par exemple `x + y`).
*  Opérateur ternaire. Un seul opérateur ternaire, `?:`, existe ; Il prend trois opérandes et utilise la notation infixe (`c ? x : y`).

L’ordre d’évaluation des opérateurs dans une expression est déterminé par la ***priorité*** et l' ***associativité*** des opérateurs ([priorité et associativité](expressions.md#operator-precedence-and-associativity)des opérateurs).

Les opérandes d’une expression sont évalués de gauche à droite. Par exemple, dans `F(i) + G(i++) * H(i)`, la méthode `F` est appelée à l’aide de l’ancienne valeur de `i`, puis la méthode `G` est appelée avec l’ancienne valeur de `i`et, enfin, la méthode `H` est appelée avec la nouvelle valeur de `i`. Cela est indépendant de et non lié à la priorité des opérateurs.

Certains opérateurs peuvent être ***surchargés***. La surcharge d’opérateur permet de spécifier des implémentations d’opérateur définies par l’utilisateur pour les opérations où l’un des opérandes ou les deux sont d’un type de classe ou de struct défini par l’utilisateur ([surcharge d’opérateur](expressions.md#operator-overloading)).

### <a name="operator-precedence-and-associativity"></a>Priorité des opérateurs et associativité

Quand une expression contient plusieurs opérateurs, la ***priorité*** des opérateurs contrôle l'ordre dans lequel les opérateurs individuels sont évalués. Par exemple, l’expression `x + y * z` est évaluée comme `x + (y * z)`, car l’opérateur `*` a une priorité plus élevée que l’opérateur `+` binaire. La priorité d’un opérateur est établie par la définition de sa production grammaticale associée. Par exemple, un *additive_expression* se compose d’une séquence de *multiplicative_expression*s séparés par `+` ou `-` opérateurs, donnant ainsi aux opérateurs `+` et `-` une priorité plus faible que les opérateurs `*`, `/`et `%`.

Le tableau suivant récapitule tous les opérateurs par ordre de priorité, du plus élevé au plus bas :

| __Section__                                                                                   | __Catégorie__                | __Opérateurs__ | 
|-----------------------------------------------------------------------------------------------|-----------------------------|---------------|
| [Expressions primaires](expressions.md#primary-expressions)                                     | Primary                     | `x.y`  `f(x)`  `a[x]`  `x++`  `x--`  `new`  `typeof`  `default`  `checked`  `unchecked`  `delegate` | 
| [Opérateurs unaires](expressions.md#unary-operators)                                             | Unaire                       | `+`  `-`  `!`  `~`  `++x`  `--x`  `(T)x` | 
| [Opérateurs arithmétiques](expressions.md#arithmetic-operators)                                   | Multiplication              | `*`  `/`  `%` | 
| [Opérateurs arithmétiques](expressions.md#arithmetic-operators)                                   | Addition                    | `+`  `-`      | 
| [Opérateurs de décalage](expressions.md#shift-operators)                                             | Shift                       | `<<`  `>>`    | 
| [Opérateurs relationnels et de test de type](expressions.md#relational-and-type-testing-operators) | Relations et test de type | `<`  `>`  `<=`  `>=`  `is`  `as` | 
| [Opérateurs relationnels et de test de type](expressions.md#relational-and-type-testing-operators) | Égalité                    | `==`  `!=`    | 
| [Opérateurs logiques](expressions.md#logical-operators)                                         | AND logique                 | `&`           | 
| [Opérateurs logiques](expressions.md#logical-operators)                                         | XOR logique                 | `^`           | 
| [Opérateurs logiques](expressions.md#logical-operators)                                         | OR logique                  | <code>&#124;</code>           |
| [Opérateurs logiques conditionnels](expressions.md#conditional-logical-operators)                 | AND conditionnel             | `&&`          | 
| [Opérateurs logiques conditionnels](expressions.md#conditional-logical-operators)                 | OR conditionnel              | <code>&#124;&#124;</code>          | 
| [Opérateur de fusion de Null](expressions.md#the-null-coalescing-operator)                   | Fusion de Null             | `??`          | 
| [Opérateur conditionnel](expressions.md#conditional-operator)                                   | Conditionnel                 | `?:`          | 
| [Opérateurs d’assignation](expressions.md#assignment-operators), [expressions de fonction anonymes](expressions.md#anonymous-function-expressions)  | Assignation et expression lambda | `=`  `*=`  `/=`  `%=`  `+=`  `-=`  `<<=`  `>>=`  `&=`  `^=`  <code>&#124;=</code>  `=>` | 

Lorsqu’un opérande se produit entre deux opérateurs ayant la même priorité, l’associativité des opérateurs contrôle l’ordre dans lequel les opérations sont effectuées :

*  À l’exception des opérateurs d’assignation et de l’opérateur de fusion Null, tous les opérateurs binaires sont ***associatifs à gauche***, ce qui signifie que les opérations sont effectuées de gauche à droite. Par exemple, `x + y + z` est évalué comme étant `(x + y) + z`.
*  Les opérateurs d’assignation, l’opérateur de fusion Null et l’opérateur conditionnel (`?:`) sont ***associatifs à droite***, ce qui signifie que les opérations sont exécutées de droite à gauche. Par exemple, `x = y = z` est évalué comme étant `x = (y = z)`.

La priorité et l’associativité peuvent être contrôlées à l’aide de parenthèses. Par exemple, `x + y * z` multiplie d’abord `y` par `z`, puis ajoute le résultat à `x`, mais `(x + y) * z` ajoute d’abord `x` et `y`, puis multiplie le résultat par `z`.

### <a name="operator-overloading"></a>Surcharge d’opérateur

Tous les opérateurs unaires et binaires ont des implémentations prédéfinies qui sont automatiquement disponibles dans toute expression. Outre les implémentations prédéfinies, les implémentations définies par l’utilisateur peuvent être introduites en incluant des déclarations de `operator` dans des classes et des structs ([opérateurs](classes.md#operators)). Les implémentations d’opérateur définies par l’utilisateur ont toujours priorité sur les implémentations d’opérateur prédéfinies : uniquement lorsqu’il n’existe aucune implémentation d’opérateur définie par l’utilisateur applicable, les implémentations d’opérateur prédéfinies sont prises en compte, comme décrit dans [résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution) et [résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution).

Les ***opérateurs unaires surchargeables*** sont :
```csharp
+   -   !   ~   ++   --   true   false
```

Même si `true` et `false` ne sont pas utilisés explicitement dans les expressions (et ne sont donc pas inclus dans la table de précédence dans la [priorité d’opérateur et l’associativité](expressions.md#operator-precedence-and-associativity)), ils sont considérés comme des opérateurs, car ils sont appelés dans plusieurs contextes d’expression : des expressions booléennes ([expressions booléennes](expressions.md#boolean-expressions)) et des expressions impliquant l’opérateur conditionnel ([opérateur conditionnel](expressions.md#conditional-operator)[) et](expressions.md#conditional-logical-operators)conditionnelles

Les ***opérateurs binaires surchargeables*** sont les suivants :
```csharp
+   -   *   /   %   &   |   ^   <<   >>   ==   !=   >   <   >=   <=
```

Seuls les opérateurs listés ci-dessus peuvent être surchargés. En particulier, il n’est pas possible de surcharger l’accès aux membres, l’appel de méthode ou les `=`, `&&`, `||`, `??`, `?:`, `=>`, `checked`, `unchecked`, `new`, `typeof`, `default`, `as`et les opérateurs `is`.

Quand un opérateur binaire est surchargé, l’opérateur d’assignation correspondant, le cas échéant, est aussi implicitement surchargé. Par exemple, une surcharge de l’opérateur `*` est également une surcharge de l’opérateur `*=`. Cela est décrit plus en détail dans [assignation composée](expressions.md#compound-assignment). Notez que l’opérateur d’assignation lui-même (`=`) ne peut pas être surchargé. Une assignation effectue toujours une simple copie bit-wise d’une valeur dans une variable.

Les opérations de cast, telles que `(T)x`, sont surchargées en fournissant des conversions définies par l’utilisateur ([conversions définies par l’utilisateur](conversions.md#user-defined-conversions)).

L’accès aux éléments, tel que `a[x]`, n’est pas considéré comme un opérateur surchargeable. À la place, l’indexation définie par l’utilisateur est prise en charge par le biais d’indexeurs ([indexeurs](classes.md#indexers)).

Dans les expressions, les opérateurs sont référencés à l’aide de la notation opérateur, et dans les déclarations, les opérateurs sont référencés à l’aide de la notation fonctionnelle. Le tableau suivant montre la relation entre les notations opérateur et opérateur pour les opérateurs unaires et binaires. Dans la première entrée, *op* dénote n’importe quel opérateur de préfixe unaire surchargeable. Dans la deuxième entrée, *op* dénote le suffixe unaire `++` et `--` opérateurs. Dans la troisième entrée, *op* dénote tout opérateur binaire surchargeable.


| __Notation d’opérateur__ | __Notation fonctionnelle__ |
|-----------------------|-------------------------|
| `op x`                | `operator op(x)`        | 
| `x op`                | `operator op(x)`        | 
| `x op y`              | `operator op(x,y)`      | 

Les déclarations d’opérateur définies par l’utilisateur requièrent toujours qu’au moins un des paramètres soit du type de classe ou de struct qui contient la déclaration d’opérateur. Par conséquent, il n’est pas possible pour un opérateur défini par l’utilisateur d’avoir la même signature qu’un opérateur prédéfini.

Les déclarations d’opérateur définies par l’utilisateur ne peuvent pas modifier la syntaxe, la priorité ou l’associativité d’un opérateur. Par exemple, l’opérateur `/` est toujours un opérateur binaire, possède toujours le niveau de précédence spécifié dans [priorité et associativité des opérateurs](expressions.md#operator-precedence-and-associativity), et est toujours associatif à gauche.

Bien qu’il soit possible pour un opérateur défini par l’utilisateur d’effectuer n’importe quel calcul, les implémentations qui produisent des résultats autres que ceux qui sont intuitivement attendus sont fortement déconseillées. Par exemple, une implémentation de `operator ==` doit comparer les deux opérandes pour déterminer leur égalité et retourner un résultat de `bool` approprié.

Les descriptions des opérateurs individuels dans les [expressions primaires](expressions.md#primary-expressions) par le biais d' [opérateurs logiques conditionnels](expressions.md#conditional-logical-operators) spécifient les implémentations prédéfinies des opérateurs et toutes les règles supplémentaires qui s’appliquent à chaque opérateur. Les descriptions utilisent les termes résolution de ***surcharge d’opérateur unaire***, ***résolution de surcharge d’opérateur binaire***et ***promotion numérique***, dont les définitions se trouvent dans les sections suivantes.

### <a name="unary-operator-overload-resolution"></a>Résolution de surcharge d’opérateur unaire

Opération de la forme `op x` ou `x op`, où `op` est un opérateur unaire surchargeable, et `x` est une expression de type `X`, est traité comme suit :

*  Le jeu d’opérateurs candidats définis par l’utilisateur fourni par `X` pour l’opération `operator op(x)` est déterminé à l’aide des règles des [opérateurs candidats définis](expressions.md#candidate-user-defined-operators)par l’utilisateur.
*  Si le jeu d’opérateurs candidats définis par l’utilisateur n’est pas vide, il devient alors l’ensemble des opérateurs candidats pour l’opération. Sinon, les implémentations unaires prédéfinies de `operator op`, y compris leurs formulaires levés, deviennent l’ensemble des opérateurs candidats pour l’opération. Les implémentations prédéfinies d’un opérateur donné sont spécifiées dans la description de l’opérateur ([expressions primaires](expressions.md#primary-expressions) et [opérateurs unaires](expressions.md#unary-operators)).
*  Les règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution) sont appliquées au jeu d’opérateurs candidats pour sélectionner le meilleur opérateur par rapport à la liste d’arguments `(x)`, et cet opérateur devient le résultat du processus de résolution de surcharge. Si la résolution de surcharge ne peut pas sélectionner un seul opérateur Best, une erreur de liaison est générée.

### <a name="binary-operator-overload-resolution"></a>Résolution de surcharge d’opérateur binaire

Une opération de la forme `x op y`, où `op` est un opérateur binaire surchargeable, `x` est une expression de type `X`, et `y` est une expression de type `Y`, est traité comme suit :

*  Le jeu d’opérateurs candidats définis par l’utilisateur fourni par `X` et `Y` pour l’opération `operator op(x,y)` est déterminé. L’ensemble se compose de l’Union des opérateurs candidats fournis par `X` et des opérateurs candidats fournis par `Y`, chacun déterminé à l’aide des règles des [opérateurs candidats définis](expressions.md#candidate-user-defined-operators)par l’utilisateur. Si `X` et `Y` sont du même type, ou si `X` et `Y` sont dérivés d’un type de base commun, les opérateurs candidats partagés se produisent uniquement dans le jeu combiné une fois.
*  Si le jeu d’opérateurs candidats définis par l’utilisateur n’est pas vide, il devient alors l’ensemble des opérateurs candidats pour l’opération. Dans le cas contraire, les implémentations binaires prédéfinies de `operator op`, y compris leurs formulaires levés, deviennent l’ensemble des opérateurs candidats pour l’opération. Les implémentations prédéfinies d’un opérateur donné sont spécifiées dans la description de l’opérateur ([opérateurs arithmétiques](expressions.md#arithmetic-operators) à l’aide d' [opérateurs logiques conditionnels](expressions.md#conditional-logical-operators)). Pour les opérateurs enum et Delegate prédéfinis, les seuls opérateurs pris en compte sont ceux définis par un type enum ou Delegate qui est le type de temps de liaison de l’un des opérandes.
*  Les règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution) sont appliquées au jeu d’opérateurs candidats pour sélectionner le meilleur opérateur par rapport à la liste d’arguments `(x,y)`, et cet opérateur devient le résultat du processus de résolution de surcharge. Si la résolution de surcharge ne peut pas sélectionner un seul opérateur Best, une erreur de liaison est générée.

### <a name="candidate-user-defined-operators"></a>Opérateurs candidats définis par l’utilisateur

Étant donné un type `T` et une opération `operator op(A)`, où `op` est un opérateur surchargeable et `A` est une liste d’arguments, l’ensemble des opérateurs candidats définis par l’utilisateur fournis par `T` pour `operator op(A)` est déterminé comme suit :

*  Déterminez le type `T0`. Si `T` est un type Nullable, `T0` est son type sous-jacent, sinon `T0` est égal à `T`.
*  Pour toutes les déclarations de `operator op` dans `T0` et toutes les formes levées de tels opérateurs, si au moins un opérateur est applicable ([membre de fonction applicable](expressions.md#applicable-function-member)) par rapport à la liste d’arguments `A`, alors l’ensemble des opérateurs candidats se compose de tous les opérateurs applicables dans `T0`.
*  Sinon, si `T0` est `object`, l’ensemble des opérateurs candidats est vide.
*  Sinon, le jeu d’opérateurs candidats fourni par `T0` est l’ensemble des opérateurs candidats fournis par la classe de base directe de `T0`ou la classe de base effective de `T0` si `T0` est un paramètre de type.

### <a name="numeric-promotions"></a>Promotions numériques

La promotion numérique consiste à effectuer automatiquement certaines conversions implicites des opérandes des opérateurs numériques unaires et binaires prédéfinis. La promotion numérique n’est pas un mécanisme distinct, mais plutôt un effet de l’application de la résolution de surcharge aux opérateurs prédéfinis. La promotion numérique n’affecte pas spécifiquement l’évaluation des opérateurs définis par l’utilisateur, bien que les opérateurs définis par l’utilisateur puissent être implémentés pour présenter des effets similaires.

À titre d’exemple de promotion numérique, prenez en compte les implémentations prédéfinies de l’opérateur de `*` binaire :

```csharp
int operator *(int x, int y);
uint operator *(uint x, uint y);
long operator *(long x, long y);
ulong operator *(ulong x, ulong y);
float operator *(float x, float y);
double operator *(double x, double y);
decimal operator *(decimal x, decimal y);
```

Lorsque des règles de résolution de surcharge ([résolution de surcharge](expressions.md#overload-resolution)) sont appliquées à cet ensemble d’opérateurs, l’effet est de sélectionner le premier des opérateurs pour lesquels des conversions implicites existent à partir des types d’opérandes. Par exemple, pour l’opération `b * s`, où `b` est un `byte` et `s` est un `short`, la résolution de surcharge sélectionne `operator *(int,int)` comme meilleur opérateur. Par conséquent, l’effet est que `b` et `s` sont convertis en `int`et que le type du résultat est `int`. De même, pour l’opération `i * d`, où `i` est un `int` et `d` est un `double`, la résolution de surcharge sélectionne `operator *(double,double)` comme meilleur opérateur.

#### <a name="unary-numeric-promotions"></a>Promotions numériques unaires

La promotion numérique unaire se produit pour les opérandes des opérateurs unaires `+`, `-`et `~` prédéfinis. La promotion numérique unaire consiste simplement à convertir les opérandes de type `sbyte`, `byte`, `short`, `ushort`ou `char` en type `int`. En outre, pour l’opérateur unaire `-`, la promotion numérique unaire convertit les opérandes de type `uint` en type `long`.

#### <a name="binary-numeric-promotions"></a>Promotions numériques binaires

La promotion numérique binaire se produit pour les opérandes des opérateurs binaires prédéfinis `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `==`, `!=`, `>`, `<`, `>=`et `<=`. La promotion numérique binaire convertit implicitement les deux opérandes en un type commun qui, dans le cas des opérateurs non relationnels, devient également le type de résultat de l’opération. La promotion numérique binaire consiste à appliquer les règles suivantes, dans l’ordre où elles apparaissent ici :

*  Si l’un des opérandes est de type `decimal`, l’autre opérande est converti en type `decimal`, ou une erreur de liaison se produit si l’autre opérande est de type `float` ou `double`.
*  Sinon, si l’un des opérandes est de type `double`, l’autre opérande est converti en type `double`.
*  Sinon, si l’un des opérandes est de type `float`, l’autre opérande est converti en type `float`.
*  Sinon, si l’un des opérandes est de type `ulong`, l’autre opérande est converti en type `ulong`, ou une erreur de liaison se produit si l’autre opérande est de type `sbyte`, `short`, `int`ou `long`.
*  Sinon, si l’un des opérandes est de type `long`, l’autre opérande est converti en type `long`.
*  Sinon, si l’un des opérandes est de type `uint` et que l’autre opérande est de type `sbyte`, `short`ou `int`, les deux opérandes sont convertis en type `long`.
*  Sinon, si l’un des opérandes est de type `uint`, l’autre opérande est converti en type `uint`.
*  Sinon, les deux opérandes sont convertis en type `int`.

Notez que la première règle interdit toutes les opérations qui mélangent le type de `decimal` avec les types de `double` et de `float`. La règle suit du fait qu’il n’existe pas de conversion implicite entre le type de `decimal` et les types de `double` et de `float`.

Notez également qu’il n’est pas possible qu’un opérande soit de type `ulong` lorsque l’autre opérande est d’un type intégral signé. La raison est qu’il n’existe aucun type intégral qui peut représenter la plage complète de `ulong` ainsi que les types intégraux signés.

Dans les deux cas ci-dessus, une expression de cast peut être utilisée pour convertir explicitement un opérande en un type compatible avec l’autre opérande.

Dans l’exemple
```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (1.0 + percent / 100.0);
}
```
une erreur de liaison s’est produite, car une `decimal` ne peut pas être multipliée par un `double`. L’erreur est résolue par la conversion explicite du deuxième opérande en `decimal`, comme suit :

```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (decimal)(1.0 + percent / 100.0);
}
```

### <a name="lifted-operators"></a>Opérateurs levés

Les ***opérateurs levés*** permettent aux opérateurs prédéfinis et définis par l’utilisateur qui fonctionnent sur des types valeur non Nullable d’être également utilisés avec les formulaires Nullable de ces types. Les opérateurs levés sont construits à partir d’opérateurs prédéfinis et définis par l’utilisateur qui répondent à certaines exigences, comme décrit dans les rubriques suivantes :

*   Pour les opérateurs unaires

    ```csharp
    +  ++  -  --  !  ~
    ```

    une forme levée d’un opérateur existe si l’opérande et les types de résultats sont des types valeur non Nullable. Le formulaire levé est construit en ajoutant un modificateur de `?` unique à l’opérande et aux types de résultats. L’opérateur levé produit une valeur null si l’opérande a la valeur null. Dans le cas contraire, l’opérateur Levé désencapsule l’opérande, applique l’opérateur sous-jacent et encapsule le résultat.

*   Pour les opérateurs binaires

    ```csharp
    +  -  *  /  %  &  |  ^  <<  >>
    ```

    une forme levée d’un opérateur existe si l’opérande et les types de résultats sont tous des types valeur non Nullable. Le formulaire levé est construit en ajoutant un modificateur de `?` unique à chaque opérande et chaque type de résultat. L’opérateur levé produit une valeur null si l’un des opérandes ou les deux sont null (une exception étant le `&` et `|` opérateurs du type de `bool?`, comme décrit dans [opérateurs logiques booléens](expressions.md#boolean-logical-operators)). Dans le cas contraire, l’opérateur Levé désencapsule les opérandes, applique l’opérateur sous-jacent et encapsule le résultat.

*   Pour les opérateurs d’égalité

    ```csharp
    ==  !=
    ```

    une forme levée d’un opérateur existe si les types d’opérandes sont des types valeur non Nullable et si le type de résultat est `bool`. Le formulaire levé est construit en ajoutant un modificateur de `?` unique à chaque type d’opérande. L’opérateur levé considère deux valeurs NULL comme étant égales et une valeur NULL n’est pas égale à une valeur non null. Si les deux opérandes n’ont pas la valeur null, l’opérateur Levé désencapsule les opérandes et applique l’opérateur sous-jacent pour produire le résultat `bool`.

*   Pour les opérateurs relationnels

    ```csharp
    <  >  <=  >=
    ```

    une forme levée d’un opérateur existe si les types d’opérandes sont des types valeur non Nullable et si le type de résultat est `bool`. Le formulaire levé est construit en ajoutant un modificateur de `?` unique à chaque type d’opérande. L’opérateur levé produit la valeur `false` si l’un ou les deux opérandes ont la valeur null. Dans le cas contraire, l’opérateur Levé désencapsule les opérandes et applique l’opérateur sous-jacent pour produire le résultat `bool`.

## <a name="member-lookup"></a>Recherche de membres

Une recherche de membre est le processus par lequel la signification d’un nom dans le contexte d’un type est déterminée. Une recherche de membre peut se produire dans le cadre de l’évaluation d’un *simple_name* ([noms simples](expressions.md#simple-names)) ou d’un *member_access* ([accès aux membres](expressions.md#member-access)) dans une expression. Si le *simple_name* ou *member_access* se produit comme *primary_expression* d’un *invocation_expression* ([appels de méthode](expressions.md#method-invocations)), le membre est dit appelé.

Si un membre est une méthode ou un événement, ou s’il s’agit d’une constante, d’un champ ou d’une propriété d’un type délégué ([délégués](delegates.md)) ou du type `dynamic` ([type dynamique](types.md#the-dynamic-type)), le membre est dit être *appelé.*

La recherche de membres prend en compte non seulement le nom d’un membre, mais également le nombre de paramètres de type que le membre possède et si le membre est accessible. Dans le cadre de la recherche de membres, les méthodes génériques et les types génériques imbriqués ont le nombre de paramètres de type indiqué dans leurs déclarations respectives et tous les autres membres ont des paramètres de type zéro.

Une recherche de membre d’un nom `N` avec `K`paramètres de type  dans un type `T` est traitée comme suit :

*  Tout d’abord, un ensemble de membres accessibles nommés `N` est déterminé :
    * Si `T` est un paramètre de type, le jeu est l’Union des ensembles de membres accessibles nommés `N` dans chacun des types spécifiés en tant que contrainte principale ou contrainte secondaire ([contraintes de paramètre de type](classes.md#type-parameter-constraints)) pour `T`, ainsi que l’ensemble des membres accessibles nommés `N` dans `object`.
    * Dans le cas contraire, l’ensemble se compose de tous les membres accessibles ([accès aux membres](basic-concepts.md#member-access)) nommés `N` dans `T`, y compris les membres hérités et les membres accessibles nommés `N` dans `object`. Si `T` est un type construit, l’ensemble des membres est obtenu en remplaçant les arguments de type comme décrit dans [membres des types construits](classes.md#members-of-constructed-types). Les membres qui incluent un modificateur `override` sont exclus de l’ensemble.
*  Ensuite, si `K` est égal à zéro, tous les types imbriqués dont les déclarations incluent des paramètres de type sont supprimés. Si `K` n’est pas égal à zéro, tous les membres avec un nombre différent de paramètres de type sont supprimés. Notez que lorsque `K` est égal à zéro, les méthodes ayant des paramètres de type ne sont pas supprimées, car le processus d’inférence de type ([inférence de type](expressions.md#type-inference)) peut être en mesure de déduire les arguments de type.
*  Ensuite, si le membre est *appelé*, tous les membres qui ne sont pas*invocables* sont supprimés du jeu.
*  Ensuite, les membres qui sont masqués par d’autres membres sont supprimés du jeu. Pour chaque membre `S.M` dans l’ensemble, où `S` est le type dans lequel le membre `M` est déclaré, les règles suivantes sont appliquées :
    * Si `M` est une constante, un champ, une propriété, un événement ou un membre de l’énumération, tous les membres déclarés dans un type de base de `S` sont supprimés du jeu.
    * Si `M` est une déclaration de type, tous les types non déclarés dans un type de base de `S` sont supprimés du jeu, et toutes les déclarations de type avec le même nombre de paramètres de type que `M` déclarés dans un type de base de `S` sont supprimées de l’ensemble.
    * Si `M` est une méthode, tous les membres qui ne sont pas des méthodes déclarés dans un type de base de `S` sont supprimés du jeu.
*  Ensuite, les membres d’interface qui sont masqués par les membres de classe sont supprimés du jeu. Cette étape n’a d’effet que si `T` est un paramètre de type et `T` a une classe de base effective autre que `object` et un ensemble d’interfaces effectif non vide ([contraintes de paramètre de type](classes.md#type-parameter-constraints)). Pour chaque membre `S.M` dans l’ensemble, où `S` est le type dans lequel l' `M` membre est déclaré, les règles suivantes sont appliquées si `S` est une déclaration de classe autre que `object`:
    * Si `M` est un constante, un champ, une propriété, un événement, un membre de l’énumération ou une déclaration de type, tous les membres déclarés dans une déclaration d’interface sont supprimés du jeu.
    * Si `M` est une méthode, tous les membres qui ne sont pas des méthodes déclarés dans une déclaration d’interface sont supprimés du jeu, et toutes les méthodes ayant la même signature que `M` déclarées dans une déclaration d’interface sont supprimées de l’ensemble.
*  Enfin, si vous avez supprimé des membres masqués, le résultat de la recherche est déterminé :
    * Si l’ensemble se compose d’un seul membre qui n’est pas une méthode, ce membre est le résultat de la recherche.
    * Sinon, si l’ensemble contient uniquement des méthodes, ce groupe de méthodes est le résultat de la recherche.
    * Dans le cas contraire, la recherche est ambiguë et une erreur de liaison s’est produite.

Pour les recherches de membres dans les types autres que les paramètres de type et les interfaces, et les recherches de membres dans les interfaces qui sont strictement à héritage simple (chaque interface dans la chaîne d’héritage a exactement zéro ou une interface de base directe), l’effet des règles de recherche est le suivant : simplement, les membres dérivés masquent les membres de base avec le même nom ou la même signature. Ces recherches à héritage unique ne sont jamais ambiguës. Les ambiguïtés qui peuvent se produire à partir de recherches de membres dans des interfaces à héritage multiple sont décrites dans [accès aux membres d’interface](interfaces.md#interface-member-access).

### <a name="base-types"></a>Types de base

Dans le cadre de la recherche de membres, un type `T` est considéré comme ayant les types de base suivants :

*  Si `T` est `object`, `T` n’a pas de type de base.
*  Si `T` est un *enum_type*, les types de base de `T` sont les types de classe `System.Enum`, `System.ValueType`et `object`.
*  Si `T` est un *struct_type*, les types de base de `T` sont les types de classe `System.ValueType` et `object`.
*  Si `T` est un *class_type*, les types de base de `T` sont les classes de base de `T`, y compris le type de classe `object`.
*  Si `T` est un *INTERFACE_TYPE*, les types de base de `T` sont les interfaces de base de `T` et le type de classe `object`.
*  Si `T` est un *array_type*, les types de base de `T` sont les types de classe `System.Array` et `object`.
*  Si `T` est un *delegate_type*, les types de base de `T` sont les types de classe `System.Delegate` et `object`.

## <a name="function-members"></a>Membres de fonction

Les fonctions membres sont des membres qui contiennent des instructions exécutables. Les membres de fonction sont toujours membres de types et ne peuvent pas être membres d’espaces de noms. C#définit les catégories suivantes de membres de fonction :

*  Méthodes
*  Propriétés
*  Events
*  Indexeurs
*  Opérateurs définis par l’utilisateur
*  Constructeurs d’instance
*  Constructeurs statiques
*  Destructeurs

À l’exception des destructeurs et des constructeurs statiques (qui ne peuvent pas être appelés explicitement), les instructions contenues dans les fonctions membres sont exécutées par le biais d’appels de membres de fonction. La syntaxe réelle pour l’écriture d’un appel de membre de fonction dépend de la catégorie de membre de fonction particulière.

La liste d’arguments ([listes d’arguments](expressions.md#argument-lists)) d’un appel de membre de fonction fournit des valeurs réelles ou des références de variables pour les paramètres de la fonction membre.

Les appels de méthodes génériques peuvent utiliser l’inférence de type pour déterminer le jeu d’arguments de type à passer à la méthode. Ce processus est décrit dans [inférence de type](expressions.md#type-inference).

Les appels de méthodes, d’indexeurs, d’opérateurs et de constructeurs d’instance utilisent la résolution de surcharge pour déterminer l’ensemble de candidats des membres de fonction à appeler. Ce processus est décrit dans [résolution de surcharge](expressions.md#overload-resolution).

Une fois qu’un membre de fonction particulier a été identifié au moment de la liaison, éventuellement par la résolution de surcharge, le processus d’exécution réel de l’appel de la fonction membre est décrit dans [vérification de la résolution de surcharge dynamique au moment](expressions.md#compile-time-checking-of-dynamic-overload-resolution)de la compilation.

Le tableau suivant résume le traitement qui a lieu dans les constructions impliquant les six catégories de fonctions membres qui peuvent être explicitement appelées. Dans le tableau, `e`, `x`, `y`et `value` indiquent des expressions classées comme variables ou valeurs, `T` indique une expression classée comme un type, `F` est le nom simple d’une méthode et `P` est le nom simple d’une propriété.


| __Composer__     | __Exemple__    | __Description__ |
|-------------------|----------------|-----------------|
| Appel de méthode | `F(x,y)`       | La résolution de surcharge est appliquée pour sélectionner la meilleure méthode `F` dans la classe ou le struct conteneur. La méthode est appelée avec la liste d’arguments `(x,y)`. Si la méthode n’est pas `static`, l’expression d’instance est `this`. | 
|                   | `T.F(x,y)`     | La résolution de surcharge est appliquée pour sélectionner la meilleure méthode `F` dans la classe ou le struct `T`. Une erreur de liaison au moment de la liaison se produit si la méthode n’est pas `static`. La méthode est appelée avec la liste d’arguments `(x,y)`. | 
|                   | `e.F(x,y)`     | La résolution de surcharge est appliquée pour sélectionner la meilleure méthode F dans la classe, la structure ou l’interface donnée par le type de `e`. Une erreur de liaison au moment de la liaison se produit si la méthode est `static`. La méthode est appelée avec l’expression d’instance `e` et la liste d’arguments `(x,y)`. | 
| Accès à la propriété   | `P`            | L’accesseur `get` de la propriété `P` dans la classe ou le struct conteneur est appelé. Une erreur de compilation se produit si `P` est en écriture seule. Si `P` n’est pas `static`, l’expression d’instance est `this`. | 
|                   | `P = value`    | L’accesseur `set` de la propriété `P` dans la classe ou le struct conteneur est appelé avec la `(value)`de liste d’arguments. Une erreur de compilation se produit si `P` est en lecture seule. Si `P` n’est pas `static`, l’expression d’instance est `this`. | 
|                   | `T.P`          | L’accesseur `get` de la propriété `P` dans la classe ou le struct `T` est appelé. Une erreur de compilation se produit si `P` n’est pas `static` ou si `P` est en écriture seule. | 
|                   | `T.P = value`  | L’accesseur `set` de la propriété `P` dans la classe ou le struct `T` est appelé avec la `(value)`de la liste d’arguments. Une erreur de compilation se produit si `P` n’est pas `static` ou si `P` est en lecture seule. | 
|                   | `e.P`          | L’accesseur `get` de la propriété `P` dans la classe, la structure ou l’interface donnée par le type de `e` est appelé avec l’expression d’instance `e`. Une erreur de liaison s’est produite si `P` est `static` ou si `P` est en écriture seule. | 
|                   | `e.P = value`  | L’accesseur `set` de la propriété `P` dans la classe, la structure ou l’interface donnée par le type de `e` est appelé avec l’expression d’instance `e` et la liste d’arguments `(value)`. Une erreur de liaison s’est produite si `P` est `static` ou si `P` est en lecture seule. | 
| Accès aux événements      | `E += value`   | L’accesseur `add` de l’événement `E` dans la classe ou le struct conteneur est appelé. Si `E` n’est pas statique, l’expression d’instance est `this`. | 
|                   | `E -= value`   | L’accesseur `remove` de l’événement `E` dans la classe ou le struct conteneur est appelé. Si `E` n’est pas statique, l’expression d’instance est `this`. | 
|                   | `T.E += value` | L’accesseur `add` de l’événement `E` dans la classe ou le struct `T` est appelé. Une erreur de liaison au moment de la liaison se produit si `E` n’est pas statique. | 
|                   | `T.E -= value` | L’accesseur `remove` de l’événement `E` dans la classe ou le struct `T` est appelé. Une erreur de liaison au moment de la liaison se produit si `E` n’est pas statique. | 
|                   | `e.E += value` | L’accesseur `add` de l’événement `E` dans la classe, la structure ou l’interface donnée par le type de `e` est appelé avec l’expression d’instance `e`. Une erreur de liaison au moment de la liaison se produit si `E` est statique. | 
|                   | `e.E -= value` | L’accesseur `remove` de l’événement `E` dans la classe, la structure ou l’interface donnée par le type de `e` est appelé avec l’expression d’instance `e`. Une erreur de liaison au moment de la liaison se produit si `E` est statique. | 
| Accès aux indexeurs    | `e[x,y]`       | La résolution de surcharge est appliquée pour sélectionner le meilleur indexeur de la classe, du struct ou de l’interface donné par le type de e. L’accesseur `get` de l’indexeur est appelé avec l’expression d’instance `e` et la liste d’arguments `(x,y)`. Une erreur de liaison au moment de la liaison se produit si l’indexeur est en écriture seule. | 
|                   | `e[x,y] = value` | La résolution de surcharge est appliquée pour sélectionner le meilleur indexeur dans la classe, la structure ou l’interface spécifiée par le type de `e`. L’accesseur `set` de l’indexeur est appelé avec l’expression d’instance `e` et la liste d’arguments `(x,y,value)`. Une erreur de liaison s’est produite si l’indexeur est en lecture seule. | 
| Appel d’opérateur | `-x`         | La résolution de surcharge est appliquée pour sélectionner le meilleur opérateur unaire dans la classe ou le struct donné par le type de `x`. L’opérateur sélectionné est appelé avec la liste d’arguments `(x)`. | 
|                     | `x + y`      | La résolution de surcharge est appliquée pour sélectionner le meilleur opérateur binaire dans les classes ou les structs spécifiés par les types d' `x` et de `y`. L’opérateur sélectionné est appelé avec la liste d’arguments `(x,y)`. | 
| Appel du constructeur d’instance | `new T(x,y)` | La résolution de surcharge est appliquée pour sélectionner le constructeur d’instance le mieux adapté à la classe ou au struct `T`. Le constructeur d’instance est appelé avec la liste d’arguments `(x,y)`. | 

### <a name="argument-lists"></a>Listes d’arguments

Chaque membre de fonction et appel de délégué inclut une liste d’arguments qui fournit des valeurs réelles ou des références de variables pour les paramètres de la fonction membre. La syntaxe permettant de spécifier la liste d’arguments d’un appel de membre de fonction dépend de la catégorie de membre de fonction :

*  Pour les constructeurs d’instance, les méthodes, les indexeurs et les délégués, les arguments sont spécifiés en tant que *argument_list*, comme décrit ci-dessous. Pour les indexeurs, lors de l’appel de l’accesseur `set`, la liste d’arguments comprend également l’expression spécifiée comme opérande droit de l’opérateur d’assignation.
*  Pour les propriétés, la liste d’arguments est vide lors de l’appel de l’accesseur `get`, et se compose de l’expression spécifiée comme opérande droit de l’opérateur d’assignation lors de l’appel de l’accesseur `set`.
*  Pour les événements, la liste d’arguments se compose de l’expression spécifiée comme opérande droit de l’opérateur `+=` ou `-=`.
*  Pour les opérateurs définis par l’utilisateur, la liste d’arguments se compose du seul opérande de l’opérateur unaire ou des deux opérandes de l’opérateur binaire.

Les arguments des propriétés ([Propriétés](classes.md#properties)), événements ([événements](classes.md#events)) et opérateurs définis par l’utilisateur ([opérateurs](classes.md#operators)) sont toujours passés en tant que paramètres de valeur ([paramètres de valeur](classes.md#value-parameters)). Les arguments des indexeurs ([indexeurs](classes.md#indexers)) sont toujours passés en tant que paramètres de valeur ([paramètres de valeur](classes.md#value-parameters)) ou tableaux de paramètres ([tableaux de paramètres](classes.md#parameter-arrays)). Les paramètres de référence et de sortie ne sont pas pris en charge pour ces catégories de membres de fonction.

Les arguments d’un constructeur d’instance, d’une méthode, d’un indexeur ou d’un appel de délégué sont spécifiés en tant que *argument_list*:

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

Un *argument_list* se compose d’un ou plusieurs *arguments*, séparés par des virgules. Chaque argument se compose d’un *argument_name* facultatif suivi d’un *argument_value*. Un *argument* avec un *argument_name* est appelé ***argument nommé***, alors qu’un argument sans *argument_name* *est un* ***argument positionnel***. L’affichage d’un argument positionnel après un argument nommé dans un *argument_list*constitue une erreur.

La *argument_value* peut prendre l’une des formes suivantes :

*  *Expression*indiquant que l’argument est passé en tant que paramètre de valeur ([paramètres de valeur](classes.md#value-parameters)).
*  Le mot clé `ref` suivi d’un *variable_reference* ([références de variable](variables.md#variable-references)), indiquant que l’argument est passé en tant que paramètre de référence (paramètres de[référence](classes.md#reference-parameters)). Une variable doit être assignée de manière définitive ([assignation définie](variables.md#definite-assignment)) avant de pouvoir être passée en tant que paramètre de référence. Le mot clé `out` suivi d’un *variable_reference* ([références de variable](variables.md#variable-references)), indiquant que l’argument est passé en tant que paramètre de sortie (paramètres de[sortie](classes.md#output-parameters)). Une variable est considérée comme assignée définitivement ([assignation définie](variables.md#definite-assignment)) à la suite d’un appel de membre de fonction dans lequel la variable est passée comme paramètre de sortie.

#### <a name="corresponding-parameters"></a>Paramètres correspondants

Pour chaque argument dans une liste d’arguments, il doit y avoir un paramètre correspondant dans le membre de fonction ou le délégué appelé.

La liste de paramètres utilisée dans les éléments suivants est déterminée comme suit :

*  Pour les méthodes virtuelles et les indexeurs définis dans les classes, la liste de paramètres est choisie à partir de la déclaration ou de la substitution la plus spécifique, en commençant par le type statique du récepteur, et en recherchant dans ses classes de base.
*  Pour les méthodes d’interface et les indexeurs, la liste de paramètres est choisie en fonction de la définition la plus spécifique du membre, en commençant par le type d’interface et en parcourant les interfaces de base. Si aucune liste de paramètres unique n’est trouvée, une liste de paramètres avec des noms inaccessibles et aucun paramètre facultatif n’est construite, de sorte que les appels ne peuvent pas utiliser de paramètres nommés ou omettre des arguments facultatifs.
*  Pour les méthodes partielles, la liste de paramètres de la déclaration de méthode partielle de définition est utilisée.
*  Pour tous les autres membres de fonction et délégués, il n’existe qu’une seule liste de paramètres, qui est celle utilisée.

La position d’un argument ou d’un paramètre est définie en tant que nombre d’arguments ou de paramètres qui la précèdent dans la liste d’arguments ou la liste de paramètres.

Les paramètres correspondants pour les arguments de membre de fonction sont établis comme suit :

*  Arguments dans le *argument_list* de constructeurs d’instance, de méthodes, d’indexeurs et de délégués :
    * Un argument positionnel dans lequel un paramètre fixe se produit à la même position dans la liste de paramètres correspond à ce paramètre.
    * Un argument positionnel d’un membre de fonction avec un tableau de paramètres appelé dans sa forme normale correspond au tableau de paramètres, qui doit se trouver à la même position dans la liste de paramètres.
    * Argument positionnel d’un membre de fonction avec un tableau de paramètres appelé dans sa forme développée, où aucun paramètre fixe ne se trouve à la même position dans la liste de paramètres, correspond à un élément dans le tableau de paramètres.
    * Un argument nommé correspond au paramètre du même nom dans la liste de paramètres.
    * Pour les indexeurs, lors de l’appel de l’accesseur `set`, l’expression spécifiée comme opérande droit de l’opérateur d’assignation correspond au paramètre de `value` implicite de la déclaration d’accesseur `set`.
*  Pour les propriétés, lors de l’appel de l’accesseur `get` il n’y a pas d’arguments. Lors de l’appel de l’accesseur `set`, l’expression spécifiée comme opérande droit de l’opérateur d’assignation correspond au paramètre de `value` implicite de la déclaration d’accesseur `set`.
*  Pour les opérateurs unaires définis par l’utilisateur (y compris les conversions), l’opérande unique correspond au paramètre unique de la déclaration d’opérateur.
*  Pour les opérateurs binaires définis par l’utilisateur, l’opérande de gauche correspond au premier paramètre, tandis que l’opérande droit correspond au deuxième paramètre de la déclaration de l’opérateur.

#### <a name="run-time-evaluation-of-argument-lists"></a>Évaluation au moment de l’exécution des listes d’arguments

Pendant le traitement au moment de l’exécution d’un appel de membre de fonction ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), les expressions ou les références de variable d’une liste d’arguments sont évaluées dans l’ordre, de gauche à droite, comme suit :

*  Pour un paramètre de valeur, l’expression d’argument est évaluée et une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) en type de paramètre correspondant est effectuée. La valeur résultante devient la valeur initiale du paramètre de valeur dans l’appel de membre de fonction.
*  Pour un paramètre de référence ou de sortie, la référence de variable est évaluée et l’emplacement de stockage résultant devient l’emplacement de stockage représenté par le paramètre dans l’appel de membre de fonction. Si la référence variable donnée en tant que paramètre de référence ou de sortie est un élément de tableau d’un *reference_type*, un contrôle au moment de l’exécution est effectué pour garantir que le type d’élément du tableau est identique au type du paramètre. En cas d’échec de cette vérification, une `System.ArrayTypeMismatchException` est levée.

Les méthodes, les indexeurs et les constructeurs d’instance peuvent déclarer leur paramètre le plus à droite comme un tableau de paramètres ([tableaux de paramètres](classes.md#parameter-arrays)). Ces fonctions sont appelées soit sous leur forme normale, soit sous leur forme développée en fonction de ce qui est applicable ([fonction membre applicable](expressions.md#applicable-function-member)) :

*  Lorsqu’un membre de fonction avec un tableau de paramètres est appelé dans sa forme normale, l’argument donné pour le tableau de paramètres doit être une expression unique qui est implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) en type de tableau de paramètres. Dans ce cas, le tableau de paramètres agit précisément comme un paramètre de valeur.
*  Lorsqu’un membre de fonction avec un tableau de paramètres est appelé dans sa forme développée, l’appel doit spécifier zéro, un ou plusieurs arguments positionnels pour le tableau de paramètres, où chaque argument est une expression implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) en type d’élément du tableau de paramètres. Dans ce cas, l’appel crée une instance du type de tableau de paramètres avec une longueur correspondant au nombre d’arguments, initialise les éléments de l’instance de tableau avec les valeurs d’argument données et utilise l’instance de tableau nouvellement créée comme argument.

Les expressions d’une liste d’arguments sont toujours évaluées dans l’ordre dans lequel elles sont écrites. Ainsi, l’exemple
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
génère la sortie
```console
x = 0, y = 1, z = 2
x = 4, y = -1, z = 3
```

Les règles de covariance de tableau ([covariance de tableau](arrays.md#array-covariance)) autorisent une valeur d’un type de tableau `A[]` être une référence à une instance d’un type de tableau `B[]`, à condition qu’il existe une conversion de référence implicite de `B` à `A`. En raison de ces règles, lorsqu’un élément de tableau d’un *reference_type* est passé en tant que paramètre de référence ou de sortie, un contrôle au moment de l’exécution est nécessaire pour garantir que le type d’élément réel du tableau est identique à celui du paramètre. Dans l’exemple
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
le deuxième appel de `F` entraîne la levée d’une `System.ArrayTypeMismatchException`, car le type d’élément réel de `b` est `string` et non `object`.

Lorsqu’un membre de fonction avec un tableau de paramètres est appelé dans sa forme développée, l’appel est traité exactement comme si une expression de création de tableau avec un initialiseur de tableau ([expressions de création de tableau](expressions.md#array-creation-expressions)) a été insérée autour des paramètres développés. Par exemple, étant donné la déclaration
```csharp
void F(int x, int y, params object[] args);
```
les appels suivants de la forme développée de la méthode
```csharp
F(10, 20);
F(10, 20, 30, 40);
F(10, 20, 1, "hello", 3.0);
```
correspondre exactement à
```csharp
F(10, 20, new object[] {});
F(10, 20, new object[] {30, 40});
F(10, 20, new object[] {1, "hello", 3.0});
```

En particulier, Notez qu’un tableau vide est créé lorsqu’il n’y a aucun argument donné pour le tableau de paramètres.

Lorsque des arguments sont omis d’un membre de fonction avec des paramètres facultatifs correspondants, les arguments par défaut de la déclaration de membre de fonction sont passés implicitement. Étant donné qu’elles sont toujours constantes, leur évaluation n’aura pas d’impact sur l’ordre d’évaluation des arguments restants.

### <a name="type-inference"></a>Inférence de type

Quand une méthode générique est appelée sans spécifier d’arguments de type, un processus d' ***inférence de type*** tente de déduire des arguments de type pour l’appel. La présence de l’inférence de type permet d’utiliser une syntaxe plus pratique pour appeler une méthode générique et permet au programmeur d’éviter de spécifier des informations de type redondantes. Par exemple, à partir de la déclaration de méthode :
```csharp
class Chooser
{
    static Random rand = new Random();

    public static T Choose<T>(T first, T second) {
        return (rand.Next(2) == 0)? first: second;
    }
}
```
Il est possible d’appeler la méthode `Choose` sans spécifier explicitement un argument de type :
```csharp
int i = Chooser.Choose(5, 213);                 // Calls Choose<int>

string s = Chooser.Choose("foo", "bar");        // Calls Choose<string>
```

Par le biais de l’inférence de type, les arguments de type `int` et `string` sont déterminés à partir des arguments à la méthode.

L’inférence de type se produit dans le cadre du traitement au moment de la liaison d’un appel de méthode ([appel de méthode](expressions.md#method-invocations)) et a lieu avant l’étape de résolution de surcharge de l’appel. Lorsqu’un groupe de méthodes particulier est spécifié dans un appel de méthode et qu’aucun argument de type n’est spécifié dans le cadre de l’appel de méthode, l’inférence de type est appliquée à chaque méthode générique dans le groupe de méthodes. Si l’inférence de type est réussie, les arguments de type déduits sont utilisés pour déterminer les types d’arguments pour la résolution de surcharge suivante. Si la résolution de surcharge choisit une méthode générique comme une méthode à appeler, les arguments de type inférés sont utilisés en tant qu’arguments de type réels pour l’appel. Si l’inférence de type pour une méthode particulière échoue, cette méthode ne participe pas à la résolution de surcharge. L’échec de l’inférence de type, en soi-même, ne provoque pas d’erreur au moment de la liaison. Toutefois, il arrive souvent à une erreur de liaison lorsque la résolution de surcharge ne parvient pas à trouver les méthodes applicables.

Si le nombre d’arguments fourni est différent du nombre de paramètres dans la méthode, l’inférence échoue immédiatement. Dans le cas contraire, supposez que la méthode générique possède la signature suivante :
```csharp
Tr M<X1,...,Xn>(T1 x1, ..., Tm xm)
```

Avec un appel de méthode de la forme `M(E1...Em)` la tâche d’inférence de type consiste à trouver des arguments de type uniques `S1...Sn` pour chacun des paramètres de type `X1...Xn` afin que l’appel `M<S1...Sn>(E1...Em)` devienne valide.

Pendant le processus d’inférence, chaque paramètre de type `Xi` est *fixé* à un type particulier `Si` ou non *résolu* avec un ensemble de *limites*associées. Chaque limite est un type `T`. Initialement, chaque variable de type `Xi` n’est pas résolue avec un ensemble vide de limites.

L’inférence de type a lieu en plusieurs phases. Chaque phase essaiera de déduire les arguments de type pour d’autres variables de type en fonction des résultats de la phase précédente. La première phase effectue quelques inférences initiales des limites, tandis que la deuxième phase résout des variables de type à des types spécifiques et déduit d’autres limites. La deuxième phase peut être répétée plusieurs fois.

*Remarque :* L’inférence de type a lieu non seulement quand une méthode générique est appelée. L’inférence de type pour la conversion de groupes de méthodes est décrite dans [inférence de type pour la conversion de groupes de méthodes](expressions.md#type-inference-for-conversion-of-method-groups) et la recherche du meilleur type commun d’un ensemble d’expressions est décrite dans [recherche du meilleur type commun d’un ensemble d’expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions).

#### <a name="the-first-phase"></a>La première phase

Pour chacun des arguments de méthode `Ei`:

*   Si `Ei` est une fonction anonyme, une *inférence de type de paramètre explicite* ([inférences de type de paramètre explicite](expressions.md#explicit-parameter-type-inferences)) est effectuée de `Ei` à `Ti`
*   Sinon, si `Ei` a un type `U` et `xi` est un paramètre de valeur, une *inférence de liaison inférieure* est effectuée *de* `U` *à* `Ti`.
*   Sinon, si `Ei` a un type `U` et `xi` est un paramètre `ref` ou `out`, une *inférence exacte* est effectuée *de* `U` *à* `Ti`.
*   Sinon, aucune inférence n’est effectuée pour cet argument.


#### <a name="the-second-phase"></a>La deuxième phase

La deuxième phase se déroule comme suit :

*   Toutes les variables de type non *fixes* `Xi` qui ne *dépendent pas de* ([dépendance](expressions.md#dependence)) tout `Xj` sont fixes ([Correction](expressions.md#fixing)).
*   S’il n’existe aucune variable de type de ce type, toutes les variables de type non *fixes* `Xi` sont *fixes* pour lesquelles tous les éléments suivants sont détenus :
    *   Il existe au moins une variable de type `Xj` qui dépend de `Xi`
    *   `Xi` a un ensemble non vide de limites
*   S’il n’existe aucune variable de type de ce type et qu’il existe encore des variables de type non *fixes* , l’inférence de type échoue.
*   Sinon, s’il n’existe aucune variable de type non *fixe* , l’inférence de type est réussie.
*   Sinon, pour tous les arguments `Ei` avec le type `Ti` de paramètre correspondant où les *types de sortie* ([types de sortie](expressions.md#output-types)) contiennent des variables `Xj` de type *non fixes*, mais pas les *types d’entrée* ([types d’entrée](expressions.md#input-types)), l’*inférence de type de sortie* ([inférences de type de sortie](expressions.md#output-type-inferences)) est effectuée *de* `Ei` *à* `Ti`. La deuxième phase est ensuite répétée.

#### <a name="input-types"></a>Types d’entrée

Si `E` est un groupe de méthodes ou une fonction anonyme implicitement typée et `T` est un type délégué ou un type d’arborescence d’expression, tous les types de paramètres de `T` sont des *types d’entrée* de `E` *avec le type* `T`.

####  <a name="output-types"></a>Types de sorties

Si `E` est un groupe de méthodes ou une fonction anonyme et `T` est un type délégué ou un type d’arborescence d’expression, le type de retour de `T` est un *type de sortie* `E` *avec le type* `T`.

#### <a name="dependence"></a>Dépendance

Une variable de type non *fixe* `Xi` *dépend directement* d’une variable de type non fixe `Xj` si, pour un argument, `Ek` avec le type `Tk` `Xj` se produit dans un *type d’entrée* de `Ek` avec le type `Tk` et `Xi` se produit dans un *type de sortie* `Ek` avec le type `Tk`.

`Xj` *dépend de* `Xi` si `Xj` *dépend directement* de `Xi` ou si `Xi` *dépend directement* de `Xk` et `Xk` *dépend de* `Xj`. Par conséquent, « dépend de » est la fermeture transitive mais non réflexive de « dépend directement de ».

#### <a name="output-type-inferences"></a>Inférences de type de sortie

Une *inférence de type de sortie* est effectuée *à partir* d’une expression `E` *à* un type `T` de la façon suivante :

*  Si `E` est une fonction anonyme avec un type de retour déduit `U`[(type de retour déduit](expressions.md#inferred-return-type)) et `T` est un type délégué ou un type d’arborescence d’expression avec le type de retour `Tb`, une *inférence à la limite inférieure* ([inférences à liaison inférieure](expressions.md#lower-bound-inferences)) est effectuée *de* `U` *à* `Tb`.
*  Sinon, si `E` est un groupe de méthodes et que `T` est un type délégué ou un type d’arborescence d’expression avec des types de paramètres `T1...Tk` et `Tb`de type de retour, et que la résolution de surcharge de `E` avec les types `T1...Tk` produit une méthode unique avec le type de retour `U`, une *inférence de liaison inférieure* est effectuée *de* `U` *à* `Tb`.
*  Sinon, si `E` est une expression de type `U`, une *inférence de liaison inférieure* est effectuée *de* `U` *à* `T`.
*  Dans le cas contraire, aucune inférence n’est effectuée.

#### <a name="explicit-parameter-type-inferences"></a>Inférences de type de paramètre explicites

Une *inférence de type de paramètre explicite* est effectuée *à partir* d’une expression `E` *à* un type `T` de la façon suivante :

*  Si `E` est une fonction anonyme explicitement typée avec des types de paramètres `U1...Uk` et `T` est un type délégué ou un type d’arborescence d’expression avec des types de paramètres `V1...Vk` puis pour chaque `Ui` une *inférence exacte* ([inférences exactes](expressions.md#exact-inferences)) est apportée *de* `Ui` *à* la `Vi`correspondante.

#### <a name="exact-inferences"></a>Inférences exactes

Une *inférence exacte* *d'* un type `U` *à* un type `V` est effectuée comme suit :

*  Si `V` est l’un des `Xi` non *fixes* , `U` est ajouté au jeu de limites exactes pour `Xi`.

*  Dans le cas contraire, définit `V1...Vk` et `U1...Uk` sont déterminés en vérifiant si l’un des cas suivants s’applique :

   *  `V` est un type de tableau `V1[...]` et `U` est un type de tableau `U1[...]` du même rang
   *  `V` est le type `V1?` et `U` est le type `U1?`
   *  `V` est un type construit `C<V1...Vk>`et `U` est un type construit `C<U1...Uk>`

   Si l’un de ces cas s’applique, une *inférence exacte* est effectuée *à partir de* chaque `Ui` *vers* le `Vi`correspondant.

*  Sinon, aucune inférence n’est effectuée.

#### <a name="lower-bound-inferences"></a>Inférences à limite inférieure

Une *inférence de liaison inférieure* *d'* un type `U` *à* un type `V` est effectuée comme suit :

*  Si `V` est l’un des `Xi` non *fixes* , `U` est ajouté au jeu de limites inférieures pour `Xi`.
*  Sinon, si `V` est le type `V1?`et `U` est le type `U1?`, une inférence de limite inférieure est effectuée de `U1` à `V1`.
*  Dans le cas contraire, définit `U1...Uk` et `V1...Vk` sont déterminés en vérifiant si l’un des cas suivants s’applique :
   *  `V` est un type de tableau `V1[...]` et `U` est un type de tableau `U1[...]` (ou un paramètre de type dont le type de base effectif est `U1[...]`) du même rang
   *  `V` est l’un des `IEnumerable<V1>`, `ICollection<V1>` ou `IList<V1>` et `U` est un type tableau unidimensionnel `U1[]`(ou un paramètre de type dont le type de base effectif est `U1[]`)
   *  `V` est une classe, un struct, une interface ou un type délégué construit `C<V1...Vk>` et il existe un type unique `C<U1...Uk>` tel que `U` (ou, si `U` est un paramètre de type, sa classe de base effective ou un membre de son ensemble d’interfaces effectif) est identique à, hérite de (directement ou indirectement) ou implémente (directement ou indirectement) `C<U1...Uk>`.

      (La restriction « unicité » signifie que, dans l’interface de cas `C<T> {} class U: C<X>, C<Y> {}`, aucune inférence n’est effectuée lors de l’inférence de `U` à `C<T>`, car `U1` peut être `X` ou `Y`.)

   Si l’un de ces cas s’applique, une inférence est effectuée *à partir de* chaque `Ui` *vers* le `Vi` correspondant, comme suit :

   *  Si `Ui` n’est pas connu comme étant un type référence, une *inférence exacte* est effectuée
   *  Sinon, si `U` est un type tableau, une *inférence de liaison inférieure* est effectuée
   *  Sinon, si `V` est `C<V1...Vk>`, l’inférence dépend du paramètre de type i-Th de `C`:
      *  S’il s’agit d’un covariant, une *inférence de liaison inférieure* est effectuée.
      *  S’il est contravariant, une *inférence de* la limite supérieure est effectuée.
      *  S’il s’agit d’un invariant, une *inférence exacte* est effectuée.
*  Dans le cas contraire, aucune inférence n’est effectuée.

#### <a name="upper-bound-inferences"></a>Inférences à la limite supérieure

Une *inférence à la limite supérieure* *d'* un type `U` *à* un type `V` est effectuée comme suit :

*  Si `V` est l’un des `Xi` non *fixes* , `U` est ajouté au jeu de limites supérieures pour `Xi`.
*  Dans le cas contraire, définit `V1...Vk` et `U1...Uk` sont déterminés en vérifiant si l’un des cas suivants s’applique :
   *  `U` est un type de tableau `U1[...]` et `V` est un type de tableau `V1[...]` du même rang
   *  `U` est l’un des `IEnumerable<Ue>`, `ICollection<Ue>` ou `IList<Ue>` et `V` est un type de tableau unidimensionnel `Ve[]`
   *  `U` est le type `U1?` et `V` est le type `V1?`
   *  `U` est construit classe, struct, interface ou type délégué `C<U1...Uk>` et `V` est une classe, un struct, une interface ou un type délégué qui est identique à, hérite de (directement ou indirectement), ou implémente (directement ou indirectement) un type unique `C<V1...Vk>`

      (La restriction d’unicité signifie que si nous avons `interface C<T>{} class V<Z>: C<X<Z>>, C<Y<Z>>{}`, aucune inférence n’est effectuée lors de l’inférence de `C<U1>` à `V<Q>`. Les inférences ne sont pas faites de `U1` à `X<Q>` ou `Y<Q>`.)

   Si l’un de ces cas s’applique, une inférence est effectuée *à partir de* chaque `Ui` *vers* le `Vi` correspondant, comme suit :
   *  Si `Ui` n’est pas connu comme étant un type référence, une *inférence exacte* est effectuée
   *  Sinon, si `V` est un type tableau, une *inférence à la limite supérieure* est effectuée
   *  Sinon, si `U` est `C<U1...Uk>`, l’inférence dépend du paramètre de type i-Th de `C`:
      *  S’il s’agit d’un covariant, une *inférence de* la limite supérieure est effectuée.
      *  S’il est contravariant, une *inférence de liaison inférieure* est effectuée.
      *  S’il s’agit d’un invariant, une *inférence exacte* est effectuée.
*  Dans le cas contraire, aucune inférence n’est effectuée.   

#### <a name="fixing"></a>Résolution

Une variable de type non *fixe* `Xi` avec un ensemble de limites est *fixe* comme suit :

*  L’ensemble de *types candidats* `Uj` commence par le jeu de tous les types dans le jeu de limites de `Xi`.
*  Nous examinons ensuite chaque limite pour `Xi` à son tour : pour chaque `U` à liaison exacte de `Xi` tous les types `Uj` qui ne sont pas identiques à `U` sont supprimés du jeu de candidats. Pour chaque `U` de limite inférieure de `Xi` tous les types `Uj` pour lesquels il n’existe *pas* de conversion implicite de `U` sont supprimés du jeu de candidats. Pour chaque `U` de la limite supérieure de `Xi` tous les types `Uj` à partir duquel il n’existe *pas* de conversion implicite en `U` sont supprimés du jeu de candidats.
*  Si parmi les types candidats restants `Uj` il existe un type unique `V` à partir duquel il existe une conversion implicite vers tous les autres types candidats, `Xi` est fixé à `V`.
*  Sinon, l’inférence de type échoue.

#### <a name="inferred-return-type"></a>Type de retour déduit

Le type de retour déduit d’une fonction anonyme `F` est utilisé pendant l’inférence de type et la résolution de surcharge. Le type de retour déduit ne peut être déterminé que pour une fonction anonyme dans laquelle tous les types de paramètre sont connus, soit parce qu’ils sont explicitement donnés, fournis par le biais d’une conversion de fonction anonyme ou déduits pendant l’inférence de type sur un générique englobant appel de méthode.

Le ***type de résultat inféré*** est déterminé comme suit :

*  Si le corps de `F` est une *expression* qui a un type, le type de résultat déduit de `F` est le type de cette expression.
*  Si le corps de `F` est un *bloc* et que l’ensemble d’expressions dans les instructions `return` du bloc a un type le mieux courant `T` ([recherche du meilleur type commun d’un ensemble d’expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)), le type de résultat déduit de `F` est `T`.
*  Dans le cas contraire, un type de résultat ne peut pas être déduit pour `F`.

Le ***type de retour déduit*** est déterminé comme suit :

*  Si `F` est Async et que le corps de `F` est une expression classifiée comme Nothing ([classifications d’expression](expressions.md#expression-classifications)) ou un bloc d’instructions où aucune instruction return n’a d’expressions, le type de retour déduit est `System.Threading.Tasks.Task`
*  Si `F` est asynchrone et a un type de résultat déduit `T`, le type de retour déduit est `System.Threading.Tasks.Task<T>`.
*  Si `F` est non asynchrone et a un type de résultat déduit `T`, le type de retour déduit est `T`.
*  Dans le cas contraire, un type de retour ne peut pas être déduit pour `F`.

En guise d’exemple d’inférence de type impliquant des fonctions anonymes, considérez la méthode d’extension `Select` déclarée dans la classe `System.Linq.Enumerable` :
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

En supposant que l’espace de noms `System.Linq` a été importé avec une clause `using` et une classe `Customer` avec une propriété `Name` de type `string`, la méthode `Select` peut être utilisée pour sélectionner les noms d’une liste de clients :
```csharp
List<Customer> customers = GetCustomerList();
IEnumerable<string> names = customers.Select(c => c.Name);
```

L’appel de méthode d’extension ([appel de méthode d’extension](expressions.md#extension-method-invocations)) de `Select` est traité en réécrivant l’appel à un appel de méthode statique :
```csharp
IEnumerable<string> names = Enumerable.Select(customers, c => c.Name);
```

Étant donné que les arguments de type n’ont pas été spécifiés explicitement, l’inférence de type est utilisée pour déduire les arguments de type. Tout d’abord, l’argument `customers` est lié au paramètre `source`, ce qui déduit `T` à `Customer`. Ensuite, à l’aide du processus d’inférence de type de fonction anonyme décrit ci-dessus, `c` reçoit le type `Customer`, et l’expression `c.Name` est liée au type de retour du paramètre `selector`, en déduisant `S` à `string`. Ainsi, l’appel est équivalent à
```csharp
Sequence.Select<Customer,string>(customers, (Customer c) => c.Name)
```
et le résultat est de type `IEnumerable<string>`.

L’exemple suivant montre comment l’inférence de type de fonction anonyme permet aux informations de type de « circuler » entre les arguments dans un appel de méthode générique. À partir de la méthode :
```csharp
static Z F<X,Y,Z>(X value, Func<X,Y> f1, Func<Y,Z> f2) {
    return f2(f1(value));
}
```

Inférence de type pour l’appel :
```csharp
double seconds = F("1:15:30", s => TimeSpan.Parse(s), t => t.TotalSeconds);
```
continue comme suit : tout d’abord, l’argument `"1:15:30"` est lié au paramètre `value`, en déduisant les `X` à `string`. Ensuite, le paramètre de la première fonction anonyme, `s`, reçoit le type déduit `string`, et l’expression `TimeSpan.Parse(s)` est liée au type de retour de `f1`, en déduisant `Y` à `System.TimeSpan`. Enfin, le paramètre de la deuxième fonction anonyme, `t`, reçoit le type déduit `System.TimeSpan`, et l’expression `t.TotalSeconds` est liée au type de retour de `f2`, en déduisant `Z` à `double`. Ainsi, le résultat de l’appel est de type `double`.

#### <a name="type-inference-for-conversion-of-method-groups"></a>Inférence de type pour la conversion de groupes de méthodes

Comme pour les appels de méthodes génériques, l’inférence de type doit également être appliquée quand un groupe de méthodes `M` contenant une méthode générique est converti en un type délégué donné `D` ([conversions de groupe de méthodes](conversions.md#method-group-conversions)). Pour une méthode donnée
```csharp
Tr M<X1...Xn>(T1 x1 ... Tm xm)
```
et le groupe de méthodes `M` assigné au type délégué `D` la tâche de l’inférence de type consiste à rechercher des arguments de type `S1...Sn` afin que l’expression :
```csharp
M<S1...Sn>
```
devient compatible ([déclarations déléguées](delegates.md#delegate-declarations)) avec `D`.

Contrairement à l’algorithme d’inférence de type pour les appels de méthode générique, dans ce cas, il n’y a que des *types*d’arguments, aucune *expression*d’argument. En particulier, il n’y a pas de fonctions anonymes et, par conséquent, n’a pas besoin de plusieurs phases d’inférence.

Au lieu de cela, tous les `Xi` sont considérés comme non *résolus*et une *inférence de liaison inférieure* est effectuée *à partir* de chaque type d’argument `Uj` de `D` *au* type de paramètre correspondant `Tj` de `M`. Si pour l’une des `Xi` aucune limite n’a été trouvée, l’inférence de type échoue. Dans le cas contraire, toutes les `Xi` sont *fixes* à la `Si`correspondante, qui sont le résultat de l’inférence de type.

#### <a name="finding-the-best-common-type-of-a-set-of-expressions"></a>Recherche du meilleur type commun d’un ensemble d’expressions

Dans certains cas, un type commun doit être déduit pour un ensemble d’expressions. En particulier, les types d’éléments des tableaux implicitement typés et les types de retour des fonctions anonymes avec des corps de *bloc* sont détectés de cette façon.

Intuitivement, étant donné un ensemble d’expressions `E1...Em` cette inférence doit être équivalente à l’appel d’une méthode
```csharp
Tr M<X>(X x1 ... X xm)
```
avec les `Ei` comme arguments.

Plus précisément, l’inférence commence par une variable de type non *fixe* `X`. Les *inférences de type de sortie* sont ensuite effectuées *à partir de* chaque `Ei` *vers* `X`. Enfin, `X` est *fixe* et, en cas de réussite, le type résultant `S` est le meilleur type commun qui en résulte pour les expressions. Si ce `S` n’existe pas, les expressions n’ont pas le meilleur type commun.

### <a name="overload-resolution"></a>Résolution de surcharge

La résolution de surcharge est un mécanisme de liaison-temps permettant de sélectionner le meilleur membre de fonction à appeler à partir d’une liste d’arguments et d’un jeu de membres de fonction candidats. La résolution de surcharge sélectionne la fonction membre à appeler dans les contextes suivants C#dans :

*  Appel d’une méthode nommée dans un *invocation_expression* ([appels de méthode](expressions.md#method-invocations)).
*  Appel d’un constructeur d’instance nommé dans une *object_creation_expression* ([expressions de création d’objet](expressions.md#object-creation-expressions)).
*  Appel d’un accesseur d’indexeur via un *element_access* ([accès à l’élément](expressions.md#element-access)).
*  Appel d’un opérateur prédéfini ou défini par l’utilisateur référencé dans une expression (résolution de[surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution) et [résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)).

Chacun de ces contextes définit l’ensemble des membres de fonction candidats et la liste d’arguments de manière unique, comme décrit en détail dans les sections répertoriées ci-dessus. Par exemple, l’ensemble de candidats pour un appel de méthode n’inclut pas les méthodes marquées `override` ([recherche de membre](expressions.md#member-lookup)) et les méthodes d’une classe de base ne sont pas candidates si une méthode dans une classe dérivée est applicable ([appels de méthode](expressions.md#method-invocations)).

Une fois que les membres de la fonction candidate et la liste d’arguments ont été identifiés, la sélection du meilleur membre de la fonction est la même dans tous les cas :

*  Compte tenu de l’ensemble des fonctions membres candidates applicables, le meilleur membre de fonction de cet ensemble est localisé. Si le jeu contient un seul membre de fonction, ce membre de fonction est le meilleur membre de fonction. Dans le cas contraire, le meilleur membre de fonction est le membre de fonction qui est mieux que tous les autres membres de fonction par rapport à la liste d’arguments donnée, à condition que chaque membre de fonction soit comparé à tous les autres membres de fonction en utilisant les règles dans un [meilleur membre](expressions.md#better-function-member)de fonction. S’il n’y a pas exactement un membre de fonction qui est mieux que tous les autres membres de fonction, l’appel de membre de fonction est ambigu et une erreur de liaison s’est produite.

Les sections suivantes définissent les significations exactes des termes du ***membre de fonction applicable*** et du ***meilleur membre de fonction***.

#### <a name="applicable-function-member"></a>Membre de fonction applicable

Un membre de fonction est considéré comme un ***membre de fonction applicable*** en ce qui concerne une liste d’arguments `A` lorsque toutes les conditions suivantes sont vraies :

*  Chaque argument de `A` correspond à un paramètre dans la déclaration de membre de fonction comme décrit dans les [paramètres correspondants](expressions.md#corresponding-parameters), et tout paramètre auquel aucun argument correspond est un paramètre facultatif.
*  Pour chaque argument dans `A`, le mode de passage de paramètre de l’argument (c’est-à-dire, value, `ref`ou `out`) est identique au mode de passage de paramètre du paramètre correspondant, et
   *  pour un paramètre de valeur ou un tableau de paramètres, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir de l’argument vers le type du paramètre correspondant, ou
   *  pour un paramètre `ref` ou `out`, le type de l’argument est identique au type du paramètre correspondant. Après tout, un paramètre `ref` ou `out` est un alias pour l’argument passé.

Pour un membre de fonction qui comprend un tableau de paramètres, si le membre de fonction est applicable par les règles ci-dessus, il est dit s’il est applicable dans sa ***forme normale***. Si un membre de fonction qui comprend un tableau de paramètres n’est pas applicable dans sa forme normale, la fonction membre peut à la place être applicable dans sa ***forme développée***:

*  La forme développée est construite en remplaçant le tableau de paramètres dans la déclaration de membre de fonction par zéro ou plusieurs paramètres de valeur du type d’élément du tableau de paramètres, de telle sorte que le nombre d’arguments dans la liste d’arguments `A` corresponde au nombre total de paramètres. Si `A` a moins d’arguments que le nombre de paramètres fixes dans la déclaration de membre de fonction, la forme développée de la fonction membre ne peut pas être construite et n’est donc pas applicable.
*  Dans le cas contraire, la forme développée s’applique si pour chaque argument dans `A` le mode de passage de paramètre de l’argument est identique au mode de passage de paramètre du paramètre correspondant, et
   *  pour un paramètre de valeur fixe ou un paramètre de valeur créé par l’expansion, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir du type de l’argument vers le type du paramètre correspondant, ou
   *  pour un paramètre `ref` ou `out`, le type de l’argument est identique au type du paramètre correspondant.

#### <a name="better-function-member"></a>Meilleure fonction membre

Pour déterminer le meilleur membre de la fonction, une liste d’arguments tronquée a est construite, contenant uniquement les expressions d’arguments, dans l’ordre dans lequel elles apparaissent dans la liste d’arguments d’origine.

Les listes de paramètres pour chacun des fonctions candidates sont construites de la façon suivante :

*  Le formulaire développé est utilisé si le membre de fonction ne s’applique qu’au formulaire développé.
*  Les paramètres facultatifs sans arguments correspondants sont supprimés de la liste de paramètres
*  Les paramètres sont réorganisés afin qu’ils se produisent à la même position que l’argument correspondant dans la liste d’arguments.

À partir d’une liste d’arguments `A` avec un ensemble d’expressions d’argument `{E1, E2, ..., En}` et deux membres de fonction applicables `Mp` et `Mq` avec des types de paramètres `{P1, P2, ..., Pn}` et `{Q1, Q2, ..., Qn}`, `Mp` est défini comme étant une ***meilleure fonction membre*** que `Mq` si

*  pour chaque argument, la conversion implicite de `Ex` en `Qx` n’est pas meilleure que la conversion implicite de `Ex` en `Px`, et
*  pour au moins un argument, la conversion de `Ex` en `Px` est meilleure que la conversion de `Ex` en `Qx`.

Lorsque vous effectuez cette évaluation, si `Mp` ou `Mq` est applicable dans sa forme développée, `Px` ou `Qx` fait référence à un paramètre dans la forme développée de la liste de paramètres.

Si les séquences de type de paramètre `{P1, P2, ..., Pn}` et `{Q1, Q2, ..., Qn}` sont équivalentes (c’est-à-dire que chaque `Pi` a une conversion d’identité en `Qi`correspondante), les règles de rupture de liaison suivantes sont appliquées, dans l’ordre, pour déterminer le meilleur membre de fonction.

*  Si `Mp` est une méthode non générique et que `Mq` est une méthode générique, `Mp` est mieux que `Mq`.
*  Dans le cas contraire, si `Mp` est applicable dans sa forme normale et `Mq` possède un tableau `params` et s’applique uniquement dans sa forme développée, `Mp` est préférable à `Mq`.
*  Sinon, si `Mp` a plus de paramètres déclarés que `Mq`, `Mp` est mieux que `Mq`. Cela peut se produire si les deux méthodes ont des `params` des tableaux et s’appliquent uniquement à leurs formes développées.
*  Sinon, si tous les paramètres de `Mp` ont un argument correspondant, alors que les arguments par défaut doivent être remplacés par au moins un paramètre facultatif dans `Mq`, `Mp` est mieux que `Mq`.
*  Sinon, si `Mp` a des types de paramètres plus spécifiques que `Mq`, `Mp` est mieux que `Mq`. Laissez `{R1, R2, ..., Rn}` et `{S1, S2, ..., Sn}` représenter les types de paramètres non instanciés et non développés de `Mp` et `Mq`. les types de paramètres de `Mp`sont plus spécifiques que `Mq`si, pour chaque paramètre, `Rx` n’est pas moins spécifique que `Sx`, et, pour au moins un paramètre, `Rx` est plus spécifique que `Sx`:
   *  Un paramètre de type est moins spécifique qu’un paramètre de non-type.
   *  De manière récursive, un type construit est plus spécifique qu’un autre type construit (avec le même nombre d’arguments de type) si au moins un argument de type est plus spécifique et qu’aucun argument de type n’est moins spécifique que l’argument de type correspondant dans l’autre.
   *  Un type tableau est plus spécifique qu’un autre type tableau (avec le même nombre de dimensions) si le type d’élément du premier est plus spécifique que le type d’élément du second.
*  Dans le cas contraire, si un membre est un opérateur non levé et que l’autre est un opérateur levé, la meilleure est la meilleure.
*  Dans le cas contraire, aucune des fonctions membres n’est meilleure.

#### <a name="better-conversion-from-expression"></a>Meilleure conversion à partir de l’expression

Dans le cas d’une conversion implicite `C1` qui convertit une expression `E` en un type `T1`, et une `C2` de conversion implicite qui convertit une expression `E` en type `T2`, `C1` est une ***meilleure conversion*** que `C2` si `E` ne correspond pas exactement à `T2` et au moins l’un des éléments suivants :

* `E` correspond exactement à `T1` ([expression correspondant exactement](expressions.md#exactly-matching-expression))
* `T1` est une meilleure cible de conversion que `T2` ([meilleure cible de conversion](expressions.md#better-conversion-target))

#### <a name="exactly-matching-expression"></a>Expression correspondant exactement

À partir d’une expression `E` et d’un type `T`, `E` correspond exactement à `T` si l’un des éléments suivants est présent :

*  `E` a un type `S`et une conversion d’identité existe de `S` à `T`
*  `E` est une fonction anonyme, `T` est soit un type délégué `D`, soit un type d’arborescence d’expression `Expression<D>` et l’un des éléments suivants :
   *  Un type de retour déduit `X` existe pour `E` dans le contexte de la liste de paramètres de `D` ([type de retour déduit](expressions.md#inferred-return-type)), et il existe une conversion d’identité de `X` vers le type de retour de `D`
   *  `E` est non asynchrone et `D` a un type de retour `Y` ou `E` est Async et `D` a un type de retour `Task<Y>`et l’un des éléments suivants :
      * Le corps de `E` est une expression qui correspond exactement à `Y`
      * Le corps de `E` est un bloc d’instruction où chaque instruction return retourne une expression qui correspond exactement à `Y`

#### <a name="better-conversion-target"></a>Meilleure cible de conversion

Étant donné deux types différents `T1` et `T2`, `T1` est une meilleure cible de conversion que `T2` si aucune conversion implicite de `T2` en `T1` n’existe, et au moins l’un des éléments suivants :

*  Une conversion implicite d' `T1` en `T2` existe
*  `T1` est un type délégué `D1` ou un type d’arborescence d’expression `Expression<D1>`, `T2` est soit un type délégué `D2`, soit un type d’arborescence d’expression `Expression<D2>`, `D1` a un type de retour `S1` et l’un des éléments suivants :
   * le `D2` est un retour void
   * `D2` a un type de retour `S2`et `S1` est une meilleure cible de conversion que `S2`
*  `T1` est `Task<S1>`, `T2` est `Task<S2>`, et `S1` est une meilleure cible de conversion que `S2`
*  `T1` est `S1` ou `S1?` où `S1` est un type intégral signé, et `T2` est `S2` ou `S2?`, où `S2` est un type intégral non signé. Plus précisément :
   * `S1` est `sbyte` et `S2` est `byte`, `ushort`, `uint`ou `ulong`
   * `S1` est `short` et `S2` est `ushort`, `uint`ou `ulong`
   * `S1` est `int` et `S2` est `uint`, ou `ulong`
   * `S1` est `long` et `S2` est `ulong`

#### <a name="overloading-in-generic-classes"></a>Surcharge dans les classes génériques

Tandis que les signatures telles qu’elles sont déclarées doivent être uniques, il est possible que la substitution des arguments de type génère des signatures identiques. Dans ce cas, les règles de résolution de surcharge ci-dessus sélectionnent le membre le plus spécifique.

Les exemples suivants montrent des surcharges qui sont valides et non valides conformément à cette règle :

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

### <a name="compile-time-checking-of-dynamic-overload-resolution"></a>Vérification de la résolution de surcharge dynamique au moment de la compilation

Pour la plupart des opérations liées de manière dynamique, l’ensemble des candidats possibles pour la résolution est inconnu au moment de la compilation. Dans certains cas, toutefois, l’ensemble de candidats est connu au moment de la compilation :

*  Appels de méthode statiques avec des arguments dynamiques
*  Appels de méthode d’instance où le récepteur n’est pas une expression dynamique
*  Appels de l’indexeur où le récepteur n’est pas une expression dynamique
*  Appels de constructeur avec des arguments dynamiques

Dans ces cas, un contrôle limité au moment de la compilation est effectué pour chaque candidat afin de déterminer si l’un d’eux peut éventuellement s’appliquer au moment de l’exécution. Cette vérification se compose des étapes suivantes :

*  Inférence de type partiel : tout argument de type qui ne dépend pas directement ou indirectement sur un argument de type `dynamic` est déduit à l’aide des règles d' [inférence de type](expressions.md#type-inference). Les arguments de type restants sont inconnus.
*  Vérification de l’applicabilité partielle : l’applicabilité est vérifiée en fonction du [membre de fonction applicable](expressions.md#applicable-function-member), mais les paramètres dont les types sont inconnus sont ignorés.
*  Si aucun candidat ne réussit ce test, une erreur de compilation se produit.

### <a name="function-member-invocation"></a>Appel de membre de fonction

Cette section décrit le processus qui a lieu au moment de l’exécution pour appeler un membre de fonction particulier. Il est supposé qu’un processus de liaison a déjà déterminé le membre particulier à appeler, éventuellement en appliquant la résolution de surcharge à un ensemble de membres de fonction candidats.

Dans le cadre de la description du processus d’appel, les membres de fonction sont divisés en deux catégories :

*  Fonctions membres statiques. Il s’agit des constructeurs d’instance, des méthodes statiques, des accesseurs de propriété statique et des opérateurs définis par l’utilisateur. Les fonctions membres statiques sont toujours non virtuelles.
*  Membres de fonction d’instance. Il s’agit des méthodes d’instance, des accesseurs de propriété d’instance et des accesseurs d’indexeurs. Les fonctions membres de fonction d’instance sont non virtuelles ou virtuelles et sont toujours appelées sur une instance particulière. L’instance est calculée par une expression d’instance et devient accessible dans le membre de fonction en tant que `this` ([cet accès](expressions.md#this-access)).

Le traitement au moment de l’exécution d’un appel de membre de fonction se compose des étapes suivantes, où `M` est le membre de fonction et, si `M` est un membre d’instance, `E` est l’expression d’instance :

*  Si `M` est un membre de fonction statique :
   * La liste d’arguments est évaluée comme décrit dans [listes d’arguments](expressions.md#argument-lists).
   * `M` est appelé.

*  Si `M` est un membre de fonction d’instance déclaré dans un *Value_type*:
   * `E` est évalué. Si cette évaluation provoque une exception, aucune autre étape n’est exécutée.
   * Si `E` n’est pas classée en tant que variable, une variable locale temporaire du type de `E`est créée et la valeur de `E` est assignée à cette variable. `E` est ensuite reclassifiée comme une référence à cette variable locale temporaire. La variable temporaire est accessible en tant que `this` dans `M`, mais pas de quelque manière que ce soit. Ainsi, uniquement lorsque `E` est une véritable variable, l’appelant peut observer les modifications apportées par `M` à `this`.
   * La liste d’arguments est évaluée comme décrit dans [listes d’arguments](expressions.md#argument-lists).
   * `M` est appelé. La variable référencée par `E` devient la variable référencée par `this`.

*  Si `M` est un membre de fonction d’instance déclaré dans un *reference_type*:
   * `E` est évalué. Si cette évaluation provoque une exception, aucune autre étape n’est exécutée.
   * La liste d’arguments est évaluée comme décrit dans [listes d’arguments](expressions.md#argument-lists).
   * Si le type de `E` est un *Value_type*, une conversion boxing ([conversions boxing](types.md#boxing-conversions)) est effectuée pour convertir `E` en type `object`, et `E` est considéré comme étant de type `object` dans les étapes suivantes. Dans ce cas, `M` ne peut être qu’un membre de `System.Object`.
   * La valeur de `E` est vérifiée comme valide. Si la valeur de `E` est `null`, une `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.
   * L’implémentation de la fonction membre à appeler est déterminée :
     * Si le type au moment de la liaison de `E` est une interface, la fonction membre à appeler est l’implémentation de `M` fournie par le type au moment de l’exécution de l’instance référencée par `E`. Cette fonction membre est déterminée en appliquant les règles de mappage d’interface ([mappage d’interface](interfaces.md#interface-mapping)) pour déterminer l’implémentation de `M` fournie par le type au moment de l’exécution de l’instance référencée par `E`.
     * Sinon, si `M` est un membre de fonction virtuelle, la fonction membre à appeler est l’implémentation de `M` fournie par le type au moment de l’exécution de l’instance référencée par `E`. Cette fonction membre est déterminée en appliquant les règles de détermination de l’implémentation la plus dérivée ([méthodes virtuelles](classes.md#virtual-methods)) de `M` en ce qui concerne le type au moment de l’exécution de l’instance référencée par `E`.
     * Dans le cas contraire, `M` est un membre de fonction non virtuel, et la fonction membre à appeler est `M` elle-même.
   * L’implémentation de la fonction membre déterminée à l’étape ci-dessus est appelée. L’objet référencé par `E` devient l’objet référencé par `this`.

#### <a name="invocations-on-boxed-instances"></a>Appels sur les instances boxed

Un membre de fonction implémenté dans un *Value_type* peut être appelé via une instance boxed de qui *Value_type* dans les situations suivantes :

*  Quand le membre de fonction est un `override` d’une méthode héritée du type `object` et est appelé par le biais d’une expression d’instance de type `object`.
*  Quand la fonction membre est une implémentation d’un membre de fonction d’interface et qu’elle est appelée par le biais d’une expression d’instance d’un *INTERFACE_TYPE*.
*  Quand la fonction membre est appelée par l’intermédiaire d’un délégué.

Dans ces situations, l’instance boxed est considérée comme contenant une variable de la *Value_type*, et cette variable devient la variable référencée par `this` dans l’appel de membre de fonction. En particulier, cela signifie que lorsqu’un membre de fonction est appelé sur une instance boxed, la fonction membre peut modifier la valeur contenue dans l’instance boxed.

## <a name="primary-expressions"></a>Expressions primaires

Les expressions primaires incluent les formes les plus simples des expressions.

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

Les expressions primaires sont réparties entre *array_creation_expression*s et *primary_no_array_creation_expression*s. Le traitement de l’expression de création de tableau de cette manière, plutôt que son affichage avec les autres formulaires d’expression simples, permet à la grammaire d’interdire le code potentiellement confus, tel que
```csharp
object o = new int[3][1];
```
qui, autrement, serait interprété comme
```csharp
object o = (new int[3])[1];
```

### <a name="literals"></a>Littéraux

Une *primary_expression* qui se compose d’un *littéral* ([littéraux](lexical-structure.md#literals)) est classée en tant que valeur.


### <a name="interpolated-strings"></a>Chaînes interpolées

Un *interpolated_string_expression* se compose d’un signe `$` suivi d’un littéral de chaîne normal ou textuel, où les trous, délimités par `{` et `}`, encadrent les expressions et les spécifications de mise en forme. Une expression de chaîne interpolée est le résultat d’une *interpolated_string_literal* qui a été divisée en jetons individuels, comme décrit dans [littéraux de chaîne interpolés](lexical-structure.md#interpolated-string-literals).

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

La *constant_expression* d’une interpolation doit avoir une conversion implicite en `int`.

Une *interpolated_string_expression* est classée en tant que valeur. Si elle est immédiatement convertie en `System.IFormattable` ou `System.FormattableString` avec une conversion de chaîne interpolée implicite ([conversions de chaînes interpolées implicites](conversions.md#implicit-interpolated-string-conversions)), l’expression de chaîne interpolée a ce type. Dans le cas contraire, il a le type `string`.

Si le type d’une chaîne interpolée est `System.IFormattable` ou `System.FormattableString`, la signification est un appel à `System.Runtime.CompilerServices.FormattableStringFactory.Create`. Si le type est `string`, la signification de l’expression est un appel à `string.Format`. Dans les deux cas, la liste d’arguments de l’appel se compose d’un littéral de chaîne de format avec des espaces réservés pour chaque interpolation, et d’un argument pour chaque expression qui correspond aux espaces réservés.

Le littéral de chaîne de format est construit comme suit, où `N` est le nombre d’interpolations dans le *interpolated_string_expression*:

*  Si un *interpolated_regular_string_whole* ou un *interpolated_verbatim_string_whole* suit le signe `$`, le littéral de chaîne de format est ce jeton.
*  Dans le cas contraire, le littéral de chaîne de format se compose des éléments suivants : 
   *  Premier *interpolated_regular_string_start* ou *interpolated_verbatim_string_start*
   *  Puis, pour chaque nombre `I` de `0` à `N-1`: 
      * Représentation décimale de `I`
      * Ensuite, si l' *interpolation* correspondante a un *constant_expression*, un `,` (virgule) suivi de la représentation décimale de la valeur de la *constant_expression*
      * Ensuite, le *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_mid* ou *interpolated_verbatim_string_end* qui suit immédiatement l’interpolation correspondante.

Les arguments suivants sont simplement les *expressions* des *interpolations* (le cas échéant), dans l’ordre.

TODO : exemples.


### <a name="simple-names"></a>Noms simples

Un *simple_name* se compose d’un identificateur, éventuellement suivi d’une liste d’arguments de type :

```antlr
simple_name
    : identifier type_argument_list?
    ;
```

Une *simple_name* se présente sous la forme `I` ou sous la forme `I<A1,...,Ak>`, où `I` est un identificateur unique et `<A1,...,Ak>` est un *type_argument_list*facultatif. Si aucun *type_argument_list* n’est spécifié, envisagez `K` à zéro. La *simple_name* est évaluée et classée comme suit :

*  Si `K` est égal à zéro et que le *simple_name* apparaît dans un *bloc* et que l’espace de déclaration de variable locale ([déclarations](basic-concepts.md#declarations)) du *bloc*(ou du *bloc*englobant) contient une variable locale, un paramètre ou une constante portant le nom `I`, le *simple_name* fait référence à cette variable locale, ce paramètre ou cette constante et est classifié comme une variable ou une valeur.
*  Si `K` est égal à zéro et que le *simple_name* apparaît dans le corps d’une déclaration de méthode générique et si cette déclaration comprend un paramètre de type portant le nom `I`, le *simple_name* fait référence à ce paramètre de type.
*  Dans le cas contraire, pour chaque type d’instance `T` ([le type d’instance](classes.md#the-instance-type)), en commençant par le type d’instance de la déclaration de type englobante immédiate et en continuant avec le type d’instance de chaque déclaration de classe ou de struct englobante (le cas échéant) :
   *  Si `K` est égal à zéro et que la déclaration de `T` comprend un paramètre de type avec le nom `I`, le *simple_name* fait référence à ce paramètre de type.
   *  Sinon, si une recherche de membre ([recherche de membre](expressions.md#member-lookup)) de `I` dans `T` avec `K`arguments de type  produit une correspondance :
      * Si `T` est le type d’instance du type de classe ou de struct immédiatement englobant et que la recherche identifie une ou plusieurs méthodes, le résultat est un groupe de méthodes avec une expression d’instance associée de `this`. Si une liste d’arguments de type a été spécifiée, elle est utilisée pour appeler une méthode générique ([appels de méthode](expressions.md#method-invocations)).
      * Sinon, si `T` est le type d’instance du type de classe ou de struct immédiatement englobant, si la recherche identifie un membre d’instance et si la référence se produit dans le corps d’un constructeur d’instance, une méthode d’instance ou un accesseur d’instance, le résultat est le même qu’un accès de membre ([accès aux membres](expressions.md#member-access)) de la `this.I`de formulaire. Cela peut se produire uniquement lorsque `K` est égal à zéro.
      * Dans le cas contraire, le résultat est le même qu’un accès de membre ([accès aux membres](expressions.md#member-access)) de la forme `T.I` ou `T.I<A1,...,Ak>`. Dans ce cas, il s’agit d’une erreur de liaison au moment de la liaison pour que le *simple_name* fait référence à un membre d’instance.

*  Sinon, pour chaque espace de noms `N`, en commençant par l’espace de noms dans lequel le *simple_name* se produit, en continuant avec chaque espace de noms englobant (le cas échéant) et en terminant par l’espace de noms global, les étapes suivantes sont évaluées jusqu’à ce qu’une entité soit localisée :
   *  Si `K` est égal à zéro et que `I` est le nom d’un espace de noms dans `N`, alors :
      * Si l’emplacement où le *simple_name* se produit est entouré d’une déclaration d’espace de noms pour `N` et que la déclaration d’espace de noms contient un *extern_alias_directive* ou *using_alias_directive* qui associe le nom `I` à un espace de noms ou à un type, le *simple_name* est ambigu et une erreur de compilation se produit.
      * Dans le cas contraire, le *simple_name* fait référence à l’espace de noms nommé `I` dans `N`.
   *  Dans le cas contraire, si `N` contient un type accessible dont le nom `I` et `K`paramètres de type  , alors :
      * Si `K` est égal à zéro et que l’emplacement où le *simple_name* se produit est entouré d’une déclaration d’espace de noms pour `N` et que la déclaration d’espace de noms contient une *extern_alias_directive* ou une *using_alias_directive* qui associe le nom `I` à un espace de noms ou à un type, le *simple_name* est ambigu et une erreur de compilation se produit.
      * Sinon, le *namespace_or_type_name* fait référence au type construit avec les arguments de type donnés.
   *  Sinon, si l’emplacement où le *simple_name* se produit est entouré d’une déclaration d’espace de noms pour `N`:
      * Si `K` est égal à zéro et que la déclaration d’espace de noms contient une *extern_alias_directive* ou *using_alias_directive* qui associe le nom `I` à un espace de noms ou un type importé, le *simple_name* fait référence à cet espace de noms ou type.
      * Sinon, si les espaces de noms et les déclarations de type importés par le *using_namespace_directive*s et *using_static_directive*s de la déclaration d’espace de noms contiennent exactement un type accessible ou un membre statique non-extension ayant le nom `I` et `K`paramètres de type  , le *simple_name* fait référence à ce type ou membre construit avec les arguments de type donnés.
      * Dans le cas contraire, si les espaces de noms et les types importés par le *using_namespace_directive*s de la déclaration d’espace de noms contiennent plusieurs types accessibles ou des membres statiques de méthode de non-extension ayant un nom `I` et `K`paramètres de type  , le *simple_name* est ambigu et une erreur se produit.

   Notez que cette étape entière est exactement parallèle à l’étape correspondante dans le traitement d’un *namespace_or_type_name* ([espace de noms et noms de type](basic-concepts.md#namespace-and-type-names)).

*  Dans le cas contraire, la *simple_name* n’est pas définie et une erreur de compilation se produit.


### <a name="parenthesized-expressions"></a>Expressions entre parenthèses

Une *parenthesized_expression* se compose d’une *expression* placée entre parenthèses.

```antlr
parenthesized_expression
    : '(' expression ')'
    ;
```

Une *parenthesized_expression* est évaluée en évaluant l' *expression* entre parenthèses. Si l' *expression* entre parenthèses désigne un espace de noms ou un type, une erreur de compilation se produit. Dans le cas contraire, le résultat de l' *parenthesized_expression* est le résultat de l’évaluation de l' *expression*contenue.

### <a name="member-access"></a>Accès au membre

Un *member_access* se compose d’un *primary_expression*, d’un *predefined_type*ou d’un *qualified_alias_member*, suivi d’un jeton «`.`», suivi d’un *identificateur*, éventuellement suivi d’un *type_argument_list*.

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

La *qualified_alias_member* production est définie dans les [qualificateurs d’alias d’espace de noms](namespaces.md#namespace-alias-qualifiers).

Une *member_access* se présente sous la forme `E.I` ou sous la forme `E.I<A1, ..., Ak>`, où `E` est une expression primaire, `I` est un identificateur unique et `<A1, ..., Ak>` est un *type_argument_list*facultatif. Si aucun *type_argument_list* n’est spécifié, envisagez `K` à zéro.

Un *member_access* avec un *primary_expression* de type `dynamic` est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas, le compilateur classe l’accès au membre comme un accès à la propriété de type `dynamic`. Les règles ci-dessous pour déterminer la signification de l' *member_access* sont ensuite appliquées au moment de l’exécution, à l’aide du type au moment de l’exécution et non du type de compilation du *primary_expression*. Si la classification au moment de l’exécution provoque un groupe de méthodes, l’accès au membre doit être le *primary_expression* d’un *invocation_expression*.

La *member_access* est évaluée et classée comme suit :

*  Si `K` est égal à zéro et `E` est un espace de noms et `E` contient un espace de noms imbriqué avec le nom `I`, le résultat est l’espace de noms.
*  Sinon, si `E` est un espace de noms et `E` contient un type accessible avec le nom `I` et `K`paramètres de type  , le résultat est ce type construit avec les arguments de type donnés.
*  Si `E` est un *predefined_type* ou un *primary_expression* classé comme type, si `E` n’est pas un paramètre de type, et si une recherche de membre ([recherche de membre](expressions.md#member-lookup)) de `I` dans `E` avec `K`paramètres de type  produit une correspondance, `E.I` est évalué et classé comme suit :
   *  Si `I` identifie un type, le résultat est ce type construit avec les arguments de type donnés.
   *  Si `I` identifie une ou plusieurs méthodes, le résultat est un groupe de méthodes sans expression d’instance associée. Si une liste d’arguments de type a été spécifiée, elle est utilisée pour appeler une méthode générique ([appels de méthode](expressions.md#method-invocations)).
   *  Si `I` identifie une propriété `static`, le résultat est un accès à une propriété sans expression d’instance associée.
   *  Si `I` identifie un champ de `static` :
      * Si le champ est `readonly` et que la référence se produit à l’extérieur du constructeur statique de la classe ou du struct dans lequel le champ est déclaré, le résultat est une valeur, à savoir la valeur du champ statique `I` dans `E`.
      * Dans le cas contraire, le résultat est une variable, à savoir le champ statique `I` dans `E`.
   *  Si `I` identifie un événement `static` :
      * Si la référence se produit dans la classe ou le struct dans lequel l’événement est déclaré, et si l’événement a été déclaré sans *event_accessor_declarations* ([événements](classes.md#events)), `E.I` est traité exactement comme si `I` était un champ statique.
      * Dans le cas contraire, le résultat est un accès à un événement sans expression d’instance associée.
   *  Si `I` identifie une constante, le résultat est une valeur, à savoir la valeur de cette constante.
    * Si `I` identifie un membre de l’énumération, le résultat est une valeur, à savoir la valeur de ce membre de l’énumération.
    * Dans le cas contraire, `E.I` est une référence de membre non valide et une erreur de compilation se produit.
*  Si `E` est un accès à la propriété, un indexeur, une variable ou une valeur, le type de qui est `T`et une recherche de membre ([recherche de membre](expressions.md#member-lookup)) de `I` dans `T` avec `K`arguments de type  produit une correspondance, `E.I` est évalué et classé comme suit :
   *  Premièrement, si `E` est un accès à une propriété ou un indexeur, la valeur de la propriété ou de l’indexeur est obtenue ([valeurs des expressions](expressions.md#values-of-expressions)) et `E` est reclassifiée comme une valeur.
   *  Si `I` identifie une ou plusieurs méthodes, le résultat est un groupe de méthodes avec une expression d’instance associée de `E`. Si une liste d’arguments de type a été spécifiée, elle est utilisée pour appeler une méthode générique ([appels de méthode](expressions.md#method-invocations)).
   *  Si `I` identifie une propriété d’instance,
      * Si `E` est `this`, `I` identifie une propriété implémentée automatiquement ([Propriétés implémentées automatiquement](classes.md#automatically-implemented-properties)) sans accesseur Set, et la référence se produit dans un constructeur d’instance pour un type de classe ou de struct `T`, le résultat est une variable, à savoir le champ de stockage masqué pour la propriété automatique fournie par `I` dans l’instance de `T` donnée par `this`.
      * Dans le cas contraire, le résultat est un accès à une propriété avec une expression d’instance associée de `E`.
   *  Si `T` est un *class_type* et `I` identifie un champ d’instance de ce *class_type*:
      * Si la valeur de `E` est `null`, une `System.NullReferenceException` est levée.
      * Sinon, si le champ est `readonly` et que la référence se produit en dehors d’un constructeur d’instance de la classe dans laquelle le champ est déclaré, le résultat est une valeur, à savoir la valeur du champ `I` dans l’objet référencé par `E`.
      * Dans le cas contraire, le résultat est une variable, à savoir le champ `I` dans l’objet référencé par `E`.
   *  Si `T` est un *struct_type* et `I` identifie un champ d’instance de ce *struct_type*:
      * Si `E` est une valeur, ou si le champ est `readonly` et que la référence se produit en dehors d’un constructeur d’instance du struct dans lequel le champ est déclaré, le résultat est une valeur, à savoir la valeur du champ `I` dans l’instance de struct donnée par `E`.
      * Dans le cas contraire, le résultat est une variable, à savoir le champ `I` dans l’instance de struct donnée par `E`.
   *  Si `I` identifie un événement d’instance :
      * Si la référence se produit dans la classe ou le struct dans lequel l’événement est déclaré, et si l’événement a été déclaré sans *event_accessor_declarations* ([événements](classes.md#events)) et si la référence ne se produit pas comme la partie gauche d’un opérateur `+=` ou `-=`, `E.I` est traité exactement comme si `I` était un champ d’instance.
      * Dans le cas contraire, le résultat est un accès aux événements avec une expression d’instance associée de `E`.
*  Sinon, une tentative est faite pour traiter `E.I` en tant qu’appel de méthode d’extension ([appels de méthode d’extension](expressions.md#extension-method-invocations)). En cas d’échec, `E.I` est une référence de membre non valide et une erreur de liaison s’est produite.

#### <a name="identical-simple-names-and-type-names"></a>Noms simples et noms de types identiques

Dans un accès aux membres de la forme `E.I`, si `E` est un identificateur unique et si la signification de `E` en tant que *simple_name* ([noms simples](expressions.md#simple-names)) est une constante, un champ, une propriété, une variable locale ou un paramètre avec le même type que la signification de `E` comme *type_name* ([espace de noms et noms de type](basic-concepts.md#namespace-and-type-names)), les deux significations possibles de `E` sont autorisées. Les deux significations possibles de `E.I` ne sont jamais ambiguës, car `I` doit nécessairement être un membre du type `E` dans les deux cas. En d’autres termes, la règle autorise simplement l’accès aux membres statiques et aux types imbriqués de `E` où une erreur de compilation se produirait autrement. Par exemple :
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

#### <a name="grammar-ambiguities"></a>Ambiguïtés grammaticales

Les productions pour *simple_name* ([noms simples](expressions.md#simple-names)) et *member_access* ([accès aux membres](expressions.md#member-access)) peuvent donner lieu à des ambiguïtés dans la grammaire des expressions. Par exemple, l’instruction suivante :
```csharp
F(G<A,B>(7));
```
peut être interprété comme un appel à `F` avec deux arguments, `G < A` et `B > (7)`. Elle peut également être interprétée comme un appel à `F` avec un argument, qui est un appel à une méthode générique `G` avec deux arguments de type et un argument normal.

Si une séquence de jetons peut être analysée (en contexte) comme un *simple_name* ([noms simples](expressions.md#simple-names)), *member_access* ([accès aux membres](expressions.md#member-access)) ou *pointer_member_access* ([accès au membre pointeur](unsafe-code.md#pointer-member-access)) se terminant par un *type_argument_list* ([arguments de type](types.md#type-arguments)), le jeton qui suit immédiatement le jeton `>` de fermeture est examiné. S’il s’agit de l’un des
```csharp
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
```
le *type_argument_list* est ensuite conservé dans le cadre du *simple_name*, *member_access* ou *pointer_member_access* et toute autre analyse possible de la séquence de jetons est ignorée. Dans le cas contraire, le *type_argument_list* n’est pas considéré comme faisant partie du *simple_name*, *member_access* ou *pointer_member_access*, même s’il n’y a pas d’autre analyse possible de la séquence de jetons. Notez que ces règles ne sont pas appliquées lors de l’analyse d’un *type_argument_list* dans un *namespace_or_type_name* ([noms d’espaces de noms et de types](basic-concepts.md#namespace-and-type-names)). L'instruction
```csharp
F(G<A,B>(7));
```
en fonction de cette règle, sera interprété comme un appel à `F` avec un argument, qui est un appel à une méthode générique `G` avec deux arguments de type et un argument normal. Les instructions
```csharp
F(G < A, B > 7);
F(G < A, B >> 7);
```
est interprété comme un appel à `F` avec deux arguments. L'instruction
```csharp
x = F < A > +y;
```
est interprété comme un opérateur inférieur à, supérieur à et opérateur unaire plus, comme si l’instruction avait été écrite `x = (F < A) > (+y)`, et non comme un *simple_name* avec un *type_argument_list* suivi d’un opérateur binaire plus. Dans l’instruction
```csharp
x = y is C<T> + z;
```
les jetons `C<T>` sont interprétés comme un *namespace_or_type_name* avec un *type_argument_list*.

### <a name="invocation-expressions"></a>Appel d’expressions

Un *invocation_expression* est utilisé pour appeler une méthode.

```antlr
invocation_expression
    : primary_expression '(' argument_list? ')'
    ;
```

Un *invocation_expression* est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)) si au moins l’un des éléments suivants est présent :

* Le *primary_expression* a des `dynamic`de type au moment de la compilation.
* Au moins un argument de la *argument_list* facultative présente un `dynamic` de type au moment de la compilation et le *primary_expression* n’a pas de type délégué.

Dans ce cas, le compilateur classe la *invocation_expression* comme une valeur de type `dynamic`. Les règles ci-dessous pour déterminer la signification du *invocation_expression* sont ensuite appliquées au moment de l’exécution, à l’aide du type au moment de l’exécution au lieu du type de compilation de celles du *primary_expression* et des arguments dont le type au moment de la compilation `dynamic`. Si le *primary_expression* n’a pas de type `dynamic`au moment de la compilation, l’appel de la méthode subit une vérification limitée au moment de la compilation, comme décrit dans [contrôle de la résolution de surcharge dynamique au moment de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

La *primary_expression* d’un *invocation_expression* doit être un groupe de méthodes ou une valeur d’un *delegate_type*. Si le *primary_expression* est un groupe de méthodes, le *invocation_expression* est un appel de méthode ([appel de méthode](expressions.md#method-invocations)). Si le *primary_expression* est une valeur d’un *delegate_type*, le *invocation_expression* est un appel de délégué ([appel de délégué](expressions.md#delegate-invocations)). Si la *primary_expression* n’est ni un groupe de méthodes, ni une valeur d’un *delegate_type*, une erreur de liaison s’est produite.

La *argument_list* facultative ([listes d’arguments](expressions.md#argument-lists)) fournit des valeurs ou des références de variables pour les paramètres de la méthode.

Le résultat de l’évaluation d’un *invocation_expression* est classé comme suit :

*  Si le *invocation_expression* appelle une méthode ou un délégué qui retourne `void`, le résultat est Nothing. Une expression classifiée comme Nothing n’est autorisée que dans le contexte d’une *statement_expression* ([instructions d’expression](statements.md#expression-statements)) ou en tant que corps d’une *lambda_expression* ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)). Sinon, une erreur de temps de liaison se produit.
*  Dans le cas contraire, le résultat est une valeur du type retourné par la méthode ou le délégué.

#### <a name="method-invocations"></a>Appels de méthode

Pour un appel de méthode, le *primary_expression* du *invocation_expression* doit être un groupe de méthodes. Le groupe de méthodes identifie la méthode à appeler ou l’ensemble de méthodes surchargées à partir desquelles choisir une méthode spécifique à appeler. Dans ce dernier cas, la détermination de la méthode spécifique à appeler est basée sur le contexte fourni par les types des arguments de la *argument_list*.

Le traitement au moment de la liaison d’un appel de méthode de la forme `M(A)`, où `M` est un groupe de méthodes (éventuellement avec un *type_argument_list*) et `A` est un *argument_list*facultatif, se compose des étapes suivantes :

*  L’ensemble des méthodes candidates pour l’appel de méthode est construit. Pour chaque méthode `F` associée au groupe de méthodes `M`:
   *  Si `F` n’est pas générique, `F` est candidate dans les cas suivants :
      * `M` n’a pas de liste d’arguments de type, et
      * `F` est applicable en ce qui concerne `A` ([fonction membre applicable](expressions.md#applicable-function-member)).
   *  Si `F` est générique et que `M` n’a pas de liste d’arguments de type, `F` est candidate dans les cas suivants :
      * L’inférence de type ([inférence de type](expressions.md#type-inference)) est réussie, en déduisant une liste d’arguments de type pour l’appel, et
      * Une fois que les arguments de type inférés sont substitués aux paramètres de type de méthode correspondants, tous les types construits dans la liste de paramètres de F répondent à leurs contraintes (qui[satisfont les contraintes](types.md#satisfying-constraints)) et la liste de paramètres de `F` s’applique à `A` ([fonction membre applicable](expressions.md#applicable-function-member)).
   *  Si `F` est générique et que `M` comprend une liste d’arguments de type, `F` est candidate dans les cas suivants :
      * `F` a le même nombre de paramètres de type de méthode que ceux qui ont été fournis dans la liste d’arguments de type, et
      * Une fois que les arguments de type sont substitués aux paramètres de type de méthode correspondants, tous les types construits dans la liste de paramètres de F répondent à leurs contraintes (qui[satisfont les contraintes](types.md#satisfying-constraints)) et la liste de paramètres de `F` s’applique à `A` ([fonction membre applicable](expressions.md#applicable-function-member)).
*  L’ensemble des méthodes candidates est réduit pour contenir uniquement des méthodes des types les plus dérivés : pour chaque méthode `C.F` dans le jeu, où `C` est le type dans lequel la méthode `F` est déclarée, toutes les méthodes déclarées dans un type de base de `C` sont supprimées de l’ensemble. En outre, si `C` est un type de classe autre que `object`, toutes les méthodes déclarées dans un type interface sont supprimées de l’ensemble. (Cette dernière règle a un impact uniquement lorsque le groupe de méthodes était le résultat d’une recherche de membre sur un paramètre de type ayant une classe de base effective autre que Object et un ensemble d’interfaces effectif non vide.)
*  Si le jeu résultant de méthodes candidates est vide, le traitement suivant des étapes suivantes est abandonné, et une tentative est effectuée pour traiter l’appel comme un appel de méthode d’extension ([appels de méthode d’extension](expressions.md#extension-method-invocations)). En cas d’échec, il n’existe aucune méthode applicable et une erreur de liaison s’est produite.
*  La meilleure méthode de l’ensemble de méthodes candidates est identifiée à l’aide des règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution). Si une seule meilleure méthode ne peut pas être identifiée, l’appel de la méthode est ambigu et une erreur de liaison s’est produite. Lors de l’exécution de la résolution de surcharge, les paramètres d’une méthode générique sont pris en compte après avoir remplacé les arguments de type (fournis ou déduits) pour les paramètres de type de méthode correspondants.
*  La validation finale de la meilleure méthode choisie est effectuée :
   * La méthode est validée dans le contexte du groupe de méthodes : si la meilleure méthode est une méthode statique, le groupe de méthodes doit résulter d’un *simple_name* ou d’un *member_access* par le biais d’un type. Si la meilleure méthode est une méthode d’instance, le groupe de méthodes doit résulter d’une *simple_name*, d’une *member_access* par le biais d’une variable ou d’une valeur ou d’un *base_access*. Si aucune de ces conditions n’est remplie, une erreur de liaison au moment de la liaison se produit.
   * Si la meilleure méthode est une méthode générique, les arguments de type (fournis ou déduits) sont vérifiés par rapport aux contraintes ([contraintes satisfaisantes](types.md#satisfying-constraints)) déclarées dans la méthode générique. Si un argument de type ne satisfait pas la ou les contraintes correspondantes sur le paramètre de type, une erreur de liaison s’est produite.

Une fois qu’une méthode a été sélectionnée et validée au moment de la liaison par les étapes ci-dessus, l’appel réel au moment de l’exécution est traité en fonction des règles d’appel des membres de fonction décrites dans [vérification de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)par la compilation.

L’effet intuitif des règles de résolution décrites ci-dessus est le suivant : pour localiser la méthode particulière appelée par un appel de méthode, commencez avec le type indiqué par l’appel de méthode et remontez la chaîne d’héritage jusqu’à au moins un applicable, une déclaration de méthode accessible, non substituée a été trouvée. Effectuez ensuite l’inférence de type et la résolution de surcharge sur l’ensemble des méthodes applicables, accessibles et non substituées déclarées dans ce type et appelez la méthode sélectionnée. Si aucune méthode n’a été trouvée, essayez plutôt de traiter l’appel comme un appel de méthode d’extension.

#### <a name="extension-method-invocations"></a>Appels de méthode d’extension

Dans un appel de méthode ([appels sur des instances boxed](expressions.md#invocations-on-boxed-instances)) de l’un des formulaires
```csharp
expr . identifier ( )

expr . identifier ( args )

expr . identifier < typeargs > ( )

expr . identifier < typeargs > ( args )
```
Si le traitement normal de l’appel ne trouve aucune méthode applicable, une tentative est faite pour traiter la construction comme un appel de méthode d’extension. Si *expr* ou l’un des *arguments* a le type au moment de la compilation `dynamic`, les méthodes d’extension ne s’appliquent pas.

L’objectif est de trouver le meilleur *type_name* `C`, afin que l’appel de la méthode statique correspondante puisse avoir lieu :
```csharp
C . identifier ( expr )

C . identifier ( expr , args )

C . identifier < typeargs > ( expr )

C . identifier < typeargs > ( expr , args )
```

Une méthode d’extension `Ci.Mj` est ***éligible*** dans les cas suivants :

*  `Ci` est une classe non imbriquée et non générique
*  Le nom de `Mj` est *identifier*
*  `Mj` est accessible et applicable lorsqu’il est appliqué aux arguments en tant que méthode statique, comme indiqué ci-dessus
*  Une identité, une référence ou une conversion boxing implicite existe entre *expr* et le type du premier paramètre de `Mj`.

La recherche de `C` se déroule comme suit :

*  À partir de la déclaration d’espace de noms englobante la plus proche, en continuant avec chaque déclaration d’espace de noms englobant, et se terminant par l’unité de compilation conteneur, des tentatives successives sont effectuées pour trouver un ensemble candidat de méthodes d’extension :
   * Si l’espace de noms ou l’unité de compilation donné contient directement des déclarations de type non génériques `Ci` avec les méthodes d’extension éligibles `Mj`, l’ensemble de ces méthodes d’extension est l’ensemble de candidats.
   * Si les types `Ci` importés par *using_static_declarations* et directement déclarés dans les espaces de noms importés par *using_namespace_directive*dans l’espace de noms ou l’unité de compilation donnés contiennent directement les méthodes d’extension éligibles `Mj`, l’ensemble de ces méthodes d’extension est l’ensemble de candidats.
*  Si aucun jeu de candidats n’est trouvé dans une déclaration d’espace de noms ou une unité de compilation englobante, une erreur de compilation se produit.
*  Sinon, la résolution de surcharge est appliquée à l’ensemble de candidats comme décrit dans ([résolution de surcharge](expressions.md#overload-resolution)). Si aucune meilleure méthode n’est trouvée, une erreur de compilation se produit.
*  `C` est le type dans lequel la meilleure méthode est déclarée en tant que méthode d’extension.

À l’aide de `C` en tant que cible, l’appel de méthode est ensuite traité comme un appel de méthode statique ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).

Les règles précédentes signifient que les méthodes d’instance sont prioritaires sur les méthodes d’extension, que les méthodes d’extension disponibles dans les déclarations d’espace de noms interne ont la priorité sur les méthodes d’extension disponibles dans les déclarations d’espaces de noms externes, et cette extension les méthodes déclarées directement dans un espace de noms ont priorité sur les méthodes d’extension importées dans ce même espace de noms avec une directive d’espace de noms using. Par exemple :
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

Dans l’exemple, la méthode de `B`est prioritaire sur la première méthode d’extension, et la méthode de `C`est prioritaire sur les deux méthodes d’extension.

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

La sortie de cet exemple est la suivante :
```console
E.F(1)
D.G(2)
C.H(3)
```
`D.G` a priorité sur `C.G`et `E.F` est prioritaire sur les `D.F` et les `C.F`.

#### <a name="delegate-invocations"></a>Appels délégués

Pour un appel de délégué, le *primary_expression* du *invocation_expression* doit être une valeur d’un *delegate_type*. En outre, si l’on considère que le *delegate_type* être une fonction membre avec la même liste de paramètres que l' *delegate_type*, la *delegate_type* doit être applicable ([membre de fonction applicable](expressions.md#applicable-function-member)) en ce qui concerne la *argument_list* du *invocation_expression*.

Le traitement au moment de l’exécution d’un appel de délégué de la forme `D(A)`, où `D` est une *primary_expression* d’un *delegate_type* et `A` est un *argument_list*facultatif, se compose des étapes suivantes :

*  `D` est évalué. Si cette évaluation provoque une exception, aucune autre étape n’est exécutée.
*  La valeur de `D` est vérifiée comme valide. Si la valeur de `D` est `null`, une `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.
*  Dans le cas contraire, `D` est une référence à une instance de délégué. Les appels de membre de fonction ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) sont exécutés sur chacune des entités pouvant être appelées dans la liste d’appel du délégué. Pour les entités pouvant être appelées constituées d’une méthode d’instance et d’instance, l’instance de l’appel est l’instance contenue dans l’entité pouvant être appelée.

### <a name="element-access"></a>Accès aux éléments

Un *element_access* se compose d’un *primary_no_array_creation_expression*, suivi d’un jeton «`[`», suivi d’un *argument_list*, suivi d’un jeton «`]`». Le *argument_list* se compose d’un ou plusieurs *arguments*, séparés par des virgules.

```antlr
element_access
    : primary_no_array_creation_expression '[' expression_list ']'
    ;
```

La *argument_list* d’un *element_access* n’est pas autorisée à contenir des arguments `ref` ou `out`.

Un *element_access* est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)) si au moins l’un des éléments suivants est présent :

* Le *primary_no_array_creation_expression* a des `dynamic`de type au moment de la compilation.
* Au moins une expression de la *argument_list* a un type au moment de la compilation `dynamic` et le *primary_no_array_creation_expression* n’a pas de type tableau.

Dans ce cas, le compilateur classe la *element_access* comme une valeur de type `dynamic`. Les règles ci-dessous pour déterminer la signification du *element_access* sont ensuite appliquées au moment de l’exécution, à l’aide du type au moment de l’exécution au lieu du type de compilation de celles du *primary_no_array_creation_expression* et des expressions *argument_list* qui ont le type au moment de la compilation `dynamic`. Si le *primary_no_array_creation_expression* n’a pas de type `dynamic`au moment de la compilation, l’accès à l’élément subit une vérification limitée au moment de la compilation, comme décrit dans [vérification de la résolution de surcharge dynamique au moment de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

Si la *primary_no_array_creation_expression* d’un *element_access* est une valeur d’un *array_type*, l' *element_access* est un accès à un tableau ([accès au tableau](expressions.md#array-access)). Sinon, le *primary_no_array_creation_expression* doit être une variable ou une valeur d’un type de classe, de struct ou d’interface qui possède un ou plusieurs membres de l’indexeur, auquel cas le *element_access* est un accès à un indexeur ([accès à l’indexeur](expressions.md#indexer-access)).

#### <a name="array-access"></a>Accès aux tableaux

Pour un accès à un tableau, le *primary_no_array_creation_expression* du *element_access* doit être une valeur d’un *array_type*. En outre, le *argument_list* d’un accès à un tableau n’est pas autorisé à contenir des arguments nommés. Le nombre d’expressions dans le *argument_list* doit être le même que le rang du *array_type*, et chaque expression doit être de type `int`, `uint`, `long`, `ulong`, ou doit être implicitement convertible en un ou plusieurs de ces types.

Le résultat de l’évaluation d’un accès à un tableau est une variable du type d’élément du tableau, à savoir l’élément de tableau sélectionné par la ou les valeurs de la ou des expressions de la *argument_list*.

Le traitement au moment de l’exécution d’un accès à un tableau de la forme `P[A]`, où `P` est un *primary_no_array_creation_expression* d’un *array_type* et `A` est un *argument_list*, se compose des étapes suivantes :

*  `P` est évalué. Si cette évaluation provoque une exception, aucune autre étape n’est exécutée.
*  Les expressions d’index des *argument_list* sont évaluées dans l’ordre, de gauche à droite. Après l’évaluation de chaque expression d’index, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) vers l’un des types suivants est effectuée : `int`, `uint`, `long`, `ulong`. Le premier type de cette liste pour lequel une conversion implicite existe est choisi. Par exemple, si l’expression d’index est de type `short`, une conversion implicite vers `int` est effectuée, puisque les conversions implicites de `short` en `int` et de `short` à `long` sont possibles. Si l’évaluation d’une expression d’index ou de la conversion implicite suivante provoque une exception, aucune autre expression d’index n’est évaluée et aucune autre étape n’est exécutée.
*  La valeur de `P` est vérifiée comme valide. Si la valeur de `P` est `null`, une `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.
*  La valeur de chaque expression dans le *argument_list* est vérifiée par rapport aux limites réelles de chaque dimension de l’instance de tableau référencée par `P`. Si une ou plusieurs valeurs sont hors limites, une `System.IndexOutOfRangeException` est levée et aucune autre étape n’est exécutée.
*  L’emplacement de l’élément de tableau donné par la ou les expressions d’index est calculé et cet emplacement devient le résultat de l’accès au tableau.

#### <a name="indexer-access"></a>Accès aux indexeurs

Pour un accès à un indexeur, le *primary_no_array_creation_expression* du *element_access* doit être une variable ou une valeur d’un type de classe, de struct ou d’interface, et ce type doit implémenter un ou plusieurs indexeurs applicables en ce qui concerne le *argument_list* du *element_access*.

Le traitement au moment de la liaison d’un accès à un indexeur de la forme `P[A]`, où `P` est un *primary_no_array_creation_expression* d’un type de classe, de struct ou d’interface `T`, et `A` est un *argument_list*, se compose des étapes suivantes :

*  L’ensemble d’indexeurs fourni par `T` est construit. L’ensemble se compose de tous les indexeurs déclarés dans `T` ou d’un type de base de `T` qui ne sont pas des déclarations `override` et sont accessibles dans le contexte actuel ([accès aux membres](basic-concepts.md#member-access)).
*  Le jeu est réduit aux indexeurs applicables et non masqués par d’autres indexeurs. Les règles suivantes sont appliquées à chaque indexeur `S.I` dans l’ensemble, où `S` est le type dans lequel l’indexeur `I` est déclaré :
   * Si `I` n’est pas applicable en ce qui concerne `A` ([fonction membre applicable](expressions.md#applicable-function-member)), `I` est supprimé de l’ensemble.
   * Si `I` est applicable en ce qui concerne `A` ([fonction membre applicable](expressions.md#applicable-function-member)), tous les indexeurs déclarés dans un type de base de `S` sont supprimés du jeu.
   * Si `I` est applicable en ce qui concerne `A` ([fonction membre applicable](expressions.md#applicable-function-member)) et `S` est un type de classe autre que `object`, tous les indexeurs déclarés dans une interface sont supprimés du jeu.
*  Si le jeu d’indexeurs candidats obtenu est vide, aucun indexeur applicable n’existe et une erreur de liaison est générée.
*  Le meilleur indexeur de l’ensemble des indexeurs candidats est identifié à l’aide des règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution). Si un seul meilleur indexeur ne peut pas être identifié, l’accès à l’indexeur est ambigu et une erreur de liaison s’est produite.
*  Les expressions d’index des *argument_list* sont évaluées dans l’ordre, de gauche à droite. Le résultat du traitement de l’accès à l’indexeur est une expression classée comme un accès à un indexeur. L’expression d’accès de l’indexeur fait référence à l’indexeur déterminé à l’étape ci-dessus et a une expression d’instance associée de `P` et une liste d’arguments associée de `A`.

En fonction du contexte dans lequel il est utilisé, un accès à l’indexeur entraîne l’appel de l' *accesseur Get* ou de l' *accesseur Set* de l’indexeur. Si l’accès à l’indexeur est la cible d’une assignation, l' *accesseur Set* est appelé pour assigner une nouvelle valeur ([assignation simple](expressions.md#simple-assignment)). Dans tous les autres cas, l' *accesseur Get* est appelé pour obtenir la valeur actuelle ([valeurs des expressions](expressions.md#values-of-expressions)).

### <a name="this-access"></a>Cet accès

Un *This_Access* se compose du mot réservé `this`.

```antlr
this_access
    : 'this'
    ;
```

Une *This_Access* est autorisée uniquement dans le *bloc* d’un constructeur d’instance, d’une méthode d’instance ou d’un accesseur d’instance. Il a l’une des significations suivantes :

*  Lorsque `this` est utilisé dans un *primary_expression* au sein d’un constructeur d’instance d’une classe, il est classé comme une valeur. Le type de la valeur est le type d’instance ([type d’instance](classes.md#the-instance-type)) de la classe dans laquelle l’utilisation se produit, et la valeur est une référence à l’objet en cours de construction.
*  Lorsque `this` est utilisé dans une *primary_expression* dans une méthode d’instance ou un accesseur d’instance d’une classe, il est classé comme une valeur. Le type de la valeur est le type d’instance ([type d’instance](classes.md#the-instance-type)) de la classe dans laquelle l’utilisation se produit, et la valeur est une référence à l’objet pour lequel la méthode ou l’accesseur a été appelé.
*  Lorsque `this` est utilisé dans un *primary_expression* au sein d’un constructeur d’instance d’un struct, il est classé en tant que variable. Le type de la variable est le type d’instance ([type d’instance](classes.md#the-instance-type)) de la structure dans laquelle l’utilisation se produit, et la variable représente le struct en cours de construction. La variable `this` d’un constructeur d’instance d’un struct se comporte exactement comme un paramètre `out` du type struct (en particulier, cela signifie que la variable doit être assignée de manière définitive dans chaque chemin d’exécution du constructeur d’instance).
*  Lorsque `this` est utilisé dans une *primary_expression* dans une méthode d’instance ou un accesseur d’instance d’un struct, il est classé en tant que variable. Le type de la variable est le type d’instance ([type d’instance](classes.md#the-instance-type)) de la structure dans laquelle l’utilisation se produit.
   * Si la méthode ou l’accesseur n’est pas un itérateur ([itérateurs](classes.md#iterators)), la variable `this` représente le struct pour lequel la méthode ou l’accesseur a été appelé, et se comporte exactement comme un paramètre `ref` du type struct.
   * Si la méthode ou l’accesseur est un itérateur, la variable `this` représente une copie du struct pour lequel la méthode ou l’accesseur a été appelé, et se comporte exactement comme un paramètre de valeur du type struct.

L’utilisation de `this` dans un *primary_expression* dans un contexte autre que ceux listés ci-dessus est une erreur de compilation. En particulier, il n’est pas possible de faire référence à `this` dans une méthode statique, un accesseur de propriété statique ou dans une *variable_initializer* d’une déclaration de champ.

### <a name="base-access"></a>Accès de base

Un *base_access* se compose du mot réservé `base` suivi d’un jeton «`.`» et d’un identificateur ou d’un *argument_list* placé entre crochets :

```antlr
base_access
    : 'base' '.' identifier
    | 'base' '[' expression_list ']'
    ;
```

Un *base_access* est utilisé pour accéder aux membres de la classe de base qui sont masqués par des membres portant le même nom dans la classe ou le struct actuel. Une *base_access* est autorisée uniquement dans le *bloc* d’un constructeur d’instance, d’une méthode d’instance ou d’un accesseur d’instance. Quand `base.I` se produit dans une classe ou un struct, `I` doit désigner un membre de la classe de base de cette classe ou structure. De même, quand `base[E]` se produit dans une classe, un indexeur applicable doit exister dans la classe de base.

Au moment de la liaison, *base_access* expressions de la forme `base.I` et `base[E]` sont évaluées exactement comme si elles étaient écrites `((B)this).I` et `((B)this)[E]`, où `B` est la classe de base de la classe ou du struct dans lequel la construction se produit. Ainsi, `base.I` et `base[E]` correspondent à `this.I` et `this[E]`, sauf que `this` est affiché en tant qu’instance de la classe de base.

Lorsqu’un *base_access* fait référence à un membre de fonction virtuelle (une méthode, une propriété ou un indexeur), la détermination du membre de fonction à appeler au moment de l’exécution ([vérification de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)au moment de la compilation) est modifiée. Le membre de fonction appelé est déterminé par la recherche de l’implémentation la plus dérivée ([méthodes virtuelles](classes.md#virtual-methods)) du membre de fonction par rapport à `B` (plutôt que par rapport au type d’exécution de `this`, comme normalement dans un accès non-base). Ainsi, dans un `override` d’un membre de fonction `virtual`, un *base_access* peut être utilisé pour appeler l’implémentation héritée du membre de fonction. Si le membre de fonction référencé par un *base_access* est abstrait, une erreur de liaison au moment de la liaison se produit.

### <a name="postfix-increment-and-decrement-operators"></a>Opérateurs suffixés d’incrémentation et de décrémentation

```antlr
post_increment_expression
    : primary_expression '++'
    ;

post_decrement_expression
    : primary_expression '--'
    ;
```

L’opérande d’une opération d’incrémentation ou de décrémentation suffixée doit être une expression classifiée comme une variable, un accès à une propriété ou un accès à un indexeur. Le résultat de l’opération est une valeur du même type que l’opérande.

Si le *primary_expression* a le type au moment de la compilation `dynamic` l’opérateur est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)), le *post_increment_expression* ou *post_decrement_expression* a le type au moment de la compilation `dynamic` et les règles suivantes sont appliquées au moment de l’exécution en utilisant le type d’exécution de l' *primary_expression*.

Si l’opérande d’une opération d’incrémentation ou de décrémentation suffixée est un accès à une propriété ou un indexeur, la propriété ou l’indexeur doit avoir un `get` et un accesseur `set`. Si ce n’est pas le cas, une erreur de temps de liaison se produit.

La résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. Les opérateurs `++` et `--` prédéfinis existent pour les types suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`et tout type enum. Les opérateurs de `++` prédéfinis retournent la valeur produite par l’ajout de 1 à l’opérande, et les opérateurs de `--` prédéfinis retournent la valeur produite par la soustraction de 1 de l’opérande. Dans un contexte de `checked`, si le résultat de l’addition ou de la soustraction est en dehors de la plage du type de résultat et que le type de résultat est un type intégral ou un type enum, une `System.OverflowException` est levée.

Le traitement au moment de l’exécution d’une opération d’incrémentation ou de décrémentation suffixée de la forme `x++` ou `x--` se compose des étapes suivantes :

*   Si `x` est classée en tant que variable :
    * `x` est évaluée pour produire la variable.
    * La valeur de `x` est enregistrée.
    * L’opérateur sélectionné est appelé avec la valeur enregistrée de `x` comme argument.
    * La valeur retournée par l’opérateur est stockée dans l’emplacement donné par l’évaluation de `x`.
    * La valeur enregistrée de `x` devient le résultat de l’opération.
*   Si `x` est classée comme un accès à une propriété ou un indexeur :
    * L’expression d’instance (si `x` n’est pas `static`) et la liste d’arguments (si `x` est un accès à un indexeur) associée à `x` sont évaluées, et les résultats sont utilisés dans les appels de `get` et d’accesseur `set` suivants.
    * L’accesseur `get` de `x` est appelé et la valeur retournée est enregistrée.
    * L’opérateur sélectionné est appelé avec la valeur enregistrée de `x` comme argument.
    * L’accesseur `set` de `x` est appelé avec la valeur retournée par l’opérateur comme son argument `value`.
    * La valeur enregistrée de `x` devient le résultat de l’opération.

Les opérateurs `++` et `--` prennent également en charge la notation de préfixe ([opérateurs de préfixe d’incrémentation et de décrémentation](expressions.md#prefix-increment-and-decrement-operators)). En général, le résultat de `x++` ou `x--` est la valeur de `x` avant l’opération, alors que le résultat de `++x` ou `--x` est la valeur de `x` après l’opération. Dans les deux cas, `x` lui-même a la même valeur après l’opération.

Une implémentation de `operator ++` ou `operator --` peut être appelée à l’aide de la notation suffixée ou de préfixe. Il n’est pas possible d’avoir des implémentations d’opérateur distinctes pour les deux notations.

### <a name="the-new-operator"></a>new, opérateur

L’opérateur `new` est utilisé pour créer de nouvelles instances de types.

Il existe trois formes d’expressions `new` :

*  Les expressions de création d’objet sont utilisées pour créer des instances de types de classe et de valeur.
*  Les expressions de création de tableau sont utilisées pour créer de nouvelles instances de types tableau.
*  Les expressions de création de délégué sont utilisées pour créer de nouvelles instances de types délégués.

L’opérateur `new` implique la création d’une instance d’un type, mais n’implique pas nécessairement l’allocation dynamique de la mémoire. En particulier, les instances de types valeur ne requièrent aucune mémoire supplémentaire au-delà des variables dans lesquelles elles résident et aucune allocation dynamique ne se produit lorsque `new` est utilisé pour créer des instances de types valeur.

#### <a name="object-creation-expressions"></a>Expressions de création d’objet

Un *object_creation_expression* est utilisé pour créer une nouvelle instance d’un *class_type* ou d’un *Value_type*.

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

Le *type* d’un *object_creation_expression* doit être un *class_type*, un *Value_type* ou un *type_parameter*. Le *type* ne peut pas être un *class_type*`abstract`.

La *argument_list* facultative ([listes d’arguments](expressions.md#argument-lists)) est autorisée uniquement si le *type* est un *class_type* ou un *struct_type*.

Une expression de création d’objet peut omettre la liste d’arguments de constructeur et les parenthèses englobantes fournies, elle comprend un initialiseur d’objet ou un initialiseur de collection. L’omission de la liste d’arguments du constructeur et des parenthèses englobantes équivaut à spécifier une liste d’arguments vide.

Le traitement d’une expression de création d’objet qui inclut un initialiseur d’objet ou un initialiseur de collection consiste à traiter en premier le constructeur d’instance, puis à traiter les initialisations de membre ou d’élément spécifiées par l’initialiseur d’objet ([Initialiseurs d’objets](expressions.md#object-initializers)) ou initialiseur de collection ([initialiseurs de collection](expressions.md#collection-initializers)).

Si l’un des arguments de la *argument_list* facultative a le type au moment de la compilation `dynamic` alors le *object_creation_expression* est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)) et les règles suivantes sont appliquées au moment de l’exécution à l’aide du type d’exécution des arguments de l' *argument_list* qui ont le type au moment de la compilation `dynamic`. Toutefois, la création d’objet subit une vérification limitée au moment de la compilation, comme décrit dans [vérification de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)par la compilation.

Le traitement au moment de la liaison d’une *object_creation_expression* de la forme `new T(A)`, où `T` est un *class_type* ou un *value_type* et `A` est un *argument_list*facultatif, se compose des étapes suivantes :

*   Si `T` est un *Value_type* et `A` n’est pas présent :
    * Le *object_creation_expression* est un appel de constructeur par défaut. Le résultat de l' *object_creation_expression* est une valeur de type `T`, à savoir la valeur par défaut pour `T` telle que définie dans [le type System. ValueType](types.md#the-systemvaluetype-type).
*   Sinon, si `T` est un *type_parameter* et `A` n’est pas présent :
    * Si aucune contrainte de type valeur ou contrainte de constructeur ([contraintes de paramètre de type](classes.md#type-parameter-constraints)) n’a été spécifiée pour `T`, une erreur de liaison s’est produite.
    * Le résultat de l' *object_creation_expression* est une valeur du type au moment de l’exécution auquel le paramètre de type a été lié, à savoir le résultat de l’appel du constructeur par défaut de ce type. Le type au moment de l’exécution peut être un type référence ou un type valeur.
*   Sinon, si `T` est un *class_type* ou un *struct_type*:
    * Si `T` est un `abstract` *class_type*, une erreur se produit au moment de la compilation.
    * Le constructeur d’instance à appeler est déterminé à l’aide des règles de résolution de surcharge de la [résolution de surcharge](expressions.md#overload-resolution). L’ensemble de constructeurs d’instance candidat se compose de tous les constructeurs d’instance accessibles déclarés dans `T` qui sont applicables à `A` ([fonction membre applicable](expressions.md#applicable-function-member)). Si le jeu de constructeurs d’instance candidat est vide, ou si un seul constructeur d’instance unique ne peut pas être identifié, une erreur de liaison s’est produite.
    * Le résultat de l' *object_creation_expression* est une valeur de type `T`, à savoir la valeur produite par l’appel du constructeur d’instance déterminé à l’étape ci-dessus.
*  Sinon, la *object_creation_expression* n’est pas valide et une erreur de liaison s’est produite.

Même si le *object_creation_expression* est lié dynamiquement, le type au moment de la compilation est toujours `T`.

Le traitement au moment de l’exécution d’une *object_creation_expression* de la forme `new T(A)`, où `T` est *class_type* ou un *struct_type* et `A` est un *argument_list*facultatif, se compose des étapes suivantes :

*   Si `T` est un *class_type*:
    * Une nouvelle instance de la classe `T` est allouée. Si la mémoire disponible est insuffisante pour allouer la nouvelle instance, une `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.
    * Tous les champs de la nouvelle instance sont initialisés à leurs valeurs par défaut ([valeurs par défaut](variables.md#default-values)).
    * Le constructeur d’instance est appelé selon les règles d’appel de membre de fonction ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)). Une référence à l’instance qui vient d’être allouée est automatiquement passée au constructeur d’instance et l’instance est accessible à partir de ce constructeur en tant que `this`.
*   Si `T` est un *struct_type*:
    * Une instance de type `T` est créée en allouant une variable locale temporaire. Étant donné qu’un constructeur d’instance d’un *struct_type* est requis pour assigner une valeur à chaque champ de l’instance en cours de création, aucune initialisation de la variable temporaire n’est nécessaire.
    * Le constructeur d’instance est appelé selon les règles d’appel de membre de fonction ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)). Une référence à l’instance qui vient d’être allouée est automatiquement passée au constructeur d’instance et l’instance est accessible à partir de ce constructeur en tant que `this`.

#### <a name="object-initializers"></a>Initialiseurs d’objet

Un ***initialiseur d’objet*** spécifie des valeurs pour zéro, un ou plusieurs champs, propriétés ou éléments indexés d’un objet.

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

Un initialiseur d’objet se compose d’une séquence d’initialiseurs de membres, placée entre `{` et `}` des jetons et séparés par des virgules. Chaque *member_initializer* désigne une cible pour l’initialisation. Un *identificateur* doit nommer un champ ou une propriété accessible de l’objet en cours d’initialisation, alors qu’un *argument_list* placé entre crochets doit spécifier des arguments pour un indexeur accessible sur l’objet en cours d’initialisation. Il y a une erreur pour qu’un initialiseur d’objet inclue plusieurs initialiseurs de membre pour le même champ ou la même propriété.

Chaque *initializer_target* est suivi d’un signe égal et d’une expression, d’un initialiseur d’objet ou d’un initialiseur de collection. Les expressions dans l’initialiseur d’objet ne peuvent pas faire référence à l’objet nouvellement créé qu’il initialise.

Un initialiseur de membre qui spécifie une expression après que le signe égal est traité de la même façon qu’une assignation ([assignation simple](expressions.md#simple-assignment)) à la cible.

Un initialiseur de membre qui spécifie un initialiseur d’objet après que le signe égal est un ***initialiseur d’objet imbriqué***, c.-à-d. une initialisation d’un objet incorporé. Au lieu d’assigner une nouvelle valeur au champ ou à la propriété, les assignations dans l’initialiseur d’objet imbriqué sont traitées comme des assignations aux membres du champ ou de la propriété. Les initialiseurs d’objets imbriqués ne peuvent pas être appliqués à des propriétés avec un type valeur, ou à des champs en lecture seule avec un type valeur.

Un initialiseur de membre qui spécifie un initialiseur de collection après le signe égal est l’initialisation d’une collection incorporée. Au lieu d’assigner une nouvelle collection au champ, à la propriété ou à l’indexeur cible, les éléments fournis dans l’initialiseur sont ajoutés à la collection référencée par la cible. La cible doit être d’un type de collection qui satisfait aux exigences spécifiées dans les [initialiseurs de collection](expressions.md#collection-initializers).

Les arguments d’un initialiseur d’index seront toujours évalués une seule fois. Ainsi, même si les arguments finissent par ne jamais être utilisés (par exemple, en raison d’un initialiseur imbriqué vide), ils sont évalués pour leurs effets secondaires.

La classe suivante représente un point avec deux coordonnées :
```csharp
public class Point
{
    int x, y;

    public int X { get { return x; } set { x = value; } }
    public int Y { get { return y; } set { y = value; } }
}
```

Une instance de `Point` peut être créée et initialisée comme suit :
```csharp
Point a = new Point { X = 0, Y = 1 };
```
ce qui a le même effet que
```csharp
Point __a = new Point();
__a.X = 0;
__a.Y = 1; 
Point a = __a;
```
où `__a` est une variable temporaire inaccessible et invisible. La classe suivante représente un rectangle créé à partir de deux points :
```csharp
public class Rectangle
{
    Point p1, p2;

    public Point P1 { get { return p1; } set { p1 = value; } }
    public Point P2 { get { return p2; } set { p2 = value; } }
}
```

Une instance de `Rectangle` peut être créée et initialisée comme suit :
```csharp
Rectangle r = new Rectangle {
    P1 = new Point { X = 0, Y = 1 },
    P2 = new Point { X = 2, Y = 3 }
};
```
ce qui a le même effet que
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
où `__r`, `__p1` et `__p2` sont des variables temporaires qui, sinon, sont inaccessibles et inaccessibles.

Si le constructeur de `Rectangle`alloue les deux instances de `Point` incorporées
```csharp
public class Rectangle
{
    Point p1 = new Point();
    Point p2 = new Point();

    public Point P1 { get { return p1; } }
    public Point P2 { get { return p2; } }
}
```
la construction suivante peut être utilisée pour initialiser les instances de `Point` incorporées au lieu d’assigner de nouvelles instances :
```csharp
Rectangle r = new Rectangle {
    P1 = { X = 0, Y = 1 },
    P2 = { X = 2, Y = 3 }
};
```
ce qui a le même effet que
```csharp
Rectangle __r = new Rectangle();
__r.P1.X = 0;
__r.P1.Y = 1;
__r.P2.X = 2;
__r.P2.Y = 3;
Rectangle r = __r;
```

Dans le cas d’une définition appropriée de C, l’exemple suivant :
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
équivaut à cette série d’affectations :
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
où `__c`, etc., sont des variables générées qui sont invisibles et inaccessibles au code source. Notez que les arguments de `[0,0]` ne sont évalués qu’une seule fois, et les arguments de `[1,2]` sont évalués une seule fois, même s’ils ne sont jamais utilisés.

#### <a name="collection-initializers"></a>Initialiseurs de collection

Un initialiseur de collection spécifie les éléments d’une collection.

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

Un initialiseur de collection se compose d’une séquence d’initialiseurs d’éléments, encadrés par `{` et `}` des jetons et séparés par des virgules. Chaque initialiseur d’élément spécifie un élément à ajouter à l’objet de collection qui est initialisé, et se compose d’une liste d’expressions encadrées par `{` et `}` des jetons et séparés par des virgules.  Un initialiseur d’élément à une seule expression peut être écrit sans accolades, mais il ne peut pas être une expression d’assignation, afin d’éviter toute ambiguïté avec les initialiseurs de membres. La *non_assignment_expression* production est définie dans [expression](expressions.md#expression).

L’exemple suivant illustre une expression de création d’objet qui comprend un initialiseur de collection :
```csharp
List<int> digits = new List<int> { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
```

L’objet de collection auquel un initialiseur de collection est appliqué doit être d’un type qui implémente `System.Collections.IEnumerable` ou une erreur de compilation se produit. Pour chaque élément spécifié dans l’ordre, l’initialiseur de collection appelle une méthode `Add` sur l’objet cible avec la liste d’expressions de l’initialiseur d’élément comme liste d’arguments, en appliquant une recherche de membre normale et une résolution de surcharge pour chaque appel. Ainsi, l’objet de collection doit avoir une instance ou une méthode d’extension applicable avec le nom `Add` pour chaque initialiseur d’élément.

La classe suivante représente un contact avec un nom et une liste de numéros de téléphone :
```csharp
public class Contact
{
    string name;
    List<string> phoneNumbers = new List<string>();

    public string Name { get { return name; } set { name = value; } }

    public List<string> PhoneNumbers { get { return phoneNumbers; } }
}
```

Vous pouvez créer et initialiser un `List<Contact>` comme suit :
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
ce qui a le même effet que
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
où `__clist`, `__c1` et `__c2` sont des variables temporaires qui, sinon, sont inaccessibles et inaccessibles.

#### <a name="array-creation-expressions"></a>Expressions de création de tableau

Un *array_creation_expression* est utilisé pour créer une nouvelle instance d’une *array_type*.

```antlr
array_creation_expression
    : 'new' non_array_type '[' expression_list ']' rank_specifier* array_initializer?
    | 'new' array_type array_initializer
    | 'new' rank_specifier array_initializer
    ;
```

Une expression de création de tableau de la première forme alloue une instance de tableau du type qui résulte de la suppression de chacune des expressions individuelles de la liste d’expressions. Par exemple, l’expression de création de tableau `new int[10,20]` produit une instance de tableau de type `int[,]`, et l’expression de création de tableau `new int[10][,]` produit un tableau de type `int[][,]`. Chaque expression de la liste d’expressions doit être de type `int`, `uint`, `long`ou `ulong`, ou être implicitement convertible en un ou plusieurs de ces types. La valeur de chaque expression détermine la longueur de la dimension correspondante dans l’instance de tableau qui vient d’être allouée. Dans la mesure où la longueur d’une dimension de tableau ne doit pas être négative, il s’agit d’une erreur au moment de la compilation pour avoir un *constant_expression* avec une valeur négative dans la liste d’expressions.

Sauf dans un contexte non sécurisé ([contextes non sécurisés](unsafe-code.md#unsafe-contexts)), la disposition des tableaux n’est pas spécifiée.

Si une expression de création de tableau du premier formulaire comprend un initialiseur de tableau, chaque expression de la liste d’expressions doit être une constante et le rang et les longueurs de dimensions spécifiés par la liste d’expressions doivent correspondre à ceux de l’initialiseur de tableau.

Dans une expression de création de tableau de la deuxième ou de la troisième forme, le rang du type de tableau ou du spécificateur de rangs spécifié doit correspondre à celui de l’initialiseur de tableau. Les longueurs de dimensions individuelles sont déduites du nombre d’éléments dans chacun des niveaux d’imbrication correspondants de l’initialiseur de tableau. Ainsi, l’expression
```csharp
new int[,] {{0, 1}, {2, 3}, {4, 5}}
```
correspond exactement à
```csharp
new int[3, 2] {{0, 1}, {2, 3}, {4, 5}}
```

Une expression de création de tableau de la troisième forme est appelée ***expression de création de tableau implicitement typée***. Elle est similaire à la deuxième forme, à ceci près que le type d’élément du tableau n’est pas explicitement donné, mais déterminé comme le meilleur type commun ([recherche du meilleur type commun d’un ensemble d’expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)) du jeu d’expressions dans l’initialiseur de tableau. Dans le cas d’un tableau multidimensionnel, c’est-à-dire un où le *rank_specifier* contient au moins une virgule, cet ensemble comprend toutes les *expressions*qui se trouvent dans les *array_initializer*imbriqué s.

Les initialiseurs de tableau sont décrits plus en détail dans les [initialiseurs de tableau](arrays.md#array-initializers).

Le résultat de l’évaluation d’une expression de création de tableau est classé comme une valeur, à savoir une référence à l’instance de tableau nouvellement allouée. Le traitement au moment de l’exécution d’une expression de création de tableau se compose des étapes suivantes :

*  Les expressions de longueur de dimension des *expression_list* sont évaluées dans l’ordre, de gauche à droite. Après l’évaluation de chaque expression, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) vers l’un des types suivants est effectuée : `int`, `uint`, `long`, `ulong`. Le premier type de cette liste pour lequel une conversion implicite existe est choisi. Si l’évaluation d’une expression ou de la conversion implicite suivante provoque une exception, aucune autre expression n’est évaluée et aucune autre étape n’est exécutée.
*  Les valeurs calculées pour les longueurs de dimensions sont validées comme suit. Si une ou plusieurs valeurs sont inférieures à zéro, une `System.OverflowException` est levée et aucune autre étape n’est exécutée.
*  Une instance de tableau avec les longueurs de dimensions données est allouée. Si la mémoire disponible est insuffisante pour allouer la nouvelle instance, une `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.
*  Tous les éléments de la nouvelle instance de tableau sont initialisés à leurs valeurs par défaut ([valeurs par défaut](variables.md#default-values)).
*  Si l’expression de création de tableau contient un initialiseur de tableau, chaque expression de l’initialiseur de tableau est évaluée et assignée à son élément de tableau correspondant. Les évaluations et les assignations sont effectuées dans l’ordre dans lequel les expressions sont écrites dans l’initialiseur de tableau. en d’autres termes, les éléments sont initialisés dans l’ordre d’index de croissance, avec la dimension la plus à droite qui s’incrémente en premier. Si l’évaluation d’une expression donnée ou de l’assignation suivante à l’élément de tableau correspondant provoque une exception, aucun autre élément n’est initialisé (et les éléments restants auront donc leurs valeurs par défaut).

Une expression de création de tableau autorise l’instanciation d’un tableau avec des éléments d’un type tableau, mais les éléments d’un tel tableau doivent être initialisés manuellement. Par exemple, l’instruction
```csharp
int[][] a = new int[100][];
```
crée un tableau unidimensionnel avec 100 éléments de type `int[]`. La valeur initiale de chaque élément est `null`. Il n’est pas possible pour la même expression de création de tableau d’instancier également les sous-tableaux, et l’instruction
```csharp
int[][] a = new int[100][5];        // Error
```
génère une erreur au moment de la compilation. L’instanciation des sous-tableaux doit plutôt être effectuée manuellement, comme dans
```csharp
int[][] a = new int[100][];
for (int i = 0; i < 100; i++) a[i] = new int[5];
```

Lorsqu’un tableau de tableaux a une forme « rectangulaire », c’est-à-dire lorsque les sous-tableaux sont tous de la même longueur, il est plus efficace d’utiliser un tableau multidimensionnel. Dans l’exemple ci-dessus, l’instanciation du tableau de tableaux crée 101 objets, un tableau externe et des sous-tableaux 100. En revanche,
```csharp
int[,] = new int[100, 5];
```
crée un seul objet, un tableau à deux dimensions, et accomplit l’allocation dans une instruction unique.

Voici des exemples d’expressions de création de tableau implicitement typées :
```csharp
var a = new[] { 1, 10, 100, 1000 };                       // int[]

var b = new[] { 1, 1.5, 2, 2.5 };                         // double[]

var c = new[,] { { "hello", null }, { "world", "!" } };   // string[,]

var d = new[] { 1, "one", 2, "two" };                     // Error
```

La dernière expression provoque une erreur au moment de la compilation, car ni `int` ni `string` n’est implicitement convertible en l’autre, et il n’y a donc pas de type commun optimal. Une expression de création de tableau explicitement typée doit être utilisée dans ce cas, par exemple en spécifiant le type à `object[]`. L’un des éléments peut également être casté en un type de base commun, qui devient alors le type d’élément inféré.

Les expressions de création de tableau implicitement typées peuvent être combinées avec des initialiseurs d’objets anonymes ([expressions de création d’objets anonymes](expressions.md#anonymous-object-creation-expressions)) pour créer des structures de données typées anonymement. Par exemple :
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

#### <a name="delegate-creation-expressions"></a>Expressions de création de délégué

Un *delegate_creation_expression* est utilisé pour créer une nouvelle instance d’une *delegate_type*.

```antlr
delegate_creation_expression
    : 'new' delegate_type '(' expression ')'
    ;
```

L’argument d’une expression de création de délégué doit être un groupe de méthodes, une fonction anonyme ou une valeur du type au moment de la compilation `dynamic` ou une *delegate_type*. Si l’argument est un groupe de méthodes, il identifie la méthode et, pour une méthode d’instance, l’objet pour lequel créer un délégué. Si l’argument est une fonction anonyme, il définit directement les paramètres et le corps de la méthode de la cible du délégué. Si l’argument est une valeur, il identifie une instance de délégué de laquelle créer une copie.

Si le type de l' *expression* est `dynamic`au moment de la compilation, le *delegate_creation_expression* est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)) et les règles ci-dessous sont appliquées au moment de l’exécution à l’aide du type d’exécution de l' *expression*. Dans le cas contraire, les règles sont appliquées au moment de la compilation.

Le traitement au moment de la liaison d’une *delegate_creation_expression* de la forme `new D(E)`, où `D` est un *delegate_type* et `E` est une *expression*, se compose des étapes suivantes :

*  Si `E` est un groupe de méthodes, l’expression de création de délégué est traitée de la même façon qu’une conversion de groupe de méthodes ([conversions de groupe de méthodes](conversions.md#method-group-conversions)) de `E` à `D`.
*  Si `E` est une fonction anonyme, l’expression de création de délégué est traitée de la même façon qu’une conversion de fonction anonyme ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)) de `E` à `D`.
*  Si `E` est une valeur, `E` doit être compatible ([déclarations de délégué](delegates.md#delegate-declarations)) avec `D`et le résultat est une référence à un délégué nouvellement créé de type `D` qui fait référence à la même liste d’appel que `E`. Si `E` n’est pas compatible avec `D`, une erreur de compilation se produit.

Le traitement au moment de l’exécution d’une *delegate_creation_expression* de la forme `new D(E)`, où `D` est un *delegate_type* et `E` est une *expression*, se compose des étapes suivantes :

*   Si `E` est un groupe de méthodes, l’expression de création de délégué est évaluée comme une conversion de groupe de méthodes ([conversions de groupe de méthodes](conversions.md#method-group-conversions)) de `E` à `D`.
*   Si `E` est une fonction anonyme, la création du délégué est évaluée comme une conversion de fonction anonyme de `E` en `D` ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)).
*   Si `E` est une valeur d’un *delegate_type*:
    * `E` est évalué. Si cette évaluation provoque une exception, aucune autre étape n’est exécutée.
    * Si la valeur de `E` est `null`, une `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.
    * Une nouvelle instance du type délégué `D` est allouée. Si la mémoire disponible est insuffisante pour allouer la nouvelle instance, une `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.
    * La nouvelle instance de délégué est initialisée avec la même liste d’appel que l’instance de délégué donnée par `E`.

La liste d’appel d’un délégué est déterminée lorsque le délégué est instancié, puis reste constante pendant toute la durée de vie du délégué. En d’autres termes, il n’est pas possible de modifier les entités pouvant être appelées par Target d’un délégué une fois qu’il a été créé. Quand deux délégués sont combinés ou s’ils sont supprimés d’un autre ([déclarations déléguées](delegates.md#delegate-declarations)), un nouveau délégué est obtenu ; aucun contenu n’a été modifié pour le délégué existant.

Il n’est pas possible de créer un délégué qui fait référence à une propriété, un indexeur, un opérateur défini par l’utilisateur, un constructeur d’instance, un destructeur ou un constructeur statique.

Comme décrit ci-dessus, quand un délégué est créé à partir d’un groupe de méthodes, la liste de paramètres formels et le type de retour du délégué déterminent les méthodes surchargées à sélectionner. Dans l’exemple
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
le champ `A.f` est initialisé avec un délégué qui fait référence à la deuxième méthode `Square`, car cette méthode correspond exactement à la liste de paramètres formels et au type de retour de `DoubleFunc`. Si la deuxième méthode `Square` n’était pas présente, une erreur de compilation s’est produite.

#### <a name="anonymous-object-creation-expressions"></a>Expressions de création d’objets anonymes

Un *anonymous_object_creation_expression* est utilisé pour créer un objet d’un type anonyme.

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

Un initialiseur d’objet anonyme déclare un type anonyme et retourne une instance de ce type. Un type anonyme est un type de classe sans type qui hérite directement de `object`. Les membres d’un type anonyme sont une séquence de propriétés en lecture seule déduites de l’initialiseur d’objet anonyme utilisé pour créer une instance du type. Plus précisément, un initialiseur d’objet anonyme de la forme
```csharp
new { p1 = e1, p2 = e2, ..., pn = en }
```
déclare un type anonyme de la forme
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
où chaque `Tx` est le type de l’expression correspondante `ex`. L’expression utilisée dans un *member_declarator* doit avoir un type. Par conséquent, il s’agit d’une erreur de compilation pour qu’une expression d’un *member_declarator* soit null ou une fonction anonyme. Il s’agit également d’une erreur au moment de la compilation pour que l’expression ait un type non sécurisé.

Les noms d’un type anonyme et du paramètre de sa méthode `Equals` sont générés automatiquement par le compilateur et ne peuvent pas être référencés dans le texte du programme.

Dans le même programme, deux initialiseurs d’objets anonymes qui spécifient une séquence de propriétés des mêmes noms et types au moment de la compilation dans le même ordre produisent des instances du même type anonyme.

Dans l’exemple
```csharp
var p1 = new { Name = "Lawnmower", Price = 495.00 };
var p2 = new { Name = "Shovel", Price = 26.95 };
p1 = p2;
```
l’assignation sur la dernière ligne est autorisée, car `p1` et `p2` sont du même type anonyme.

Les méthodes `Equals` et `GetHashcode` sur les types anonymes substituent les méthodes héritées de `object`, et sont définies en termes de `Equals` et de `GetHashcode` des propriétés, afin que deux instances du même type anonyme soient égales si et seulement si toutes leurs propriétés sont égales.

Un déclarateur de membre peut être abrégé en un nom simple ([inférence de type](expressions.md#type-inference)), un accès de membre ([vérification au moment de la compilation de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), un accès de base ([accès de base](expressions.md#base-access)) ou un accès de membre conditionnel null ([expressions conditionnelles NULL comme initialiseurs de projection](expressions.md#null-conditional-expressions-as-projection-initializers)). C’est ce qu’on appelle un ***initialiseur de projection*** et est un raccourci pour une déclaration et une assignation à une propriété du même nom. Plus précisément, les déclarateurs de membres des formulaires
```csharp
identifier
expr.identifier
```
sont exactement équivalents à ce qui suit, respectivement :
```csharp
identifier = identifier
identifier = expr.identifier
```

Ainsi, dans un initialiseur de projection, l' *identificateur* sélectionne à la fois la valeur et le champ ou la propriété auquel la valeur est assignée. Intuitivement, un initialiseur de projection projette non seulement une valeur, mais également le nom de la valeur.

### <a name="the-typeof-operator"></a>Opérateur typeof

L’opérateur `typeof` est utilisé pour obtenir l’objet `System.Type` pour un type.

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

La première forme de *typeof_expression* se compose d’un mot clé `typeof` suivi d’un *type*entre parenthèses. Le résultat d’une expression de ce formulaire est l’objet `System.Type` pour le type indiqué. Il n’existe qu’un seul objet `System.Type` pour un type donné. Cela signifie que pour un type `T`, `typeof(T) == typeof(T)` a toujours la valeur true. Le *type* ne peut pas être `dynamic`.

La deuxième forme de *typeof_expression* se compose d’un mot clé `typeof` suivi d’un *unbound_type_name*entre parenthèses. Un *unbound_type_name* est très similaire à un *type_name* ([nom de l’espace de noms et type](basic-concepts.md#namespace-and-type-names)), à ceci près qu’un *unbound_type_name* contient *generic_dimension_specifier*s où un *type_name* contient *type_argument_list*s. Lorsque l’opérande d’un *typeof_expression* est une séquence de jetons qui satisfait les grammaires de *unbound_type_name* et *type_name*, à savoir lorsqu’il ne contient ni un *generic_dimension_specifier* ni un *type_argument_list*, la séquence de jetons est considérée comme un *type_name*. La signification d’un *unbound_type_name* est déterminée comme suit :

*  Convertissez la séquence de jetons en *type_name* en remplaçant chaque *generic_dimension_specifier* par un *type_argument_list* ayant le même nombre de virgules et le mot clé `object` comme chaque *type_argument*.
*  Évaluez le *type_name*résultant, en ignorant toutes les contraintes de paramètre de type.
*  Le *unbound_type_name* est résolu en type générique indépendant associé au type construit résultant ([types liés et indépendants](types.md#bound-and-unbound-types)).

Le résultat de l' *typeof_expression* est l’objet `System.Type` pour le type générique indépendant qui en résulte.

La troisième forme de *typeof_expression* se compose d’un mot clé `typeof` suivi d’un mot clé `void` entre parenthèses. Le résultat d’une expression de ce formulaire est l’objet `System.Type` qui représente l’absence d’un type. L’objet de type retourné par `typeof(void)` est différent de l’objet de type retourné pour tout type. Cet objet de type spécial est utile dans les bibliothèques de classes qui autorisent la réflexion sur les méthodes dans le langage, où ces méthodes souhaitent avoir un moyen de représenter le type de retour d’une méthode quelconque, y compris les méthodes void, avec une instance de `System.Type`.

L’opérateur `typeof` peut être utilisé sur un paramètre de type. Le résultat est l’objet `System.Type` pour le type au moment de l’exécution qui a été lié au paramètre de type. L’opérateur `typeof` peut également être utilisé sur un type construit ou un type générique indépendant ([types liés et indépendants](types.md#bound-and-unbound-types)). L’objet `System.Type` pour un type générique indépendant n’est pas le même que l’objet `System.Type` du type d’instance. Le type d’instance est toujours un type construit fermé au moment de l’exécution, de sorte que son objet `System.Type` dépend des arguments de type au moment de l’exécution, tandis que le type générique indépendant n’a aucun argument de type.

L’exemple
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
produit la sortie suivante :
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

Notez que `int` et `System.Int32` sont du même type.

Notez également que le résultat de `typeof(X<>)` ne dépend pas de l’argument de type, mais le résultat de `typeof(X<T>)` le fait.

### <a name="the-checked-and-unchecked-operators"></a>Opérateurs vérifiés et non vérifiés

Les opérateurs `checked` et `unchecked` sont utilisés pour contrôler le ***contexte*** de contrôle de dépassement de capacité pour les opérations arithmétiques de type intégral et les conversions.

```antlr
checked_expression
    : 'checked' '(' expression ')'
    ;

unchecked_expression
    : 'unchecked' '(' expression ')'
    ;
```

L’opérateur `checked` évalue l’expression contenue dans un contexte vérifié, tandis que l’opérateur `unchecked` évalue l’expression contenue dans un contexte non vérifié. Un *checked_expression* ou *unchecked_expression* correspond exactement à un *parenthesized_expression* ([expressions entre parenthèses](expressions.md#parenthesized-expressions)), sauf que l’expression contenue est évaluée dans le contexte de contrôle de dépassement de capacité donné.

Le contexte de contrôle de dépassement de capacité peut également être contrôlé via les instructions `checked` et `unchecked` ([les instructions checked et unchecked](statements.md#the-checked-and-unchecked-statements)).

Les opérations suivantes sont affectées par le contexte de contrôle de dépassement de capacité établi par le `checked` et `unchecked` les opérateurs et les instructions :

*  Les opérateurs unaires `++` et `--` prédéfinis ([suffixe d’incrémentation et de décrémentation](expressions.md#postfix-increment-and-decrement-operators) et [opérateurs d’incrémentation et de décrémentation de préfixe](expressions.md#prefix-increment-and-decrement-operators)), lorsque l’opérande est de type intégral.
*  Opérateur unaire `-` prédéfini ([opérateur moins unaire](expressions.md#unary-minus-operator)), lorsque l’opérande est de type intégral.
*  Les opérateurs binaires prédéfinis `+`, `-`, `*`et `/` ([opérateurs arithmétiques](expressions.md#arithmetic-operators)), lorsque les deux opérandes sont des types intégraux.
*  Conversions numériques explicites ([conversions numériques explicites](conversions.md#explicit-numeric-conversions)) d’un type intégral vers un autre type intégral, ou de `float` ou `double` à un type intégral.

Lorsque l’une des opérations ci-dessus génère un résultat qui est trop grand pour être représenté dans le type de destination, le contexte dans lequel l’opération est effectuée contrôle le comportement résultant :

*  Dans un contexte de `checked`, si l’opération est une expression constante ([expressions constantes](expressions.md#constant-expressions)), une erreur de compilation se produit. Dans le cas contraire, lorsque l’opération est effectuée au moment de l’exécution, une `System.OverflowException` est levée.
*  Dans un contexte de `unchecked`, le résultat est tronqué en ignorant les bits de poids fort qui ne tiennent pas dans le type de destination.

Pour les expressions non constantes (expressions évaluées au moment de l’exécution) qui ne sont pas encadrées par des opérateurs ou des instructions `checked` ou `unchecked`, le contexte de contrôle de dépassement de capacité par défaut est `unchecked`, à moins que des facteurs externes (tels que les commutateurs de compilateur et la configuration de l’environnement d’exécution) appellent pour `checked` évaluation.

Pour les expressions constantes (expressions qui peuvent être entièrement évaluées au moment de la compilation), le contexte de contrôle de dépassement de capacité par défaut est toujours `checked`. À moins qu’une expression constante soit explicitement placée dans un contexte de `unchecked`, les débordements qui se produisent pendant l’évaluation de l’expression au moment de la compilation provoquent toujours des erreurs au moment de la compilation.

Le corps d’une fonction anonyme n’est pas affecté par `checked` ou `unchecked` contextes dans lesquels la fonction anonyme se produit.

Dans l’exemple
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
aucune erreur de compilation n’est signalée, car aucune des expressions ne peut être évaluée au moment de la compilation. Au moment de l’exécution, la méthode `F` lève une `System.OverflowException`et la méthode `G` retourne-727379968 (les 32 de poids faible du résultat hors limites). Le comportement de la méthode `H` dépend du contexte de vérification de dépassement de capacité par défaut pour la compilation, mais est identique à `F` ou identique à `G`.

Dans l’exemple
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
les débordements qui se produisent lors de l’évaluation des expressions constantes dans `F` et `H` entraînent le signalement des erreurs de compilation, car les expressions sont évaluées dans un contexte de `checked`. Un dépassement de capacité se produit également lors de l’évaluation de l’expression constante dans `G`, mais étant donné que l’évaluation a lieu dans un contexte de `unchecked`, le dépassement de capacité n’est pas signalé.

Les opérateurs `checked` et `unchecked` affectent uniquement le contexte de contrôle de dépassement de capacité pour les opérations qui sont textuellement contenues dans les jetons «`(`» et «`)`». Les opérateurs n’ont aucun effet sur les fonctions membres qui sont appelées suite à l’évaluation de l’expression contenue. Dans l’exemple
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
l’utilisation de `checked` dans `F` n’affecte pas l’évaluation de `x * y` dans `Multiply`, donc `x * y` est évaluée dans le contexte de contrôle de dépassement de capacité par défaut.

L’opérateur `unchecked` est pratique lors de l’écriture de constantes des types intégraux signés en notation hexadécimale. Par exemple :
```csharp
class Test
{
    public const int AllBits = unchecked((int)0xFFFFFFFF);

    public const int HighBit = unchecked((int)0x80000000);
}
```

Les deux constantes hexadécimales ci-dessus sont de type `uint`. Étant donné que les constantes se trouvent en dehors de la plage de `int`, sans l’opérateur `unchecked`, les casts en `int` produisent des erreurs de compilation.

Les opérateurs et les instructions `checked` et `unchecked` permettent aux programmeurs de contrôler certains aspects de certains calculs numériques. Toutefois, le comportement de certains opérateurs numériques dépend des types de données des opérandes. Par exemple, la multiplication de deux décimales produit toujours une exception en cas de dépassement de capacité, même dans une construction de `unchecked` explicite. De même, la multiplication de deux valeurs float n’entraîne jamais d’exception en cas de dépassement de capacité, même dans une construction de `checked` explicite. En outre, les autres opérateurs ne sont jamais affectés par le mode de vérification, qu’il s’agisse d’une valeur par défaut ou explicite.

### <a name="default-value-expressions"></a>Expressions de valeur par défaut

Une expression de valeur par défaut est utilisée pour obtenir la valeur par défaut ([valeurs par défaut](variables.md#default-values)) d’un type. En général, une expression de valeur par défaut est utilisée pour les paramètres de type, car elle peut ne pas être connue si le paramètre de type est un type valeur ou un type référence. (Aucune conversion n’existe entre le littéral `null` et un paramètre de type, sauf si le paramètre de type est connu comme étant un type référence.)

```antlr
default_value_expression
    : 'default' '(' type ')'
    ;
```

Si le *type* d’une *default_value_expression* évalue au moment de l’exécution sur un type référence, le résultat est `null` converti en ce type. Si le *type* d’une *default_value_expression* évalue au moment de l’exécution sur un type valeur, le résultat est la valeur par défaut de *Value_type*([constructeurs par défaut](types.md#default-constructors)).

Une *default_value_expression* est une expression constante ([expressions constantes](expressions.md#constant-expressions)) si le type est un type référence ou un paramètre de type qui est connu comme étant un type référence ([contraintes de paramètre de type](classes.md#type-parameter-constraints)). En outre, une *default_value_expression* est une expression constante si le type est l’un des types de valeurs suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`ou n’importe quel type énumération.


### <a name="nameof-expressions"></a>Expressions Nameof

Un *nameof_expression* est utilisé pour obtenir le nom d’une entité de programme sous la forme d’une chaîne constante.

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

Par programmation, l’opérande *named_entity* est toujours une expression. Étant donné que `nameof` n’est pas un mot clé réservé, une expression nameof est toujours ambiguë syntaxiquement avec un appel du nom simple `nameof`. Pour des raisons de compatibilité, si une recherche de nom ([noms simples](expressions.md#simple-names)) du nom `nameof` réussie, l’expression est traitée comme un *invocation_expression* , que l’appel soit légal ou non. Dans le cas contraire, il s’agit d’un *nameof_expression*.

La signification de la *named_entity* d’une *nameof_expression* est la signification de celle-ci en tant qu’expression ; autrement dit, qu’il s’agisse d’un *simple_name*, d’un *base_access* ou d’un *member_access*. Toutefois, lorsque la recherche décrite dans [noms simples](expressions.md#simple-names) et [accès aux membres](expressions.md#member-access) génère une erreur parce qu’un membre d’instance a été trouvé dans un contexte statique, une *nameof_expression* ne produit aucune erreur de ce type.

Il s’agit d’une erreur de compilation pour un *named_entity* désignant un groupe de méthodes pour avoir une *type_argument_list*. Il s’agit d’une erreur au moment de la compilation pour qu’un *named_entity_target* ait le type `dynamic`.

Une *nameof_expression* est une expression constante de type `string`et n’a aucun effet au moment de l’exécution. En particulier, sa *named_entity* n’est pas évaluée et est ignorée dans le cadre de l’analyse de l’assignation définie ([règles générales pour les expressions simples](variables.md#general-rules-for-simple-expressions)). Sa valeur est le dernier identificateur de la *named_entity* avant le *type_argument_list*final facultatif, transformée de la façon suivante :

* Le préfixe «`@`», s’il est utilisé, est supprimé.
* Chaque *unicode_escape_sequence* est transformée en son caractère Unicode correspondant.
* Toutes les *formatting_characters* sont supprimées.

Ce sont les mêmes transformations appliquées dans les [identificateurs](lexical-structure.md#identifiers) lors du test d’égalité entre les identificateurs.

TODO : exemples

### <a name="anonymous-method-expressions"></a>Expressions de méthode anonymes

Une *anonymous_method_expression* est l’une des deux façons de définir une fonction anonyme. Celles-ci sont décrites plus en détail dans [expressions de fonction anonymes](expressions.md#anonymous-function-expressions).

## <a name="unary-operators"></a>Les opérateurs unaires.

Les opérateurs `?`, `+`, `-`, `!`, `~`, `++`, `--`, Cast et `await` sont appelés opérateurs unaires.

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

Si l’opérande d’un *unary_expression* a le type au moment de la compilation `dynamic`, il est lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas, le type au moment de la compilation de l' *unary_expression* est `dynamic`et la résolution décrite ci-après aura lieu au moment de l’exécution à l’aide du type d’exécution de l’opérande.

### <a name="null-conditional-operator"></a>Opérateur conditionnel null

L’opérateur conditionnel null applique une liste d’opérations à son opérande uniquement si cet opérande n’a pas la valeur null. Sinon, le résultat de l’application de l’opérateur est `null`.

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

La liste des opérations peut inclure l’accès au membre et les opérations d’accès aux éléments (qui peuvent eux-mêmes être conditionnels null), ainsi que l’appel.

Par exemple, l’expression `a.b?[0]?.c()` est une *null_conditional_expression* avec une `a.b` *primary_expression* et *null_conditional_operations* `?[0]` (accès aux éléments conditionnels null), `?.c` (accès aux membres conditionnels null) et `()` (appel).

Pour une *null_conditional_expression* `E` avec un `P`*primary_expression* , laissez `E0` être l’expression obtenue en supprimant textuellement la `?` de début de chaque *null_conditional_operations de `E`* qui en a une. D’un point de vue conceptuel, `E0` est l’expression qui sera évaluée si aucune des vérifications null représentées par le `?`s ne trouve de `null`.

Par ailleurs, laissez `E1` être l’expression obtenue en supprimant textuellement le `?` de début de la première des *null_conditional_operations* dans `E`. Cela peut aboutir à une *expression primaire* (s’il n’y en a qu’une seule `?`) ou à une autre *null_conditional_expression*.

Par exemple, si `E` est l’expression `a.b?[0]?.c()`, `E0` est l’expression `a.b[0].c()` et `E1` est l’expression `a.b[0]?.c()`.

Si `E0` est classé comme Nothing, `E` est classé comme Nothing. Dans le cas contraire, E est classé comme une valeur.

`E0` et `E1` sont utilisés pour déterminer la signification des `E`:

*  Si `E` se produit en tant que *statement_expression* la signification de `E` est identique à celle de l’instruction

   ```csharp
   if ((object)P != null) E1;
   ```

   sauf que P n’est évalué qu’une seule fois.

*  Sinon, si `E0` est classé comme rien, une erreur de compilation se produit.

*  Dans le cas contraire, laissez `T0` le type de `E0`.

   *  Si `T0` est un paramètre de type qui n’est pas connu comme étant un type référence ou un type valeur n’acceptant pas les valeurs NULL, une erreur de compilation se produit.

   *  Si `T0` est un type valeur qui n’autorise pas les valeurs NULL, le type de `E` est `T0?`, et la signification de `E` est identique à

      ```csharp
      ((object)P == null) ? (T0?)null : E1
      ```

      Hormis le fait que `P` n’est évalué qu’une seule fois.

   *  Dans le cas contraire, le type de E est T0, et la signification de E est la même que

      ```csharp
      ((object)P == null) ? null : E1
      ```

      Hormis le fait que `P` n’est évalué qu’une seule fois.

Si `E1` est lui-même une *null_conditional_expression*, ces règles sont à nouveau appliquées, en imbriquant les tests pour `null` jusqu’à ce qu’il n’y ait plus de `?`, et l’expression a été réduite jusqu’au `E0`de l’expression primaire.

Par exemple, si l’expression `a.b?[0]?.c()` se présente sous la forme d’une instruction-expression, comme dans l’instruction :
```csharp
a.b?[0]?.c();
```
son sens est équivalent à :
```csharp
if (a.b != null) a.b[0]?.c();
```
ce qui équivaut à :
```csharp
if (a.b != null) if (a.b[0] != null) a.b[0].c();
```
À ceci près que les `a.b` et les `a.b[0]` ne sont évalués qu’une seule fois.

Si elle se produit dans un contexte où sa valeur est utilisée, comme dans :
```csharp
var x = a.b?[0]?.c();
```
en partant du principe que le type de l’appel final n’est pas un type valeur non Nullable, son sens est équivalent à :
```csharp
var x = (a.b == null) ? null : (a.b[0] == null) ? null : a.b[0].c();
```
à ceci près que les `a.b` et les `a.b[0]` ne sont évalués qu’une seule fois.

#### <a name="null-conditional-expressions-as-projection-initializers"></a>Expressions conditionnelles null en tant qu’initialiseurs de projection

Une expression conditionnelle null est uniquement autorisée en tant que *member_declarator* dans un *anonymous_object_creation_expression* ([expressions de création d’objet anonyme](expressions.md#anonymous-object-creation-expressions)) si elle se termine par un accès au membre (éventuellement nullement conditionnel). Par programmation, cette exigence peut être exprimée comme suit :

```antlr
null_conditional_member_access
    : primary_expression null_conditional_operations? '?' '.' identifier type_argument_list?
    | primary_expression null_conditional_operations '.' identifier type_argument_list?
    ;
```

Il s’agit d’un cas particulier de la grammaire pour *null_conditional_expression* ci-dessus. La production pour *member_declarator* dans les [expressions de création d’objet anonyme](expressions.md#anonymous-object-creation-expressions) comprend alors uniquement *null_conditional_member_access*.

#### <a name="null-conditional-expressions-as-statement-expressions"></a>Expressions conditionnelles null en tant qu’expressions d’instruction

Une expression conditionnelle null est uniquement autorisée en tant que *statement_expression* ([instructions d’expression](statements.md#expression-statements)) si elle se termine par un appel. Par programmation, cette exigence peut être exprimée comme suit :

```antlr
null_conditional_invocation_expression
    : primary_expression null_conditional_operations '(' argument_list? ')'
    ;
```

Il s’agit d’un cas particulier de la grammaire pour *null_conditional_expression* ci-dessus. La production de *statement_expression* dans les [instructions d’expression](statements.md#expression-statements) comprend alors uniquement *null_conditional_invocation_expression*.


### <a name="unary-plus-operator"></a>Opérateur plus unaire

Pour une opération de la forme `+x`, la résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. L’opérande est converti en type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur. Les opérateurs plus unaires prédéfinis sont les suivants :

```csharp
int operator +(int x);
uint operator +(uint x);
long operator +(long x);
ulong operator +(ulong x);
float operator +(float x);
double operator +(double x);
decimal operator +(decimal x);
```

Pour chacun de ces opérateurs, le résultat est simplement la valeur de l’opérande.

### <a name="unary-minus-operator"></a>Opération moins unaire

Pour une opération de la forme `-x`, la résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. L’opérande est converti en type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur. Les opérateurs de négation prédéfinis sont les suivants :

*  Négation d’entier :

   ```csharp
   int operator -(int x);
   long operator -(long x);
   ```

   Le résultat est calculé en soustrayant `x` de zéro. Si la valeur de `x` est la plus petite valeur représentable du type d’opérande (-2 ^ 31 pour `int` ou-2 ^ 63 pour `long`), la négation mathématique de `x` n’est pas représentable dans le type d’opérande. Si cela se produit dans un contexte de `checked`, un `System.OverflowException` est levé ; Si elle se produit dans un contexte de `unchecked`, le résultat est la valeur de l’opérande et le dépassement de capacité n’est pas signalé.

   Si l’opérande de l’opérateur de négation est de type `uint`, il est converti en type `long`et le type du résultat est `long`. Une exception est la règle qui autorise l’écriture de la valeur de `int`-2147483648 (-2 ^ 31) sous la forme d’un littéral d’entier décimal ([littéraux entiers](lexical-structure.md#integer-literals)).

   Si l’opérande de l’opérateur de négation est de type `ulong`, une erreur de compilation se produit. Une exception est la règle qui autorise l’écriture de la valeur de `long`-9223372036854775808 (-2 ^ 63) sous la forme d’un littéral d’entier décimal ([littéraux entiers](lexical-structure.md#integer-literals)).

*  Négation à virgule flottante :

   ```csharp
   float operator -(float x);
   double operator -(double x);
   ```

   Le résultat est la valeur de `x` avec son signe inversé. Si `x` est NaN, le résultat est également NaN.

*  Négation décimale :

   ```csharp
   decimal operator -(decimal x);
   ```

   Le résultat est calculé en soustrayant `x` de zéro. La négation décimale équivaut à utiliser l’opérateur moins unaire de type `System.Decimal`.

### <a name="logical-negation-operator"></a>Opérateur de négation logique

Pour une opération de la forme `!x`, la résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. L’opérande est converti en type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur. Il n’existe qu’un seul opérateur de négation logique prédéfini :
```csharp
bool operator !(bool x);
```

Cet opérateur calcule la négation logique de l’opérande : si l’opérande est `true`, le résultat est `false`. Si l’opérande est `false`, le résultat est `true`.

### <a name="bitwise-complement-operator"></a>Opérateur de complément de bits

Pour une opération de la forme `~x`, la résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. L’opérande est converti en type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur. Les opérateurs de complément de bits prédéfinis sont les suivants :
```csharp
int operator ~(int x);
uint operator ~(uint x);
long operator ~(long x);
ulong operator ~(ulong x);
```

Pour chacun de ces opérateurs, le résultat de l’opération est le complément de bits de `x`.

Chaque type d’énumération `E` fournit implicitement l’opérateur de complément de bits suivant :

```csharp
E operator ~(E x);
```

Le résultat de l’évaluation de `~x`, où `x` est une expression d’un type d’énumération `E` avec un type sous-jacent `U`, est exactement le même que l’évaluation de `(E)(~(U)x)`, à ceci près que la conversion en `E` est toujours effectuée comme si dans un contexte `unchecked` ([opérateurs activés et désactivés](expressions.md#the-checked-and-unchecked-operators)).

### <a name="prefix-increment-and-decrement-operators"></a>Opérateurs préfixés d’incrémentation et de décrémentation

```antlr
pre_increment_expression
    : '++' unary_expression
    ;

pre_decrement_expression
    : '--' unary_expression
    ;
```

L’opérande d’une opération d’incrémentation ou de décrémentation de préfixe doit être une expression classifiée comme une variable, un accès à une propriété ou un accès à un indexeur. Le résultat de l’opération est une valeur du même type que l’opérande.

Si l’opérande d’une opération de préfixe d’incrémentation ou de décrémentation est un accès à une propriété ou un indexeur, la propriété ou l’indexeur doit avoir un `get` et un accesseur `set`. Si ce n’est pas le cas, une erreur de temps de liaison se produit.

La résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. Les opérateurs `++` et `--` prédéfinis existent pour les types suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`et tout type enum. Les opérateurs de `++` prédéfinis retournent la valeur produite par l’ajout de 1 à l’opérande, et les opérateurs de `--` prédéfinis retournent la valeur produite par la soustraction de 1 de l’opérande. Dans un contexte de `checked`, si le résultat de l’addition ou de la soustraction est en dehors de la plage du type de résultat et que le type de résultat est un type intégral ou un type enum, une `System.OverflowException` est levée.

Le traitement au moment de l’exécution d’une opération de préfixe d’incrémentation ou de décrémentation de la forme `++x` ou `--x` se compose des étapes suivantes :

*   Si `x` est classée en tant que variable :
    * `x` est évaluée pour produire la variable.
    * L’opérateur sélectionné est appelé avec la valeur de `x` comme argument.
    * La valeur retournée par l’opérateur est stockée dans l’emplacement donné par l’évaluation de `x`.
    * La valeur retournée par l’opérateur devient le résultat de l’opération.
*   Si `x` est classée comme un accès à une propriété ou un indexeur :
    * L’expression d’instance (si `x` n’est pas `static`) et la liste d’arguments (si `x` est un accès à un indexeur) associée à `x` sont évaluées, et les résultats sont utilisés dans les appels de `get` et d’accesseur `set` suivants.
    * L’accesseur `get` de `x` est appelé.
    * L’opérateur sélectionné est appelé avec la valeur retournée par l’accesseur `get` comme argument.
    * L’accesseur `set` de `x` est appelé avec la valeur retournée par l’opérateur comme son argument `value`.
    * La valeur retournée par l’opérateur devient le résultat de l’opération.

Les opérateurs `++` et `--` prennent également en charge la notation suffixée ([opérateurs d’incrémentation et de décrémentation suffixés](expressions.md#postfix-increment-and-decrement-operators)). En général, le résultat de `x++` ou `x--` est la valeur de `x` avant l’opération, alors que le résultat de `++x` ou `--x` est la valeur de `x` après l’opération. Dans les deux cas, `x` lui-même a la même valeur après l’opération.

Une implémentation de `operator++` ou `operator--` peut être appelée à l’aide de la notation suffixée ou de préfixe. Il n’est pas possible d’avoir des implémentations d’opérateur distinctes pour les deux notations.

### <a name="cast-expressions"></a>Expressions de cast

Un *cast_expression* est utilisé pour convertir explicitement une expression en un type donné.

```antlr
cast_expression
    : '(' type ')' unary_expression
    ;
```

*Cast_expression* de la forme `(T)E`, où `T` est un *type* et `E` est un *unary_expression*, effectue une conversion explicite ([conversions explicites](conversions.md#explicit-conversions)) de la valeur de `E` en type `T`. S’il n’existe aucune conversion explicite de `E` à `T`, une erreur de liaison s’est produite. Dans le cas contraire, le résultat est la valeur produite par la conversion explicite. Le résultat est toujours classifié comme une valeur, même si `E` désigne une variable.

La grammaire d’un *cast_expression* provoque certaines ambiguïtés syntaxiques. Par exemple, l’expression `(x)-y` peut être interprétée comme un *cast_expression* (un cast de `-y` en type `x`) ou en tant que *additive_expression* combiné avec un *parenthesized_expression* (qui calcule la valeur `x - y)`.

Pour résoudre *cast_expression* ambiguïtés, la règle suivante existe : une séquence d’un ou plusieurs *jetons*([espace blanc](lexical-structure.md#white-space)) entre parenthèses est considérée comme le début d’un *cast_expression* uniquement si au moins l’une des conditions suivantes est vraie :

*  La séquence de jetons est une grammaire correcte pour un *type*, mais pas pour une *expression*.
*  La séquence de jetons est une grammaire correcte pour un *type*, et le jeton qui suit immédiatement les parenthèses fermantes est le jeton «`~`», le jeton «`!`», le jeton «`(`», un *identificateur* ([séquences d’échappement de caractères Unicode](lexical-structure.md#unicode-character-escape-sequences)), un *littéral* ([littéraux](lexical-structure.md#literals)) ou tout *mot clé* ([Mots clés](lexical-structure.md#keywords)) sauf `as` et `is`.

Le terme « grammaire correcte » ci-dessus signifie uniquement que la séquence de jetons doit se conformer à la production grammaticale particulière. Elle ne tient pas compte de la signification réelle des identificateurs constitutifs. Par exemple, si `x` et `y` sont des identificateurs, `x.y` est une grammaire correcte pour un type, même si `x.y` ne désigne pas un type.

À partir de la règle de désambiguïsation, il s’agit de savoir que, si `x` et `y` sont des identificateurs, `(x)y`, `(x)(y)`et `(x)(-y)` sont *cast_expression*s, mais `(x)-y` ne l’est pas, même si `x` identifie un type. Toutefois, si `x` est un mot clé qui identifie un type prédéfini (tel que `int`), les quatre formes sont *cast_expression*s (car ce mot clé n’est peut-être pas une expression par lui-même).

### <a name="await-expressions"></a>Expressions await

L’opérateur await est utilisé pour interrompre l’évaluation de la fonction Async englobante jusqu’à ce que l’opération asynchrone représentée par l’opérande soit terminée.

```antlr
await_expression
    : 'await' unary_expression
    ;
```

Une *await_expression* est uniquement autorisée dans le corps d’une fonction Async ([itérateurs](classes.md#iterators)). Dans la fonction Async englobante la plus proche, un *await_expression* peut ne pas se produire dans les emplacements suivants :

*  Dans une fonction anonyme imbriquée (non asynchrone)
*  À l’intérieur du bloc d’un *lock_statement*
*  Dans un contexte non sécurisé

Notez qu’un *await_expression* ne peut pas se produire dans la plupart des cas au sein d’un *query_expression*, car ceux-ci sont convertis de façon syntaxique pour utiliser des expressions lambda non asynchrones.

À l’intérieur d’une fonction Async, `await` ne peut pas être utilisé en tant qu’identificateur. Il n’y a donc pas d’ambiguïté syntaxique entre await-expressions et diverses expressions impliquant des identificateurs. En dehors des fonctions Async, `await` agit comme un identificateur normal.

L’opérande d’un *await_expression* est appelé la ***tâche***. Il représente une opération asynchrone qui peut ou non se terminer au moment où le *await_expression* est évalué. L’objectif de l’opérateur await est de suspendre l’exécution de la fonction Async englobante jusqu’à ce que la tâche attendue soit terminée, puis d’obtenir son résultat.

#### <a name="awaitable-expressions"></a>Expressions await

La tâche d’une expression await doit être ***attendue***. Une expression `t` est await si l’un des éléments suivants est attendu :

*  `t` est de type au moment de la compilation `dynamic`
*  `t` a une instance accessible ou une méthode d’extension appelée `GetAwaiter` sans paramètres ni aucun paramètre de type, et un type de retour `A` pour lequel tous les éléments suivants sont détenus :
   * `A` implémente l’interface `System.Runtime.CompilerServices.INotifyCompletion` (ci-après dénommées `INotifyCompletion` par souci de concision)
   * `A` a une propriété d’instance accessible accessible en lecture `IsCompleted` de type `bool`
   * `A` a une méthode d’instance accessible `GetResult` sans paramètres et sans paramètres de type

L’objectif de la méthode `GetAwaiter` est d’obtenir un ***await*** pour la tâche. Le type `A` est appelé le ***type d’attente*** pour l’expression await.

L’objectif de la propriété `IsCompleted` est de déterminer si la tâche est déjà terminée. Dans ce cas, il n’est pas nécessaire d’interrompre l’évaluation.

L’objectif de la méthode `INotifyCompletion.OnCompleted` consiste à inscrire une « continuation » à la tâche ; c’est-à-dire un délégué (de type `System.Action`) qui sera appelé une fois la tâche terminée.

L’objectif de la méthode `GetResult` est d’obtenir le résultat de la tâche une fois qu’elle est terminée. Ce résultat peut être terminé avec succès, éventuellement avec une valeur de résultat, ou il peut s’agir d’une exception levée par la méthode `GetResult`.

#### <a name="classification-of-await-expressions"></a>Classification des expressions await

L’expression `await t` est classée de la même façon que l’expression `(t).GetAwaiter().GetResult()`. Ainsi, si le type de retour de `GetResult` est `void`, le *await_expression* est classé comme Nothing. S’il a un type de retour non void `T`, le *await_expression* est classé comme une valeur de type `T`.

#### <a name="runtime-evaluation-of-await-expressions"></a>Évaluation du runtime des expressions await

Au moment de l’exécution, l’expression `await t` est évaluée comme suit :

*  Un `a` await est obtenu en évaluant l’expression `(t).GetAwaiter()`.
*  Une `b` `bool` est obtenue en évaluant l’expression `(a).IsCompleted`.
*  Si `b` est `false`, l’évaluation varie selon que `a` implémente les `System.Runtime.CompilerServices.ICriticalNotifyCompletion` d’interface (ci-après appelées `ICriticalNotifyCompletion` par souci de concision). Cette vérification s’effectue au moment de la liaison. par exemple, lors de l’exécution, si `a` a le type au moment de la compilation `dynamic`, et au moment de la compilation, sinon. Laissez `r` désigner le délégué de reprise ([itérateurs](classes.md#iterators)) :
    * Si `a` n’implémente pas `ICriticalNotifyCompletion`, l’expression `(a as (INotifyCompletion)).OnCompleted(r)` est évaluée.
    * Si `a` implémente `ICriticalNotifyCompletion`, l’expression `(a as (ICriticalNotifyCompletion)).UnsafeOnCompleted(r)` est évaluée.
    * L’évaluation est ensuite interrompue et le contrôle est retourné à l’appelant actuel de la fonction Async.
*  Soit immédiatement après (si `b` a été `true`), soit lors de l’appel ultérieur du délégué de reprise (si `b` était `false`), l’expression `(a).GetResult()` est évaluée. Si elle retourne une valeur, cette valeur est le résultat de l' *await_expression*. Sinon, le résultat est Nothing.

L’implémentation de l’interface await des méthodes d’interface `INotifyCompletion.OnCompleted` et `ICriticalNotifyCompletion.UnsafeOnCompleted` doit entraîner l’appel de la `r` du délégué au plus une fois. Dans le cas contraire, le comportement de la fonction Async englobante n’est pas défini.

## <a name="arithmetic-operators"></a>Opérateurs arithmétiques

Les opérateurs `*`, `/`, `%`, `+`et `-` sont appelés opérateurs arithmétiques.

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

Si un opérande d’un opérateur arithmétique a le type au moment de la compilation `dynamic`, l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas, le type au moment de la compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous a lieu au moment de l’exécution à l’aide du type d’exécution des opérandes qui ont le type au moment de la compilation `dynamic`.

### <a name="multiplication-operator"></a>Opérateur de multiplication

Pour une opération de la forme `x * y`, la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.

Les opérateurs de multiplication prédéfinis sont répertoriés ci-dessous. Les opérateurs calculent tous le produit de `x` et `y`.

*  Multiplication d’entiers :

   ```csharp
   int operator *(int x, int y);
   uint operator *(uint x, uint y);
   long operator *(long x, long y);
   ulong operator *(ulong x, ulong y);
   ```

   Dans un contexte de `checked`, si le produit est en dehors de la plage du type de résultat, un `System.OverflowException` est levé. Dans un contexte de `unchecked`, les dépassements de capacité ne sont pas signalés et les bits de poids fort significatifs en dehors de la plage du type de résultat sont ignorés.


*  Multiplication à virgule flottante :

   ```csharp
   float operator *(float x, float y);
   double operator *(double x, double y);
   ```

   Le produit est calculé en fonction des règles de l’arithmétique IEEE 754. Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies différentes de zéro, de zéros, d’infinis et de NaN. Dans le tableau, `x` et `y` sont des valeurs finies positives. `z` est le résultat de `x * y`. Si le résultat est trop grand pour le type de destination, `z` est l’infini. Si le résultat est trop petit pour le type de destination, `z` est égal à zéro.

   |      |      |      |     |     |      |      |     |
   |:----:|-----:|:----:|:---:|:---:|:----:|:----:|:----|
   |      | +y   | -y   | +0  | -0  | +inf | -inf | NaN | 
   | +x   | +z   | -z   | +0  | -0  | +inf | -inf | NaN | 
   | -x   | -z   | +z   | -0  | +0  | -inf | +inf | NaN | 
   | +0   | +0   | -0   | +0  | -0  | NaN  | NaN  | NaN | 
   | -0   | -0   | +0   | -0  | +0  | NaN  | NaN  | NaN | 
   | +inf | +inf | -inf | NaN | NaN | +inf | -inf | NaN | 
   | -inf | -inf | +inf | NaN | NaN | -inf | +inf | NaN | 
   | NaN  | NaN  | NaN  | NaN | NaN | NaN  | NaN  | NaN | 

*  Multiplication décimale :

   ```csharp
   decimal operator *(decimal x, decimal y);
   ```

   Si la valeur résultante est trop grande pour être représentée au format `decimal`, un `System.OverflowException` est levé. Si la valeur de résultat est trop petite pour être représentée au format `decimal`, le résultat est égal à zéro. L’échelle du résultat, avant tout arrondi, est la somme des échelles des deux opérandes.

   La multiplication décimale équivaut à utiliser l’opérateur de multiplication de type `System.Decimal`.


### <a name="division-operator"></a>Opérateur de division

Pour une opération de la forme `x / y`, la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.

Les opérateurs de division prédéfinis sont répertoriés ci-dessous. Les opérateurs calculent tous le quotient de `x` et `y`.

*  Division d’entier :

   ```csharp
   int operator /(int x, int y);
   uint operator /(uint x, uint y);
   long operator /(long x, long y);
   ulong operator /(ulong x, ulong y);
   ```

   Si la valeur de l’opérande de droite est égale à zéro, une `System.DivideByZeroException` est levée.

   La Division arrondit le résultat vers zéro. Par conséquent, la valeur absolue du résultat est le plus grand entier possible qui est inférieur ou égal à la valeur absolue du quotient des deux opérandes. Le résultat est zéro ou positif lorsque les deux opérandes ont le même signe et zéro ou une valeur négative lorsque les deux opérandes ont des signes opposés.

   Si l’opérande de gauche est le plus petit `int` représentable ou la même valeur de `long` et que l’opérande de droite est `-1`, un dépassement de capacité se produit. Dans un contexte de `checked`, cela entraîne la levée d’une `System.ArithmeticException` (ou d’une sous-classe). Dans un contexte de `unchecked`, il est défini par l’implémentation pour déterminer si une `System.ArithmeticException` (ou une sous-classe de celle-ci) est levée ou si le dépassement de capacité n’est pas signalé avec la valeur résultante qui est celle de l’opérande de gauche.

*  Division à virgule flottante :

   ```csharp
   float operator /(float x, float y);
   double operator /(double x, double y);
   ```

   Le quotient est calculé en fonction des règles de l’arithmétique IEEE 754. Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies différentes de zéro, de zéros, d’infinis et de NaN. Dans le tableau, `x` et `y` sont des valeurs finies positives. `z` est le résultat de `x / y`. Si le résultat est trop grand pour le type de destination, `z` est l’infini. Si le résultat est trop petit pour le type de destination, `z` est égal à zéro.

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | +y   | -y   | +0   | -0   | +inf | -inf | NaN  | 
   | +x   | +z   | -z   | +inf | -inf | +0   | -0   | NaN  | 
   | -x   | -z   | +z   | -inf | +inf | -0   | +0   | NaN  | 
   | +0   | +0   | -0   | NaN  | NaN  | +0   | -0   | NaN  | 
   | -0   | -0   | +0   | NaN  | NaN  | -0   | +0   | NaN  | 
   | +inf | +inf | -inf | +inf | -inf | NaN  | NaN  | NaN  | 
   | -inf | -inf | +inf | -inf | +inf | NaN  | NaN  | NaN  | 
   | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | 

*  Division décimale :

   ```csharp
   decimal operator /(decimal x, decimal y);
   ```

   Si la valeur de l’opérande de droite est égale à zéro, une `System.DivideByZeroException` est levée. Si la valeur résultante est trop grande pour être représentée au format `decimal`, un `System.OverflowException` est levé. Si la valeur de résultat est trop petite pour être représentée au format `decimal`, le résultat est égal à zéro. L’échelle du résultat est la plus petite échelle qui conserve un résultat égal à la valeur décimale représentable la plus proche du résultat mathématique vrai.

   La Division décimale équivaut à utiliser l’opérateur de division de type `System.Decimal`.


### <a name="remainder-operator"></a>Opérateur de reste

Pour une opération de la forme `x % y`, la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.

Les opérateurs de reste prédéfinis sont répertoriés ci-dessous. Les opérateurs calculent tous le reste de la division entre `x` et `y`.

*  Reste entier :

   ```csharp
   int operator %(int x, int y);
   uint operator %(uint x, uint y);
   long operator %(long x, long y);
   ulong operator %(ulong x, ulong y);
   ```

   Le résultat de `x % y` est la valeur produite par `x - (x / y) * y`. Si `y` est égal à zéro, un `System.DivideByZeroException` est levé.

   Si l’opérande de gauche est le plus petit `int` ou `long` valeur et que l’opérande de droite est `-1`, une `System.OverflowException` est levée. Dans le cas contraire, ne lève `x % y` d’exception lorsque `x / y` ne lèvera pas d’exception.

*  Reste à virgule flottante :

   ```csharp
   float operator %(float x, float y);
   double operator %(double x, double y);
   ```

   Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies différentes de zéro, de zéros, d’infinis et de NaN. Dans le tableau, `x` et `y` sont des valeurs finies positives. `z` est le résultat de `x % y` et est calculé comme `x - n * y`, où `n` est le plus grand entier possible qui est inférieur ou égal à `x / y`. Cette méthode de calcul du reste est analogue à celle utilisée pour les opérandes entiers, mais diffère de la définition IEEE 754 (dans laquelle `n` est l’entier le plus proche de `x / y`).

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | +y   | -y   | +0   | -0   | +inf | -inf | NaN  | 
   | +x   | +z   | +z   | NaN  | NaN  | x    | x    | NaN  | 
   | -x   | -z   | -z   | NaN  | NaN  | -x   | -x   | NaN  | 
   | +0   | +0   | +0   | NaN  | NaN  | +0   | +0   | NaN  | 
   | -0   | -0   | -0   | NaN  | NaN  | -0   | -0   | NaN  | 
   | +inf | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | 
   | -inf | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | 
   | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | 

*  Reste décimal :

   ```csharp
   decimal operator %(decimal x, decimal y);
   ```

   Si la valeur de l’opérande de droite est égale à zéro, une `System.DivideByZeroException` est levée. L’échelle du résultat, avant tout arrondi, est la plus grande des échelles des deux opérandes, et le signe du résultat, s’il est différent de zéro, est le même que celui de `x`.

   Le reste décimal est équivalent à l’utilisation de l’opérateur de reste de type `System.Decimal`.


### <a name="addition-operator"></a>Opérateur d’addition

Pour une opération de la forme `x + y`, la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.

Les opérateurs d’addition prédéfinis sont répertoriés ci-dessous. Pour les types numériques et d’énumération, les opérateurs d’addition prédéfinis calculent la somme des deux opérandes. Quand l’un des opérandes ou les deux sont de type chaîne, les opérateurs d’addition prédéfinis concatènent la représentation sous forme de chaîne des opérandes.

*  Ajout d’un entier :

   ```csharp
   int operator +(int x, int y);
   uint operator +(uint x, uint y);
   long operator +(long x, long y);
   ulong operator +(ulong x, ulong y);
   ```

   Dans un contexte de `checked`, si la somme est en dehors de la plage du type de résultat, une `System.OverflowException` est levée. Dans un contexte de `unchecked`, les dépassements de capacité ne sont pas signalés et les bits de poids fort significatifs en dehors de la plage du type de résultat sont ignorés.

*  Addition à virgule flottante :

   ```csharp
   float operator +(float x, float y);
   double operator +(double x, double y);
   ```

   La somme est calculée en fonction des règles de l’arithmétique IEEE 754. Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies différentes de zéro, de zéros, d’infinis et de NaN. Dans le tableau, `x` et `y` sont des valeurs finies non nulles, et `z` est le résultat de `x + y`. Si `x` et `y` ont la même magnitude mais des signes opposés, `z` est un zéro positif. Si `x + y` est trop grand pour être représenté dans le type de destination, `z` est un infini avec le même signe que `x + y`.

   |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | o    | +0   | -0   | +inf | -inf | NaN  | 
   | x    | e    | x    | x    | +inf | -inf | NaN  | 
   | +0   | o    | +0   | +0   | +inf | -inf | NaN  | 
   | -0   | o    | +0   | -0   | +inf | -inf | NaN  | 
   | +inf | +inf | +inf | +inf | +inf | NaN  | NaN  | 
   | -inf | -inf | -inf | -inf | NaN  | -inf | NaN  | 
   | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | 

*  Addition de nombres décimaux :

   ```csharp
   decimal operator +(decimal x, decimal y);
   ```

   Si la valeur résultante est trop grande pour être représentée au format `decimal`, un `System.OverflowException` est levé. L’échelle du résultat, avant tout arrondi, est la plus grande des échelles des deux opérandes.

   L’addition décimale équivaut à utiliser l’opérateur d’addition de type `System.Decimal`.

*  Addition d’énumération. Chaque type d’énumération fournit implicitement les opérateurs prédéfinis suivants, où `E` est le type enum, et `U` est le type sous-jacent de `E`:

   ```csharp
   E operator +(E x, U y);
   E operator +(U x, E y);
   ```

   Au moment de l’exécution, ces opérateurs sont évalués exactement comme `(E)((U)x + (U)y)`.

*  Concaténation de chaînes :

   ```csharp
   string operator +(string x, string y);
   string operator +(string x, object y);
   string operator +(object x, string y);
   ```

   Ces surcharges de l’opérateur binaire `+` effectuent la concaténation de chaînes. Si un opérande de concaténation de chaîne est `null`, une chaîne vide est substituée. Sinon, tout argument qui n’est pas une chaîne est converti en sa représentation sous forme de chaîne en appelant la méthode `ToString` virtuelle héritée du type `object`. Si `ToString` retourne `null`, une chaîne vide est substituée.

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

   Le résultat de l’opérateur de concaténation de chaînes est une chaîne qui se compose des caractères de l’opérande de gauche, suivis des caractères de l’opérande de droite. L’opérateur de concaténation de chaînes ne retourne jamais de valeur `null`. Une `System.OutOfMemoryException` peut être levée si la mémoire disponible est insuffisante pour allouer la chaîne résultante.

*  Combinaison de délégués. Chaque type délégué fournit implicitement l’opérateur prédéfini suivant, où `D` est le type délégué :

   ```csharp
   D operator +(D x, D y);
   ```

   L’opérateur de `+` binaire effectue une combinaison de délégués lorsque les deux opérandes sont d’un type délégué `D`. (Si les opérandes ont des types délégués différents, une erreur de temps de liaison se produit.) Si le premier opérande est `null`, le résultat de l’opération est la valeur du deuxième opérande (même s’il est également `null`). Sinon, si le second opérande est `null`, le résultat de l’opération est la valeur du premier opérande. Dans le cas contraire, le résultat de l’opération est une nouvelle instance de délégué qui, lorsqu’elle est appelée, appelle le premier opérande, puis appelle le second opérande. Pour obtenir des exemples de combinaison de délégués, consultez [opérateur de soustraction](expressions.md#subtraction-operator) et [appel de délégué](delegates.md#delegate-invocation). Étant donné que `System.Delegate` n’est pas un type délégué, `operator` `+` n’est pas défini pour celui-ci.

### <a name="subtraction-operator"></a>Opérateur de soustraction

Pour une opération de la forme `x - y`, la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.

Les opérateurs de soustraction prédéfinis sont répertoriés ci-dessous. Les opérateurs soustraitnt tous les `y` de `x`.

*  Soustraction d’entiers :

   ```csharp
   int operator -(int x, int y);
   uint operator -(uint x, uint y);
   long operator -(long x, long y);
   ulong operator -(ulong x, ulong y);
   ```

   Dans un contexte de `checked`, si la différence se situe en dehors de la plage du type de résultat, une `System.OverflowException` est levée. Dans un contexte de `unchecked`, les dépassements de capacité ne sont pas signalés et les bits de poids fort significatifs en dehors de la plage du type de résultat sont ignorés.

*  Soustraction à virgule flottante :

   ```csharp
   float operator -(float x, float y);
   double operator -(double x, double y);
   ```

   La différence est calculée en fonction des règles de l’arithmétique IEEE 754. Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies différentes de zéro, de zéros, d’infinis et de valeurs NaN. Dans le tableau, `x` et `y` sont des valeurs finies non nulles, et `z` est le résultat de `x - y`. Si `x` et `y` sont égaux, `z` est un zéro positif. Si `x - y` est trop grand pour être représenté dans le type de destination, `z` est un infini avec le même signe que `x - y`.

   |      |      |      |      |      |      |     |
   |:----:|:----:|:----:|:----:|:----:|:----:|:---:|
   |      | o    | +0   | -0   | +inf | -inf | NaN | 
   | x    | e    | x    | x    | -inf | +inf | NaN | 
   | +0   | -y   | +0   | +0   | -inf | +inf | NaN | 
   | -0   | -y   | -0   | +0   | -inf | +inf | NaN | 
   | +inf | +inf | +inf | +inf | NaN  | +inf | NaN | 
   | -inf | -inf | -inf | -inf | -inf | NaN  | NaN | 
   | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN | 

*  Soustraction de décimale :

   ```csharp
   decimal operator -(decimal x, decimal y);
   ```

   Si la valeur résultante est trop grande pour être représentée au format `decimal`, un `System.OverflowException` est levé. L’échelle du résultat, avant tout arrondi, est la plus grande des échelles des deux opérandes.

   La soustraction de décimale équivaut à utiliser l’opérateur de soustraction de type `System.Decimal`.

*  Soustraction d’énumération. Chaque type d’énumération fournit implicitement l’opérateur prédéfini suivant, où `E` est le type enum, et `U` est le type sous-jacent de `E`:

   ```csharp
   U operator -(E x, E y);
   ```

   Cet opérateur est évalué exactement comme `(U)((U)x - (U)y)`. En d’autres termes, l’opérateur calcule la différence entre les valeurs ordinales de `x` et `y`, et le type du résultat est le type sous-jacent de l’énumération.

   ```csharp
   E operator -(E x, U y);
   ```

   Cet opérateur est évalué exactement comme `(E)((U)x - y)`. En d’autres termes, l’opérateur soustrait une valeur du type sous-jacent de l’énumération, ce qui produit une valeur de l’énumération.

*  Suppression du délégué. Chaque type délégué fournit implicitement l’opérateur prédéfini suivant, où `D` est le type délégué :

   ```csharp
   D operator -(D x, D y);
   ```

   L’opérateur de `-` binaire effectue une suppression de délégué lorsque les deux opérandes sont d’un type délégué `D`. Si les opérandes ont des types délégués différents, une erreur au moment de la liaison se produit. Si le premier opérande a la valeur `null`, le résultat de l’opération est `null`. Sinon, si le second opérande est `null`, le résultat de l’opération est la valeur du premier opérande. Sinon, les deux opérandes représentent les listes d’appel ([déclarations de délégué](delegates.md#delegate-declarations)) ayant une ou plusieurs entrées, et le résultat est une nouvelle liste d’appel comprenant la liste de la première opérande avec les entrées du deuxième opérande supprimées, à condition que la liste du deuxième opérande soit une sous-liste contiguë correcte du premier.     (Pour déterminer l’égalité des sous-listes, les entrées correspondantes sont comparées à celles de l’opérateur d’égalité de délégué ([opérateurs d’égalité de délégué](expressions.md#delegate-equality-operators)).) Dans le cas contraire, le résultat est la valeur de l’opérande gauche. Aucune des listes d’opérandes n’est modifiée dans le processus. Si la liste du deuxième opérande correspond à plusieurs sous-listes d’entrées contiguës dans la liste du premier opérande, la sous-liste correspondante la plus à droite des entrées contiguës est supprimée. Si la suppression aboutit à une liste vide, le résultat est `null`. Par exemple :

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

## <a name="shift-operators"></a>Opérateurs de décalage

Les opérateurs `<<` et `>>` sont utilisés pour effectuer des opérations de décalage de bits.

```antlr
shift_expression
    : additive_expression
    | shift_expression '<<' additive_expression
    | shift_expression right_shift additive_expression
    ;
```

Si un opérande d’un *shift_expression* a le type au moment de la compilation `dynamic`, l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas, le type au moment de la compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous a lieu au moment de l’exécution à l’aide du type d’exécution des opérandes qui ont le type au moment de la compilation `dynamic`.

Pour une opération de la forme `x << count` ou `x >> count`, la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.

Lors de la déclaration d’un opérateur de décalage surchargé, le type du premier opérande doit toujours être la classe ou le struct contenant la déclaration d’opérateur, et le type du second opérande doit toujours être `int`.

Les opérateurs de décalage prédéfinis sont répertoriés ci-dessous.

*  Décalage vers la gauche :

   ```csharp
   int operator <<(int x, int count);
   uint operator <<(uint x, int count);
   long operator <<(long x, int count);
   ulong operator <<(ulong x, int count);
   ```

   L’opérateur `<<` décale `x` vers la gauche d’un nombre de bits calculés comme décrit ci-dessous.

   Les bits de poids fort en dehors de la plage du type de résultat de `x` sont ignorés, les bits restants sont décalés vers la gauche et les positions de bits vides de poids faible sont définies sur zéro.

*  Décalage vers la droite :

   ```csharp
   int operator >>(int x, int count);
   uint operator >>(uint x, int count);
   long operator >>(long x, int count);
   ulong operator >>(ulong x, int count);
   ```

   L’opérateur `>>` décale `x` vers la droite d’un certain nombre de bits calculés comme décrit ci-dessous.

   Lorsque `x` est de type `int` ou `long`, les bits de poids faible de `x` sont ignorés, les bits restants sont décalés vers la droite, et les positions de bits vides de poids fort ont la valeur zéro si `x` est non négative et défini sur un si `x` est négatif.

   Lorsque `x` est de type `uint` ou `ulong`, les bits de poids faible de `x` sont ignorés, les bits restants sont décalés vers la droite, et les positions de bits vides de poids fort sont définies sur zéro.

Pour les opérateurs prédéfinis, le nombre de bits à décaler est calculé comme suit :

*  Lorsque le type de `x` est `int` ou `uint`, le nombre de décalages est donné par les cinq bits de poids faible de `count`. En d’autres termes, le nombre de décalages est calculé à partir de `count & 0x1F`.
*  Lorsque le type de `x` est `long` ou `ulong`, le nombre de décalages est donné par les six bits de poids faible de `count`. En d’autres termes, le nombre de décalages est calculé à partir de `count & 0x3F`.

Si le nombre de décalages résultant est égal à zéro, les opérateurs de décalage retournent simplement la valeur de `x`.

Les opérations de décalage ne provoquent jamais de dépassements de capacité et produisent les mêmes résultats dans des contextes de `checked` et de `unchecked`.

Lorsque l’opérande gauche de l’opérateur `>>` est d’un type intégral signé, l’opérateur effectue un décalage arithmétique vers la droite, où la valeur du bit le plus significatif (le bit de signe) de l’opérande est propagée aux positions de bit vides de poids fort. Lorsque l’opérande gauche de l’opérateur `>>` est d’un type intégral non signé, l’opérateur effectue un décalage logique vers la droite où les positions de bits vides d’ordre élevé ont toujours la valeur zéro. Pour effectuer l’opération inverse de celle déduite du type d’opérande, vous pouvez utiliser des casts explicites. Par exemple, si `x` est une variable de type `int`, l’opération `unchecked((int)((uint)x >> y))` effectue un décalage logique à droite de `x`.

## <a name="relational-and-type-testing-operators"></a>Opérateurs relationnels et de test de type

Les opérateurs `==`, `!=`, `<`, `>`, `<=`, `>=`, `is` et `as` sont appelés opérateurs relationnels et de test de type.

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

L’opérateur `is` est décrit dans [l’opérateur is](expressions.md#the-is-operator) et l’opérateur `as` est décrit dans [l’opérateur as](expressions.md#the-as-operator).

Les opérateurs `==`, `!=`, `<`, `>`, `<=` et `>=` sont des ***opérateurs de comparaison***.

Si un opérande d’un opérateur de comparaison a le type `dynamic`au moment de la compilation, l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas, le type au moment de la compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous a lieu au moment de l’exécution à l’aide du type d’exécution des opérandes qui ont le type au moment de la compilation `dynamic`.

Pour une opération de la forme `x` *op* `y`, où *op* est un opérateur de comparaison, la résolution de surcharge ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.

Les opérateurs de comparaison prédéfinis sont décrits dans les sections suivantes. Tous les opérateurs de comparaison prédéfinis retournent un résultat de type `bool`, comme décrit dans le tableau suivant.


| __Opération__ | __Résultat__                                                       |
|---------------|------------------------------------------------------------------|
| `x == y`      | `true` si `x` est égal à `y`, `false` dans le cas contraire                 | 
| `x != y`      | `true` si `x` n’est pas égal à `y`, `false` dans le cas contraire             | 
| `x < y`       | `true` si `x` est inférieur à `y`, `false` dans le cas contraire                | 
| `x > y`       | `true` si `x` est supérieur à `y`, `false` dans le cas contraire             | 
| `x <= y`      | `true` si `x` est inférieur ou égal à `y`, `false` dans le cas contraire    | 
| `x >= y`      | `true` si `x` est supérieur ou égal à `y`, `false` sinon | 

### <a name="integer-comparison-operators"></a>Opérateurs de comparaison d’entiers

Les opérateurs de comparaison d’entiers prédéfinis sont les suivants :
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

Chacun de ces opérateurs compare les valeurs numériques des deux opérandes entiers et retourne une valeur `bool` qui indique si la relation particulière est `true` ou `false`.

### <a name="floating-point-comparison-operators"></a>Opérateurs de comparaison de nombres à virgule flottante

Les opérateurs de comparaison prédéfinis en virgule flottante sont les suivants :
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

Les opérateurs comparent les opérandes en fonction des règles de la norme IEEE 754 :

*  Si l’un des opérandes est NaN, le résultat est `false` pour tous les opérateurs, à l’exception des `!=`, pour lesquels le résultat est `true`. Pour deux opérandes, `x != y` produit toujours le même résultat que `!(x == y)`. Toutefois, lorsque l’un des opérandes ou les deux sont NaN, les opérateurs `<`, `>`, `<=`et `>=` ne produisent pas les mêmes résultats que la négation logique de l’opérateur opposé. Par exemple, si l’un des `x` et `y` est NaN, `x < y` est `false`, mais `!(x >= y)` est `true`.
*  Quand aucun des opérandes n’est NaN, les opérateurs comparent les valeurs des deux opérandes à virgule flottante en ce qui concerne le classement.

   ```csharp
   -inf < -max < ... < -min < -0.0 == +0.0 < +min < ... < +max < +inf
   ```

   où `min` et `max` sont les valeurs finies positives les plus petites et les plus grandes qui peuvent être représentées dans le format à virgule flottante donné. Les effets notables de ce classement sont les suivants :
   * Les zéros positifs et négatifs sont considérés comme égaux.
   * Un infini négatif est considéré comme inférieur à toutes les autres valeurs, mais égal à un autre infini négatif.
   * Un infini positif est considéré comme supérieur à toutes les autres valeurs, mais égal à un autre infini positif.

### <a name="decimal-comparison-operators"></a>Opérateurs de comparaison décimale

Les opérateurs de comparaison décimaux prédéfinis sont les suivants :
```csharp
bool operator ==(decimal x, decimal y);
bool operator !=(decimal x, decimal y);
bool operator <(decimal x, decimal y);
bool operator >(decimal x, decimal y);
bool operator <=(decimal x, decimal y);
bool operator >=(decimal x, decimal y);
```

Chacun de ces opérateurs compare les valeurs numériques des deux opérandes décimaux et retourne une valeur `bool` qui indique si la relation particulière est `true` ou `false`. Chaque comparaison décimale équivaut à utiliser l’opérateur relationnel ou d’égalité correspondant de type `System.Decimal`.

### <a name="boolean-equality-operators"></a>Opérateurs d’égalité booléennes

Les opérateurs d’égalité booléenne prédéfinis sont les suivants :
```csharp
bool operator ==(bool x, bool y);
bool operator !=(bool x, bool y);
```

Le résultat de `==` est `true` si `x` et `y` sont `true` ou si `x` et `y` sont `false`. Sinon, le résultat est `false`.

Le résultat de `!=` est `false` si `x` et `y` sont `true` ou si `x` et `y` sont `false`. Sinon, le résultat est `true`. Quand les opérandes sont de type `bool`, l’opérateur `!=` produit le même résultat que l’opérateur `^`.

### <a name="enumeration-comparison-operators"></a>Opérateurs de comparaison d’énumération

Chaque type d’énumération fournit implicitement les opérateurs de comparaison prédéfinis suivants :
```csharp
bool operator ==(E x, E y);
bool operator !=(E x, E y);
bool operator <(E x, E y);
bool operator >(E x, E y);
bool operator <=(E x, E y);
bool operator >=(E x, E y);
```

Le résultat de l’évaluation de `x op y`, où `x` et `y` sont des expressions d’un type d’énumération `E` avec un type sous-jacent `U`, et `op` est l’un des opérateurs de comparaison, est exactement le même que l’évaluation de `((U)x) op ((U)y)`. En d’autres termes, les opérateurs de comparaison de type énumération comparent simplement les valeurs intégrales sous-jacentes des deux opérandes.

### <a name="reference-type-equality-operators"></a>Opérateurs d’égalité de type référence

Les opérateurs d’égalité de type référence prédéfinis sont les suivants :
```csharp
bool operator ==(object x, object y);
bool operator !=(object x, object y);
```

Les opérateurs retournent le résultat de la comparaison des deux références pour l’égalité ou l’absence d’égalité.

Étant donné que les opérateurs d’égalité de type référence prédéfinis acceptent les opérandes de type `object`, ils s’appliquent à tous les types qui ne déclarent pas les membres `operator ==` et `operator !=` applicables. À l’inverse, tous les opérateurs d’égalité définis par l’utilisateur applicables masquent effectivement les opérateurs d’égalité de type référence prédéfinis.

Les opérateurs d’égalité de type référence prédéfinis nécessitent l’un des éléments suivants :

*  Les deux opérandes sont une valeur d’un type connu comme étant un *reference_type* ou le `null`littéral. En outre, une conversion de référence explicite ([conversions de références explicites](conversions.md#explicit-reference-conversions)) existe à partir du type de l’un des opérandes vers le type de l’autre opérande.
*  Un opérande est une valeur de type `T` où `T` est un *type_parameter* et l’autre opérande est le littéral `null`. En outre `T` n’a pas la contrainte de type valeur.

À moins que l’une de ces conditions ne soit vraie, une erreur de liaison au moment de la liaison se produit. Les implications notables de ces règles sont les suivantes :

*  Il s’agit d’une erreur de liaison au moment de la liaison pour utiliser les opérateurs d’égalité de type référence prédéfinis pour comparer deux références qui sont connues pour être différentes au moment de la liaison. Par exemple, si les types au moment de la liaison des opérandes sont deux types de classe `A` et `B`, et si ni `A` ni `B` dérivent de l’autre, il serait impossible pour les deux opérandes de référencer le même objet. Ainsi, l’opération est considérée comme une erreur au moment de la liaison.
*  Les opérateurs d’égalité de type référence prédéfinis n’autorisent pas la comparaison des opérandes de type valeur. Par conséquent, à moins qu’un type struct ne déclare ses propres opérateurs d’égalité, il n’est pas possible de comparer les valeurs de ce type struct.
*  Les opérateurs d’égalité de type référence prédéfinis n’entraînent jamais la levée des opérations de boxing pour leurs opérandes. Il serait inutile d’effectuer ces opérations de boxing, car les références aux instances boxed nouvellement allouées seraient nécessairement différentes de toutes les autres références.
*  Si un opérande d’un type de paramètre de type `T` est comparé à `null`et que le type de `T` au moment de l’exécution est un type valeur, le résultat de la comparaison est `false`.

L’exemple suivant vérifie si un argument d’un type de paramètre de type sans contrainte est `null`.
```csharp
class C<T>
{
    void F(T x) {
        if (x == null) throw new ArgumentNullException();
        ...
    }
}
```

La construction `x == null` est autorisée même si `T` peut représenter un type valeur, et le résultat est simplement défini pour être `false` quand `T` est un type valeur.

Pour une opération de la forme `x == y` ou `x != y`, s’il existe des `operator ==` ou des `operator !=` applicables, les règles de résolution de surcharge d’opérateur ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) sélectionnent cet opérateur au lieu de l’opérateur d’égalité de type référence prédéfini. Toutefois, il est toujours possible de sélectionner l’opérateur d’égalité de type référence prédéfini en effectuant un cast explicite de l’un des opérandes ou des deux en type `object`. L’exemple
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
génère la sortie
```console
True
False
False
False
```

Les variables `s` et `t` font référence à deux instances `string` distinctes contenant les mêmes caractères. La première comparaison génère `True`, car l’opérateur d’égalité de chaînes prédéfini ([opérateurs d’égalité de chaînes](expressions.md#string-equality-operators)) est sélectionné lorsque les deux opérandes sont de type `string`. Les autres comparaisons `False`nt toutes les sorties, car l’opérateur d’égalité de type référence prédéfini est sélectionné quand l’un des opérandes ou les deux sont de type `object`.

Notez que la technique ci-dessus n’est pas significative pour les types valeur. L’exemple
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
génère `False`, car les casts créent des références à deux instances distinctes de valeurs de `int` boxed.

### <a name="string-equality-operators"></a>Opérateurs d’égalité de chaînes

Les opérateurs d’égalité de chaînes prédéfinis sont les suivants :
```csharp
bool operator ==(string x, string y);
bool operator !=(string x, string y);
```

Deux valeurs `string` sont considérées comme égales quand l’une des conditions suivantes est vraie :

*  Les deux valeurs sont `null`.
*  Les deux valeurs sont des références non null aux instances de chaîne qui ont des longueurs identiques et des caractères identiques dans chaque position de caractère.

Les opérateurs d’égalité de chaînes comparent les valeurs de chaîne plutôt que les références de chaîne. Lorsque deux instances de chaîne distinctes contiennent exactement la même séquence de caractères, les valeurs des chaînes sont égales, mais les références sont différentes. Comme décrit dans [opérateurs d’égalité de type référence](expressions.md#reference-type-equality-operators), les opérateurs d’égalité de type référence peuvent être utilisés pour comparer des références de chaîne à la place de valeurs de chaîne.

### <a name="delegate-equality-operators"></a>Opérateurs d’égalité de délégué

Chaque type délégué fournit implicitement les opérateurs de comparaison prédéfinis suivants :

```csharp
bool operator ==(System.Delegate x, System.Delegate y);
bool operator !=(System.Delegate x, System.Delegate y);
```

Deux instances de délégué sont considérées comme étant égales comme suit :

*  Si l’une des instances de délégué est `null`, elles sont égales si et seulement si les deux sont `null`.
*  Si les délégués ont un type au moment de l’exécution différent, ils ne sont jamais égaux.
*  Si les deux instances de délégué ont une liste d’appel ([déclarations de délégué](delegates.md#delegate-declarations)), ces instances sont égales si et seulement si leurs listes d’appel sont de la même longueur, et que chaque entrée de la liste d’appel de l’une d’elles est égale (comme indiqué ci-dessous) à l’entrée correspondante, dans l’ordre, dans la liste d’appel de l’autre.

Les règles suivantes régissent l’égalité des entrées de la liste d’appel :

*  Si deux entrées de liste d’appel font toutes deux référence à la même méthode statique, les entrées sont égales.
*  Si deux entrées de liste d’appel font toutes deux référence à la même méthode non statique sur le même objet cible (tel que défini par les opérateurs d’égalité de référence), les entrées sont égales.
*  Les entrées de la liste d’invocation générées à partir de l’évaluation d' *anonymous_method_expression*s ou d' *lambda_expression*s sémantiquement identiques avec le même jeu d’instances de variable externe capturées sont autorisées (mais pas obligatoires) pour être égales.

### <a name="equality-operators-and-null"></a>Opérateurs d’égalité et null

Les opérateurs `==` et `!=` permettent à un opérande d’être une valeur d’un type Nullable et l’autre est le littéral `null`, même si aucun opérateur prédéfini ou défini par l’utilisateur (sous forme non levée) n’existe pour l’opération.

Pour une opération de l’une des formes
```csharp
x == null
null == x
x != null
null != x
```
où `x` est une expression d’un type Nullable, si la résolution de surcharge d’opérateur ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) ne parvient pas à trouver un opérateur applicable, le résultat est à la place calculé à partir de la propriété `HasValue` de `x`. Plus précisément, les deux premières formes sont converties en `!x.HasValue`, et les deux dernières formes sont traduites en `x.HasValue`.

### <a name="the-is-operator"></a>Opérateur is

L’opérateur `is` est utilisé pour vérifier dynamiquement si le type d’exécution d’un objet est compatible avec un type donné. Le résultat de l’opération `E is T`, où `E` est une expression et `T` est un type, est une valeur booléenne indiquant si `E` peut être converti en type `T` par une conversion de référence, une conversion boxing ou une conversion unboxing. L’opération est évaluée comme suit, après que les arguments de type ont été substitués à tous les paramètres de type :

*  Si `E` est une fonction anonyme, une erreur se produit au moment de la compilation
*  Si `E` est un groupe de méthodes ou le littéral `null`, de si le type de `E` est un type référence ou un type Nullable et que la valeur de `E` est null, le résultat est false.
*  Dans le cas contraire, laissez `D` représenter le type dynamique de `E` comme suit :
   * Si le type de `E` est un type référence, `D` est le type au moment de l’exécution de la référence de l’instance par `E`.
   * Si le type de `E` est un type Nullable, `D` est le type sous-jacent de ce type Nullable.
   * Si le type de `E` est un type valeur qui n’autorise pas les valeurs NULL, `D` est le type de `E`.
*  Le résultat de l’opération dépend `D` et `T` comme suit :
   * Si `T` est un type référence, le résultat est true si `D` et `T` sont du même type, si `D` est un type référence et une conversion de référence implicite de `D` à `T` existe, ou si `D` est un type valeur et qu’une conversion boxing de `D` en `T` existe.
   * Si `T` est un type Nullable, le résultat est true si `D` est le type sous-jacent de `T`.
   * Si `T` est un type valeur qui n’autorise pas les valeurs NULL, le résultat est true si `D` et `T` sont du même type.
   * Dans le cas contraire, le résultat est false.

Notez que les conversions définies par l’utilisateur ne sont pas prises en compte par l’opérateur `is`.

### <a name="the-as-operator"></a>Opérateur as

L’opérateur `as` est utilisé pour convertir explicitement une valeur en un type référence ou un type Nullable donné. Contrairement à une expression de cast ([expressions de cast](expressions.md#cast-expressions)), l’opérateur `as` ne lève jamais d’exception. Au lieu de cela, si la conversion indiquée n’est pas possible, la valeur résultante est `null`.

Dans une opération de la forme `E as T`, `E` doit être une expression et `T` doit être un type référence, un paramètre de type connu comme étant un type référence ou un type Nullable. En outre, au moins l’une des conditions suivantes doit être vraie, sinon une erreur de compilation se produit :

*  Une identité ([conversion d’identité](conversions.md#identity-conversion)), un Nullable implicite ([conversions implicites Nullable](conversions.md#implicit-nullable-conversions)), une référence implicite (conversions de[référence implicites](conversions.md#implicit-reference-conversions)), une conversion boxing ([conversions boxing](conversions.md#boxing-conversions)), un Nullable explicite ([conversions](conversions.md#explicit-nullable-conversions)explicites Nullable), une référence explicite ([conversions de références explicites](conversions.md#explicit-reference-conversions)) ou une `T``E` conversion unboxing ([conversions unboxing](conversions.md#unboxing-conversions)) est
*  Le type de `E` ou `T` est un type ouvert.
*  `E` est le littéral `null`.

Si le type au moment de la compilation de `E` n’est pas `dynamic`, l’opération `E as T` produit le même résultat que
```csharp
E is T ? (T)(E) : (T)null
```
sauf que `E` n’est évalué qu’une seule fois. Le compilateur peut être supposé optimiser `E as T` pour exécuter au plus un contrôle de type dynamique au lieu des deux contrôles de type dynamique qui sont implicites dans le cadre de l’expansion ci-dessus.

Si le type de `E` au moment de la compilation est `dynamic`, contrairement à l’opérateur de cast, l’opérateur `as` n’est pas lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Par conséquent, dans ce cas, le développement est le suivant :
```csharp
E is T ? (T)(object)(E) : (T)null
```

Notez que certaines conversions, telles que les conversions définies par l’utilisateur, ne sont pas possibles avec l’opérateur `as` et doivent plutôt être effectuées à l’aide d’expressions de cast.

Dans l’exemple
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
le paramètre de type `T` de `G` est connu comme un type référence, car il a la contrainte de classe. Toutefois, le paramètre de type `U` de `H` n’est pas ; par conséquent, l’utilisation de l’opérateur `as` dans `H` n’est pas autorisée.

## <a name="logical-operators"></a>Opérateurs logiques.

Les opérateurs `&`, `^`et `|` sont appelés opérateurs logiques.

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

Si un opérande d’un opérateur logique a le type au moment de la compilation `dynamic`, l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas, le type au moment de la compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous a lieu au moment de l’exécution à l’aide du type d’exécution des opérandes qui ont le type au moment de la compilation `dynamic`.

Pour une opération de la forme `x op y`, où `op` est l’un des opérateurs logiques, la résolution de surcharge ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner une implémentation d’opérateur spécifique. Les opérandes sont convertis en types de paramètre de l’opérateur sélectionné, et le type du résultat est le type de retour de l’opérateur.

Les opérateurs logiques prédéfinis sont décrits dans les sections suivantes.

### <a name="integer-logical-operators"></a>Opérateurs logiques Integer

Les opérateurs logiques d’entiers prédéfinis sont les suivants :
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

L’opérateur `&` calcule le `AND` logique au niveau du bit des deux opérandes, l’opérateur `|` calcule le `OR` logique au niveau du bit des deux opérandes, et l’opérateur `^` calcule la `OR` logique exclusive au niveau du bit des deux opérandes. Aucun dépassement de capacité n’est possible à partir de ces opérations.

### <a name="enumeration-logical-operators"></a>Opérateurs logiques d’énumération

Chaque type d’énumération `E` fournit implicitement les opérateurs logiques prédéfinis suivants :

```csharp
E operator &(E x, E y);
E operator |(E x, E y);
E operator ^(E x, E y);
```

Le résultat de l’évaluation de `x op y`, où `x` et `y` sont des expressions d’un type d’énumération `E` avec un type sous-jacent `U`, et `op` est l’un des opérateurs logiques, est exactement le même que l’évaluation de `(E)((U)x op (U)y)`. En d’autres termes, les opérateurs logiques de type énumération effectuent simplement l’opération logique sur le type sous-jacent des deux opérandes.

### <a name="boolean-logical-operators"></a>Opérateurs logiques booléens

Les opérateurs logiques booléens prédéfinis sont les suivants :
```csharp
bool operator &(bool x, bool y);
bool operator |(bool x, bool y);
bool operator ^(bool x, bool y);
```

Le résultat de `x & y` est `true` si `x` et `y` sont `true`. Sinon, le résultat est `false`.

Le résultat de `x | y` est `true` si `x` ou `y` est `true`. Sinon, le résultat est `false`.

Le résultat de `x ^ y` est `true` si `x` est `true` et `y` est `false`, ou `x` est `false` et `y` est `true`. Sinon, le résultat est `false`. Quand les opérandes sont de type `bool`, l’opérateur `^` calcule le même résultat que l’opérateur `!=`.

### <a name="nullable-boolean-logical-operators"></a>Opérateurs logiques booléens Nullable

Le type booléen Nullable `bool?` peut représenter trois valeurs, `true`, `false`et `null`, et est conceptuellement similaire au type à trois valeurs utilisé pour les expressions booléennes dans SQL. Pour vous assurer que les résultats produits par les opérateurs `&` et `|` pour les opérandes `bool?` sont cohérents avec la logique à trois valeurs de SQL, les opérateurs prédéfinis suivants sont fournis :

```csharp
bool? operator &(bool? x, bool? y);
bool? operator |(bool? x, bool? y);
```

Le tableau suivant répertorie les résultats produits par ces opérateurs pour toutes les combinaisons des valeurs `true`, `false`et `null`.

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

## <a name="conditional-logical-operators"></a>Opérateurs logiques conditionnels

Les opérateurs `&&` et `||` sont appelés opérateurs logiques conditionnels. Elles sont également appelées opérateurs logiques de « court-circuit ».

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

Les opérateurs `&&` et `||` sont des versions conditionnelles des opérateurs `&` et `|` :

*  L’opération `x && y` correspond à l’opération `x & y`, sauf que `y` n’est évaluée que si `x` n’est pas `false`.
*  L’opération `x || y` correspond à l’opération `x | y`, sauf que `y` n’est évaluée que si `x` n’est pas `true`.

Si un opérande d’un opérateur logique conditionnel a le type au moment de la compilation `dynamic`, l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas, le type au moment de la compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous a lieu au moment de l’exécution à l’aide du type d’exécution des opérandes qui ont le type au moment de la compilation `dynamic`.

Une opération de la forme `x && y` ou `x || y` est traitée en appliquant la résolution de surcharge ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) comme si l’opération avait été écrite `x & y` ou `x | y`. Ainsi,

*  Si la résolution de surcharge ne parvient pas à trouver un seul opérateur le mieux, ou si la résolution de surcharge sélectionne l’un des opérateurs logiques d’entiers prédéfinis, une erreur de liaison s’est produite.
*  Sinon, si l’opérateur sélectionné est l’un des opérateurs logiques booléens prédéfinis ([opérateurs logiques booléens](expressions.md#boolean-logical-operators)) ou des opérateurs logiques booléens Nullable ([opérateurs logiques booléens Nullable](expressions.md#nullable-boolean-logical-operators)), l’opération est traitée comme décrit dans [opérateurs logiques conditionnels booléens](expressions.md#boolean-conditional-logical-operators).
*  Dans le cas contraire, l’opérateur sélectionné est un opérateur défini par l’utilisateur et l’opération est traitée comme décrit dans [opérateurs logiques conditionnels définis par l’utilisateur](expressions.md#user-defined-conditional-logical-operators).

Il n’est pas possible de surcharger directement les opérateurs logiques conditionnels. Toutefois, étant donné que les opérateurs logiques conditionnels sont évalués en termes d’opérateurs logiques standard, les surcharges des opérateurs logiques normaux sont, avec certaines restrictions, également considérées comme des surcharges des opérateurs logiques conditionnels. Cela est décrit plus en détail dans [opérateurs logiques conditionnels définis par l’utilisateur](expressions.md#user-defined-conditional-logical-operators).

### <a name="boolean-conditional-logical-operators"></a>Opérateurs logiques conditionnels booléens

Lorsque les opérandes de `&&` ou `||` sont de type `bool`, ou lorsque les opérandes sont de types qui ne définissent pas d' `operator &` ou de `operator |`applicables, mais qui définissent les conversions implicites en `bool`, l’opération est traitée comme suit :

*  L’opération `x && y` est évaluée comme `x ? y : false`. En d’autres termes, `x` est d’abord évalué et converti en type `bool`. Ensuite, si `x` est `true`, `y` est évalué et converti en type `bool`, ce qui devient le résultat de l’opération. Dans le cas contraire, le résultat de l’opération est `false`.
*  L’opération `x || y` est évaluée comme `x ? true : y`. En d’autres termes, `x` est d’abord évalué et converti en type `bool`. Ensuite, si `x` est `true`, le résultat de l’opération est `true`. Dans le cas contraire, `y` est évalué et converti en type `bool`, ce qui devient le résultat de l’opération.

### <a name="user-defined-conditional-logical-operators"></a>Opérateurs logiques conditionnels définis par l’utilisateur

Lorsque les opérandes de `&&` ou `||` sont des types qui déclarent un `operator &` ou `operator |`défini par l’utilisateur applicable, les deux conditions suivantes doivent être vraies, où `T` est le type dans lequel l’opérateur sélectionné est déclaré :

*  Le type de retour et le type de chaque paramètre de l’opérateur sélectionné doivent être `T`. En d’autres termes, l’opérateur doit calculer le `AND` logique ou le `OR` logique de deux opérandes de type `T`, et doit retourner un résultat de type `T`.
*  `T` doit contenir des déclarations de `operator true` et de `operator false`.

Une erreur de liaison au moment de la liaison se produit si l’une de ces conditions n’est pas satisfaite. Dans le cas contraire, l’opération `&&` ou `||` est évaluée en combinant le `operator true` défini par l’utilisateur ou `operator false` à l’opérateur défini par l’utilisateur sélectionné :

*  L’opération `x && y` est évaluée comme `T.false(x) ? x : T.&(x, y)`, où `T.false(x)` est un appel de l' `operator false` déclaré dans `T`, et `T.&(x, y)` est un appel de la `operator &`sélectionnée. En d’autres termes, `x` est évaluée en premier et `operator false` est appelé sur le résultat pour déterminer si `x` a la valeur false. Ensuite, si `x` est définitivement false, le résultat de l’opération est la valeur précédemment calculée pour `x`. Dans le cas contraire, `y` est évalué et le `operator &` sélectionné est appelé sur la valeur précédemment calculée pour `x` et la valeur calculée pour `y` pour produire le résultat de l’opération.
*  L’opération `x || y` est évaluée comme `T.true(x) ? x : T.|(x, y)`, où `T.true(x)` est un appel de l' `operator true` déclaré dans `T`, et `T.|(x,y)` est un appel de la `operator|`sélectionnée. En d’autres termes, `x` est évaluée en premier et `operator true` est appelé sur le résultat pour déterminer si `x` est absolument true. Ensuite, si `x` est défini sur true, le résultat de l’opération est la valeur précédemment calculée pour `x`. Dans le cas contraire, `y` est évalué et le `operator |` sélectionné est appelé sur la valeur précédemment calculée pour `x` et la valeur calculée pour `y` pour produire le résultat de l’opération.

Dans l’une ou l’autre de ces opérations, l’expression donnée par `x` n’est évaluée qu’une seule fois, et l’expression donnée par `y` n’est pas évaluée ou évaluée une seule fois.

Pour obtenir un exemple de type qui implémente `operator true` et `operator false`, consultez [Database Boolean type](structs.md#database-boolean-type).

## <a name="the-null-coalescing-operator"></a>Opérateur de fusion Null

L’opérateur `??` est appelé opérateur de fusion Null.

```antlr
null_coalescing_expression
    : conditional_or_expression
    | conditional_or_expression '??' null_coalescing_expression
    ;
```

Une expression de fusion Null de la forme `a ?? b` nécessite que `a` soit un type Nullable ou un type référence. Si `a` est non null, le résultat de `a ?? b` est `a`; dans le cas contraire, le résultat est `b`. L’opération évalue `b` uniquement si `a` a la valeur null.

L’opérateur de fusion Null est associatif à droite, ce qui signifie que les opérations sont regroupées de droite à gauche. Par exemple, une expression de la forme `a ?? b ?? c` est évaluée comme `a ?? (b ?? c)`. En général, une expression de la forme `E1 ?? E2 ?? ... ?? En` retourne le premier des opérandes qui n’est pas null, ou null si tous les opérandes ont la valeur null.

Le type de l’expression `a ?? b` dépend des conversions implicites disponibles sur les opérandes. Par ordre de préférence, le type de `a ?? b` est `A0`, `A`ou `B`, où `A` est le type de `a` (à condition que `a` ait un type), `B` est le type de `b` (à condition que `b` ait un type) et `A0` est le type sous-jacent de `A` si `A` est un type Nullable, ou `A` dans le cas contraire. Plus précisément, `a ?? b` est traité comme suit :

*  Si `A` existe et qu’il ne s’agit pas d’un type Nullable ou d’un type référence, une erreur de compilation se produit.
*  Si `b` est une expression dynamique, le type de résultat est `dynamic`. Au moment de l’exécution, `a` est évaluée pour la première fois. Si `a` n’a pas la valeur null, `a` est converti en type dynamique, ce qui devient le résultat. Dans le cas contraire, `b` est évaluée et devient le résultat.
*  Sinon, si `A` existe et qu’il s’agit d’un type Nullable et qu’une conversion implicite existe de `b` à `A0`, le type de résultat est `A0`. Au moment de l’exécution, `a` est évaluée pour la première fois. Si `a` n’a pas la valeur null, `a` est désencapsulée dans le type `A0`, ce qui devient le résultat. Dans le cas contraire, `b` est évalué et converti en type `A0`, ce qui devient le résultat.
*  Sinon, si `A` existe et qu’une conversion implicite existe de `b` à `A`, le type de résultat est `A`. Au moment de l’exécution, `a` est évaluée pour la première fois. Si `a` n’a pas la valeur null, `a` devient le résultat. Dans le cas contraire, `b` est évalué et converti en type `A`, ce qui devient le résultat.
*  Sinon, si `b` a un type `B` et qu’il existe une conversion implicite de `a` en `B`, le type de résultat est `B`. Au moment de l’exécution, `a` est évaluée pour la première fois. Si `a` n’a pas la valeur null, `a` est désencapsulée dans le type `A0` (si `A` existe et est Nullable) et converti en type `B`, et cela devient le résultat. Dans le cas contraire, `b` est évalué et devient le résultat.
*  Sinon, les `a` et les `b` sont incompatibles, et une erreur de compilation se produit.

## <a name="conditional-operator"></a>Opérateur conditionnel

L’opérateur `?:` est appelé opérateur conditionnel. Il est parfois également appelé opérateur ternaire.

```antlr
conditional_expression
    : null_coalescing_expression
    | null_coalescing_expression '?' expression ':' expression
    ;
```

Une expression conditionnelle de la forme `b ? x : y` évalue d’abord la condition `b`. Ensuite, si `b` est `true`, `x` est évaluée et devient le résultat de l’opération. Dans le cas contraire, `y` est évalué et devient le résultat de l’opération. Une expression conditionnelle n’évalue jamais à la fois `x` et `y`.

L’opérateur conditionnel est associatif à droite, ce qui signifie que les opérations sont regroupées de droite à gauche. Par exemple, une expression de la forme `a ? b : c ? d : e` est évaluée comme `a ? b : (c ? d : e)`.

Le premier opérande de l’opérateur `?:` doit être une expression qui peut être implicitement convertie en `bool`ou une expression d’un type qui implémente `operator true`. Si aucune de ces exigences n’est satisfaite, une erreur de compilation se produit.

Le deuxième et le troisième opérandes, `x` et `y`de l’opérateur `?:` contrôlent le type de l’expression conditionnelle.

*  Si `x` a le type `X` et `y` a le type `Y` puis
   * Si une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir de `X` vers `Y`, mais pas de `Y` à `X`, `Y` est le type de l’expression conditionnelle.
   * Si une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir de `Y` vers `X`, mais pas de `X` à `Y`, `X` est le type de l’expression conditionnelle.
   * Dans le cas contraire, aucun type d’expression ne peut être déterminé et une erreur de compilation se produit.
*  Si une seule des `x` et `y` a un type, et que `x` et `y`, de sont implicitement convertibles en ce type, alors il s’agit du type de l’expression conditionnelle.
*  Dans le cas contraire, aucun type d’expression ne peut être déterminé et une erreur de compilation se produit.

Le traitement au moment de l’exécution d’une expression conditionnelle de la forme `b ? x : y` se compose des étapes suivantes :

*  Tout d’abord, `b` est évaluée et la valeur `bool` de `b` est déterminée :
   * Si une conversion implicite du type de `b` en `bool` existe, cette conversion implicite est effectuée pour produire une valeur de `bool`.
   * Dans le cas contraire, le `operator true` défini par le type de `b` est appelé pour produire une valeur de `bool`.
*  Si la valeur `bool` produite par l’étape ci-dessus est `true`, `x` est évaluée et convertie en type de l’expression conditionnelle, et devient le résultat de l’expression conditionnelle.
*  Dans le cas contraire, `y` est évalué et converti en type de l’expression conditionnelle, ce qui devient le résultat de l’expression conditionnelle.

## <a name="anonymous-function-expressions"></a>Expressions de fonctions anonymes

Une ***fonction anonyme*** est une expression qui représente une définition de méthode « in-line ». Une fonction anonyme n’a pas de valeur ou de type dans et de elle-même, mais elle peut être convertie en un délégué compatible ou un type d’arborescence d’expression. L’évaluation d’une conversion de fonction anonyme dépend du type de cible de la conversion : s’il s’agit d’un type délégué, la conversion correspond à une valeur de délégué qui référence la méthode définie par la fonction anonyme. S’il s’agit d’un type d’arborescence d’expression, la conversion prend la valeur d’une arborescence d’expressions qui représente la structure de la méthode sous la forme d’une structure d’objet.

Pour des raisons historiques, il existe deux versions syntaxiques des fonctions anonymes, à savoir *lambda_expression*s et *anonymous_method_expression*s. Pour presque tous les besoins, *lambda_expression*s sont plus concises et plus expressifs que *anonymous_method_expression*s, qui restent dans le langage à des fins de compatibilité descendante.

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

L’opérateur `=>` a la même priorité que l’assignation (`=`) et est associatif à droite.

Une fonction anonyme avec le modificateur `async` est une fonction Async et suit les règles décrites dans [itérateurs](classes.md#iterators).

Les paramètres d’une fonction anonyme sous la forme d’un *lambda_expression* peuvent être explicitement ou implicitement typés. Dans une liste de paramètres typés explicitement, le type de chaque paramètre est défini explicitement. Dans une liste de paramètres typée implicitement, les types des paramètres sont déduits du contexte dans lequel la fonction anonyme se produit, en particulier lorsque la fonction anonyme est convertie en un type délégué compatible ou un type d’arborescence d’expression, ce type fournit les types de paramètres ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)).

Dans une fonction anonyme avec un seul paramètre typé implicitement, les parenthèses peuvent être omises dans la liste de paramètres. En d’autres termes, une fonction anonyme de la forme
```csharp
( param ) => expr
```
peut être abrégé en
```csharp
param => expr
```

La liste des paramètres d’une fonction anonyme sous la forme d’un *anonymous_method_expression* est facultative. Si elles sont spécifiées, les paramètres doivent être explicitement typés. Si ce n’est pas le cas, la fonction anonyme est convertible en un délégué avec une liste de paramètres qui ne contient pas de paramètres `out`.

Un corps de *bloc* d’une fonction anonyme est accessible ([points de terminaison et accessibilité](statements.md#end-points-and-reachability)) sauf si la fonction anonyme se produit à l’intérieur d’une instruction inaccessible.

Voici quelques exemples de fonctions anonymes :

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

Le comportement de *lambda_expression*s et *anonymous_method_expression*s est le même, à l’exception des points suivants :

*  *anonymous_method_expression*s permettent l’omission complète de la liste de paramètres, ce qui donne convertibilité aux types délégués d’une liste de paramètres de valeur.
*  *lambda_expression*s autorisent l’omission et la déduction des types de paramètres, tandis que les types de paramètres doivent être déclarés explicitement *anonymous_method_expression*s.
*  Le corps d’un *lambda_expression* peut être une expression ou un bloc d’instructions, tandis que le corps d’un *anonymous_method_expression* doit être un bloc d’instructions.
*  Seuls les *lambda_expression*s ont des conversions en types d’arborescences d’expressions compatibles ([types d’arborescence d’expression](types.md#expression-tree-types)).

### <a name="anonymous-function-signatures"></a>Signatures de fonctions anonymes

La *anonymous_function_signature* facultative d’une fonction anonyme définit les noms et éventuellement les types des paramètres formels pour la fonction anonyme. La portée des paramètres de la fonction anonyme est le *anonymous_function_body*. ([Étendues](basic-concepts.md#scopes)) Avec la liste de paramètres (si elle est donnée), le corps de la méthode anonyme constitue un espace de déclaration ([déclarations](basic-concepts.md#declarations)). Il s’agit donc d’une erreur de compilation pour le nom d’un paramètre de la fonction anonyme qui correspond au nom d’une variable locale, d’une constante locale ou d’un paramètre dont l’étendue comprend le *anonymous_method_expression* ou *lambda_expression*.

Si une fonction anonyme a un *explicit_anonymous_function_signature*, l’ensemble de types de délégués compatibles et de types d’arborescence d’expression est limité à ceux qui ont les mêmes types de paramètres et modificateurs dans le même ordre. Contrairement aux conversions de groupe de méthodes ([conversions de groupe de méthodes](conversions.md#method-group-conversions)), la contre-variance des types de paramètre de fonction anonyme n’est pas prise en charge. Si une fonction anonyme n’a pas de *anonymous_function_signature*, l’ensemble de types de délégués compatibles et de types d’arborescence d’expression est limité à ceux qui n’ont pas de paramètres `out`.

Notez qu’un *anonymous_function_signature* ne peut pas inclure d’attributs ou un tableau de paramètres. Toutefois, un *anonymous_function_signature* peut être compatible avec un type délégué dont la liste de paramètres contient un tableau de paramètres.

Notez également que la conversion en un type d’arborescence d’expression, même si elle est compatible, peut encore échouer au moment de la compilation ([types d’arborescence d’expression](types.md#expression-tree-types)).

### <a name="anonymous-function-bodies"></a>Corps des fonctions anonymes

Le corps (*expression* ou *bloc*) d’une fonction anonyme est soumis aux règles suivantes :

*  Si la fonction anonyme comprend une signature, les paramètres spécifiés dans la signature sont disponibles dans le corps. Si la fonction anonyme n’a pas de signature, elle peut être convertie en type délégué ou type d’expression ayant des paramètres ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)), mais les paramètres ne sont pas accessibles dans le corps.
*  À l’exception des paramètres `ref` ou `out` spécifiés dans la signature (le cas échéant) de la fonction anonyme englobante la plus proche, il s’agit d’une erreur au moment de la compilation pour que le corps accède à un paramètre `ref` ou `out`.
*  Lorsque le type de `this` est un type struct, il s’agit d’une erreur au moment de la compilation pour que le corps accède à `this`. Cela est vrai, que l’accès soit explicite (comme dans `this.x`) ou implicite (comme dans `x` où `x` est un membre d’instance du struct). Cette règle interdit simplement cet accès et n’affecte pas si la recherche de membres est un membre du struct.
*  Le corps a accès aux variables externes ([variables externes](expressions.md#outer-variables)) de la fonction anonyme. L’accès à une variable externe fait référence à l’instance de la variable qui est active au moment où le *lambda_expression* ou *anonymous_method_expression* est évalué ([évaluation des expressions de fonction anonymes](expressions.md#evaluation-of-anonymous-function-expressions)).
*  Il s’agit d’une erreur au moment de la compilation pour que le corps contienne une instruction `goto`, une instruction `break` ou une instruction `continue` dont la cible est en dehors du corps ou dans le corps d’une fonction anonyme contenue.
*  Une instruction `return` dans le corps retourne le contrôle à partir d’un appel de la fonction anonyme englobante la plus proche, et non du membre de la fonction englobante. Une expression spécifiée dans une instruction `return` doit être implicitement convertible en type de retour du type délégué ou du type d’arborescence d’expression vers lequel le *lambda_expression* ou le *anonymous_method_expression* englobant le plus proche est converti ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)).

Elle n’est pas explicitement spécifiée, qu’il existe un moyen d’exécuter le bloc d’une fonction anonyme autre que par le biais de l’évaluation et de l’appel du *lambda_expression* ou *anonymous_method_expression*. En particulier, le compilateur peut choisir d’implémenter une fonction anonyme en synthétisant une ou plusieurs méthodes ou types nommés. Les noms de ces éléments synthétisés doivent être d’une forme réservée à l’utilisation du compilateur.

### <a name="overload-resolution-and-anonymous-functions"></a>Résolution de surcharge et fonctions anonymes

Les fonctions anonymes dans une liste d’arguments participent à l’inférence de type et à la résolution de surcharge. Reportez-vous à l' [inférence de type](expressions.md#type-inference) et à la [résolution de surcharge](expressions.md#overload-resolution) pour les règles exactes.

L’exemple suivant illustre l’effet des fonctions anonymes sur la résolution de surcharge.

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

La classe `ItemList<T>` a deux méthodes `Sum`. Chaque accepte un argument `selector`, qui extrait la valeur à additionner d’un élément de liste. La valeur extraite peut être un `int` ou un `double` et la somme résultante est également un `int` ou un `double`.

Les méthodes de `Sum` peuvent par exemple être utilisées pour calculer des sommes à partir d’une liste de lignes de détails dans un ordre.

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

Lors du premier appel de `orderDetails.Sum`, les deux méthodes `Sum` sont applicables, car la fonction anonyme `d => d. UnitCount` est compatible avec `Func<Detail,int>` et `Func<Detail,double>`. Toutefois, la résolution de surcharge choisit la première méthode `Sum`, car la conversion en `Func<Detail,int>` est meilleure que la conversion en `Func<Detail,double>`.

Dans le deuxième appel de `orderDetails.Sum`, seule la deuxième méthode `Sum` est applicable, car la fonction anonyme `d => d.UnitPrice * d.UnitCount` produit une valeur de type `double`. Par conséquent, la résolution de surcharge sélectionne la deuxième méthode `Sum` pour cet appel.

### <a name="anonymous-functions-and-dynamic-binding"></a>Fonctions anonymes et liaison dynamique

Une fonction anonyme ne peut pas être un récepteur, un argument ou un opérande d’une opération liée dynamiquement.

### <a name="outer-variables"></a>Variables externes

Toute variable locale, paramètre de valeur ou tableau de paramètres dont la portée comprend la *lambda_expression* ou *anonymous_method_expression* est appelée une ***variable externe*** de la fonction anonyme. Dans une fonction membre d’instance d’une classe, la valeur `this` est considérée comme un paramètre de valeur et est une variable externe de toute fonction anonyme contenue dans la fonction membre.

#### <a name="captured-outer-variables"></a>Variables externes capturées

Lorsqu’une variable externe est référencée par une fonction anonyme, la variable externe est dite ***capturée*** par la fonction anonyme. En règle générale, la durée de vie d’une variable locale est limitée à l’exécution du bloc ou de l’instruction à laquelle elle est associée ([variables locales](variables.md#local-variables)). Toutefois, la durée de vie d’une variable externe capturée est étendue au moins jusqu’à ce que le délégué ou l’arborescence d’expressions créé à partir de la fonction anonyme devienne éligible pour garbage collection.

Dans l’exemple
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
la variable locale `x` est capturée par la fonction anonyme, et la durée de vie de `x` est étendue au moins jusqu’à ce que le délégué retourné par `F` devienne éligible pour garbage collection (ce qui ne se produit pas jusqu’à la fin du programme). Étant donné que chaque appel de la fonction anonyme opère sur la même instance de `x`, la sortie de l’exemple est la suivante :
```console
1
2
3
```

Lorsqu’une variable locale ou un paramètre de valeur est capturé par une fonction anonyme, la variable locale ou le paramètre n’est plus considéré comme une variable fixe ([variables fixes et déplaçables](unsafe-code.md#fixed-and-moveable-variables)), mais est considéré comme une variable déplaçable. Ainsi, tout `unsafe` code qui prend l’adresse d’une variable externe capturée doit d’abord utiliser l’instruction `fixed` pour corriger la variable.

Notez que contrairement à une variable non capturée, une variable locale capturée peut être exposée simultanément à plusieurs threads d’exécution.

#### <a name="instantiation-of-local-variables"></a>Instanciation de variables locales

Une variable locale est considérée comme ***instanciée*** lorsque l’exécution entre dans l’étendue de la variable. Par exemple, lorsque la méthode suivante est appelée, la variable locale `x` est instanciée et initialisée trois fois, une fois pour chaque itération de la boucle.

```csharp
static void F() {
    for (int i = 0; i < 3; i++) {
        int x = i * 2 + 1;
        ...
    }
}
```

Toutefois, le déplacement de la déclaration de `x` en dehors de la boucle entraîne une seule instanciation de `x`:
```csharp
static void F() {
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        ...
    }
}
```

Lorsqu’elles ne sont pas capturées, il n’existe aucun moyen d’observer exactement la fréquence à laquelle une variable locale est instanciée. étant donné que les durées de vie des instanciations sont disjointes, il est possible que chaque instanciation utilise simplement le même emplacement de stockage. Toutefois, lorsqu’une fonction anonyme capture une variable locale, les effets de l’instanciation deviennent évidents.

L’exemple
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
génère cette sortie :
```console
1
3
5
```

Toutefois, lorsque la déclaration de `x` est déplacée en dehors de la boucle :
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
le résultat est :
```console
5
5
5
```

Si une boucle for déclare une variable d’itération, cette variable est considérée comme déclarée en dehors de la boucle. Ainsi, si l’exemple est modifié pour capturer la variable d’itération elle-même :

```csharp
static D[] F() {
    D[] result = new D[3];
    for (int i = 0; i < 3; i++) {
        result[i] = () => { Console.WriteLine(i); };
    }
    return result;
}
```
une seule instance de la variable d’itération est capturée, ce qui génère la sortie :
```console
3
3
3
```

Il est possible que les délégués de fonction anonymes partagent des variables capturées, mais des instances distinctes d’autres. Par exemple, si `F` est remplacé par
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
les trois délégués capturent la même instance de `x` mais des instances distinctes de `y`, et la sortie est :
```console
1 1
2 1
3 1
```

Des fonctions anonymes distinctes peuvent capturer la même instance d’une variable externe. Dans l'exemple :
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
les deux fonctions anonymes capturent la même instance de la variable locale `x`, et elles peuvent donc « communiquer » par le biais de cette variable. La sortie de l’exemple est la suivante :
```console
5
10
```

### <a name="evaluation-of-anonymous-function-expressions"></a>Évaluation des expressions de fonction anonymes

Une fonction anonyme `F` doit toujours être convertie en un type délégué `D` ou un type d’arborescence d’expression `E`, soit directement, soit par le biais de l’exécution d’une expression de création de délégué `new D(F)`. Cette conversion détermine le résultat de la fonction anonyme, comme décrit dans [conversions de fonctions anonymes](conversions.md#anonymous-function-conversions).

## <a name="query-expressions"></a>Expressions de requête

Les ***expressions de requête*** fournissent une syntaxe intégrée au langage pour les requêtes qui est semblable aux langages de requête relationnels et hiérarchiques tels que SQL et XQuery.

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

Une expression de requête commence par une clause `from` et se termine par une clause `select` ou `group`. La clause de `from` initiale peut être suivie par zéro, une ou plusieurs clauses `from`, `let`, `where`, `join` ou `orderby`. Chaque clause `from` est un générateur qui introduit une ***variable de portée*** qui s’étend sur les éléments d’une ***séquence***. Chaque clause `let` introduit une variable de portée représentant une valeur calculée au moyen de variables de plage précédentes. Chaque `where` clause est un filtre qui exclut des éléments du résultat. Chaque clause `join` compare les clés spécifiées de la séquence source avec les clés d’une autre séquence, ce qui génère des paires correspondantes. Chaque clause `orderby` réorganise les éléments en fonction des critères spécifiés. La dernière clause `select` ou `group` spécifie la forme du résultat en termes de variables de plage. Enfin, une clause `into` peut être utilisée pour « épisser » des requêtes en traitant les résultats d’une requête sous la forme d’un générateur dans une requête suivante.

### <a name="ambiguities-in-query-expressions"></a>Ambiguïtés dans les expressions de requête

Les expressions de requête contiennent un certain nombre de « mots-clés contextuels », c’est-à-dire des identificateurs qui ont une signification particulière dans un contexte donné. En particulier, il s’agit de `from`, `where`, `join`, `on`, `equals`, `into`, `let`, `orderby`, `ascending`, `descending`, `select`, `group` et `by`. Afin d’éviter les ambiguïtés dans les expressions de requête provoquées par l’utilisation mixte de ces identificateurs en tant que Mots clés ou noms simples, ces identificateurs sont considérés comme des mots clés lorsqu’ils se trouvent n’importe où dans une expression de requête.

À cet effet, une expression de requête est une expression qui commence par «`from identifier`» suivi de n’importe quel jeton, à l’exception de «`;`», «`=`» ou «`,`».

Pour pouvoir utiliser ces mots comme identificateurs dans une expression de requête, ils peuvent être précédés de «`@`» ([identificateurs](lexical-structure.md#identifiers)).

### <a name="query-expression-translation"></a>Traduction d’expression de requête

Le C# langage ne spécifie pas la sémantique d’exécution des expressions de requête. Au lieu de cela, les expressions de requête sont traduites en appels de méthodes qui adhèrent au *modèle d’expression de requête* ([le modèle d’expression de requête](expressions.md#the-query-expression-pattern)). Plus précisément, les expressions de requête sont traduites en appels de méthodes nommées `Where`, `Select`, `SelectMany`, `Join`, `GroupJoin`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `GroupBy`et `Cast`. Ces méthodes sont supposées avoir des signatures et des types de résultats particuliers, comme décrit dans [le modèle d’expression de requête](expressions.md#the-query-expression-pattern). Ces méthodes peuvent être des méthodes d’instance de l’objet interrogé ou des méthodes d’extension qui sont externes à l’objet, et elles implémentent l’exécution réelle de la requête.

La traduction des expressions de requête en appels de méthode est un mappage syntaxique qui se produit avant l’exécution d’une quelconque liaison de type ou de surcharge. La syntaxe de la traduction est garantie, mais il n’est pas garanti que la génération de code est C# correcte. À la suite de la traduction des expressions de requête, les appels de méthode résultants sont traités comme des appels de méthode normaux, ce qui peut à son tour dévoiler les erreurs, par exemple si les méthodes n’existent pas, si les arguments ont des types incorrects, ou si les méthodes sont génériques et l’inférence de type échoue.

Une expression de requête est traitée en appliquant à plusieurs reprises les traductions suivantes jusqu’à ce qu’aucune réduction supplémentaire ne soit possible. Les traductions sont répertoriées dans l’ordre de l’application : chaque section suppose que les traductions dans les sections précédentes ont été effectuées de façon exhaustive, et une fois épuisées, une section n’est pas revisitée ultérieurement dans le traitement de la même expression de requête.

L’assignation à des variables de portée n’est pas autorisée dans les expressions de requête. Toutefois, C# une implémentation est autorisée à ne pas toujours appliquer cette restriction, car cela peut parfois ne pas être possible avec le schéma de traduction syntaxique présenté ici.

Certaines traductions injectent des variables de portée avec des identificateurs transparents dénotés par `*`. Les propriétés spéciales des identificateurs transparents sont décrites plus en détail dans [identificateurs transparents](expressions.md#transparent-identifiers).

#### <a name="select-and-groupby-clauses-with-continuations"></a>Clauses SELECT et GroupBy avec continuations

Expression de requête avec une continuation
```csharp
from ... into x ...
```
est traduit en
```csharp
from x in ( from ... ) ...
```

Les traductions des sections suivantes supposent que les requêtes n’ont aucune continuation `into`.

L’exemple
```csharp
from c in customers
group c by c.Country into g
select new { Country = g.Key, CustCount = g.Count() }
```
est traduit en
```csharp
from g in
    from c in customers
    group c by c.Country
select new { Country = g.Key, CustCount = g.Count() }
```
traduction finale de qui est
```csharp
customers.
GroupBy(c => c.Country).
Select(g => new { Country = g.Key, CustCount = g.Count() })
```

#### <a name="explicit-range-variable-types"></a>Types de variable de portée explicite

Une clause `from` qui spécifie explicitement un type de variable de portée
```csharp
from T x in e
```
est traduit en
```csharp
from x in ( e ) . Cast < T > ( )
```

Une clause `join` qui spécifie explicitement un type de variable de portée
```csharp
join T x in e on k1 equals k2
```
est traduit en
```csharp
join x in ( e ) . Cast < T > ( ) on k1 equals k2
```

Les traductions dans les sections suivantes supposent que les requêtes n’ont pas de types de variable de portée explicite.

L’exemple
```csharp
from Customer c in customers
where c.City == "London"
select c
```
est traduit en
```csharp
from c in customers.Cast<Customer>()
where c.City == "London"
select c
```
traduction finale de qui est
```csharp
customers.
Cast<Customer>().
Where(c => c.City == "London")
```

Les types de variable de portée explicite sont utiles pour interroger des collections qui implémentent l’interface `IEnumerable` non générique, mais pas l’interface de `IEnumerable<T>` générique. Dans l’exemple ci-dessus, ce serait le cas si `customers` était de type `ArrayList`.

#### <a name="degenerate-query-expressions"></a>Dégénérer des expressions de requête

Expression de requête de la forme
```csharp
from x in e select x
```
est traduit en
```csharp
( e ) . Select ( x => x )
```

L’exemple
```csharp
from c in customers
select c
```
est traduit en
```csharp
customers.Select(c => c)
```

Une expression de requête dégénérée est une expression qui sélectionne de façon triviale les éléments de la source. Une phase ultérieure de la traduction supprime les requêtes dégénérées introduites par d’autres étapes de traduction en les remplaçant par leur source. Toutefois, il est important de s’assurer que le résultat d’une expression de requête n’est jamais l’objet source lui-même, car cela révélerait le type et l’identité de la source au client de la requête. Par conséquent, cette étape protège les requêtes dégénérées écrites directement dans le code source en appelant explicitement `Select` sur la source. Il s’agit ensuite des implémenteurs de `Select` et d’autres opérateurs de requête pour garantir que ces méthodes ne retournent jamais l’objet source lui-même.

#### <a name="from-let-where-join-and-orderby-clauses"></a>Clauses from, Let, Where, Join et OrderBy

Expression de requête avec une deuxième clause `from` suivie d’une clause `select`
```csharp
from x1 in e1
from x2 in e2
select v
```
est traduit en
```csharp
( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => v )
```

Une expression de requête avec une deuxième clause `from` suivie d’autres éléments qu’une clause `select` :

```csharp
from x1 in e1
from x2 in e2
...
```
est traduit en
```csharp
from * in ( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => new { x1 , x2 } )
...
```

Expression de requête avec une clause `let`
```csharp
from x in e
let y = f
...
```
est traduit en
```csharp
from * in ( e ) . Select ( x => new { x , y = f } )
...
```

Expression de requête avec une clause `where`
```csharp
from x in e
where f
...
```
est traduit en
```csharp
from x in ( e ) . Where ( x => f )
...
```

Expression de requête avec une clause `join` sans `into` suivie d’une clause `select`
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
select v
```
est traduit en
```csharp
( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => v )
```

Une expression de requête avec une clause `join` sans `into` suivie d’autres éléments qu’une clause `select`
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
...
```
est traduit en
```csharp
from * in ( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => new { x1 , x2 })
...
```

Expression de requête avec une clause `join` avec une `into` suivie d’une clause `select`
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
select v
```
est traduit en
```csharp
( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => v )
```

Expression de requête avec une clause `join` avec un `into` suivi d’un autre nom qu’une clause `select`
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
...
```
est traduit en
```csharp
from * in ( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => new { x1 , g })
...
```

Expression de requête avec une clause `orderby`
```csharp
from x in e
orderby k1 , k2 , ..., kn
...
```
est traduit en
```csharp
from x in ( e ) . 
OrderBy ( x => k1 ) . 
ThenBy ( x => k2 ) .
... .
ThenBy ( x => kn )
...
```

Si une clause de classement spécifie un indicateur de direction `descending`, un appel de `OrderByDescending` ou `ThenByDescending` est généré à la place.

Les traductions suivantes supposent qu’il n’y a pas de clauses `let`, `where`, `join` ou `orderby`, et pas plus que la seule clause `from` initiale dans chaque expression de requête.

L’exemple
```csharp
from c in customers
from o in c.Orders
select new { c.Name, o.OrderID, o.Total }
```
est traduit en
```csharp
customers.
SelectMany(c => c.Orders,
     (c,o) => new { c.Name, o.OrderID, o.Total }
)
```

L’exemple
```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```
est traduit en
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```
traduction finale de qui est
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.OrderID, x.o.Total })
```
où `x` est un identificateur généré par le compilateur qui est autrement invisible et inaccessible.

L’exemple
```csharp
from o in orders
let t = o.Details.Sum(d => d.UnitPrice * d.Quantity)
where t >= 1000
select new { o.OrderID, Total = t }
```
est traduit en
```csharp
from * in orders.
    Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) })
where t >= 1000 
select new { o.OrderID, Total = t }
```
traduction finale de qui est
```csharp
orders.
Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) }).
Where(x => x.t >= 1000).
Select(x => new { x.o.OrderID, Total = x.t })
```
où `x` est un identificateur généré par le compilateur qui est autrement invisible et inaccessible.

L’exemple
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
select new { c.Name, o.OrderDate, o.Total }
```
est traduit en
```csharp
customers.Join(orders, c => c.CustomerID, o => o.CustomerID,
    (c, o) => new { c.Name, o.OrderDate, o.Total })
```

L’exemple
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID into co
let n = co.Count()
where n >= 10
select new { c.Name, OrderCount = n }
```
est traduit en
```csharp
from * in customers.
    GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
        (c, co) => new { c, co })
let n = co.Count()
where n >= 10 
select new { c.Name, OrderCount = n }
```
traduction finale de qui est
```csharp
customers.
GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
    (c, co) => new { c, co }).
Select(x => new { x, n = x.co.Count() }).
Where(y => y.n >= 10).
Select(y => new { y.x.c.Name, OrderCount = y.n)
```
où `x` et `y` sont des identificateurs générés par le compilateur qui sont autrement inaccessibles et inaccessibles.

L’exemple
```csharp
from o in orders
orderby o.Customer.Name, o.Total descending
select o
```
a la traduction finale
```csharp
orders.
OrderBy(o => o.Customer.Name).
ThenByDescending(o => o.Total)
```

#### <a name="select-clauses"></a>Clauses SELECT

Expression de requête de la forme
```csharp
from x in e select v
```
est traduit en
```csharp
( e ) . Select ( x => v )
```
sauf lorsque v est l’identificateur x, la traduction est simplement
```csharp
( e )
```

Exemple :
```csharp
from c in customers.Where(c => c.City == "London")
select c
```
est simplement traduit en
```csharp
customers.Where(c => c.City == "London")
```

#### <a name="groupby-clauses"></a>Clauses GROUPBY

Expression de requête de la forme
```csharp
from x in e group v by k
```
est traduit en
```csharp
( e ) . GroupBy ( x => k , x => v )
```
sauf lorsque v est l’identificateur x, la traduction est
```csharp
( e ) . GroupBy ( x => k )
```

L’exemple
```csharp
from c in customers
group c.Name by c.Country
```
est traduit en
```csharp
customers.
GroupBy(c => c.Country, c => c.Name)
```

#### <a name="transparent-identifiers"></a>Identificateurs transparents

Certaines traductions injectent des variables de portée avec des ***identificateurs transparents*** dénotés par `*`. Les identificateurs transparents ne sont pas une fonctionnalité de langage appropriée. ils existent uniquement comme étape intermédiaire dans le processus de traduction d’expression de requête.

Quand une traduction de requête injecte un identificateur transparent, d’autres étapes de traduction propagent l’identificateur transparent dans des fonctions anonymes et des initialiseurs d’objets anonymes. Dans ces contextes, les identificateurs transparents ont le comportement suivant :

*  Lorsqu’un identificateur transparent se produit en tant que paramètre dans une fonction anonyme, les membres du type anonyme associé sont automatiquement dans la portée dans le corps de la fonction anonyme.
*  Lorsqu’un membre avec un identificateur transparent est dans la portée, les membres de ce membre sont également dans la portée.
*  Lorsqu’un identificateur transparent se produit comme déclarateur de membre dans un initialiseur d’objet anonyme, il introduit un membre avec un identificateur transparent.
*  Dans les étapes de traduction décrites ci-dessus, les identificateurs transparents sont toujours introduits en même temps que les types anonymes, dans le but de capturer plusieurs variables de portée en tant que membres d’un seul objet. Une implémentation de C# est autorisée à utiliser un autre mécanisme que les types anonymes pour regrouper plusieurs variables de portée. Les exemples de traduction suivants supposent que les types anonymes sont utilisés et montrent comment les identificateurs transparents peuvent être traduits.

L’exemple
```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.Total }
```
est traduit en
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.Total }
```

qui est ensuite traduite en
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(* => o.Total).
Select(* => new { c.Name, o.Total })
```
qui, lorsque les identificateurs transparents sont effacés, est équivalent à
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.Total })
```
où `x` est un identificateur généré par le compilateur qui est autrement invisible et inaccessible.

L’exemple
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
est traduit en
```csharp
from * in customers.
    Join(orders, c => c.CustomerID, o => o.CustomerID, 
        (c, o) => new { c, o })
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
ce qui est encore plus réduit à
```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID, (c, o) => new { c, o }).
Join(details, * => o.OrderID, d => d.OrderID, (*, d) => new { *, d }).
Join(products, * => d.ProductID, p => p.ProductID, (*, p) => new { *, p }).
Select(* => new { c.Name, o.OrderDate, p.ProductName })
```
traduction finale de qui est
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
où `x`, `y`et `z` sont des identificateurs générés par le compilateur qui, sinon, sont inaccessibles et inaccessibles.

### <a name="the-query-expression-pattern"></a>Modèle d’expression de requête

Le ***modèle d’expression de requête*** établit un modèle de méthodes que les types peuvent implémenter pour prendre en charge les expressions de requête. Étant donné que les expressions de requête sont traduites en appels de méthode au moyen d’un mappage syntaxique, les types ont une flexibilité considérable dans la manière dont ils implémentent le modèle d’expression de requête. Par exemple, les méthodes du modèle peuvent être implémentées en tant que méthodes d’instance ou en tant que méthodes d’extension, car les deux ont la même syntaxe d’appel, et les méthodes peuvent demander des délégués ou des arborescences d’expressions, car les fonctions anonymes sont convertibles dans les deux.

La forme recommandée d’un type générique `C<T>` qui prend en charge le modèle d’expression de requête est illustrée ci-dessous. Un type générique est utilisé pour illustrer les relations appropriées entre les types de paramètres et de résultats, mais il est possible d’implémenter le modèle pour les types non génériques également.

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

Les méthodes ci-dessus utilisent les types délégués génériques `Func<T1,R>` et `Func<T1,T2,R>`, mais elles pouvaient tout aussi bien utiliser d’autres types de délégué ou d’arborescence d’expression avec les mêmes relations dans les types de paramètres et de résultats.

Notez la relation recommandée entre `C<T>` et `O<T>` qui garantit que les méthodes `ThenBy` et `ThenByDescending` sont uniquement disponibles sur le résultat d’un `OrderBy` ou d’un `OrderByDescending`. Notez également la forme recommandée du résultat de `GroupBy`--une séquence de séquences, où chaque séquence interne a une propriété de `Key` supplémentaire.

L’espace de noms `System.Linq` fournit une implémentation du modèle d’opérateur de requête pour tout type qui implémente l’interface `System.Collections.Generic.IEnumerable<T>`.

## <a name="assignment-operators"></a>Opérateurs d’affectation

Les opérateurs d’assignation affectent une nouvelle valeur à une variable, une propriété, un événement ou un élément d’indexeur.

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

L’opérande gauche d’une assignation doit être une expression classifiée comme une variable, un accès à une propriété, un accès à un indexeur ou un accès à un événement.

L’opérateur `=` est appelé ***opérateur d’assignation simple***. Elle assigne la valeur de l’opérande droit à la variable, à la propriété ou à l’élément d’indexeur donné par l’opérande gauche. L’opérande gauche de l’opérateur d’assignation simple ne peut pas être un accès à un événement (sauf s’il est décrit dans [événements de type champ](classes.md#field-like-events)). L’opérateur d’assignation simple est décrit dans [assignation simple](expressions.md#simple-assignment).

Les opérateurs d’assignation autres que l’opérateur `=` sont appelés des ***opérateurs d’assignation composée***. Ces opérateurs effectuent l’opération indiquée sur les deux opérandes, puis assignent la valeur résultante à la variable, à la propriété ou à l’élément d’indexeur donné par l’opérande gauche. Les opérateurs d’assignation composée sont décrits dans [assignation composée](expressions.md#compound-assignment).

Les opérateurs `+=` et `-=` avec une expression d’accès à l’événement comme opérande gauche sont appelés *opérateurs d’assignation d’événement*. Aucun autre opérateur d’assignation n’est valide avec un accès aux événements comme opérande de gauche. Les opérateurs d’assignation d’événement sont décrits dans [assignation d’événement](expressions.md#event-assignment).

Les opérateurs d’assignation sont associatifs à droite, ce qui signifie que les opérations sont regroupées de droite à gauche. Par exemple, une expression de la forme `a = b = c` est évaluée comme `a = (b = c)`.

### <a name="simple-assignment"></a>Assignation simple

L’opérateur `=` est appelé opérateur d’assignation simple.

Si l’opérande gauche d’une assignation simple est de la forme `E.P` ou `E[Ei]` où `E` a le type au moment de la compilation `dynamic`, l’assignation est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas, le type au moment de la compilation de l’expression d’assignation est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution en fonction du type d’exécution de `E`.

Dans une assignation simple, l’opérande de droite doit être une expression implicitement convertible en type de l’opérande de gauche. L’opération assigne la valeur de l’opérande droit à la variable, à la propriété ou à l’élément d’indexeur donné par l’opérande gauche.

Le résultat d’une expression d’assignation simple est la valeur assignée à l’opérande de gauche. Le résultat a le même type que l’opérande de gauche et est toujours classifié comme une valeur.

Si l’opérande de gauche est un accès à une propriété ou un indexeur, la propriété ou l’indexeur doit avoir un accesseur `set`. Si ce n’est pas le cas, une erreur de temps de liaison se produit.

Le traitement au moment de l’exécution d’une assignation simple de la forme `x = y` se compose des étapes suivantes :

*  Si `x` est classée en tant que variable :
   * `x` est évaluée pour produire la variable.
   * `y` est évaluée et, si nécessaire, convertie en type de `x` par le biais d’une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).
   * Si la variable fournie par `x` est un élément de tableau d’un *reference_type*, une vérification au moment de l’exécution est effectuée pour s’assurer que la valeur calculée pour `y` est compatible avec l’instance de tableau de laquelle `x` est un élément. La vérification est réussie si `y` est `null`ou si une conversion de référence implicite ([conversions de référence implicites](conversions.md#implicit-reference-conversions)) existe à partir du type réel de l’instance référencée par `y` vers le type d’élément réel de l’instance de tableau contenant `x`. Sinon, une exception `System.ArrayTypeMismatchException` est levée.
   * La valeur résultant de l’évaluation et de la conversion de `y` est stockée dans l’emplacement donné par l’évaluation de `x`.
*  Si `x` est classée comme un accès à une propriété ou un indexeur :
   * L’expression d’instance (si `x` n’est pas `static`) et la liste d’arguments (si `x` est un accès à un indexeur) associée à `x` sont évaluées, et les résultats sont utilisés dans l’appel d’accesseur `set` suivant.
   * `y` est évaluée et, si nécessaire, convertie en type de `x` par le biais d’une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).
   * L’accesseur `set` de `x` est appelé avec la valeur calculée pour `y` comme argument de `value`.

Les règles de covariance de tableau ([covariance de tableau](arrays.md#array-covariance)) autorisent une valeur d’un type de tableau `A[]` être une référence à une instance d’un type de tableau `B[]`, à condition qu’il existe une conversion de référence implicite de `B` à `A`. En raison de ces règles, l’assignation à un élément de tableau d’un *reference_type* nécessite un contrôle à l’exécution pour garantir que la valeur assignée est compatible avec l’instance de tableau. Dans l’exemple
```csharp
string[] sa = new string[10];
object[] oa = sa;

oa[0] = null;               // Ok
oa[1] = "Hello";            // Ok
oa[2] = new ArrayList();    // ArrayTypeMismatchException
```
la dernière assignation entraîne la levée d’une `System.ArrayTypeMismatchException`, car une instance de `ArrayList` ne peut pas être stockée dans un élément d’un `string[]`.

Lorsqu’une propriété ou un indexeur déclaré dans un *struct_type* est la cible d’une assignation, l’expression d’instance associée à l’accès à la propriété ou à l’indexeur doit être classée en tant que variable. Si l’expression d’instance est classée en tant que valeur, une erreur de liaison au moment de la liaison se produit. En raison de l' [accès aux membres](expressions.md#member-access), la même règle s’applique également aux champs.

Compte tenu des déclarations :
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
dans l’exemple
```csharp
Point p = new Point();
p.X = 100;
p.Y = 100;
Rectangle r = new Rectangle();
r.A = new Point(10, 10);
r.B = p;
```
les assignations à `p.X`, `p.Y`, `r.A`et `r.B` sont autorisées, car `p` et `r` sont des variables. Toutefois, dans l’exemple
```csharp
Rectangle r = new Rectangle();
r.A.X = 10;
r.A.Y = 10;
r.B.X = 100;
r.B.Y = 100;
```
les attributions ne sont pas valides, car `r.A` et `r.B` ne sont pas des variables.

### <a name="compound-assignment"></a>Assignation composée

Si l’opérande gauche d’une assignation composée se présente sous la forme `E.P` ou `E[Ei]` où `E` a le type au moment de la compilation `dynamic`, l’assignation est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas, le type au moment de la compilation de l’expression d’assignation est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution en fonction du type d’exécution de `E`.

Une opération de la forme `x op= y` est traitée en appliquant la résolution de surcharge d’opérateur binaire ([résolution de surcharge d’opérateur binaire](expressions.md#binary-operator-overload-resolution)) comme si l’opération avait été écrite `x op y`. Ainsi,

*  Si le type de retour de l’opérateur sélectionné est implicitement convertible en type de `x`, l’opération est évaluée comme `x = x op y`, sauf que `x` n’est évaluée qu’une seule fois.
*  Sinon, si l’opérateur sélectionné est un opérateur prédéfini, si le type de retour de l’opérateur sélectionné est explicitement convertible en type de `x`, et si `y` est implicitement convertible en type de `x` ou si l’opérateur est un opérateur de décalage, l’opération est évaluée comme `x = (T)(x op y)`, où `T` est le type de `x`, sauf que `x` est évaluée une seule fois.
*  Dans le cas contraire, l’assignation composée n’est pas valide et une erreur de liaison s’est produite.

Le terme « évalué une seule fois » signifie que, lors de l’évaluation de `x op y`, les résultats de toutes les expressions constitutives de `x` sont temporairement enregistrés et réutilisés lors de l’assignation à `x`. Par exemple, dans l’assignation `A()[B()] += C()`, où `A` est une méthode qui retourne `int[]`, et `B` et `C` sont des méthodes qui retournent `int`, les méthodes sont appelées une seule fois, dans l’ordre `A`, `B``C`.

Lorsque l’opérande gauche d’une assignation composée est un accès à une propriété ou un indexeur, la propriété ou l’indexeur doit avoir un accesseur `get` et un accesseur `set`. Si ce n’est pas le cas, une erreur de temps de liaison se produit.

La deuxième règle ci-dessus permet d’évaluer `x op= y` comme `x = (T)(x op y)` dans certains contextes. La règle existe de sorte que les opérateurs prédéfinis peuvent être utilisés comme opérateurs composés lorsque l’opérande de gauche est de type `sbyte`, `byte`, `short`, `ushort`ou `char`. Même si les deux arguments sont de l’un de ces types, les opérateurs prédéfinis produisent un résultat de type `int`, comme décrit dans [promotions numériques binaires](expressions.md#binary-numeric-promotions). Ainsi, sans cast, il n’est pas possible d’assigner le résultat à l’opérande gauche.

L’effet intuitif de la règle pour les opérateurs prédéfinis est simplement que `x op= y` est autorisé si les `x op y` et `x = y` sont autorisés. Dans l’exemple
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
la raison la plus intuitive pour chaque erreur est qu’une assignation simple correspondante aurait également été une erreur.

Cela signifie également que les opérations d’assignation composée prennent en charge les opérations levées. Dans l’exemple
```csharp
int? i = 0;
i += 1;             // Ok
```
l’opérateur levé `+(int?,int?)` est utilisé.

### <a name="event-assignment"></a>Assignation d’événement

Si l’opérande gauche d’un opérateur `+=` ou `-=` est classé comme un accès aux événements, l’expression est évaluée comme suit :

*  L’expression d’instance, le cas échéant, de l’accès à l’événement est évaluée.
*  L’opérande droit de l’opérateur `+=` ou `-=` est évalué et, si nécessaire, est converti en type de l’opérande gauche via une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).
*  Un accesseur d’événement de l’événement est appelé, avec la liste d’arguments composée de l’opérande de droite, après l’évaluation et, si nécessaire, la conversion. Si l’opérateur a été `+=`, l’accesseur `add` est appelé ; Si l’opérateur a été `-=`, l’accesseur `remove` est appelé.

Une expression d’assignation d’événement ne produit pas de valeur. Ainsi, une expression d’assignation d’événement est valide uniquement dans le contexte d’une *statement_expression* ([instructions d’expression](statements.md#expression-statements)).

## <a name="expression"></a>Expression

Une *expression* est soit une *non_assignment_expression* , soit une *assignation*.

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

## <a name="constant-expressions"></a>Expressions de constantes

Une *constant_expression* est une expression qui peut être complètement évaluée au moment de la compilation.

```antlr
constant_expression
    : expression
    ;
```

Une expression constante doit être le `null` littéral ou une valeur avec l’un des types suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `object`, `string`ou n’importe quel type énumération. Seules les constructions suivantes sont autorisées dans les expressions constantes :

*  Littéraux (y compris le littéral `null`).
*  Références à `const` membres de types classe et struct.
*  Références aux membres de types énumération.
*  Références aux paramètres de `const` ou aux variables locales
*  Sous-expressions entre parenthèses, qui sont elles-mêmes des expressions constantes.
*  Expressions de cast, à condition que le type cible soit l’un des types indiqués ci-dessus.
*  expressions `checked` et `unchecked`
*  Expressions de valeur par défaut
*  Expressions Nameof
*  Les opérateurs unaires `+`, `-`, `!`et `~` prédéfinis.
*  Les opérateurs binaires prédéfinis `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, `&&`, `||`, `==`, `!=`, `<`, `>`, `<=`et `>=`, à condition que chaque opérande soit d’un type indiqué ci-dessus.
*  Opérateur conditionnel `?:`.

Les conversions suivantes sont autorisées dans les expressions constantes :

*  Conversions d’identité
*  Conversions numériques
*  Conversions d’énumération
*  Conversions d’expressions constantes
*  Conversions de référence implicites et explicites, à condition que la source des conversions soit une expression constante qui prend la valeur null.

Les autres conversions, y compris les conversions boxing, unboxing et implicites des valeurs non null, ne sont pas autorisées dans les expressions constantes. Par exemple :
```csharp
class C {
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
l’initialisation de i est une erreur, car une conversion boxing est requise. L’initialisation de str est une erreur, car une conversion de référence implicite à partir d’une valeur non null est requise.

Chaque fois qu’une expression répond aux spécifications mentionnées ci-dessus, l’expression est évaluée au moment de la compilation. Cela est vrai même si l’expression est une sous-expression d’une expression plus grande qui contient des constructions non constantes.

L’évaluation au moment de la compilation des expressions constantes utilise les mêmes règles que l’évaluation au moment de l’exécution des expressions non constantes, à la différence près que lorsque l’évaluation au moment de l’exécution a levé une exception, l’évaluation au moment de la compilation provoque une erreur au moment de la compilation.

À moins qu’une expression constante soit explicitement placée dans un contexte de `unchecked`, les dépassements qui se produisent dans les opérations arithmétiques de type intégral et les conversions pendant l’évaluation de l’expression au moment de la compilation provoquent toujours des erreurs au moment de la compilation ([expressions constantes](expressions.md#constant-expressions)).

Les expressions constantes se trouvent dans les contextes listés ci-dessous. Dans ces contextes, une erreur de compilation se produit si une expression ne peut pas être complètement évaluée au moment de la compilation.

*  Déclarations de constantes ([constantes](classes.md#constants)).
*  Déclarations de membre d’énumération ([membres enum](enums.md#enum-members)).
*  Arguments par défaut des listes de paramètres formels ([paramètres de méthode](classes.md#method-parameters))
*  `case` étiquettes d’une instruction `switch` ([instruction switch](statements.md#the-switch-statement)).
*  instructions `goto case` ([instruction goto](statements.md#the-goto-statement)).
*  Longueurs de dimension dans une expression de création de tableau ([expressions de création de tableau](expressions.md#array-creation-expressions)) qui comprend un initialiseur.
*  Attributs ([attributs](attributes.md)).

Une conversion d’expression constante implicite ([conversions d’expressions constantes implicites](conversions.md#implicit-constant-expression-conversions)) permet à une expression constante de type `int` d’être convertie en `sbyte`, `byte`, `short`, `ushort`, `uint`ou `ulong`, à condition que la valeur de l’expression constante soit comprise dans la plage du type de destination.

## <a name="boolean-expressions"></a>expressions booléennes

Une *Boolean_expression* est une expression qui produit un résultat de type `bool`; soit directement, soit par le biais de l’application de `operator true` dans certains contextes, comme indiqué dans le code suivant.

```antlr
boolean_expression
    : expression
    ;
```

L’expression conditionnelle de contrôle d' *un if_statement* ([instruction if](statements.md#the-if-statement)), *while_statement* ([l’instruction while](statements.md#the-while-statement)) *, do_statement* ([instruction do](statements.md#the-do-statement)) ou *for_Statement* ([l’instruction for](statements.md#the-for-statement)) est un *Boolean_expression*. L’expression conditionnelle de contrôle de l’opérateur `?:` ([opérateur conditionnel](expressions.md#conditional-operator)) suit les mêmes règles qu’un *Boolean_expression*, mais pour des raisons de priorité d’opérateur est classée en tant que *conditional_or_expression*.

Un *boolean_expression* `E` est requis pour pouvoir produire une valeur de type `bool`, comme suit :

*  Si `E` est implicitement convertible en `bool` alors, au moment de l’exécution, que la conversion implicite est appliquée.
*  Sinon, la résolution de surcharge d’opérateur unaire ([résolution de surcharge d’opérateur unaire](expressions.md#unary-operator-overload-resolution)) est utilisée pour rechercher une meilleure implémentation unique de l’opérateur `true` sur `E`, et cette implémentation est appliquée au moment de l’exécution.
*  Si aucun opérateur de ce type n’est trouvé, une erreur au moment de la liaison se produit.

Le type struct `DBBool` dans la [base de données de type booléen](structs.md#database-boolean-type) fournit un exemple de type qui implémente `operator true` et `operator false`.
