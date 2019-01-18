---
ms.openlocfilehash: ab41a3c99f79c4cc70f7d4720f7e53b91a410859
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2019
ms.locfileid: "49640897"
---
# <a name="introduction"></a>Introduction

C# (prononcé « C Sharp ») est un langage de programmation simple, moderne, orienté objet et de type sécurisé. C# a ses racines dans la famille de langages C et sera immédiatement reconnaissable aux programmeurs en C, C++ et Java. C# est normalisé par ECMA International en tant que le ***ECMA-334*** standard et par la norme ISO/IEC comme le ***ISO/CEI 23270*** standard. Compilateur c# de Microsoft pour le .NET Framework est une implémentation conforme de ces deux normes.

C# est un langage orienté objet, mais C# inclut de plus la prise en charge de la programmation ***orientée composant***. La conception logicielle moderne s’appuie de plus en plus sur les composants logiciels sous la forme de packages de fonctionnalités autonomes et autodescriptifs. Point important, ces composants présentent un modèle de programmation avec propriétés, méthodes et événements ; ils ont des attributs qui fournissent des informations déclaratives sur le composant ; et ils intègrent leur propre documentation. C# fournit des constructions de langage qui prennent directement en charge ces concepts, rendre c# un langage très naturel permettant de créer et utiliser des composants logiciels.

Plusieurs C# fonctionnalités d’aide dans la construction d’applications robustes et fiables : ***Le garbage collection*** récupère automatiquement la mémoire occupée par les objets inutilisés ; ***gestion des exceptions*** offre une approche structurée et extensible pour la détection d’erreur et de récupération ; et le ***type-safe*** conception du langage ne permet pas de lire à partir de variables non initialisées, pour indexer des tableaux au-delà de leurs limites, ou réaliser unchecked casts de type.

C# a un ***système de type unifié***. Tous les types C#, y compris les types primitifs tels que `int` et `double`, héritent d’un seul type `object` racine. Par conséquent, tous les types partagent un ensemble d’opérations communes, et des valeurs de tous types peuvent être stockées, transmises et exploitées de manière cohérente. En outre, C# prend en charge les types référence et les types valeur définis par l’utilisateur, ce qui permet l’allocation dynamique d’objets, ainsi que le stockage en ligne de structures légères.

Pour vous assurer que les bibliothèques et les programmes c# peuvent évoluer au fil du temps de manière compatible, l’accent a été placé sur ***versioning*** dans la conception de #. De nombreux langages de programmation n’accordent que peu d’attention à ce problème ; par conséquent, les programmes écrits dans ces langages s’arrêtent plus souvent que nécessaire lors de l’introduction de versions plus récentes des bibliothèques dépendantes. Aspects de conception de # qui ont été directement influencés par les considérations relatives à la gestion des versions incluent le distinct `virtual` et `override` modificateurs, les règles de résolution de surcharge de méthode et la prise en charge pour les déclarations de membre d’interface explicite.

Le reste de ce chapitre décrit les principales fonctionnalités du langage c#. Bien que les chapitres suivants décrivent les règles et les exceptions de manière très détaillée et parfois mathématique, ce chapitre s’efforce de clarté et la concision au détriment d’exhaustivité. L’objectif est de fournir au lecteur une introduction au langage qui facilite l’écriture de programmes précoce et la lecture des chapitres.

## <a name="hello-world"></a>Hello World

Le programme « Hello, World » est souvent utilisé pour présenter un langage de programmation. Le voici en C# :

```csharp
using System;

class Hello
{
    static void Main() {
        Console.WriteLine("Hello, World");
    }
}
```

Les fichiers sources C# ont généralement l’extension de fichier `.cs`. En supposant que le programme « Hello, World » est stocké dans le fichier `hello.cs`, le programme peut être compilé avec le compilateur Microsoft c# à l’aide de la ligne de commande
```
csc hello.cs
```
ce qui génère un assembly exécutable nommé `hello.exe`. La sortie générée par cette application lorsqu’elle est exécutée
```
Hello, World
```

Le programme « Hello, World » commence par une directive `using` qui fait référence à l’espace de noms `System`. Les espaces de noms représentent un moyen hiérarchique d’organiser les bibliothèques et les programmes C#. Les espaces de noms contiennent des types et d’autres espaces de noms ; par exemple, l’espace de noms `System` contient plusieurs types, notamment la classe `Console` référencée dans le programme, et d’autres espaces de noms, tels que `IO` et `Collections`. Une directive `using` qui fait référence à un espace de noms donné permet l’utilisation non qualifiée des types membres de cet espace de noms. En raison de la directive `using`, le programme peut utiliser `Console.WriteLine` comme raccourci pour `System.Console.WriteLine`.

La classe `Hello` déclarée par le programme « Hello, World » a un membre unique, la méthode nommée `Main`. Le `Main` méthode est déclarée avec le `static` modificateur. Si les méthodes d’instance peuvent faire référence à une instance d’objet englobante particulière avec le mot clé `this`, les méthodes statiques fonctionnent sans référence à un objet particulier. Par convention, une méthode statique nommée `Main` sert de point d’entrée d’un programme.

La sortie du programme est générée par la méthode `WriteLine` de la classe `Console` dans l’espace de noms `System`. Cette classe est fournie par les bibliothèques de classes .NET Framework, qui, par défaut, sont référencés automatiquement par le compilateur Microsoft C#. Notez que c# lui-même n’a pas une bibliothèque d’exécution distinct. Au lieu de cela, le .NET Framework est la bibliothèque runtime du langage c#.

## <a name="program-structure"></a>Structure de programme

Les concepts clés d’organisation en C# sont les ***programmes***, ***espaces de noms***, ***types***, ***membres*** et ***assemblys***. Les programmes C++, comme les programmes C, se composent d'un ou plusieurs fichiers sources. Les programmes déclarent des types qui contiennent des membres et peuvent être organisés en espaces de noms. Les classes et les interfaces sont des exemples de types. Les champs, méthodes, propriétés et événements sont des exemples de membres. Lorsque les programmes C# sont compilés, ils sont physiquement empaquetés dans des assemblys. Assemblys ont généralement l’extension de fichier `.exe` ou `.dll`, selon qu’ils implémentent ***applications*** ou ***bibliothèques***.

L’exemple

```csharp
using System;

namespace Acme.Collections
{
    public class Stack
    {
        Entry top;

        public void Push(object data) {
            top = new Entry(top, data);
        }

        public object Pop() {
            if (top == null) throw new InvalidOperationException();
            object result = top.data;
            top = top.next;
            return result;
        }

        class Entry
        {
            public Entry next;
            public object data;
    
            public Entry(Entry next, object data) {
                this.next = next;
                this.data = data;
            }
        }
    }
}
```
déclare une classe nommée `Stack` dans un espace de noms appelé `Acme.Collections`. Le nom qualifié complet de cette classe est `Acme.Collections.Stack`. La classe contient plusieurs membres : un champ nommé `top`, deux méthodes nommées `Push` et `Pop`, et une classe imbriquée nommée `Entry`. La classe `Entry` contient trois membres en plus : un champ nommé `next`, un autre nommé `data` et un constructeur. En supposant que le code source de l’exemple est stocké dans le fichier `acme.cs`, la ligne de commande

```
csc /t:library acme.cs
```
compile l’exemple en tant que bibliothèque (code sans point d’entrée `Main`) et produit un assembly nommé `acme.dll`.

Les assemblys contiennent du code exécutable sous la forme de ***Intermediate Language*** instructions (IL) et des informations symboliques sous la forme de ***métadonnées***. Avant son exécution, le code de langage intermédiaire dans un assembly est automatiquement converti en code spécifique au processeur par le compilateur juste à temps (JIT) du Common Language Runtime .NET.

Comme un assembly est une unité de fonctionnalité autodescriptive contenant du code et des métadonnées, les directives `#include` et les fichiers d’en-tête ne sont pas nécessaires en C#. Les membres et types publics contenus dans un assembly particulier sont disponibles dans un programme C# par simple référence à cet assembly lors de la compilation du programme. Par exemple, ce programme utilise la classe `Acme.Collections.Stack` à partir de l’assembly `acme.dll` :

```csharp
using System;
using Acme.Collections;

class Test
{
    static void Main() {
        Stack s = new Stack();
        s.Push(1);
        s.Push(10);
        s.Push(100);
        Console.WriteLine(s.Pop());
        Console.WriteLine(s.Pop());
        Console.WriteLine(s.Pop());
    }
}
```
Si le programme est stocké dans le fichier `test.cs`, lorsque `test.cs` est compilé, le `acme.dll` assembly peut être référencé à l’aide du compilateur `/r` option :

```
csc /r:acme.dll test.cs
```
Cela permet de créer un assembly exécutable nommé `test.exe`, qui, lors de l’exécution, produit la sortie :

```
100
10
1
```
C# permet le stockage du texte source d’un programme dans plusieurs fichiers source. Lorsqu’un programme C# multifichier est compilé, tous les fichiers source sont traités ensemble et les fichiers source peuvent librement se référencer mutuellement. Sur le plan conceptuel, c’est comme si tous les fichiers source étaient concaténés en un seul fichier volumineux avant d’être traités. Déclarations anticipées ne sont jamais nécessaires en C#, car, à de très rares exceptions près, l’ordre de déclaration n’a pas d’importance. C# ne limite pas un fichier source à la déclaration d’un seul type public et ne nécessite pas non plus que le nom du fichier source corresponde à un type déclaré dans ce fichier.

## <a name="types-and-variables"></a>Types et variables

Il existe deux genres de types en C# : les ***types référence*** et les ***types valeur***. Les variables des types valeur contiennent directement leurs données alors que les variables des types référence contiennent des références à leurs données, connues sous le nom d’objets. Avec les types référence, deux variables peuvent faire référence au même objet et, par conséquent, les opérations sur une variable peuvent affecter le même objet référencé par l'autre variable. Avec les types valeur, les variables possèdent leur propre copie de données, et les opérations sur une variable ne peuvent absolument pas affecter l'autre (sauf pour les variables de paramètre `ref` et `out`).

Les types de valeur de # sont divisés en ***types simples***, ***types enum***, ***types struct***, et ***types nullable***et la référence de # les types sont divisés en ***types de classes***, ***types d’interfaces***, ***types tableau***, et ***types délégués***.

