---
ms.openlocfilehash: 6cf489595654236c18edee94c0af380e605c9571
ms.sourcegitcommit: f61a06970fa0562d2e40363fae3948eb168624ca
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "79485174"
---
# <a name="private-protected"></a>protégé privé

* [x] proposé
* [x] prototype : [terminé](https://github.com/dotnet/roslyn/blob/master/docs/features/private-protected.md)
* [x] implémentation : [terminée](https://github.com/dotnet/roslyn/blob/master/docs/features/private-protected.md)
* [x] spécification : [terminé](#detailed-design)

## <a name="summary"></a>Résumé
[summary]: #summary

Exposez le niveau d’accessibilité C# du CLR `protectedAndInternal` en tant que `private protected`.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Dans de nombreux cas, une API contient des membres qui sont uniquement destinés à être implémentés et utilisés par les sous-classes contenues dans l’assembly qui fournit le type. Alors que le CLR fournit un niveau d’accessibilité à cet effet, il n’est C#pas disponible dans. Par conséquent, les propriétaires d’API sont obligés d’utiliser la protection de `internal` et l’auto-discipline ou un analyseur personnalisé, ou d’utiliser `protected` avec une documentation supplémentaire qui explique que, alors que le membre apparaît dans la documentation publique du type, il n’est pas destiné à faire partie de l’API publique.  Pour obtenir des exemples de ce dernier, consultez membres de la `CSharpCompilationOptions` de Roslyn dont les noms commencent par `Common`.

Fournir directement la prise en charge de ce C# niveau d’accès dans permet d’exprimer naturellement ces circonstances dans le langage.

## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

### <a name="private-protected-access-modifier"></a>`private protected` (modificateur d’accès)

Nous proposons d’ajouter une nouvelle combinaison de modificateurs d’accès `private protected` (qui peut apparaître dans n’importe quel ordre parmi les modificateurs). Cela correspond à la notion CLR de protectedAndInternal et emprunte la même syntaxe actuellement utilisée dans [ C++/CLI](https://docs.microsoft.com/cpp/dotnet/how-to-define-and-consume-classes-and-structs-cpp-cli#BKMK_Member_visibility).

Un membre déclaré `private protected` est accessible dans une sous-classe de son conteneur si cette sous-classe se trouve dans le même assembly que le membre.

Nous modifions la spécification du langage comme suit (ajouts en gras). Les numéros de section ne sont pas affichés ci-dessous, car ils peuvent varier en fonction de la version de la spécification dans laquelle il est intégré.

-----

> L’accessibilité déclarée d’un membre peut être l’une des suivantes :
- Public, qui est sélectionné en incluant un modificateur public dans la déclaration de membre. La signification intuitive du public est « accès non limité ».
- Protégé, qui est sélectionné en incluant un modificateur protected dans la déclaration de membre. La signification intuitive de protected est « l’accès est limité à la classe conteneur ou aux types dérivés de la classe conteneur ».
- Interne, qui est sélectionné en incluant un modificateur interne dans la déclaration de membre. La signification intuitive de Internal est « accès limité à cet assembly ».
- Protected internal, qui est sélectionné en incluant à la fois un modificateur protected et un modificateur Internal dans la déclaration du membre. La signification intuitive de protected internal est « accessible dans cet assembly et les types dérivés de la classe conteneur ».
- **Private protected, qui est sélectionné en incluant à la fois un modificateur Private et un modificateur protected dans la déclaration du membre. La signification intuitive de la protection privée est « accessible dans cet assembly par les types dérivés de la classe conteneur ».**

-----

> En fonction du contexte dans lequel la déclaration d’un membre a lieu, seuls certains types d’accessibilité déclarés sont autorisés. En outre, lorsqu’une déclaration de membre n’inclut pas de modificateur d’accès, le contexte dans lequel la déclaration a lieu détermine l’accessibilité déclarée par défaut. 
- Les espaces de noms ont implicitement une accessibilité déclarée publique. Aucun modificateur d’accès n’est autorisé sur les déclarations d’espace de noms.
- Les types déclarés directement dans les unités de compilation ou les espaces de noms (par opposition à dans d’autres types) peuvent avoir une accessibilité déclarée publique ou interne et par défaut pour l’accessibilité déclarée interne.
- Les membres de classe peuvent avoir l’un des cinq types d’accessibilité déclarés et la valeur par défaut est l’accessibilité déclarée privée. [Remarque : un type déclaré en tant que membre d’une classe peut avoir l’un des cinq types d’accessibilité déclarés, alors qu’un type déclaré en tant que membre d’un espace de noms ne peut avoir que l’accessibilité déclarée publique ou interne. fin de la note]
- Les membres de struct peuvent avoir une accessibilité déclarée publique, interne ou privée et une accessibilité déclarée privée par défaut, car les structs sont implicitement sealed. Les membres de struct introduits dans un struct (autrement dit, qui n’est pas hérité par ce struct) ne peuvent pas avoir d’accessibilité déclarée protected *,* ~~protected~~ Internal**ou Private protected** . [Remarque : un type déclaré en tant que membre d’un struct peut avoir une accessibilité déclarée publique, interne ou privée, alors qu’un type déclaré en tant que membre d’un espace de noms ne peut avoir que l’accessibilité déclarée publique ou interne. fin de la note]
- Les membres d’interface ont implicitement une accessibilité déclarée publique. Aucun modificateur d’accès n’est autorisé sur les déclarations de membres d’interface.
- Les membres de l’énumération ont implicitement une accessibilité déclarée publique. Aucun modificateur d’accès n’est autorisé sur les déclarations de membre d’énumération.

-----

> Le domaine d’accessibilité d’un membre imbriqué M déclaré dans un type T dans un programme P, est défini comme suit (en notant que M lui-même peut être un type) :
- Si l’accessibilité déclarée de M est publique, le domaine d’accessibilité de M est le domaine d’accessibilité de T.
- Si l’accessibilité déclarée de M est protected internal, commençons par l’Union du texte de programme de P et le texte de programme de n’importe quel type dérivé de T, qui est déclaré en dehors de P. Le domaine d’accessibilité de M est l’intersection du domaine d’accessibilité de T avec D.
- **Si l’accessibilité déclarée de M est protégée par une protection privée, je vais être l’intersection du texte de programme de P et du texte de programme de n’importe quel type dérivé de T. Le domaine d’accessibilité de M est l’intersection du domaine d’accessibilité de T avec D.**
- Si l’accessibilité déclarée de M est protégée, commençons par l’Union du texte de programme T et le texte de programme de n’importe quel type dérivé de T. Le domaine d’accessibilité de M est l’intersection du domaine d’accessibilité de T avec D.
- Si l’accessibilité déclarée de M est interne, le domaine d’accessibilité de M est l’intersection du domaine d’accessibilité de T avec le texte de programme P.
- Si l’accessibilité déclarée de M est privée, le domaine d’accessibilité de M est le texte de programme T.

-----

> Lorsqu’un membre d’instance protégé **ou privé** est accessible en dehors du texte de programme de la classe dans laquelle il est déclaré, et quand un membre d’instance interne protégé est accessible en dehors du texte de programme du programme dans lequel il est déclaré, l’accès doit avoir lieu dans une déclaration de classe qui dérive de la classe dans laquelle il est déclaré. En outre, l’accès doit être effectué par le biais d’une instance de ce type de classe dérivée ou d’un type de classe construit à partir de celui-ci. Cette restriction empêche une classe dérivée d’accéder aux membres protégés d’autres classes dérivées, même lorsque les membres sont hérités de la même classe de base.

-----

> Les modificateurs d’accès autorisés et l’accès par défaut pour une déclaration de type dépendent du contexte dans lequel la déclaration a lieu (§ 9.5.2) :
- Les types déclarés dans les unités de compilation ou les espaces de noms peuvent avoir un accès public ou interne. La valeur par défaut est accès interne.
- Les types déclarés dans les classes peuvent avoir un accès public, protected internal, **Private**protected, protected, Internal ou Private. La valeur par défaut est accès privé.
- Les types déclarés dans des structs peuvent avoir un accès public, Internal ou Private. La valeur par défaut est accès privé.

-----

> Une déclaration de classe statique est soumise aux restrictions suivantes :
- Une classe statique ne doit pas inclure de modificateur sealed ou abstract. (Toutefois, étant donné qu’une classe statique ne peut pas être instanciée ou dérivée de, elle se comporte comme si elle était sealed et abstract.)
- Une classe statique ne doit pas inclure une spécification de classe-base (§ 16.2.5) et ne peut pas spécifier explicitement une classe de base ou une liste d’interfaces implémentées. Une classe statique hérite implicitement de l’objet de type.
- Une classe statique ne doit contenir que des membres statiques (§ 16.4.8). [Remarque : toutes les constantes et tous les types imbriqués sont classés en tant que membres statiques. fin de la note]
- Une classe statique ne doit pas avoir de membres avec l’accessibilité déclarée protégée **, protégée privée** ou protégée interne.

> Il s’agit d’une erreur au moment de la compilation pour enfreindre l’une de ces restrictions. 

-----

> Une déclaration de membre de classe peut avoir l’un des ~~cinq~~types d’accessibilité déclarés**possibles (** § 9.5.2) : public, **Private protected**, protected internal, protected, Internal ou Private. À l’exception**des combinaisons protégées**internes **et privées** protégées, il s’agit d’une erreur au moment de la compilation pour spécifier plusieurs modificateurs d’accès. Quand une déclaration de membre de classe n’inclut pas de modificateur d’accès, Private est pris par défaut.

-----

> Les types non imbriqués peuvent avoir une accessibilité déclarée publique ou interne et avoir une accessibilité déclarée interne par défaut. Les types imbriqués peuvent également avoir ces formes d’accessibilité déclarée, plus une ou plusieurs formes supplémentaires d’accessibilité déclarée, selon que le type conteneur est une classe ou un struct :
- Un type imbriqué déclaré dans une classe peut avoir l’une des ~~cinq~~**formes d'** accessibilité déclarée (public, **privé protégé**, protected internal, protected, Internal ou Private) et, comme les autres membres de classe, par défaut, l’accessibilité déclarée privée.
- Un type imbriqué qui est déclaré dans un struct peut avoir l’une des trois formes d’accessibilité déclarée (public, Internal ou Private) et, comme les autres membres de struct, a comme valeur par défaut l’accessibilité déclarée privée.

-----

> La méthode substituée par une déclaration override est connue sous le nom de méthode de base substituée pour une méthode de substitution M déclarée dans une classe C, la méthode de base substituée est déterminée en examinant chaque type de classe de base C, en commençant par le type de classe de base directe de C et en continuant avec chaque type de classe de base directe successif, jusqu’à ce que dans un type de classe de base donné, au moins une méthode accessible se trouve avec la même signature que M après la substitution des arguments de type. Dans le cadre de la recherche de la méthode de base substituée, une méthode est considérée comme accessible si elle est publique, si elle est protégée, si elle est protégée interne ou si elle est interne **ou privée** et qu' **elle est** déclarée dans le même programme que C.

-----

> L’utilisation de modificateurs d’accesseur est régie par les restrictions suivantes :
- Un accesseur-modifier ne doit pas être utilisé dans une interface ou dans une implémentation de membre d’interface explicite.
- Pour une propriété ou un indexeur qui n’a pas de modificateur override, un accesseur-modifier est autorisé uniquement si la propriété ou l’indexeur a un accesseur get et Set, puis n’est autorisé que sur l’un de ces accesseurs.
- Pour une propriété ou un indexeur qui comprend un modificateur override, un accesseur doit correspondre à l’accesseur-modifier, le cas échéant, de l’accesseur en cours de substitution.
- L’accesseur-modifier doit déclarer une accessibilité qui est strictement plus restrictive que l’accessibilité déclarée de la propriété ou de l’indexeur lui-même. Pour être précis :
  - Si la propriété ou l’indexeur a une accessibilité déclarée publique, l’accesseur-modifier peut être **privé protégé**,, protected internal, Internal, protected ou Private.
  - Si la propriété ou l’indexeur a une accessibilité déclarée de protected internal, l’accesseur-modifier peut être **Private protected**, Internal, protected ou Private.
  - Si la propriété ou l’indexeur a une accessibilité déclarée de Internal ou protected, l’accesseur-modifier est Private **protected ou** Private.
  - **Si la propriété ou l’indexeur a une accessibilité déclarée private protected, l’accesseur-modifier doit être privé.**
  - Si la propriété ou l’indexeur a une accessibilité déclarée de Private, aucun accesseur-modifier ne peut être utilisé.

-----

> Étant donné que l’héritage n’est pas pris en charge pour les structs, l’accessibilité déclarée d’un membre de struct ne peut pas être protected, **protected protected**ou protected internal.

-----

## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Comme pour toutes les fonctionnalités de langage, nous devons déterminer si la complexité supplémentaire de la langue est remboursée dans la clarté supplémentaire offerte par le C# corps des programmes qui tireraient parti de la fonctionnalité.

## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Une alternative consisterait à fournir une API combinant un attribut et un analyseur. L’attribut est placé par le programmeur sur un membre `internal` pour indiquer que le membre est destiné à être utilisé uniquement dans les sous-classes, et l’analyseur vérifie que ces restrictions sont respectées. 

## <a name="unresolved-questions"></a>Questions non résolues
[unresolved]: #unresolved-questions

L’implémentation est en grande partie terminée. Le seul élément de travail ouvert consiste à élaborer une spécification correspondante pour VB.

## <a name="design-meetings"></a>Réunions de conception

TBD
