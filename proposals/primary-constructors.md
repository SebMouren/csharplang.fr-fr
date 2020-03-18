---
ms.openlocfilehash: 77c9ffda3a59cc5f3dcc3ec9848600c6c5e03eed
ms.sourcegitcommit: 27487fa0294f4cdb7e5f2478884149e05314fd8a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "79484971"
---
# <a name="primary-constructors"></a><span data-ttu-id="66e1e-101">Constructeurs principaux</span><span class="sxs-lookup"><span data-stu-id="66e1e-101">Primary constructors</span></span>

* <span data-ttu-id="66e1e-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="66e1e-102">[x] Proposed</span></span>
* <span data-ttu-id="66e1e-103">[] Prototype : non démarré</span><span class="sxs-lookup"><span data-stu-id="66e1e-103">[ ] Prototype: Not started</span></span>
* <span data-ttu-id="66e1e-104">[] Implémentation : non démarrée</span><span class="sxs-lookup"><span data-stu-id="66e1e-104">[ ] Implementation: Not started</span></span>
* <span data-ttu-id="66e1e-105">[] Spécification : non démarrée</span><span class="sxs-lookup"><span data-stu-id="66e1e-105">[ ] Specification: Not started</span></span>

## <a name="summary"></a><span data-ttu-id="66e1e-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="66e1e-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="66e1e-107">Les classes peuvent avoir une liste de paramètres et, le cas échéant, la spécification de la classe de base peut avoir une liste d’arguments.</span><span class="sxs-lookup"><span data-stu-id="66e1e-107">Classes can have a parameter list, and when they do, their base class specification can have an argument list.</span></span>
<span data-ttu-id="66e1e-108">Les paramètres de constructeur principal sont dans la portée tout au long de la déclaration de classe, et s’ils sont capturés par un membre de fonction ou une fonction anonyme, ils sont stockés en tant que champs privés dans la classe.</span><span class="sxs-lookup"><span data-stu-id="66e1e-108">Primary constructor parameters are in scope throughout the class declaration, and if they are captured by a function member or anonymous function, they are stored as private fields in the class.</span></span>

## <a name="motivation"></a><span data-ttu-id="66e1e-109">Motivation</span><span class="sxs-lookup"><span data-stu-id="66e1e-109">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="66e1e-110">Il est courant d’avoir beaucoup de réutilisables dans le code d’initialisation du programme.</span><span class="sxs-lookup"><span data-stu-id="66e1e-110">It is common to have a lot of boilerplate in program initialization code.</span></span> <span data-ttu-id="66e1e-111">Dans le cas général, un élément de données donné `x` est mentionné plusieurs fois :</span><span class="sxs-lookup"><span data-stu-id="66e1e-111">In the general case, a given piece of data `x` is mentioned many times:</span></span>

- <span data-ttu-id="66e1e-112">En tant que champ privé `_x`</span><span class="sxs-lookup"><span data-stu-id="66e1e-112">As a private field `_x`</span></span>
- <span data-ttu-id="66e1e-113">En tant que paramètre `x` à un constructeur</span><span class="sxs-lookup"><span data-stu-id="66e1e-113">As a parameter `x` to a constructor</span></span>
- <span data-ttu-id="66e1e-114">Dans une `_x = x;` d’assignation du champ à partir du paramètre dans le constructeur</span><span class="sxs-lookup"><span data-stu-id="66e1e-114">In an assignment `_x = x;` of the field from the parameter in the constructor</span></span>
- <span data-ttu-id="66e1e-115">En tant que propriété `X`</span><span class="sxs-lookup"><span data-stu-id="66e1e-115">As a property `X`</span></span>
- <span data-ttu-id="66e1e-116">Dans l’accesseur Set de propriété `x = value;`</span><span class="sxs-lookup"><span data-stu-id="66e1e-116">In the property setter `x = value;`</span></span>
- <span data-ttu-id="66e1e-117">Dans l’accesseur Get de propriété `return x;`</span><span class="sxs-lookup"><span data-stu-id="66e1e-117">In the property getter `return x;`</span></span>

