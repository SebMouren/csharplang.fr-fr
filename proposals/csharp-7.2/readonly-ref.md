---
ms.openlocfilehash: 39fb0aab5e8bb0d422f25fd2e92ab3d8256d3f59
ms.sourcegitcommit: b8f1103eb686c5d82e294837c9386d9b667da292
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "79485111"
---
# <a name="readonly-references"></a>Références en lecture seule

* [x] proposé
* [x] prototype
* [x] implémentation : démarré
* [] Spécification : non démarrée

## <a name="summary"></a>Résumé
[summary]: #summary

La fonctionnalité « références ReadOnly » est en fait un groupe de fonctionnalités qui tirent parti de l’efficacité du passage des variables par référence, mais sans exposer les données aux modifications :  
- paramètres `in`
- Retours `ref readonly`
- structs de `readonly`
- `ref`/méthodes d’extension `in`
- `ref readonly` variables locales
- `ref` des expressions conditionnelles

## <a name="passing-arguments-as-readonly-references"></a>Passage des arguments en tant que références ReadOnly.

Il existe déjà une proposition qui touche cette rubrique https://github.com/dotnet/roslyn/issues/115 comme un cas spécial de paramètres ReadOnly sans passer par de nombreux détails.
Ici, je veux simplement confirmer que l’idée n’est pas très nouvelle.

### <a name="motivation"></a>Motivation

