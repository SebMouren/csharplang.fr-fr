---
ms.openlocfilehash: a4b0fbbc600eaf1e705ad8e6bd9fcecb44100761
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484439"
---
# <a name="local-functions"></a><span data-ttu-id="5cf17-101">Fonctions locales</span><span class="sxs-lookup"><span data-stu-id="5cf17-101">Local functions</span></span>

<span data-ttu-id="5cf17-102">Nous étendons C# pour prendre en charge la déclaration de fonctions dans la portée de bloc.</span><span class="sxs-lookup"><span data-stu-id="5cf17-102">We extend C# to support the declaration of functions in block scope.</span></span> <span data-ttu-id="5cf17-103">Les fonctions locales peuvent utiliser (capturer) des variables de la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="5cf17-103">Local functions may use (capture) variables from the enclosing scope.</span></span>

<span data-ttu-id="5cf17-104">Le compilateur utilise l’analyse de Flow pour détecter les variables utilisées par une fonction locale avant de lui assigner une valeur.</span><span class="sxs-lookup"><span data-stu-id="5cf17-104">The compiler uses flow analysis to detect which variables a local function uses before assigning it a value.</span></span> <span data-ttu-id="5cf17-105">Chaque appel de la fonction requiert que ces variables soient définitivement assignées.</span><span class="sxs-lookup"><span data-stu-id="5cf17-105">Every call of the function requires such variables to be definitely assigned.</span></span> <span data-ttu-id="5cf17-106">De même, le compilateur détermine les variables qui sont définitivement assignées au retour.</span><span class="sxs-lookup"><span data-stu-id="5cf17-106">Similarly the compiler determines which variables are definitely assigned on return.</span></span> <span data-ttu-id="5cf17-107">Ces variables sont considérées comme définitivement assignées après l’appel de la fonction locale.</span><span class="sxs-lookup"><span data-stu-id="5cf17-107">Such variables are considered definitely assigned after the local function is invoked.</span></span>

<span data-ttu-id="5cf17-108">Les fonctions locales peuvent être appelées à partir d’un point lexical avant sa définition.</span><span class="sxs-lookup"><span data-stu-id="5cf17-108">Local functions may be called from a lexical point before its definition.</span></span> <span data-ttu-id="5cf17-109">Les instructions de déclaration de fonction locale ne génèrent pas d’avertissement lorsqu’elles ne sont pas accessibles.</span><span class="sxs-lookup"><span data-stu-id="5cf17-109">Local function declaration statements do not cause a warning when they are not reachable.</span></span>

<span data-ttu-id="5cf17-110">TODO : _écrire la spécification_</span><span class="sxs-lookup"><span data-stu-id="5cf17-110">TODO: _WRITE SPEC_</span></span>

## <a name="syntax-grammar"></a><span data-ttu-id="5cf17-111">Grammaire de syntaxe</span><span class="sxs-lookup"><span data-stu-id="5cf17-111">Syntax grammar</span></span>

<span data-ttu-id="5cf17-112">Cette grammaire est représentée comme une différence par rapport à la grammaire actuelle des spécifications.</span><span class="sxs-lookup"><span data-stu-id="5cf17-112">This grammar is represented as a diff from the current spec grammar.</span></span>

```diff
declaration-statement
    : local-variable-declaration ';'
    | local-constant-declaration ';'
+   | local-function-declaration
    ;

+local-function-declaration
+   : local-function-header local-function-body
+   ;

+local-function-header
+   : local-function-modifiers? return-type identifier type-parameter-list?
+       ( formal-parameter-list? ) type-parameter-constraints-clauses
+   ;

+local-function-modifiers
+   : (async | unsafe)
+   ;

+local-function-body
+   : block
+   | arrow-expression-body
+   ;
```

