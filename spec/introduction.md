---
ms.openlocfilehash: 9a9baf63b83ae4eb8af0e3b8c65ed3256222f12f
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488891"
---
# <a name="introduction"></a><span data-ttu-id="7a608-101">Introduction</span><span class="sxs-lookup"><span data-stu-id="7a608-101">Introduction</span></span>

<span data-ttu-id="7a608-102">C# (prononcé « C Sharp ») est un langage de programmation simple, moderne, orienté objet et de type sécurisé.</span><span class="sxs-lookup"><span data-stu-id="7a608-102">C# (pronounced "See Sharp") is a simple, modern, object-oriented, and type-safe programming language.</span></span> <span data-ttu-id="7a608-103">C# a ses racines dans la famille de langages C et sera immédiatement reconnaissable aux programmeurs en C, C++ et Java.</span><span class="sxs-lookup"><span data-stu-id="7a608-103">C# has its roots in the C family of languages and will be immediately familiar to C, C++, and Java programmers.</span></span> <span data-ttu-id="7a608-104">C# est normalisé par ECMA International en tant que le ***ECMA-334*** standard et par la norme ISO/IEC comme le ***ISO/CEI 23270*** standard.</span><span class="sxs-lookup"><span data-stu-id="7a608-104">C# is standardized by ECMA International as the ***ECMA-334*** standard and by ISO/IEC as the ***ISO/IEC 23270*** standard.</span></span> <span data-ttu-id="7a608-105">Compilateur c# de Microsoft pour le .NET Framework est une implémentation conforme de ces deux normes.</span><span class="sxs-lookup"><span data-stu-id="7a608-105">Microsoft's C# compiler for the .NET Framework is a conforming implementation of both of these standards.</span></span>

<span data-ttu-id="7a608-106">C# est un langage orienté objet, mais C# inclut de plus la prise en charge de la programmation ***orientée composant***.</span><span class="sxs-lookup"><span data-stu-id="7a608-106">C# is an object-oriented language, but C# further includes support for ***component-oriented*** programming.</span></span> <span data-ttu-id="7a608-107">La conception logicielle moderne s’appuie de plus en plus sur les composants logiciels sous la forme de packages de fonctionnalités autonomes et autodescriptifs.</span><span class="sxs-lookup"><span data-stu-id="7a608-107">Contemporary software design increasingly relies on software components in the form of self-contained and self-describing packages of functionality.</span></span> <span data-ttu-id="7a608-108">Point important, ces composants présentent un modèle de programmation avec propriétés, méthodes et événements ; ils ont des attributs qui fournissent des informations déclaratives sur le composant ; et ils intègrent leur propre documentation.</span><span class="sxs-lookup"><span data-stu-id="7a608-108">Key to such components is that they present a programming model with properties, methods, and events; they have attributes that provide declarative information about the component; and they incorporate their own documentation.</span></span> <span data-ttu-id="7a608-109">C# fournit des constructions de langage qui prennent directement en charge ces concepts, rendre c# un langage très naturel permettant de créer et utiliser des composants logiciels.</span><span class="sxs-lookup"><span data-stu-id="7a608-109">C# provides language constructs to directly support these concepts, making C# a very natural language in which to create and use software components.</span></span>

<span data-ttu-id="7a608-110">Plusieurs fonctionnalités de C# participent à la construction d’applications robustes et fiables : ***Le garbage collection*** récupère automatiquement la mémoire occupée par les objets inutilisés ; ***gestion des exceptions*** offre une approche structurée et extensible pour la détection d’erreur et de récupération ; et le ***type-safe*** conception du langage ne permet pas de lire à partir de variables non initialisées, pour indexer des tableaux au-delà de leurs limites, ou réaliser unchecked casts de type.</span><span class="sxs-lookup"><span data-stu-id="7a608-110">Several C# features aid in the construction of robust and durable applications: ***Garbage collection*** automatically reclaims memory occupied by unused objects; ***exception handling*** provides a structured and extensible approach to error detection and recovery; and the ***type-safe*** design of the language makes it impossible to read from uninitialized variables, to index arrays beyond their bounds, or to perform unchecked type casts.</span></span>

<span data-ttu-id="7a608-111">C# a un ***système de type unifié***.</span><span class="sxs-lookup"><span data-stu-id="7a608-111">C# has a ***unified type system***.</span></span> <span data-ttu-id="7a608-112">Tous les types C#, y compris les types primitifs tels que `int` et `double`, héritent d’un seul type `object` racine.</span><span class="sxs-lookup"><span data-stu-id="7a608-112">All C# types, including primitive types such as `int` and `double`, inherit from a single root `object` type.</span></span> <span data-ttu-id="7a608-113">Par conséquent, tous les types partagent un ensemble d’opérations communes, et des valeurs de tous types peuvent être stockées, transmises et exploitées de manière cohérente.</span><span class="sxs-lookup"><span data-stu-id="7a608-113">Thus, all types share a set of common operations, and values of any type can be stored, transported, and operated upon in a consistent manner.</span></span> <span data-ttu-id="7a608-114">En outre, C# prend en charge les types référence et les types valeur définis par l’utilisateur, ce qui permet l’allocation dynamique d’objets, ainsi que le stockage en ligne de structures légères.</span><span class="sxs-lookup"><span data-stu-id="7a608-114">Furthermore, C# supports both user-defined reference types and value types, allowing dynamic allocation of objects as well as in-line storage of lightweight structures.</span></span>

<span data-ttu-id="7a608-115">Pour vous assurer que les bibliothèques et les programmes c# peuvent évoluer au fil du temps de manière compatible, l’accent a été placé sur ***versioning*** dans la conception de #.</span><span class="sxs-lookup"><span data-stu-id="7a608-115">To ensure that C# programs and libraries can evolve over time in a compatible manner, much emphasis has been placed on ***versioning*** in C#'s design.</span></span> <span data-ttu-id="7a608-116">De nombreux langages de programmation n’accordent que peu d’attention à ce problème ; par conséquent, les programmes écrits dans ces langages s’arrêtent plus souvent que nécessaire lors de l’introduction de versions plus récentes des bibliothèques dépendantes.</span><span class="sxs-lookup"><span data-stu-id="7a608-116">Many programming languages pay little attention to this issue, and, as a result, programs written in those languages break more often than necessary when newer versions of dependent libraries are introduced.</span></span> <span data-ttu-id="7a608-117">Aspects de C#include de conception qui ont été directement influencés par les considérations relatives à la gestion des versions de la distinct `virtual` et `override` modificateurs, les règles de résolution de surcharge de méthode et la prise en charge pour les déclarations de membre d’interface explicite.</span><span class="sxs-lookup"><span data-stu-id="7a608-117">Aspects of C#'s design that were directly influenced by versioning considerations include the separate `virtual` and `override` modifiers, the rules for method overload resolution, and support for explicit interface member declarations.</span></span>

<span data-ttu-id="7a608-118">Le reste de ce chapitre décrit les principales fonctionnalités du langage c#.</span><span class="sxs-lookup"><span data-stu-id="7a608-118">The rest of this chapter describes the essential features of the C# language.</span></span> <span data-ttu-id="7a608-119">Bien que les chapitres suivants décrivent les règles et les exceptions de manière très détaillée et parfois mathématique, ce chapitre s’efforce de clarté et la concision au détriment d’exhaustivité.</span><span class="sxs-lookup"><span data-stu-id="7a608-119">Although later chapters describe rules and exceptions in a detail-oriented and sometimes mathematical manner, this chapter strives for clarity and brevity at the expense of completeness.</span></span> <span data-ttu-id="7a608-120">L’objectif est de fournir au lecteur une introduction au langage qui facilite l’écriture de programmes précoce et la lecture des chapitres.</span><span class="sxs-lookup"><span data-stu-id="7a608-120">The intent is to provide the reader with an introduction to the language that will facilitate the writing of early programs and the reading of later chapters.</span></span>

## <a name="hello-world"></a><span data-ttu-id="7a608-121">Hello World</span><span class="sxs-lookup"><span data-stu-id="7a608-121">Hello world</span></span>

<span data-ttu-id="7a608-122">Le programme « Hello, World » est souvent utilisé pour présenter un langage de programmation.</span><span class="sxs-lookup"><span data-stu-id="7a608-122">The "Hello, World" program is traditionally used to introduce a programming language.</span></span> <span data-ttu-id="7a608-123">Le voici en C# :</span><span class="sxs-lookup"><span data-stu-id="7a608-123">Here it is in C#:</span></span>

```csharp
using System;

class Hello
{
    static void Main() {
        Console.WriteLine("Hello, World");
    }
}
```

<span data-ttu-id="7a608-124">Les fichiers sources C# ont généralement l’extension de fichier `.cs`.</span><span class="sxs-lookup"><span data-stu-id="7a608-124">C# source files typically have the file extension `.cs`.</span></span> <span data-ttu-id="7a608-125">En supposant que le programme « Hello, World » est stocké dans le fichier `hello.cs`, le programme peut être compilé avec le compilateur Microsoft c# à l’aide de la ligne de commande</span><span class="sxs-lookup"><span data-stu-id="7a608-125">Assuming that the "Hello, World" program is stored in the file `hello.cs`, the program can be compiled with the Microsoft C# compiler using the command line</span></span>
```
csc hello.cs
```
<span data-ttu-id="7a608-126">ce qui génère un assembly exécutable nommé `hello.exe`.</span><span class="sxs-lookup"><span data-stu-id="7a608-126">which produces an executable assembly named `hello.exe`.</span></span> <span data-ttu-id="7a608-127">La sortie générée par cette application lorsqu’elle est exécutée</span><span class="sxs-lookup"><span data-stu-id="7a608-127">The output produced by this application when it is run is</span></span>
```
Hello, World
```

<span data-ttu-id="7a608-128">Le programme « Hello, World » commence par une directive `using` qui fait référence à l’espace de noms `System`.</span><span class="sxs-lookup"><span data-stu-id="7a608-128">The "Hello, World" program starts with a `using` directive that references the `System` namespace.</span></span> <span data-ttu-id="7a608-129">Les espaces de noms représentent un moyen hiérarchique d’organiser les bibliothèques et les programmes C#.</span><span class="sxs-lookup"><span data-stu-id="7a608-129">Namespaces provide a hierarchical means of organizing C# programs and libraries.</span></span> <span data-ttu-id="7a608-130">Les espaces de noms contiennent des types et d’autres espaces de noms ; par exemple, l’espace de noms `System` contient plusieurs types, notamment la classe `Console` référencée dans le programme, et d’autres espaces de noms, tels que `IO` et `Collections`.</span><span class="sxs-lookup"><span data-stu-id="7a608-130">Namespaces contain types and other namespaces—for example, the `System` namespace contains a number of types, such as the `Console` class referenced in the program, and a number of other namespaces, such as `IO` and `Collections`.</span></span> <span data-ttu-id="7a608-131">Une directive `using` qui fait référence à un espace de noms donné permet l’utilisation non qualifiée des types membres de cet espace de noms.</span><span class="sxs-lookup"><span data-stu-id="7a608-131">A `using` directive that references a given namespace enables unqualified use of the types that are members of that namespace.</span></span> <span data-ttu-id="7a608-132">En raison de la directive `using`, le programme peut utiliser `Console.WriteLine` comme raccourci pour `System.Console.WriteLine`.</span><span class="sxs-lookup"><span data-stu-id="7a608-132">Because of the `using` directive, the program can use `Console.WriteLine` as shorthand for `System.Console.WriteLine`.</span></span>

<span data-ttu-id="7a608-133">La classe `Hello` déclarée par le programme « Hello, World » a un membre unique, la méthode nommée `Main`.</span><span class="sxs-lookup"><span data-stu-id="7a608-133">The `Hello` class declared by the "Hello, World" program has a single member, the method named `Main`.</span></span> <span data-ttu-id="7a608-134">Le `Main` méthode est déclarée avec le `static` modificateur.</span><span class="sxs-lookup"><span data-stu-id="7a608-134">The `Main` method is declared with the `static` modifier.</span></span> <span data-ttu-id="7a608-135">Si les méthodes d’instance peuvent faire référence à une instance d’objet englobante particulière avec le mot clé `this`, les méthodes statiques fonctionnent sans référence à un objet particulier.</span><span class="sxs-lookup"><span data-stu-id="7a608-135">While instance methods can reference a particular enclosing object instance using the keyword `this`, static methods operate without reference to a particular object.</span></span> <span data-ttu-id="7a608-136">Par convention, une méthode statique nommée `Main` sert de point d’entrée d’un programme.</span><span class="sxs-lookup"><span data-stu-id="7a608-136">By convention, a static method named `Main` serves as the entry point of a program.</span></span>

<span data-ttu-id="7a608-137">La sortie du programme est générée par la méthode `WriteLine` de la classe `Console` dans l’espace de noms `System`.</span><span class="sxs-lookup"><span data-stu-id="7a608-137">The output of the program is produced by the `WriteLine` method of the `Console` class in the `System` namespace.</span></span> <span data-ttu-id="7a608-138">Cette classe est fournie par les bibliothèques de classes .NET Framework, qui, par défaut, sont référencés automatiquement par le compilateur Microsoft C#.</span><span class="sxs-lookup"><span data-stu-id="7a608-138">This class is provided by the .NET Framework class libraries, which, by default, are automatically referenced by the Microsoft C# compiler.</span></span> <span data-ttu-id="7a608-139">Notez que c# lui-même n’a pas une bibliothèque d’exécution distinct.</span><span class="sxs-lookup"><span data-stu-id="7a608-139">Note that C# itself does not have a separate runtime library.</span></span> <span data-ttu-id="7a608-140">Au lieu de cela, le .NET Framework est la bibliothèque runtime du langage c#.</span><span class="sxs-lookup"><span data-stu-id="7a608-140">Instead, the .NET Framework is the runtime library of C#.</span></span>

## <a name="program-structure"></a><span data-ttu-id="7a608-141">Structure de programme</span><span class="sxs-lookup"><span data-stu-id="7a608-141">Program structure</span></span>

<span data-ttu-id="7a608-142">Les concepts clés d’organisation en C# sont les ***programmes***, ***espaces de noms***, ***types***, ***membres*** et ***assemblys***.</span><span class="sxs-lookup"><span data-stu-id="7a608-142">The key organizational concepts in C# are ***programs***, ***namespaces***, ***types***, ***members***, and ***assemblies***.</span></span> <span data-ttu-id="7a608-143">Les programmes C++, comme les programmes C, se composent d'un ou plusieurs fichiers sources.</span><span class="sxs-lookup"><span data-stu-id="7a608-143">C# programs consist of one or more source files.</span></span> <span data-ttu-id="7a608-144">Les programmes déclarent des types qui contiennent des membres et peuvent être organisés en espaces de noms.</span><span class="sxs-lookup"><span data-stu-id="7a608-144">Programs declare types, which contain members and can be organized into namespaces.</span></span> <span data-ttu-id="7a608-145">Les classes et les interfaces sont des exemples de types.</span><span class="sxs-lookup"><span data-stu-id="7a608-145">Classes and interfaces are examples of types.</span></span> <span data-ttu-id="7a608-146">Les champs, méthodes, propriétés et événements sont des exemples de membres.</span><span class="sxs-lookup"><span data-stu-id="7a608-146">Fields, methods, properties, and events are examples of members.</span></span> <span data-ttu-id="7a608-147">Lorsque les programmes C# sont compilés, ils sont physiquement empaquetés dans des assemblys.</span><span class="sxs-lookup"><span data-stu-id="7a608-147">When C# programs are compiled, they are physically packaged into assemblies.</span></span> <span data-ttu-id="7a608-148">Assemblys ont généralement l’extension de fichier `.exe` ou `.dll`, selon qu’ils implémentent ***applications*** ou ***bibliothèques***.</span><span class="sxs-lookup"><span data-stu-id="7a608-148">Assemblies typically have the file extension `.exe` or `.dll`, depending on whether they implement ***applications*** or ***libraries***.</span></span>

<span data-ttu-id="7a608-149">L’exemple</span><span class="sxs-lookup"><span data-stu-id="7a608-149">The example</span></span>

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
<span data-ttu-id="7a608-150">déclare une classe nommée `Stack` dans un espace de noms appelé `Acme.Collections`.</span><span class="sxs-lookup"><span data-stu-id="7a608-150">declares a class named `Stack` in a namespace called `Acme.Collections`.</span></span> <span data-ttu-id="7a608-151">Le nom qualifié complet de cette classe est `Acme.Collections.Stack`.</span><span class="sxs-lookup"><span data-stu-id="7a608-151">The fully qualified name of this class is `Acme.Collections.Stack`.</span></span> <span data-ttu-id="7a608-152">La classe contient plusieurs membres : un champ nommé `top`, deux méthodes nommées `Push` et `Pop`, et une classe imbriquée nommée `Entry`.</span><span class="sxs-lookup"><span data-stu-id="7a608-152">The class contains several members: a field named `top`, two methods named `Push` and `Pop`, and a nested class named `Entry`.</span></span> <span data-ttu-id="7a608-153">La classe `Entry` contient trois membres en plus : un champ nommé `next`, un autre nommé `data` et un constructeur.</span><span class="sxs-lookup"><span data-stu-id="7a608-153">The `Entry` class further contains three members: a field named `next`, a field named `data`, and a constructor.</span></span> <span data-ttu-id="7a608-154">En supposant que le code source de l’exemple est stocké dans le fichier `acme.cs`, la ligne de commande</span><span class="sxs-lookup"><span data-stu-id="7a608-154">Assuming that the source code of the example is stored in the file `acme.cs`, the command line</span></span>

```
csc /t:library acme.cs
```
<span data-ttu-id="7a608-155">compile l’exemple en tant que bibliothèque (code sans point d’entrée `Main`) et produit un assembly nommé `acme.dll`.</span><span class="sxs-lookup"><span data-stu-id="7a608-155">compiles the example as a library (code without a `Main` entry point) and produces an assembly named `acme.dll`.</span></span>

<span data-ttu-id="7a608-156">Les assemblys contiennent du code exécutable sous la forme de ***Intermediate Language*** instructions (IL) et des informations symboliques sous la forme de ***métadonnées***.</span><span class="sxs-lookup"><span data-stu-id="7a608-156">Assemblies contain executable code in the form of ***Intermediate Language*** (IL) instructions, and symbolic information in the form of ***metadata***.</span></span> <span data-ttu-id="7a608-157">Avant son exécution, le code de langage intermédiaire dans un assembly est automatiquement converti en code spécifique au processeur par le compilateur juste à temps (JIT) du Common Language Runtime .NET.</span><span class="sxs-lookup"><span data-stu-id="7a608-157">Before it is executed, the IL code in an assembly is automatically converted to processor-specific code by the Just-In-Time (JIT) compiler of .NET Common Language Runtime.</span></span>

<span data-ttu-id="7a608-158">Comme un assembly est une unité de fonctionnalité autodescriptive contenant du code et des métadonnées, les directives `#include` et les fichiers d’en-tête ne sont pas nécessaires en C#.</span><span class="sxs-lookup"><span data-stu-id="7a608-158">Because an assembly is a self-describing unit of functionality containing both code and metadata, there is no need for `#include` directives and header files in C#.</span></span> <span data-ttu-id="7a608-159">Les membres et types publics contenus dans un assembly particulier sont disponibles dans un programme C# par simple référence à cet assembly lors de la compilation du programme.</span><span class="sxs-lookup"><span data-stu-id="7a608-159">The public types and members contained in a particular assembly are made available in a C# program simply by referencing that assembly when compiling the program.</span></span> <span data-ttu-id="7a608-160">Par exemple, ce programme utilise la classe `Acme.Collections.Stack` à partir de l’assembly `acme.dll` :</span><span class="sxs-lookup"><span data-stu-id="7a608-160">For example, this program uses the `Acme.Collections.Stack` class from the `acme.dll` assembly:</span></span>

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
<span data-ttu-id="7a608-161">Si le programme est stocké dans le fichier `test.cs`, lorsque `test.cs` est compilé, le `acme.dll` assembly peut être référencé à l’aide du compilateur `/r` option :</span><span class="sxs-lookup"><span data-stu-id="7a608-161">If the program is stored in the file `test.cs`, when `test.cs` is compiled, the `acme.dll` assembly can be referenced using the compiler's `/r` option:</span></span>

```
csc /r:acme.dll test.cs
```
<span data-ttu-id="7a608-162">Cela permet de créer un assembly exécutable nommé `test.exe`, qui, lors de l’exécution, produit la sortie :</span><span class="sxs-lookup"><span data-stu-id="7a608-162">This creates an executable assembly named `test.exe`, which, when run, produces the output:</span></span>

```
100
10
1
```
<span data-ttu-id="7a608-163">C# permet le stockage du texte source d’un programme dans plusieurs fichiers source.</span><span class="sxs-lookup"><span data-stu-id="7a608-163">C# permits the source text of a program to be stored in several source files.</span></span> <span data-ttu-id="7a608-164">Lorsqu’un programme C# multifichier est compilé, tous les fichiers source sont traités ensemble et les fichiers source peuvent librement se référencer mutuellement. Sur le plan conceptuel, c’est comme si tous les fichiers source étaient concaténés en un seul fichier volumineux avant d’être traités.</span><span class="sxs-lookup"><span data-stu-id="7a608-164">When a multi-file C# program is compiled, all of the source files are processed together, and the source files can freely reference each other—conceptually, it is as if all the source files were concatenated into one large file before being processed.</span></span> <span data-ttu-id="7a608-165">Déclarations anticipées ne sont jamais nécessaires en C#, car, à de très rares exceptions près, l’ordre de déclaration n’a pas d’importance.</span><span class="sxs-lookup"><span data-stu-id="7a608-165">Forward declarations are never needed in C# because, with very few exceptions, declaration order is insignificant.</span></span> <span data-ttu-id="7a608-166">C# ne limite pas un fichier source à la déclaration d’un seul type public et ne nécessite pas non plus que le nom du fichier source corresponde à un type déclaré dans ce fichier.</span><span class="sxs-lookup"><span data-stu-id="7a608-166">C# does not limit a source file to declaring only one public type nor does it require the name of the source file to match a type declared in the source file.</span></span>

## <a name="types-and-variables"></a><span data-ttu-id="7a608-167">Types et variables</span><span class="sxs-lookup"><span data-stu-id="7a608-167">Types and variables</span></span>

<span data-ttu-id="7a608-168">Il existe deux genres de types en C# : les ***types référence*** et les ***types valeur***.</span><span class="sxs-lookup"><span data-stu-id="7a608-168">There are two kinds of types in C#: ***value types*** and ***reference types***.</span></span> <span data-ttu-id="7a608-169">Les variables des types valeur contiennent directement leurs données alors que les variables des types référence contiennent des références à leurs données, connues sous le nom d’objets.</span><span class="sxs-lookup"><span data-stu-id="7a608-169">Variables of value types directly contain their data whereas variables of reference types store references to their data, the latter being known as objects.</span></span> <span data-ttu-id="7a608-170">Avec les types référence, deux variables peuvent faire référence au même objet et, par conséquent, les opérations sur une variable peuvent affecter le même objet référencé par l'autre variable.</span><span class="sxs-lookup"><span data-stu-id="7a608-170">With reference types, it is possible for two variables to reference the same object and thus possible for operations on one variable to affect the object referenced by the other variable.</span></span> <span data-ttu-id="7a608-171">Avec les types valeur, les variables possèdent leur propre copie de données, et les opérations sur une variable ne peuvent absolument pas affecter l'autre (sauf pour les variables de paramètre `ref` et `out`).</span><span class="sxs-lookup"><span data-stu-id="7a608-171">With value types, the variables each have their own copy of the data, and it is not possible for operations on one to affect the other (except in the case of `ref` and `out` parameter variables).</span></span>