Le tableau suivant fournit une vue d’ensemble du système de type de #.

| __Catégorie__    |                 | __Description__ |
|-----------------|-----------------|-----------------|
| Types de valeur     | Types simples    | Entier signé : `sbyte`, `short`, `int`, `long` |
|                 |                 | Entier non signé : `byte`, `ushort`, `uint`, `ulong` |
|                 |                 | Caractères Unicode : `char` |
|                 |                 | Virgule flottante IEEE : `float`, `double` |
|                 |                 | Décimale haute précision :`decimal` |
|                 |                 | Booléen : `bool` |
|                 | Types d'enum      | Types définis par l'utilisateur de la forme `enum E {...}` |
|                 | Types struct    | Types définis par l'utilisateur de la forme `struct S {...}` |
|                 | Types Nullable  | Extensions de tous les autres types de valeurs avec une valeur `null` |
| Types référence | Types de classes     | Classe de base fondamentale de tous les autres types : `object` |
|                 |                 | Chaînes Unicode : `string` |
|                 |                 | Types définis par l'utilisateur de la forme `class C {...}` |
|                 | Types interface | Types définis par l'utilisateur de la forme `interface I {...}` |
|                 | Types de tableaux     | Uni et multidimensionnels, par exemple `int[]` et `int[,]` |
|                 | Types délégués  | Les types définis par l’utilisateur du formulaire, par exemple `delegate int  D(...)` |

Les types intégraux huit prennent en charge les valeurs 8 bits, 16 bits, 32 bits et 64 bits sous forme signée ou non signée.

Les deux flottante types, de points `float` et `double`, sont représentés au format les 32 bits simple précision et 64 bits double précision IEEE 754.

Le type `decimal` est un type de données 128 bits adapté aux calculs financiers et monétaires.

C# `bool` type est utilisé pour représenter des valeurs booléennes, qui peuvent être `true` ou `false`.

Le traitement des caractères et chaînes dans le langage C# utilise l’encodage Unicode. Le type `char` représente une unité de code UTF-16, et le type `string` représente une séquence d’unités de code UTF-16.

Le tableau suivant récapitule les types numériques de #.


| __Catégorie__      | __Bits__ | __Type__  | __Plage/précision__ |
|-------------------|----------|-----------|---------------------|
| Type intégral signé   | 8        | `sbyte`   | -128...127 |
|                   | 16       | `short`   | -32,768...32,767 |
|                   | 32       | `int`     | -2,147,483,648...2,147,483,647 |
|                   | 64       | `long`    | -9,223,372,036,854,775,808...9,223,372,036,854,775,807 |
| Entier non signé | 8        | `byte`    | 0...255 |
|                   | 16       | `ushort`  | 0...65,535 |
|                   | 32       | `uint`    | 0...4,294,967,295 |
|                   | 64       | `ulong`   | 0...18,446,744,073,709,551,615 |
| Virgule flottante    | 32       | `float`   | 1,5 × 10 ^ −45 à 3,4 × 10 ^ 38, précision de 7 chiffres |
|                   | 64       | `double`  | 5,0 × 10 ^ −324 à 1,7 × 10 ^ 308, précision de 15 chiffres |
| Decimal           | 128      | `decimal` | 1.0 × 10 ^ −28 à 7,9 × 10 ^ 28, 28 chiffres |

Les programmes C# utilisent les ***déclarations de type*** pour créer de nouveaux types. Une déclaration de type spécifie le nom et les membres du nouveau type. Cinq des catégories de # de types sont définis par l’utilisateur : classe de types, les types struct, les types d’interface, les types enum et les types délégués.

Un type de classe définit une structure de données qui contient les données membres (champs) et des fonctions membres (méthodes, propriétés, etc.). Les types de classes prennent en charge l’héritage unique et le polymorphisme, des mécanismes par lesquels les classes dérivées peuvent étendre et spécialiser les classes de base.

Un type struct est similaire à un type de classe, car il représente une structure avec des membres de données et des fonctions membres. Toutefois, contrairement aux classes, structs sont des types valeur et ne nécessitent pas d’allocation de tas. Les types struct ne prennent pas en charge l’héritage spécifié par l’utilisateur, et tous les types struct héritent implicitement du type `object`.

Un type d’interface définit un contrat comme un jeu nommé de membres de la fonction publique. Une classe ou un struct qui implémente une interface doit fournir des implémentations de fonctions membres de l’interface. Une interface peut hériter de plusieurs interfaces de base, et une classe ou un struct peut implémenter plusieurs interfaces.

Un type délégué représente des références aux méthodes avec une liste de paramètres particulier et le type de retour. Les délégués permettent de traiter les méthodes en tant qu’entités qui peuvent être affectées à des variables et passées comme paramètres. Les délégués sont similaires au concept de pointeurs de fonction dans d’autres langages, mais contrairement aux pointeurs de fonction, les délégués sont orientés objet et de type sécurisé.

Classe, struct, interface et délégué types tous en charge les génériques, ce qui leur peuvent être paramétrés avec d’autres types.

Un type enum est un type distinct avec des constantes nommées. Chaque type énumération a un type sous-jacent, ce qui doit être un des huit types intégraux. L’ensemble de valeurs d’un type enum est identique à l’ensemble de valeurs du type sous-jacent.

C# prend en charge les tableaux uni et multidimensionnels de tout type. Contrairement aux types mentionnés ci-dessus, les types de tableaux n’ont pas à être déclarés avant de pouvoir être utilisés. Au lieu de cela, les types de tableaux sont construits en ajoutant des crochets à un nom de type. Par exemple, `int[]` est un tableau unidimensionnel de `int`, `int[,]` est un tableau bidimensionnel de `int`, et `int[][]` est un tableau unidimensionnel de tableaux unidimensionnels de `int`.

Également, les types nullables n’ont pas à être déclarés avant de pouvoir être utilisés. Pour chaque type de valeur non nullable `T` est un type nullable correspondant `T?`, qui peut contenir une valeur supplémentaire `null`. Par exemple, `int?` est un type qui peut contenir n’importe quel entier 32 bits ou la valeur `null`.

Système de type de # est unifié afin qu’une valeur de n’importe quel type peut être traitée en tant qu’objet. Chaque type dans C# dérive directement ou indirectement du type `object`, et `object` est la classe de base fondamentale de tous les types. Les valeurs des types référence sont considérées comme des objets simplement en affichant les valeurs en tant que type `object`. Valeurs des types valeur sont traitées en tant qu’objets en effectuant ***boxing*** et ***unboxing*** operations. Dans l’exemple suivant, une valeur `int` est convertie en `object` et à nouveau en `int`.

```csharp
using System;

class Test
{
    static void Main() {
        int i = 123;
        object o = i;          // Boxing
        int j = (int)o;        // Unboxing
    }
}
```
Quand une valeur d’un type valeur est convertie en type `object`, une instance d’objet, également appelée « boîte », est allouée pour contenir la valeur et la valeur est copiée dans cette zone. À l’inverse, lorsque un `object` référence est castée en un type valeur, une vérification est effectuée que l’objet référencé est une boîte du bon type valeur, et, si la vérification réussit, la valeur dans la zone est copiée.

Système de type unifié de # signifie que les types valeur peuvent devenir des objets « à la demande ». En raison de l’unification, les bibliothèques à usage général qui utilisent le type `object` peuvent être utilisées avec les types référence et les types valeur.

Il existe plusieurs types de ***variables*** en C#, y compris les champs, les éléments de tableau, les variables locales et les paramètres. Les variables représentent des emplacements de stockage, et chaque variable possède un type qui détermine les valeurs pouvant être stockées dans la variable, comme indiqué dans le tableau suivant.


| __Type de Variable__    | __Contenu possible__ |
|-------------------------|-----------------------|
| Type de valeur n’acceptant pas Null | Une valeur de ce type exact |
| Types valeur Nullable     | Une valeur null ou une valeur de ce type exact |
| `object`                | Une référence null, une référence à un objet de n’importe quel type de référence ou une référence à une valeur boxed de n’importe quel type de valeur |
| Type de classe              | Une référence null, une référence à une instance de ce type de classe ou une référence à une instance d’une classe dérivée de ce type de classe |
| Type d'interface          | Une référence null, une référence à une instance d’un type de classe qui implémente ce type d’interface ou une référence à une valeur boxed d’un type valeur qui implémente ce type d’interface |
| Type tableau              | Une référence null, une référence à une instance de ce type de tableau ou une référence à une instance d’un type tableau compatible |
| Type délégué           | Une référence null ou une référence à une instance de ce type de délégué |

## <a name="expressions"></a>Expressions

Les ***expressions*** sont construites à partir de ***d’opérandes*** et ***d’opérateurs***. Les opérateurs d’une expression indiquent les opérations à appliquer aux opérandes. Parmi les exemples d’opérateurs figurent `+`, `-`, `*`, `/` et `new`. Les littéraux, les champs, les variables locales et les expressions sont des exemples d’opérandes.

Quand une expression contient plusieurs opérateurs, la ***priorité*** des opérateurs contrôle l'ordre dans lequel les opérateurs individuels sont évalués. Par exemple, l’expression `x + y * z` est évaluée comme `x + (y * z)`, car l’opérateur `*` a une priorité plus élevée que `+`.

La plupart des opérateurs peuvent être ***surchargés***. La surcharge d’opérateur autorise la spécification d’implémentations d’opérateurs définies par l’utilisateur pour les opérations où l’un des deux opérandes ou les deux sont d’un type classe ou struct défini par l’utilisateur.

Le tableau suivant récapitule les opérateurs de #, répertoriant les catégories d’opérateurs dans l’ordre de priorité, du plus élevé au plus bas. Les opérateurs de la même catégorie ont la même priorité.