Avant cette fonctionnalité C# , n’avait pas de moyen efficace d’exprimer un souhait de passer des variables de struct dans des appels de méthode à des fins ReadOnly sans intention de modifier. Le passage d’un argument par valeur normal implique une copie, ce qui ajoute des coûts inutiles.  Cela permet aux utilisateurs d’utiliser un argument par référence en passant et de s’appuyer sur des commentaires/une documentation pour indiquer que les données ne sont pas censées être mutées par l’appelé. Ce n’est pas une bonne solution pour de nombreuses raisons.  
Les exemples sont des opérateurs mathématiques à plusieurs vecteurs/matrices dans les bibliothèques de graphiques, comme [XNA](https://msdn.microsoft.com/library/bb194944.aspx) , dont les opérandes de référence sont purement en raison des considérations relatives aux performances. Il existe du code dans le compilateur Roslyn lui-même, qui utilise des structs pour éviter les allocations, puis les passe par référence afin d’éviter de copier les coûts.

### <a name="solution-in-parameters"></a>Solution (paramètres de`in`)

De la même façon que les paramètres `out`, `in` paramètres sont passés comme références managées avec des garanties supplémentaires de l’appelé.  
Contrairement à `out` paramètres qui _doivent_ être assignés par l’appelé avant toute autre utilisation, `in` paramètres ne peuvent pas être attribués par l’appelé.

Par conséquent `in` paramètres permettent l’efficacité du passage indirect des arguments sans exposer les arguments aux mutations par l’appelé.

### <a name="declaring-in-parameters"></a>Déclarer des paramètres `in`

`in` paramètres sont déclarés à l’aide du mot clé `in` en tant que modificateur dans la signature de paramètre.

Dans tous les cas, le paramètre `in` est traité comme une variable `readonly`. La plupart des restrictions sur l’utilisation des paramètres de `in` à l’intérieur de la méthode sont les mêmes que pour les champs de `readonly`.

> En effet, un paramètre `in` peut représenter un champ `readonly`. La similarité des restrictions n’est pas une coïncidence.

Par exemple, les champs d’un paramètre `in` qui a un type struct sont tous classés de manière récursive en tant que variables `readonly`.

```csharp
static Vector3 Add (in Vector3 v1, in Vector3 v2)
{
    // not OK!!
    v1 = default(Vector3);

    // not OK!!
    v1.X = 0;

    // not OK!!
    foo(ref v1.X);

    // OK
    return new Vector3(v1.X + v2.X, v1.Y + v2.Y, v1.Z + v2.Z);
}
```

- les paramètres de `in` sont autorisés partout où des paramètres ByVal ordinaires sont autorisés. Cela inclut les indexeurs, les opérateurs (y compris les conversions), les délégués, les expressions lambda, les fonctions locales.

> ```csharp
>  (in int x) => x                                                     // lambda expression  
>  TValue this[in TKey index];                                         // indexer
>  public static Vector3 operator +(in Vector3 x, in Vector3 y) => ... // operator
>  ```

- `in` n’est pas autorisée en combinaison avec `out` ou avec tout ce que `out` ne combine pas avec.

- Elle n’est pas autorisée à se surcharger sur `ref`/`out`/les différences `in`.

- Elle est autorisée à se surcharger sur les différences ordinaires ByVal et `in`.

- Dans le cadre de OHI (surcharge, masquage, implémentation), `in` se comporte de la même façon qu’un paramètre `out`.
Toutes les mêmes règles s’appliquent.
Par exemple, la méthode de substitution devra faire correspondre `in` paramètres avec `in` paramètres d’un type convertible en identité.

- À des fins de conversions de groupe délégué/lambda/méthode, `in` se comporte de la même façon qu’un paramètre `out`.
Les expressions lambda et les candidats de conversion de groupe de méthodes applicables doivent correspondre aux paramètres de `in` du délégué cible avec les paramètres `in` d’un type convertible en identité.

- Dans le cadre de la variance générique, les paramètres de `in` ne sont pas des variantes.

> Remarque : il n’y a aucun avertissement sur les paramètres de `in` qui ont des types de référence ou de Primitives.
Cela peut s’avérer inutile en général, mais dans certains cas, l’utilisateur doit/veulent passer des primitives comme `in`. Exemples : substitution d’une méthode générique comme `Method(in T param)` lorsque `T` a été substitué à `int`ou lorsque des méthodes telles que `Volatile.Read(in int location)`
>
> Il est concevable de disposer d’un analyseur qui vous avertit en cas d’utilisation inefficace des paramètres de `in`, mais les règles de cette analyse seraient trop approximatives pour faire partie d’une spécification de langage.

### <a name="use-of-in-at-call-sites-in-arguments"></a>Utilisation de `in` sur les sites d’appel. (arguments`in`)

Il existe deux façons de passer des arguments à `in` paramètres.

#### <a name="in-arguments-can-match-in-parameters"></a>`in` arguments peuvent correspondre `in` paramètres :

Un argument avec un modificateur de `in` sur le site d’appel peut faire correspondre `in` paramètres.

```csharp
int x = 1;

void M1<T>(in T x)
{
  // . . .
}

var x = M1(in x);  // in argument to a method

class D
{
    public string this[in Guid index];
}

D dictionary = . . . ;
var y = dictionary[in Guid.Empty]; // in argument to an indexer
```

- `in` argument doit être une LValue _lisible_ (*).
Exemple : `M1(in 42)` n’est pas valide

> (*) La notion de [LValue/RValue](https://en.wikipedia.org/wiki/Value_(computer_science)#lrvalue) varie selon les langages.  
Ici, par LValue, je entend une expression qui représente un emplacement qui peut être référencé directement.
Et RValue signifie une expression qui produit un résultat temporaire qui ne persiste pas de manière autonome.  

- En particulier, il est possible de passer des `readonly` des champs, des paramètres de `in` ou d’autres variables de `readonly` formelles en tant qu' `in` arguments.
Exemple : `dictionary[in Guid.Empty]` est légal. `Guid.Empty` est un champ readonly statique.

- `in` argument doit avoir un type d' _identité convertible_ en type du paramètre.
Exemple : `M1<object>(in Guid.Empty)` n’est pas valide. `Guid.Empty` n’est pas _convertible en identité_ pour `object`

La motivation des règles ci-dessus est que `in` arguments garantissent l' _alias_ de la variable d’argument. L’appelé reçoit toujours une référence directe au même emplacement que celui représenté par l’argument.

- dans les rares cas où `in` arguments doivent être débordés de pile en raison des expressions `await` utilisées comme opérandes du même appel, le comportement est le même qu’avec les arguments `out` et `ref`. Si la variable ne peut pas être détournée de manière référentielle, une erreur est signalée.

Exemples :
1. `M1(in staticField, await SomethingAsync())` est valide.
`staticField` est un champ statique accessible plusieurs fois sans effets secondaires observables. Par conséquent, l’ordre des effets secondaires et des exigences d’alias peut être fourni.
2. `M1(in RefReturningMethod(), await SomethingAsync())` génère une erreur.
`RefReturningMethod()` est une `ref` méthode de retour. Un appel de méthode peut avoir des effets secondaires observables. par conséquent, il doit être évalué avant l’opérande `SomethingAsync()`. Toutefois, le résultat de l’appel est une référence qui ne peut pas être conservée à travers le point de suspension `await` qui rend l’exigence de référence directe impossible.

> Remarque : les erreurs de dépassement de la pile sont considérées comme des limitations spécifiques à l’implémentation. Par conséquent, elles n’ont pas d’effet sur la résolution de surcharge ou l’inférence lambda.

#### <a name="ordinary-byval-arguments-can-match-in-parameters"></a>Les arguments ByVal ordinaires peuvent correspondre à `in` paramètres :

Les arguments normaux sans modificateurs peuvent correspondre à des paramètres de `in`. Dans ce cas, les arguments ont les mêmes contraintes assouplies qu’un argument ByVal ordinaire aurait.

La motivation de ce scénario est que `in` paramètres dans les API peuvent entraîner des désagréments pour l’utilisateur lorsque les arguments ne peuvent pas être passés en tant que référence directe-ex : littéraux, résultats calculés ou `await`Ed ou arguments qui ont des types plus spécifiques.  
Tous ces cas ont une solution triviale qui stocke la valeur d’argument dans un local temporaire de type approprié et transmet ce local en tant qu’argument `in`.  
Pour réduire le besoin d’un tel compilateur de code réutilisable, il est possible d’effectuer la même transformation, si nécessaire, lorsque `in` modificateur n’est pas présent sur le site d’appel.  

En outre, dans certains cas, tels que l’appel d’opérateurs ou les méthodes d’extension `in`, il n’existe pas de méthode syntaxique pour spécifier `in` du tout. Cela nécessite de spécifier le comportement des arguments ByVal ordinaires lorsqu’ils correspondent à `in` paramètres.

En particulier :

- Il est possible de passer RValues.
Dans ce cas, une référence à un temporaire est passée.
Exemple :
```csharp
Print("hello");      // not an error.

void Print<T>(in T x)
{
  //. . .
}
```

- les conversions implicites sont autorisées.

> Il s’agit en fait d’un cas particulier de passage d’une RValue  

Une référence à une valeur convertie temporaire est passée dans ce cas.
Exemple :
```csharp
Print<int>(Short.MaxValue)     // not an error.
```

- dans le cas d’un récepteur d’une méthode d’extension `in` (par opposition aux méthodes d’extension `ref`), RValues ou les _conversions_ implicites de cet argument sont autorisées.
Une référence à une valeur convertie temporaire est passée dans ce cas.
Exemple :
```csharp
public static IEnumerable<T> Concat<T>(in this (IEnumerable<T>, IEnumerable<T>) arg)  => . . .;

("aa", "bb").Concat<char>()    // not an error.
```
Vous trouverez plus d’informations sur les méthodes d’extension `ref`/`in` dans ce document.

- le débordement d’argument en raison des opérandes de `await` peut déborder « par valeur », si nécessaire.
Dans les scénarios où il n’est pas possible de fournir une référence directe à l’argument en raison d’une intervention `await` une copie de la valeur de l’argument est débordée à la place.  
Exemple :
```csharp
M1(RefReturningMethod(), await SomethingAsync())   // not an error.
```
Étant donné que le résultat d’un appel à effet secondaire est une référence qui ne peut pas être conservée entre `await` suspension, un temporaire contenant la valeur réelle sera conservé à la place (comme dans un cas de paramètre ByVal ordinaire).

#### <a name="omitted-optional-arguments"></a>Arguments facultatifs omis

Il est permis à un paramètre `in` de spécifier une valeur par défaut. Cela rend l’argument correspondant facultatif.

L’omission de l’argument facultatif sur le site d’appel permet de passer la valeur par défaut via un temporaire.

```csharp
Print("hello");      // not an error, same as
Print("hello", c: Color.Black);

void Print(string s, in Color c = Color.Black)
{
    // . . .
}
```

### <a name="aliasing-behavior-in-general"></a>Comportement d’alias en général

Tout comme les variables `ref` et `out`, les variables `in` sont des références/alias à des emplacements existants.

Alors que l’appelé n’est pas autorisé à y écrire, la lecture d’un paramètre `in` peut observer des valeurs différentes en tant qu’effet secondaire d’autres évaluations.

Exemple :

```C#
static Vector3 v = Vector3.UnitY;

static void Main()
{
    Test(v);
}

static void Test(in Vector3 v1)
{
    Debug.Assert(v1 == Vector3.UnitY);
    // changes v1 deterministically (no races required)
    ChangeV();
    Debug.Assert(v1 == Vector3.UnitX);
}

static void ChangeV()
{
    v = Vector3.UnitX;
}
```

### <a name="in-parameters-and-capturing-of-local-variables"></a>`in` paramètres et la capture de variables locales.  
Dans le cadre de la capture lambda/Async `in` paramètres se comportent de la même façon que les paramètres `out` et `ref`.

- les paramètres de `in` ne peuvent pas être capturés dans une fermeture
- les paramètres de `in` ne sont pas autorisés dans les méthodes Iterator
- les paramètres de `in` ne sont pas autorisés dans les méthodes Async

### <a name="temporary-variables"></a>Variables temporaires.  
Certaines utilisations de `in` le passage de paramètres peuvent nécessiter l’utilisation indirecte d’une variable locale temporaire :  
- les arguments `in` sont toujours passés comme alias directs lorsque le site d’appel utilise `in`. Le type temporaire n’est jamais utilisé dans ce cas.
- les arguments de `in` ne doivent pas obligatoirement être des alias directs lorsque le site d’appel n’utilise pas `in`. Lorsque l’argument n’est pas une LValue, un temporaire peut être utilisé.
- `in` paramètre peut avoir une valeur par défaut. Lorsque l’argument correspondant est omis sur le site d’appel, la valeur par défaut est transmise via un temporaire.
- `in` arguments peuvent avoir des conversions implicites, y compris celles qui ne préservent pas l’identité. Un temporaire est utilisé dans ces cas.
- les destinataires des appels struct ordinaires peuvent ne pas être inscriptibles LValues (**cas existant !** ). Un temporaire est utilisé dans ces cas.

La durée de vie de l’argument de temporisation correspond à la portée englobante la plus proche du site d’appel.

La durée de vie formelle des variables temporaires est sémantiquement significative dans les scénarios impliquant une analyse d’échappement des variables retournées par référence.

### <a name="metadata-representation-of-in-parameters"></a>Représentation de métadonnées des paramètres de `in`.
Lorsque `System.Runtime.CompilerServices.IsReadOnlyAttribute` est appliqué à un paramètre ByRef, cela signifie que le paramètre est un paramètre `in`.

En outre, si la méthode est *abstraite* ou *virtuelle*, la signature de ces paramètres (et seulement ces paramètres) doit avoir `modreq[System.Runtime.InteropServices.InAttribute]`.

**Motivation**: cela permet de s’assurer que, dans le cas d’une méthode, la substitution/la mise en œuvre des paramètres de `in` correspondent.

Les mêmes exigences s’appliquent aux méthodes `Invoke` dans les délégués.

**Motivation**: cela permet de s’assurer que les compilateurs existants ne peuvent pas ignorer simplement `readonly` lors de la création ou de l’assignation de délégués.

## <a name="returning-by-readonly-reference"></a>Retour par référence ReadOnly.

### <a name="motivation"></a>Motivation
La motivation de cette sous-fonctionnalité est à peu près symétrique pour les raisons des paramètres `in`, à l’exception de la copie, mais du côté du retour. Avant cette fonctionnalité, une méthode ou un indexeur avait deux options : 1) retournent par référence et sont exposées à des mutations possibles ou 2) retournent par valeur, ce qui entraîne la copie.