<span data-ttu-id="7a608-172">C#de types valeur sont divisés en ***types simples***, ***types enum***, ***types struct***, et ***types nullable***, et C#de types référence sont divisés en ***types de classes***, ***types d’interfaces***, ***types tableau***, et ***types délégués***.</span><span class="sxs-lookup"><span data-stu-id="7a608-172">C#'s value types are further divided into ***simple types***, ***enum types***, ***struct types***, and ***nullable types***, and C#'s reference types are further divided into ***class types***, ***interface types***, ***array types***, and ***delegate types***.</span></span>

<span data-ttu-id="7a608-173">Le tableau suivant fournit une vue d’ensemble de C#du système de type.</span><span class="sxs-lookup"><span data-stu-id="7a608-173">The following table provides an overview of C#'s type system.</span></span>

| <span data-ttu-id="7a608-174">__Catégorie__</span><span class="sxs-lookup"><span data-stu-id="7a608-174">__Category__</span></span>    |                 | <span data-ttu-id="7a608-175">__Description__</span><span class="sxs-lookup"><span data-stu-id="7a608-175">__Description__</span></span> |
|-----------------|-----------------|-----------------|
| <span data-ttu-id="7a608-176">Types de valeur</span><span class="sxs-lookup"><span data-stu-id="7a608-176">Value types</span></span>     | <span data-ttu-id="7a608-177">Types simples</span><span class="sxs-lookup"><span data-stu-id="7a608-177">Simple types</span></span>    | <span data-ttu-id="7a608-178">Entier signé : `sbyte`, `short`, `int`, `long`</span><span class="sxs-lookup"><span data-stu-id="7a608-178">Signed integral: `sbyte`, `short`, `int`, `long`</span></span> |
|                 |                 | <span data-ttu-id="7a608-179">Entier non signé : `byte`, `ushort`, `uint`, `ulong`</span><span class="sxs-lookup"><span data-stu-id="7a608-179">Unsigned integral: `byte`, `ushort`, `uint`, `ulong`</span></span> |
|                 |                 | <span data-ttu-id="7a608-180">Caractères Unicode : `char`</span><span class="sxs-lookup"><span data-stu-id="7a608-180">Unicode characters: `char`</span></span> |
|                 |                 | <span data-ttu-id="7a608-181">Virgule flottante IEEE : `float`, `double`</span><span class="sxs-lookup"><span data-stu-id="7a608-181">IEEE floating point: `float`, `double`</span></span> |
|                 |                 | <span data-ttu-id="7a608-182">Décimale haute précision :`decimal`</span><span class="sxs-lookup"><span data-stu-id="7a608-182">High-precision decimal: `decimal`</span></span> |
|                 |                 | <span data-ttu-id="7a608-183">Booléen : `bool`</span><span class="sxs-lookup"><span data-stu-id="7a608-183">Boolean: `bool`</span></span> |
|                 | <span data-ttu-id="7a608-184">Types d'enum</span><span class="sxs-lookup"><span data-stu-id="7a608-184">Enum types</span></span>      | <span data-ttu-id="7a608-185">Types définis par l'utilisateur de la forme `enum E {...}`</span><span class="sxs-lookup"><span data-stu-id="7a608-185">User-defined types of the form `enum E {...}`</span></span> |
|                 | <span data-ttu-id="7a608-186">Types struct</span><span class="sxs-lookup"><span data-stu-id="7a608-186">Struct types</span></span>    | <span data-ttu-id="7a608-187">Types définis par l'utilisateur de la forme `struct S {...}`</span><span class="sxs-lookup"><span data-stu-id="7a608-187">User-defined types of the form `struct S {...}`</span></span> |
|                 | <span data-ttu-id="7a608-188">Types Nullable</span><span class="sxs-lookup"><span data-stu-id="7a608-188">Nullable types</span></span>  | <span data-ttu-id="7a608-189">Extensions de tous les autres types de valeurs avec une valeur `null`</span><span class="sxs-lookup"><span data-stu-id="7a608-189">Extensions of all other value types with a `null` value</span></span> |
| <span data-ttu-id="7a608-190">Types référence</span><span class="sxs-lookup"><span data-stu-id="7a608-190">Reference types</span></span> | <span data-ttu-id="7a608-191">Types de classes</span><span class="sxs-lookup"><span data-stu-id="7a608-191">Class types</span></span>     | <span data-ttu-id="7a608-192">Classe de base fondamentale de tous les autres types : `object`</span><span class="sxs-lookup"><span data-stu-id="7a608-192">Ultimate base class of all other types: `object`</span></span> |
|                 |                 | <span data-ttu-id="7a608-193">Chaînes Unicode : `string`</span><span class="sxs-lookup"><span data-stu-id="7a608-193">Unicode strings: `string`</span></span> |
|                 |                 | <span data-ttu-id="7a608-194">Types définis par l'utilisateur de la forme `class C {...}`</span><span class="sxs-lookup"><span data-stu-id="7a608-194">User-defined types of the form `class C {...}`</span></span> |
|                 | <span data-ttu-id="7a608-195">Types interface</span><span class="sxs-lookup"><span data-stu-id="7a608-195">Interface types</span></span> | <span data-ttu-id="7a608-196">Types définis par l'utilisateur de la forme `interface I {...}`</span><span class="sxs-lookup"><span data-stu-id="7a608-196">User-defined types of the form `interface I {...}`</span></span> |
|                 | <span data-ttu-id="7a608-197">Types de tableaux</span><span class="sxs-lookup"><span data-stu-id="7a608-197">Array types</span></span>     | <span data-ttu-id="7a608-198">Uni et multidimensionnels, par exemple `int[]` et `int[,]`</span><span class="sxs-lookup"><span data-stu-id="7a608-198">Single- and multi-dimensional, for example, `int[]` and `int[,]`</span></span> |
|                 | <span data-ttu-id="7a608-199">Types délégués</span><span class="sxs-lookup"><span data-stu-id="7a608-199">Delegate types</span></span>  | <span data-ttu-id="7a608-200">Les types définis par l’utilisateur du formulaire, par exemple `delegate int  D(...)`</span><span class="sxs-lookup"><span data-stu-id="7a608-200">User-defined types of the form e.g. `delegate int  D(...)`</span></span> |

<span data-ttu-id="7a608-201">Les types intégraux huit prennent en charge les valeurs 8 bits, 16 bits, 32 bits et 64 bits sous forme signée ou non signée.</span><span class="sxs-lookup"><span data-stu-id="7a608-201">The eight integral types provide support for 8-bit, 16-bit, 32-bit, and 64-bit values in signed or unsigned form.</span></span>

<span data-ttu-id="7a608-202">Les deux flottante types, de points `float` et `double`, sont représentés au format les 32 bits simple précision et 64 bits double précision IEEE 754.</span><span class="sxs-lookup"><span data-stu-id="7a608-202">The two floating point types, `float` and `double`, are represented using the 32-bit single-precision and 64-bit double-precision IEEE 754 formats.</span></span>

<span data-ttu-id="7a608-203">Le type `decimal` est un type de données 128 bits adapté aux calculs financiers et monétaires.</span><span class="sxs-lookup"><span data-stu-id="7a608-203">The `decimal` type is a 128-bit data type suitable for financial and monetary calculations.</span></span>

<span data-ttu-id="7a608-204">C# `bool` type est utilisé pour représenter des valeurs booléennes, qui peuvent être `true` ou `false`.</span><span class="sxs-lookup"><span data-stu-id="7a608-204">C#'s `bool` type is used to represent boolean values—values that are either `true` or `false`.</span></span>

<span data-ttu-id="7a608-205">Le traitement des caractères et chaînes dans le langage C# utilise l’encodage Unicode.</span><span class="sxs-lookup"><span data-stu-id="7a608-205">Character and string processing in C# uses Unicode encoding.</span></span> <span data-ttu-id="7a608-206">Le type `char` représente une unité de code UTF-16, et le type `string` représente une séquence d’unités de code UTF-16.</span><span class="sxs-lookup"><span data-stu-id="7a608-206">The `char` type represents a UTF-16 code unit, and the `string` type represents a sequence of UTF-16 code units.</span></span>

<span data-ttu-id="7a608-207">Le tableau suivant récapitule C#de types numériques.</span><span class="sxs-lookup"><span data-stu-id="7a608-207">The following table summarizes C#'s numeric types.</span></span>


| <span data-ttu-id="7a608-208">__Catégorie__</span><span class="sxs-lookup"><span data-stu-id="7a608-208">__Category__</span></span>      | <span data-ttu-id="7a608-209">__Bits__</span><span class="sxs-lookup"><span data-stu-id="7a608-209">__Bits__</span></span> | <span data-ttu-id="7a608-210">__Type__</span><span class="sxs-lookup"><span data-stu-id="7a608-210">__Type__</span></span>  | <span data-ttu-id="7a608-211">__Plage/précision__</span><span class="sxs-lookup"><span data-stu-id="7a608-211">__Range/Precision__</span></span> |
|-------------------|----------|-----------|---------------------|
| <span data-ttu-id="7a608-212">Type intégral signé</span><span class="sxs-lookup"><span data-stu-id="7a608-212">Signed integral</span></span>   | <span data-ttu-id="7a608-213">8</span><span class="sxs-lookup"><span data-stu-id="7a608-213">8</span></span>        | `sbyte`   | <span data-ttu-id="7a608-214">-128...127</span><span class="sxs-lookup"><span data-stu-id="7a608-214">-128...127</span></span> |
|                   | <span data-ttu-id="7a608-215">16</span><span class="sxs-lookup"><span data-stu-id="7a608-215">16</span></span>       | `short`   | <span data-ttu-id="7a608-216">-32,768...32,767</span><span class="sxs-lookup"><span data-stu-id="7a608-216">-32,768...32,767</span></span> |
|                   | <span data-ttu-id="7a608-217">32</span><span class="sxs-lookup"><span data-stu-id="7a608-217">32</span></span>       | `int`     | <span data-ttu-id="7a608-218">-2,147,483,648...2,147,483,647</span><span class="sxs-lookup"><span data-stu-id="7a608-218">-2,147,483,648...2,147,483,647</span></span> |
|                   | <span data-ttu-id="7a608-219">64</span><span class="sxs-lookup"><span data-stu-id="7a608-219">64</span></span>       | `long`    | <span data-ttu-id="7a608-220">-9,223,372,036,854,775,808...9,223,372,036,854,775,807</span><span class="sxs-lookup"><span data-stu-id="7a608-220">-9,223,372,036,854,775,808...9,223,372,036,854,775,807</span></span> |
| <span data-ttu-id="7a608-221">Entier non signé</span><span class="sxs-lookup"><span data-stu-id="7a608-221">Unsigned integral</span></span> | <span data-ttu-id="7a608-222">8</span><span class="sxs-lookup"><span data-stu-id="7a608-222">8</span></span>        | `byte`    | <span data-ttu-id="7a608-223">0...255</span><span class="sxs-lookup"><span data-stu-id="7a608-223">0...255</span></span> |
|                   | <span data-ttu-id="7a608-224">16</span><span class="sxs-lookup"><span data-stu-id="7a608-224">16</span></span>       | `ushort`  | <span data-ttu-id="7a608-225">0...65,535</span><span class="sxs-lookup"><span data-stu-id="7a608-225">0...65,535</span></span> |
|                   | <span data-ttu-id="7a608-226">32</span><span class="sxs-lookup"><span data-stu-id="7a608-226">32</span></span>       | `uint`    | <span data-ttu-id="7a608-227">0...4,294,967,295</span><span class="sxs-lookup"><span data-stu-id="7a608-227">0...4,294,967,295</span></span> |
|                   | <span data-ttu-id="7a608-228">64</span><span class="sxs-lookup"><span data-stu-id="7a608-228">64</span></span>       | `ulong`   | <span data-ttu-id="7a608-229">0...18,446,744,073,709,551,615</span><span class="sxs-lookup"><span data-stu-id="7a608-229">0...18,446,744,073,709,551,615</span></span> |
| <span data-ttu-id="7a608-230">Virgule flottante</span><span class="sxs-lookup"><span data-stu-id="7a608-230">Floating point</span></span>    | <span data-ttu-id="7a608-231">32</span><span class="sxs-lookup"><span data-stu-id="7a608-231">32</span></span>       | `float`   | <span data-ttu-id="7a608-232">1,5 × 10 ^ −45 à 3,4 × 10 ^ 38, précision de 7 chiffres</span><span class="sxs-lookup"><span data-stu-id="7a608-232">1.5 × 10^−45 to 3.4 × 10^38, 7-digit precision</span></span> |
|                   | <span data-ttu-id="7a608-233">64</span><span class="sxs-lookup"><span data-stu-id="7a608-233">64</span></span>       | `double`  | <span data-ttu-id="7a608-234">5,0 × 10 ^ −324 à 1,7 × 10 ^ 308, précision de 15 chiffres</span><span class="sxs-lookup"><span data-stu-id="7a608-234">5.0 × 10^−324 to 1.7 × 10^308, 15-digit precision</span></span> |
| <span data-ttu-id="7a608-235">Decimal</span><span class="sxs-lookup"><span data-stu-id="7a608-235">Decimal</span></span>           | <span data-ttu-id="7a608-236">128</span><span class="sxs-lookup"><span data-stu-id="7a608-236">128</span></span>      | `decimal` | <span data-ttu-id="7a608-237">1.0 × 10 ^ −28 à 7,9 × 10 ^ 28, 28 chiffres</span><span class="sxs-lookup"><span data-stu-id="7a608-237">1.0 × 10^−28 to 7.9 × 10^28, 28-digit precision</span></span> |

<span data-ttu-id="7a608-238">Les programmes C# utilisent les ***déclarations de type*** pour créer de nouveaux types.</span><span class="sxs-lookup"><span data-stu-id="7a608-238">C# programs use ***type declarations*** to create new types.</span></span> <span data-ttu-id="7a608-239">Une déclaration de type spécifie le nom et les membres du nouveau type.</span><span class="sxs-lookup"><span data-stu-id="7a608-239">A type declaration specifies the name and the members of the new type.</span></span> <span data-ttu-id="7a608-240">Cinq de C#de catégories de types sont définis par l’utilisateur : classe de types, les types struct, les types d’interface, les types enum et les types délégués.</span><span class="sxs-lookup"><span data-stu-id="7a608-240">Five of C#'s categories of types are user-definable: class types, struct types, interface types, enum types, and delegate types.</span></span>

<span data-ttu-id="7a608-241">Un type de classe définit une structure de données qui contient les données membres (champs) et des fonctions membres (méthodes, propriétés, etc.).</span><span class="sxs-lookup"><span data-stu-id="7a608-241">A class type defines a data structure that contains data members (fields) and function members (methods, properties, and others).</span></span> <span data-ttu-id="7a608-242">Les types de classes prennent en charge l’héritage unique et le polymorphisme, des mécanismes par lesquels les classes dérivées peuvent étendre et spécialiser les classes de base.</span><span class="sxs-lookup"><span data-stu-id="7a608-242">Class types support single inheritance and polymorphism, mechanisms whereby derived classes can extend and specialize base classes.</span></span>

<span data-ttu-id="7a608-243">Un type struct est similaire à un type de classe, car il représente une structure avec des membres de données et des fonctions membres.</span><span class="sxs-lookup"><span data-stu-id="7a608-243">A struct type is similar to a class type in that it represents a structure with data members and function members.</span></span> <span data-ttu-id="7a608-244">Toutefois, contrairement aux classes, structs sont des types valeur et ne nécessitent pas d’allocation de tas.</span><span class="sxs-lookup"><span data-stu-id="7a608-244">However, unlike classes, structs are value types and do not require heap allocation.</span></span> <span data-ttu-id="7a608-245">Les types struct ne prennent pas en charge l’héritage spécifié par l’utilisateur, et tous les types struct héritent implicitement du type `object`.</span><span class="sxs-lookup"><span data-stu-id="7a608-245">Struct types do not support user-specified inheritance, and all struct types implicitly inherit from type `object`.</span></span>

<span data-ttu-id="7a608-246">Un type d’interface définit un contrat comme un jeu nommé de membres de la fonction publique.</span><span class="sxs-lookup"><span data-stu-id="7a608-246">An interface type defines a contract as a named set of public function members.</span></span> <span data-ttu-id="7a608-247">Une classe ou un struct qui implémente une interface doit fournir des implémentations de fonctions membres de l’interface.</span><span class="sxs-lookup"><span data-stu-id="7a608-247">A class or struct that implements an interface must provide implementations of the interface's function members.</span></span> <span data-ttu-id="7a608-248">Une interface peut hériter de plusieurs interfaces de base, et une classe ou un struct peut implémenter plusieurs interfaces.</span><span class="sxs-lookup"><span data-stu-id="7a608-248">An interface may inherit from multiple base interfaces, and a class or struct may implement multiple interfaces.</span></span>

<span data-ttu-id="7a608-249">Un type délégué représente des références aux méthodes avec une liste de paramètres particulier et le type de retour.</span><span class="sxs-lookup"><span data-stu-id="7a608-249">A delegate type represents references to methods with a particular parameter list and return type.</span></span> <span data-ttu-id="7a608-250">Les délégués permettent de traiter les méthodes en tant qu’entités qui peuvent être affectées à des variables et passées comme paramètres.</span><span class="sxs-lookup"><span data-stu-id="7a608-250">Delegates make it possible to treat methods as entities that can be assigned to variables and passed as parameters.</span></span> <span data-ttu-id="7a608-251">Les délégués sont similaires au concept de pointeurs de fonction dans d’autres langages, mais contrairement aux pointeurs de fonction, les délégués sont orientés objet et de type sécurisé.</span><span class="sxs-lookup"><span data-stu-id="7a608-251">Delegates are similar to the concept of function pointers found in some other languages, but unlike function pointers, delegates are object-oriented and type-safe.</span></span>

<span data-ttu-id="7a608-252">Classe, struct, interface et délégué types tous en charge les génériques, ce qui leur peuvent être paramétrés avec d’autres types.</span><span class="sxs-lookup"><span data-stu-id="7a608-252">Class, struct, interface and delegate types all support generics, whereby they can be parameterized with other types.</span></span>

<span data-ttu-id="7a608-253">Un type enum est un type distinct avec des constantes nommées.</span><span class="sxs-lookup"><span data-stu-id="7a608-253">An enum type is a distinct type with named constants.</span></span> <span data-ttu-id="7a608-254">Chaque type énumération a un type sous-jacent, ce qui doit être un des huit types intégraux.</span><span class="sxs-lookup"><span data-stu-id="7a608-254">Every enum type has an underlying type, which must be one of the eight integral types.</span></span> <span data-ttu-id="7a608-255">L’ensemble de valeurs d’un type enum est identique à l’ensemble de valeurs du type sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="7a608-255">The set of values of an enum type is the same as the set of values of the underlying type.</span></span>

<span data-ttu-id="7a608-256">C# prend en charge les tableaux uni et multidimensionnels de tout type.</span><span class="sxs-lookup"><span data-stu-id="7a608-256">C# supports single- and multi-dimensional arrays of any type.</span></span> <span data-ttu-id="7a608-257">Contrairement aux types mentionnés ci-dessus, les types de tableaux n’ont pas à être déclarés avant de pouvoir être utilisés.</span><span class="sxs-lookup"><span data-stu-id="7a608-257">Unlike the types listed above, array types do not have to be declared before they can be used.</span></span> <span data-ttu-id="7a608-258">Au lieu de cela, les types de tableaux sont construits en ajoutant des crochets à un nom de type.</span><span class="sxs-lookup"><span data-stu-id="7a608-258">Instead, array types are constructed by following a type name with square brackets.</span></span> <span data-ttu-id="7a608-259">Par exemple, `int[]` est un tableau unidimensionnel de `int`, `int[,]` est un tableau bidimensionnel de `int`, et `int[][]` est un tableau unidimensionnel de tableaux unidimensionnels de `int`.</span><span class="sxs-lookup"><span data-stu-id="7a608-259">For example, `int[]` is a single-dimensional array of `int`, `int[,]` is a two-dimensional array of `int`, and `int[][]` is a single-dimensional array of single-dimensional arrays of `int`.</span></span>

<span data-ttu-id="7a608-260">Également, les types nullables n’ont pas à être déclarés avant de pouvoir être utilisés.</span><span class="sxs-lookup"><span data-stu-id="7a608-260">Nullable types also do not have to be declared before they can be used.</span></span> <span data-ttu-id="7a608-261">Pour chaque type de valeur non nullable `T` est un type nullable correspondant `T?`, qui peut contenir une valeur supplémentaire `null`.</span><span class="sxs-lookup"><span data-stu-id="7a608-261">For each non-nullable value type `T` there is a corresponding nullable type `T?`, which can hold an additional value `null`.</span></span> <span data-ttu-id="7a608-262">Par exemple, `int?` est un type qui peut contenir n’importe quel entier 32 bits ou la valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="7a608-262">For instance, `int?` is a type that can hold any 32 bit integer or the value `null`.</span></span>

<span data-ttu-id="7a608-263">C#du système de type est unifié afin qu’une valeur de n’importe quel type peut être traitée en tant qu’objet.</span><span class="sxs-lookup"><span data-stu-id="7a608-263">C#'s type system is unified such that a value of any type can be treated as an object.</span></span> <span data-ttu-id="7a608-264">Chaque type dans C# dérive directement ou indirectement du type `object`, et `object` est la classe de base fondamentale de tous les types.</span><span class="sxs-lookup"><span data-stu-id="7a608-264">Every type in C# directly or indirectly derives from the `object` class type, and `object` is the ultimate base class of all types.</span></span> <span data-ttu-id="7a608-265">Les valeurs des types référence sont considérées comme des objets simplement en affichant les valeurs en tant que type `object`.</span><span class="sxs-lookup"><span data-stu-id="7a608-265">Values of reference types are treated as objects simply by viewing the values as type `object`.</span></span> <span data-ttu-id="7a608-266">Valeurs des types valeur sont traitées en tant qu’objets en effectuant ***boxing*** et ***unboxing*** operations.</span><span class="sxs-lookup"><span data-stu-id="7a608-266">Values of value types are treated as objects by performing ***boxing*** and ***unboxing*** operations.</span></span> <span data-ttu-id="7a608-267">Dans l’exemple suivant, une valeur `int` est convertie en `object` et à nouveau en `int`.</span><span class="sxs-lookup"><span data-stu-id="7a608-267">In the following example, an `int` value is converted to `object` and back again to `int`.</span></span>

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
<span data-ttu-id="7a608-268">Quand une valeur d’un type valeur est convertie en type `object`, une instance d’objet, également appelée « boîte », est allouée pour contenir la valeur et la valeur est copiée dans cette zone.</span><span class="sxs-lookup"><span data-stu-id="7a608-268">When a value of a value type is converted to type `object`, an object instance, also called a "box," is allocated to hold the value, and the value is copied into that box.</span></span> <span data-ttu-id="7a608-269">À l’inverse, lorsque un `object` référence est castée en un type valeur, une vérification est effectuée que l’objet référencé est une boîte du bon type valeur, et, si la vérification réussit, la valeur dans la zone est copiée.</span><span class="sxs-lookup"><span data-stu-id="7a608-269">Conversely, when an `object` reference is cast to a value type, a check is made that the referenced object is a box of the correct value type, and, if the check succeeds, the value in the box is copied out.</span></span>