| __Catégorie__                     | __Expression__    | __Description__ |
|----------------------------------|-------------------|-----------------|
| Principale                          | `x.m`             | Accès au membre |
|                                  | `x(...)`          | Méthode et appel de délégué |
|                                  | `x[...]`          | Tableau et accès d'indexeur |
|                                  | `x++`             | Post-incrémentation |
|                                  | `x--`             | Post-décrémentation |
|                                  | `new T(...)`      | Création d'objet et de délégué |
|                                  | `new T(...){...}` | Création d’objet avec l’initialiseur |
|                                  | `new {...}`       | Initialiseur d’objet anonyme |
|                                  | `new T[...]`      | Création d’un tableau |
|                                  | `typeof(T)`       | Obtenir `System.Type` pour l’objet `T` |
|                                  | `checked(x)`      | Évaluer l'expression dans le contexte vérifié (checked) |
|                                  | `unchecked(x)`    | Évaluer l'expression dans le contexte non vérifié (unchecked) |
|                                  | `default(T)`      | Obtenir la valeur par défaut de type `T` |
|                                  | `delegate {...}`  | Fonction anonyme (méthode anonyme) |
| Unaire                            | `+x`              | Identité |
|                                  | `-x`              | Négation |
|                                  | `!x`              | Négation logique |
|                                  | `~x`              | Négation d'opération de bits |
|                                  | `++x`             | Pré-incrémentation |
|                                  | `--x`             | Pré-décrémentation |
|                                  | `(T)x`            | Convertir explicitement `x` au type `T` |
|                                  | `await x`         | Attendre de façon asynchrone `x` pour terminer |
| Multiplication                   | `x * y`           | Multiplication |
|                                  | `x / y`           | Division |
|                                  | `x % y`           | Reste |
| Addition                         | `x + y`           | Addition, concaténation de chaînes, combinaison de délégués |
|                                  | `x - y`           | Soustraction, suppression de délégué |
| Shift                            | `x << y`          | Décalage à gauche |
|                                  | `x >> y`          | Décalage à droite |
| Relations et test de type      | `x < y`           | Inférieur à |
|                                  | `x > y`           | Supérieur à |
|                                  | `x <= y`          | Inférieur ou égal à |
|                                  | `x >= y`          | Supérieur ou égal à |
|                                  | `x is T`          | Retourner `true` si `x` est un `T`, `false` sinon |
|                                  | `x as T`          | Retourner `x` typé en tant que `T`, ou `null` si `x` n’est pas un `T` |
| Égalité                         | `x == y`          | Égal      |
|                                  | `x != y`          | Différence |
| AND logique                      | `x & y`           | Entier au niveau du bit AND, boolean logique AND |
| XOR logique                      | `x ^ y`           | Opération de bits entière XOR, Boolean logique XOR |
| OR logique                       | <code>x &#124; y</code> | Opération de bits entière OR, Boolean logique OR |
| AND conditionnel                  | `x && y`          | Prend la valeur `y` uniquement si `x` est `true` |
| OR conditionnel                   | <code>x &#124;&#124; y</code> | Prend la valeur `y` uniquement si `x` est `false` |
| Fusion de Null                  | `X ?? y`          | Prend la valeur `y` si `x` est `null`à `x` sinon |
| Conditionnel                      | `x ? y : z`       | Prend la valeur `y` si `x` est `true`, `z` si `x` est `false` |
| Attribution ou fonction anonyme | `x = y`           | Attribution |
|                                  | `x op= y`         | Assignation composée ; opérateurs pris en charge sont `*=` `/=` `%=` `+=` `-=` `<<=` `>>=` `&=` `^=` <code>&#124;=</code> |
|                                  | `(T x) => y`      | Fonction anonyme (expression lambda) |

## <a name="statements"></a>Instructions

Les actions d’un programme sont exprimées à l’aide ***d’instructions***. C# prend en charge plusieurs types d’instructions, dont plusieurs sont définies en termes d’instructions incorporées.

Un ***bloc*** autorise l’écriture de plusieurs instructions dans les contextes où une seule instruction est autorisée. Un bloc se compose d’une liste d’instructions écrites entre les délimiteurs `{` et `}`.

Les ***instructions de déclaration*** sont utilisées pour déclarer des variables locales et des constantes.

Les ***instructions d’expression*** sont utilisées pour évaluer des expressions. Expressions qui peuvent être utilisées en tant qu’instructions comprennent les appels de méthode, allocations d’objet avec la `new` opérateur, les affectations avec `=` et opérateurs d’assignation composée, les opérations d’incrémentation et de décrémentation à l’aide de la `++`et `--` opérateurs et expressions await.

Les ***instructions de sélection*** sont utilisées pour sélectionner un nombre d’instructions possibles pour l’exécution en fonction de la valeur d’une expression. Dans ce groupe, ce sont les instructions `if` et `switch`.

***Instructions d’itération*** servent à exécuter à plusieurs reprises une instruction incorporée. Dans ce groupe, ce sont les instructions `while`, `do`, `for` et `foreach`.

Les ***instructions de saut*** sont utilisées pour transférer le contrôle. Dans ce groupe, ce sont les instructions `break`, `continue`, `goto`, `throw`, `return` et `yield`.

L’instruction `try`...`catch` est utilisée pour intercepter les exceptions qui se produisent pendant l’exécution d’un bloc, et l’instruction `try`...`finally` permet de spécifier que le code de finalisation est toujours exécuté, qu’une exception se soit produite ou non.

Le `checked` et `unchecked` instructions permettent de contrôler le contexte pour les opérations arithmétiques de type intégral et les conversions de vérification de dépassement de capacité.

L’instruction `lock` est utilisée pour obtenir le verrou d’exclusion mutuelle pour un objet donné, exécuter une instruction, puis libérer le verrou.

L’instruction `using` est utilisée pour obtenir une ressource, exécuter une instruction puis supprimer cette ressource.

Voici des exemples de chaque type d’instruction

__Déclarations de variables locales__

```csharp
static void Main() {
   int a;
   int b = 2, c = 3;
   a = 1;
   Console.WriteLine(a + b + c);
}
```


__Déclaration de constante locale__

```csharp
static void Main() {
    const float pi = 3.1415927f;
    const int r = 25;
    Console.WriteLine(pi * r * r);
}
```


__Instruction d’expression__

```csharp
static void Main() {
    int i;
    i = 123;                // Expression statement
    Console.WriteLine(i);   // Expression statement
    i++;                    // Expression statement
    Console.WriteLine(i);   // Expression statement
}
```

__`if` Instruction__

```csharp
static void Main(string[] args) {
    if (args.Length == 0) {
        Console.WriteLine("No arguments");
    }
    else {
        Console.WriteLine("One or more arguments");
    }
}
```


__`switch` Instruction__

```csharp
static void Main(string[] args) {
    int n = args.Length;
    switch (n) {
        case 0:
            Console.WriteLine("No arguments");
            break;
        case 1:
            Console.WriteLine("One argument");
            break;
        default:
            Console.WriteLine("{0} arguments", n);
            break;
    }
}
```

__`while` Instruction__

```csharp
static void Main(string[] args) {
    int i = 0;
    while (i < args.Length) {
        Console.WriteLine(args[i]);
        i++;
    }
}
```


__`do` Instruction__

```csharp
static void Main() {
    string s;
    do {
        s = Console.ReadLine();
        if (s != null) Console.WriteLine(s);
    } while (s != null);
}
```

__`for` Instruction__

```csharp
static void Main(string[] args) {
    for (int i = 0; i < args.Length; i++) {
        Console.WriteLine(args[i]);
    }
}
```

__`foreach` Instruction__

```csharp
static void Main(string[] args) {
    foreach (string s in args) {
        Console.WriteLine(s);
    }
}
```

__`break` Instruction__

```csharp
static void Main() {
    while (true) {
        string s = Console.ReadLine();
        if (s == null) break;
        Console.WriteLine(s);
    }
}
```

__`continue` Instruction__

```csharp
static void Main(string[] args) {
    for (int i = 0; i < args.Length; i++) {
        if (args[i].StartsWith("/")) continue;
        Console.WriteLine(args[i]);
    }
}
```

__`goto` Instruction__

```csharp
static void Main(string[] args) {
    int i = 0;
    goto check;
    loop:
    Console.WriteLine(args[i++]);
    check:
    if (i < args.Length) goto loop;
}
```

__`return` Instruction__

```csharp
static int Add(int a, int b) {
    return a + b;
}

static void Main() {
    Console.WriteLine(Add(1, 2));
    return;
}
```

__`yield` Instruction__

```csharp
static IEnumerable<int> Range(int from, int to) {
    for (int i = from; i < to; i++) {
        yield return i;
    }
    yield break;
}

static void Main() {
    foreach (int x in Range(-10,10)) {
        Console.WriteLine(x);
    }
}
```

__`throw` et `try` instructions__

```csharp
static double Divide(double x, double y) {
    if (y == 0) throw new DivideByZeroException();
    return x / y;
}

static void Main(string[] args) {
    try {
        if (args.Length != 2) {
            throw new Exception("Two numbers required");
        }
        double x = double.Parse(args[0]);
        double y = double.Parse(args[1]);
        Console.WriteLine(Divide(x, y));
    }
    catch (Exception e) {
        Console.WriteLine(e.Message);
    }
    finally {
        Console.WriteLine("Good bye!");
    }
}
```

__`checked` et `unchecked` instructions__

```csharp
static void Main() {
    int i = int.MaxValue;
    checked {
        Console.WriteLine(i + 1);        // Exception
    }
    unchecked {
        Console.WriteLine(i + 1);        // Overflow
    }
}
```

__`lock` Instruction__

```csharp
class Account
{
    decimal balance;
    public void Withdraw(decimal amount) {
        lock (this) {
            if (amount > balance) {
                throw new Exception("Insufficient funds");
            }
            balance -= amount;
        }
    }
}
```

__`using` Instruction__

```csharp
static void Main() {
    using (TextWriter w = File.CreateText("test.txt")) {
        w.WriteLine("Line one");
        w.WriteLine("Line two");
        w.WriteLine("Line three");
    }
}
```

## <a name="classes-and-objects"></a>Classes et objets

Les ***classes*** représentent le type le plus fondamental de C#. Une classe est une structure de données qui combine l’état (champs) et les actions (méthodes et autres fonctions membres) en une seule unité. Une classe fournit une définition pour les ***instances*** créées dynamiquement de la classe, également appelées ***objets***. Les classes prennent en charge ***l’héritage*** et le ***polymorphisme***, des mécanismes par lesquels les ***classes dérivées*** peuvent étendre et spécialiser les ***classes de base***.