### <a name="solution-ref-readonly-returns"></a>Solution (`ref readonly` retourne)  
La fonctionnalité permet à un membre de retourner des variables par référence sans les exposer à des mutations.

### <a name="declaring-ref-readonly-returning-members"></a>Déclaration de `ref readonly` retournant des membres

Une combinaison de modificateurs `ref readonly` sur la signature de retour est utilisée pour indiquer que le membre retourne une référence en lecture seule.

Dans tous les cas, un membre `ref readonly` est traité comme une variable `readonly`, de la même façon que des champs `readonly` et `in` paramètres.

Par exemple, les champs de `ref readonly` membre qui a un type struct sont tous classés de manière récursive en tant que variables `readonly`. -Il est autorisé à les passer en tant qu’arguments `in`, mais pas en tant qu’arguments `ref` ou `out`.

```csharp
ref readonly Guid Method1()
{
}

Method2(in Method1()); // valid. Can pass as `in` argument.

Method3(ref Method1()); // not valid. Cannot pass as `ref` argument
```

- `ref readonly` retours sont autorisés dans les mêmes emplacements que les retours `ref` sont autorisés.
Cela comprend les indexeurs, les délégués, les expressions lambda, les fonctions locales.

- Elle n’est pas autorisée à se surcharger sur `ref`/`ref readonly`/différences.