<span data-ttu-id="7a608-270">C#unifié de type de système signifie que les types valeur peuvent devenir des objets « à la demande ».</span><span class="sxs-lookup"><span data-stu-id="7a608-270">C#'s unified type system effectively means that value types can become objects "on demand."</span></span> <span data-ttu-id="7a608-271">En raison de l’unification, les bibliothèques à usage général qui utilisent le type `object` peuvent être utilisées avec les types référence et les types valeur.</span><span class="sxs-lookup"><span data-stu-id="7a608-271">Because of the unification, general-purpose libraries that use type `object` can be used with both reference types and value types.</span></span>

<span data-ttu-id="7a608-272">Il existe plusieurs types de ***variables*** en C#, y compris les champs, les éléments de tableau, les variables locales et les paramètres.</span><span class="sxs-lookup"><span data-stu-id="7a608-272">There are several kinds of ***variables*** in C#, including fields, array elements, local variables, and parameters.</span></span> <span data-ttu-id="7a608-273">Les variables représentent des emplacements de stockage, et chaque variable possède un type qui détermine les valeurs pouvant être stockées dans la variable, comme indiqué dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="7a608-273">Variables represent storage locations, and every variable has a type that determines what values can be stored in the variable, as shown by the following table.</span></span>


| <span data-ttu-id="7a608-274">__Type de Variable__</span><span class="sxs-lookup"><span data-stu-id="7a608-274">__Type of Variable__</span></span>    | <span data-ttu-id="7a608-275">__Contenu possible__</span><span class="sxs-lookup"><span data-stu-id="7a608-275">__Possible Contents__</span></span> |
|-------------------------|-----------------------|
| <span data-ttu-id="7a608-276">Type de valeur n’acceptant pas Null</span><span class="sxs-lookup"><span data-stu-id="7a608-276">Non-nullable value type</span></span> | <span data-ttu-id="7a608-277">Une valeur de ce type exact</span><span class="sxs-lookup"><span data-stu-id="7a608-277">A value of that exact type</span></span> |
| <span data-ttu-id="7a608-278">Types valeur Nullable</span><span class="sxs-lookup"><span data-stu-id="7a608-278">Nullable value type</span></span>     | <span data-ttu-id="7a608-279">Une valeur null ou une valeur de ce type exact</span><span class="sxs-lookup"><span data-stu-id="7a608-279">A null value or a value of that exact type</span></span> |
| `object`                | <span data-ttu-id="7a608-280">Une référence null, une référence à un objet de n’importe quel type de référence ou une référence à une valeur boxed de n’importe quel type de valeur</span><span class="sxs-lookup"><span data-stu-id="7a608-280">A null reference, a reference to an object of any reference type, or a reference to a boxed value of any value type</span></span> |
| <span data-ttu-id="7a608-281">Type de classe</span><span class="sxs-lookup"><span data-stu-id="7a608-281">Class type</span></span>              | <span data-ttu-id="7a608-282">Une référence null, une référence à une instance de ce type de classe ou une référence à une instance d’une classe dérivée de ce type de classe</span><span class="sxs-lookup"><span data-stu-id="7a608-282">A null reference, a reference to an instance of that class type, or a reference to an instance of a class derived from that class type</span></span> |
| <span data-ttu-id="7a608-283">Type d'interface</span><span class="sxs-lookup"><span data-stu-id="7a608-283">Interface type</span></span>          | <span data-ttu-id="7a608-284">Une référence null, une référence à une instance d’un type de classe qui implémente ce type d’interface ou une référence à une valeur boxed d’un type valeur qui implémente ce type d’interface</span><span class="sxs-lookup"><span data-stu-id="7a608-284">A null reference, a reference to an instance of a class type that implements that interface type, or a reference to a boxed value of a value type that implements that interface type</span></span> |
| <span data-ttu-id="7a608-285">Type tableau</span><span class="sxs-lookup"><span data-stu-id="7a608-285">Array type</span></span>              | <span data-ttu-id="7a608-286">Une référence null, une référence à une instance de ce type de tableau ou une référence à une instance d’un type tableau compatible</span><span class="sxs-lookup"><span data-stu-id="7a608-286">A null reference, a reference to an instance of that array type, or a reference to an instance of a compatible array type</span></span> |
| <span data-ttu-id="7a608-287">Type délégué</span><span class="sxs-lookup"><span data-stu-id="7a608-287">Delegate type</span></span>           | <span data-ttu-id="7a608-288">Une référence null ou une référence à une instance de ce type de délégué</span><span class="sxs-lookup"><span data-stu-id="7a608-288">A null reference or a reference to an instance of that delegate type</span></span> |

## <a name="expressions"></a><span data-ttu-id="7a608-289">Expressions</span><span class="sxs-lookup"><span data-stu-id="7a608-289">Expressions</span></span>

<span data-ttu-id="7a608-290">Les ***expressions*** sont construites à partir de ***d’opérandes*** et ***d’opérateurs***.</span><span class="sxs-lookup"><span data-stu-id="7a608-290">***Expressions*** are constructed from ***operands*** and ***operators***.</span></span> <span data-ttu-id="7a608-291">Les opérateurs d’une expression indiquent les opérations à appliquer aux opérandes.</span><span class="sxs-lookup"><span data-stu-id="7a608-291">The operators of an expression indicate which operations to apply to the operands.</span></span> <span data-ttu-id="7a608-292">Parmi les exemples d’opérateurs figurent `+`, `-`, `*`, `/` et `new`.</span><span class="sxs-lookup"><span data-stu-id="7a608-292">Examples of operators include `+`, `-`, `*`, `/`, and `new`.</span></span> <span data-ttu-id="7a608-293">Les littéraux, les champs, les variables locales et les expressions sont des exemples d’opérandes.</span><span class="sxs-lookup"><span data-stu-id="7a608-293">Examples of operands include literals, fields, local variables, and expressions.</span></span>

<span data-ttu-id="7a608-294">Quand une expression contient plusieurs opérateurs, la ***priorité*** des opérateurs contrôle l'ordre dans lequel les opérateurs individuels sont évalués.</span><span class="sxs-lookup"><span data-stu-id="7a608-294">When an expression contains multiple operators, the ***precedence*** of the operators controls the order in which the individual operators are evaluated.</span></span> <span data-ttu-id="7a608-295">Par exemple, l’expression `x + y * z` est évaluée comme `x + (y * z)`, car l’opérateur `*` a une priorité plus élevée que `+`.</span><span class="sxs-lookup"><span data-stu-id="7a608-295">For example, the expression `x + y * z` is evaluated as `x + (y * z)` because the `*` operator has higher precedence than the `+` operator.</span></span>

<span data-ttu-id="7a608-296">La plupart des opérateurs peuvent être ***surchargés***.</span><span class="sxs-lookup"><span data-stu-id="7a608-296">Most operators can be ***overloaded***.</span></span> <span data-ttu-id="7a608-297">La surcharge d’opérateur autorise la spécification d’implémentations d’opérateurs définies par l’utilisateur pour les opérations où l’un des deux opérandes ou les deux sont d’un type classe ou struct défini par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7a608-297">Operator overloading permits user-defined operator implementations to be specified for operations where one or both of the operands are of a user-defined class or struct type.</span></span>

<span data-ttu-id="7a608-298">Le tableau suivant récapitule C#d’opérateurs répertoriant les catégories d’opérateurs dans l’ordre de priorité, du plus élevé au plus bas.</span><span class="sxs-lookup"><span data-stu-id="7a608-298">The following table summarizes C#'s operators, listing the operator categories in order of precedence from highest to lowest.</span></span> <span data-ttu-id="7a608-299">Les opérateurs de la même catégorie ont la même priorité.</span><span class="sxs-lookup"><span data-stu-id="7a608-299">Operators in the same category have equal precedence.</span></span>


| <span data-ttu-id="7a608-300">__Catégorie__</span><span class="sxs-lookup"><span data-stu-id="7a608-300">__Category__</span></span>                     | <span data-ttu-id="7a608-301">__Expression__</span><span class="sxs-lookup"><span data-stu-id="7a608-301">__Expression__</span></span>    | <span data-ttu-id="7a608-302">__Description__</span><span class="sxs-lookup"><span data-stu-id="7a608-302">__Description__</span></span> |
|----------------------------------|-------------------|-----------------|
| <span data-ttu-id="7a608-303">Principale</span><span class="sxs-lookup"><span data-stu-id="7a608-303">Primary</span></span>                          | `x.m`             | <span data-ttu-id="7a608-304">Accès au membre</span><span class="sxs-lookup"><span data-stu-id="7a608-304">Member access</span></span> |
|                                  | `x(...)`          | <span data-ttu-id="7a608-305">Méthode et appel de délégué</span><span class="sxs-lookup"><span data-stu-id="7a608-305">Method and delegate invocation</span></span> |
|                                  | `x[...]`          | <span data-ttu-id="7a608-306">Tableau et accès d'indexeur</span><span class="sxs-lookup"><span data-stu-id="7a608-306">Array and indexer access</span></span> |
|                                  | `x++`             | <span data-ttu-id="7a608-307">Post-incrémentation</span><span class="sxs-lookup"><span data-stu-id="7a608-307">Post-increment</span></span> |
|                                  | `x--`             | <span data-ttu-id="7a608-308">Post-décrémentation</span><span class="sxs-lookup"><span data-stu-id="7a608-308">Post-decrement</span></span> |
|                                  | `new T(...)`      | <span data-ttu-id="7a608-309">Création d'objet et de délégué</span><span class="sxs-lookup"><span data-stu-id="7a608-309">Object and delegate creation</span></span> |
|                                  | `new T(...){...}` | <span data-ttu-id="7a608-310">Création d’objet avec initialiseur</span><span class="sxs-lookup"><span data-stu-id="7a608-310">Object creation with initializer</span></span> |
|                                  | `new {...}`       | <span data-ttu-id="7a608-311">Initialiseur d’objet anonyme</span><span class="sxs-lookup"><span data-stu-id="7a608-311">Anonymous object initializer</span></span> |
|                                  | `new T[...]`      | <span data-ttu-id="7a608-312">Création de tableau</span><span class="sxs-lookup"><span data-stu-id="7a608-312">Array creation</span></span> |
|                                  | `typeof(T)`       | <span data-ttu-id="7a608-313">Obtenir l’objet `System.Type` de `T`</span><span class="sxs-lookup"><span data-stu-id="7a608-313">Obtain `System.Type` object for `T`</span></span> |
|                                  | `checked(x)`      | <span data-ttu-id="7a608-314">Évaluer l'expression dans le contexte vérifié (checked)</span><span class="sxs-lookup"><span data-stu-id="7a608-314">Evaluate expression in checked context</span></span> |
|                                  | `unchecked(x)`    | <span data-ttu-id="7a608-315">Évaluer l'expression dans le contexte non vérifié (unchecked)</span><span class="sxs-lookup"><span data-stu-id="7a608-315">Evaluate expression in unchecked context</span></span> |
|                                  | `default(T)`      | <span data-ttu-id="7a608-316">Obtenir la valeur par défaut du type `T`</span><span class="sxs-lookup"><span data-stu-id="7a608-316">Obtain default value of type `T`</span></span> |
|                                  | `delegate {...}`  | <span data-ttu-id="7a608-317">Fonction anonyme (méthode anonyme)</span><span class="sxs-lookup"><span data-stu-id="7a608-317">Anonymous function (anonymous method)</span></span> |
| <span data-ttu-id="7a608-318">Unaire</span><span class="sxs-lookup"><span data-stu-id="7a608-318">Unary</span></span>                            | `+x`              | <span data-ttu-id="7a608-319">Identité</span><span class="sxs-lookup"><span data-stu-id="7a608-319">Identity</span></span> |
|                                  | `-x`              | <span data-ttu-id="7a608-320">Négation</span><span class="sxs-lookup"><span data-stu-id="7a608-320">Negation</span></span> |
|                                  | `!x`              | <span data-ttu-id="7a608-321">Négation logique</span><span class="sxs-lookup"><span data-stu-id="7a608-321">Logical negation</span></span> |
|                                  | `~x`              | <span data-ttu-id="7a608-322">Négation d'opération de bits</span><span class="sxs-lookup"><span data-stu-id="7a608-322">Bitwise negation</span></span> |
|                                  | `++x`             | <span data-ttu-id="7a608-323">Pré-incrémentation</span><span class="sxs-lookup"><span data-stu-id="7a608-323">Pre-increment</span></span> |
|                                  | `--x`             | <span data-ttu-id="7a608-324">Pré-décrémentation</span><span class="sxs-lookup"><span data-stu-id="7a608-324">Pre-decrement</span></span> |
|                                  | `(T)x`            | <span data-ttu-id="7a608-325">Convertir explicitement `x` en type `T`</span><span class="sxs-lookup"><span data-stu-id="7a608-325">Explicitly convert `x` to type `T`</span></span> |
|                                  | `await x`         | <span data-ttu-id="7a608-326">Attendre de façon asynchrone la fin de `x`</span><span class="sxs-lookup"><span data-stu-id="7a608-326">Asynchronously wait for `x` to complete</span></span> |
| <span data-ttu-id="7a608-327">Multiplication</span><span class="sxs-lookup"><span data-stu-id="7a608-327">Multiplicative</span></span>                   | `x * y`           | <span data-ttu-id="7a608-328">Multiplication</span><span class="sxs-lookup"><span data-stu-id="7a608-328">Multiplication</span></span> |
|                                  | `x / y`           | <span data-ttu-id="7a608-329">Division</span><span class="sxs-lookup"><span data-stu-id="7a608-329">Division</span></span> |
|                                  | `x % y`           | <span data-ttu-id="7a608-330">Reste</span><span class="sxs-lookup"><span data-stu-id="7a608-330">Remainder</span></span> |
| <span data-ttu-id="7a608-331">Addition</span><span class="sxs-lookup"><span data-stu-id="7a608-331">Additive</span></span>                         | `x + y`           | <span data-ttu-id="7a608-332">Addition, concaténation de chaînes, combinaison de délégués</span><span class="sxs-lookup"><span data-stu-id="7a608-332">Addition, string concatenation, delegate combination</span></span> |
|                                  | `x - y`           | <span data-ttu-id="7a608-333">Soustraction, suppression de délégué</span><span class="sxs-lookup"><span data-stu-id="7a608-333">Subtraction, delegate removal</span></span> |
| <span data-ttu-id="7a608-334">Shift</span><span class="sxs-lookup"><span data-stu-id="7a608-334">Shift</span></span>                            | `x << y`          | <span data-ttu-id="7a608-335">Décalage à gauche</span><span class="sxs-lookup"><span data-stu-id="7a608-335">Shift left</span></span> |
|                                  | `x >> y`          | <span data-ttu-id="7a608-336">Décalage à droite</span><span class="sxs-lookup"><span data-stu-id="7a608-336">Shift right</span></span> |
| <span data-ttu-id="7a608-337">Relations et test de type</span><span class="sxs-lookup"><span data-stu-id="7a608-337">Relational and type testing</span></span>      | `x < y`           | <span data-ttu-id="7a608-338">Inférieur à</span><span class="sxs-lookup"><span data-stu-id="7a608-338">Less than</span></span> |
|                                  | `x > y`           | <span data-ttu-id="7a608-339">Supérieur à</span><span class="sxs-lookup"><span data-stu-id="7a608-339">Greater than</span></span> |
|                                  | `x <= y`          | <span data-ttu-id="7a608-340">Inférieur ou égal à</span><span class="sxs-lookup"><span data-stu-id="7a608-340">Less than or equal</span></span> |
|                                  | `x >= y`          | <span data-ttu-id="7a608-341">Supérieur ou égal à</span><span class="sxs-lookup"><span data-stu-id="7a608-341">Greater than or equal</span></span> |
|                                  | `x is T`          | <span data-ttu-id="7a608-342">Retourne `true` si `x` est un `T`, `false` sinon</span><span class="sxs-lookup"><span data-stu-id="7a608-342">Return `true` if `x` is a `T`, `false` otherwise</span></span> |
|                                  | `x as T`          | <span data-ttu-id="7a608-343">Retourne `x` de type `T`, ou `null` si `x` n’est pas un `T`</span><span class="sxs-lookup"><span data-stu-id="7a608-343">Return `x` typed as `T`, or `null` if `x` is not a `T`</span></span> |
| <span data-ttu-id="7a608-344">Égalité</span><span class="sxs-lookup"><span data-stu-id="7a608-344">Equality</span></span>                         | `x == y`          | <span data-ttu-id="7a608-345">Égal</span><span class="sxs-lookup"><span data-stu-id="7a608-345">Equal</span></span>      |
|                                  | `x != y`          | <span data-ttu-id="7a608-346">Différence</span><span class="sxs-lookup"><span data-stu-id="7a608-346">Not equal</span></span> |
| <span data-ttu-id="7a608-347">AND logique</span><span class="sxs-lookup"><span data-stu-id="7a608-347">Logical AND</span></span>                      | `x & y`           | <span data-ttu-id="7a608-348">AND d’entiers au niveau du bit, AND logique booléen</span><span class="sxs-lookup"><span data-stu-id="7a608-348">Integer bitwise AND, boolean logical AND</span></span> |
| <span data-ttu-id="7a608-349">XOR logique</span><span class="sxs-lookup"><span data-stu-id="7a608-349">Logical XOR</span></span>                      | `x ^ y`           | <span data-ttu-id="7a608-350">Opération de bits entière XOR, Boolean logique XOR</span><span class="sxs-lookup"><span data-stu-id="7a608-350">Integer bitwise XOR, boolean logical XOR</span></span> |
| <span data-ttu-id="7a608-351">OR logique</span><span class="sxs-lookup"><span data-stu-id="7a608-351">Logical OR</span></span>                       | <code>x &#124; y</code> | <span data-ttu-id="7a608-352">Opération de bits entière OR, Boolean logique OR</span><span class="sxs-lookup"><span data-stu-id="7a608-352">Integer bitwise OR, boolean logical OR</span></span> |
| <span data-ttu-id="7a608-353">AND conditionnel</span><span class="sxs-lookup"><span data-stu-id="7a608-353">Conditional AND</span></span>                  | `x && y`          | <span data-ttu-id="7a608-354">Prend la valeur `y` uniquement si `x` est `true`</span><span class="sxs-lookup"><span data-stu-id="7a608-354">Evaluates `y` only if `x` is `true`</span></span> |
| <span data-ttu-id="7a608-355">OR conditionnel</span><span class="sxs-lookup"><span data-stu-id="7a608-355">Conditional OR</span></span>                   | <code>x &#124;&#124; y</code> | <span data-ttu-id="7a608-356">Prend la valeur `y` uniquement si `x` est `false`</span><span class="sxs-lookup"><span data-stu-id="7a608-356">Evaluates `y` only if `x` is `false`</span></span> |
| <span data-ttu-id="7a608-357">Fusion de Null</span><span class="sxs-lookup"><span data-stu-id="7a608-357">Null coalescing</span></span>                  | `x ?? y`          | <span data-ttu-id="7a608-358">Prend la valeur `y` si `x` est `null`à `x` sinon</span><span class="sxs-lookup"><span data-stu-id="7a608-358">Evaluates to `y` if `x` is `null`, to `x` otherwise</span></span> |
| <span data-ttu-id="7a608-359">Conditionnel</span><span class="sxs-lookup"><span data-stu-id="7a608-359">Conditional</span></span>                      | `x ? y : z`       | <span data-ttu-id="7a608-360">Prend la valeur `y` si `x` est `true`, `z` si `x` est `false`</span><span class="sxs-lookup"><span data-stu-id="7a608-360">Evaluates `y` if `x` is `true`, `z` if `x` is `false`</span></span> |
| <span data-ttu-id="7a608-361">Attribution ou fonction anonyme</span><span class="sxs-lookup"><span data-stu-id="7a608-361">Assignment or anonymous function</span></span> | `x = y`           | <span data-ttu-id="7a608-362">Attribution</span><span class="sxs-lookup"><span data-stu-id="7a608-362">Assignment</span></span> |
|                                  | `x op= y`         | <span data-ttu-id="7a608-363">Assignation composée ; opérateurs pris en charge sont `*=` `/=` `%=` `+=` `-=` `<<=` `>>=` `&=` `^=` <code>&#124;=</code></span><span class="sxs-lookup"><span data-stu-id="7a608-363">Compound assignment; supported operators are `*=` `/=` `%=` `+=` `-=` `<<=` `>>=` `&=` `^=` <code>&#124;=</code></span></span> |
|                                  | `(T x) => y`      | <span data-ttu-id="7a608-364">Fonction anonyme (expression lambda)</span><span class="sxs-lookup"><span data-stu-id="7a608-364">Anonymous function (lambda expression)</span></span> |

## <a name="statements"></a><span data-ttu-id="7a608-365">Instructions</span><span class="sxs-lookup"><span data-stu-id="7a608-365">Statements</span></span>

<span data-ttu-id="7a608-366">Les actions d’un programme sont exprimées à l’aide ***d’instructions***.</span><span class="sxs-lookup"><span data-stu-id="7a608-366">The actions of a program are expressed using ***statements***.</span></span> <span data-ttu-id="7a608-367">C# prend en charge plusieurs types d’instructions, dont plusieurs sont définies en termes d’instructions incorporées.</span><span class="sxs-lookup"><span data-stu-id="7a608-367">C# supports several different kinds of statements, a number of which are defined in terms of embedded statements.</span></span>

<span data-ttu-id="7a608-368">Un ***bloc*** autorise l’écriture de plusieurs instructions dans les contextes où une seule instruction est autorisée.</span><span class="sxs-lookup"><span data-stu-id="7a608-368">A ***block*** permits multiple statements to be written in contexts where a single statement is allowed.</span></span> <span data-ttu-id="7a608-369">Un bloc se compose d’une liste d’instructions écrites entre les délimiteurs `{` et `}`.</span><span class="sxs-lookup"><span data-stu-id="7a608-369">A block consists of a list of statements written between the delimiters `{` and `}`.</span></span>

<span data-ttu-id="7a608-370">Les ***instructions de déclaration*** sont utilisées pour déclarer des variables locales et des constantes.</span><span class="sxs-lookup"><span data-stu-id="7a608-370">***Declaration statements*** are used to declare local variables and constants.</span></span>