Les nouvelles classes sont créées à l’aide des déclarations de classe. Une déclaration de classe commence par un en-tête qui spécifie les attributs et modificateurs de la classe, le nom de la classe, la classe de base (si fournie) et les interfaces implémentées par la classe. L’en-tête est suivi par le corps de la classe, qui se compose d’une liste de déclarations de membres écrites entre les délimiteurs `{` et `}`.

Voici une déclaration d’une classe simple nommée `Point` :

```csharp
public class Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
Les instances de classes sont créées à l’aide de l’opérateur `new`, qui alloue la mémoire pour une nouvelle instance, appelle un constructeur pour initialiser l’instance et retourne une référence à l’instance. Les instructions suivantes créent deux `Point` objets et de stocker les références à ces objets dans les deux variables :

```
Point p1 = new Point(0, 0);
Point p2 = new Point(10, 20);
```
La mémoire occupée par un objet est automatiquement libérée lorsque l’objet n’est plus en cours d’utilisation. Il n’est ni possible ni nécessaire de libérer explicitement des objets dans C#.

### <a name="members"></a>Membres

Les membres d’une classe sont soit ***membres statiques*** ou ***membres d’instance***. Les membres statiques appartiennent à des classes, et les membres d’instance appartiennent à des objets (instances de classes).

Le tableau suivant fournit une vue d’ensemble des types de membres de qu'une classe peut contenir.


| __Membre__   | __Description__ |
|------------  |-----------------|
| Constantes    | Valeurs constantes associées à la classe |
| Champs       | Variables de la classe |
| Méthodes      | Calculs et les actions qui peuvent être effectués par la classe |
| Propriétés   | Actions associées à la lecture et l’écriture des propriétés nommées de la classe |
| Indexeurs     | Actions liées à l’indexation des instances de la classe comme un tableau |
| Événements       | Les notifications qui peuvent être générées par la classe |
| Opérateurs    | Les opérateurs de conversion et d’expression pris en charge par la classe |
| Constructeurs | Les actions requises pour initialiser les instances de la classe ou la classe elle-même |
| Destructeurs  | Actions à effectuer avant que les instances de la classe soient abandonnées de façon définitive |
| Types        | Types imbriqués déclarés par la classe |

### <a name="accessibility"></a>Accessibilité

Chaque membre d’une classe a une accessibilité associée, qui contrôle les régions du texte du programme qui sont en mesure d’accéder au membre. Il existe cinq formes possibles d’accessibilité. Ceux-ci sont résumés dans le tableau ci-après.


| __Accessibilité__    | __Signification__ |
|----------------------|-----------------|
| `public`             | Accès non limité |
| `protected`          | Accès limité à cette classe ou aux classes dérivées de cette classe |
| `internal`           | Accès limité à ce programme |
| `protected internal` | Accès limité à ce programme ou aux classes dérivées de cette classe |
| `private`            | Accès limité à cette classe |

### <a name="type-parameters"></a>Paramètres de type

Une définition de classe peut spécifier un jeu de paramètres de type en faisant suivre le nom de classe par une liste de noms de paramètre de type entre crochets. Les paramètres de type peuvent l’être utilisé dans le corps des déclarations de classe pour définir les membres de la classe. Dans l’exemple suivant, les paramètres de type de `Pair` sont `TFirst` et `TSecond` :

```csharp
public class Pair<TFirst,TSecond>
{
    public TFirst First;
    public TSecond Second;
}
```
Un type de classe est déclaré pour prendre des paramètres de type est appelé un type de classe générique. Les types struct, interface et délégué peuvent également être génériques.

Lorsque la classe générique est utilisée, des arguments de type doivent être fournis pour chacun des paramètres de type :

```csharp
Pair<int,string> pair = new Pair<int,string> { First = 1, Second = "two" };
int i = pair.First;     // TFirst is int
string s = pair.Second; // TSecond is string
```
Un type générique avec des arguments de type fournis, comme `Pair<int,string>
    ` ci-dessus, est appelé type construit.

### <a name="base-classes"></a>Classes de base

Une déclaration de classe peut spécifier une classe de base en faisant suivre les paramètres de nom et de type de classe par un signe deux-points et le nom de la classe de base. L’omission d’une spécification de classe de base revient à dériver du type `object`. Dans l'exemple suivant, la classe de base de `Point3D` est `Point`, et la classe de base de `Point` est `object` :

```csharp
public class Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

public class Point3D: Point
{
    public int z;

    public Point3D(int x, int y, int z): base(x, y) {
        this.z = z;
    }
}
```
Une classe hérite des membres de sa classe de base. L’héritage signifie qu’une classe contient implicitement tous les membres de sa classe de base, à l’exception de l’instance et les constructeurs statiques et les destructeurs de classe de base. Une classe dérivée peut ajouter des membres hérités, mais ne peut pas supprimer la définition d’un membre hérité. Dans l’exemple précédent, `Point3D` hérite des champs `x` et `y` de `Point`, et chaque instance de `Point3D` contient trois champs, `x`, `y` et `z`.

Il existe une conversion implicite d’un type de classe vers un de ses types de classe de base. Par conséquent, une variable d’un type de classe peut référencer une instance de cette classe ou une instance d’une classe dérivée. Par exemple, pour les déclarations de classe précédentes, une variable de type `Point` peut faire référence à un objet `Point` ou `Point3D` :

```csharp
Point a = new Point(10, 20);
Point b = new Point3D(10, 20, 30);
```

### <a name="fields"></a>Champs

Un champ est une variable qui est associée à une classe ou à une instance d’une classe.

Un champ déclaré avec le `static` modificateur définit un ***champ statique***. Un champ statique identifie exactement un seul emplacement de stockage. Quel que soit le nombre d’instances d’une classe qui sont créées, il n’existe qu’une seule copie d’un champ statique.

Un champ déclaré sans le `static` modificateur définit un ***champ d’instance***. Chaque instance d’une classe contient une copie distincte de tous les champs d’instance de cette classe.

Dans l’exemple suivant, chaque instance de la classe `Color` possède une copie distincte des champs d’instance `r`, `g` et `b`, mais il existe une seule copie des champs statiques `Black`, `White`, `Red`, `Green` et `Blue` :

```csharp
public class Color
{
    public static readonly Color Black = new Color(0, 0, 0);
    public static readonly Color White = new Color(255, 255, 255);
    public static readonly Color Red = new Color(255, 0, 0);
    public static readonly Color Green = new Color(0, 255, 0);
    public static readonly Color Blue = new Color(0, 0, 255);
    private byte r, g, b;

    public Color(byte r, byte g, byte b) {
        this.r = r;
        this.g = g;
        this.b = b;
    }
}
```
Comme indiqué dans l’exemple précédent, les ***champs en lecture seule*** peuvent être déclarés avec un modificateur `readonly`. Affectation à un `readonly` champ peut se produire uniquement dans le cadre de la déclaration du champ ou dans un constructeur de la même classe.

### <a name="methods"></a>Méthodes

Une ***méthode*** est un membre qui implémente un calcul ou une action qui peut être effectuée par un objet ou une classe. Les ***méthodes statiques*** sont accessibles à travers la classe. Les ***méthodes d’instance*** sont accessibles via des instances de la classe.

Méthodes ont une liste (éventuellement vide) de ***paramètres***, qui représentent des valeurs ou des références variables passées à la méthode et un ***type de retour***, qui spécifie le type de la valeur calculée et retournée par la méthode. Type de retour d’une méthode est `void` si elle ne retourne pas une valeur.

Comme les types, les méthodes peuvent également être un jeu de paramètres de type pour lesquels les arguments de type doivent être spécifiés lorsque la méthode est appelée. Contrairement aux types, les arguments de type peuvent souvent être déduits à partir des arguments d’un appel de méthode et n’ont pas à être fournis explicitement.

La ***signature*** d’une méthode doit être unique dans la classe dans laquelle la méthode est déclarée. La signature d’une méthode se compose du nom de la méthode, du nombre de paramètres de type et du nombre, des modificateurs et des types de ses paramètres. La signature d'une méthode n'inclut pas le type de retour.

#### <a name="parameters"></a>Paramètres

Les paramètres sont utilisés pour passer des valeurs ou des références variables aux méthodes. Les paramètres d’une méthode obtiennent leurs valeurs réelles à partir des ***arguments*** qui sont spécifiés lorsque la méthode est appelée. Il existe quatre types de paramètres : les paramètres de valeur, les paramètres de référence, les paramètres de sortie et les tableaux de paramètres.

Un ***paramètre de valeur*** est utilisé pour le passage de paramètres d’entrée. Un paramètre de valeur correspond à une variable locale qui obtient sa valeur initiale de l’argument qui a été transmis pour le paramètre. Les modifications apportées à un paramètre de valeur n’affectent pas l’argument qui a été transmis pour le paramètre.

Les paramètres de valeur peuvent être facultatifs, en spécifiant une valeur par défaut afin que les arguments correspondants puissent être omis.

Un ***paramètre de référence*** est utilisé pour la transmission de paramètres en entrée et en sortie. L’argument passé pour un paramètre de référence doit être une variable, et pendant l’exécution de la méthode, le paramètre de référence représente le même emplacement de stockage que la variable d’argument. Un paramètre de référence est déclaré avec le modificateur `ref`. L'exemple suivant illustre l'utilisation des paramètres `ref`.

```csharp
using System;

class Test
{
    static void Swap(ref int x, ref int y) {
        int temp = x;
        x = y;
        y = temp;
    }

    static void Main() {
        int i = 1, j = 2;
        Swap(ref i, ref j);
        Console.WriteLine("{0} {1}", i, j);            // Outputs "2 1"
    }
}
```
Un ***paramètre de sortie*** est utilisé pour le passage de paramètres de sortie. Un paramètre de sortie est similaire à un paramètre de référence, sauf que la valeur initiale de l’argument fourni par l’appelant n’a pas d’importance. Un paramètre de sortie est déclaré avec le modificateur `out`. L'exemple suivant illustre l'utilisation des paramètres `out`.

```csharp
using System;

class Test
{
    static void Divide(int x, int y, out int result, out int remainder) {
        result = x / y;
        remainder = x % y;
    }

