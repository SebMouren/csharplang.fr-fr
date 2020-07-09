---
ms.openlocfilehash: a26af810ced3fe5a7d7108f38a22d40aa64543f5
ms.sourcegitcommit: cc68af0b2a6e2ef5780eeb43935600b5927ee720
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86157195"
---
# <a name="repeated-attributes-in-partial-members"></a>Attributs répétés dans les membres partiels

## <a name="summary"></a>Résumé

Autorisez chaque déclaration d’un membre partiel à appliquer indépendamment un attribut non marqué avec `[AttributeUsage(AllowMultiple = true)]` , à condition que les arguments d’attribut soient identiques dans toutes les applications.

## <a name="motivation"></a>Motivation

Lors de la prise en compte des attributs présents sur une méthode « partial », le langage Union tous les attributs de toutes les positions correspondantes sur les deux déclarations. Par exemple, la méthode `M` ci-dessous a des attributs `A` et `B` .

```cs
[A]
partial void M();

[B]
partial void M() { }
```

Cela signifie que les attributs qui ne sont pas marqués `[AttributeUsage(AllowMultiple = true)]` ne peuvent pas être présents dans les deux parties :

```cs
[A]
partial void M();

[A] // error: duplicate attribute!
partial void M() { }
```

Cela présente un problème d’utilisation/de lisibilité, car certains attributs sont conçus pour informer l’utilisateur et/ou le chargé de maintenance de la méthode des pré/post-conditions ou des invariantes requis par la méthode. Par exemple :

```cs
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue(out object? value) { ... }
```

Un membre partiel facilite généralement la relation entre un générateur de code et un utilisateur final--chaque tiers fournit l’une des déclarations du membre partiel afin qu’un générateur de code fournisse des fonctionnalités à l’utilisateur ou que l’utilisateur puisse accéder à un point d’extension dans le code généré. Dans le cas où une seule déclaration est autorisée à avoir ces attributs d’application unique, le générateur et l’utilisateur ne peuvent pas communiquer efficacement leurs exigences. Si un générateur produit une déclaration de définition avec un `NotNullWhen` attribut, par exemple, l’utilisateur ne peut pas écrire de déclaration d’implémentation avec le même attribut, même si la post-condition est applicable à l’implémentation et vérifiée par le compilateur. Cela crée une confusion pour les utilisateurs lorsqu’ils effectuent le suivi des causes racines des avertissements ou lorsque vous essayez de comprendre les comportements d’une méthode.

## <a name="solution"></a>Solution

Autorisez l’utilisation d’un attribut non-AllowMultiple une seule fois dans chaque déclaration partielle, à condition que les arguments d’attribut soient identiques. Étant donné que les arguments d’attribut sont toutes des constantes, cela ne doit pas être très difficile à vérifier au moment de la compilation. Lorsque les attributs sont unions entre les déclarations, chaque attribut non-AllowMultiple est dédupliqué et une seule instance de l’attribut est émise.

```cs
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue([NotNullWhen(true)] out object? value) { ... } // ok

// equivalent to:
public bool TryGetValue([NotNullWhen(true)] out object value) { ... }

// error when attribute arguments do not match
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue([NotNullWhen(false)] out object? value) { ... } // error
```

### <a name="open-questions"></a>Questions ouvertes

1. Une telle répétition des attributs doit-elle être autorisée sur les déclarations de type « partiels » ou uniquement sur les membres qui ne sont pas de type (par exemple, méthodes) ?
2. Les attributs qui autorisent l’utilisation de plusieurs utilisations sur un symbole doivent *-ils être* autorisés à déduplication des utilisations équivalentes d’un attribut ?

### <a name="design-meetings"></a>Réunions de conception
#### <a name="6th-july-2020"></a>[6 juillet 2020](/meetings/2020/LDM-2020-07-06.md#repeated-attributes-on-partial-members)
La proposition est acceptée.
  - La répétition des attributs non-AllowMultiple est autorisée dans les déclarations de type partiel (Open question 1).
  - L’application répétée d’attributs AllowMultiple ne changera pas dans le comportement, et un mécanisme d’abonnement pour la déduplication peut être envisagé dans une prochaine proposition (Open question 2).