``` c#
class C
{
    private string _x;
    
    public C(string x)
    {
        _x = x;
    }
    public string X
    {
        get => _x;
        set { if (value == null) throw new NullArgumentException(nameof(X)); _x = value; }
    }
}
```

<span data-ttu-id="66e1e-118">Pour les propriétés qui ne nécessitent pas de validation ou de calcul, la caractère fastidieux peut être réduite à l’aide de propriétés automatiques, ce qui réduit la nécessité de déclarer un champ de stockage explicite pour la propriété.</span><span class="sxs-lookup"><span data-stu-id="66e1e-118">For properties that don't require validation or computation, the tedium can be reduced using auto-properties, thus cutting out the need to declare an explicit backing field for the property.</span></span> <span data-ttu-id="66e1e-119">Toutefois, si votre propriété nécessite une sorte de logique allant au-delà de ce qu’une propriété automatique fournit, la méthode ci-dessus est la meilleure solution.</span><span class="sxs-lookup"><span data-stu-id="66e1e-119">But if your property requires any sort of logic beyond what an auto-property provides, the above is the best you an do.</span></span>

<span data-ttu-id="66e1e-120">À la place, les constructeurs principaux réduisent la surcharge en plaçant des arguments de constructeur directement dans la portée de la classe, à nouveau évite la nécessité de déclarer explicitement un champ de stockage.</span><span class="sxs-lookup"><span data-stu-id="66e1e-120">Primary constructors instead reduce the overhead by putting constructor arguments directly in scope throughout the class, again obviating the need to explicitly declare a backing field.</span></span> <span data-ttu-id="66e1e-121">Ainsi, l’exemple ci-dessus deviendrait :</span><span class="sxs-lookup"><span data-stu-id="66e1e-121">Thus, the above example would become:</span></span>

``` c#
class C(string x)
{
    public string X
    {
        get => x;
        set { if (value == null) throw new NullArgumentException(nameof(X)); x = value; }
    }
}
```

<span data-ttu-id="66e1e-122">Dans cet exemple, le constructeur principal réduit le nombre d’entités nommées pour `x` de trois à deux, évite le `_x` champ de stockage.</span><span class="sxs-lookup"><span data-stu-id="66e1e-122">In this example, the primary constructor reduces the number of named entities for `x` from three to two, obviating the `_x` backing field.</span></span> <span data-ttu-id="66e1e-123">Elle supprime deux déclarations de membre sur trois (en gardant uniquement la déclaration de propriété elle-même) et réduit le nombre total de mentions de `x`/`_x`/`X` de huit à cinq.</span><span class="sxs-lookup"><span data-stu-id="66e1e-123">It removes two out of three member declarations (keeping only the property declaration itself), and reduces the total number of mentions of `x`/`_x`/`X` from eight to five.</span></span>


## <a name="detailed-design"></a><span data-ttu-id="66e1e-124">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="66e1e-124">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="66e1e-125">Les classes peuvent avoir une liste de paramètres :</span><span class="sxs-lookup"><span data-stu-id="66e1e-125">Classes can have a parameter list:</span></span>

``` c#
public class C(int i, string s)
{
    ...
}
```

<span data-ttu-id="66e1e-126">La liste de paramètres entraîne la déclaration implicite d’un constructeur pour la classe, avec la même accessibilité que la classe elle-même.</span><span class="sxs-lookup"><span data-stu-id="66e1e-126">The parameter list causes a constructor to be implicitly declared for the class, with the same accessibility as the class itself.</span></span>

``` c#
new C(5, "Hello");
```

<span data-ttu-id="66e1e-127">Les paramètres de constructeur principal sont dans la portée dans le corps de la classe.</span><span class="sxs-lookup"><span data-stu-id="66e1e-127">Primary constructor parameters are in scope throughout the class body.</span></span> <span data-ttu-id="66e1e-128">Si elles sont capturées par un membre de fonction ou une fonction anonyme, elles sont stockées en tant que champs privés dans la classe.</span><span class="sxs-lookup"><span data-stu-id="66e1e-128">If they are captured by a function member or anonymous function, they become stored as private fields in the class.</span></span> <span data-ttu-id="66e1e-129">S’ils sont utilisés uniquement pendant l’initialisation, ils ne sont pas stockés dans l’objet.</span><span class="sxs-lookup"><span data-stu-id="66e1e-129">If they are only used during initialization they will not be stored in the object.</span></span>

