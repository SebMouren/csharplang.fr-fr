---
ms.openlocfilehash: 54f9a372ca0329a284f06876f544e0b4936af02a
ms.sourcegitcommit: 356ee04506a2a82292be25d7b029e7ce2a39e63a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82037400"
---
# <a name="nullable-reference-types-in-c"></a><span data-ttu-id="b54d0-101">Types de référence nuls en C #</span><span class="sxs-lookup"><span data-stu-id="b54d0-101">Nullable reference types in C#</span></span> #

<span data-ttu-id="b54d0-102">Le but de cette fonctionnalité est de :</span><span class="sxs-lookup"><span data-stu-id="b54d0-102">The goal of this feature is to:</span></span>

* <span data-ttu-id="b54d0-103">Permettre aux développeurs d’exprimer si une variable, un paramètre ou un résultat d’un type de référence est destiné à être nul ou non.</span><span class="sxs-lookup"><span data-stu-id="b54d0-103">Allow developers to express whether a variable, parameter or result of a reference type is intended to be null or not.</span></span>
* <span data-ttu-id="b54d0-104">Fournir des avertissements lorsque de telles variables, paramètres et résultats ne sont pas utilisés en fonction de cette intention.</span><span class="sxs-lookup"><span data-stu-id="b54d0-104">Provide warnings when such variables, parameters and results are not used according to that intent.</span></span>

## <a name="expression-of-intent"></a><span data-ttu-id="b54d0-105">Expression d’intention</span><span class="sxs-lookup"><span data-stu-id="b54d0-105">Expression of intent</span></span>

<span data-ttu-id="b54d0-106">La langue contient `T?` déjà la syntaxe pour les types de valeur.</span><span class="sxs-lookup"><span data-stu-id="b54d0-106">The language already contains the `T?` syntax for value types.</span></span> <span data-ttu-id="b54d0-107">Il est simple d’étendre cette syntaxe aux types de référence.</span><span class="sxs-lookup"><span data-stu-id="b54d0-107">It is straightforward to extend this syntax to reference types.</span></span>

<span data-ttu-id="b54d0-108">On suppose que l’intention d’un `T` type de référence sans fioritures est qu’il soit non nul.</span><span class="sxs-lookup"><span data-stu-id="b54d0-108">It is assumed that the intent of an unadorned reference type `T` is for it to be non-null.</span></span>

## <a name="checking-of-nullable-references"></a><span data-ttu-id="b54d0-109">Vérification des références annulées</span><span class="sxs-lookup"><span data-stu-id="b54d0-109">Checking of nullable references</span></span>

<span data-ttu-id="b54d0-110">Une analyse de flux suit les variables de référence annulables.</span><span class="sxs-lookup"><span data-stu-id="b54d0-110">A flow analysis tracks nullable reference variables.</span></span> <span data-ttu-id="b54d0-111">Lorsque l’analyse estime qu’ils ne seraient pas nuls (p. ex., après une vérification ou une cession), leur valeur sera considérée comme une référence non nulle.</span><span class="sxs-lookup"><span data-stu-id="b54d0-111">Where the analysis deems that they would not be null (e.g. after a check or an assignment), their value will be considered a non-null reference.</span></span>

<span data-ttu-id="b54d0-112">Une référence annulée peut également être traitée explicitement comme `x!` non nulle avec l’opérateur de poteaux (l’opérateur "damnit"), car lorsque l’analyse du débit ne peut pas établir une situation non nulle que le développeur sait être là.</span><span class="sxs-lookup"><span data-stu-id="b54d0-112">A nullable reference can also explicitly be treated as non-null with the postfix `x!` operator (the "damnit" operator), for when flow analysis cannot establish a non-null situation that the developer knows is there.</span></span>

<span data-ttu-id="b54d0-113">Dans le cas contraire, un avertissement est donné si une référence annulée est déreférée ou est convertie en un type non nul.</span><span class="sxs-lookup"><span data-stu-id="b54d0-113">Otherwise, a warning is given if a nullable reference is dereferenced, or is converted to a non-null type.</span></span>

