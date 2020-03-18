---
ms.openlocfilehash: 76065293f652979ab395e131d657e44899c5a65b
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484551"
---
# <a name="simplified-null-argument-checking"></a>Vérification simplifiée des arguments null

## <a name="summary"></a>Résumé
Cette proposition fournit une syntaxe simplifiée pour la validation des arguments de méthode qui ne sont pas `null` et lèvent `ArgumentNullException` de manière appropriée.

## <a name="motivation"></a>Motivation
L’utilisation de la conception de types référence Nullable nous a permis d’examiner le code nécessaire à la validation de l’argument `null`. Étant donné que Diagnostics proactifs NRT n’affecte pas l’exécution du code, les développeurs doivent toujours ajouter `if (arg is null) throw` code de plaque de chaudière, même dans des projets entièrement `null` propres. Cela nous a permis d’explorer une syntaxe minimale pour l’argument `null` la validation dans le langage. 

Bien que cette `null` syntaxe de validation de paramètre soit censée être couplée fréquemment avec Diagnostics proactifs NRT, la proposition est entièrement indépendante de celle-ci. La syntaxe peut être utilisée indépendamment des directives de `#nullable`.

## <a name="detailed-design"></a>Conception détaillée 

### <a name="null-validation-parameter-syntax"></a>Syntaxe des paramètres de validation null
L’opérateur Bang, `!`, peut être positionné après un nom de paramètre dans une liste de paramètres, ce qui C# entraîne l’émission par le compilateur d’un code de vérification de `null` standard pour ce paramètre. C’est ce qu’on appelle la syntaxe de paramètre de validation `null`. Par exemple :

``` csharp
void M(string name!) {
    ...
}
```

Seront traduites dans :

``` csharp
void M(string name) {
    if (name is null) {
        throw new ArgumentNullException(nameof(name));
    }
    ...
}
```

La vérification de `null` générée se produit avant tout code créé par le développeur dans la méthode. Lorsque plusieurs paramètres contiennent l’opérateur `!`, les contrôles sont effectués dans le même ordre que celui où les paramètres sont déclarés.

``` csharp
void M(string p1, string p2) {
    if (p1 is null) {
        throw new ArgumentNullException(nameof(p1));
    }
    if (p2 is null) {
        throw new ArgumentNullException(nameof(p2));
    }
    ...
}
```

La vérification concernera spécifiquement l’égalité des références pour `null`, elle n’appelle pas `==` ou n’importe quel opérateur défini par l’utilisateur. Cela signifie également que l’opérateur `!` peut uniquement être ajouté aux paramètres dont le type peut être testé pour l’égalité par rapport à `null`. Cela signifie qu’il ne peut pas être utilisé sur un paramètre dont le type est connu comme étant un type valeur.

``` csharp
// Error: Cannot use ! on parameters who types derive from System.ValueType
void G<T>(T arg!) where T : struct {

}
```

Dans le cas d’un constructeur, la validation `null` se produit avant tout autre code dans le constructeur. Cela comprend les éléments suivants : 

- Chaînage à d’autres constructeurs avec `this` ou `base` 
- Initialiseurs de champ qui se produisent implicitement dans le constructeur

Par exemple :

``` csharp
class C {
    string field = GetString();
    C(string name!): this(name) {
        ...
    }
}
```

Seront traduits approximativement de la façon suivante :

``` csharp
class C {
    C(string name)
        if (name is null) {
            throw new ArgumentNullException(nameof(name));
        }
        field = GetString();
        :this(name);
        ...
}
```

Remarque : il ne s’agit C# pas d’un code légal, mais à la place d’une approximation de ce que fait l’implémentation. 

La syntaxe du paramètre de validation `null` est également valide dans les listes de paramètres lambda. Cela est valide même dans la syntaxe de paramètre unique qui n’a pas de parenthèses.

``` csharp
void G() {
    // An identity lambda which throws on a null input
    Func<string, string> s = x! => x;
}
```

La syntaxe est également valide sur les paramètres des méthodes d’itérateur. Contrairement à l’autre code de l’itérateur, la validation `null` se produit lorsque la méthode Iterator est appelée, et non lorsque l’énumérateur sous-jacent est parcouru. Cela est vrai pour les itérateurs traditionnels ou `async`.

``` csharp
class Iterators {
    IEnumerable<char> GetCharacters(string s!) {
        foreach (var c in s) {
            yield return c;
        }
    }

    void Use() {
        // The invocation of GetCharacters will throw
        IEnumerable<char> e = GetCharacters(null);
    }
}
```

L’opérateur `!` peut uniquement être utilisé pour les listes de paramètres qui ont un corps de méthode associé. Cela signifie qu’il ne peut pas être utilisé dans une méthode `abstract`, `interface`, `delegate` ou `partial` définition de méthode.

### <a name="extending-is-null"></a>L’extension de est null
Les types pour lesquels l’expression `is null` est valide sont étendus pour inclure des paramètres de type sans contrainte. Cela lui permettra de rechercher les `null` sur tous les types dont le contrôle de `null` est valide. En particulier, il s’agit de types qui ne sont pas vraiment connus comme des types valeur. Par exemple, les paramètres de type qui sont limités à `struct` ne peuvent pas être utilisés avec cette syntaxe.