``` c#
public class C(int i, string s)
{
    int[] a = new int[i]; // i not captured
    public int S => s;    // s captured
}
```

<span data-ttu-id="66e1e-130">Si une classe avec un constructeur principal a une spécification de classe de base, celle-ci peut avoir une liste d’arguments.</span><span class="sxs-lookup"><span data-stu-id="66e1e-130">If a class with a primary constructor has a base class specification, that one can have an argument list.</span></span> <span data-ttu-id="66e1e-131">Cela sert de liste d’arguments à un initialiseur `base(...)` du constructeur déclaré implicitement.</span><span class="sxs-lookup"><span data-stu-id="66e1e-131">This serves as the argument list to a `base(...)` initializer of the implicitly declared constructor.</span></span> <span data-ttu-id="66e1e-132">Si aucune liste d’arguments n’est fournie, une liste vide est utilisée.</span><span class="sxs-lookup"><span data-stu-id="66e1e-132">If no argument list is provided, an empty one is assumed.</span></span>

``` c#
public class C(int i, string s) : B(s)
{
    ...
}
```
<span data-ttu-id="66e1e-133">La classe peut également avoir des constructeurs explicitement définis, mais ceux-ci doivent tous utiliser un initialiseur de `this(...)`.</span><span class="sxs-lookup"><span data-stu-id="66e1e-133">The class can have explicitly defined constructors as well, but those all have to use a `this(...)` initializer.</span></span> <span data-ttu-id="66e1e-134">Cela garantit que le constructeur principal est toujours appelé lorsqu’une nouvelle instance est construite.</span><span class="sxs-lookup"><span data-stu-id="66e1e-134">This ensures that the primary constructor is always called when a new instance is constructed.</span></span>

<span data-ttu-id="66e1e-135">Tous les initialiseurs dans le corps de la classe deviennent des assignations dans le constructeur généré.</span><span class="sxs-lookup"><span data-stu-id="66e1e-135">All initializers in the class body will become assignments in the generated constructor.</span></span> <span data-ttu-id="66e1e-136">Cela signifie que, contrairement à d’autres classes, les initialiseurs s’exécuteront *après* l’appel du constructeur de base, et non avant.</span><span class="sxs-lookup"><span data-stu-id="66e1e-136">This means that, unlike other classes, initializers will run *after* the base constructor has been invoked, not before.</span></span> <span data-ttu-id="66e1e-137">En outre, la classe générée contiendra des assignations pour initialiser tous les champs privés qui ont été générés pour stocker les paramètres du constructeur principal capturés par les membres.</span><span class="sxs-lookup"><span data-stu-id="66e1e-137">In addition, the generated class will contain assignments to initialize any private fields that were generated to store primary constructor parameters that were captured by members.</span></span> <span data-ttu-id="66e1e-138">Ces membres sont réécrits pour utiliser le champ privé au lieu du paramètre de la même façon que les fermetures pour les expressions lambda.</span><span class="sxs-lookup"><span data-stu-id="66e1e-138">Those members are rewritten to use the private field instead of the parameter in a manner similar to closures for lambda expressions.</span></span> <span data-ttu-id="66e1e-139">Les champs primaires générés sont d’abord initialisés, puis les assignations générées par l’initialiseur sont exécutées dans l’ordre d’apparition dans la classe.</span><span class="sxs-lookup"><span data-stu-id="66e1e-139">The generated primary fields are initialized first, and then the initializer-generated assignments are executed in the order of appearance in the class.</span></span>

<span data-ttu-id="66e1e-140">Dans l’exemple ci-dessus, l’effet de la déclaration de classe est comme si elle était réécrite comme suit :</span><span class="sxs-lookup"><span data-stu-id="66e1e-140">For the above example, the effect of the class declaration is as if rewritten like this:</span></span>

