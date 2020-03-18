---
ms.openlocfilehash: d0bb80305ccc755a555cf47a1d010fc4cb9a7bcd
ms.sourcegitcommit: 5688b13e66dd77b224a1223338de9e3b1f66d7f0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79485517"
---
# <a name="readonly-instance-members"></a><span data-ttu-id="8d2aa-101">Membres d’instance en lecture seule</span><span class="sxs-lookup"><span data-stu-id="8d2aa-101">Readonly Instance Members</span></span>

<span data-ttu-id="8d2aa-102">Problème champion : <https://github.com/dotnet/csharplang/issues/1710></span><span class="sxs-lookup"><span data-stu-id="8d2aa-102">Championed Issue: <https://github.com/dotnet/csharplang/issues/1710></span></span>

## <a name="summary"></a><span data-ttu-id="8d2aa-103">Résumé</span><span class="sxs-lookup"><span data-stu-id="8d2aa-103">Summary</span></span>
[summary]: #summary

<span data-ttu-id="8d2aa-104">Fournir un moyen de spécifier des membres d’instance individuels sur un struct ne pas modifier l’État, de la même façon que `readonly struct` spécifie qu’aucun membre d’instance ne modifie l’État.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-104">Provide a way to specify individual instance members on a struct do not modify state, in the same way that `readonly struct` specifies no instance members modify state.</span></span>

<span data-ttu-id="8d2aa-105">Il est à noter que `readonly instance member` ! = `pure instance member`.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-105">It is worth noting that `readonly instance member` != `pure instance member`.</span></span> <span data-ttu-id="8d2aa-106">Un membre d’instance `pure` garantit qu’aucun État ne sera modifié.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-106">A `pure` instance member guarantees no state will be modified.</span></span> <span data-ttu-id="8d2aa-107">Un membre d’instance `readonly` garantit uniquement que l’état de l’instance ne sera pas modifié.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-107">A `readonly` instance member only guarantees that instance state will not be modified.</span></span>

<span data-ttu-id="8d2aa-108">Tous les membres d’instance sur un `readonly struct` peuvent être considérés comme des `readonly instance members`implicites.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-108">All instance members on a `readonly struct` could be considered implicitly `readonly instance members`.</span></span> <span data-ttu-id="8d2aa-109">Les `readonly instance members` explicites déclarés sur des structs non ReadOnly se comportent de la même manière.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-109">Explicit `readonly instance members` declared on non-readonly structs would behave in the same manner.</span></span> <span data-ttu-id="8d2aa-110">Par exemple, ils créent toujours des copies masquées si vous avez appelé un membre d’instance (sur l’instance actuelle ou sur un champ de l’instance) qui n’était lui-même pas en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-110">For example, they would still create hidden copies if you called an instance member (on the current instance or on a field of the instance) which was itself not-readonly.</span></span>

## <a name="motivation"></a><span data-ttu-id="8d2aa-111">Motivation</span><span class="sxs-lookup"><span data-stu-id="8d2aa-111">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="8d2aa-112">Aujourd’hui, les utilisateurs ont la possibilité de créer des types de `readonly struct` que le compilateur impose que tous les champs soient ReadOnly (et, par extension, qu’aucun membre d’instance ne modifie l’État).</span><span class="sxs-lookup"><span data-stu-id="8d2aa-112">Today, users have the ability to create `readonly struct` types which the compiler enforces that all fields are readonly (and by extension, that no instance members modify the state).</span></span> <span data-ttu-id="8d2aa-113">Toutefois, il existe des scénarios où vous disposez d’une API existante qui expose des champs accessibles ou qui a une combinaison de membres mutants et non mutants.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-113">However, there are some scenarios where you have an existing API that exposes accessible fields or that has a mix of mutating and non-mutating members.</span></span> <span data-ttu-id="8d2aa-114">Dans ces circonstances, vous ne pouvez pas marquer le type comme `readonly` (il s’agit d’une modification avec rupture).</span><span class="sxs-lookup"><span data-stu-id="8d2aa-114">Under these circumstances, you cannot mark the type as `readonly` (it would be a breaking change).</span></span>