<span data-ttu-id="5cf17-113">Les fonctions locales peuvent utiliser des variables définies dans la portée englobante.</span><span class="sxs-lookup"><span data-stu-id="5cf17-113">Local functions may use variables defined in the enclosing scope.</span></span> <span data-ttu-id="5cf17-114">L’implémentation actuelle requiert que chaque variable lue à l’intérieur d’une fonction locale soit assignée de manière définitive, comme si vous exécutiez la fonction locale à son point de définition.</span><span class="sxs-lookup"><span data-stu-id="5cf17-114">The current implementation requires that every variable read inside a local function be definitely assigned, as if executing the local function at its point of definition.</span></span> <span data-ttu-id="5cf17-115">En outre, la définition de fonction locale doit avoir été « exécutée » à un point d’utilisation quelconque.</span><span class="sxs-lookup"><span data-stu-id="5cf17-115">Also, the local function definition must have been "executed" at any use point.</span></span>

<span data-ttu-id="5cf17-116">Après avoir expérimenté un peu (par exemple, il n’est pas possible de définir deux fonctions locales mutuellement récursives), nous avons depuis revu la manière dont nous voulons que l’assignation définie fonctionne.</span><span class="sxs-lookup"><span data-stu-id="5cf17-116">After experimenting with that a bit (for example, it is not possible to define two mutually recursive local functions), we've since revised how we want the definite assignment to work.</span></span> <span data-ttu-id="5cf17-117">La révision (pas encore implémentée) est que toutes les variables locales lues dans une fonction locale doivent être assignées de manière définitive à chaque appel de la fonction locale.</span><span class="sxs-lookup"><span data-stu-id="5cf17-117">The revision (not yet implemented) is that all local variables read in a local function must be definitely assigned at each invocation of the local function.</span></span> <span data-ttu-id="5cf17-118">C’est en fait plus subtil qu’il ne le semble, et il reste beaucoup de travail à faire fonctionner.</span><span class="sxs-lookup"><span data-stu-id="5cf17-118">That's actually more subtle than it sounds, and there is a bunch of work remaining to make it work.</span></span> <span data-ttu-id="5cf17-119">Une fois cette opération effectuée, vous pouvez déplacer vos fonctions locales à la fin de son bloc englobant.</span><span class="sxs-lookup"><span data-stu-id="5cf17-119">Once it is done you'll be able to move your local functions to the end of its enclosing block.</span></span>

<span data-ttu-id="5cf17-120">Les nouvelles règles d’assignation définie sont incompatibles avec l’inférence du type de retour d’une fonction locale. nous allons donc supprimer la prise en charge de l’inférence du type de retour.</span><span class="sxs-lookup"><span data-stu-id="5cf17-120">The new definite assignment rules are incompatible with inferring the return type of a local function, so we'll likely be removing support for inferring the return type.</span></span>

<span data-ttu-id="5cf17-121">Sauf si vous convertissez une fonction locale en délégué, la capture s’effectue dans des frames qui sont des types valeur.</span><span class="sxs-lookup"><span data-stu-id="5cf17-121">Unless you convert a local function to a delegate, capturing is done into frames that are value types.</span></span> <span data-ttu-id="5cf17-122">Cela signifie que vous n’avez pas de pression GC sur l’utilisation de fonctions locales avec la capture.</span><span class="sxs-lookup"><span data-stu-id="5cf17-122">That means you don't get any GC pressure from using local functions with capturing.</span></span>

### <a name="reachability"></a><span data-ttu-id="5cf17-123">Accessibilité</span><span class="sxs-lookup"><span data-stu-id="5cf17-123">Reachability</span></span>

<span data-ttu-id="5cf17-124">Nous ajoutons à la spécification</span><span class="sxs-lookup"><span data-stu-id="5cf17-124">We add to the spec</span></span>

> <span data-ttu-id="5cf17-125">Le corps d’une expression lambda ou d’une fonction locale de l’instruction est considéré comme accessible.</span><span class="sxs-lookup"><span data-stu-id="5cf17-125">The body of a statement-bodied lambda expression or local function is considered reachable.</span></span>