- Elle est autorisée à se surcharger sur les différences de retour ByVal et `ref readonly`.

- Dans le cadre de OHI (surcharge, masquage, implémentation), `ref readonly` est similaire, mais distinct de `ref`.
Par exemple, une méthode qui remplace `ref readonly` une, doit elle-même être `ref readonly` et avoir un type convertible en identité.

- À des fins de conversions de groupe délégué/lambda/méthode, `ref readonly` est similaire, mais distinct de `ref`.
Les expressions lambda et les candidats de conversion de groupe de méthodes applicables doivent correspondre `ref readonly` retour du délégué cible avec `ref readonly` retour du type convertible par l’identité.

- Dans le cadre de la variance générique, `ref readonly` retourne sont non variants.

> Remarque : il n’y a aucun avertissement sur `ref readonly` retourne qui ont des types de référence ou de Primitives.
Cela peut s’avérer inutile en général, mais dans certains cas, l’utilisateur doit/veulent passer des primitives comme `in`. Exemples : substitution d’une méthode générique comme `ref readonly T Method()` lorsque `T` a été substitué à `int`.
>
>Il est concevable de disposer d’un analyseur qui avertit les cas d’utilisation inefficace de `ref readonly` retourne, mais les règles de cette analyse seraient trop approximatives pour faire partie d’une spécification de langage.

### <a name="returning-from-ref-readonly-members"></a>Retourner à partir de `ref readonly` membres
Dans le corps de la méthode, la syntaxe est la même qu’avec les retours Ref standard. La `readonly` est déduite de la méthode conteneur.

La motivation est que `return ref readonly <expression>` est inutile et n’autorise que les incompatibilités sur la partie `readonly` qui aboutirait toujours à des erreurs.
Toutefois, le `ref` est requis pour des besoins de cohérence avec d’autres scénarios où un événement est passé via un alias strict et par valeur.

> Contrairement au cas avec `in` paramètres, `ref readonly` ne retourne jamais par le biais d’une copie locale. Considérer que la copie cesserait d’exister immédiatement lors du retour de cette pratique serait inutile et dangereuse. Par conséquent `ref readonly` retours sont toujours des références directes.

Exemple :

```csharp
struct ImmutableArray<T>
{
    private readonly T[] array;

    public ref readonly T ItemRef(int i)
    {
        // returning a readonly reference to an array element
        return ref this.array[i];
    }
}

```