``` c#
public class C : B
{
    public C(int i, string s) : base(s)
    {
        __s = s;        // store parameter s for captured use
        a = new int[i]; // initialize a
    }
    int __s; // generated field for capture of s
    
    int[] a;
    public int S => __s; // s replaced with captured __s
}
```

<span data-ttu-id="66e1e-141">La capture présente des restrictions similaires à la capture de variables locales par des expressions lambda.</span><span class="sxs-lookup"><span data-stu-id="66e1e-141">The capture has similar restrictions to the capture of local variables by lambda expressions.</span></span> <span data-ttu-id="66e1e-142">Par exemple, les paramètres `ref` et `out` sont autorisés dans les constructeurs principaux, mais ils ne peuvent pas être capturés dans mes corps membres.</span><span class="sxs-lookup"><span data-stu-id="66e1e-142">For instance, `ref` and `out` parameters are allowed in primary constructors, but cannot be captured my member bodies.</span></span>


## <a name="drawbacks"></a><span data-ttu-id="66e1e-143">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="66e1e-143">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="66e1e-144">Dans un ordre approximatif de précision.</span><span class="sxs-lookup"><span data-stu-id="66e1e-144">In rough order of significance.</span></span>

* <span data-ttu-id="66e1e-145">La proposition utilise une syntaxe qui a également été proposée pour les enregistrements positionnels.</span><span class="sxs-lookup"><span data-stu-id="66e1e-145">The proposal uses syntax that has also been proposed for positional records.</span></span> <span data-ttu-id="66e1e-146">Si nous souhaitons ces deux fonctionnalités, un hébergement est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="66e1e-146">If we desire both features, some accommodation is required.</span></span> <span data-ttu-id="66e1e-147">Par exemple,</span><span class="sxs-lookup"><span data-stu-id="66e1e-147">E.g.</span></span> <span data-ttu-id="66e1e-148">un modificateur de `data` a été proposé sur les enregistrements.</span><span class="sxs-lookup"><span data-stu-id="66e1e-148">a `data` modifier on records has been proposed.</span></span>
* <span data-ttu-id="66e1e-149">La taille d’allocation des objets construits est moins évidente, car le compilateur détermine s’il faut allouer un champ pour un paramètre de constructeur principal en fonction du texte complet de la classe.</span><span class="sxs-lookup"><span data-stu-id="66e1e-149">The allocation size of constructed objects is less obvious, as the compiler determines whether to allocate a field for a primary constructor parameter based on the full text of the class.</span></span> <span data-ttu-id="66e1e-150">Ce risque est similaire à la capture implicite de variables par des expressions lambda.</span><span class="sxs-lookup"><span data-stu-id="66e1e-150">This risk is similar to the implicit capture of variables by lambda expressions.</span></span>
* <span data-ttu-id="66e1e-151">Une tentation courante (ou un modèle accidentel) peut consister à capturer le paramètre « identique » à plusieurs niveaux d’héritage, car il est passé en haut de la chaîne du constructeur au lieu de l’allouer explicitement à un champ protégé au niveau de la classe de base, ce qui aboutit à des allocations dupliquées pour les mêmes données dans les objets.</span><span class="sxs-lookup"><span data-stu-id="66e1e-151">A common temptation (or accidental pattern) might be to capture the "same" parameter at multiple levels of inheritance as it is passed up the constructor chain instead of explicitly allotting it a protected field at the base class, leading to duplicated allocations for the same data in objects.</span></span> <span data-ttu-id="66e1e-152">Cela est très similaire au risque actuel de substitution des propriétés automatiques avec des propriétés automatiques.</span><span class="sxs-lookup"><span data-stu-id="66e1e-152">This is very similar to today's risk of overriding auto-properties with auto-properties.</span></span> 
* <span data-ttu-id="66e1e-153">Comme nous l’avons proposé ci-dessus, il n’existe pas de place pour une logique supplémentaire qui peut généralement être exprimée dans des corps de constructeur.</span><span class="sxs-lookup"><span data-stu-id="66e1e-153">As proposed above, there is no place for additional logic that might usually expressed in constructor bodies.</span></span> <span data-ttu-id="66e1e-154">L’extension « corps du constructeur principal » ci-dessous résout le.</span><span class="sxs-lookup"><span data-stu-id="66e1e-154">The "Primary constructor bodies" extension below addresses that.</span></span>
* <span data-ttu-id="66e1e-155">Comme nous l’avons proposé, la sémantique d’ordre d’exécution est légèrement différente de celle des constructeurs ordinaires.</span><span class="sxs-lookup"><span data-stu-id="66e1e-155">As proposed, execution order semantics are subtly different than with ordinary constructors.</span></span> <span data-ttu-id="66e1e-156">Cela peut probablement être résolu, mais au détriment de certaines des propositions d’extension (notamment les « corps de constructeur principal »).</span><span class="sxs-lookup"><span data-stu-id="66e1e-156">This could probably be remedied, but at the cost of some of the extension proposals (notably "Primary constructor bodies").</span></span>
* <span data-ttu-id="66e1e-157">La proposition ne fonctionne que lorsqu’un seul constructeur peut être désigné comme principal.</span><span class="sxs-lookup"><span data-stu-id="66e1e-157">The proposal only works when a single constructor can be designated primary.</span></span>
* <span data-ttu-id="66e1e-158">Il n’existe aucun moyen d’avoir une accessibilité distincte de la classe et du constructeur principal.</span><span class="sxs-lookup"><span data-stu-id="66e1e-158">There is no way to have separate accessibility of the class and the primary constructor.</span></span> <span data-ttu-id="66e1e-159">Par exemple, dans les situations où les constructeurs publics délèguent tous à un constructeur « Build-it-all » privé qui serait nécessaire.</span><span class="sxs-lookup"><span data-stu-id="66e1e-159">For instance, in situations where public constructors all delegate to one private "build-it-all" constructor that would be needed.</span></span> <span data-ttu-id="66e1e-160">Si nécessaire, la syntaxe peut être proposée ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="66e1e-160">If necessary, syntax could be proposed for that later.</span></span>


