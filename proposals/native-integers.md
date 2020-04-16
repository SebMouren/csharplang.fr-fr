---
ms.openlocfilehash: 42839a8c233468dd0b5ec6dad436dc71f056a6d9
ms.sourcegitcommit: d414836632ba2730545e0b058373a94696bba5e4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81447817"
---
# <a name="native-sized-integers"></a>Intégrants de taille autochtone

## <a name="summary"></a>Résumé
[summary]: #summary

Soutien linguistique pour les types d’intégreurs signés et non signés de taille autochtone.

La motivation est pour les scénarios interop et pour les bibliothèques de bas niveau.

## <a name="design"></a>Conception
[design]: #design

Les identificateurs `nint` et `nuint` sont de nouveaux mots-clés contextuels qui représentent les types d’integer signés et non signés natifs.
Les identificateurs ne sont traités comme des mots clés que lorsque le lookup nom ne trouve pas de résultat viable à cet emplacement du programme.
```C#
nint x = 3;
string y = nameof(nuint);
_ = nint.Equals(x, 3);
```

Les `nint` types `nuint` et sont représentés `System.IntPtr` `System.UIntPtr` par les types sous-jacents et avec compilateur revêtement conversions supplémentaires et les opérations pour ces types en tant qu’ints indigènes.

### <a name="constants"></a>Constantes

Les expressions constantes `nint` `nuint`peuvent être de type ou .
Il n’y a pas de syntaxe directe pour les littérals int indigènes. Des moulages implicites ou explicites d’autres valeurs constantes intégrales peuvent plutôt être utilisés : `const nint i = (nint)42;`.

`nint`constantes sont dans `int.MinValue`la `int.MaxValue` gamme [ , ].

`nuint`constantes sont dans `uint.MinValue`la `uint.MaxValue` gamme [ , ].

Il n’y a `MinValue` pas ou `MaxValue` champs sur `nint` ou `nuint` parce que, autre que, `nuint.MinValue`ces valeurs ne peuvent pas être émises comme des constantes.

Le pliage constant est pris `+`en `-` `~` charge pour tous `+` `-`les `*` `/`opérateurs `%` `==`nonaires, et les opérateurs binaires, et les opérateurs binaires, et , , , , `!=` `<`, , , `<=`, `>` `>=`, `&`, `^` `<<` `>>` `|`
Les opérations de pliage `Int32` `UInt32` constantes sont évaluées avec et opérandes plutôt que des ints indigènes pour un comportement cohérent indépendamment de la plate-forme de compilateur.
Si l’opération se traduit par une valeur constante en 32 bits, le pliage constant est effectué au temps de compilation.
Dans le cas contraire, l’opération est exécutée au moment de la course et n’est pas considérée comme une constante.

### <a name="conversions"></a>Conversions
Il y a `nint` une `IntPtr`conversion `nuint` d’identité entre et , et entre et `UIntPtr`.
Il y a une conversion d’identité entre les types de composés qui diffèrent par les ints indigènes et les types sous-jacents seulement : les tableaux, `Nullable<>`les types construits, et les tuples.

Les tableaux ci-dessous couvrent les conversions entre les types spéciaux.
(L’IL pour chaque conversion `unchecked` comprend `checked` les variantes et les contextes si différent.)

