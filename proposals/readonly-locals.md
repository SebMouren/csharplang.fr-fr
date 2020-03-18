---
ms.openlocfilehash: 922353d043653ddb651534a01f3fb98f85cd756e
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484481"
---
# <a name="readonly-locals-and-parameters"></a><span data-ttu-id="4490b-101">paramètres et variables locales ReadOnly</span><span class="sxs-lookup"><span data-stu-id="4490b-101">readonly locals and parameters</span></span>

* <span data-ttu-id="4490b-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="4490b-102">[x] Proposed</span></span>
* <span data-ttu-id="4490b-103">[] Prototype</span><span class="sxs-lookup"><span data-stu-id="4490b-103">[ ] Prototype</span></span>
* <span data-ttu-id="4490b-104">[] Implémentation</span><span class="sxs-lookup"><span data-stu-id="4490b-104">[ ] Implementation</span></span>
* <span data-ttu-id="4490b-105">[] Spécification</span><span class="sxs-lookup"><span data-stu-id="4490b-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="4490b-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="4490b-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="4490b-107">Autorisez les variables locales et les paramètres à annoter en lecture seule afin d’éviter une mutation superficielle de ces variables locales et paramètres.</span><span class="sxs-lookup"><span data-stu-id="4490b-107">Allow locals and parameters to be annotated as readonly in order to prevent shallow mutation of those locals and parameters.</span></span>

## <a name="motivation"></a><span data-ttu-id="4490b-108">Motivation</span><span class="sxs-lookup"><span data-stu-id="4490b-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="4490b-109">Aujourd’hui, le mot clé `readonly` peut être appliqué aux champs. Cela a pour effet de s’assurer qu’un champ ne peut être écrit que pendant la construction (construction statique dans le cas d’un champ statique, ou construction d’instance dans le cas d’un champ d’instance), ce qui permet aux développeurs d’éviter des erreurs en remplaçant accidentellement l’État qui ne doit pas être modifié.</span><span class="sxs-lookup"><span data-stu-id="4490b-109">Today, the `readonly` keyword can be applied to fields; this has the effect of ensuring that a field can only be written to during construction (static construction in the case of a static field, or instance construction in the case of an instance field), which helps developers avoid mistakes by accidentally overwriting state which should not be modified.</span></span> <span data-ttu-id="4490b-110">Toutefois, les champs ne sont pas les seuls à pouvoir s’assurer que les valeurs ne sont pas mutées.</span><span class="sxs-lookup"><span data-stu-id="4490b-110">But fields aren't the only places developers want to ensure that values aren't mutated.</span></span> <span data-ttu-id="4490b-111">En particulier, il est courant de créer une variable locale pour stocker l’état temporaire, et la mise à jour accidentelle de cet État peut entraîner des calculs erronés et d’autres bogues, en particulier lorsque ces « variables locales » sont capturées dans des expressions lambda, auquel cas ils sont levés sur des champs, mais il n’existe aucun moyen de marquer ces champs augmentés comme `readonly`.</span><span class="sxs-lookup"><span data-stu-id="4490b-111">In particular, it's common to create a local variable to store temporary state, and accidentally updating that temporary state can result in erroneous calculations and other such bugs, especially when such "locals" are captured in lambdas, at which point they are lifted to fields, but there's no way today to mark such lifted fields as `readonly`.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="4490b-112">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="4490b-112">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="4490b-113">Les variables locales peuvent également être annotées en tant que `readonly`, le compilateur s’assurant qu’elles sont uniquement définies au moment de la déclaration ( C# certains variables locales dans sont déjà implicitement ReadOnly, telles que la variable d’itération dans une boucle « foreach » ou la variable utilisée dans un bloc « using », mais actuellement, un développeur ne peut pas marquer d’autres variables locales comme `readonly`)</span><span class="sxs-lookup"><span data-stu-id="4490b-113">Locals will be annotatable as `readonly` as well, with the compiler ensuring that they're only set at the time of declaration (certain locals in C# are already implicitly readonly, such as the iteration variable in a 'foreach' loop or the used variable in a 'using' block, but currently a developer has no ability to mark other locals as `readonly`).</span></span> <span data-ttu-id="4490b-114">Ces `readonly` variables locales doivent avoir un initialiseur :</span><span class="sxs-lookup"><span data-stu-id="4490b-114">Such `readonly` locals must have an initializer:</span></span>

