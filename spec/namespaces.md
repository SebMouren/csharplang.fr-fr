---
ms.openlocfilehash: 9c3863c9a139f5b8309fca6e0c099d0fae7677c3
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229718"
---
# <a name="namespaces"></a><span data-ttu-id="4b063-101">Espaces de noms</span><span class="sxs-lookup"><span data-stu-id="4b063-101">Namespaces</span></span>

<span data-ttu-id="4b063-102">Les programmes c# sont organisés à l’aide d’espaces de noms.</span><span class="sxs-lookup"><span data-stu-id="4b063-102">C# programs are organized using namespaces.</span></span> <span data-ttu-id="4b063-103">Espaces de noms sont utilisés comme un système d’organisation « interne » pour un programme et comme un système d’organisation « external » : une façon de présenter des éléments de programme qui sont exposés à d’autres programmes.</span><span class="sxs-lookup"><span data-stu-id="4b063-103">Namespaces are used both as an "internal" organization system for a program, and as an "external" organization system—a way of presenting program elements that are exposed to other programs.</span></span>

<span data-ttu-id="4b063-104">Directives using ([à l’aide de directives](namespaces.md#using-directives)) sont fournies pour faciliter l’utilisation des espaces de noms.</span><span class="sxs-lookup"><span data-stu-id="4b063-104">Using directives ([Using directives](namespaces.md#using-directives)) are provided to facilitate the use of namespaces.</span></span>

## <a name="compilation-units"></a><span data-ttu-id="4b063-105">Unités de compilation</span><span class="sxs-lookup"><span data-stu-id="4b063-105">Compilation units</span></span>

<span data-ttu-id="4b063-106">Un *compilation_unit* définit la structure globale d’un fichier source.</span><span class="sxs-lookup"><span data-stu-id="4b063-106">A *compilation_unit* defines the overall structure of a source file.</span></span> <span data-ttu-id="4b063-107">Une unité de compilation se compose de zéro ou plusieurs *using_directive*s suivie de zéro ou plusieurs *global_attributes* suivie de zéro ou plusieurs *namespace_member_declaration*s .</span><span class="sxs-lookup"><span data-stu-id="4b063-107">A compilation unit consists of zero or more *using_directive*s followed by zero or more *global_attributes* followed by zero or more *namespace_member_declaration*s.</span></span>

```antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? namespace_member_declaration*
    ;
```

<span data-ttu-id="4b063-108">Un programme c# se compose d’une ou plusieurs unités de compilation, tous contenus dans un fichier source séparé.</span><span class="sxs-lookup"><span data-stu-id="4b063-108">A C# program consists of one or more compilation units, each contained in a separate source file.</span></span> <span data-ttu-id="4b063-109">Lorsqu’un programme c# est compilé, toutes les unités de compilation sont traités ensemble.</span><span class="sxs-lookup"><span data-stu-id="4b063-109">When a C# program is compiled, all of the compilation units are processed together.</span></span> <span data-ttu-id="4b063-110">Par conséquent, les unités de compilation peuvent dépendre eux, éventuellement de manière circulaire.</span><span class="sxs-lookup"><span data-stu-id="4b063-110">Thus, compilation units can depend on each other, possibly in a circular fashion.</span></span>

<span data-ttu-id="4b063-111">Le *using_directive*s d’un effet d’unité de compilation la *global_attributes* et *namespace_member_declaration*s de cette unité de compilation, mais n’ont aucun effet autres unités de compilation.</span><span class="sxs-lookup"><span data-stu-id="4b063-111">The *using_directive*s of a compilation unit affect the *global_attributes* and *namespace_member_declaration*s of that compilation unit, but have no effect on other compilation units.</span></span>

<span data-ttu-id="4b063-112">Le *global_attributes* ([attributs](attributes.md)) d’une unité de compilation permettent la spécification d’attributs pour l’assembly cible et le module.</span><span class="sxs-lookup"><span data-stu-id="4b063-112">The *global_attributes* ([Attributes](attributes.md)) of a compilation unit permit the specification of attributes for the target assembly and module.</span></span> <span data-ttu-id="4b063-113">Les modules et assemblys agissent comme conteneurs physiques pour les types.</span><span class="sxs-lookup"><span data-stu-id="4b063-113">Assemblies and modules act as physical containers for types.</span></span> <span data-ttu-id="4b063-114">Un assembly peut se composer de plusieurs modules distincts.</span><span class="sxs-lookup"><span data-stu-id="4b063-114">An assembly may consist of several physically separate modules.</span></span>

<span data-ttu-id="4b063-115">Le *namespace_member_declaration*s de chaque unité de compilation d’un programme fournissent des membres à un espace de déclaration unique appelée espace de noms global.</span><span class="sxs-lookup"><span data-stu-id="4b063-115">The *namespace_member_declaration*s of each compilation unit of a program contribute members to a single declaration space called the global namespace.</span></span> <span data-ttu-id="4b063-116">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4b063-116">For example:</span></span>

<span data-ttu-id="4b063-117">Fichier `A.cs`:</span><span class="sxs-lookup"><span data-stu-id="4b063-117">File `A.cs`:</span></span>
```csharp
class A {}
```

<span data-ttu-id="4b063-118">Fichier `B.cs`:</span><span class="sxs-lookup"><span data-stu-id="4b063-118">File `B.cs`:</span></span>
```csharp
class B {}
```

<span data-ttu-id="4b063-119">Les unités de deux compilation contribuent à l’espace de noms global unique, dans ce cas déclare deux classes dont les noms qualifiés complets `A` et `B`.</span><span class="sxs-lookup"><span data-stu-id="4b063-119">The two compilation units contribute to the single global namespace, in this case declaring two classes with the fully qualified names `A` and `B`.</span></span> <span data-ttu-id="4b063-120">Étant donné que les unités de deux compilation contribuent au même espace de déclaration, il aurait été une erreur si les deux contenaient une déclaration d’un membre portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="4b063-120">Because the two compilation units contribute to the same declaration space, it would have been an error if each contained a declaration of a member with the same name.</span></span>

## <a name="namespace-declarations"></a><span data-ttu-id="4b063-121">Déclarations d'espaces de noms</span><span class="sxs-lookup"><span data-stu-id="4b063-121">Namespace declarations</span></span>

<span data-ttu-id="4b063-122">Un *namespace_declaration* se compose du mot clé `namespace`, suivie d’un espace de noms et le corps, éventuellement suivie d’un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="4b063-122">A *namespace_declaration* consists of the keyword `namespace`, followed by a namespace name and body, optionally followed by a semicolon.</span></span>

```antlr
namespace_declaration
    : 'namespace' qualified_identifier namespace_body ';'?
    ;

qualified_identifier
    : identifier ('.' identifier)*
    ;

namespace_body
    : '{' extern_alias_directive* using_directive* namespace_member_declaration* '}'
    ;
```

<span data-ttu-id="4b063-123">Un *namespace_declaration* peut se produire dans une déclaration de niveau supérieur dans un *compilation_unit* ou comme une déclaration de membre dans une autre *namespace_declaration*.</span><span class="sxs-lookup"><span data-stu-id="4b063-123">A *namespace_declaration* may occur as a top-level declaration in a *compilation_unit* or as a member declaration within another *namespace_declaration*.</span></span> <span data-ttu-id="4b063-124">Quand un *namespace_declaration* se produit en tant que déclaration de niveau supérieur dans un *compilation_unit*, l’espace de noms devient membre de l’espace de noms global.</span><span class="sxs-lookup"><span data-stu-id="4b063-124">When a *namespace_declaration* occurs as a top-level declaration in a *compilation_unit*, the namespace becomes a member of the global namespace.</span></span> <span data-ttu-id="4b063-125">Quand un *namespace_declaration* se produit dans une autre *namespace_declaration*, l’espace de noms interne devient un membre de l’espace de noms externe.</span><span class="sxs-lookup"><span data-stu-id="4b063-125">When a *namespace_declaration* occurs within another *namespace_declaration*, the inner namespace becomes a member of the outer namespace.</span></span> <span data-ttu-id="4b063-126">Dans les deux cas, le nom d’un espace de noms doit être unique au sein de l’espace de noms qui le contient.</span><span class="sxs-lookup"><span data-stu-id="4b063-126">In either case, the name of a namespace must be unique within the containing namespace.</span></span>

<span data-ttu-id="4b063-127">Espaces de noms sont implicitement `public` et la déclaration d’un espace de noms ne peut pas inclure des modificateurs d’accès.</span><span class="sxs-lookup"><span data-stu-id="4b063-127">Namespaces are implicitly `public` and the declaration of a namespace cannot include any access modifiers.</span></span>

<span data-ttu-id="4b063-128">Au sein d’un *namespace_body*, le paramètre facultatif *using_directive*s importer les noms des autres espaces de noms, les types et membres, ce qui leur permet d’être référencées directement au lieu de par le biais des noms qualifiés.</span><span class="sxs-lookup"><span data-stu-id="4b063-128">Within a *namespace_body*, the optional *using_directive*s import the names of other namespaces, types and members, allowing them to be referenced directly instead of through qualified names.</span></span> <span data-ttu-id="4b063-129">Le paramètre facultatif *namespace_member_declaration*s fournissent des membres à l’espace de déclaration de l’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="4b063-129">The optional *namespace_member_declaration*s contribute members to the declaration space of the namespace.</span></span> <span data-ttu-id="4b063-130">Notez que tous les *using_directive*s doit apparaître avant les déclarations de membre.</span><span class="sxs-lookup"><span data-stu-id="4b063-130">Note that all *using_directive*s must appear before any member declarations.</span></span>

<span data-ttu-id="4b063-131">Le *qualified_identifier* d’un *namespace_declaration* peut être un identificateur unique ou une séquence d’identificateurs séparés par «`.`« jetons.</span><span class="sxs-lookup"><span data-stu-id="4b063-131">The *qualified_identifier* of a *namespace_declaration* may be a single identifier or a sequence of identifiers separated by "`.`" tokens.</span></span> <span data-ttu-id="4b063-132">Cette dernière forme autorise un programme pour définir un espace de noms imbriqué sans imbriquer lexicalement plusieurs déclarations d’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="4b063-132">The latter form permits a program to define a nested namespace without lexically nesting several namespace declarations.</span></span> <span data-ttu-id="4b063-133">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4b063-133">For example,</span></span>

```csharp
namespace N1.N2
{
    class A {}

    class B {}
}
```
<span data-ttu-id="4b063-134">est sémantiquement équivalente à</span><span class="sxs-lookup"><span data-stu-id="4b063-134">is semantically equivalent to</span></span>
```csharp
namespace N1
{
    namespace N2
    {
        class A {}

        class B {}
    }
}
```

<span data-ttu-id="4b063-135">Espaces de noms sont ouverts, et deux déclarations d’espace de noms portant le même nom qualifié complet contribuent au même espace de déclaration ([déclarations](basic-concepts.md#declarations)).</span><span class="sxs-lookup"><span data-stu-id="4b063-135">Namespaces are open-ended, and two namespace declarations with the same fully qualified name contribute to the same declaration space ([Declarations](basic-concepts.md#declarations)).</span></span> <span data-ttu-id="4b063-136">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="4b063-136">In the example</span></span>
```csharp
namespace N1.N2
{
    class A {}
}

namespace N1.N2
{
    class B {}
}
```
<span data-ttu-id="4b063-137">les deux déclarations d’espace de noms contribuent au même espace de déclaration, dans ce cas déclare deux classes dont les noms qualifiés complets `N1.N2.A` et `N1.N2.B`.</span><span class="sxs-lookup"><span data-stu-id="4b063-137">the two namespace declarations above contribute to the same declaration space, in this case declaring two classes with the fully qualified names `N1.N2.A` and `N1.N2.B`.</span></span> <span data-ttu-id="4b063-138">Étant donné que les deux déclarations contribuent au même espace de déclaration, il aurait été une erreur si chaque contenait une déclaration d’un membre portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="4b063-138">Because the two declarations contribute to the same declaration space, it would have been an error if each contained a declaration of a member with the same name.</span></span>

## <a name="extern-aliases"></a><span data-ttu-id="4b063-139">Alias extern</span><span class="sxs-lookup"><span data-stu-id="4b063-139">Extern aliases</span></span>

<span data-ttu-id="4b063-140">Un *extern_alias_directive* présente un identificateur qui sert d’alias pour un espace de noms.</span><span class="sxs-lookup"><span data-stu-id="4b063-140">An *extern_alias_directive* introduces an identifier that serves as an alias for a namespace.</span></span> <span data-ttu-id="4b063-141">La spécification de l’espace de noms d’alias est externe au code source du programme et s’applique également à des espaces de noms imbriqués de l’espace de noms d’alias.</span><span class="sxs-lookup"><span data-stu-id="4b063-141">The specification of the aliased namespace is external to the source code of the program and applies also to nested namespaces of the aliased namespace.</span></span>

```antlr
extern_alias_directive
    : 'extern' 'alias' identifier ';'
    ;
```

<span data-ttu-id="4b063-142">L’étendue d’un *extern_alias_directive* s’étend sur la *using_directive*s, *global_attributes* et *namespace_member_declaration*s de son corps d’unité ou un espace de noms compilation conteneur immédiat.</span><span class="sxs-lookup"><span data-stu-id="4b063-142">The scope of an *extern_alias_directive* extends over the *using_directive*s, *global_attributes* and *namespace_member_declaration*s of its immediately containing compilation unit or namespace body.</span></span>

<span data-ttu-id="4b063-143">Dans un corps de compilation unité ou un espace de noms qui contient un *extern_alias_directive*, l’identificateur introduit par le *extern_alias_directive* peut être utilisé pour référencer l’espace de noms d’alias.</span><span class="sxs-lookup"><span data-stu-id="4b063-143">Within a compilation unit or namespace body that contains an *extern_alias_directive*, the identifier introduced by the *extern_alias_directive* can be used to reference the aliased namespace.</span></span> <span data-ttu-id="4b063-144">Il s’agit d’une erreur lors de la compilation pour le *identificateur* du mot `global`.</span><span class="sxs-lookup"><span data-stu-id="4b063-144">It is a compile-time error for the *identifier* to be the word `global`.</span></span>

<span data-ttu-id="4b063-145">Un *extern_alias_directive* rend un alias disponible dans un corps d’unité ou un espace de noms de compilation particulier, mais il ne contribue pas tous les nouveaux membres à l’espace de déclaration sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="4b063-145">An *extern_alias_directive* makes an alias available within a particular compilation unit or namespace body, but it does not contribute any new members to the underlying declaration space.</span></span> <span data-ttu-id="4b063-146">En d’autres termes, une *extern_alias_directive* n’est pas transitive, mais, au lieu de cela, affecte uniquement la compilation unité ou un espace de noms du corps dans lequel il se produit.</span><span class="sxs-lookup"><span data-stu-id="4b063-146">In other words, an *extern_alias_directive* is not transitive, but, rather, affects only the compilation unit or namespace body in which it occurs.</span></span>

<span data-ttu-id="4b063-147">Le programme suivant déclare et utilise deux alias extern, `X` et `Y`, chacun de qui représentent la racine d’une hiérarchie de l’espace de noms distincts :</span><span class="sxs-lookup"><span data-stu-id="4b063-147">The following program declares and uses two extern aliases, `X` and `Y`, each of which represent the root of a distinct namespace hierarchy:</span></span>
```csharp
extern alias X;
extern alias Y;

class Test
{
    X::N.A a;
    X::N.B b1;
    Y::N.B b2;
    Y::N.C c;
}
```

<span data-ttu-id="4b063-148">Le programme déclare l’existence d’extern alias `X` et `Y`, mais les définitions actuelles des alias sont externes au programme.</span><span class="sxs-lookup"><span data-stu-id="4b063-148">The program declares the existence of the extern aliases `X` and `Y`, but the actual definitions of the aliases are external to the program.</span></span> <span data-ttu-id="4b063-149">Portant le même nom `N.B` classes peuvent désormais être référencées en tant que `X.N.B` et `Y.N.B`, ou à l’aide du qualificateur d’alias d’espace de noms, `X::N.B` et `Y::N.B`.</span><span class="sxs-lookup"><span data-stu-id="4b063-149">The identically named `N.B` classes can now be referenced as `X.N.B` and `Y.N.B`, or, using the namespace alias qualifier, `X::N.B` and `Y::N.B`.</span></span> <span data-ttu-id="4b063-150">Une erreur se produit si un programme déclare un alias extern pour lesquels aucune définition externe n’est fournie.</span><span class="sxs-lookup"><span data-stu-id="4b063-150">An error occurs if a program declares an extern alias for which no external definition is provided.</span></span>

## <a name="using-directives"></a><span data-ttu-id="4b063-151">directives using</span><span class="sxs-lookup"><span data-stu-id="4b063-151">Using directives</span></span>

<span data-ttu-id="4b063-152">***À l’aide de directives*** facilitent l’utilisation des espaces de noms et les types définis dans d’autres espaces de noms.</span><span class="sxs-lookup"><span data-stu-id="4b063-152">***Using directives*** facilitate the use of namespaces and types defined in other namespaces.</span></span> <span data-ttu-id="4b063-153">À l’aide d’impact sur les directives du processus de résolution de noms de *namespace_or_type_name*s ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)) et *simple_name*s ([noms simples ](expressions.md#simple-names)), mais contrairement aux déclarations, à l’aide de directives ne contribuent pas de nouveaux membres pour les espaces de déclaration sous-jacente des unités de compilation ou espaces de noms dans lequel ils sont utilisés.</span><span class="sxs-lookup"><span data-stu-id="4b063-153">Using directives impact the name resolution process of *namespace_or_type_name*s ([Namespace and type names](basic-concepts.md#namespace-and-type-names)) and *simple_name*s ([Simple names](expressions.md#simple-names)), but unlike declarations, using directives do not contribute new members to the underlying declaration spaces of the compilation units or namespaces within which they are used.</span></span>

```antlr
using_directive
    : using_alias_directive
    | using_namespace_directive
    | using_static_directive
    ;
```

<span data-ttu-id="4b063-154">Un *using_alias_directive* ([à l’aide de directives d’alias](namespaces.md#using-alias-directives)) introduit un alias pour un espace de noms ou un type.</span><span class="sxs-lookup"><span data-stu-id="4b063-154">A *using_alias_directive* ([Using alias directives](namespaces.md#using-alias-directives)) introduces an alias for a namespace or type.</span></span>

<span data-ttu-id="4b063-155">Un *using_namespace_directive* ([à l’aide de directives d’espace de noms](namespaces.md#using-namespace-directives)) importe les membres de type d’un espace de noms.</span><span class="sxs-lookup"><span data-stu-id="4b063-155">A *using_namespace_directive* ([Using namespace directives](namespaces.md#using-namespace-directives)) imports the type members of a namespace.</span></span>

<span data-ttu-id="4b063-156">Un *using_static_directive* ([directives Using static](namespaces.md#using-static-directives)) importe les types imbriqués et les membres statiques d’un type.</span><span class="sxs-lookup"><span data-stu-id="4b063-156">A *using_static_directive* ([Using static directives](namespaces.md#using-static-directives)) imports the nested types and static members of a type.</span></span>

<span data-ttu-id="4b063-157">L’étendue d’un *using_directive* s’étend sur la *namespace_member_declaration*s de son corps d’unité ou un espace de noms compilation conteneur immédiat.</span><span class="sxs-lookup"><span data-stu-id="4b063-157">The scope of a *using_directive* extends over the *namespace_member_declaration*s of its immediately containing compilation unit or namespace body.</span></span> <span data-ttu-id="4b063-158">L’étendue d’un *using_directive* n’inclut pas spécifiquement son homologue *using_directive*s.</span><span class="sxs-lookup"><span data-stu-id="4b063-158">The scope of a *using_directive* specifically does not include its peer *using_directive*s.</span></span> <span data-ttu-id="4b063-159">Par conséquent, de l’homologue *using_directive*s n’affectent pas les uns des autres, et l’ordre dans lequel ils sont écrits n’est pas significatif.</span><span class="sxs-lookup"><span data-stu-id="4b063-159">Thus, peer *using_directive*s do not affect each other, and the order in which they are written is insignificant.</span></span>

### <a name="using-alias-directives"></a><span data-ttu-id="4b063-160">À l’aide de directives d’alias</span><span class="sxs-lookup"><span data-stu-id="4b063-160">Using alias directives</span></span>

<span data-ttu-id="4b063-161">Un *using_alias_directive* présente un identificateur qui sert d’alias pour un espace de noms ou un type dans le corps d’unité ou un espace de noms compilation immédiatement englobant.</span><span class="sxs-lookup"><span data-stu-id="4b063-161">A *using_alias_directive* introduces an identifier that serves as an alias for a namespace or type within the immediately enclosing compilation unit or namespace body.</span></span>

```antlr
using_alias_directive
    : 'using' identifier '=' namespace_or_type_name ';'
    ;
```

<span data-ttu-id="4b063-162">Dans les déclarations de membre d’un corps de compilation unité ou un espace de noms qui contient un *using_alias_directive*, l’identificateur introduit par le *using_alias_directive* peut être utilisé pour référencer le donné espace de noms ou type.</span><span class="sxs-lookup"><span data-stu-id="4b063-162">Within member declarations in a compilation unit or namespace body that contains a *using_alias_directive*, the identifier introduced by the *using_alias_directive* can be used to reference the given namespace or type.</span></span> <span data-ttu-id="4b063-163">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4b063-163">For example:</span></span>
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using A = N1.N2.A;

    class B: A {}
}
```

<span data-ttu-id="4b063-164">Ci-dessus, dans les déclarations de membre dans le `N3` espace de noms, `A` est un alias pour `N1.N2.A`et par conséquent, la classe `N3.B` dérive de la classe `N1.N2.A`.</span><span class="sxs-lookup"><span data-stu-id="4b063-164">Above, within member declarations in the `N3` namespace, `A` is an alias for `N1.N2.A`, and thus class `N3.B` derives from class `N1.N2.A`.</span></span> <span data-ttu-id="4b063-165">Le même effet peut être obtenu en créant un alias `R` pour `N1.N2` et y faire référence `R.A`:</span><span class="sxs-lookup"><span data-stu-id="4b063-165">The same effect can be obtained by creating an alias `R` for `N1.N2` and then referencing `R.A`:</span></span>
```csharp
namespace N3
{
    using R = N1.N2;

    class B: R.A {}
}
```

<span data-ttu-id="4b063-166">Le *identificateur* d’un *using_alias_directive* doit être unique dans l’espace de déclaration de l’unité de compilation ou d’un espace de noms qui contient le *using_alias_directive* .</span><span class="sxs-lookup"><span data-stu-id="4b063-166">The *identifier* of a *using_alias_directive* must be unique within the declaration space of the compilation unit or namespace that immediately contains the *using_alias_directive*.</span></span> <span data-ttu-id="4b063-167">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4b063-167">For example:</span></span>
```csharp
namespace N3
{
    class A {}
}

namespace N3
{
    using A = N1.N2.A;        // Error, A already exists
}
```

<span data-ttu-id="4b063-168">Ci-dessus, `N3` contient déjà un membre `A`, de sorte que c’est une erreur de compilation pour un *using_alias_directive* à utiliser cet identificateur.</span><span class="sxs-lookup"><span data-stu-id="4b063-168">Above, `N3` already contains a member `A`, so it is a compile-time error for a *using_alias_directive* to use that identifier.</span></span> <span data-ttu-id="4b063-169">De même, il est une erreur de compilation pour au moins deux *using_alias_directive*s dans la même compilation unité ou un espace de noms corps pour déclarer des alias par le même nom.</span><span class="sxs-lookup"><span data-stu-id="4b063-169">Likewise, it is a compile-time error for two or more *using_alias_directive*s in the same compilation unit or namespace body to declare aliases by the same name.</span></span>

<span data-ttu-id="4b063-170">Un *using_alias_directive* rend un alias disponible dans un corps d’unité ou un espace de noms de compilation particulier, mais il ne contribue pas tous les nouveaux membres à l’espace de déclaration sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="4b063-170">A *using_alias_directive* makes an alias available within a particular compilation unit or namespace body, but it does not contribute any new members to the underlying declaration space.</span></span> <span data-ttu-id="4b063-171">En d’autres termes, une *using_alias_directive* n’est pas transitive, mais au lieu de cela affecte uniquement la compilation unité ou un espace de noms du corps dans lequel il se produit.</span><span class="sxs-lookup"><span data-stu-id="4b063-171">In other words, a *using_alias_directive* is not transitive but rather affects only the compilation unit or namespace body in which it occurs.</span></span> <span data-ttu-id="4b063-172">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="4b063-172">In the example</span></span>
```csharp
namespace N3
{
    using R = N1.N2;
}

namespace N3
{
    class B: R.A {}            // Error, R unknown
}
```
<span data-ttu-id="4b063-173">l’étendue de la *using_alias_directive* qui introduit `R` s’étend uniquement aux déclarations de membre dans le corps de l’espace de noms dans lequel elle est contenue, donc `R` est inconnu dans la seconde déclaration d’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="4b063-173">the scope of the *using_alias_directive* that introduces `R` only extends to member declarations in the namespace body in which it is contained, so `R` is unknown in the second namespace declaration.</span></span> <span data-ttu-id="4b063-174">Toutefois, en plaçant le *using_alias_directive* dans la compilation conteneur unité entraîne l’alias devient disponible dans les deux déclarations d’espace de noms :</span><span class="sxs-lookup"><span data-stu-id="4b063-174">However, placing the *using_alias_directive* in the containing compilation unit causes the alias to become available within both namespace declarations:</span></span>
```csharp
using R = N1.N2;

namespace N3
{
    class B: R.A {}
}

namespace N3
{
    class C: R.A {}
}
```

<span data-ttu-id="4b063-175">Tout comme les membres standard, les noms introduits par *using_alias_directive*s sont masqués par des membres portant le même nom dans les portées imbriquées.</span><span class="sxs-lookup"><span data-stu-id="4b063-175">Just like regular members, names introduced by *using_alias_directive*s are hidden by similarly named members in nested scopes.</span></span> <span data-ttu-id="4b063-176">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="4b063-176">In the example</span></span>
```csharp
using R = N1.N2;

namespace N3
{
    class R {}

    class B: R.A {}        // Error, R has no member A
}
```
<span data-ttu-id="4b063-177">la référence à `R.A` dans la déclaration de `B` provoque une erreur de compilation, car `R` fait référence à `N3.R`, et non `N1.N2`.</span><span class="sxs-lookup"><span data-stu-id="4b063-177">the reference to `R.A` in the declaration of `B` causes a compile-time error because `R` refers to `N3.R`, not `N1.N2`.</span></span>

<span data-ttu-id="4b063-178">L’ordre dans lequel *using_alias_directive*s sont écrits n’a aucune signification et la résolution de la *namespace_or_type_name* référencé par un *using_alias_directive*n’est pas affectée par le *using_alias_directive* lui-même ou par d’autres *using_directive*s dans le corps d’unité ou un espace de noms compilation conteneur immédiat.</span><span class="sxs-lookup"><span data-stu-id="4b063-178">The order in which *using_alias_directive*s are written has no significance, and resolution of the *namespace_or_type_name* referenced by a *using_alias_directive* is not affected by the *using_alias_directive* itself or by other *using_directive*s in the immediately containing compilation unit or namespace body.</span></span> <span data-ttu-id="4b063-179">En d’autres termes, le *namespace_or_type_name* d’un *using_alias_directive* est résolue comme si le corps d’unité ou un espace de noms conteneur immédiat compilation n’avait pas *using_directive*s.</span><span class="sxs-lookup"><span data-stu-id="4b063-179">In other words, the *namespace_or_type_name* of a *using_alias_directive* is resolved as if the immediately containing compilation unit or namespace body had no *using_directive*s.</span></span> <span data-ttu-id="4b063-180">Un *using_alias_directive* peut toutefois être affecté par *extern_alias_directive*s dans le corps d’unité ou un espace de noms compilation conteneur immédiat.</span><span class="sxs-lookup"><span data-stu-id="4b063-180">A *using_alias_directive* may however be affected by *extern_alias_directive*s in the immediately containing compilation unit or namespace body.</span></span> <span data-ttu-id="4b063-181">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="4b063-181">In the example</span></span>
```csharp
namespace N1.N2 {}

namespace N3
{
    extern alias E;

    using R1 = E.N;        // OK

    using R2 = N1;         // OK

    using R3 = N1.N2;      // OK

    using R4 = R2.N2;      // Error, R2 unknown
}
```
<span data-ttu-id="4b063-182">la dernière *using_alias_directive* entraîne une erreur de compilation, car il n’est pas affecté par la première *using_alias_directive*.</span><span class="sxs-lookup"><span data-stu-id="4b063-182">the last *using_alias_directive* results in a compile-time error because it is not affected by the first *using_alias_directive*.</span></span> <span data-ttu-id="4b063-183">La première *using_alias_directive* n’entraîne pas une erreur depuis l’étendue de l’alias extern `E` inclut le *using_alias_directive*.</span><span class="sxs-lookup"><span data-stu-id="4b063-183">The first *using_alias_directive* does not result in an error since the scope of the extern alias `E` includes the *using_alias_directive*.</span></span>

<span data-ttu-id="4b063-184">Un *using_alias_directive* pouvez créer un alias pour n’importe quel espace de noms ou un type, y compris l’espace de noms dans lequel il apparaît et tout espace de noms ou un type imbriqué dans cet espace de noms.</span><span class="sxs-lookup"><span data-stu-id="4b063-184">A *using_alias_directive* can create an alias for any namespace or type, including the namespace within which it appears and any namespace or type nested within that namespace.</span></span>

<span data-ttu-id="4b063-185">L’accès d’un espace de noms ou un type via un alias produit exactement le même résultat que l’accès à cet espace de noms ou d’un type par son nom déclaré.</span><span class="sxs-lookup"><span data-stu-id="4b063-185">Accessing a namespace or type through an alias yields exactly the same result as accessing that namespace or type through its declared name.</span></span> <span data-ttu-id="4b063-186">Par exemple, étant donné</span><span class="sxs-lookup"><span data-stu-id="4b063-186">For example, given</span></span>
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using R1 = N1;
    using R2 = N1.N2;

    class B
    {
        N1.N2.A a;            // refers to N1.N2.A
        R1.N2.A b;            // refers to N1.N2.A
        R2.A c;               // refers to N1.N2.A
    }
}
```
<span data-ttu-id="4b063-187">les noms `N1.N2.A`, `R1.N2.A`, et `R2.A` sont équivalent et tous font référence à la classe dont le nom qualifié complet est `N1.N2.A`.</span><span class="sxs-lookup"><span data-stu-id="4b063-187">the names `N1.N2.A`, `R1.N2.A`, and `R2.A` are equivalent and all refer to the class whose fully qualified name is `N1.N2.A`.</span></span>

<span data-ttu-id="4b063-188">L’utilisation d’alias peut le nom d’un type construit fermé, mais ne peut pas de nom d’une déclaration de type générique indépendant sans fournir d’arguments de type.</span><span class="sxs-lookup"><span data-stu-id="4b063-188">Using aliases can name a closed constructed type, but cannot name an unbound generic type declaration without supplying type arguments.</span></span> <span data-ttu-id="4b063-189">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4b063-189">For example:</span></span>
```csharp
namespace N1
{
    class A<T>
    {
        class B {}
    }
}

namespace N2
{
    using W = N1.A;          // Error, cannot name unbound generic type

    using X = N1.A.B;        // Error, cannot name unbound generic type

    using Y = N1.A<int>;     // Ok, can name closed constructed type

    using Z<T> = N1.A<T>;    // Error, using alias cannot have type parameters
}
```

### <a name="using-namespace-directives"></a><span data-ttu-id="4b063-190">À l’aide de directives d’espace de noms</span><span class="sxs-lookup"><span data-stu-id="4b063-190">Using namespace directives</span></span>

<span data-ttu-id="4b063-191">Un *using_namespace_directive* importe les types contenus dans un espace de noms dans l’immédiatement englobante compilation unité ou un espace de noms corps, l’activation de l’identificateur de chaque type à être utilisé sans qualification.</span><span class="sxs-lookup"><span data-stu-id="4b063-191">A *using_namespace_directive* imports the types contained in a namespace into the immediately enclosing compilation unit or namespace body, enabling the identifier of each type to be used without qualification.</span></span>

```antlr
using_namespace_directive
    : 'using' namespace_name ';'
    ;
```

<span data-ttu-id="4b063-192">Dans les déclarations de membre d’un corps de compilation unité ou un espace de noms qui contient un *using_namespace_directive*, les types contenus dans l’espace de noms donné peuvent être référencés directement.</span><span class="sxs-lookup"><span data-stu-id="4b063-192">Within member declarations in a compilation unit or namespace body that contains a *using_namespace_directive*, the types contained in the given namespace can be referenced directly.</span></span> <span data-ttu-id="4b063-193">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4b063-193">For example:</span></span>
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using N1.N2;

    class B: A {}
}
```

<span data-ttu-id="4b063-194">Ci-dessus, dans les déclarations de membre dans le `N3` espace de noms, les membres de type de `N1.N2` sont directement disponibles et par conséquent, la classe `N3.B` dérive de la classe `N1.N2.A`.</span><span class="sxs-lookup"><span data-stu-id="4b063-194">Above, within member declarations in the `N3` namespace, the type members of `N1.N2` are directly available, and thus class `N3.B` derives from class `N1.N2.A`.</span></span>

<span data-ttu-id="4b063-195">Un *using_namespace_directive* importe les types contenus dans l’espace de noms donné, mais plus précisément n’importe pas les espaces de noms imbriqués.</span><span class="sxs-lookup"><span data-stu-id="4b063-195">A *using_namespace_directive* imports the types contained in the given namespace, but specifically does not import nested namespaces.</span></span> <span data-ttu-id="4b063-196">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="4b063-196">In the example</span></span>
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using N1;

    class B: N2.A {}        // Error, N2 unknown
}
```
<span data-ttu-id="4b063-197">le *using_namespace_directive* importe les types contenus dans `N1`, mais pas les espaces de noms imbriqués dans `N1`.</span><span class="sxs-lookup"><span data-stu-id="4b063-197">the *using_namespace_directive* imports the types contained in `N1`, but not the namespaces nested in `N1`.</span></span> <span data-ttu-id="4b063-198">Par conséquent, la référence à `N2.A` dans la déclaration de `B` génère une erreur de compilation, car aucun membre ne nommé `N2` sont dans la portée.</span><span class="sxs-lookup"><span data-stu-id="4b063-198">Thus, the reference to `N2.A` in the declaration of `B` results in a compile-time error because no members named `N2` are in scope.</span></span>

<span data-ttu-id="4b063-199">Contrairement à un *using_alias_directive*, un *using_namespace_directive* peut importer les types dont les identificateurs sont déjà définis dans le corps d’unité ou un espace de noms englobant compilation.</span><span class="sxs-lookup"><span data-stu-id="4b063-199">Unlike a *using_alias_directive*, a *using_namespace_directive* may import types whose identifiers are already defined within the enclosing compilation unit or namespace body.</span></span> <span data-ttu-id="4b063-200">En effet, les noms importés par un *using_namespace_directive* sont masqués par des membres portant le même nom dans le corps d’unité ou un espace de noms englobant compilation.</span><span class="sxs-lookup"><span data-stu-id="4b063-200">In effect, names imported by a *using_namespace_directive* are hidden by similarly named members in the enclosing compilation unit or namespace body.</span></span> <span data-ttu-id="4b063-201">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4b063-201">For example:</span></span>
```csharp
namespace N1.N2
{
    class A {}

    class B {}
}

namespace N3
{
    using N1.N2;

    class A {}
}
```

<span data-ttu-id="4b063-202">Ici, dans les déclarations de membre dans le `N3` espace de noms, `A` fait référence à `N3.A` plutôt que `N1.N2.A`.</span><span class="sxs-lookup"><span data-stu-id="4b063-202">Here, within member declarations in the `N3` namespace, `A` refers to `N3.A` rather than `N1.N2.A`.</span></span>

<span data-ttu-id="4b063-203">Lorsque plus d’un espace de noms ou le type importé par *using_namespace_directive*s ou *using_static_directive*s dans le même corps de compilation unité ou un espace de noms contiennent des types portant le même nom, les références à Ce nom comme un *type_name* sont considérés comme ambiguë.</span><span class="sxs-lookup"><span data-stu-id="4b063-203">When more than one namespace or type imported by *using_namespace_directive*s or *using_static_directive*s in the same compilation unit or namespace body contain types by the same name, references to that name as a *type_name* are considered ambiguous.</span></span> <span data-ttu-id="4b063-204">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="4b063-204">In the example</span></span>
```csharp
namespace N1
{
    class A {}
}

namespace N2
{
    class A {}
}

namespace N3
{
    using N1;

    using N2;

    class B: A {}                // Error, A is ambiguous
}
```
<span data-ttu-id="4b063-205">les deux `N1` et `N2` contiennent un membre `A`et parce que `N3` importe les deux, faisant référence à `A` dans `N3` est une erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="4b063-205">both `N1` and `N2` contain a member `A`, and because `N3` imports both, referencing `A` in `N3` is a compile-time error.</span></span> <span data-ttu-id="4b063-206">Dans ce cas, le conflit peut être résolu par la qualification des références à `A`, ou en introduisant un *using_alias_directive* qui sélectionne un particulier `A`.</span><span class="sxs-lookup"><span data-stu-id="4b063-206">In this situation, the conflict can be resolved either through qualification of references to `A`, or by introducing a *using_alias_directive* that picks a particular `A`.</span></span> <span data-ttu-id="4b063-207">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4b063-207">For example:</span></span>
```csharp
namespace N3
{
    using N1;

    using N2;

    using A = N1.A;

    class B: A {}                // A means N1.A
}
```

<span data-ttu-id="4b063-208">En outre, lorsque plus d’un espace de noms ou le type importé par *using_namespace_directive*s ou *using_static_directive*s dans le même corps de compilation unité ou un espace de noms contiennent des types ou membres par le même nom, fait référence à ce nom comme un *simple_name* sont considérés comme ambiguë.</span><span class="sxs-lookup"><span data-stu-id="4b063-208">Furthermore, when more than one namespace or type imported by *using_namespace_directive*s or *using_static_directive*s in the same compilation unit or namespace body contain types or members by the same name, references to that name as a *simple_name* are considered ambiguous.</span></span> <span data-ttu-id="4b063-209">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="4b063-209">In the example</span></span>
```csharp
namespace N1
{
    class A {}
}

class C
{
    public static int A;
}

namespace N2
{
    using N1;
    using static C;

    class B
    {
        void M() 
        { 
            A a = new A();   // Ok, A is unambiguous as a type-name
            A.Equals(2);     // Error, A is ambiguous as a simple-name
        }
    }
}
```
<span data-ttu-id="4b063-210">`N1` contient un membre de type `A`, et `C` contient une méthode statique `A`et parce que `N2` importe les deux, faisant référence à `A` comme un *simple_name* est ambigu et un moment de la compilation erreur.</span><span class="sxs-lookup"><span data-stu-id="4b063-210">`N1` contains a type member `A`, and `C` contains a static method `A`, and because `N2` imports both, referencing `A` as a *simple_name* is ambiguous and a compile-time error.</span></span> 

<span data-ttu-id="4b063-211">Comme un *using_alias_directive*, un *using_namespace_directive* ne contribue pas tous les nouveaux membres à l’espace de déclaration sous-jacent de l’unité de compilation ou d’un espace de noms, mais affecte uniquement le compilation unité corps ou d’espace de noms dans lequel elle apparaît.</span><span class="sxs-lookup"><span data-stu-id="4b063-211">Like a *using_alias_directive*, a *using_namespace_directive* does not contribute any new members to the underlying declaration space of the compilation unit or namespace, but rather affects only the compilation unit or namespace body in which it appears.</span></span>

<span data-ttu-id="4b063-212">Le *nom_espace_de_noms* référencé par un *using_namespace_directive* est résolu dans la même façon que le *namespace_or_type_name* référencé par un  *using_alias_directive*.</span><span class="sxs-lookup"><span data-stu-id="4b063-212">The *namespace_name* referenced by a *using_namespace_directive* is resolved in the same way as the *namespace_or_type_name* referenced by a *using_alias_directive*.</span></span> <span data-ttu-id="4b063-213">Par conséquent, *using_namespace_directive*s dans le même corps d’unité ou un espace de noms de compilation n’affectent pas les uns des autres et peuvent être écrites dans n’importe quel ordre.</span><span class="sxs-lookup"><span data-stu-id="4b063-213">Thus, *using_namespace_directive*s in the same compilation unit or namespace body do not affect each other and can be written in any order.</span></span>

### <a name="using-static-directives"></a><span data-ttu-id="4b063-214">Directives using static</span><span class="sxs-lookup"><span data-stu-id="4b063-214">Using static directives</span></span>

<span data-ttu-id="4b063-215">Un *using_static_directive* importe les types imbriqués et les membres statiques contenus directement dans une déclaration de type dans l’immédiatement englobante compilation unité ou un espace de noms corps, l’activation de l’identificateur de chaque membre et le type à être utilisé sans qualification.</span><span class="sxs-lookup"><span data-stu-id="4b063-215">A *using_static_directive* imports the nested types and static members contained directly in a type declaration into the immediately enclosing compilation unit or namespace body, enabling the identifier of each member and type to be used without qualification.</span></span>

```antlr
using_static_directive
    : 'using' 'static' type_name ';'
    ;
```

<span data-ttu-id="4b063-216">Dans les déclarations de membre d’un corps de compilation unité ou un espace de noms qui contient un *using_static_directive*, l’accessibles imbriqué types et membres statiques (à l’exception des méthodes d’extension) contenus directement dans la déclaration de la type donné peut être référencé directement.</span><span class="sxs-lookup"><span data-stu-id="4b063-216">Within member declarations in a compilation unit or namespace body that contains a *using_static_directive*, the accessible nested types and static members (except extension methods) contained directly in the declaration of the given type can be referenced directly.</span></span> <span data-ttu-id="4b063-217">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4b063-217">For example:</span></span>

```csharp
namespace N1
{
    class A 
    {
        public class B{}
        public static B M(){ return new B(); }
    }
}

namespace N2
{
    using static N1.A;
    class C
    {
        void N() { B b = M(); }
    }
}
```

<span data-ttu-id="4b063-218">Ci-dessus, dans les déclarations de membre dans le `N2` espace de noms, les membres statiques et les types imbriqués de `N1.A` sont directement disponibles et par conséquent, la méthode `N` est en mesure de faire référence à la fois le `B` et `M` membres de `N1.A`.</span><span class="sxs-lookup"><span data-stu-id="4b063-218">Above, within member declarations in the `N2` namespace, the static members and nested types of `N1.A` are directly available, and thus the method `N` is able to reference both the `B` and `M` members of `N1.A`.</span></span>

<span data-ttu-id="4b063-219">Un *using_static_directive* spécifiquement n’importe pas les méthodes d’extension directement en tant que méthodes statiques, mais les rend disponibles pour l’appel de méthode d’extension ([appels de méthode d’Extension](expressions.md#extension-method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="4b063-219">A *using_static_directive* specifically does not import extension methods directly as static methods, but makes them available for extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)).</span></span> <span data-ttu-id="4b063-220">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="4b063-220">In the example</span></span>

```csharp
namespace N1 
{
    static class A 
    {
        public static void M(this string s){}
    }
}

namespace N2
{
    using static N1.A;

    class B
    {
        void N() 
        {
            M("A");      // Error, M unknown
            "B".M();     // Ok, M known as extension method
            N1.A.M("C"); // Ok, fully qualified
        }
    }
}
```
<span data-ttu-id="4b063-221">le *using_static_directive* importe la méthode d’extension `M` contenue dans `N1.A`, mais uniquement comme une méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="4b063-221">the *using_static_directive* imports the extension method `M` contained in `N1.A`, but only as an extension method.</span></span> <span data-ttu-id="4b063-222">Par conséquent, la première référence à `M` dans le corps de `B.N` génère une erreur de compilation, car aucun membre ne nommé `M` sont dans la portée.</span><span class="sxs-lookup"><span data-stu-id="4b063-222">Thus, the first reference to `M` in the body of `B.N` results in a compile-time error because no members named `M` are in scope.</span></span>

<span data-ttu-id="4b063-223">Un *using_static_directive* importe uniquement les membres et types déclarés directement dans le type donné, pas les types et les membres déclarés dans les classes de base.</span><span class="sxs-lookup"><span data-stu-id="4b063-223">A *using_static_directive* only imports members and types declared directly in the given type, not members and types declared in base classes.</span></span>

<span data-ttu-id="4b063-224">TODO: Exemple</span><span class="sxs-lookup"><span data-stu-id="4b063-224">TODO: Example</span></span>

<span data-ttu-id="4b063-225">Ambiguïtés entre plusieurs *using_namespace_directives* et *using_static_directives* sont abordées dans [à l’aide de directives d’espace de noms](namespaces.md#using-namespace-directives).</span><span class="sxs-lookup"><span data-stu-id="4b063-225">Ambiguities between multiple *using_namespace_directives* and *using_static_directives* are discussed in [Using namespace directives](namespaces.md#using-namespace-directives).</span></span>

## <a name="namespace-members"></a><span data-ttu-id="4b063-226">Membres de Namespace</span><span class="sxs-lookup"><span data-stu-id="4b063-226">Namespace members</span></span>

<span data-ttu-id="4b063-227">Un *namespace_member_declaration* est soit un *namespace_declaration* ([Namespace déclarations](namespaces.md#namespace-declarations)) ou un *type_declaration* () [Les déclarations de type](namespaces.md#type-declarations)).</span><span class="sxs-lookup"><span data-stu-id="4b063-227">A *namespace_member_declaration* is either a *namespace_declaration* ([Namespace declarations](namespaces.md#namespace-declarations)) or a *type_declaration* ([Type declarations](namespaces.md#type-declarations)).</span></span>

```antlr
namespace_member_declaration
    : namespace_declaration
    | type_declaration
    ;
```

<span data-ttu-id="4b063-228">Une unité de compilation ou un corps de l’espace de noms peut contenir *namespace_member_declaration*s et ces déclarations contribueront de nouveaux membres à l’espace de déclaration sous-jacent contenant compilation unité ou un espace de noms du corps de la.</span><span class="sxs-lookup"><span data-stu-id="4b063-228">A compilation unit or a namespace body can contain *namespace_member_declaration*s, and such declarations contribute new members to the underlying declaration space of the containing compilation unit or namespace body.</span></span>

## <a name="type-declarations"></a><span data-ttu-id="4b063-229">Déclarations de type</span><span class="sxs-lookup"><span data-stu-id="4b063-229">Type declarations</span></span>

<span data-ttu-id="4b063-230">Un *type_declaration* est un *class_declaration* ([déclarations de classe](classes.md#class-declarations)), un *struct_declaration* ([Struct déclarations](structs.md#struct-declarations)), un *interface_declaration* ([déclarations d’Interface](interfaces.md#interface-declarations)), un *enum_declaration* ([Enum déclarations](enums.md#enum-declarations)), ou un *delegate_declaration* ([déclarations Delegate](delegates.md#delegate-declarations)).</span><span class="sxs-lookup"><span data-stu-id="4b063-230">A *type_declaration* is a *class_declaration* ([Class declarations](classes.md#class-declarations)), a *struct_declaration* ([Struct declarations](structs.md#struct-declarations)), an *interface_declaration* ([Interface declarations](interfaces.md#interface-declarations)), an *enum_declaration* ([Enum declarations](enums.md#enum-declarations)), or a *delegate_declaration* ([Delegate declarations](delegates.md#delegate-declarations)).</span></span>

```antlr
type_declaration
    : class_declaration
    | struct_declaration
    | interface_declaration
    | enum_declaration
    | delegate_declaration
    ;
```

<span data-ttu-id="4b063-231">Un *type_declaration* peut se produire sous la forme d’une déclaration de niveau supérieur dans une unité de compilation ou une déclaration de membre dans un espace de noms, une classe ou un struct.</span><span class="sxs-lookup"><span data-stu-id="4b063-231">A *type_declaration* can occur as a top-level declaration in a compilation unit or as a member declaration within a namespace, class, or struct.</span></span>

<span data-ttu-id="4b063-232">Lorsqu’une déclaration de type pour un type `T` se produit en tant que déclaration de niveau supérieur dans une unité de compilation, le nom qualifié complet du type qui vient d’être déclaré est simplement `T`.</span><span class="sxs-lookup"><span data-stu-id="4b063-232">When a type declaration for a type `T` occurs as a top-level declaration in a compilation unit, the fully qualified name of the newly declared type is simply `T`.</span></span> <span data-ttu-id="4b063-233">Lorsqu’une déclaration de type pour un type `T` se produit dans un espace de noms, classe ou struct, le nom qualifié complet du type qui vient d’être déclaré est `N.T`, où `N` est le nom qualifié complet de l’espace de noms, classe ou struct qui le contient.</span><span class="sxs-lookup"><span data-stu-id="4b063-233">When a type declaration for a type `T` occurs within a namespace, class, or struct, the fully qualified name of the newly declared type is `N.T`, where `N` is the fully qualified name of the containing namespace, class, or struct.</span></span>

<span data-ttu-id="4b063-234">Un type déclaré dans une classe ou struct est appelé type imbriqué ([les types imbriqués](classes.md#nested-types)).</span><span class="sxs-lookup"><span data-stu-id="4b063-234">A type declared within a class or struct is called a nested type ([Nested types](classes.md#nested-types)).</span></span>

<span data-ttu-id="4b063-235">Les modificateurs d’accès autorisé et l’accès par défaut pour une déclaration de type dépendent du contexte dans lequel la déclaration a lieu ([accessibilité déclarée](basic-concepts.md#declared-accessibility)) :</span><span class="sxs-lookup"><span data-stu-id="4b063-235">The permitted access modifiers and the default access for a type declaration depend on the context in which the declaration takes place ([Declared accessibility](basic-concepts.md#declared-accessibility)):</span></span>

*  <span data-ttu-id="4b063-236">Les types déclarés dans les unités de compilation ou d’espaces de noms peuvent avoir `public` ou `internal` accès.</span><span class="sxs-lookup"><span data-stu-id="4b063-236">Types declared in compilation units or namespaces can have `public` or `internal` access.</span></span> <span data-ttu-id="4b063-237">La valeur par défaut est `internal` accès.</span><span class="sxs-lookup"><span data-stu-id="4b063-237">The default is `internal` access.</span></span>
*  <span data-ttu-id="4b063-238">Les types déclarés dans les classes peuvent avoir `public`, `protected internal`, `protected`, `internal`, ou `private` accès.</span><span class="sxs-lookup"><span data-stu-id="4b063-238">Types declared in classes can have `public`, `protected internal`, `protected`, `internal`, or `private` access.</span></span> <span data-ttu-id="4b063-239">La valeur par défaut est `private` accès.</span><span class="sxs-lookup"><span data-stu-id="4b063-239">The default is `private` access.</span></span>
*  <span data-ttu-id="4b063-240">Les types déclarés dans les structures peuvent avoir `public`, `internal`, ou `private` accès.</span><span class="sxs-lookup"><span data-stu-id="4b063-240">Types declared in structs can have `public`, `internal`, or `private` access.</span></span> <span data-ttu-id="4b063-241">La valeur par défaut est `private` accès.</span><span class="sxs-lookup"><span data-stu-id="4b063-241">The default is `private` access.</span></span>

## <a name="namespace-alias-qualifiers"></a><span data-ttu-id="4b063-242">Qualificateurs d’alias Namespace</span><span class="sxs-lookup"><span data-stu-id="4b063-242">Namespace alias qualifiers</span></span>

<span data-ttu-id="4b063-243">Le ***qualificateur d’alias d’espace de noms*** `::` rend possible garantir que les recherches de nom de type ne sont pas affectés par l’introduction de nouveaux types et membres.</span><span class="sxs-lookup"><span data-stu-id="4b063-243">The ***namespace alias qualifier*** `::` makes it possible to guarantee that type name lookups are unaffected by the introduction of new types and members.</span></span> <span data-ttu-id="4b063-244">Le qualificateur d’alias d’espace de noms apparaît toujours entre deux identificateurs sont appelés les identificateurs de gauche et droite.</span><span class="sxs-lookup"><span data-stu-id="4b063-244">The namespace alias qualifier always appears between two identifiers referred to as the left-hand and right-hand identifiers.</span></span> <span data-ttu-id="4b063-245">Contrairement à la mise à jour `.` qualificateur, l’identificateur de gauche de la `::` qualificateur s’effectue la recherche à distance uniquement comme un extern ou à l’aide d’alias.</span><span class="sxs-lookup"><span data-stu-id="4b063-245">Unlike the regular `.` qualifier, the left-hand identifier of the `::` qualifier is looked up only as an extern or using alias.</span></span>

<span data-ttu-id="4b063-246">Un *qualified_alias_member* est défini comme suit :</span><span class="sxs-lookup"><span data-stu-id="4b063-246">A *qualified_alias_member* is defined as follows:</span></span>

```antlr
qualified_alias_member
    : identifier '::' identifier type_argument_list?
    ;
```

<span data-ttu-id="4b063-247">Un *qualified_alias_member* peut être utilisé comme un *namespace_or_type_name* ([Namespace noms et type](basic-concepts.md#namespace-and-type-names)) ou en tant que l’opérande gauche dans un *member_access* ([L’accès au membre](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="4b063-247">A *qualified_alias_member* can be used as a *namespace_or_type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)) or as the left operand in a *member_access* ([Member access](expressions.md#member-access)).</span></span>

<span data-ttu-id="4b063-248">Un *qualified_alias_member* a une des deux formes :</span><span class="sxs-lookup"><span data-stu-id="4b063-248">A *qualified_alias_member* has one of two forms:</span></span>

*  <span data-ttu-id="4b063-249">`N::I<A1, ..., Ak>`, où `N` et `I` représentent des identificateurs, et `<A1, ..., Ak>` est une liste d’arguments de type.</span><span class="sxs-lookup"><span data-stu-id="4b063-249">`N::I<A1, ..., Ak>`, where `N` and `I` represent identifiers, and `<A1, ..., Ak>` is a type argument list.</span></span> <span data-ttu-id="4b063-250">(`K` est toujours au moins un.)</span><span class="sxs-lookup"><span data-stu-id="4b063-250">(`K` is always at least one.)</span></span>
*  <span data-ttu-id="4b063-251">`N::I`, où `N` et `I` représentent des identificateurs.</span><span class="sxs-lookup"><span data-stu-id="4b063-251">`N::I`, where `N` and `I` represent identifiers.</span></span> <span data-ttu-id="4b063-252">(Dans ce cas, `K` est considéré comme étant égal à zéro.)</span><span class="sxs-lookup"><span data-stu-id="4b063-252">(In this case, `K` is considered to be zero.)</span></span>

<span data-ttu-id="4b063-253">À l’aide de cette notation, la signification d’un *qualified_alias_member* est déterminée comme suit :</span><span class="sxs-lookup"><span data-stu-id="4b063-253">Using this notation, the meaning of a *qualified_alias_member* is determined as follows:</span></span>

*  <span data-ttu-id="4b063-254">Si `N` est l’identificateur `global`, puis est recherchée dans l’espace de noms global `I`:</span><span class="sxs-lookup"><span data-stu-id="4b063-254">If `N` is the identifier `global`, then the global namespace is searched for `I`:</span></span>
   * <span data-ttu-id="4b063-255">Si l’espace de noms global contient un espace de noms nommé `I` et `K` est égal à zéro, puis le *qualified_alias_member* fait référence à cet espace de noms.</span><span class="sxs-lookup"><span data-stu-id="4b063-255">If the global namespace contains a namespace named `I` and `K` is zero, then the *qualified_alias_member* refers to that namespace.</span></span>
   * <span data-ttu-id="4b063-256">Sinon, si l’espace de noms global contient un type non générique nommé `I` et `K` est égal à zéro, puis le *qualified_alias_member* fait référence à ce type.</span><span class="sxs-lookup"><span data-stu-id="4b063-256">Otherwise, if the global namespace contains a non-generic type named `I` and `K` is zero, then the *qualified_alias_member* refers to that type.</span></span>
   * <span data-ttu-id="4b063-257">Sinon, si l’espace de noms global contient un type nommé `I` qui a `K`  paramètres de type, puis le *qualified_alias_member* fait référence à ce type construit avec les arguments de type donné.</span><span class="sxs-lookup"><span data-stu-id="4b063-257">Otherwise, if the global namespace contains a type named `I` that has `K` type parameters, then the *qualified_alias_member* refers to that type constructed with the given type arguments.</span></span>
   * <span data-ttu-id="4b063-258">Sinon, le *qualified_alias_member* est non défini et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="4b063-258">Otherwise, the *qualified_alias_member* is undefined and a compile-time error occurs.</span></span>

*  <span data-ttu-id="4b063-259">Sinon, en commençant par la déclaration d’espace de noms ([Namespace déclarations](namespaces.md#namespace-declarations)) immédiatement contenant le *qualified_alias_member* (le cas échéant), poursuite de l’opération avec chaque déclaration d’espace de noms englobant (le cas échéant) et se terminant par l’unité de compilation contenant le *qualified_alias_member*, les étapes suivantes sont évaluées jusqu'à ce qu’une entité se trouve :</span><span class="sxs-lookup"><span data-stu-id="4b063-259">Otherwise, starting with the namespace declaration ([Namespace declarations](namespaces.md#namespace-declarations)) immediately containing the *qualified_alias_member* (if any), continuing with each enclosing namespace declaration (if any), and ending with the compilation unit containing the *qualified_alias_member*, the following steps are evaluated until an entity is located:</span></span>

   * <span data-ttu-id="4b063-260">Si l’unité de compilation ou de la déclaration d’espace de noms contient un *using_alias_directive* qui associe `N` avec un type, puis le *qualified_alias_member* n’est pas défini et le moment de la compilation erreur se produit.</span><span class="sxs-lookup"><span data-stu-id="4b063-260">If the namespace declaration or compilation unit contains a *using_alias_directive* that associates `N` with a type, then the *qualified_alias_member* is undefined and a compile-time error occurs.</span></span>
   * <span data-ttu-id="4b063-261">Sinon, si l’unité de compilation ou de la déclaration d’espace de noms contient un *extern_alias_directive* ou *using_alias_directive* qui associe `N` avec un espace de noms, puis :</span><span class="sxs-lookup"><span data-stu-id="4b063-261">Otherwise, if the namespace declaration or compilation unit contains an *extern_alias_directive* or *using_alias_directive* that associates `N` with a namespace, then:</span></span>
     * <span data-ttu-id="4b063-262">Si l’espace de noms associé à `N` contient un espace de noms nommé `I` et `K` est égal à zéro, puis le *qualified_alias_member* fait référence à cet espace de noms.</span><span class="sxs-lookup"><span data-stu-id="4b063-262">If the namespace associated with `N` contains a namespace named `I` and `K` is zero, then the *qualified_alias_member* refers to that namespace.</span></span>
     * <span data-ttu-id="4b063-263">Sinon, si l’espace de noms associé à `N` contient un type non générique nommé `I` et `K` est égal à zéro, puis le *qualified_alias_member* fait référence à ce type.</span><span class="sxs-lookup"><span data-stu-id="4b063-263">Otherwise, if the namespace associated with `N` contains a non-generic type named `I` and `K` is zero, then the *qualified_alias_member* refers to that type.</span></span>
     * <span data-ttu-id="4b063-264">Sinon, si l’espace de noms associé à `N` contient un type nommé `I` qui a `K`  paramètres de type, puis le *qualified_alias_member* fait référence à que type construit avec les arguments de type donné.</span><span class="sxs-lookup"><span data-stu-id="4b063-264">Otherwise, if the namespace associated with `N` contains a type named `I` that has `K` type parameters, then the *qualified_alias_member* refers to that type constructed with the given type arguments.</span></span>
     * <span data-ttu-id="4b063-265">Sinon, le *qualified_alias_member* est non défini et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="4b063-265">Otherwise, the *qualified_alias_member* is undefined and a compile-time error occurs.</span></span>
*  <span data-ttu-id="4b063-266">Sinon, le *qualified_alias_member* est non défini et une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="4b063-266">Otherwise, the *qualified_alias_member* is undefined and a compile-time error occurs.</span></span>

<span data-ttu-id="4b063-267">Notez que l’utilisation du qualificateur d’alias d’espace de noms avec un alias qui référence un type entraîne une erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="4b063-267">Note that using the namespace alias qualifier with an alias that references a type causes a compile-time error.</span></span> <span data-ttu-id="4b063-268">Notez que si l’identificateur `N` est `global`, puis recherche est effectuée dans l’espace de noms global, même s’il existe une association d’alias en utilisant `global` avec un type ou d’un espace de noms.</span><span class="sxs-lookup"><span data-stu-id="4b063-268">Also note that if the identifier `N` is `global`, then lookup is performed in the global namespace, even if there is a using alias associating `global` with a type or namespace.</span></span>

### <a name="uniqueness-of-aliases"></a><span data-ttu-id="4b063-269">Unicité d’alias</span><span class="sxs-lookup"><span data-stu-id="4b063-269">Uniqueness of aliases</span></span>

<span data-ttu-id="4b063-270">Chaque corps de la compilation unité et espace de noms a un espace de déclaration distincte pour les alias externes et l’utilisation d’alias.</span><span class="sxs-lookup"><span data-stu-id="4b063-270">Each compilation unit and namespace body has a separate declaration space for extern aliases and using aliases.</span></span> <span data-ttu-id="4b063-271">Par conséquent, bien que le nom d’un alias extern ou à l’aide d’alias doit être unique dans le jeu d’alias extern et l’utilisation d’alias déclarées dans le corps d’unité ou un espace de noms compilation conteneur immédiat, un alias est autorisé à avoir le même nom en tant que type ou espace de noms tant que j’ai t est utilisé uniquement avec le `::` qualificateur.</span><span class="sxs-lookup"><span data-stu-id="4b063-271">Thus, while the name of an extern alias or using alias must be unique within the set of extern aliases and using aliases declared in the immediately containing compilation unit or namespace body, an alias is permitted to have the same name as a type or namespace as long as it is used only with the `::` qualifier.</span></span>

<span data-ttu-id="4b063-272">Dans l’exemple</span><span class="sxs-lookup"><span data-stu-id="4b063-272">In the example</span></span>
```csharp
namespace N
{
    public class A {}

    public class B {}
}

namespace N
{
    using A = System.IO;

    class X
    {
        A.Stream s1;            // Error, A is ambiguous

        A::Stream s2;           // Ok
    }
}
```
<span data-ttu-id="4b063-273">le nom `A` a deux significations possibles dans le corps de la deuxième espace de noms, car les deux la classe `A` et à l’aide de l’alias `A` sont dans la portée.</span><span class="sxs-lookup"><span data-stu-id="4b063-273">the name `A` has two possible meanings in the second namespace body because both the class `A` and the using alias `A` are in scope.</span></span> <span data-ttu-id="4b063-274">Pour cette raison, l’utilisation de `A` dans le nom qualifié `A.Stream` est ambigu et provoque une erreur de compilation se produise.</span><span class="sxs-lookup"><span data-stu-id="4b063-274">For this reason, use of `A` in the qualified name `A.Stream` is ambiguous and causes a compile-time error to occur.</span></span> <span data-ttu-id="4b063-275">Toutefois, utiliser des `A` avec la `::` qualificateur n’est pas une erreur, car `A` est recherchée uniquement comme un alias d’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="4b063-275">However, use of `A` with the `::` qualifier is not an error because `A` is looked up only as a namespace alias.</span></span>
