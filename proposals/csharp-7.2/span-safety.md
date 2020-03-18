---
ms.openlocfilehash: 6088a5cd41926d828013f1b8e5736fd2b7939e44
ms.sourcegitcommit: da452002c3f472165a0e1fa7759f494cc703ae31
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "79485041"
---
# <a name="compile-time-enforcement-of-safety-for-ref-like-types"></a>Mise en œuvre de la sécurité au moment de la compilation pour les types de type Ref

## <a name="introduction"></a>Introduction

La principale raison des règles de sécurité supplémentaires en ce qui concerne les types tels que `Span<T>` et `ReadOnlySpan<T>` est que ces types doivent être confinés à la pile d’exécution.
 
Il existe deux raisons pour lesquelles les `Span<T>` et les types similaires doivent être des types de pile uniquement.

1. `Span<T>` est sémantiquement un struct contenant une référence et un `(ref T data, int length)`de plage. Quelle que soit l’implémentation réelle, les écritures dans ce type de structure ne seraient pas atomiques. Une « déchirure » simultanée de ce type de struct entraînerait la possibilité de `length` ne correspond pas à la `data`, provoquant des accès hors limites et des violations de sécurité de type, ce qui finit par provoquer l’endommagement du tas GC dans du code apparemment « sûr ».
2. Certaines implémentations de `Span<T>` contiennent littéralement un pointeur managé dans l’un de ses champs. Les pointeurs managés ne sont pas pris en charge en tant que champs d’objets de tas et le code qui gère pour placer un pointeur managé sur le tas GC se bloque généralement au moment du JIT.

Tous les problèmes ci-dessus sont atténués si des instances de `Span<T>` sont contractions pour exister uniquement sur la pile d’exécution. 

Un autre problème survient en raison de la composition. Il est généralement préférable de créer des types de données plus complexes qui incorporaient des instances `Span<T>` et `ReadOnlySpan<T>`. Ces types composites devraient être des structs et partager tous les dangers et exigences de `Span<T>`. Par conséquent, les règles de sécurité décrites ici doivent être affichées en fonction de l’ensemble des **_types de type REF_** .