<span data-ttu-id="b54d0-114">Un avertissement est donné `S[]` lors `T?[]` de `S?[]` `T[]`la conversion de et de .</span><span class="sxs-lookup"><span data-stu-id="b54d0-114">A warning is given when converting from `S[]` to `T?[]` and from `S?[]` to `T[]`.</span></span>

<span data-ttu-id="b54d0-115">Un avertissement est donné `C<S>` lors `C<T?>` de la conversion à partir`out`de sauf lorsque `C<S?>` le `C<T>` paramètre de type est covariant ( ), et lors de la conversion à partir de sauf lorsque le paramètre de type est contravariant (`in`).</span><span class="sxs-lookup"><span data-stu-id="b54d0-115">A warning is given when converting from `C<S>` to `C<T?>` except when the type parameter is covariant (`out`), and when converting from `C<S?>` to `C<T>` except when the type parameter is contravariant (`in`).</span></span>

<span data-ttu-id="b54d0-116">Un avertissement est `C<T?>` donné sur si le paramètre de type a des contraintes non nulles.</span><span class="sxs-lookup"><span data-stu-id="b54d0-116">A warning is given on `C<T?>` if the type parameter has non-null constraints.</span></span>

## <a name="checking-of-non-null-references"></a><span data-ttu-id="b54d0-117">Vérification des références non nulles</span><span class="sxs-lookup"><span data-stu-id="b54d0-117">Checking of non-null references</span></span>

<span data-ttu-id="b54d0-118">Un avertissement est donné si un littéral nul est attribué à une variable non nulle ou adopté comme paramètre non nul.</span><span class="sxs-lookup"><span data-stu-id="b54d0-118">A warning is given if a null literal is assigned to a non-null variable or passed as a non-null parameter.</span></span>

<span data-ttu-id="b54d0-119">Un avertissement est également donné si un constructeur n’initialise pas explicitement les champs de référence non nuls.</span><span class="sxs-lookup"><span data-stu-id="b54d0-119">A warning is also given if a constructor does not explicitly initialize non-null reference fields.</span></span>

<span data-ttu-id="b54d0-120">Nous ne pouvons pas suivre adéquatement que tous les éléments d’un éventail de références non nulles sont initialisés.</span><span class="sxs-lookup"><span data-stu-id="b54d0-120">We cannot adequately track that all elements of an array of non-null references are initialized.</span></span> <span data-ttu-id="b54d0-121">Cependant, nous pourrions émettre un avertissement si aucun élément d’un tableau nouvellement créé n’est attribué avant que le tableau ne soit lu ou transmis.</span><span class="sxs-lookup"><span data-stu-id="b54d0-121">However, we could issue a warning if no element of a newly created array is assigned to before the array is read from or passed on.</span></span> <span data-ttu-id="b54d0-122">Cela pourrait gérer le cas commun sans être trop bruyant.</span><span class="sxs-lookup"><span data-stu-id="b54d0-122">That might handle the common case without being too noisy.</span></span>

<span data-ttu-id="b54d0-123">Nous devons décider `default(T)` si génère un avertissement, ou est simplement `T?`traité comme étant du type .</span><span class="sxs-lookup"><span data-stu-id="b54d0-123">We need to decide whether `default(T)` generates a warning, or is simply treated as being of the type `T?`.</span></span>

## <a name="metadata-representation"></a><span data-ttu-id="b54d0-124">Représentation des métadonnées</span><span class="sxs-lookup"><span data-stu-id="b54d0-124">Metadata representation</span></span>

<span data-ttu-id="b54d0-125">Les ornements d’annulation doivent être représentés dans les métadonnées comme attributs.</span><span class="sxs-lookup"><span data-stu-id="b54d0-125">Nullability adornments should be represented in metadata as attributes.</span></span> <span data-ttu-id="b54d0-126">Cela signifie que les compilateurs à bas de niveau les ignoreront.</span><span class="sxs-lookup"><span data-stu-id="b54d0-126">This means that downlevel compilers will ignore them.</span></span>

