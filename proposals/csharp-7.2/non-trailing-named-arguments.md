---
ms.openlocfilehash: ac2b233eb703b5eea3bd2dfdbeeadd7494b0c695
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484670"
---
# <a name="non-trailing-named-arguments"></a>Arguments nommés non placés en position de fin

## <a name="summary"></a>Résumé
[summary]: #summary
Autorisez l’utilisation d’arguments nommés en position non finale, à condition qu’ils soient utilisés à leur position correcte. Par exemple : `DoSomething(isEmployed:true, name, age);`.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

La motivation principale est d’éviter de taper des informations redondantes. Il est courant de nommer un argument qui est un littéral (par exemple, `null`, `true`) afin de clarifier le code, plutôt que de passer des arguments dans le désordre.
Cela n’est pas autorisé actuellement (`CS1738`), sauf si tous les arguments suivants sont également nommés.

```csharp
DoSomething(isEmployed:true, name, age); // currently disallowed, even though all arguments are in position
// CS1738 "Named argument specifications must appear after all fixed arguments have been specified"
```

Exemples supplémentaires :
```csharp
public void DoSomething(bool isEmployed, string personName, int personAge) { ... }

DoSomething(isEmployed:true, name, age); // currently CS1738, but would become legal
DoSomething(true, personName:name, age); // currently CS1738, but would become legal
DoSomething(name, isEmployed:true, age); // remains illegal
DoSomething(name, age, isEmployed:true); // remains illegal
DoSomething(true, personAge:age, personName:name); // already legal
```

Cela fonctionne également avec les paramètres :
```csharp
public class Task
{
    public static Task When(TaskStatus all, TaskStatus any, params Task[] tasks);
}
Task.When(all: TaskStatus.RanToCompletion, any: TaskStatus.Faulted, task1, task2)
```

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Dans § 7.5.1 (listes d’arguments), la spécification indique actuellement :
> Un *argument* avec un *argument-Name* est désigné sous le nom d' __argument nommé__, tandis qu’un *argument sans* *argument-Name* est un __argument positionnel__. L’affichage d’un argument positionnel après un argument nommé dans une *liste d’arguments*constitue une erreur.

La proposition consiste à supprimer cette erreur et à mettre à jour les règles de recherche du paramètre correspondant pour un argument (§ 7.5.1.1) :

Arguments dans la liste d’arguments des constructeurs d’instance, des méthodes, des indexeurs et des délégués :
- [règles existantes]
- Un argument sans nom correspond à aucun paramètre quand il se trouve après un argument nommé hors position ou un argument nommé params.

En particulier, cela empêche l’appel de `void M(bool a = true, bool b = true, bool c = true, );` avec `M(c: false, valueB);`. Le premier argument est utilisé en dehors de la position (l’argument est utilisé à la première position, mais le paramètre nommé « c » est en troisième position), donc les arguments suivants doivent être nommés.

En d’autres termes, les arguments nommés non finaux sont uniquement autorisés lorsque le nom et la position entraînent la recherche du même paramètre correspondant.

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Cette proposition aggrave les subtilités existantes avec les arguments nommés dans la résolution de surcharge. Exemple :

```csharp
void M(int x, int y) { }
void M<T>(T y, int x) { }

void M2()
{
    M(3, 4);
    M(y: 3, x: 4); // Invokes M(int, int)
    M(y: 3, 4); // Invokes M<T>(T, int)
}
```

Vous pouvez vous procurer cette situation dès aujourd’hui en échangeant les paramètres :

```csharp
void M(int y, int x) { }
void M<T>(int x, T y) { }

void M2()
{
    M(3, 4);
    M(x: 3, y: 4); // Invokes M(int, int)
    M(3, y: 4); // Invokes M<T>(int, T)
}
```

De même, si vous avez deux méthodes `void M(int a, int b)` et `void M(int x, string y)`, l’invocation de l’invocation `M(x: 1, 2)` produira un diagnostic basé sur la deuxième surcharge (« impossible de convertir «int » en « String »»). Ce problème existe déjà lorsque l’argument nommé est utilisé dans une position de fin.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Il existe deux alternatives à prendre en compte :

- État actuel
- Fournir une assistance IDE pour remplir tous les noms d’arguments de fin lorsque vous tapez un nom spécifique au milieu.

Ces deux éléments souffrent d’un plus grand niveau de détail, car ils introduisent plusieurs arguments nommés, même si vous avez simplement besoin d’un nom de littéral au début de la liste d’arguments.

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

## <a name="design-meetings"></a>Réunions de conception
[ldm]: #ldm
La fonctionnalité a été brièvement abordée dans LDM le 16 mai 2017, avec une approbation en principe (OK pour passer à la proposition/prototype). Il a également été brièvement abordé le 28 juin 2017.

Est lié à la discussion initiale https://github.com/dotnet/csharplang/issues/518 se réfère au problème https://github.com/dotnet/csharplang/issues/570
