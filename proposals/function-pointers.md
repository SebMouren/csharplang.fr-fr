---
ms.openlocfilehash: 6bf0b29d16297ae5201d9a7773fc9cb95a47f9cb
ms.sourcegitcommit: 1bb454804d017a9ca18c5de47737dd1d68ce3eea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85835141"
---
# <a name="function-pointers"></a>Pointeurs fonction

## <a name="summary"></a>Résumé

Cette proposition fournit des constructions de langage qui exposent des opcodes IL qui ne sont pas actuellement accessibles efficacement, ou du tout, en C# : `ldftn` et `calli` . Ces opcodes IL peuvent être importants dans du code haute performance et les développeurs doivent disposer d’un moyen efficace pour y accéder.

## <a name="motivation"></a>Motivation

Les motivations et l’arrière-plan de cette fonctionnalité sont décrits dans le problème suivant (comme c’est une implémentation potentielle de la fonctionnalité) :

https://github.com/dotnet/csharplang/issues/191

Il s’agit d’une proposition de conception alternative aux [intrinsèques du compilateur](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)

## <a name="detailed-design"></a>Conception détaillée

### <a name="function-pointers"></a>Pointeurs fonction

Le langage autorisera la déclaration des pointeurs de fonction à l’aide de la `delegate*` syntaxe. La syntaxe complète est décrite en détail dans la section suivante, mais elle est destinée à ressembler à la syntaxe utilisée par `Func` et aux `Action` déclarations de type.

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

Ces types sont représentés à l’aide du type de pointeur de fonction, comme indiqué dans la norme ECMA-335. Cela signifie que l’appel d’un `delegate*` utilisera `calli` lorsque l’appel d’un `delegate` sera `callvirt` utilisé sur la `Invoke` méthode.
Toutefois, la syntaxe de l’appel est identique pour les deux constructions.

La définition ECMA-335 des pointeurs de méthode comprend la Convention d’appel dans le cadre de la signature de type (section 7,1).
La Convention d’appel par défaut sera `managed` . Les conventions d’appel non managées peuvent être spécifiées en plaçant un `unmanaged` mot clé après la `delegate*` syntaxe, qui utilise la valeur par défaut de la plateforme Runtime. Des conventions non managées spécifiques peuvent ensuite être spécifiées entre crochets au `unmanaged` mot clé en spécifiant tout type commençant par `CallConv` dans l' `System.Runtime.CompilerServices` espace de noms. Ces types doivent provenir de la bibliothèque principale du programme, et l’ensemble des combinaisons valides dépend de la plateforme.

``` csharp
//This method has a managed calling convention. This is the same as leaving the managed keyword off.
delegate* managed<int, int>;

// This method will be invoked using whatever the default unmanaged calling convention on the runtime
// platform is. This is platform and architecture dependent and is determined by the CLR at runtime.
delegate* unmanaged<int, int>;

// This method will be invoked using the cdecl calling convention
delegate* unmanaged[CallConvCdecl] <int, int>;

// This method will be invoked using the stdcall calling convention, and suppresses GC transition
delegate* unmanaged[CallConvStdCall, CallConvSuppressGCTransition] <int, int>;
```

Les conversions entre `delegate*` les types sont effectuées en fonction de leur signature, y compris la Convention d’appel.

``` csharp
unsafe class Example {
    void Conversions() {
        delegate*<int, int, int> p1 = ...;
        delegate* managed<int, int, int> p2 = ...;
        delegate* unmanaged<int, int, int> p3 = ...;

        p1 = p2; // okay p1 and p2 have compatible signatures
        Console.WriteLine(p2 == p1); // True
        p2 = p3; // error: calling conventions are incompatible
    }
}
```

Un `delegate*` type est un type pointeur, ce qui signifie qu’il possède toutes les fonctionnalités et restrictions d’un type pointeur standard :

- Valide uniquement dans un `unsafe` contexte.
- Les méthodes qui contiennent un `delegate*` paramètre ou un type de retour ne peuvent être appelées qu’à partir d’un `unsafe` contexte.
- Ne peut pas être converti en `object` .
- Ne peut pas être utilisé comme argument générique.
- Peut convertir implicitement `delegate*` en `void*` .
- Peut effectuer une conversion explicite de `void*` en `delegate*` .