| Opérande | Cible | Conversion | IL |
|:---:|:---:|:---:|:---:|
| `object` | `nint` | Unboxing | `unbox` |
| `void*` | `nint` | PointerToVoid | `conv.i` |
| `sbyte` | `nint` | ImpliciteNumeric | `conv.i` |
| `byte` | `nint` | ImpliciteNumeric | `conv.u` |
| `short` | `nint` | ImpliciteNumeric | `conv.i` |
| `ushort` | `nint` | ImpliciteNumeric | `conv.u` |
| `int` | `nint` | ImpliciteNumeric | `conv.i` |
| `uint` | `nint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `long` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `ulong` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `char` | `nint` | ImpliciteNumeric | `conv.i` |
| `float` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `double` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `decimal` | `nint` | ExplicitNumeric | `long decimal.op_Explicit(decimal) conv.i` / `... conv.ovf.i` |
| `IntPtr` | `nint` | Identité | |
| `UIntPtr` | `nint` | None | |
| `object` | `nuint` | Unboxing | `unbox` |
| `void*` | `nuint` | PointerToVoid | `conv.u` |
| `sbyte` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `byte` | `nuint` | ImpliciteNumeric | `conv.u` |
| `short` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `ushort` | `nuint` | ImpliciteNumeric | `conv.u` |
| `int` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `uint` | `nuint` | ImpliciteNumeric | `conv.u` |
| `long` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `ulong` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `char` | `nuint` | ImpliciteNumeric | `conv.u` |
| `float` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `double` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `decimal` | `nuint` | ExplicitNumeric | `ulong decimal.op_Explicit(decimal) conv.u` / `... conv.ovf.u.un`  |
| `IntPtr` | `nuint` | None | |
| `UIntPtr` | `nuint` | Identité | |

| Opérande | Cible | Conversion | IL |
|:---:|:---:|:---:|:---:|
| `nint` | `object` | Boxing | `box` |
| `nint` | `void*` | PointerToVoid | `conv.i` |
| `nint` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `nint` | `sbyte` | ExplicitNumeric | `conv.i1` / `conv.ovf.i1` |
| `nint` | `byte` | ExplicitNumeric | `conv.u1` / `conv.ovf.u1` |
| `nint` | `short` | ExplicitNumeric | `conv.i2` / `conv.ovf.i2` |
| `nint` | `ushort` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2` |
| `nint` | `int` | ExplicitNumeric | `conv.i4` / `conv.ovf.i4` |
| `nint` | `uint` | ExplicitNumeric | `conv.u4` / `conv.ovf.u4` |
| `nint` | `long` | ImpliciteNumeric | `conv.i8` / `conv.ovf.i8` |
| `nint` | `ulong` | ExplicitNumeric | `conv.i8` / `conv.ovf.i8` |
| `nint` | `char` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2` |
| `nint` | `float` | ImpliciteNumeric | `conv.r4` |
| `nint` | `double` | ImpliciteNumeric | `conv.r8` |
| `nint` | `decimal` | ImpliciteNumeric | `conv.i8 decimal decimal.op_Implicit(long)` |
| `nint` | `IntPtr` | Identité | |
| `nint` | `UIntPtr` | None | |
| `nuint` | `object` | Boxing | `box` |
| `nuint` | `void*` | PointerToVoid | `conv.u` |
| `nuint` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `nuint` | `sbyte` | ExplicitNumeric | `conv.i1` / `conv.ovf.i1` |
| `nuint` | `byte` | ExplicitNumeric | `conv.u1` / `conv.ovf.u1` |
| `nuint` | `short` | ExplicitNumeric | `conv.i2` / `conv.ovf.i2` |
| `nuint` | `ushort` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2` |
| `nuint` | `int` | ExplicitNumeric | `conv.i4` / `conv.ovf.i4` |
| `nuint` | `uint` | ExplicitNumeric | `conv.u4` / `conv.ovf.u4` |
| `nuint` | `long` | ExplicitNumeric | `conv.i8` / `conv.ovf.i8` |
| `nuint` | `ulong` | ImpliciteNumeric | `conv.u8` / `conv.ovf.u8` |
| `nuint` | `char` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2.un` |
| `nuint` | `float` | ImpliciteNumeric | `conv.r.un conv.r4` |
| `nuint` | `double` | ImpliciteNumeric | `conv.r.un conv.r8` |
| `nuint` | `decimal` | ImpliciteNumeric | `conv.u8 decimal decimal.op_Implicit(ulong)` |
| `nuint` | `IntPtr` | None | |
| `nuint` | `UIntPtr` | Identité | |

Conversion `A` à `Nullable<B>` partir de est:
- une conversion implicite in annulable s’il `A` y `B`a une conversion d’identité ou une conversion implicite à ;
- une conversion annulée explicite s’il `A` y `B`a une conversion explicite de ;
- autrement invalide.

Conversion `Nullable<A>` à `B` partir de est:
- une conversion annulée explicite s’il y a une `A` conversion `B`d’identité ou une conversion numérique implicite ou explicite de ;
- autrement invalide.

Conversion `Nullable<A>` à `Nullable<B>` partir de est:
- une conversion d’identité s’il y a une conversion d’identité de `A` ; `B`
- une conversion nulle explicite s’il y a `A` `B`une conversion numérique implicite ou explicite de ;
- autrement invalide.

### <a name="operators"></a>Opérateurs

Les opérateurs prédéfinis sont les suivants.
Ces opérateurs sont considérés lors de la résolution de surcharge basée sur des règles normales pour les conversions implicites _si au moins l’un des `nint` opérandes est de type ou `nuint` _.

(L’IL pour chaque opérateur `unchecked` comprend `checked` les variantes et les contextes si différent.)

| Unaire | Signature de l’opérateur | IL |
|:---:|:---:|:---:|
| `+` | `nint operator +(nint value)` | `nop` |
| `+` | `nuint operator +(nuint value)` | `nop` |
| `-` | `nint operator -(nint value)` | `neg` |
| `~` | `nint operator ~(nint value)` | `not` |
| `~` | `nuint operator ~(nuint value)` | `not` |

| Binary | Signature de l’opérateur | IL |
|:---:|:---:|:---:|
| `+` | `nint operator +(nint left, nint right)` | `add` / `add.ovf` |
| `+` | `nuint operator +(nuint left, nuint right)` | `add` / `add.ovf.un` |
| `-` | `nint operator -(nint left, nint right)` | `sub` / `sub.ovf` |
| `-` | `nuint operator -(nuint left, nuint right)` | `sub` / `sub.ovf.un` |
| `*` | `nint operator *(nint left, nint right)` | `mul` / `mul.ovf` |
| `*` | `nuint operator *(nuint left, nuint right)` | `mul` / `mul.ovf.un` |
| `/` | `nint operator /(nint left, nint right)` | `div` |
| `/` | `nuint operator /(nuint left, nuint right)` | `div.un` |
| `%` | `nint operator %(nint left, nint right)` | `rem` |
| `%` | `nuint operator %(nuint left, nuint right)` | `rem.un` |
| `==` | `bool operator ==(nint left, nint right)` | `beq` / `ceq` |
| `==` | `bool operator ==(nuint left, nuint right)` | `beq` / `ceq` |
| `!=` | `bool operator !=(nint left, nint right)` | `bne` |
| `!=` | `bool operator !=(nuint left, nuint right)` | `bne` |
| `<` | `bool operator <(nint left, nint right)` | `blt` / `clt` |
| `<` | `bool operator <(nuint left, nuint right)` | `blt.un` / `clt.un` |
| `<=` | `bool operator <=(nint left, nint right)` | `ble` |
| `<=` | `bool operator <=(nuint left, nuint right)` | `ble.un` |
| `>` | `bool operator >(nint left, nint right)` | `bgt` / `cgt` |
| `>` | `bool operator >(nuint left, nuint right)` | `bgt.un` / `cgt.un` |
| `>=` | `bool operator >=(nint left, nint right)` | `bge` |
| `>=` | `bool operator >=(nuint left, nuint right)` | `bge.un` |
| `&` | `nint operator &(nint left, nint right)` | `and` |
| `&` | `nuint operator &(nuint left, nuint right)` | `and` |
| <code>&#124;</code> | <code>nint operator &#124;(nint left, nint right)</code> | `or` |
| <code>&#124;</code> | <code>nuint operator &#124;(nuint left, nuint right)</code> | `or` |
| `^` | `nint operator ^(nint left, nint right)` | `xor` |
| `^` | `nuint operator ^(nuint left, nuint right)` | `xor` |
| `<<` | `nint operator <<(nint left, int right)` | `shl` |
| `<<` | `nuint operator <<(nuint left, int right)` | `shl` |
| `>>` | `nint operator >>(nint left, int right)` | `shr` |
| `>>` | `nuint operator >>(nuint left, int right)` | `shr.un` |

Pour certains opérateurs binaires, les opérateurs de l’IL prennent en charge d’autres types d’opéra (voir [ECMA-335](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf) III.1.5 Tableau de type Operand).
Mais l’ensemble des types d’opéras soutenus par le C est limité pour la simplicité et la cohérence avec les opérateurs existants dans la langue.

Les versions levées des opérateurs, où les arguments et les types de retour sont `nint?` et, `nuint?`sont pris en charge.

Les opérations `x op= y` `x` d’affectation composées où les `y` ints indigènes suivent les mêmes règles qu’avec d’autres types primitifs avec des opérateurs prédéfinis.
Plus précisément, l’expression est liée `x = (T)(x op y)` comme où `T` est le type et où `x` `x` n’est évalué qu’une seule fois.

Les opérateurs de quart doivent masquer le nombre de `sizeof(nint)` bits à déplacer - à `sizeof(nint)` 5 bits si est de 4, et à 6 bits si est de 8.
(voir [opérateurs de quarts](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#shift-operators) dans C spec).

### <a name="dynamic"></a>Dynamique

Les conversions et les opérateurs sont synthétisés par le `IntPtr` `UIntPtr` compilateur et ne font pas partie du sous-jacent et des types.
En conséquence, ces conversions et les opérateurs ne `dynamic`sont pas _disponibles_ à partir du classeur de temps d’exécution pour . 

```C#
nint x = 2;
nint y = x + x; // ok
dynamic d = x;
nint z = d + x; // RuntimeBinderException: '+' cannot be applied 'System.IntPtr' and 'System.IntPtr'
```

### <a name="type-members"></a>Membres de type

Le seul constructeur `nint` `nuint` pour ou est le constructeur sans paramètres.

Les membres `System.IntPtr` suivants `System.UIntPtr` et sont `nint` explicitement `nuint` _exclus_ ou :
```C#
// constructors
// arithmetic operators
// implicit and explicit conversions
public static readonly IntPtr Zero; // use 0 instead
public static int Size { get; }     // use sizeof() instead
public static IntPtr Add(IntPtr pointer, int offset);
public static IntPtr Subtract(IntPtr pointer, int offset);
public int ToInt32();
public long ToInt64();
public void* ToPointer();
```

Les autres `System.IntPtr` membres `System.UIntPtr` et _sont implicitement inclus_ dans `nint` et `nuint`. Pour .NET Framework 4.7.2:
```C#
public override bool Equals(object obj);
public override int GetHashCode();
public override string ToString();
public string ToString(string format);
```

Les interfaces `System.IntPtr` `System.UIntPtr` mises en œuvre par et _sont implicitement incluses_ dans `nint` et, `nuint`avec des occurrences des types sous-jacents remplacés par les types d’intégreurs indigènes correspondants.
Par `IntPtr` exemple, `ISerializable, IEquatable<IntPtr>, IComparable<IntPtr>`si `nint` les `ISerializable, IEquatable<nint>, IComparable<nint>`impléments , puis implémente .

### <a name="overriding-hiding-and-implementing"></a>Dépassement, dissimulation et mise en œuvre

`nint`et, `System.IntPtr` `nuint` et `System.UIntPtr`, sont considérés comme équivalents pour la prépondérer, la dissimulation et la mise en œuvre.

Les surcharges ne `nint` `System.IntPtr`peuvent `nuint` pas `System.UIntPtr`différer par et, et, à elle seule.
Les remplacements et les `nint` implémentations peuvent différer d’ici et, `System.IntPtr` `nuint` `System.UIntPtr`ou, seul.
Les méthodes cachent `nint` d’autres `System.UIntPtr`méthodes qui diffèrent par et, `System.IntPtr`ou, `nuint` seul.

### <a name="miscellaneous"></a>Divers

`nint`et `nuint` les expressions utilisées comme indices de tableau sont émises sans conversion.
```C#
static object GetItem(object[] array, nint index)
{
    return array[index]; // ok
}
```

`nint`et `nuint` peut être `enum` utilisé comme type de base.
```C#
enum E : nint // ok
{
}
```

Lectures et écrits `nint`sont `nuint`atomiques pour les types , , et `enum` avec le type `nint` de base ou `nuint`.

Les champs `volatile` peuvent `nint` être `nuint`marqués pour les types et .
[ECMA-334](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-334.pdf) 15.5.4 n’inclut `enum` `System.IntPtr` pas `System.UIntPtr` avec le type de base ou cependant.

`default(nint)`et `new nint()` sont `(nint)0`équivalents à .

`typeof(nint)` a la valeur `typeof(IntPtr)`.

`sizeof(nint)`est pris en charge, mais nécessite la `sizeof(IntPtr)`compilation dans un contexte dangereux (comme le fait ).
La valeur n’est pas une constante de compilateur.
`sizeof(nint)`est mis `sizeof(IntPtr)` en `IntPtr.Size`œuvre comme plutôt que .

Compilateur de diagnostics `nint` pour `nuint` `nint` les `nuint` références `IntPtr` `UIntPtr`de type impliquant ou de rapport ou plutôt ou .

### <a name="metadata"></a>Métadonnées

`nint`et `nuint` sont représentés dans `System.IntPtr` les `System.UIntPtr`métadonnées au fur et à mesure.

Type de `nint` références `nuint` qui incluent `System.Runtime.CompilerServices.NativeIntegerAttribute` ou sont émises avec un pour indiquer quelles parties de la référence de type sont des ints indigènes.

```C#
namespace System.Runtime.CompilerServices
{
    [AttributeUsage(
        AttributeTargets.Class |
        AttributeTargets.Event |
        AttributeTargets.Field |
        AttributeTargets.GenericParameter |
        AttributeTargets.Parameter |
        AttributeTargets.Property |
        AttributeTargets.ReturnValue,
        AllowMultiple = false,
        Inherited = false)]
    public sealed class NativeIntegerAttribute : Attribute
    {
        public NativeIntegerAttribute()
        {
            TransformFlags = new[] { true };
        }
        public NativeIntegerAttribute(bool[] flags)
        {
            TransformFlags = flags;
        }
        public IList<bool> TransformFlags { get; }
    }
}
```

L’encodage utilise l’approche utilisée pour coder `DynamicAttribute`, bien qu’il soit évident `DynamicAttribute` qu’il s’agit d’encoder quels types de référence sont `dynamic` plutôt que les types d’ints natifs.
Si l’encodage `false` donne lieu `NativeIntegerAttribute` à un éventail de valeurs, aucun n’est nécessaire.
Le constructeur `NativeIntegerAttribute` sans paramètres génère un codage d’une seule `true` valeur.

```C#
nuint A;                    // [NativeInteger] UIntPtr A
(Stream, nint) B;           // [NativeInteger(new[] { false, false, true })] ValueType<Stream, IntPtr> B
```

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Une alternative à l’approche de « l’effacement `System.NativeInt` `System.NativeUInt`de type » ci-dessus est d’introduire de nouveaux types : et .
```C#
public readonly struct NativeInt
{
    public IntPtr Value;
}
```
Les types distincts permettraient `IntPtr` une surcharge distincte de `ToString()`l’analyse et de l’analyse distinctes et.
Mais il y aurait plus de travail pour le CLR pour gérer ces types efficacement qui va à l’encontre de l’objectif principal de la fonctionnalité - l’efficacité.
Et interop avec le code `IntPtr` int indigène existant qui utilise serait plus difficile. 

Une autre alternative est d’ajouter `IntPtr` plus de soutien int natif pour dans le cadre, mais sans aucun support compilateur spécifique.
Toute nouvelle conversion et opérations d’arithmétique seraient prises en charge automatiquement par le compilateur.
Mais la langue ne fournirait pas `checked` de mots clés, de constantes ou d’opérations.

## <a name="design-meetings"></a>Réunions de conception

https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-26.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-13.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-07-05.md#native-int-and-intptr-operators https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-10-23.md https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md