- Un argument de `return ref` doit être une LValue (**règle existante**)
- Un argument de `return ref` doit être « Safe to return » (**règle existante**)
- Dans un membre `ref readonly` un argument de `return ref` ne doit _pas être accessible en écriture_ .
Par exemple, ce membre peut retourner un champ ReadOnly ou l’un de ses paramètres de `in`.

### <a name="safe-to-return-rules"></a>Règles de retour sécurisé.
Les règles normal de retour sécurisé pour les références s’appliquent également aux références ReadOnly.

Notez qu’un `ref readonly` peut être obtenu à partir d’une `ref` locale/paramètre/retour standard, mais pas l’inverse. Sinon, la sécurité des retours de `ref readonly` est déduite de la même façon que pour les retours de `ref` normaux.

En considérant que RValues peut être passé comme paramètre `in` et retourné comme `ref readonly` nous avons besoin d’une règle supplémentaire- **RValues ne sont pas sécurisés par référence**.

> Tenez compte de la situation où une RValue est passée à un paramètre `in` via une copie, puis retournée sous la forme d’un `ref readonly`. Dans le contexte de l’appelant, le résultat de cet appel est une référence aux données locales et, par conséquent, il n’est pas possible de retourner.
> Une fois que les RValues ne peuvent pas être renvoyés, la règle existante `#6` gère déjà ce cas.

Exemple :
```csharp
ref readonly Vector3 Test1()
{
    // can pass an RValue as "in" (via a temp copy)
    // but the result is not safe to return
    // because the RValue argument was not safe to return by reference
    return ref Test2(default(Vector3));
}

ref readonly Vector3 Test2(in Vector3 r)
{
    // this is ok, r is returnable
    return ref r;
}
```

Règles de `safe to return` mises à jour :

1.  **les références aux variables sur le tas peuvent être renvoyées en toute sécurité**
2.  **les paramètres ref/in sont en toute sécurité** retournés
`in` paramètres ne peuvent être retournés naturellement que en lecture seule.
3.  les **paramètres out peuvent être renvoyés en toute sécurité** (mais doivent être définitivement affectés, comme c’est déjà le cas aujourd’hui)
4.  **les champs de struct d’instance peuvent être renvoyés en toute sécurité tant que le récepteur peut être retourné en toute sécurité**
5.  **'This’ne peut pas être retourné en toute sécurité à partir de membres struct**
6.  **une référence, retournée à partir d’une autre méthode, peut être retournée en toute sécurité si toutes les références/sorties passées à cette méthode en tant que paramètres formels ont été** retournées en toute sécurité.
*plus particulièrement, il est inutile de retourner le récepteur en toute sécurité, qu’il s’agisse d’un struct, d’une classe ou d’un type en tant que paramètre de type générique.*
7.  **Les rvalues ne peuvent pas être retournés par référence.** 
*spécifiquement rvalues sont sécurisés pour passer comme paramètres in.*

> Remarque : il existe des règles supplémentaires concernant la sécurité des retours qui entrent en jeu lorsque les types de type REF et les réaffectations de référence sont impliquées.
> Les règles s’appliquent de manière équitable aux membres `ref` et `ref readonly` et ne sont donc pas mentionnées ici.

### <a name="aliasing-behavior"></a>Comportement de l’alias.
les membres de `ref readonly` fournissent le même comportement d’alias que les membres `ref` ordinaires (sauf en lecture seule).
Par conséquent, à des fins de capture dans des expressions lambda, async, itérateurs, empilement, etc... les mêmes restrictions s’appliquent. celles. en raison de l’impossibilité de capturer les références réelles et en raison de la nature des effets secondaires de l’évaluation des membres, ces scénarios ne sont pas autorisés.

> Il est autorisé et obligatoire de faire une copie lorsque `ref readonly` retour est un récepteur de méthodes struct régulières, qui acceptent `this` comme référence inscriptible ordinaire. Historiquement dans tous les cas où ces appels sont appliqués à une variable ReadOnly, une copie locale est effectuée.

### <a name="metadata-representation"></a>Représentation de métadonnées.
Lorsque `System.Runtime.CompilerServices.IsReadOnlyAttribute` est appliqué au retour d’une méthode de retour par ByRef, cela signifie que la méthode retourne une référence en lecture seule.

En outre, la signature de résultats de ces méthodes (et uniquement ces méthodes) doit avoir `modreq[System.Runtime.CompilerServices.IsReadOnlyAttribute]`.

**Motivation**: cela permet de s’assurer que les compilateurs existants ne peuvent pas ignorer simplement `readonly` lors de l’appel de méthodes avec `ref readonly` retourne

## <a name="readonly-structs"></a>Structs en lecture seule
En bref, fonctionnalité qui rend `this` paramètre de tous les membres d’instance d’un struct, à l’exception des constructeurs, un paramètre `in`.

### <a name="motivation"></a>Motivation
Le compilateur doit supposer que tout appel de méthode sur une instance de struct peut modifier l’instance. En effet, une référence accessible en écriture est transmise à la méthode en tant que paramètre `this` et active entièrement ce comportement. Pour autoriser ces appels sur les variables de `readonly`, les appels sont appliqués aux copies temporaires. Cela peut ne pas être intuitif et oblige parfois les utilisateurs à abandonner `readonly` pour des raisons de performances.  
Exemple : https://codeblog.jonskeet.uk/2014/07/16/micro-optimization-the-surprising-inefficiency-of-readonly-fields/