<span data-ttu-id="8d2aa-115">Cela n’a généralement pas d’impact, sauf dans le cas des paramètres de `in`.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-115">This normally doesn't have much impact, except in the case of `in` parameters.</span></span> <span data-ttu-id="8d2aa-116">Avec les paramètres de `in` pour les structs qui ne sont pas en lecture seule, le compilateur effectue une copie du paramètre pour chaque appel de membre d’instance, car il ne peut pas garantir que l’appel ne modifie pas l’état interne.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-116">With `in` parameters for non-readonly structs, the compiler will make a copy of the parameter for each instance member invocation, since it cannot guarantee that the invocation does not modify internal state.</span></span> <span data-ttu-id="8d2aa-117">Cela peut aboutir à une multitude de copies et à de mauvaises performances globales que si vous aviez juste passé la structure directement par valeur.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-117">This can lead to a multitude of copies and worse overall performance than if you had just passed the struct directly by value.</span></span> <span data-ttu-id="8d2aa-118">Pour obtenir un exemple, consultez ce code sur [sharplab](https://sharplab.io/#v2:CYLg1APgAgDABFAjAbgLACgNQMxwM4AuATgK4DGBcAagKYUD2RATBgN4ZycK4BmANvQCGlAB5p0XbnH5DKAT3GSOXHNIHC4AGRoA7AOYEAFgGUAjiUFEawZZ3YTJXPTQK3H9x54QB2OAAoROAAqOBEASjgwNy8YvzlguDkwxS8AXzd09EysXCgmOABhOA8VXnVKAFk/AEsdajoCRnyAN0E+EhoIks8oX1b2mgA6bX0jMwsrYEi4fo7h3QMTc0trFM5M1KA==)</span><span class="sxs-lookup"><span data-stu-id="8d2aa-118">For an example, see this code on [sharplab](https://sharplab.io/#v2:CYLg1APgAgDABFAjAbgLACgNQMxwM4AuATgK4DGBcAagKYUD2RATBgN4ZycK4BmANvQCGlAB5p0XbnH5DKAT3GSOXHNIHC4AGRoA7AOYEAFgGUAjiUFEawZZ3YTJXPTQK3H9x54QB2OAAoROAAqOBEASjgwNy8YvzlguDkwxS8AXzd09EysXCgmOABhOA8VXnVKAFk/AEsdajoCRnyAN0E+EhoIks8oX1b2mgA6bX0jMwsrYEi4fo7h3QMTc0trFM5M1KA==)</span></span>

<span data-ttu-id="8d2aa-119">Voici d’autres scénarios dans lesquels des copies masquées peuvent se produire : `static readonly fields` et `literals`.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-119">Some other scenarios where hidden copies can occur include `static readonly fields` and `literals`.</span></span> <span data-ttu-id="8d2aa-120">Si elles sont prises en charge à l’avenir, `blittable constants` se termineraient dans le même bateau ; Cela nécessite actuellement une copie complète (sur l’appel de membre d’instance) si le struct n’est pas marqué `readonly`.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-120">If they are supported in the future, `blittable constants` would end up in the same boat; that is they all currently necessitate a full copy (on instance member invocation) if the struct is not marked `readonly`.</span></span>

## <a name="design"></a><span data-ttu-id="8d2aa-121">Conception</span><span class="sxs-lookup"><span data-stu-id="8d2aa-121">Design</span></span>
[design]: #design

<span data-ttu-id="8d2aa-122">Permet à un utilisateur de spécifier qu’un membre d’instance est lui-même `readonly` et ne modifie pas l’état de l’instance (avec toutes les vérifications appropriées effectuées par le compilateur, bien entendu).</span><span class="sxs-lookup"><span data-stu-id="8d2aa-122">Allow a user to specify that an instance member is, itself, `readonly` and does not modify the state of the instance (with all the appropriate verification done by the compiler, of course).</span></span> <span data-ttu-id="8d2aa-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8d2aa-123">For example:</span></span>