    static void Main() {
        int res, rem;
        Divide(10, 3, out res, out rem);
        Console.WriteLine("{0} {1}", res, rem);    // Outputs "3 1"
    }
}
```
Un ***tableau de paramètres*** autorise le passage d’un nombre variable d’arguments à une méthode. Un tableau de paramètres est déclaré avec le modificateur `params`. Seul le dernier paramètre d’une méthode peut être un tableau de paramètres, et le type d’un tableau de paramètres doit être un type tableau unidimensionnel. Le `Write` et `WriteLine` méthodes de la `System.Console` classe sont de bons exemples d’utilisation des tableaux de paramètre. Vous les déclarez de la façon suivante.

```csharp
public class Console
{
    public static void Write(string fmt, params object[] args) {...}
    public static void WriteLine(string fmt, params object[] args) {...}
    ...
}
```
Dans une méthode qui utilise un tableau de paramètres, le tableau de paramètres se comporte exactement comme un paramètre ordinaire de type tableau. Toutefois, dans un appel à une méthode avec un tableau de paramètres, il est possible de passer un argument unique de type tableau de paramètres ou n’importe quel nombre d’arguments du type d’élément du tableau de paramètres. Dans ce cas, une instance de tableau est automatiquement créée et initialisée avec les arguments donnés. Cet exemple

```csharp
Console.WriteLine("x={0} y={1} z={2}", x, y, z);
```
revient à écrire ce qui suit.

```csharp
string s = "x={0} y={1} z={2}";
object[] args = new object[3];
args[0] = x;
args[1] = y;
args[2] = z;
Console.WriteLine(s, args);
```

#### <a name="method-body-and-local-variables"></a>Corps de la méthode et variables locales

Corps d’une méthode spécifie les instructions à exécuter lorsque la méthode est appelée.

Un corps de méthode peut déclarer des variables qui sont spécifiques à l’appel de la méthode. Ces variables sont appelées ***variables locales***. Une déclaration de variable locale spécifie un nom de type, un nom de variable et éventuellement une valeur initiale. L’exemple suivant déclare une variable locale `i` avec une valeur initiale de zéro et une variable locale `j` sans valeur initiale.

```csharp
using System;

class Squares
{
    static void Main() {
        int i = 0;
        int j;
        while (i < 10) {
            j = i * i;
            Console.WriteLine("{0} x {0} = {1}", i, j);
            i = i + 1;
        }
    }
}
```
C# requiert qu’une variable locale soit ***assignée de manière définitive*** avant de pouvoir obtenir sa valeur. Par exemple, si la déclaration du `i` précédent n’inclut pas de valeur initiale, le compilateur signale une erreur pour les utilisations ultérieures de `i`, car `i` ne serait pas assigné de manière définitive à ces points dans le programme.

Une méthode peut utiliser les instructions `return` pour retourner le contrôle à son appelant. Dans une méthode retournant `void`, les instructions `return` ne peuvent pas spécifier une expression. Dans une méthode retournant non -`void`, `return` les instructions doivent inclure une expression qui calcule la valeur de retour.

#### <a name="static-and-instance-methods"></a>Méthodes statiques et d’instance

Une méthode déclarée avec un `static` modificateur est un ***méthode statique***. Une méthode statique n’opère pas sur une instance spécifique et permet uniquement d’accéder directement à des membres statiques.

Une méthode déclarée sans un `static` modificateur est un ***méthode d’instance***. Une méthode d’instance opère sur une instance spécifique et peut accéder aux membres statiques et d’instance. L’instance sur laquelle une méthode d’instance a été appelée est explicitement accessible en tant que `this`. Une erreur consiste à faire référence à `this` dans une méthode statique.

La classe `Entity` suivante a à la fois des statiques et des membres d’instance.

```csharp
class Entity
{
    static int nextSerialNo;
    int serialNo;

    public Entity() {
        serialNo = nextSerialNo++;
    }

    public int GetSerialNo() {
        return serialNo;
    }

    public static int GetNextSerialNo() {
        return nextSerialNo;
    }

    public static void SetNextSerialNo(int value) {
        nextSerialNo = value;
    }
}
```
Chaque instance `Entity` contient un numéro de série (et probablement d’autres informations qui ne sont pas indiquées ici). Le constructeur `Entity` (qui est similaire à une méthode d’instance) initialise la nouvelle instance avec le numéro de série suivant. Étant donné que le constructeur est un membre d’instance, il est autorisé à accéder à la fois au champ d’instance `serialNo` et au champ statique `nextSerialNo`.

Les méthodes statiques `GetNextSerialNo` et `SetNextSerialNo` peuvent accéder au champ statique `nextSerialNo`, mais ce serait une erreur pour eux d’accéder directement au champ d’instance `serialNo`.

L’exemple suivant illustre l’utilisation de la `Entity` classe.

```csharp
using System;

class Test
{
    static void Main() {
        Entity.SetNextSerialNo(1000);
        Entity e1 = new Entity();
        Entity e2 = new Entity();
        Console.WriteLine(e1.GetSerialNo());           // Outputs "1000"
        Console.WriteLine(e2.GetSerialNo());           // Outputs "1001"
        Console.WriteLine(Entity.GetNextSerialNo());   // Outputs "1002"
    }
}
```
Notez que les méthodes statiques `SetNextSerialNo` et `GetNextSerialNo` sont appelées sur la classe alors que la méthode d’instance `GetSerialNo` est appelée sur les instances de la classe.

#### <a name="virtual-override-and-abstract-methods"></a>Méthodes virtuelles, de substitution et abstraites

Lorsqu’une déclaration de méthode d’instance inclut un modificateur `virtual`, la méthode est appelée ***méthode virtuelle***. En cas de non `virtual` modificateur est présent, la méthode est dite un ***méthode non virtuelle***.

Lorsqu’une méthode virtuelle est appelée, le ***type au moment de l’exécution*** de l’instance pour laquelle cet appel prend place détermine l’implémentation de méthode à appeler. Dans un appel de méthode non virtuelle, le ***type lors de la compilation*** de l’instance est le facteur déterminant.

Une méthode virtuelle peut être ***substituée*** dans une classe dérivée. Lorsqu’une déclaration de méthode d’instance inclut un `override` modificateur, la méthode substitue à une méthode virtuelle héritée ayant la même signature. Là où une déclaration de méthode virtuelle présente une nouvelle méthode, une déclaration de méthode de substitution spécialise une méthode virtuelle héritée existante en fournissant une nouvelle implémentation de cette méthode.

Un ***abstraite*** méthode est une méthode virtuelle sans implémentation. Une méthode abstraite est déclarée avec le `abstract` modificateur et n’est autorisée uniquement dans une classe qui est également déclarée `abstract`. Une méthode abstraite doit être remplacée dans chaque classe dérivée non abstraite.

L’exemple suivant déclare une classe abstraite, `Expression`, qui représente un nœud d’arborescence de l’expression et trois classes dérivées, `Constant`, `VariableReference` et `Operation`, qui implémentent des nœuds d’arborescence de l’expression pour les références variables, les constantes et les opérations arithmétiques. (Cela revient à, mais à ne pas confondre avec les types d’arborescence expression introduit dans [types arborescence d’Expression](types.md#expression-tree-types)).

```csharp
using System;
using System.Collections;

public abstract class Expression
{
    public abstract double Evaluate(Hashtable vars);
}

public class Constant: Expression
{
    double value;

    public Constant(double value) {
        this.value = value;
    }

    public override double Evaluate(Hashtable vars) {
        return value;
    }
}

public class VariableReference: Expression
{
    string name;

    public VariableReference(string name) {
        this.name = name;
    }

    public override double Evaluate(Hashtable vars) {
        object value = vars[name];
        if (value == null) {
            throw new Exception("Unknown variable: " + name);
        }
        return Convert.ToDouble(value);
    }
}

public class Operation: Expression
{
    Expression left;
    char op;
    Expression right;

    public Operation(Expression left, char op, Expression right) {
        this.left = left;
        this.op = op;
        this.right = right;
    }

    public override double Evaluate(Hashtable vars) {
        double x = left.Evaluate(vars);
        double y = right.Evaluate(vars);
        switch (op) {
            case '+': return x + y;
            case '-': return x - y;
            case '*': return x * y;
            case '/': return x / y;
        }
        throw new Exception("Unknown operator");
    }
}
```
Les quatre classes précédentes permet de modéliser des expressions arithmétiques. Par exemple, en utilisant des instances de ces classes, l’expression `x + 3` peut être représentée comme suit.

```csharp
Expression e = new Operation(
    new VariableReference("x"),
    '+',
    new Constant(3));
```
La méthode `Evaluate` d’une instance `Expression` est appelée pour évaluer l’expression donnée et produire une valeur `double`. La méthode prend comme argument une `Hashtable` qui contient les noms de variables (comme clés des entrées) et des valeurs (comme les valeurs des entrées). Le `Evaluate` méthode est une méthode virtuelle abstraite, ce qui signifie que les classes non abstraites dérivées doivent remplacer pour fournir une implémentation réelle.

Une implémentation de `Constant` de `Evaluate` renvoie simplement la constante stockée. Un `VariableReference`d’implémentation recherche le nom de variable dans la table de hachage et retourne la valeur résultante. Une implémentation de `Operation` évalue d’abord les opérandes de gauche et de droite (en appelant de manière récursive leurs méthodes `Evaluate`), puis effectue l’opération arithmétique donnée.

Le programme suivant utilise les classes `Expression` pour évaluer l’expression `x * (y + 2)` pour différentes valeurs de `x` et `y`.

```csharp
using System;
using System.Collections;

class Test
{
    static void Main() {
        Expression e = new Operation(
            new VariableReference("x"),
            '*',
            new Operation(
                new VariableReference("y"),
                '+',
                new Constant(2)
            )
        );
        Hashtable vars = new Hashtable();
        vars["x"] = 3;
        vars["y"] = 5;
        Console.WriteLine(e.Evaluate(vars));        // Outputs "21"
        vars["x"] = 1.5;
        vars["y"] = 9;
        Console.WriteLine(e.Evaluate(vars));        // Outputs "16.5"
    }
}
```

#### <a name="method-overloading"></a>Surcharge de méthode

La ***surcharge*** de méthode permet d’avoir plusieurs méthodes dans la même classe avec le même nom, tant qu’elles ont des signatures uniques. Lors de la compilation d’un appel à une méthode surchargée, le compilateur utilise ***la résolution de surcharge*** pour déterminer la méthode spécifique à appeler. La résolution de surcharge trouve la méthode qui correspond le mieux aux arguments ou signale une erreur si aucune meilleure correspondance ne peut être trouvée. L’exemple suivant montre la résolution de surcharge en action. Le commentaire pour chaque appel de la méthode `Main` affiche une méthode qui est réellement appelée.

```csharp
class Test
{
    static void F() {
        Console.WriteLine("F()");
    }