## <a name="alternatives"></a><span data-ttu-id="66e1e-161">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="66e1e-161">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="66e1e-162">Les enregistrements positionnels complets peuvent être une alternative ou coexister avec les constructeurs principaux, selon les spécificités.</span><span class="sxs-lookup"><span data-stu-id="66e1e-162">Full-on positional records may be an alternative, or may coexist with primary constructors, depending on the specifics.</span></span> <span data-ttu-id="66e1e-163">Ils permettent d' *obtenir plus* d’abréviation dans un plus *petit* nombre de scénarios.</span><span class="sxs-lookup"><span data-stu-id="66e1e-163">They would allow for *more* abbreviation in a *smaller* number of scenarios.</span></span> <span data-ttu-id="66e1e-164">Les deux sont potentiellement utiles, mais ils peuvent être excessifs, sauf s’ils peuvent être facilement intégrés entre eux.</span><span class="sxs-lookup"><span data-stu-id="66e1e-164">So both are potentially useful, but having both may be overkill, unless they can be somewhat neatly integrated with each other.</span></span>


## <a name="possible-extensions"></a><span data-ttu-id="66e1e-165">Extensions possibles</span><span class="sxs-lookup"><span data-stu-id="66e1e-165">Possible extensions</span></span>
[extensions]: #possible-extensions

<span data-ttu-id="66e1e-166">Il s’agit de variations ou d’ajouts à la proposition principale qui peuvent être considérées conjointement avec elle, ou à un moment ultérieur si elles sont jugées utiles.</span><span class="sxs-lookup"><span data-stu-id="66e1e-166">These are variations or additions to the core proposal that may be considered in conjunction with it, or at a later stage if deemed useful.</span></span>

### <a name="primary-constructor-bodies"></a><span data-ttu-id="66e1e-167">Corps du constructeur principal</span><span class="sxs-lookup"><span data-stu-id="66e1e-167">Primary constructor bodies</span></span>

