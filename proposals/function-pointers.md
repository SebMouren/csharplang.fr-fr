---
ms.openlocfilehash: 9e7059b579060e547e4665ac50518581fe3512e6
ms.sourcegitcommit: 52624f54c0ad99a4d659fe4e2560a472be795c49
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81005702"
---
# <a name="function-pointers"></a>Pointeurs fonction

## <a name="summary"></a>Récapitulatif

Cette proposition fournit des constructions linguistiques qui exposent les codes d’opcodes de `ldftn` `calli`l’IL qui ne peuvent pas actuellement être consultés efficacement, ou du tout, dans Cô aujourd’hui: et . Ces codes d’opcodes IL peuvent être importants dans le code haute performance et les développeurs ont besoin d’un moyen efficace d’y accéder.

## <a name="motivation"></a>Motivation

Les motivations et les antécédents de cette fonctionnalité sont décrits dans le numéro suivant (comme c’est une mise en œuvre potentielle de la fonctionnalité):

https://github.com/dotnet/csharplang/issues/191

Il s’agit d’une proposition de conception alternative pour [compiler intrinsèques](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)

## <a name="detailed-design"></a>Conception détaillée

### <a name="function-pointers"></a>Pointeurs fonction

Le langage permettra la déclaration des pointeurs de fonction à l’aide de la `delegate*` syntaxe. La syntaxe complète est décrite en détail dans la section suivante, mais elle est destinée à ressembler à la syntaxe utilisée par `Func` et `Action` les déclarations de type.

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

Ces types sont représentés à l’aide du type de pointeur de fonction tel qu’il est décrit dans ECMA-335. Cela `delegate*` signifie l’invocation `calli` d’un volonté `delegate` utilisé `callvirt` où l’invocation d’un volonté utilisé sur la `Invoke` méthode.
Syntactiquement cependant l’invocation est identique pour les deux constructions.

La définition ecMA-335 des pointeurs de méthode inclut la convention d’appel dans le cadre de la signature type (article 7.1).
La convention d’appel par défaut sera `managed`. D’autres formes peuvent être spécifiées `delegate*` en `managed`ajoutant `cdecl` `stdcall`le `thiscall`modificateur approprié après la syntaxe : , , , , ou `unmanaged`. Exemple :

``` csharp
// This method will be invoked using the cdecl calling convention
delegate* cdecl<int, int>;

// This method will be invoked using the stdcall calling convention
delegate* stdcall<int, int>;
```

Les conversions entre `delegate*` types se font en fonction de leur signature, y compris la convention d’appel.

``` csharp
unsafe class Example {
    void Conversions() {
        delegate*<int, int, int> p1 = ...;
        delegate* managed<int, int, int> p2 = ...;
        delegate* cdecl<int, int, int> p3 = ...;

        p1 = p2; // okay p1 and p2 have compatible signatures
        Console.WriteLine(p2 == p1); // True
        p2 = p3; // error: calling conventions are incompatible
    }
}
```

Un `delegate*` type est un type de pointeur qui signifie qu’il a toutes les capacités et les restrictions d’un type de pointeur standard:

- Seulement valable `unsafe` dans un contexte.
- Les méthodes `delegate*` qui contiennent un paramètre ou `unsafe` un type de retour ne peuvent être appelées que dans un contexte.
- Impossible de convertir `object`en .
- Ne peut pas être utilisé comme argument générique.
- Peut implicitement `delegate*` `void*`convertir en .
- Peut explicitement `void*` convertir `delegate*`de .

Restrictions :

- Les attributs personnalisés `delegate*` ne peuvent pas être appliqués à un ou à l’un de ses éléments.
- Un `delegate*` paramètre ne peut pas être marqué comme`params`
- Un `delegate*` type a toutes les restrictions d’un type de pointeur normal.

### <a name="function-pointer-syntax"></a>Syntaxe de pointeur de fonction

La syntaxe de pointeur de fonction complète est représentée par la grammaire suivante :

```antlr
pointer_type
    : ...
    | funcptr_type
    ;

funcptr_type
    : 'delegate' '*' calling_convention? '<' (funcptr_parameter_modifier? type ',')* funcptr_return_modifier? return_type '>'
    ;

calling_convention
    : 'cdecl'
    | 'managed'
    | 'stdcall'
    | 'thiscall'
    | 'unmanaged'
    ;

funcptr_parameter_modifier
    : 'ref'
    | 'out'
    | 'in'
    ;

funcptr_return_modifier
    : 'ref'
    | 'ref readonly'
    ;
```