    static void F(object x) {
        Console.WriteLine("F(object)");
    }

    static void F(int x) {
        Console.WriteLine("F(int)");
    }

    static void F(double x) {
        Console.WriteLine("F(double)");
    }

    static void F<T>(T x) {
        Console.WriteLine("F<T>(T)");
    }

    static void F(double x, double y) {
        Console.WriteLine("F(double, double)");
    }

    static void Main() {
        F();                 // Invokes F()
        F(1);                // Invokes F(int)
        F(1.0);              // Invokes F(double)
        F("abc");            // Invokes F(object)
        F((double)1);        // Invokes F(double)
        F((object)1);        // Invokes F(object)
        F<int>(1);           // Invokes F<T>(T)
        F(1, 1);             // Invokes F(double, double)
    }
}
```
Comme le montre l’exemple, une méthode particulière peut toujours être sélectionnée en effectuant un cast explicite des arguments aux types de paramètres exacts et/ou en fournissant explicitement les arguments de type.

### <a name="other-function-members"></a>Autres fonctions membres

Les membres qui contiennent du code exécutable sont collectivement regroupés sous les ***membres de fonction*** d’une classe. La section précédente décrit les méthodes qui sont du type principal des fonctions membres. Cette section décrit les autres types de fonctions membres pris en charge par c# : constructeurs, propriétés, indexeurs, événements, opérateurs et les destructeurs.

Le code suivant montre une classe générique appelée `List<T>`, qui implémente une liste d’objets évolutive. La classe contient plusieurs exemples des types les plus courants de membres de fonction.


```csharp
public class List<T> {
    // Constant...
    const int defaultCapacity = 4;

    // Fields...
    T[] items;
    int count;

    // Constructors...
    public List(int capacity = defaultCapacity) {
        items = new T[capacity];
    }

    // Properties...
    public int Count {
        get { return count; }
    }
    public int Capacity {
        get {
            return items.Length;
        }
        set {
            if (value < count) value = count;
            if (value != items.Length) {
                T[] newItems = new T[value];
                Array.Copy(items, 0, newItems, 0, count);
                items = newItems;
            }
        }
    }

    // Indexer...
    public T this[int index] {
        get {
            return items[index];
        }
        set {
            items[index] = value;
            OnChanged();
        }
    }

    // Methods...
    public void Add(T item) {
        if (count == Capacity) Capacity = count * 2;
        items[count] = item;
        count++;
        OnChanged();
    }
    protected virtual void OnChanged() {
        if (Changed != null) Changed(this, EventArgs.Empty);
    }
    public override bool Equals(object other) {
        return Equals(this, other as List<T>);
    }
    static bool Equals(List<T> a, List<T> b) {
        if (a == null) return b == null;
        if (b == null || a.count != b.count) return false;
        for (int i = 0; i < a.count; i++) {
            if (!object.Equals(a.items[i], b.items[i])) {
                return false;
            }
        }
        return true;
    }

    // Event...
    public event EventHandler Changed;

    // Operators...
    public static bool operator ==(List<T> a, List<T> b) {
        return Equals(a, b);
    }
    public static bool operator !=(List<T> a, List<T> b) {
        return !Equals(a, b);
    }
}
```

#### <a name="constructors"></a>Constructeurs

C# prend en charge les constructeurs statiques et d’instance. Un ***constructeur d’instance*** est un membre qui implémente les actions requises pour initialiser une instance d’une classe. Un ***constructeur statique*** est un membre qui implémente les actions requises pour initialiser une classe lui-même lorsqu’il est chargé.

Un constructeur est déclaré comme une méthode sans aucun type de retour et le même nom que la classe contenante. Si une déclaration de constructeur comprend un `static` modificateur, elle déclare un constructeur statique. Dans le cas contraire, elle déclare un constructeur d’instance.

Constructeurs d’instance peuvent être surchargés. Par exemple, la classe `List<T>
` déclare deux constructeurs d’instance, un sans paramètres et une fonction qui accepte un paramètre `int`. Les constructeurs d’instance sont appelés en utilisant l’opérateur `new`. Les instructions suivantes allouent deux `List<string>
` instances à l’aide de chacun des constructeurs de la `List` classe.

```csharp
List<string> list1 = new List<string>();
List<string> list2 = new List<string>(10);
```
Contrairement aux autres membres, les constructeurs d’instance ne sont pas hérités, et une classe n’a aucun constructeur d’instance autre que ceux réellement déclarés dans la classe. Si aucun constructeur d’instance n’est fourni pour une classe, un constructeur vide sans paramètre est fourni automatiquement.

#### <a name="properties"></a>Propriétés

Les ***propriétés*** sont une extension naturelle des champs. Les deux sont des membres nommés avec des types associés, et la syntaxe pour accéder aux champs et propriétés est la même. Toutefois, contrairement aux champs, les propriétés ne désignent pas des emplacements de stockage. Au lieu de cela, les propriétés ont des ***accesseurs*** qui spécifient les instructions à exécuter lorsque les valeurs sont lues ou écrites.

Une propriété est déclarée comme un champ, à ceci près que la déclaration se termine par un `get` accesseur et/ou un `set` accesseur écrite entre les délimiteurs `{` et `}` au lieu de se terminant par un point-virgule. Une propriété qui possède à la fois un `get` accesseur et un `set` accesseur est une ***propriété en lecture-écriture***, une propriété qui possède uniquement un `get` accesseur est une ***propriété en lecture seule***et un propriété qui a uniquement un `set` accesseur est une ***propriété en écriture seule***.

Un `get` accesseur correspond à une méthode sans paramètre avec une valeur de retour du type de propriété. À l’exception comme cible d’une assignation, lorsqu’une propriété est référencée dans une expression, le `get` accesseur de la propriété est appelé pour calculer la valeur de la propriété.

Un `set` accesseur correspond à une méthode avec un paramètre unique nommé `value` et aucun type de retour. Lorsqu’une propriété est référencée en tant que la cible d’une assignation ou l’opérande de `++` ou `--`, le `set` accesseur est appelé avec un argument qui fournit la nouvelle valeur.

Le `List<T>
` classe déclare deux propriétés, `Count` et `Capacity`, qui sont en lecture seule et en lecture-écriture, respectivement. Voici un exemple d’utilisation de ces propriétés.

```csharp
List<string> names = new List<string>();
names.Capacity = 100;            // Invokes set accessor
int i = names.Count;             // Invokes get accessor
int j = names.Capacity;          // Invokes get accessor
```
C# prend en charge les propriétés d’instance et les propriétés statiques, similaires aux champs et aux méthodes. Propriétés statiques sont déclarées avec le `static` modificateur et les propriétés de l’instance sont déclarées sans.

Le ou les accesseurs d’une propriété peuvent être virtuels. Lorsqu’une déclaration de propriété inclut un modificateur `virtual`, `abstract` ou `override`, elle s’applique aux accesseurs de la propriété.

#### <a name="indexers"></a>Indexeurs

Un ***indexeur*** est un membre qui permet l’indexation des objets de la même façon en tant que tableau. Un indexeur est déclaré comme une propriété, sauf que le nom du membre est `this` suivie d’une liste de paramètres écrite entre les délimiteurs `[` et `]`. Les paramètres sont disponibles dans le ou les accesseurs de l’indexeur. Similaires aux propriétés, les indexeurs peuvent être en lecture-écriture, en lecture seule et en écriture seule, et les accesseurs d’un indexeur peuvent être virtuels.

La classe `List` déclare un indexeur en lecture-écriture unique qui prend un paramètre `int`. L’indexeur rend possible l’indexation des instances `List` avec des valeurs `int`. Exemple :

```csharp
List<string> names = new List<string>();
names.Add("Liz");
names.Add("Martha");
names.Add("Beth");
for (int i = 0; i < names.Count; i++) {
    string s = names[i];
    names[i] = s.ToUpper();
}
```
Les indexeurs peuvent être surchargés, ce qui signifie qu’une classe peut déclarer plusieurs indexeurs tant que le nombre ou les types de leurs paramètres diffèrent.

#### <a name="events"></a>Événements

Un ***événement*** est un membre qui permet à une classe ou un objet de fournir des notifications. Un événement est déclaré comme un champ, à ceci près que la déclaration inclut un `event` mot clé et le type doivent être un type délégué.

Dans une classe qui déclare un membre d’événement, l’événement se comporte comme un champ d’un type délégué (à condition que l’événement n’est pas abstrait et ne déclare pas d’accesseurs). Le champ stocke une référence à un délégué qui représente les gestionnaires d’événements qui ont été ajoutés à l’événement. Si aucun handle d’événement n’est présents, le champ est `null`.

La classe `List<T>
` déclare un membre d’événement unique appelé `Changed`, qui indique qu’un nouvel élément a été ajouté à la liste. Le `Changed` événement est déclenché par le `OnChanged` méthode virtuelle, qui vérifie si l’événement est `null` (ce qui signifie qu’aucun gestionnaire n’est présent). La notion de déclenchement d’un événement est équivalente à l’appel de délégué représenté par l’événement. Par conséquent, il n’existe aucune construction de langage particulière pour déclencher des événements.

Les clients réagissent aux événements via les ***gestionnaires d’événements***. Les gestionnaires d’événements sont joints à l’aide de l’opérateur `+=` et supprimés à l’aide de l’opérateur `-=`. L'exemple suivant joint un gestionnaire d'événements à l'événement `Changed` d’un `List<string>
`.

```csharp
using System;

class Test
{
    static int changeCount;

    static void ListChanged(object sender, EventArgs e) {
        changeCount++;
    }