<span data-ttu-id="b54d0-127">Nous devons décider si seules des annotations annulables sont incluses, ou il y a aussi une indication de si non-null était "sur" à l’Assemblée.</span><span class="sxs-lookup"><span data-stu-id="b54d0-127">We need to decide if only nullable annotations are included, or there's also some indication of whether non-null was "on" in the assembly.</span></span>

## <a name="generics"></a><span data-ttu-id="b54d0-128">Génériques</span><span class="sxs-lookup"><span data-stu-id="b54d0-128">Generics</span></span>

<span data-ttu-id="b54d0-129">Si un `T` paramètre de type a des contraintes non annulables, il est considéré comme non annulable dans son champ d’application.</span><span class="sxs-lookup"><span data-stu-id="b54d0-129">If a type parameter `T` has non-nullable constraints, it is treated as non-nullable within its scope.</span></span>

<span data-ttu-id="b54d0-130">Si un paramètre de type n’est pas tendu ou n’a que des contraintes annulables, la situation est un peu plus complexe : cela signifie que *l’argument* de type correspondant peut être soit annulable, soit non annulable.</span><span class="sxs-lookup"><span data-stu-id="b54d0-130">If a type parameter is unconstrained or has only nullable constraints, the situation is a little more complex: this means that the corresponding type argument could be *either* nullable or non-nullable.</span></span> <span data-ttu-id="b54d0-131">La chose sûre à faire dans cette situation est de traiter le paramètre de type comme à la *fois* annulable et non-nullable, donnant des avertissements lorsque l’un ou l’autre est violé.</span><span class="sxs-lookup"><span data-stu-id="b54d0-131">The safe thing to do in that situation is to treat the type parameter as *both* nullable and non-nullable, giving warnings when either is violated.</span></span> 

<span data-ttu-id="b54d0-132">Il convient d’examiner si des contraintes de référence nulles explicites devraient être autorisées.</span><span class="sxs-lookup"><span data-stu-id="b54d0-132">It is worth considering whether explicit nullable reference constraints should be allowed.</span></span> <span data-ttu-id="b54d0-133">Notez toutefois que nous ne pouvons éviter que les types de référence nuls soient *implicitement* des contraintes dans certains cas (contraintes héritées).</span><span class="sxs-lookup"><span data-stu-id="b54d0-133">Note, however, that we cannot avoid having nullable reference types *implicitly* be constraints in certain cases (inherited constraints).</span></span>

<span data-ttu-id="b54d0-134">La `class` contrainte n’est pas nulle.</span><span class="sxs-lookup"><span data-stu-id="b54d0-134">The `class` constraint is non-null.</span></span> <span data-ttu-id="b54d0-135">Nous pouvons `class?` examiner s’il devrait s’agir d’une contrainte nulle valide indiquant le « type de référence nul ».</span><span class="sxs-lookup"><span data-stu-id="b54d0-135">We can consider whether `class?` should be a valid nullable constraint denoting "nullable reference type".</span></span>

## <a name="type-inference"></a><span data-ttu-id="b54d0-136">Inférence type</span><span class="sxs-lookup"><span data-stu-id="b54d0-136">Type inference</span></span>

<span data-ttu-id="b54d0-137">Dans l’inférence de type, si un type contributif est un type de référence nul, le type résultant doit être annulé.</span><span class="sxs-lookup"><span data-stu-id="b54d0-137">In type inference, if a contributing type is a nullable reference type, the resulting type should be nullable.</span></span> <span data-ttu-id="b54d0-138">En d’autres termes, la nullité est propagée.</span><span class="sxs-lookup"><span data-stu-id="b54d0-138">In other words, nullness is propagated.</span></span>

<span data-ttu-id="b54d0-139">Nous devrions nous `null` demander si le littéral en tant qu’expression participante devrait contribuer à l’nullité.</span><span class="sxs-lookup"><span data-stu-id="b54d0-139">We should consider whether the `null` literal as a participating expression should contribute nullness.</span></span> <span data-ttu-id="b54d0-140">Il n’est pas aujourd’hui: pour les types de valeur, il conduit à une erreur, tandis que pour les types de référence les null convertis avec succès au type simple.</span><span class="sxs-lookup"><span data-stu-id="b54d0-140">It doesn't today: for value types it leads to an error, whereas for reference types the null successfully converts to the plain type.</span></span>