<span data-ttu-id="7a608-371">Les ***instructions d’expression*** sont utilisées pour évaluer des expressions.</span><span class="sxs-lookup"><span data-stu-id="7a608-371">***Expression statements*** are used to evaluate expressions.</span></span> <span data-ttu-id="7a608-372">Expressions qui peuvent être utilisées en tant qu’instructions comprennent les appels de méthode, allocations d’objet avec la `new` opérateur, les affectations avec `=` et opérateurs d’assignation composée, les opérations d’incrémentation et de décrémentation à l’aide de la `++`et `--` opérateurs et expressions await.</span><span class="sxs-lookup"><span data-stu-id="7a608-372">Expressions that can be used as statements include method invocations, object allocations using the `new` operator, assignments using `=` and the compound assignment operators, increment and decrement operations using the `++` and `--` operators and await expressions.</span></span>

<span data-ttu-id="7a608-373">Les ***instructions de sélection*** sont utilisées pour sélectionner un nombre d’instructions possibles pour l’exécution en fonction de la valeur d’une expression.</span><span class="sxs-lookup"><span data-stu-id="7a608-373">***Selection statements*** are used to select one of a number of possible statements for execution based on the value of some expression.</span></span> <span data-ttu-id="7a608-374">Dans ce groupe, ce sont les instructions `if` et `switch`.</span><span class="sxs-lookup"><span data-stu-id="7a608-374">In this group are the `if` and `switch` statements.</span></span>

<span data-ttu-id="7a608-375">***Instructions d’itération*** servent à exécuter à plusieurs reprises une instruction incorporée.</span><span class="sxs-lookup"><span data-stu-id="7a608-375">***Iteration statements*** are used to repeatedly execute an embedded statement.</span></span> <span data-ttu-id="7a608-376">Dans ce groupe, ce sont les instructions `while`, `do`, `for` et `foreach`.</span><span class="sxs-lookup"><span data-stu-id="7a608-376">In this group are the `while`, `do`, `for`, and `foreach` statements.</span></span>

<span data-ttu-id="7a608-377">Les ***instructions de saut*** sont utilisées pour transférer le contrôle.</span><span class="sxs-lookup"><span data-stu-id="7a608-377">***Jump statements*** are used to transfer control.</span></span> <span data-ttu-id="7a608-378">Dans ce groupe, ce sont les instructions `break`, `continue`, `goto`, `throw`, `return` et `yield`.</span><span class="sxs-lookup"><span data-stu-id="7a608-378">In this group are the `break`, `continue`, `goto`, `throw`, `return`, and `yield` statements.</span></span>

<span data-ttu-id="7a608-379">L’instruction `try`...`catch` est utilisée pour intercepter les exceptions qui se produisent pendant l’exécution d’un bloc, et l’instruction `try`...`finally` permet de spécifier que le code de finalisation est toujours exécuté, qu’une exception se soit produite ou non.</span><span class="sxs-lookup"><span data-stu-id="7a608-379">The `try`...`catch` statement is used to catch exceptions that occur during execution of a block, and the `try`...`finally` statement is used to specify finalization code that is always executed, whether an exception occurred or not.</span></span>

<span data-ttu-id="7a608-380">Le `checked` et `unchecked` instructions permettent de contrôler le contexte pour les opérations arithmétiques de type intégral et les conversions de vérification de dépassement de capacité.</span><span class="sxs-lookup"><span data-stu-id="7a608-380">The `checked` and `unchecked` statements are used to control the overflow checking context for integral-type arithmetic operations and conversions.</span></span>

<span data-ttu-id="7a608-381">L’instruction `lock` est utilisée pour obtenir le verrou d’exclusion mutuelle pour un objet donné, exécuter une instruction, puis libérer le verrou.</span><span class="sxs-lookup"><span data-stu-id="7a608-381">The `lock` statement is used to obtain the mutual-exclusion lock for a given object, execute a statement, and then release the lock.</span></span>

<span data-ttu-id="7a608-382">L’instruction `using` est utilisée pour obtenir une ressource, exécuter une instruction puis supprimer cette ressource.</span><span class="sxs-lookup"><span data-stu-id="7a608-382">The `using` statement is used to obtain a resource, execute a statement, and then dispose of that resource.</span></span>

<span data-ttu-id="7a608-383">Voici des exemples de chaque type d’instruction</span><span class="sxs-lookup"><span data-stu-id="7a608-383">Below are examples of each kind of statement</span></span>

<span data-ttu-id="7a608-384">__Déclarations de variables locales__</span><span class="sxs-lookup"><span data-stu-id="7a608-384">__Local variable declarations__</span></span>

```csharp
static void Main() {
   int a;
   int b = 2, c = 3;
   a = 1;
   Console.WriteLine(a + b + c);
}
```


<span data-ttu-id="7a608-385">__Déclaration de constante locale__</span><span class="sxs-lookup"><span data-stu-id="7a608-385">__Local constant declaration__</span></span>

```csharp
static void Main() {
    const float pi = 3.1415927f;
    const int r = 25;
    Console.WriteLine(pi * r * r);
}
```


<span data-ttu-id="7a608-386">__Instruction d’expression__</span><span class="sxs-lookup"><span data-stu-id="7a608-386">__Expression statement__</span></span>

```csharp
static void Main() {
    int i;
    i = 123;                // Expression statement
    Console.WriteLine(i);   // Expression statement
    i++;                    // Expression statement
    Console.WriteLine(i);   // Expression statement
}
```

<span data-ttu-id="7a608-387">__`if` Instruction__</span><span class="sxs-lookup"><span data-stu-id="7a608-387">__`if` statement__</span></span>

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


<span data-ttu-id="7a608-388">__`switch` Instruction__</span><span class="sxs-lookup"><span data-stu-id="7a608-388">__`switch` statement__</span></span>

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

<span data-ttu-id="7a608-389">__`while` Instruction__</span><span class="sxs-lookup"><span data-stu-id="7a608-389">__`while` statement__</span></span>

```csharp
static void Main(string[] args) {
    int i = 0;
    while (i < args.Length) {
        Console.WriteLine(args[i]);
        i++;
    }
}
```


<span data-ttu-id="7a608-390">__`do` Instruction__</span><span class="sxs-lookup"><span data-stu-id="7a608-390">__`do` statement__</span></span>

```csharp
static void Main() {
    string s;
    do {
        s = Console.ReadLine();
        if (s != null) Console.WriteLine(s);
    } while (s != null);
}
```

<span data-ttu-id="7a608-391">__`for` Instruction__</span><span class="sxs-lookup"><span data-stu-id="7a608-391">__`for` statement__</span></span>

```csharp
static void Main(string[] args) {
    for (int i = 0; i < args.Length; i++) {
        Console.WriteLine(args[i]);
    }
}
```

<span data-ttu-id="7a608-392">__`foreach` Instruction__</span><span class="sxs-lookup"><span data-stu-id="7a608-392">__`foreach` statement__</span></span>

```csharp
static void Main(string[] args) {
    foreach (string s in args) {
        Console.WriteLine(s);
    }
}
```

<span data-ttu-id="7a608-393">__`break` Instruction__</span><span class="sxs-lookup"><span data-stu-id="7a608-393">__`break` statement__</span></span>

```csharp
static void Main() {
    while (true) {
        string s = Console.ReadLine();
        if (s == null) break;
        Console.WriteLine(s);
    }
}
```

<span data-ttu-id="7a608-394">__`continue` Instruction__</span><span class="sxs-lookup"><span data-stu-id="7a608-394">__`continue` statement__</span></span>

```csharp
static void Main(string[] args) {
    for (int i = 0; i < args.Length; i++) {
        if (args[i].StartsWith("/")) continue;
        Console.WriteLine(args[i]);
    }
}
```

<span data-ttu-id="7a608-395">__`goto` Instruction__</span><span class="sxs-lookup"><span data-stu-id="7a608-395">__`goto` statement__</span></span>

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

<span data-ttu-id="7a608-396">__`return` Instruction__</span><span class="sxs-lookup"><span data-stu-id="7a608-396">__`return` statement__</span></span>

```csharp
static int Add(int a, int b) {
    return a + b;
}

static void Main() {
    Console.WriteLine(Add(1, 2));
    return;
}
```

<span data-ttu-id="7a608-397">__`yield` Instruction__</span><span class="sxs-lookup"><span data-stu-id="7a608-397">__`yield` statement__</span></span>

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

<span data-ttu-id="7a608-398">__`throw` et `try` instructions__</span><span class="sxs-lookup"><span data-stu-id="7a608-398">__`throw` and `try` statements__</span></span>

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

<span data-ttu-id="7a608-399">__`checked` et `unchecked` instructions__</span><span class="sxs-lookup"><span data-stu-id="7a608-399">__`checked` and `unchecked` statements__</span></span>

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

<span data-ttu-id="7a608-400">__`lock` Instruction__</span><span class="sxs-lookup"><span data-stu-id="7a608-400">__`lock` statement__</span></span>

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

<span data-ttu-id="7a608-401">__`using` Instruction__</span><span class="sxs-lookup"><span data-stu-id="7a608-401">__`using` statement__</span></span>

```csharp
static void Main() {
    using (TextWriter w = File.CreateText("test.txt")) {
        w.WriteLine("Line one");
        w.WriteLine("Line two");
        w.WriteLine("Line three");
    }
}
```

## <a name="classes-and-objects"></a><span data-ttu-id="7a608-402">Classes et objets</span><span class="sxs-lookup"><span data-stu-id="7a608-402">Classes and objects</span></span>

<span data-ttu-id="7a608-403">Les ***classes*** représentent le type le plus fondamental de C#.</span><span class="sxs-lookup"><span data-stu-id="7a608-403">***Classes*** are the most fundamental of C#'s types.</span></span> <span data-ttu-id="7a608-404">Une classe est une structure de données qui combine l’état (champs) et les actions (méthodes et autres fonctions membres) en une seule unité.</span><span class="sxs-lookup"><span data-stu-id="7a608-404">A class is a data structure that combines state (fields) and actions (methods and other function members) in a single unit.</span></span> <span data-ttu-id="7a608-405">Une classe fournit une définition pour les ***instances*** créées dynamiquement de la classe, également appelées ***objets***.</span><span class="sxs-lookup"><span data-stu-id="7a608-405">A class provides a definition for dynamically created ***instances*** of the class, also known as ***objects***.</span></span> <span data-ttu-id="7a608-406">Les classes prennent en charge ***l’héritage*** et le ***polymorphisme***, des mécanismes par lesquels les ***classes dérivées*** peuvent étendre et spécialiser les ***classes de base***.</span><span class="sxs-lookup"><span data-stu-id="7a608-406">Classes support ***inheritance*** and ***polymorphism***, mechanisms whereby ***derived classes*** can extend and specialize ***base classes***.</span></span>

<span data-ttu-id="7a608-407">Les nouvelles classes sont créées à l’aide des déclarations de classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-407">New classes are created using class declarations.</span></span> <span data-ttu-id="7a608-408">Une déclaration de classe commence par un en-tête qui spécifie les attributs et modificateurs de la classe, le nom de la classe, la classe de base (si fournie) et les interfaces implémentées par la classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-408">A class declaration starts with a header that specifies the attributes and modifiers of the class, the name of the class, the base class (if given), and the interfaces implemented by the class.</span></span> <span data-ttu-id="7a608-409">L’en-tête est suivi par le corps de la classe, qui se compose d’une liste de déclarations de membres écrites entre les délimiteurs `{` et `}`.</span><span class="sxs-lookup"><span data-stu-id="7a608-409">The header is followed by the class body, which consists of a list of member declarations written between the delimiters `{` and `}`.</span></span>

<span data-ttu-id="7a608-410">Voici une déclaration d’une classe simple nommée `Point` :</span><span class="sxs-lookup"><span data-stu-id="7a608-410">The following is a declaration of a simple class named `Point`:</span></span>

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
<span data-ttu-id="7a608-411">Les instances de classes sont créées à l’aide de l’opérateur `new`, qui alloue la mémoire pour une nouvelle instance, appelle un constructeur pour initialiser l’instance et retourne une référence à l’instance.</span><span class="sxs-lookup"><span data-stu-id="7a608-411">Instances of classes are created using the `new` operator, which allocates memory for a new instance, invokes a constructor to initialize the instance, and returns a reference to the instance.</span></span> <span data-ttu-id="7a608-412">Les instructions suivantes créent deux `Point` objets et de stocker les références à ces objets dans les deux variables :</span><span class="sxs-lookup"><span data-stu-id="7a608-412">The following statements create two `Point` objects and store references to those objects in two variables:</span></span>

```
Point p1 = new Point(0, 0);
Point p2 = new Point(10, 20);
```
<span data-ttu-id="7a608-413">La mémoire occupée par un objet est automatiquement libérée lorsque l’objet n’est plus en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="7a608-413">The memory occupied by an object is automatically reclaimed when the object is no longer in use.</span></span> <span data-ttu-id="7a608-414">Il n’est ni possible ni nécessaire de libérer explicitement des objets dans C#.</span><span class="sxs-lookup"><span data-stu-id="7a608-414">It is neither necessary nor possible to explicitly deallocate objects in C#.</span></span>

### <a name="members"></a><span data-ttu-id="7a608-415">Membres</span><span class="sxs-lookup"><span data-stu-id="7a608-415">Members</span></span>

<span data-ttu-id="7a608-416">Les membres d’une classe sont soit ***membres statiques*** ou ***membres d’instance***.</span><span class="sxs-lookup"><span data-stu-id="7a608-416">The members of a class are either ***static members*** or ***instance members***.</span></span> <span data-ttu-id="7a608-417">Les membres statiques appartiennent à des classes, et les membres d’instance appartiennent à des objets (instances de classes).</span><span class="sxs-lookup"><span data-stu-id="7a608-417">Static members belong to classes, and instance members belong to objects (instances of classes).</span></span>

<span data-ttu-id="7a608-418">Le tableau suivant fournit une vue d’ensemble des types de membres de qu'une classe peut contenir.</span><span class="sxs-lookup"><span data-stu-id="7a608-418">The following table provides an overview of the kinds of members a class can contain.</span></span>


| <span data-ttu-id="7a608-419">__Membre__</span><span class="sxs-lookup"><span data-stu-id="7a608-419">__Member__</span></span>   | <span data-ttu-id="7a608-420">__Description__</span><span class="sxs-lookup"><span data-stu-id="7a608-420">__Description__</span></span> |
|------------  |-----------------|
| <span data-ttu-id="7a608-421">Constantes</span><span class="sxs-lookup"><span data-stu-id="7a608-421">Constants</span></span>    | <span data-ttu-id="7a608-422">Valeurs constantes associées à la classe</span><span class="sxs-lookup"><span data-stu-id="7a608-422">Constant values associated with the class</span></span> |
| <span data-ttu-id="7a608-423">Champs</span><span class="sxs-lookup"><span data-stu-id="7a608-423">Fields</span></span>       | <span data-ttu-id="7a608-424">Variables de la classe</span><span class="sxs-lookup"><span data-stu-id="7a608-424">Variables of the class</span></span> |
| <span data-ttu-id="7a608-425">Méthodes</span><span class="sxs-lookup"><span data-stu-id="7a608-425">Methods</span></span>      | <span data-ttu-id="7a608-426">Calculs et les actions qui peuvent être effectués par la classe</span><span class="sxs-lookup"><span data-stu-id="7a608-426">Computations and actions that can be performed by the class</span></span> |
| <span data-ttu-id="7a608-427">Propriétés</span><span class="sxs-lookup"><span data-stu-id="7a608-427">Properties</span></span>   | <span data-ttu-id="7a608-428">Actions associées à la lecture et l’écriture des propriétés nommées de la classe</span><span class="sxs-lookup"><span data-stu-id="7a608-428">Actions associated with reading and writing named properties of the class</span></span> |
| <span data-ttu-id="7a608-429">Indexeurs</span><span class="sxs-lookup"><span data-stu-id="7a608-429">Indexers</span></span>     | <span data-ttu-id="7a608-430">Actions liées à l’indexation des instances de la classe comme un tableau</span><span class="sxs-lookup"><span data-stu-id="7a608-430">Actions associated with indexing instances of the class like an array</span></span> |
| <span data-ttu-id="7a608-431">Événements</span><span class="sxs-lookup"><span data-stu-id="7a608-431">Events</span></span>       | <span data-ttu-id="7a608-432">Les notifications qui peuvent être générées par la classe</span><span class="sxs-lookup"><span data-stu-id="7a608-432">Notifications that can be generated by the class</span></span> |
| <span data-ttu-id="7a608-433">Opérateurs</span><span class="sxs-lookup"><span data-stu-id="7a608-433">Operators</span></span>    | <span data-ttu-id="7a608-434">Les opérateurs de conversion et d’expression pris en charge par la classe</span><span class="sxs-lookup"><span data-stu-id="7a608-434">Conversions and expression operators supported by the class</span></span> |
| <span data-ttu-id="7a608-435">Constructeurs</span><span class="sxs-lookup"><span data-stu-id="7a608-435">Constructors</span></span> | <span data-ttu-id="7a608-436">Les actions requises pour initialiser les instances de la classe ou la classe elle-même</span><span class="sxs-lookup"><span data-stu-id="7a608-436">Actions required to initialize instances of the class or the class itself</span></span> |
| <span data-ttu-id="7a608-437">Destructeurs</span><span class="sxs-lookup"><span data-stu-id="7a608-437">Destructors</span></span>  | <span data-ttu-id="7a608-438">Actions à effectuer avant que les instances de la classe soient abandonnées de façon définitive</span><span class="sxs-lookup"><span data-stu-id="7a608-438">Actions to perform before instances of the class are permanently discarded</span></span> |
| <span data-ttu-id="7a608-439">Types</span><span class="sxs-lookup"><span data-stu-id="7a608-439">Types</span></span>        | <span data-ttu-id="7a608-440">Types imbriqués déclarés par la classe</span><span class="sxs-lookup"><span data-stu-id="7a608-440">Nested types declared by the class</span></span> |

### <a name="accessibility"></a><span data-ttu-id="7a608-441">Accessibilité</span><span class="sxs-lookup"><span data-stu-id="7a608-441">Accessibility</span></span>

<span data-ttu-id="7a608-442">Chaque membre d’une classe a une accessibilité associée, qui contrôle les régions du texte du programme qui sont en mesure d’accéder au membre.</span><span class="sxs-lookup"><span data-stu-id="7a608-442">Each member of a class has an associated accessibility, which controls the regions of program text that are able to access the member.</span></span> <span data-ttu-id="7a608-443">Il existe cinq formes possibles d’accessibilité.</span><span class="sxs-lookup"><span data-stu-id="7a608-443">There are five possible forms of accessibility.</span></span> <span data-ttu-id="7a608-444">Ceux-ci sont résumés dans le tableau ci-après.</span><span class="sxs-lookup"><span data-stu-id="7a608-444">These are summarized in the following table.</span></span>


| <span data-ttu-id="7a608-445">__Accessibilité__</span><span class="sxs-lookup"><span data-stu-id="7a608-445">__Accessibility__</span></span>    | <span data-ttu-id="7a608-446">__Signification__</span><span class="sxs-lookup"><span data-stu-id="7a608-446">__Meaning__</span></span> |
|----------------------|-----------------|
| `public`             | <span data-ttu-id="7a608-447">Accès non limité</span><span class="sxs-lookup"><span data-stu-id="7a608-447">Access not limited</span></span> |
| `protected`          | <span data-ttu-id="7a608-448">Accès limité à cette classe ou aux classes dérivées de cette classe</span><span class="sxs-lookup"><span data-stu-id="7a608-448">Access limited to this class or classes derived from this class</span></span> |
| `internal`           | <span data-ttu-id="7a608-449">Accès limité à ce programme</span><span class="sxs-lookup"><span data-stu-id="7a608-449">Access limited to this program</span></span> |
| `protected internal` | <span data-ttu-id="7a608-450">Accès limité à ce programme ou aux classes dérivées de cette classe</span><span class="sxs-lookup"><span data-stu-id="7a608-450">Access limited to this program or classes derived from this class</span></span> |
| `private`            | <span data-ttu-id="7a608-451">Accès limité à cette classe</span><span class="sxs-lookup"><span data-stu-id="7a608-451">Access limited to this class</span></span> |

### <a name="type-parameters"></a><span data-ttu-id="7a608-452">Paramètres de type</span><span class="sxs-lookup"><span data-stu-id="7a608-452">Type parameters</span></span>

<span data-ttu-id="7a608-453">Une définition de classe peut spécifier un jeu de paramètres de type en faisant suivre le nom de classe par une liste de noms de paramètre de type entre crochets.</span><span class="sxs-lookup"><span data-stu-id="7a608-453">A class definition may specify a set of type parameters by following the class name with angle brackets enclosing a list of type parameter names.</span></span> <span data-ttu-id="7a608-454">Les paramètres de type peuvent l’être utilisé dans le corps des déclarations de classe pour définir les membres de la classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-454">The type parameters can the be used in the body of the class declarations to define the members of the class.</span></span> <span data-ttu-id="7a608-455">Dans l’exemple suivant, les paramètres de type de `Pair` sont `TFirst` et `TSecond` :</span><span class="sxs-lookup"><span data-stu-id="7a608-455">In the following example, the type parameters of `Pair` are `TFirst` and `TSecond`:</span></span>

```csharp
public class Pair<TFirst,TSecond>
{
    public TFirst First;
    public TSecond Second;
}
```
<span data-ttu-id="7a608-456">Un type de classe est déclaré pour prendre des paramètres de type est appelé un type de classe générique.</span><span class="sxs-lookup"><span data-stu-id="7a608-456">A class type that is declared to take type parameters is called a generic class type.</span></span> <span data-ttu-id="7a608-457">Les types struct, interface et délégué peuvent également être génériques.</span><span class="sxs-lookup"><span data-stu-id="7a608-457">Struct, interface and delegate types can also be generic.</span></span>

<span data-ttu-id="7a608-458">Lorsque la classe générique est utilisée, des arguments de type doivent être fournis pour chacun des paramètres de type :</span><span class="sxs-lookup"><span data-stu-id="7a608-458">When the generic class is used, type arguments must be provided for each of the type parameters:</span></span>

```csharp
Pair<int,string> pair = new Pair<int,string> { First = 1, Second = "two" };
int i = pair.First;     // TFirst is int
string s = pair.Second; // TSecond is string
```
<span data-ttu-id="7a608-459">Un type générique avec des arguments de type fournis, comme `Pair<int,string>
    ` ci-dessus, est appelé type construit.</span><span class="sxs-lookup"><span data-stu-id="7a608-459">A generic type with type arguments provided, like `Pair<int,string>
    ` above, is called a constructed type.</span></span>

### <a name="base-classes"></a><span data-ttu-id="7a608-460">Classes de base</span><span class="sxs-lookup"><span data-stu-id="7a608-460">Base classes</span></span>