    static void Main() {
        List<string> names = new List<string>();
        names.Changed += new EventHandler(ListChanged);
        names.Add("Liz");
        names.Add("Martha");
        names.Add("Beth");
        Console.WriteLine(changeCount);        // Outputs "3"
    }
}
```
Pour les scénarios avancés où le contrôle du stockage sous-jacent d’un événement est souhaité, une déclaration d’événement peut fournir explicitement des accesseurs `add` et `remove`, qui sont plutôt similaires à l’accesseur `set` d’une propriété.

#### <a name="operators"></a>Opérateurs

Un ***opérateur*** est un membre qui définit la signification de l’application d’un opérateur d’expression particulière aux instances d’une classe. Trois types d’opérateurs peuvent être définis : les opérateurs unaires, les opérateurs binaires et les opérateurs de conversion. Tous les opérateurs doivent être déclarés comme `public` et `static`.

La classe `List<T>
` déclare deux opérateurs, `operator==` et `operator!=`, et donne donc une nouvelle signification aux expressions qui appliquent ces opérateurs aux instances `List`. Plus précisément, les opérateurs de définissent une égalité de deux `List<T>
` instances comme la comparaison de chacun des objets de relation contenant-contenus à l’aide de leurs `Equals` méthodes. L’exemple suivant utilise l’opérateur `==` pour comparer deux instances de `List<int>
`.

```csharp
using System;

class Test
{
    static void Main() {
        List<int> a = new List<int>();
        a.Add(1);
        a.Add(2);
        List<int> b = new List<int>();
        b.Add(1);
        b.Add(2);
        Console.WriteLine(a == b);        // Outputs "True"
        b.Add(3);
        Console.WriteLine(a == b);        // Outputs "False"
    }
}
```

La première `Console.WriteLine` génère `True`, car les deux listes contiennent le même nombre d’objets avec les mêmes valeurs dans le même ordre. Si `List<T>
` n’avait pas défini `operator==`, la première `Console.WriteLine` aurait affiché `False`, car `a` et `b` référencent des instances `List<int>
` différentes.

#### <a name="destructors"></a>Destructeurs

Un ***destructeur*** est un membre qui implémente les actions requises pour détruire une instance d’une classe. Les destructeurs ne peuvent pas avoir de paramètres, ils ne peuvent pas avoir de modificateurs d’accessibilité et ils ne peuvent pas être appelés explicitement. Le destructeur d’une instance est appelé automatiquement lors du garbage collection.

Le garbage collector est autorisé à toute latitude pour déterminer quand collecter des objets et exécuter des destructeurs. Plus précisément, le minutage des appels de destructeur n’est pas déterministe, et les destructeurs ne peuvent être exécutés sur n’importe quel thread. Pour ces raisons et d’autres, les classes doivent implémenter des destructeurs uniquement lorsque aucune des autres solutions ne sont envisageables.

L’instruction `using` fournit une meilleure approche pour la destruction d’objets.

## <a name="structs"></a>Structs

Comme les classes, les ***structs*** sont des structures de données qui peuvent contenir des membres de données et des fonctions membres. Mais contrairement aux classes, les structures sont des types valeur et ne nécessitent pas d’allocation de tas. Une variable de type struct stocke directement les données de la structure, alors qu’une variable de type class stocke une référence à un objet alloué dynamiquement. Les types struct ne prennent pas en charge l’héritage spécifié par l’utilisateur, et tous les types struct héritent implicitement du type `object`.

Les structures sont particulièrement utiles pour les petites structures de données qui ont une sémantique par rapport à leurs valeurs. Les nombres complexes, les points dans un système de coordonnées ou les paires clé-valeur dans un dictionnaire sont de bons exemples de structures. L’utilisation de structures plutôt que de classes pour les petites structures de données peut faire une grande différence dans le nombre d’allocations de mémoire effectuées par une application. Par exemple, le programme suivant crée et initialise un tableau de 100 points. Avec `Point` implémenté en tant que classe, 101 objets séparés sont instanciés : un pour le tableau et un pour chacun des 100 éléments.

```csharp
class Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

class Test
{
    static void Main() {
        Point[] points = new Point[100];
        for (int i = 0; i < 100; i++) points[i] = new Point(i, i);
    }
}
```
Une alternative consiste à faire `Point` un struct.

```csharp
struct Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
Maintenant, un seul objet est instancié, celui du tableau, et les instances `Point` sont stockées à la suite dans le tableau.

Vous appelez les constructeurs de struct avec l’opérateur `new`, mais cela n’implique pas que de la mémoire soit allouée. Au lieu d’allouer dynamiquement un objet et de renvoyer une référence à cet objet, un constructeur de struct retourne simplement la valeur du struct (généralement dans un emplacement temporaire sur la pile), et cette valeur est ensuite copiée si nécessaire.

Avec les classes, deux variables peuvent faire référence au même objet et, par conséquent, les opérations sur une variable peuvent affecter le même objet référencé par l'autre variable. Avec les structs, les variables disposent chacune de leur propre copie des données, et il n’est pas possible pour les opérations sur une d’affecter les autres. Par exemple, la sortie produite par le fragment de code suivant varie selon que `Point` est une classe ou un struct.

```csharp
Point a = new Point(10, 10);
Point b = a;
a.x = 20;
Console.WriteLine(b.x);
```
Si `Point` est une classe, la sortie est `20` car `a` et `b` référencent le même objet. Si `Point` est un struct, la sortie est `10` , car l’affectation de `a` à `b` crée une copie de la valeur, et cette copie n’est pas affectée par l’assignation consécutive de `a.x`.

L’exemple précédent illustre deux des limitations des structs. Tout d’abord, la copie d’un struct entier est généralement moins efficace que la copie d’une référence d’objet, aussi le passage de paramètres d’affectation et de valeur peut être plus coûteux avec les structures qu’avec les types référence. Ensuite, à l’exception des paramètres `ref` et `out`, il n’est pas possible de créer des références aux structures, ce qui rend leur utilisation impossible dans un certain nombre de situations.

## <a name="arrays"></a>Tableaux

Un ***tableau*** est une structure de données qui contient un certain nombre de variables qui sont accessibles par des indices calculés. Les variables contenues dans un tableau, également appelé ***éléments*** du tableau, sont tous du même type, et ce type est appelé ***type d’élément*** du tableau.

Les types tableau sont des types référence, et la déclaration d’une variable tableau réserve simplement un espace pour une référence à une instance de tableau. Instances de tableau réelles sont créées dynamiquement au moment de l’exécution à l’aide du `new` opérateur. Le `new` opération spécifie la ***longueur*** de la nouvelle instance de tableau, qui est ensuite fixée pour la durée de vie de l’instance. Les indices des éléments d’un tableau vont de `0` à `Length - 1`. L’opérateur `new` initialise automatiquement les éléments d’un tableau à leur valeur par défaut, c'est-à-dire, par exemple, zéro pour tous les types numériques et `null` pour tous les types référence.

L’exemple suivant crée un tableau de `int` éléments, initialise le tableau et imprime le contenu du tableau.

```csharp
using System;

class Test
{
    static void Main() {
        int[] a = new int[10];
        for (int i = 0; i < a.Length; i++) {
            a[i] = i * i;
        }
        for (int i = 0; i < a.Length; i++) {
            Console.WriteLine("a[{0}] = {1}", i, a[i]);
        }
    }
}
```
Cet exemple crée et utilise un ***tableau unidimensionnel***. C# prend également en charge les ***tableaux multidimensionnels***. Le nombre de dimensions d’un type tableau, également appelé ***rang*** du type tableau, est de un plus le nombre de virgules entre les crochets du type tableau. L’exemple suivant alloue un tableau tridimensionnel, une dimension et un à deux dimensions.

```csharp
int[] a1 = new int[10];
int[,] a2 = new int[10, 5];
int[,,] a3 = new int[10, 5, 2];
```
Le tableau `a1` contient 10 éléments, le tableau `a2` en contient 50 (10 x 5) et le tableau `a3` en contient 100 (10 × 5 × 2).

Le type d’élément d’un tableau peut être de n’importe quel type, y compris un type tableau. Un tableau avec des éléments d’un type tableau est parfois appelé un ***tableau en escalier***, car les longueurs des tableaux d’éléments ne sont pas nécessairement pas les mêmes. L’exemple suivant alloue un tableau de tableaux de `int` :

```csharp
int[][] a = new int[3][];
a[0] = new int[10];
a[1] = new int[5];
a[2] = new int[20];
```
La première ligne crée un tableau avec trois éléments, chacun de type `int[]` et chacun avec une valeur initiale de `null`. Les lignes suivantes initialisent ensuite les trois éléments avec des références aux instances individuelles de tableau de longueur variable.

Le `new` opérateur autorise les valeurs initiales des éléments du tableau en utilisant un ***initialiseur de tableau***, qui est une liste d’expressions écrites entre les délimiteurs `{` et `}`. L’exemple suivant alloue et initialise un `int[]` avec trois éléments.

```csharp
int[] a = new int[] {1, 2, 3};
```
Notez que la longueur du tableau est déduite à partir du nombre d’expressions entre `{` et `}`. Les déclarations locales des champs et variables peuvent être abrégées davantage afin de ne pas avoir à redéclarer le type tableau.

```csharp
int[] a = {1, 2, 3};
```
Les deux exemples précédents sont équivalents à ce qui suit :

```csharp
int[] t = new int[3];
t[0] = 1;
t[1] = 2;
t[2] = 3;
int[] a = t;
```
## <a name="interfaces"></a>Interfaces

Une ***interface*** définit un contrat qui peut être implémenté par des classes et structures. Une interface peut contenir des méthodes, des propriétés, des événements et des indexeurs. Une interface ne fournit pas les implémentations des membres qu’elle définit, elle indique simplement les membres qui doivent être fournis par les classes ou les structs qui implémentent l’interface.

Les interfaces peuvent utiliser ***l’héritage multiple***. Dans l’exemple suivant, l’interface `IComboBox` hérite à la fois de `ITextBox` et `IListBox`.

```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

interface IListBox: IControl
{
    void SetItems(string[] items);
}

interface IComboBox: ITextBox, IListBox {}
```
Les classes et les structs peuvent implémenter plusieurs interfaces. Dans l’exemple suivant, la classe `EditBox` implémente à la fois `IControl` et `IDataBound`.

```csharp
interface IDataBound
{
    void Bind(Binder b);
}

public class EditBox: IControl, IDataBound
{
    public void Paint() {...}
    public void Bind(Binder b) {...}
}
```
Lorsqu’une classe ou un struct implémente une interface spécifique, les instances de cette classe ou struct peuvent être converties implicitement en ce type d’interface. Exemple :