```csharp
string? n = "world";
var x = b ? "Hello" : n; // string?
var y = b ? "Hello" : null; // string? or error
var z = b ? 7 : null; // Error today, could be int?
```

## <a name="null-guard-guidance"></a><span data-ttu-id="b54d0-141">Guidage de garde nul</span><span class="sxs-lookup"><span data-stu-id="b54d0-141">Null guard guidance</span></span>

<span data-ttu-id="b54d0-142">En tant que caractéristique, les types de référence inquérables permettent aux développeurs d’exprimer leur intention et de fournir des avertissements par l’analyse du débit si cette intention est contredite.</span><span class="sxs-lookup"><span data-stu-id="b54d0-142">As a feature, nullable reference types allow developers to express their intent, and provide warnings through flow analysis if that intent is contradicted.</span></span> <span data-ttu-id="b54d0-143">Il y a une question commune quant à savoir si des gardes nuls sont nécessaires ou non.</span><span class="sxs-lookup"><span data-stu-id="b54d0-143">There is a common question as to whether or not null guards are necessary.</span></span>

### <a name="example-of-null-guard"></a><span data-ttu-id="b54d0-144">Exemple de garde nulle</span><span class="sxs-lookup"><span data-stu-id="b54d0-144">Example of null guard</span></span>

```csharp
public void DoWork(Worker worker)
{
    // Guard against worker being null
    if (worker is null)
    {
        throw new ArgumentNullException(nameof(worker));
    }

    // Otherwise use worker argument
}
```

<span data-ttu-id="b54d0-145">Dans l’exemple `DoWork` précédent, la `Worker` fonction accepte un `null`et se garde contre elle potentiellement être .</span><span class="sxs-lookup"><span data-stu-id="b54d0-145">In the previous example, the `DoWork` function accepts a `Worker` and guards against it potentially being `null`.</span></span> <span data-ttu-id="b54d0-146">Si `worker` l’argument est `null`, la `DoWork` fonction sera `throw`.</span><span class="sxs-lookup"><span data-stu-id="b54d0-146">If the `worker` argument is `null`, the `DoWork` function will `throw`.</span></span> <span data-ttu-id="b54d0-147">Avec les types de référence nuls, le code `Worker` dans l’exemple précédent fait l’intention que le paramètre *ne* serait `null`pas .</span><span class="sxs-lookup"><span data-stu-id="b54d0-147">With nullable reference types, the code in the previous example makes the intent that the `Worker` parameter would *not* be `null`.</span></span> <span data-ttu-id="b54d0-148">Si `DoWork` la fonction était une API publique, comme un paquet NuGet ou une bibliothèque partagée - comme des conseils, vous devriez laisser les gardes nuls en place.</span><span class="sxs-lookup"><span data-stu-id="b54d0-148">If the `DoWork` function was a public API, such as a NuGet package or a shared library - as guidance you should leave null guards in place.</span></span> <span data-ttu-id="b54d0-149">En tant qu’API public, la seule garantie `null` qu’un appelant ne passe pas est de se prémunir contre elle.</span><span class="sxs-lookup"><span data-stu-id="b54d0-149">As a public API, the only guarantee that a caller isn't passing `null` is to guard against it.</span></span>

### <a name="express-intent"></a><span data-ttu-id="b54d0-150">Intention expresse</span><span class="sxs-lookup"><span data-stu-id="b54d0-150">Express intent</span></span>

