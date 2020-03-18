---
ms.openlocfilehash: 2070cf3b3269585055791adc3427cbd134df444d
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484656"
---
# <a name="pattern-based-fixed-statement"></a>Instruction `fixed` basée sur un modèle

## <a name="summary"></a>Résumé
[summary]: #summary

Introduisez un modèle qui permettrait aux types de participer aux instructions `fixed`. 

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Le langage fournit un mécanisme permettant d’épingler les données managées et d’obtenir un pointeur natif vers la mémoire tampon sous-jacente.

```csharp
fixed(byte* ptr = byteArray)
{
   // ptr is a native pointer to the first element of the array
   // byteArray is protected from being moved/collected by the GC for the duration of this block 
}

```

L’ensemble de types qui peut participer à `fixed` est codé en dur et limité aux tableaux et aux `System.String`. Les types « spéciaux » en dur ne sont pas mis à l’échelle quand de nouvelles primitives telles que `ImmutableArray<T>`, `Span<T>``Utf8String` sont introduites. 

En outre, la solution actuelle pour `System.String` s’appuie sur une API assez rigide. La forme de l’API implique que `System.String` est un objet contigu qui incorpore des données encodées en UTF16 à un offset fixe à partir de l’en-tête de l’objet. Une telle approche a été trouvée problématique dans plusieurs propositions qui pourraient nécessiter des modifications de la disposition sous-jacente. Il serait souhaitable de pouvoir basculer vers une solution plus souple qui découple `System.String` objet de sa représentation interne à des fins d’interopérabilité non managée. 

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

## <a name="pattern"></a>*Modèle* ##
Un modèle viable, basé sur un modèle, A besoin d’effectuer les opérations suivantes :
-   Fournissez les références managées pour épingler l’instance et initialiser le pointeur (de préférence, il s’agit de la même référence)
-   Transmettent sans ambiguïté le type de l’élément non managé (par exemple, « char » pour « String »)
-   Prescrire le comportement dans le cas « vide » quand il n’y a rien à référencer. 
-   Ne doit pas pousser les auteurs d’API vers des décisions de conception qui ont un impact sur l’utilisation du type en dehors de `fixed`.

Je pense que ce qui précède peut être respecté en reconnaissant un membre de retour de référence spécialement nommé : `ref [readonly] T GetPinnableReference()`.

Pour pouvoir être utilisé par l’instruction `fixed`, les conditions suivantes doivent être remplies :

1. Il n’y a qu’un seul membre de ce type fourni pour un type.
1. Retourne par `ref` ou `ref readonly`. (`readonly` est autorisé afin que les auteurs de types immuables/ReadOnly puissent implémenter le modèle sans ajouter une API inscriptible pouvant être utilisée dans du code sécurisé)
1. T est un type non managé.
(étant donné que `T*` devient le type pointeur. La restriction se développe naturellement si/lorsque la notion de « non géré » est développée)
1. Retourne les `nullptr` managés lorsqu’il n’y a aucune donnée à épingler, ce qui est probablement le moyen le plus économique pour transmettre des éléments de vidage.
(Notez que « » la chaîne retourne une référence à «\ 0 », car les chaînes se terminent par un caractère null)

Pour le `#3`, nous pouvons également autoriser le résultat dans les cas vides à être non défini ou spécifique à l’implémentation. Cela peut toutefois rendre l’API plus dangereuse et sujette à des abus et à des charges de compatibilité involontaires. 

## <a name="translation"></a>*Traduction* ##

```csharp
fixed(byte* ptr = thing)
{ 
    // <BODY>
}
```

devient le pseudo-code suivant (pas tous C#les éléments exprimables dans)

```csharp
byte* ptr;
// specially decorated "pinned" IL local slot, not visible to user code.
pinned ref byte _pinned;

try
{
    // NOTE: null check is omitted for value types 
    // NOTE: `thing` is evaluated only once (temporary is introduced if necessary) 
    if (thing != null)
    {
        // obtain and "pin" the reference
        _pinned = ref thing.GetPinnableReference();

        // unsafe cast in IL
        ptr = (byte*)_pinned;
    }
    else
    {
        ptr = default(byte*);
    }

    // <BODY> 
}
finally   // finally can be omitted when not observable
{
    // "unpin" the object
    _pinned = nullptr;
}
```

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

- GetPinnableReference est destiné à être utilisé uniquement dans `fixed`, mais rien n’empêche son utilisation dans du code sécurisé. par conséquent, l’implémenteur doit garder cela à l’esprit.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Les utilisateurs peuvent introduire GetPinnableReference ou un membre similaire et l’utiliser en tant que
 
```csharp
fixed(byte* ptr = thing.GetPinnableReference())
{ 
    // <BODY>
}
```

Il n’existe aucune solution pour `System.String` si une autre solution est souhaitée.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

- [] Comportement dans l’état « vide ». - `nullptr` ou `undefined` ? 
- [] Les méthodes d’extension doivent-elles être prises en compte ? 
- [] Si un modèle est détecté sur `System.String`, dois-je le gagner ? 

## <a name="design-meetings"></a>Réunions de conception

Aucun. 