La `unmanaged` convention d’appel représente la convention d’appel par défaut pour le code natif sur la plate-forme actuelle, et est codé comme winapi.
Tous `calling_convention`sont des mots-clés contextuels lorsqu’ils sont précédés d’un `delegate*`.

``` csharp
delegate int Func1(string s);
delegate Func1 Func2(Func1 f);

// Function pointer equivalent without calling convention
delegate*<string, int>;
delegate*<delegate*<string, int>, delegate*<string, int>>;

// Function pointer equivalent with calling convention
delegate* managed<string, int>;
delegate*<delegate* managed<string, int>, delegate*<string, int>>;
```

### <a name="function-pointer-conversions"></a>Conversions de pointeurs de fonction

Dans un contexte dangereux, l’ensemble des conversions implicites disponibles (conversions implicites) est étendu pour inclure les conversions implicites suivantes :
- [_Conversions existantes_](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#pointer-conversions)
- Du `F0` type _funcptr\__ à un autre type `F1` _funcptr\__ , à condition que tous les éléments suivants sont vrais:
    - `F0`et `F1` ont le même nombre de `D0n` paramètres, et `ref` `out`chaque `in` paramètre a `F0` le `D1n` `F1`même , , ou modificateurs que le paramètre correspondant dans .
    - Pour chaque paramètre de `ref`valeur `out`(un paramètre sans, ou `in` modificateur), une conversion d’identité, une conversion implicite de référence, ou la conversion implicite de pointeur existe du type de paramètre dans `F0` le type de paramètre correspondant dans `F1`.
    - Pour `ref`chaque `out`, `in` , ou paramètre, le type de paramètre `F0` est le même que le type de paramètre correspondant dans `F1`.
    - Si le type de retour `ref` `ref readonly`est par valeur (non ou ), une identité, `F1` une référence implicite, ou la conversion implicite pointeur existe du type de retour au type de retour de `F0`.
    - Si le type de`ref` retour `ref readonly`est par `ref` référence (ou), le type de retour et les modificateurs de `F1` sont les mêmes que le type de retour et `ref` les modificateurs de `F0`.
    - La convention `F0` d’appel de est `F1`la même que la convention d’appel de .

### <a name="allow-address-of-to-target-methods"></a>Autoriser l’adresse des méthodes cibles

Les groupes de méthode seront désormais autorisés comme arguments à une adresse d’expression. Le type d’une telle `delegate*` expression sera un qui a la signature équivalente de la méthode cible et une convention d’appel gérée:

``` csharp
unsafe class Util {
    public static void Log() { }

    void Use() {
        delegate*<void> ptr1 = &Util.Log;

        // Error: type "delegate*<void>" not compatible with "delegate*<int>";
        delegate*<int> ptr2 = &Util.Log;

        // Okay. Conversion to void* is always allowed.
        void* v = &Util.Log;
   }
}
```

Dans un contexte dangereux, une méthode `M` est `F` compatible avec un type de pointeur de fonction si tous les éléments suivants sont vrais :
- `M`et `F` ont le même nombre de paramètres, et `ref` `out`chaque `in` paramètre a `D` le `F`même , , ou modificateurs que le paramètre correspondant dans .
- Pour chaque paramètre de `ref`valeur `out`(un paramètre sans, ou `in` modificateur), une conversion d’identité, une conversion implicite de référence, ou la conversion implicite de pointeur existe du type de paramètre dans `M` le type de paramètre correspondant dans `F`.
- Pour `ref`chaque `out`, `in` , ou paramètre, le type de paramètre `M` est le même que le type de paramètre correspondant dans `F`.
- Si le type de retour `ref` `ref readonly`est par valeur (non ou ), une identité, `F` une référence implicite, ou la conversion implicite pointeur existe du type de retour au type de retour de `M`.
- Si le type de`ref` retour `ref readonly`est par `ref` référence (ou), le type de retour et les modificateurs de `F` sont les mêmes que le type de retour et `ref` les modificateurs de `M`.
- La convention `M` d’appel de est `F`la même que la convention d’appel de .
- `M` est une méthode statique.

Dans un contexte dangereux, une conversion implicite existe d’une adresse `E` d’expression dont `F` `E` la cible est un groupe de méthode à un type compatible de pointeur `F`de fonction si elle contient au moins une méthode qui s’applique dans sa forme normale à une liste d’arguments construite à l’aide des types de paramètres et des modificateurs de , tel que décrit dans ce qui suit.
- Une seule `M` méthode est sélectionnée correspondant à une `E(A)` invocation de la forme par méthode avec les modifications suivantes :
   - La liste `A` des arguments est une liste d’expressions, chacune`ref` `out`classée `in`comme variable et avec le type et le modificateur ( , , ou ) de la liste de _\_\_paramètres formels_ correspondantes de `D`.
   - Les méthodes candidates ne sont que les méthodes qui s’appliquent dans leur forme normale, et non celles applicables sous leur forme élargie.
   - Les méthodes candidates ne sont que les méthodes statiques.
- Si l’algorithme des invocations de méthode produit une erreur, alors une erreur de compilation-temps se produit. Dans le cas contraire, `M` l’algorithme produit une `F` meilleure méthode ayant le même nombre de paramètres que la conversion est considérée comme existant.
- La méthode `M` sélectionnée doit être compatible (comme défini `F`ci-dessus) avec le type de pointeur de fonction . Sinon, une erreur de compilation se produit.
- Le résultat de la conversion est `F`un pointeur de fonction de type .

Une conversion implicite existe à partir d’une adresse `E` `void*` d’expression dont la `M` `E`cible est un groupe de méthode à s’il n’y a qu’une seule méthode statique dans .
S’il ya une méthode statique, `E` alors `M`la meilleure méthode unique de est .
Sinon, une erreur de compilation se produit.

Cela signifie que les développeurs peuvent compter sur les règles de résolution de surcharge pour fonctionner en collaboration avec l’adresse de l’opérateur :

``` csharp
unsafe class Util {
    public static void Log() { }
    public static void Log(string p1) { }
    public static void Log(int i) { };

    void Use() {
        delegate*<void> a1 = &Log; // Log()
        delegate*<int, void> a2 = &Log; // Log(int i)

        // Error: ambiguous conversion from method group Log to "void*"
        void* v = &Log;
    }
```

L’adresse de l’opérateur `ldftn` sera mise en œuvre à l’aide de l’instruction.

Restrictions de cette fonctionnalité:

- Ne s’applique `static`qu’aux méthodes marquées comme .
- Les`static` fonctions non locales `&`ne peuvent pas être utilisées dans . Les détails de mise en œuvre de ces méthodes ne sont délibérément pas spécifiés par la langue. Cela comprend s’ils sont statiques par rapport à l’instance ou exactement avec quelle signature ils sont émis.


### <a name="operators-on-function-pointer-types"></a>Opérateurs sur les types de pointeurs de fonction

La section en code dangereux sur les opérateurs est modifiée en tant que telle :

> Dans un contexte dangereux, plusieurs constructions sont disponibles\_pour fonctionner sur tous les\__pointer type_s qui ne sont pas _funcptr type_s :
>
> *  L’opérateur `*` peut être utilisé pour effectuer l’indirection pointeur ([indirection pointeur](unsafe-code.md#pointer-indirection)).
> *  L’opérateur `->` peut être utilisé pour accéder à un membre d’une struction par l’intermédiaire d’un pointeur[(accès au membre Pointeur](unsafe-code.md#pointer-member-access)).
> *  L’opérateur `[]` peut être utilisé pour indexer un pointeur[(accès à l’élément Pointeur](unsafe-code.md#pointer-element-access)).
> *  L’opérateur `&` peut être utilisé pour obtenir l’adresse d’une variable[(L’adresse de l’opérateur](unsafe-code.md#the-address-of-operator)).
> *  Les `++` `--` opérateurs et les opérateurs peuvent être utilisés pour incrémenter et décroisser les pointeurs[(augmentation et décroissement des pointeurs).](unsafe-code.md#pointer-increment-and-decrement)
> *  Les `+` `-` opérateurs et les opérateurs peuvent être utilisés pour effectuer l’arithmétique pointeur ([arithmétique pointeur](unsafe-code.md#pointer-arithmetic)).
> *  Le `==` `!=`, `<` `>`, `<=`, `=>` , et les opérateurs peuvent être utilisés pour comparer les pointeurs ([comparaison Pointer](unsafe-code.md#pointer-comparison)).
> *  L’opérateur `stackalloc` peut être utilisé pour allouer la mémoire de la pile d’appels[(tampons de taille fixe](unsafe-code.md#fixed-size-buffers)).
> *  L’instruction `fixed` peut être utilisée pour corriger temporairement une variable afin que son adresse puisse être obtenue ([L’instruction fixe](unsafe-code.md#the-fixed-statement)).
> 
> Dans un contexte dangereux, plusieurs constructions sont disponibles\_pour fonctionner sur tous les _funcptr type_s :
> *  L’opérateur `&` peut être utilisé pour obtenir l’adresse des méthodes statiques[(Autoriser l’adresse des méthodes cibles](function-pointers.md#allow-address-of-to-target-methods))
> *  Le `==` `!=`, `<` `>`, `<=`, `=>` , et les opérateurs peuvent être utilisés pour comparer les pointeurs ([comparaison Pointer](unsafe-code.md#pointer-comparison)).

En outre, nous modifions toutes les sections pour `Pointers in expressions` interdire les types de pointeurs de fonction, sauf `Pointer comparison` et `The sizeof operator`.

### <a name="better-function-member"></a>Meilleur membre de la fonction

La meilleure spécification des membres de fonction sera modifiée pour inclure la ligne suivante :

> A `delegate*` est plus spécifique que`void*`

Cela signifie qu’il est `void*` possible `delegate*` de surcharger et d’utiliser l’adresse de l’opérateur et d’utiliser toujours judicieusement.

## <a name="open-issues"></a>Open Issues

### <a name="nativecallableattribute"></a>NativeCallableAttribute NativeCallableAttribute

Il s’agit d’un attribut utilisé par le CLR pour éviter le prologue réussi à native lors de l’invocation. Les méthodes marquées par cet attribut ne sont callables qu’à partir du code natif, non gérées (ne peut pas appeler des méthodes, créer un délégué, etc...). L’attribut n’est pas spécial pour mscorlib; l’exécution traitera n’importe quel attribut avec ce nom avec la même sémantique.

Il est possible que le temps d’exécution et la langue travaillent ensemble pour soutenir pleinement cela. La langue pourrait choisir de `static` traiter `NativeCallable` l’adresse `delegate*` des membres avec un attribut comme un avec la convention d’appel spécifiée.

``` csharp
unsafe class NativeCallableExample {
    [NativeCallable(CallingConvention.CDecl)]
    static void CloseHandle(IntPtr p) => Marshal.FreeHGlobal(p);

    void Use() {
        delegate*<IntPtr, void> p1 = &CloseHandle; // Error: Invalid calling convention

        delegate* cdecl<IntPtr, void> p2 = &CloseHandle; // Okay
    }
}

```

En outre, la langue voudrait probablement aussi:

- Signalez tous les appels gérés `NativeCallable` vers une méthode étiquetée comme une erreur. Étant donné que la fonction ne peut pas être invoquée à partir de code géré, le compilateur devrait empêcher les développeurs de tenter une telle invocation.
- Empêcher les conversions `delegate` de groupe de `NativeCallable`méthode à quand la méthode est étiquetée avec .

Ce n’est `NativeCallable` pas nécessaire pour soutenir cependant. Le compilateur peut `NativeCallable` prendre en charge l’attribut à l’aide de la syntaxe existante. Le programme aurait simplement `void*` besoin de jeter `delegate*` à avant de lancer à la signature correcte. Ce ne serait pas pire que le soutien aujourd’hui.

``` csharp
void* v = &CloseHandle;
delegate* cdecl<IntPtr, bool> f1 = (delegate* cdecl<IntPtr, bool>)v;
```

### <a name="extensible-set-of-unmanaged-calling-conventions"></a>Ensemble extensible de conventions d’appel non gestion

L’ensemble des conventions d’appel non gestion soutenues par les codages actuels de l’ECMA-335 est dépassé. Nous avons vu des demandes d’ajout d’un soutien pour des conventions d’appel plus non gestion, par exemple:

- [vectorcall](https://docs.microsoft.com/cpp/cpp/vectorcall)https://github.com/dotnet/coreclr/issues/12120
- StdCall avec explicite cettehttps://github.com/dotnet/coreclr/pull/23974#issuecomment-482991750

La conception de cette fonctionnalité devrait permettre d’étendre l’ensemble des conventions d’appel non gestion au besoin à l’avenir. Les problèmes comprennent un espace limité pour l’encodage des `IMAGE_CEE_CS_CALLCONV_MASK`conventions d’appel (12 sur 16 valeurs sont prises en) et le nombre d’endroits qui doivent être touchés afin d’ajouter une nouvelle convention d’appel. Une solution potentielle consiste à introduire un nouveau [`System.Runtime.InteropServices.CallingConvention`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.callingconvention) codage qui représente la convention d’appel à l’aide d’enum.

Pour référence, https://github.com/llvm/llvm-project/blob/master/llvm/include/llvm/IR/CallingConv.h a la liste des conventions d’appel soutenue par LLVM. Bien qu’il soit peu probable que .NET n’aura jamais besoin de les soutenir tous, il démontre que l’espace d’appeler les conventions est très riche.

## <a name="considerations"></a>Considérations

### <a name="allow-instance-methods"></a>Autoriser les méthodes d’instance

La proposition pourrait être étendue pour soutenir `EXPLICITTHIS` les méthodes d’instance en tirant parti de la convention d’appel de l’ICM (nommée `instance` dans le code C). Cette forme de pointeurs de `this` fonction CLI met le paramètre comme un premier paramètre explicite de la syntaxe pointeur de fonction.

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

C’est sain, mais ajoute une certaine complication à la proposition. En particulier parce que les pointeurs `instance` `managed` de fonction qui différaient par la convention d’appel et seraient incompatibles même si les deux cas sont utilisés pour invoquer des méthodes gérées avec la même signature C. Aussi dans tous les cas considérés où cela serait précieux `static` pour avoir il y avait un travail simple autour: utiliser une fonction locale.

``` csharp
unsafe class Instance {
    void Use() {
        static string toString(Instance i) = i.ToString();
        delgate*<Instance, string> f = &toString;
        f(this);
    }
}
```

### <a name="dont-require-unsafe-at-declaration"></a>Ne pas exiger de sécurité à la déclaration

Au lieu `unsafe` d’exiger `delegate*`à chaque utilisation d’un , seulement l’exiger au point où un groupe de méthode est converti en un `delegate*`. C’est là que les principaux problèmes de sécurité entrent en jeu (sachant que l’assemblage contenant ne peut pas être déchargé pendant que la valeur est vivante). Exiger `unsafe` sur les autres endroits peut être considéré comme excessif.

C’est ainsi que la conception était initialement prévue. Mais les règles linguistiques qui en résultaient se sentaient très maladroites. Il est impossible de cacher le fait qu’il s’agit d’une valeur pointeur et il a continué à jeter un coup d’oeil à travers même sans le `unsafe` mot clé. Par exemple, `object` la conversion à ne peut pas être autorisé, il ne peut pas être un membre d’un `class`, etc ... La conception C est `unsafe` d’exiger pour toutes les utilisations de pointeur et donc cette conception suit cela.

Les développeurs seront toujours _safe_ capables de présenter `delegate*` un emballage sûr sur le dessus des valeurs de la même manière qu’ils le font pour les types de pointeurs normaux aujourd’hui. Vous devez :

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a>Utilisation des délégués

Au lieu d’utiliser un `delegate*`nouvel élément `delegate` syntaxe, il suffit d’utiliser les types existants avec un `*` suivant le type:

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

La gestion de la convention d’appel peut se faire en annotant les `delegate` types avec un attribut qui spécifie une `CallingConvention` valeur. L’absence d’attribut signifierait la convention d’appel gérée.

L’encodage de ce dans IL est problématique. La valeur sous-jacente doit être représentée comme un pointeur mais elle doit aussi :

1. Avoir un type unique pour permettre des surcharges avec différents types de pointeur de fonction.
1. Être équivalent aux fins de l’OHI au-delà des limites de l’assemblage.

Le dernier point est particulièrement problématique. Cela signifie que chaque `Func<int>*` assemblage qui utilise doit coder `Func<int>*` un type équivalent dans les métadonnées, même si elle est définie dans un assemblage mais ne contrôle pas.
En outre, tout autre type `System.Func<T>` qui est défini avec le nom dans un assemblage qui n’est pas mscorlib doit être différent de la version définie dans mscorlib.

Une option qui a été explorée `mod_req(Func<int>) void*`était d’émettre un tel pointeur que . Cela ne fonctionne pas `mod_req` bien que `TypeSpec` comme un ne peut pas se lier à un et ne peut donc pas cibler les instantanéisations génériques.

### <a name="named-function-pointers"></a>Indicateurs de fonction nommés

La syntaxe de pointeur de fonction peut être lourde, particulièrement dans les cas complexes comme les pointeurs de fonction imbriqués. Plutôt que d’avoir des développeurs tapent la signature chaque fois que la `delegate`langue pourrait permettre des déclarations nommées de pointeurs de fonction comme c’est fait avec .

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

Une partie du problème ici est le primitif CLI sous-jacent n’a pas de noms d’où ce serait purement une invention C et nécessitent un peu de travail de métadonnées pour permettre. C’est faisable, mais c’est une question importante de travail. Il exige essentiellement que CMD ait un compagnon à la table de def de type uniquement pour ces noms.

Aussi, lorsque les arguments pour les pointeurs de fonction nommés ont été examinés, nous avons constaté qu’ils pouvaient s’appliquer tout aussi bien à un certain nombre d’autres scénarios. Par exemple, il serait tout aussi commode de déclarer les tuples nommés pour réduire la nécessité de taper la signature complète dans tous les cas.

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

Après discussion, nous avons décidé `delegate*` de ne pas autoriser la déclaration nommée de types. Si nous constatons qu’il ya un besoin important pour cela basé sur la rétroaction de l’utilisation du client, alors nous allons enquêter sur une solution de nommage qui fonctionne pour les pointeurs de fonction, tuples, génériques, etc ... Cela est susceptible d’être similaire dans `typedef` la forme à d’autres suggestions comme le plein soutien dans la langue.

## <a name="future-considerations"></a>Éléments futurs à prendre en considération

### <a name="static-local-functions"></a>fonctions locales statiques

Il s’agit de la `static` [proposition](https://github.com/dotnet/csharplang/issues/1565) d’autoriser le modificateur sur les fonctions locales. Une telle fonction serait garantie d’être émise au fur et à mesure que `static` la signature exacte est spécifiée dans le code source. Une telle fonction devrait être `&` un argument valable à car il ne contient aucun des problèmes des fonctions locales ont aujourd’hui

### <a name="static-delegates"></a>délégués statiques

Il s’agit de [la proposition](https://github.com/dotnet/csharplang/issues/302) `delegate` visant à permettre `static` la déclaration des types qui ne peuvent se référer qu’aux membres. L’avantage étant `delegate` que de tels cas peuvent être libres d’allocation et mieux dans les scénarios sensibles aux performances.

Si la fonction de `static delegate` pointeur de fonction est mise en œuvre, la proposition sera probablement fermée. L’avantage proposé de cette fonctionnalité est la nature libre d’allocation. Toutefois, des enquêtes récentes ont révélé que ce n’est pas possible d’atteindre en raison du déchargement de l’assemblage. Il doit y avoir `static delegate` une poignée forte de la méthode à laquelle il se réfère afin d’empêcher l’assemblage d’être déchargé hors de dessous elle.

Pour maintenir `static delegate` chaque instance serait nécessaire d’allouer une nouvelle poignée qui va à l’encontre des objectifs de la proposition. Il y avait quelques conceptions où l’allocation pourrait être amortie à une seule allocation par site d’appel, mais qui était un peu complexe et ne semblait pas valoir le compromis.

Cela signifie que les développeurs doivent essentiellement décider entre les compromis suivants:

1. Sécurité face au déchargement de l’assemblage : `delegate` cela nécessite des allocations et est donc déjà une option suffisante.
1. Pas de sécurité face au déchargement `delegate*`de l’assemblage : utilisez un . Cela peut être `struct` enveloppé dans un `unsafe` pour permettre l’utilisation en dehors d’un contexte dans le reste du code.