``` csharp
void NullCheck<T1, T2>(T1 p1, T2 p2) where T2 : struct {
    // Okay: T1 could be a class or struct here.
    if (p1 is null) {
        ...
    }

    // Error 
    if (p2 is null) { 
        ...
    }
}
```

Le comportement de `is null` sur un paramètre de type sera le même que `== null` aujourd’hui. Dans les cas où le paramètre de type est instancié en tant que type valeur, le code est évalué comme `false`. Dans les cas où il s’agit d’un type référence, le code effectue un contrôle de `is null` approprié.

### <a name="intersection-with-nullable-reference-types"></a>Intersection avec les types de référence Nullable
Tout paramètre ayant un opérateur `!` appliqué à son nom commence avec l’État Nullable non `null`. Cela est vrai même si le type du paramètre lui-même est potentiellement `null`. Cela peut se produire avec un type explicitement Nullable, comme par exemple `string?`ou avec un paramètre de type sans contrainte. 

Quand une syntaxe de `!` sur des paramètres est combinée avec un type Nullable explicite sur le paramètre, un avertissement est émis par le compilateur :

``` csharp
void WarnCase<T>(
    string? name!, // Warning: combining explicit null checking with a nullable type
    T value1 // Okay
)
```

## <a name="open-issues"></a>Problèmes ouverts
None

## <a name="considerations"></a>Considérations

### <a name="constructors"></a>Constructeurs
La génération de code pour les constructeurs signifie qu’il y a un changement de comportement faible, mais observable, lors du passage d’une validation de `null` standard aujourd’hui à la syntaxe de paramètre de validation `null` (`!`). La validation de la `null` dans la validation standard intervient après les initialiseurs de champ et les appels de `base` ou de `this`. Cela signifie qu’un développeur ne peut pas nécessairement migrer 100% de sa validation `null` vers la nouvelle syntaxe. Les constructeurs nécessitent au moins une inspection.

Après discussion, il a été décidé qu’il est très peu probable que cela entraîne des problèmes d’adoption significatifs. Il est plus logique que le contrôle de `null` s’exécute avant toute logique du constructeur. Peut revoir si des problèmes de compatibilité importants sont détectés.

### <a name="warning-when-mixing--and-"></a>AVERTISSEMENT lors du mixage les!
Il y avait une longue discussion sur la nécessité ou non de l’émission d’un avertissement lorsque la syntaxe de `!` est appliquée à un paramètre explicitement typé en type Nullable. Sur la surface, il semblerait qu’il s’agissait d’une déclaration absurde par le développeur, mais dans certains cas, les hiérarchies de types peuvent forcer les développeurs dans une telle situation. 

Considérez la hiérarchie de classes suivante sur une série d’assemblys (en supposant que tous les éléments sont compilés avec `null` vérification activée) :

``` csharp
// Assembly1
abstract class C1 {
    protected abstract void M(object o); 
}

// Assembly2
abstract class C2 : C1 {

}

// Assembly3
abstract class C3 : C2 { 
    protected override void M(object o!) {
        ...
    }
}
```

Ici, l’auteur de `C3` a décidé d’ajouter `null` validation au paramètre `o`. Cela est entièrement conforme à la façon dont la fonctionnalité est destinée à être utilisée.

À présent, imaginez à une date ultérieure que l’auteur de Assembly2 décide d’ajouter la substitution suivante :

``` csharp
// Assembly2
abstract class C2 : C1 {
   protected override void M(object? o) { 
       ...
   }
}
```

Cela est autorisé par les types de référence Nullable, car il est légal de rendre le contrat plus flexible pour les positions d’entrée. La fonctionnalité Diagnostics proactifs NRT en général permet une co-contravariance raisonnable sur le paramètre/la possibilité de valeur null. Toutefois, le langage effectue la vérification de la coexistence en fonction de la substitution la plus spécifique, et non de la déclaration d’origine. Cela signifie que l’auteur de Assembly3 recevra un avertissement concernant le type de `o` ne correspondant pas et devra changer la signature en ce qui suit pour l’éliminer : 

``` csharp
// Assembly3
abstract class C3 : C2 { 
    protected override void M(object? o!) {
        ...
    }
}
```

À ce stade, l’auteur de Assembly3 a plusieurs choix :

- Ils peuvent accepter/supprimer l’avertissement concernant `object?` et `object` incompatibilité.
- Ils peuvent accepter/supprimer l’avertissement concernant `object?` et `!` incompatibilité.
- Ils peuvent simplement supprimer le contrôle de validation `null` (supprimer `!` et effectuer des vérifications explicites)

Il s’agit d’un scénario réel, mais pour l’instant, l’idée est d’avancer avec l’avertissement. S’il s’avère que l’avertissement se produit plus fréquemment que prévu, nous pouvons le supprimer plus tard (l’inverse n’est pas vrai).

### <a name="implicit-property-setter-arguments"></a>Arguments de l’accesseur Set de propriété implicite
L’argument `value` d’un paramètre est implicite et n’apparaît dans aucune liste de paramètres. Cela signifie qu’il ne peut pas être une cible de cette fonctionnalité. La syntaxe de l’accesseur Set de la propriété peut être étendue pour inclure une liste de paramètres pour permettre l’application de l’opérateur `!`. Mais cela réduit l’idée de cette fonctionnalité en simplifiant la validation de `null`. En tant que tel, l’argument `value` implicite ne fonctionne pas avec cette fonctionnalité.

## <a name="future-considerations"></a>Éléments futurs à prendre en considération
None