<span data-ttu-id="7a608-461">Une déclaration de classe peut spécifier une classe de base en faisant suivre les paramètres de nom et de type de classe par un signe deux-points et le nom de la classe de base.</span><span class="sxs-lookup"><span data-stu-id="7a608-461">A class declaration may specify a base class by following the class name and type parameters with a colon and the name of the base class.</span></span> <span data-ttu-id="7a608-462">L’omission d’une spécification de classe de base revient à dériver du type `object`.</span><span class="sxs-lookup"><span data-stu-id="7a608-462">Omitting a base class specification is the same as deriving from type `object`.</span></span> <span data-ttu-id="7a608-463">Dans l'exemple suivant, la classe de base de `Point3D` est `Point`, et la classe de base de `Point` est `object` :</span><span class="sxs-lookup"><span data-stu-id="7a608-463">In the following example, the base class of `Point3D` is `Point`, and the base class of `Point` is `object`:</span></span>

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
<span data-ttu-id="7a608-464">Une classe hérite des membres de sa classe de base.</span><span class="sxs-lookup"><span data-stu-id="7a608-464">A class inherits the members of its base class.</span></span> <span data-ttu-id="7a608-465">L’héritage signifie qu’une classe contient implicitement tous les membres de sa classe de base, à l’exception de l’instance et les constructeurs statiques et les destructeurs de classe de base.</span><span class="sxs-lookup"><span data-stu-id="7a608-465">Inheritance means that a class implicitly contains all members of its base class, except for the instance and static constructors, and the destructors of the base class.</span></span> <span data-ttu-id="7a608-466">Une classe dérivée peut ajouter des membres hérités, mais ne peut pas supprimer la définition d’un membre hérité.</span><span class="sxs-lookup"><span data-stu-id="7a608-466">A derived class can add new members to those it inherits, but it cannot remove the definition of an inherited member.</span></span> <span data-ttu-id="7a608-467">Dans l’exemple précédent, `Point3D` hérite des champs `x` et `y` de `Point`, et chaque instance de `Point3D` contient trois champs, `x`, `y` et `z`.</span><span class="sxs-lookup"><span data-stu-id="7a608-467">In the previous example, `Point3D` inherits the `x` and `y` fields from `Point`, and every `Point3D` instance contains three fields, `x`, `y`, and `z`.</span></span>

<span data-ttu-id="7a608-468">Il existe une conversion implicite d’un type de classe vers un de ses types de classe de base.</span><span class="sxs-lookup"><span data-stu-id="7a608-468">An implicit conversion exists from a class type to any of its base class types.</span></span> <span data-ttu-id="7a608-469">Par conséquent, une variable d’un type de classe peut référencer une instance de cette classe ou une instance d’une classe dérivée.</span><span class="sxs-lookup"><span data-stu-id="7a608-469">Therefore, a variable of a class type can reference an instance of that class or an instance of any derived class.</span></span> <span data-ttu-id="7a608-470">Par exemple, pour les déclarations de classe précédentes, une variable de type `Point` peut faire référence à un objet `Point` ou `Point3D` :</span><span class="sxs-lookup"><span data-stu-id="7a608-470">For example, given the previous class declarations, a variable of type `Point` can reference either a `Point` or a `Point3D`:</span></span>

```csharp
Point a = new Point(10, 20);
Point b = new Point3D(10, 20, 30);
```

### <a name="fields"></a><span data-ttu-id="7a608-471">Champs</span><span class="sxs-lookup"><span data-stu-id="7a608-471">Fields</span></span>

<span data-ttu-id="7a608-472">Un champ est une variable qui est associée à une classe ou à une instance d’une classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-472">A field is a variable that is associated with a class or with an instance of a class.</span></span>

<span data-ttu-id="7a608-473">Un champ déclaré avec le `static` modificateur définit un ***champ statique***.</span><span class="sxs-lookup"><span data-stu-id="7a608-473">A field declared with the `static` modifier defines a ***static field***.</span></span> <span data-ttu-id="7a608-474">Un champ statique identifie exactement un seul emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="7a608-474">A static field identifies exactly one storage location.</span></span> <span data-ttu-id="7a608-475">Quel que soit le nombre d’instances d’une classe qui sont créées, il n’existe qu’une seule copie d’un champ statique.</span><span class="sxs-lookup"><span data-stu-id="7a608-475">No matter how many instances of a class are created, there is only ever one copy of a static field.</span></span>

<span data-ttu-id="7a608-476">Un champ déclaré sans le `static` modificateur définit un ***champ d’instance***.</span><span class="sxs-lookup"><span data-stu-id="7a608-476">A field declared without the `static` modifier defines an ***instance field***.</span></span> <span data-ttu-id="7a608-477">Chaque instance d’une classe contient une copie distincte de tous les champs d’instance de cette classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-477">Every instance of a class contains a separate copy of all the instance fields of that class.</span></span>

<span data-ttu-id="7a608-478">Dans l’exemple suivant, chaque instance de la classe `Color` possède une copie distincte des champs d’instance `r`, `g` et `b`, mais il existe une seule copie des champs statiques `Black`, `White`, `Red`, `Green` et `Blue` :</span><span class="sxs-lookup"><span data-stu-id="7a608-478">In the following example, each instance of the `Color` class has a separate copy of the `r`, `g`, and `b` instance fields, but there is only one copy of the `Black`, `White`, `Red`, `Green`, and `Blue` static fields:</span></span>

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
<span data-ttu-id="7a608-479">Comme indiqué dans l’exemple précédent, les ***champs en lecture seule*** peuvent être déclarés avec un modificateur `readonly`.</span><span class="sxs-lookup"><span data-stu-id="7a608-479">As shown in the previous example, ***read-only fields*** may be declared with a `readonly` modifier.</span></span> <span data-ttu-id="7a608-480">Affectation à un `readonly` champ peut se produire uniquement dans le cadre de la déclaration du champ ou dans un constructeur de la même classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-480">Assignment to a `readonly` field can only occur as part of the field's declaration or in a constructor in the same class.</span></span>

### <a name="methods"></a><span data-ttu-id="7a608-481">Méthodes</span><span class="sxs-lookup"><span data-stu-id="7a608-481">Methods</span></span>

<span data-ttu-id="7a608-482">Une ***méthode*** est un membre qui implémente un calcul ou une action qui peut être effectuée par un objet ou une classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-482">A ***method*** is a member that implements a computation or action that can be performed by an object or class.</span></span> <span data-ttu-id="7a608-483">Les ***méthodes statiques*** sont accessibles à travers la classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-483">***Static methods*** are accessed through the class.</span></span> <span data-ttu-id="7a608-484">Les ***méthodes d’instance*** sont accessibles via des instances de la classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-484">***Instance methods*** are accessed through instances of the class.</span></span>

<span data-ttu-id="7a608-485">Méthodes ont une liste (éventuellement vide) de ***paramètres***, qui représentent des valeurs ou des références variables passées à la méthode et un ***type de retour***, qui spécifie le type de la valeur calculée et retournée par la méthode.</span><span class="sxs-lookup"><span data-stu-id="7a608-485">Methods have a (possibly empty) list of ***parameters***, which represent values or variable references passed to the method, and a ***return type***, which specifies the type of the value computed and returned by the method.</span></span> <span data-ttu-id="7a608-486">Type de retour d’une méthode est `void` si elle ne retourne pas une valeur.</span><span class="sxs-lookup"><span data-stu-id="7a608-486">A method's return type is `void` if it does not return a value.</span></span>

<span data-ttu-id="7a608-487">Comme les types, les méthodes peuvent également être un jeu de paramètres de type pour lesquels les arguments de type doivent être spécifiés lorsque la méthode est appelée.</span><span class="sxs-lookup"><span data-stu-id="7a608-487">Like types, methods may also have a set of type parameters, for which type arguments must be specified when the method is called.</span></span> <span data-ttu-id="7a608-488">Contrairement aux types, les arguments de type peuvent souvent être déduits à partir des arguments d’un appel de méthode et n’ont pas à être fournis explicitement.</span><span class="sxs-lookup"><span data-stu-id="7a608-488">Unlike types, the type arguments can often be inferred from the arguments of a method call and need not be explicitly given.</span></span>

<span data-ttu-id="7a608-489">La ***signature*** d’une méthode doit être unique dans la classe dans laquelle la méthode est déclarée.</span><span class="sxs-lookup"><span data-stu-id="7a608-489">The ***signature*** of a method must be unique in the class in which the method is declared.</span></span> <span data-ttu-id="7a608-490">La signature d’une méthode se compose du nom de la méthode, du nombre de paramètres de type et du nombre, des modificateurs et des types de ses paramètres.</span><span class="sxs-lookup"><span data-stu-id="7a608-490">The signature of a method consists of the name of the method, the number of type parameters and the number, modifiers, and types of its parameters.</span></span> <span data-ttu-id="7a608-491">La signature d'une méthode n'inclut pas le type de retour.</span><span class="sxs-lookup"><span data-stu-id="7a608-491">The signature of a method does not include the return type.</span></span>

#### <a name="parameters"></a><span data-ttu-id="7a608-492">Paramètres</span><span class="sxs-lookup"><span data-stu-id="7a608-492">Parameters</span></span>

<span data-ttu-id="7a608-493">Les paramètres sont utilisés pour passer des valeurs ou des références variables aux méthodes.</span><span class="sxs-lookup"><span data-stu-id="7a608-493">Parameters are used to pass values or variable references to methods.</span></span> <span data-ttu-id="7a608-494">Les paramètres d’une méthode obtiennent leurs valeurs réelles à partir des ***arguments*** qui sont spécifiés lorsque la méthode est appelée.</span><span class="sxs-lookup"><span data-stu-id="7a608-494">The parameters of a method get their actual values from the ***arguments*** that are specified when the method is invoked.</span></span> <span data-ttu-id="7a608-495">Il existe quatre types de paramètres : les paramètres de valeur, les paramètres de référence, les paramètres de sortie et les tableaux de paramètres.</span><span class="sxs-lookup"><span data-stu-id="7a608-495">There are four kinds of parameters: value parameters, reference parameters, output parameters, and parameter arrays.</span></span>

<span data-ttu-id="7a608-496">Un ***paramètre de valeur*** est utilisé pour le passage de paramètres d’entrée.</span><span class="sxs-lookup"><span data-stu-id="7a608-496">A ***value parameter*** is used for input parameter passing.</span></span> <span data-ttu-id="7a608-497">Un paramètre de valeur correspond à une variable locale qui obtient sa valeur initiale de l’argument qui a été transmis pour le paramètre.</span><span class="sxs-lookup"><span data-stu-id="7a608-497">A value parameter corresponds to a local variable that gets its initial value from the argument that was passed for the parameter.</span></span> <span data-ttu-id="7a608-498">Les modifications apportées à un paramètre de valeur n’affectent pas l’argument qui a été transmis pour le paramètre.</span><span class="sxs-lookup"><span data-stu-id="7a608-498">Modifications to a value parameter do not affect the argument that was passed for the parameter.</span></span>

<span data-ttu-id="7a608-499">Les paramètres de valeur peuvent être facultatifs, en spécifiant une valeur par défaut afin que les arguments correspondants puissent être omis.</span><span class="sxs-lookup"><span data-stu-id="7a608-499">Value parameters can be optional, by specifying a default value so that corresponding arguments can be omitted.</span></span>

<span data-ttu-id="7a608-500">Un ***paramètre de référence*** est utilisé pour la transmission de paramètres en entrée et en sortie.</span><span class="sxs-lookup"><span data-stu-id="7a608-500">A ***reference parameter*** is used for both input and output parameter passing.</span></span> <span data-ttu-id="7a608-501">L’argument passé pour un paramètre de référence doit être une variable, et pendant l’exécution de la méthode, le paramètre de référence représente le même emplacement de stockage que la variable d’argument.</span><span class="sxs-lookup"><span data-stu-id="7a608-501">The argument passed for a reference parameter must be a variable, and during execution of the method, the reference parameter represents the same storage location as the argument variable.</span></span> <span data-ttu-id="7a608-502">Un paramètre de référence est déclaré avec le modificateur `ref`.</span><span class="sxs-lookup"><span data-stu-id="7a608-502">A reference parameter is declared with the `ref` modifier.</span></span> <span data-ttu-id="7a608-503">L'exemple suivant illustre l'utilisation des paramètres `ref`.</span><span class="sxs-lookup"><span data-stu-id="7a608-503">The following example shows the use of `ref` parameters.</span></span>

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
<span data-ttu-id="7a608-504">Un ***paramètre de sortie*** est utilisé pour le passage de paramètres de sortie.</span><span class="sxs-lookup"><span data-stu-id="7a608-504">An ***output parameter*** is used for output parameter passing.</span></span> <span data-ttu-id="7a608-505">Un paramètre de sortie est similaire à un paramètre de référence, sauf que la valeur initiale de l’argument fourni par l’appelant n’a pas d’importance.</span><span class="sxs-lookup"><span data-stu-id="7a608-505">An output parameter is similar to a reference parameter except that the initial value of the caller-provided argument is unimportant.</span></span> <span data-ttu-id="7a608-506">Un paramètre de sortie est déclaré avec le modificateur `out`.</span><span class="sxs-lookup"><span data-stu-id="7a608-506">An output parameter is declared with the `out` modifier.</span></span> <span data-ttu-id="7a608-507">L'exemple suivant illustre l'utilisation des paramètres `out`.</span><span class="sxs-lookup"><span data-stu-id="7a608-507">The following example shows the use of `out` parameters.</span></span>

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
<span data-ttu-id="7a608-508">Un ***tableau de paramètres*** autorise le passage d’un nombre variable d’arguments à une méthode.</span><span class="sxs-lookup"><span data-stu-id="7a608-508">A ***parameter array*** permits a variable number of arguments to be passed to a method.</span></span> <span data-ttu-id="7a608-509">Un tableau de paramètres est déclaré avec le modificateur `params`.</span><span class="sxs-lookup"><span data-stu-id="7a608-509">A parameter array is declared with the `params` modifier.</span></span> <span data-ttu-id="7a608-510">Seul le dernier paramètre d’une méthode peut être un tableau de paramètres, et le type d’un tableau de paramètres doit être un type tableau unidimensionnel.</span><span class="sxs-lookup"><span data-stu-id="7a608-510">Only the last parameter of a method can be a parameter array, and the type of a parameter array must be a single-dimensional array type.</span></span> <span data-ttu-id="7a608-511">Le `Write` et `WriteLine` méthodes de la `System.Console` classe sont de bons exemples d’utilisation des tableaux de paramètre.</span><span class="sxs-lookup"><span data-stu-id="7a608-511">The `Write` and `WriteLine` methods of the `System.Console` class are good examples of parameter array usage.</span></span> <span data-ttu-id="7a608-512">Vous les déclarez de la façon suivante.</span><span class="sxs-lookup"><span data-stu-id="7a608-512">They are declared as follows.</span></span>

```csharp
public class Console
{
    public static void Write(string fmt, params object[] args) {...}
    public static void WriteLine(string fmt, params object[] args) {...}
    ...
}
```
<span data-ttu-id="7a608-513">Dans une méthode qui utilise un tableau de paramètres, le tableau de paramètres se comporte exactement comme un paramètre ordinaire de type tableau.</span><span class="sxs-lookup"><span data-stu-id="7a608-513">Within a method that uses a parameter array, the parameter array behaves exactly like a regular parameter of an array type.</span></span> <span data-ttu-id="7a608-514">Toutefois, dans un appel à une méthode avec un tableau de paramètres, il est possible de passer un argument unique de type tableau de paramètres ou n’importe quel nombre d’arguments du type d’élément du tableau de paramètres.</span><span class="sxs-lookup"><span data-stu-id="7a608-514">However, in an invocation of a method with a parameter array, it is possible to pass either a single argument of the parameter array type or any number of arguments of the element type of the parameter array.</span></span> <span data-ttu-id="7a608-515">Dans ce cas, une instance de tableau est automatiquement créée et initialisée avec les arguments donnés.</span><span class="sxs-lookup"><span data-stu-id="7a608-515">In the latter case, an array instance is automatically created and initialized with the given arguments.</span></span> <span data-ttu-id="7a608-516">Cet exemple</span><span class="sxs-lookup"><span data-stu-id="7a608-516">This example</span></span>

```csharp
Console.WriteLine("x={0} y={1} z={2}", x, y, z);
```
<span data-ttu-id="7a608-517">revient à écrire ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="7a608-517">is equivalent to writing the following.</span></span>

```csharp
string s = "x={0} y={1} z={2}";
object[] args = new object[3];
args[0] = x;
args[1] = y;
args[2] = z;
Console.WriteLine(s, args);
```

#### <a name="method-body-and-local-variables"></a><span data-ttu-id="7a608-518">Corps de la méthode et variables locales</span><span class="sxs-lookup"><span data-stu-id="7a608-518">Method body and local variables</span></span>

<span data-ttu-id="7a608-519">Corps d’une méthode spécifie les instructions à exécuter lorsque la méthode est appelée.</span><span class="sxs-lookup"><span data-stu-id="7a608-519">A method's body specifies the statements to execute when the method is invoked.</span></span>

<span data-ttu-id="7a608-520">Un corps de méthode peut déclarer des variables qui sont spécifiques à l’appel de la méthode.</span><span class="sxs-lookup"><span data-stu-id="7a608-520">A method body can declare variables that are specific to the invocation of the method.</span></span> <span data-ttu-id="7a608-521">Ces variables sont appelées ***variables locales***.</span><span class="sxs-lookup"><span data-stu-id="7a608-521">Such variables are called ***local variables***.</span></span> <span data-ttu-id="7a608-522">Une déclaration de variable locale spécifie un nom de type, un nom de variable et éventuellement une valeur initiale.</span><span class="sxs-lookup"><span data-stu-id="7a608-522">A local variable declaration specifies a type name, a variable name, and possibly an initial value.</span></span> <span data-ttu-id="7a608-523">L’exemple suivant déclare une variable locale `i` avec une valeur initiale de zéro et une variable locale `j` sans valeur initiale.</span><span class="sxs-lookup"><span data-stu-id="7a608-523">The following example declares a local variable `i` with an initial value of zero and a local variable `j` with no initial value.</span></span>

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
<span data-ttu-id="7a608-524">C# requiert qu’une variable locale soit ***assignée de manière définitive*** avant de pouvoir obtenir sa valeur.</span><span class="sxs-lookup"><span data-stu-id="7a608-524">C# requires a local variable to be ***definitely assigned*** before its value can be obtained.</span></span> <span data-ttu-id="7a608-525">Par exemple, si la déclaration du `i` précédent n’inclut pas de valeur initiale, le compilateur signale une erreur pour les utilisations ultérieures de `i`, car `i` ne serait pas assigné de manière définitive à ces points dans le programme.</span><span class="sxs-lookup"><span data-stu-id="7a608-525">For example, if the declaration of the previous `i` did not include an initial value, the compiler would report an error for the subsequent usages of `i` because `i` would not be definitely assigned at those points in the program.</span></span>

<span data-ttu-id="7a608-526">Une méthode peut utiliser les instructions `return` pour retourner le contrôle à son appelant.</span><span class="sxs-lookup"><span data-stu-id="7a608-526">A method can use `return` statements to return control to its caller.</span></span> <span data-ttu-id="7a608-527">Dans une méthode retournant `void`, les instructions `return` ne peuvent pas spécifier une expression.</span><span class="sxs-lookup"><span data-stu-id="7a608-527">In a method returning `void`, `return` statements cannot specify an expression.</span></span> <span data-ttu-id="7a608-528">Dans une méthode retournant non -`void`, `return` les instructions doivent inclure une expression qui calcule la valeur de retour.</span><span class="sxs-lookup"><span data-stu-id="7a608-528">In a method returning non-`void`, `return` statements must include an expression that computes the return value.</span></span>

#### <a name="static-and-instance-methods"></a><span data-ttu-id="7a608-529">Méthodes statiques et d’instance</span><span class="sxs-lookup"><span data-stu-id="7a608-529">Static and instance methods</span></span>

<span data-ttu-id="7a608-530">Une méthode déclarée avec un `static` modificateur est un ***méthode statique***.</span><span class="sxs-lookup"><span data-stu-id="7a608-530">A method declared with a `static` modifier is a ***static method***.</span></span> <span data-ttu-id="7a608-531">Une méthode statique n’opère pas sur une instance spécifique et permet uniquement d’accéder directement à des membres statiques.</span><span class="sxs-lookup"><span data-stu-id="7a608-531">A static method does not operate on a specific instance and can only directly access static members.</span></span>

<span data-ttu-id="7a608-532">Une méthode déclarée sans un `static` modificateur est un ***méthode d’instance***.</span><span class="sxs-lookup"><span data-stu-id="7a608-532">A method declared without a `static` modifier is an ***instance method***.</span></span> <span data-ttu-id="7a608-533">Une méthode d’instance opère sur une instance spécifique et peut accéder aux membres statiques et d’instance.</span><span class="sxs-lookup"><span data-stu-id="7a608-533">An instance method operates on a specific instance and can access both static and instance members.</span></span> <span data-ttu-id="7a608-534">L’instance sur laquelle une méthode d’instance a été appelée est explicitement accessible en tant que `this`.</span><span class="sxs-lookup"><span data-stu-id="7a608-534">The instance on which an instance method was invoked can be explicitly accessed as `this`.</span></span> <span data-ttu-id="7a608-535">Une erreur consiste à faire référence à `this` dans une méthode statique.</span><span class="sxs-lookup"><span data-stu-id="7a608-535">It is an error to refer to `this` in a static method.</span></span>

<span data-ttu-id="7a608-536">La classe `Entity` suivante a à la fois des statiques et des membres d’instance.</span><span class="sxs-lookup"><span data-stu-id="7a608-536">The following `Entity` class has both static and instance members.</span></span>

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
<span data-ttu-id="7a608-537">Chaque instance `Entity` contient un numéro de série (et probablement d’autres informations qui ne sont pas indiquées ici).</span><span class="sxs-lookup"><span data-stu-id="7a608-537">Each `Entity` instance contains a serial number (and presumably some other information that is not shown here).</span></span> <span data-ttu-id="7a608-538">Le constructeur `Entity` (qui est similaire à une méthode d’instance) initialise la nouvelle instance avec le numéro de série suivant.</span><span class="sxs-lookup"><span data-stu-id="7a608-538">The `Entity` constructor (which is like an instance method) initializes the new instance with the next available serial number.</span></span> <span data-ttu-id="7a608-539">Étant donné que le constructeur est un membre d’instance, il est autorisé à accéder à la fois au champ d’instance `serialNo` et au champ statique `nextSerialNo`.</span><span class="sxs-lookup"><span data-stu-id="7a608-539">Because the constructor is an instance member, it is permitted to access both the `serialNo` instance field and the `nextSerialNo` static field.</span></span>

<span data-ttu-id="7a608-540">Les méthodes statiques `GetNextSerialNo` et `SetNextSerialNo` peuvent accéder au champ statique `nextSerialNo`, mais ce serait une erreur pour eux d’accéder directement au champ d’instance `serialNo`.</span><span class="sxs-lookup"><span data-stu-id="7a608-540">The `GetNextSerialNo` and `SetNextSerialNo` static methods can access the `nextSerialNo` static field, but it would be an error for them to directly access the `serialNo` instance field.</span></span>

