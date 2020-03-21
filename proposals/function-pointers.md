---
ms.openlocfilehash: 8bf3a18dc42e225e64bd3ccda2106aed89b421ed
ms.sourcegitcommit: 9aa177443b83116fe1be2ab28e2c7291947fe32d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2020
ms.locfileid: "80108387"
---
# <a name="function-pointers"></a>Pointeurs fonction

## <a name="summary"></a>Résumé

Cette proposition fournit des constructions de langage qui exposent des opcodes IL qui ne sont pas actuellement accessibles efficacement, C# ou du tout, dans aujourd’hui : `ldftn` et `calli`. Ces opcodes IL peuvent être importants dans du code haute performance et les développeurs doivent disposer d’un moyen efficace pour y accéder.

## <a name="motivation"></a>Motivation

Les motivations et l’arrière-plan de cette fonctionnalité sont décrits dans le problème suivant (comme c’est une implémentation potentielle de la fonctionnalité) :

https://github.com/dotnet/csharplang/issues/191

Il s’agit d’une proposition de conception alternative aux [intrinsèques du compilateur](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)

## <a name="detailed-design"></a>Conception détaillée

### <a name="function-pointers"></a>Pointeurs fonction

Le langage autorisera la déclaration des pointeurs de fonction à l’aide de la syntaxe `delegate*`. La syntaxe complète est décrite en détail dans la section suivante, mais elle doit ressembler à la syntaxe utilisée par les déclarations de type `Func` et `Action`.

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

Ces types sont représentés à l’aide du type de pointeur de fonction, comme indiqué dans la norme ECMA-335. Cela signifie que l’appel d’un `delegate*` utilise `calli` où l’appel d’un `delegate` utilisera `callvirt` sur la méthode `Invoke`.
Toutefois, la syntaxe de l’appel est identique pour les deux constructions.

La définition ECMA-335 des pointeurs de méthode comprend la Convention d’appel dans le cadre de la signature de type (section 7,1).
La Convention d’appel par défaut sera `managed`. Les autres formulaires peuvent être spécifiés en ajoutant le modificateur approprié après la syntaxe de `delegate*` : `managed`, `cdecl`, `stdcall`, `thiscall`ou `unmanaged`. Exemple :

``` csharp
// This method will be invoked using the cdecl calling convention
delegate* cdecl<int, int>;

// This method will be invoked using the stdcall calling convention
delegate* stdcall<int, int>;
```

Les conversions entre les types de `delegate*` sont effectuées en fonction de leur signature, y compris la Convention d’appel.

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

Un type de `delegate*` est un type pointeur, ce qui signifie qu’il possède toutes les fonctionnalités et restrictions d’un type pointeur standard :

- Valide uniquement dans un contexte de `unsafe`.
- Les méthodes qui contiennent un paramètre `delegate*` ou un type de retour ne peuvent être appelées qu’à partir d’un contexte de `unsafe`.
- Ne peut pas être converti en `object`.
- Ne peut pas être utilisé comme argument générique.
- Peut convertir implicitement `delegate*` en `void*`.
- Peut effectuer une conversion explicite de `void*` en `delegate*`.

Restrictions :

- Les attributs personnalisés ne peuvent pas être appliqués à un `delegate*` ou à l’un de ses éléments.
- Un paramètre de `delegate*` ne peut pas être marqué comme `params`
- Un type de `delegate*` présente toutes les restrictions d’un type pointeur normal.

### <a name="function-pointer-syntax"></a>Syntaxe du pointeur de fonction

La syntaxe complète du pointeur fonction est représentée par la grammaire suivante :

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

La Convention d’appel `unmanaged` représente la Convention d’appel par défaut pour le code natif sur la plateforme actuelle, et est encodée en winapi.
Tous les `calling_convention`s sont des mots clés contextuels lorsqu’ils sont précédés d’un `delegate*`.

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

### <a name="function-pointer-conversions"></a>Conversions de pointeur de fonction