```csharp
public struct Vector2
{
    public float x;
    public float y;

    public readonly float GetLengthReadonly()
    {
        return MathF.Sqrt(LengthSquared);
    }

    public float GetLength()
    {
        return MathF.Sqrt(LengthSquared);
    }

    public readonly float GetLengthIllegal()
    {
        var tmp = MathF.Sqrt(LengthSquared);

        x = tmp;    // Compiler error, cannot write x
        y = tmp;    // Compiler error, cannot write y

        return tmp;
    }

    public float LengthSquared
    {
        readonly get
        {
            return (x * x) +
                   (y * y);
        }
    }
}

public static class MyClass
{
    public static float ExistingBehavior(in Vector2 vector)
    {
        // This code causes a hidden copy, the compiler effectively emits:
        //    var tmpVector = vector;
        //    return tmpVector.GetLength();
        //
        // This is done because the compiler doesn't know that `GetLength()`
        // won't mutate `vector`.

        return vector.GetLength();
    }

    public static float ReadonlyBehavior(in Vector2 vector)
    {
        // This code is emitted exactly as listed. There are no hidden
        // copies as the `readonly` modifier indicates that the method
        // won't mutate `vector`.

        return vector.GetLengthReadonly();
    }
}
```

<span data-ttu-id="8d2aa-124">ReadOnly peut être appliqué aux accesseurs de propriété pour indiquer que les `this` ne seront pas mutés dans l’accesseur.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-124">Readonly can be applied to property accessors to indicate that `this` will not be mutated in the accessor.</span></span> <span data-ttu-id="8d2aa-125">Les exemples suivants ont des accesseurs set en lecture seule, car ces accesseurs modifient l’état du champ de membre, mais ne modifient pas la valeur de ce champ de membre.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-125">The following examples have readonly setters because those accessors modify the state of member field, but do not modify the value of that member field.</span></span>

```csharp
public int Prop1
{
    readonly get
    {
        return this._store["Prop1"];
    }
    readonly set
    {
        this._store["Prop1"] = value;
    }
}
```

<span data-ttu-id="8d2aa-126">Lorsque `readonly` est appliqué à la syntaxe de propriété, cela signifie que tous les accesseurs sont `readonly`.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-126">When `readonly` is applied to the property syntax, it means that all accessors are `readonly`.</span></span>

```csharp
public readonly int Prop2
{
    get
    {
        return this._store["Prop2"];
    }
    set
    {
        this._store["Prop2"] = value;
    }
}
```

<span data-ttu-id="8d2aa-127">ReadOnly ne peut être appliqué qu’à des accesseurs qui n’effectuent pas de mutation sur le type conteneur.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-127">Readonly can only be applied to accessors which do not mutate the containing type.</span></span>

```csharp
public int Prop3
{
    readonly get
    {
        return this._prop3;
    }
    set
    {
        this._prop3 = value;
    }
}
```

<span data-ttu-id="8d2aa-128">ReadOnly peut être appliqué à certaines propriétés implémentées automatiquement, mais il n’aura pas d’effet significatif.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-128">Readonly can be applied to some auto-implemented properties, but it won't have a meaningful effect.</span></span> <span data-ttu-id="8d2aa-129">Le compilateur traite tous les getters implémentés automatiquement comme ReadOnly, que le mot clé `readonly` soit présent ou non.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-129">The compiler will treat all auto-implemented getters as readonly whether or not the `readonly` keyword is present.</span></span>

```csharp
// Allowed
public readonly int Prop4 { get; }
public int Prop5 { readonly get; }
public int Prop6 { readonly get; set; }

// Not allowed
public readonly int Prop7 { get; set; }
public int Prop8 { get; readonly set; }
```

<span data-ttu-id="8d2aa-130">La lecture seule peut être appliquée à des événements implémentés manuellement, mais pas à des événements de type champ.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-130">Readonly can be applied to manually-implemented events, but not field-like events.</span></span> <span data-ttu-id="8d2aa-131">ReadOnly ne peut pas être appliqué à des accesseurs d’événement individuels (Add/Remove).</span><span class="sxs-lookup"><span data-stu-id="8d2aa-131">Readonly cannot be applied to individual event accessors (add/remove).</span></span>