<span data-ttu-id="7a608-541">L’exemple suivant illustre l’utilisation de la `Entity` classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-541">The following example shows the use of the `Entity` class.</span></span>

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
<span data-ttu-id="7a608-542">Notez que les méthodes statiques `SetNextSerialNo` et `GetNextSerialNo` sont appelées sur la classe alors que la méthode d’instance `GetSerialNo` est appelée sur les instances de la classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-542">Note that the `SetNextSerialNo` and `GetNextSerialNo` static methods are invoked on the class whereas the `GetSerialNo` instance method is invoked on instances of the class.</span></span>

#### <a name="virtual-override-and-abstract-methods"></a><span data-ttu-id="7a608-543">Méthodes virtuelles, de substitution et abstraites</span><span class="sxs-lookup"><span data-stu-id="7a608-543">Virtual, override, and abstract methods</span></span>

<span data-ttu-id="7a608-544">Lorsqu’une déclaration de méthode d’instance inclut un modificateur `virtual`, la méthode est appelée ***méthode virtuelle***.</span><span class="sxs-lookup"><span data-stu-id="7a608-544">When an instance method declaration includes a `virtual` modifier, the method is said to be a ***virtual method***.</span></span> <span data-ttu-id="7a608-545">En cas de non `virtual` modificateur est présent, la méthode est dite un ***méthode non virtuelle***.</span><span class="sxs-lookup"><span data-stu-id="7a608-545">When no `virtual` modifier is present, the method is said to be a ***non-virtual method***.</span></span>

<span data-ttu-id="7a608-546">Lorsqu’une méthode virtuelle est appelée, le ***type au moment de l’exécution*** de l’instance pour laquelle cet appel prend place détermine l’implémentation de méthode à appeler.</span><span class="sxs-lookup"><span data-stu-id="7a608-546">When a virtual method is invoked, the ***run-time type*** of the instance for which that invocation takes place determines the actual method implementation to invoke.</span></span> <span data-ttu-id="7a608-547">Dans un appel de méthode non virtuelle, le ***type lors de la compilation*** de l’instance est le facteur déterminant.</span><span class="sxs-lookup"><span data-stu-id="7a608-547">In a nonvirtual method invocation, the ***compile-time type*** of the instance is the determining factor.</span></span>

<span data-ttu-id="7a608-548">Une méthode virtuelle peut être ***substituée*** dans une classe dérivée.</span><span class="sxs-lookup"><span data-stu-id="7a608-548">A virtual method can be ***overridden*** in a derived class.</span></span> <span data-ttu-id="7a608-549">Lorsqu’une déclaration de méthode d’instance inclut un `override` modificateur, la méthode substitue à une méthode virtuelle héritée ayant la même signature.</span><span class="sxs-lookup"><span data-stu-id="7a608-549">When an instance method declaration includes an `override` modifier, the method overrides an inherited virtual method with the same signature.</span></span> <span data-ttu-id="7a608-550">Là où une déclaration de méthode virtuelle présente une nouvelle méthode, une déclaration de méthode de substitution spécialise une méthode virtuelle héritée existante en fournissant une nouvelle implémentation de cette méthode.</span><span class="sxs-lookup"><span data-stu-id="7a608-550">Whereas a virtual method declaration introduces a new method, an override method declaration specializes an existing inherited virtual method by providing a new implementation of that method.</span></span>

<span data-ttu-id="7a608-551">Un ***abstraite*** méthode est une méthode virtuelle sans implémentation.</span><span class="sxs-lookup"><span data-stu-id="7a608-551">An ***abstract*** method is a virtual method with no implementation.</span></span> <span data-ttu-id="7a608-552">Une méthode abstraite est déclarée avec le `abstract` modificateur et n’est autorisée uniquement dans une classe qui est également déclarée `abstract`.</span><span class="sxs-lookup"><span data-stu-id="7a608-552">An abstract method is declared with the `abstract` modifier and is permitted only in a class that is also declared `abstract`.</span></span> <span data-ttu-id="7a608-553">Une méthode abstraite doit être remplacée dans chaque classe dérivée non abstraite.</span><span class="sxs-lookup"><span data-stu-id="7a608-553">An abstract method must be overridden in every non-abstract derived class.</span></span>