Après l’ajout de la prise en charge de `in` paramètres et `ref readonly` retourne le problème de la copie défensive est plus grave puisque les variables ReadOnly deviennent plus courantes.

### <a name="solution"></a>Solution
Autorisez `readonly` modificateur sur les déclarations de struct, ce qui entraînerait le traitement des `this` comme `in` paramètre sur toutes les méthodes d’instance de struct, à l’exception des constructeurs.

```csharp
static void Test(in Vector3 v1)
{
    // no need to make a copy of v1 since Vector3 is a readonly struct
    System.Console.WriteLine(v1.ToString());
}

readonly struct Vector3
{
    . . .

    public override string ToString()
    {
        // not OK!!  `this` is an `in` parameter
        foo(ref this.X);

        // OK
        return $"X: {X}, Y: {Y}, Z: {Z}";
    }
}
```

### <a name="restrictions-on-members-of-readonly-struct"></a>Restrictions sur les membres d’un struct ReadOnly
- Les champs d’instance d’un struct ReadOnly doivent être en lecture seule.  
**Motivation :** peut uniquement être écrit en externe, mais pas par le biais de membres.
- Les propriétés autoproperties d’un struct ReadOnly doivent être en lecture seule.  
**Motivation :** conséquence de la restriction sur les champs d’instance.
- Un struct ReadOnly ne peut pas déclarer des événements de type champ.  
**Motivation :** conséquence de la restriction sur les champs d’instance.

### <a name="metadata-representation"></a>Représentation de métadonnées.
Lorsque `System.Runtime.CompilerServices.IsReadOnlyAttribute` est appliqué à un type valeur, cela signifie que le type est un `readonly struct`.

En particulier :
-  L’identité du type de `IsReadOnlyAttribute` n’a pas d’importance. En fait, il peut être incorporé par le compilateur dans l’assembly conteneur, si nécessaire.

## <a name="refin-extension-methods"></a>`ref`/méthodes d’extension `in`
Il existe en fait une proposition existante (https://github.com/dotnet/roslyn/issues/165) et le prototype correspondant (https://github.com/dotnet/roslyn/pull/15650).
Je veux simplement confirmer que cette idée n’est pas entièrement nouvelle. C’est toutefois le cas ici, car `ref readonly` supprime de façon élégante le problème le plus lié à ces méthodes, que faire avec les récepteurs RValue.

L’idée générale est d’autoriser les méthodes d’extension à prendre le paramètre `this` par référence, à condition que le type soit connu comme étant un type struct.

```csharp
public static void Extension(ref this Guid self)
{
    // do something
}
```

Les raisons justifiant l’écriture de ces méthodes d’extension sont principalement les suivantes :  
1.  Éviter la copie lorsque le récepteur est un grand struct
2.  Autoriser les méthodes d’extension mutantes sur des structs

Les raisons pour lesquelles nous ne souhaitons pas autoriser cela sur les classes  
1.  Elle est très limitée.
2.  Cela entraînerait un arrêt invariant à long terme qu’un appel de méthode ne peut pas désactiver le destinataire non`null` pour qu’il devienne `null` après l’appel.
> En fait, actuellement, une variable non-`null` ne peut pas être `null`, sauf si elle est _explicitement_ assignée ou transmise par `ref` ou `out`.
> Cela contribue beaucoup à la lisibilité ou à d’autres formes de l’analyse « peut-il s’agir d’une valeur NULL ici ».
3.  Il serait difficile de procéder au rapprochement avec la sémantique « évaluer une fois » des accès conditionnels null.
Exemple : `obj.stringField?.RefExtension(...)`-doit capturer une copie de `stringField` pour rendre le contrôle null explicite, mais les assignations à `this` à l’intérieur de RefExtension ne sont pas reflétées dans le champ.

La possibilité de déclarer des méthodes d’extension sur des **structs** qui acceptent le premier argument par référence était une requête de longue durée. L’une des considérations de blocage était « que se passe-t-il si le récepteur n’est pas une LValue ? ».

- L’une des méthodes d’extension peut également être appelée comme une méthode statique (parfois, il s’agit de la seule façon de résoudre l’ambiguïté). Cela impliquerait que les récepteurs RValue ne soient pas autorisés.
- En revanche, il existe une pratique qui consiste à effectuer un appel sur une copie dans des situations similaires lorsque des méthodes d’instance de struct sont impliquées.

La raison pour laquelle la « copie implicite » existe, c’est parce que la majorité des méthodes struct ne modifient pas réellement la structure alors qu’elle ne peut pas l’indiquer. Par conséquent, la solution la plus pratique consistait simplement à effectuer l’appel sur une copie, mais cette pratique est connue pour nuire aux performances et provoquer des bogues.

Désormais, avec la disponibilité des paramètres de `in`, il est possible qu’une extension signale l’intention. Par conséquent, le énigme peut être résolu en exigeant que les extensions `ref` soient appelées avec des récepteurs accessibles en écriture, tandis que les extensions `in` autorisent la copie implicite si nécessaire.

```csharp
// this can be called on either RValue or an LValue
public static void Reader(in this Guid self)
{
    // do something nonmutating.
    WriteLine(self == default(Guid));
}

// this can be called only on an LValue
public static void Mutator(ref this Guid self)
{
    // can mutate self
    self = new Guid();
}
```

### <a name="in-extensions-and-generics"></a>les génériques et les extensions de `in`.
L’objectif des méthodes d’extension `ref` consiste à muter le récepteur directement ou en appelant des membres mutants. Par conséquent, `ref this T` extensions sont autorisées tant que `T` est imposé comme un struct.

En revanche `in` méthodes d’extension existent spécifiquement pour réduire la copie implicite. Toutefois, toute utilisation d’un paramètre de `in T` doit être effectuée par le biais d’un membre d’interface. Étant donné que tous les membres d’interface sont considérés comme mutants, toute utilisation de ce type requiert une copie. -Au lieu de réduire la copie, l’effet serait le contraire. Par conséquent, `in this T` n’est pas autorisé quand `T` est un paramètre de type générique indépendamment des contraintes.

### <a name="valid-kinds-of-extension-methods-recap"></a>Types valides de méthodes d’extension (récapitulatif) :
Les formes suivantes de `this` déclaration dans une méthode d’extension sont désormais autorisées :
1) `this T arg`-extension ByVal régulière. (**cas existant**)
- T peut être n’importe quel type, y compris des types référence ou des paramètres de type.
L’instance sera la même variable après l’appel.
Autorise les conversions implicites de ce genre de _conversion d’arguments_ .
Peut être appelé sur RValues.