<span data-ttu-id="b54d0-151">Une utilisation plus convaincante de l’exemple `Worker` précédent `null`est d’exprimer que le paramètre pourrait être, ce qui rend la garde nulle plus appropriée.</span><span class="sxs-lookup"><span data-stu-id="b54d0-151">A more compelling use of the previous example is to express that the `Worker` parameter could be `null`, thus making the null guard more appropriate.</span></span> <span data-ttu-id="b54d0-152">Si vous supprimez la garde nulle dans l’exemple suivant, le compilateur avertit que vous pouvez être différenciation nulle.</span><span class="sxs-lookup"><span data-stu-id="b54d0-152">If you remove the null guard in the following example, the compiler warns that you may be dereferencing null.</span></span> <span data-ttu-id="b54d0-153">Quoi qu’il en soit, les deux gardes nuls sont toujours valides.</span><span class="sxs-lookup"><span data-stu-id="b54d0-153">Regardless, both null guards are still valid.</span></span>

```csharp
public void DoWork(Worker? worker)
{
    // Guard against worker being null
    if (worker is null)
    {
        throw new ArgumentNullException(nameof(worker));
    }

    // Otherwise use worker argument
}
```

<span data-ttu-id="b54d0-154">Pour les API non publiques, telles que le code source entièrement sous contrôle par un développeur ou une équipe de développement - les types de référence nuls pourraient permettre la suppression en toute sécurité des gardes nuls lorsque les développeurs peuvent garantir qu’il n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b54d0-154">For non-public APIs, such as source code entirely in control by a developer or dev team - the nullable reference types could allow for the safe removal of null guards where the developers can guarantee it is not necessary.</span></span> <span data-ttu-id="b54d0-155">La fonctionnalité peut aider avec des avertissements, mais il ne `NullReferenceException`peut pas garantir qu’à l’exécution du code runtime pourrait entraîner un .</span><span class="sxs-lookup"><span data-stu-id="b54d0-155">The feature can help with warnings, but it cannot guarantee that at runtime code execution could result in a `NullReferenceException`.</span></span>

## <a name="breaking-changes"></a><span data-ttu-id="b54d0-156">Changements cassants</span><span class="sxs-lookup"><span data-stu-id="b54d0-156">Breaking changes</span></span>

<span data-ttu-id="b54d0-157">Les avertissements non nuls sont une modification évidente de rupture sur le code existant, et doivent être accompagnés d’un mécanisme d’opt-in.</span><span class="sxs-lookup"><span data-stu-id="b54d0-157">Non-null warnings are an obvious breaking change on existing code, and should be accompanied with an opt-in mechanism.</span></span>

<span data-ttu-id="b54d0-158">De toute évidence, les avertissements de types nuls (comme décrit ci-dessus) sont une modification de rupture sur le code existant dans certains scénarios où l’annulation est implicite :</span><span class="sxs-lookup"><span data-stu-id="b54d0-158">Less obviously, warnings from nullable types (as described above) are a breaking change on existing code in certain scenarios where the nullability is implicit:</span></span>

* <span data-ttu-id="b54d0-159">Les paramètres de type non tendus seront traités comme implicitement `object` in annulables, `ToString` de sorte que les attribuer ou y accéder, par exemple, donnera des avertissements.</span><span class="sxs-lookup"><span data-stu-id="b54d0-159">Unconstrained type parameters will be treated as implicitly nullable, so assigning them to `object` or accessing e.g. `ToString` will yield warnings.</span></span>
* <span data-ttu-id="b54d0-160">si l’inférence de type `null` déduit l’annulation des expressions, alors le code existant produira parfois des types nuls plutôt que non annulables, ce qui peut conduire à de nouveaux avertissements.</span><span class="sxs-lookup"><span data-stu-id="b54d0-160">if type inference infers nullness from `null` expressions, then existing code will sometimes yield nullable rather than non-nullable types, which can lead to new warnings.</span></span>

<span data-ttu-id="b54d0-161">Les avertissements nuls doivent donc également être facultatifs</span><span class="sxs-lookup"><span data-stu-id="b54d0-161">So nullable warnings also need to be optional</span></span>