```csharp
readonly long maxBytesToDelete = (stream.LimitBytes - stream.MaxBytes) / 10;
...
maxBytesToDelete = 0; // Error: can't assign to readonly locals outside of declaration
```

<span data-ttu-id="4490b-115">En guise de raccourci pour `readonly var`, le mot clé contextuel existant `let` peut être utilisé, par exemple</span><span class="sxs-lookup"><span data-stu-id="4490b-115">And as shorthand for `readonly var`, the existing contextual keyword `let` may be used, e.g.</span></span>

```csharp
let maxBytesToDelete = (stream.LimitBytes - stream.MaxBytes) / 10;
...
maxBytesToDelete = 0; // Error: can't assign to readonly locals outside of declaration
```

<span data-ttu-id="4490b-116">Il n’existe aucune contrainte spéciale pour ce que l’initialiseur peut être, et peut être tout ce qui est actuellement valide en tant qu’initialiseur pour les variables locales, par exemple</span><span class="sxs-lookup"><span data-stu-id="4490b-116">There are no special constraints for what the initializer can be, and can be anything currently valid as an initializer for locals, e.g.</span></span>

```csharp
readonly T data = arg1 ?? arg2;
```

<span data-ttu-id="4490b-117">`readonly` sur les variables locales est particulièrement utile lorsque vous utilisez des expressions lambda et des fermetures.</span><span class="sxs-lookup"><span data-stu-id="4490b-117">`readonly` on locals is particularly valuable when working with lambdas and closures.</span></span> <span data-ttu-id="4490b-118">Quand une méthode anonyme ou une expression lambda accède à l’état local à partir de la portée englobante, cet État est capturé dans une fermeture par le compilateur, qui est représenté par une « classe d’affichage ».</span><span class="sxs-lookup"><span data-stu-id="4490b-118">When an anonymous method or lambda accesses local state from the enclosing scope, that state is captured into a closure by the compiler, which is represented by a "display class."</span></span>  <span data-ttu-id="4490b-119">Chaque « local » capturé est un champ de cette classe, mais étant donné que le compilateur génère ce champ en votre nom, vous n’avez pas la possibilité de l’annoter en tant que `readonly` pour empêcher l’expression lambda d’écrire de manière erronée dans le « local » (guillemets, car il ne s’agit pas vraiment d’un local, au moins dans le MSIL résultant).</span><span class="sxs-lookup"><span data-stu-id="4490b-119">Each "local" that's captured is a field in this class, yet because the compiler is generating this field on your behalf, you have no opportunity to annotate it as `readonly` in order to prevent the lambda from erroneously writing to the "local" (in quotes because it's really not a local, at least not in the resulting MSIL).</span></span> <span data-ttu-id="4490b-120">Avec `readonly` variables locales, le compilateur peut empêcher le lambda d’écrire dans un emplacement local, ce qui est particulièrement utile dans les scénarios impliquant le multithread, où une écriture erronée peut entraîner un bogue de concurrence risqué, mais rare et difficile à trouver.</span><span class="sxs-lookup"><span data-stu-id="4490b-120">With `readonly` locals, the compiler can prevent the lambda from writing to local, which is particularly valuable in scenarios involving multithreading where an erroneous write could result in a dangerous but rare and hard-to-find concurrency bug.</span></span>

```csharp
readonly long index = ...;
Parallel.ForEach(data, item => {
    T element = item[index];
    index = 0; // Error: can't assign to readonly locals outside of declaration
});
```

