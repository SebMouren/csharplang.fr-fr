---
ms.openlocfilehash: ac2b233eb703b5eea3bd2dfdbeeadd7494b0c695
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484670"
---
# <a name="non-trailing-named-arguments"></a><span data-ttu-id="0489d-101">Arguments nommés non placés en position de fin</span><span class="sxs-lookup"><span data-stu-id="0489d-101">Non-trailing named arguments</span></span>

## <a name="summary"></a><span data-ttu-id="0489d-102">Résumé</span><span class="sxs-lookup"><span data-stu-id="0489d-102">Summary</span></span>
[summary]: #summary
<span data-ttu-id="0489d-103">Autorisez l’utilisation d’arguments nommés en position non finale, à condition qu’ils soient utilisés à leur position correcte.</span><span class="sxs-lookup"><span data-stu-id="0489d-103">Allow named arguments to be used in non-trailing position, as long as they are used in their correct position.</span></span> <span data-ttu-id="0489d-104">Par exemple : `DoSomething(isEmployed:true, name, age);`.</span><span class="sxs-lookup"><span data-stu-id="0489d-104">For example: `DoSomething(isEmployed:true, name, age);`.</span></span>

## <a name="motivation"></a><span data-ttu-id="0489d-105">Motivation</span><span class="sxs-lookup"><span data-stu-id="0489d-105">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="0489d-106">La motivation principale est d’éviter de taper des informations redondantes.</span><span class="sxs-lookup"><span data-stu-id="0489d-106">The main motivation is to avoid typing redundant information.</span></span> <span data-ttu-id="0489d-107">Il est courant de nommer un argument qui est un littéral (par exemple, `null`, `true`) afin de clarifier le code, plutôt que de passer des arguments dans le désordre.</span><span class="sxs-lookup"><span data-stu-id="0489d-107">It is common to name an argument that is a literal (such as `null`, `true`) for the purpose of clarifying the code, rather than of passing arguments out-of-order.</span></span>
<span data-ttu-id="0489d-108">Cela n’est pas autorisé actuellement (`CS1738`), sauf si tous les arguments suivants sont également nommés.</span><span class="sxs-lookup"><span data-stu-id="0489d-108">That is currently disallowed (`CS1738`) unless all the following arguments are also named.</span></span>

```csharp
DoSomething(isEmployed:true, name, age); // currently disallowed, even though all arguments are in position
// CS1738 "Named argument specifications must appear after all fixed arguments have been specified"
```

<span data-ttu-id="0489d-109">Exemples supplémentaires :</span><span class="sxs-lookup"><span data-stu-id="0489d-109">Some additional examples:</span></span>
```csharp
public void DoSomething(bool isEmployed, string personName, int personAge) { ... }

DoSomething(isEmployed:true, name, age); // currently CS1738, but would become legal
DoSomething(true, personName:name, age); // currently CS1738, but would become legal
DoSomething(name, isEmployed:true, age); // remains illegal
DoSomething(name, age, isEmployed:true); // remains illegal
DoSomething(true, personAge:age, personName:name); // already legal
```

<span data-ttu-id="0489d-110">Cela fonctionne également avec les paramètres :</span><span class="sxs-lookup"><span data-stu-id="0489d-110">This would also work with params:</span></span>
```csharp
public class Task
{
    public static Task When(TaskStatus all, TaskStatus any, params Task[] tasks);
}
Task.When(all: TaskStatus.RanToCompletion, any: TaskStatus.Faulted, task1, task2)
```

## <a name="detailed-design"></a><span data-ttu-id="0489d-111">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="0489d-111">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="0489d-112">Dans § 7.5.1 (listes d’arguments), la spécification indique actuellement :</span><span class="sxs-lookup"><span data-stu-id="0489d-112">In §7.5.1 (Argument lists), the spec currently says:</span></span>
> <span data-ttu-id="0489d-113">Un *argument* avec un *argument-Name* est désigné sous le nom d' __argument nommé__, tandis qu’un *argument sans* *argument-Name* est un __argument positionnel__.</span><span class="sxs-lookup"><span data-stu-id="0489d-113">An *argument* with an *argument-name* is referred to as a __named argument__, whereas an *argument* without an *argument-name* is a __positional argument__.</span></span> <span data-ttu-id="0489d-114">L’affichage d’un argument positionnel après un argument nommé dans une *liste d’arguments*constitue une erreur.</span><span class="sxs-lookup"><span data-stu-id="0489d-114">It is an error for a positional argument to appear after a named argument in an *argument-list*.</span></span>

<span data-ttu-id="0489d-115">La proposition consiste à supprimer cette erreur et à mettre à jour les règles de recherche du paramètre correspondant pour un argument (§ 7.5.1.1) :</span><span class="sxs-lookup"><span data-stu-id="0489d-115">The proposal is to remove this error and update the rules for finding the corresponding parameter for an argument (§7.5.1.1):</span></span>

<span data-ttu-id="0489d-116">Arguments dans la liste d’arguments des constructeurs d’instance, des méthodes, des indexeurs et des délégués :</span><span class="sxs-lookup"><span data-stu-id="0489d-116">Arguments in the argument-list of instance constructors, methods, indexers and delegates:</span></span>
- <span data-ttu-id="0489d-117">[règles existantes]</span><span class="sxs-lookup"><span data-stu-id="0489d-117">[existing rules]</span></span>
- <span data-ttu-id="0489d-118">Un argument sans nom correspond à aucun paramètre quand il se trouve après un argument nommé hors position ou un argument nommé params.</span><span class="sxs-lookup"><span data-stu-id="0489d-118">An unnamed argument corresponds to no parameter when it is after an out-of-position named argument or a named params argument.</span></span>