<span data-ttu-id="b54d0-162">Enfin, l’ajout d’annotations à une API existante sera un changement de rupture pour les utilisateurs qui ont opté pour les avertissements, quand ils mettent à niveau la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="b54d0-162">Finally, adding annotations to an existing API will be a breaking change to users who have opted in to warnings, when they upgrade the library.</span></span> <span data-ttu-id="b54d0-163">Cela, aussi, mérite la possibilité d’opter pour ou hors. "Je veux que les corrections des insectes, mais je ne suis pas prêt à faire face à leurs nouvelles annotations"</span><span class="sxs-lookup"><span data-stu-id="b54d0-163">This, too, merits the ability to opt in or out. "I want the bug fixes, but I am not ready to deal with their new annotations"</span></span>

<span data-ttu-id="b54d0-164">En résumé, vous devez être en mesure d’opter pour/hors de:</span><span class="sxs-lookup"><span data-stu-id="b54d0-164">In summary, you need to be able to opt in/out of:</span></span>
* <span data-ttu-id="b54d0-165">Avertissements nuls</span><span class="sxs-lookup"><span data-stu-id="b54d0-165">Nullable warnings</span></span>
* <span data-ttu-id="b54d0-166">Avertissements non nuls</span><span class="sxs-lookup"><span data-stu-id="b54d0-166">Non-null warnings</span></span>
* <span data-ttu-id="b54d0-167">Avertissements d’annotations dans d’autres fichiers</span><span class="sxs-lookup"><span data-stu-id="b54d0-167">Warnings from annotations in other files</span></span>

<span data-ttu-id="b54d0-168">La granularité de l’opt-in suggère un modèle de type analyseur, où des bandes de code peuvent opter avec des pragmas et des niveaux de gravité peuvent être choisis par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b54d0-168">The granularity of the opt-in suggests an analyzer-like model, where swaths of code can opt in and out with pragmas and severity levels can be chosen by the user.</span></span> <span data-ttu-id="b54d0-169">En outre, les options par bibliothèque ("ignorer les annotations de JSON.NET jusqu’à ce que je suis prêt à faire face à la chute") peut être expressible dans le code comme attributs.</span><span class="sxs-lookup"><span data-stu-id="b54d0-169">Additionally, per-library options ("ignore the annotations from JSON.NET until I'm ready to deal with the fall out") may be expressible in code as attributes.</span></span>

<span data-ttu-id="b54d0-170">La conception de l’expérience opt-in/transition est cruciale pour le succès et l’utilité de cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="b54d0-170">The design of the opt-in/transition experience is crucial to the success and usefulness of this feature.</span></span> <span data-ttu-id="b54d0-171">Nous devons nous assurer que :</span><span class="sxs-lookup"><span data-stu-id="b54d0-171">We need to make sure that:</span></span>

* <span data-ttu-id="b54d0-172">Les utilisateurs peuvent adopter la vérification de la nullabilité progressivement comme ils le souhaitent</span><span class="sxs-lookup"><span data-stu-id="b54d0-172">Users can adopt nullability checking gradually as they want to</span></span>
* <span data-ttu-id="b54d0-173">Les auteurs de la bibliothèque peuvent ajouter des annotations de nullabilité sans crainte de briser les clients</span><span class="sxs-lookup"><span data-stu-id="b54d0-173">Library authors can add nullability annotations without fear of breaking customers</span></span>
* <span data-ttu-id="b54d0-174">Malgré cela, il n’y a pas un sentiment de «cauchemar de configuration»</span><span class="sxs-lookup"><span data-stu-id="b54d0-174">Despite these, there is not a sense of "configuration nightmare"</span></span>

## <a name="tweaks"></a><span data-ttu-id="b54d0-175">Réglages</span><span class="sxs-lookup"><span data-stu-id="b54d0-175">Tweaks</span></span>

<span data-ttu-id="b54d0-176">Nous pourrions envisager `?` de ne pas utiliser les annotations sur les habitants, mais simplement d’observer si elles sont utilisées conformément à ce qui leur est assigné.</span><span class="sxs-lookup"><span data-stu-id="b54d0-176">We could consider not using the `?` annotations on locals, but just observing whether they are used in accordance with what gets assigned to them.</span></span> <span data-ttu-id="b54d0-177">Je ne suis pas en faveur de cela; Je pense que nous devrions uniformément laisser les gens exprimer leur intention.</span><span class="sxs-lookup"><span data-stu-id="b54d0-177">I don't favor this; I think we should uniformly let people express their intent.</span></span>