<span data-ttu-id="66e1e-168">Les constructeurs eux-mêmes contiennent souvent une logique de validation de paramètre ou un autre code d’initialisation non trivial qui ne peut pas être exprimé comme des initialiseurs.</span><span class="sxs-lookup"><span data-stu-id="66e1e-168">Constructors themselves often contain parameter validation logic or other nontrivial initialization code that cannot be expressed as initializers.</span></span>

<span data-ttu-id="66e1e-169">Les constructeurs principaux peuvent être étendus pour permettre aux blocs d’instructions d’apparaître directement dans le corps de la classe.</span><span class="sxs-lookup"><span data-stu-id="66e1e-169">Primary constructors could be extended to allow statement blocks to appear directly in the class body.</span></span> <span data-ttu-id="66e1e-170">Ces instructions seraient insérées dans le constructeur généré au point où elles apparaissent entre l’initialisation des assignations, et seraient donc exécutées avec des initialiseurs.</span><span class="sxs-lookup"><span data-stu-id="66e1e-170">Those statements would be inserted in the generated constructor at the point where they appear between initializing assignments, and would thus be executed interspersed with initializers.</span></span> <span data-ttu-id="66e1e-171">Exemple :</span><span class="sxs-lookup"><span data-stu-id="66e1e-171">For instance:</span></span>

``` c#
public class C(int i, string s) : B(s)
{
    {
        if (i < 0) throw new ArgumentOutOfRangeException(nameof(i));
    }
    int[] a = new int[i];
    public int S => s;
}
```

### <a name="initializer-fields-and-initializer-functions"></a><span data-ttu-id="66e1e-172">Champs d’initialiseur et fonctions d’initialiseur</span><span class="sxs-lookup"><span data-stu-id="66e1e-172">Initializer fields and initializer functions</span></span>

<span data-ttu-id="66e1e-173">Dans une classe avec un constructeur principal, nous pourrions considérer les déclarations de champ et de méthode sans modificateurs d’accessibilité comme des variables locales et des fonctions locales :</span><span class="sxs-lookup"><span data-stu-id="66e1e-173">In a class with a primary constructor we could consider field and method declarations without accessibility modifiers to be more like local variables and local functions:</span></span>

* <span data-ttu-id="66e1e-174">Tout comme les paramètres de constructeur principal, les « champs d’initialiseur » ne sont capturés dans un champ privé réel que s’ils ont été utilisés dans les fonctions membres.</span><span class="sxs-lookup"><span data-stu-id="66e1e-174">Just like primary constructor parameters the "initializer fields" would only be captured into an actual private field if they were used in function members.</span></span>
* <span data-ttu-id="66e1e-175">Les « fonctions d’initialiseur » ne sont prises en compte que pour capturer les paramètres de constructeur principal et les champs d’initialiseur s’ils étaient eux-mêmes utilisés dans d’autres fonctions membres.</span><span class="sxs-lookup"><span data-stu-id="66e1e-175">The "initializer functions" would only be considered to capture primary constructor parameters and initializer fields if they were themselves used in other function members.</span></span> <span data-ttu-id="66e1e-176">Si elles ne sont pas capturées, elles peuvent être générées de façon plus optimale, comme les fonctions locales.</span><span class="sxs-lookup"><span data-stu-id="66e1e-176">If not captured, they could be generated in a more optimal fashion, like local functions.</span></span>
* <span data-ttu-id="66e1e-177">Tout comme les paramètres du constructeur principal, ils ne sont pas disponibles via l’accès aux membres, mais uniquement en tant que nom simple.</span><span class="sxs-lookup"><span data-stu-id="66e1e-177">Just like primary constructor parameters they would not be available via member access, but only as a simple name.</span></span>

<span data-ttu-id="66e1e-178">Cela peut être utilisé pour les variables temporaires et les fonctions d’assistance qui ne concernent que l’initialisation :</span><span class="sxs-lookup"><span data-stu-id="66e1e-178">This could be used for temporary variables and helper functions that are only relevant to initialization:</span></span>