```csharp
// Allowed
public readonly event Action<EventArgs> Event1
{
    add { }
    remove { }
}

// Not allowed
public readonly event Action<EventArgs> Event2;
public event Action<EventArgs> Event3
{
    readonly add { }
    readonly remove { }
}
public static readonly event Event4
{
    add { }
    remove { }
}
```

<span data-ttu-id="8d2aa-132">Autres exemples de syntaxe :</span><span class="sxs-lookup"><span data-stu-id="8d2aa-132">Some other syntax examples:</span></span>

* <span data-ttu-id="8d2aa-133">Membres de l’expression : `public readonly float ExpressionBodiedMember => (x * x) + (y * y);`</span><span class="sxs-lookup"><span data-stu-id="8d2aa-133">Expression bodied members: `public readonly float ExpressionBodiedMember => (x * x) + (y * y);`</span></span>
* <span data-ttu-id="8d2aa-134">Contraintes génériques : `public static readonly void GenericMethod<T>(T value) where T : struct { }`</span><span class="sxs-lookup"><span data-stu-id="8d2aa-134">Generic constraints: `public static readonly void GenericMethod<T>(T value) where T : struct { }`</span></span>

<span data-ttu-id="8d2aa-135">Le compilateur émettra le membre d’instance, comme d’habitude, et émettrait en outre un attribut reconnu par le compilateur indiquant que le membre d’instance ne modifie pas l’État.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-135">The compiler would emit the instance member, as usual, and would additionally emit a compiler recognized attribute indicating that the instance member does not modify state.</span></span> <span data-ttu-id="8d2aa-136">Cela amène efficacement le paramètre de `this` masqué à devenir `in T` au lieu de `ref T`.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-136">This effectively causes the hidden `this` parameter to become `in T` instead of `ref T`.</span></span>

<span data-ttu-id="8d2aa-137">Cela permet à l’utilisateur d’appeler sans risque cette méthode d’instance sans que le compilateur doive effectuer une copie.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-137">This would allow the user to safely call said instance method without the compiler needing to make a copy.</span></span>

<span data-ttu-id="8d2aa-138">Les restrictions sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="8d2aa-138">The restrictions would include:</span></span>

* <span data-ttu-id="8d2aa-139">Le modificateur `readonly` ne peut pas être appliqué à des méthodes statiques, à des constructeurs ou à des destructeurs.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-139">The `readonly` modifier cannot be applied to static methods, constructors or destructors.</span></span>
* <span data-ttu-id="8d2aa-140">Le modificateur `readonly` ne peut pas être appliqué aux délégués.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-140">The `readonly` modifier cannot be applied to delegates.</span></span>
* <span data-ttu-id="8d2aa-141">Le modificateur `readonly` ne peut pas être appliqué aux membres de la classe ou de l’interface.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-141">The `readonly` modifier cannot be applied to members of class or interface.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="8d2aa-142">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="8d2aa-142">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="8d2aa-143">Mêmes inconvénients que pour les méthodes de `readonly struct` dès aujourd’hui.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-143">Same drawbacks as exist with `readonly struct` methods today.</span></span> <span data-ttu-id="8d2aa-144">Certains codes peuvent toujours provoquer des copies masquées.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-144">Certain code may still cause hidden copies.</span></span>

## <a name="notes"></a><span data-ttu-id="8d2aa-145">Notes</span><span class="sxs-lookup"><span data-stu-id="8d2aa-145">Notes</span></span>
[notes]: #notes

<span data-ttu-id="8d2aa-146">L’utilisation d’un attribut ou d’un autre mot clé peut également être possible.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-146">Using an attribute or another keyword may also be possible.</span></span>

<span data-ttu-id="8d2aa-147">Cette proposition est quelque peu liée à (mais il s’agit plus d’un sous-ensemble de) `functional purity` et/ou `constant expressions`, qui ont tous les deux été des propositions existantes.</span><span class="sxs-lookup"><span data-stu-id="8d2aa-147">This proposal is somewhat related to (but is more a subset of) `functional purity` and/or `constant expressions`, both of which have had some existing proposals.</span></span>
