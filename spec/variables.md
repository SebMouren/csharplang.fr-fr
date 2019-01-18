---
ms.openlocfilehash: b7bb7dd575d9e2e6d5dd85bdd3e535411e29fcf4
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229616"
---
# <a name="variables"></a>Variables

Les variables représentent des emplacements de stockage. Chaque variable possède un type qui détermine les valeurs pouvant être stockées dans la variable. C# est un langage de type sécurisé et le compilateur c# garantit que les valeurs stockées dans des variables sont toujours du type approprié. La valeur d’une variable peut être modifiée par assignation ou par le biais de la `++` et `--` opérateurs.

Une variable doit être ***définitivement assignée*** ([assignation définie](variables.md#definite-assignment)) avant de pouvoir obtenir sa valeur.

Comme décrit dans les sections suivantes, les variables sont soit ***initialement attribué*** ou ***initialement non assignées***. Une variable initialement assignée possède une valeur initiale bien définie et est toujours considéré comme définitivement assignée. Il n’y a aucune valeur initiale pour une variable initialement non assignée. Pour une variable initialement non assignée être considéré comme définitivement assignée à un emplacement donné, une affectation à la variable doit se produire dans chaque chemin d’accès d’exécution possibles menant à cet emplacement.

## <a name="variable-categories"></a>Catégories de variables

C# définit sept catégories de variables : les variables statiques, variables d’instance, éléments de tableau, paramètres de valeur, paramètres de référence, paramètres de sortie et les variables locales. Les sections qui suivent décrivent chacun de ces catégories.

Dans l’exemple
```csharp
class A
{
    public static int x;
    int y;

    void F(int[] v, int a, ref int b, out int c) {
        int i = 1;
        c = a + b++;
    }
}
```
`x` est une variable statique, `y` est une variable d’instance, `v[0]` est un élément de tableau, `a` est un paramètre de valeur, `b` est un paramètre de référence, `c` est un paramètre de sortie, et `i` est une variable locale.

### <a name="static-variables"></a>Variables statiques

Un champ déclaré avec le `static` modificateur est appelé un ***variable statique***. Une variable statique entame son existence avant l’exécution du constructeur statique ([constructeurs statiques](classes.md#static-constructors)) pour son type conteneur et cesse d’exister quand le domaine d’application associé n’existe plus.

La valeur initiale d’une variable statique est la valeur par défaut ([valeurs par défaut](variables.md#default-values)) de type de la variable.

À des fins de vérification de l’assignation définie, une variable statique est considéré comme initialement affecté.

### <a name="instance-variables"></a>Variables d’instance

Un champ déclaré sans le `static` modificateur est appelé un ***variable d’instance***.

#### <a name="instance-variables-in-classes"></a>Variables d’instance dans les classes

Une variable d’instance d’une classe entame son existence lorsqu’une nouvelle instance de cette classe est créée et cesse d’exister quand il n’existe aucune référence à cette instance et le destructeur de l’instance (le cas échéant) a été exécutée.

La valeur initiale d’une variable d’instance d’une classe est la valeur par défaut ([valeurs par défaut](variables.md#default-values)) de type de la variable.

À des fins de vérification de l’assignation définie, une variable d’instance d’une classe est considérée comme initialement affecté.

#### <a name="instance-variables-in-structs"></a>Variables d’instance dans les structs

Une variable d’instance d’un struct a exactement la même durée de vie que la variable de structure à laquelle il appartient. En d’autres termes, quand une variable d’un type struct naît ou cesse d’exister, faire trop les variables d’instance du struct.

L’état d’affectation initiale d’une variable d’instance d’un struct est identique à celui de la variable de struct qui le contient. En d’autres termes, quand une variable de struct est considérée comme initialement attribuée, donc trop sont ses variables d’instance, et lorsqu’une variable de struct est considérée comme initialement non assignée, ses variables d’instance sont de même non attribués.

### <a name="array-elements"></a>Éléments de tableau

Les éléments d’un tableau entrent en vigueur lorsqu’une instance de tableau est créée et cessent d’exister lorsqu’il n’existe aucune référence à cette instance de tableau.

La valeur initiale de chacun des éléments d’un tableau est la valeur par défaut ([valeurs par défaut](variables.md#default-values)) du type des éléments du tableau.

À des fins de vérification de l’assignation définie, un élément de tableau est considéré comme initialement affecté.

### <a name="value-parameters"></a>Paramètres de valeur

Un paramètre déclaré sans un `ref` ou `out` modificateur est un ***paramètre de valeur***.

Un paramètre de valeur naît dès l’appel de la fonction membre (méthode, constructeur d’instance, accesseur ou opérateur) ou une fonction anonyme à laquelle appartient le paramètre et est initialisé avec la valeur de l’argument fourni dans l’appel. Un paramètre de valeur normalement cesse d’exister dès le retour de la fonction membre ou une fonction anonyme. Toutefois, si le paramètre value est capturé par une fonction anonyme ([expressions de fonction anonyme](expressions.md#anonymous-function-expressions)), sa durée de vie s’étend au moins jusqu'à ce que le délégué ou arborescence de l’expression créée à partir de cette fonction anonyme est éligible pour le garbage collection.

À des fins de vérification de l’assignation définie, un paramètre de valeur est considéré comme initialement affecté.

### <a name="reference-parameters"></a>Paramètres de référence

Un paramètre déclaré avec un `ref` modificateur est un ***de référencer le paramètre***.

Un paramètre de référence ne crée pas un nouvel emplacement de stockage. Au lieu de cela, un paramètre de référence représente le même emplacement de stockage que la variable fournie comme argument dans la fonction membre ou un appel de fonction anonyme. Par conséquent, la valeur d’un paramètre de référence est toujours identique à la variable sous-jacente.

Les règles d’assignation définie suivantes s’appliquent aux paramètres de référence. Notez les règles différentes pour les paramètres output décrits dans [paramètres de sortie](variables.md#output-parameters).

*  Une variable doit absolument être assignée ([assignation définie](variables.md#definite-assignment)) avant qu’il peut être passé comme paramètre de référence dans un appel de fonction membre ou un délégué.
*  Au sein d’une fonction membre ou une fonction anonyme, un paramètre de référence est considérée comme étant initialement assignée.

Dans une méthode d’instance ou un accesseur d’instance d’un type struct, le `this` mot clé se comporte exactement comme un paramètre de référence du type struct ([cet accès](expressions.md#this-access)).

### <a name="output-parameters"></a>Paramètres de sortie

Un paramètre déclaré avec un `out` modificateur est un ***paramètre de sortie***.

Un paramètre de sortie ne crée pas un nouvel emplacement de stockage. Au lieu de cela, un paramètre de sortie représente le même emplacement de stockage que la variable fournie comme argument dans l’appel de fonction membre ou un délégué. Par conséquent, la valeur d’un paramètre de sortie est toujours identique à la variable sous-jacente.

Les règles d’assignation définie suivantes s’appliquent aux paramètres de sortie. Notez les règles différentes pour les paramètres de référence décrits dans [paramètres de référence](variables.md#reference-parameters).

*  Une variable ne doive pas être définitivement assignée avant qu’il peut être passé comme paramètre de sortie dans une fonction membre ou appel de délégué.
*  Après l’exécution normale d’un appel de fonction membre ou un délégué, chaque variable qui a été passé comme un paramètre de sortie est considérée comme assignée dans ce chemin d’exécution.
*  Au sein d’une fonction membre ou une fonction anonyme, un paramètre de sortie est considéré comme initialement non assigné.
*  Chaque paramètre de sortie d’une fonction membre ou une fonction anonyme doit absolument être assigné ([assignation définie](variables.md#definite-assignment)) avant que la fonction membre ou une fonction anonyme retourne normalement.

Au sein d’un constructeur d’instance d’un type struct, le `this` mot clé se comporte exactement comme un paramètre de sortie du type struct ([cet accès](expressions.md#this-access)).

### <a name="local-variables"></a>Variables locales

Un ***variable locale*** est déclarée par un *local_variable_declaration*, ce qui peut se produire dans un *bloc*, un *for_statement*, un *switch_statement* ou un *using_statement*; ou par un *foreach_statement* ou un *specific_catch_clause* pour un *try_statement*.

La durée de vie d’une variable locale est la partie de l’exécution du programme au cours de laquelle le stockage est garantie comme étant réservé pour celui-ci. Cette durée de vie s’étend au moins à partir de l’entrée dans le *bloc*, *for_statement*, *switch_statement*, *using_statement*, *foreach_statement*, ou *specific_catch_clause* auquel il est associé, jusqu'à l’exécution de ce *bloc*, *for_statement*, *switch_statement*, *using_statement*, *foreach_statement*, ou *specific_catch_clause* se termine en aucune façon. (Entrer un texte délimité *bloc* ou de l’appel d’une méthode suspend, mais ne termine pas, l’exécution de l’actuel *bloc*, *for_statement*, *switch_statement* , *using_statement*, *foreach_statement*, ou *specific_catch_clause*.) Si la variable locale est capturée par une fonction anonyme ([externes variables capturées](expressions.md#captured-outer-variables)), sa durée de vie s’étend au moins jusqu'à ce que l’arborescence de délégué ou une expression créée à partir de la fonction anonyme, ainsi que tous les autres objets qui viennent à référencez la variable capturée, sont éligibles pour le garbage collection.

Si le parent *bloc*, *for_statement*, *switch_statement*, *using_statement*, *foreach_statement*, ou *specific_catch_clause* est entrée récursive, une nouvelle instance de la variable locale est créée chaque fois et son *local_variable_initializer*, le cas échéant, est évalué chaque fois.

Une variable locale introduite par un *local_variable_declaration* n’est pas initialisée automatiquement et n’a donc aucune valeur par défaut. À des fins de vérification de l’assignation définie, une variable locale introduites par un *local_variable_declaration* est considéré comme initialement non assignées. Un *local_variable_declaration* peut inclure un *local_variable_initializer*, auquel cas la variable est considéré comme définitivement assignée uniquement après l’expression d’initialisation ([ Les instructions de déclaration](variables.md#declaration-statements)).

Dans l’étendue d’une variable locale introduite par un *local_variable_declaration*, il s’agit d’une erreur de compilation pour faire référence à cette variable locale dans une position du texte qui précède sa *local_variable_declarator*. Si la déclaration de variable locale est implicite ([déclarations de variables locales](statements.md#local-variable-declarations)), il est également une erreur pour faire référence à la variable au sein de son *local_variable_declarator*.

Une variable locale introduite par un *foreach_statement* ou un *specific_catch_clause* est considéré comme définitivement assignée dans toute sa portée.

La durée de vie réelle d’une variable locale est dépend de l’implémentation. Par exemple, un compilateur peut déterminer de manière statique qu’une variable locale dans un bloc est uniquement utilisée pour une petite portion de ce bloc. À l’aide de cette analyse, le compilateur peut générer du code qui résulte dans le stockage de la variable ayant une durée de vie plus courte que son bloc conteneur.

Le stockage référencé par une variable de référence locale est récupéré indépendamment de la durée de vie de cette variable de référence locale ([gestion automatique de la mémoire](basic-concepts.md#automatic-memory-management)).

## <a name="default-values"></a>Valeurs par défaut

Les catégories de variables suivantes sont automatiquement initialisés à leurs valeurs par défaut :

*  variables statiques
*  Variables d’instance des instances de classe.
*  Éléments de tableau.

La valeur par défaut d’une variable dépend du type de la variable et est déterminée comme suit :

*  Pour une variable d’un *value_type*, la valeur par défaut est identique à la valeur calculée par le *value_type*du constructeur par défaut ([constructeurs par défaut](types.md#default-constructors)).
*  Pour une variable d’un *reference_type*, la valeur par défaut est `null`.

L’initialisation de valeurs par défaut est généralement effectuée en ayant le Gestionnaire de mémoire ou du garbage collector initialiser la mémoire à tous les bits à zéro avant qu’il est alloué pour l’utilisation. Pour cette raison, il est pratique d’utiliser tous les bits à zéro pour représenter la référence null.

## <a name="definite-assignment"></a>Assignation définie

À un emplacement donné dans le code exécutable d’une fonction membre, une variable est dite ***définitivement assignée*** si le compilateur peut prouver, par une analyse de flux statique particulier ([règles précises de détermination définitive affectation](variables.md#precise-rules-for-determining-definite-assignment)), que la variable a été initialisée automatiquement ou a été la cible d’au moins une assignation. Les règles d’assignation définie de manière informelle indiqué, sont :

*  Une variable initialement assignée ([initialement affecté variables](variables.md#initially-assigned-variables)) est toujours considéré comme définitivement assignée.
*  Une variable initialement non assignée ([initialement non assignées variables](variables.md#initially-unassigned-variables)) est considérée comme définitivement assignée à un emplacement donné si tous les chemins d’exécution possibles conduisant à cet emplacement contiennent au moins un des éléments suivants :
    * Une assignation simple ([assignation Simple](expressions.md#simple-assignment)) dans lequel la variable est l’opérande de gauche.
    * Une expression d’appel ([expressions d’appel](expressions.md#invocation-expressions)) ou une expression de création d’objet ([des expressions de la création d’objet](expressions.md#object-creation-expressions)) qui passe la variable comme un paramètre de sortie.
    * Pour une variable locale, une déclaration de variable locale ([déclarations de variables locales](statements.md#local-variable-declarations)) qui inclut un initialiseur de variable.

La spécification formelle sous-jacent informelles règles ci-dessus est décrite dans [initialement affecté variables](variables.md#initially-assigned-variables), [initialement non assignées variables](variables.md#initially-unassigned-variables), et [règles précises de détermination assignation définie](variables.md#precise-rules-for-determining-definite-assignment).

Les États d’assignation définie des variables d’instance d’un *struct_type* variable sont suivies individuellement, ainsi que collectivement. Dans supplémentaires aux règles ci-dessus, les règles suivantes s’appliquent à *struct_type* variables et leurs variables de l’instance :

*  Une variable d’instance est considérée comme définitivement assignée si son contenant *struct_type* variable est considéré comme définitivement assignée.
*  Un *struct_type* variable est considérée comme assignée définitivement si chacune de ses variables d’instance est considérée comme définitivement assignée.

Assignation définie est obligatoire dans les contextes suivants :

*  Une variable doit être assignée de manière définitive à chaque emplacement où sa valeur est obtenue. Cela garantit que les valeurs non définis se produisent pas. L’occurrence d’une variable dans une expression est considérée pour obtenir la valeur de la variable, sauf quand
    * la variable est l’opérande gauche d’une assignation simple,
    * la variable est passée comme paramètre de sortie, ou
    * la variable est un *struct_type* variable et se produit comme opérande gauche d’un accès au membre.
*  Une variable doit être assignée de manière définitive à chaque emplacement où elle est transmise comme paramètre de référence. Cela garantit que le membre de fonction appelé peut prendre en compte le paramètre de référence est affecté initialement.
*  Tous les paramètres de sortie d’une fonction membre doivent absolument être assignés à chaque emplacement où la fonction membre retourne (via un `return` instruction ou exécution atteint la fin du corps du membre de fonction). Cela garantit que les fonctions membres ne retournent pas des valeurs non définis dans les paramètres de sortie, ce qui permet au compilateur de considérer un appel de fonction membre qui prend une variable comme paramètre de sortie équivalent à une assignation à la variable.
*  Le `this` variable d’un *struct_type* constructeur d’instance doit absolument être assigné à chaque emplacement où ce constructeur retourne.

### <a name="initially-assigned-variables"></a>Variables initialement attribuées

Les catégories suivantes de variables sont considérées comme initialement assignées :

*  variables statiques
*  Variables d’instance des instances de classe.
*  Variables d’instance de variables struct initialement attribué.
*  Éléments de tableau.
*  Paramètres de valeur.
*  Paramètres de référence.
*  Variables déclarées dans un `catch` clause ou un `foreach` instruction.

### <a name="initially-unassigned-variables"></a>Variables initialement non assignées

Les catégories suivantes de variables sont considérées comme initialement non assignées :

*  Variables d’instance de variables struct initialement non assignées.
*  Paramètres de sortie, y compris le `this` variable struct constructeurs d’instance.
*  Les variables locales, à l’exception de ceux déclarés dans un `catch` clause ou un `foreach` instruction.

### <a name="precise-rules-for-determining-definite-assignment"></a>Règles précises de détermination d’assignation définie

Afin de déterminer que chaque variable utilisée est une assignation, le compilateur doit utiliser un processus qui est équivalent à celle décrite dans cette section.

Le compilateur traite le corps de chaque fonction membre qui a une ou plusieurs variables initialement non assignées. Pour chaque variable initialement non assignée *v*, le compilateur détermine un ***état d’assignation définie*** pour *v* sur chacun des points suivants dans la fonction membre :

*  Au début de chaque instruction.
*  Au point de terminaison ([points de terminaison et accessibilité](statements.md#end-points-and-reachability)) de chaque instruction
*  Sur chaque arc qui transfère le contrôle à une autre instruction ou au point de terminaison d’une instruction
*  Au début de chaque expression
*  À la fin de chaque expression

L’état d’assignation définie *v* peut être :

*  Une assignation. Cela indique que sur tous les flux de contrôle possibles à ce stade, *v* une valeur a été attribuée.
*  Pas définitivement assignée. Pour l’état d’une variable à la fin d’une expression de type `bool`, l’état d’une variable qui n’est pas définitivement assignée mai (mais n’est pas nécessairement) appartiennent à un des états secondaires suivants :
    * Définitivement assignée après une expression true. Cet état indique que *v* est définitivement assignée si l’expression booléenne évaluée comme vraie, mais n’est pas forcément assignée si l’expression booléenne évaluée comme false.
    * Définitivement assignée après une expression false. Cet état indique que *v* est définitivement assignée si l’expression booléenne évaluée comme false, mais n’est pas forcément assignée si l’expression booléenne évaluée comme vraie.

Les règles suivantes régissent comment l’état d’une variable *v* est déterminé à chaque emplacement.

#### <a name="general-rules-for-statements"></a>Règles générales pour les instructions

*  *v* n’est pas définitivement assignée au début du corps d’un membre de fonction.
*  *v* est définitivement assignée au début de n’importe quelle instruction inaccessible.
*  L’état d’assignation définie *v* au début de toute autre instruction est déterminée en vérifiant l’état d’assignation définie *v* sur tous les transferts de flux de contrôle qui ciblent le début de ce instruction. Si (et seulement si) *v* est définitivement assignée sur tous ces transferts de flux de contrôle, puis *v* est définitivement assignée au début de l’instruction. L’ensemble des transferts de flux de contrôle possibles est déterminé de la même façon que la vérification de l’accessibilité d’instruction ([points de terminaison et accessibilité](statements.md#end-points-and-reachability)).
*  L’état d’assignation définie *v* au point de fin d’un bloc, `checked`, `unchecked`, `if`, `while`, `do`, `for`, `foreach`, `lock`, `using`, ou `switch` est déterminé par la vérification de l’état d’assignation définie *v* sur tous les transferts de flux de contrôle qui ciblent le point de terminaison de cette instruction. Si *v* est définitivement assignée sur tous ces transferts de flux de contrôle, puis *v* est définitivement assignée à la fin de l’instruction. Sinon, *v* n’est pas définitivement assignée à la fin de l’instruction. L’ensemble des transferts de flux de contrôle possibles est déterminé de la même façon que la vérification de l’accessibilité d’instruction ([points de terminaison et accessibilité](statements.md#end-points-and-reachability)).

#### <a name="block-statements-checked-and-unchecked-statements"></a>Instructions de bloc, activées et elle est désactivée

L’état d’assignation définie *v* sur le contrôle de transfert à la première instruction de la liste d’instructions dans le bloc (ou au point de terminaison du bloc, si la liste d’instructions est vide) est identique à l’instruction d’assignation définie de *v* avant le bloc, `checked`, ou `unchecked` instruction.

#### <a name="expression-statements"></a>Instructions d’expression

Pour une instruction d’expression *stmt* qui se compose de l’expression *expr*:

*  *v* a le même état d’assignation définie au début de *expr* qu’au début de *stmt*.
*  Si *v* si définitivement assignée à la fin de *expr*, il est définitivement assignée à la fin de *stmt*; sinon, elle n’est pas définitivement assignée à la fin de *stmt*.

#### <a name="declaration-statements"></a>Instructions de déclaration

*  Si *stmt* est une instruction de déclaration sans initialiseurs, puis *v* a le même état d’assignation définie à la fin de *stmt* qu’au début de *stmt*.
*  Si *stmt* est une instruction de déclaration avec initialiseurs, puis l’état d’assignation définie pour *v* est déterminé comme si *stmt* ont été une liste d’instructions, avec une assignation instruction pour chaque déclaration avec un initialiseur (dans l’ordre de déclaration).

#### <a name="if-statements"></a>Si les instructions

Pour un `if` instruction *stmt* du formulaire :
```csharp
if ( expr ) then_stmt else else_stmt
```

*  *v* a le même état d’assignation définie au début de *expr* qu’au début de *stmt*.
*  Si *v* est définitivement assignée à la fin de *expr*, puis elle est définitivement assignée lors du transfert de flux de contrôle vers *then_stmt* et à *else_stmt*  ou au point de terminaison de *stmt* s’il n’existe aucune clause else.
*  Si *v* a l’état « définitivement assignée après une expression true » à la fin de *expr*, puis elle est définitivement assignée lors du transfert de flux de contrôle vers *then_stmt*et non définitivement assignée lors du transfert de flux de contrôle soit *else_stmt* ou au point de terminaison de *stmt* s’il n’existe aucune clause else.
*  Si *v* a l’état « définitivement assignée après une expression false » à la fin de *expr*, puis elle est définitivement assignée lors du transfert de flux de contrôle vers *else_stmt*et non définitivement assignée lors du transfert de flux de contrôle à *then_stmt*. Il est définitivement assignée à la fin de *stmt* si et seulement si elle est définitivement assignée à la fin de *then_stmt*.
*  Sinon, *v* est considérée comme non assignée lors du transfert de flux de contrôle soit le *then_stmt* ou *else_stmt*, ou au point de terminaison de  *stmt* s’il n’existe aucune clause else.

#### <a name="switch-statements"></a>Instructions switch

Dans un `switch` instruction *stmt* avec une expression de contrôle *expr*:

*  L’état d’assignation définie *v* au début de *expr* est identique à l’état de *v* au début de *stmt*.
*  L’état d’assignation définie *v* sur le flux de contrôle transfert vers une liste d’instructions de bloc switch accessible est identique à l’état d’assignation définie *v* à la fin de *expr*.

#### <a name="while-statements"></a>Tandis que les instructions

Pour un `while` instruction *stmt* du formulaire :
```csharp
while ( expr ) while_body
```

*  *v* a le même état d’assignation définie au début de *expr* qu’au début de *stmt*.
*  Si *v* est définitivement assignée à la fin de *expr*, puis elle est définitivement assignée lors du transfert de flux de contrôle vers *while_body* et au point de terminaison de  *stmt*.
*  Si *v* a l’état « définitivement assignée après une expression true » à la fin de *expr*, puis elle est définitivement assignée lors du transfert de flux de contrôle vers *while_body*, mais pas définitivement assignée à la fin de *stmt*.
*  Si *v* a l’état « définitivement assignée après une expression false » à la fin de *expr*, puis elle est définitivement assignée lors du transfert de flux de contrôle vers le point de terminaison de *stmt* , mais pas définitivement assignée lors du transfert de flux de contrôle à *while_body*.

#### <a name="do-statements"></a>Les instructions

Pour un `do` instruction *stmt* du formulaire :
```csharp
do do_body while ( expr ) ;
```

*  *v* a le même état d’assignation définie lors du transfert de flux de contrôle à partir du début de *stmt* à *do_body* qu’au début de *stmt*.
*  *v* a le même état d’assignation définie au début de *expr* au point de terminaison de *do_body*.
*  Si *v* est définitivement assignée à la fin de *expr*, puis elle est définitivement assignée lors du transfert de flux de contrôle vers le point de terminaison de *stmt*.
*  Si *v* a l’état « définitivement assignée après une expression false » à la fin de *expr*, puis elle est définitivement assignée lors du transfert de flux de contrôle vers le point de terminaison de *stmt* .

#### <a name="for-statements"></a>Pour les instructions

État d’assignation pour un `for` instruction du formulaire :
```csharp
for ( for_initializer ; for_condition ; for_iterator ) embedded_statement
```
est effectué comme si l’instruction était écrite :
```csharp
{
    for_initializer ;
    while ( for_condition ) {
        embedded_statement ;
        for_iterator ;
    }
}
```

Si le *for_condition* est omis à partir de la `for` instruction, puis d’évaluation de revenu de l’assignation définie comme si *for_condition* ont été remplacés par `true` dans l’expansion ci-dessus .

#### <a name="break-continue-and-goto-statements"></a>Interrompre, continuer et les instructions goto

L’état d’assignation définie *v* lors du transfert de flux de contrôle a provoqué par un `break`, `continue`, ou `goto` instruction est identique à l’état d’assignation définie *v* à la début de l’instruction.

#### <a name="throw-statements"></a>Instructions throw

Pour une instruction *stmt* du formulaire
```csharp
throw expr ;
```

L’état d’assignation définie *v* au début de *expr* est identique à l’état d’assignation définie *v* au début de *stmt*.

#### <a name="return-statements"></a>Instructions return

Pour une instruction *stmt* du formulaire
```csharp
return expr ;
```

*  L’état d’assignation définie *v* au début de *expr* est identique à l’état d’assignation définie *v* au début de *stmt*.
*  Si *v* est un paramètre de sortie, puis il doit absolument être assigné soit :
    * une fois *expr*
    * ou à la fin de la `finally` de blocs à un `try` - `finally` ou `try` - `catch` - `finally` qui englobe la `return` instruction.

Une instruction stmt a la forme suivante :
```csharp
return ;
```

*  Si *v* est un paramètre de sortie, puis il doit absolument être assigné soit :
    * before *stmt*
    * ou à la fin de la `finally` de blocs à un `try` - `finally` ou `try` - `catch` - `finally` qui englobe la `return` instruction.

#### <a name="try-catch-statements"></a>Instructions try-catch

Pour une instruction *stmt* du formulaire :
```csharp
try try_block
catch(...) catch_block_1
...
catch(...) catch_block_n
```

*  L’état d’assignation définie *v* au début de *try_block* est identique à l’état d’assignation définie *v* au début de *stmt*.
*  L’état d’assignation définie *v* au début de *catch_block_i* (pour tout *je*) est identique à l’état d’assignation définie *v*au début de *stmt*.
*  L’état d’assignation définie *v* à la fin de *stmt* est sans aucun doute si (et seulement si) *v* est définitivement assignée à la fin de  *try_block* et chaque *catch_block_i* (pour chaque *je* comprise entre 1 et *n*).

#### <a name="try-finally-statements"></a>Instructions try-finally

Pour un `try` instruction *stmt* du formulaire :
```csharp
try try_block finally finally_block
```

*  L’état d’assignation définie *v* au début de *try_block* est identique à l’état d’assignation définie *v* au début de *stmt*.
*  L’état d’assignation définie *v* au début de *finally_block* est identique à l’état d’assignation définie *v* au début de *stmt* .
*  L’état d’assignation définie *v* à la fin de *stmt* est sans aucun doute si (et seulement si) au moins une des opérations suivantes est vraie :
    * *v* est définitivement assignée à la fin de *try_block*
    * *v* est définitivement assignée à la fin de *finally_block*

Si un transfert de flux de contrôle (par exemple, un `goto` instruction) est effectuée en ce commence dans *try_block*et se termine en dehors de *try_block*, puis *v* est également considérés comme définitivement assignée sur ce transfert si *v* est définitivement assignée à la fin de *finally_block*. (Ce n’est pas un uniquement si — si *v* est définitivement assignée pour une autre raison sur ce transfert de flux de contrôle, il est toujours considéré comme définitivement assignée.)

#### <a name="try-catch-finally-statements"></a>Instructions try-catch-finally

L’analyse d’assignation pour un `try` - `catch` - `finally` instruction du formulaire :
```csharp
try try_block
catch(...) catch_block_1
...
catch(...) catch_block_n
finally *finally_block*
```
est effectué comme si l’instruction ont été une `try` - `finally` instruction englobante un `try` - `catch` instruction :
```csharp
try {
    try try_block
    catch(...) catch_block_1
    ...
    catch(...) catch_block_n
}
finally finally_block
```

L’exemple suivant montre comment les différents blocs d’un `try` instruction ([l’instruction try](statements.md#the-try-statement)) affectent une assignation.
```csharp
class A
{
    static void F() {
        int i, j;
        try {
            goto LABEL;
            // neither i nor j definitely assigned
            i = 1;
            // i definitely assigned
        }

        catch {
            // neither i nor j definitely assigned
            i = 3;
            // i definitely assigned
        }

        finally {
            // neither i nor j definitely assigned
            j = 5;
            // j definitely assigned
            }
        // i and j definitely assigned
        LABEL:;
        // j definitely assigned

    }
}
```

#### <a name="foreach-statements"></a>Instructions foreach

Pour un `foreach` instruction *stmt* du formulaire :
```csharp
foreach ( type identifier in expr ) embedded_statement
```

*  L’état d’assignation définie *v* au début de *expr* est identique à l’état de *v* au début de *stmt*.
*  L’état d’assignation définie *v* lors du transfert de flux de contrôle à *embedded_statement* ou au point de terminaison de *stmt* est identique à l’état de *v* à la fin de *expr*.

#### <a name="using-statements"></a>À l’aide d’instructions

Pour un `using` instruction *stmt* du formulaire :
```csharp
using ( resource_acquisition ) embedded_statement
```

*  L’état d’assignation définie *v* au début de *resource_acquisition* est identique à l’état de *v* au début de *stmt*.
*  L’état d’assignation définie *v* lors du transfert de flux de contrôle à *embedded_statement* est identique à l’état de *v* à la fin de *resource_ acquisition*.

#### <a name="lock-statements"></a>Instructions Lock

Pour un `lock` instruction *stmt* du formulaire :
```csharp
lock ( expr ) embedded_statement
```

*  L’état d’assignation définie *v* au début de *expr* est identique à l’état de *v* au début de *stmt*.
*  L’état d’assignation définie *v* lors du transfert de flux de contrôle à *embedded_statement* est identique à l’état de *v* à la fin de *expr*.

#### <a name="yield-statements"></a>Instructions yield

Pour un `yield return` instruction *stmt* du formulaire :
```csharp
yield return expr ;
```

*  L’état d’assignation définie *v* au début de *expr* est identique à l’état de *v* au début de *stmt*.
*  L’état d’assignation définie *v* à la fin de *stmt* est identique à l’état de *v* à la fin de *expr*.
*  Un `yield break` instruction n’a aucun effet sur l’état d’assignation définie.

#### <a name="general-rules-for-simple-expressions"></a>Règles générales pour les expressions simples

La règle suivante s’applique à ces types d’expressions : littéraux ([littéraux](expressions.md#literals)), des noms simples ([noms simples](expressions.md#simple-names)), les expressions d’accès au membre ([l’accès au membre](expressions.md#member-access)), expressions d’accès de base non indexées ([accès de Base](expressions.md#base-access)), `typeof` expressions ([l’opérateur typeof](expressions.md#the-typeof-operator)), les expressions de valeur par défaut ([expressions de valeur par défaut ](expressions.md#default-value-expressions)) et `nameof` expressions ([Nameof expressions](expressions.md#nameof-expressions)).

*  L’état d’assignation définie *v* à la fin d’une telle expression est identique à l’état d’assignation définie *v* au début de l’expression.

#### <a name="general-rules-for-expressions-with-embedded-expressions"></a>Règles générales pour les expressions avec des expressions incorporées

Les règles suivantes s’appliquent à ces types d’expressions : expressions entre parenthèses ([expressions entre parenthèses](expressions.md#parenthesized-expressions)), les expressions d’accès élément ([accès à un élément](expressions.md#element-access)), base accéder aux expressions avec l’indexation ([accès de Base](expressions.md#base-access)), incrémenter et décrémenter des expressions ([d’incrémentation et décrémentation opérateurs](expressions.md#postfix-increment-and-decrement-operators), [Incrément préfixé opérateurs et de décrémentation](expressions.md#prefix-increment-and-decrement-operators)), les expressions de cast ([les expressions de Cast](expressions.md#cast-expressions)), unaire `+`, `-`, `~`, `*` expressions, binaire `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `<`, `<=`, `>`, `>=`, `==`, `!=`, `is`, `as`, `&`, `|`, `^` expressions ([opérateurs arithmétiques](expressions.md#arithmetic-operators), [opérateurs de décalage](expressions.md#shift-operators), [relationnels et opérateurs de test de type](expressions.md#relational-and-type-testing-operators) [Opérateurs logiques](expressions.md#logical-operators)), composée d’expressions d’assignation ([assignation composée](expressions.md#compound-assignment)), `checked` et `unchecked` expressions ([checked et unchecked opérateurs](expressions.md#the-checked-and-unchecked-operators)), ainsi que les expressions de création de tableau et le délégué ([l’opérateur new](expressions.md#the-new-operator)).

Chacune de ces expressions a un ou plusieurs sous-expressions qui sont évaluées de manière inconditionnelle dans un ordre fixe. Par exemple, le fichier binaire `%` opérateur évalue la partie gauche de l’opérateur, puis la partie droite. Une opération d’indexation évalue l’expression indexée, puis évalue chaque expression d’index, dans l’ordre de gauche à droite. Pour une expression *expr*, qui a des sous-expressions *e1, e2,..., eN*, évaluée dans cet ordre :

*  L’état d’assignation définie *v* au début de *e1* est identique à l’état d’assignation définie au début de *expr*.
*  L’état d’assignation définie *v* au début de *ei* (*je* supérieure à 1) est le même que l’état d’assignation définie à la fin de la sous-expression précédente.
*  L’état d’assignation définie *v* à la fin de *expr* est identique à l’état d’assignation définie à la fin de *fr*

#### <a name="invocation-expressions-and-object-creation-expressions"></a>Expressions d’appel et les expressions de la création d’objet

Pour une expression d’appel *expr* du formulaire :
```csharp
primary_expression ( arg1 , arg2 , ... , argN )
```
ou une expression de création d’objet sous la forme :
```csharp
new type ( arg1 , arg2 , ... , argN )
```

*  Pour une expression d’appel, l’état d’assignation définie *v* avant *primary_expression* est identique à l’état de *v* avant *expr*.
*  Pour une expression d’appel, l’état d’assignation définie *v* avant *arg1* est identique à l’état de *v* après *primary_expression*.
*  Pour une expression de création d’objet, l’état d’assignation définie *v* avant *arg1* est identique à l’état de *v* avant *expr*.
*  Pour chaque argument *argi*, l’état d’assignation définie *v* après *argi* est déterminée par les règles de l’expression normale, en ignorant les `ref` ou `out`modificateurs.
*  Pour chaque argument *argi* pour toute *je* supérieur à un, l’état d’assignation définie *v* avant *argi* est identique à l’état de *v* après la précédente *arg*.
*  Si la variable *v* est passée comme un `out` argument (par exemple, un argument de la forme `out v`) dans un des arguments, puis l’état de *v* après *expr* est une assignation. Sinon, l’état de *v* après *expr* est identique à l’état de *v* après *argN*.
*  Pour les initialiseurs de tableau ([expressions de création de tableau](expressions.md#array-creation-expressions)), initialiseurs d’objets ([initialiseurs d’objets](expressions.md#object-initializers)), les initialiseurs de collection ([initialiseurs de collections](expressions.md#collection-initializers)) et initialiseurs d’objet ([expressions de création d’objet anonyme](expressions.md#anonymous-object-creation-expressions)), l’état d’assignation définie est déterminé par l’expansion de ces constructions sont définies en termes de.

#### <a name="simple-assignment-expressions"></a>Expressions d’assignation simple

Pour une expression *expr* du formulaire `w = expr_rhs`:

*  L’état d’assignation définie *v* avant *expr_rhs* est identique à l’état d’assignation définie *v* avant *expr*.
*  L’état d’assignation définie *v* après *expr* est déterminé par :
   * Si *w* est la même variable en tant que *v*, puis l’état d’assignation définie *v* après *expr* est définitivement assignée.
   * Sinon, si l’affectation se produit dans le constructeur d’instance d’un type struct, si *w* est un accès à la propriété qui désigne une propriété implémentée automatiquement *P* sur l’instance en cours de construction et *v* est le champ de stockage masqué de *P*, puis l’état d’assignation définie *v* après *expr* est sans aucun doute attribué.
   * Sinon, l’état d’assignation définie *v* après *expr* est identique à l’état d’assignation définie *v* après *expr_rhs*.

#### <a name="-conditional-and-expressions"></a>& & (opérateur AND conditionnel) des expressions

Pour une expression *expr* du formulaire `expr_first && expr_second`:

*  L’état d’assignation définie *v* avant *expr_first* est identique à l’état d’assignation définie *v* avant *expr*.
*  L’état d’assignation définie *v* avant *expr_second* est définitivement assignée si l’état de *v* après *expr_first* est définitivement assignée ou « définitivement assignée après une expression true ». Sinon, elle n’est pas définitivement assignée.
*  L’état d’assignation définie *v* après *expr* est déterminé par :
    * Si *expr_first* est une expression constante ayant la valeur `false`, puis l’état d’assignation définie *v* après *expr* est identique à l’assignation définie état de *v* après *expr_first*.
    * Sinon, si l’état de *v* après *expr_first* est définitivement assignée, puis l’état de *v* après *expr* est définitivement assignée.
    * Sinon, si l’état de *v* après *expr_second* est définitivement assignée et l’état de *v* après *expr_first* est « sans aucun doute affectés après une expression false », puis l’état de *v* après *expr* est définitivement assignée.
    * Sinon, si l’état de *v* après *expr_second* est définitivement assignée ou « définitivement assignée après une expression true », puis l’état de *v* après  *Expr* est « définitivement assignée après une expression true ».
    * Sinon, si l’état de *v* après *expr_first* est « définitivement assignée après une expression false » et l’état de *v* après *expr_second* est « définitivement assignée après une expression false », l’état de *v* après *expr* est « définitivement assignée après une expression false ».
    * Sinon, l’état de *v* après *expr* n’est pas définitivement assignée.

Dans l’exemple
```csharp
class A
{
    static void F(int x, int y) {
        int i;
        if (x >= 0 && (i = y) >= 0) {
            // i definitely assigned
        }
        else {
            // i not definitely assigned
        }
        // i not definitely assigned
    }
}
```
la variable `i` est considéré comme définitivement assignée dans une des instructions incorporées d’une `if` instruction, mais pas dans l’autre. Dans le `if` instruction dans la méthode `F`, la variable `i` est définitivement assignée dans la première instruction incorporée, car l’exécution de l’expression `(i = y)` précède toujours l’exécution de cette instruction incorporée. En revanche, la variable `i` n'est pas définitivement assignée dans la deuxième instruction incorporée, étant donné que `x >= 0` peut avoir été vérifié false, ce qui entraîne la variable `i` ne sont pas attribuées.

#### <a name="-conditional-or-expressions"></a>|| (opérateur OR conditionnel) des expressions

Pour une expression *expr* du formulaire `expr_first || expr_second`:

*  L’état d’assignation définie *v* avant *expr_first* est identique à l’état d’assignation définie *v* avant *expr*.
*  L’état d’assignation définie *v* avant *expr_second* est définitivement assignée si l’état de *v* après *expr_first* est définitivement assignée ou « définitivement assignée après une expression false ». Sinon, elle n’est pas définitivement assignée.
*  L’instruction d’assignation définie de *v* après *expr* est déterminé par :
    * Si *expr_first* est une expression constante ayant la valeur `true`, puis l’état d’assignation définie *v* après *expr* est identique à l’assignation définie état de *v* après *expr_first*.
    * Sinon, si l’état de *v* après *expr_first* est définitivement assignée, puis l’état de *v* après *expr* est définitivement assignée.
    * Sinon, si l’état de *v* après *expr_second* est définitivement assignée et l’état de *v* après *expr_first* est « sans aucun doute affectés après une expression true », puis l’état de *v* après *expr* est définitivement assignée.
    * Sinon, si l’état de *v* après *expr_second* est définitivement assignée ou « définitivement assignée après une expression false », puis l’état de *v* après *expr* est « définitivement assignée après une expression false ».
    * Sinon, si l’état de *v* après *expr_first* est « définitivement assignée après une expression true » et l’état de *v* après *expr_second*est « définitivement assignée après une expression true », l’état de *v* après *expr* est « définitivement assignée après une expression true ».
    * Sinon, l’état de *v* après *expr* n’est pas définitivement assignée.

Dans l’exemple
```csharp
class A
{
    static void G(int x, int y) {
        int i;
        if (x >= 0 || (i = y) >= 0) {
            // i not definitely assigned
        }
        else {
            // i definitely assigned
        }
        // i not definitely assigned
    }
}
```
la variable `i` est considéré comme définitivement assignée dans une des instructions incorporées d’une `if` instruction, mais pas dans l’autre. Dans le `if` instruction dans la méthode `G`, la variable `i` est définitivement assignée dans la deuxième instruction incorporée, car l’exécution de l’expression `(i = y)` précède toujours l’exécution de cette instruction incorporée. En revanche, la variable `i` n'est pas définitivement assignée dans la première instruction incorporée, étant donné que `x >= 0` peut avoir été vérifié true, auquel cas la variable `i` ne sont pas attribuées.

#### <a name="-logical-negation-expressions"></a>! expressions (négation logique)

Pour une expression *expr* du formulaire `! expr_operand`:

*  L’état d’assignation définie *v* avant *expr_operand* est identique à l’état d’assignation définie *v* avant *expr*.
*  L’état d’assignation définie *v* après *expr* est déterminé par :
    * Si l’état de *v* après * expr_operand * est définitivement assignée, puis l’état de *v* après *expr* est définitivement assignée.
    * Si l’état de *v* après * expr_operand * n'est pas définitivement assignée, puis l’état de *v* après *expr* n’est pas définitivement assignée.
    * Si l’état de *v* après * expr_operand * est « définitivement assignée après une expression false », l’état de *v* après *expr* est « définitivement assignée après la valeur true expression ».
    * Si l’état de *v* après * expr_operand * est « définitivement assignée après une expression true », l’état de *v* après *expr* est « définitivement assignée après false expression ».

#### <a name="-null-coalescing-expressions"></a>?? expressions (fusion de null)

Pour une expression *expr* du formulaire `expr_first ?? expr_second`:

*  L’état d’assignation définie *v* avant *expr_first* est identique à l’état d’assignation définie *v* avant *expr*.
*  L’état d’assignation définie *v* avant *expr_second* est identique à l’état d’assignation définie *v* après *expr_first*.
*  L’instruction d’assignation définie de *v* après *expr* est déterminé par :
    * Si *expr_first* est une expression constante ([expressions constantes](expressions.md#constant-expressions)) avec la valeur null, alors la l’état de *v* après *expr* est le même en tant que l’état de *v* après *expr_second*.
*  Sinon, l’état de *v* après *expr* est identique à l’état d’assignation définie *v* après *expr_first*.

#### <a name="-conditional-expressions"></a>? : (conditionnelles) expressions

Pour une expression *expr* du formulaire `expr_cond ? expr_true : expr_false`:

*  L’état d’assignation définie *v* avant *expr_cond* est identique à l’état de *v* avant *expr*.
*  L’état d’assignation définie *v* avant *expr_true* est définitivement assignée si et seulement si une des opérations suivantes conserve :
    * *expr_cond* est une expression constante ayant la valeur `false`
    * l’état de *v* après *expr_cond* est définitivement assignée ou « définitivement assignée après une expression true ».
*  L’état d’assignation définie *v* avant *expr_false* est définitivement assignée si et seulement si une des opérations suivantes conserve :
    * *expr_cond* est une expression constante ayant la valeur `true`
*  l’état de *v* après *expr_cond* est définitivement assignée ou « définitivement assignée après une expression false ».
*  L’état d’assignation définie *v* après *expr* est déterminé par :
    * Si *expr_cond* est une expression constante ([expressions constantes](expressions.md#constant-expressions)) avec la valeur `true` ensuite l’état de *v* après *expr* est le même que l’état de *v* après *expr_true*.
    * Sinon, si *expr_cond* est une expression constante ([expressions constantes](expressions.md#constant-expressions)) avec la valeur `false` ensuite l’état de *v* après *expr* est identique à l’état de *v* après *expr_false*.
    * Sinon, si l’état de *v* après *expr_true* est définitivement assigné et l’état de *v* après *expr_false* est sans aucun doute reçu, puis l’état de *v* après *expr* est définitivement assignée.
    * Sinon, l’état de *v* après *expr* n’est pas définitivement assignée.

#### <a name="anonymous-functions"></a>Fonctions anonymes

Pour un *lambda_expression* ou *anonymous_method_expression* *expr* avec un corps (soit *bloc* ou *expression* ) *corps*:

*  L’état d’assignation définie d’une variable externe *v* avant *corps* est identique à l’état de *v* avant *expr*. Autrement dit, état d’assignation définie des variables externes est hérité à partir du contexte de la fonction anonyme.
*  L’état d’assignation définie d’une variable externe *v* après *expr* est identique à l’état de *v* avant *expr*.

L’exemple
```csharp
delegate bool Filter(int i);

void F() {
    int max;

    // Error, max is not definitely assigned
    Filter f = (int n) => n < max;

    max = 5;
    DoWork(f);
}
```
génère une erreur de compilation depuis `max` n'est pas définitivement assignée où la fonction anonyme est déclarée. L’exemple
```csharp
delegate void D();

void F() {
    int n;
    D d = () => { n = 1; };

    d();

    // Error, n is not definitely assigned
    Console.WriteLine(n);
}
```
génère également une erreur de compilation depuis l’assignation à `n` dans la fonction anonyme n’a aucun effet sur l’état d’assignation définie `n` en dehors de la fonction anonyme.

## <a name="variable-references"></a>Références de variable

Un *variable_reference* est un *expression* qui est classé en tant que variable. Un *variable_reference* désigne un emplacement de stockage qui est accessible à la fois pour extraire la valeur actuelle et pour stocker une nouvelle valeur.

```antlr
variable_reference
    : expression
    ;
```

En C et C++, un *variable_reference* est appelé un *lvalue*.

## <a name="atomicity-of-variable-references"></a>Atomicité des références de variable

Lectures et écritures de types de données suivants sont atomiques : `bool`, `char`, `byte`, `sbyte`, `short`, `ushort`, `uint`, `int`, `float`et les types référence. En outre, lectures et écritures de types enum avec un type sous-jacent de la liste précédente sont également atomiques. Lit et écrit des autres types, y compris `long`, `ulong`, `double`, et `decimal`, ainsi que les types définis par l’utilisateur, n’est pas garanti pour être atomiques. Outre les fonctions de bibliothèque conçues à cet effet, aucun des est garantie atomique en lecture-modification-écriture, comme dans le cas d’incrémentation ou de décrémentation.

