---
ms.openlocfilehash: 066c300d4c2baa8749e132730ecd48275e2957f7
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64489012"
---
# <a name="expressions"></a>Expressions

Une expression est une séquence d’opérateurs et d’opérandes. Ce chapitre définit la syntaxe, l’ordre d’évaluation des opérandes et opérateurs et la signification des expressions.

## <a name="expression-classifications"></a>Classifications des expressions

Une expression est classée comme l'un des éléments suivants :

*  Valeur. Chaque valeur possède un type associé.
*  Une variable. Chaque variable possède un type associé, à savoir le type déclaré de la variable.
*  Espace de noms. Une expression dotée de cette classification peut apparaître uniquement comme la partie gauche d’un *member_access* ([l’accès au membre](expressions.md#member-access)). Dans un autre contexte, une expression classifiée comme un espace de noms provoque une erreur de compilation.
*  Type. Une expression dotée de cette classification peut apparaître uniquement comme la partie gauche d’un *member_access* ([l’accès au membre](expressions.md#member-access)), ou en tant qu’opérande pour le `as` opérateur ([l’opérateur as ](expressions.md#the-as-operator)), la `is` opérateur ([l’opérateur](expressions.md#the-is-operator)), ou le `typeof` opérateur ([l’opérateur typeof](expressions.md#the-typeof-operator)). Dans un autre contexte, une expression classifiée comme un type entraîne une erreur de compilation.
*  Un groupe de méthodes, qui est un ensemble de méthodes surchargées résultant d’une recherche de membres ([recherche de membres](expressions.md#member-lookup)). Un groupe de méthodes peut-être avoir une expression d’instance associée et une liste d’arguments de type associé. Lorsqu’une méthode d’instance est appelée, le résultat de l’évaluation de l’expression d’instance devient l’instance représentée par `this` ([cet accès](expressions.md#this-access)). Un groupe de méthodes est autorisé dans une *invocation_expression* ([expressions d’appel](expressions.md#invocation-expressions)), un *delegate_creation_expression* ([déléguer la création expressions](expressions.md#delegate-creation-expressions)) et en tant que le côté gauche d’un opérateur et peut être implicitement converti en un type délégué compatible ([conversions de groupes de méthode](conversions.md#method-group-conversions)). Dans un autre contexte, une expression classifiée comme un groupe de méthodes provoque une erreur de compilation.
*  Un littéral null. Une expression dotée de cette classification peut être implicitement convertie en un type référence ou un type nullable.
*  Une fonction anonyme. Une expression dotée de cette classification peut être convertie implicitement à un type délégué compatible ou le type arborescence d’expression.
*  Un accès à la propriété. Chaque accès à la propriété possède un type associé, à savoir le type de la propriété. En outre, un accès à la propriété peut avoir une expression d’instance associée. Lorsqu’un accesseur (le `get` ou `set` bloc) d’une instance d’accès à la propriété est appelée, le résultat de l’évaluation de l’expression d’instance devient l’instance représentée par `this` ([cet accès](expressions.md#this-access)).
*  Accès à un événement. Chaque accès à l’événement a un type associé, à savoir le type de l’événement. En outre, un accès à l’événement peut avoir une expression d’instance associée. Accès à un événement peut apparaître en tant que l’opérande de gauche de la `+=` et `-=` opérateurs ([assignation d’événement](expressions.md#event-assignment)). Dans un autre contexte, une expression classifiée comme un accès à l’événement provoque une erreur de compilation.
*  Accès à un indexeur. Chaque accès à l’indexeur a un type associé, à savoir le type d’élément de l’indexeur. En outre, un accès à l’indexeur a une expression d’instance associée et une liste d’arguments. Lorsqu’un accesseur (le `get` ou `set` bloc) d’un indexeur accès est appelé, le résultat de l’évaluation de l’expression d’instance devient l’instance représentée par `this` ([cet accès](expressions.md#this-access)) et le résultat de l’évaluation de la liste d’arguments devient la liste des paramètres de l’appel.
*  rien. Cela se produit lorsque l’expression est un appel d’une méthode avec un type de retour `void`. Une expression classifiée rien n’est valide dans le contexte d’un *statement_expression* ([instructions d’Expression](statements.md#expression-statements)).

Le résultat final d’une expression n’est jamais un espace de noms, un type, un groupe de méthodes ou un accès à l’événement. Au lieu de cela, comme indiqué ci-dessus, ces catégories d’expressions sont des constructions intermédiaires qui ne sont autorisées que dans certains contextes.

Un accès à la propriété ou indexeur est toujours reclassifié comme une valeur en effectuant un appel de la *accesseur get* ou *accesseur set*. L’accesseur particulier est déterminé par le contexte de l’accès de propriété ou l’indexeur : Si l’accès est la cible d’une assignation, le *accesseur set* est appelé pour assigner une nouvelle valeur ([assignation Simple](expressions.md#simple-assignment)). Sinon, le *accesseur get* est appelé pour obtenir la valeur actuelle ([valeurs des expressions](expressions.md#values-of-expressions)).

### <a name="values-of-expressions"></a>Valeurs des expressions

La plupart des constructions qui impliquent une expression nécessitent finalement l’expression dénote un ***valeur***. Dans ce cas, si l’expression réelle dénote un espace de noms, un type, un groupe de méthodes ou rien, une erreur de compilation se produit. Toutefois, si l’expression dénote un accès à la propriété, accès à un indexeur ou une variable, la valeur de la propriété, un indexeur ou une variable est implicitement substituée :

*  La valeur d’une variable est simplement la valeur actuellement stockée dans l’emplacement de stockage identifié par la variable. Une variable doit être considérée comme assignée définitivement ([assignation définie](variables.md#definite-assignment)) avant que sa valeur peut être obtenue ou sinon une erreur de compilation se produit.
*  La valeur d’une expression d’accès de propriété est obtenue en appelant le *accesseur get* de la propriété. Si la propriété n’a pas *accesseur get*, une erreur de compilation se produit. Sinon, un appel de fonction membre ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) est effectuée, et le résultat de l’appel devient la valeur de l’expression d’accès de propriété.
*  La valeur d’une expression d’accès indexeur est obtenue en appelant le *accesseur get* de l’indexeur. Si l’indexeur n’a pas *accesseur get*, une erreur de compilation se produit. Sinon, un appel de fonction membre ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) est effectuée avec l’argument liste associé à l’expression d’accès indexeur et le résultat de l’appel devient la valeur de l’expression d’accès indexeur.

## <a name="static-and-dynamic-binding"></a>Liaison statique et dynamique

Le processus de détermination de la signification d’une opération basée sur le type de valeur d’expressions qui le composent (arguments, opérandes, récepteurs) est souvent appelé ***liaison***. Par exemple la signification d’un appel de méthode est déterminée en fonction du type de récepteur et d’arguments. La signification d’un opérateur est déterminée en fonction du type de ses opérandes.

En C# la signification d’une opération est généralement déterminée au moment de la compilation, en fonction du type de compilation de ses expressions qui le composent. De même, si une expression contient une erreur, l’erreur est détecté et signalé par le compilateur. Cette approche est appelée ***liaison statique***.

Toutefois, si une expression est une expression dynamique (par exemple, a le type `dynamic`) cela indique que toute liaison il participe doit être basée sur son type au moment de l’exécution (par exemple, le type réel de l’objet, il indique au moment de l’exécution) plutôt que le type sur moment de la compilation. Par conséquent, la liaison d’une telle opération est différée jusqu’au moment où l’opération doit être exécutée pendant l’exécution du programme. Cela est appelé ***liaison dynamique***.

Lorsqu’une opération est liée dynamiquement, peu ou aucune vérification est effectuée par le compilateur. Au lieu de cela si la liaison de l’exécution échoue, les erreurs sont signalées en tant qu’exceptions au moment de l’exécution.

Les opérations suivantes en c# sont soumises à liaison :

*  Accès au membre : `e.M`
*  Appel de méthode : `e.M(e1, ..., eN)`
*  Appel de délégué :`e(e1, ..., eN)`
*  Accès à un élément : `e[e1, ..., eN]`
*  Création d’objets : `new C(e1, ..., eN)`
*  Surcharge des opérateurs unaires : `+`, `-`, `!`, `~`, `++`, `--`, `true`, `false`
*  Les opérateurs binaires surchargés : `+`, `-`, `*`, `/`, `%`, `&`, `&&`, `|`, `||`, `??`, `^`, `<<` , `>>`, `==`,`!=`, `>`, `<`, `>=`, `<=`
*  Opérateurs d’assignation : `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, `>>=`
*  Conversions implicites et explicites

Si aucune expression dynamique n’est impliquées, c# par défaut est une liaison statique, ce qui signifie que les types de compilation d’expressions constitutifs sont utilisés dans le processus de sélection. Toutefois, lorsque l’une des expressions qui le composent dans les opérations répertoriées ci-dessus est une expression dynamique, l’opération est liée à la place dynamiquement.

### <a name="binding-time"></a>Moment de la liaison

Liaison statique intervient au moment de la compilation, tandis que la liaison dynamique a lieu au moment de l’exécution. Dans les sections suivantes, le terme ***moment de la liaison*** fait référence au moment de la compilation ou d’exécution, en fonction de laquelle la liaison a lieu.

L’exemple suivant illustre les notions de liaison statique et dynamique et du moment de la liaison :
```csharp
object  o = 5;
dynamic d = 5;

Console.WriteLine(5);  // static  binding to Console.WriteLine(int)
Console.WriteLine(o);  // static  binding to Console.WriteLine(object)
Console.WriteLine(d);  // dynamic binding to Console.WriteLine(int)
```

Les deux premiers appels sont liées statiquement : la surcharge de `Console.WriteLine` est choisi en fonction du type de compilation de son argument. Par conséquent, le moment de la liaison est lors de la compilation.

Le troisième appel est dynamiquement lié : la surcharge de `Console.WriteLine` est choisi en fonction du type d’exécution de son argument. Cela se produit, car l’argument est une expression dynamique--son type de compilation est `dynamic`. Par conséquent, le temps de liaison pour le troisième appel est exécution.

### <a name="dynamic-binding"></a>Liaison dynamique

L’objectif de la liaison dynamique consiste à autoriser des programmes c# interagir avec ***objets dynamiques***, par exemple, les objets qui ne suivent pas les règles normales de C# système de type. Objets dynamiques peuvent être des objets à partir d’autres langages de programmation avec les systèmes de types différents, ou ils peuvent être des objets qui sont par programmation le programme d’installation pour implémenter leur propre sémantique de liaison pour différentes opérations.

Le mécanisme par lequel un objet dynamique implémente sa propre sémantique est définie par l’implémentation. Une interface donnée--à nouveau définie par l’implémentation--est implémentée par les objets dynamiques afin de signaler à la c# durée d’exécution qu’ils ont une sémantique spéciale. Par conséquent, chaque fois que les opérations sur un objet dynamique sont liées dynamiquement, leur propre sémantique de liaison, plutôt que ceux de c#, comme spécifié dans ce document, reprendre.

Alors que la liaison dynamique vise à permettre l’interopérabilité avec les objets dynamiques, c# permet de liaison dynamique sur tous les objets, qu’ils soient dynamiques ou non. Cela permet une intégration plus lisse des objets dynamiques, comme les résultats des opérations sur ces derniers pas eux-mêmes peuvent être des objets dynamiques, mais sont toujours d’un type inconnu pour le programmeur au moment de la compilation. Liaison dynamique peut également aider à éliminer le code de basée sur reflection sujette aux erreurs même quand aucune objets impliqués ne sont des objets dynamiques.

Les sections suivantes décrivent chaque construction dans le langage for exactement lorsque liaison dynamique est appliqué, ce qui compile la vérification au moment--si toute--est appliquée, et quelles la compilation résultat et expression classification est.

### <a name="types-of-constituent-expressions"></a>Types d’expressions qui le composent

Lorsqu’une opération est liée statiquement, le type d’une expression qui le composent (par exemple, un récepteur, un argument, un index ou un opérande) est toujours considéré comme le type de compilation de cette expression.

Lorsqu’une opération est dynamiquement liée, le type d’une expression qui le composent est déterminé de différentes manières selon le type de compilation de l’expression qui le composent :

*  Une expression de type au moment de la compilation de constitutif `dynamic` est considérée comme ayant le type de la valeur réelle que l’expression prend la valeur lors de l’exécution
*  Une expression qui le composent, dont le type de compilation est un paramètre de type est considéré comme ayant le type auquel est lié le paramètre de type lors de l’exécution
*  Sinon l’expression qui le composent est considérée comme ayant le type de sa compilation.

## <a name="operators"></a>Opérateurs

Les expressions sont construites à partir ***opérandes*** et ***opérateurs***. Les opérateurs d’une expression indiquent les opérations à appliquer aux opérandes. Parmi les exemples d’opérateurs figurent `+`, `-`, `*`, `/` et `new`. Les littéraux, les champs, les variables locales et les expressions sont des exemples d’opérandes.

Il existe trois types d’opérateurs :

*  Les opérateurs unaires. Les opérateurs unaires prennent un opérande et utiliser une notation de préfixe (tel que `--x`) ou un suffixe de notation (tel que `x++`).
*  Opérateurs binaires. Les opérateurs binaires prennent deux opérandes et utilisent toutes la notation infixe (tel que `x + y`).
*  opérateur ternaire. Qu’un seul opérateur ternaire, `?:`, existe ; il prend trois opérandes et emploie la notation infix (`c ? x : y`).

L’ordre d’évaluation des opérateurs dans une expression est déterminé par le ***priorité*** et ***associativité*** des opérateurs ([priorité et associativité](expressions.md#operator-precedence-and-associativity)) .

Les opérandes dans une expression sont évalués de gauche à droite. Par exemple, dans `F(i) + G(i++) * H(i)`, méthode `F` est appelée à l’aide de l’ancienne valeur de `i`, méthode then `G` est appelée avec l’ancienne valeur de `i`et, enfin, méthode `H` est appelée avec la nouvelle valeur de `i`. Cela est distincte et non liées à la priorité des opérateurs.

Certains opérateurs peuvent être ***surchargé***. Surcharge d’opérateur autorise des implémentations de l’opérateur défini par l’utilisateur à être spécifié pour les opérations où un ou les deux opérandes sont d’un type classe ou struct défini par l’utilisateur ([la surcharge d’opérateur](expressions.md#operator-overloading)).

### <a name="operator-precedence-and-associativity"></a>Priorité des opérateurs et associativité

Quand une expression contient plusieurs opérateurs, la ***priorité*** des opérateurs contrôle l'ordre dans lequel les opérateurs individuels sont évalués. Par exemple, l’expression `x + y * z` est évalué comme `x + (y * z)` , car le `*` opérateur a une priorité plus élevée que le fichier binaire `+` opérateur. La priorité d’un opérateur est établie par la définition de sa production grammaticale associée. Par exemple, un *additive_expression* se compose d’une séquence de *multiplicative_expression*s séparés par `+` ou `-` opérateurs, donnant ainsi la `+` et `-` opérateurs diminuer la priorité que la `*`, `/`, et `%` opérateurs.

Le tableau suivant récapitule tous les opérateurs dans l’ordre de priorité, du plus élevé au plus bas :

| __Section__                                                                                   | __Catégorie__                | __Opérateurs__ | 
|-----------------------------------------------------------------------------------------------|-----------------------------|---------------|
| [Expressions primaires](expressions.md#primary-expressions)                                     | Principale                     | `x.y`  `f(x)`  `a[x]`  `x++`  `x--`  `new`  `typeof`  `default`  `checked`  `unchecked`  `delegate` | 
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
| [Opérateurs d’assignation](expressions.md#assignment-operators), [des expressions de fonction anonyme](expressions.md#anonymous-function-expressions)  | Assignation et expression lambda | `=`  `*=`  `/=`  `%=`  `+=`  `-=`  `<<=`  `>>=`  `&=`  `^=`  <code>&#124;=</code>  `=>` | 

Lorsqu’un opérande se produit entre deux opérateurs de même priorité, l’associativité des opérateurs détermine l’ordre dans lequel les opérations sont effectuées :

*  Sauf pour les opérateurs d’assignation et l’opérateur de fusion null, tous les opérateurs binaires sont ***associatifs sur leur gauche***, ce qui signifie que les opérations sont effectuées de gauche à droite. Par exemple, `x + y + z` est évalué comme étant `(x + y) + z`.
*  Les opérateurs d’assignation, l’opérateur de fusion null et l’opérateur conditionnel (`?:`) sont ***associatif à droite***, ce qui signifie que les opérations sont effectuées de droite à gauche. Par exemple, `x = y = z` est évalué comme étant `x = (y = z)`.

La priorité et l’associativité peuvent être contrôlées à l’aide de parenthèses. Par exemple, `x + y * z` multiplie d’abord `y` par `z`, puis ajoute le résultat à `x`, mais `(x + y) * z` ajoute d’abord `x` et `y`, puis multiplie le résultat par `z`.

### <a name="operator-overloading"></a>Surcharge d’opérateur

Tous les opérateurs unaires et binaires ont des implémentations prédéfinies qui sont automatiquement disponibles dans n’importe quelle expression. En plus des implémentations prédéfinies, les implémentations définies par l’utilisateur peuvent être introduites en incluant `operator` déclarations dans les classes et structs ([opérateurs](classes.md#operators)). Implémentations d’opérateurs définis par l’utilisateur ont toujours priorité sur les implémentations d’opérateurs prédéfinis : Uniquement lorsqu’il n’existe aucune implémentation d’opérateur définie par l’utilisateur applicable implémentations considérée, comme décrit dans [opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution) et [surcharge d’opérateur binaire résolution](expressions.md#binary-operator-overload-resolution).

Le ***opérateurs surchargeables unaires*** sont :
```csharp
+   -   !   ~   ++   --   true   false
```

Bien que `true` et `false` ne sont pas explicitement utilisés dans les expressions (et par conséquent ne sont pas inclus dans le tableau de priorité dans [priorité et associativité](expressions.md#operator-precedence-and-associativity)), ils sont considérés comme les opérateurs, car ils sont appelé dans plusieurs contextes d’expression : expressions booléennes ([expressions booléennes](expressions.md#boolean-expressions)) et les expressions impliquant l’instruction conditionnelle ([opérateur conditionnel](expressions.md#conditional-operator)) et conditionnel logique opérateurs ([opérateurs logiques conditionnels](expressions.md#conditional-logical-operators)).

Le ***surchargeables opérateurs binaires*** sont :
```csharp
+   -   *   /   %   &   |   ^   <<   >>   ==   !=   >   <   >=   <=
```

Seuls les opérateurs répertoriés ci-dessus peuvent être surchargés. En particulier, il n’est pas possible de surcharger l’accès au membre, l’appel de méthode, ou le `=`, `&&`, `||`, `??`, `?:`, `=>`, `checked`, `unchecked`, `new`, `typeof`, `default`, `as`, et `is` opérateurs.

Quand un opérateur binaire est surchargé, l’opérateur d’assignation correspondant, le cas échéant, est aussi implicitement surchargé. Par exemple, une surcharge d’opérateur `*` est également une surcharge d’opérateur `*=`. Cela est décrite plus loin dans [assignation composée](expressions.md#compound-assignment). Notez que l’opérateur d’assignation (`=`) ne peut pas être surchargé. Une assignation effectue toujours une copie bit par bit simple d’une valeur dans une variable.

Effectuer un cast des opérations, telles que `(T)x`, sont surchargées en fournissant des conversions définies par l’utilisateur ([conversions définies par l’utilisateur](conversions.md#user-defined-conversions)).

Élément d’accès, tel que `a[x]`, n’est pas considéré comme un opérateur surchargeable. Au lieu de cela, défini par l’utilisateur de l’indexation est prise en charge via des indexeurs ([indexeurs](classes.md#indexers)).

Dans les expressions, opérateurs sont référencées à l’aide de la notation de l’opérateur et dans les déclarations, les opérateurs sont référencées à l’aide de la notation fonctionnelle. Le tableau suivant montre la relation entre l’opérateur et les notations fonctionnelles pour les opérateurs unaires et binaires. Dans la première entrée, *op* désigne n’importe quel opérateur de préfixe unaire surchargeable. Dans la deuxième entrée, *op* désigne le suffixe unaire `++` et `--` opérateurs. Dans la troisième entrée *op* désigne tout opérateur binaire surchargeable.


| __Notation de l’opérateur__ | __Notation fonctionnelle__ |
|-----------------------|-------------------------|
| `op x`                | `operator op(x)`        | 
| `x op`                | `operator op(x)`        | 
| `x op y`              | `operator op(x,y)`      | 

Déclarations d’opérateur défini par l’utilisateur nécessitent toujours au moins un des paramètres de type classe ou struct qui contient la déclaration d’opérateur. Par conséquent, il n’est pas possible pour un opérateur défini par l’utilisateur d’avoir la même signature qu’un opérateur prédéfini.

Déclarations d’opérateur défini par l’utilisateur ne peut pas modifier la syntaxe, priorité ou associativité d’un opérateur. Par exemple, le `/` opérateur est toujours un opérateur binaire, a toujours le niveau de priorité spécifié dans [priorité et associativité](expressions.md#operator-precedence-and-associativity)et est toujours associatifs sur leur gauche.

S’il est possible pour un opérateur défini par l’utilisateur effectuer des calculs qu’il souhaite, les implémentations qui produisent des résultats autres que ceux qui sont intuitivement attendus sont fortement déconseillées. Par exemple, une implémentation de `operator ==` doit comparer les deux opérandes sont égaux et renvoyer un approprié `bool` résultat.

Les descriptions de chacun des opérateurs dans [expressions primaires](expressions.md#primary-expressions) via [opérateurs logiques conditionnels](expressions.md#conditional-logical-operators) spécifient les implémentations prédéfinies des opérateurs et des règles supplémentaires qui s’appliquent à chaque opérateur. Les descriptions utiliser des termes du contrat ***opérateur unaire de résolution de surcharge***, ***opérateur binaire de résolution de surcharge***, et ***promotion numérique***, dont les définitions trouvé dans les sections suivantes.

### <a name="unary-operator-overload-resolution"></a>Résolution de surcharge d’opérateur unaire

Une opération de la forme `op x` ou `x op`, où `op` est un opérateur unaire surchargeable, et `x` est une expression de type `X`, est traitée comme suit :

*  L’ensemble des opérateurs de défini par l’utilisateur candidats fourni par `X` pour l’opération `operator op(x)` est déterminée en utilisant les règles de [opérateurs définis par l’utilisateur candidats](expressions.md#candidate-user-defined-operators).
*  Si l’ensemble des opérateurs définis par l’utilisateur de candidat n’est pas vide, il devient alors l’ensemble des opérateurs candidats pour l’opération. Sinon, l’unaire prédéfinie `operator op` implémentations, y compris leurs formes levés, devient l’ensemble des opérateurs candidats pour l’opération. Les implémentations prédéfinies d’un opérateur donné sont spécifiées dans la description de l’opérateur ([expressions primaires](expressions.md#primary-expressions) et [opérateurs unaires](expressions.md#unary-operators)).
*  Les règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution) sont appliquées à l’ensemble des opérateurs candidats pour sélectionner le meilleur opérateur en ce qui concerne la liste d’arguments `(x)`, et cet opérateur devient le résultat de la surcharge processus de résolution. Si la résolution de surcharge ne parvient pas à sélectionner un meilleur opérateur unique, une erreur au moment de la liaison se produit.

### <a name="binary-operator-overload-resolution"></a>Résolution de surcharge d’opérateur binaire

Une opération de la forme `x op y`, où `op` est un opérateur binaire surchargeable, `x` est une expression de type `X`, et `y` est une expression de type `Y`, est traitée comme suit :

*  L’ensemble des opérateurs de défini par l’utilisateur candidats fourni par `X` et `Y` pour l’opération `operator op(x,y)` est déterminée. Le jeu se compose de l’union des opérateurs candidats fournis par `X` et opérateurs candidats fournis par `Y`, chaque déterminée en utilisant les règles de [opérateurs définis par l’utilisateur candidats](expressions.md#candidate-user-defined-operators). Si `X` et `Y` sont du même type, ou si `X` et `Y` sont dérivées à partir d’un type de base commun, puis les opérateurs candidats partagés se produisent uniquement dans l’ensemble combiné qu’une seule fois.
*  Si l’ensemble des opérateurs définis par l’utilisateur de candidat n’est pas vide, il devient alors l’ensemble des opérateurs candidats pour l’opération. Sinon, le fichier binaire prédéfini `operator op` implémentations, y compris leurs formes levés, devient l’ensemble des opérateurs candidats pour l’opération. Les implémentations prédéfinies d’un opérateur donné sont spécifiées dans la description de l’opérateur ([opérateurs arithmétiques](expressions.md#arithmetic-operators) via [opérateurs logiques conditionnels](expressions.md#conditional-logical-operators)). Pour les opérateurs d’enum et le délégué prédéfinis, seuls les opérateurs considéré comme sont celles définies par un type enum ou un délégué qui est le type au moment de la liaison d’un des opérandes.
*  Les règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution) sont appliquées à l’ensemble des opérateurs candidats pour sélectionner le meilleur opérateur en ce qui concerne la liste d’arguments `(x,y)`, et cet opérateur devient le résultat de la surcharge processus de résolution. Si la résolution de surcharge ne parvient pas à sélectionner un meilleur opérateur unique, une erreur au moment de la liaison se produit.

### <a name="candidate-user-defined-operators"></a>Opérateurs définis par l’utilisateur candidats

Un type donné `T` et une opération `operator op(A)`, où `op` est un opérateur surchargeable et `A` est une liste d’arguments, l’ensemble des candidats les opérateurs définis par l’utilisateur fournis par `T` pour `operator op(A)` est déterminé comme suit :

*  Déterminer le type `T0`. Si `T` est un type nullable, `T0` est son type sous-jacent, sinon `T0` est égal à `T`.
*  Pour toutes les `operator op` déclarations dans `T0` et tous les levé ces opérateurs, si au moins un opérateur n’est applicable ([membre de fonction Applicable](expressions.md#applicable-function-member)) par rapport à la liste d’arguments `A`, puis l’ensemble des opérateurs candidats se compose de tous les opérateurs qui font l’objet de ce type applicables dans `T0`.
*  Sinon, si `T0` est `object`, l’ensemble des opérateurs candidats est vide.
*  Sinon, l’ensemble des opérateurs candidats fourni par `T0` est l’ensemble des opérateurs candidats fournis par la classe de base directe de `T0`, ou la classe de base efficace de `T0` si `T0` est un paramètre de type.

### <a name="numeric-promotions"></a>Promotions numériques

La promotion numérique consiste à effectuer automatiquement certaines conversions implicites des opérandes de le prédéfinis opérateurs unaires et binaires numériques. La promotion numérique n’est pas un mécanisme distinct, mais plutôt un effet de l’application de la résolution de surcharge pour les opérateurs prédéfinis. La promotion numérique n’affecte pas, en particulier d’évaluation des opérateurs définis par l’utilisateur, bien que les opérateurs définis par l’utilisateur peuvent être implémentées pour présenter des effets similaires.

Comme exemple de promotion numérique, examinons les implémentations prédéfinies du binaire `*` opérateur :

```csharp
int operator *(int x, int y);
uint operator *(uint x, uint y);
long operator *(long x, long y);
ulong operator *(ulong x, ulong y);
float operator *(float x, float y);
double operator *(double x, double y);
decimal operator *(decimal x, decimal y);
```

Lorsque les règles de résolution de surcharge ([la résolution de surcharge](expressions.md#overload-resolution)) sont appliquées à cet ensemble d’opérateurs, l’effet consiste à sélectionner le premier des opérateurs pour lesquels existent des conversions implicites à partir de types d’opérandes. Par exemple, pour l’opération `b * s`, où `b` est un `byte` et `s` est un `short`, sélectionne de résolution de surcharge `operator *(int,int)` comme meilleur opérateur. Par conséquent, le résultat est que `b` et `s` sont convertis en `int`, et le type du résultat est `int`. De même, pour l’opération `i * d`, où `i` est un `int` et `d` est un `double`, sélectionne de résolution de surcharge `operator *(double,double)` comme meilleur opérateur.

#### <a name="unary-numeric-promotions"></a>Promotions numériques unaires

La promotion numérique unaire se produit pour les opérandes de prédéfinis `+`, `-`, et `~` opérateurs unaires. La promotion numérique unaire se compose simplement de la conversion des opérandes de type `sbyte`, `byte`, `short`, `ushort`, ou `char` à taper `int`. En outre, dans le cas de `-` opérateur, la promotion numérique unaire convertit les opérandes de type `uint` à taper `long`.

#### <a name="binary-numeric-promotions"></a>Promotions numériques binaires

La promotion numérique binaire se produit pour les opérandes de prédéfinis `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `==`, `!=`, `>`, `<`, `>=`, et `<=` opérateurs binaires. La promotion numérique binaire convertit implicitement les deux opérandes en un type commun qui, dans le cas des opérateurs non relationnels, devient également le type de résultat de l’opération. La promotion numérique binaire consiste à appliquer les règles suivantes, dans l’ordre de qu'apparition ici :

*  Si des opérandes est de type `decimal`, l’autre opérande est converti en type `decimal`, ou une erreur au moment de la liaison se produit si l’autre opérande est de type `float` ou `double`.
*  Sinon, si des opérandes est de type `double`, l’autre opérande est converti en type `double`.
*  Sinon, si des opérandes est de type `float`, l’autre opérande est converti en type `float`.
*  Sinon, si des opérandes est de type `ulong`, l’autre opérande est converti en type `ulong`, ou une erreur au moment de la liaison se produit si l’autre opérande est de type `sbyte`, `short`, `int`, ou `long`.
*  Sinon, si des opérandes est de type `long`, l’autre opérande est converti en type `long`.
*  Sinon, si des opérandes est de type `uint` et l’autre opérande est de type `sbyte`, `short`, ou `int`, les deux opérandes sont convertis en type `long`.
*  Sinon, si des opérandes est de type `uint`, l’autre opérande est converti en type `uint`.
*  Sinon, les deux opérandes sont convertis en type `int`.

Notez que la première règle n’autorise pas toutes les opérations qui combinent le `decimal` type avec le `double` et `float` types. La règle résulte du fait qu’aucune conversion implicite entre le `decimal` type et le `double` et `float` types.

Notez également qu’il n’est pas possible pour un opérande de type `ulong` lorsque l’autre opérande est d’un type intégral signé. La raison est qu’il existe aucun type intégral qui peut représenter la gamme complète des `ulong` , ainsi que les types intégraux signés.

Dans les deux cas ci-dessus, une expression de cast peut être utilisée pour convertir explicitement un opérande en un type qui est compatible avec l’autre opérande.

Dans l’exemple
```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (1.0 + percent / 100.0);
}
```
une erreur au moment de la liaison se produit, car un `decimal` ne peut pas être multiplié par un `double`. L’erreur est résolue en la convertissant explicitement le deuxième opérande à `decimal`, comme suit :

```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (decimal)(1.0 + percent / 100.0);
}
```

### <a name="lifted-operators"></a>Opérateurs levés

***Levé opérateurs*** autoriser les opérateurs prédéfinis et définis par l’utilisateur qui fonctionnent sur des types valeur non nullable à utiliser avec les formulaires nullables de ces types. Opérateurs capturées sont construites à partir d’opérateurs prédéfinis et définis par l’utilisateur qui répondent à certaines exigences, comme décrit dans le code suivant :

*   Pour les opérateurs unaires

    ```csharp
    +  ++  -  --  !  ~
    ```

    Il existe un formulaire levé d’un opérateur si les types d’opérande et le résultat sont les deux types de valeur non nullable. Le formulaire levé est construit en ajoutant un seul `?` modificateur aux types d’opérande et le résultat. L’opérateur levé produit une valeur null si l’opérande a la valeur null. Sinon, l’opérateur levé Désencapsule l’opérande s’applique l’opérateur sous-jacent et encapsule le résultat.

*   Pour les opérateurs binaires

    ```csharp
    +  -  *  /  %  &  |  ^  <<  >>
    ```

    Il existe un formulaire levé d’un opérateur si les types d’opérande et le résultat sont tous les types valeur non nullable. Le formulaire levé est construit en ajoutant un seul `?` modificateur à chaque type d’opérande et le résultat. L’opérateur levé produit une valeur null si un ou les deux opérandes ont la valeur null (une exception qui est la `&` et `|` opérateurs de la `bool?` type, comme décrit dans [booléennes opérateurs logiques](expressions.md#boolean-logical-operators)). Sinon, l’opérateur levé désencapsule les opérandes s’applique l’opérateur sous-jacent et encapsule le résultat.

*   Pour les opérateurs d’égalité

    ```csharp
    ==  !=
    ```

    Il existe un formulaire levé d’un opérateur si les types d’opérandes sont des types valeur non nullable et si le type de résultat est `bool`. Le formulaire levé est construit en ajoutant un seul `?` modificateur à chaque type d’opérande. L’opérateur levé considère qu’égal de deux valeurs null et une valeur null inégal à n’importe quelle valeur non null. Si les deux opérandes sont non null, l’opérateur levé désencapsule les opérandes et applique l’opérateur sous-jacente pour produire le `bool` résultat.

*   Pour les opérateurs relationnels

    ```csharp
    <  >  <=  >=
    ```

    Il existe un formulaire levé d’un opérateur si les types d’opérandes sont des types valeur non nullable et si le type de résultat est `bool`. Le formulaire levé est construit en ajoutant un seul `?` modificateur à chaque type d’opérande. L’opérateur levé produit la valeur `false` si un ou deux opérandes sont null. Sinon, l’opérateur levé désencapsule les opérandes et applique l’opérateur sous-jacente pour produire le `bool` résultat.

## <a name="member-lookup"></a>Recherche de membres

Recherche d’un membre est le processus par lequel la signification d’un nom dans le contexte d’un type est déterminée. Recherche d’un membre peut se produire dans le cadre de l’évaluation d’un *simple_name* ([noms simples](expressions.md#simple-names)) ou un *member_access* ([l’accès au membre](expressions.md#member-access)) dans un expression. Si le *simple_name* ou *member_access* se produit en tant que le *primary_expression* d’un *invocation_expression* ([ Appels de méthode](expressions.md#method-invocations)), le membre est dit à appeler.

Si un membre est une méthode ou un événement, ou s’il s’agit d’une constante, le champ ou la propriété de type délégué ([délégués](delegates.md)) ou le type `dynamic` ([du type dynamic](types.md#the-dynamic-type)), le membre est dit être *invocable*.

Recherche de membres considère que non seulement le nom d’un membre, mais également le nombre de paramètres de type pour le membre et si le membre est accessible. Dans le cadre de la recherche de membres, les méthodes génériques et des types génériques imbriqués ont le nombre de paramètres de type indiqué dans leurs déclarations respectifs et tous les autres membres ont aucun paramètre de type.

Recherche d’un membre d’un nom `N` avec `K`  paramètres de type dans un type `T` est traité comme suit :

*  Tout d’abord, un ensemble de membres accessibles nommé `N` est déterminé :
    * Si `T` est un paramètre de type, le jeu est l’union des jeux de membres accessibles nommés `N` dans chacun des types spécifiés en tant que contrainte primaire ou secondaire contrainte ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)) pour  `T`, ainsi que l’ensemble des membres accessibles nommé `N` dans `object`.
    * Sinon, le jeu est composé de tous accessibles ([l’accès au membre](basic-concepts.md#member-access)) membres nommés `N` dans `T`, y compris les membres hérités et les membres accessibles nommés `N` dans `object`. Si `T` est un type construit, le jeu de membres est obtenu en remplaçant les arguments de type comme décrit dans [membres des types construits](classes.md#members-of-constructed-types). Les membres qui incluent un `override` modificateur sont exclus de l’ensemble.
*  Ensuite, si `K` est égal à zéro, imbriquée de tous les types dont les déclarations incluent des paramètres de type sont supprimés. Si `K` est ne pas égal à zéro, tous les membres avec un nombre différent de type de paramètres sont supprimés. Notez que lorsque `K` sont zéro, les méthodes ayant le type de paramètres ne sont pas supprimées, depuis le processus d’inférence de type ([l’inférence de Type](expressions.md#type-inference)) peut être en mesure de déduire les arguments de type.
*  Ensuite, si le membre est *appelé*, tous les éléments non-*invocable* membres sont supprimés de l’ensemble.
*  Ensuite, les membres qui sont masqués par d’autres membres sont supprimés de l’ensemble. Pour chaque membre `S.M` dans l’ensemble, où `S` est le type dans lequel le membre `M` est déclaré, les règles suivantes sont appliquées :
    * Si `M` est constante, champ, propriété, événement ou membre d’énumération, puis tous les membres déclarés dans un type de base de `S` sont supprimés de l’ensemble.
    * Si `M` est une déclaration de type, puis tous les non-types déclarés dans un type de base de `S` sont supprimés de l’ensemble, et toutes les déclarations avec le même nombre de paramètres de type en tant que de type `M` déclaré dans un type de base de `S` sont supprimés à partir du jeu.
    * Si `M` est une méthode, puis tous les membres non méthode déclarée dans un type de base de `S` sont supprimés de l’ensemble.
*  Ensuite, les membres d’interface qui sont masqués par les membres de classe sont supprimés de l’ensemble. Cette étape n’a d’effet que si `T` est un paramètre de type et `T` a à la fois une classe de base efficace autre que `object` et une interface efficace vide définie ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)). Pour chaque membre `S.M` dans l’ensemble, où `S` est le type dans lequel le membre `M` est déclaré, les règles suivantes sont appliquées si `S` est une déclaration de classe autre que `object`:
    * Si `M` est une constante, champ, propriété, événement, membre d’énumération ou déclaration de type, puis tous les membres déclarés dans une déclaration d’interface sont supprimés de l’ensemble.
    * Si `M` est une méthode, puis tous les membres non méthode déclarées dans une déclaration d’interface sont supprimés de l’ensemble et toutes les méthodes avec la même signature que `M` déclaré dans une interface déclaration sont supprimés de l’ensemble.
*  Enfin, le résultat de la recherche d’avoir supprimé les membres masqués, est déterminé :
    * Si le jeu se compose d’un seul membre qui n’est pas une méthode, ce membre est le résultat de la recherche.
    * Sinon, si le jeu contient uniquement des méthodes, ce groupe de méthodes est le résultat de la recherche.
    * Sinon, la recherche est ambiguë et une erreur au moment de la liaison se produit.

Pour les recherches de membres dans les types autres que les paramètres de type et les interfaces et les recherches de membres dans les interfaces qui sont strictement à héritage unique (chaque interface dans la chaîne d’héritage possède exactement zéro ou une interface de base directe), est l’effet des règles de recherche simplement qui dérivée des membres de base de masquer les membres avec le même nom ou la même signature. Ces recherches à héritage unique ne sont jamais ambiguës. Les ambiguïtés qui peuvent éventuellement résulter des recherches dans les interfaces de l’héritage multiple sont décrites dans [accès aux membres de l’Interface](interfaces.md#interface-member-access).

### <a name="base-types"></a>Types de base

À des fins de recherche de membres, un type `T` est considérée comme ayant les types de base suivants :

*  Si `T` est `object`, puis `T` n’a aucun type de base.
*  Si `T` est un *type_de_liste*, les types de base de `T` sont les types de classe `System.Enum`, `System.ValueType`, et `object`.
*  Si `T` est un *struct_type*, les types de base de `T` sont les types de classe `System.ValueType` et `object`.
*  Si `T` est un *class_type*, les types de base de `T` sont des classes de base de `T`, y compris le type de classe `object`.
*  Si `T` est un *interface_type*, les types de base de `T` sont les interfaces de base de `T` et le type de classe `object`.
*  Si `T` est un *array_type*, les types de base de `T` sont les types de classe `System.Array` et `object`.
*  Si `T` est un *delegate_type*, les types de base de `T` sont les types de classe `System.Delegate` et `object`.

## <a name="function-members"></a>Fonctions membres

Les fonctions membres sont des membres qui contiennent des instructions exécutables. Fonctions membres sont toujours membres de types et ne peut pas être membres d’espaces de noms. C# définit les catégories de fonctions membres suivantes :

*  Méthodes
*  Propriétés
*  Événements
*  Indexeurs
*  Opérateurs définis par l’utilisateur
*  Constructeurs d’instance
*  Constructeurs statiques
*  Destructeurs

À l’exception des destructeurs et des constructeurs statiques (qui ne peut pas être appelés explicitement), les instructions contenues dans les fonctions membres sont exécutées via des appels de fonctions membres. La syntaxe réelle pour l’écriture d’un appel de fonction membre dépend de la catégorie de membre de fonction particulière.

La liste d’arguments ([listes d’arguments](expressions.md#argument-lists)) d’une fonction membre appel fournit des valeurs réelles ou des références variables pour les paramètres de la fonction membre.

Appels de méthodes génériques peuvent utiliser l’inférence de type pour déterminer le jeu d’arguments de type à passer à la méthode. Ce processus est décrit dans [l’inférence de Type](expressions.md#type-inference).

Les appels de méthodes, indexeurs, opérateurs et constructeurs d’instance emploient la résolution de surcharge pour déterminer parmi un ensemble de candidats de membres de fonction à appeler. Ce processus est décrit dans [la résolution de surcharge](expressions.md#overload-resolution).

Une fois qu’une fonction membre particulière a été identifié au moment de la liaison, éventuellement via la résolution de surcharge, le processus d’exécution réel de l’appel de la fonction membre est décrit dans [la vérification de la résolution de surcharge dynamiquelorsdelacompilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

Le tableau suivant récapitule le traitement qui a lieu dans les constructions qui impliquent les six catégories de fonctions membres qui peuvent être appelés explicitement. Dans la table, `e`, `x`, `y`, et `value` indiquent les expressions classifiées en tant que variables ou des valeurs, `T` indique une expression classifiée comme un type, `F` est le nom simple d’une méthode, `P` est le nom simple d’une propriété.


| __Construction__     | __Exemple__    | __Description__ |
|-------------------|----------------|-----------------|
| Appel de méthode | `F(x,y)`       | Résolution de surcharge est appliquée pour sélectionner la meilleure méthode `F` dans la classe conteneur ou un struct. La méthode est appelée avec la liste d’arguments `(x,y)`. Si la méthode n’est pas `static`, l’expression d’instance est `this`. | 
|                   | `T.F(x,y)`     | Résolution de surcharge est appliquée pour sélectionner la meilleure méthode `F` dans la classe ou structure `T`. Une erreur au moment de la liaison se produit si la méthode n’est pas `static`. La méthode est appelée avec la liste d’arguments `(x,y)`. | 
|                   | `e.F(x,y)`     | Résolution de surcharge est appliquée pour sélectionner la meilleure méthode F dans la classe, un struct ou une interface donnée par le type de `e`. Une erreur au moment de la liaison se produit si la méthode est `static`. La méthode est appelée avec l’expression d’instance `e` et la liste d’arguments `(x,y)`. | 
| Accès à la propriété   | `P`            | Le `get` accesseur de la propriété `P` dans la classe conteneur ou un struct est appelé. Une erreur de compilation se produit si `P` est en écriture seule. Si `P` n’est pas `static`, l’expression d’instance est `this`. | 
|                   | `P = value`    | Le `set` accesseur de la propriété `P` dans la classe conteneur ou un struct est appelé avec la liste d’arguments `(value)`. Une erreur de compilation se produit si `P` est en lecture seule. Si `P` n’est pas `static`, l’expression d’instance est `this`. | 
|                   | `T.P`          | Le `get` accesseur de la propriété `P` dans la classe ou structure `T` est appelé. Une erreur de compilation se produit si `P` n’est pas `static` ou si `P` est en écriture seule. | 
|                   | `T.P = value`  | Le `set` accesseur de la propriété `P` dans la classe ou structure `T` est appelé avec la liste d’arguments `(value)`. Une erreur de compilation se produit si `P` n’est pas `static` ou si `P` est en lecture seule. | 
|                   | `e.P`          | Le `get` accesseur de la propriété `P` dans la classe, un struct ou une interface donnée par le type de `e` est appelé avec l’expression d’instance `e`. Une erreur au moment de la liaison se produit si `P` est `static` ou si `P` est en écriture seule. | 
|                   | `e.P = value`  | Le `set` accesseur de la propriété `P` dans la classe, un struct ou une interface donnée par le type de `e` est appelé avec l’expression d’instance `e` et la liste d’arguments `(value)`. Une erreur au moment de la liaison se produit si `P` est `static` ou si `P` est en lecture seule. | 
| Accès à l’événement      | `E += value`   | Le `add` accesseur de l’événement `E` dans la classe conteneur ou un struct est appelé. Si `E` est non statique, l’expression d’instance est `this`. | 
|                   | `E -= value`   | Le `remove` accesseur de l’événement `E` dans la classe conteneur ou un struct est appelé. Si `E` est non statique, l’expression d’instance est `this`. | 
|                   | `T.E += value` | Le `add` accesseur de l’événement `E` dans la classe ou structure `T` est appelé. Une erreur au moment de la liaison se produit si `E` n’est pas statique. | 
|                   | `T.E -= value` | Le `remove` accesseur de l’événement `E` dans la classe ou structure `T` est appelé. Une erreur au moment de la liaison se produit si `E` n’est pas statique. | 
|                   | `e.E += value` | Le `add` accesseur de l’événement `E` dans la classe, un struct ou une interface donnée par le type de `e` est appelé avec l’expression d’instance `e`. Une erreur au moment de la liaison se produit si `E` est statique. | 
|                   | `e.E -= value` | Le `remove` accesseur de l’événement `E` dans la classe, un struct ou une interface donnée par le type de `e` est appelé avec l’expression d’instance `e`. Une erreur au moment de la liaison se produit si `E` est statique. | 
| Accès aux indexeurs    | `e[x,y]`       | Résolution de surcharge est appliquée pour sélectionner le meilleur indexeur dans la classe, un struct ou une interface donné par le type de e. Le `get` accesseur de l’indexeur est appelé avec l’expression d’instance `e` et la liste d’arguments `(x,y)`. Une erreur au moment de la liaison se produit si l’indexeur est en écriture seule. | 
|                   | `e[x,y] = value` | Résolution de surcharge est appliquée pour sélectionner le meilleur indexeur dans la classe, un struct ou une interface donnée par le type de `e`. Le `set` accesseur de l’indexeur est appelé avec l’expression d’instance `e` et la liste d’arguments `(x,y,value)`. Une erreur au moment de la liaison se produit si l’indexeur est en lecture seule. | 
| Appel d’opérateur | `-x`         | Résolution de surcharge est appliquée pour sélectionner le meilleur opérateur unaire dans la classe ou le struct donné par le type de `x`. L’opérateur sélectionné est appelé avec la liste d’arguments `(x)`. | 
|                     | `x + y`      | Résolution de surcharge est appliquée pour sélectionner le meilleur opérateur binaire dans les classes ou les structs donnés par les types de `x` et `y`. L’opérateur sélectionné est appelé avec la liste d’arguments `(x,y)`. | 
| Appel de constructeur d’instance | `new T(x,y)` | Résolution de surcharge est appliquée pour sélectionner le meilleur constructeur d’instance dans la classe ou structure `T`. Le constructeur d’instance est appelé avec la liste d’arguments `(x,y)`. | 

### <a name="argument-lists"></a>Listes d’arguments

Chaque fonction membre et appel de délégué inclut une liste d’arguments qui fournit les valeurs réelles ou des références variables pour les paramètres de la fonction membre. La syntaxe de spécification de la liste d’arguments d’un appel de fonction membre dépend de la catégorie de membre de fonction :

*  Par exemple, les constructeurs, méthodes, indexeurs et délégués, les arguments sont spécifiés comme un *argument_list*, comme décrit ci-dessous. Pour les indexeurs, lors de l’appel le `set` accesseur, la liste d’arguments inclut en outre l’expression spécifiée comme opérande de droite de l’opérateur d’assignation.
*  Pour les propriétés, la liste d’arguments est vide lors de l’appel le `get` accesseur et se compose de l’expression spécifiée en tant qu’opérande de droite de l’opérateur d’assignation lors de l’appel le `set` accesseur.
*  Pour les événements, la liste d’arguments se compose de l’expression spécifiée en tant qu’opérande de droite de la `+=` ou `-=` opérateur.
*  Opérateurs définis par l’utilisateur, la liste d’arguments se compose de l’opérande unique de l’opérateur unaire ou les deux opérandes de l’opérateur binaire.

Les arguments des propriétés ([propriétés](classes.md#properties)), événements ([événements](classes.md#events)) et les opérateurs définis par l’utilisateur ([opérateurs](classes.md#operators)) sont toujours transmis en tant que paramètres de valeur ([ Paramètres de valeur](classes.md#value-parameters)). Les arguments des indexeurs ([indexeurs](classes.md#indexers)) sont toujours transmis en tant que paramètres de valeur ([paramètres de valeur](classes.md#value-parameters)) ou des tableaux de paramètres ([tableaux de paramètres](classes.md#parameter-arrays)). Paramètres de référence et de sortie ne sont pas pris en charge pour ces catégories de fonctions membres.

Les arguments d’un appel de constructeur, de méthode, indexeur ou délégué instance sont spécifiés comme un *argument_list*:

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

Un *argument_list* se compose d’une ou plusieurs *argument*s, séparés par des virgules. Chaque argument se compose d’un texte facultatif *nom_argument* suivie d’une *argument_value*. Un *argument* avec un *nom_argument* est appelé un ***argument nommé***, tandis qu’un *argument* sans un  *nom_argument* est un ***argument positionnel***. C’est une erreur pour un argument positionnel à apparaître après un argument nommé dans une *argument_list*.

Le *argument_value* peut prendre l’une des formes suivantes :

*  Un *expression*, ce qui indique que l’argument est passé comme un paramètre de valeur ([paramètres de valeur](classes.md#value-parameters)).
*  Le mot clé `ref` suivie d’un *variable_reference* ([références de Variable](variables.md#variable-references)), ce qui indique que l’argument est passé comme paramètre de référence ([paramètres de référence ](classes.md#reference-parameters)). Une variable doit absolument être assignée ([assignation définie](variables.md#definite-assignment)) avant de pouvoir être passée comme paramètre de référence. Le mot clé `out` suivie d’un *variable_reference* ([références de Variable](variables.md#variable-references)), ce qui indique que l’argument est passé comme paramètre de sortie ([deparamètresdesortie](classes.md#output-parameters)). Une variable est considéré comme définitivement assignée ([assignation définie](variables.md#definite-assignment)) après l’appel d’une fonction membre dans lequel la variable est passée comme paramètre de sortie.

#### <a name="corresponding-parameters"></a>Paramètres correspondants

Pour chaque argument dans une liste d’arguments il doit être un paramètre correspondant dans le membre de fonction ou un délégué appelé.

La liste des paramètres utilisée dans le code suivant est déterminée comme suit :

*  Pour les méthodes virtuelles et des indexeurs définis dans les classes, la liste de paramètres est récupéré à partir de la déclaration de la plus spécifique ou la substitution de la fonction membre, en commençant par le type statique du récepteur et la recherche dans ses classes de base.
*  Pour les méthodes d’interface et indexeurs, la liste de paramètres est prélevée forment la définition la plus spécifique du membre, en commençant par le type d’interface et la recherche dans les interfaces de base. Si aucune liste de paramètres unique n’est trouvé, une liste de paramètres avec les noms sont inaccessibles et aucun paramètre facultatif est construite, afin que les appels ne peut pas utiliser des paramètres nommés ou omettre les arguments facultatifs.
*  Pour les méthodes partielles, la liste des paramètres de la déclaration de méthode partielle de définition est utilisée.
*  Pour toutes les autres fonctions membres et les délégués, il existe uniquement une liste de paramètres unique, qui est celui utilisé.

La position d’un argument ou un paramètre est définie comme le nombre d’arguments ou paramètres précèdent dans la liste d’arguments ou d’une liste de paramètres.

Les paramètres correspondants pour les arguments de fonction membre sont établies comme suit :

*  Arguments dans le *argument_list* de constructeurs d’instance, méthodes, indexeurs et délégués :
    * Un argument positionnel où un paramètre fixe se produit à la même position dans la liste de paramètres correspond à ce paramètre.
    * Un argument positionnel d’une fonction membre avec un tableau de paramètres appelé dans sa forme normale correspond au tableau de paramètres, qui doit se produire à la même position dans la liste de paramètres.
    * Un argument positionnel d’une fonction membre avec un tableau de paramètres appelé dans sa forme étendue, où aucun paramètre fixe se produit à la même position dans la liste de paramètres, correspond à un élément dans le tableau de paramètres.
    * Un argument nommé correspond au paramètre du même nom dans la liste de paramètres.
    * Pour les indexeurs, lors de l’appel le `set` accesseur, l’expression spécifiée comme opérande de droite de l’opérateur d’assignation correspond à l’implicite `value` paramètre de la `set` déclaration d’accesseur.
*  Pour les propriétés, lors de l’appel le `get` accesseur il y a aucun argument. Lors de l’appel le `set` accesseur, l’expression spécifiée comme opérande de droite de l’opérateur d’assignation correspond à l’implicite `value` paramètre de la `set` déclaration d’accesseur.
*  Pour les opérateurs unaire défini par l’utilisateur (y compris les conversions), l’opérande unique correspond au paramètre unique de la déclaration d’opérateur.
*  Pour les opérateurs binaires définis par l’utilisateur, l’opérande gauche correspond au premier paramètre, et l’opérande de droite correspond au second paramètre de la déclaration d’opérateur.

#### <a name="run-time-evaluation-of-argument-lists"></a>Version d’évaluation de l’exécution de listes d’arguments

Pendant le traitement de l’exécution d’un appel de fonction membre ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), les expressions ou les références de variable d’une liste d’arguments sont évalués dans l’ordre, de gauche à droite, en tant que suit :

*  Pour un paramètre de valeur, l’expression d’argument est évaluée et une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) au paramètre correspondant type est effectué. La valeur résultante devient la valeur initiale du paramètre de valeur dans l’appel de fonction membre.
*  Pour un paramètre de référence ou de sortie, la référence variable est évaluée et l’emplacement de stockage résultant devient l’emplacement de stockage représenté par le paramètre dans l’appel de fonction membre. Si la référence de variable donnée comme un paramètre de référence ou de sortie est un élément d’un *reference_type*, une vérification de l’exécution est effectuée pour vous assurer que le type d’élément du tableau est identique au type du paramètre. Si cette vérification échoue, un `System.ArrayTypeMismatchException` est levée.

Méthodes, les indexeurs et les constructeurs d’instance peuvent déclarer leur paramètre le plus à droite pour être un tableau de paramètres ([tableaux de paramètres](classes.md#parameter-arrays)). Ces fonctions membres sont appelés dans leur forme normale ou dans leur forme étendue selon laquelle s’applique ([membre de fonction Applicable](expressions.md#applicable-function-member)) :

*  Lorsqu’une fonction membre avec un tableau de paramètres est appelée dans sa forme normale, l’argument fourni pour le tableau de paramètres doit être une expression unique qui est implicitement convertible ([conversions implicites](conversions.md#implicit-conversions)) pour le type de tableau de paramètres. Dans ce cas, le tableau de paramètres opère précisément comme un paramètre de valeur.
*  Lorsqu’une fonction membre avec un tableau de paramètres est appelée dans sa forme étendue, l’appel doit spécifier zéro ou plusieurs arguments de position pour le tableau de paramètres, où chaque argument est une expression qui est implicitement convertible ([implicite conversions](conversions.md#implicit-conversions)) pour le type d’élément de tableau de paramètres. Dans ce cas, l’appel crée une instance de type tableau de paramètres d’une longueur correspondant au nombre d’arguments, initialise les éléments de l’instance de tableau avec les valeurs d’argument donné et utilise l’instance de tableau qui vient d’être créé en tant que le texte réel argument.

Les expressions d’une liste d’arguments sont toujours évaluées dans l’ordre qu’ils sont écrits. Par conséquent, l’exemple
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
```
x = 0, y = 1, z = 2
x = 4, y = -1, z = 3
```

Les règles de variance co tableau ([covariance de tableau](arrays.md#array-covariance)) permettent à une valeur d’un type tableau `A[]` soit une référence à une instance d’un type tableau `B[]`, à condition qu’existe une conversion de référence implicite de `B` à `A`. En raison de ces règles, lorsqu’elles sont un élément d’un *reference_type* est passé comme paramètre de référence ou de sortie, une vérification de l’exécution est nécessaire pour garantir que le type réel des éléments du tableau est identique à celui du paramètre. Dans l’exemple
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
le deuxième appel de `F` provoque un `System.ArrayTypeMismatchException` levée, car le type de l’élément réel de `b` est `string` et non `object`.

Lorsqu’une fonction membre avec un tableau de paramètres est appelée dans sa forme étendue, l’appel est traité exactement comme si une expression de création de tableau avec un initialiseur de tableau ([expressions de création de tableau](expressions.md#array-creation-expressions)) a été inséré autour de la paramètres de l’étendue. Par exemple, prenons la déclaration
```csharp
void F(int x, int y, params object[] args);
```
les appels suivants de la forme étendue de la méthode
```csharp
F(10, 20);
F(10, 20, 30, 40);
F(10, 20, 1, "hello", 3.0);
```
correspondent exactement à
```csharp
F(10, 20, new object[] {});
F(10, 20, new object[] {30, 40});
F(10, 20, new object[] {1, "hello", 3.0});
```

En particulier, notez qu’un tableau vide est créé lorsqu’il y a aucun argument spécifié pour le tableau de paramètres.

Lorsque les arguments sont omis à partir d’une fonction membre avec correspondante des paramètres facultatifs, les arguments par défaut de la déclaration de membre de fonction sont implicitement passés. Car il s’agit toujours constante, leur évaluation n’affecte pas l’ordre d’évaluation des autres arguments.

### <a name="type-inference"></a>Inférence de type

Lorsqu’une méthode générique est appelée sans spécifier les arguments de type, un ***l’inférence de type*** processus tente de déduire les arguments de type pour l’appel. La présence de l’inférence de type permet une syntaxe plus pratique à utiliser pour appeler une méthode générique et permet au programmeur d’éviter de spécifier des informations de type redondant. Par exemple, étant donné la déclaration de méthode :
```csharp
class Chooser
{
    static Random rand = new Random();

    public static T Choose<T>(T first, T second) {
        return (rand.Next(2) == 0)? first: second;
    }
}
```
Il est possible d’appeler le `Choose` méthode sans spécifier explicitement un argument de type :
```csharp
int i = Chooser.Choose(5, 213);                 // Calls Choose<int>

string s = Chooser.Choose("foo", "bar");        // Calls Choose<string>
```

Via l’inférence de type, les arguments de type `int` et `string` sont déterminées à partir des arguments à la méthode.

Inférence de type se produit dans le cadre du traitement du moment de la liaison d’un appel de méthode ([appels de méthode](expressions.md#method-invocations)) et intervient avant l’étape de résolution de surcharge de l’appel. Quand un groupe de méthode particulière est spécifié dans un appel de méthode et sans arguments de type sont spécifiés dans le cadre de l’appel de méthode, l’inférence de type est appliquée à chaque méthode générique, dans le groupe de méthodes. Si l’inférence de type réussit, les arguments de type déduits sont utilisés pour déterminer les types d’arguments pour la résolution de surcharge suivantes. Si la résolution de surcharge choisit une méthode générique que celui à appeler, les arguments de type déduits sont utilisés comme arguments de type réel pour l’appel. Cas d’échec de l’inférence de type pour une méthode particulière, cette méthode ne participe pas la résolution de surcharge. L’échec de l’inférence de type elle-même, n’entraîne pas une erreur au moment de la liaison. Toutefois, il conduit souvent à une erreur de liaison lors de la résolution de surcharge puis ne trouve pas de toutes les méthodes applicables.

Si le nombre d’arguments fournis est différent du nombre de paramètres dans la méthode, l’inférence échoue immédiatement. Sinon, on suppose que la méthode générique a la signature suivante :
```csharp
Tr M<X1,...,Xn>(T1 x1, ..., Tm xm)
```

Avec un appel de méthode sous la forme `M(E1...Em)` la tâche d’inférence de type consiste à trouver les arguments de type unique `S1...Sn` pour chacun des paramètres de type `X1...Xn` afin que l’appel `M<S1...Sn>(E1...Em)` devient valide.

Pendant le processus d’inférence de chaque paramètre de type `Xi` est soit *fixe* à un type particulier `Si` ou *non fixed* avec un ensemble associé de *limites*. Chacune des limites est un type `T`. Chaque variable de type initialement `Xi` est corrigé avec un ensemble de limites vide.

Inférence de type a lieu en plusieurs phases. Chaque phase essaiera de déduire les arguments de type pour les variables de type supplémentaires en fonction des résultats de la phase précédente. La première phase rend certains des inférences initiales des limites, tandis que la deuxième phase résout les variables de type pour des types spécifiques et déduit davantage de limites. La deuxième phase peut-être être répétée un nombre de fois.

*Remarque :* Inférence type intervient non seulement quand une méthode générique est appelée. Inférence de type pour la conversion de groupes de méthodes est décrite dans [inférence pour la conversion de groupes de méthodes de Type](expressions.md#type-inference-for-conversion-of-method-groups) et recherche le meilleur type courantes d’un ensemble d’expressions est décrite dans [recherche le meilleur type courantes d’un jeu des expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions).

#### <a name="the-first-phase"></a>La première phase

Pour chacun des arguments de la méthode `Ei`:

*   Si `Ei` est une fonction anonyme, un *inférence de type de paramètre explicite* ([des inférences de type de paramètre explicite](expressions.md#explicit-parameter-type-inferences)) est effectuée à partir de `Ei` à `Ti`
*   Sinon, si `Ei` a un type `U` et `xi` est un paramètre de valeur un *inférence de limite inférieure* est effectuée *de* `U` *à* `Ti`.
*   Sinon, si `Ei` a un type `U` et `xi` est un `ref` ou `out` paramètre une *exacte inférence* est effectuée *à partir de* `U` *à* `Ti`.
*   Sinon, aucune inférence est effectuée pour cet argument.


#### <a name="the-second-phase"></a>La deuxième phase

La deuxième phase se déroule comme suit :

*   Tous les *non fixed* variables de type `Xi` qui n’est pas le cas *dépendent* ([dépendance](expressions.md#dependence)) n’importe quel `Xj` sont fixes ([fixation](expressions.md#fixing)).
*   Si aucune variable de type ce type n’existe, tous les *non fixed* variables de type `Xi` sont *fixe* pour lequel toutes les conditions suivantes contiennent :
    *   Il existe au moins une variable de type `Xj` qui dépend de `Xi`
    *   `Xi` possède un ensemble non vide de limites
*   Si aucun ces variables de type n’existent et sont toujours *non fixed* variables de type, l’échec de l’inférence de type.
*   Sinon, si aucune autre *non fixed* les variables de type existent, l’inférence de type réussit.
*   Sinon, pour tous les arguments `Ei` avec le type de paramètre correspondant `Ti` où le *types de sortie* ([types de sortie](expressions.md#output-types)) contiennent *non fixed* variables de type `Xj` mais le *de types d’entrées* ([de types d’entrées](expressions.md#input-types)) ne le faites pas, un *l’inférence de type de sortie* ([des inférences de type de sortie ](expressions.md#output-type-inferences)) est effectuée *de* `Ei` *à* `Ti`. Puis la deuxième phase est répétée.

#### <a name="input-types"></a>Types d’entrée

Si `E` est un groupe de méthodes ou implicitement typé fonction anonyme et `T` est un délégué de type ou de type arborescence d’expression puis de tous les types de paramètres de `T` sont *de types d’entrées* de `E` *avec type* `T`.

####  <a name="output-types"></a>Types de sortie

Si `E` est un groupe de méthodes ou une fonction anonyme et `T` est un délégué de type ou de type arborescence d’expression puis le type de retour de `T` est un *type de sortie* `E` *avec type*  `T`.

#### <a name="dependence"></a>Dépendance

Un *non fixed* variable de type `Xi` *dépend directement de* une variable de type non fixed `Xj` if pour certains arguments `Ek` avec type `Tk` `Xj` se produit dans un *type d’entrée* de `Ek` avec type `Tk` et `Xi` se produit dans un *type de sortie* de `Ek` avec type `Tk`.

`Xj` *dépend de* `Xi` si `Xj` *dépend directement de* `Xi` ou si `Xi` *dépend directement de* `Xk` et `Xk` *dépend* `Xj`. Par conséquent, « dépend » est la fermeture transitive mais pas réflexive de « dépend directement ».

#### <a name="output-type-inferences"></a>Inférences de type de sortie

Un *l’inférence de type de sortie* est effectuée *de* une expression `E` *à* un type `T` de la façon suivante :

*  Si `E` est une fonction anonyme avec le type de retour déduit `U` ([type de retour déduit](expressions.md#inferred-return-type)) et `T` est un type délégué ou type arborescence d’expression avec un type de retour `Tb`, puis un *inférence de limite inférieure* ([des inférences de limite inférieure](expressions.md#lower-bound-inferences)) est effectuée *de* `U` *à* `Tb`.
*  Sinon, si `E` est un groupe de méthodes et `T` est un type délégué ou l’expression type d’arborescence avec les types de paramètres `T1...Tk` et type de retour `Tb`et la résolution de surcharge des `E` avec les types `T1...Tk` génère un méthode avec le type de retour unique `U`, puis un *inférence de limite inférieure* est effectuée *à partir de* `U` *à* `Tb`.
*  Sinon, si `E` est une expression avec type `U`, puis un *inférence de limite inférieure* est effectuée *à partir de* `U` *à* `T`.
*  Sinon, aucune des inférences ne sont effectuées.

#### <a name="explicit-parameter-type-inferences"></a>Inférences de type de paramètre explicite

Un *inférence de type de paramètre explicite* est effectuée *de* une expression `E` *à* un type `T` de la façon suivante :

*  Si `E` est une fonction anonyme explicitement typée avec les types de paramètres `U1...Uk` et `T` est un type délégué ou l’expression type d’arborescence avec les types de paramètres `V1...Vk` puis pour chaque `Ui` un *exacte inférence* ([exacte des inférences](expressions.md#exact-inferences)) est effectuée *de* `Ui` *à* correspondant `Vi`.

#### <a name="exact-inferences"></a>Inférences exactes

Un *exacte inférence* *à partir de* un type `U` *à* un type `V` est effectué comme suit :

*  Si `V` est un de la *non fixed* `Xi` puis `U` est ajouté à l’ensemble des limites exactes pour `Xi`.

*  Sinon, définit `V1...Vk` et `U1...Uk` sont déterminées en vérifiant si un des cas suivants s’applique :

   *  `V` est un type tableau `V1[...]` et `U` est un type tableau `U1[...]` du même rang
   *  `V` est le type `V1?` et `U` est le type `U1?`
   *  `V` est un type construit `C<V1...Vk>`et `U` est un type construit `C<U1...Uk>`

   Si un de ces cas s’applique ensuite une *exacte inférence* est effectuée *à partir de* chaque `Ui` *à* correspondant `Vi`.

*  Sinon, aucune des inférences ne sont effectuées.

#### <a name="lower-bound-inferences"></a>Limite inférieure inférences

A *inférence de limite inférieure* *de* un type `U` *à* un type `V` est effectué comme suit :

*  Si `V` fait partie de la *non fixed* `Xi` puis `U` est ajouté à l’ensemble des limites inférieures `Xi`.
*  Sinon, si `V` est le type `V1?`et `U` est le type `U1?` une inférence de limite inférieure est effectuée à partir de `U1` à `V1`.
*  Sinon, définit `U1...Uk` et `V1...Vk` sont déterminées en vérifiant si un des cas suivants s’applique :
   *  `V` est un type tableau `V1[...]` et `U` est un type tableau `U1[...]` (ou un paramètre de type dont le type de base efficace est `U1[...]`) du même rang
   *  `V` est un des `IEnumerable<V1>`, `ICollection<V1>` ou `IList<V1>` et `U` est un tableau unidimensionnel de type `U1[]`(ou un paramètre de type dont le type de base efficace est `U1[]`)
   *  `V` est un type classe, struct, interface ou un délégué construit `C<V1...Vk>` et il existe un type unique `C<U1...Uk>` telles que `U` (ou, si `U` est un paramètre de type, sa classe de base efficace ou n’importe quel membre de son set interface efficace) est identique à, hérite (directement ou indirectement), ou implémente (directement ou indirectement) `C<U1...Uk>`.

      (La restriction « unicité » signifie que dans l’interface cas `C<T> {} class U: C<X>, C<Y> {}`, aucune inférence est effectuée lors de l’inférence de `U` à `C<T>` car `U1` peut être `X` ou `Y`.)

   Si un de ces cas s’applique une inférence est effectuée *de* chaque `Ui` *à* correspondant `Vi` comme suit :

   *  Si `Ui` n’est pas connu pour être un type référence une *exacte inférence* est effectuée
   *  Sinon, si `U` est un type tableau un *limite inférieure inférence* est effectuée
   *  Sinon, si `V` est `C<V1...Vk>` puis inférence varie selon le paramètre de type i-ème `C`:
      *  S’il est covariant un *limite inférieure inférence* est effectuée.
      *  S’il est contravariant une *supérieures inférence* est effectuée.
      *  Si elle est invariante une *exacte inférence* est effectuée.
*  Sinon, aucune des inférences ne sont effectuées.

#### <a name="upper-bound-inferences"></a>Inférences de limite supérieure

Un *inférence de limite supérieure* *de* un type `U` *à* un type `V` est effectué comme suit :

*  Si `V` fait partie de la *non fixed* `Xi` puis `U` est ajouté à l’ensemble des limites supérieures pour `Xi`.
*  Sinon, définit `V1...Vk` et `U1...Uk` sont déterminées en vérifiant si un des cas suivants s’applique :
   *  `U` est un type tableau `U1[...]` et `V` est un type tableau `V1[...]` du même rang
   *  `U` est un des `IEnumerable<Ue>`, `ICollection<Ue>` ou `IList<Ue>` et `V` est un tableau unidimensionnel de type `Ve[]`
   *  `U` est le type `U1?` et `V` est le type `V1?`
   *  `U` est la classe construite, struct, interface ou un type délégué `C<U1...Uk>` et `V` est une classe, struct, interface ou un type délégué qui est identique à hérite (directement ou indirectement) ou implémente (directement ou indirectement) un type unique `C<V1...Vk>`

      (La restriction « unicité » signifie que si nous avons `interface C<T>{} class V<Z>: C<X<Z>>, C<Y<Z>>{}`, aucune inférence est effectuée lors de l’inférence de `C<U1>` à `V<Q>`. Des inférences ne sont pas effectuées à partir de `U1` soit `X<Q>` ou `Y<Q>`.)

   Si un de ces cas s’applique une inférence est effectuée *de* chaque `Ui` *à* correspondant `Vi` comme suit :
   *  Si `Ui` n’est pas connu pour être un type référence une *exacte inférence* est effectuée
   *  Sinon, si `V` est un type tableau, une *supérieures inférence* est effectuée
   *  Sinon, si `U` est `C<U1...Uk>` puis inférence varie selon le paramètre de type i-ème `C`:
      *  S’il est covariant une *supérieures inférence* est effectuée.
      *  S’il est contravariant un *limite inférieure inférence* est effectuée.
      *  Si elle est invariante une *exacte inférence* est effectuée.
*  Sinon, aucune des inférences ne sont effectuées.   

#### <a name="fixing"></a>Résolution

Un *non fixed* variable de type `Xi` avec un ensemble de limites est *fixe* comme suit :

*  L’ensemble de *types candidats* `Uj` démarre en tant que l’ensemble de tous les types dans l’ensemble de limites pour `Xi`.
*  Nous examinons ensuite chaque limite pour `Xi` à son tour : Pour chaque limite exacte `U` de `Xi` tous les types `Uj` qui ne sont pas identique à `U` sont supprimés de l’ensemble de candidats. Pour chaque limite inférieure `U` de `Xi` tous les types `Uj` pour lequel il est *pas* une conversion implicite de `U` sont supprimés de l’ensemble de candidats. Pour chaque limite supérieure `U` de `Xi` tous les types `Uj` à partir de laquelle est *pas* une conversion implicite vers `U` sont supprimés de l’ensemble de candidats.
*  If parmi les types de candidats restants `Uj` est un type unique `V` à partir de laquelle il existe une conversion implicite pour tous les autres types candidats, puis `Xi` est fixé à `V`.
*  Sinon, l’inférence de type échoue.

#### <a name="inferred-return-type"></a>Type de retour déduit

Type d’une fonction anonyme de retour le déduit `F` est utilisé lors de la résolution de surcharge et de l’inférence de type. Le type de retour déduit peut uniquement être déterminé pour une fonction anonyme, où le paramètre tous les types sont connus, soit, car ils sont fournis explicitement, fournies via une conversion de la fonction anonyme ou déduit lors de l’inférence de type sur une englobante est générique appel de méthode.

Le ***déduit le type de résultat*** est déterminée comme suit :

*  Si le corps de `F` est un *expression* qui a un type, alors que le type de résultat déduit de `F` est le type de cette expression.
*  Si le corps de `F` est un *bloc* et l’ensemble d’expressions dans le bloc `return` instructions a un type commun meilleures `T` ([recherche le meilleur type courantes d’un ensemble d’expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)), puis le type de résultat déduit de `F` est `T`.
*  Sinon, un type de résultat ne peut pas être déduit pour `F`.

Le ***déduit le type de retour*** est déterminée comme suit :

*  Si `F` est asynchrone et le corps de `F` est soit une expression classée nothing ([classifications des expressions](expressions.md#expression-classifications)), ou un bloc d’instructions où aucune instruction de retour n’ont des expressions, le déduit type de retour est `System.Threading.Tasks.Task`
*  Si `F` est asynchrone et a un type de résultat déduit `T`, le déduit type de retour est `System.Threading.Tasks.Task<T>`.
*  Si `F` est non asynchrone et a un type de résultat déduit `T`, le déduit type de retour est `T`.
*  Sinon, un type de retour ne peut pas être déduit pour `F`.

Prenons un exemple d’inférence de type impliquant des fonctions anonymes, le `Select` méthode d’extension déclarée dans la `System.Linq.Enumerable` classe :
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

En supposant que le `System.Linq` espace de noms a été importé avec un `using` clause et une classe `Customer` avec un `Name` propriété de type `string`, le `Select` méthode peut être utilisée pour sélectionner les noms d’une liste de clients :
```csharp
List<Customer> customers = GetCustomerList();
IEnumerable<string> names = customers.Select(c => c.Name);
```

L’appel de méthode d’extension ([appels de méthode d’Extension](expressions.md#extension-method-invocations)) de `Select` est traité par la réécriture de l’appel à un appel de méthode statique :
```csharp
IEnumerable<string> names = Enumerable.Select(customers, c => c.Name);
```

Dans la mesure où les arguments de type n’étaient pas explicitement spécifiés, l’inférence de type est utilisé pour déduire les arguments de type. Tout d’abord, le `customers` argument est lié à la `source` paramètre, déduction `T` être `Customer`. Ensuite, à l’aide de la fonction anonyme tapez des processus d’inférence décrit ci-dessus, `c` est de type donné `Customer`et l’expression `c.Name` concerne le type de retour de la `selector` paramètre, déduction `S` être `string`. Par conséquent, l’appel est équivalent à
```csharp
Sequence.Select<Customer,string>(customers, (Customer c) => c.Name)
```
et le résultat est de type `IEnumerable<string>`.

L’exemple suivant montre comment anonyme type fonction inférence permet des informations de type « flux » entre les arguments dans un appel de méthode générique. Étant donné la méthode :
```csharp
static Z F<X,Y,Z>(X value, Func<X,Y> f1, Func<Y,Z> f2) {
    return f2(f1(value));
}
```

Inférence de type pour l’appel :
```csharp
double seconds = F("1:15:30", s => TimeSpan.Parse(s), t => t.TotalSeconds);
```
procède comme suit : Tout d’abord, l’argument `"1:15:30"` est liée à la `value` paramètre, déduction `X` être `string`. Ensuite, le paramètre de la première fonction anonyme, `s`, reçoit le type déduit `string`et l’expression `TimeSpan.Parse(s)` concerne le type de retour de `f1`, inférence `Y` être `System.TimeSpan`. Enfin, le paramètre de la deuxième fonction anonyme, `t`, reçoit le type déduit `System.TimeSpan`et l’expression `t.TotalSeconds` concerne le type de retour de `f2`, inférence `Z` être `double`. Par conséquent, le résultat de l’appel est de type `double`.

#### <a name="type-inference-for-conversion-of-method-groups"></a>Inférence de type pour la conversion de groupes de méthodes

Comme pour les appels de méthodes génériques, inférence de type doit également être appliquée lorsqu’un groupe de méthodes `M` contenant une méthode générique est converti en un type délégué donné `D` ([conversions de groupes de méthode](conversions.md#method-group-conversions)). Soit une méthode
```csharp
Tr M<X1...Xn>(T1 x1 ... Tm xm)
```
et le groupe de méthodes `M` assignée au type délégué `D` la tâche d’inférence de type consiste à trouver les arguments de type `S1...Sn` afin que l’expression :
```csharp
M<S1...Sn>
```
devient compatible ([déclarations Delegate](delegates.md#delegate-declarations)) avec `D`.

Contrairement à l’algorithme d’inférence de type pour les appels de méthode générique, dans ce cas il existe uniquement argument *types*, aucun argument *expressions*. En particulier, il n’existe aucune fonction anonyme et donc pas besoin de plusieurs phases d’inférence.

Au lieu de cela, tous les `Xi` sont considérés comme *non fixed*et un *inférence de limite inférieure* est effectuée *de* chaque type d’argument `Uj` de `D` *à* le type de paramètre correspondant `Tj` de `M`. If pour toutes les `Xi` pas de limites ont été trouvés, l’inférence de type échoue. Sinon, tous les `Xi` sont *fixe* correspondant `Si`, qui sont le résultat de l’inférence de type.

#### <a name="finding-the-best-common-type-of-a-set-of-expressions"></a>Recherche le meilleur type courantes d’un ensemble d’expressions

Dans certains cas, un type commun doit être déduite pour un ensemble d’expressions. En particulier, les types d’éléments des tableaux implicitement typés et les types de retour de fonctions anonymes avec *bloc* corps sont trouvent de cette façon.

Intuitivement, étant donné un ensemble d’expressions `E1...Em` cette inférence doit être équivalente à l’appel d’une méthode
```csharp
Tr M<X>(X x1 ... X xm)
```
avec la `Ei` en tant qu’arguments.

Plus précisément, l’inférence démarre avec un *non fixed* variable de type `X`. *Sortie des inférences de type* sont ensuite effectuées *à partir de* chaque `Ei` *à* `X`. Enfin, `X` est *fixe* et, en cas de réussite, le résultat type `S` est le meilleur type commun qui en résulte pour les expressions. Si aucun `S` existe, les expressions n’ont aucun meilleur type commun.

### <a name="overload-resolution"></a>Résolution de surcharge

Résolution de surcharge est un mécanisme de liaison-heure pour sélectionner la meilleure fonction membre à appeler étant donnés une liste d’arguments et un ensemble de fonctions membres candidates. Résolution de surcharge sélectionne la fonction membre à appeler dans les différents contextes au sein de c# suivants :

*  Appel d’une méthode nommée dans une *invocation_expression* ([appels de méthode](expressions.md#method-invocations)).
*  Appel d’un constructeur d’instance nommé dans un *object_creation_expression* ([des expressions de la création d’objet](expressions.md#object-creation-expressions)).
*  Appel d’un accesseur d’indexeur via un *element_access* ([accès à un élément](expressions.md#element-access)).
*  Appel d’un opérateur prédéfini ou définies par l’utilisateur référencé dans une expression ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution) et [opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)).

Chacun de ces contextes définit l’ensemble des fonctions membres candidates et la liste d’arguments dans sa propre méthode unique, comme décrit en détail dans les sections ci-dessus. Par exemple, l’ensemble de candidats pour un appel de méthode n’inclut pas les méthodes marquées `override` ([recherche de membres](expressions.md#member-lookup)), et les méthodes dans une classe de base ne sont pas des candidats si n’importe quelle méthode dans une classe dérivée est applicable ([ Appels de méthode](expressions.md#method-invocations)).

Une fois que les fonctions membres candidates et la liste d’arguments ont été identifiés, la sélection de la meilleure fonction membre est la même dans tous les cas :

*  Compte tenu du jeu de membres de fonctions candidats applicables, la meilleure fonction membre dans la mesure ensemble se trouve. Si le jeu ne contient qu’un seul membre de fonction, cette fonction membre est la meilleure fonction membre. Sinon, la meilleure fonction membre est le membre d’une fonction qui est mieux que tous les autres membres de fonction en ce qui concerne la liste d’arguments donné, à condition que chaque fonction membre est comparée à toutes les autres fonctions membres en utilisant les règles dans [ Meilleur membre de fonction](expressions.md#better-function-member). S’il n’est pas exactement une seule fonction membre qui est meilleure que toutes les autres fonctions membres, puis l’appel de fonction membre est ambigu et une erreur au moment de la liaison se produit.

Les sections suivantes définissent la signification exacte des termes du contrat ***membre de fonction applicable*** et ***meilleur membre de fonction***.

#### <a name="applicable-function-member"></a>Membre de fonction applicable

Une fonction membre est dite un ***membre de fonction applicable*** par rapport à une liste d’arguments `A` lorsque toutes les conditions suivantes sont remplies :

*  Chaque argument dans `A` correspond à un paramètre dans la déclaration de membre de fonction, comme décrit dans [les paramètres correspondants](expressions.md#corresponding-parameters), et n’importe quel paramètre auquel ne correspond aucun argument est un paramètre facultatif.
*  Pour chaque argument dans `A`, le paramètre en passant du mode de l’argument (par exemple, la valeur, `ref`, ou `out`) est identique au mode de transmission du paramètre correspondant, et
   *  pour un paramètre de valeur ou un tableau de paramètres, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir de l’argument vers le type du paramètre correspondant, ou
   *  pour un `ref` ou `out` paramètre, le type de l’argument est identique au type du paramètre correspondant. Après tout, un `ref` ou `out` paramètre est un alias pour l’argument passé.

Pour une fonction membre qui inclut un tableau de paramètres, si la fonction membre est applicable par les règles ci-dessus, elle est dite applicable dans son ***forme normale***. Si une fonction membre qui inclut un tableau de paramètres n’est pas applicable dans sa forme normale, la fonction membre peut alors être applicable dans son ***développé formulaire***:

*  La forme étendue est construite en remplaçant le tableau de paramètres dans la déclaration de fonction membre par zéro ou plusieurs paramètres de valeur du type d’élément du paramètre de tableau telles que le nombre d’arguments dans la liste d’arguments `A` correspond au total nombre de paramètres. Si `A` a moins d’arguments que le nombre de paramètres fixes dans la déclaration de fonction membre, la forme étendue de la fonction membre ne peut pas être construite et par conséquent, n’est pas applicable.
*  Sinon, la forme étendue est applicable si, pour chaque argument dans `A` le mode de passage de paramètre de l’argument est identique au mode de transmission du paramètre correspondant, et
   *  pour un paramètre de valeur fixe ou un paramètre de valeur créé par l’expansion, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir du type de l’argument vers le type du paramètre correspondant, ou
   *  pour un `ref` ou `out` paramètre, le type de l’argument est identique au type du paramètre correspondant.

#### <a name="better-function-member"></a>Meilleur membre de fonction

Dans le cadre de déterminer le meilleur membre de fonction, une liste d’arguments épurée A est construite, contenant uniquement les expressions d’arguments eux-mêmes dans l’ordre de qu'apparition dans la liste d’arguments d’origine.

Listes des paramètres pour chacun de la fonction candidate membres sont construits de la façon suivante :

*  La forme étendue est utilisée si la fonction membre est applicable uniquement dans la forme étendue.
*  Paramètres facultatifs avec aucun argument correspondant sont supprimés de la liste de paramètres
*  Les paramètres sont réorganisés afin qu’elles se produisent à la même position que l’argument correspondant dans la liste d’arguments.

Étant donné une liste d’arguments `A` avec un ensemble d’expressions d’arguments `{E1, E2, ..., En}` et deux membres de fonction applicable `Mp` et `Mq` avec les types de paramètres `{P1, P2, ..., Pn}` et `{Q1, Q2, ..., Qn}`, `Mp` est défini comme étant un ***meilleur membre de fonction*** à `Mq` si

*  pour chaque argument, la conversion implicite de `Ex` à `Qx` n’est pas meilleur que la conversion implicite de `Ex` à `Px`, et
*  au moins un argument, la conversion de `Ex` à `Px` est meilleure que la conversion de `Ex` à `Qx`.

Lorsque vous effectuez cette évaluation, si `Mp` ou `Mq` est applicable dans sa forme étendue, puis `Px` ou `Qx` fait référence à un paramètre dans la forme étendue de la liste de paramètres.

Dans le cas où le type de paramètre séquences `{P1, P2, ..., Pn}` et `{Q1, Q2, ..., Qn}` sont équivalentes (autrement dit, chaque `Pi` a une conversion d’identité correspondant `Qi`), les règles de saut de lien suivantes sont appliquées, dans l’ordre, pour déterminer la meilleure fonction membre.

*  Si `Mp` est une méthode non générique et `Mq` est une méthode générique, puis `Mp` est meilleure que `Mq`.
*  Sinon, si `Mp` est applicable dans sa forme normale et `Mq` a un `params` de tableau et est applicable uniquement dans sa forme étendue, puis `Mp` est meilleure que `Mq`.
*  Sinon, si `Mp` a plusieurs déclarés de paramètres que `Mq`, puis `Mp` est meilleure que `Mq`. Cela peut se produire si les deux méthodes ont `params` tableaux et sont applicables uniquement dans leurs formes développées.
*  Sinon, si tous les paramètres de `Mp` ont un argument correspondant, tandis que les arguments par défaut doivent être substitué au moins un paramètre facultatif dans `Mq` puis `Mp` est meilleure que `Mq`.
*  Sinon, si `Mp` a des types de paramètres plus spécifiques que `Mq`, puis `Mp` est meilleure que `Mq`. Laisser `{R1, R2, ..., Rn}` et `{S1, S2, ..., Sn}` représentent les types de paramètre non instancié et non développées de `Mp` et `Mq`. `Mp`de types de paramètres sont plus précises que `Mq`d’if, pour chaque paramètre, `Rx` n’est pas moins spécifiques que `Sx`et, pour au moins un paramètre, `Rx` est plus spécifique que `Sx`:
   *  Un paramètre de type est moins spécifique à un paramètre sans type.
   *  Récursive, un type construit est plus spécifique à un autre type construit (avec le même nombre d’arguments de type) si au moins un argument de type est plus spécifique et aucun argument de type n’est moins spécifique que l’argument de type correspondant dans l’autre.
   *  Un type tableau est plus spécifique qu’un autre type de tableau (avec le même nombre de dimensions) si le type d’élément de la première est plus spécifique que le type d’élément de la deuxième.
*  Sinon, si un membre est un opérateur non levé et l’autre est un opérateur levé, non levé celui qui est préférable.
*  Sinon, aucune fonction membre est préférable.

#### <a name="better-conversion-from-expression"></a>Meilleure conversion à partir de l’expression

Étant donné une conversion implicite `C1` qui convertit à partir d’une expression `E` à un type `T1`et une conversion implicite `C2` qui convertit à partir d’une expression `E` à un type `T2`, `C1` est un ***une meilleure conversion*** à `C2` si `E` ne correspond pas exactement à `T2` et au moins une des opérations suivantes conserve :

* `E` correspond exactement à `T1` ([correspondant exactement à Expression](expressions.md#exactly-matching-expression))
* `T1` est une cible de conversion mieux que `T2` ([mieux cibler conversion](expressions.md#better-conversion-target))

#### <a name="exactly-matching-expression"></a>Expression de correspondance exacte

Étant donné une expression `E` et un type `T`, `E` correspond exactement à `T` si dont l’un des éléments suivants :

*  `E` a un type `S`, et il existe une conversion d’identité à partir de `S` à `T`
*  `E` est une fonction anonyme, `T` est soit un type de délégué `D` ou un type d’arborescence expression `Expression<D>` et dont l’un des éléments suivants :
   *  Un type de retour déduit `X` existe pour `E` dans le contexte de la liste des paramètres `D` ([type de retour déduit](expressions.md#inferred-return-type)), et il existe une conversion d’identité à partir de `X` pour le type de retour `D`
   *  Soit `E` est non asynchrone et `D` a un type de retour `Y` ou `E` est asynchrone et `D` a un type de retour `Task<Y>`, et dont l’un des éléments suivants :
      * Le corps de `E` est une expression correspondant exactement à `Y`
      * Le corps de `E` est un bloc d’instructions où chaque instruction return renvoie une expression correspondant exactement à `Y`

#### <a name="better-conversion-target"></a>Mieux cibler de conversion

Étant donné deux types différents `T1` et `T2`, `T1` est une cible de conversion mieux que `T2` si aucune conversion implicite de `T2` à `T1` existe, et au moins une des opérations suivantes conserve :

*  Une conversion implicite de `T1` à `T2` existe
*  `T1` est soit un type de délégué `D1` ou un type d’arborescence expression `Expression<D1>`, `T2` est soit un type de délégué `D2` ou un type d’arborescence expression `Expression<D2>`, `D1` a un type de retour `S1` et l’une du blocages suivantes :
   * `D2` void retourne
   * `D2` a un type de retour `S2`, et `S1` est une cible de conversion mieux que `S2`
*  `T1` est `Task<S1>`, `T2` est `Task<S2>`, et `S1` est une cible de conversion mieux que `S2`
*  `T1` est `S1` ou `S1?` où `S1` est un type intégral signé, et `T2` est `S2` ou `S2?` où `S2` est un type intégral non signé. Plus précisément :
   * `S1` est `sbyte` et `S2` est `byte`, `ushort`, `uint`, ou `ulong`
   * `S1` est `short` et `S2` est `ushort`, `uint`, ou `ulong`
   * `S1` est `int` et `S2` est `uint`, ou `ulong`
   * `S1` est `long` et `S2` est `ulong`

#### <a name="overloading-in-generic-classes"></a>La surcharge dans les classes génériques

Tandis que les signatures tel que déclaré doivent être uniques, il est possible que la substitution des arguments de type génère des signatures identiques. Dans ce cas, les règles de saut de lien de résolution de surcharge ci-dessus sélectionnera le membre plus spécifique.

Les exemples suivants montrent des surcharges qui sont valides et non valides en fonction de cette règle :

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

### <a name="compile-time-checking-of-dynamic-overload-resolution"></a>La vérification de la résolution de surcharge dynamique lors de la compilation

Pour les opérations dépendantes plus dynamiquement l’ensemble de candidats possibles pour la résolution est inconnu au moment de la compilation. Dans certains cas, toutefois l’ensemble de candidats est connu au moment de la compilation :

*  Appels de méthode statique avec des arguments dynamiques
*  Appels de méthode d’instance où le destinataire n’est pas une expression dynamique
*  Appels d’indexeur où le destinataire n’est pas une expression dynamique
*  Appels de constructeur avec des arguments dynamiques

Dans ce cas, une vérification limitée de la compilation est effectuée pour chaque candidat voir si un d’eux peut éventuellement s’appliquent au moment de l’exécution. Cette vérification se compose des étapes suivantes :

*  Inférence de type partiel : Tout type d’argument qui ne dépende pas directement ou indirectement un argument de type `dynamic` est déduit à l’aide des règles de [l’inférence de Type](expressions.md#type-inference). Les autres arguments de type sont inconnues.
*  Vérification de la mise en application partielle : Mise en application est vérifiée en fonction de [membre de fonction Applicable](expressions.md#applicable-function-member), mais en ignorant les paramètres dont les types sont inconnus.
*  Si aucun candidat ne réussit ce test, une erreur de compilation se produit.

### <a name="function-member-invocation"></a>Appel de fonction membre

Cette section décrit le processus qui a lieu au moment de l’exécution pour appeler une fonction membre particulière. Il est supposé qu’un processus du moment de la liaison a déjà déterminé le membre particulier à appeler, éventuellement en appliquant résolution de surcharge à un ensemble de fonctions membres candidates.

Pour les besoins de description du processus d’appel, les fonctions membres sont divisées en deux catégories :

*  Les fonctions membres static. Il s’agit de constructeurs d’instance, les méthodes statiques, les accesseurs de propriété statique et les opérateurs définis par l’utilisateur. Les fonctions membres static sont toujours non virtuelle.
*  Fonctions membres d’instance. Il s’agit de méthodes d’instance, les accesseurs de propriété d’instance et accesseurs des indexeurs. Fonctions membres d’instance sont non virtuel ou virtuel et sont toujours appelées sur une instance particulière. L’instance est calculée par une expression d’instance, et il devienne accessible au sein de la fonction membre en tant que `this` ([cet accès](expressions.md#this-access)).

Le traitement de l’exécution d’un appel de fonction membre se compose des étapes suivantes, où `M` est la fonction membre et, si `M` est un membre d’instance, `E` est l’expression d’instance :

*  Si `M` est une fonction membre statique :
   * La liste d’arguments est évaluée comme décrit dans [listes d’arguments](expressions.md#argument-lists).
   * `M` est appelé.

*  Si `M` est une fonction membre d’instance déclarée dans un *value_type*:
   * `E` est évalué. Si cette version d’évaluation provoque une exception, aucune étape supplémentaire n’est exécutées.
   * Si `E` n’est pas classé comme une variable, puis une variable locale temporaire du `E`du type est créé et la valeur de `E` est affecté à cette variable. `E` est ensuite reclassé en tant que référence à cette variable locale temporaire. La variable temporaire est accessible en tant que `this` dans `M`, mais pas de quelque autre manière. Par conséquent, uniquement lorsque `E` est une véritable variable est-il permet à l’appelant afin d’observer les modifications qui `M` apporte à `this`.
   * La liste d’arguments est évaluée comme décrit dans [listes d’arguments](expressions.md#argument-lists).
   * `M` est appelé. La variable référencée par `E` devient la variable référencée par `this`.

*  Si `M` est une fonction membre d’instance déclarée dans un *reference_type*:
   * `E` est évalué. Si cette version d’évaluation provoque une exception, aucune étape supplémentaire n’est exécutées.
   * La liste d’arguments est évaluée comme décrit dans [listes d’arguments](expressions.md#argument-lists).
   * Si le type de `E` est un *value_type*, une conversion boxing ([conversions Boxing](types.md#boxing-conversions)) est effectuée pour convertir `E` à taper `object`, et `E` est considéré comme pour être de type `object` dans les étapes suivantes. Dans ce cas, `M` peut uniquement appartenir à de `System.Object`.
   * La valeur de `E` est vérifié pour être valide. Si la valeur de `E` est `null`, un `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.
   * L’implémentation de fonction membre à appeler est déterminée :
     * Si le type de la liaison et l’heure de `E` est une interface, la fonction membre à appeler est l’implémentation de `M` fournies par le type au moment de l’exécution de l’instance référencée par `E`. Cette fonction membre est déterminée en appliquant les règles de mappage d’interface ([mappage d’Interface](interfaces.md#interface-mapping)) pour déterminer l’implémentation de `M` fournies par le type au moment de l’exécution de l’instance référencée par `E`.
     * Sinon, si `M` est une fonction membre virtuelle, la fonction membre à appeler est l’implémentation de `M` fournies par le type au moment de l’exécution de l’instance référencée par `E`. Cette fonction membre est déterminée en appliquant les règles pour déterminer l’implémentation la plus dérivée ([les méthodes virtuelles](classes.md#virtual-methods)) de `M` en ce qui concerne le type au moment de l’exécution de l’instance référencée par `E`.
     * Sinon, `M` est une fonction membre non virtuelle, et la fonction membre à appeler est `M` lui-même.
   * L’implémentation de fonction membre déterminée à l’étape précédente est appelée. L’objet référencé par `E` devient l’objet référencé par `this`.

#### <a name="invocations-on-boxed-instances"></a>Appels sur les instances

Une fonction membre implémenté dans un *value_type* peut être appelée via une instance boxed qui *value_type* dans les situations suivantes :

*  Lorsque la fonction membre est un `override` d’une méthode héritée de type `object` et est appelée via une expression d’instance de type `object`.
*  Lorsque la fonction membre est une implémentation d’une fonction membre d’interface et est appelé via une expression d’instance d’un *interface_type*.
*  Lorsque la fonction membre est appelé via un délégué.

Dans ces situations, l’instance boxed est considérée comme contenant une variable de la *value_type*, et cette variable devient la variable référencée par `this` au sein de l’appel de fonction membre. En particulier, cela signifie que lorsqu’une fonction membre est appelée sur une instance boxed, il est possible pour le membre de fonction Modifier la valeur contenue dans l’instance boxed.

## <a name="primary-expressions"></a>Expressions primaires

Expressions primaires incluent les formes les plus simples d’expressions.

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

Expressions primaires sont réparties entre *array_creation_expression*s et *primary_no_array_creation_expression*s. Traitement d’expression de création de tableau de cette façon, au lieu de répertorier, ainsi que d’autres formes d’expression simple, permet à la grammaire interdire pouvant prêter à confusion code tel que
```csharp
object o = new int[3][1];
```
ce qui pourrait être interprété en tant que
```csharp
object o = (new int[3])[1];
```

### <a name="literals"></a>Littéraux

Un *primary_expression* qui se compose d’un *littéral* ([littéraux](lexical-structure.md#literals)) est classé en tant que valeur.


### <a name="interpolated-strings"></a>Chaînes interpolées

Un *interpolated_string_expression* se compose d’un `$` connexion suivies d’une chaîne régulier ou textuelle literal, où les trous, délimitées par `{` et `}`, placez les expressions et mise en forme spécifications. Une expression de chaîne interpolée est le résultat d’une *interpolated_string_literal* qui a été divisé en jetons individuels, comme décrit dans [interpolées littéraux de chaîne](lexical-structure.md#interpolated-string-literals).

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

Le *constant_expression* une interpolation doit avoir une conversion implicite vers `int`.

Un *interpolated_string_expression* est classé en tant que valeur. Si elle est convertie immédiatement en `System.IFormattable` ou `System.FormattableString` avec une conversion implicite de chaîne interpolée ([implicite interpolées conversions de chaînes](conversions.md#implicit-interpolated-string-conversions)), l’expression de chaîne interpolée est du même type. Sinon, elle a le type `string`.

Si le type d’une chaîne interpolée est `System.IFormattable` ou `System.FormattableString`, la signification est un appel à `System.Runtime.CompilerServices.FormattableStringFactory.Create`. Si le type est `string`, la signification de l’expression est un appel à `string.Format`. Dans les deux cas, la liste d’arguments de l’appel se compose d’une chaîne de format littérale avec des espaces réservés pour chaque interpolation et un argument pour chaque expression correspondant pour les espaces réservés.

Le littéral de chaîne de format est construit comme suit, où `N` est le nombre d’interpolations dans le *interpolated_string_expression*:

*  Si un *interpolated_regular_string_whole* ou un *interpolated_verbatim_string_whole* suit le `$` signer, puis le littéral de chaîne de format est ce jeton.
*  Sinon, le littéral de chaîne de format se compose de : 
   *  Première le *interpolated_regular_string_start* ou *interpolated_verbatim_string_start*
   *  Puis pour chaque numéro `I` de `0` à `N-1`: 
      * Représentation décimale de `I`
      * Puis, si le correspondant *interpolation* a un *constant_expression*, un `,` (virgule), suivie par la représentation décimale de la valeur de la *constant_expression*
      * Le *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_mid* ou *interpolated_ verbatim_string_end* qui suit immédiatement l’interpolation correspondante.

Les arguments suivants sont tout simplement le *expressions* à partir de la *interpolations* (le cas échéant), dans l’ordre.

TODO : exemples.


### <a name="simple-names"></a>Noms simples

Un *simple_name* se compose d’un identificateur, éventuellement suivi d’une liste d’arguments de type :

```antlr
simple_name
    : identifier type_argument_list?
    ;
```

Un *simple_name* est soit sous la forme `I` ou sous la forme `I<A1,...,Ak>`, où `I` est un identificateur unique et `<A1,...,Ak>` est facultative *type_argument_list*. En cas de non *type_argument_list* est spécifié, envisagez `K` à zéro. Le *simple_name* est évalué et classé comme suit :

*  Si `K` est égal à zéro et le *simple_name* apparaît dans un *bloc* et si le *bloc*du (ou une englobante *bloc*du) local espace de déclaration de variable ([déclarations](basic-concepts.md#declarations)) contient une variable locale, paramètre ou une constante avec le nom `I`, puis le *simple_name* fait référence à cette variable locale, paramètre ou une constante et est considéré comme une variable ou une valeur.
*  Si `K` est égal à zéro et le *simple_name* s’affiche dans le corps d’une déclaration de méthode générique et si cette déclaration inclut un paramètre de type avec nom `I`, puis le *simple_name*fait référence à ce paramètre de type.
*  Sinon, pour chaque type d’instance `T` ([le type d’instance](classes.md#the-instance-type)), en commençant par le type d’instance de la déclaration de type immédiatement englobante et en continuant avec le type d’instance de chaque classe ou struct englobant déclaration (le cas échéant) :
   *  Si `K` est zéro et la déclaration de `T` inclut un paramètre de type avec nom `I`, puis le *simple_name* fait référence à ce paramètre de type.
   *  Sinon, si une recherche de membres ([recherche de membres](expressions.md#member-lookup)) de `I` dans `T` avec `K`  arguments de type génère une correspondance :
      * Si `T` est le type d’instance du type classe ou struct englobant immédiatement et la recherche indique qu’une ou plusieurs méthodes, le résultat est un groupe de méthodes avec une expression d’instance associée de `this`. Si une liste d’arguments de type a été spécifiée, il est utilisé dans l’appel d’une méthode générique ([appels de méthode](expressions.md#method-invocations)).
      * Sinon, si `T` est le type d’instance du type classe ou struct immédiatement englobant, si la recherche identifie un membre d’instance, et si la référence se produit dans le corps d’un constructeur d’instance, une méthode d’instance ou un accesseur d’instance, le Il en résulte le même qu’un accès de membre ([l’accès au membre](expressions.md#member-access)) sous la forme `this.I`. Cela peut uniquement se produire lorsque `K` est égal à zéro.
      * Sinon, le résultat est identique à un accès au membre ([l’accès au membre](expressions.md#member-access)) sous la forme `T.I` ou `T.I<A1,...,Ak>`. Dans ce cas, il est une erreur au moment de la liaison pour le *simple_name* pour faire référence à un membre d’instance.

*  Sinon, pour chaque espace de noms `N`, en commençant par l’espace de noms dans lequel le *simple_name* se produit, en continuant avec chaque espace de noms (le cas échéant) englobant et se terminant par l’espace de noms global, les étapes suivantes sont évaluée jusqu'à ce qu’une entité se trouve :
   *  Si `K` est égal à zéro et `I` est le nom d’un espace de noms `N`, puis :
      * Si l’emplacement où le *simple_name* se produit est délimitée par une déclaration d’espace de noms pour `N` et la déclaration d’espace de noms contient un *extern_alias_directive* ou  *using_alias_directive* qui associe le nom `I` avec un espace de noms ou un type, puis le *simple_name* est ambigu et une erreur de compilation se produit.
      * Sinon, le *simple_name* fait référence à l’espace de noms nommé `I` dans `N`.
   *  Sinon, si `N` contient un nom de type accessible `I` et `K`  paramètres de type, puis :
      * Si `K` est égal à zéro et l’emplacement où le *simple_name* se produit est délimitée par une déclaration d’espace de noms pour `N` et la déclaration d’espace de noms contient un *extern_alias_directive*ou *using_alias_directive* qui associe le nom `I` avec un espace de noms ou un type, puis le *simple_name* est ambigu et une erreur de compilation se produit.
      * Sinon, le *namespace_or_type_name* fait référence au type construit avec les arguments de type donné.
   *  Sinon, si l’emplacement où le *simple_name* se produit est délimitée par une déclaration d’espace de noms pour `N`:
      * Si `K` est égal à zéro et la déclaration d’espace de noms contient un *extern_alias_directive* ou *using_alias_directive* qui associe le nom `I` avec un espace de noms importé ou type, puis le *simple_name* fait référence à cet espace de noms ou un type.
      * Sinon, si les déclarations d’espaces de noms et le type importé par le *using_namespace_directive*s et *using_static_directive*s de la déclaration d’espace de noms contiennent exactement un type accessible ou membre statique sans extension ayant le nom `I` et `K`  paramètres de type, puis le *simple_name* fait référence à ce type ou membre construit avec les arguments de type donné.
      * Sinon, si les espaces de noms et les types importés par le *using_namespace_directive*s de la déclaration d’espace de noms contiennent plus d’un type accessible ou non--méthode d’extension de membre statique ayant le nom `I` et `K`  paramètres de type, puis le *simple_name* est ambigu et une erreur se produit.

   Notez que cette étape est exactement parallèlement à l’étape correspondante dans le traitement d’un *namespace_or_type_name* ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)).

*  Sinon, le *simple_name* est non défini et une erreur de compilation se produit.


### <a name="parenthesized-expressions"></a>Expressions entre parenthèses

Un *parenthesized_expression* se compose d’un *expression* placés entre parenthèses.

```antlr
parenthesized_expression
    : '(' expression ')'
    ;
```

Un *parenthesized_expression* est évaluée en évaluant le *expression* entre parenthèses. Si le *expression* entre les parenthèses qui désigne un espace de noms ou un type, une erreur de compilation se produit. Sinon, le résultat de la *parenthesized_expression* est le résultat de l’évaluation de la relation contenant-contenu *expression*.

### <a name="member-access"></a>Accès au membre

Un *member_access* se compose d’un *primary_expression*, un *predefined_type*, ou un *qualified_alias_member*, suivi par un»`.`« jeton, suivi par un *identificateur*, suivie éventuellement un *type_argument_list*.

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

Le *qualified_alias_member* production est définie dans [qualificateurs d’alias Namespace](namespaces.md#namespace-alias-qualifiers).

Un *member_access* est soit sous la forme `E.I` ou sous la forme `E.I<A1, ..., Ak>`, où `E` est une expression primaire, `I` est un identificateur unique et `<A1, ..., Ak>` est facultatif  *type_argument_list*. En cas de non *type_argument_list* est spécifié, envisagez `K` à zéro.

Un *member_access* avec un *primary_expression* de type `dynamic` lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas le compilateur classifie l’accès au membre en tant qu’un accès à la propriété de type `dynamic`. Les règles ci-dessous pour déterminer la signification de la *member_access* sont ensuite appliquées au moment de l’exécution, à l’aide du type au moment de l’exécution au lieu du type de compilation de la *primary_expression*. Si cette classification exécution mène à un groupe de méthodes, l’accès au membre doit être le *primary_expression* d’un *invocation_expression*.

Le *member_access* est évalué et classé comme suit :

*  Si `K` est égal à zéro et `E` est un espace de noms et `E` contient un espace de noms imbriqué avec le nom `I`, le résultat est de cet espace de noms.
*  Sinon, si `E` est un espace de noms et `E` contient un nom de type accessible `I` et `K`  paramètres de type, le résultat est ce type construit avec les arguments de type donné.
*  Si `E` est un *predefined_type* ou un *primary_expression* classés en tant que type, si `E` n’est pas un paramètre de type et si une recherche de membres ([recherche de membres](expressions.md#member-lookup)) de `I` dans `E` avec `K`  paramètres de type génère une correspondance, puis `E.I` est évalué et classé comme suit :
   *  Si `I` identifie un type, le résultat est ce type construit avec les arguments de type donné.
   *  Si `I` identifie une ou plusieurs méthodes, le résultat est un groupe de méthodes sans expression d’instance associée. Si une liste d’arguments de type a été spécifiée, il est utilisé dans l’appel d’une méthode générique ([appels de méthode](expressions.md#method-invocations)).
   *  Si `I` identifie un `static` propriété, le résultat est un accès à la propriété sans expression d’instance associée.
   *  Si `I` identifie un `static` champ :
      * Si le champ est `readonly` et la référence se produit en dehors du constructeur statique de la classe ou structure dans laquelle le champ est déclaré, le résultat est une valeur, à savoir la valeur du champ static `I` dans `E`.
      * Sinon, le résultat est une variable, à savoir le champ statique `I` dans `E`.
   *  Si `I` identifie un `static` événement :
      * Si la référence se produit au sein de la classe ou structure dans laquelle l’événement est déclaré, et l’événement a été déclaré sans *event_accessor_declarations* ([événements](classes.md#events)), puis `E.I` est traitée exactement comme si `I` ont un champ statique.
      * Sinon, le résultat est un accès à l’événement sans expression d’instance associée.
   *  Si `I` identifie une constante, le résultat est une valeur, à savoir la valeur de cette constante.
    * Si `I` identifie un membre d’énumération, le résultat est une valeur, à savoir la valeur de ce membre d’énumération.
    * Sinon, `E.I` est une référence de membre non valide et une erreur de compilation se produit.
*  Si `E` est un accès à la propriété, un accès à un indexeur, une variable ou une valeur, dont le type est `T`et une recherche de membres ([recherche de membres](expressions.md#member-lookup)) de `I` dans `T` avec `K`  arguments de type génère une correspondance, puis `E.I` est évalué et classé comme suit :
   *  Tout d’abord, if `E` est une propriété ou indexeur, la valeur de la propriété ou l’accès à l’indexeur est obtenue ([valeurs des expressions](expressions.md#values-of-expressions)) et `E` est reclassé en tant que valeur.
   *  Si `I` identifie une ou plusieurs méthodes, le résultat est un groupe de méthodes avec une expression d’instance associée de `E`. Si une liste d’arguments de type a été spécifiée, il est utilisé dans l’appel d’une méthode générique ([appels de méthode](expressions.md#method-invocations)).
   *  Si `I` identifie une propriété d’instance,
      * Si `E` est `this`, `I` identifie une propriété implémentée automatiquement ([implémenté automatiquement les propriétés](classes.md#automatically-implemented-properties)) sans un accesseur Set et la référence se produit au sein d’un constructeur d’instance pour un type de classe ou struct `T`, le résultat est une variable, à savoir le champ de stockage masqué pour la propriété automatique fournie par `I` dans l’instance de `T` donné par `this`.
      * Sinon, le résultat est un accès à la propriété avec une expression d’instance associée de `E`.
   *  Si `T` est un *class_type* et `I` identifie un champ d’instance de qui *class_type*:
      * Si la valeur de `E` est `null`, puis un `System.NullReferenceException` est levée.
      * Sinon, si le champ est `readonly` et la référence se produit en dehors d’un constructeur d’instance de la classe dans laquelle le champ est déclaré, le résultat est une valeur, à savoir la valeur du champ `I` dans l’objet référencé par `E`.
      * Sinon, le résultat est une variable, à savoir le champ `I` dans l’objet référencé par `E`.
   *  Si `T` est un *struct_type* et `I` identifie un champ d’instance de qui *struct_type*:
      * Si `E` est une valeur, ou si le champ est `readonly` et la référence se produit en dehors d’un constructeur d’instance du struct dans lequel le champ est déclaré, le résultat est une valeur, à savoir la valeur du champ `I` dans l’instance de struct donnée par  `E`.
      * Sinon, le résultat est une variable, à savoir le champ `I` dans l’instance de struct donnée par `E`.
   *  Si `I` identifie un événement d’instance :
      * Si la référence se produit au sein de la classe ou structure dans laquelle l’événement est déclaré, et l’événement a été déclaré sans *event_accessor_declarations* ([événements](classes.md#events)), et la référence ne se produit pas en tant que le côté gauche d’un `+=` ou `-=` opérateur, puis `E.I` est traitée exactement comme si `I` était un champ d’instance.
      * Sinon, le résultat est un accès à l’événement avec une expression d’instance associée de `E`.
*  Sinon, une tentative est effectuée pour traiter les `E.I` comme un appel de méthode d’extension ([appels de méthode d’Extension](expressions.md#extension-method-invocations)). En cas d’échec, `E.I` est une référence de membre non valide, et se produit une erreur au moment de la liaison.

#### <a name="identical-simple-names-and-type-names"></a>Des noms identiques simples et les noms de types

Dans un accès de membre de la forme `E.I`, si `E` est un identificateur unique et si la signification de `E` comme un *simple_name* ([noms simples](expressions.md#simple-names)) est une constante, le champ, la propriété, variable locale ou un paramètre avec le même type que la signification de `E` comme un *type_name* ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)), puis les deux significations possibles de `E` sont autorisé. Les deux significations possibles de `E.I` ne sont jamais ambiguës, depuis `I` doit être nécessairement un membre du type `E` dans les deux cas. En d’autres termes, la règle permet simplement d’accéder aux membres statiques et les types imbriqués de `E` où une erreur de compilation se serait produite. Exemple :
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

#### <a name="grammar-ambiguities"></a>Ambiguïtés au niveau de grammaire

Les productions pour *simple_name* ([noms simples](expressions.md#simple-names)) et *member_access* ([l’accès au membre](expressions.md#member-access)) peut donner lieu à des ambiguïtés dans le Grammaire des expressions. Par exemple, l’instruction :
```
F(G<A,B>(7));
```
peut être interprétée comme un appel à `F` avec deux arguments, `G < A` et `B > (7)`. Sinon, elle peut être interprétée comme un appel à `F` avec un seul argument, qui est un appel à une méthode générique `G` avec deux arguments de type et un seul argument régulière.

Si une séquence de jetons peut être analysée (dans le contexte) comme un *simple_name* ([noms simples](expressions.md#simple-names)), *member_access* ([l’accès au membre](expressions.md#member-access)), ou *pointer_member_access* ([accès des membres pointeurs](unsafe-code.md#pointer-member-access)) se terminant par un *type_argument_list* ([arguments de Type](types.md#type-arguments)), le jeton immédiatement après la fermeture `>` jeton est examiné. S’il s’agit d’un des
```csharp
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
```
le *type_argument_list* est conservé dans le cadre de la *simple_name*, *member_access* ou *pointer_member_access* et n’importe quel autres analyse possible de la séquence de jetons est ignoré. Sinon, le *type_argument_list* n’est pas considéré comme faisant partie de la *simple_name*, *member_access* ou *pointer_member_access*, même s’il n’existe aucune autre analyse possible de la séquence de jetons. Notez que ces règles ne sont pas appliquées lors de l’analyse un *type_argument_list* dans un *namespace_or_type_name* ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)). L'instruction
```csharp
F(G<A,B>(7));
```
être interprété en fonction de cette règle, comme un appel à `F` avec un seul argument, qui est un appel à une méthode générique `G` avec deux arguments de type et un seul argument régulière. Les instructions
```csharp
F(G < A, B > 7);
F(G < A, B >> 7);
```
chaque interprété comme un appel à `F` avec deux arguments. L'instruction
```csharp
x = F < A > +y;
```
sera interprété comme un opérateur inférieur à, supérieur à (opérateur) et opérateur plus unaire, comme si l’instruction avait été écrit `x = (F < A) > (+y)`, au lieu de comme un *simple_name* avec un *type_argument_list* suivi d’un opérateur plus binaire. Dans l’instruction
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

Un *invocation_expression* lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)) si au moins une des opérations suivantes conserve :

* Le *primary_expression* a le type de compilation `dynamic`.
* Au moins un argument de l’élément facultatif *argument_list* a le type de compilation `dynamic` et *primary_expression* n’a pas un type délégué.

Dans ce cas le compilateur classifie les *invocation_expression* en tant que valeur de type `dynamic`. Les règles ci-dessous pour déterminer la signification de la *invocation_expression* sont ensuite appliquées au moment de l’exécution, à l’aide du type au moment de l’exécution au lieu du type de compilation de celles de la *primary_expression* et arguments qui ont le type de compilation `dynamic`. Si le *primary_expression* n’a pas de type au moment de la compilation `dynamic`, puis l’appel de méthode fait l’objet d’une vérification de temps de compilation limité comme décrit dans [la vérification de la résolution de surcharge dynamique lors de la compilation ](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

Le *primary_expression* d’un *invocation_expression* doit être un groupe de méthodes ou une valeur d’un *delegate_type*. Si le *primary_expression* est un groupe de méthodes, les *invocation_expression* est un appel de méthode ([appels de méthode](expressions.md#method-invocations)). Si le *primary_expression* est une valeur d’un *delegate_type*, le *invocation_expression* est un appel de délégué ([d’appelsdedélégué](expressions.md#delegate-invocations)). Si le *primary_expression* n’est ni un groupe de méthodes ni une valeur d’un *delegate_type*, se produit une erreur au moment de la liaison.

Le paramètre facultatif *argument_list* ([listes d’arguments](expressions.md#argument-lists)) fournit des valeurs ou des références variables pour les paramètres de la méthode.

Le résultat de l’évaluation d’une *invocation_expression* est classé comme suit :

*  Si le *invocation_expression* appelle une méthode ou un délégué qui retourne `void`, le résultat est nothing. Une expression est classée comme rien n’est autorisé uniquement dans le contexte d’un *statement_expression* ([instructions d’Expression](statements.md#expression-statements)) ou en tant que le corps d’un *lambda_expression*([Expressions de fonction anonyme](expressions.md#anonymous-function-expressions)). Sinon, une erreur au moment de la liaison se produit.
*  Sinon, le résultat est une valeur du type retourné par la méthode ou le délégué.

#### <a name="method-invocations"></a>Appels de méthode

Appel d’une méthode, le *primary_expression* de la *invocation_expression* doit être un groupe de méthodes. Le groupe de méthodes identifie la méthode à appeler ou l’ensemble de méthodes surchargées parmi lesquelles choisir une méthode spécifique à appeler. Dans ce cas, la détermination de la méthode spécifique à appeler est basée sur le contexte fourni par les types des arguments de la *argument_list*.

Le traitement au moment de la liaison d’un appel de méthode sous la forme `M(A)`, où `M` est un groupe de méthodes (y compris éventuellement un *type_argument_list*), et `A` est facultative *argument_ liste*, se compose des étapes suivantes :

*  L’ensemble des méthodes candidates pour l’appel de méthode est construit. Pour chaque méthode `F` associé au groupe de la méthode `M`:
   *  Si `F` n’est pas générique, `F` est un candidat lorsque :
      * `M` n’a aucune liste d’arguments de type, et
      * `F` s’applique au niveau `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)).
   *  Si `F` est générique et `M` n’a aucune liste d’arguments de type, `F` est un candidat lorsque :
      * Inférence de type ([l’inférence de Type](expressions.md#type-inference)) réussit, la déduction d’une liste d’arguments de type pour l’appel, et
      * Une fois que les arguments de type déduits sont substitués pour les paramètres de type de méthode correspondante, tous les types construits dans la liste des paramètres de F satisfont leurs contraintes ([qui satisfait aux contraintes](types.md#satisfying-constraints)) et la liste des paramètres de `F` est applicable par rapport à `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)).
   *  Si `F` est générique et `M` inclut une liste d’arguments de type, `F` est un candidat lorsque :
      * `F` a le même nombre de paramètres de type de méthode comme ont été fournies dans la liste d’arguments de type, et
      * Une fois que les arguments de type sont substitués pour les paramètres de type de méthode correspondante, tous les types construits dans la liste des paramètres de F satisfont leurs contraintes ([qui satisfait aux contraintes](types.md#satisfying-constraints)) et la liste des paramètres `F` s’applique au niveau `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)).
*  L’ensemble des méthodes candidates est réduit pour qu’il contienne uniquement les méthodes à partir des types plus dérivés : Pour chaque méthode `C.F` dans l’ensemble, où `C` est le type dans lequel la méthode `F` est déclaré, toutes les méthodes déclarées dans un type de base de `C` sont supprimés de l’ensemble. En outre, si `C` est un type de classe autre que `object`, toutes les méthodes déclarées dans un type d’interface sont supprimés de l’ensemble. (Cette règle de ce dernier n’a effet lorsque le groupe de méthodes était le résultat d’une recherche de membres sur un paramètre de type ayant une classe de base efficace autre que de l’objet et une interface efficace vide définie.)
*  Si le jeu résultant des méthodes candidates est vide, puis un traitement supplémentaire sur les étapes suivantes sont abandonnés et à la place une tentative est effectuée pour traiter l’appel comme un appel de méthode d’extension ([appels de méthode d’Extension](expressions.md#extension-method-invocations)). En cas d’échec, n’existe aucune méthode applicable et une erreur au moment de la liaison se produit.
*  La meilleure méthode de l’ensemble de méthodes de candidat est identifiée à l’aide de règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution). Si une meilleure méthode unique ne peut pas être identifiée, l’appel de méthode est ambigu et une erreur au moment de la liaison se produit. Lorsque vous effectuez la résolution de surcharge, les paramètres d’une méthode générique sont considérés comme après la substitution des arguments de type (fourni ou déduit) pour les paramètres de type de méthode correspondante.
*  La validation finale de la meilleure méthode choisie est effectuée :
   * La méthode est validée dans le contexte du groupe de méthodes : Si la meilleure méthode est une méthode statique, le groupe de méthodes doit résulter d’un *simple_name* ou un *member_access* via un type. Si la meilleure méthode est une méthode d’instance, le groupe de méthodes doit résulter d’un *simple_name*, un *member_access* via une variable ou une valeur, ou un *base_access*. Si aucune de ces conditions est true, une erreur au moment de la liaison se produit.
   * Si la meilleure méthode est une méthode générique, les arguments de type (fourni ou déduit) sont vérifiées sur les contraintes ([qui satisfait aux contraintes](types.md#satisfying-constraints)) déclaré dans la méthode générique. Si tout argument de type ne satisfait pas les contraintes correspondante sur le paramètre de type, une erreur au moment de la liaison se produit.

Une fois qu’une méthode a été sélectionnée et validée au moment de la liaison par les étapes ci-dessus, l’appel d’exécution réel est traité selon les règles de l’appel de fonction membre décrit dans [la vérification de la résolution de surcharge dynamique lors de la compilation ](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

L’effet intuitif des règles de résolution décrites ci-dessus est comme suit : Pour localiser la méthode particulière appelée par un appel de méthode, commencez avec le type indiqué par l’appel de méthode et remontez à la chaîne d’héritage jusqu'à ce que la déclaration d’au moins une méthode applicable, accessible et non-override est trouvée. Effectuer l’inférence de type et sur l’ensemble de méthodes applicables, accessibles et non-override déclaré dans ce type de la résolution de surcharge, puis appeler la méthode donc sélectionnée. Si aucune méthode n’a été trouvée, essayez plutôt traiter l’appel comme un appel de méthode d’extension.

#### <a name="extension-method-invocations"></a>Appels de méthode d’extension

Dans un appel de méthode ([appels sur les instances](expressions.md#invocations-on-boxed-instances)) d’une des formes
```csharp
expr . identifier ( )

expr . identifier ( args )

expr . identifier < typeargs > ( )

expr . identifier < typeargs > ( args )
```
Si le traitement normal de l’appel ne trouve aucune méthode applicable, une tentative est effectuée pour traiter la construction comme un appel de méthode d’extension. Si *expr* ou l’un de le *args* a le type de compilation `dynamic`, méthodes d’extension ne s’applique pas.

L’objectif est de trouver le meilleur *type_name* `C`, de sorte que l’appel de méthode statique correspondante peut avoir lieu :
```csharp
C . identifier ( expr )

C . identifier ( expr , args )

C . identifier < typeargs > ( expr )

C . identifier < typeargs > ( expr , args )
```

Une méthode d’extension `Ci.Mj` est ***éligibles*** si :

*  `Ci` est une classe non générique, non imbriquées
*  Le nom de `Mj` est *identificateur*
*  `Mj` est accessible et applicables lorsqu’il est appliqué aux arguments comme une méthode statique, comme indiqué ci-dessus
*  Il existe une référence, une conversion boxing ou un identité implicites à partir de *expr* pour le type du premier paramètre de `Mj`.

La recherche de `C` procède comme suit :

*  En commençant par la déclaration espace de noms englobant le plus proche, poursuivre chaque déclaration d’espace de noms englobant et se terminant par l’unité de compilation qui le contient, les tentatives consécutives sont apportées pour rechercher un jeu de candidat de méthodes d’extension :
   * Si l’unité de compilation ou d’espace de noms donnée contient directement des déclarations de type non générique `Ci` avec les méthodes d’extension éligibles `Mj`, puis l’ensemble de ces méthodes d’extension est l’ensemble de candidats.
   * Si les types `Ci` importé par *using_static_declarations* et sont directement déclarées dans les espaces de noms importés par *using_namespace_directive*s dans l’unité donnée de compilation ou d’espace de noms directement contient des méthodes d’extension éligibles `Mj`, puis l’ensemble de ces méthodes d’extension est l’ensemble de candidats.
*  Si aucun ensemble de candidats n’est trouvée dans n’importe quelle unité de compilation ou de la déclaration d’espace de noms englobante, une erreur de compilation se produit.
*  Sinon, la résolution de surcharge est appliquée au candidat configuré comme décrit dans ([la résolution de surcharge](expressions.md#overload-resolution)). Si aucune méthode meilleures unique n’est trouvée, une erreur de compilation se produit.
*  `C` est le type dans lequel la meilleure méthode est déclarée comme méthode d’extension.

À l’aide de `C` en tant que cible, l’appel de méthode est ensuite traité comme un appel de méthode statique ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).

Les règles précédentes signifient que les méthodes d’instance sont prioritaires sur les méthodes d’extension, que les méthodes d’extension disponibles dans les déclarations d’espace de noms interne sont prioritaires sur les méthodes d’extension disponibles dans les déclarations d’espace de noms externe et cette extension les méthodes déclarées directement dans un espace de noms sont prioritaires sur les méthodes d’extension importés dans ce même espace de noms avec un à l’aide de la directive d’espace de noms. Exemple :
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

Dans l’exemple, `B`de méthode est prioritaire sur la première méthode d’extension, et `C`de méthode est prioritaire sur les deux méthodes d’extension.

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

La sortie de cet exemple est :
```
E.F(1)
D.G(2)
C.H(3)
```
`D.G` est prioritaire sur `C.G`, et `E.F` est prioritaire sur les deux `D.F` et `C.F`.

#### <a name="delegate-invocations"></a>Appels de délégué

Pour un appel de délégué, le *primary_expression* de la *invocation_expression* doit être une valeur d’un *delegate_type*. En outre, envisagez la *delegate_type* pour être une fonction membre avec la même liste de paramètres en tant que le *delegate_type*, le *delegate_type* doit être applicable ( [Membre de fonction applicable](expressions.md#applicable-function-member)) par rapport à la *argument_list* de la *invocation_expression*.

Le traitement de l’exécution d’un appel de délégué sous la forme `D(A)`, où `D` est un *primary_expression* d’un *delegate_type* et `A` est une option *argument_list*, se compose des étapes suivantes :

*  `D` est évalué. Si cette version d’évaluation provoque une exception, aucune étape supplémentaire n’est exécutées.
*  La valeur de `D` est vérifié pour être valide. Si la valeur de `D` est `null`, un `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.
*  Sinon, `D` est une référence à une instance de délégué. Appels de membre de fonction ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) sont effectuées sur chacune des entités pouvant être appelées dans la liste d’appel du délégué. Pour les entités pouvant être appelées consistant en une instance et une méthode d’instance, l’instance pour l’appel est l’instance contenue dans l’entité pouvant être appelée.

### <a name="element-access"></a>Accès à un élément

Un *element_access* se compose d’un *primary_no_array_creation_expression*, suivie une »`[`» jeton, suivie une *argument_list*, suivi par un » `]`« token. Le *argument_list* se compose d’une ou plusieurs *argument*s, séparés par des virgules.

```antlr
element_access
    : primary_no_array_creation_expression '[' expression_list ']'
    ;
```

Le *argument_list* d’un *element_access* n’est pas autorisé à contenir `ref` ou `out` arguments.

Un *element_access* lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)) si au moins une des opérations suivantes conserve :

* Le *primary_no_array_creation_expression* a le type de compilation `dynamic`.
* Au moins une expression de la *argument_list* a le type de compilation `dynamic` et *primary_no_array_creation_expression* n’a pas un type tableau.

Dans ce cas le compilateur classifie les *element_access* en tant que valeur de type `dynamic`. Les règles ci-dessous pour déterminer la signification de la *element_access* sont ensuite appliquées au moment de l’exécution, à l’aide du type au moment de l’exécution au lieu du type de compilation de celles de la *primary_no_array_creation_expression*et *argument_list* expressions qui ont le type de compilation `dynamic`. Si le *primary_no_array_creation_expression* n’a pas de type au moment de la compilation `dynamic`, puis l’accès à un élément fait l’objet d’une vérification de temps de compilation limité comme décrit dans [vérification dynamique lors de la compilation résolution de surcharge](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

Si le *primary_no_array_creation_expression* d’un *element_access* est une valeur d’un *array_type*, le *element_access* est un tableau d’accès ([tableau accès](expressions.md#array-access)). Sinon, le *primary_no_array_creation_expression* doit être une variable ou une valeur d’une classe, un struct ou un type d’interface qui a un ou plusieurs membres d’indexeur, auquel cas la *element_access* est un accès à l’indexeur ([accès indexeur](expressions.md#indexer-access)).

#### <a name="array-access"></a>Accès aux tableaux

Pour l’accès au tableau, le *primary_no_array_creation_expression* de la *element_access* doit être une valeur d’un *array_type*. En outre, le *argument_list* d’un tableau accès n’est pas autorisé à contenir des arguments nommés. Le nombre d’expressions dans le *argument_list* doit être le même que le rang de la *array_type*, et chaque expression doit être de type `int`, `uint`, `long`, `ulong`, ou doit être implicitement convertible en un ou plusieurs de ces types.

Le résultat de l’évaluation de l’accès au tableau est une variable du type d’élément du tableau, à savoir l’élément de tableau sélectionné par les valeurs des expressions de la *argument_list*.

Le traitement de l’exécution de l’accès à un tableau sous la forme `P[A]`, où `P` est un *primary_no_array_creation_expression* d’un *array_type* et `A` est un *argument_list*, se compose des étapes suivantes :

*  `P` est évalué. Si cette version d’évaluation provoque une exception, aucune étape supplémentaire n’est exécutées.
*  Les expressions d’index de la *argument_list* sont évaluées dans l’ordre, de gauche à droite. Suite de l’évaluation de chaque expression d’index, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) à un des types suivants est effectuée : `int`, `uint`, `long`, `ulong`. Le premier type dans cette liste pour laquelle il existe une conversion implicite est choisi. Par exemple, si l’expression d’index est de type `short` ensuite une conversion implicite vers `int` est effectuée, car les conversions implicites à partir de `short` à `int` et à partir de `short` à `long` sont possibles. Si l’évaluation d’une expression d’index ou la conversion implicite suite provoque une exception, puis aucune nouvelle expression d’index n’est évaluées et aucune autre procédure est exécutée.
*  La valeur de `P` est vérifié pour être valide. Si la valeur de `P` est `null`, un `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.
*  La valeur de chaque expression contenue dans le *argument_list* est comparée aux limites réelles de chaque dimension de l’instance de tableau référencé par `P`. Si une ou plusieurs valeurs sont hors limites, un `System.IndexOutOfRangeException` est levée et aucune autre étape n’est exécutée.
*  L’emplacement de l’élément de tableau donné par les expressions d’index est calculé et cet emplacement devient le résultat de l’accès.

#### <a name="indexer-access"></a>Accès aux indexeurs

Pour l’accès à un indexeur, le *primary_no_array_creation_expression* de la *element_access* doit être une variable ou une valeur d’une classe, un struct ou un type d’interface, et ce type doit implémenter un ou plusieurs indexeurs qui sont appliquent au niveau du *argument_list* de la *element_access*.

Le traitement au moment de la liaison d’un accès d’indexeur de la forme `P[A]`, où `P` est un *primary_no_array_creation_expression* d’une classe, un struct ou un type d’interface `T`, et `A` est un *argument_list*, se compose des étapes suivantes :

*  L’ensemble des indexeurs fournis par `T` est construit. Le jeu est composé de tous les indexeurs déclarés dans `T` ou un type de base de `T` qui ne sont pas `override` déclarations et ils sont accessibles dans le contexte actuel ([l’accès au membre](basic-concepts.md#member-access)).
*  L’ensemble est réduit aux indexeurs qui sont applicables et non masqués par d’autres indexeurs. Les règles suivantes sont appliquées à chaque indexeur `S.I` dans l’ensemble, où `S` est le type dans lequel l’indexeur `I` est déclarée :
   * Si `I` n’est pas applicable au niveau `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)), puis `I` est supprimée du jeu.
   * Si `I` est applicable par rapport à `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)), puis tous les indexeurs déclarés dans un type de base de `S` sont supprimés de l’ensemble.
   * Si `I` est applicable par rapport à `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)) et `S` est un type de classe autre que `object`, tous les indexeurs déclarés dans une interface sont supprimés de l’ensemble.
*  Si le jeu résultant des indexeurs candidats est vide, n’existe aucun indexeur applicable et une erreur au moment de la liaison se produit.
*  Le meilleur indexeur de l’ensemble des indexeurs candidats est identifié à l’aide de règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution). Si un seul meilleur indexeur ne peut pas être identifié, l’accès à l’indexeur est ambigu et une erreur au moment de la liaison se produit.
*  Les expressions d’index de la *argument_list* sont évaluées dans l’ordre, de gauche à droite. Le résultat du traitement de l’accès à l’indexeur d’est une expression classifiée comme accès à un indexeur. L’expression d’accès indexeur fait référence à l’indexeur déterminé à l’étape précédente et possède une expression d’instance associée de `P` et une liste d’arguments de `A`.

En fonction du contexte dans lequel elle est utilisée, accès à un indexeur provoque l’appel d’un le *accesseur get* ou *accesseur set* de l’indexeur. Si l’accès à l’indexeur est la cible d’une assignation, le *accesseur set* est appelé pour assigner une nouvelle valeur ([assignation Simple](expressions.md#simple-assignment)). Dans tous les autres cas, le *accesseur get* est appelé pour obtenir la valeur actuelle ([valeurs des expressions](expressions.md#values-of-expressions)).

### <a name="this-access"></a>Cet accès

Un *this_access* se compose d’un mot réservé `this`.

```antlr
this_access
    : 'this'
    ;
```

Un *this_access* est autorisée uniquement dans le *bloc* d’un constructeur d’instance, une méthode d’instance ou un accesseur d’instance. Il possède une des significations suivantes :

*  Lorsque `this` est utilisée dans un *primary_expression* au sein d’un constructeur d’instance d’une classe, il est considéré comme une valeur. Le type de la valeur est le type d’instance ([le type d’instance](classes.md#the-instance-type)) de la classe dans laquelle a lieu l’utilisation et la valeur est une référence à l’objet en cours de construction.
*  Lorsque `this` est utilisée dans un *primary_expression* au sein d’une méthode d’instance ou un accesseur d’instance d’une classe, il est considéré comme une valeur. Le type de la valeur est le type d’instance ([le type d’instance](classes.md#the-instance-type)) de la classe dans laquelle a lieu l’utilisation et la valeur est une référence à l’objet pour lequel la méthode ou l’accesseur a été appelée.
*  Lorsque `this` est utilisée dans un *primary_expression* au sein d’un constructeur d’instance d’un struct, il est classé en tant que variable. Le type de la variable est le type d’instance ([le type d’instance](classes.md#the-instance-type)) du struct dans lequel a lieu l’utilisation et la variable représente la structure en cours de construction. Le `this` variable d’un constructeur d’instance d’un struct comporte exactement comme un `out` paramètre du type struct, en particulier, cela signifie que la variable doit absolument être assignée dans chaque chemin d’accès de l’exécution de l’instance constructeur.
*  Lorsque `this` est utilisée dans un *primary_expression* au sein d’une méthode d’instance ou un accesseur d’instance d’un struct, il est classé en tant que variable. Le type de la variable est le type d’instance ([le type d’instance](classes.md#the-instance-type)) du struct dans lequel se déroule l’utilisation.
   * Si la méthode ou l’accesseur n’est pas un itérateur ([itérateurs](classes.md#iterators)), la `this` variable représente le struct pour lequel la méthode ou l’accesseur a été appelée et se comporte exactement comme un `ref` paramètre du type struct.
   * Si la méthode ou l’accesseur est un itérateur, le `this` variable représente une copie du struct pour lequel la méthode ou l’accesseur a été appelée et se comporte exactement comme un paramètre de valeur du type struct.

Utilisation de `this` dans un *primary_expression* dans un contexte autre que ceux répertoriés ci-dessus est une erreur de compilation. En particulier, il n’est pas possible de faire référence à `this` dans une méthode statique, un accesseur de propriété statique, ou dans un *variable_initializer* d’une déclaration de champ.

### <a name="base-access"></a>Accès de base

Un *base_access* se compose d’un mot réservé `base` suivie par un «`.`« jeton et un identificateur ou un *argument_list* entre crochets :

```antlr
base_access
    : 'base' '.' identifier
    | 'base' '[' expression_list ']'
    ;
```

Un *base_access* est utilisé pour accéder aux membres de classe de base qui sont masqués par les membres de même nom dans la classe ou structure actuelle. Un *base_access* est autorisée uniquement dans le *bloc* d’un constructeur d’instance, une méthode d’instance ou un accesseur d’instance. Lorsque `base.I` se produit dans une classe ou un struct, `I` doit désigner un membre de la classe de base de cette classe ou un struct. De même, quand `base[E]` se produit dans une classe, un indexeur applicable doit exister dans la classe de base.

Au moment de la liaison, *base_access* expressions de la forme `base.I` et `base[E]` sont évaluées comme si elles étaient écrites `((B)this).I` et `((B)this)[E]`, où `B` est la classe de base de la classe ou un struct dans lequel la construction se produit. Par conséquent, `base.I` et `base[E]` correspondent aux `this.I` et `this[E]`, à l’exception `this` est considéré comme une instance de la classe de base.

Quand un *base_access* fait référence à une fonction membre virtuelle (méthode, propriété ou indexeur), la détermination de qui fonction membre à appeler au moment de l’exécution ([la vérification de la résolution de surcharge dynamique lors de la compilation ](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) est modifié. La fonction membre qui est appelée est déterminée en recherchant l’implémentation la plus dérivée ([les méthodes virtuelles](classes.md#virtual-methods)) de la fonction membre au niveau `B` (au lieu d’en ce qui concerne le type au moment de l’exécution de `this`, en tant que serait un accès de base autre). Par conséquent, au sein d’un `override` d’un `virtual` fonction membre, un *base_access* peut être utilisée pour appeler l’implémentation héritée de la fonction membre. Si la fonction membre référencé par un *base_access* est abstrait, une erreur au moment de la liaison se produit.

### <a name="postfix-increment-and-decrement-operators"></a>Opérateurs suffixés d’incrémentation et de décrémentation

```antlr
post_increment_expression
    : primary_expression '++'
    ;

post_decrement_expression
    : primary_expression '--'
    ;
```

L’opérande d’une incrémentation ou de décrémentation opération doit être une expression classifiée comme une variable, un accès à la propriété ou un indexeur. Le résultat de l’opération est une valeur du même type que l’opérande.

Si le *primary_expression* a le type de compilation `dynamic` l’opérateur est dynamiquement lié ([liaison dynamique](expressions.md#dynamic-binding)), la *post_increment_expression*ou *post_decrement_expression* a le type de compilation `dynamic` et les règles suivantes sont appliquées au moment de l’exécution à l’aide du type au moment de l’exécution de la *primary_expression*.

Si l’opérande d’un suffixe incrémente ou opération de décrémentation est une propriété ou indexeur, la propriété ou l’indexeur doit avoir à la fois un `get` et un `set` accesseur. Si ce n’est pas le cas, une erreur au moment de la liaison se produit.

Opérateur unaire de résolution de surcharge ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. Prédéfinies `++` et `--` opérateurs existent pour les types suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char` , `float`, `double`, `decimal`et tout type enum. Prédéfinis `++` opérateurs retournent la valeur produite en ajoutant 1 à l’opérande et sur les `--` opérateurs retournent la valeur produite en soustrayant 1 de l’opérande. Dans un `checked` contexte, si le résultat de l’addition ou la soustraction est en dehors de la plage du type du résultat et le type de résultat est un type intégral ou enum, une `System.OverflowException` est levée.

Le traitement de l’exécution d’une incrémentation ou décrémentation opération sous la forme `x++` ou `x--` se compose des étapes suivantes :

*   Si `x` est classé en tant que variable :
    * `x` est évalué pour produire la variable.
    * La valeur de `x` est enregistré.
    * L’opérateur sélectionné est appelé avec la valeur enregistrée de `x` comme argument.
    * La valeur retournée par l’opérateur est stockée dans l’emplacement indiqué par la version d’évaluation de `x`.
    * La valeur enregistrée de `x` devient le résultat de l’opération.
*   Si `x` est classé comme un accès de propriété ou l’indexeur :
    * L’expression d’instance (si `x` n’est pas `static`) et la liste d’arguments (si `x` est l’accès à un indexeur) associé `x` sont évaluées, et les résultats sont utilisés dans l’ultérieure `get` et `set` appels d’accesseur.
    * Le `get` l’accesseur de `x` est appelée et la valeur retournée est enregistrée.
    * L’opérateur sélectionné est appelé avec la valeur enregistrée de `x` comme argument.
    * Le `set` l’accesseur de `x` est appelé avec la valeur retournée par l’opérateur en tant que son `value` argument.
    * La valeur enregistrée de `x` devient le résultat de l’opération.

Le `++` et `--` opérateurs prennent également en charge la notation de préfixe ([Incrément préfixé et opérateurs de décrémentation](expressions.md#prefix-increment-and-decrement-operators)). En règle générale, le résultat de `x++` ou `x--` est la valeur de `x` avant l’opération, tandis que le résultat de `++x` ou `--x` est la valeur de `x` après l’opération. Dans les deux cas, `x` lui-même a la même valeur après l’opération.

Un `operator ++` ou `operator --` implémentation peut être appelée à l’aide de la notation de préfixe ou de suffixe. Il n’est pas possible d’avoir des implémentations d’opérateurs distincts pour les deux notations.

### <a name="the-new-operator"></a>new, opérateur

Le `new` opérateur est utilisé pour créer des instances de types.

Il existe trois formes de `new` expressions :

*  Expressions de la création d’objet sont utilisées pour créer des instances des types classe et des types valeur.
*  Expressions de création de tableau sont utilisées pour créer des instances de types de tableau.
*  Expressions de création de délégué sont utilisées pour créer des instances de délégué types.

Le `new` opérateur implique la création d’une instance d’un type, mais n’implique pas nécessairement l’allocation dynamique de mémoire. En particulier, les instances de types valeur ne requièrent aucune mémoire supplémentaire au-delà des variables dans lequel elles résident, et aucune allocation dynamique se produire lorsque `new` est utilisé pour créer des instances de types valeur.

#### <a name="object-creation-expressions"></a>Expressions de la création d’objet

Un *object_creation_expression* est utilisé pour créer une nouvelle instance d’un *class_type* ou un *value_type*.

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

Le *type* d’un *object_creation_expression* doit être un *class_type*, un *value_type* ou un *type_parameter* . Le *type* ne peut pas être un `abstract` *class_type*.

Le paramètre facultatif *argument_list* ([listes d’arguments](expressions.md#argument-lists)) est autorisée uniquement si le *type* est un *class_type* ou un *struct_ type*.

Une expression de création d’objet peut omettre la liste d’arguments de constructeur et englobante parenthèses fourni que contient un initialiseur d’objet ou un initialiseur de collection. L’omission de la liste d’arguments de constructeur et en les plaçant entre parenthèses sont équivalente à la spécification d’une liste d’arguments vide.

Traitement d’une expression de création d’objet qui inclut un initialiseur d’objet ou un initialiseur de collection se compose de traitement tout d’abord le constructeur d’instance, puis en traitant les initialisations de membre ou l’élément spécifiées par l’initialiseur d’objet ([ Initialiseurs d’objets](expressions.md#object-initializers)) ou un initialiseur de collection ([initialiseurs de Collection](expressions.md#collection-initializers)).

Si un des arguments de l’élément facultatif *argument_list* a le type de compilation `dynamic` le *object_creation_expression* lié dynamiquement ([deliaisondynamique](expressions.md#dynamic-binding)) et les règles suivantes sont appliquées au moment de l’exécution à l’aide du type au moment de l’exécution de ces arguments de la *argument_list* qui ont le type de temps de compilation `dynamic`. Toutefois, la création de l’objet fait l’objet d’une vérification de temps de compilation limité comme décrit dans [compilation la vérification de la résolution de surcharge dynamique](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

Le traitement au moment de la liaison d’un *object_creation_expression* du formulaire `new T(A)`, où `T` est un *class_type* ou un *value_type* et `A` est facultative *argument_list*, se compose des étapes suivantes :

*   Si `T` est un *value_type* et `A` n’est pas présent :
    * Le *object_creation_expression* est un appel de constructeur par défaut. Le résultat de la *object_creation_expression* est une valeur de type `T`, à savoir la valeur par défaut `T` tel que défini dans [System.ValueType le type](types.md#the-systemvaluetype-type).
*   Sinon, si `T` est un *type_parameter* et `A` n’est pas présent :
    * Si aucune contrainte de type valeur ou une contrainte de constructeur ([les contraintes de paramètre de Type](classes.md#type-parameter-constraints)) a été spécifiée pour `T`, une erreur au moment de la liaison se produit.
    * Le résultat de la *object_creation_expression* est une valeur du type d’exécution auquel le paramètre de type a été lié, à savoir le résultat de l’appel du constructeur par défaut de ce type. Le type d’exécution peut être un type référence ou un type valeur.
*   Sinon, si `T` est un *class_type* ou un *struct_type*:
    * Si `T` est un `abstract` *class_type*, une erreur de compilation se produit.
    * Le constructeur d’instance à appeler est déterminé en utilisant les règles de résolution de surcharge de [la résolution de surcharge](expressions.md#overload-resolution). L’ensemble de constructeurs d’instance candidats se compose de tous les constructeurs d’instance accessibles déclarés dans `T` qui sont applicables au niveau `A` ([membre de fonction Applicable](expressions.md#applicable-function-member)). Si l’ensemble de constructeurs d’instance candidats est vide, ou si un seul constructeur d’instance optimal ne peut pas être identifié, une erreur au moment de la liaison se produit.
    * Le résultat de la *object_creation_expression* est une valeur de type `T`, à savoir la valeur produite en appelant le constructeur d’instance déterminé à l’étape précédente.
*  Sinon, le *object_creation_expression* n’est pas valide, et se produit une erreur au moment de la liaison.

Même si le *object_creation_expression* est dynamiquement lié, le type de compilation est toujours `T`.

L’exécution du traitement d’un *object_creation_expression* du formulaire `new T(A)`, où `T` est *class_type* ou un *struct_type* et `A` est facultative *argument_list*, se compose des étapes suivantes :

*   Si `T` est un *class_type*:
    * Une nouvelle instance de la classe `T` est allouée. Si vous ne comporte pas assez de mémoire disponible pour allouer la nouvelle instance, un `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.
    * Tous les champs de la nouvelle instance sont initialisés à leurs valeurs par défaut ([valeurs par défaut](variables.md#default-values)).
    * Le constructeur d’instance est appelé conformément aux règles d’appel de fonction membre ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)). Une référence à l’instance nouvellement allouée est automatiquement passée au constructeur d’instance et l’instance est accessible à partir de ce constructeur en tant que `this`.
*   Si `T` est un *struct_type*:
    * Une instance du type `T` est créée en allouant une variable locale temporaire. Depuis un constructeur d’instance d’un *struct_type* est nécessaire à affecter définitivement une valeur pour chaque champ de l’instance en cours de création, aucune initialisation de la variable temporaire est nécessaire.
    * Le constructeur d’instance est appelé conformément aux règles d’appel de fonction membre ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)). Une référence à l’instance nouvellement allouée est automatiquement passée au constructeur d’instance et l’instance est accessible à partir de ce constructeur en tant que `this`.

#### <a name="object-initializers"></a>Initialiseurs d’objet

Un ***initialiseur d’objet*** spécifie les valeurs de zéro ou plusieurs champs, des propriétés ou des éléments indexés d’un objet.

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

Un initialiseur d’objet se compose d’une séquence d’initialiseurs de membres, encadré par `{` et `}` des jetons et séparés par des virgules. Chaque *member_initializer* désigne une cible pour l’initialisation. Un *identificateur* doit nommer un champ accessible ou une propriété de l’objet en cours d’initialisation, tandis qu’un *argument_list* placé entre crochets doit spécifier des arguments pour un indexeur accessible sur le objet en cours d’initialisation. C’est une erreur pour un initialiseur d’objet inclure plus d’un initialiseur de membre pour le même champ ou propriété.

Chaque *initializer_target* est suivi par un signe égal et une expression, un initialiseur d’objet ou un initialiseur de collection. Il n’est pas possible pour les expressions au sein de l’initialiseur d’objet pour faire référence à l’objet nouvellement créé, qu'il est en cours d’initialisation.

Un initialiseur de membre qui spécifie une expression après le signe égal est traité de la même façon qu’une affectation ([assignation Simple](expressions.md#simple-assignment)) à la cible.

Un initialiseur de membre qui spécifie un initialiseur d’objet après le signe égal est un ***initialiseur d’objet imbriqués***, c'est-à-dire une initialisation d’un objet incorporé. Au lieu d’affecter une nouvelle valeur pour le champ ou la propriété, les attributions dans l’initialiseur d’objet imbriqués sont traitées comme affectations aux membres du champ ou de propriété. Initialiseurs d’objets imbriqués ne peut pas être appliqués aux propriétés avec un type valeur, ou à des champs en lecture seule avec un type valeur.

Un initialiseur de membre qui spécifie un initialiseur de collection après le signe égal est une initialisation d’une collection incorporée. Au lieu d’affecter une nouvelle collection pour le champ cible, la propriété ou l’indexeur, les éléments contenus dans l’initialiseur sont ajoutés à la collection référencée par la cible. La cible doit être d’un type de collection qui satisfait aux exigences spécifiées dans [initialiseurs de collections](expressions.md#collection-initializers).

Les arguments à un initialiseur de l’index seront toujours évaluées une seule fois. Par conséquent, même si les arguments se retrouvent jamais utilisé (par exemple, en raison d’un initialiseur vide imbriqué), ils sont évalués pour leurs effets.

La classe suivante représente un point avec deux coordonnées :
```csharp
public class Point
{
    int x, y;

    public int X { get { return x; } set { x = value; } }
    public int Y { get { return y; } set { y = value; } }
}
```

Une instance de `Point` peut être créé et initialisé comme suit :
```csharp
Point a = new Point { X = 0, Y = 1 };
```
qui a le même effet que
```csharp
Point __a = new Point();
__a.X = 0;
__a.Y = 1; 
Point a = __a;
```
où `__a` est une variable temporaire sinon invisible et inaccessible. La classe suivante représente un rectangle créé à partir de deux points :
```csharp
public class Rectangle
{
    Point p1, p2;

    public Point P1 { get { return p1; } set { p1 = value; } }
    public Point P2 { get { return p2; } set { p2 = value; } }
}
```

Une instance de `Rectangle` peut être créé et initialisé comme suit :
```csharp
Rectangle r = new Rectangle {
    P1 = new Point { X = 0, Y = 1 },
    P2 = new Point { X = 2, Y = 3 }
};
```
qui a le même effet que
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
où `__r`, `__p1` et `__p2` sont des variables temporaires qui sont sinon invisible et inaccessible.

Si `Rectangle`du constructeur alloue les deux incorporés `Point` instances
```csharp
public class Rectangle
{
    Point p1 = new Point();
    Point p2 = new Point();

    public Point P1 { get { return p1; } }
    public Point P2 { get { return p2; } }
}
```
la construction suivante peut être utilisée pour initialiser l’embedded `Point` instances au lieu d’affecter de nouvelles instances :
```csharp
Rectangle r = new Rectangle {
    P1 = { X = 0, Y = 1 },
    P2 = { X = 2, Y = 3 }
};
```
qui a le même effet que
```csharp
Rectangle __r = new Rectangle();
__r.P1.X = 0;
__r.P1.Y = 1;
__r.P2.X = 2;
__r.P2.Y = 3;
Rectangle r = __r;
```

Étant donné une définition appropriée de C, l’exemple suivant :
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
est équivalente à cette série d’affectations :
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
où `__c`, etc., sont des variables générées sont invisibles et inaccessibles au code source. Notez que les arguments pour `[0,0]` sont évaluée qu’une seule fois et les arguments pour `[1,2]` sont évaluées qu’une seule fois, même si elles ne sont jamais utilisées.

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

Un initialiseur de collection se compose d’une séquence d’initialiseurs d’éléments, encadré par `{` et `}` des jetons et séparés par des virgules. Chaque initialiseur d’élément spécifie un élément à ajouter à la collection d’objets en cours d’initialisation et se compose d’une liste d’expressions délimitée par `{` et `}` des jetons et séparés par des virgules.  Un initialiseur d’élément de contenant une seule expression peut être écrite sans accolades, mais ne peut pas être ensuite une expression d’assignation, pour éviter toute ambiguïté avec initialiseurs de membres. Le *non_assignment_expression* production est définie dans [Expression](expressions.md#expression).

Voici un exemple d’une expression de création d’objet qui inclut un initialiseur de collection :
```csharp
List<int> digits = new List<int> { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
```

L’objet de collection à laquelle est appliqué un initialiseur de collection doit être d’un type qui implémente `System.Collections.IEnumerable` ou une erreur de compilation se produit. Pour chaque élément est spécifié dans l’ordre, l’initialiseur de collection appelle un `Add` méthode sur la cible de l’objet avec la liste d’expressions de l’initialiseur d’élément en tant que liste d’arguments, application de recherche de membres normales et pour chaque appel de la résolution de surcharge. Par conséquent, l’objet de collection doit avoir une méthode d’extension ou d’instance applicable avec le nom `Add` pour chaque initialiseur d’élément.

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

Un `List<Contact>` peut être créé et initialisé comme suit :
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
qui a le même effet que
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
où `__clist`, `__c1` et `__c2` sont des variables temporaires qui sont sinon invisible et inaccessible.

#### <a name="array-creation-expressions"></a>Expressions de création de tableau

Un *array_creation_expression* est utilisé pour créer une nouvelle instance d’un *array_type*.

```antlr
array_creation_expression
    : 'new' non_array_type '[' expression_list ']' rank_specifier* array_initializer?
    | 'new' array_type array_initializer
    | 'new' rank_specifier array_initializer
    ;
```

Une expression de création de tableau de la première forme alloue une instance de tableau du type qui résulte de la suppression de chacune des expressions individuelles à partir de la liste d’expressions. Par exemple, l’expression de création de tableau `new int[10,20]` génère une instance de tableau de type `int[,]`et l’expression de création de tableau `new int[10][,]` génère un tableau de type `int[][,]`. Chaque expression contenue dans la liste d’expressions doit être de type `int`, `uint`, `long`, ou `ulong`, ou implicitement convertible en un ou plusieurs de ces types. La valeur de chaque expression détermine la longueur de la dimension correspondante dans l’instance de tableau nouvellement allouée. Étant donné que la longueur d’une dimension de tableau doit être non négative, il est une erreur de compilation d’avoir un *constant_expression* avec une valeur négative dans la liste d’expressions.

Sauf dans un contexte unsafe ([contextes Unsafe](unsafe-code.md#unsafe-contexts)), la disposition des tableaux n’est pas spécifiée.

Si une expression de création de tableau de la première forme comprend un initialiseur de tableau, chaque expression dans la liste d’expressions doit être une constante et les longueurs de classement et de dimension spécifiés par la liste de l’expression doivent correspondre à ceux de l’initialiseur de tableau.

Dans une expression de création de tableau sous la forme deuxième ou troisième, le rang du spécificateur de type ou le rang de tableau spécifié doit correspondre à celui de l’initialiseur de tableau. Les longueurs de dimensions individuelles sont déduits à partir du nombre d’éléments dans chacun des niveaux d’imbrication correspondants de l’initialiseur de tableau. Par conséquent, l’expression
```csharp
new int[,] {{0, 1}, {2, 3}, {4, 5}}
```
correspond exactement à
```csharp
new int[3, 2] {{0, 1}, {2, 3}, {4, 5}}
```

Une expression de création de tableau de la troisième forme est appelée un ***implicitement typées expression de création de tableau***. Elle est similaire à la seconde forme, à ceci près que le type d’élément du tableau n’est explicitement indiqué, déterminé, mais le type commun mieux ([recherche le meilleur type courantes d’un ensemble d’expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)) de l’ensemble d’expressions dans le tableau initialiseur. Pour un tableau multidimensionnel, c'est-à-dire celui dans lequel le *rank_specifier* contient au moins une virgule, ce jeu comprend l’ensemble *expression*s trouvé dans imbriqués *array_initializer*s.

Les initialiseurs de tableau sont décrites dans [les initialiseurs de tableaux](arrays.md#array-initializers).

Le résultat de l’évaluation d’une expression de création de tableau est classé en tant que valeur, à savoir une référence à l’instance de tableau nouvellement allouée. Le traitement de l’exécution d’une expression de création de tableau se compose des étapes suivantes :

*  Les expressions de longueur de dimension de la *liste d’expressions* sont évaluées dans l’ordre, de gauche à droite. Suite de l’évaluation de chaque expression, une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) à un des types suivants est effectuée : `int`, `uint`, `long`, `ulong`. Le premier type dans cette liste pour laquelle il existe une conversion implicite est choisi. Si l’évaluation d’une expression ou la conversion implicite suite provoque une exception, puis aucune nouvelle expression n’est évaluées et aucune autre étape n’est exécutés.
*  Les valeurs calculées pour les longueurs de dimensions sont validées comme suit. Si un ou plusieurs des valeurs sont inférieures à zéro, un `System.OverflowException` est levée et aucune autre étape n’est exécutée.
*  Une instance de tableau avec les longueurs de dimensions donné est allouée. Si vous ne comporte pas assez de mémoire disponible pour allouer la nouvelle instance, un `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.
*  Tous les éléments de la nouvelle instance de tableau sont initialisés à leurs valeurs par défaut ([valeurs par défaut](variables.md#default-values)).
*  Si l’expression de création de tableau contient un initialiseur de tableau, chaque expression dans l’initialiseur de tableau est évaluée et assignée à l’élément de tableau correspondant. Les évaluations et les affectations sont effectuées dans l’ordre les expressions sont écrites dans l’initialiseur de tableau, en d’autres termes, les éléments sont initialisés dans l’index l’ordre croissant, avec la dimension à l’extrême droite tout d’abord l’augmentation. Si l’évaluation d’une expression donnée ou l’assignation consécutive de l’élément de tableau correspondant provoque une exception, aucun autre élément n’est initialisés (et les éléments restants ainsi auront leurs valeurs par défaut).

Une expression de création de tableau permet l’instanciation d’un tableau avec des éléments d’un type tableau, mais les éléments d’un tel tableau doivent être initialisés manuellement. Par exemple, l’instruction
```csharp
int[][] a = new int[100][];
```
Crée un tableau unidimensionnel avec 100 éléments de type `int[]`. La valeur initiale de chaque élément est `null`. Il n’est pas possible pour la même expression de création de tableau instancier également les sous-tableaux et l’instruction
```csharp
int[][] a = new int[100][5];        // Error
```
génère une erreur de compilation. Instanciation des sous-tableaux doit plutôt être effectuée manuellement, comme dans
```csharp
int[][] a = new int[100][];
for (int i = 0; i < 100; i++) a[i] = new int[5];
```

Quand un tableau de tableaux a une forme « rectangulaire », c'est-à-dire lorsque les sous-tableaux ont tous la même longueur, il est plus efficace d’utiliser un tableau multidimensionnel. Dans l’exemple ci-dessus, l’instanciation du tableau de tableaux crée 101 objets — un tableau extérieur et 100 sous-tableaux. En revanche,
```csharp
int[,] = new int[100, 5];
```
Crée un seul objet, un tableau à deux dimensions et réalise l’allocation dans une seule instruction.

Voici quelques exemples d’expressions de la création de tableau implicitement typé :
```csharp
var a = new[] { 1, 10, 100, 1000 };                       // int[]

var b = new[] { 1, 1.5, 2, 2.5 };                         // double[]

var c = new[,] { { "hello", null }, { "world", "!" } };   // string[,]

var d = new[] { 1, "one", 2, "two" };                     // Error
```

La dernière expression provoque une erreur de compilation, car ni `int` ni `string` est implicitement convertible à l’autre, puis tapez et il n’est pas mieux courants. Une expression de création de tableau explicitement typée doit être utilisée dans ce cas, par exemple en spécifiant le type doit être `object[]`. Vous pouvez également l’un des éléments pouvant être casté en un type de base commun, qui deviendrait ensuite le type d’élément déduits.

Expressions de création de tableau implicitement typé peuvent être combinées avec des initialiseurs d’objet ([expressions de création d’objet anonyme](expressions.md#anonymous-object-creation-expressions)) pour créer de manière anonyme tapé des structures de données. Exemple :
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

Un *delegate_creation_expression* est utilisé pour créer une nouvelle instance d’un *delegate_type*.

```antlr
delegate_creation_expression
    : 'new' delegate_type '(' expression ')'
    ;
```

L’argument d’une expression de création de délégué doit être un groupe de méthodes, une fonction anonyme ou une valeur du type de temps de compilation `dynamic` ou un *delegate_type*. Si l’argument est un groupe de méthodes, il identifie la méthode et, pour une méthode d’instance, l’objet pour lequel créer un délégué. Si l’argument est une fonction anonyme définit directement les paramètres et le corps de la méthode de la cible du délégué. Si l’argument est une valeur, il identifie une instance de délégué de laquelle créer une copie.

Si le *expression* a le type de compilation `dynamic`, le *delegate_creation_expression* lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)), ainsi que les règles ci-dessous sont appliquées au moment de l’exécution à l’aide du type au moment de l’exécution de la *expression*. Sinon, les règles sont appliquées au moment de la compilation.

Le traitement au moment de la liaison d’un *delegate_creation_expression* du formulaire `new D(E)`, où `D` est un *delegate_type* et `E` est un *expression* , se compose des étapes suivantes :

*  Si `E` est un groupe de méthodes, l’expression de création de délégué est traitée de la même façon en tant qu’une conversion de groupe (méthode) ([conversions de groupes de méthode](conversions.md#method-group-conversions)) à partir de `E` à `D`.
*  Si `E` est une fonction anonyme, l’expression de création de délégué est traitée de la même façon en tant qu’une conversion de la fonction anonyme ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)) à partir de `E` à `D`.
*  Si `E` est une valeur, `E` doivent être compatibles ([déclarations Delegate](delegates.md#delegate-declarations)) avec `D`, et le résultat est une référence à un nouveau délégué de type `D` qui fait référence à l’appel de la même liste en tant que `E`. Si `E` n’est pas compatible avec `D`, une erreur de compilation se produit.

L’exécution du traitement d’un *delegate_creation_expression* du formulaire `new D(E)`, où `D` est un *delegate_type* et `E` est un *expression* , se compose des étapes suivantes :

*   Si `E` est un groupe de méthodes, l’expression de création de délégué est évaluée comme une conversion de groupe (méthode) ([conversions de groupes de méthode](conversions.md#method-group-conversions)) à partir de `E` à `D`.
*   Si `E` est une fonction anonyme, la création de délégué est évaluée comme une conversion d’une fonction anonyme de `E` à `D` ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)).
*   Si `E` est une valeur d’un *delegate_type*:
    * `E` est évalué. Si cette version d’évaluation provoque une exception, aucune étape supplémentaire n’est exécutées.
    * Si la valeur de `E` est `null`, un `System.NullReferenceException` est levée et aucune autre étape n’est exécutée.
    * Une nouvelle instance du type délégué `D` est allouée. Si vous ne comporte pas assez de mémoire disponible pour allouer la nouvelle instance, un `System.OutOfMemoryException` est levée et aucune autre étape n’est exécutée.
    * La nouvelle instance de délégué est initialisée avec la même liste d’appel que l’instance de délégué fourni par `E`.

La liste d’appel d’un délégué est déterminée lorsque le délégué est instancié et reste constant pendant toute la durée de vie du délégué. En d’autres termes, il n’est pas possible de modifier les entités pouvant être appelé cible d’un délégué une fois qu’il a été créé. Lorsque deux délégués sont combinés ou un est supprimé d’un autre ([déclarations Delegate](delegates.md#delegate-declarations)), entraîne un nouveau délégué ; aucun délégué existant n’a son contenu modifié.

Il n’est pas possible de créer un délégué qui fait référence à une propriété, l’indexeur, l’opérateur défini par l’utilisateur, constructeur d’instance, destructeur ou constructeur statique.

Comme décrit ci-dessus, quand un délégué est créé à partir d’un groupe de méthodes, la liste de paramètres formels et type de retour du délégué déterminer laquelle des méthodes surchargées à sélectionner. Dans l’exemple
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
le `A.f` champ est initialisé avec un délégué qui fait référence à la seconde `Square` (méthode), car cette méthode correspond exactement à la liste de paramètres formels et le type de retour de `DoubleFunc`. Les deuxièmes `Square` méthode était pas présent, une erreur de compilation aurait été obtenu.

#### <a name="anonymous-object-creation-expressions"></a>Expressions de création d’objet anonyme

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

Un initialiseur d’objet anonyme déclare un type anonyme et retourne une instance de ce type. Un type anonyme est un type de classe sans nom qui hérite directement de `object`. Les membres d’un type anonyme sont une série de propriétés en lecture seule déduit à partir de l’initialiseur d’objet anonyme utilisé pour créer une instance du type. Plus précisément, un initialiseur d’objet anonyme du formulaire
```csharp
new { p1 = e1, p2 = e2, ..., pn = en }
```
déclare un type anonyme du formulaire
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
où chaque `Tx` est le type de l’expression correspondante `ex`. L’expression utilisée dans un *member_declarator* doit avoir un type. Par conséquent, il est une erreur de compilation pour une expression dans une *member_declarator* doit être null ou une fonction anonyme. Il est également une erreur de compilation pour l’expression avoir un type non sécurisé.

Les noms d’un type anonyme et du paramètre à son `Equals` méthode sont générées automatiquement par le compilateur et ne peut pas être référencé dans le texte de programme.

Dans le même programme, les deux initialiseurs d’objet anonymes qui spécifient une séquence de propriétés des mêmes noms et types de compilation dans le même ordre sont produisent des instances du même type anonyme.

Dans l’exemple
```csharp
var p1 = new { Name = "Lawnmower", Price = 495.00 };
var p2 = new { Name = "Shovel", Price = 26.95 };
p1 = p2;
```
l’attribution de la dernière ligne est autorisée car `p1` et `p2` sont du même type anonyme.

Le `Equals` et `GetHashcode` méthodes sur des types anonymes substituer les méthodes héritées de `object`et sont définis en termes de la `Equals` et `GetHashcode` des propriétés, afin que les deux instances du même type anonyme sont égales si et uniquement si toutes leurs propriétés sont égales.

Un déclarateur de membre peut être abrégé en un nom simple ([l’inférence de Type](expressions.md#type-inference)), un accès au membre ([la vérification de la résolution de surcharge dynamique lors de la compilation](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), un accès de base ([d’accèsdeBase](expressions.md#base-access)) ou un accès aux membres conditionnels null ([des expressions de condition Null en tant qu’initialiseurs de projection](expressions.md#null-conditional-expressions-as-projection-initializers)). Il s’agit une ***initialiseurs de projection*** et est un raccourci pour une déclaration d’et l’attribution à une propriété portant le même nom. Plus précisément, les déclarateurs de membres des formes
```csharp
identifier
expr.identifier
```
sont précisément équivalentes à ce qui suit, respectivement :
```csharp
identifier = identifier
identifier = expr.identifier
```

Par conséquent, dans un initialiseur de projection la *identificateur* sélectionne la valeur et le champ ou propriété à laquelle la valeur est affectée. Intuitivement, un initialiseur de projection projette pas seulement une valeur, mais également le nom de la valeur.

### <a name="the-typeof-operator"></a>L’opérateur typeof

Le `typeof` opérateur permet d’obtenir le `System.Type` objet pour un type.

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

La première forme de *typeof_expression* se compose d’un `typeof` mot clé suivi par un entre parenthèses *type*. Le résultat d’une expression de cette forme est la `System.Type` objet pour le type indiqué. Il existe un seul `System.Type` objet pour un type donné. Cela signifie que pour un type `T`, `typeof(T) == typeof(T)` est toujours true. Le *type* ne peut pas être `dynamic`.

La deuxième forme de *typeof_expression* se compose d’un `typeof` mot clé suivi par un entre parenthèses *unbound_type_name*. Un *unbound_type_name* est très similaire à un *type_name* ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)), sauf qu’un *unbound_type_name* contient *generic_dimension_specifier*s où une *type_name* contient *type_argument_list*s. Lorsque l’opérande d’un *typeof_expression* est une séquence de jetons qui satisfait les grammaires des deux *unbound_type_name* et *type_name*, à savoir lorsqu’il contient ni un *generic_dimension_specifier* ni un *type_argument_list*, la séquence de jetons est considéré comme un *type_name*. La signification d’un *unbound_type_name* est déterminée comme suit :

*  Convertir la séquence de jetons à un *type_name* en remplaçant chaque *generic_dimension_specifier* avec un *type_argument_list* ayant le même nombre de virgules et le mot clé `object` que chaque *type_argument*.
*  Évaluer résultant *type_name*, tout en ignorant toutes les contraintes de paramètre de type.
*  Le *unbound_type_name* correspond au type générique indépendant associé avec le type construit résultant ([liés et indépendants des types](types.md#bound-and-unbound-types)).

Le résultat de la *typeof_expression* est le `System.Type` objet résultant indépendants de type générique.

La troisième forme de *typeof_expression* se compose d’un `typeof` mot clé suivi par un entre parenthèses `void` mot clé. Le résultat d’une expression de cette forme est la `System.Type` objet qui représente l’absence d’un type. L’objet de type retourné par `typeof(void)` est distincte de l’objet de type retourné pour n’importe quel type. Cet objet de type spécial est utile dans les bibliothèques de classes qui permettent la réflexion sur des méthodes dans le langage, lorsque ces méthodes souhaitent disposer d’un moyen pour représenter le type de retour de toute méthode, y compris les méthodes void, avec une instance de `System.Type`.

Le `typeof` opérateur peut être utilisé sur un paramètre de type. Le résultat est le `System.Type` objet pour le type d’exécution qui a été lié au paramètre de type. Le `typeof` opérateur peut également être utilisé sur un type construit ou un type générique indépendant ([liés et indépendants des types](types.md#bound-and-unbound-types)). Le `System.Type` de l’objet pour un type générique indépendant n’est pas le même que le `System.Type` objet du type d’instance. Le type d’instance est toujours un type construit fermé au moment de l’exécution pour son `System.Type` dépend de l’objet sur les arguments de type au moment de l’exécution en cours d’utilisation, tandis que le type générique indépendant ne comporte aucun argument de type.

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
génère la sortie suivante :
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

Notez que `int` et `System.Int32` sont du même type.

Notez également que le résultat de `typeof(X<>)` ne dépend pas de l’argument de type, mais le résultat de `typeof(X<T>)` est.

### <a name="the-checked-and-unchecked-operators"></a>Opérateurs vérifiés et non vérifiés

Le `checked` et `unchecked` opérateurs sont utilisés pour contrôler la ***contexte de vérification de dépassement de capacité*** pour les conversions et les opérations arithmétiques de type intégral.

```antlr
checked_expression
    : 'checked' '(' expression ')'
    ;

unchecked_expression
    : 'unchecked' '(' expression ')'
    ;
```

Le `checked` opérateur évalue l’expression contenue dans un contexte vérifié et le `unchecked` opérateur évalue l’expression contenue dans un contexte non vérifié. Un *checked_expression* ou *unchecked_expression* correspond exactement à un *parenthesized_expression* ([expressions entre parenthèses](expressions.md#parenthesized-expressions)), sauf que l’expression de relation contenant-contenue est évaluée dans le contexte de contrôle de dépassement donné.

Le contexte de vérification de dépassement de capacité peut également être contrôlé via la `checked` et `unchecked` instructions ([les instructions checked et unchecked](statements.md#the-checked-and-unchecked-statements)).

Les opérations suivantes sont affectées par le dépassement de la vérification du contexte établi par le `checked` et `unchecked` opérateurs et instructions :

*  Prédéfinis `++` et `--` opérateurs unaires ([d’incrémentation et décrémentation opérateurs](expressions.md#postfix-increment-and-decrement-operators) et [Incrément préfixé et opérateurs de décrémentation](expressions.md#prefix-increment-and-decrement-operators)), lorsque l’opérande est d’un type intégral type.
*  Prédéfinis `-` opérateur unaire ([opérateur moins unaire](expressions.md#unary-minus-operator)), lorsque l’opérande est de type intégral.
*  Prédéfinis `+`, `-`, `*`, et `/` opérateurs binaires ([opérateurs arithmétiques](expressions.md#arithmetic-operators)), lorsque les deux opérandes sont des types intégraux.
*  Conversions numériques explicites ([conversions numériques explicites](conversions.md#explicit-numeric-conversions)) à partir d’un type intégral vers un autre type intégral, ou de `float` ou `double` vers un type intégral.

Lorsque l’une des opérations ci-dessus produit un résultat qui est trop grand pour représenter le type de destination, le contexte dans lequel l’opération est effectuée contrôle le comportement résultant :

*  Dans un `checked` contexte, si l’opération est une expression constante ([expressions constantes](expressions.md#constant-expressions)), une erreur de compilation se produit. Sinon, lorsque l’opération est effectuée au moment de l’exécution, un `System.OverflowException` est levée.
*  Dans un `unchecked` contexte, le résultat est tronqué en supprimant tous les bits de poids fort qui ne tiennent pas dans le type de destination.

Pour les expressions non constantes (expressions sont évaluées au moment de l’exécution) qui ne sont pas entourées de `checked` ou `unchecked` opérateurs ou instructions, le contexte de contrôle de dépassement de par défaut est `unchecked` , sauf si des facteurs externes (tels que le compilateur commutateurs et configuration de l’environnement d’exécution) appellent pour `checked` évaluation.

Pour les expressions constantes (expressions qui peuvent être complètement évaluées au moment de la compilation), le dépassement de capacité par défaut la vérification du contexte est toujours `checked`. Sauf si une expression constante est explicitement placée dans un `unchecked` contexte, les dépassements de capacité qui se produisent pendant l’évaluation de la compilation de l’expression toujours entraînent des erreurs de compilation.

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
aucune erreur de compilation n’est signalées dans la mesure où aucune des expressions pouvant être évaluées au moment de la compilation. Au moment de l’exécution, le `F` méthode lève un `System.OverflowException`et le `G` méthode retourne-727379968 (les 32 bits du résultat out-of-range). Le comportement de la `H` méthode varie selon le contexte pour la compilation de contrôle de dépassement par défaut, mais il est identique à celui `F` ou identique à `G`.

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
le dépassement de capacité qui se produire lors de l’évaluation des expressions de constante dans `F` et `H` provoquer des erreurs de compilation, car les expressions sont évaluées dans un `checked` contexte. Un dépassement de capacité se produit également lors de l’évaluation de l’expression constante dans `G`, mais étant donné que la version d’évaluation a lieu un `unchecked` contexte, le dépassement de capacité n’est pas signalée.

Le `checked` et `unchecked` opérateurs affectent uniquement le dépassement de la vérification de contexte pour les opérations qui sont contenus textuellement dans le «`(`« et »`)`« jetons. Les opérateurs n’ont aucun effet sur les fonctions membres qui sont appelés en évaluant l’expression de relation contenant-contenue. Dans l’exemple
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
l’utilisation de `checked` dans `F` n’affecte pas la version d’évaluation de `x * y` dans `Multiply`, de sorte que `x * y` est évaluée dans le contexte de contrôle de dépassement par défaut.

Le `unchecked` opérateur est pratique lorsque vous écrivez des constantes des types intégraux signés en notation hexadécimale. Exemple :
```csharp
class Test
{
    public const int AllBits = unchecked((int)0xFFFFFFFF);

    public const int HighBit = unchecked((int)0x80000000);
}
```

Les deux constantes hexadécimales ci-dessus sont de type `uint`. Étant donné que les constantes sont en dehors de la `int` de plage, sans le `unchecked` opérateur, les casts à `int` produiraient des erreurs de compilation.

Le `checked` et `unchecked` opérateurs et instructions permettent aux programmeurs de contrôler certains aspects des calculs numériques. Toutefois, le comportement de certains opérateurs numériques dépend des types de données de leurs opérandes. Par exemple, toujours la multiplication de deux décimales génère une exception de dépassement de capacité même dans une explicitement `unchecked` construire. De même, la multiplication de deux flotte jamais résultats dans une exception de dépassement de capacité même dans une explicitement `checked` construire. En outre, les autres opérateurs ne sont jamais affectés par le mode de contrôle, si la valeur par défaut ou explicite.

### <a name="default-value-expressions"></a>Expressions de valeur par défaut

Une expression de valeur par défaut est utilisée pour obtenir la valeur par défaut ([valeurs par défaut](variables.md#default-values)) d’un type. En général, une expression de valeur par défaut est utilisée pour les paramètres de type, car il ne peut pas connue si le paramètre de type est un type valeur ou un type référence. (Il n’existe aucune conversion à partir de la `null` littéral à un paramètre de type, sauf si le paramètre de type est connu pour être un type référence.)

```antlr
default_value_expression
    : 'default' '(' type ')'
    ;
```

Si le *type* dans un *default_value_expression* prend la valeur au moment de l’exécution à un type référence, le résultat est `null` convertie en ce type. Si le *type* dans un *default_value_expression* prend la valeur au moment de l’exécution à un type valeur, le résultat est le *value_type*de valeur par défaut ([par défaut constructeurs](types.md#default-constructors)).

Un *default_value_expression* est une expression constante ([expressions constantes](expressions.md#constant-expressions)) si le type est un type référence ou un paramètre de type est connu pour être un type référence ([paramètre de Type contraintes](classes.md#type-parameter-constraints)). En outre, un *default_value_expression* est une expression constante si le type est un des types de valeurs suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, ou tout type d’énumération.


### <a name="nameof-expressions"></a>Expressions Nameof

Un *nameof_expression* est utilisé pour obtenir le nom d’une entité de programme sous forme de chaîne constante.

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

Grammaticalement parlant, les *named_entity* opérande est toujours une expression. Étant donné que `nameof` n’est pas un mot clé réservé, une expression nameof est toujours syntaxiquement ambiguë dans un appel de nom simple `nameof`. Pour des raisons de compatibilité, si une recherche de nom ([noms simples](expressions.md#simple-names)) du nom `nameof` réussit, l’expression est traitée comme un *invocation_expression* --que si l’appel soit juridique. Sinon, il est un *nameof_expression*.

La signification de la *named_entity* d’un *nameof_expression* est la signification de celle-ci en tant qu’expression ; autrement dit, soit en tant qu’un *simple_name*, un *base_access*  ou un *member_access*. Toutefois, dans lequel la recherche est décrit dans [noms simples](expressions.md#simple-names) et [l’accès au membre](expressions.md#member-access) génère une erreur, car un membre d’instance a été trouvé dans un contexte statique, un *nameof_expression*ne produit aucune erreur de ce type.

Il s’agit d’une erreur lors de la compilation pour un *named_entity* désignant un groupe de méthodes pour avoir un *type_argument_list*. Il est une erreur de compilation pour un *named_entity_target* comme ayant le type `dynamic`.

Un *nameof_expression* est une expression constante de type `string`, et n’a aucun effet lors de l’exécution. Plus précisément, sa *named_entity* n’est pas évaluée et est ignoré dans le cadre de l’analyse d’assignation ([règles générales pour les expressions simples](variables.md#general-rules-for-simple-expressions)). Sa valeur est le dernier identificateur de la *named_entity* avant la dernière facultative *type_argument_list*, transformées de la façon suivante :

* Le préfixe «`@`», le cas échéant, est supprimé.
* Chaque *unicode_escape_sequence* est transformée en son caractère Unicode correspondant.
* N’importe quel *formatting_characters* sont supprimés.

Ce sont les mêmes transformations appliquées dans [identificateurs](lexical-structure.md#identifiers) lors du test d’égalité entre les identificateurs.

TODO : exemples

### <a name="anonymous-method-expressions"></a>Expressions de méthode anonyme

Un *anonymous_method_expression* est une des deux façons de définir une fonction anonyme. Celles-ci sont décrites en détail dans [expressions de fonction anonyme](expressions.md#anonymous-function-expressions).

## <a name="unary-operators"></a>Les opérateurs unaires.

Le `?`, `+`, `-`, `!`, `~`, `++`, `--`, effectuez un cast, et `await` sont appelés les opérateurs unaires.

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

Si l’opérande d’un *unary_expression* a le type de compilation `dynamic`, elle est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas de type de la compilation de la *unary_expression* est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution à l’aide du type au moment de l’exécution de l’opérande.

### <a name="null-conditional-operator"></a>Opérateur conditionnel null

L’opérateur conditionnel null s’applique à une liste des opérations à son opérande uniquement si l’opérande est non null. Sinon, le résultat de l’application de l’opérateur est `null`.

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

La liste des opérations peut inclure l’accès au membre et les opérations d’accès élément (qui peuvent se trouver conditionnels null), ainsi que les appel.

Par exemple, l’expression `a.b?[0]?.c()` est un *null_conditional_expression* avec un *primary_expression* `a.b` et *null_conditional_operations* `?[0]` (accès de l’élément de condition null), `?.c` (accès membres conditionnels null) et `()` (appel).

Pour un *null_conditional_expression* `E` avec un *primary_expression* `P`, let `E0` être l’expression obtenue en supprimant textuellement l’interligne `?`à partir de chaque le *null_conditional_operations* de `E` qui ont un. Conceptuellement, `E0` est l’expression sera évaluée si aucune des vérifications null représenté par le `?`s trouvez un `null`.

Aussi, laisser `E1` être l’expression obtenue en supprimant textuellement le caractère de début `?` de simplement le premier de la *null_conditional_operations* dans `E`. Cela peut conduire à une *expression primaire* (cas échéant simplement `?`) ou à un autre *null_conditional_expression*.

Par exemple, si `E` est l’expression `a.b?[0]?.c()`, puis `E0` est l’expression `a.b[0].c()` et `E1` est l’expression `a.b[0]?.c()`.

Si `E0` est classé comme rien, puis `E` est classé comme rien. Sinon, E est classé en tant que valeur.

`E0` et `E1` servent à déterminer la signification de `E`:

*  Si `E` se produit comme un *statement_expression* la signification de `E` est identique à l’instruction

   ```csharp
   if ((object)P != null) E1;
   ```

   à ceci près que P est évaluée une seule fois.

*  Sinon, si `E0` est classé en tant que rien ne se produit une erreur de compilation.

*  Sinon, laissez le `T0` être du type de `E0`.

   *  Si `T0` est un paramètre de type qui n’est pas connu pour être un type référence ou un type valeur non nullable, une erreur de compilation se produit.

   *  Si `T0` est un type valeur non nullable, le type de `E` est `T0?`et la signification de `E` est identique à

      ```csharp
      ((object)P == null) ? (T0?)null : E1
      ```

      à ceci près que `P` est évaluée une seule fois.

   *  Sinon, le type de E est T0, et la signification de E est identique à

      ```csharp
      ((object)P == null) ? null : E1
      ```

      à ceci près que `P` est évaluée une seule fois.

Si `E1` est lui-même un *null_conditional_expression*, puis ces règles sont appliquées à nouveau, l’imbrication les tests pour `null` jusqu'à ce qu’il y a aucune autre `?`de, et l’expression a été réduite tout en bas à l’expression primaire `E0`.

Par exemple, si l’expression `a.b?[0]?.c()` se produit comme une expression d’instruction, comme dans l’instruction :
```csharp
a.b?[0]?.c();
```
sa signification est équivalente à :
```csharp
if (a.b != null) a.b[0]?.c();
```
qui est à nouveau équivalent à :
```csharp
if (a.b != null) if (a.b[0] != null) a.b[0].c();
```
À ceci près que `a.b` et `a.b[0]` sont évaluées qu’une seule fois.

S’il se produit dans un contexte où sa valeur est utilisée, comme dans :
```csharp
var x = a.b?[0]?.c();
```
et, en supposant que le type de l’appel final n’est pas un type valeur non nullable, sa signification est équivalente à :
```csharp
var x = (a.b == null) ? null : (a.b[0] == null) ? null : a.b[0].c();
```
À ceci près que `a.b` et `a.b[0]` sont évaluées qu’une seule fois.

#### <a name="null-conditional-expressions-as-projection-initializers"></a>Expressions de condition NULL en tant qu’initialiseurs de projection

Une expression de condition null est uniquement autorisée dans un *member_declarator* dans un *anonymous_object_creation_expression* ([expressions de création d’objet anonyme](expressions.md#anonymous-object-creation-expressions)) si elle se termine avec un accès de membres (éventuellement conditionnels null). Grammaticalement, cette exigence peut être exprimée en tant que :

```antlr
null_conditional_member_access
    : primary_expression null_conditional_operations? '?' '.' identifier type_argument_list?
    | primary_expression null_conditional_operations '.' identifier type_argument_list?
    ;
```

Il s’agit d’un cas spécial de la grammaire pour *null_conditional_expression* ci-dessus. La production de *member_declarator* dans [expressions de création d’objet anonyme](expressions.md#anonymous-object-creation-expressions) puis inclut uniquement *null_conditional_member_access*.

#### <a name="null-conditional-expressions-as-statement-expressions"></a>Expressions de condition NULL en tant qu’expressions d’instruction

Une expression de condition null est uniquement autorisée dans un *statement_expression* ([instructions d’Expression](statements.md#expression-statements)) si elle se termine par un appel. Grammaticalement, cette exigence peut être exprimée en tant que :

```antlr
null_conditional_invocation_expression
    : primary_expression null_conditional_operations '(' argument_list? ')'
    ;
```

Il s’agit d’un cas spécial de la grammaire pour *null_conditional_expression* ci-dessus. La production de *statement_expression* dans [instructions d’Expression](statements.md#expression-statements) puis inclut uniquement *null_conditional_invocation_expression*.


### <a name="unary-plus-operator"></a>Opérateur plus unaire

Pour une opération du formulaire `+x`, la résolution de surcharge opérateur unaire ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. L’opérande est converti au type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur. Les opérateurs plus unaire prédéfinie sont :

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

Pour une opération du formulaire `-x`, la résolution de surcharge opérateur unaire ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. L’opérande est converti au type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur. Les opérateurs de négation prédéfinis sont :

*  Négation d’entier :

   ```csharp
   int operator -(int x);
   long operator -(long x);
   ```

   Le résultat est calculé en soustrayant `x` à partir de zéro. Si la valeur de `x` est la plus petite valeur représentable du type d’opérande (-2 ^ 31 pour `int` ou -2 ^ 63 pour `long`), puis la négation mathématique de `x` n’est pas représentable dans le type d’opérande. Si cela se produit dans un `checked` contexte, un `System.OverflowException` est levée ; si elle se produit dans un `unchecked` contexte, le résultat est la valeur de l’opérande et le dépassement de capacité n’est pas signalée.

   Si l’opérande de l’opérateur de négation est de type `uint`, il est converti en type `long`, et le type du résultat est `long`. Une exception est la règle qui autorise le `int` valeur -2147483648 (-2 ^ 31) sous la forme d’un littéral d’entier décimal ([littéraux entiers](lexical-structure.md#integer-literals)).

   Si l’opérande de l’opérateur de négation est de type `ulong`, une erreur de compilation se produit. Une exception est la règle qui autorise le `long` valeur -9223372036854775808 (-2 ^ 63) sous la forme d’un littéral d’entier décimal ([littéraux entiers](lexical-structure.md#integer-literals)).

*  Négation de virgule flottante :

   ```csharp
   float operator -(float x);
   double operator -(double x);
   ```

   Le résultat est la valeur de `x` avec son signe inversé. Si `x` est NaN, le résultat est également NaN.

*  Négation de décimal :

   ```csharp
   decimal operator -(decimal x);
   ```

   Le résultat est calculé en soustrayant `x` à partir de zéro. Négation de décimal est équivalente à l’aide de l’opérateur moins unaire du type `System.Decimal`.

### <a name="logical-negation-operator"></a>Opérateur de négation logique

Pour une opération du formulaire `!x`, la résolution de surcharge opérateur unaire ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. L’opérande est converti au type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur. Il existe qu’un seul opérateur de négation logique prédéfini :
```csharp
bool operator !(bool x);
```

Cet opérateur calcule la négation logique de l’opérande : Si l’opérande est `true`, le résultat est `false`. Si l’opérande est `false`, le résultat est `true`.

### <a name="bitwise-complement-operator"></a>Opérateur de complément de bits

Pour une opération du formulaire `~x`, la résolution de surcharge opérateur unaire ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. L’opérande est converti au type de paramètre de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur. Les opérateurs de complément de bits prédéfinis sont :
```csharp
int operator ~(int x);
uint operator ~(uint x);
long operator ~(long x);
ulong operator ~(ulong x);
```

Pour chacun de ces opérateurs, le résultat de l’opération est le complément de bits de `x`.

Chaque type d’énumération `E` implicitement fournit l’opérateur de complément de bits suivant :

```csharp
E operator ~(E x);
```

Le résultat de l’évaluation `~x`, où `x` est une expression d’un type énumération `E` avec un type sous-jacent `U`, est exactement le même que l’évaluation `(E)(~(U)x)`, sauf que la conversion en `E` est toujours effectuée comme dans le cas une `unchecked` contexte ([les opérateurs checked et unchecked](expressions.md#the-checked-and-unchecked-operators)).

### <a name="prefix-increment-and-decrement-operators"></a>Opérateurs préfixés d’incrémentation et de décrémentation

```antlr
pre_increment_expression
    : '++' unary_expression
    ;

pre_decrement_expression
    : '--' unary_expression
    ;
```

L’opérande d’une incrémentation de préfixe ou de décrémentation opération doit être une expression classifiée comme une variable, un accès à la propriété ou un indexeur. Le résultat de l’opération est une valeur du même type que l’opérande.

Si l’opérande d’un préfixe d’incrémente ou opération de décrémentation est une propriété ou indexeur, la propriété ou l’indexeur doit avoir à la fois un `get` et un `set` accesseur. Si ce n’est pas le cas, une erreur au moment de la liaison se produit.

Opérateur unaire de résolution de surcharge ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. Prédéfinies `++` et `--` opérateurs existent pour les types suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char` , `float`, `double`, `decimal`et tout type enum. Prédéfinis `++` opérateurs retournent la valeur produite en ajoutant 1 à l’opérande et sur les `--` opérateurs retournent la valeur produite en soustrayant 1 de l’opérande. Dans un `checked` contexte, si le résultat de l’addition ou la soustraction est en dehors de la plage du type du résultat et le type de résultat est un type intégral ou enum, une `System.OverflowException` est levée.

Le traitement de l’exécution d’un incrément de préfixe ou de l’opération sous la forme de décrémentation `++x` ou `--x` se compose des étapes suivantes :

*   Si `x` est classé en tant que variable :
    * `x` est évalué pour produire la variable.
    * L’opérateur sélectionné est appelé avec la valeur de `x` comme argument.
    * La valeur retournée par l’opérateur est stockée dans l’emplacement indiqué par la version d’évaluation de `x`.
    * La valeur retournée par l’opérateur devient le résultat de l’opération.
*   Si `x` est classé comme un accès de propriété ou l’indexeur :
    * L’expression d’instance (si `x` n’est pas `static`) et la liste d’arguments (si `x` est l’accès à un indexeur) associé `x` sont évaluées, et les résultats sont utilisés dans l’ultérieure `get` et `set` appels d’accesseur.
    * Le `get` l’accesseur de `x` est appelé.
    * L’opérateur sélectionné est appelé avec la valeur retournée par la `get` accesseur comme argument.
    * Le `set` l’accesseur de `x` est appelé avec la valeur retournée par l’opérateur en tant que son `value` argument.
    * La valeur retournée par l’opérateur devient le résultat de l’opération.

Le `++` et `--` opérateurs prennent également en charge postfix notation ([d’incrémentation et décrémentation opérateurs](expressions.md#postfix-increment-and-decrement-operators)). En règle générale, le résultat de `x++` ou `x--` est la valeur de `x` avant l’opération, tandis que le résultat de `++x` ou `--x` est la valeur de `x` après l’opération. Dans les deux cas, `x` lui-même a la même valeur après l’opération.

Un `operator++` ou `operator--` implémentation peut être appelée à l’aide de la notation de préfixe ou de suffixe. Il n’est pas possible d’avoir des implémentations d’opérateurs distincts pour les deux notations.

### <a name="cast-expressions"></a>Expressions de cast

Un *cast_expression* est utilisé pour convertir explicitement une expression en un type donné.

```antlr
cast_expression
    : '(' type ')' unary_expression
    ;
```

Un *cast_expression* du formulaire `(T)E`, où `T` est un *type* et `E` est un *unary_expression*, effectue une explicite conversion ([conversions explicites](conversions.md#explicit-conversions)) de la valeur de `E` à taper `T`. Si aucune conversion explicite `E` à `T`, se produit une erreur au moment de la liaison. Sinon, le résultat est la valeur produite par la conversion explicite. Le résultat est toujours classifié en tant que valeur, même si `E` désigne une variable.

La grammaire d’un *cast_expression* donne lieu à certaines ambiguïtés syntaxiques. Par exemple, l’expression `(x)-y` peut soit être interprétée comme un *cast_expression* (un cast de `-y` vers le type `x`) ou en tant qu’un *additive_expression* combiné avec un *parenthesized_expression* (qui calcule la valeur `x - y)`.

Pour résoudre *cast_expression* ambiguïtés, la règle suivante existe : Une séquence d’un ou plusieurs *jeton*s ([espace blanc](lexical-structure.md#white-space)) placé entre parenthèses est considéré comme le début d’un *cast_expression* uniquement si au moins une des opérations suivantes est remplie :

*  La séquence de jetons est la grammaire correcte pour un *type*, mais pas pour un *expression*.
*  La séquence de jetons est la grammaire correcte pour un *type*, et le jeton qui suit immédiatement la parenthèse fermante est le jeton «`~`», le jeton «`!`», le jeton «`(`», un  *identificateur* ([séquences d’échappement de caractère Unicode](lexical-structure.md#unicode-character-escape-sequences)), un *littéral* ([littéraux](lexical-structure.md#literals)), ou n’importe quel *mot clé*([Mots clés](lexical-structure.md#keywords)) à l’exception `as` et `is`.

Le terme « grammaire correcte » ci-dessus indique seulement que la séquence de jetons doit être conforme à la production grammaticale particulière. Plus précisément, il ne considère pas la signification réelle de tous les identificateurs qui le composent. Par exemple, si `x` et `y` sont des identificateurs, puis `x.y` est la grammaire correcte pour un type, même si `x.y` ne désigne en réalité un type.

À partir de la règle de suppression qui, si `x` et `y` sont des identificateurs, `(x)y`, `(x)(y)`, et `(x)(-y)` sont *cast_expression*s, mais `(x)-y` n’est pas, même si `x` identifie un type. Toutefois, si `x` est un mot clé qui identifie un type prédéfini (tel que `int`), puis les quatre formes sont *cast_expression*s (car un tel mot clé ne peut pas être une expression en lui-même).

### <a name="await-expressions"></a>Expressions await

L’opérateur await est utilisé pour suspendre l’évaluation de la fonction async englobante jusqu'à ce que l’opération asynchrone représentée par l’opérande est terminée.

```antlr
await_expression
    : 'await' unary_expression
    ;
```

Un *await_expression* est autorisée uniquement dans le corps d’une fonction asynchrone ([itérateurs](classes.md#iterators)). Dans l’englobante la plus proche fonction asynchrone, un *await_expression* ne peut pas se produire dans ces emplacements :

*  À l’intérieur d’une fonction anonyme imbriquées (non asynchrone)
*  Dans le bloc d’un *lock_statement*
*  Dans un contexte unsafe

Notez qu’un *await_expression* ne peut pas apparaître à la plupart des emplacements au sein d’un *query_expression*, car celles sont syntaxiquement transformés pour utiliser des expressions lambda non asynchrone.

À l’intérieur d’une fonction async, `await` ne peut pas être utilisé en tant qu’identificateur. Par conséquent, il n’existe aucune ambiguïté syntaxique entre diverses expressions impliquant des identificateurs et les expressions await. En dehors des fonctions asynchrones, `await` agit comme un identificateur normal.

L’opérande d’un *await_expression* est appelé le ***tâche***. Il représente une opération asynchrone qui peut être ou non complète au moment où le *await_expression* est évaluée. L’objectif de l’opérateur await est pour suspendre l’exécution de la fonction async englobante jusqu'à ce que la tâche attendue soit terminée et pour obtenir son résultat.

#### <a name="awaitable-expressions"></a>Expressions await

La tâche d’une expression await est nécessaire pour être ***awaitable***. Une expression `t` peut être attendu si dont l’un des éléments suivants :

*  `t` est de type au moment de compilation `dynamic`
*  `t` a une méthode d’extension ou d’instance accessible appelée `GetAwaiter` sans paramètres et aucun paramètre de type et un type de retour `A` pour lequel toutes les conditions suivantes contiennent :
   * `A` implémente l’interface `System.Runtime.CompilerServices.INotifyCompletion` (ci-après appelée `INotifyCompletion` par souci de concision)
   * `A` a une propriété d’instance accessible et lisible `IsCompleted` de type `bool`
   * `A` a une méthode d’instance accessible `GetResult` sans paramètres et aucun paramètre de type

L’objectif de la `GetAwaiter` méthode consiste à obtenir un ***awaiter*** pour la tâche. Le type `A` est appelé le ***type d’entité awaiter*** pour l’expression await.

L’objectif de la `IsCompleted` propriété consiste à déterminer si la tâche est déjà terminée. Dans ce cas, il n’est pas nécessaire de suspendre l’évaluation.

L’objectif de la `INotifyCompletion.OnCompleted` méthode consiste à créer une « continuation » à la tâche ; par exemple, un délégué (de type `System.Action`) qui sera appelé une fois que la tâche est terminée.

L’objectif de la `GetResult` méthode consiste à obtenir le résultat de la tâche une fois celle-ci terminée. Ce résultat peut être réussite, éventuellement avec une valeur de résultat, ou il peut être une exception qui est levée par le `GetResult` (méthode).

#### <a name="classification-of-await-expressions"></a>Classification des expressions await

L’expression `await t` est classé de la même façon que l’expression `(t).GetAwaiter().GetResult()`. Par conséquent, si le type de retour de `GetResult` est `void`, le *await_expression* est classé comme rien. S’il a un type de retour non void `T`, le *await_expression* est classé en tant que valeur de type `T`.

#### <a name="runtime-evaluation-of-await-expressions"></a>Évaluation de runtime des expressions await

Lors de l’exécution, l’expression `await t` est évaluée comme suit :

*  Un élément awaiter `a` est obtenue en évaluant l’expression `(t).GetAwaiter()`.
*  Un `bool` `b` est obtenue en évaluant l’expression `(a).IsCompleted`.
*  Si `b` est `false` puis évaluation varie selon que `a` implémente l’interface `System.Runtime.CompilerServices.ICriticalNotifyCompletion` (ci-après appelée `ICriticalNotifyCompletion` par souci de concision). Cette vérification est effectuée lors de la liaison de temps ; par exemple, lors de l’exécution si `a` a le type de temps de compilation `dynamic`et au moment de la compilation dans le cas contraire. Laisser `r` désignent le délégué de reprise ([itérateurs](classes.md#iterators)) :
    * Si `a` n’implémente pas `ICriticalNotifyCompletion`, puis l’expression `(a as (INotifyCompletion)).OnCompleted(r)` est évaluée.
    * Si `a` implémente `ICriticalNotifyCompletion`, puis l’expression `(a as (ICriticalNotifyCompletion)).UnsafeOnCompleted(r)` est évaluée.
    * Version d’évaluation est interrompue puis et contrôle est retourné à l’appelant actuel de la fonction async.
*  Soit immédiatement après (si `b` a été `true`), ou en cas d’un appel ultérieur du délégué de reprise (si `b` a été `false`), l’expression `(a).GetResult()` est évaluée. Si elle retourne une valeur, cette valeur est le résultat de la *await_expression*. Sinon, le résultat est nothing.

Implémentation d’un élément awaiter méthodes d’interface `INotifyCompletion.OnCompleted` et `ICriticalNotifyCompletion.UnsafeOnCompleted` doit provoquer le délégué `r` à appeler au maximum une fois. Sinon, le comportement de la fonction async englobante est indéfini.

## <a name="arithmetic-operators"></a>Opérateurs arithmétiques

Le `*`, `/`, `%`, `+`, et `-` sont appelés les opérateurs arithmétiques.

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

Si un opérande d’un opérateur arithmétique a le type de compilation `dynamic`, puis l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas le type de compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution à l’aide du type au moment de l’exécution de ces opérandes qui ont le type de compilation `dynamic`.

### <a name="multiplication-operator"></a>Opérateur de multiplication

Pour une opération du formulaire `x * y`, opérateur binaire de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.

Les opérateurs de multiplication prédéfinis sont répertoriés ci-dessous. Tous les opérateurs calculent le produit des `x` et `y`.

*  Multiplication d’entiers :

   ```csharp
   int operator *(int x, int y);
   uint operator *(uint x, uint y);
   long operator *(long x, long y);
   ulong operator *(ulong x, ulong y);
   ```

   Dans un `checked` contexte, si le produit est en dehors de la plage du type du résultat, un `System.OverflowException` est levée. Dans un `unchecked` contexte, dépassements de capacité ne sont pas signalés et n’importe quel bits de poids fort significatifs en dehors de la plage du type du résultat sont ignorés.


*  Multiplication à virgule flottante :

   ```csharp
   float operator *(float x, float y);
   double operator *(double x, double y);
   ```

   Le produit est calculé selon les règles de la norme IEEE 754 arithmétique. Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies, les zéros non significatifs, les valeurs infinies et NaN. Dans la table, `x` et `y` sont des valeurs finies positives. `z` est le résultat de `x * y`. Si le résultat est trop grand pour le type de destination, `z` est l’infini. Si le résultat est trop petit pour le type de destination, `z` est égal à zéro.

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

*  Multiplication de nombres décimaux :

   ```csharp
   decimal operator *(decimal x, decimal y);
   ```

   Si la valeur résultante est trop grande pour être représenté dans le `decimal` format, un `System.OverflowException` est levée. Si la valeur de résultat est trop petite pour être représenté dans le `decimal` format, le résultat est égal à zéro. La mise à l’échelle du résultat, avant tout arrondi, est la somme des échelles des deux opérandes.

   Multiplication de nombres décimaux est équivalente à l’aide de l’opérateur de multiplication du type `System.Decimal`.


### <a name="division-operator"></a>Opérateur de division

Pour une opération du formulaire `x / y`, opérateur binaire de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.

Les opérateurs de division prédéfinis sont répertoriés ci-dessous. Tous les opérateurs calculent le quotient de `x` et `y`.

*  Division d’entier :

   ```csharp
   int operator /(int x, int y);
   uint operator /(uint x, uint y);
   long operator /(long x, long y);
   ulong operator /(ulong x, ulong y);
   ```

   Si la valeur de l’opérande droite est zéro, un `System.DivideByZeroException` est levée.

   La division arrondit le résultat vers zéro. Par conséquent, la valeur absolue du résultat est le plus grand entier possible qui est inférieur ou égal à la valeur absolue du quotient de deux opérandes. Le résultat est zéro ou une valeur positive lorsque les deux opérandes ont le même signe et zéro ou négatif lorsque les deux opérandes ont des signes opposés.

   Si l’opérande gauche est le plus petit qui peut être représenté `int` ou `long` valeur et l’opérande de droite est `-1`, un dépassement de capacité se produit. Dans un `checked` contexte, cela entraîne un `System.ArithmeticException` (ou une sous-classe) devant être levé. Dans un `unchecked` contexte, il est défini par l’implémentation pour savoir si un `System.ArithmeticException` (ou une sous-classe) est levée ou le dépassement de capacité n’est pas rapporté par la valeur obtenue en cours de l’opérande gauche.

*  Division à virgule flottante :

   ```csharp
   float operator /(float x, float y);
   double operator /(double x, double y);
   ```

   Le quotient est calculé selon les règles de la norme IEEE 754 arithmétique. Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies, les zéros non significatifs, les valeurs infinies et NaN. Dans la table, `x` et `y` sont des valeurs finies positives. `z` est le résultat de `x / y`. Si le résultat est trop grand pour le type de destination, `z` est l’infini. Si le résultat est trop petit pour le type de destination, `z` est égal à zéro.

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

   Si la valeur de l’opérande droite est zéro, un `System.DivideByZeroException` est levée. Si la valeur résultante est trop grande pour être représenté dans le `decimal` format, un `System.OverflowException` est levée. Si la valeur de résultat est trop petite pour être représenté dans le `decimal` format, le résultat est égal à zéro. La mise à l’échelle du résultat est la plus petite qui conservera un résultat égal à la plus proche de la valeur décimale représentable le résultat mathématique true.

   La division décimale équivaut à l’utilisation de l’opérateur de division du type `System.Decimal`.


### <a name="remainder-operator"></a>Opérateur de reste

Pour une opération du formulaire `x % y`, opérateur binaire de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.

Les opérateurs de reste prédéfinis sont répertoriés ci-dessous. Tous les opérateurs de calcul le reste de la division entre `x` et `y`.

*  Reste entier :

   ```csharp
   int operator %(int x, int y);
   uint operator %(uint x, uint y);
   long operator %(long x, long y);
   ulong operator %(ulong x, ulong y);
   ```

   Le résultat de `x % y` est la valeur produite par `x - (x / y) * y`. Si `y` est égal à zéro, un `System.DivideByZeroException` est levée.

   Si l’opérande gauche est le plus petit `int` ou `long` valeur et l’opérande de droite est `-1`, un `System.OverflowException` est levée. En aucun cas ne `x % y` lever une exception où `x / y` pas lève une exception.

*  Reste à virgule flottante :

   ```csharp
   float operator %(float x, float y);
   double operator %(double x, double y);
   ```

   Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies, les zéros non significatifs, les valeurs infinies et NaN. Dans la table, `x` et `y` sont des valeurs finies positives. `z` est le résultat de `x % y` et est calculée en tant que `x - n * y`, où `n` est le plus grand entier possible qui est inférieur ou égal à `x / y`. Cette méthode de calcul du reste est analogue à celle utilisée pour les opérandes entiers, mais diffère de la définition de la norme IEEE 754 (dans lequel `n` est l’entier le plus proche `x / y`).

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

*  Reste de nombres décimaux :

   ```csharp
   decimal operator %(decimal x, decimal y);
   ```

   Si la valeur de l’opérande droite est zéro, un `System.DivideByZeroException` est levée. La mise à l’échelle du résultat, avant tout arrondi, est la plus grande des échelles des deux opérandes, et le signe du résultat, si différente de zéro, est identique à celui de `x`.

   Reste de nombres décimaux est équivalente à l’aide de l’opérateur de reste du type `System.Decimal`.


### <a name="addition-operator"></a>Opérateur d’addition

Pour une opération du formulaire `x + y`, opérateur binaire de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.

Les opérateurs d’addition prédéfinis sont répertoriées ci-dessous. Pour les types numériques et d’énumération, les opérateurs d’addition prédéfinis calculent la somme des deux opérandes. Lorsqu’un ou deux opérandes sont de type chaîne, les opérateurs d’addition prédéfinis concaténer la représentation sous forme de chaîne des opérandes.

*  Ajout de l’entier :

   ```csharp
   int operator +(int x, int y);
   uint operator +(uint x, uint y);
   long operator +(long x, long y);
   ulong operator +(ulong x, ulong y);
   ```

   Dans un `checked` contexte, si la somme est en dehors de la plage du type du résultat, un `System.OverflowException` est levée. Dans un `unchecked` contexte, dépassements de capacité ne sont pas signalés et n’importe quel bits de poids fort significatifs en dehors de la plage du type du résultat sont ignorés.

*  Addition à virgule flottante :

   ```csharp
   float operator +(float x, float y);
   double operator +(double x, double y);
   ```

   La somme est calculée selon les règles de la norme IEEE 754 arithmétique. Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies, les zéros non significatifs, les valeurs infinies et NaN. Dans la table, `x` et `y` sont des valeurs finies, et `z` est le résultat de `x + y`. Si `x` et `y` ont la même grandeur, mais l’opposé de signes, `z` est un zéro positif. Si `x + y` est trop grand pour être représenté dans le type de destination, `z` est un infini ayant le même signe que `x + y`.

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

   Si la valeur résultante est trop grande pour être représenté dans le `decimal` format, un `System.OverflowException` est levée. L’échelle du résultat, avant tout arrondi, est la plus grande des échelles des deux opérandes.

   Addition de nombres décimaux est équivalente à l’aide de l’opérateur d’addition de type `System.Decimal`.

*  Ajout de l’énumération. Chaque type énumération fournit implicitement suivantes des opérateurs, prédéfinis où `E` est le type enum, et `U` est le type sous-jacent de `E`:

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

   Ces surcharges de la ressource binaire `+` opérateur effectuer la concaténation de chaînes. Si un opérande de concaténation de chaînes est `null`, une chaîne vide est substituée. Sinon, n’importe quel argument autre qu’une chaîne est converti en sa représentation de chaîne en appelant virtuel `ToString` méthode héritée du type `object`. Si `ToString` retourne `null`, une chaîne vide est substituée.

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

   Le résultat de l’opérateur de concaténation de chaîne est une chaîne qui se compose des caractères de l’opérande gauche suivi par les caractères de l’opérande de droite. L’opérateur de concaténation de chaîne ne retourne jamais un `null` valeur. Un `System.OutOfMemoryException` peut être levée si n’est pas suffisamment de mémoire disponible pour l’allocation de la chaîne résultante.

*  Combinaison de délégués. Chaque type délégué fournit implicitement l’opérateur prédéfini suivant, où `D` est le type délégué :

   ```csharp
   D operator +(D x, D y);
   ```

   Le fichier binaire `+` opérateur effectue la combinaison de délégués lorsque les deux opérandes sont d’un type délégué `D`. (Si les opérandes sont de types délégués différents, une erreur au moment de la liaison se produit.) Si le premier opérande est `null`, le résultat de l’opération est la valeur du deuxième opérande (même si c’est également `null`). Sinon, si le second opérande est `null`, le résultat de l’opération est la valeur du premier opérande. Sinon, le résultat de l’opération est un nouvelle instance de délégué qui, lors de l’appelé, appelle le premier opérande et appelle ensuite le second opérande. Pour obtenir des exemples de combinaison de délégués, consultez [opérateur de soustraction](expressions.md#subtraction-operator) et [appel de délégué](delegates.md#delegate-invocation). Dans la mesure où `System.Delegate` n’est pas un type délégué, `operator`  `+` n’est pas défini pour elle.

### <a name="subtraction-operator"></a>Opérateur de soustraction

Pour une opération du formulaire `x - y`, opérateur binaire de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.

Les opérateurs de soustraction prédéfinis sont répertoriés ci-dessous. Les opérateurs tous soustraire `y` de `x`.

*  Soustraction de nombres entiers :

   ```csharp
   int operator -(int x, int y);
   uint operator -(uint x, uint y);
   long operator -(long x, long y);
   ulong operator -(ulong x, ulong y);
   ```

   Dans un `checked` contexte, si la différence est en dehors de la plage du type du résultat, un `System.OverflowException` est levée. Dans un `unchecked` contexte, dépassements de capacité ne sont pas signalés et n’importe quel bits de poids fort significatifs en dehors de la plage du type du résultat sont ignorés.

*  Soustraction de nombres à virgule flottante :

   ```csharp
   float operator -(float x, float y);
   double operator -(double x, double y);
   ```

   La différence est calculée selon les règles de la norme IEEE 754 arithmétique. Le tableau suivant répertorie les résultats de toutes les combinaisons possibles de valeurs finies, les zéros non significatifs, les valeurs infinies et NaN. Dans la table, `x` et `y` sont des valeurs finies, et `z` est le résultat de `x - y`. Si `x` et `y` sont égales, `z` est un zéro positif. Si `x - y` est trop grand pour être représenté dans le type de destination, `z` est un infini ayant le même signe que `x - y`.

   |      |      |      |      |      |      |     |
   |:----:|:----:|:----:|:----:|:----:|:----:|:---:|
   |      | o    | +0   | -0   | +inf | -inf | NaN | 
   | x    | e    | x    | x    | -inf | +inf | NaN | 
   | +0   | -y   | +0   | +0   | -inf | +inf | NaN | 
   | -0   | -y   | -0   | +0   | -inf | +inf | NaN | 
   | +inf | +inf | +inf | +inf | NaN  | +inf | NaN | 
   | -inf | -inf | -inf | -inf | -inf | NaN  | NaN | 
   | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN | 

*  Soustraction de nombres décimaux :

   ```csharp
   decimal operator -(decimal x, decimal y);
   ```

   Si la valeur résultante est trop grande pour être représenté dans le `decimal` format, un `System.OverflowException` est levée. L’échelle du résultat, avant tout arrondi, est la plus grande des échelles des deux opérandes.

   Soustraction de nombres décimaux est équivalente à l’aide de l’opérateur de soustraction de type `System.Decimal`.

*  Soustraction de l’énumération. Chaque type énumération fournit implicitement l’opérateur prédéfini suivant, où `E` est le type enum, et `U` est le type sous-jacent de `E`:

   ```csharp
   U operator -(E x, E y);
   ```

   Cet opérateur est évalué exactement comme `(U)((U)x - (U)y)`. En d’autres termes, l’opérateur calcule la différence entre des valeurs ordinales des `x` et `y`, et le type du résultat est le type sous-jacent de l’énumération.

   ```csharp
   E operator -(E x, U y);
   ```

   Cet opérateur est évalué exactement comme `(E)((U)x - y)`. En d’autres termes, l’opérateur soustrait une valeur à partir du type sous-jacent de l’énumération, on obtient une valeur de l’énumération.

*  Suppression de délégué. Chaque type délégué fournit implicitement l’opérateur prédéfini suivant, où `D` est le type délégué :

   ```csharp
   D operator -(D x, D y);
   ```

   Le fichier binaire `-` opérateur effectue la suppression de délégué lorsque les deux opérandes sont d’un type délégué `D`. Si les opérandes ont des types délégués différents, une erreur au moment de la liaison se produit. Si le premier opérande est `null`, le résultat de l’opération est `null`. Sinon, si le second opérande est `null`, le résultat de l’opération est la valeur du premier opérande. Sinon, les deux opérandes représentent des listes d’appel ([déclarations Delegate](delegates.md#delegate-declarations)) ayant une ou plusieurs entrées et le résultat est une nouvelle liste d’appel qui se compose de la liste du premier opérande avec les entrées du deuxième opérande retirées Il, fourni la liste du deuxième opérande est une sous-liste contiguë correcte de la première.     (Pour déterminer l’égalité sous-liste, les entrées correspondantes sont comparées comme pour l’opérateur d’égalité de délégué ([déléguer des opérateurs d’égalité](expressions.md#delegate-equality-operators)).) Sinon, le résultat est la valeur de l’opérande gauche. Aucune des listes des opérandes est modifiée dans le processus. Si la liste du deuxième opérande correspond à plusieurs sous-listes d’entrées contiguës dans la liste du premier opérande, la sous-liste correspondante la plus à droite d’entrées contiguës est supprimée. Si les résultats de la suppression d’une liste vide, le résultat est `null`. Exemple :

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

Le `<<` et `>>` opérateurs sont utilisés pour effectuer des opérations de décalage de bits.

```antlr
shift_expression
    : additive_expression
    | shift_expression '<<' additive_expression
    | shift_expression right_shift additive_expression
    ;
```

Si un opérande d’un *shift_expression* a le type de compilation `dynamic`, puis l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas le type de compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution à l’aide du type au moment de l’exécution de ces opérandes qui ont le type de compilation `dynamic`.

Pour une opération du formulaire `x << count` ou `x >> count`, opérateur binaire de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.

Lorsque vous déclarez un opérateur de décalage surchargé, le type du premier opérande doit toujours être la classe ou structure qui contient la déclaration d’opérateur et le type du second opérande doit toujours être `int`.

Les opérateurs de décalage prédéfinis sont répertoriés ci-dessous.

*  Déplacer vers la gauche :

   ```csharp
   int operator <<(int x, int count);
   uint operator <<(uint x, int count);
   long operator <<(long x, int count);
   ulong operator <<(ulong x, int count);
   ```

   Le `<<` opérateur shifts `x` gauche d’un nombre de bits calculé comme indiqué ci-dessous.

   Les bits de poids fort en dehors de la plage du type de résultat de `x` sont ignorées, les bits restants sont décalés à gauche, et les positions de bits vides de poids faible sont définies à zéro.

*  Décalage à droite :

   ```csharp
   int operator >>(int x, int count);
   uint operator >>(uint x, int count);
   long operator >>(long x, int count);
   ulong operator >>(ulong x, int count);
   ```

   Le `>>` opérateur équipes `x` droite d’un nombre de bits calculé comme indiqué ci-dessous.

   Lorsque `x` est de type `int` ou `long`, les bits de poids faible de `x` sont ignorés, les bits restants sont décalés vers la droite, et les positions de bits vides de poids fort sont définies sur zéro si `x` est négative et défini 1 se `x` est un nombre négatif.

   Lorsque `x` est de type `uint` ou `ulong`, les bits de poids faible de `x` sont ignorées, les bits restants sont décalés vers la droite, et les positions de bits vides de poids fort sont définies à zéro.

Pour les opérateurs prédéfinis, le nombre de bits de décalage est calculé comme suit :

*  Lorsque le type de `x` est `int` ou `uint`, la valeur du décalage est donnée par les cinq bits de poids faible de `count`. En d’autres termes, la valeur du décalage est calculée à partir de `count & 0x1F`.
*  Lorsque le type de `x` est `long` ou `ulong`, la valeur du décalage est donnée par les six bits de poids faible de `count`. En d’autres termes, la valeur du décalage est calculée à partir de `count & 0x3F`.

Si le compteur de décalage est égal à zéro, les opérateurs de décalage doit simplement retournent la valeur de `x`.

Opérations de décalage jamais provoquer des dépassements de capacité et produisent les mêmes résultats dans `checked` et `unchecked` contextes.

Lorsque l’opérande gauche de la `>>` opérateur est d’un type intégral signé, l’opérateur effectue un décalage arithmétique vers la droite dans laquelle la valeur de bit le plus significatif (le bit de signe) de l’opérande est propagée vers les positions de bits vides de poids fort. Lorsque l’opérande gauche de la `>>` opérateur est de type intégral non signé, l’opérateur effectue un décalage logique droite fort les positions des bits vides de poids fort sont toujours mises à zéro. Pour effectuer l’opération inverse de qui déduit le type d’opérande, des casts explicites peuvent être utilisés. Par exemple, si `x` est une variable de type `int`, l’opération `unchecked((int)((uint)x >> y))` effectue un décalage logique droite de `x`.

## <a name="relational-and-type-testing-operators"></a>Opérateurs relationnels et de test de type

Le `==`, `!=`, `<`, `>`, `<=`, `>=`, `is` et `as` sont appelés les opérateurs relationnels et de test de type.

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

Le `is` opérateur est décrit dans [l’opérateur n’est](expressions.md#the-is-operator) et le `as` opérateur est décrit dans [l’opérateur as](expressions.md#the-as-operator).

Le `==`, `!=`, `<`, `>`, `<=` et `>=` opérateurs sont ***opérateurs de comparaison***.

Si un opérande d’un opérateur de comparaison a le type de compilation `dynamic`, puis l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas le type de compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution à l’aide du type au moment de l’exécution de ces opérandes qui ont le type de compilation `dynamic`.

Pour une opération du formulaire `x` *op* `y`, où *op* est un opérateur de comparaison, la résolution de surcharge ([opérateur binaire surcharge résolution](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.

Les opérateurs de comparaison prédéfinis sont décrits dans les sections suivantes. Tous les opérateurs de comparaison prédéfinis retournent un résultat de type `bool`, comme décrit dans le tableau suivant.


| __Opération__ | __Résultat__                                                       |
|---------------|------------------------------------------------------------------|
| `x == y`      | `true` Si `x` est égal à `y`, `false` sinon                 | 
| `x != y`      | `true` Si `x` n’est pas égal à `y`, `false` sinon             | 
| `x < y`       | `true` Si `x` est inférieure à `y`, `false` sinon                | 
| `x > y`       | `true` Si `x` est supérieur à `y`, `false` sinon             | 
| `x <= y`      | `true` Si `x` est inférieure ou égale à `y`, `false` sinon    | 
| `x >= y`      | `true` Si `x` est supérieur ou égal à `y`, `false` sinon | 

### <a name="integer-comparison-operators"></a>Opérateurs de comparaison d’entiers

Les opérateurs de comparaison d’entiers prédéfinis sont :
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

Chacun de ces opérateurs compare les valeurs numériques des deux opérandes entiers et retourne un `bool` valeur qui indique si la relation particulière est `true` ou `false`.

### <a name="floating-point-comparison-operators"></a>Opérateurs de comparaison à virgule flottante

Les opérateurs de comparaison à virgule flottante prédéfinis sont :
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

Les opérateurs comparent les opérandes selon les règles de la norme IEEE 754 :

*  Si des opérandes sont une NaN, le résultat est `false` pour tous les opérateurs à l’exception `!=`, pour lequel le résultat est `true`. Pour les deux opérandes, `x != y` produit toujours le même résultat que `!(x == y)`. Toutefois, quand un ou deux opérandes sont des NaN, la `<`, `>`, `<=`, et `>=` opérateurs ne produisent pas les mêmes résultats que la négation logique de l’opérateur opposé. Par exemple, si un de `x` et `y` est NaN, puis `x < y` est `false`, mais `!(x >= y)` est `true`.
*  Lorsque aucun des opérandes sont une NaN, les opérateurs comparent les valeurs des deux opérandes à virgule flottante en ce qui concerne l’ordre

   ```
   -inf < -max < ... < -min < -0.0 == +0.0 < +min < ... < +max < +inf
   ```

   où `min` et `max` sont les plus grandes et positif finis valeurs qui peuvent être représentés dans le format à virgule flottante donné. Les effets notables de cet ordre sont :
   * Les zéros non significatifs positifs et négatifs sont considérées comme égales.
   * Un infini négatif est considéré comme inférieur à toutes les autres valeurs, mais égal à un autre infini négatif.
   * Un infini positif est considéré comme supérieur à toutes les autres valeurs, mais il est égal à un autre infini positif.

### <a name="decimal-comparison-operators"></a>Opérateurs de comparaison de nombres décimaux

Les opérateurs de comparaison de décimaux prédéfinis sont :
```csharp
bool operator ==(decimal x, decimal y);
bool operator !=(decimal x, decimal y);
bool operator <(decimal x, decimal y);
bool operator >(decimal x, decimal y);
bool operator <=(decimal x, decimal y);
bool operator >=(decimal x, decimal y);
```

Chacun de ces opérateurs compare les valeurs numériques des deux opérandes décimales et retourne un `bool` valeur qui indique si la relation particulière est `true` ou `false`. Chaque comparaison de nombres décimaux est équivalente à l’aide de l’opérateur d’égalité de type ou relationnel correspondant `System.Decimal`.

### <a name="boolean-equality-operators"></a>Opérateurs d’égalité booléenne

Les opérateurs d’égalité booléenne prédéfinis sont :
```csharp
bool operator ==(bool x, bool y);
bool operator !=(bool x, bool y);
```

Le résultat de `==` est `true` si les deux `x` et `y` sont `true` ou si les deux `x` et `y` sont `false`. Sinon, le résultat est `false`.

Le résultat de `!=` est `false` si les deux `x` et `y` sont `true` ou si les deux `x` et `y` sont `false`. Sinon, le résultat est `true`. Lorsque les opérandes sont de type `bool`, le `!=` opérateur génère le même résultat que la `^` opérateur.

### <a name="enumeration-comparison-operators"></a>Opérateurs de comparaison d’énumération

Chaque type énumération fournit implicitement les opérateurs de comparaison prédéfinis suivants :
```csharp
bool operator ==(E x, E y);
bool operator !=(E x, E y);
bool operator <(E x, E y);
bool operator >(E x, E y);
bool operator <=(E x, E y);
bool operator >=(E x, E y);
```

Le résultat de l’évaluation `x op y`, où `x` et `y` sont des expressions d’un type énumération `E` avec un type sous-jacent `U`, et `op` est un des opérateurs de comparaison, est exactement identique à l’évaluation `((U)x) op ((U)y)`. En d’autres termes, les opérateurs de comparaison de type énumération simplement comparent les valeurs intégrales sous-jacentes des deux opérandes.

### <a name="reference-type-equality-operators"></a>Opérateurs d’égalité de type référence

Les opérateurs d’égalité de type référence prédéfinis sont :
```csharp
bool operator ==(object x, object y);
bool operator !=(object x, object y);
```

Les opérateurs retournent le résultat de la comparaison des deux références pour l’égalité ou non égalité.

Étant donné que les opérateurs d’égalité de type référence prédéfinis acceptent les opérandes de type `object`, elles s’appliquent à tous les types qui ne déclarent pas applicables `operator ==` et `operator !=` membres. À l’inverse, tous les opérateurs d’égalité de défini par l’utilisateur applicable masque effectivement les opérateurs d’égalité de type référence prédéfinis.

Les opérateurs d’égalité de type référence prédéfinis nécessitent une des opérations suivantes :

*  Les deux opérandes sont une valeur d’un type connu pour être un *reference_type* ou le littéral `null`. En outre, une conversion de référence explicite ([conversions de référence explicite](conversions.md#explicit-reference-conversions)) existe à partir du type de des opérandes vers le type de l’autre opérande.
*  Un opérande est une valeur de type `T` où `T` est un *type_parameter* et l’autre opérande est le littéral `null`. En outre `T` n’a pas la contrainte de type valeur.

Sauf si une des conditions suivantes sont remplies, une erreur au moment de la liaison se produit. Les implications notables de ces règles sont :

*  Il est une erreur au moment de la liaison à utiliser les opérateurs d’égalité de type référence prédéfinis pour comparer deux références qui sont connues pour être différentes au moment de la liaison. Par exemple, si les types de liaison-heure des opérandes sont deux types de classe `A` et `B`et si ni `A` ni `B` dérive de l’autre, puis il serait impossible pour les deux opérandes référencer le même objet. Par conséquent, l’opération est considérée comme une erreur au moment de la liaison.
*  Les opérateurs d’égalité de type référence prédéfinis ne permettent pas de valeur des opérandes de type à comparer. Par conséquent, sauf si un type struct déclare ses propres opérateurs d’égalité, il n’est pas possible de comparer les valeurs de ce type struct.
*  Les opérateurs d’égalité de type référence prédéfinis provoquent jamais les opérations de boxing pour leurs opérandes. Il est inutile d’effectuer ces opérations de boxing, étant donné que les références aux instances boxed nouvellement allouées seraient nécessairement différentes de toutes les autres références.
*  Si un opérande d’un type de paramètre de type `T` est comparé à `null`et le type au moment de l’exécution de `T` est un type valeur, le résultat de la comparaison est `false`.

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

Le `x == null` construction est autorisée même si `T` pourrait représenter un type valeur, et le résultat est simplement défini pour être `false` lorsque `T` est un type valeur.

Pour une opération du formulaire `x == y` ou `x != y`, le cas échéant toute `operator ==` ou `operator !=` existe, la résolution de surcharge d’opérateur ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) règles qui sélectionnera opérateur au lieu de l’opérateur d’égalité de type référence prédéfinis. Toutefois, il est toujours possible de sélectionner l’opérateur d’égalité de type référence prédéfinis en effectuant un cast explicite d’une ou les deux opérandes en type `object`. L’exemple
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
```
True
False
False
False
```

Le `s` et `t` variables font référence à deux distinctes `string` instances contenant les mêmes caractères. La première comparaison génère `True` , car l’opérateur d’égalité de chaîne prédéfinie ([opérateurs d’égalité de chaîne](expressions.md#string-equality-operators)) est sélectionné lorsque les deux opérandes sont de type `string`. Les autres comparaisons donnent toutes `False` , car l’opérateur d’égalité de type référence prédéfinis est sélectionné quand un ou les deux opérandes sont de type `object`.

Notez que la technique ci-dessus n’est pas significative pour les types de valeur. L’exemple
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
sorties `False` converti (boxed), car les casts créent des références à deux instances distinctes de `int` valeurs.

### <a name="string-equality-operators"></a>Opérateurs d’égalité

Les opérateurs d’égalité de chaîne prédéfinies sont :
```csharp
bool operator ==(string x, string y);
bool operator !=(string x, string y);
```

Deux `string` valeurs sont considérées comme égales lorsqu’une des opérations suivantes est vraie :

*  Les deux valeurs sont `null`.
*  Les deux valeurs sont des références non-null à des instances de chaîne qui ont des longueurs identiques et des caractères identiques dans chaque position de caractère.

Les opérateurs d’égalité de chaîne comparent des valeurs de chaîne plutôt que des références de chaîne. Lorsque deux instances de chaîne séparées contiennent exactement la même séquence de caractères, les valeurs des chaînes sont égales, mais les références sont différentes. Comme décrit dans [opérateurs d’égalité de type référence](expressions.md#reference-type-equality-operators), les opérateurs d’égalité de type référence peuvent être utilisés pour comparer les références de chaîne au lieu de valeurs de chaîne.

### <a name="delegate-equality-operators"></a>Opérateurs d’égalité de délégué

Chaque type de délégué fournit implicitement les opérateurs de comparaison prédéfinis suivants :

```csharp
bool operator ==(System.Delegate x, System.Delegate y);
bool operator !=(System.Delegate x, System.Delegate y);
```

Délégué de deux instances est considérées comme égales comme suit :

*  Si une des instances de délégué est `null`, elles sont égales si et seulement si les deux sont `null`.
*  Si les délégués ont un autre type d’exécution qu’ils ne sont jamais égaux.
*  Si les deux instances de délégué ont une liste d’appel ([déclarations Delegate](delegates.md#delegate-declarations)), ces instances sont égales si et seulement si leurs listes d’appel sont de même longueur, et chaque entrée dans sa liste d’appel est égale (tel que défini ci-dessous) à l’entrée correspondante dans l’ordre, dans la liste d’appel pour l’autre.

Les règles suivantes régissent l’égalité des entrées de liste d’appel :

*  Si deux appel entrées de la liste les deux référence au même statique (méthode), puis les entrées sont égale.
*  Si deux appel entrées de la liste les deux font référence à la même méthode non statique sur le même objet cible (comme défini par les opérateurs d’égalité de référence) les entrées sont égale.
*  Entrées de liste d’appel provenant à partir de la version d’évaluation de sémantiquement identiques *anonymous_method_expression*s ou *lambda_expression*s avec le même jeu (éventuellement vide) de la variable externe capturée instances sont autorisés (mais pas obligatoires) sont égales.

### <a name="equality-operators-and-null"></a>NULL et les opérateurs d’égalité

Le `==` et `!=` opérateurs permettent un opérande soit une valeur d’un type nullable et l’autre pour être le `null` littéral, même si aucun opérateur prédéfinie ou définies par l’utilisateur (dans unlifted ou levé formulaire) n’existe pour l’opération.

Pour une opération d’une des formes
```csharp
x == null
null == x
x != null
null != x
```
où `x` est une expression d’un type nullable, si l’opérateur de résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) ne parvient pas à trouver un opérateur applicable, le résultat est calculé à la place à partir du `HasValue` propriété de `x`. Plus précisément, les deux premiers formulaires sont traduites en `!x.HasValue`, et les deux derniers formulaires sont traduites en `x.HasValue`.

### <a name="the-is-operator"></a>L’opérateur is

Le `is` opérateur est utilisé pour contrôler de manière dynamique si le type au moment de l’exécution d’un objet est compatible avec un type donné. Le résultat de l’opération `E is T`, où `E` est une expression et `T` est un type, est une valeur booléenne valeur indiquant si `E` peut être converti en type `T` par une conversion de référence, une conversion boxing conversion, ou une conversion unboxing. L’opération est évaluée comme suit, après que les arguments de type ont été substitués à tous les paramètres de type :

*  Si `E` est une fonction anonyme, une erreur de compilation se produit
*  Si `E` est un groupe de méthodes ou les `null` littéral of si le type de `E` est un type référence ou un type nullable et la valeur de `E` est null, le résultat est false.
*  Sinon, laissez le `D` représentent le type dynamique de `E` comme suit :
   * Si le type de `E` est un type référence, `D` est le type au moment de l’exécution de la référence d’instance par `E`.
   * Si le type de `E` est un type nullable, `D` est le type sous-jacent de ce type nullable.
   * Si le type de `E` est un type valeur non nullable, `D` est le type de `E`.
*  Le résultat de l’opération dépend `D` et `T` comme suit :
   * Si `T` est un type référence, le résultat est true si `D` et `T` sont le même type, si `D` est un type référence et une conversion de référence implicite à partir de `D` à `T` existe, ou si `D` est un type valeur et une conversion boxing de `D` à `T` existe.
   * Si `T` est un type nullable, le résultat est true si `D` est le type sous-jacent de `T`.
   * Si `T` est un type valeur non nullable, le résultat est true si `D` et `T` sont du même type.
   * Sinon, le résultat est false.

Notez que les conversions définies par l’utilisateur, ne sont pas envisagés par le `is` opérateur.

### <a name="the-as-operator"></a>L’opérateur as

Le `as` opérateur est utilisé pour convertir explicitement une valeur à un type de référence donnée ou d’un type nullable. Contrairement à une expression de cast ([les expressions de Cast](expressions.md#cast-expressions)), le `as` opérateur lève jamais d’exception. Au lieu de cela, si la conversion indiquée n’est pas possible, la valeur résultante est `null`.

Dans une opération de la forme `E as T`, `E` doit être une expression et `T` doit être un type référence, un paramètre de type connu pour être un type référence ou un type nullable. En outre, au moins un des éléments suivants doit être remplie, ou sinon une erreur de compilation se produit :

*  Une identité ([conversion d’identité](conversions.md#identity-conversion)), implicites nullable ([les conversions implicites nullables](conversions.md#implicit-nullable-conversions)), référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions)), le boxing ([ Les conversions boxing](conversions.md#boxing-conversions)) explicite nullable ([conversions nullables explicites](conversions.md#explicit-nullable-conversions)), référence explicite ([conversions de référence explicite](conversions.md#explicit-reference-conversions)), ou unboxing ([Les conversions Unboxing](conversions.md#unboxing-conversions)) conversion existe entre `E` à `T`.
*  Le type de `E` ou `T` est un type ouvert.
*  `E` est le `null` littéral.

Si le type de la compilation de `E` n’est pas `dynamic`, l’opération `E as T` produit le même résultat en tant que
```csharp
E is T ? (T)(E) : (T)null
```
sauf que `E` n’est évalué qu’une seule fois. Le compilateur peut s’attendre à optimiser `E as T` à effectuer au maximum une vérification de type dynamique par opposition à deux contrôles de type dynamique impliquée par l’expansion ci-dessus.

Si le type de la compilation de `E` est `dynamic`, contrairement à l’opérateur de cast le `as` opérateur n’est pas lié dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Par conséquent, l’expansion est dans ce cas :
```csharp
E is T ? (T)(object)(E) : (T)null
```

Notez que certaines conversions, telles que les conversions définies par l’utilisateur, ne sont pas possibles avec la `as` opérateur et doivent être effectuées à l’aide d’expressions de cast.

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
le paramètre de type `T` de `G` est censée être un type référence, car il a la contrainte de classe. Le paramètre de type `U` de `H` n’est pas toutefois ; par conséquent, l’utilisation de la `as` opérateur dans `H` n’est pas autorisée.

## <a name="logical-operators"></a>Opérateurs logiques

Le `&`, `^`, et `|` sont appelés les opérateurs logiques.

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

Si un opérande d’un opérateur logique a le type de compilation `dynamic`, puis l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas le type de compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution à l’aide du type au moment de l’exécution de ces opérandes qui ont le type de compilation `dynamic`.

Pour une opération du formulaire `x op y`, où `op` est un des opérateurs logiques, la résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) est appliquée pour sélectionner l’implémentation d’un opérateur spécifique. Les opérandes sont convertis en types de paramètres de l’opérateur sélectionné et le type du résultat est le type de retour de l’opérateur.

Les opérateurs logiques prédéfinis sont décrits dans les sections suivantes.

### <a name="integer-logical-operators"></a>Opérateurs logiques d’entiers

Les opérateurs logiques d’entiers prédéfinis sont :
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

Le `&` opérateur calcule l’opérateur de bits logiques `AND` des deux opérandes, le `|` opérateur calcule l’opérateur de bits logiques `OR` des deux opérandes et le `^` opérateur calcule l’OR exclusif logique `OR` des deux opérandes. Aucun dépassements de capacité ne sont possibles à partir de ces opérations.

### <a name="enumeration-logical-operators"></a>Opérateurs logiques d’énumération

Chaque type d’énumération `E` implicitement fournit les éléments suivants prédéfinies des opérateurs logiques :

```csharp
E operator &(E x, E y);
E operator |(E x, E y);
E operator ^(E x, E y);
```

Le résultat de l’évaluation `x op y`, où `x` et `y` sont des expressions d’un type énumération `E` avec un type sous-jacent `U`, et `op` est un des opérateurs logiques, est exactement identique à l’évaluation `(E)((U)x op (U)y)`. En d’autres termes, les opérateurs logiques de type énumération simplement effectuent l’opération logique sur le type sous-jacent des deux opérandes.

### <a name="boolean-logical-operators"></a>Opérateurs logiques booléens

Les opérateurs logiques booléens prédéfinis sont :
```csharp
bool operator &(bool x, bool y);
bool operator |(bool x, bool y);
bool operator ^(bool x, bool y);
```

Le résultat de `x & y` est `true` si `x` et `y` sont `true`. Sinon, le résultat est `false`.

Le résultat de `x | y` est `true` si `x` ou `y` est `true`. Sinon, le résultat est `false`.

Le résultat de `x ^ y` est `true` si `x` est `true` et `y` est `false`, ou `x` est `false` et `y` est `true`. Sinon, le résultat est `false`. Lorsque les opérandes sont de type `bool`, le `^` opérateur calcule le même résultat que la `!=` opérateur.

### <a name="nullable-boolean-logical-operators"></a>Opérateurs logiques de type boolean nullables

Le type boolean nullable `bool?` peut représenter les trois valeurs, `true`, `false`, et `null`et est conceptuellement semblable au type à trois valeurs utilisé pour les expressions booléennes dans SQL. Pour vous assurer que les résultats générés par le `&` et `|` opérateurs pour `bool?` opérandes sont cohérentes avec une logique à trois valeurs de SQL, les opérateurs prédéfinis suivants sont fournis :

```csharp
bool? operator &(bool? x, bool? y);
bool? operator |(bool? x, bool? y);
```

Le tableau suivant répertorie les résultats générés par ces opérateurs pour toutes les combinaisons des valeurs `true`, `false`, et `null`.

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

Le `&&` et `||` sont appelés des opérateurs logiques conditionnels. Ils sont également appelés les opérateurs logiques « court-circuit ».

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

Le `&&` et `||` opérateurs sont des versions conditionnelles de la `&` et `|` opérateurs :

*  L’opération `x && y` correspond à l’opération `x & y`, sauf que `y` est évaluée uniquement si `x` n’est pas `false`.
*  L’opération `x || y` correspond à l’opération `x | y`, sauf que `y` est évaluée uniquement si `x` n’est pas `true`.

Si un opérande d’un opérateur logique conditionnel a le type de compilation `dynamic`, puis l’expression est liée dynamiquement ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas le type de compilation de l’expression est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution à l’aide du type au moment de l’exécution de ces opérandes qui ont le type de compilation `dynamic`.

Une opération de la forme `x && y` ou `x || y` est traitée en appliquant la résolution de surcharge ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) comme si l’opération a été écrite `x & y` ou `x | y`. Ensuite,

*  Si la résolution de surcharge ne parvient pas à trouver un seul meilleur opérateur, ou si la résolution de surcharge sélectionne l’un des opérateurs logiques d’entiers prédéfinis, une erreur au moment de la liaison se produit.
*  Sinon, si l’opérateur sélectionné est un des opérateurs logiques booléens prédéfinis ([des opérateurs logiques booléens](expressions.md#boolean-logical-operators)) ou des opérateurs logiques booléens nullables ([des opérateurs logiques booléens Nullable](expressions.md#nullable-boolean-logical-operators)), le opération est traitée comme décrit dans [booléennes opérateurs logiques conditionnels](expressions.md#boolean-conditional-logical-operators).
*  Sinon, l’opérateur sélectionné est un opérateur défini par l’utilisateur, et l’opération est traitée comme décrit dans [opérateurs logiques conditionnels définis par l’utilisateur](expressions.md#user-defined-conditional-logical-operators).

Il n’est pas possible de surcharger directement les opérateurs logiques conditionnels. Toutefois, étant donné que les opérateurs logiques conditionnels sont évaluées en termes d’opérateurs logiques normaux, les surcharges des opérateurs logiques normaux sont, avec certaines restrictions, considérées comme des surcharges des opérateurs logiques conditionnels. Cela est décrite plus loin dans [opérateurs logiques conditionnels définis par l’utilisateur](expressions.md#user-defined-conditional-logical-operators).

### <a name="boolean-conditional-logical-operators"></a>Opérateurs logiques conditionnels booléens

Lorsque les opérandes de `&&` ou `||` sont de type `bool`, ou lorsque les opérandes sont des types qui ne définissent pas un applicable `operator &` ou `operator |`, mais ne définissent pas de conversions implicites vers `bool`, l’opération est traitées comme suit :

*  L’opération `x && y` est évalué comme `x ? y : false`. En d’autres termes, `x` est tout d’abord évaluées et converties en type `bool`. Ensuite, si `x` est `true`, `y` est évaluée et converti en type `bool`, et cela devient le résultat de l’opération. Sinon, le résultat de l’opération est `false`.
*  L’opération `x || y` est évalué comme `x ? true : y`. En d’autres termes, `x` est tout d’abord évaluées et converties en type `bool`. Ensuite, si `x` est `true`, le résultat de l’opération est `true`. Sinon, `y` est évaluée et converti en type `bool`, et cela devient le résultat de l’opération.

### <a name="user-defined-conditional-logical-operators"></a>Opérateurs logiques conditionnels définis par l’utilisateur

Lorsque les opérandes de `&&` ou `||` sont des types qui déclarent un applicables définies par l’utilisateur `operator &` ou `operator |`, des opérations suivantes doivent être remplies, où `T` est le type dans lequel l’opérateur sélectionné est déclarée :

*  Le type de retour et le type de chaque paramètre de l’opérateur sélectionné doivent être `T`. En d’autres termes, l’opérateur doit calculer la logique `AND` ou l’opérateur logique `OR` des deux opérandes de type `T`et doit retourner un résultat de type `T`.
*  `T` doit contenir les déclarations de `operator true` et `operator false`.

Une erreur au moment de la liaison se produit si une de ces conditions n’est pas satisfaite. Sinon, le `&&` ou `||` est évaluée en combinant le défini par l’utilisateur `operator true` ou `operator false` avec l’opérateur défini par l’utilisateur sélectionné :

*  L’opération `x && y` est évalué comme `T.false(x) ? x : T.&(x, y)`, où `T.false(x)` est un appel de la `operator false` déclaré dans `T`, et `T.&(x, y)` est un appel de sélectionné `operator &`. En d’autres termes, `x` est évalué en premier et `operator false` est appelée sur le résultat pour déterminer si `x` est définitivement false. Ensuite, si `x` est définitivement false, le résultat de l’opération est la valeur précédemment calculée pour `x`. Sinon, `y` est évaluée et le texte sélectionné `operator &` est appelée sur la valeur précédemment calculée pour `x` et la valeur calculée pour `y` pour produire le résultat de l’opération.
*  L’opération `x || y` est évalué comme `T.true(x) ? x : T.|(x, y)`, où `T.true(x)` est un appel de la `operator true` déclaré dans `T`, et `T.|(x,y)` est un appel de sélectionné `operator|`. En d’autres termes, `x` est évalué en premier et `operator true` est appelée sur le résultat pour déterminer si `x` vaut sans aucun doute. Ensuite, si `x` vaut sans aucun doute, le résultat de l’opération est la valeur précédemment calculée pour `x`. Sinon, `y` est évaluée et le texte sélectionné `operator |` est appelée sur la valeur précédemment calculée pour `x` et la valeur calculée pour `y` pour produire le résultat de l’opération.

Dans une de ces opérations, l’expression donnée par `x` est évaluée une seule fois et l’expression donnée par `y` est pas évalué, ni évaluée une seule fois.

Pour obtenir un exemple d’un type qui implémente `operator true` et `operator false`, consultez [de base de données de type booléen](structs.md#database-boolean-type).

## <a name="the-null-coalescing-operator"></a>L’opérateur de fusion null

Le `??` opérateur est appelé l’opérateur de fusion null.

```antlr
null_coalescing_expression
    : conditional_or_expression
    | conditional_or_expression '??' null_coalescing_expression
    ;
```

Une expression de fusion null sous la forme `a ?? b` nécessite `a` pour être d’un type référence ou de type nullable. Si `a` n’est pas null, le résultat de `a ?? b` est `a`; sinon, le résultat est `b`. L’opération a la valeur `b` uniquement si `a` a la valeur null.

L’opérateur de fusion null est associatif à droite, ce qui signifie que les opérations sont groupées de droite à gauche. Par exemple, une expression sous la forme `a ?? b ?? c` est évalué comme `a ?? (b ?? c)`. Dans de manière générale, une expression sous la forme `E1 ?? E2 ?? ... ?? En` retourne le premier des opérandes est non null, ou null si tous les opérandes sont null.

Le type de l’expression `a ?? b` varie selon les conversions implicites sont disponibles sur les opérandes. Dans l’ordre de préférence, le type de `a ?? b` est `A0`, `A`, ou `B`, où `A` est le type de `a` (sous réserve que `a` a un type), `B` est le type de `b` () condition que `b` a un type), et `A0` est le type sous-jacent de `A` si `A` est un type nullable, ou `A` dans le cas contraire. Plus précisément, `a ?? b` est traité comme suit :

*  Si `A` existe et n’est pas un type nullable ou un type référence, une erreur de compilation se produit.
*  Si `b` est une expression dynamique, le type de résultat est `dynamic`. Au moment de l’exécution, `a` est évalué en premier. Si `a` n’est pas null, `a` est converti en dynamique, et cela devient le résultat. Sinon, `b` est évaluée, et cela devient le résultat.
*  Sinon, si `A` existe et est un type nullable et existe une conversion implicite de `b` à `A0`, le type de résultat est `A0`. Au moment de l’exécution, `a` est évalué en premier. Si `a` n’est pas null, `a` est désencapsulé pour taper `A0`, et cela devient le résultat. Sinon, `b` est évaluée et converti en type `A0`, et cela devient le résultat.
*  Sinon, si `A` existe et qu’une conversion implicite existe à partir de `b` à `A`, le type de résultat est `A`. Au moment de l’exécution, `a` est évalué en premier. Si `a` n’est pas null, `a` devient le résultat. Sinon, `b` est évaluée et converti en type `A`, et cela devient le résultat.
*  Sinon, si `b` a un type `B` et existe une conversion implicite de `a` à `B`, le type de résultat est `B`. Au moment de l’exécution, `a` est évalué en premier. Si `a` n’est pas null, `a` est désencapsulé pour taper `A0` (si `A` existe et est nullable) et converti en type `B`, et cela devient le résultat. Sinon, `b` est évaluée et devient le résultat.
*  Sinon, `a` et `b` sont incompatibles et une erreur de compilation se produit.

## <a name="conditional-operator"></a>Opérateur conditionnel

Le `?:` opérateur est appelé l’opérateur conditionnel. Il est parfois également appelé l’opérateur ternaire.

```antlr
conditional_expression
    : null_coalescing_expression
    | null_coalescing_expression '?' expression ':' expression
    ;
```

Une expression conditionnelle de la forme `b ? x : y` évalue la condition d’abord `b`. Ensuite, si `b` est `true`, `x` est évaluée et devient le résultat de l’opération. Sinon, `y` est évaluée et devient le résultat de l’opération. Une expression conditionnelle évalue jamais les deux `x` et `y`.

L’opérateur conditionnel est associatif à droite, ce qui signifie que les opérations sont groupées de droite à gauche. Par exemple, une expression sous la forme `a ? b : c ? d : e` est évalué comme `a ? b : (c ? d : e)`.

Le premier opérande de le `?:` opérateur doit être une expression qui peut être implicitement convertie en `bool`, ou une expression d’un type qui implémente `operator true`. Si aucune de ces conditions n’est remplie, une erreur de compilation se produit.

Les deuxième et troisième opérandes `x` et `y`, de la `?:` opérateur contrôler le type de l’expression conditionnelle.

*  Si `x` a type `X` et `y` a type `Y` puis
   * Si une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir de `X` à `Y`, mais pas de `Y` à `X`, puis `Y` est le type de l’expression conditionnelle.
   * Si une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)) existe à partir de `Y` à `X`, mais pas de `X` à `Y`, puis `X` est le type de l’expression conditionnelle.
   * Sinon, aucun type de l’expression ne peut être déterminé, et une erreur de compilation se produit.
*  Si seul un des `x` et `y` a un type et les deux `x` et `y`, de sont implicitement convertible vers ce type, qui est le type de l’expression conditionnelle.
*  Sinon, aucun type de l’expression ne peut être déterminé, et une erreur de compilation se produit.

Le traitement de l’exécution d’une expression conditionnelle de la forme `b ? x : y` se compose des étapes suivantes :

*  Tout d’abord, `b` est évaluée et le `bool` valeur `b` est déterminé :
   * Si une conversion implicite du type de `b` à `bool` existe, cette conversion implicite est effectuée pour produire un `bool` valeur.
   * Sinon, le `operator true` défini par le type de `b` est appelé pour produire un `bool` valeur.
*  Si le `bool` valeur produite par l’étape précédente est `true`, puis `x` est évaluée et convertie vers le type de l’expression conditionnelle, et cela devient le résultat de l’expression conditionnelle.
*  Sinon, `y` est évaluée et convertie vers le type de l’expression conditionnelle, et cela devient le résultat de l’expression conditionnelle.

## <a name="anonymous-function-expressions"></a>Expressions de fonction anonyme

Un ***fonction anonyme*** est une expression qui représente une définition de méthode de « en ligne ». Une fonction anonyme n’a pas un type ou la valeur elle-même, mais est convertible en un type d’arborescence délégué ou une expression compatible. L’évaluation d’une conversion de la fonction anonyme varie selon le type de cible de la conversion : Dans le cas d’un type délégué, la conversion prend une valeur de délégué faisant référence à la méthode qui définit la fonction anonyme. Dans le cas d’un type arborescence d’expression, la conversion évalue à une arborescence d’expression qui représente la structure de la méthode comme une structure d’objet.

Pour des raisons historiques il existe deux variantes syntaxiques de fonctions anonymes, à savoir *lambda_expression*s et *anonymous_method_expression*s. À des fins de presque tous les, *lambda_expression*s sont plus concises et plus expressif que *anonymous_method_expression*s, qui restent dans la langue pour des raisons de compatibilité descendante.

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

Le `=>` a la même priorité que l’assignation (`=`) et est associatif à droite.

Une fonction anonyme avec le `async` modificateur est une fonction async et suit les règles décrites dans [itérateurs](classes.md#iterators).

Les paramètres d’une fonction anonyme sous la forme d’un *lambda_expression* peut être explicitement ou implicitement typé. Dans une liste de paramètres explicitement typée, le type de chaque paramètre est défini explicitement. Dans une liste de paramètres implicitement typé, les types des paramètres sont déduits à partir du contexte dans lequel la fonction anonyme se produit, en particulier, lorsque la fonction anonyme est convertie en type délégué compatible ou type arborescence d’expression, qui fournit de type les types de paramètres ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)).

Dans une fonction anonyme avec un paramètre unique, implicitement typé, les parenthèses peuvent être omises de la liste de paramètres. En d’autres termes, une fonction anonyme du formulaire
```csharp
( param ) => expr
```
peut être abrégé en
```csharp
param => expr
```

La liste des paramètres d’une fonction anonyme sous la forme d’un *anonymous_method_expression* est facultatif. Si spécifié, les paramètres doivent être explicitement typées. Sinon, la fonction anonyme est convertible en un délégué avec n’importe quel paramètre de liste ne contenant ne pas `out` paramètres.

Un *bloc* corps d’une fonction anonyme est accessible ([points de terminaison et accessibilité](statements.md#end-points-and-reachability)), sauf si la fonction anonyme se produit à l’intérieur d’une instruction inaccessible.

Suivent les quelques exemples de fonctions anonymes ci-dessous :

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

Le comportement de *lambda_expression*s et *anonymous_method_expression*s est identique à l’exception des points suivants :

*  *anonymous_method_expression*s autoriser la liste de paramètres pour être omis, produisant la convertibilité de variance pour déléguer les types de n’importe quelle liste de paramètres de valeur.
*  *lambda_expression*s autoriser à être omis et déduit alors que les types de paramètres *anonymous_method_expression*s requièrent des types de paramètre d’être explicitement spécifié.
*  Le corps d’un *lambda_expression* peut être une expression ou un bloc d’instructions tandis que le corps d’un *anonymous_method_expression* doit être un bloc d’instructions.
*  Uniquement *lambda_expression*s ont des conversions de types d’arborescence expression compatible ([types arborescence d’Expression](types.md#expression-tree-types)).

### <a name="anonymous-function-signatures"></a>Signatures de fonction anonyme

Le paramètre facultatif *anonymous_function_signature* d’une fonction anonyme définit les noms et éventuellement les types des paramètres formels de la fonction anonyme. L’étendue des paramètres de la fonction anonyme est la *anonymous_function_body*. ([Étendues](basic-concepts.md#scopes)) ainsi que la liste de paramètres (si fournie) anonymes-méthode-body constitue un espace de déclaration ([déclarations](basic-concepts.md#declarations)). Il est donc une erreur de compilation pour le nom d’un paramètre de la fonction anonyme pour correspondre au nom d’une variable locale, la constante locale ou le paramètre dont l’étendue inclut la *anonymous_method_expression* ou *lambda_ expression*.

Si une fonction anonyme a une *explicit_anonymous_function_signature*, puis l’ensemble des types délégués compatibles et types arborescence d’expression est limitée à ceux qui ont les mêmes types de paramètres et les modificateurs dans le même ordre. Contrairement aux conversions de groupe (méthode) ([conversions de groupes de méthode](conversions.md#method-group-conversions)), contra-variance des types de paramètres de fonction anonyme n’est pas pris en charge. Si une fonction anonyme n’est pas un *anonymous_function_signature*, puis l’ensemble des types délégués compatibles et types arborescence d’expression est limitée à ceux qui n’ont pas `out` paramètres.

Notez qu’un *anonymous_function_signature* ne peut pas inclure des attributs ou un tableau de paramètres. Néanmoins, une *anonymous_function_signature* peut être compatible avec un type de délégué dont la liste paramètre contient un tableau de paramètres.

Notez également que la conversion vers un type arborescence d’expression, même si compatible, peut encore échouer au moment de la compilation ([types arborescence d’Expression](types.md#expression-tree-types)).

### <a name="anonymous-function-bodies"></a>Corps de fonction anonyme

Le corps (*expression* ou *bloc*) d’une fonction anonyme est soumis aux règles suivantes :

*  Si la fonction anonyme inclut une signature, les paramètres spécifiés dans la signature sont disponibles dans le corps. Si la fonction anonyme n’a aucune signature elle peut être convertie à un type de délégué ou d’un type d’expression ayant des paramètres ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)), mais les paramètres ne sont pas accessibles dans le corps.
*  À l’exception de `ref` ou `out` paramètres spécifiés dans la signature (le cas échéant) de l’englobante la plus proche fonction anonyme, il est une erreur de compilation pour le corps d’accéder à un `ref` ou `out` paramètre.
*  Lorsque le type de `this` est un type struct, c’est une erreur de compilation pour le corps d’accéder à `this`. Cela est vrai si l’accès est explicite (comme dans `this.x`) ou implicites (comme dans `x` où `x` est un membre d’instance du struct). Cette règle simplement interdit l’accès et n’affecte pas si les résultats de recherche de membres dans un membre du struct.
*  Le corps a accès aux variables externes ([Outer variables](expressions.md#outer-variables)) de la fonction anonyme. Accès d’une variable externe fait référence à l’instance de la variable qui est active au moment où le *lambda_expression* ou *anonymous_method_expression* est évaluée ([version d’évaluation de expressions de fonction anonyme](expressions.md#evaluation-of-anonymous-function-expressions)).
*  C’est une erreur de compilation pour le corps contienne un `goto` instruction, `break` instruction, ou `continue` instruction dont la cible est en dehors du corps ou dans le corps d’une fonction anonyme contenue.
*  Un `return` retourne le contrôle à partir d’un appel à l’englobante la plus proche instruction dans le corps de la fonction anonyme, pas à partir de la fonction membre englobante. Une expression spécifiée dans un `return` instruction doit être implicitement convertible au type de retour du type de délégué ou de type arborescence d’expression à laquelle l’englobante la plus proche *lambda_expression* ou *anonymous_ method_expression* est converti ([conversions de fonctions anonymes](conversions.md#anonymous-function-conversions)).

Il est explicitement non spécifiée s’il existe un moyen d’exécuter le bloc d’une fonction anonyme autre que par le biais d’évaluation et l’appel de la *lambda_expression* ou *anonymous_method_expression*. En particulier, le compilateur peut choisir d’implémenter une fonction anonyme en synthétisant une ou plusieurs méthodes ou des types. Les noms de ces éléments synthétisées doivent être d’un formulaire réservé à l’utilisation par le compilateur.

### <a name="overload-resolution-and-anonymous-functions"></a>Résolution de surcharge et les fonctions anonymes

Les fonctions anonymes dans une liste d’arguments participent à l’inférence de type et la résolution de surcharge. Reportez-vous à [l’inférence de Type](expressions.md#type-inference) et [la résolution de surcharge](expressions.md#overload-resolution) pour connaître les règles exactes.

L’exemple suivant illustre l’effet de fonctions anonymes sur la résolution de surcharge.

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

Le `ItemList<T>` classe a deux `Sum` méthodes. Chacun prend un `selector` argument, qui extrait la valeur à la somme sur un élément de liste. La valeur extraite peut être soit un `int` ou un `double` et la somme résultante est de même un `int` ou un `double`.

Le `Sum` méthodes pourraient par exemple être utilisées pour calculer la somme à partir d’une liste des lignes de détails dans un ordre.

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

Dans la première invocation de `orderDetails.Sum`, à la fois `Sum` méthodes sont applicables, car la fonction anonyme `d => d. UnitCount` est compatible avec les deux `Func<Detail,int>` et `Func<Detail,double>`. Toutefois, la résolution de surcharge sélectionne la première `Sum` (méthode), car la conversion en `Func<Detail,int>` est meilleure que la conversion en `Func<Detail,double>`.

Dans le deuxième appel de `orderDetails.Sum`, seul le deuxième `Sum` méthode s’applique, car la fonction anonyme `d => d.UnitPrice * d.UnitCount` produit une valeur de type `double`. Par conséquent, la surcharge résolution choisit la seconde `Sum` méthode pour cet appel.

### <a name="anonymous-functions-and-dynamic-binding"></a>Liaison dynamique et fonctions anonymes

Une fonction anonyme ne peut pas être un destinataire, un argument ou un opérande d’une opération dynamique liée.

### <a name="outer-variables"></a>Variables externes

Toute variable locale, un paramètre de valeur ou un tableau de paramètres dont l’étendue inclut la *lambda_expression* ou *anonymous_method_expression* est appelée un ***variable externe*** de la fonction anonyme. Dans une fonction membre d’instance d’une classe, le `this` valeur est considérée comme un paramètre de valeur et est une variable externe d’une fonction anonyme contenue dans la fonction membre.

#### <a name="captured-outer-variables"></a>Capture des variables externes

Lorsqu’une variable externe est référencée par une fonction anonyme, la variable externe est dite avoir été ***capturées*** par la fonction anonyme. En règle générale, la durée de vie d’une variable locale est limitée à l’exécution de l’instruction auquel il est associé ou le bloc ([variables locales](variables.md#local-variables)). Toutefois, la durée de vie d’une variable externe capturée est étendue au moins jusqu'à ce que le délégué ou arborescence de l’expression créée à partir de la fonction anonyme devient éligible pour le garbage collection.

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
la variable locale `x` est capturé par la fonction anonyme et la durée de vie de `x` est étendue au moins jusqu'à ce que le délégué retourné par `F` devient éligible pour le garbage collection (qui ne se produit pas jusqu'à la fin de le programme). Étant donné que chaque appel de la fonction anonyme opère sur la même instance de `x`, la sortie de l’exemple est :
```
1
2
3
```

Lorsqu’une variable locale ou un paramètre de valeur est capturé par une fonction anonyme, la variable locale ou du paramètre est n’est plus considéré comme une variable fixe ([variables fixes et déplaçables](unsafe-code.md#fixed-and-moveable-variables)), mais est considéré à la place comme un moveable variable. Par conséquent, les `unsafe` le code qui prend l’adresse d’une variable capturée externe doit tout d’abord utiliser la `fixed` instruction pour corriger la variable.

Notez que contrairement à une variable de rejet, une variable locale capturée peut être exposée simultanément à plusieurs threads d’exécution.

#### <a name="instantiation-of-local-variables"></a>Instanciation des variables locales

Une variable locale est considéré comme étant ***instancié*** lorsque l’exécution entre l’étendue de la variable. Par exemple, quand la méthode suivante est appelée, la variable locale `x` est instancié et initialisé trois fois, une fois pour chaque itération de la boucle.

```csharp
static void F() {
    for (int i = 0; i < 3; i++) {
        int x = i * 2 + 1;
        ...
    }
}
```

Toutefois, déplacer la déclaration de `x` à l’extérieur les résultats de la boucle dans une instanciation unique de `x`:
```csharp
static void F() {
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        ...
    }
}
```

Lorsque l’élément ne pas capturé, il n’existe aucun moyen d’observer exactement la fréquence à laquelle une variable locale est instanciée, car les durées de vie des instanciations sont disjointes, il est possible pour chaque instanciation simplement d’utiliser le même emplacement de stockage. Toutefois, lorsqu’une fonction anonyme capture une variable locale, les effets de l’instanciation apparu.

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
```
1
3
5
```

Toutefois, lorsque la déclaration de `x` est déplacé en dehors de la boucle :
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
La sortie est :
```
5
5
5
```

Si une boucle for déclare une variable d’itération, cette variable lui-même est considéré comme déclaré en dehors de la boucle. Par conséquent, si l’exemple est modifié pour capturer la variable d’itération :

```csharp
static D[] F() {
    D[] result = new D[3];
    for (int i = 0; i < 3; i++) {
        result[i] = () => { Console.WriteLine(i); };
    }
    return result;
}
```
seule une instance de la variable d’itération est capturée, ce qui génère la sortie :
```
3
3
3
```

Il est possible pour les délégués de fonction anonyme à partager certaines variables capturées encore avoir des instances distinctes d’autres personnes. Par exemple, si `F` est remplacée par
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
les trois délégués capturer la même instance de `x` , mais des instances distinctes de `y`, et la sortie est :
```
1 1
2 1
3 1
```

Séparer les fonctions anonymes peuvent capturer la même instance d’une variable externe. Dans l'exemple :
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
les deux fonctions anonymes capturer la même instance de la variable locale `x`, et ils peuvent ainsi « communiquer » via cette variable. La sortie de l’exemple est :
```
5
10
```

### <a name="evaluation-of-anonymous-function-expressions"></a>Évaluation des expressions de fonction anonyme

Une fonction anonyme `F` doit toujours être convertie en un type délégué `D` ou un type d’arborescence expression `E`, directement ou via l’exécution d’une expression de création de délégué `new D(F)`. Cette conversion détermine le résultat de la fonction anonyme, comme décrit dans [conversions de fonctions anonymes](conversions.md#anonymous-function-conversions).

## <a name="query-expressions"></a>Expressions de requête

***Expressions de requête*** fournissent une syntaxe de langage intégrée pour les requêtes qui est similaire aux langages de requête hiérarchiques et relationnelles telles que SQL ou XQuery.

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

Une expression de requête commence par un `from` clause et se termine par soit un `select` ou `group` clause. Initial `from` clause peut être suivie de zéro ou plusieurs `from`, `let`, `where`, `join` ou `orderby` clauses. Chaque `from` clause est un générateur de présentation un ***variable de portée*** qui couvre les éléments d’un ***séquence***. Chaque `let` clause introduit une variable de portée qui représente une valeur calculée au moyen de variables de portée précédente. Chaque `where` clause est un filtre qui exclut des éléments à partir du résultat. Chaque `join` clause compare les clés spécifiées de la séquence source avec des clés d’une autre séquence, produisant des paires correspondantes. Chaque `orderby` clause réorganise les éléments en fonction de critères spécifiés. La dernière `select` ou `group` clause spécifie la forme du résultat en termes des variables de plage. Enfin, un `into` clause peut être utilisée pour « splice « requêtes en traitant les résultats d’une requête en tant que générateur dans une requête suivante.

### <a name="ambiguities-in-query-expressions"></a>Ambiguïtés dans les expressions de requête

Expressions de requête contiennent un nombre de « mots clés contextuels », par exemple, les identificateurs qui ont une signification spéciale dans un contexte donné. Plus précisément, il s’agit de `from`, `where`, `join`, `on`, `equals`, `into`, `let`, `orderby`, `ascending`, `descending`, `select`, `group` et `by`. Afin d’éviter les ambiguïtés dans les expressions de requête causées par une utilisation mixte de ces identificateurs en tant que mots clés ou des noms simples, ces identificateurs sont considérés comme mots clés lors de n’importe où dans une expression de requête.

À cet effet, une expression de requête est toute expression qui commence par «`from identifier`« suivie de n’importe quel jeton à l’exception »`;`«, »`=`« ou »`,`».

Pour pouvoir utiliser ces mots comme identificateurs dans une expression de requête, ils peuvent être le préfixe «`@`» ([identificateurs](lexical-structure.md#identifiers)).

### <a name="query-expression-translation"></a>Traduction des expressions de requête

Le langage c# ne spécifie pas la sémantique d’exécution d’expressions de requête. Au lieu de cela, les expressions de requête sont traduites en appels de méthodes qui respectent le *modèle d’expression de requête* ([le modèle d’expression de requête](expressions.md#the-query-expression-pattern)). Plus précisément, les expressions de requête sont traduites en appels de méthodes nommées `Where`, `Select`, `SelectMany`, `Join`, `GroupJoin`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `GroupBy`, et `Cast`. Ces méthodes sont supposées comporter des signatures particuliers et les types de résultats, comme décrit dans [le modèle d’expression de requête](expressions.md#the-query-expression-pattern). Ces méthodes peuvent être des méthodes d’instance de l’objet en cours d’interrogation ou des méthodes d’extension qui sont externes à l’objet, et qu’ils implémentent l’exécution réelle de la requête.

La traduction à partir d’expressions de requête aux appels de méthode est un mappage syntaxique qui se produit avant la liaison de n’importe quel type ou la résolution de surcharge a été effectuée. La traduction est garantie est syntaxiquement correct, mais il n’est pas garanti pour produire le code c# sémantiquement correct. Après la traduction d’expressions de requête, les appels de méthode qui en résulte sont traités comme des appels de méthode normal et cela peut révéler à son tour les erreurs, par exemple si les méthodes n’existent pas, si les arguments ont des types incorrects, ou si les méthodes sont génériques et inférence de type échoue.

Une expression de requête est traitée par l’application à plusieurs reprises les traductions suivantes jusqu'à ce qu’aucune réduction supplémentaire n’est possibles. Les traductions sont répertoriées dans l’ordre d’application : chaque section part du principe que les traductions dans les sections précédentes ont été effectuées de manière exhaustive, et une fois atteint, une section ne sera pas plus tard être revisitée lors du traitement de la même expression de requête.

Affectation à des variables de plage n’est pas autorisée dans les expressions de requête. Toutefois une implémentation c# est autorisée à n'appliquer pas toujours cette restriction, dans la mesure où cela est parfois pas possible avec le schéma de conversion syntaxique présenté ici.

Certaines traductions injectent des variables de portée avec des identificateurs transparents dénotés par `*`. Les propriétés spéciales d’identificateurs transparents sont abordées plus en détail dans [identificateurs Transparent](expressions.md#transparent-identifiers).

#### <a name="select-and-groupby-clauses-with-continuations"></a>Clauses SELECT et groupby avec des continuations

Une expression de requête avec une continuation
```csharp
from ... into x ...
```
est traduite en
```csharp
from x in ( from ... ) ...
```

Les traductions dans les sections suivantes supposent que les requêtes n’ont pas `into` continuations.

L’exemple
```csharp
from c in customers
group c by c.Country into g
select new { Country = g.Key, CustCount = g.Count() }
```
est traduite en
```csharp
from g in
    from c in customers
    group c by c.Country
select new { Country = g.Key, CustCount = g.Count() }
```
la traduction finale qui est
```csharp
customers.
GroupBy(c => c.Country).
Select(g => new { Country = g.Key, CustCount = g.Count() })
```

#### <a name="explicit-range-variable-types"></a>Types de variables de plage explicite

Un `from` clause qui spécifie explicitement un type de variable de plage
```csharp
from T x in e
```
est traduite en
```csharp
from x in ( e ) . Cast < T > ( )
```

Un `join` clause qui spécifie explicitement un type de variable de plage
```
join T x in e on k1 equals k2
```
est traduite en
```
join x in ( e ) . Cast < T > ( ) on k1 equals k2
```

Les traductions dans les sections suivantes supposent que les requêtes ne disposent aucun type de variable de portée explicite.

L’exemple
```csharp
from Customer c in customers
where c.City == "London"
select c
```
est traduite en
```csharp
from c in customers.Cast<Customer>()
where c.City == "London"
select c
```
la traduction finale qui est
```csharp
customers.
Cast<Customer>().
Where(c => c.City == "London")
```

Types de variables de plage explicites sont utiles pour l’interrogation des collections qui implémentent le non générique `IEnumerable` interface, mais pas le générique `IEnumerable<T>` interface. Dans l’exemple ci-dessus, ce serait le cas si `customers` étaient de type `ArrayList`.

#### <a name="degenerate-query-expressions"></a>Expressions de requête dégénérée

Une expression de requête sous la forme
```csharp
from x in e select x
```
est traduite en
```csharp
( e ) . Select ( x => x )
```

L’exemple
```csharp
from c in customers
select c
```
est traduite en
```csharp
customers.Select(c => c)
```

Une expression de requête dégénérée est une façon triviale sélectionne les éléments de la source. Une étape ultérieure de la traduction supprime les requêtes dégénérées introduites dans les autres étapes de traduction en les remplaçant par leur source. Il convient toutefois, pour vous assurer que le résultat d’une requête expression n’est jamais l’objet de source lui-même, qui serait révèlent le type et l’identité de la source au client de la requête. Par conséquent, cette étape protège dégénérées requêtes écrites directement dans le code source en appelant explicitement `Select` sur la source. Il revient ensuite les implémenteurs de `Select` et d’autres opérateurs de requête pour vous assurer que ces méthodes retournent jamais l’objet de source lui-même.

#### <a name="from-let-where-join-and-orderby-clauses"></a>À partir, where, join et orderby clauses let

Une expression de requête avec un second `from` clause suivie d’un `select` clause
```csharp
from x1 in e1
from x2 in e2
select v
```
est traduite en
```csharp
( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => v )
```

Une expression de requête avec un second `from` clause suivie d’un élément autre qu’un `select` clause :

```csharp
from x1 in e1
from x2 in e2
...
```
est traduite en
```csharp
from * in ( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => new { x1 , x2 } )
...
```

Une expression de requête avec un `let` clause
```csharp
from x in e
let y = f
...
```
est traduite en
```csharp
from * in ( e ) . Select ( x => new { x , y = f } )
...
```

Une expression de requête avec un `where` clause
```csharp
from x in e
where f
...
```
est traduite en
```csharp
from x in ( e ) . Where ( x => f )
...
```

Une expression de requête avec un `join` clause sans un `into` suivie d’un `select` clause
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
select v
```
est traduite en
```csharp
( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => v )
```

Une expression de requête avec un `join` clause sans un `into` suivie d’un élément autre qu’un `select` clause
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
...
```
est traduite en
```csharp
from * in ( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => new { x1 , x2 })
...
```

Une expression de requête avec un `join` clause avec un `into` suivie d’un `select` clause
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
select v
```
est traduite en
```csharp
( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => v )
```

Une expression de requête avec un `join` clause avec un `into` suivie d’un élément autre qu’un `select` clause
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
...
```
est traduite en
```csharp
from * in ( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => new { x1 , g })
...
```

Une expression de requête avec un `orderby` clause
```csharp
from x in e
orderby k1 , k2 , ..., kn
...
```
est traduite en
```csharp
from x in ( e ) . 
OrderBy ( x => k1 ) . 
ThenBy ( x => k2 ) .
... .
ThenBy ( x => kn )
...
```

Si un classement spécifie un `descending` indicateur de direction, un appel à `OrderByDescending` ou `ThenByDescending` est généré à la place.

Les traductions suivantes supposent qu’il n’y aucun `let`, `where`, `join` ou `orderby` clauses et pas plus de celui qui initial `from` clause dans chaque expression de requête.

L’exemple
```csharp
from c in customers
from o in c.Orders
select new { c.Name, o.OrderID, o.Total }
```
est traduite en
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
est traduite en
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```
la traduction finale qui est
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.OrderID, x.o.Total })
```
où `x` est un identificateur généré par le compilateur qui est sinon invisible et inaccessible.

L’exemple
```csharp
from o in orders
let t = o.Details.Sum(d => d.UnitPrice * d.Quantity)
where t >= 1000
select new { o.OrderID, Total = t }
```
est traduite en
```csharp
from * in orders.
    Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) })
where t >= 1000 
select new { o.OrderID, Total = t }
```
la traduction finale qui est
```csharp
orders.
Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) }).
Where(x => x.t >= 1000).
Select(x => new { x.o.OrderID, Total = x.t })
```
où `x` est un identificateur généré par le compilateur qui est sinon invisible et inaccessible.

L’exemple
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
select new { c.Name, o.OrderDate, o.Total }
```
est traduite en
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
est traduite en
```csharp
from * in customers.
    GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
        (c, co) => new { c, co })
let n = co.Count()
where n >= 10 
select new { c.Name, OrderCount = n }
```
la traduction finale qui est
```csharp
customers.
GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
    (c, co) => new { c, co }).
Select(x => new { x, n = x.co.Count() }).
Where(y => y.n >= 10).
Select(y => new { y.x.c.Name, OrderCount = y.n)
```
où `x` et `y` sont des identificateurs générés par le compilateur qui ne sont autrement invisibles et inaccessible.

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

Une expression de requête sous la forme
```csharp
from x in e select v
```
est traduite en
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
est simplement traduit
```csharp
customers.Where(c => c.City == "London")
```

#### <a name="groupby-clauses"></a>Clauses GroupBy

Une expression de requête sous la forme
```csharp
from x in e group v by k
```
est traduite en
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
est traduite en
```csharp
customers.
GroupBy(c => c.Country, c => c.Name)
```

#### <a name="transparent-identifiers"></a>Identificateurs transparents

Certaines traductions injectent des variables de portée avec ***identificateurs transparents*** dénoté par `*`. Identificateurs transparents ne sont pas une fonctionnalité de langage approprié ; ils existent uniquement comme une étape intermédiaire dans le processus de traduction d’expression de requête.

Lorsqu’une traduction de requête injecte un identificateur transparent, étapes de traduction propagent davantage l’identificateur transparent dans les fonctions anonymes et les initialiseurs d’objet. Dans ces contextes, identificateurs transparents ont le comportement suivant :

*  Lorsqu’un identificateur transparent se produit en tant que paramètre dans une fonction anonyme, les membres du type anonyme associé sont automatiquement dans la portée dans le corps de la fonction anonyme.
*  Lorsqu’un membre avec un identificateur transparent est dans la portée, les membres de ce membre sont dans l’étendue ainsi.
*  En cas d’un identificateur transparent comme un déclarateur de membre dans un initialiseur d’objet anonyme, elle introduit un membre avec un identificateur transparent.
*  Dans les étapes de traduction décrites ci-dessus, les identificateurs transparents sont toujours introduits avec des types anonymes, avec l’intention de capturer plusieurs variables de plage en tant que membres d’un objet unique. Une implémentation du langage c# est autorisée à utiliser un autre mécanisme que les types anonymes pour regrouper plusieurs variables de plage. Les exemples de traduction suivants supposent que les types anonymes sont utilisés et affichent le degré de transparence identificateurs peuvent être traduites de suite.

L’exemple
```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.Total }
```
est traduite en
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.Total }
```

qui est davantage de traduire
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(* => o.Total).
Select(* => new { c.Name, o.Total })
```
ce qui, lorsque les identificateurs transparents sont effacés, équivaut à
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.Total })
```
où `x` est un identificateur généré par le compilateur qui est sinon invisible et inaccessible.

L’exemple
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
est traduite en
```csharp
from * in customers.
    Join(orders, c => c.CustomerID, o => o.CustomerID, 
        (c, o) => new { c, o })
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
qui est davantage réduite à
```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID, (c, o) => new { c, o }).
Join(details, * => o.OrderID, d => d.OrderID, (*, d) => new { *, d }).
Join(products, * => d.ProductID, p => p.ProductID, (*, p) => new { *, p }).
Select(* => new { c.Name, o.OrderDate, p.ProductName })
```
la traduction finale qui est
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
où `x`, `y`, et `z` sont des identificateurs générés par le compilateur qui ne sont autrement invisibles et inaccessible.

### <a name="the-query-expression-pattern"></a>Le modèle d’expression de requête

Le ***modèle d’expression de requête*** établit un modèle de méthodes types peuvent implémenter pour prendre en charge les expressions de requête. Étant donné que les expressions de requête sont converties en appels de méthode au moyen d’un mappage syntaxique, les types ont une flexibilité considérable dans la manière dont elles implémentent le modèle d’expression de requête. Par exemple, les méthodes du modèle peuvent être implémentés en tant que méthodes d’instance ou en tant que méthodes d’extension, car les deux ont la même syntaxe d’appel et les méthodes peuvent demander des délégués ou les arborescences d’expression, car les fonctions anonymes sont convertibles vers les deux.

La forme recommandée d’un type générique `C<T>` que prend en charge le modèle d’expression de requête est indiqué ci-dessous. Un type générique est utilisé afin d’illustrer les relations appropriées entre les types de paramètre et le résultat, mais il est possible d’implémenter le modèle pour également les types non génériques.

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

Les méthodes ci-dessus utilisent les types délégués génériques `Func<T1,R>` et `Func<T1,T2,R>`, mais pourrait tout aussi bien avoir utilisées autres types d’arborescence délégué ou une expression avec les mêmes relations dans les types de paramètre et le résultat.

Notez que la relation recommandée entre `C<T>` et `O<T>` qui garantit que le `ThenBy` et `ThenByDescending` méthodes sont disponibles uniquement sur le résultat d’une `OrderBy` ou `OrderByDescending`. Notez également la forme recommandée du résultat de `GroupBy` --une séquence de séquences, où chaque séquence interne a un autre `Key` propriété.

Le `System.Linq` espace de noms fournit une implémentation du modèle d’opérateur de requête pour n’importe quel type qui implémente le `System.Collections.Generic.IEnumerable<T>` interface.

## <a name="assignment-operators"></a>Opérateurs d'assignation

Les opérateurs d’assignation affecter une nouvelle valeur à une variable, une propriété, un événement ou un élément d’indexeur.

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

L’opérande gauche d’une assignation doit être une expression classifiée comme une variable, un accès à la propriété, accès à un indexeur ou un accès à l’événement.

Le `=` opérateur est appelé le ***opérateur d’assignation simple***. Il attribue la valeur de l’opérande droit à l’élément de variable, propriété ou indexeur indiqué par l’opérande de gauche. L’opérande gauche de l’opérateur d’assignation simple est peut-être pas accès à un événement (sauf indication contraire dans [les événements de type champ](classes.md#field-like-events)). L’opérateur d’assignation simple est décrit dans [assignation Simple](expressions.md#simple-assignment).

Les opérateurs d’assignation autres que le `=` opérateur sont appelés les ***opérateurs d’assignation composée***. Ces opérateurs effectuent l’opération indiquée sur les deux opérandes et puis affectez la valeur obtenue à l’élément de variable, propriété ou indexeur indiqué par l’opérande de gauche. Les opérateurs d’assignation composée sont décrits dans [assignation composée](expressions.md#compound-assignment).

Le `+=` et `-=` avec une expression d’accès événement en tant que l’opérande de gauche sont appelés les *opérateurs d’assignation événement*. Aucun autre opérateur d’assignation n’est valide avec un accès à l’événement en tant que l’opérande de gauche. Les opérateurs d’assignation événement sont décrites dans [assignation d’événement](expressions.md#event-assignment).

Les opérateurs d’assignation sont associatifs sur leur droite, ce qui signifie que les opérations sont groupées de droite à gauche. Par exemple, une expression sous la forme `a = b = c` est évalué comme `a = (b = c)`.

### <a name="simple-assignment"></a>Assignation simple

Le `=` opérateur est appelé l’opérateur d’assignation simple.

Si l’opérande gauche d’une assignation simple est au format `E.P` ou `E[Ei]` où `E` a le type de compilation `dynamic`, l’attribution est dynamiquement lié ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas le type de compilation de l’expression d’assignation est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution en fonction du type d’exécution de `E`.

Dans une assignation simple, l’opérande de droite doit être une expression qui est implicitement convertible dans le type de l’opérande gauche. L’opération assigne la valeur de l’opérande droit à l’élément de variable, propriété ou indexeur indiqué par l’opérande de gauche.

Le résultat d’une expression d’assignation simple est la valeur assignée à l’opérande de gauche. Le résultat a le même type que l’opérande de gauche et est toujours considéré comme une valeur.

Si l’opérande gauche est un accès de propriété ou un indexeur, la propriété ou l’indexeur doit avoir un `set` accesseur. Si ce n’est pas le cas, une erreur au moment de la liaison se produit.

Le traitement de l’exécution d’une assignation simple sous la forme `x = y` se compose des étapes suivantes :

*  Si `x` est classé en tant que variable :
   * `x` est évalué pour produire la variable.
   * `y` est évaluée et, si nécessaire, converti vers le type de `x` via une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).
   * Si la variable donnée par `x` est un élément d’un *reference_type*, une vérification de l’exécution est effectuée pour vous assurer que la valeur calculée pour `y` est compatible avec l’instance de tableau dont `x` est un élément. La vérification réussit si `y` est `null`, ou si une conversion de référence implicite ([conversions de référence implicite](conversions.md#implicit-reference-conversions)) existe dans le type réel de l’instance référencée par `y` au type d’élément réel de l’instance de tableau contenant `x`. Sinon, une exception `System.ArrayTypeMismatchException` est levée.
   * La valeur résultant de l’évaluation et la conversion de `y` est stocké dans l’emplacement indiqué par l’évaluation de `x`.
*  Si `x` est classé comme un accès de propriété ou l’indexeur :
   * L’expression d’instance (si `x` n’est pas `static`) et la liste d’arguments (si `x` est l’accès à un indexeur) associé à `x` sont évaluées, et les résultats sont utilisés dans l’ultérieure `set` invocation de l’accesseur.
   * `y` est évaluée et, si nécessaire, converti vers le type de `x` via une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).
   * Le `set` l’accesseur de `x` est appelé avec la valeur calculée de `y` en tant que son `value` argument.

Les règles de variance co tableau ([covariance de tableau](arrays.md#array-covariance)) permettent à une valeur d’un type tableau `A[]` soit une référence à une instance d’un type tableau `B[]`, à condition qu’existe une conversion de référence implicite de `B` à `A`. En raison de ces règles, l’assignation à un élément d’un *reference_type* requiert une vérification de l’exécution pour vous assurer que la valeur assignée est compatible avec l’instance de tableau. Dans l’exemple
```csharp
string[] sa = new string[10];
object[] oa = sa;

oa[0] = null;               // Ok
oa[1] = "Hello";            // Ok
oa[2] = new ArrayList();    // ArrayTypeMismatchException
```
la dernière assignation entraîne un `System.ArrayTypeMismatchException` levée, car une instance de `ArrayList` ne peut pas être stocké dans un élément d’un `string[]`.

Lorsqu’une propriété ou un indexeur déclaré dans un *struct_type* est la cible d’une assignation, l’expression d’instance associée de la propriété ou l’accès à l’indexeur doit être classée en tant que variable. Si l’expression d’instance est classifiée en tant que valeur, une erreur au moment de la liaison se produit. Raison de [l’accès au membre](expressions.md#member-access), la même règle s’applique également aux champs.

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
Dans l’exemple
```csharp
Point p = new Point();
p.X = 100;
p.Y = 100;
Rectangle r = new Rectangle();
r.A = new Point(10, 10);
r.B = p;
```
les affectations à `p.X`, `p.Y`, `r.A`, et `r.B` sont autorisées car `p` et `r` sont des variables. Toutefois, dans l’exemple
```csharp
Rectangle r = new Rectangle();
r.A.X = 10;
r.A.Y = 10;
r.B.X = 100;
r.B.Y = 100;
```
les affectations sont non valides depuis `r.A` et `r.B` ne sont pas des variables.

### <a name="compound-assignment"></a>Assignation composée

Si l’opérande gauche d’une assignation composée est au format `E.P` ou `E[Ei]` où `E` a le type de compilation `dynamic`, l’attribution est dynamiquement lié ([liaison dynamique](expressions.md#dynamic-binding)). Dans ce cas le type de compilation de l’expression d’assignation est `dynamic`, et la résolution décrite ci-dessous aura lieu au moment de l’exécution en fonction du type d’exécution de `E`.

Une opération de la forme `x op= y` est traitée en appliquant la résolution de surcharge d’opérateur binaire ([opérateur binaire de résolution de surcharge](expressions.md#binary-operator-overload-resolution)) comme si l’opération a été écrit `x op y`. Ensuite,

*  Si le type de retour de l’opérateur sélectionné est implicitement convertible au type de `x`, l’opération est évaluée en tant que `x = x op y`, sauf que `x` est évaluée une seule fois.
*  Sinon, si l’opérateur sélectionné est un opérateur prédéfini, si le type de retour de l’opérateur sélectionné est explicitement convertible au type de `x`et si `y` est implicitement convertible au type de `x` ou l’opérateur est un Shift, opérateur, puis l’opération est évaluée en tant que `x = (T)(x op y)`, où `T` est le type de `x`, sauf que `x` est évaluée une seule fois.
*  Sinon, l’assignation composée n’est pas valide, et se produit une erreur au moment de la liaison.

Le terme « évalué une seule fois » signifie que dans la version d’évaluation de `x op y`, les résultats de toutes les expressions constitutifs de `x` sont temporairement enregistrés et puis réutilisées lors de l’exécution de l’assignation à `x`. Par exemple, dans l’assignation `A()[B()] += C()`, où `A` est une méthode retournant `int[]`, et `B` et `C` sont des méthodes qui retournent `int`, les méthodes sont appelées une seule fois, dans l’ordre `A`, `B`, `C`.

Lorsque l’opérande gauche d’une assignation composée est un accès à la propriété ou indexeur, la propriété ou l’indexeur doit avoir un à la fois un `get` accesseur et un `set` accesseur. Si ce n’est pas le cas, une erreur au moment de la liaison se produit.

La deuxième règle ci-dessus permet `x op= y` soit évaluée comme `x = (T)(x op y)` dans certains contextes. La règle existe telles que les opérateurs prédéfinis peuvent être utilisés comme opérateurs composés lorsque l’opérande gauche est de type `sbyte`, `byte`, `short`, `ushort`, ou `char`. Même lorsque les deux arguments sont d’un de ces types, les opérateurs prédéfinis produisent un résultat de type `int`, comme décrit dans [promotions numériques binaires](expressions.md#binary-numeric-promotions). Par conséquent, sans un cast il pas serait possible d’affecter le résultat à l’opérande de gauche.

L’effet intuitif de la règle pour les opérateurs prédéfinis est simplement que `x op= y` est autorisé si les deux de `x op y` et `x = y` sont autorisées. Dans l’exemple
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
la raison intuitive pour chaque erreur est qu’une assignation simple correspondante aurait également été une erreur.

Cela signifie également que les opérations prennent en charge l’assignation composée levé operations. Dans l’exemple
```csharp
int? i = 0;
i += 1;             // Ok
```
l’opérateur levé `+(int?,int?)` est utilisé.

### <a name="event-assignment"></a>Assignation d’événement

Si l’opérande de gauche d’un `+=` ou `-=` opérateur est classé comme un accès à l’événement, puis l’expression est évaluée comme suit :

*  L’expression d’instance, le cas échéant, de l’accès à l’événement est évaluée.
*  L’opérande de droite de la `+=` ou `-=` opérateur est évalué et, si nécessaire, converti vers le type de l’opérande gauche via une conversion implicite ([conversions implicites](conversions.md#implicit-conversions)).
*  Un accesseur d’événement de l’événement est appelé, avec la liste d’arguments composée de l’opérande de droite, après évaluation et, si nécessaire, conversion. Si l’opérateur a été `+=`, le `add` accesseur est appelé ; si l’opérateur a été `-=`, le `remove` accesseur est appelé.

Une expression d’assignation événement ne produit pas de valeur. Par conséquent, une expression d’assignation événement est valide uniquement dans le contexte d’un *statement_expression* ([instructions d’Expression](statements.md#expression-statements)).

## <a name="expression"></a>Expression

Un *expression* est soit un *non_assignment_expression* ou un *attribution*.

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

## <a name="constant-expressions"></a>Expressions constantes

Un *constant_expression* est une expression qui peut être complètement évaluée au moment de la compilation.

```antlr
constant_expression
    : expression
    ;
```

Une expression constante doit être le `null` littéral ou une valeur avec l’un des types suivants : `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char` , `float`, `double`, `decimal`, `bool`, `object`, `string`, ou tout type d’énumération. Uniquement les constructions suivantes sont autorisées dans les expressions constantes :

*  Littéraux (y compris le `null` littérale).
*  Références à `const` membres de classe et les types struct.
*  Références aux membres des types énumération.
*  Références à `const` paramètres ou variables locales
*  Entre parenthèses sous-expressions, qui sont eux-mêmes des expressions constantes.
*  Expressions de cast, à condition que le type cible est un des types mentionnés ci-dessus.
*  `checked` et `unchecked` expressions
*  Expressions de valeur par défaut
*  Expressions Nameof
*  Prédéfinis `+`, `-`, `!`, et `~` opérateurs unaires.
*  Prédéfinis `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, `&&`, `||`, `==`, `!=`, `<`, `>`, `<=`, et `>=` fourni d’opérateurs binaires, chaque opérande est d’un type répertorié ci-dessus.
*  Le `?:` opérateur conditionnel.

Les conversions suivantes sont autorisées dans les expressions constantes :

*  Conversions d’identité
*  Conversions numériques
*  Conversions d’énumération
*  Conversions d’expression constante
*  Conversions de référence implicites et explicites, fournies que la source des conversions est une expression constante qui correspond à la valeur null.

D’autres conversions, y compris le boxing, les conversions de référence unboxing et implicites des valeurs non null ne sont pas autorisées dans les expressions constantes. Exemple :
```csharp
class C {
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
l’initialisation de i est une erreur car une conversion boxing est nécessaire. L’initialisation de str est une erreur car une conversion de référence implicite à partir d’une valeur non null est nécessaire.

Chaque fois qu’une expression satisfait les spécifications répertoriées ci-dessus, l’expression est évaluée au moment de la compilation. Cela est vrai même si l’expression est une sous-expression d’une expression plus longue qui contient des constructions non constantes.

L’évaluation de la compilation d’expressions constantes utilise les mêmes règles que l’évaluation de l’exécution des expressions non constantes, sauf que là où l’évaluation au moment de l’exécution aurait levé une exception, l’évaluation de la compilation provoque une erreur de compilation se produisent.

Sauf si une expression constante est explicitement placée dans un `unchecked` contexte, les dépassements de capacité qui se produisent dans les opérations arithmétiques de type intégral et les conversions lors de l’évaluation de la compilation de l’expression toujours provoquer des erreurs de compilation ([Expressions constantes](expressions.md#constant-expressions)).

Expressions constantes se produisent dans les contextes répertoriés ci-dessous. Dans ces contextes, une erreur de compilation se produit si une expression ne peut pas être complètement évaluée au moment de la compilation.

*  Déclarations de constante ([constantes](classes.md#constants)).
*  Déclarations de membre d’énumération ([membres Enum](enums.md#enum-members)).
*  Arguments de listes de paramètres formels par défaut ([paramètres de méthode](classes.md#method-parameters))
*  `case` étiquettes d’un `switch` instruction ([l’instruction switch](statements.md#the-switch-statement)).
*  `goto case` instructions ([l’instruction goto](statements.md#the-goto-statement)).
*  Longueurs des dimensions dans une expression de création de tableau ([expressions de création de tableau](expressions.md#array-creation-expressions)) qui comprend un initialiseur.
*  Attributs ([attributs](attributes.md)).

Une conversion implicite expression constante ([conversions d’expression constante implicites](conversions.md#implicit-constant-expression-conversions)) autorise une expression constante de type `int` à convertir en `sbyte`, `byte`, `short`, `ushort`, `uint`, ou `ulong`, à condition que la valeur de l’expression constante se trouve dans la plage du type de destination.

## <a name="boolean-expressions"></a>expressions booléennes

Un *boolean_expression* est une expression qui produit un résultat de type `bool`; soit directement ou via l’application de `operator true` dans certains contextes, comme indiqué dans l’exemple suivant.

```antlr
boolean_expression
    : expression
    ;
```

Expression conditionnelle de contrôle d’un *if_statement* ([if instruction](statements.md#the-if-statement)), *while_statement* ([l’instruction while](statements.md#the-while-statement)), *do_statement* ([l’instruction do](statements.md#the-do-statement)), ou *for_statement* ([l’instruction for](statements.md#the-for-statement)) est un *boolean_ expression*. L’expression conditionnelle de contrôle de la `?:` opérateur ([opérateur conditionnel](expressions.md#conditional-operator)) suit les mêmes règles qu’un *boolean_expression*, mais pour des raisons de l’opérateur de priorité est classifiée comme un *conditional_or_expression*.

Un *boolean_expression* `E` est nécessaire pour pouvoir produire une valeur de type `bool`, comme suit :

*  Si `E` est implicitement convertible en `bool` lors de l’exécution par la conversion implicite est appliquée.
*  Sinon, la résolution de surcharge opérateur unaire ([opérateur unaire de résolution de surcharge](expressions.md#unary-operator-overload-resolution)) est utilisé pour trouver une meilleure implémentation unique de l’opérateur `true` sur `E`, et que l’implémentation est appliquée lors de l’exécution.
*  Si aucun opérateur n’est trouvée, une erreur au moment de la liaison se produit.

Le `DBBool` type struct dans [de base de données de type booléen](structs.md#database-boolean-type) fournit un exemple d’un type qui implémente `operator true` et `operator false`.