- `in this T self` - extension de `in`.
T doit être un type struct réel.
L’instance sera la même variable après l’appel.
Autorise les conversions implicites de ce genre de _conversion d’arguments_ .
Peut être appelé sur RValues (peut être appelé sur un temp si nécessaire).

- `ref this T self` - extension de `ref`.
T doit être un type struct ou un paramètre de type générique qui est imposé comme struct.
L’instance peut être écrite par l’appel.
Autorise uniquement les conversions d’identité.
Doit être appelé sur une LValue accessible en écriture. (jamais appelé via un temp).

## <a name="readonly-ref-locals"></a>Variables locales de référence en lecture seule.

### <a name="motivation"></a>Raisons.
Une fois `ref readonly` membres introduits, il était évident de l’utilisation qu’ils devaient être associés à un type de local approprié. L’évaluation d’un membre peut produire ou observer des effets secondaires. par conséquent, si le résultat doit être utilisé plusieurs fois, il doit être stocké. Les variables locales `ref` ordinaires ne sont pas là pour vous, car elles ne peuvent pas se voir assigner une référence `readonly`.   

### <a name="solution"></a>Elle.
Autorisez la déclaration de `ref readonly` variables locales. Il s’agit d’un nouveau type de `ref` variables locales qui ne sont pas accessibles en écriture. Par conséquent `ref readonly` les variables locales peuvent accepter des références à des variables en lecture seule sans exposer ces variables aux écritures.

### <a name="declaring-and-using-ref-readonly-locals"></a>Déclaration et utilisation de `ref readonly` variables locales.

La syntaxe de ces variables locales utilise des modificateurs de `ref readonly` au niveau du site de déclaration (dans cet ordre spécifique). De la même façon que les variables locales de `ref` ordinaires, `ref readonly` variables locales doivent être de type REF au niveau de la déclaration. Contrairement aux variables locales de `ref` standard, `ref readonly` variables locales peuvent faire référence à `readonly` LValues comme les paramètres `in`, les champs `readonly`, les méthodes `ref readonly`.

Dans tous les cas, un `ref readonly` local est traité comme une variable de `readonly`. La plupart des restrictions relatives à l’utilisation sont les mêmes que pour les champs de `readonly` ou les paramètres de `in`.

Par exemple, les champs d’un paramètre `in` qui a un type struct sont tous classés de manière récursive en tant que variables `readonly`.   

```csharp
static readonly ref Vector3 M1() => . . .

static readonly ref Vector3 M1_Trace()
{
    // OK
    ref readonly var r1 = ref M1();

    // Not valid. Need an LValue
    ref readonly Vector3 r2 = ref default(Vector3);

    // Not valid. r1 is readonly.
    Mutate(ref r1);

    // OK.
    Print(in r1);

    // OK.
    return ref r1;
}
```

### <a name="restrictions-on-use-of-ref-readonly-locals"></a>Restrictions sur l’utilisation de `ref readonly` variables locales
À l’exception de leur nature `readonly`, `ref readonly` variables locales se comportent comme des variables locales `ref` ordinaires et sont soumises à des restrictions rigoureuses.  
Par exemple, les restrictions liées à la capture dans des fermetures, la déclaration dans `async` méthodes ou l’analyse des `safe-to-return` s’appliquent également à `ref readonly` variables locales.