<span data-ttu-id="b54d0-178">Nous pourrions envisager `T! x` un raccourci sur les paramètres, qui génère automatiquement une vérification nulle durée de course.</span><span class="sxs-lookup"><span data-stu-id="b54d0-178">We could consider a shorthand `T! x` on parameters, that auto-generates a runtime null check.</span></span>

<span data-ttu-id="b54d0-179">Certains modèles sur les `FirstOrDefault` types `TryGet`génériques, tels que ou , ont un comportement légèrement bizarre avec des arguments de type non-nullable, parce qu’ils donnent explicitement des valeurs par défaut dans certaines situations.</span><span class="sxs-lookup"><span data-stu-id="b54d0-179">Certain patterns on generic types, such as `FirstOrDefault` or `TryGet`, have slightly weird behavior with non-nullable type arguments, because they explicitly yield default values in certain situations.</span></span> <span data-ttu-id="b54d0-180">Nous pourrions essayer de nuancer le système de type pour mieux les accueillir.</span><span class="sxs-lookup"><span data-stu-id="b54d0-180">We could try to nuance the type system to accommodate these better.</span></span> <span data-ttu-id="b54d0-181">Par exemple, nous `?` pourrions autoriser sur les paramètres de type non tendu, même si l’argument de type pourrait déjà être annulé.</span><span class="sxs-lookup"><span data-stu-id="b54d0-181">For instance, we could allow `?` on unconstrained type parameters, even though the type argument could already be nullable.</span></span> <span data-ttu-id="b54d0-182">Je doute que cela en vaut la peine, et il conduit à l’étrangeté liée à l’interaction avec les types de *valeur* nulle.</span><span class="sxs-lookup"><span data-stu-id="b54d0-182">I doubt that it is worth it, and it leads to weirdness related to interaction with nullable *value* types.</span></span> 

## <a name="nullable-value-types"></a><span data-ttu-id="b54d0-183">Types valeur Nullable</span><span class="sxs-lookup"><span data-stu-id="b54d0-183">Nullable value types</span></span>

<span data-ttu-id="b54d0-184">Nous pourrions envisager d’adopter certaines des sémantiques ci-dessus pour les types de valeur nulle ainsi.</span><span class="sxs-lookup"><span data-stu-id="b54d0-184">We could consider adopting some of the above semantics for nullable value types as well.</span></span>

<span data-ttu-id="b54d0-185">Nous avons déjà mentionné l’inférence `int?` `(7, null)`de type, où nous pourrions déduire de , au lieu de simplement donner une erreur.</span><span class="sxs-lookup"><span data-stu-id="b54d0-185">We already mentioned type inference, where we could infer `int?` from `(7, null)`, instead of just giving an error.</span></span>

<span data-ttu-id="b54d0-186">Une autre occasion est d’appliquer l’analyse du débit aux types de valeur nuls.</span><span class="sxs-lookup"><span data-stu-id="b54d0-186">Another opportunity is to apply the flow analysis to nullable value types.</span></span> <span data-ttu-id="b54d0-187">Lorsqu’ils sont jugés non nuls, nous pourrions en fait permettre d’utiliser comme type non annulable de certaines façons (p. ex. l’accès des membres).</span><span class="sxs-lookup"><span data-stu-id="b54d0-187">When they are deemed non-null, we could actually allow using as the non-nullable type in certain ways (e.g. member access).</span></span> <span data-ttu-id="b54d0-188">Nous devons juste faire attention que les choses que vous pouvez *déjà* faire sur un type de valeur nulle sera préférée, pour des raisons de compat arrière.</span><span class="sxs-lookup"><span data-stu-id="b54d0-188">We just have to be careful that the things that you can *already* do on a nullable value type will be preferred, for back compat reasons.</span></span>