Restrictions :

- Les attributs personnalisés ne peuvent pas être appliqués à un `delegate*` ou à l’un de ses éléments.
- Un `delegate*` paramètre ne peut pas être marqué comme`params`
- Un `delegate*` type présente toutes les restrictions d’un type pointeur normal.
- Les opérations arithmétiques sur les pointeurs ne peuvent pas être effectuées directement sur les types de pointeur fonction.

### <a name="function-pointer-syntax"></a>Syntaxe du pointeur de fonction

La syntaxe complète du pointeur fonction est représentée par la grammaire suivante :

```antlr
pointer_type
    : ...
    | funcptr_type
    ;

funcptr_type
    : 'delegate' '*' calling_convention_specifier? '<' funcptr_parameter_list funcptr_return_type '>'
    ;

calling_convention_specifier
    : 'managed'
    | 'unmanaged' ('[' unmanaged_calling_convention ']')?
    ;

unmanaged_calling_convention
    : 'Cdecl'
    | 'Stdcall'
    | 'Thiscall'
    | 'Fastcall'
    | identifier (',' identifier)*

funptr_parameter_list
    : (funcptr_parameter ',')*
    ;

funcptr_parameter
    : funcptr_parameter_modifier? type
    ;

funcptr_return_type
    : funcptr_return_modifier? return_type
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

Si aucun `calling_convention_specifier` n’est fourni, la valeur par défaut est `managed` . L’encodage des métadonnées précis de `calling_convention_specifier` et de ce qui `identifier` sont valides dans le `unmanaged_calling_convention` est couvert dans la [représentation des métadonnées des conventions d’appel](#Metadata-Representation-of-Calling-Conventions).

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
- De _ \_ type funcptr_ `F0` à un _autre \_ type funcptr_ `F1` , à condition que les conditions suivantes soient remplies :
    - `F0`et `F1` ont le même nombre de paramètres, et chaque paramètre `D0n` dans `F0` a les mêmes `ref` `out` modificateurs,, ou `in` que le paramètre correspondant `D1n` dans `F1` .
    - Pour chaque paramètre de valeur (paramètre sans `ref` `out` modificateur, ou `in` ), il existe une conversion d’identité, une conversion de référence implicite ou une conversion de pointeur implicite du type de paramètre dans `F0` le type de paramètre correspondant dans `F1` .
    - Pour chaque `ref` `out` paramètre, ou `in` , le type de paramètre dans `F0` est le même que le type de paramètre correspondant dans `F1` .
    - Si le type de retour est par valeur (no `ref` ou `ref readonly` ), une identité, une référence implicite ou une conversion de pointeur implicite existe du type de retour de `F1` au type de retour de `F0` .
    - Si le type de retour est par référence ( `ref` ou `ref readonly` ), le type de retour et les `ref` modificateurs de `F1` sont les mêmes que le type de retour et les `ref` modificateurs de `F0` .
    - La Convention d’appel de `F0` est identique à la Convention d’appel de `F1` .

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

Dans un contexte non sécurisé, une méthode `M` est compatible avec un type de pointeur de fonction `F` si toutes les conditions suivantes sont vraies :
- `M`et `F` ont le même nombre de paramètres, et chaque paramètre dans `M` a les mêmes `ref` `out` modificateurs,, ou `in` que le paramètre correspondant dans `F` .
- Pour chaque paramètre de valeur (paramètre sans `ref` `out` modificateur, ou `in` ), il existe une conversion d’identité, une conversion de référence implicite ou une conversion de pointeur implicite du type de paramètre dans `M` le type de paramètre correspondant dans `F` .
- Pour chaque `ref` `out` paramètre, ou `in` , le type de paramètre dans `M` est le même que le type de paramètre correspondant dans `F` .
- Si le type de retour est par valeur (no `ref` ou `ref readonly` ), une identité, une référence implicite ou une conversion de pointeur implicite existe du type de retour de `F` au type de retour de `M` .
- Si le type de retour est par référence ( `ref` ou `ref readonly` ), le type de retour et les `ref` modificateurs de `F` sont les mêmes que le type de retour et les `ref` modificateurs de `M` .
- La Convention d’appel de `M` est identique à la Convention d’appel de `F` . Cela comprend le bit de convention d’appel, ainsi que tous les indicateurs de convention d’appel spécifiés dans l’identificateur non managé.
- `M` est une méthode statique.

Dans un contexte non sécurisé, une conversion implicite existe à partir d’une expression d’adresse dont la cible est un groupe `E` de méthodes vers un type de pointeur fonction compatible `F` si `E` contient au moins une méthode applicable dans sa forme normale à une liste d’arguments construite à l’aide des types de paramètres et des modificateurs de `F` , comme décrit dans l’exemple suivant.
- Une méthode unique `M` est sélectionnée correspondant à un appel de méthode de la forme `E(A)` avec les modifications suivantes :
   - La liste `A` d’arguments est une liste d’expressions, chacune classée comme une variable et avec le type et le modificateur ( `ref` , `out` ou `in` ) de _la \_ \_ liste de paramètres funcptr_ correspondante de `F` .
   - Les méthodes candidates sont uniquement les méthodes applicables dans leur forme normale, et non celles applicables dans leur forme développée.
   - Les méthodes candidates sont uniquement les méthodes statiques.
- Si l’algorithme de la résolution de surcharge génère une erreur, une erreur de compilation se produit. Dans le cas contraire, l’algorithme produit une seule meilleure méthode qui `M` a le même nombre de paramètres que `F` et la conversion est considérée comme existante.
- La méthode sélectionnée `M` doit être compatible (comme défini ci-dessus) avec le type de pointeur de fonction `F` . Sinon, une erreur de compilation se produit.
- Le résultat de la conversion est un pointeur de fonction de type `F` .

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

L’opérateur d’adresse sera implémenté à l’aide de l' `ldftn` instruction.

Restrictions de cette fonctionnalité :

- S’applique uniquement aux méthodes marquées comme `static` .
- Les `static` fonctions non locales ne peuvent pas être utilisées dans `&` . Les détails de l’implémentation de ces méthodes ne sont délibérément pas spécifiés par le langage. Cela indique s’ils sont statiques ou d’instance ou exactement la signature avec laquelle ils sont émis.


### <a name="operators-on-function-pointer-types"></a>Opérateurs sur les types de pointeur de fonction

La section du code unsafe sur les opérateurs est modifiée comme suit :

> Dans un contexte non sécurisé, plusieurs constructions sont disponibles pour fonctionner sur tous les _pointer \_ type_s qui ne sont pas _funcptr \_ type_s :
>
> *  L' `*` opérateur peut être utilisé pour effectuer une indirection de pointeur ([indirection de pointeur](unsafe-code.md#pointer-indirection)).
> *  L' `->` opérateur peut être utilisé pour accéder à un membre d’un struct via un pointeur ([accès aux membres du pointeur](unsafe-code.md#pointer-member-access)).
> *  L' `[]` opérateur peut être utilisé pour indexer un pointeur ([accès à un élément pointeur](unsafe-code.md#pointer-element-access)).
> *  L' `&` opérateur peut être utilisé pour obtenir l’adresse d’une variable ([opérateur d’adresse](unsafe-code.md#the-address-of-operator)).
> *  Les `++` `--` opérateurs et peuvent être utilisés pour incrémenter et décrémenter des pointeurs ([incrément et décrémentation de pointeur](unsafe-code.md#pointer-increment-and-decrement)).
> *  Les `+` `-` opérateurs et peuvent être utilisés pour effectuer des opérations arithmétiques sur les pointeurs ([opérations arithmétiques](unsafe-code.md#pointer-arithmetic)sur les pointeurs).
> *  Les `==` `!=` opérateurs,, `<` ,, `>` `<=` et `=>` peuvent être utilisés pour comparer des pointeurs ([Comparaison de pointeur](unsafe-code.md#pointer-comparison)).
> *  L' `stackalloc` opérateur peut être utilisé pour allouer de la mémoire à partir de la pile des appels ([mémoires tampons de taille fixe](unsafe-code.md#fixed-size-buffers)).
> *  L' `fixed` instruction peut être utilisée pour corriger temporairement une variable afin que son adresse puisse être obtenue ([l’instruction fixed](unsafe-code.md#the-fixed-statement)).
> 
> Dans un contexte non sécurisé, plusieurs constructions sont disponibles pour fonctionner sur tous les _funcptr \_ type_s :
> *  L' `&` opérateur peut être utilisé pour obtenir l’adresse des méthodes statiques ([autoriser l’adresse-de aux méthodes cibles](function-pointers.md#allow-address-of-to-target-methods)).
> *  Les `==` `!=` opérateurs,, `<` ,, `>` `<=` et `=>` peuvent être utilisés pour comparer des pointeurs ([Comparaison de pointeur](unsafe-code.md#pointer-comparison)).

En outre, nous modifions toutes les sections dans `Pointers in expressions` pour interdire les types de pointeur de fonction, à l’exception `Pointer comparison` de et `The sizeof operator` .

### <a name="better-function-member"></a>Meilleure fonction membre

La meilleure spécification de membre de fonction sera modifiée pour inclure la ligne suivante :

> Un `delegate*` est plus spécifique que`void*`

Cela signifie qu’il est possible de surcharger sur `void*` et, `delegate*` et d’utiliser judicieusement l’opérateur d’adresse.

## <a name="metadata-representation-of-in-out-and-ref-readonly-parameters-and-return-types"></a>Représentation de métadonnées des `in` `out` paramètres,, et `ref readonly` et des types de retour

Les signatures de pointeur de fonction n’ont pas d’emplacement d’indicateurs de paramètres. nous devons donc Encoder si les paramètres et le type de retour sont `in` , `out` ou à `ref readonly` l’aide de modreqs.

### `in`

Nous utilisons `System.Runtime.InteropServices.InAttribute` la réutilisation, appliquée en tant que `modreq` au spécificateur Ref sur un paramètre ou un type de retour, pour signifier ce qui suit :
* S’il est appliqué à un spécificateur de référence de paramètre, ce paramètre est traité comme `in` .
* S’il est appliqué au spécificateur Ref de type de retour, le type de retour est traité comme `ref readonly` .

### `out`

Nous utilisons `System.Runtime.InteropServices.OutAttribute` , appliqué en tant que `modreq` au spécificateur Ref sur un type de paramètre, pour signifier que le paramètre est un `out` paramètre.

### <a name="errors"></a>Errors

* Il s’agit d’une erreur à appliquer `OutAttribute` en tant que modreq à un type de retour.
* L’application de `InAttribute` et `OutAttribute` en tant que modreq à un type de paramètre est une erreur.
* Si les deux sont spécifiés via modopt, ils sont ignorés.

### <a name="metadata-representation-of-calling-conventions"></a>Représentation des métadonnées des conventions d’appel

Les conventions d’appel sont encodées dans une signature de méthode dans les métadonnées par une combinaison de l' `CallKind` indicateur dans la signature et zéro ou plusieurs `modopt` s au début de la signature. ECMA-335 déclare actuellement les éléments suivants dans l' `CallKind` indicateur :

```antlr
CallKind
   : default
   | unmanaged cdecl
   | unmanaged fastcall
   | unmanaged thiscall
   | unmanaged stdcall
   | varargs
   ;
