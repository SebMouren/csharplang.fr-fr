---
ms.openlocfilehash: a01cf9387b8dc47de036bf0bd1496c19a441d81c
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876796"
---
# <a name="variables"></a>Variables

Les variables représentent des emplacements de stockage. Chaque variable a un type qui détermine les valeurs qui peuvent être stockées dans la variable. C#est un langage de type sécurisé, et le C# compilateur garantit que les valeurs stockées dans les variables sont toujours du type approprié. La valeur d’une variable peut être modifiée par le biais de l’assignation ou `++` par `--` le biais de l’utilisation des opérateurs et.

Une variable doit être ***assignée définitivement*** ([assignation définie](variables.md#definite-assignment)) avant que sa valeur puisse être obtenue.

Comme décrit dans les sections suivantes, les variables sont ***initialement assignées*** ou ***initialement non assignées***. Une variable initialement assignée a une valeur initiale bien définie et est toujours considérée comme assignée définitivement. Une variable initialement non assignée n’a pas de valeur initiale. Pour qu’une variable initialement non assignée soit considérée comme définitivement assignée à un certain emplacement, une assignation à la variable doit se produire dans chaque chemin d’exécution possible menant à cet emplacement.

## <a name="variable-categories"></a>Catégories de variables

C#définit sept catégories de variables : les variables statiques, les variables d’instance, les éléments de tableau, les paramètres de valeur, les paramètres de référence, les paramètres de sortie et les variables locales. Les sections qui suivent décrivent chacune de ces catégories.

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
`x`est une variable statique, `y` est une variable d’instance `v[0]` , est un élément de `a` tableau, est un paramètre `b` de valeur, est un `c` paramètre de référence, est un `i` paramètre de sortie et est une variable locale .

### <a name="static-variables"></a>Variables statiques

Un champ déclaré avec le `static` modificateur est appelé une ***variable statique***. Une variable statique est entamée avant l’exécution du constructeur statique ([constructeurs statiques](classes.md#static-constructors)) pour son type conteneur et cesse d’exister lorsque le domaine d’application associé cesse d’exister.

La valeur initiale d’une variable statique est la valeur par défaut ([valeurs par défaut](variables.md#default-values)) du type de la variable.

À des fins de vérification de l’attribution définie, une variable statique est considérée comme initialement assignée.

### <a name="instance-variables"></a>Variables d’instance

Un champ déclaré sans le `static` modificateur est appelé une ***variable d’instance***.

#### <a name="instance-variables-in-classes"></a>Variables d’instance dans les classes

Une variable d’instance d’une classe intervient lorsqu’une nouvelle instance de cette classe est créée et cesse d’exister lorsqu’il n’existe aucune référence à cette instance et que le destructeur de l’instance (le cas échéant) a été exécuté.

La valeur initiale d’une variable d’instance d’une classe est la valeur par défaut ([valeurs par défaut](variables.md#default-values)) du type de la variable.

Dans le cadre de la vérification de l’assignation définie, une variable d’instance d’une classe est considérée comme initialement assignée.

#### <a name="instance-variables-in-structs"></a>Variables d’instance dans les structs

Une variable d’instance d’un struct a exactement la même durée de vie que la variable struct à laquelle elle appartient. En d’autres termes, lorsqu’une variable d’un type struct entre en existence ou cesse d’exister, il en est de même pour les variables d’instance du struct.

L’état d’assignation initial d’une variable d’instance d’un struct est le même que celui de la variable de struct conteneur. En d’autres termes, lorsqu’une variable de struct est considérée comme initialement assignée, il s’agit également de ses variables d’instance, et lorsqu’une variable de struct est considérée comme initialement non assignée, ses variables d’instance ne sont pas assignées de la même façon.

### <a name="array-elements"></a>Éléments de tableau

Les éléments d’un tableau existent lorsqu’une instance de tableau est créée et cessent d’exister lorsqu’il n’existe aucune référence à cette instance de tableau.

La valeur initiale de chacun des éléments d’un tableau est la valeur par défaut ([valeurs par défaut](variables.md#default-values)) du type des éléments du tableau.

Dans le cadre de la vérification de l’assignation définie, un élément de tableau est considéré comme initialement assigné.

### <a name="value-parameters"></a>Paramètres de valeur

Un paramètre déclaré sans `ref` modificateur ou `out` est un paramètre de ***valeur***.

Un paramètre de valeur intervient lors de l’appel de la fonction membre (méthode, constructeur d’instance, accesseur ou opérateur) ou de la fonction anonyme à laquelle le paramètre appartient, et est initialisée avec la valeur de l’argument fourni dans l’appel. Un paramètre de valeur cesse normalement d’exister lors du retour de la fonction membre ou de la fonction anonyme. Toutefois, si le paramètre de valeur est capturé par une fonction anonyme ([expressions de fonction anonymes](expressions.md#anonymous-function-expressions)), sa durée de vie s’étend au moins jusqu’à ce que le délégué ou l’arborescence d’expression créé à partir de cette fonction anonyme soit éligible pour garbage collection.

Dans le cadre de la vérification de l’attribution définie, un paramètre de valeur est considéré comme initialement assigné.

### <a name="reference-parameters"></a>Paramètres de référence

Un paramètre déclaré avec un `ref` modificateur est un ***paramètre de référence***.

Un paramètre de référence ne crée pas un nouvel emplacement de stockage. Au lieu de cela, un paramètre de référence représente le même emplacement de stockage que la variable donnée comme argument dans le membre de fonction ou l’appel de fonction anonyme. Ainsi, la valeur d’un paramètre de référence est toujours la même que la variable sous-jacente.

Les règles d’assignation définie suivantes s’appliquent aux paramètres de référence. Notez les différentes règles pour les paramètres de sortie décrits dans [paramètres de sortie](variables.md#output-parameters).

*  Une variable doit être assignée de manière définitive ([assignation définie](variables.md#definite-assignment)) avant de pouvoir être passée en tant que paramètre de référence dans un membre de fonction ou un appel de délégué.
*  Dans un membre de fonction ou une fonction anonyme, un paramètre de référence est considéré comme initialement assigné.

Dans une méthode d’instance ou un accesseur d’instance d’un `this` type struct, le mot clé se comporte exactement comme un paramètre de référence du type struct ([cet accès](expressions.md#this-access)).

### <a name="output-parameters"></a>Paramètres de sortie

Un paramètre déclaré avec un `out` modificateur est un ***paramètre de sortie***.

Un paramètre de sortie ne crée pas un nouvel emplacement de stockage. Au lieu de cela, un paramètre de sortie représente le même emplacement de stockage que la variable donnée comme argument dans le membre de fonction ou l’appel de délégué. Ainsi, la valeur d’un paramètre de sortie est toujours la même que la variable sous-jacente.

Les règles d’assignation définie suivantes s’appliquent aux paramètres de sortie. Notez les différentes règles pour les paramètres de référence décrites dans [paramètres de référence](variables.md#reference-parameters).

*  Une variable n’a pas besoin d’être définitivement assignée avant de pouvoir être passée en tant que paramètre de sortie dans un membre de fonction ou un appel de délégué.
*  Après l’achèvement normal d’un membre de fonction ou d’un appel de délégué, chaque variable qui a été passée comme paramètre de sortie est considérée comme affectée dans ce chemin d’exécution.
*  Dans un membre de fonction ou une fonction anonyme, un paramètre de sortie est considéré comme initialement non assigné.
*  Chaque paramètre de sortie d’un membre de fonction ou d’une fonction anonyme doit être assignée définitivement ([assignation définie](variables.md#definite-assignment)) avant que la fonction membre ou la fonction anonyme retourne normalement.

Dans un constructeur d’instance d’un type struct, `this` le mot clé se comporte exactement comme un paramètre de sortie du type struct ([cet accès](expressions.md#this-access)).

### <a name="local-variables"></a>Variables locales

Une ***variable locale*** est déclarée par un *local_variable_declaration*, qui peut se produire dans un *bloc*, un *for_Statement*, un *switch_Statement* ou un *using_statement*; ou par un *foreach_statement* ou un *specific_catch_clause* pour un *try_statement*.

La durée de vie d’une variable locale correspond à la partie de l’exécution du programme pendant laquelle le stockage est garanti comme étant réservé. Cette durée de vie étend au moins l’entrée dans le *bloc*, *for_Statement*, *switch_Statement*, *using_statement*, *foreach_statement*ou *specific_catch_clause* auquel elle est associée, jusqu’à ce que l’exécution de ce *bloc*, *for_Statement*, *switch_Statement*, *using_statement*, *foreach_statement*ou *specific_catch_clause* se termine de quelque manière que ce soit. (L’entrée d’un *bloc* délimité ou l’appel d’une méthode interrompt, mais ne termine pas, l’exécution du *bloc*en cours, *for_Statement*, *switch_Statement*, *using_statement*, *foreach_statement*ou *specific_ catch_clause*.) Si la variable locale est capturée par une fonction anonyme ([variables externes capturées](expressions.md#captured-outer-variables)), sa durée de vie s’étend au moins jusqu’à ce que le délégué ou l’arborescence de l’expression soit créée à partir de la fonction anonyme, avec tous les autres objets qui font référence à l’objet variable capturée, pouvant être garbage collection.

Si le *bloc*parent, *for_Statement*, *switch_Statement*, *using_statement*, *foreach_statement*ou *specific_catch_clause* est entré de manière récursive, une nouvelle instance de la variable locale est créée chaque Time, et son *local_variable_initializer*, le cas échéant, est évalué à chaque fois.

Une variable locale introduite par un *local_variable_declaration* n’est pas initialisée automatiquement et n’a donc pas de valeur par défaut. Dans le cadre de la vérification de l’attribution définie, une variable locale introduite par un *local_variable_declaration* est considérée comme initialement non assignée. Un *local_variable_declaration* peut inclure un *local_variable_initializer*, auquel cas la variable est considérée comme définitivement assignée uniquement après l’expression d’initialisation ([instructions de déclaration](variables.md#declaration-statements)).

Dans l’étendue d’une variable locale introduite par un *local_variable_declaration*, il s’agit d’une erreur de compilation pour faire référence à cette variable locale dans une position textuelle qui précède son *local_variable_declarator*. Si la déclaration de variable locale est implicite ([déclarations de variables locales](statements.md#local-variable-declarations)), il s’agit également d’une erreur qui fait référence à la variable dans son *local_variable_declarator*.

Une variable locale introduite par un *foreach_statement* ou un *specific_catch_clause* est considérée comme assignée de manière définitive dans sa portée entière.

La durée de vie réelle d’une variable locale dépend de l’implémentation. Par exemple, un compilateur peut déterminer statiquement qu’une variable locale dans un bloc est utilisée uniquement pour une petite partie de ce bloc. À l’aide de cette analyse, le compilateur peut générer du code qui donne au stockage de la variable une durée de vie plus faible que le bloc qui le contient.

Le stockage référencé par une variable de référence locale est récupéré indépendamment de la durée de vie de cette variable de référence locale ([gestion automatique](basic-concepts.md#automatic-memory-management)de la mémoire).

## <a name="default-values"></a>Valeurs par défaut

Les catégories de variables suivantes sont automatiquement initialisées à leurs valeurs par défaut :

*  variables statiques
*  Variables d’instance des instances de classe.
*  Éléments du tableau.

La valeur par défaut d’une variable dépend du type de la variable et est déterminée comme suit :

*  Pour une variable d’un *Value_type*, la valeur par défaut est la même que la valeur calculée par le constructeur par défaut de *Value_type*([constructeurs par défaut](types.md#default-constructors)).
*  Pour une variable d’un *reference_type*, la valeur par défaut `null`est.

L’initialisation aux valeurs par défaut est généralement effectuée en faisant en sorte que le gestionnaire de mémoire ou le garbage collector initialise la mémoire à tous les bits-zéro avant qu’il ne soit alloué pour utilisation. Pour cette raison, il est pratique d’utiliser All-bits-Zero pour représenter la référence null.

## <a name="definite-assignment"></a>Assignation définie

À un emplacement donné dans le code exécutable d’un membre de fonction, une variable est dite ***assignée*** de manière définitive si le compilateur peut prouver, par une analyse de workflow statique particulière ([règles précises pour déterminer l’assignation définie](variables.md#precise-rules-for-determining-definite-assignment)), que la variable a été initialisé automatiquement ou a été la cible d’au moins une attribution. Comme indiqué de façon informelle, les règles d’attribution définie sont les suivantes :

*  Une variable initialement assignée ([variables initialement affectées](variables.md#initially-assigned-variables)) est toujours considérée comme assignée définitivement.
*  Une variable initialement non assignée ([variables initialement non assignées](variables.md#initially-unassigned-variables)) est considérée comme définitivement assignée à un emplacement donné si tous les chemins d’exécution possibles conduisant à cet emplacement contiennent au moins l’un des éléments suivants :
    * Assignation simple ([assignation simple](expressions.md#simple-assignment)) dans laquelle la variable est l’opérande de gauche.
    * Une expression d’appel ([expressions d’appel](expressions.md#invocation-expressions)) ou une expression de création d’objet ([expressions de création d’objet](expressions.md#object-creation-expressions)) qui passe la variable en tant que paramètre de sortie.
    * Pour une variable locale, déclaration de variable locale ([déclarations de variable locale](statements.md#local-variable-declarations)) qui comprend un initialiseur de variable.

La spécification formelle sous-jacente aux règles informelles ci-dessus est décrite dans [variables initialement attribuées](variables.md#initially-assigned-variables), [variables initialement non assignées](variables.md#initially-unassigned-variables)et [règles précises pour déterminer l’assignation définie](variables.md#precise-rules-for-determining-definite-assignment).

Les États d’assignation définie des variables d’instance d’une variable *struct_type* sont suivis individuellement et collectivement. Outre les règles ci-dessus, les règles suivantes s’appliquent aux variables *struct_type* et à leurs variables d’instance :

*  Une variable d’instance est considérée comme assignée définitivement si sa variable *struct_type* conteneur est considérée comme assignée de manière définitive.
*  Une variable *struct_type* est considérée comme assignée définitivement si chacune de ses variables d’instance est considérée comme assignée de manière définitive.

L’assignation définie est une exigence dans les contextes suivants :

*  Une variable doit être assignée de manière définitive à chaque emplacement où sa valeur est obtenue. Cela permet de s’assurer que les valeurs non définies ne se produisent jamais. L’occurrence d’une variable dans une expression est considérée pour obtenir la valeur de la variable, sauf lorsque
    * la variable est l’opérande gauche d’une assignation simple.
    * la variable est transmise en tant que paramètre de sortie, ou
    * la variable est une variable *struct_type* et se produit comme opérande gauche d’un accès au membre.
*  Une variable doit être assignée de manière définitive à chaque emplacement où elle est passée en tant que paramètre de référence. Cela garantit que le membre de fonction appelé peut prendre en compte le paramètre de référence initialement assigné.
*  Tous les paramètres de sortie d’un membre de fonction doivent être assignés définitivement à chaque emplacement où le membre `return` de fonction retourne (via une instruction ou l’exécution atteint la fin du corps du membre de la fonction). Cela garantit que les fonctions membres ne retournent pas de valeurs non définies dans les paramètres de sortie, ce qui permet au compilateur de considérer un appel de membre de fonction qui prend une variable comme paramètre de sortie équivalent à une assignation à la variable.
*  La `this` variable d’un constructeur d’instance *struct_type* doit être assignée de manière définitive à chaque emplacement que le constructeur d’instance retourne.

### <a name="initially-assigned-variables"></a>Variables initialement attribuées

Les catégories de variables suivantes sont classées comme initialement attribuées :

*  variables statiques
*  Variables d’instance des instances de classe.
*  Variables d’instance de variables de struct initialement attribuées.
*  Éléments du tableau.
*  Paramètres de valeur.
*  Paramètres de référence.
*  Variables déclarées dans `catch` une clause ou `foreach` une instruction.

### <a name="initially-unassigned-variables"></a>Variables initialement non assignées

Les catégories de variables suivantes sont classées comme étant initialement non assignées :

*  Variables d’instance de variables de struct initialement non assignées.
*  Paramètres de sortie, y `this` compris la variable des constructeurs d’instance de struct.
*  Les variables locales, à l’exception de `catch` celles déclarées `foreach` dans une clause ou une instruction.

### <a name="precise-rules-for-determining-definite-assignment"></a>Règles précises pour déterminer l’assignation définie

Pour déterminer que chaque variable utilisée est assignée de manière définitive, le compilateur doit utiliser un processus qui est équivalent à celui décrit dans cette section.

Le compilateur traite le corps de chaque membre de fonction qui a une ou plusieurs variables initialement non assignées. Pour chaque variable *v*non assignée initialement, le compilateur détermine un ***État d’assignation définie*** pour *v* à chacun des points suivants dans le membre de fonction :

*  Au début de chaque instruction
*  Au point de terminaison ([points de terminaison et accessibilité](statements.md#end-points-and-reachability)) de chaque instruction
*  Sur chaque arc qui transfère le contrôle à une autre instruction ou jusqu’au point de terminaison d’une instruction
*  Au début de chaque expression
*  À la fin de chaque expression

L’état d’assignation définie de *v* peut être :

*  Assigné de manière définitive. Cela indique qu’une valeur a été assignée à *v* pour tous les flux de contrôle possibles à ce stade.
*  Pas définitivement assigné. Pour l’état d’une variable à la fin d’une expression de type `bool`, l’état d’une variable qui n’est pas définitivement assignée peut (mais n’est pas nécessairement) appartenir à l’un des sous-États suivants :
    * Assigné définitivement après une expression true. Cet état indique que *v* est définitivement affecté si l’expression booléenne est évaluée comme true, mais n’est pas nécessairement affectée si l’expression booléenne est évaluée comme false.
    * Assigné définitivement après une expression false. Cet état indique que *v* est définitivement affecté si l’expression booléenne est évaluée comme false, mais n’est pas nécessairement affectée si l’expression booléenne est évaluée comme true.

Les règles suivantes régissent la façon dont l’état d’une variable *v* est déterminé à chaque emplacement.

#### <a name="general-rules-for-statements"></a>Règles générales pour les instructions

*  *v* n’est pas définitivement assignée au début d’un corps de membre de fonction.
*  *v* est définitivement assignée au début de toute instruction inaccessible.
*  L’état d’assignation définie de *v* au début d’une autre instruction est déterminé en vérifiant l’état d’assignation définie de *v* sur tous les transferts de workflow de contrôle qui ciblent le début de cette instruction. Si (et seulement si) *v* est définitivement assigné sur tous les transferts de ce type de contrôle, *v* est définitivement assigné au début de l’instruction. L’ensemble des transferts de workflow possibles est déterminé de la même façon que pour la vérification de l’accessibilité des instructions ([points de terminaison et accessibilité](statements.md#end-points-and-reachability)).
*  État d’assignation définie de *v* au point de terminaison d’un bloc, `checked` `do`, `foreach` `unchecked` `if` `while`,,,, `for`,, `lock` `using`, ou `switch`l’instruction est déterminée en vérifiant l’état d’assignation définie de *v* sur tous les transferts de workflow de contrôle qui ciblent le point de terminaison de cette instruction. Si *v* est définitivement assignée à tous les transferts de ce type de contrôle, *v* est définitivement assigné au point de terminaison de l’instruction. Dispose *v* n’est pas définitivement assigné au point de terminaison de l’instruction. L’ensemble des transferts de workflow possibles est déterminé de la même façon que pour la vérification de l’accessibilité des instructions ([points de terminaison et accessibilité](statements.md#end-points-and-reachability)).

#### <a name="block-statements-checked-and-unchecked-statements"></a>Instructions de bloc, instructions vérifiées et non vérifiées

L’état d’assignation définie de *v* sur le transfert de contrôle vers la première instruction de la liste d’instructions dans le bloc (ou jusqu’au point de terminaison du bloc, si la liste d’instructions est vide) est le même que l’instruction d’assignation définie de *v* avant le bloc instruction `checked`, ou `unchecked` .

#### <a name="expression-statements"></a>Instructions d’expression

Pour une instruction d’expression *stmt* qui se compose de l’expression *expr*:

*  *v* a le même état d’assignation définie au début de *expr* qu’au début de *stmt*.
*  Si *v* est assignée de manière définitive à la fin de *expr*, elle est définitivement assignée au point de terminaison de *stmt*; dispose elle n’est pas définitivement assignée au point de terminaison de *stmt*.

#### <a name="declaration-statements"></a>Instructions de déclaration

*  Si *stmt* est une instruction de déclaration sans initialiseurs, alors *v* a le même état d’assignation définie au point de terminaison de *stmt* qu’au début de *stmt*.
*  Si *stmt* est une instruction de déclaration avec des initialiseurs, l’état d’assignation définie pour *v* est déterminé comme si *stmt* était une liste d’instructions, avec une instruction d’assignation pour chaque déclaration avec un initialiseur (dans l’ordre de déclaration).

#### <a name="if-statements"></a>Instructions If

Pour une `if` instruction *stmt* de la forme :
```csharp
if ( expr ) then_stmt else else_stmt
```

*  *v* a le même état d’assignation définie au début de *expr* qu’au début de *stmt*.
*  Si *v* est définitivement assignée à la fin de *expr*, il est définitivement assigné sur le transfert du workflow de contrôle à *then_stmt* et *else_stmt* ou sur le point de terminaison de *stmt* s’il n’y a aucune clause Else.
*  Si *v* a l’état « définitivement assigné après une expression true » à la fin de *expr*, il est définitivement affecté sur le transfert du workflow de contrôle à *then_stmt*et n’est pas définitivement assigné sur le transfert de workflow à *else_ stmt* ou jusqu’au point de terminaison de *stmt* s’il n’y a aucune clause Else.
*  Si *v* a l’état « définitivement assigné après une expression false » à la fin de *expr*, il est définitivement assigné sur le transfert du workflow de contrôle à *else_stmt*et n’est pas définitivement assigné sur le transfert du workflow de contrôle à *then_stmt* . Elle est définitivement assignée au point de terminaison de *stmt* si et seulement si elle est définitivement assignée au point de terminaison de *then_stmt*.
*  Dans le cas contraire, *v* est considéré comme n’étant pas définitivement affecté sur le transfert du workflow de contrôle vers *then_stmt* ou *else_stmt*, ou vers le point de terminaison de *stmt* s’il n’y a aucune clause Else.

#### <a name="switch-statements"></a>Instructions Switch

Dans une `switch` instruction *stmt* avec une expression de contrôle *expr*:

*  L’état d’assignation définie de *v* au début de *expr* est identique à l’état *v* au début de *stmt*.
*  L’état d’assignation définie de *v* sur le transfert du workflow de contrôle vers une liste d’instructions de bloc switch accessible est le même que l’état d’assignation définie de *v* à la fin de *expr*.

#### <a name="while-statements"></a>Instructions while

Pour une `while` instruction *stmt* de la forme :
```csharp
while ( expr ) while_body
```

*  *v* a le même état d’assignation définie au début de *expr* qu’au début de *stmt*.
*  Si *v* est définitivement assignée à la fin de *expr*, il est définitivement affecté sur le transfert du workflow de contrôle à *while_body* et jusqu’au point de terminaison de *stmt*.
*  Si *v* a l’état « définitivement assigné après une expression true » à la fin de *expr*, il est définitivement affecté au transfert du workflow de contrôle à *while_body*, mais il n’est pas définitivement assigné au point de terminaison de *stmt*.
*  Si *v* a l’état « définitivement assigné après une expression false » à la fin de *expr*, il est définitivement affecté sur le transfert du workflow de contrôle au point de terminaison de *stmt*, mais pas définitivement assigné sur le transfert du workflow de contrôle à *quand _body*.

#### <a name="do-statements"></a>Instructions do

Pour une `do` instruction *stmt* de la forme :
```csharp
do do_body while ( expr ) ;
```

*  *v* a le même état d’assignation définie sur le transfert du workflow de contrôle entre le début de *stmt* et *do_body* qu’au début de *stmt*.
*  *v* a le même état d’assignation définie au début de *expr* comme au point de terminaison de *do_body*.
*  Si *v* est définitivement assignée à la fin de *expr*, elle est définitivement assignée sur le transfert du workflow de contrôle vers le point de terminaison de *stmt*.
*  Si *v* a l’état « définitivement assigné après une expression false » à la fin de *expr*, il est définitivement affecté sur le transfert du workflow de contrôle au point de terminaison de *stmt*.

#### <a name="for-statements"></a>Pour les instructions

Vérification de l’assignation définie pour `for` une instruction de la forme :
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

Si le *for_condition* est omis de l' `for` instruction, l’évaluation de l’assignation définie se poursuit comme si *for_condition* était remplacé par `true` dans l’expansion ci-dessus.

#### <a name="break-continue-and-goto-statements"></a>Instructions break, continue et GOTO

L’état d’assignation définie de *v* sur le transfert de workflow de contrôle provoqué `break`par `continue`une instruction `goto` , ou est le même que l’état d’assignation définie de *v* au début de l’instruction.

#### <a name="throw-statements"></a>Instructions throw

Pour une instruction *stmt* au format
```csharp
throw expr ;
```

L’état d’assignation définie de *v* au début de *expr* est le même que l’état d’assignation définie de *v* au début de *stmt*.

#### <a name="return-statements"></a>Instructions Return

Pour une instruction *stmt* au format
```csharp
return expr ;
```

*  L’état d’assignation définie de *v* au début de *expr* est le même que l’état d’assignation définie de *v* au début de *stmt*.
*  Si *v* est un paramètre de sortie, il doit être affecté définitivement :
    * après *expr*
    * ou `finally` à la fin du bloc d’un `try` - ou`return` qui englobe l’instruction. `catch` `try` - `finally` - `finally`

Pour une instruction stmt de la forme :
```csharp
return ;
```

*  Si *v* est un paramètre de sortie, il doit être affecté définitivement :
    * avant *stmt*
    * ou `finally` à la fin du bloc d’un `try` - ou`return` qui englobe l’instruction. `catch` `try` - `finally` - `finally`

#### <a name="try-catch-statements"></a>Instructions Try-Catch

Pour une instruction *stmt* de la forme :
```csharp
try try_block
catch(...) catch_block_1
...
catch(...) catch_block_n
```

*  L’état d’assignation définie de *v* au début de *try_block* est identique à l’état d’assignation définie de *v* au début de *stmt*.
*  L’état d’assignation définie de *v* au début de *catch_block_i* (pour Any *i*) est identique à l’état d’assignation définie de *v* au début de *stmt*.
*  L’état d’assignation définie de *v* à l’extrémité de *stmt* est définitivement affecté si (et seulement si) *v* est définitivement assigné au point de terminaison de *try_block* et chaque *catch_block_i* (pour chaque *i* de 1 à *n* ).

#### <a name="try-finally-statements"></a>Instructions try-finally

Pour une `try` instruction *stmt* de la forme :
```csharp
try try_block finally finally_block
```

*  L’état d’assignation définie de *v* au début de *try_block* est identique à l’état d’assignation définie de *v* au début de *stmt*.
*  L’état d’assignation définie de *v* au début de *finally_block* est identique à l’état d’assignation définie de *v* au début de *stmt*.
*  L’état d’assignation définie de *v* à l’extrémité de *stmt* est définitivement affecté si (et seulement si) au moins l’une des conditions suivantes est vraie :
    * *v* est définitivement assignée au point de terminaison de *try_block*
    * *v* est définitivement assignée au point de terminaison de *finally_block*

Si un transfert de workflow de contrôle (par exemple `goto` , une instruction) commence dans *try_block*et se termine en dehors de *try_block*, *v* est également considéré comme définitivement assigné sur ce transfert de workflow si *v* est assigné de manière définitive au point de terminaison de *finally_block*. (Il ne s’agit pas d’un seul si, si *v* est définitivement affecté pour une autre raison sur ce transfert de workflow, il est toujours considéré comme définitivement affecté.)

#### <a name="try-catch-finally-statements"></a>Instructions try-catch-finally

Analyse d’assignation définie pour une `try` - `catch` - instruction delaforme:`finally`
```csharp
try try_block
catch(...) catch_block_1
...
catch(...) catch_block_n
finally *finally_block*
```
est effectué comme si l’instruction était une `try` - `try` - `finally` instruction englobant une `catch` instruction :
```csharp
try {
    try try_block
    catch(...) catch_block_1
    ...
    catch(...) catch_block_n
}
finally finally_block
```

L’exemple suivant montre comment les différents blocs d’une `try` instruction ([instruction try](statements.md#the-try-statement)) affectent l’assignation définie.
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

Pour une `foreach` instruction *stmt* de la forme :
```csharp
foreach ( type identifier in expr ) embedded_statement
```

*  L’état d’assignation définie de *v* au début de *expr* est identique à l’état *v* au début de *stmt*.
*  L’état d’assignation définie de *v* sur le transfert du workflow de contrôle vers *embedded_statement* ou vers le point de terminaison de *stmt* est le même que l’état de *v* à la fin de *expr*.

#### <a name="using-statements"></a>Instructions using

Pour une `using` instruction *stmt* de la forme :
```csharp
using ( resource_acquisition ) embedded_statement
```

*  L’état d’assignation définie de *v* au début de *resource_acquisition* est identique à l’état *v* au début de *stmt*.
*  L’état d’assignation définie de *v* sur le transfert du workflow de contrôle vers *embedded_statement* est identique à l’état *v* à la fin de *resource_acquisition*.

#### <a name="lock-statements"></a>Instructions Lock

Pour une `lock` instruction *stmt* de la forme :
```csharp
lock ( expr ) embedded_statement
```

*  L’état d’assignation définie de *v* au début de *expr* est identique à l’état *v* au début de *stmt*.
*  L’état d’assignation définie de *v* sur le transfert du workflow de contrôle vers *embedded_statement* est identique à l’état *v* à la fin de *expr*.

#### <a name="yield-statements"></a>Instructions yield

Pour une `yield return` instruction *stmt* de la forme :
```csharp
yield return expr ;
```

*  L’état d’assignation définie de *v* au début de *expr* est identique à l’état *v* au début de *stmt*.
*  L’état d’assignation définie de *v* à la fin de *stmt* est identique à l’état *v* à la fin de *expr*.
*  Une `yield break` instruction n’a aucun effet sur l’état d’assignation définie.

#### <a name="general-rules-for-simple-expressions"></a>Règles générales pour les expressions simples

La règle suivante s’applique à ces types d’expressions : les littéraux ([littéraux](expressions.md#literals)), les noms simples ([noms simples](expressions.md#simple-names)), les expressions d’accès au membre ([accès aux membres](expressions.md#member-access)), les expressions d’accès de base non indexées ([accès de base](expressions.md#base-access)) `typeof`.expressions ([opérateur typeof](expressions.md#the-typeof-operator)), expressions de valeur par défaut ([expressions de valeur par défaut](expressions.md#default-value-expressions)) et `nameof` expressions ([expressions Nameof](expressions.md#nameof-expressions)).

*  L’état d’assignation définie de *v* à la fin d’une telle expression est le même que l’état d’assignation définie de *v* au début de l’expression.

#### <a name="general-rules-for-expressions-with-embedded-expressions"></a>Règles générales pour les expressions avec des expressions incorporées

Les règles suivantes s’appliquent à ces types d’expressions : expressions entre parenthèses ([expressions entre parenthèses](expressions.md#parenthesized-expressions)), expressions d’accès aux éléments ([accès aux éléments](expressions.md#element-access)), expressions d’accès de base avec indexation ([accès de base](expressions.md#base-access)), incrémentation et expressions de décrémentation ([opérateurs d’incrémentation et de décrémentation suffixés](expressions.md#postfix-increment-and-decrement-operators), [opérateurs de préfixe d’incrémentation et de décrémentation](expressions.md#prefix-increment-and-decrement-operators)), `~`expressions de cast (expressions de[Cast](expressions.md#cast-expressions)), unaire `+` `-`,, `*`,expressions, Binary `+` `-` ,`*`,, ,`%`, ,`>>`,, ,,`>`, `<<` `<` `/` `<=` `>=` `==`, `!=`, ,`is`,, ,`^`expressions ([opérateurs arithmétiques](expressions.md#arithmetic-operators), [opérateurs de décalage](expressions.md#shift-operators), relationnel et test de type `|` `as` `&` [ opérateurs](expressions.md#relational-and-type-testing-operators), [opérateurs logiques](expressions.md#logical-operators)), expressions d’assignation composée ( `checked` [assignation composée](expressions.md#compound-assignment)) `unchecked` et expressions ([opérateurs activés et désactivés](expressions.md#the-checked-and-unchecked-operators)), plus un tableau et un délégué expressions de création ([opérateur New](expressions.md#the-new-operator)).

Chacune de ces expressions a une ou plusieurs sous-expressions qui sont évaluées de manière non conditionnelle dans un ordre fixe. Par exemple, l’opérateur `%` binaire évalue le côté gauche de l’opérateur, puis le côté droit. Une opération d’indexation évalue l’expression indexée, puis évalue chacune des expressions d’index, dans l’ordre, de gauche à droite. Pour une expression *expr*, qui contient des sous-expressions *E1, E2,...,* en, évaluées dans cet ordre :

*  L’état d’assignation définie de *v* au début de *E1* est le même que l’état d’assignation définie au début de *expr*.
*  L’état d’assignation définie de *v* au début de l' *AE* (*i* supérieur à un) est identique à l’état d’assignation définie à la fin de la sous-expression précédente.
*  L’état d’assignation définie de *v* à la fin de *expr* est identique à l’état d’assignation définie à la *fin de la*

#### <a name="invocation-expressions-and-object-creation-expressions"></a>Expressions d’appel et expressions de création d’objet

Pour une expression d’appel *expr* de la forme :
```csharp
primary_expression ( arg1 , arg2 , ... , argN )
```
ou une expression de création d’objet de la forme suivante :
```csharp
new type ( arg1 , arg2 , ... , argN )
```

*  Pour une expression d’appel, l’état d’assignation définie de *v* avant *primary_expression* est identique à l’état *v* avant *expr*.
*  Pour une expression d’appel, l’état d’assignation définie de *v* avant *Arg1* est le même que l’état de *v* après *primary_expression*.
*  Pour une expression de création d’objet, l’état d’assignation définie de *v* avant *Arg1* est le même que l’état de *v* avant *expr*.
*  Pour chaque argument *Argi*, l’état d’assignation définie de *v* après *Argi* est déterminé par les règles d’expression normales, en ignorant `ref` les `out` modificateurs ou.
*  Pour chaque argument *Argi* pour un *i* supérieur à un, l’état d’assignation définie de *v* avant *Argi* est identique à l’état *v* après l' *argument précédent.*
*  Si la variable *v* est `out` passée comme argument (c’est-à-dire un argument du `out v`formulaire) dans l’un des arguments, l’état *v* après *expr* est définitivement assigné. Dispose l’état de *v* après *expr* est identique à l’état *v* après *argN*.
*  Pour les initialiseurs de tableau ([expressions de création de tableau](expressions.md#array-creation-expressions)), les initialiseurs d’objets ([initialiseurs d’objets](expressions.md#object-initializers)), les initialiseurs de collection ([initialiseurs de collection](expressions.md#collection-initializers)) et les initialiseurs d’objets anonymes (création d'[objets anonymes expressions](expressions.md#anonymous-object-creation-expressions)), l’état d’assignation défini est déterminé par l’expansion que ces constructions sont définies en termes de.

#### <a name="simple-assignment-expressions"></a>Expressions d’assignation simples

Pour une expression *expr* de la forme `w = expr_rhs`:

*  L’état d’assignation définie de *v* avant *expr_rhs* est identique à l’état d’assignation définie de *v* avant *expr*.
*  L’état d’assignation définie de *v* après *expr* est déterminé par :
   * Si *w* est la même variable que *v*, l’état d’assignation définie de *v* après *expr* est définitivement assigné.
   * Sinon, si l’assignation se produit dans le constructeur d’instance d’un type struct, si *w* est un accès à la propriété désignant une propriété implémentée automatiquement *P* sur l’instance en cours de construction et que *v* est le champ de stockage masqué de *P*, puis l’état d’assignation définie de *v* après *expr* est définitivement assigné.
   * Dans le cas contraire, l’état d’assignation définie de *v* après *expr* est le même que l’état d’assignation définie de *v* après *expr_rhs*.

#### <a name="-conditional-and-expressions"></a>& les expressions & (conditionnelles et)

Pour une expression *expr* de la forme `expr_first && expr_second`:

*  L’état d’assignation définie de *v* avant *expr_first* est identique à l’état d’assignation définie de *v* avant *expr*.
*  L’état d’assignation définie de *v* avant *expr_second* est définitivement assigné si l’état de *v* après *expr_first* est défini de manière définitive ou « définitivement assigné après l’expression true ». Dans le cas contraire, elle n’est pas définitivement assignée.
*  L’état d’assignation définie de *v* après *expr* est déterminé par :
    * Si *expr_first* est une expression constante avec la valeur `false`, l’état d’assignation définie de *v* après *expr* est le même que l’état d’assignation définie de *v* après *expr_first*.
    * Sinon, si l’état de *v* après *expr_first* est définitivement assigné, l’état *v* après *expr* est définitivement assigné.
    * Sinon, si l’état de *v* après *expr_second* est assignée définitivement, et que l’état de *v* après *expr_first* est « définitivement assigné après false expression », alors l’état de *v* après *expr* est définitivement ont.
    * Dans le cas contraire, si l’état de *v* après *expr_second* est définitivement assigné ou « définitivement assigné après une expression true », l’état *v* après *expr* est « définitivement assigné après une expression true ».
    * Sinon, si l’état de *v* après *expr_first* est « définitivement assigné après une expression false » et que l’état de *v* après *expr_second* est « définitivement assigné après une expression false », alors l’état de *v* après  *Expr* est « définitivement assigné après false expression ».
    * Dans le cas contraire, l’état de *v* après *expr* n’est pas définitivement assigné.

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
la variable `i` est considérée comme définitivement assignée dans l’une des instructions `if` incorporées d’une instruction, mais pas dans l’autre. Dans l' `if` instruction de la `F`méthode, la `i` variable est définitivement assignée dans la première instruction incorporée, `(i = y)` car l’exécution de l’expression précède toujours l’exécution de cette instruction incorporée. En revanche, la variable `i` n’est pas définitivement assignée dans la deuxième instruction `x >= 0` incorporée, car peut avoir testé false `i` , ce qui entraîne la non-attribution de la variable.

#### <a name="-conditional-or-expressions"></a>|| expressions (conditionnelles ou)

Pour une expression *expr* de la forme `expr_first || expr_second`:

*  L’état d’assignation définie de *v* avant *expr_first* est identique à l’état d’assignation définie de *v* avant *expr*.
*  L’état d’assignation définie de *v* avant *expr_second* est définitivement assigné si l’état de *v* après *expr_first* est défini de manière définitive ou « définitivement assigné après une expression false ». Dans le cas contraire, elle n’est pas définitivement assignée.
*  L’instruction d’assignation définie de *v* après *expr* est déterminée par :
    * Si *expr_first* est une expression constante avec la valeur `true`, l’état d’assignation définie de *v* après *expr* est le même que l’état d’assignation définie de *v* après *expr_first*.
    * Sinon, si l’état de *v* après *expr_first* est définitivement assigné, l’état *v* après *expr* est définitivement assigné.
    * Dans le cas contraire, si l’état de *v* après *expr_second* est assignée définitivement, et que l’état de *v* après *expr_first* est « définitivement assigné après true expression », alors l’état de *v* après *expr* est définitivement ont.
    * Sinon, si l’état de *v* après *expr_second* est définitivement assigné ou « définitivement assigné après false expression », alors l’état de *v* après *expr* est « définitivement assigné après une expression false ».
    * Sinon, si l’état de *v* après *expr_first* est « définitivement assigné après une expression true », et que l’état *v* après *expr_second* est « définitivement assigné après l’expression true », alors l’état de *v* après *expr* est « définitivement assigné après l’expression true ».
    * Dans le cas contraire, l’état de *v* après *expr* n’est pas définitivement assigné.

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
la variable `i` est considérée comme définitivement assignée dans l’une des instructions `if` incorporées d’une instruction, mais pas dans l’autre. Dans l' `if` instruction de la `G`méthode, la `i` variable est définitivement assignée dans la deuxième instruction incorporée, `(i = y)` car l’exécution de l’expression précède toujours l’exécution de cette instruction incorporée. En revanche, la variable `i` n’est pas définitivement assignée dans la première instruction `x >= 0` incorporée, car peut avoir testé la valeur `i` true, ce qui a pour effet que la variable n’est pas assignée.

#### <a name="-logical-negation-expressions"></a>! expressions (négation logique)

Pour une expression *expr* de la forme `! expr_operand`:

*  L’état d’assignation définie de *v* avant *expr_operand* est identique à l’état d’assignation définie de *v* avant *expr*.
*  L’état d’assignation définie de *v* après *expr* est déterminé par :
    * Si l’état *v* après * expr_operand * est définitivement affecté, l’état *v* après *expr* est définitivement assigné.
    * Si l’état de *v* après * expr_operand * n’est pas définitivement assigné, l’état de *v* après *expr* n’est pas définitivement assigné.
    * Si l’état *v* après * expr_operand * est « définitivement assigné après une expression false », alors l’état de *v* après *expr* est « définitivement assigné après l’expression true ».
    * Si l’état de *v* après * expr_operand * est « définitivement assigné après l’expression true », alors l’état de *v* après *expr* est « définitivement assigné après une expression false ».

#### <a name="-null-coalescing-expressions"></a>?? expressions (fusion de valeurs null)

Pour une expression *expr* de la forme `expr_first ?? expr_second`:

*  L’état d’assignation définie de *v* avant *expr_first* est identique à l’état d’assignation définie de *v* avant *expr*.
*  L’état d’assignation définie de *v* avant *expr_second* est identique à l’état d’assignation définie de *v* après *expr_first*.
*  L’instruction d’assignation définie de *v* après *expr* est déterminée par :
    * Si *expr_first* est une expression constante ([expressions constantes](expressions.md#constant-expressions)) avec la valeur null, l’état de *v* après *expr* est identique à l’état *v* après *expr_second*.
*  Dans le cas contraire, l’état de *v* après *expr* est le même que l’état d’assignation définie de *v* après *expr_first*.

#### <a name="-conditional-expressions"></a>?: (conditionnel), expressions

Pour une expression *expr* de la forme `expr_cond ? expr_true : expr_false`:

*  L’état d’assignation définie de *v* avant *expr_cond* est identique à l’état *v* avant *expr*.
*  L’état d’assignation définie de *v* avant *expr_true* est définitivement affecté si et seulement si l’un des éléments suivants est présent :
    * *expr_cond* est une expression constante avec la valeur`false`
    * État de *v* après que *expr_cond* a été assigné définitivement ou « définitivement assigné après l’expression true ».
*  L’état d’assignation définie de *v* avant *expr_false* est définitivement affecté si et seulement si l’un des éléments suivants est présent :
    * *expr_cond* est une expression constante avec la valeur`true`
*  État de *v* après que *expr_cond* a été assigné définitivement ou « définitivement assigné après une expression false ».
*  L’état d’assignation définie de *v* après *expr* est déterminé par :
    * Si *expr_cond* est une expression constante ([expressions constantes](expressions.md#constant-expressions)) avec `true` la valeur, l’état de *v* après *expr* est identique à l’état *v* après *expr_true*.
    * Sinon, si *expr_cond* est une expression constante ([expressions constantes](expressions.md#constant-expressions)) avec `false` la valeur, l’état de *v* après *expr* est identique à l’état *v* après *expr_false*.
    * Sinon, si l’état de *v* après *expr_true* est assignée définitivement et que l’état de *v* après l’assignation définitive de *expr_false* , l’état *v* après *expr* est définitivement assigné.
    * Dans le cas contraire, l’état de *v* après *expr* n’est pas définitivement assigné.

#### <a name="anonymous-functions"></a>Fonctions anonymes

Pour un *expr* *lambda_expression* ou *anonymous_method_expression* avec un corps (de type *bloc* ou expression *) :*

*  L’état d’assignation définie d’une variable externe *v* avant le *corps* est identique à l’état *v* avant *expr*. Autrement dit, l’état d’assignation définie des variables externes est hérité du contexte de la fonction anonyme.
*  L’état d’assignation définie d’une variable externe *v* après *expr* est identique à l’état *v* avant *expr*.

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
génère une erreur au moment de la `max` compilation, car n’est pas définitivement assignée où la fonction anonyme est déclarée. L’exemple
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
génère également une erreur au moment de la compilation, car `n` l’assignation à dans la fonction anonyme n’a aucun effet sur l’état `n` d’assignation définie de l’extérieur de la fonction anonyme.

## <a name="variable-references"></a>Références de variables

Un *variable_reference* est une *expression* classée en tant que variable. Un *variable_reference* désigne un emplacement de stockage qui est accessible à la fois pour extraire la valeur actuelle et pour stocker une nouvelle valeur.

```antlr
variable_reference
    : expression
    ;
```

En C et C++, un *variable_reference* est appelé *lvalue*.

## <a name="atomicity-of-variable-references"></a>Atomicité des références de variable

Les lectures et les écritures des types de données suivants sont `bool`des `char`types de référence Atomic : `uint`, `int`, `float` `short` `byte` `sbyte`,,, `ushort`,,, et. En outre, les lectures et écritures de types ENUM avec un type sous-jacent dans la liste précédente sont également atomiques. Les lectures et écritures d’autres types, `long`notamment `ulong` `double`,,, `decimal`et, ainsi que les types définis par l’utilisateur, ne sont pas nécessairement atomiques. Outre les fonctions de bibliothèque conçues à cet effet, il n’existe aucune garantie de lecture-modification/écriture atomique, comme dans le cas de l’incrémentation ou de la décrémentation.