``` c#
public class C(string s)
{
    int size = s.Length;             // not captured
    int[] Create() => new int[size]; // not captured
    int[] a = Create();
    ...
}
```

<span data-ttu-id="66e1e-179">Cela peut être trop subtil, en particulier dans la mesure où l’absence de modificateurs d’accessibilité ne signifie simplement `private`.</span><span class="sxs-lookup"><span data-stu-id="66e1e-179">This may be too subtle, especially since the absence of accessibility modifiers elsewhere simply means `private`.</span></span> 

### <a name="initializer-statements"></a><span data-ttu-id="66e1e-180">Instructions d’initialiseur</span><span class="sxs-lookup"><span data-stu-id="66e1e-180">Initializer statements</span></span>

<span data-ttu-id="66e1e-181">Une combinaison radicale des extensions ci-dessus consiste à autoriser simplement les instructions directement dans le corps de la classe.</span><span class="sxs-lookup"><span data-stu-id="66e1e-181">A radical combination of the above to extensions would be to simply allow statements directly in the class body.</span></span> <span data-ttu-id="66e1e-182">Ces instructions sont exactement les mêmes que les corps de constructeur intercalés proposés ci-dessus, sauf qu’ils n’ont pas besoin d’être placés dans `{ }`.</span><span class="sxs-lookup"><span data-stu-id="66e1e-182">Such statements are exactly as the interspersed constructor bodies proposed above, except they don't need to be enclosed in `{ }`.</span></span> <span data-ttu-id="66e1e-183">Pour que cela soit suffisamment utile, les variables « locales » et les fonctions d’assistance doivent également être exprimables au niveau supérieur de la classe, de la même façon que dans l’extension « champs d’initialiseur et fonctions d’initialiseur » ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="66e1e-183">For this to be sufficiently useful, "local" variables and helper functions would need to also be expressible at the top level of the class, in the manner explored in the extension "Initializer fields and initializer functions" above.</span></span>


### <a name="member-access"></a><span data-ttu-id="66e1e-184">Accès au membre</span><span class="sxs-lookup"><span data-stu-id="66e1e-184">Member access</span></span>

<span data-ttu-id="66e1e-185">La proposition principale traite les paramètres de constructeur principal comme des paramètres qui peuvent uniquement être appelés noms simples.</span><span class="sxs-lookup"><span data-stu-id="66e1e-185">The core proposal treats primary constructor parameters as parameters that can only be referred as simple names.</span></span> <span data-ttu-id="66e1e-186">Une alternative consiste à les autoriser à être référencés comme s’il s’agissait de champs privés, par exemple avec un accès de membre, *même* s’ils ne sont parfois pas générés en tant que champs.</span><span class="sxs-lookup"><span data-stu-id="66e1e-186">An alternative is to allow them to be referenced as if they were private fields, i.e. with a member access, *even* if they are sometimes not generated as fields.</span></span> <span data-ttu-id="66e1e-187">Cela leur permet d’être référencés en tant que `this.x` lorsqu’ils sont masqués par des variables locales et qu’ils sont accessibles à partir d’une autre instance comme `other.x`.</span><span class="sxs-lookup"><span data-stu-id="66e1e-187">This would allow them to be referenced as `this.x` when shadowed by local variables, and accessed from a different instance as `other.x`.</span></span>

<span data-ttu-id="66e1e-188">En cas d’application à l’extension « champs d’initialiseur et fonctions d’initialiseur », cela réduirait également le degré de différence entre ces derniers et les membres privés ordinaires.</span><span class="sxs-lookup"><span data-stu-id="66e1e-188">If applied to the "initializer fields and initializer functions" extension this would also reduce the degree to which those were different from ordinary private members.</span></span> <span data-ttu-id="66e1e-189">La seule différence est que le compilateur est libre de les elider à partir de l’objet s’il n’est utilisé qu’au cours de l’initialisation.</span><span class="sxs-lookup"><span data-stu-id="66e1e-189">The only difference would then be that the compiler is free to elide them from the object if only used during initialization.</span></span>

