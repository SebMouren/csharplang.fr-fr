---
ms.openlocfilehash: d0bb80305ccc755a555cf47a1d010fc4cb9a7bcd
ms.sourcegitcommit: 5688b13e66dd77b224a1223338de9e3b1f66d7f0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79485517"
---
# <a name="readonly-instance-members"></a>Membres d’instance en lecture seule

Problème champion : <https://github.com/dotnet/csharplang/issues/1710>

## <a name="summary"></a>Résumé
[summary]: #summary

Fournir un moyen de spécifier des membres d’instance individuels sur un struct ne pas modifier l’État, de la même façon que `readonly struct` spécifie qu’aucun membre d’instance ne modifie l’État.

Il est à noter que `readonly instance member` ! = `pure instance member`. Un membre d’instance `pure` garantit qu’aucun État ne sera modifié. Un membre d’instance `readonly` garantit uniquement que l’état de l’instance ne sera pas modifié.

Tous les membres d’instance sur un `readonly struct` peuvent être considérés comme des `readonly instance members`implicites. Les `readonly instance members` explicites déclarés sur des structs non ReadOnly se comportent de la même manière. Par exemple, ils créent toujours des copies masquées si vous avez appelé un membre d’instance (sur l’instance actuelle ou sur un champ de l’instance) qui n’était lui-même pas en lecture seule.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Aujourd’hui, les utilisateurs ont la possibilité de créer des types de `readonly struct` que le compilateur impose que tous les champs soient ReadOnly (et, par extension, qu’aucun membre d’instance ne modifie l’État). Toutefois, il existe des scénarios où vous disposez d’une API existante qui expose des champs accessibles ou qui a une combinaison de membres mutants et non mutants. Dans ces circonstances, vous ne pouvez pas marquer le type comme `readonly` (il s’agit d’une modification avec rupture).

Cela n’a généralement pas d’impact, sauf dans le cas des paramètres de `in`. Avec les paramètres de `in` pour les structs qui ne sont pas en lecture seule, le compilateur effectue une copie du paramètre pour chaque appel de membre d’instance, car il ne peut pas garantir que l’appel ne modifie pas l’état interne. Cela peut aboutir à une multitude de copies et à de mauvaises performances globales que si vous aviez juste passé la structure directement par valeur. Pour obtenir un exemple, consultez ce code sur [sharplab](https://sharplab.io/#v2:CYLg1APgAgDABFAjAbgLACgNQMxwM4AuATgK4DGBcAagKYUD2RATBgN4ZycK4BmANvQCGlAB5p0XbnH5DKAT3GSOXHNIHC4AGRoA7AOYEAFgGUAjiUFEawZZ3YTJXPTQK3H9x54QB2OAAoROAAqOBEASjgwNy8YvzlguDkwxS8AXzd09EysXCgmOABhOA8VXnVKAFk/AEsdajoCRnyAN0E+EhoIks8oX1b2mgA6bX0jMwsrYEi4fo7h3QMTc0trFM5M1KA==)

Voici d’autres scénarios dans lesquels des copies masquées peuvent se produire : `static readonly fields` et `literals`. Si elles sont prises en charge à l’avenir, `blittable constants` se termineraient dans le même bateau ; Cela nécessite actuellement une copie complète (sur l’appel de membre d’instance) si le struct n’est pas marqué `readonly`.

## <a name="design"></a>Conception
[design]: #design

Permet à un utilisateur de spécifier qu’un membre d’instance est lui-même `readonly` et ne modifie pas l’état de l’instance (avec toutes les vérifications appropriées effectuées par le compilateur, bien entendu). Par exemple :

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

ReadOnly peut être appliqué aux accesseurs de propriété pour indiquer que les `this` ne seront pas mutés dans l’accesseur. Les exemples suivants ont des accesseurs set en lecture seule, car ces accesseurs modifient l’état du champ de membre, mais ne modifient pas la valeur de ce champ de membre.

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

Lorsque `readonly` est appliqué à la syntaxe de propriété, cela signifie que tous les accesseurs sont `readonly`.

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

ReadOnly ne peut être appliqué qu’à des accesseurs qui n’effectuent pas de mutation sur le type conteneur.

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

ReadOnly peut être appliqué à certaines propriétés implémentées automatiquement, mais il n’aura pas d’effet significatif. Le compilateur traite tous les getters implémentés automatiquement comme ReadOnly, que le mot clé `readonly` soit présent ou non.

```csharp
// Allowed
public readonly int Prop4 { get; }
public int Prop5 { readonly get; }
public int Prop6 { readonly get; set; }

// Not allowed
public readonly int Prop7 { get; set; }
public int Prop8 { get; readonly set; }
```

La lecture seule peut être appliquée à des événements implémentés manuellement, mais pas à des événements de type champ. ReadOnly ne peut pas être appliqué à des accesseurs d’événement individuels (Add/Remove).

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

Autres exemples de syntaxe :

* Membres de l’expression : `public readonly float ExpressionBodiedMember => (x * x) + (y * y);`
* Contraintes génériques : `public static readonly void GenericMethod<T>(T value) where T : struct { }`

Le compilateur émettra le membre d’instance, comme d’habitude, et émettrait en outre un attribut reconnu par le compilateur indiquant que le membre d’instance ne modifie pas l’État. Cela amène efficacement le paramètre de `this` masqué à devenir `in T` au lieu de `ref T`.

Cela permet à l’utilisateur d’appeler sans risque cette méthode d’instance sans que le compilateur doive effectuer une copie.

Les restrictions sont les suivantes :

* Le modificateur `readonly` ne peut pas être appliqué à des méthodes statiques, à des constructeurs ou à des destructeurs.
* Le modificateur `readonly` ne peut pas être appliqué aux délégués.
* Le modificateur `readonly` ne peut pas être appliqué aux membres de la classe ou de l’interface.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Mêmes inconvénients que pour les méthodes de `readonly struct` dès aujourd’hui. Certains codes peuvent toujours provoquer des copies masquées.

## <a name="notes"></a>Notes
[notes]: #notes

L’utilisation d’un attribut ou d’un autre mot clé peut également être possible.

Cette proposition est quelque peu liée à (mais il s’agit plus d’un sous-ensemble de) `functional purity` et/ou `constant expressions`, qui ont tous les deux été des propositions existantes.