<span data-ttu-id="0489d-119">En particulier, cela empêche l’appel de `void M(bool a = true, bool b = true, bool c = true, );` avec `M(c: false, valueB);`.</span><span class="sxs-lookup"><span data-stu-id="0489d-119">In particular, this prevents invoking `void M(bool a = true, bool b = true, bool c = true, );` with `M(c: false, valueB);`.</span></span> <span data-ttu-id="0489d-120">Le premier argument est utilisé en dehors de la position (l’argument est utilisé à la première position, mais le paramètre nommé « c » est en troisième position), donc les arguments suivants doivent être nommés.</span><span class="sxs-lookup"><span data-stu-id="0489d-120">The first argument is used out-of-position (the argument is used in first position, but the parameter named "c" is in third position), so the following arguments should be named.</span></span>

<span data-ttu-id="0489d-121">En d’autres termes, les arguments nommés non finaux sont uniquement autorisés lorsque le nom et la position entraînent la recherche du même paramètre correspondant.</span><span class="sxs-lookup"><span data-stu-id="0489d-121">In other words, non-trailing named arguments are only allowed when the name and the position result in finding the same corresponding parameter.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="0489d-122">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="0489d-122">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="0489d-123">Cette proposition aggrave les subtilités existantes avec les arguments nommés dans la résolution de surcharge.</span><span class="sxs-lookup"><span data-stu-id="0489d-123">This proposal exacerbates existing subtleties with named arguments in overload resolution.</span></span> <span data-ttu-id="0489d-124">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0489d-124">For instance:</span></span>

```csharp
void M(int x, int y) { }
void M<T>(T y, int x) { }

void M2()
{
    M(3, 4);
    M(y: 3, x: 4); // Invokes M(int, int)
    M(y: 3, 4); // Invokes M<T>(T, int)
}
```

<span data-ttu-id="0489d-125">Vous pouvez vous procurer cette situation dès aujourd’hui en échangeant les paramètres :</span><span class="sxs-lookup"><span data-stu-id="0489d-125">You could get this situation today by swapping the parameters:</span></span>

```csharp
void M(int y, int x) { }
void M<T>(int x, T y) { }

void M2()
{
    M(3, 4);
    M(x: 3, y: 4); // Invokes M(int, int)
    M(3, y: 4); // Invokes M<T>(int, T)
}
```

<span data-ttu-id="0489d-126">De même, si vous avez deux méthodes `void M(int a, int b)` et `void M(int x, string y)`, l’invocation de l’invocation `M(x: 1, 2)` produira un diagnostic basé sur la deuxième surcharge (« impossible de convertir «int » en « String »»).</span><span class="sxs-lookup"><span data-stu-id="0489d-126">Similarly, if you have two methods `void M(int a, int b)` and `void M(int x, string y)`, the mistaken invocation `M(x: 1, 2)` will produce a diagnostic based on the second overload ("cannot convert from 'int' to 'string'").</span></span> <span data-ttu-id="0489d-127">Ce problème existe déjà lorsque l’argument nommé est utilisé dans une position de fin.</span><span class="sxs-lookup"><span data-stu-id="0489d-127">This problem already exists when the named argument is used in a trailing position.</span></span>

## <a name="alternatives"></a><span data-ttu-id="0489d-128">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="0489d-128">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="0489d-129">Il existe deux alternatives à prendre en compte :</span><span class="sxs-lookup"><span data-stu-id="0489d-129">There are a couple of alternatives to consider:</span></span>

- <span data-ttu-id="0489d-130">État actuel</span><span class="sxs-lookup"><span data-stu-id="0489d-130">The status quo</span></span>
- <span data-ttu-id="0489d-131">Fournir une assistance IDE pour remplir tous les noms d’arguments de fin lorsque vous tapez un nom spécifique au milieu.</span><span class="sxs-lookup"><span data-stu-id="0489d-131">Providing IDE assistance to fill-in all the names of trailing arguments when you type specific a name in the middle.</span></span>

<span data-ttu-id="0489d-132">Ces deux éléments souffrent d’un plus grand niveau de détail, car ils introduisent plusieurs arguments nommés, même si vous avez simplement besoin d’un nom de littéral au début de la liste d’arguments.</span><span class="sxs-lookup"><span data-stu-id="0489d-132">Both of those suffer from more verbosity, as they introduce multiple named arguments even if you just need one name of a literal at the beginning of the argument list.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="0489d-133">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="0489d-133">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

## <a name="design-meetings"></a><span data-ttu-id="0489d-134">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="0489d-134">Design meetings</span></span>
[ldm]: #ldm
<span data-ttu-id="0489d-135">La fonctionnalité a été brièvement abordée dans LDM le 16 mai 2017, avec une approbation en principe (OK pour passer à la proposition/prototype).</span><span class="sxs-lookup"><span data-stu-id="0489d-135">The feature was briefly discussed in LDM on May 16th 2017, with approval in principle (ok to move to proposal/prototype).</span></span> <span data-ttu-id="0489d-136">Il a également été brièvement abordé le 28 juin 2017.</span><span class="sxs-lookup"><span data-stu-id="0489d-136">It was also briefly discussed on June 28th 2017.</span></span>

<span data-ttu-id="0489d-137">Est lié à la discussion initiale https://github.com/dotnet/csharplang/issues/518 se réfère au problème https://github.com/dotnet/csharplang/issues/570</span><span class="sxs-lookup"><span data-stu-id="0489d-137">Relates to initial discussion https://github.com/dotnet/csharplang/issues/518 Relates to championed issue https://github.com/dotnet/csharplang/issues/570</span></span>