## <a name="ternary-ref-expressions-aka-conditional-lvalues"></a>Expressions `ref` ternaires. (également appelé « LValues conditionnel »)

### <a name="motivation"></a>Motivation
L’utilisation de `ref` et `ref readonly` des variables locales exposait un besoin de référencer ces variables locales avec une ou une autre variable cible basée sur une condition.

Une solution de contournement classique consiste à introduire une méthode comme :

```csharp
ref T Choice(bool condition, ref T consequence, ref T alternative)
{
    if (condition)
    {
         return ref consequence;
    }
    else
    {
         return ref alternative;
    }
}
```

Notez que `Choice` n’est pas un remplacement exact d’un ternaire, car _tous les_ arguments doivent être évalués sur le site d’appel, ce qui a entraîné un comportement non intuitif et des bogues.

Les éléments suivants ne fonctionnent pas comme prévu :

```csharp
    // will crash with NRE because 'arr[0]' will be executed unconditionally
    ref var r = ref Choice(arr != null, ref arr[0], ref otherArr[0]);
```

### <a name="solution"></a>Solution
Autorise un type spécial d’expression conditionnelle qui prend la valeur d’une référence à l’un des arguments LValue en fonction d’une condition.

### <a name="using-ref-ternary-expression"></a>À l’aide d' `ref` expression ternaire.

La syntaxe de la `ref` version d’une expression conditionnelle est `<condition> ? ref <consequence> : ref <alternative>;`

Comme avec l’expression conditionnelle ordinaire uniquement `<consequence>` ou `<alternative>` est évaluée en fonction du résultat de l’expression de condition booléenne.

Contrairement à l’expression conditionnelle ordinaire, `ref` expression conditionnelle :
- requiert que `<consequence>` et `<alternative>` soient LValues.
- `ref` expression conditionnelle elle-même est une LValue et
- `ref` expression conditionnelle peut être accessible en écriture si `<consequence>` et `<alternative>` sont accessibles en écriture LValues

Exemples :  
`ref` ternaire est une LValue et, par conséquent, elle peut être passée/affectée/retournée par référence ;
```csharp
     // pass by reference
     foo(ref (arr != null ? ref arr[0]: ref otherArr[0]));

     // return by reference
     return ref (arr != null ? ref arr[0]: ref otherArr[0]);
```

Étant une LValue, elle peut également être assignée à.
```csharp
     // assign to
     (arr != null ? ref arr[0]: ref otherArr[0]) = 1;

     // error. readOnlyField is readonly and thus conditional expression is readonly
     (arr != null ? ref arr[0]: ref obj.readOnlyField) = 1;
```

Peut être utilisé comme récepteur d’un appel de méthode et ignorer la copie si nécessaire.
```csharp
     // no copies
     (arr != null ? ref arr[0]: ref otherArr[0]).StructMethod();

     // invoked on a copy.
     // The receiver is `readonly` because readOnlyField is readonly.
     (arr != null ? ref arr[0]: ref obj.readOnlyField).StructMethod();

     // no copies. `ReadonlyStructMethod` is a method on a `readonly` struct
     // and can be invoked directly on a readonly receiver
     (arr != null ? ref arr[0]: ref obj.readOnlyField).ReadonlyStructMethod();
```

`ref` ternaire peut également être utilisé dans un contexte standard (non Ref).
```csharp
     // only an example
     // a regular ternary could work here just the same
     int x = (arr != null ? ref arr[0]: ref otherArr[0]);
```

### <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Je peux voir deux arguments principaux sur la prise en charge améliorée pour les références et les références ReadOnly :

1) Les problèmes résolus ici sont très anciens. Pourquoi les résoudre soudainement maintenant, en particulier dans la mesure où elle n’aide pas le code existant ?

Comme nous l' C# avons trouvé et que .net est utilisé dans les nouveaux domaines, certains problèmes deviennent plus évidents.  
Comme des exemples d’environnements qui sont plus critiques que la moyenne des frais de calcul, je peux répertorier

* scénarios Cloud/de centre de donnes où le calcul est facturé et la réactivité est un avantage concurrentiel.
* Jeux/VR/AR avec exigences de temps réel en temps réel sur les latences     

Cette fonctionnalité ne sacrifie pas les forces existantes telles que le type-Safety, tout en permettant de réduire les surplus dans certains scénarios courants.

2) Est-il possible de garantir raisonnablement que l’appelé sera joué par les règles lorsqu’il se contente de `readonly` des contrats ?

Nous avons une confiance similaire lors de l’utilisation de `out`. Une implémentation incorrecte de `out` peut entraîner un comportement non spécifié, mais, en réalité, elle se produit rarement.  

Le fait de faire en sorte que les règles de vérification formelles soient familières à `ref readonly` atténuer le problème d’approbation.

### <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

La conception concurrente principale est vraiment « ne rien faire ».

### <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

### <a name="design-meetings"></a>Réunions de conception

https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-02-22.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-01.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-08-28.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-09-25.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-09-27.md