Dans un contexte non sécurisé, l’ensemble des conversions implicites disponibles (conversions implicites) est étendu pour inclure les conversions de pointeur implicites suivantes :
- [_Conversions existantes_](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#pointer-conversions)
- À partir de _funcptr\_type_ `F0` à un autre `F1`_type de\_funcptr_ , à condition que les conditions suivantes soient remplies :
    - `F0` et `F1` ont le même nombre de paramètres, et chaque paramètre `D0n` dans `F0` a les mêmes modificateurs `ref`, `out`ou `in` que le paramètre correspondant `D1n` dans `F1`.
    - Pour chaque paramètre de valeur (paramètre sans `ref`, `out`ou modificateur `in`), il existe une conversion d’identité, une conversion de référence implicite ou une conversion de pointeur implicite du type de paramètre dans `F0` vers le type de paramètre correspondant dans `F1`.
    - Pour chaque paramètre `ref`, `out`ou `in`, le type de paramètre dans `F0` est le même que le type de paramètre correspondant dans `F1`.
    - Si le type de retour est par valeur (aucune `ref` ou `ref readonly`), une identité, une référence implicite ou une conversion de pointeur implicite existe du type de retour de `F1` au type de retour de `F0`.
    - Si le type de retour est par référence (`ref` ou `ref readonly`), le type de retour et les modificateurs de `ref` de `F1` sont les mêmes que le type de retour et les modificateurs `ref` de `F0`.
    - La Convention d’appel de `F0` est identique à la Convention d’appel de `F1`.

### <a name="allow-address-of-to-target-methods"></a>Autoriser l’adresse à cibler des méthodes

Les groupes de méthodes sont désormais autorisés comme arguments d’une expression d’adresse. Le type d’une telle expression sera un `delegate*` qui a la signature équivalente de la méthode cible et une convention d’appel managée :

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

Dans un contexte unsafe, une méthode `M` est compatible avec un type de pointeur fonction `F` si toutes les conditions suivantes sont vraies :
- `M` et `F` ont le même nombre de paramètres, et chaque paramètre dans `D` a les mêmes modificateurs `ref`, `out`ou `in` que le paramètre correspondant dans `F`.
- Pour chaque paramètre de valeur (paramètre sans `ref`, `out`ou modificateur `in`), il existe une conversion d’identité, une conversion de référence implicite ou une conversion de pointeur implicite du type de paramètre dans `M` vers le type de paramètre correspondant dans `F`.
- Pour chaque paramètre `ref`, `out`ou `in`, le type de paramètre dans `M` est le même que le type de paramètre correspondant dans `F`.
- Si le type de retour est par valeur (aucune `ref` ou `ref readonly`), une identité, une référence implicite ou une conversion de pointeur implicite existe du type de retour de `F` au type de retour de `M`.
- Si le type de retour est par référence (`ref` ou `ref readonly`), le type de retour et les modificateurs de `ref` de `F` sont les mêmes que le type de retour et les modificateurs `ref` de `M`.
- La Convention d’appel de `M` est identique à la Convention d’appel de `F`.
- `M` est une méthode statique.

Dans un contexte non sécurisé, une conversion implicite existe à partir d’une expression d’adresse dont la cible est un groupe de méthodes `E` à un type pointeur fonction compatible `F` si `E` contient au moins une méthode qui est applicable dans sa forme normale à une liste d’arguments construite à l’aide des types de paramètres et des modificateurs de `F`, comme décrit dans l’exemple suivant.
- Une seule méthode `M` est sélectionnée correspondant à un appel de méthode de la forme `E(A)` avec les modifications suivantes :
   - La liste d’arguments `A` est une liste d’expressions, chacune classée en tant que variable et avec le type et le modificateur (`ref`, `out`ou `in`) du _paramètre\_formel correspondant\_liste_ des `D`.
   - Les méthodes candidates sont uniquement les méthodes applicables dans leur forme normale, et non celles applicables dans leur forme développée.
   - Les méthodes candidates sont uniquement les méthodes statiques.
- Si l’algorithme d’appels de méthode génère une erreur, une erreur de compilation se produit. Dans le cas contraire, l’algorithme produit une seule meilleure méthode `M` avoir le même nombre de paramètres que `F` et la conversion est considérée comme existante.
- La méthode sélectionnée `M` doit être compatible (comme défini ci-dessus) avec le type de pointeur de fonction `F`. Sinon, une erreur de compilation se produit.
- Le résultat de la conversion est un pointeur de fonction de type `F`.

Une conversion implicite existe à partir d’une expression d’adresse dont la cible est un groupe de méthodes `E` pour `void*` s’il n’existe qu’une seule méthode statique `M` dans `E`.
S’il y a une méthode statique, la seule meilleure méthode de `E` est `M`.
Sinon, une erreur de compilation se produit.

Cela signifie que les développeurs peuvent dépendre de règles de résolution de surcharge pour fonctionner conjointement avec l’opérateur d’adresse :

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

L’opérateur address-of sera implémenté à l’aide de l’instruction `ldftn`.

Restrictions de cette fonctionnalité :

- S’applique uniquement aux méthodes marquées comme `static`.
- Les fonctions locales non`static` ne peuvent pas être utilisées dans les `&`. Les détails de l’implémentation de ces méthodes ne sont délibérément pas spécifiés par le langage. Cela indique s’ils sont statiques ou d’instance ou exactement la signature avec laquelle ils sont émis.

### <a name="better-function-member"></a>Meilleure fonction membre

La meilleure spécification de membre de fonction sera modifiée pour inclure la ligne suivante :

> Une `delegate*` est plus spécifique que `void*`

Cela signifie qu’il est possible de surcharger sur `void*` et un `delegate*` tout en utilisant judicieusement l’opérateur d’adresse.

## <a name="open-issues"></a>Problèmes ouverts

### <a name="nativecallableattribute"></a>NativeCallableAttribute

Il s’agit d’un attribut utilisé par le CLR pour éviter le prologue managé vers natif lors de l’appel de. Les méthodes marquées par cet attribut peuvent uniquement être appelées à partir du code natif, non managées (impossible d’appeler des méthodes, créer un délégué, etc.). L’attribut n’est pas spécial pour mscorlib ; le runtime traitera tout attribut portant ce nom avec la même sémantique.

Il est possible que le runtime et le langage fonctionnent ensemble pour une prise en charge complète. Le langage peut choisir de traiter l’adresse de `static` membres avec un attribut `NativeCallable` en tant que `delegate*` avec la Convention d’appel spécifiée.

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

En outre, le langage souhaiterait probablement également :

- Marquez tous les appels managés à une méthode marquée avec `NativeCallable` comme une erreur. Étant donné que la fonction ne peut pas être appelée à partir du code managé, le compilateur doit empêcher les développeurs de tenter un tel appel.
- Empêche les conversions de groupe de méthodes en `delegate` lorsque la méthode est marquée avec `NativeCallable`.

Toutefois, cela n’est pas nécessaire pour prendre en charge `NativeCallable`. Le compilateur peut prendre en charge l’attribut `NativeCallable` comme utilise la syntaxe existante. Le programme devra simplement effectuer un cast en `void*` avant d’effectuer un cast vers la signature de `delegate*` correcte. Cela ne serait pas pire que le support technique aujourd’hui.

``` csharp
void* v = &CloseHandle;
delegate* cdecl<IntPtr, bool> f1 = (delegate* cdecl<IntPtr, bool>)v;
```

### <a name="extensible-set-of-unmanaged-calling-conventions"></a>Ensemble extensible de conventions d’appel non managées

L’ensemble de conventions d’appel non managées prises en charge par les encodages ECMA-335 actuels est obsolète. Nous avons vu des demandes d’ajout de la prise en charge de plus de conventions d’appel non managées, par exemple :

- [vectorcall](https://docs.microsoft.com/cpp/cpp/vectorcall) https://github.com/dotnet/coreclr/issues/12120
- StdCall avec explicite This https://github.com/dotnet/coreclr/pull/23974#issuecomment-482991750

La conception de cette fonctionnalité doit permettre d’étendre l’ensemble des conventions d’appel non managées si nécessaire. Les problèmes incluent un espace limité pour l’encodage des conventions d’appel (12 valeurs sur 16 sont prises en `IMAGE_CEE_CS_CALLCONV_MASK`) et le nombre d’emplacements qui doivent être horodatés pour ajouter une nouvelle convention d’appel. Une solution possible consiste à introduire un nouvel encodage qui représente la Convention d’appel à l’aide de [`System.Runtime.InteropServices.CallingConvention`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.callingconvention) Enum.

Pour référence, https://github.com/llvm/llvm-project/blob/master/llvm/include/llvm/IR/CallingConv.h contient la liste des conventions d’appel prises en charge par LLVM. Même s’il est peu probable que .NET doive en prendre en charge tous, il démontre que l’espace de conventions d’appel est très riche.

## <a name="considerations"></a>Considérations

### <a name="allow-instance-methods"></a>Autoriser les méthodes d’instance

La proposition peut être étendue pour prendre en charge les méthodes d’instance en tirant parti de la Convention d’appel `EXPLICITTHIS` C# CLI (nommée `instance` dans le code). Cette forme de pointeurs de fonction CLI place le paramètre `this` en tant que premier paramètre explicite de la syntaxe du pointeur de fonction.

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

C’est un son, mais il ajoute une certaine compliquation à la proposition. En particulier, les pointeurs de fonction qui diffèrent par la Convention d’appel `instance` et `managed` seraient incompatibles même si les deux cas sont utilisés pour appeler C# des méthodes managées avec la même signature. Dans tous les cas, dans tous les cas, il est utile de disposer d’une solution de contournement simple : utilisez une `static` fonction locale.

``` csharp
unsafe class Instance {
    void Use() {
        static string toString(Instance i) = i.ToString();
        delgate*<Instance, string> f = &toString;
        f(this);
    }
}
```

### <a name="dont-require-unsafe-at-declaration"></a>Ne nécessite pas de non-sécurité au niveau de la déclaration

Au lieu de demander à `unsafe` à chaque utilisation d’une `delegate*`, n’en avez besoin qu’au point où un groupe de méthodes est converti en `delegate*`. C’est là qu’interviennent les principaux problèmes de sécurité (sachant que l’assembly conteneur ne peut pas être déchargé tant que la valeur est active). Il peut être considéré comme excessif de `unsafe` les autres emplacements.

C’est ainsi que la conception était prévue à l’origine. Mais les règles de langage qui en résultent sont très maladroites. Il est impossible de masquer le fait qu’il s’agit d’une valeur de pointeur et qu’elle reste en lecture seule sans le mot clé `unsafe`. Par exemple, la conversion en `object` ne peut pas être autorisée, elle ne peut pas être membre d’un `class`, etc... La C# conception est d’exiger `unsafe` pour toutes les utilisations du pointeur et cette conception suit donc cela.

Les développeurs pourront toujours présenter un wrapper _sécurisé_ au-dessus des valeurs `delegate*` de la même façon que pour les types pointeur normaux. Vous devez :

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a>Utilisation de délégués

Au lieu d’utiliser un nouvel élément de syntaxe, `delegate*`, il vous suffit d’utiliser les types de `delegate` existants avec un `*` suivant le type :

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

La gestion de la Convention d’appel peut être effectuée en annotant les types de `delegate` avec un attribut qui spécifie une valeur de `CallingConvention`. L’absence d’un attribut signifie la Convention d’appel managée.

L’encodage de ce en IL pose problème. La valeur sous-jacente doit être représentée sous la forme d’un pointeur, mais elle doit également :

1. Avoir un type unique pour autoriser les surcharges avec différents types de pointeur de fonction.
1. Être équivalent à des fins de OHI dans les limites de l’assembly.

Le dernier point est particulièrement problématique. Cela signifie que chaque assembly qui utilise `Func<int>*` doit encoder un type équivalent dans les métadonnées même si `Func<int>*` est défini dans un assembly, même si ne contrôle pas.
En outre, tout autre type défini avec le nom `System.Func<T>` dans un assembly qui n’est pas mscorlib doit être différent de la version définie dans mscorlib.

Une option qui a été explored a émis un tel pointeur comme `mod_req(Func<int>) void*`. Cela ne fonctionne pas tant qu’un `mod_req` ne peut pas être lié à un `TypeSpec` et ne peut donc pas cibler des instanciations génériques.

### <a name="named-function-pointers"></a>Pointeurs de fonction nommés

La syntaxe du pointeur de fonction peut être lourde, en particulier dans les cas complexes comme les pointeurs de fonction imbriqués. Au lieu de demander aux développeurs de taper la signature chaque fois que le langage peut autoriser des déclarations nommées de pointeurs de fonction, comme c’est le cas avec `delegate`.

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

Une partie du problème ici est que la primitive de l’interface CLI sous-jacente n’a pas C# de nom, ce qui n’est pas vraiment une invention et nécessite un peu de métadonnées à activer. C’est réalisable, mais il s’agit d’un travail important. Il est essentiel C# d’avoir un complément à la table de définition de type pour ces noms uniquement.

De même, lorsque les arguments des pointeurs de fonction nommés ont été examinés, nous avons trouvé qu’ils s’appliquent aussi bien à un certain nombre d’autres scénarios. Par exemple, il serait tout aussi commode de déclarer des tuples nommés pour réduire le besoin de taper la signature complète dans tous les cas.

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

Après la discussion, nous avons décidé de ne pas autoriser la déclaration nommée des types de `delegate*`. Si nous constatons qu’il y a des besoins importants en ce qui concerne les commentaires sur l’utilisation des clients, nous étudierons une solution d’attribution de noms qui fonctionne pour les pointeurs de fonction, les tuples, les génériques, etc. Cela est susceptible d’être similaire à d’autres suggestions, par exemple la prise en charge de `typedef` complète dans le langage.

## <a name="future-considerations"></a>Éléments futurs à prendre en considération

### <a name="static-local-functions"></a>fonctions locales statiques

Cela fait référence à [la proposition](https://github.com/dotnet/csharplang/issues/1565) pour autoriser le modificateur `static` sur les fonctions locales. Une telle fonction serait toujours émise comme `static` et avec la signature exacte spécifiée dans le code source. Une telle fonction doit être un argument valide pour `&`, car elle ne contient aucun des problèmes liés aux fonctions locales aujourd’hui

### <a name="static-delegates"></a>délégués statiques

Cela fait référence à [la proposition](https://github.com/dotnet/csharplang/issues/302) permettant de déclarer des types de `delegate` qui peuvent uniquement faire référence à des membres de `static`. L’avantage est que ces instances de `delegate` peuvent être allouées gratuitement et mieux dans des scénarios de performances sensibles.

Si la fonctionnalité du pointeur de fonction est implémentée, le `static delegate` proposition sera probablement fermée. L’avantage proposé par cette fonctionnalité est la nature libre de l’allocation. Toutefois, des investigations récentes ont détecté que le déchargement d’assembly n’est pas possible en raison du déchargement de l’assembly. Il doit y avoir un handle fort de l' `static delegate` à la méthode à laquelle il fait référence pour empêcher l’assembly d’être déchargé à partir de celui-ci.

Pour conserver chaque `static delegate` instance est nécessaire pour allouer un nouveau descripteur qui s’exécute sur les objectifs de la proposition. Il existait des conceptions où l’allocation pouvait être amortie en une seule allocation par site d’appel, mais cela était un peu complexe et ne semble pas avoir un intérêt pour le compromis.

Cela signifie que les développeurs doivent essentiellement décider entre les compromis suivants :

1. Sécurité face au déchargement d’un assembly : cela nécessite des allocations et par conséquent `delegate` est déjà une option suffisante.
1. Aucune sécurité en face du déchargement d’assembly : utilisez un `delegate*`. Cela peut être encapsulé dans un `struct` pour permettre l’utilisation en dehors d’un contexte de `unsafe` dans le reste du code.