```csharp
EditBox editBox = new EditBox();
IControl control = editBox;
IDataBound dataBound = editBox;
```
Si une instance n’est pas connue pour implémenter une interface donnée de façon statique, des casts de type dynamiques peuvent être utilisés. Par exemple, les instructions suivantes utilisent des casts de type dynamique pour obtenir d’un objet `IControl` et `IDataBound` implémentations d’interface. Étant donné que le type réel de l’objet est `EditBox`, les casts réussissent.

```csharp
object obj = new EditBox();
IControl control = (IControl)obj;
IDataBound dataBound = (IDataBound)obj;
```
Dans le précédent `EditBox` (classe), le `Paint` méthode à partir de la `IControl` interface et le `Bind` méthode à partir de la `IDataBound` interface sont implémentées à l’aide de `public` membres. C# prend également en charge ***implémentations de membres d’interface explicite***, à l’aide de laquelle la classe ou struct permettre éviter d’effectuer les membres `public`. Une implémentation de membre d’interface explicite est écrite à l’aide du nom de membre d’interface qualifié complet. Par exemple, la classe `EditBox` peut implémenter les méthodes `IControl.Paint` et `IDataBound.Bind` à l’aide des implémentations de membres d’interface explicites, comme suit.

```csharp
public class EditBox: IControl, IDataBound
{
    void IControl.Paint() {...}
    void IDataBound.Bind(Binder b) {...}
}
```
Les membres d’interface explicites sont accessibles uniquement via le type interface. Par exemple, l’implémentation de `IControl.Paint` fournie par la précédente `EditBox` classe ne peut être appelée en convertissant d’abord en le `EditBox` font référence à la `IControl` type d’interface.

```csharp
EditBox editBox = new EditBox();
editBox.Paint();                        // Error, no such method
IControl control = editBox;
control.Paint();                        // Ok
```

## <a name="enums"></a>Enums

Un ***type enum*** est un type valeur distinct avec un ensemble de constantes nommées. L’exemple suivant déclare et utilise un type enum nommé `Color` avec trois valeurs de constante, `Red`, `Green`, et `Blue`.

```csharp
using System;

enum Color
{
    Red,
    Green,
    Blue
}

class Test
{
    static void PrintColor(Color color) {
        switch (color) {
            case Color.Red:
                Console.WriteLine("Red");
                break;
            case Color.Green:
                Console.WriteLine("Green");
                break;
            case Color.Blue:
                Console.WriteLine("Blue");
                break;
            default:
                Console.WriteLine("Unknown color");
                break;
        }
    }

    static void Main() {
        Color c = Color.Red;
        PrintColor(c);
        PrintColor(Color.Blue);
    }
}
```
Chaque type énumération a un type intégral appelé le ***type sous-jacent*** du type enum. Un type enum qui ne déclare pas explicitement un type sous-jacent a un type sous-jacent de `int`. Un type enum le format de stockage et la plage de valeurs possibles sont déterminés par son type sous-jacent. L’ensemble de valeurs qui peut prendre un type enum n’est pas limité par ses membres enum. En particulier, n’importe quelle valeur du type sous-jacent d’une énumération peut être casté en type enum et est une valeur valide distincte de ce type enum.

L’exemple suivant déclare un type enum nommé `Alignment` avec un type sous-jacent de `sbyte`.

```csharp
enum Alignment: sbyte
{
    Left = -1,
    Center = 0,
    Right = 1
}
```
Comme le montre l’exemple précédent, une déclaration de membre enum peut inclure une expression constante qui spécifie la valeur du membre. La valeur de constante pour chaque membre enum doit être dans la plage du type sous-jacent de l’enum. Lorsqu’une déclaration de membre enum ne spécifie pas explicitement une valeur, le membre reçoit la valeur zéro (s’il est le premier membre dans le type enum) ou la valeur du membre enum qui précède plus un.

Valeurs d’énumération peuvent être converties en valeurs intégrales et inversement à l’aide de casts de type. Exemple :

```csharp
int i = (int)Color.Blue;        // int i = 2;
Color c = (Color)2;             // Color c = Color.Blue;
```
La valeur par défaut de tout type enum est la valeur intégrale de zéro convertie vers le type enum. Dans les cas où les variables sont initialisées automatiquement à une valeur par défaut, il s’agit de la valeur donnée aux variables des types enum. Afin que la valeur par défaut d’un type enum soit disponible, le littéral `0` convertit implicitement en un type enum. Ce qui suit est donc autorisé.

```csharp
Color c = 0;
```

## <a name="delegates"></a>Délégués

Un ***type délégué*** représente des références aux méthodes avec une liste de paramètres et un type de retour particuliers. Les délégués permettent de traiter les méthodes en tant qu’entités qui peuvent être affectées à des variables et passées comme paramètres. Les délégués sont similaires au concept de pointeurs de fonction dans d’autres langages, mais contrairement aux pointeurs de fonction, les délégués sont orientés objet et de type sécurisé.

L’exemple suivant déclare et utilise un type délégué nommé `Function`.

```csharp
using System;

delegate double Function(double x);

class Multiplier
{
    double factor;

    public Multiplier(double factor) {
        this.factor = factor;
    }

    public double Multiply(double x) {
        return x * factor;
    }
}

class Test
{
    static double Square(double x) {
        return x * x;
    }

    static double[] Apply(double[] a, Function f) {
        double[] result = new double[a.Length];
        for (int i = 0; i < a.Length; i++) result[i] = f(a[i]);
        return result;
    }

    static void Main() {
        double[] a = {0.0, 0.5, 1.0};
        double[] squares = Apply(a, Square);
        double[] sines = Apply(a, Math.Sin);
        Multiplier m = new Multiplier(2.0);
        double[] doubles =  Apply(a, m.Multiply);
    }
}
```
Une instance du type délégué `Function` peut faire référence à toute méthode qui accepte un argument `double` et retourne une valeur `double`. Le `Apply` méthode s’applique une donnée `Function` aux éléments d’un `double[]`, qui retourne un `double[]` avec les résultats. Dans la méthode `Main`, `Apply` sert à appliquer trois fonctions différentes à un `double[]`.

Un délégué peut faire référence à une méthode statique (comme `Square` ou `Math.Sin` dans l’exemple précédent) ou à une méthode d’instance (comme `m.Multiply` dans l’exemple précédent). Un délégué qui référence une méthode d’instance référence aussi un objet particulier, et quand la méthode d’instance est appelée via le délégué, cet objet devient `this` dans l’appel.

Les délégués peuvent également être créés à l’aide de fonctions anonymes, qui sont des « méthodes inline » créées à la volée. Les fonctions anonymes peuvent voir les variables locales des méthodes qui l’entourent. Par conséquent, l’exemple de multiplicateur ci-dessus peut être écrit plus facilement sans utiliser un `Multiplier` classe :

```csharp
double[] doubles =  Apply(a, (double x) => x * 2.0);
```
Une propriété intéressante et utile d’un délégué est qu’il ne connaît pas la classe de la méthode qu’il référence, et cela lui est égal. Tout ce qui importe est que la méthode référencée ait les mêmes paramètres et type de retour que le délégué.

## <a name="attributes"></a>Attributs

Les types, membres et autres entités d’un programme C# prennent en charge les modificateurs qui contrôlent certains aspects de leur comportement. Par exemple, l’accessibilité d’une méthode est contrôlée à l’aide des modificateurs `public`, `protected`, `internal` et `private`. C# généralise cette fonctionnalité pour que les types d’informations déclaratives définis par l’utilisateur puissent être associés aux entités de programme et récupérés au moment de l’exécution. Les programmes spécifient ces informations déclaratives supplémentaires en définissant et en utilisant les ***attributs***.

L’exemple suivant déclare un attribut `HelpAttribute` qui peut être placé sur des entités de programme pour fournir des liens vers leur documentation associée.

```csharp
using System;

public class HelpAttribute: Attribute
{
    string url;
    string topic;

    public HelpAttribute(string url) {
        this.url = url;
    }

    public string Url {
        get { return url; }
    }

    public string Topic {
        get { return topic; }
        set { topic = value; }
    }
}
```
Toutes les classes d’attributs dérivent le `System.Attribute` fourni par le .NET Framework de classe de base. Les attributs peuvent être appliqués en donnant leur nom, ainsi que les éventuels arguments, à l’intérieur de crochets juste avant la déclaration associée. Si le nom d’un attribut se termine par `Attribute`, cette partie du nom peut être omise lorsque l’attribut est référencé. Par exemple, l’attribut `HelpAttribute` peut être utilisé comme suit.

```csharp
[Help("http://msdn.microsoft.com/.../MyClass.htm")]
public class Widget
{
    [Help("http://msdn.microsoft.com/.../MyClass.htm", Topic = "Display")]
    public void Display(string text) {}
}
```
Cet exemple attache un `HelpAttribute` à la `Widget` classe et l’autre `HelpAttribute` à la `Display` méthode dans la classe. Les constructeurs publics d’une classe d’attributs contrôlent les informations qui doivent être fournies lorsque l’attribut est joint à une entité de programme. Des informations supplémentaires peuvent être fournies en référençant les propriétés en lecture-écriture publiques de la classe d’attributs (comme la référence à la propriété `Topic`, précédemment).

L’exemple suivant montre comment les informations d’attribut pour une entité de programme donné peuvent être récupérées au moment de l’exécution à l’aide de la réflexion.

```csharp
using System;
using System.Reflection;

class Test
{
    static void ShowHelp(MemberInfo member) {
        HelpAttribute a = Attribute.GetCustomAttribute(member,
            typeof(HelpAttribute)) as HelpAttribute;
        if (a == null) {
            Console.WriteLine("No help for {0}", member);
        }
        else {
            Console.WriteLine("Help for {0}:", member);
            Console.WriteLine("  Url={0}, Topic={1}", a.Url, a.Topic);
        }
    }

    static void Main() {
        ShowHelp(typeof(Widget));
        ShowHelp(typeof(Widget).GetMethod("Display"));
    }
}
```
Lorsqu’un attribut particulier est demandé par réflexion, le constructeur de la classe d’attributs est appelé avec les informations fournies dans la source du programme, et l’instance d’attribut qui en résulte est retournée. Si des informations supplémentaires ont été fournies via les propriétés, ces propriétés sont définies sur les valeurs données avant que le renvoi de l’instance de l’attribut.