<span data-ttu-id="4490b-121">Comme une forme spéciale de locale, les paramètres sont également annotés comme `readonly`.</span><span class="sxs-lookup"><span data-stu-id="4490b-121">As a special form of local, parameters will also be annotatable as `readonly`.</span></span> <span data-ttu-id="4490b-122">Cela n’a aucun effet sur ce que l’appelant de la méthode est en mesure de passer au paramètre (tout comme il n’existe aucune contrainte sur les valeurs qui peuvent être stockées dans un champ `readonly`), mais comme avec n’importe quel `readonly` local, le compilateur empêche l’écriture du code dans le paramètre après la déclaration, ce qui signifie que le corps de la méthode est interdit d’écrire dans le paramètre</span><span class="sxs-lookup"><span data-stu-id="4490b-122">This would have no effect on what the caller of the method is able to pass to the parameter (just as there's no constraint on what values may be stored into a `readonly` field), but as with any `readonly` local, the compiler would prohibit code from writing to the parameter after declaration, which means the body of the method is prohibited from writing to the parameter.</span></span>

```csharp
public void Update(readonly int index = 0) // Default values are ok though not required
{
    ...
    index = 0; // Error: can't assign to readonly parameters
    ...
}
```

<span data-ttu-id="4490b-123">les paramètres de `readonly` n’affectent pas la signature/les métadonnées émises par le compilateur pour cette méthode et affectent simplement la façon dont le compilateur gère la compilation du corps de la méthode.</span><span class="sxs-lookup"><span data-stu-id="4490b-123">`readonly` parameters do not affect the signature/metadata emitted by the compiler for that method, and simply affect how the compiler handles the compilation of the method's body.</span></span> <span data-ttu-id="4490b-124">Ainsi, par exemple, une méthode virtuelle de base peut avoir un paramètre `readonly`, et ce paramètre peut être accessible en écriture dans une substitution.</span><span class="sxs-lookup"><span data-stu-id="4490b-124">Thus, for example, a base virtual method could have a `readonly` parameter, and that parameter could be writable in an override.</span></span>

<span data-ttu-id="4490b-125">Comme pour les champs, les `readonly` pour les variables locales et les paramètres sont superficielles, ce qui affecte l’emplacement de stockage, mais n’affecte pas de manière transitive le graphique d’objets.</span><span class="sxs-lookup"><span data-stu-id="4490b-125">As with fields, `readonly` for locals and parameters is shallow, affecting the storage location but not transitively affecting the object graph.</span></span> <span data-ttu-id="4490b-126">Toutefois, comme avec les champs, l’appel d’une méthode sur une `readonly` structure locale/de paramètre effectue en fait une copie de la structure et appelle la méthode sur la copie, afin d’éviter la mutation interne des `this`.</span><span class="sxs-lookup"><span data-stu-id="4490b-126">However, also as with fields, calling a method on a `readonly` local/parameter struct will actually make a copy of the struct and call the method on the copy, in order to avoid internal mutation of `this`.</span></span>

<span data-ttu-id="4490b-127">`readonly` paramètres et variables locales ne peuvent pas être passés en tant qu’arguments `ref` ou `out`, sauf si/jusqu’à ce que `ref readonly` soit également pris en charge.</span><span class="sxs-lookup"><span data-stu-id="4490b-127">`readonly` locals and parameters can't be passed as `ref` or `out` arguments, unless/until `ref readonly` is also supported.</span></span>

## <a name="alternatives"></a><span data-ttu-id="4490b-128">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="4490b-128">Alternatives</span></span>
[alternatives]: #alternatives

- <span data-ttu-id="4490b-129">`val` peut être utilisé comme autre raccourci pour `let`.</span><span class="sxs-lookup"><span data-stu-id="4490b-129">`val` could be used as an alternative shorthand to `let`.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="4490b-130">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="4490b-130">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="4490b-131">`readonly ref` / `ref readonly` / `readonly ref readonly`: J’ai laissé la question de savoir comment gérer les `ref readonly` séparément de cette offre.</span><span class="sxs-lookup"><span data-stu-id="4490b-131">`readonly ref` / `ref readonly` / `readonly ref readonly`: I've left the question of how to handle `ref readonly` as separate from this proposal.</span></span>
- <span data-ttu-id="4490b-132">Cette proposition ne traite pas les structs/types immuables en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="4490b-132">This proposal does not tackle readonly structs / immutable types.</span></span> <span data-ttu-id="4490b-133">Cela reste pour une proposition distincte.</span><span class="sxs-lookup"><span data-stu-id="4490b-133">That is left for a separate proposal.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="4490b-134">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="4490b-134">Design meetings</span></span>

- <span data-ttu-id="4490b-135">Brièvement abordé le 21 janvier 2015 (<https://github.com/dotnet/roslyn/issues/98>)</span><span class="sxs-lookup"><span data-stu-id="4490b-135">Briefly discussed on Jan 21, 2015 (<https://github.com/dotnet/roslyn/issues/98>)</span></span>