<span data-ttu-id="7a608-554">L’exemple suivant déclare une classe abstraite, `Expression`, qui représente un nœud d’arborescence de l’expression et trois classes dérivées, `Constant`, `VariableReference` et `Operation`, qui implémentent des nœuds d’arborescence de l’expression pour les références variables, les constantes et les opérations arithmétiques.</span><span class="sxs-lookup"><span data-stu-id="7a608-554">The following example declares an abstract class, `Expression`, which represents an expression tree node, and three derived classes, `Constant`, `VariableReference`, and `Operation`, which implement expression tree nodes for constants, variable references, and arithmetic operations.</span></span> <span data-ttu-id="7a608-555">(Cela revient à, mais à ne pas confondre avec les types d’arborescence expression introduit dans [types arborescence d’Expression](types.md#expression-tree-types)).</span><span class="sxs-lookup"><span data-stu-id="7a608-555">(This is similar to, but not to be confused with the expression tree types introduced in [Expression tree types](types.md#expression-tree-types)).</span></span>

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
<span data-ttu-id="7a608-556">Les quatre classes précédentes permet de modéliser des expressions arithmétiques.</span><span class="sxs-lookup"><span data-stu-id="7a608-556">The previous four classes can be used to model arithmetic expressions.</span></span> <span data-ttu-id="7a608-557">Par exemple, en utilisant des instances de ces classes, l’expression `x + 3` peut être représentée comme suit.</span><span class="sxs-lookup"><span data-stu-id="7a608-557">For example, using instances of these classes, the expression `x + 3` can be represented as follows.</span></span>

```csharp
Expression e = new Operation(
    new VariableReference("x"),
    '+',
    new Constant(3));
```
<span data-ttu-id="7a608-558">La méthode `Evaluate` d’une instance `Expression` est appelée pour évaluer l’expression donnée et produire une valeur `double`.</span><span class="sxs-lookup"><span data-stu-id="7a608-558">The `Evaluate` method of an `Expression` instance is invoked to evaluate the given expression and produce a `double` value.</span></span> <span data-ttu-id="7a608-559">La méthode prend comme argument une `Hashtable` qui contient les noms de variables (comme clés des entrées) et des valeurs (comme les valeurs des entrées).</span><span class="sxs-lookup"><span data-stu-id="7a608-559">The method takes as an argument a `Hashtable` that contains variable names (as keys of the entries) and values (as values of the entries).</span></span> <span data-ttu-id="7a608-560">Le `Evaluate` méthode est une méthode virtuelle abstraite, ce qui signifie que les classes non abstraites dérivées doivent remplacer pour fournir une implémentation réelle.</span><span class="sxs-lookup"><span data-stu-id="7a608-560">The `Evaluate` method is a virtual abstract method, meaning that non-abstract derived classes must override it to provide an actual implementation.</span></span>

<span data-ttu-id="7a608-561">Une implémentation de `Constant` de `Evaluate` renvoie simplement la constante stockée.</span><span class="sxs-lookup"><span data-stu-id="7a608-561">A `Constant`'s implementation of `Evaluate` simply returns the stored constant.</span></span> <span data-ttu-id="7a608-562">Un `VariableReference`d’implémentation recherche le nom de variable dans la table de hachage et retourne la valeur résultante.</span><span class="sxs-lookup"><span data-stu-id="7a608-562">A `VariableReference`'s implementation looks up the variable name in the hashtable and returns the resulting value.</span></span> <span data-ttu-id="7a608-563">Une implémentation de `Operation` évalue d’abord les opérandes de gauche et de droite (en appelant de manière récursive leurs méthodes `Evaluate`), puis effectue l’opération arithmétique donnée.</span><span class="sxs-lookup"><span data-stu-id="7a608-563">An `Operation`'s implementation first evaluates the left and right operands (by recursively invoking their `Evaluate` methods) and then performs the given arithmetic operation.</span></span>

<span data-ttu-id="7a608-564">Le programme suivant utilise les classes `Expression` pour évaluer l’expression `x * (y + 2)` pour différentes valeurs de `x` et `y`.</span><span class="sxs-lookup"><span data-stu-id="7a608-564">The following program uses the `Expression` classes to evaluate the expression `x * (y + 2)` for different values of `x` and `y`.</span></span>

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

#### <a name="method-overloading"></a><span data-ttu-id="7a608-565">Surcharge de méthode</span><span class="sxs-lookup"><span data-stu-id="7a608-565">Method overloading</span></span>

<span data-ttu-id="7a608-566">La ***surcharge*** de méthode permet d’avoir plusieurs méthodes dans la même classe avec le même nom, tant qu’elles ont des signatures uniques.</span><span class="sxs-lookup"><span data-stu-id="7a608-566">Method ***overloading*** permits multiple methods in the same class to have the same name as long as they have unique signatures.</span></span> <span data-ttu-id="7a608-567">Lors de la compilation d’un appel à une méthode surchargée, le compilateur utilise ***la résolution de surcharge*** pour déterminer la méthode spécifique à appeler.</span><span class="sxs-lookup"><span data-stu-id="7a608-567">When compiling an invocation of an overloaded method, the compiler uses ***overload resolution*** to determine the specific method to invoke.</span></span> <span data-ttu-id="7a608-568">La résolution de surcharge trouve la méthode qui correspond le mieux aux arguments ou signale une erreur si aucune meilleure correspondance ne peut être trouvée.</span><span class="sxs-lookup"><span data-stu-id="7a608-568">Overload resolution finds the one method that best matches the arguments or reports an error if no single best match can be found.</span></span> <span data-ttu-id="7a608-569">L’exemple suivant montre la résolution de surcharge en action.</span><span class="sxs-lookup"><span data-stu-id="7a608-569">The following example shows overload resolution in effect.</span></span> <span data-ttu-id="7a608-570">Le commentaire pour chaque appel de la méthode `Main` affiche une méthode qui est réellement appelée.</span><span class="sxs-lookup"><span data-stu-id="7a608-570">The comment for each invocation in the `Main` method shows which method is actually invoked.</span></span>

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
<span data-ttu-id="7a608-571">Comme le montre l’exemple, une méthode particulière peut toujours être sélectionnée en effectuant un cast explicite des arguments aux types de paramètres exacts et/ou en fournissant explicitement les arguments de type.</span><span class="sxs-lookup"><span data-stu-id="7a608-571">As shown by the example, a particular method can always be selected by explicitly casting the arguments to the exact parameter types and/or explicitly supplying type arguments.</span></span>

### <a name="other-function-members"></a><span data-ttu-id="7a608-572">Autres fonctions membres</span><span class="sxs-lookup"><span data-stu-id="7a608-572">Other function members</span></span>

<span data-ttu-id="7a608-573">Les membres qui contiennent du code exécutable sont collectivement regroupés sous les ***membres de fonction*** d’une classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-573">Members that contain executable code are collectively known as the ***function members*** of a class.</span></span> <span data-ttu-id="7a608-574">La section précédente décrit les méthodes qui sont du type principal des fonctions membres.</span><span class="sxs-lookup"><span data-stu-id="7a608-574">The preceding section describes methods, which are the primary kind of function members.</span></span> <span data-ttu-id="7a608-575">Cette section décrit les autres types de fonctions membres pris en charge par c# : constructeurs, propriétés, indexeurs, événements, opérateurs et les destructeurs.</span><span class="sxs-lookup"><span data-stu-id="7a608-575">This section describes the other kinds of function members supported by C#: constructors, properties, indexers, events, operators, and destructors.</span></span>

<span data-ttu-id="7a608-576">Le code suivant montre une classe générique appelée `List<T>`, qui implémente une liste d’objets évolutive.</span><span class="sxs-lookup"><span data-stu-id="7a608-576">The following code shows a generic class called `List<T>`, which implements a growable list of objects.</span></span> <span data-ttu-id="7a608-577">La classe contient plusieurs exemples des types les plus courants de membres de fonction.</span><span class="sxs-lookup"><span data-stu-id="7a608-577">The class contains several examples of the most common kinds of function members.</span></span>


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

#### <a name="constructors"></a><span data-ttu-id="7a608-578">Constructeurs</span><span class="sxs-lookup"><span data-stu-id="7a608-578">Constructors</span></span>

<span data-ttu-id="7a608-579">C# prend en charge les constructeurs statiques et d’instance.</span><span class="sxs-lookup"><span data-stu-id="7a608-579">C# supports both instance and static constructors.</span></span> <span data-ttu-id="7a608-580">Un ***constructeur d’instance*** est un membre qui implémente les actions requises pour initialiser une instance d’une classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-580">An ***instance constructor*** is a member that implements the actions required to initialize an instance of a class.</span></span> <span data-ttu-id="7a608-581">Un ***constructeur statique*** est un membre qui implémente les actions requises pour initialiser une classe lui-même lorsqu’il est chargé.</span><span class="sxs-lookup"><span data-stu-id="7a608-581">A ***static constructor*** is a member that implements the actions required to initialize a class itself when it is first loaded.</span></span>

<span data-ttu-id="7a608-582">Un constructeur est déclaré comme une méthode sans aucun type de retour et le même nom que la classe contenante.</span><span class="sxs-lookup"><span data-stu-id="7a608-582">A constructor is declared like a method with no return type and the same name as the containing class.</span></span> <span data-ttu-id="7a608-583">Si une déclaration de constructeur comprend un `static` modificateur, elle déclare un constructeur statique.</span><span class="sxs-lookup"><span data-stu-id="7a608-583">If a constructor declaration includes a `static` modifier, it declares a static constructor.</span></span> <span data-ttu-id="7a608-584">Dans le cas contraire, elle déclare un constructeur d’instance.</span><span class="sxs-lookup"><span data-stu-id="7a608-584">Otherwise, it declares an instance constructor.</span></span>

<span data-ttu-id="7a608-585">Constructeurs d’instance peuvent être surchargés.</span><span class="sxs-lookup"><span data-stu-id="7a608-585">Instance constructors can be overloaded.</span></span> <span data-ttu-id="7a608-586">Par exemple, la classe `List<T>
` déclare deux constructeurs d’instance, un sans paramètres et une fonction qui accepte un paramètre `int`.</span><span class="sxs-lookup"><span data-stu-id="7a608-586">For example, the `List<T>
` class declares two instance constructors, one with no parameters and one that takes an `int` parameter.</span></span> <span data-ttu-id="7a608-587">Les constructeurs d’instance sont appelés en utilisant l’opérateur `new`.</span><span class="sxs-lookup"><span data-stu-id="7a608-587">Instance constructors are invoked using the `new` operator.</span></span> <span data-ttu-id="7a608-588">Les instructions suivantes allouent deux `List<string>
` instances à l’aide de chacun des constructeurs de la `List` classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-588">The following statements allocate two `List<string>
` instances using each of the constructors of the `List` class.</span></span>

```csharp
List<string> list1 = new List<string>();
List<string> list2 = new List<string>(10);
```
<span data-ttu-id="7a608-589">Contrairement aux autres membres, les constructeurs d’instance ne sont pas hérités, et une classe n’a aucun constructeur d’instance autre que ceux réellement déclarés dans la classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-589">Unlike other members, instance constructors are not inherited, and a class has no instance constructors other than those actually declared in the class.</span></span> <span data-ttu-id="7a608-590">Si aucun constructeur d’instance n’est fourni pour une classe, un constructeur vide sans paramètre est fourni automatiquement.</span><span class="sxs-lookup"><span data-stu-id="7a608-590">If no instance constructor is supplied for a class, then an empty one with no parameters is automatically provided.</span></span>

#### <a name="properties"></a><span data-ttu-id="7a608-591">Propriétés</span><span class="sxs-lookup"><span data-stu-id="7a608-591">Properties</span></span>

<span data-ttu-id="7a608-592">Les ***propriétés*** sont une extension naturelle des champs.</span><span class="sxs-lookup"><span data-stu-id="7a608-592">***Properties*** are a natural extension of fields.</span></span> <span data-ttu-id="7a608-593">Les deux sont des membres nommés avec des types associés, et la syntaxe pour accéder aux champs et propriétés est la même.</span><span class="sxs-lookup"><span data-stu-id="7a608-593">Both are named members with associated types, and the syntax for accessing fields and properties is the same.</span></span> <span data-ttu-id="7a608-594">Toutefois, contrairement aux champs, les propriétés ne désignent pas des emplacements de stockage.</span><span class="sxs-lookup"><span data-stu-id="7a608-594">However, unlike fields, properties do not denote storage locations.</span></span> <span data-ttu-id="7a608-595">Au lieu de cela, les propriétés ont des ***accesseurs*** qui spécifient les instructions à exécuter lorsque les valeurs sont lues ou écrites.</span><span class="sxs-lookup"><span data-stu-id="7a608-595">Instead, properties have ***accessors*** that specify the statements to be executed when their values are read or written.</span></span>

<span data-ttu-id="7a608-596">Une propriété est déclarée comme un champ, à ceci près que la déclaration se termine par un `get` accesseur et/ou un `set` accesseur écrite entre les délimiteurs `{` et `}` au lieu de se terminant par un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="7a608-596">A property is declared like a field, except that the declaration ends with a `get` accessor and/or a `set` accessor written between the delimiters `{` and `}` instead of ending in a semicolon.</span></span> <span data-ttu-id="7a608-597">Une propriété qui possède à la fois un `get` accesseur et un `set` accesseur est une ***propriété en lecture-écriture***, une propriété qui possède uniquement un `get` accesseur est une ***propriété en lecture seule***et un propriété qui a uniquement un `set` accesseur est une ***propriété en écriture seule***.</span><span class="sxs-lookup"><span data-stu-id="7a608-597">A property that has both a `get` accessor and a `set` accessor is a ***read-write property***, a property that has only a `get` accessor is a ***read-only property***, and a property that has only a `set` accessor is a ***write-only property***.</span></span>

<span data-ttu-id="7a608-598">Un `get` accesseur correspond à une méthode sans paramètre avec une valeur de retour du type de propriété.</span><span class="sxs-lookup"><span data-stu-id="7a608-598">A `get` accessor corresponds to a parameterless method with a return value of the property type.</span></span> <span data-ttu-id="7a608-599">À l’exception comme cible d’une assignation, lorsqu’une propriété est référencée dans une expression, le `get` accesseur de la propriété est appelé pour calculer la valeur de la propriété.</span><span class="sxs-lookup"><span data-stu-id="7a608-599">Except as the target of an assignment, when a property is referenced in an expression, the `get` accessor of the property is invoked to compute the value of the property.</span></span>

<span data-ttu-id="7a608-600">Un `set` accesseur correspond à une méthode avec un paramètre unique nommé `value` et aucun type de retour.</span><span class="sxs-lookup"><span data-stu-id="7a608-600">A `set` accessor corresponds to a method with a single parameter named `value` and no return type.</span></span> <span data-ttu-id="7a608-601">Lorsqu’une propriété est référencée en tant que la cible d’une assignation ou l’opérande de `++` ou `--`, le `set` accesseur est appelé avec un argument qui fournit la nouvelle valeur.</span><span class="sxs-lookup"><span data-stu-id="7a608-601">When a property is referenced as the target of an assignment or as the operand of `++` or `--`, the `set` accessor is invoked with an argument that provides the new value.</span></span>

<span data-ttu-id="7a608-602">La classe `List<T>
` déclare deux propriétés, `Count` et `Capacity`, qui sont respectivement en lecture seule et en lecture-écriture.</span><span class="sxs-lookup"><span data-stu-id="7a608-602">The `List<T>
` class declares two properties, `Count` and `Capacity`, which are read-only and read-write, respectively.</span></span> <span data-ttu-id="7a608-603">Voici un exemple d’utilisation de ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="7a608-603">The following is an example of use of these properties.</span></span>

```csharp
List<string> names = new List<string>();
names.Capacity = 100;            // Invokes set accessor
int i = names.Count;             // Invokes get accessor
int j = names.Capacity;          // Invokes get accessor
```
<span data-ttu-id="7a608-604">C# prend en charge les propriétés d’instance et les propriétés statiques, similaires aux champs et aux méthodes.</span><span class="sxs-lookup"><span data-stu-id="7a608-604">Similar to fields and methods, C# supports both instance properties and static properties.</span></span> <span data-ttu-id="7a608-605">Propriétés statiques sont déclarées avec le `static` modificateur et les propriétés de l’instance sont déclarées sans.</span><span class="sxs-lookup"><span data-stu-id="7a608-605">Static properties are declared with the `static` modifier, and instance properties are declared without it.</span></span>

<span data-ttu-id="7a608-606">Le ou les accesseurs d’une propriété peuvent être virtuels.</span><span class="sxs-lookup"><span data-stu-id="7a608-606">The accessor(s) of a property can be virtual.</span></span> <span data-ttu-id="7a608-607">Lorsqu’une déclaration de propriété inclut un modificateur `virtual`, `abstract` ou `override`, elle s’applique aux accesseurs de la propriété.</span><span class="sxs-lookup"><span data-stu-id="7a608-607">When a property declaration includes a `virtual`, `abstract`, or `override` modifier, it applies to the accessor(s) of the property.</span></span>

#### <a name="indexers"></a><span data-ttu-id="7a608-608">Indexeurs</span><span class="sxs-lookup"><span data-stu-id="7a608-608">Indexers</span></span>

<span data-ttu-id="7a608-609">Un ***indexeur*** est un membre qui permet l’indexation des objets de la même façon en tant que tableau.</span><span class="sxs-lookup"><span data-stu-id="7a608-609">An ***indexer*** is a member that enables objects to be indexed in the same way as an array.</span></span> <span data-ttu-id="7a608-610">Un indexeur est déclaré comme une propriété, sauf que le nom du membre est `this` suivie d’une liste de paramètres écrite entre les délimiteurs `[` et `]`.</span><span class="sxs-lookup"><span data-stu-id="7a608-610">An indexer is declared like a property except that the name of the member is `this` followed by a parameter list written between the delimiters `[` and `]`.</span></span> <span data-ttu-id="7a608-611">Les paramètres sont disponibles dans le ou les accesseurs de l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="7a608-611">The parameters are available in the accessor(s) of the indexer.</span></span> <span data-ttu-id="7a608-612">Similaires aux propriétés, les indexeurs peuvent être en lecture-écriture, en lecture seule et en écriture seule, et les accesseurs d’un indexeur peuvent être virtuels.</span><span class="sxs-lookup"><span data-stu-id="7a608-612">Similar to properties, indexers can be read-write, read-only, and write-only, and the accessor(s) of an indexer can be virtual.</span></span>

<span data-ttu-id="7a608-613">La classe `List` déclare un indexeur en lecture-écriture unique qui prend un paramètre `int`.</span><span class="sxs-lookup"><span data-stu-id="7a608-613">The `List` class declares a single read-write indexer that takes an `int` parameter.</span></span> <span data-ttu-id="7a608-614">L’indexeur rend possible l’indexation des instances `List` avec des valeurs `int`.</span><span class="sxs-lookup"><span data-stu-id="7a608-614">The indexer makes it possible to index `List` instances with `int` values.</span></span> <span data-ttu-id="7a608-615">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7a608-615">For example</span></span>

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
<span data-ttu-id="7a608-616">Les indexeurs peuvent être surchargés, ce qui signifie qu’une classe peut déclarer plusieurs indexeurs tant que le nombre ou les types de leurs paramètres diffèrent.</span><span class="sxs-lookup"><span data-stu-id="7a608-616">Indexers can be overloaded, meaning that a class can declare multiple indexers as long as the number or types of their parameters differ.</span></span>

#### <a name="events"></a><span data-ttu-id="7a608-617">Événements</span><span class="sxs-lookup"><span data-stu-id="7a608-617">Events</span></span>

<span data-ttu-id="7a608-618">Un ***événement*** est un membre qui permet à une classe ou un objet de fournir des notifications.</span><span class="sxs-lookup"><span data-stu-id="7a608-618">An ***event*** is a member that enables a class or object to provide notifications.</span></span> <span data-ttu-id="7a608-619">Un événement est déclaré comme un champ, à ceci près que la déclaration inclut un `event` mot clé et le type doivent être un type délégué.</span><span class="sxs-lookup"><span data-stu-id="7a608-619">An event is declared like a field except that the declaration includes an `event` keyword and the type must be a delegate type.</span></span>

<span data-ttu-id="7a608-620">Dans une classe qui déclare un membre d’événement, l’événement se comporte comme un champ d’un type délégué (à condition que l’événement n’est pas abstrait et ne déclare pas d’accesseurs).</span><span class="sxs-lookup"><span data-stu-id="7a608-620">Within a class that declares an event member, the event behaves just like a field of a delegate type (provided the event is not abstract and does not declare accessors).</span></span> <span data-ttu-id="7a608-621">Le champ stocke une référence à un délégué qui représente les gestionnaires d’événements qui ont été ajoutés à l’événement.</span><span class="sxs-lookup"><span data-stu-id="7a608-621">The field stores a reference to a delegate that represents the event handlers that have been added to the event.</span></span> <span data-ttu-id="7a608-622">Si aucun handle d’événement n’est présents, le champ est `null`.</span><span class="sxs-lookup"><span data-stu-id="7a608-622">If no event handles are present, the field is `null`.</span></span>

<span data-ttu-id="7a608-623">La classe `List<T>
` déclare un membre d’événement unique appelé `Changed`, qui indique qu’un nouvel élément a été ajouté à la liste.</span><span class="sxs-lookup"><span data-stu-id="7a608-623">The `List<T>
` class declares a single event member called `Changed`, which indicates that a new item has been added to the list.</span></span> <span data-ttu-id="7a608-624">Le `Changed` événement est déclenché par le `OnChanged` méthode virtuelle, qui vérifie si l’événement est `null` (ce qui signifie qu’aucun gestionnaire n’est présent).</span><span class="sxs-lookup"><span data-stu-id="7a608-624">The `Changed` event is raised by the `OnChanged` virtual method, which first checks whether the event is `null` (meaning that no handlers are present).</span></span> <span data-ttu-id="7a608-625">La notion de déclenchement d’un événement est équivalente à l’appel de délégué représenté par l’événement. Par conséquent, il n’existe aucune construction de langage particulière pour déclencher des événements.</span><span class="sxs-lookup"><span data-stu-id="7a608-625">The notion of raising an event is precisely equivalent to invoking the delegate represented by the event—thus, there are no special language constructs for raising events.</span></span>

<span data-ttu-id="7a608-626">Les clients réagissent aux événements via les ***gestionnaires d’événements***.</span><span class="sxs-lookup"><span data-stu-id="7a608-626">Clients react to events through ***event handlers***.</span></span> <span data-ttu-id="7a608-627">Les gestionnaires d’événements sont joints à l’aide de l’opérateur `+=` et supprimés à l’aide de l’opérateur `-=`.</span><span class="sxs-lookup"><span data-stu-id="7a608-627">Event handlers are attached using the `+=` operator and removed using the `-=` operator.</span></span> <span data-ttu-id="7a608-628">L'exemple suivant joint un gestionnaire d'événements à l'événement `Changed` d’un `List<string>
`.</span><span class="sxs-lookup"><span data-stu-id="7a608-628">The following example attaches an event handler to the `Changed` event of a `List<string>
`.</span></span>

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
<span data-ttu-id="7a608-629">Pour les scénarios avancés où le contrôle du stockage sous-jacent d’un événement est souhaité, une déclaration d’événement peut fournir explicitement des accesseurs `add` et `remove`, qui sont plutôt similaires à l’accesseur `set` d’une propriété.</span><span class="sxs-lookup"><span data-stu-id="7a608-629">For advanced scenarios where control of the underlying storage of an event is desired, an event declaration can explicitly provide `add` and `remove` accessors, which are somewhat similar to the `set` accessor of a property.</span></span>

#### <a name="operators"></a><span data-ttu-id="7a608-630">Opérateurs</span><span class="sxs-lookup"><span data-stu-id="7a608-630">Operators</span></span>

<span data-ttu-id="7a608-631">Un ***opérateur*** est un membre qui définit la signification de l’application d’un opérateur d’expression particulière aux instances d’une classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-631">An ***operator*** is a member that defines the meaning of applying a particular expression operator to instances of a class.</span></span> <span data-ttu-id="7a608-632">Trois types d’opérateurs peuvent être définis : les opérateurs unaires, les opérateurs binaires et les opérateurs de conversion.</span><span class="sxs-lookup"><span data-stu-id="7a608-632">Three kinds of operators can be defined: unary operators, binary operators, and conversion operators.</span></span> <span data-ttu-id="7a608-633">Tous les opérateurs doivent être déclarés comme `public` et `static`.</span><span class="sxs-lookup"><span data-stu-id="7a608-633">All operators must be declared as `public` and `static`.</span></span>

<span data-ttu-id="7a608-634">La classe `List<T>
` déclare deux opérateurs, `operator==` et `operator!=`, et donne donc une nouvelle signification aux expressions qui appliquent ces opérateurs aux instances `List`.</span><span class="sxs-lookup"><span data-stu-id="7a608-634">The `List<T>
` class declares two operators, `operator==` and `operator!=`, and thus gives new meaning to expressions that apply those operators to `List` instances.</span></span> <span data-ttu-id="7a608-635">Plus précisément, les opérateurs de définissent une égalité de deux `List<T>
` instances comme la comparaison de chacun des objets de relation contenant-contenus à l’aide de leurs `Equals` méthodes.</span><span class="sxs-lookup"><span data-stu-id="7a608-635">Specifically, the operators define equality of two `List<T>
` instances as comparing each of the contained objects using their `Equals` methods.</span></span> <span data-ttu-id="7a608-636">L’exemple suivant utilise l’opérateur `==` pour comparer deux instances de `List<int>
`.</span><span class="sxs-lookup"><span data-stu-id="7a608-636">The following example uses the `==` operator to compare two `List<int>
` instances.</span></span>

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

<span data-ttu-id="7a608-637">La première `Console.WriteLine` génère `True`, car les deux listes contiennent le même nombre d’objets avec les mêmes valeurs dans le même ordre.</span><span class="sxs-lookup"><span data-stu-id="7a608-637">The first `Console.WriteLine` outputs `True` because the two lists contain the same number of objects with the same values in the same order.</span></span> <span data-ttu-id="7a608-638">Si `List<T>
` n’avait pas défini `operator==`, la première `Console.WriteLine` aurait affiché `False`, car `a` et `b` référencent des instances `List<int>
` différentes.</span><span class="sxs-lookup"><span data-stu-id="7a608-638">Had `List<T>
` not defined `operator==`, the first `Console.WriteLine` would have output `False` because `a` and `b` reference different `List<int>
` instances.</span></span>

#### <a name="destructors"></a><span data-ttu-id="7a608-639">Destructeurs</span><span class="sxs-lookup"><span data-stu-id="7a608-639">Destructors</span></span>

<span data-ttu-id="7a608-640">Un ***destructeur*** est un membre qui implémente les actions requises pour détruire une instance d’une classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-640">A ***destructor*** is a member that implements the actions required to destruct an instance of a class.</span></span> <span data-ttu-id="7a608-641">Les destructeurs ne peuvent pas avoir de paramètres, ils ne peuvent pas avoir de modificateurs d’accessibilité et ils ne peuvent pas être appelés explicitement.</span><span class="sxs-lookup"><span data-stu-id="7a608-641">Destructors cannot have parameters, they cannot have accessibility modifiers, and they cannot be invoked explicitly.</span></span> <span data-ttu-id="7a608-642">Le destructeur d’une instance est appelé automatiquement lors du garbage collection.</span><span class="sxs-lookup"><span data-stu-id="7a608-642">The destructor for an instance is invoked automatically during garbage collection.</span></span>

<span data-ttu-id="7a608-643">Le garbage collector est autorisé à toute latitude pour déterminer quand collecter des objets et exécuter des destructeurs.</span><span class="sxs-lookup"><span data-stu-id="7a608-643">The garbage collector is allowed wide latitude in deciding when to collect objects and run destructors.</span></span> <span data-ttu-id="7a608-644">Plus précisément, le minutage des appels de destructeur n’est pas déterministe, et les destructeurs ne peuvent être exécutés sur n’importe quel thread.</span><span class="sxs-lookup"><span data-stu-id="7a608-644">Specifically, the timing of destructor invocations is not deterministic, and destructors may be executed on any thread.</span></span> <span data-ttu-id="7a608-645">Pour ces raisons et d’autres, les classes doivent implémenter des destructeurs uniquement lorsque aucune des autres solutions ne sont envisageables.</span><span class="sxs-lookup"><span data-stu-id="7a608-645">For these and other reasons, classes should implement destructors only when no other solutions are feasible.</span></span>

<span data-ttu-id="7a608-646">L’instruction `using` fournit une meilleure approche pour la destruction d’objets.</span><span class="sxs-lookup"><span data-stu-id="7a608-646">The `using` statement provides a better approach to object destruction.</span></span>

## <a name="structs"></a><span data-ttu-id="7a608-647">Structs</span><span class="sxs-lookup"><span data-stu-id="7a608-647">Structs</span></span>

<span data-ttu-id="7a608-648">Comme les classes, les ***structs*** sont des structures de données qui peuvent contenir des membres de données et des fonctions membres. Mais contrairement aux classes, les structures sont des types valeur et ne nécessitent pas d’allocation de tas.</span><span class="sxs-lookup"><span data-stu-id="7a608-648">Like classes, ***structs*** are data structures that can contain data members and function members, but unlike classes, structs are value types and do not require heap allocation.</span></span> <span data-ttu-id="7a608-649">Une variable de type struct stocke directement les données de la structure, alors qu’une variable de type class stocke une référence à un objet alloué dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="7a608-649">A variable of a struct type directly stores the data of the struct, whereas a variable of a class type stores a reference to a dynamically allocated object.</span></span> <span data-ttu-id="7a608-650">Les types struct ne prennent pas en charge l’héritage spécifié par l’utilisateur, et tous les types struct héritent implicitement du type `object`.</span><span class="sxs-lookup"><span data-stu-id="7a608-650">Struct types do not support user-specified inheritance, and all struct types implicitly inherit from type `object`.</span></span>

<span data-ttu-id="7a608-651">Les structures sont particulièrement utiles pour les petites structures de données qui ont une sémantique par rapport à leurs valeurs.</span><span class="sxs-lookup"><span data-stu-id="7a608-651">Structs are particularly useful for small data structures that have value semantics.</span></span> <span data-ttu-id="7a608-652">Les nombres complexes, les points dans un système de coordonnées ou les paires clé-valeur dans un dictionnaire sont de bons exemples de structures.</span><span class="sxs-lookup"><span data-stu-id="7a608-652">Complex numbers, points in a coordinate system, or key-value pairs in a dictionary are all good examples of structs.</span></span> <span data-ttu-id="7a608-653">L’utilisation de structures plutôt que de classes pour les petites structures de données peut faire une grande différence dans le nombre d’allocations de mémoire effectuées par une application.</span><span class="sxs-lookup"><span data-stu-id="7a608-653">The use of structs rather than classes for small data structures can make a large difference in the number of memory allocations an application performs.</span></span> <span data-ttu-id="7a608-654">Par exemple, le programme suivant crée et initialise un tableau de 100 points.</span><span class="sxs-lookup"><span data-stu-id="7a608-654">For example, the following program creates and initializes an array of 100 points.</span></span> <span data-ttu-id="7a608-655">Avec `Point` implémenté en tant que classe, 101 objets séparés sont instanciés : un pour le tableau et un pour chacun des 100 éléments.</span><span class="sxs-lookup"><span data-stu-id="7a608-655">With `Point` implemented as a class, 101 separate objects are instantiated—one for the array and one each for the 100 elements.</span></span>

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
<span data-ttu-id="7a608-656">Une alternative consiste à faire `Point` un struct.</span><span class="sxs-lookup"><span data-stu-id="7a608-656">An alternative is to make `Point` a struct.</span></span>

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
<span data-ttu-id="7a608-657">Maintenant, un seul objet est instancié, celui du tableau, et les instances `Point` sont stockées à la suite dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="7a608-657">Now, only one object is instantiated—the one for the array—and the `Point` instances are stored in-line in the array.</span></span>

<span data-ttu-id="7a608-658">Vous appelez les constructeurs de struct avec l’opérateur `new`, mais cela n’implique pas que de la mémoire soit allouée.</span><span class="sxs-lookup"><span data-stu-id="7a608-658">Struct constructors are invoked with the `new` operator, but that does not imply that memory is being allocated.</span></span> <span data-ttu-id="7a608-659">Au lieu d’allouer dynamiquement un objet et de renvoyer une référence à cet objet, un constructeur de struct retourne simplement la valeur du struct (généralement dans un emplacement temporaire sur la pile), et cette valeur est ensuite copiée si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7a608-659">Instead of dynamically allocating an object and returning a reference to it, a struct constructor simply returns the struct value itself (typically in a temporary location on the stack), and this value is then copied as necessary.</span></span>

<span data-ttu-id="7a608-660">Avec les classes, deux variables peuvent faire référence au même objet et, par conséquent, les opérations sur une variable peuvent affecter le même objet référencé par l'autre variable.</span><span class="sxs-lookup"><span data-stu-id="7a608-660">With classes, it is possible for two variables to reference the same object and thus possible for operations on one variable to affect the object referenced by the other variable.</span></span> <span data-ttu-id="7a608-661">Avec les structs, les variables disposent chacune de leur propre copie des données, et il n’est pas possible pour les opérations sur une d’affecter les autres.</span><span class="sxs-lookup"><span data-stu-id="7a608-661">With structs, the variables each have their own copy of the data, and it is not possible for operations on one to affect the other.</span></span> <span data-ttu-id="7a608-662">Par exemple, la sortie produite par le fragment de code suivant varie selon que `Point` est une classe ou un struct.</span><span class="sxs-lookup"><span data-stu-id="7a608-662">For example, the output produced by the following code fragment depends on whether `Point` is a class or a struct.</span></span>

```csharp
Point a = new Point(10, 10);
Point b = a;
a.x = 20;
Console.WriteLine(b.x);
```
<span data-ttu-id="7a608-663">Si `Point` est une classe, la sortie est `20` car `a` et `b` référencent le même objet.</span><span class="sxs-lookup"><span data-stu-id="7a608-663">If `Point` is a class, the output is `20` because `a` and `b` reference the same object.</span></span> <span data-ttu-id="7a608-664">Si `Point` est un struct, la sortie est `10` , car l’affectation de `a` à `b` crée une copie de la valeur, et cette copie n’est pas affectée par l’assignation consécutive de `a.x`.</span><span class="sxs-lookup"><span data-stu-id="7a608-664">If `Point` is a struct, the output is `10` because the assignment of `a` to `b` creates a copy of the value, and this copy is unaffected by the subsequent assignment to `a.x`.</span></span>

<span data-ttu-id="7a608-665">L’exemple précédent illustre deux des limitations des structs.</span><span class="sxs-lookup"><span data-stu-id="7a608-665">The previous example highlights two of the limitations of structs.</span></span> <span data-ttu-id="7a608-666">Tout d’abord, la copie d’un struct entier est généralement moins efficace que la copie d’une référence d’objet, aussi le passage de paramètres d’affectation et de valeur peut être plus coûteux avec les structures qu’avec les types référence.</span><span class="sxs-lookup"><span data-stu-id="7a608-666">First, copying an entire struct is typically less efficient than copying an object reference, so assignment and value parameter passing can be more expensive with structs than with reference types.</span></span> <span data-ttu-id="7a608-667">Ensuite, à l’exception des paramètres `ref` et `out`, il n’est pas possible de créer des références aux structures, ce qui rend leur utilisation impossible dans un certain nombre de situations.</span><span class="sxs-lookup"><span data-stu-id="7a608-667">Second, except for `ref` and `out` parameters, it is not possible to create references to structs, which rules out their usage in a number of situations.</span></span>

## <a name="arrays"></a><span data-ttu-id="7a608-668">Tableaux</span><span class="sxs-lookup"><span data-stu-id="7a608-668">Arrays</span></span>

<span data-ttu-id="7a608-669">Un ***tableau*** est une structure de données qui contient un certain nombre de variables qui sont accessibles par des indices calculés.</span><span class="sxs-lookup"><span data-stu-id="7a608-669">An ***array*** is a data structure that contains a number of variables that are accessed through computed indices.</span></span> <span data-ttu-id="7a608-670">Les variables contenues dans un tableau, également appelé ***éléments*** du tableau, sont tous du même type, et ce type est appelé ***type d’élément*** du tableau.</span><span class="sxs-lookup"><span data-stu-id="7a608-670">The variables contained in an array, also called the ***elements*** of the array, are all of the same type, and this type is called the ***element type*** of the array.</span></span>

<span data-ttu-id="7a608-671">Les types tableau sont des types référence, et la déclaration d’une variable tableau réserve simplement un espace pour une référence à une instance de tableau.</span><span class="sxs-lookup"><span data-stu-id="7a608-671">Array types are reference types, and the declaration of an array variable simply sets aside space for a reference to an array instance.</span></span> <span data-ttu-id="7a608-672">Instances de tableau réelles sont créées dynamiquement au moment de l’exécution à l’aide du `new` opérateur.</span><span class="sxs-lookup"><span data-stu-id="7a608-672">Actual array instances are created dynamically at run-time using the `new` operator.</span></span> <span data-ttu-id="7a608-673">Le `new` opération spécifie la ***longueur*** de la nouvelle instance de tableau, qui est ensuite fixée pour la durée de vie de l’instance.</span><span class="sxs-lookup"><span data-stu-id="7a608-673">The `new` operation specifies the ***length*** of the new array instance, which is then fixed for the lifetime of the instance.</span></span> <span data-ttu-id="7a608-674">Les indices des éléments d’un tableau vont de `0` à `Length - 1`.</span><span class="sxs-lookup"><span data-stu-id="7a608-674">The indices of the elements of an array range from `0` to `Length - 1`.</span></span> <span data-ttu-id="7a608-675">L’opérateur `new` initialise automatiquement les éléments d’un tableau à leur valeur par défaut, c'est-à-dire, par exemple, zéro pour tous les types numériques et `null` pour tous les types référence.</span><span class="sxs-lookup"><span data-stu-id="7a608-675">The `new` operator automatically initializes the elements of an array to their default value, which, for example, is zero for all numeric types and `null` for all reference types.</span></span>

<span data-ttu-id="7a608-676">L’exemple suivant crée un tableau de `int` éléments, initialise le tableau et imprime le contenu du tableau.</span><span class="sxs-lookup"><span data-stu-id="7a608-676">The following example creates an array of `int` elements, initializes the array, and prints out the contents of the array.</span></span>

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
<span data-ttu-id="7a608-677">Cet exemple crée et utilise un ***tableau unidimensionnel***.</span><span class="sxs-lookup"><span data-stu-id="7a608-677">This example creates and operates on a ***single-dimensional array***.</span></span> <span data-ttu-id="7a608-678">C# prend également en charge les ***tableaux multidimensionnels***.</span><span class="sxs-lookup"><span data-stu-id="7a608-678">C# also supports ***multi-dimensional arrays***.</span></span> <span data-ttu-id="7a608-679">Le nombre de dimensions d’un type tableau, également appelé ***rang*** du type tableau, est de un plus le nombre de virgules entre les crochets du type tableau.</span><span class="sxs-lookup"><span data-stu-id="7a608-679">The number of dimensions of an array type, also known as the ***rank*** of the array type, is one plus the number of commas written between the square brackets of the array type.</span></span> <span data-ttu-id="7a608-680">L’exemple suivant alloue un tableau tridimensionnel, une dimension et un à deux dimensions.</span><span class="sxs-lookup"><span data-stu-id="7a608-680">The following example allocates a one-dimensional, a two-dimensional, and a three-dimensional array.</span></span>

```csharp
int[] a1 = new int[10];
int[,] a2 = new int[10, 5];
int[,,] a3 = new int[10, 5, 2];
```
<span data-ttu-id="7a608-681">Le tableau `a1` contient 10 éléments, le tableau `a2` en contient 50 (10 x 5) et le tableau `a3` en contient 100 (10 × 5 × 2).</span><span class="sxs-lookup"><span data-stu-id="7a608-681">The `a1` array contains 10 elements, the `a2` array contains 50 (10 × 5) elements, and the `a3` array contains 100 (10 × 5 × 2) elements.</span></span>

<span data-ttu-id="7a608-682">Le type d’élément d’un tableau peut être de n’importe quel type, y compris un type tableau.</span><span class="sxs-lookup"><span data-stu-id="7a608-682">The element type of an array can be any type, including an array type.</span></span> <span data-ttu-id="7a608-683">Un tableau avec des éléments d’un type tableau est parfois appelé un ***tableau en escalier***, car les longueurs des tableaux d’éléments ne sont pas nécessairement pas les mêmes.</span><span class="sxs-lookup"><span data-stu-id="7a608-683">An array with elements of an array type is sometimes called a ***jagged array*** because the lengths of the element arrays do not all have to be the same.</span></span> <span data-ttu-id="7a608-684">L’exemple suivant alloue un tableau de tableaux de `int` :</span><span class="sxs-lookup"><span data-stu-id="7a608-684">The following example allocates an array of arrays of `int`:</span></span>

```csharp
int[][] a = new int[3][];
a[0] = new int[10];
a[1] = new int[5];
a[2] = new int[20];
```
<span data-ttu-id="7a608-685">La première ligne crée un tableau avec trois éléments, chacun de type `int[]` et chacun avec une valeur initiale de `null`.</span><span class="sxs-lookup"><span data-stu-id="7a608-685">The first line creates an array with three elements, each of type `int[]` and each with an initial value of `null`.</span></span> <span data-ttu-id="7a608-686">Les lignes suivantes initialisent ensuite les trois éléments avec des références aux instances individuelles de tableau de longueur variable.</span><span class="sxs-lookup"><span data-stu-id="7a608-686">The subsequent lines then initialize the three elements with references to individual array instances of varying lengths.</span></span>

<span data-ttu-id="7a608-687">Le `new` opérateur autorise les valeurs initiales des éléments du tableau en utilisant un ***initialiseur de tableau***, qui est une liste d’expressions écrites entre les délimiteurs `{` et `}`.</span><span class="sxs-lookup"><span data-stu-id="7a608-687">The `new` operator permits the initial values of the array elements to be specified using an ***array initializer***, which is a list of expressions written between the delimiters `{` and `}`.</span></span> <span data-ttu-id="7a608-688">L’exemple suivant alloue et initialise un `int[]` avec trois éléments.</span><span class="sxs-lookup"><span data-stu-id="7a608-688">The following example allocates and initializes an `int[]` with three elements.</span></span>

```csharp
int[] a = new int[] {1, 2, 3};
```
<span data-ttu-id="7a608-689">Notez que la longueur du tableau est déduite à partir du nombre d’expressions entre `{` et `}`.</span><span class="sxs-lookup"><span data-stu-id="7a608-689">Note that the length of the array is inferred from the number of expressions between `{` and `}`.</span></span> <span data-ttu-id="7a608-690">Les déclarations locales des champs et variables peuvent être abrégées davantage afin de ne pas avoir à redéclarer le type tableau.</span><span class="sxs-lookup"><span data-stu-id="7a608-690">Local variable and field declarations can be shortened further such that the array type does not have to be restated.</span></span>

```csharp
int[] a = {1, 2, 3};
```
<span data-ttu-id="7a608-691">Les deux exemples précédents sont équivalents à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="7a608-691">Both of the previous examples are equivalent to the following:</span></span>

```csharp
int[] t = new int[3];
t[0] = 1;
t[1] = 2;
t[2] = 3;
int[] a = t;
```
## <a name="interfaces"></a><span data-ttu-id="7a608-692">Interfaces</span><span class="sxs-lookup"><span data-stu-id="7a608-692">Interfaces</span></span>

<span data-ttu-id="7a608-693">Une ***interface*** définit un contrat qui peut être implémenté par des classes et structures.</span><span class="sxs-lookup"><span data-stu-id="7a608-693">An ***interface*** defines a contract that can be implemented by classes and structs.</span></span> <span data-ttu-id="7a608-694">Une interface peut contenir des méthodes, des propriétés, des événements et des indexeurs.</span><span class="sxs-lookup"><span data-stu-id="7a608-694">An interface can contain methods, properties, events, and indexers.</span></span> <span data-ttu-id="7a608-695">Une interface ne fournit pas les implémentations des membres qu’elle définit, elle indique simplement les membres qui doivent être fournis par les classes ou les structs qui implémentent l’interface.</span><span class="sxs-lookup"><span data-stu-id="7a608-695">An interface does not provide implementations of the members it defines—it merely specifies the members that must be supplied by classes or structs that implement the interface.</span></span>

<span data-ttu-id="7a608-696">Les interfaces peuvent utiliser ***l’héritage multiple***.</span><span class="sxs-lookup"><span data-stu-id="7a608-696">Interfaces may employ ***multiple inheritance***.</span></span> <span data-ttu-id="7a608-697">Dans l’exemple suivant, l’interface `IComboBox` hérite à la fois de `ITextBox` et `IListBox`.</span><span class="sxs-lookup"><span data-stu-id="7a608-697">In the following example, the interface `IComboBox` inherits from both `ITextBox` and `IListBox`.</span></span>

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
<span data-ttu-id="7a608-698">Les classes et les structs peuvent implémenter plusieurs interfaces.</span><span class="sxs-lookup"><span data-stu-id="7a608-698">Classes and structs can implement multiple interfaces.</span></span> <span data-ttu-id="7a608-699">Dans l’exemple suivant, la classe `EditBox` implémente à la fois `IControl` et `IDataBound`.</span><span class="sxs-lookup"><span data-stu-id="7a608-699">In the following example, the class `EditBox` implements both `IControl` and `IDataBound`.</span></span>

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
<span data-ttu-id="7a608-700">Lorsqu’une classe ou un struct implémente une interface spécifique, les instances de cette classe ou struct peuvent être converties implicitement en ce type d’interface.</span><span class="sxs-lookup"><span data-stu-id="7a608-700">When a class or struct implements a particular interface, instances of that class or struct can be implicitly converted to that interface type.</span></span> <span data-ttu-id="7a608-701">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7a608-701">For example</span></span>

```csharp
EditBox editBox = new EditBox();
IControl control = editBox;
IDataBound dataBound = editBox;
```
<span data-ttu-id="7a608-702">Si une instance n’est pas connue pour implémenter une interface donnée de façon statique, des casts de type dynamiques peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="7a608-702">In cases where an instance is not statically known to implement a particular interface, dynamic type casts can be used.</span></span> <span data-ttu-id="7a608-703">Par exemple, les instructions suivantes utilisent des casts de type dynamique pour obtenir d’un objet `IControl` et `IDataBound` implémentations d’interface.</span><span class="sxs-lookup"><span data-stu-id="7a608-703">For example, the following statements use dynamic type casts to obtain an object's `IControl` and `IDataBound` interface implementations.</span></span> <span data-ttu-id="7a608-704">Étant donné que le type réel de l’objet est `EditBox`, les casts réussissent.</span><span class="sxs-lookup"><span data-stu-id="7a608-704">Because the actual type of the object is `EditBox`, the casts succeed.</span></span>

```csharp
object obj = new EditBox();
IControl control = (IControl)obj;
IDataBound dataBound = (IDataBound)obj;
```
<span data-ttu-id="7a608-705">Dans le précédent `EditBox` (classe), le `Paint` méthode à partir de la `IControl` interface et le `Bind` méthode à partir de la `IDataBound` interface sont implémentées à l’aide de `public` membres.</span><span class="sxs-lookup"><span data-stu-id="7a608-705">In the previous `EditBox` class, the `Paint` method from the `IControl` interface and the `Bind` method from the `IDataBound` interface are implemented using `public` members.</span></span> <span data-ttu-id="7a608-706">C# prend également en charge ***implémentations de membres d’interface explicite***, à l’aide de laquelle la classe ou struct permettre éviter d’effectuer les membres `public`.</span><span class="sxs-lookup"><span data-stu-id="7a608-706">C# also supports ***explicit interface member implementations***, using which the class or struct can avoid making the members `public`.</span></span> <span data-ttu-id="7a608-707">Une implémentation de membre d’interface explicite est écrite à l’aide du nom de membre d’interface qualifié complet.</span><span class="sxs-lookup"><span data-stu-id="7a608-707">An explicit interface member implementation is written using the fully qualified interface member name.</span></span> <span data-ttu-id="7a608-708">Par exemple, la classe `EditBox` peut implémenter les méthodes `IControl.Paint` et `IDataBound.Bind` à l’aide des implémentations de membres d’interface explicites, comme suit.</span><span class="sxs-lookup"><span data-stu-id="7a608-708">For example, the `EditBox` class could implement the `IControl.Paint` and `IDataBound.Bind` methods using explicit interface member implementations as follows.</span></span>

```csharp
public class EditBox: IControl, IDataBound
{
    void IControl.Paint() {...}
    void IDataBound.Bind(Binder b) {...}
}
```
<span data-ttu-id="7a608-709">Les membres d’interface explicites sont accessibles uniquement via le type interface.</span><span class="sxs-lookup"><span data-stu-id="7a608-709">Explicit interface members can only be accessed via the interface type.</span></span> <span data-ttu-id="7a608-710">Par exemple, l’implémentation de `IControl.Paint` fournie par la précédente `EditBox` classe ne peut être appelée en convertissant d’abord en le `EditBox` font référence à la `IControl` type d’interface.</span><span class="sxs-lookup"><span data-stu-id="7a608-710">For example, the implementation of `IControl.Paint` provided by the previous `EditBox` class can only be invoked by first converting the `EditBox` reference to the `IControl` interface type.</span></span>

```csharp
EditBox editBox = new EditBox();
editBox.Paint();                        // Error, no such method
IControl control = editBox;
control.Paint();                        // Ok
```

## <a name="enums"></a><span data-ttu-id="7a608-711">Enums</span><span class="sxs-lookup"><span data-stu-id="7a608-711">Enums</span></span>

<span data-ttu-id="7a608-712">Un ***type enum*** est un type valeur distinct avec un ensemble de constantes nommées.</span><span class="sxs-lookup"><span data-stu-id="7a608-712">An ***enum type*** is a distinct value type with a set of named constants.</span></span> <span data-ttu-id="7a608-713">L’exemple suivant déclare et utilise un type enum nommé `Color` avec trois valeurs de constante, `Red`, `Green`, et `Blue`.</span><span class="sxs-lookup"><span data-stu-id="7a608-713">The following example declares and uses an enum type named `Color` with three constant values, `Red`, `Green`, and `Blue`.</span></span>

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
<span data-ttu-id="7a608-714">Chaque type énumération a un type intégral appelé le ***type sous-jacent*** du type enum.</span><span class="sxs-lookup"><span data-stu-id="7a608-714">Each enum type has a corresponding integral type called the ***underlying type*** of the enum type.</span></span> <span data-ttu-id="7a608-715">Un type enum qui ne déclare pas explicitement un type sous-jacent a un type sous-jacent de `int`.</span><span class="sxs-lookup"><span data-stu-id="7a608-715">An enum type that does not explicitly declare an underlying type has an underlying type of `int`.</span></span> <span data-ttu-id="7a608-716">Un type enum le format de stockage et la plage de valeurs possibles sont déterminés par son type sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="7a608-716">An enum type's storage format and range of possible values are determined by its underlying type.</span></span> <span data-ttu-id="7a608-717">L’ensemble de valeurs qui peut prendre un type enum n’est pas limité par ses membres enum.</span><span class="sxs-lookup"><span data-stu-id="7a608-717">The set of values that an enum type can take on is not limited by its enum members.</span></span> <span data-ttu-id="7a608-718">En particulier, n’importe quelle valeur du type sous-jacent d’une énumération peut être casté en type enum et est une valeur valide distincte de ce type enum.</span><span class="sxs-lookup"><span data-stu-id="7a608-718">In particular, any value of the underlying type of an enum can be cast to the enum type and is a distinct valid value of that enum type.</span></span>

<span data-ttu-id="7a608-719">L’exemple suivant déclare un type enum nommé `Alignment` avec un type sous-jacent de `sbyte`.</span><span class="sxs-lookup"><span data-stu-id="7a608-719">The following example declares an enum type named `Alignment` with an underlying type of `sbyte`.</span></span>

```csharp
enum Alignment: sbyte
{
    Left = -1,
    Center = 0,
    Right = 1
}
```
<span data-ttu-id="7a608-720">Comme le montre l’exemple précédent, une déclaration de membre enum peut inclure une expression constante qui spécifie la valeur du membre.</span><span class="sxs-lookup"><span data-stu-id="7a608-720">As shown by the previous example, an enum member declaration can include a constant expression that specifies the value of the member.</span></span> <span data-ttu-id="7a608-721">La valeur de constante pour chaque membre enum doit être dans la plage du type sous-jacent de l’enum.</span><span class="sxs-lookup"><span data-stu-id="7a608-721">The constant value for each enum member must be in the range of the underlying type of the enum.</span></span> <span data-ttu-id="7a608-722">Lorsqu’une déclaration de membre enum ne spécifie pas explicitement une valeur, le membre reçoit la valeur zéro (s’il est le premier membre dans le type enum) ou la valeur du membre enum qui précède plus un.</span><span class="sxs-lookup"><span data-stu-id="7a608-722">When an enum member declaration does not explicitly specify a value, the member is given the value zero (if it is the first member in the enum type) or the value of the textually preceding enum member plus one.</span></span>

<span data-ttu-id="7a608-723">Valeurs d’énumération peuvent être converties en valeurs intégrales et inversement à l’aide de casts de type.</span><span class="sxs-lookup"><span data-stu-id="7a608-723">Enum values can be converted to integral values and vice versa using type casts.</span></span> <span data-ttu-id="7a608-724">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7a608-724">For example</span></span>

```csharp
int i = (int)Color.Blue;        // int i = 2;
Color c = (Color)2;             // Color c = Color.Blue;
```
<span data-ttu-id="7a608-725">La valeur par défaut de tout type enum est la valeur intégrale de zéro convertie vers le type enum.</span><span class="sxs-lookup"><span data-stu-id="7a608-725">The default value of any enum type is the integral value zero converted to the enum type.</span></span> <span data-ttu-id="7a608-726">Dans les cas où les variables sont initialisées automatiquement à une valeur par défaut, il s’agit de la valeur donnée aux variables des types enum.</span><span class="sxs-lookup"><span data-stu-id="7a608-726">In cases where variables are automatically initialized to a default value, this is the value given to variables of enum types.</span></span> <span data-ttu-id="7a608-727">Afin que la valeur par défaut d’un type enum soit disponible, le littéral `0` convertit implicitement en un type enum.</span><span class="sxs-lookup"><span data-stu-id="7a608-727">In order for the default value of an enum type to be easily available, the literal `0` implicitly converts to any enum type.</span></span> <span data-ttu-id="7a608-728">Ce qui suit est donc autorisé.</span><span class="sxs-lookup"><span data-stu-id="7a608-728">Thus, the following is permitted.</span></span>

```csharp
Color c = 0;
```

## <a name="delegates"></a><span data-ttu-id="7a608-729">Délégués</span><span class="sxs-lookup"><span data-stu-id="7a608-729">Delegates</span></span>

<span data-ttu-id="7a608-730">Un ***type délégué*** représente des références aux méthodes avec une liste de paramètres et un type de retour particuliers.</span><span class="sxs-lookup"><span data-stu-id="7a608-730">A ***delegate type*** represents references to methods with a particular parameter list and return type.</span></span> <span data-ttu-id="7a608-731">Les délégués permettent de traiter les méthodes en tant qu’entités qui peuvent être affectées à des variables et passées comme paramètres.</span><span class="sxs-lookup"><span data-stu-id="7a608-731">Delegates make it possible to treat methods as entities that can be assigned to variables and passed as parameters.</span></span> <span data-ttu-id="7a608-732">Les délégués sont similaires au concept de pointeurs de fonction dans d’autres langages, mais contrairement aux pointeurs de fonction, les délégués sont orientés objet et de type sécurisé.</span><span class="sxs-lookup"><span data-stu-id="7a608-732">Delegates are similar to the concept of function pointers found in some other languages, but unlike function pointers, delegates are object-oriented and type-safe.</span></span>

<span data-ttu-id="7a608-733">L’exemple suivant déclare et utilise un type délégué nommé `Function`.</span><span class="sxs-lookup"><span data-stu-id="7a608-733">The following example declares and uses a delegate type named `Function`.</span></span>

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
<span data-ttu-id="7a608-734">Une instance du type délégué `Function` peut faire référence à toute méthode qui accepte un argument `double` et retourne une valeur `double`.</span><span class="sxs-lookup"><span data-stu-id="7a608-734">An instance of the `Function` delegate type can reference any method that takes a `double` argument and returns a `double` value.</span></span> <span data-ttu-id="7a608-735">Le `Apply` méthode s’applique une donnée `Function` aux éléments d’un `double[]`, qui retourne un `double[]` avec les résultats.</span><span class="sxs-lookup"><span data-stu-id="7a608-735">The `Apply` method applies a given `Function` to the elements of a `double[]`, returning a `double[]` with the results.</span></span> <span data-ttu-id="7a608-736">Dans la méthode `Main`, `Apply` sert à appliquer trois fonctions différentes à un `double[]`.</span><span class="sxs-lookup"><span data-stu-id="7a608-736">In the `Main` method, `Apply` is used to apply three different functions to a `double[]`.</span></span>

<span data-ttu-id="7a608-737">Un délégué peut faire référence à une méthode statique (comme `Square` ou `Math.Sin` dans l’exemple précédent) ou à une méthode d’instance (comme `m.Multiply` dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="7a608-737">A delegate can reference either a static method (such as `Square` or `Math.Sin` in the previous example) or an instance method (such as `m.Multiply` in the previous example).</span></span> <span data-ttu-id="7a608-738">Un délégué qui référence une méthode d’instance référence aussi un objet particulier, et quand la méthode d’instance est appelée via le délégué, cet objet devient `this` dans l’appel.</span><span class="sxs-lookup"><span data-stu-id="7a608-738">A delegate that references an instance method also references a particular object, and when the instance method is invoked through the delegate, that object becomes `this` in the invocation.</span></span>

<span data-ttu-id="7a608-739">Les délégués peuvent également être créés à l’aide de fonctions anonymes, qui sont des « méthodes inline » créées à la volée.</span><span class="sxs-lookup"><span data-stu-id="7a608-739">Delegates can also be created using anonymous functions, which are "inline methods" that are created on the fly.</span></span> <span data-ttu-id="7a608-740">Les fonctions anonymes peuvent voir les variables locales des méthodes qui l’entourent.</span><span class="sxs-lookup"><span data-stu-id="7a608-740">Anonymous functions can see the local variables of the surrounding methods.</span></span> <span data-ttu-id="7a608-741">Par conséquent, l’exemple de multiplicateur ci-dessus peut être écrit plus facilement sans utiliser un `Multiplier` classe :</span><span class="sxs-lookup"><span data-stu-id="7a608-741">Thus, the multiplier example above can be written more easily without using a `Multiplier` class:</span></span>

```csharp
double[] doubles =  Apply(a, (double x) => x * 2.0);
```
<span data-ttu-id="7a608-742">Une propriété intéressante et utile d’un délégué est qu’il ne connaît pas la classe de la méthode qu’il référence, et cela lui est égal. Tout ce qui importe est que la méthode référencée ait les mêmes paramètres et type de retour que le délégué.</span><span class="sxs-lookup"><span data-stu-id="7a608-742">An interesting and useful property of a delegate is that it does not know or care about the class of the method it references; all that matters is that the referenced method has the same parameters and return type as the delegate.</span></span>

## <a name="attributes"></a><span data-ttu-id="7a608-743">Attributs</span><span class="sxs-lookup"><span data-stu-id="7a608-743">Attributes</span></span>

<span data-ttu-id="7a608-744">Les types, membres et autres entités d’un programme C# prennent en charge les modificateurs qui contrôlent certains aspects de leur comportement.</span><span class="sxs-lookup"><span data-stu-id="7a608-744">Types, members, and other entities in a C# program support modifiers that control certain aspects of their behavior.</span></span> <span data-ttu-id="7a608-745">Par exemple, l’accessibilité d’une méthode est contrôlée à l’aide des modificateurs `public`, `protected`, `internal` et `private`.</span><span class="sxs-lookup"><span data-stu-id="7a608-745">For example, the accessibility of a method is controlled using the `public`, `protected`, `internal`, and `private` modifiers.</span></span> <span data-ttu-id="7a608-746">C# généralise cette fonctionnalité pour que les types d’informations déclaratives définis par l’utilisateur puissent être associés aux entités de programme et récupérés au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="7a608-746">C# generalizes this capability such that user-defined types of declarative information can be attached to program entities and retrieved at run-time.</span></span> <span data-ttu-id="7a608-747">Les programmes spécifient ces informations déclaratives supplémentaires en définissant et en utilisant les ***attributs***.</span><span class="sxs-lookup"><span data-stu-id="7a608-747">Programs specify this additional declarative information by defining and using ***attributes***.</span></span>

<span data-ttu-id="7a608-748">L’exemple suivant déclare un attribut `HelpAttribute` qui peut être placé sur des entités de programme pour fournir des liens vers leur documentation associée.</span><span class="sxs-lookup"><span data-stu-id="7a608-748">The following example declares a `HelpAttribute` attribute that can be placed on program entities to provide links to their associated documentation.</span></span>

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
<span data-ttu-id="7a608-749">Toutes les classes d’attributs dérivent le `System.Attribute` fourni par le .NET Framework de classe de base.</span><span class="sxs-lookup"><span data-stu-id="7a608-749">All attribute classes derive from the `System.Attribute` base class provided by the .NET Framework.</span></span> <span data-ttu-id="7a608-750">Les attributs peuvent être appliqués en donnant leur nom, ainsi que les éventuels arguments, à l’intérieur de crochets juste avant la déclaration associée.</span><span class="sxs-lookup"><span data-stu-id="7a608-750">Attributes can be applied by giving their name, along with any arguments, inside square brackets just before the associated declaration.</span></span> <span data-ttu-id="7a608-751">Si le nom d’un attribut se termine par `Attribute`, cette partie du nom peut être omise lorsque l’attribut est référencé.</span><span class="sxs-lookup"><span data-stu-id="7a608-751">If an attribute's name ends in `Attribute`, that part of the name can be omitted when the attribute is referenced.</span></span> <span data-ttu-id="7a608-752">Par exemple, l’attribut `HelpAttribute` peut être utilisé comme suit.</span><span class="sxs-lookup"><span data-stu-id="7a608-752">For example, the `HelpAttribute` attribute can be used as follows.</span></span>

```csharp
[Help("http://msdn.microsoft.com/.../MyClass.htm")]
public class Widget
{
    [Help("http://msdn.microsoft.com/.../MyClass.htm", Topic = "Display")]
    public void Display(string text) {}
}
```
<span data-ttu-id="7a608-753">Cet exemple attache un `HelpAttribute` à la `Widget` classe et l’autre `HelpAttribute` à la `Display` méthode dans la classe.</span><span class="sxs-lookup"><span data-stu-id="7a608-753">This example attaches a `HelpAttribute` to the `Widget` class and another `HelpAttribute` to the `Display` method in the class.</span></span> <span data-ttu-id="7a608-754">Les constructeurs publics d’une classe d’attributs contrôlent les informations qui doivent être fournies lorsque l’attribut est joint à une entité de programme.</span><span class="sxs-lookup"><span data-stu-id="7a608-754">The public constructors of an attribute class control the information that must be provided when the attribute is attached to a program entity.</span></span> <span data-ttu-id="7a608-755">Des informations supplémentaires peuvent être fournies en référençant les propriétés en lecture-écriture publiques de la classe d’attributs (comme la référence à la propriété `Topic`, précédemment).</span><span class="sxs-lookup"><span data-stu-id="7a608-755">Additional information can be provided by referencing public read-write properties of the attribute class (such as the reference to the `Topic` property previously).</span></span>

<span data-ttu-id="7a608-756">L’exemple suivant montre comment les informations d’attribut pour une entité de programme donné peuvent être récupérées au moment de l’exécution à l’aide de la réflexion.</span><span class="sxs-lookup"><span data-stu-id="7a608-756">The following example shows how attribute information for a given program entity can be retrieved at run-time using reflection.</span></span>

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
<span data-ttu-id="7a608-757">Lorsqu’un attribut particulier est demandé par réflexion, le constructeur de la classe d’attributs est appelé avec les informations fournies dans la source du programme, et l’instance d’attribut qui en résulte est retournée.</span><span class="sxs-lookup"><span data-stu-id="7a608-757">When a particular attribute is requested through reflection, the constructor for the attribute class is invoked with the information provided in the program source, and the resulting attribute instance is returned.</span></span> <span data-ttu-id="7a608-758">Si des informations supplémentaires ont été fournies via les propriétés, ces propriétés sont définies sur les valeurs données avant que le renvoi de l’instance de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="7a608-758">If additional information was provided through properties, those properties are set to the given values before the attribute instance is returned.</span></span>