La [spécification de langage brouillon](#draft-language-specification) est destinée à garantir que les valeurs d’un type de type REF se produisent uniquement sur la pile.

## <a name="generalized-ref-like-types-in-source-code"></a>Types de `ref-like` généralisés dans le code source

`ref-like` structs sont explicitement marqués dans le code source à l’aide du modificateur `ref` :

```csharp
ref struct TwoSpans<T>
{
    // can have ref-like instance fields
    public Span<T> first;
    public Span<T> second;
} 

// error: arrays of ref-like types are not allowed. 
TwoSpans<T>[] arr = null;

```

La désignation d’un struct comme REF permet à la structure d’avoir des champs d’instance de type REF et rend également toutes les exigences des types de type REF applicables à la structure. 

## <a name="metadata-representation-or-ref-like-structs"></a>Structures de représentation de métadonnées ou de type Ref

Les structs de type REF sont marqués avec l’attribut **System. Runtime. CompilerServices. IsRefLikeAttribute** .

L’attribut sera ajouté aux bibliothèques de base courantes, telles que `mscorlib`. Dans un cas, si l’attribut n’est pas disponible, le compilateur génère un en interne similaire à d’autres attributs incorporés à la demande, tels que `IsReadOnlyAttribute`.

Une mesure supplémentaire sera prise pour empêcher l’utilisation de structs de type REF dans les compilateurs qui ne sont pas familiarisés avec les règles de C# sécurité (y compris les compilateurs antérieurs à celui dans lequel cette fonctionnalité est implémentée). 

Si vous n’avez pas d’autres alternatives qui fonctionnent dans les compilateurs anciens sans maintenance, un attribut `Obsolete` avec une chaîne connue est ajouté à tous les structs de type Ref. Les compilateurs qui savent utiliser les types de type REF ignorent cette forme particulière de `Obsolete`.

Représentation classique des métadonnées :

```csharp
    [IsRefLike]
    [Obsolete("Types with embedded references are not supported in this version of your compiler.")]
    public struct TwoSpans<T>
    {
       // . . . .
    }
```

Remarque : ce n’est pas l’objectif de faire en sorte que toute utilisation de types de type REF sur les compilateurs anciens échoue 100%. Ce qui est difficile à atteindre et n’est pas strictement nécessaire. Par exemple, il serait toujours possible de contourner le `Obsolete` à l’aide de code dynamique ou, par exemple, en créant un tableau de types de type REF à travers la réflexion.

En particulier, si l’utilisateur souhaite réellement placer un `Obsolete` ou un attribut `Deprecated` sur un type de type REF, nous n’aurons pas d’autre choix que d’émettre un attribut prédéfini, car `Obsolete` attribut ne peut pas être appliqué plusieurs fois.  

## <a name="examples"></a>Exemples :

```csharp
SpanLikeType M1(ref SpanLikeType x, Span<byte> y)
{
    // this is all valid, unconcerned with stack-referring stuff
    var local = new SpanLikeType(y);
    x = local;
    return x;
}

void Test1(ref SpanLikeType param1, Span<byte> param2)
{
    Span<byte> stackReferring1 = stackalloc byte[10];
    var stackReferring2 = new SpanLikeType(stackReferring1);

    // this is allowed
    stackReferring2 = M1(ref stackReferring2, stackReferring1);

    // this is NOT allowed
    stackReferring2 = M1(ref param1, stackReferring1);

    // this is NOT allowed
    param1 = M1(ref stackReferring2, stackReferring1);

    // this is NOT allowed
    param2 = stackReferring1.Slice(10);

    // this is allowed
    param1 = new SpanLikeType(param2);

    // this is allowed
    stackReferring2 = param1;
}

ref SpanLikeType M2(ref SpanLikeType x)
{
    return ref x;
}

ref SpanLikeType Test2(ref SpanLikeType param1, Span<byte> param2)
{
    Span<byte> stackReferring1 = stackalloc byte[10];
    var stackReferring2 = new SpanLikeType(stackReferring1);

    ref var stackReferring3 = M2(ref stackReferring2);

    // this is allowed
    stackReferring3 = M1(ref stackReferring2, stackReferring1);

    // this is allowed
    M2(ref stackReferring3) = stackReferring2;

    // this is NOT allowed
    M1(ref param1) = stackReferring2;

    // this is NOT allowed
    param1 = stackReferring3;

    // this is NOT allowed
    return ref stackReferring3;

    // this is allowed
    return ref param1;
}

```

----------------

## <a name="draft-language-specification"></a>Spécification du langage brouillon

Ci-dessous, nous décrivons un ensemble de règles de sécurité pour les types de type REF (`ref struct`s) pour garantir que les valeurs de ces types se produisent uniquement sur la pile. Un ensemble de règles de sécurité différent, plus simple, serait possible si les variables locales ne peuvent pas être passées par référence. Cette spécification permet également la réaffectation sécurisée des variables locales Ref.

### <a name="overview"></a>Vue d’ensemble

Nous associons à chaque expression au moment de la compilation le concept de l’étendue à laquelle l’expression est autorisée à échapper, « sans risque ». De même, pour chaque lvalue, nous gérons un concept de l’étendue dans laquelle une référence est autorisée à échapper, « Ref-Safe-to-Escape ». Pour une expression lvalue donnée, elles peuvent être différentes.

Celles-ci sont analogues au « Safe to Return » de la fonctionnalité de variables locales REF, mais elles sont plus précises. Où le « sans risque » d’une expression enregistre uniquement si (ou non) il peut échapper à la méthode englobante dans son ensemble, les enregistrements sécurisés de l’échappement qui peuvent être échappés (ce qui peut ne pas dépasser le cadre d’une séquence d’échappement). Le mécanisme de sécurité de base est appliqué comme suit. Dans le cas d’une assignation d’une expression E1 avec une étendue sécurisée à l’échappement S1, à une expression (lvalue) E2 avec une portée sécurisée à l’échappement S2, il s’agit d’une erreur si S2 est une portée plus étendue que S1. Par construction, les deux étendues S1 et S2 se trouvent dans une relation d’imbrication, car une expression légale est toujours sûre à retourner à partir d’une portée englobant l’expression.

Pour le moment, c’est suffisant, à des fins d’analyse, pour prendre en charge uniquement deux étendues, externes à la méthode, et la portée de niveau supérieur de la méthode. Cela est dû au fait que les valeurs de type REF avec étendues internes ne peuvent pas être créées et que les variables locales Ref ne prennent pas en charge la réaffectation. Toutefois, les règles peuvent prendre en charge plus de deux niveaux de portée.

Les règles précises pour calculer l’état de *retour sécurisé* d’une expression et les règles régissant la légalité des expressions, suivez.

### <a name="ref-safe-to-escape"></a>Ref-sécurisé-à-Escape

La *référence de référence* est une portée, englobant une expression lvalue, à laquelle il est possible de sécuriser une référence à la lvalue pour échapper à. Si cette étendue est la méthode entière, nous disons qu’une référence à la lvalue peut être *retournée en toute sécurité* à partir de la méthode.

### <a name="safe-to-escape"></a>sans risque

La *sécurité de l’échappement* est une étendue, englobant une expression, à laquelle la valeur doit être échappée en toute sécurité. Si cette portée est la méthode entière, nous disons qu’une valeur peut être *retournée en toute sécurité* à partir de la méthode.

Une expression dont le type n’est pas un type `ref struct` est *sécurisée pour retourner* la totalité de la méthode englobante. Dans le cas contraire, nous faisons référence aux règles ci-dessous.

#### <a name="parameters"></a>Paramètres

Une lvalue désignant un paramètre formel est *ref-Safe-to-Escape* (par référence) comme suit :
- Si le paramètre est un paramètre `ref`, `out`ou `in`, il est *ref-Safe-to-Escape* de la méthode entière (par exemple, par une instruction `return ref`). dispose
- Si le paramètre est le paramètre `this` d’un type struct, il est de type *ref-Safe* pour la portée de niveau supérieur de la méthode (mais pas de la totalité de la méthode elle-même); [Exemple](#struct-this-escape)
- Dans le cas contraire, le paramètre est un paramètre de valeur, et il est conforme à la méthode de *référence* pour la portée de niveau supérieur de la méthode (mais pas à partir de la méthode elle-même).

Une expression qui est une valeur Rvalue désignant l’utilisation d’un paramètre formel est la sécurité de l' *échappement* (par valeur) de la méthode entière (par exemple, par une instruction `return`). Cela s’applique également au paramètre `this`.

#### <a name="locals"></a>Variables locales

Une lvalue désignant une variable locale est *ref-Safe-to-Escape* (par référence) comme suit :
- Si la variable est une variable `ref`, son « *ref-Safe-to-Escape* » est extrait de l’expression de *référence sécurisée à l’échappement* de son expression d’initialisation ; dispose
- La variable est de *référence (Ref-safe) pour* la portée dans laquelle elle a été déclarée.

Une expression qui est une rvalue désignant l’utilisation d’une variable locale est une valeur *d’échappement sécurisée* (par valeur) comme suit :
- Toutefois, la règle générale ci-dessus, une variable locale dont le type n’est pas un type `ref struct` est *sécurisée pour retourner* la totalité de la méthode englobante.
- Si la variable est une variable d’itération d’une boucle `foreach`, la portée *sécurisée* de la variable est la même que *celle de l’expression de la* boucle `foreach`.
- Une valeur locale de `ref struct` type et non initialisée au point de déclaration est *sécurisée à retourner* à partir de l’intégralité de la méthode englobante.
- Dans le cas contraire, le type de la variable est un type `ref struct` et la déclaration de la variable requiert un initialiseur. La portée de l' *échappement sécurisé* de la variable est la même que celle *de son* initialiseur.

#### <a name="field-reference"></a>Référence du champ

Une lvalue désignant une référence à un champ, `e.F`, est de référence conforme à la *séquence d’échappement* (par référence) comme suit :
- Si `e` est d’un type référence, il s’agit d’un type de référence ( *ref-Safe-to-Escape* ) de la méthode entière ; dispose
- Si `e` est d’un type valeur, son « *ref-Safe-to-Escape* » est extrait de l’espace de `e`de type *Safe-to-Escape* .

Rvalue désignant une référence à un champ, `e.F`, a une portée *sécurisée* qui est la même que la *valeur d’échappement sécurisée* de `e`.

#### <a name="operators-including-"></a>Opérateurs incluant `?:`

L’application d’un opérateur défini par l’utilisateur est traitée comme un appel de méthode.

Pour un opérateur qui produit une rvalue, telle que `e1 + e2` ou `c ? e1 : e2`, le *niveau de sécurité d’échappement* du résultat est la portée la plus étroite parmi les opérandes d' *échappement* de l’opérateur.  Par conséquent, dans le cas d’un opérateur unaire qui produit une rvalue, telle que `+e`, la sécurité de l' *échappement* du résultat est la *sécurité* de l’opérande.

Pour un opérateur qui produit une lvalue, telle que `c ? ref e1 : ref e2`
- le *ref-Safe-to-Escape* du résultat est l’étendue la plus étroite parmi les opérandes de l’opérateur de *référence sécurisée* .
- l' *échappement sécurisé* des opérandes doit s’accorder et c’est la sécurité de l' *échappement* de la lvalue résultante.

#### <a name="method-invocation"></a>Appel de méthode

Une lvalue résultant d’un appel de méthode de retour de référence `e1.M(e2, ...)` est *ref-Safe pour échapper* la plus petite des étendues suivantes :
- La totalité de la méthode englobante
- les expressions de référence de tous les arguments `ref` et `out` ( *à* l’exclusion du récepteur)
- Pour chaque paramètre `in` de la méthode, s’il existe une expression correspondante qui est une lvalue, son *ref-Safe-to-Escape*, sinon la portée englobante la plus proche
- la *sécurité* de l’échappement de toutes les expressions d’arguments (y compris le récepteur)

> Remarque : la dernière puce est nécessaire pour gérer le code, par exemple
> ```csharp
> var sp = new Span(...)
> return ref sp[0];
> ```
> or
> ```csharp
> return ref M(sp, 0);
> ```

Une rvalue qui résulte d’un appel de méthode `e1.M(e2, ...)` est *sécurisée pour échapper* de la plus petite des étendues suivantes :
- La totalité de la méthode englobante
- la *sécurité* de l’échappement de toutes les expressions d’arguments (y compris le récepteur)

#### <a name="an-rvalue"></a>Rvalue
Une rvalue est de *référence pour la sécurité* de la portée la plus proche. Cela se produit par exemple dans un appel tel que `M(ref d.Length)` où `d` est de type `dynamic`. Elle est également cohérente avec (et peut-être subsumes) notre gestion des arguments correspondant aux paramètres de `in`. *

#### <a name="property-invocations"></a>Appels de propriété

Un appel de propriété (`get` ou `set`) traité comme un appel de méthode de la méthode sous-jacente par les règles ci-dessus.

#### `stackalloc`

Une expression stackalloc est une rvalue qui est *sécurisée pour échapper* à la portée de niveau supérieur de la méthode (mais pas de la totalité de la méthode elle-même).

#### <a name="constructor-invocations"></a>Appels de constructeur

Une expression `new` qui appelle un constructeur obéit aux mêmes règles qu’un appel de méthode qui est considéré pour retourner le type en cours de construction.

En outre, la *sécurité* de l’échappement n’est pas plus étendue que la plus petite *de l’ensemble* des arguments/opérandes des expressions d’initialiseur d’objet, de manière récursive, si l’initialiseur est présent. 

#### <a name="span-constructor"></a>Span, constructeur
Le langage s’appuie sur `Span<T>` ne possédant pas de constructeur de la forme suivante :

```csharp
void Example(ref int x)
{
    // Create a span of length one
    var span = new Span<int>(ref x); 
}
```

Un constructeur de ce type rend `Span<T>` qui sont utilisés comme champs et ne peuvent pas être différenciés d’un champ `ref`. Les règles de sécurité décrites dans ce document dépendent de `ref` champs qui ne sont pas une C#construction valide dans, ou .net.

#### <a name="default-expressions"></a>Expressions `default`

Une expression `default` est *sécurisée pour échapper* de l’intégralité de la méthode englobante.

## <a name="language-constraints"></a>Contraintes de langue

Nous souhaitons garantir qu’aucune variable locale `ref`, et aucune variable de type `ref struct`, ne fasse référence à la mémoire de Stack ou aux variables qui ne sont plus actives. Nous avons donc les contraintes de langue suivantes :

- Ni un paramètre ref, ni un local de référence, ni un paramètre ou un local d’un type `ref struct` peuvent être levés dans une fonction lambda ou locale.

- Aucun paramètre ref ou paramètre d’un type `ref struct` ne peut être un argument sur une méthode Iterator ou une méthode `async`.

- Ni une variable locale REF, ni une variable locale d’un type `ref struct` peuvent se trouver dans la portée au point d’une instruction `yield return` ou d’une expression `await`.

- Un type de `ref struct` ne peut pas être utilisé comme argument de type ou comme type d’élément dans un type de Tuple.

- Un type de `ref struct` ne peut pas être le type déclaré d’un champ, à ceci près qu’il peut s’agir du type déclaré d’un champ d’instance d’un autre `ref struct`.

- Un type de `ref struct` ne peut pas être le type d’élément d’un tableau.

- Une valeur d’un type de `ref struct` ne peut pas être convertie :
  - Il n’y a aucune conversion d’un type de `ref struct` au type `object` ou au type `System.ValueType`.
  - Un type de `ref struct` ne peut pas être déclaré pour implémenter une interface
  - Aucune méthode d’instance déclarée dans `object` ou dans `System.ValueType` mais non substituée dans un type de `ref struct` peut être appelée avec un récepteur de ce type de `ref struct`.
  - Aucune méthode d’instance d’un type de `ref struct` ne peut être capturée par la conversion de méthode en un type délégué.

- Pour une réassignation de référence `ref e1 = ref e2`, le niveau de sécurité de la *référence à l’échappement* de l' `e2` doit être au moins aussi vaste que la portée de la *référence* de `e1`.

- Dans le cas d’une instruction de retour de référence `return ref e1`, la référence de la méthode Ref- *Safe à Escape* de `e1` *doit être de* la même façon que la méthode entière. (TODO : nous avons également besoin d’une règle qui `e1` doit être *sécurisée pour l’échappement* de la méthode entière, ou est-ce redondant ?)

- Pour une instruction return `return e1`, la sécurité de l' *échappement* de `e1` doit être *sécurisée pour* la totalité de la méthode.

- Pour une assignation `e1 = e2`, si le type de `e1` est un type de `ref struct`, l' *échappement* de `e2` doit être au moins aussi vaste que l’étendue comme étant la sécurité d' *échappement* de `e1`.

- Dans le cas d’un appel de méthode s’il existe un argument `ref` ou `out` d’un type `ref struct` (y compris le récepteur), avec une *valeur d’échappement sans risque* , aucun argument (y compris le récepteur) peut avoir une *valeur d’échappement* plus restreinte que E1. [Exemple](#method-arguments-must-match)

- Une fonction locale ou une fonction anonyme ne peut pas faire référence à un paramètre local ou de `ref struct` de type déclaré dans une portée englobante.

> ***Problème d’ouverture :*** Nous avons besoin d’une règle qui nous permette de générer une erreur en cas de dépassement d’une valeur de pile d’un type de `ref struct` au niveau d’une expression await, par exemple dans le code
> ```csharp
> Foo(new Span<int>(...), await e2);
> ```

## <a name="explanations"></a>Descriptions
Ces explications et exemples aident à expliquer la raison pour laquelle de nombreuses règles de sécurité ci-dessus existent

### <a name="method-arguments-must-match"></a>Les arguments de méthode doivent correspondre
Lors de l’appel d’une méthode contenant un `out`, `ref` paramètre qui est un `ref struct`, y compris le récepteur, alors tous les `ref struct` doivent avoir la même durée de vie. Cela est nécessaire, C# car doit prendre toutes les décisions concernant la sécurité de la durée de vie en fonction des informations disponibles dans la signature de la méthode et de la durée de vie des valeurs sur le site d’appel. 

Lorsqu’il y a `ref` paramètres qui sont `ref struct`, il y a éventualité qu’ils peuvent échanger autour de leur contenu. Par conséquent, sur le site d’appel, nous devons nous assurer que tous ces échanges **potentiels** sont compatibles. Si la langue ne s’est pas mise en application, elle autorisera un code incorrect comme celui-ci.

```csharp
void M1(ref Span<int> s1)
{
    Span<int> s2 = stackalloc int[1];
    Swap(ref s1, ref s2);
}

void Swap(ref Span<int> x, ref int Span<int> y)
{
    // This will effectively assign the stackalloc to the s1 parameter and allow it
    // to escape to the caller of M1
    ref x = ref y; 
}
```

La restriction sur le récepteur est nécessaire car, si aucun de ses contenus n’est de niveau « Ref-Safe », il peut stocker les valeurs fournies. Cela signifie que les durées de vie ne correspondent pas, vous pouvez créer un trou de sécurité de type de la façon suivante :

```csharp
ref struct S
{
    public Span<int> Span;

    public void Set(Span<int> span)
    {
        Span = span;
    }
}

void Broken(ref S s)
{
    Span<int> span = stackalloc int[1];

    // The result of a stackalloc is now stored in s.Span and escaped to the caller
    // of Broken
    s.Set(span); 
}
```

### <a name="struct-this-escape"></a>Structurer ce caractère d’échappement
Lorsqu’il s’agit d’étendre les règles de sécurité, la valeur `this` dans un membre d’instance est modélisée en tant que paramètre pour le membre. À présent, pour un `struct` le type de `this` est `ref S` où dans un `class` il est simplement `S` (pour les membres d’un `class / struct` nommé S). 

Pourtant `this` a des règles d’échappement différentes des autres paramètres de `ref`. Plus précisément, il n’est pas de référence pour la sécurité de l’échappement, alors que d’autres paramètres sont :

```csharp
ref struct S
{ 
    int Field;

    // Illegal because this isn't safe to escape as ref
    ref int Get() => ref Field;

    // Legal
    ref int GetParam(ref int p) => ref p;
}
```

La raison de cette restriction a en fait peu de choses à faire avec `struct` appel de membre. Il existe des règles qui doivent être traitées en ce qui concerne l’appel de membre sur `struct` membres où le récepteur est un rvalue. Mais cela est très proche. 

La raison de cette restriction est en fait l’appel de l’interface. En particulier, il s’agit de savoir si l’exemple suivant doit ou non être compilé ;

```csharp
interface I1
{
    ref int Get();
}

ref int Use<T>(T p)
    where T : I1
{
    return ref p.Get();
}
```

Considérez le cas où `T` est instancié en tant que `struct`. Si le paramètre `this` est de type REF-Safe-to-Escape, le retour de `p.Get` peut pointer vers la pile (en particulier, il peut s’agir d’un champ à l’intérieur du type instancié de `T`). Cela signifie que le langage n’a pas pu permettre à cet exemple de se compiler, car il pourrait retourner un `ref` à un emplacement de pile. En revanche, si `this` n’est pas de référence pour la sécurité de l’échappement, `p.Get` ne peut pas faire référence à la pile et, par conséquent, il est possible de retourner en toute sécurité. 

C’est la raison pour laquelle l’échappement des `this` dans un `struct` est tout à fait sur les interfaces. Il est tout à fait possible de travailler, mais il a un compromis. La conception a finalement été mise en place pour rendre les interfaces plus flexibles. 

Toutefois, nous pouvons l’assouplir à l’avenir. 

## <a name="future-considerations"></a>Éléments futurs à prendre en considération

### <a name="length-one-spant-over-ref-values"></a>Longueur une étendue\<T > sur les valeurs Ref
Bien qu’il ne soit pas légal aujourd’hui, il y a des cas où la création d’une instance de longueur un `Span<T>` sur une valeur serait avantageuse :

```csharp
void RefExample()
{
    int x = ...;

    // Today creating a length one Span<int> requires a stackalloc and a new 
    // local
    Span<int> span1 = stackalloc [] { x };
    Use(span1);
    x = span1[0]; 

    // Simpler to just allow length one span
    var span2 = new Span<int>(ref x);
    Use(span2);
}
```

Cette fonctionnalité est plus intéressante si nous remontons les restrictions sur les [mémoires tampons de taille fixe](https://github.com/dotnet/csharplang/blob/master/proposals/fixed-sized-buffers.md) , car cela permettrait à `Span<T>` instances d’une longueur encore plus longue. 

Si vous avez toujours besoin de descendre dans ce chemin, le langage peut le prendre en compte en veillant à ce que les instances de `Span<T>` soient uniquement dirigées vers le bas. Il n’était jamais possible d' *échapper* à l’étendue dans laquelle ils ont été créés. Cela permet de s’assurer que le langage n’a jamais eu à prendre en compte une `ref` valeur d’échappement dans une méthode par le biais d’une `ref struct` retour ou d’un champ de `ref struct`. Cela nécessitera probablement également des modifications supplémentaires pour reconnaître de tels constructeurs comme la capture d’un paramètre `ref` de cette manière.