```

Parmi ces fonctionnalités, les pointeurs de fonction en C# prendront en charge tous sauf `varargs` .

En outre, le runtime (et finalement 335) sera mis à jour pour inclure un nouveau `CallKind` sur les nouvelles plateformes. Cela n’a pas de nom formel actuellement, mais ce document sera utilisé `unmanaged ext` comme espace réservé pour créer le nouveau format de convention d’appel extensible. Avec no `modopt` s, `unmanaged ext` est la Convention d’appel de la plateforme par défaut, `unmanaged` sans les crochets.

#### <a name="mapping-the-calling_convention_specifier-to-a-callkind"></a>Mappage `calling_convention_specifier` de à un`CallKind`

`calling_convention_specifier`Qui est omis, ou spécifié comme `managed` , correspond à `default` `CallKind` . Il s’agit par défaut `CallKind` de toute méthode non attribuée avec `UnmanagedCallersOnly` .

C# reconnaît 4 identificateurs spéciaux qui mappent à des s non managés spécifiques existants `CallKind` à partir d’ECMA 335. Pour que ce mappage se produise, ces identificateurs doivent être spécifiés eux-mêmes, sans aucun autre identificateur, et cette spécification est encodée dans la spécification pour `unmanaged_calling_convention` s. Ces identificateurs sont `Cdecl` , `Thiscall` , `Stdcall` et `Fastcall` , qui correspondent respectivement à `unmanaged cdecl` , `unmanaged thiscall` , `unmanaged stdcall` et `unmanaged fastcall` . Si plusieurs `identifer` identificateurs sont spécifiés, ou si le seul ne `identifier` fait pas partie des identificateurs spécialement reconnus, nous effectuons une recherche de nom spéciale sur l’identificateur avec les règles suivantes :

* Nous allons ajouter la `identifier` chaîne`CallConv`
* Nous examinons uniquement les types définis dans l' `System.Runtime.CompilerServices` espace de noms.
* Nous examinons uniquement les types définis dans la bibliothèque principale de l’application, qui est la bibliothèque qui définit `System.Object` et n’a pas de dépendances.

Si la recherche aboutit sur tous les `identifier` s spécifiés dans un `unmanaged_calling_convention` , nous encodez le `CallKind` en tant que `unmanaged ext` et encodez chacun des types résolus dans le jeu de `modopt` s au début de la signature du pointeur de fonction. En guise de note, ces règles signifient que les utilisateurs ne peuvent pas préfixer ces `identifier` s avec `CallConv` , car cela entraînera la recherche `CallConvCallConvVectorCall` .

Lors de l’interprétation des métadonnées, nous examinons d’abord le `CallKind` . Si ce n’est pas le cas `unmanaged ext` , nous ignorons tous les `modopt` sur le type de retour dans le cadre de la détermination de la Convention d’appel et n’utilisez que le `CallKind` . Si `CallKind` est `unmanaged ext` , nous examinons le modopts au début du type de pointeur de fonction, en prenant l’Union de tous les types qui remplissent les conditions suivantes :

* Est défini dans la bibliothèque principale, qui est la bibliothèque qui ne référence aucune autre bibliothèque et définit `System.Object` .
* Le type est défini dans l' `System.Runtime.CompilerServices` espace de noms.
* Le type commence par le préfixe `CallConv` .

 Celles-ci représentent les types qui doivent être trouvés lors de l’exécution de la recherche sur les `identifier` dans une lors de la `unmanaged_calling_convention` définition d’un type de pointeur fonction dans la source.

La tentative d’utilisation d’un pointeur de fonction avec un `CallKind` de `unmanaged ext` si le runtime cible ne prend pas en charge la fonctionnalité est une erreur. Cela sera déterminé en recherchant la présence de la `System.Runtime.CompilerServices.RuntimeFeature.UnmanagedCallKind` constante. Si cette constante est présente, le runtime est pris en compte pour prendre en charge la fonctionnalité.

### `System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute`

`System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute`est un attribut utilisé par le CLR pour indiquer qu’une méthode doit être appelée avec une convention d’appel spécifique. Pour cette raison, nous présentons la prise en charge suivante de l’utilisation de l’attribut :

* L’appel direct d’une méthode annotée avec cet attribut à partir de C# est une erreur. Les utilisateurs doivent obtenir un pointeur de fonction vers la méthode, puis appeler ce pointeur.
* L’application de l’attribut à autre chose qu’une méthode statique est une erreur. Le compilateur C# marque toutes les méthodes non statiques importées à partir des métadonnées avec cet attribut comme non prises en charge par le langage.
* C’est une erreur d’avoir des types non non managés comme paramètres ou le type de retour d’une méthode marquée avec l’attribut.
* La conversion d’une méthode marquée avec l’attribut en type délégué est une erreur.
* La spécification de tous les types pour `UnmanagedCallersOnly.CallConvs` qui ne satisfont pas aux conditions requises pour l’appel de Convention dans les métadonnées est une erreur `modopt` .

Lors de la détermination de la Convention d’appel d’une méthode marquée avec un `UnmanagedCallersOnly` attribut valide, le compilateur effectue les vérifications suivantes sur les types spécifiés dans la `CallConvs` propriété afin de déterminer les effectifs `CallKind` et les `modopt` à utiliser pour déterminer la Convention d’appel :

* Si aucun type n’est spécifié, `CallKind` est traité comme `unmanaged ext` , sans convention d’appel, `modopt` au début du type de pointeur de fonction.
* Si un type est spécifié et que ce type est nommé `CallConvCdecl` , `CallConvThiscall` , `CallConvStdcall` ou `CallConvFastcall` , le `CallKind` est traité comme `unmanaged cdecl` , `unmanaged thiscall` , `unmanaged stdcall` ou `unmanaged fastcall` , respectivement, sans convention d’appel `modopt` au début du type de pointeur de fonction.
* Si plusieurs types sont spécifiés ou si le type unique n’est pas nommé l’un des types spécialement appelés ci-dessus, le `CallKind` est traité comme `unmanaged ext` , avec l’Union des types spécifiés traités comme `modopt` s au début du type de pointeur de fonction.

Le compilateur examine ensuite cet effet `CallKind` et cette `modopt` collection et utilise des règles de métadonnées normales pour déterminer la Convention d’appel finale du type de pointeur de fonction.

## <a name="open-questions"></a>Questions ouvertes

### <a name="detecting-runtime-support-for-unmanaged-ext"></a>Détection de la prise en charge du runtime pour`unmanaged ext`

https://github.com/dotnet/runtime/issues/38135effectue le suivi de l’ajout de cet indicateur. Selon les commentaires de la revue, nous utilisons la propriété spécifiée dans le problème ou utilisons la présence de `UnmanagedCallersOnlyAttribute` comme indicateur qui détermine si les runtimes prennent en charge `unmanaged ext` .

## <a name="considerations"></a>Considérations

### <a name="allow-instance-methods"></a>Autoriser les méthodes d’instance

La proposition peut être étendue pour prendre en charge les méthodes d’instance en tirant parti de la `EXPLICITTHIS` Convention d’appel CLI (nommée `instance` dans le code C#). Cette forme de pointeurs de fonction CLI place le `this` paramètre en tant que premier paramètre explicite de la syntaxe du pointeur de fonction.

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

C’est un son, mais il ajoute une certaine compliquation à la proposition. En particulier parce que les pointeurs de fonction qui diffèrent par la Convention d’appel `instance` et `managed` seraient incompatibles même si les deux cas sont utilisés pour appeler des méthodes managées avec la même signature C#. Dans tous les cas, dans tous les cas, il est utile de disposer d’une solution de contournement simple : utilisez une `static` fonction locale.

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

Au lieu de demander `unsafe` à chaque utilisation d’un `delegate*` , n’en avez besoin qu’au point où un groupe de méthodes est converti en `delegate*` . C’est là qu’interviennent les principaux problèmes de sécurité (sachant que l’assembly conteneur ne peut pas être déchargé tant que la valeur est active). `unsafe`Le besoin d’un autre emplacement peut être considéré comme excessif.

C’est ainsi que la conception était prévue à l’origine. Mais les règles de langage qui en résultent sont très maladroites. Il est impossible de masquer le fait qu’il s’agit d’une valeur de pointeur et qu’elle reste en lecture seule sans le `unsafe` mot clé. Par exemple, la conversion vers `object` ne peut pas être autorisée, il ne peut pas être membre d’un `class` , etc... La conception C# est nécessaire `unsafe` pour toutes les utilisations de pointeur. par conséquent, cette conception suit cela.

Les développeurs peuvent toujours présenter un wrapper _sécurisé_ sur `delegate*` les valeurs de la même façon que pour les types pointeur normaux. Considérez les aspects suivants :

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a>Utilisation de délégués

Au lieu d’utiliser un nouvel élément de syntaxe, `delegate*` , utilisez simplement `delegate` les types existants avec `*` le type suivant :

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

La gestion de la Convention d’appel peut être effectuée en annotant les `delegate` types avec un attribut qui spécifie une `CallingConvention` valeur. L’absence d’un attribut signifie la Convention d’appel managée.

L’encodage de ce en IL pose problème. La valeur sous-jacente doit être représentée sous la forme d’un pointeur, mais elle doit également :

1. Avoir un type unique pour autoriser les surcharges avec différents types de pointeur de fonction.
1. Être équivalent à des fins de OHI dans les limites de l’assembly.

Le dernier point est particulièrement problématique. Cela signifie que chaque assembly qui utilise `Func<int>*` doit encoder un type équivalent dans les métadonnées même si `Func<int>*` est défini dans un assembly, même si ne contrôle pas.
En outre, tout autre type défini avec le nom `System.Func<T>` dans un assembly qui n’est pas mscorlib doit être différent de la version définie dans mscorlib.

Une option qui a été explorée a émis un tel pointeur comme `mod_req(Func<int>) void*` . Cela ne fonctionne pas tant qu’un `mod_req` ne peut pas être lié à un `TypeSpec` et ne peut donc pas cibler des instanciations génériques.

### <a name="named-function-pointers"></a>Pointeurs de fonction nommés

La syntaxe du pointeur de fonction peut être lourde, en particulier dans les cas complexes comme les pointeurs de fonction imbriqués. Au lieu de demander aux développeurs de taper la signature chaque fois que le langage peut autoriser des déclarations nommées de pointeurs de fonction comme c’est le cas avec `delegate` .

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

Une partie du problème est ici que la primitive CLI sous-jacente n’a pas de nom, ce qui est purement une invention C# et nécessite un peu de métadonnées pour permettre l’activation. C’est réalisable, mais il s’agit d’un travail important. En fait, C# a besoin d’un compagnon pour la table de définition de type pour ces noms uniquement.

De même, lorsque les arguments des pointeurs de fonction nommés ont été examinés, nous avons trouvé qu’ils s’appliquent aussi bien à un certain nombre d’autres scénarios. Par exemple, il serait tout aussi commode de déclarer des tuples nommés pour réduire le besoin de taper la signature complète dans tous les cas.

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

Après la discussion, nous avons décidé de ne pas autoriser la déclaration nommée de `delegate*` types. Si nous constatons qu’il y a des besoins importants en ce qui concerne les commentaires sur l’utilisation des clients, nous étudierons une solution d’attribution de noms qui fonctionne pour les pointeurs de fonction, les tuples, les génériques, etc. Cela peut être similaire à d’autres suggestions, telles que `typedef` la prise en charge complète dans le langage.

## <a name="future-considerations"></a>Éléments futurs à prendre en considération

### <a name="static-delegates"></a>délégués statiques

Cela fait référence à [la proposition](https://github.com/dotnet/csharplang/issues/302) pour permettre la déclaration de `delegate` types qui peuvent uniquement faire référence à des `static` membres. L’avantage est qu' `delegate` il est possible d’allouer gratuitement et mieux les scénarios de performances.

Si la fonctionnalité du pointeur de fonction est implémentée `static delegate` , la proposition sera probablement fermée. L’avantage proposé par cette fonctionnalité est la nature libre de l’allocation. Toutefois, des investigations récentes ont détecté que le déchargement d’assembly n’est pas possible en raison du déchargement de l’assembly. Il doit y avoir un handle fort de l’objet `static delegate` à la méthode à laquelle il fait référence pour empêcher le déchargement de l’assembly à partir de celui-ci.

Pour conserver chaque `static delegate` instance, il est nécessaire d’allouer un nouveau descripteur qui s’exécute sur les objectifs de la proposition. Il existait des conceptions où l’allocation pouvait être amortie en une seule allocation par site d’appel, mais cela était un peu complexe et ne semble pas avoir un intérêt pour le compromis.

Cela signifie que les développeurs doivent essentiellement décider entre les compromis suivants :

1. Sécurité face au déchargement d’un assembly : cela nécessite des allocations et `delegate` est donc déjà une option suffisante.
1. Aucune sécurité en face du déchargement d’assembly : utilisez un `delegate*` . Cela peut être encapsulé dans un `struct` pour permettre l’utilisation en dehors d’un `unsafe` contexte dans le reste du code.
