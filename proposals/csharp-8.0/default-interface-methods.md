---
ms.openlocfilehash: b0d0fa70d90f7493c6c23be576155a77cec36cf8
ms.sourcegitcommit: f61a06970fa0562d2e40363fae3948eb168624ca
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "79485181"
---
# <a name="default-interface-methods"></a><span data-ttu-id="149df-101">Méthodes d’interface par défaut</span><span class="sxs-lookup"><span data-stu-id="149df-101">default interface methods</span></span>

* <span data-ttu-id="149df-102">[x] proposé</span><span class="sxs-lookup"><span data-stu-id="149df-102">[x] Proposed</span></span>
* <span data-ttu-id="149df-103">[] Prototype : [en cours](https://github.com/dotnet/roslyn/blob/master/docs/features/DefaultInterfaceImplementation.md)</span><span class="sxs-lookup"><span data-stu-id="149df-103">[ ] Prototype: [In progress](https://github.com/dotnet/roslyn/blob/master/docs/features/DefaultInterfaceImplementation.md)</span></span>
* <span data-ttu-id="149df-104">[] Implémentation : aucune</span><span class="sxs-lookup"><span data-stu-id="149df-104">[ ] Implementation: None</span></span>
* <span data-ttu-id="149df-105">[] Spécification : en cours, en dessous</span><span class="sxs-lookup"><span data-stu-id="149df-105">[ ] Specification: In progress, below</span></span>

## <a name="summary"></a><span data-ttu-id="149df-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="149df-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="149df-107">Ajoutez la prise en charge des _méthodes d’extension virtuelles_ (méthodes) dans les interfaces avec des implémentations concrètes.</span><span class="sxs-lookup"><span data-stu-id="149df-107">Add support for _virtual extension methods_ - methods in interfaces with concrete implementations.</span></span> <span data-ttu-id="149df-108">Une classe ou un struct qui implémente une telle interface doit avoir une implémentation unique _la plus spécifique_ pour la méthode d’interface, implémentée par la classe ou le struct, ou héritée de ses classes de base ou interfaces.</span><span class="sxs-lookup"><span data-stu-id="149df-108">A class or struct that implements such an interface is required to have a single _most specific_ implementation for the interface method, either implemented by the class or struct, or inherited from its base classes or interfaces.</span></span> <span data-ttu-id="149df-109">Les méthodes d’extension virtuelles permettent à un auteur d’API d’ajouter des méthodes à une interface dans les futures versions sans altérer la compatibilité source ou binaire avec les implémentations existantes de cette interface.</span><span class="sxs-lookup"><span data-stu-id="149df-109">Virtual extension methods enable an API author to add methods to an interface in future versions without breaking source or binary compatibility with existing implementations of that interface.</span></span>

<span data-ttu-id="149df-110">Celles-ci sont similaires aux [« méthodes par défaut](http://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)» de Java.</span><span class="sxs-lookup"><span data-stu-id="149df-110">These are similar to Java's ["Default Methods"](http://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html).</span></span>

<span data-ttu-id="149df-111">(En fonction de la technique d’implémentation probable) cette fonctionnalité requiert la prise en charge correspondante dans l’interface CLI/CLR.</span><span class="sxs-lookup"><span data-stu-id="149df-111">(Based on the likely implementation technique) this feature requires corresponding support in the CLI/CLR.</span></span> <span data-ttu-id="149df-112">Les programmes qui tirent parti de cette fonctionnalité ne peuvent pas s’exécuter sur des versions antérieures de la plateforme.</span><span class="sxs-lookup"><span data-stu-id="149df-112">Programs that take advantage of this feature cannot run on earlier versions of the platform.</span></span>

## <a name="motivation"></a><span data-ttu-id="149df-113">Motivation</span><span class="sxs-lookup"><span data-stu-id="149df-113">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="149df-114">Les motivations principales de cette fonctionnalité sont les</span><span class="sxs-lookup"><span data-stu-id="149df-114">The principal motivations for this feature are</span></span>

- <span data-ttu-id="149df-115">Les méthodes d’interface par défaut permettent à un auteur d’API d’ajouter des méthodes à une interface dans les versions futures sans altérer la compatibilité source ou binaire avec les implémentations existantes de cette interface.</span><span class="sxs-lookup"><span data-stu-id="149df-115">Default interface methods enable an API author to add methods to an interface in future versions without breaking source or binary compatibility with existing implementations of that interface.</span></span>
- <span data-ttu-id="149df-116">Cette fonctionnalité permet C# à d’interagir avec les API ciblant [Android (Java)](http://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html) et [iOS (SWIFT)](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-ID267), qui prennent en charge des fonctionnalités similaires.</span><span class="sxs-lookup"><span data-stu-id="149df-116">The feature enables C# to interoperate with APIs targeting [Android (Java)](http://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html) and [iOS (Swift)](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-ID267), which support similar features.</span></span>
- <span data-ttu-id="149df-117">Il s’avère que l’ajout d’implémentations d’interface par défaut fournit les éléments de la fonctionnalité de langage « traits » (<https://en.wikipedia.org/wiki/Trait_(computer_programming)>).</span><span class="sxs-lookup"><span data-stu-id="149df-117">As it turns out, adding default interface implementations provides the elements of the "traits" language feature (<https://en.wikipedia.org/wiki/Trait_(computer_programming)>).</span></span> <span data-ttu-id="149df-118">Les caractéristiques ont prouvé qu’il s’agit d’une technique de programmation puissante (<http://scg.unibe.ch/archive/papers/Scha03aTraits.pdf>).</span><span class="sxs-lookup"><span data-stu-id="149df-118">Traits have proven to be a powerful programming technique (<http://scg.unibe.ch/archive/papers/Scha03aTraits.pdf>).</span></span>

## <a name="detailed-design"></a><span data-ttu-id="149df-119">Conception détaillée</span><span class="sxs-lookup"><span data-stu-id="149df-119">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="149df-120">La syntaxe d’une interface est étendue pour permettre</span><span class="sxs-lookup"><span data-stu-id="149df-120">The syntax for an interface is extended to permit</span></span>

- <span data-ttu-id="149df-121">déclarations de membre qui déclarent des constantes, des opérateurs, des constructeurs statiques et des types imbriqués ;</span><span class="sxs-lookup"><span data-stu-id="149df-121">member declarations that declare constants, operators, static constructors, and nested types;</span></span>
- <span data-ttu-id="149df-122">*corps* d’une méthode ou d’un indexeur, d’une propriété ou d’un accesseur d’événement (autrement dit, une implémentation « par défaut »);</span><span class="sxs-lookup"><span data-stu-id="149df-122">a *body* for a method or indexer, property, or event accessor (that is, a "default" implementation);</span></span>
- <span data-ttu-id="149df-123">déclarations de membre qui déclarent des champs, des méthodes, des propriétés, des indexeurs et des événements statiques ;</span><span class="sxs-lookup"><span data-stu-id="149df-123">member declarations that declare static fields, methods, properties, indexers, and events;</span></span>
- <span data-ttu-id="149df-124">déclarations de membre à l’aide de la syntaxe d’implémentation d’interface explicite ; les</span><span class="sxs-lookup"><span data-stu-id="149df-124">member declarations using the explicit interface implementation syntax; and</span></span>
- <span data-ttu-id="149df-125">Modificateurs d’accès explicites (l’accès par défaut est `public`).</span><span class="sxs-lookup"><span data-stu-id="149df-125">Explicit access modifiers (the default access is `public`).</span></span>

<span data-ttu-id="149df-126">Les membres avec des corps permettent à l’interface de fournir une implémentation « par défaut » pour la méthode dans les classes et les structs qui ne fournissent pas d’implémentation de substitution.</span><span class="sxs-lookup"><span data-stu-id="149df-126">Members with bodies permit the interface to provide a "default" implementation for the method in classes and structs that do not provide an overriding implementation.</span></span>

<span data-ttu-id="149df-127">Les interfaces ne peuvent pas contenir l’état de l’instance.</span><span class="sxs-lookup"><span data-stu-id="149df-127">Interfaces may not contain instance state.</span></span> <span data-ttu-id="149df-128">Alors que les champs statiques sont désormais autorisés, les champs d’instance ne sont pas autorisés dans les interfaces.</span><span class="sxs-lookup"><span data-stu-id="149df-128">While static fields are now permitted, instance fields are not permitted in interfaces.</span></span> <span data-ttu-id="149df-129">Les propriétés auto des instances ne sont pas prises en charge dans les interfaces, car elles déclarent implicitement un champ masqué.</span><span class="sxs-lookup"><span data-stu-id="149df-129">Instance auto-properties are not supported in interfaces, as they would implicitly declare a hidden field.</span></span>

<span data-ttu-id="149df-130">Les méthodes statiques et privées autorisent la refactorisation et l’organisation de code utiles utilisées pour implémenter l’API publique de l’interface.</span><span class="sxs-lookup"><span data-stu-id="149df-130">Static and private methods permit useful refactoring and organization of code used to implement the interface's public API.</span></span>

<span data-ttu-id="149df-131">Une substitution de méthode dans une interface doit utiliser la syntaxe d’implémentation d’interface explicite.</span><span class="sxs-lookup"><span data-stu-id="149df-131">A method override in an interface must use the explicit interface implementation syntax.</span></span>

<span data-ttu-id="149df-132">La déclaration d’un type de classe, d’un type struct ou d’un type enum dans la portée d’un paramètre de type qui a été déclaré avec un *variance_annotation*est une erreur.</span><span class="sxs-lookup"><span data-stu-id="149df-132">It is an error to declare a class type, struct type, or enum type within the scope of a type parameter that was declared with a *variance_annotation*.</span></span>  <span data-ttu-id="149df-133">Par exemple, la déclaration de `C` ci-dessous est une erreur.</span><span class="sxs-lookup"><span data-stu-id="149df-133">For example, the declaration of `C` below is an error.</span></span>

```csharp
interface IOuter<out T>
{
    class C { } // error: class declaration within the scope of variant type parameter 'T'
}
```

### <a name="concrete-methods-in-interfaces"></a><span data-ttu-id="149df-134">Méthodes concrètes dans les interfaces</span><span class="sxs-lookup"><span data-stu-id="149df-134">Concrete methods in interfaces</span></span>

<span data-ttu-id="149df-135">La forme la plus simple de cette fonctionnalité est la possibilité de déclarer une *méthode concrète* dans une interface, qui est une méthode avec un corps.</span><span class="sxs-lookup"><span data-stu-id="149df-135">The simplest form of this feature is the ability to declare a *concrete method* in an interface, which is a method with a body.</span></span>

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
```

<span data-ttu-id="149df-136">Une classe qui implémente cette interface n’A pas besoin d’implémenter sa méthode concrète.</span><span class="sxs-lookup"><span data-stu-id="149df-136">A class that implements this interface need not implement its concrete method.</span></span>

```csharp
class C : IA { } // OK

IA i = new C();
i.M(); // prints "IA.M"
```

<span data-ttu-id="149df-137">La substitution finale pour `IA.M` dans la classe `C` est la méthode concrète `M` déclarée dans `IA`.</span><span class="sxs-lookup"><span data-stu-id="149df-137">The final override for `IA.M` in class `C` is the concrete method `M` declared in `IA`.</span></span> <span data-ttu-id="149df-138">Notez qu’une classe n’hérite pas des membres de ses interfaces ; Cela n’est pas modifié par cette fonctionnalité :</span><span class="sxs-lookup"><span data-stu-id="149df-138">Note that a class does not inherit members from its interfaces; that is not changed by this feature:</span></span>

```csharp
new C().M(); // error: class 'C' does not contain a member 'M'
```

<span data-ttu-id="149df-139">Dans un membre d’instance d’une interface, `this` a le type de l’interface englobante.</span><span class="sxs-lookup"><span data-stu-id="149df-139">Within an instance member of an interface, `this` has the type of the enclosing interface.</span></span>

### <a name="modifiers-in-interfaces"></a><span data-ttu-id="149df-140">Modificateurs dans les interfaces</span><span class="sxs-lookup"><span data-stu-id="149df-140">Modifiers in interfaces</span></span>

<span data-ttu-id="149df-141">La syntaxe d’une interface est assouplie pour autoriser les modificateurs sur ses membres.</span><span class="sxs-lookup"><span data-stu-id="149df-141">The syntax for an interface is relaxed to permit modifiers on its members.</span></span> <span data-ttu-id="149df-142">Les éléments suivants sont autorisés : `private`, `protected`, `internal`, `public`, `virtual`, `abstract`, `sealed`, `static`, `extern`et `partial`.</span><span class="sxs-lookup"><span data-stu-id="149df-142">The following are permitted: `private`, `protected`, `internal`, `public`, `virtual`, `abstract`, `sealed`, `static`, `extern`, and `partial`.</span></span>

> <span data-ttu-id="149df-143">***TODO***: Vérifiez les autres modificateurs existants.</span><span class="sxs-lookup"><span data-stu-id="149df-143">***TODO***: check what other modifiers exist.</span></span>

<span data-ttu-id="149df-144">Un membre d’interface dont la déclaration comprend un corps est un membre `virtual`, sauf si le modificateur `sealed` ou `private` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="149df-144">An interface member whose declaration includes a body is a `virtual` member unless the `sealed` or `private` modifier is used.</span></span> <span data-ttu-id="149df-145">Le modificateur de `virtual` peut être utilisé sur un membre de fonction qui, autrement, serait implicitement `virtual`.</span><span class="sxs-lookup"><span data-stu-id="149df-145">The `virtual` modifier may be used on a function member that would otherwise be implicitly `virtual`.</span></span> <span data-ttu-id="149df-146">De même, bien que `abstract` soit la valeur par défaut sur les membres d’interface sans corps, ce modificateur peut être fourni explicitement.</span><span class="sxs-lookup"><span data-stu-id="149df-146">Similarly, although `abstract` is the default on interface members without bodies, that modifier may be given explicitly.</span></span> <span data-ttu-id="149df-147">Un membre non virtuel peut être déclaré à l’aide du mot clé `sealed`.</span><span class="sxs-lookup"><span data-stu-id="149df-147">A non-virtual member may be declared using the `sealed` keyword.</span></span>

<span data-ttu-id="149df-148">Un `private` ou `sealed` fonction membre d’une interface n’a pas de corps.</span><span class="sxs-lookup"><span data-stu-id="149df-148">It is an error for a `private` or `sealed` function member of an interface to have no body.</span></span> <span data-ttu-id="149df-149">Un membre de fonction `private` ne peut pas avoir le modificateur `sealed`.</span><span class="sxs-lookup"><span data-stu-id="149df-149">A `private` function member may not have the modifier `sealed`.</span></span>

<span data-ttu-id="149df-150">Les modificateurs d’accès peuvent être utilisés sur les membres d’interface de tous les types de membres qui sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="149df-150">Access modifiers may be used on interface members of all kinds of members that are permitted.</span></span> <span data-ttu-id="149df-151">Le niveau d’accès `public` est la valeur par défaut, mais il peut être fourni explicitement.</span><span class="sxs-lookup"><span data-stu-id="149df-151">The access level `public` is the default but it may be given explicitly.</span></span>

> <span data-ttu-id="149df-152">***Problème d’ouverture :*** Nous devons spécifier la signification précise des modificateurs d’accès, tels que `protected` et `internal`, et les déclarations qui ne se substituent pas à elles (dans une interface dérivée) ou qui les implémentent (dans une classe qui implémente l’interface).</span><span class="sxs-lookup"><span data-stu-id="149df-152">***Open Issue:*** We need to specify the precise meaning of the access modifiers such as `protected` and `internal`, and which declarations do and do not override them (in a derived interface) or implement them (in a class that implements the interface).</span></span>

<span data-ttu-id="149df-153">Les interfaces peuvent déclarer des membres `static`, y compris les types imbriqués, les méthodes, les indexeurs, les propriétés, les événements et les constructeurs statiques.</span><span class="sxs-lookup"><span data-stu-id="149df-153">Interfaces may declare `static` members, including nested types, methods, indexers, properties, events, and static constructors.</span></span> <span data-ttu-id="149df-154">Le niveau d’accès par défaut pour tous les membres d’interface est `public`.</span><span class="sxs-lookup"><span data-stu-id="149df-154">The default access level for all interface members is `public`.</span></span>

<span data-ttu-id="149df-155">Les interfaces ne peuvent pas déclarer des constructeurs, des destructeurs ou des champs d’instance.</span><span class="sxs-lookup"><span data-stu-id="149df-155">Interfaces may not declare instance constructors, destructors, or fields.</span></span>

> <span data-ttu-id="149df-156">***Problème fermé :*** Les déclarations d’opérateur doivent-elles être autorisées dans une interface ?</span><span class="sxs-lookup"><span data-stu-id="149df-156">***Closed Issue:*** Should operator declarations be permitted in an interface?</span></span> <span data-ttu-id="149df-157">Les opérateurs de conversion ne sont probablement pas, mais qu’en est-il des autres ?</span><span class="sxs-lookup"><span data-stu-id="149df-157">Probably not conversion operators, but what about others?</span></span> <span data-ttu-id="149df-158">***Décision***: les opérateurs sont autorisés, *à l’exception* des opérateurs de conversion, d’égalité et d’inégalité.</span><span class="sxs-lookup"><span data-stu-id="149df-158">***Decision***: Operators are permitted *except* for conversion, equality, and inequality operators.</span></span>

> <span data-ttu-id="149df-159">***Problème fermé :*** Faut-`new` être autorisé sur les déclarations de membres d’interface qui masquent les membres des interfaces de base ?</span><span class="sxs-lookup"><span data-stu-id="149df-159">***Closed Issue:*** Should `new` be permitted on interface member declarations that hide members from base interfaces?</span></span> <span data-ttu-id="149df-160">***Décision***: Oui.</span><span class="sxs-lookup"><span data-stu-id="149df-160">***Decision***: Yes.</span></span>

> <span data-ttu-id="149df-161">***Problème fermé :*** Actuellement, nous n’autorisons pas les `partial` sur une interface ou ses membres.</span><span class="sxs-lookup"><span data-stu-id="149df-161">***Closed Issue:*** We do not currently permit `partial` on an interface or its members.</span></span> <span data-ttu-id="149df-162">Cela nécessiterait une proposition distincte.</span><span class="sxs-lookup"><span data-stu-id="149df-162">That would require a separate proposal.</span></span> <span data-ttu-id="149df-163">***Décision***: Oui.</span><span class="sxs-lookup"><span data-stu-id="149df-163">***Decision***: Yes.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#permit-partial-in-interface>

### <a name="overrides-in-interfaces"></a><span data-ttu-id="149df-164">Substitutions dans les interfaces</span><span class="sxs-lookup"><span data-stu-id="149df-164">Overrides in interfaces</span></span>

<span data-ttu-id="149df-165">Les déclarations override (c’est-à-dire celles contenant le modificateur `override`) permettent au programmeur de fournir une implémentation la plus spécifique d’un membre virtuel dans une interface où le compilateur ou le runtime n’en trouvera pas un.</span><span class="sxs-lookup"><span data-stu-id="149df-165">Override declarations (i.e. those containing the `override` modifier) allow the programmer to provide a most specific implementation of a virtual member in an interface where the compiler or runtime would not otherwise find one.</span></span> <span data-ttu-id="149df-166">Il permet également de transformer un membre abstrait d’une super interface en membre par défaut dans une interface dérivée.</span><span class="sxs-lookup"><span data-stu-id="149df-166">It also allows turning an abstract member from a super-interface into a default member in a derived interface.</span></span> <span data-ttu-id="149df-167">Une déclaration override est autorisée à substituer *explicitement* une méthode d’interface de base particulière en qualifiant la déclaration avec le nom de l’interface (aucun modificateur d’accès n’est autorisé dans ce cas).</span><span class="sxs-lookup"><span data-stu-id="149df-167">An override declaration is permitted to *explicitly* override a particular base interface method by qualifying the declaration with the interface name (no access modifier is permitted in this case).</span></span> <span data-ttu-id="149df-168">Les substitutions implicites ne sont pas autorisées.</span><span class="sxs-lookup"><span data-stu-id="149df-168">Implicit overrides are not permitted.</span></span>

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    override void IA.M() { WriteLine("IB.M"); } // explicitly named
}
interface IC : IA
{
    override void M() { WriteLine("IC.M"); } // implicitly named
}
```

<span data-ttu-id="149df-169">Les déclarations override dans les interfaces ne peuvent pas être déclarées `sealed`.</span><span class="sxs-lookup"><span data-stu-id="149df-169">Override declarations in interfaces may not be declared `sealed`.</span></span>

<span data-ttu-id="149df-170">Les membres de fonction `virtual` publics dans une interface peuvent être substitués explicitement dans une interface dérivée (en qualifiant le nom dans la déclaration override avec le type d’interface qui a initialement déclaré la méthode et en omettant un modificateur d’accès).</span><span class="sxs-lookup"><span data-stu-id="149df-170">Public `virtual` function members in an interface may be overridden in a derived interface explicitly (by qualifying the name in the override declaration with the interface type that originally declared the method, and omitting an access modifier).</span></span>

<span data-ttu-id="149df-171">`virtual` membres de fonction dans une interface ne peuvent être substitués explicitement (pas implicitement) dans les interfaces dérivées, et les membres qui ne sont pas `public` peuvent être implémentés explicitement dans une classe ou un struct explicitement (pas implicitement).</span><span class="sxs-lookup"><span data-stu-id="149df-171">`virtual` function members in an interface may only be overridden explicitly (not implicitly) in derived interfaces, and members that are not `public` may only be implemented in a class or struct explicitly (not implicitly).</span></span> <span data-ttu-id="149df-172">Dans les deux cas, le membre substitué ou implémenté doit être *accessible* là où il est substitué.</span><span class="sxs-lookup"><span data-stu-id="149df-172">In either case, the overridden or implemented member must be *accessible* where it is overridden.</span></span>

### <a name="reabstraction"></a><span data-ttu-id="149df-173">Réabstraction</span><span class="sxs-lookup"><span data-stu-id="149df-173">Reabstraction</span></span>

<span data-ttu-id="149df-174">Une méthode virtuelle (concrète) déclarée dans une interface peut être substituée pour être abstraite dans une interface dérivée</span><span class="sxs-lookup"><span data-stu-id="149df-174">A virtual (concrete) method declared in an interface may be overridden to be abstract in a derived interface</span></span>

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    abstract void IA.M();
}
class C : IB { } // error: class 'C' does not implement 'IA.M'.
```

<span data-ttu-id="149df-175">Le modificateur `abstract` n’est pas requis dans la déclaration de `IB.M` (il s’agit de la valeur par défaut dans les interfaces), mais il est probablement conseillé d’être explicite dans une déclaration override.</span><span class="sxs-lookup"><span data-stu-id="149df-175">The `abstract` modifier is not required in the declaration of `IB.M` (that is the default in interfaces), but it is probably good practice to be explicit in an override declaration.</span></span>

<span data-ttu-id="149df-176">Cela est utile dans les interfaces dérivées où l’implémentation par défaut d’une méthode est inappropriée et une implémentation plus appropriée doit être fournie par l’implémentation de classes.</span><span class="sxs-lookup"><span data-stu-id="149df-176">This is useful in derived interfaces where the default implementation of a method is inappropriate and a more appropriate implementation should be provided by implementing classes.</span></span>

> <span data-ttu-id="149df-177">***Problème d’ouverture :*** La réabstraction doit-elle être autorisée ?</span><span class="sxs-lookup"><span data-stu-id="149df-177">***Open Issue:*** Should reabstraction be permitted?</span></span>

### <a name="the-most-specific-override-rule"></a><span data-ttu-id="149df-178">Règle de remplacement la plus spécifique</span><span class="sxs-lookup"><span data-stu-id="149df-178">The most specific override rule</span></span>

<span data-ttu-id="149df-179">Nous avons besoin que chaque interface et classe aient une *substitution la plus spécifique* pour chaque membre virtuel parmi les substitutions apparaissant dans le type ou ses interfaces directes et indirectes.</span><span class="sxs-lookup"><span data-stu-id="149df-179">We require that every interface and class have a *most specific override* for every virtual member among the overrides appearing in the type or its direct and indirect interfaces.</span></span> <span data-ttu-id="149df-180">La *substitution la plus spécifique* est un remplacement unique qui est plus spécifique que tous les autres remplacements.</span><span class="sxs-lookup"><span data-stu-id="149df-180">The *most specific override* is a unique override that is more specific than every other override.</span></span> <span data-ttu-id="149df-181">S’il n’y a pas de substitution, le membre lui-même est considéré comme la substitution la plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="149df-181">If there is no override, the member itself is considered the most specific override.</span></span>

<span data-ttu-id="149df-182">Une `M1` de remplacement est considérée comme *plus spécifique* qu’une autre substitution `M2` si `M1` est déclarée sur le type `T1`, `M2` est déclarée sur le type `T2`et soit</span><span class="sxs-lookup"><span data-stu-id="149df-182">One override `M1` is considered *more specific* than another override `M2` if `M1` is declared on type `T1`, `M2` is declared on type `T2`, and either</span></span>

1. <span data-ttu-id="149df-183">`T1` contient des `T2` parmi ses interfaces directes ou indirectes, ou</span><span class="sxs-lookup"><span data-stu-id="149df-183">`T1` contains `T2` among its direct or indirect interfaces, or</span></span>
2. <span data-ttu-id="149df-184">`T2` est un type d’interface, mais `T1` n’est pas un type d’interface.</span><span class="sxs-lookup"><span data-stu-id="149df-184">`T2` is an interface type but `T1` is not an interface type.</span></span>

<span data-ttu-id="149df-185">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="149df-185">For example:</span></span>

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    void IA.M() { WriteLine("IB.M"); }
}
interface IC : IA
{
    void IA.M() { WriteLine("IC.M"); }
}
interface ID : IB, IC { } // error: no most specific override for 'IA.M'
abstract class C : IB, IC { } // error: no most specific override for 'IA.M'
abstract class D : IA, IB, IC // ok
{
    public abstract void M();
}

```

<span data-ttu-id="149df-186">La règle de remplacement la plus spécifique garantit qu’un conflit (c.-à-d. une ambiguïté résultant de l’héritage de diamant) est résolu explicitement par le programmeur au point où le conflit se produit.</span><span class="sxs-lookup"><span data-stu-id="149df-186">The most specific override rule ensures that a conflict (i.e. an ambiguity arising from diamond inheritance) is resolved explicitly by the programmer at the point where the conflict arises.</span></span>

<span data-ttu-id="149df-187">Étant donné que nous prenons en charge les substitutions abstraites explicites dans les interfaces, nous pourrions le faire également dans les classes</span><span class="sxs-lookup"><span data-stu-id="149df-187">Because we support explicit abstract overrides in interfaces, we could do so in classes as well</span></span>

```csharp
abstract class E : IA, IB, IC // ok
{
    abstract void IA.M();
}
```

> <span data-ttu-id="149df-188">***Problème d’ouverture***: devons-nous prendre en charge les remplacements abstraits d’interface explicites dans les classes ?</span><span class="sxs-lookup"><span data-stu-id="149df-188">***Open issue***: should we support explicit interface abstract overrides in classes?</span></span>

<span data-ttu-id="149df-189">En outre, il s’agit d’une erreur si dans une déclaration de classe la substitution la plus spécifique d’une méthode d’interface est une substitution abstraite qui a été déclarée dans une interface.</span><span class="sxs-lookup"><span data-stu-id="149df-189">In addition, it is an error if in a class declaration the most specific override of some interface method is an abstract override that was declared in an interface.</span></span> <span data-ttu-id="149df-190">Il s’agit d’une règle existante retraitée à l’aide de la nouvelle terminologie.</span><span class="sxs-lookup"><span data-stu-id="149df-190">This is an existing rule restated using the new terminology.</span></span>

```csharp
interface IF
{
    void M();
}
abstract class F : IF { } // error: 'F' does not implement 'IF.M'
```

<span data-ttu-id="149df-191">Il est possible pour une propriété virtuelle déclarée dans une interface d’avoir une substitution la plus spécifique pour son accesseur `get` dans une interface et une substitution la plus spécifique pour son accesseur `set` dans une interface différente.</span><span class="sxs-lookup"><span data-stu-id="149df-191">It is possible for a virtual property declared in an interface to have a most specific override for its `get` accessor in one interface and a most specific override for its `set` accessor in a different interface.</span></span> <span data-ttu-id="149df-192">Cela est considéré comme une violation de la règle de *remplacement la plus spécifique* .</span><span class="sxs-lookup"><span data-stu-id="149df-192">This is considered a violation of the *most specific override* rule.</span></span>

### <a name="static-and-private-methods"></a><span data-ttu-id="149df-193">Méthodes `static` et `private`</span><span class="sxs-lookup"><span data-stu-id="149df-193">`static` and `private` methods</span></span>

<span data-ttu-id="149df-194">Étant donné que les interfaces peuvent désormais contenir du code exécutable, il est utile d’extraire le code commun en méthodes privées et statiques.</span><span class="sxs-lookup"><span data-stu-id="149df-194">Because interfaces may now contain executable code, it is useful to abstract common code into private and static methods.</span></span> <span data-ttu-id="149df-195">Nous les autorisons maintenant dans les interfaces.</span><span class="sxs-lookup"><span data-stu-id="149df-195">We now permit these in interfaces.</span></span>

> <span data-ttu-id="149df-196">***Problème fermé***: devons-nous prendre en charge les méthodes privées ?</span><span class="sxs-lookup"><span data-stu-id="149df-196">***Closed issue***: Should we support private methods?</span></span> <span data-ttu-id="149df-197">Devons-nous prendre en charge les méthodes statiques ?</span><span class="sxs-lookup"><span data-stu-id="149df-197">Should we support static methods?</span></span> <span data-ttu-id="149df-198">**Décision : Oui**</span><span class="sxs-lookup"><span data-stu-id="149df-198">**Decision: YES**</span></span>

> <span data-ttu-id="149df-199">***Problème ouvert***: les méthodes d’interface doivent-elles être `protected` ou `internal` ou un autre accès ?</span><span class="sxs-lookup"><span data-stu-id="149df-199">***Open issue***: should we permit interface methods to be `protected` or `internal` or other access?</span></span> <span data-ttu-id="149df-200">Dans ce cas, quelle est la sémantique ?</span><span class="sxs-lookup"><span data-stu-id="149df-200">If so, what are the semantics?</span></span> <span data-ttu-id="149df-201">Sont-ils `virtual` par défaut ?</span><span class="sxs-lookup"><span data-stu-id="149df-201">Are they `virtual` by default?</span></span> <span data-ttu-id="149df-202">Dans ce cas, existe-t-il un moyen de les rendre non virtuels ?</span><span class="sxs-lookup"><span data-stu-id="149df-202">If so, is there a way to make them non-virtual?</span></span>

> <span data-ttu-id="149df-203">***Problème d’ouverture***: si nous prenons en charge les méthodes statiques, devons-nous prendre en charge les opérateurs (statiques) ?</span><span class="sxs-lookup"><span data-stu-id="149df-203">***Open issue***: If we support static methods, should we support (static) operators?</span></span>

### <a name="base-interface-invocations"></a><span data-ttu-id="149df-204">Appels de l’interface de base</span><span class="sxs-lookup"><span data-stu-id="149df-204">Base interface invocations</span></span>

<span data-ttu-id="149df-205">Le code d’un type qui dérive d’une interface avec une méthode par défaut peut appeler explicitement l’implémentation de « base » de cette interface.</span><span class="sxs-lookup"><span data-stu-id="149df-205">Code in a type that derives from an interface with a default method can explicitly invoke that interface's "base" implementation.</span></span>

```csharp
interface I0
{
   void M() { Console.WriteLine("I0"); }
}
interface I1 : I0
{
   override void M() { Console.WriteLine("I1"); }
}
interface I2 : I0
{
   override void M() { Console.WriteLine("I2"); }
}
interface I3 : I1, I2
{
   // an explicit override that invoke's a base interface's default method
   void I0.M() { I2.base.M(); }
}

```

<span data-ttu-id="149df-206">Une méthode d’instance (non statique) est autorisée à appeler l’implémentation d’une méthode d’instance accessible dans une interface de base directe de manière non virtuelle en la nommant à l’aide de la syntaxe `base(Type).M`.</span><span class="sxs-lookup"><span data-stu-id="149df-206">An instance (nonstatic) method is permitted to invoke the implementation of an accessible instance method in a direct base interface nonvirtually by naming it using the syntax `base(Type).M`.</span></span> <span data-ttu-id="149df-207">Cela est utile quand une substitution qui doit être fournie en raison de l’héritage Diamond est résolue par la délégation à une implémentation de base particulière.</span><span class="sxs-lookup"><span data-stu-id="149df-207">This is useful when an override that is required to be provided due to diamond inheritance is resolved by delegating to one particular base implementation.</span></span>

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    override void IA.M() { WriteLine("IB.M"); }
}
interface IC : IA
{
    override void IA.M() { WriteLine("IC.M"); }
}

class D : IA, IB, IC
{
    void IA.M() { base(IB).M(); }
}
```

<span data-ttu-id="149df-208">Lorsqu’un membre `virtual` ou `abstract` est accessible à l’aide de la syntaxe `base(Type).M`, `Type` contient une substitution unique *la plus spécifique* pour `M`.</span><span class="sxs-lookup"><span data-stu-id="149df-208">When a `virtual` or `abstract` member is accessed using the syntax `base(Type).M`, it is required that `Type` contains a unique *most specific override* for `M`.</span></span>

### <a name="binding-base-clauses"></a><span data-ttu-id="149df-209">Liaison de clauses de base</span><span class="sxs-lookup"><span data-stu-id="149df-209">Binding base clauses</span></span>

<span data-ttu-id="149df-210">Les interfaces contiennent désormais des types.</span><span class="sxs-lookup"><span data-stu-id="149df-210">Interfaces now contain types.</span></span>  <span data-ttu-id="149df-211">Ces types peuvent être utilisés dans la clause de base comme interfaces de base.</span><span class="sxs-lookup"><span data-stu-id="149df-211">These types may be used in the base clause as base interfaces.</span></span>  <span data-ttu-id="149df-212">Lors de la liaison d’une clause de base, nous pouvons être amenés à connaître l’ensemble des interfaces de base pour lier ces types (par exemple, pour effectuer une recherche dans ceux-ci et pour résoudre l’accès protégé).</span><span class="sxs-lookup"><span data-stu-id="149df-212">When binding a base clause, we may need to know the set of base interfaces to bind those types (e.g. to lookup in them and to resolve protected access).</span></span>  <span data-ttu-id="149df-213">La signification de la clause de base d’une interface est donc définie de manière circulaire.</span><span class="sxs-lookup"><span data-stu-id="149df-213">The meaning of an interface's base clause is thus circularly defined.</span></span>  <span data-ttu-id="149df-214">Pour rompre le cycle, nous ajoutons une nouvelle règle de langage correspondant à une règle similaire déjà en place pour les classes.</span><span class="sxs-lookup"><span data-stu-id="149df-214">To break the cycle, we add a new language rules corresponding to a similar rule already in place for classes.</span></span>

<span data-ttu-id="149df-215">Tout en déterminant la signification de la *interface_base* d’une interface, les interfaces de base sont temporairement supposées être vides.</span><span class="sxs-lookup"><span data-stu-id="149df-215">While determining the meaning of the *interface_base* of an interface, the base interfaces are temporarily assumed to be empty.</span></span> <span data-ttu-id="149df-216">Intuitivement, cela garantit que la signification d’une clause de base ne peut pas dépendre de lui-même de manière récursive.</span><span class="sxs-lookup"><span data-stu-id="149df-216">Intuitively this ensures that the meaning of a base clause cannot recursively depend on itself.</span></span> 

<span data-ttu-id="149df-217">**Nous avons utilisé les règles suivantes :**</span><span class="sxs-lookup"><span data-stu-id="149df-217">**We used to have the following rules:**</span></span>

<span data-ttu-id="149df-218">«Quand une classe B dérive d’une classe A, il s’agit d’une erreur de compilation qui dépend de B. Une classe **dépend directement de** sa classe de base directe (le cas échéant) et **dépend directement de** la ~~**classe**~~ dans laquelle elle est immédiatement imbriquée (le cas échéant).</span><span class="sxs-lookup"><span data-stu-id="149df-218">"When a class B derives from a class A, it is a compile-time error for A to depend on B. A class **directly depends on** its direct base class (if any) and **directly depends on** the ~~**class**~~ within which it is immediately nested (if any).</span></span> <span data-ttu-id="149df-219">À partir de cette définition, l’ensemble complet des ~~**classes**~~ dont dépend une classe est la fermeture réflexive et transitive de la relation **directement dépend** de la relation.</span><span class="sxs-lookup"><span data-stu-id="149df-219">Given this definition, the complete set of ~~**classes**~~ upon which a class depends is the reflexive and transitive closure of the **directly depends on** relationship."</span></span>

<span data-ttu-id="149df-220">Il s’agit d’une erreur au moment de la compilation pour qu’une interface hérite directement ou indirectement de lui-même.</span><span class="sxs-lookup"><span data-stu-id="149df-220">It is a compile-time error for an interface to directly or indirectly inherit from itself.</span></span>
<span data-ttu-id="149df-221">Les **interfaces de base** d’une interface sont les interfaces de base explicites et leurs interfaces de base.</span><span class="sxs-lookup"><span data-stu-id="149df-221">The **base interfaces** of an interface are the explicit base interfaces and their base interfaces.</span></span> <span data-ttu-id="149df-222">En d’autres termes, l’ensemble des interfaces de base est la fermeture transitive complète des interfaces de base explicites, leurs interfaces de base explicites, et ainsi de suite.</span><span class="sxs-lookup"><span data-stu-id="149df-222">In other words, the set of base interfaces is the complete transitive closure of the explicit base interfaces, their explicit base interfaces, and so on.</span></span>

<span data-ttu-id="149df-223">**Nous les ajustons comme suit :**</span><span class="sxs-lookup"><span data-stu-id="149df-223">**We are adjusting them as follows:**</span></span>

<span data-ttu-id="149df-224">Quand une classe B dérive d’une classe A, il s’agit d’une erreur au moment de la compilation pour qu’un dépende de B. Une classe **dépend directement de** sa classe de base directe (le cas échéant) et **dépend directement** du _**type**_ dans lequel elle est immédiatement imbriquée (le cas échéant).</span><span class="sxs-lookup"><span data-stu-id="149df-224">When a class B derives from a class A, it is a compile-time error for A to depend on B. A class **directly depends on** its direct base class (if any) and **directly depends on** the _**type**_ within which it is immediately nested (if any).</span></span>

<span data-ttu-id="149df-225">Quand une interface IB étend une interface IA, il s’agit d’une erreur de compilation pour IA qui dépend de IB.</span><span class="sxs-lookup"><span data-stu-id="149df-225">When an interface IB extends an interface IA, it is a compile-time error for IA to depend on IB.</span></span> <span data-ttu-id="149df-226">Une interface **dépend directement de** ses interfaces de base directes (le cas échéant) et **dépend directement** du type dans lequel elle est immédiatement imbriquée (le cas échéant).</span><span class="sxs-lookup"><span data-stu-id="149df-226">An interface **directly depends on** its direct base interfaces (if any) and **directly depends on** the type within which it is immediately nested (if any).</span></span>

<span data-ttu-id="149df-227">Étant donné ces définitions, le jeu complet de **types** dont dépend un type est la fermeture réflexive et transitive de la relation **directement dépend de** la relation.</span><span class="sxs-lookup"><span data-stu-id="149df-227">Given these definitions, the complete set of **types** upon which a type depends is the reflexive and transitive closure of the **directly depends on** relationship.</span></span>

### <a name="effect-on-existing-programs"></a><span data-ttu-id="149df-228">Effet sur les programmes existants</span><span class="sxs-lookup"><span data-stu-id="149df-228">Effect on existing programs</span></span>

<span data-ttu-id="149df-229">Les règles présentées ici sont destinées à n’avoir aucun effet sur la signification des programmes existants.</span><span class="sxs-lookup"><span data-stu-id="149df-229">The rules presented here are intended to have no effect on the meaning of existing programs.</span></span>

<span data-ttu-id="149df-230">Exemple 1 :</span><span class="sxs-lookup"><span data-stu-id="149df-230">Example 1:</span></span>

```csharp
interface IA
{
    void M();
}
class C: IA // Error: IA.M has no concrete most specific override in C
{
    public static void M() { } // method unrelated to 'IA.M' because static
}
```

<span data-ttu-id="149df-231">Exemple 2 :</span><span class="sxs-lookup"><span data-stu-id="149df-231">Example 2:</span></span>

```csharp
interface IA
{
    void M();
}
class Base: IA
{
    void IA.M() { }
}
class Derived: Base, IA // OK, all interface members have a concrete most specific override
{
    private void M() { } // method unrelated to 'IA.M' because private
}
```

<span data-ttu-id="149df-232">Les mêmes règles donnent des résultats similaires à la situation analogue impliquant des méthodes d’interface par défaut :</span><span class="sxs-lookup"><span data-stu-id="149df-232">The same rules give similar results to the analogous situation involving default interface methods:</span></span>

```csharp
interface IA
{
    void M() { }
}
class Derived: IA // OK, all interface members have a concrete most specific override
{
    private void M() { } // method unrelated to 'IA.M' because private
}
```

> <span data-ttu-id="149df-233">***Problème fermé***: confirmez qu’il s’agit d’une conséquence prévue de la spécification.</span><span class="sxs-lookup"><span data-stu-id="149df-233">***Closed issue***: confirm that this is an intended consequence of the specification.</span></span> <span data-ttu-id="149df-234">**Décision : Oui**</span><span class="sxs-lookup"><span data-stu-id="149df-234">**Decision: YES**</span></span>

### <a name="runtime-method-resolution"></a><span data-ttu-id="149df-235">Résolution de méthode d’exécution</span><span class="sxs-lookup"><span data-stu-id="149df-235">Runtime method resolution</span></span>

> <span data-ttu-id="149df-236">***Problème fermé :*** La spécification doit décrire l’algorithme de résolution de la méthode d’exécution en face des méthodes par défaut de l’interface.</span><span class="sxs-lookup"><span data-stu-id="149df-236">***Closed Issue:*** The spec should describe the runtime method resolution algorithm in the face of interface default methods.</span></span> <span data-ttu-id="149df-237">Nous devons nous assurer que la sémantique est cohérente avec la sémantique de langue, par exemple les méthodes déclarées qui font et ne substituent pas ou n’implémentent pas une méthode `internal`.</span><span class="sxs-lookup"><span data-stu-id="149df-237">We need to ensure that the semantics are consistent with the language semantics, e.g. which declared methods do and do not override or implement an `internal` method.</span></span>

### <a name="clr-support-api"></a><span data-ttu-id="149df-238">API de prise en charge CLR</span><span class="sxs-lookup"><span data-stu-id="149df-238">CLR support API</span></span>

<span data-ttu-id="149df-239">Pour que les compilateurs détectent lorsqu’ils sont en cours de compilation pour un Runtime qui prend en charge cette fonctionnalité, les bibliothèques de ces runtimes sont modifiées pour annoncer ce fait via l’API décrite dans <https://github.com/dotnet/corefx/issues/17116>.</span><span class="sxs-lookup"><span data-stu-id="149df-239">In order for compilers to detect when they are compiling for a runtime that supports this feature, libraries for such runtimes are modified to advertise that fact through the API discussed in <https://github.com/dotnet/corefx/issues/17116>.</span></span> <span data-ttu-id="149df-240">Nous ajoutons</span><span class="sxs-lookup"><span data-stu-id="149df-240">We add</span></span>

```csharp
namespace System.Runtime.CompilerServices
{
    public static class RuntimeFeature
    {
        // Presence of the field indicates runtime support
        public const string DefaultInterfaceImplementation = nameof(DefaultInterfaceImplementation);
    }
}
```

> <span data-ttu-id="149df-241">***Problème ouvert***: est-ce que le meilleur nom pour la fonctionnalité *CLR* ?</span><span class="sxs-lookup"><span data-stu-id="149df-241">***Open issue***: Is that the best name for the *CLR* feature?</span></span> <span data-ttu-id="149df-242">La fonctionnalité CLR fait bien plus que cela (par exemple, assouplit les contraintes de protection, prend en charge les remplacements dans les interfaces, etc.).</span><span class="sxs-lookup"><span data-stu-id="149df-242">The CLR feature does much more than just that (e.g. relaxes protection constraints, supports overrides in interfaces, etc).</span></span> <span data-ttu-id="149df-243">Par exemple, elle doit être appelée « méthodes concrètes dans les interfaces » ou « caractéristiques » ?</span><span class="sxs-lookup"><span data-stu-id="149df-243">Perhaps it should be called something like "concrete methods in interfaces", or "traits"?</span></span>

### <a name="further-areas-to-be-specified"></a><span data-ttu-id="149df-244">Autres zones à spécifier</span><span class="sxs-lookup"><span data-stu-id="149df-244">Further areas to be specified</span></span>

- <span data-ttu-id="149df-245">[] Il serait utile de cataloguer les genres d’effets de compatibilité source et binaire dus à l’ajout de méthodes d’interface par défaut et de remplacements à des interfaces existantes.</span><span class="sxs-lookup"><span data-stu-id="149df-245">[ ] It would be useful to catalog the kinds of source and binary compatibility effects caused by adding default interface methods and overrides to existing interfaces.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="149df-246">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="149df-246">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="149df-247">Cette proposition requiert une mise à jour coordonnée de la spécification du CLR (pour prendre en charge les méthodes concrètes dans les interfaces et la résolution de méthode).</span><span class="sxs-lookup"><span data-stu-id="149df-247">This proposal requires a coordinated update to the CLR specification (to support concrete methods in interfaces and method resolution).</span></span> <span data-ttu-id="149df-248">Par conséquent, il est relativement « coûteux » et il peut être intéressant d’utiliser d’autres fonctionnalités que nous prévoyons d’exiger également des modifications du CLR.</span><span class="sxs-lookup"><span data-stu-id="149df-248">It is therefore fairly "expensive" and it may be worth doing in combination with other features that we also anticipate would require CLR changes.</span></span>

## <a name="alternatives"></a><span data-ttu-id="149df-249">Autres solutions</span><span class="sxs-lookup"><span data-stu-id="149df-249">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="149df-250">Aucun.</span><span class="sxs-lookup"><span data-stu-id="149df-250">None.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="149df-251">Questions non résolues</span><span class="sxs-lookup"><span data-stu-id="149df-251">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="149df-252">Les questions ouvertes sont dénommées tout au long de la proposition, ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="149df-252">Open questions are called out throughout the proposal, above.</span></span>
- <span data-ttu-id="149df-253">Voir aussi <https://github.com/dotnet/csharplang/issues/406> pour obtenir la liste des questions ouvertes.</span><span class="sxs-lookup"><span data-stu-id="149df-253">See also <https://github.com/dotnet/csharplang/issues/406> for a list of open questions.</span></span>
- <span data-ttu-id="149df-254">La spécification détaillée doit décrire le mécanisme de résolution utilisé lors de l’exécution pour sélectionner la méthode précise à appeler.</span><span class="sxs-lookup"><span data-stu-id="149df-254">The detailed specification must describe the resolution mechanism used at runtime to select the precise method to be invoked.</span></span>
- <span data-ttu-id="149df-255">L’interaction des métadonnées produites par les nouveaux compilateurs et consommées par les compilateurs plus anciens doit être traitée en détail.</span><span class="sxs-lookup"><span data-stu-id="149df-255">The interaction of metadata produced by new compilers and consumed by older compilers needs to be worked out in detail.</span></span> <span data-ttu-id="149df-256">Par exemple, nous devons nous assurer que la représentation de métadonnées que nous utilisons n’entraîne pas l’ajout d’une implémentation par défaut dans une interface pour arrêter une classe existante qui implémente cette interface lorsqu’elle est compilée par un compilateur plus ancien.</span><span class="sxs-lookup"><span data-stu-id="149df-256">For example, we need to ensure that the metadata representation that we use does not cause the addition of a default implementation in an interface to break an existing class that implements that interface when compiled by an older compiler.</span></span> <span data-ttu-id="149df-257">Cela peut affecter la représentation des métadonnées que nous pouvons utiliser.</span><span class="sxs-lookup"><span data-stu-id="149df-257">This may affect the metadata representation that we can use.</span></span>
- <span data-ttu-id="149df-258">La conception doit prendre en compte l’interopérabilité avec d’autres langages et des compilateurs existants pour d’autres langages.</span><span class="sxs-lookup"><span data-stu-id="149df-258">The design must consider interoperation with other languages and existing compilers for other languages.</span></span>

## <a name="resolved-questions"></a><span data-ttu-id="149df-259">Questions résolues</span><span class="sxs-lookup"><span data-stu-id="149df-259">Resolved Questions</span></span>

### <a name="abstract-override"></a><span data-ttu-id="149df-260">Remplacement abstrait</span><span class="sxs-lookup"><span data-stu-id="149df-260">Abstract Override</span></span>

<span data-ttu-id="149df-261">La spécification préliminaire précédente contenait la possibilité de « reabstract » une méthode héritée :</span><span class="sxs-lookup"><span data-stu-id="149df-261">The earlier draft spec contained the ability to "reabstract" an inherited method:</span></span>

```csharp
interface IA
{
    void M();
}
interface IB : IA
{
    override void M() { }
}
interface IC : IB
{
    override void M(); // make it abstract again
}
```

<span data-ttu-id="149df-262">Mes notes pour 2017-03-20 ont montré que nous avons décidé de ne pas les autoriser.</span><span class="sxs-lookup"><span data-stu-id="149df-262">My notes for 2017-03-20 showed that we decided not to allow this.</span></span> <span data-ttu-id="149df-263">Toutefois, il existe au moins deux cas d’utilisation :</span><span class="sxs-lookup"><span data-stu-id="149df-263">However, there are at least two use cases for it:</span></span>

1. <span data-ttu-id="149df-264">Les API Java, avec lesquelles certains utilisateurs de cette fonctionnalité espèrent interagir, dépendent de cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="149df-264">The Java APIs, with which some users of this feature hope to interoperate, depend on this facility.</span></span>
2. <span data-ttu-id="149df-265">La programmation avec les *caractéristiques* tire parti de cette.</span><span class="sxs-lookup"><span data-stu-id="149df-265">Programming with *traits* benefits from this.</span></span> <span data-ttu-id="149df-266">La réabstraction est l’un des éléments de la fonctionnalité de langage « traits » (https://en.wikipedia.org/wiki/Trait_(computer_programming)).</span><span class="sxs-lookup"><span data-stu-id="149df-266">Reabstraction is one of the elements of the "traits" language feature (https://en.wikipedia.org/wiki/Trait_(computer_programming)).</span></span> <span data-ttu-id="149df-267">Les éléments suivants sont autorisés avec les classes :</span><span class="sxs-lookup"><span data-stu-id="149df-267">The following is permitted with classes:</span></span>

```csharp
public abstract class Base
{
    public abstract void M();
}
public abstract class A : Base
{
    public override void M() { }
}
public abstract class B : A
{
    public override abstract void M(); // reabstract Base.M
}
```

<span data-ttu-id="149df-268">Malheureusement, ce code ne peut pas être refactorisé comme un ensemble d’interfaces (traits) à moins que cela ne soit autorisé.</span><span class="sxs-lookup"><span data-stu-id="149df-268">Unfortunately this code cannot be refactored as a set of interfaces (traits) unless this is permitted.</span></span> <span data-ttu-id="149df-269">Le *principe Jared de cupidité*doit être autorisé.</span><span class="sxs-lookup"><span data-stu-id="149df-269">By the *Jared principle of greed*, it should be permitted.</span></span>

> <span data-ttu-id="149df-270">***Problème fermé :*** La réabstraction doit-elle être autorisée ?</span><span class="sxs-lookup"><span data-stu-id="149df-270">***Closed issue:*** Should reabstraction be permitted?</span></span> <span data-ttu-id="149df-271">Oui Mes notes sont incorrectes.</span><span class="sxs-lookup"><span data-stu-id="149df-271">[YES] My notes were wrong.</span></span> <span data-ttu-id="149df-272">Les [Notes LDM](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-21.md) indiquent que la réabstraction est autorisée dans une interface.</span><span class="sxs-lookup"><span data-stu-id="149df-272">The [LDM notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-21.md) say that reabstraction is permitted in an interface.</span></span> <span data-ttu-id="149df-273">Pas dans une classe.</span><span class="sxs-lookup"><span data-stu-id="149df-273">Not in a class.</span></span>

### <a name="virtual-modifier-vs-sealed-modifier"></a><span data-ttu-id="149df-274">Modificateur virtuel et modificateur sealed</span><span class="sxs-lookup"><span data-stu-id="149df-274">Virtual Modifier vs Sealed Modifier</span></span>

<span data-ttu-id="149df-275">À partir de [Aleksey Tsingauz](https://github.com/AlekseyTs):</span><span class="sxs-lookup"><span data-stu-id="149df-275">From [Aleksey Tsingauz](https://github.com/AlekseyTs):</span></span>

> <span data-ttu-id="149df-276">Nous avons décidé d’autoriser les modificateurs explicitement indiqués sur les membres d’interface, à moins qu’il y ait une raison d’en interdire certains.</span><span class="sxs-lookup"><span data-stu-id="149df-276">We decided to allow modifiers explicitly stated on interface members, unless there is a reason to disallow some of them.</span></span> <span data-ttu-id="149df-277">Cela pose une question intéressante concernant le modificateur virtuel.</span><span class="sxs-lookup"><span data-stu-id="149df-277">This brings an interesting question around virtual modifier.</span></span> <span data-ttu-id="149df-278">Doit-il être requis sur les membres avec l’implémentation par défaut ?</span><span class="sxs-lookup"><span data-stu-id="149df-278">Should it be required on members with default implementation?</span></span>
>
> <span data-ttu-id="149df-279">Nous pouvons affirmer que :</span><span class="sxs-lookup"><span data-stu-id="149df-279">We could say that:</span></span>
>
> - <span data-ttu-id="149df-280">s’il n’existe aucune implémentation et que ni Virtual, ni sealed ne sont spécifiés, nous supposons que le membre est abstrait.</span><span class="sxs-lookup"><span data-stu-id="149df-280">if there is no implementation and neither virtual, nor sealed are specified, we assume the member is abstract.</span></span>
> - <span data-ttu-id="149df-281">s’il existe une implémentation et que ni abstract, ni sealed ne sont spécifiés, nous supposons que le membre est virtuel.</span><span class="sxs-lookup"><span data-stu-id="149df-281">if there is an implementation and neither abstract, nor sealed are specified, we assume the member is virtual.</span></span>
> - <span data-ttu-id="149df-282">un modificateur sealed est requis pour rendre une méthode non virtuelle ou abstraite.</span><span class="sxs-lookup"><span data-stu-id="149df-282">sealed modifier is required to make a method neither virtual, nor abstract.</span></span>
>
> <span data-ttu-id="149df-283">Vous pouvez également indiquer que le modificateur virtuel est requis pour un membre virtuel.</span><span class="sxs-lookup"><span data-stu-id="149df-283">Alternatively, we could say that virtual modifier is required for a virtual member.</span></span> <span data-ttu-id="149df-284">Par exemple, s’il existe un membre dont l’implémentation n’est pas explicitement marquée avec le modificateur Virtual, il n’est ni virtuel ni abstrait.</span><span class="sxs-lookup"><span data-stu-id="149df-284">I.e, if there is a member with implementation not explicitly marked with virtual modifier, it is neither virtual, nor abstract.</span></span> <span data-ttu-id="149df-285">Cette approche peut offrir une meilleure expérience quand une méthode est déplacée d’une classe vers une interface :</span><span class="sxs-lookup"><span data-stu-id="149df-285">This approach might provide better experience when a method is moved from a class to an interface:</span></span>
>
> - <span data-ttu-id="149df-286">une méthode abstraite reste abstraite.</span><span class="sxs-lookup"><span data-stu-id="149df-286">an abstract method stays abstract.</span></span>
> - <span data-ttu-id="149df-287">une méthode virtuelle reste virtuelle.</span><span class="sxs-lookup"><span data-stu-id="149df-287">a virtual method stays virtual.</span></span>
> - <span data-ttu-id="149df-288">une méthode sans modificateur ne reste ni virtuelle, ni abstraite.</span><span class="sxs-lookup"><span data-stu-id="149df-288">a method without any modifier stays neither virtual, nor abstract.</span></span>
> - <span data-ttu-id="149df-289">un modificateur sealed ne peut pas être appliqué à une méthode qui n’est pas une substitution.</span><span class="sxs-lookup"><span data-stu-id="149df-289">sealed modifier cannot be applied to a method that is not an override.</span></span>
>
> <span data-ttu-id="149df-290">Qu'en penses-tu?</span><span class="sxs-lookup"><span data-stu-id="149df-290">What do you think?</span></span>

> <span data-ttu-id="149df-291">***Problème fermé :*** Une méthode concrète (avec implémentation) doit-elle être implicitement `virtual`?</span><span class="sxs-lookup"><span data-stu-id="149df-291">***Closed Issue:*** Should a concrete method (with implementation) be implicitly `virtual`?</span></span> <span data-ttu-id="149df-292">Oui</span><span class="sxs-lookup"><span data-stu-id="149df-292">[YES]</span></span>

<span data-ttu-id="149df-293">***Décisions :*** Créé dans le LDM 2017-04-05 :</span><span class="sxs-lookup"><span data-stu-id="149df-293">***Decisions:*** Made in the LDM 2017-04-05:</span></span>

1. <span data-ttu-id="149df-294">les non virtuelles doivent être exprimées explicitement par `sealed` ou `private`.</span><span class="sxs-lookup"><span data-stu-id="149df-294">non-virtual should be explicitly expressed through `sealed` or `private`.</span></span>
2. <span data-ttu-id="149df-295">`sealed` est le mot clé pour rendre les membres d’instance d’interface avec des corps non virtuels</span><span class="sxs-lookup"><span data-stu-id="149df-295">`sealed` is the keyword to make interface instance members with bodies non-virtual</span></span>
3. <span data-ttu-id="149df-296">Nous souhaitons autoriser tous les modificateurs dans les interfaces</span><span class="sxs-lookup"><span data-stu-id="149df-296">We want to allow all modifiers in interfaces</span></span>  
4. <span data-ttu-id="149df-297">L’accessibilité par défaut pour les membres d’interface est publique, y compris les types imbriqués</span><span class="sxs-lookup"><span data-stu-id="149df-297">Default accessibility for interface members is public, including nested types</span></span>
5. <span data-ttu-id="149df-298">les fonctions membres privées dans les interfaces sont implicitement sealed et `sealed` ne sont pas autorisées sur elles.</span><span class="sxs-lookup"><span data-stu-id="149df-298">private function members in interfaces are implicitly sealed, and `sealed` is not permitted on them.</span></span>
6. <span data-ttu-id="149df-299">Les classes privées (dans les interfaces) sont autorisées et peuvent être sealed, ce qui signifie qu’elles sont scellées dans le sens de la classe de Sealed.</span><span class="sxs-lookup"><span data-stu-id="149df-299">Private classes (in interfaces) are permitted and can be sealed, and that means sealed in the class sense of sealed.</span></span>
7. <span data-ttu-id="149df-300">L’absence d’une bonne proposition, partielle n’est toujours pas autorisée sur les interfaces ou leurs membres.</span><span class="sxs-lookup"><span data-stu-id="149df-300">Absent a good proposal, partial is still not allowed on interfaces or their members.</span></span>

### <a name="binary-compatibility-1"></a><span data-ttu-id="149df-301">Compatibilité binaire 1</span><span class="sxs-lookup"><span data-stu-id="149df-301">Binary Compatibility 1</span></span>

<span data-ttu-id="149df-302">Quand une bibliothèque fournit une implémentation par défaut</span><span class="sxs-lookup"><span data-stu-id="149df-302">When a library provides a default implementation</span></span>

```csharp
interface I1
{
    void M() { Impl1 }
}
interface I2 : I1
{
}
class C : I2
{
}
```

<span data-ttu-id="149df-303">Nous savons que l’implémentation de `I1.M` dans `C` est `I1.M`.</span><span class="sxs-lookup"><span data-stu-id="149df-303">We understand that the implementation of `I1.M` in `C` is `I1.M`.</span></span> <span data-ttu-id="149df-304">Que se passe-vous si l’assembly contenant `I2` est modifié comme suit et recompilé</span><span class="sxs-lookup"><span data-stu-id="149df-304">What if the assembly containing `I2` is changed as follows and recompiled</span></span>

```csharp
interface I2 : I1
{
    override void M() { Impl2 }
}
```

<span data-ttu-id="149df-305">mais `C` n’est pas recompilée.</span><span class="sxs-lookup"><span data-stu-id="149df-305">but `C` is not recompiled.</span></span> <span data-ttu-id="149df-306">Que se passe-t-il lorsque le programme est exécuté ?</span><span class="sxs-lookup"><span data-stu-id="149df-306">What happens when the program is run?</span></span> <span data-ttu-id="149df-307">Un appel de `(C as I1).M()`</span><span class="sxs-lookup"><span data-stu-id="149df-307">An invocation of `(C as I1).M()`</span></span>

1. <span data-ttu-id="149df-308">S’exécute `I1.M`</span><span class="sxs-lookup"><span data-stu-id="149df-308">Runs `I1.M`</span></span>
2. <span data-ttu-id="149df-309">S’exécute `I2.M`</span><span class="sxs-lookup"><span data-stu-id="149df-309">Runs `I2.M`</span></span>
3. <span data-ttu-id="149df-310">Lève un certain type d’erreur d’exécution</span><span class="sxs-lookup"><span data-stu-id="149df-310">Throws some kind of runtime error</span></span>

<span data-ttu-id="149df-311">***Décision :*** Rendu 2017-04-11 : s’exécute `I2.M`, ce qui correspond à la substitution la plus spécifique non ambiguë au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="149df-311">***Decision:*** Made 2017-04-11: Runs `I2.M`, which is the unambiguously most specific override at runtime.</span></span>

### <a name="event-accessors-closed"></a><span data-ttu-id="149df-312">Accesseurs d’événement (fermé)</span><span class="sxs-lookup"><span data-stu-id="149df-312">Event accessors (closed)</span></span>

> <span data-ttu-id="149df-313">***Problème fermé :*** Un événement peut-il être remplacé par « par morceaux » ?</span><span class="sxs-lookup"><span data-stu-id="149df-313">***Closed Issue:*** Can an event be overridden "piecewise"?</span></span>

<span data-ttu-id="149df-314">Prenons le cas suivant :</span><span class="sxs-lookup"><span data-stu-id="149df-314">Consider this case:</span></span>

```csharp
public interface I1
{
    event T e1;
}
public interface I2 : I1
{
    override event T
    {
        add { }
        // error: "remove" accessor missing
    }
}
```

<span data-ttu-id="149df-315">Cette implémentation « partielle » de l’événement n’est pas autorisée, car, comme dans une classe, la syntaxe d’une déclaration d’événement n’autorise qu’un seul accesseur ; les deux (ou aucune) doivent être fournis.</span><span class="sxs-lookup"><span data-stu-id="149df-315">This "partial" implementation of the event is not permitted because, as in a class, the syntax for an event declaration does not permit only one accessor; both (or neither) must be provided.</span></span> <span data-ttu-id="149df-316">Vous pourriez accomplir la même chose en autorisant l’accesseur remove abstrait dans la syntaxe à être implicitement abstrait par l’absence d’un corps :</span><span class="sxs-lookup"><span data-stu-id="149df-316">You could accomplish the same thing by permitting the abstract remove accessor in the syntax to be implicitly abstract by the absence of a body:</span></span>

```csharp
public interface I1
{
    event T e1;
}
public interface I2 : I1
{
    override event T
    {
        add { }
        remove; // implicitly abstract
    }
}
```

<span data-ttu-id="149df-317">Notez qu' *il s’agit d’une nouvelle syntaxe (proposée)* .</span><span class="sxs-lookup"><span data-stu-id="149df-317">Note that *this is a new (proposed) syntax*.</span></span> <span data-ttu-id="149df-318">Dans la grammaire actuelle, les accesseurs d’événement ont un corps obligatoire.</span><span class="sxs-lookup"><span data-stu-id="149df-318">In the current grammar, event accessors have a mandatory body.</span></span>

> <span data-ttu-id="149df-319">***Problème fermé :*** Un accesseur d’événement peut-il être (implicitement) abstrait par l’omission d’un corps, de la même façon que les méthodes des accesseurs d’interfaces et de propriétés sont abstraites (implicitement) par l’omission d’un corps ?</span><span class="sxs-lookup"><span data-stu-id="149df-319">***Closed Issue:*** Can an event accessor be (implicitly) abstract by the omission of a body, similarly to the way that methods in interfaces and property accessors are (implicitly) abstract by the omission of a body?</span></span>

<span data-ttu-id="149df-320">***Décision :*** (2017-04-18) non, les déclarations d’événements requièrent des accesseurs concrets (ou aucun).</span><span class="sxs-lookup"><span data-stu-id="149df-320">***Decision:*** (2017-04-18) No, event declarations require both concrete accessors (or neither).</span></span>

### <a name="reabstraction-in-a-class-closed"></a><span data-ttu-id="149df-321">Réabstraction dans une classe (fermée)</span><span class="sxs-lookup"><span data-stu-id="149df-321">Reabstraction in a Class (closed)</span></span>

<span data-ttu-id="149df-322">***Problème fermé :*** Nous devons confirmer que cela est autorisé (sinon, l’ajout d’une implémentation par défaut serait une modification avec rupture) :</span><span class="sxs-lookup"><span data-stu-id="149df-322">***Closed Issue:*** We should confirm that this is permitted (otherwise adding a default implementation would be a breaking change):</span></span>

```csharp
interface I1
{
    void M() { }
}
abstract class C : I1
{
    public abstract void M(); // implement I1.M with an abstract method in C
}
```

<span data-ttu-id="149df-323">***Décision :*** (2017-04-18) Oui, l’ajout d’un corps à une déclaration de membre d’interface ne doit pas rompre la valeur C.</span><span class="sxs-lookup"><span data-stu-id="149df-323">***Decision:*** (2017-04-18) Yes, adding a body to an interface member declaration shouldn't break C.</span></span>

### <a name="sealed-override-closed"></a><span data-ttu-id="149df-324">Remplacement sealed (fermé)</span><span class="sxs-lookup"><span data-stu-id="149df-324">Sealed Override (closed)</span></span>

<span data-ttu-id="149df-325">La question précédente suppose implicitement que le modificateur `sealed` peut être appliqué à un `override` dans une interface.</span><span class="sxs-lookup"><span data-stu-id="149df-325">The previous question implicitly assumes that the `sealed` modifier can be applied to an `override` in an interface.</span></span> <span data-ttu-id="149df-326">Cela contredit la spécification brouillon.</span><span class="sxs-lookup"><span data-stu-id="149df-326">This contradicts the draft specification.</span></span> <span data-ttu-id="149df-327">Voulez-vous autoriser le scellement d’un remplacement ?</span><span class="sxs-lookup"><span data-stu-id="149df-327">Do we want to permit sealing an override?</span></span> <span data-ttu-id="149df-328">Les effets de la compatibilité source et binaire de la fermeture doivent être pris en compte.</span><span class="sxs-lookup"><span data-stu-id="149df-328">Source and binary compatibility effects of sealing should be considered.</span></span>

> <span data-ttu-id="149df-329">***Problème fermé :*** Faut-il autoriser le scellement d’un remplacement ?</span><span class="sxs-lookup"><span data-stu-id="149df-329">***Closed Issue:*** Should we permit sealing an override?</span></span>

<span data-ttu-id="149df-330">***Décision :*** (2017-04-18) n’est pas autorisé `sealed` sur les remplacements dans les interfaces.</span><span class="sxs-lookup"><span data-stu-id="149df-330">***Decision:*** (2017-04-18) Let's not allowed `sealed` on overrides in interfaces.</span></span> <span data-ttu-id="149df-331">La seule utilisation de `sealed` sur les membres d’interface consiste à les rendre non virtuelles dans leur déclaration initiale.</span><span class="sxs-lookup"><span data-stu-id="149df-331">The only use of `sealed` on interface members is to make them non-virtual in their initial declaration.</span></span>

### <a name="diamond-inheritance-and-classes-closed"></a><span data-ttu-id="149df-332">Héritage et classes Diamond (fermé)</span><span class="sxs-lookup"><span data-stu-id="149df-332">Diamond inheritance and classes (closed)</span></span>

<span data-ttu-id="149df-333">Le brouillon de la proposition préfère les remplacements de classe aux remplacements d’interface dans les scénarios d’héritage Diamond :</span><span class="sxs-lookup"><span data-stu-id="149df-333">The draft of the proposal prefers class overrides to interface overrides in diamond inheritance scenarios:</span></span>

> <span data-ttu-id="149df-334">Nous avons besoin que chaque interface et classe aient une *substitution la plus spécifique* pour chaque méthode d’interface parmi les substitutions apparaissant dans le type ou ses interfaces directes et indirectes.</span><span class="sxs-lookup"><span data-stu-id="149df-334">We require that every interface and class have a *most specific override* for every interface method among the overrides appearing in the type or its direct and indirect interfaces.</span></span> <span data-ttu-id="149df-335">La *substitution la plus spécifique* est un remplacement unique qui est plus spécifique que tous les autres remplacements.</span><span class="sxs-lookup"><span data-stu-id="149df-335">The *most specific override* is a unique override that is more specific than every other override.</span></span> <span data-ttu-id="149df-336">S’il n’y a pas de substitution, la méthode elle-même est considérée comme la substitution la plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="149df-336">If there is no override, the method itself is considered the most specific override.</span></span>
>
> <span data-ttu-id="149df-337">Une `M1` de remplacement est considérée comme *plus spécifique* qu’une autre substitution `M2` si `M1` est déclarée sur le type `T1`, `M2` est déclarée sur le type `T2`et soit</span><span class="sxs-lookup"><span data-stu-id="149df-337">One override `M1` is considered *more specific* than another override `M2` if `M1` is declared on type `T1`, `M2` is declared on type `T2`, and either</span></span>
>
> 1. <span data-ttu-id="149df-338">`T1` contient des `T2` parmi ses interfaces directes ou indirectes, ou</span><span class="sxs-lookup"><span data-stu-id="149df-338">`T1` contains `T2` among its direct or indirect interfaces, or</span></span>
> 2. <span data-ttu-id="149df-339">`T2` est un type d’interface, mais `T1` n’est pas un type d’interface.</span><span class="sxs-lookup"><span data-stu-id="149df-339">`T2` is an interface type but `T1` is not an interface type.</span></span>

<span data-ttu-id="149df-340">Le scénario est le</span><span class="sxs-lookup"><span data-stu-id="149df-340">The scenario is this</span></span>

```csharp
interface IA
{
    void M();
}
interface IB : IA
{
    override void M() { WriteLine("IB"); }
}
class Base : IA
{
    void IA.M() { WriteLine("Base"); }
}
class Derived : Base, IB // allowed?
{
    static void Main()
    {
        Ia a = new Derived();
        a.M();           // what does it do?
    }
}
```

<span data-ttu-id="149df-341">Nous devons confirmer ce comportement (ou décider autrement)</span><span class="sxs-lookup"><span data-stu-id="149df-341">We should confirm this behavior (or decide otherwise)</span></span>

> <span data-ttu-id="149df-342">***Problème fermé :*** Confirmez la spécification préliminaire, ci-dessus, pour *la substitution la plus spécifique* , telle qu’elle s’applique aux classes et interfaces mixtes (une classe est prioritaire sur une interface).</span><span class="sxs-lookup"><span data-stu-id="149df-342">***Closed Issue:*** Confirm the draft spec, above, for *most specific override* as it applies to mixed classes and interfaces (a class takes priority over an interface).</span></span> <span data-ttu-id="149df-343">Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#diamonds-with-classes>.</span><span class="sxs-lookup"><span data-stu-id="149df-343">See <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#diamonds-with-classes>.</span></span>

### <a name="interface-methods-vs-structs-closed"></a><span data-ttu-id="149df-344">Méthodes d’interface et structs (fermé)</span><span class="sxs-lookup"><span data-stu-id="149df-344">Interface methods vs structs (closed)</span></span>

<span data-ttu-id="149df-345">Il existe des interactions fâcheuses entre les méthodes et structs de l’interface par défaut.</span><span class="sxs-lookup"><span data-stu-id="149df-345">There are some unfortunate interactions between default interface methods and structs.</span></span>

```csharp
interface IA
{
    public void M() { }
}
struct S : IA
{
}
```

<span data-ttu-id="149df-346">Notez que les membres d’interface ne sont pas hérités :</span><span class="sxs-lookup"><span data-stu-id="149df-346">Note that interface members are not inherited:</span></span>

```csharp
var s = default(S);
s.M(); // error: 'S' does not contain a member 'M'
```

<span data-ttu-id="149df-347">Par conséquent, le client doit Box le struct pour appeler des méthodes d’interface</span><span class="sxs-lookup"><span data-stu-id="149df-347">Consequently, the client must box the struct to invoke interface methods</span></span>

```csharp
IA s = default(S); // an S, boxed
s.M(); // ok
```

<span data-ttu-id="149df-348">De cette façon, le boxing est à l’encontre des principaux avantages d’un type de `struct`.</span><span class="sxs-lookup"><span data-stu-id="149df-348">Boxing in this way defeats the principal benefits of a `struct` type.</span></span> <span data-ttu-id="149df-349">En outre, toutes les méthodes de mutation n’auront aucun effet apparent, car elles fonctionnent sur une *copie boxed* du struct :</span><span class="sxs-lookup"><span data-stu-id="149df-349">Moreover, any mutation methods will have no apparent effect, because they are operating on a *boxed copy* of the struct:</span></span>

```csharp
interface IB
{
    public void Increment() { P += 1; }
    public int P { get; set; }
}
struct T : IB
{
    public int P { get; set; } // auto-property
}

T t = default(T);
Console.WriteLine(t.P); // prints 0
(t as IB).Increment();
Console.WriteLine(t.P); // prints 0
```

> <span data-ttu-id="149df-350">***Problème fermé :*** Que pouvons-nous faire pour cela :</span><span class="sxs-lookup"><span data-stu-id="149df-350">***Closed Issue:*** What can we do about this:</span></span>
>
> 1. <span data-ttu-id="149df-351">Interdire à un `struct` d’hériter d’une implémentation par défaut.</span><span class="sxs-lookup"><span data-stu-id="149df-351">Forbid a `struct` from inheriting a default implementation.</span></span> <span data-ttu-id="149df-352">Toutes les méthodes d’interface seraient traitées comme abstraites dans une `struct`.</span><span class="sxs-lookup"><span data-stu-id="149df-352">All interface methods would be treated as abstract in a `struct`.</span></span> <span data-ttu-id="149df-353">Ensuite, nous pouvons prendre du temps ultérieurement pour décider comment améliorer son fonctionnement.</span><span class="sxs-lookup"><span data-stu-id="149df-353">Then we may take time later to decide how to make it work better.</span></span>
> 2. <span data-ttu-id="149df-354">Trouvez un type de stratégie de génération de code qui évite la conversion boxing.</span><span class="sxs-lookup"><span data-stu-id="149df-354">Come up with some kind of code generation strategy that avoids boxing.</span></span> <span data-ttu-id="149df-355">À l’intérieur d’une méthode comme `IB.Increment`, le type de `this` serait peut-être similaire à un paramètre de type restreint à `IB`.</span><span class="sxs-lookup"><span data-stu-id="149df-355">Inside a method like `IB.Increment`, the type of `this` would perhaps be akin to a type parameter constrained to `IB`.</span></span> <span data-ttu-id="149df-356">En conjonction avec cela, pour éviter le boxing dans l’appelant, les méthodes non abstraites sont héritées des interfaces.</span><span class="sxs-lookup"><span data-stu-id="149df-356">In conjunction with that, to avoid boxing in the caller, non-abstract methods would be inherited from interfaces.</span></span> <span data-ttu-id="149df-357">Cela peut augmenter considérablement le fonctionnement du compilateur et de l’implémentation du CLR.</span><span class="sxs-lookup"><span data-stu-id="149df-357">This may increase compiler and CLR implementation work substantially.</span></span>
> 3. <span data-ttu-id="149df-358">Ne vous inquiétez pas et ne vous contentez pas de gênant.</span><span class="sxs-lookup"><span data-stu-id="149df-358">Not worry about it and just leave it as a wart.</span></span>
> 4. <span data-ttu-id="149df-359">Autres idées ?</span><span class="sxs-lookup"><span data-stu-id="149df-359">Other ideas?</span></span>

<span data-ttu-id="149df-360">***Décision :*** Ne vous inquiétez pas et ne vous contentez pas de gênant.</span><span class="sxs-lookup"><span data-stu-id="149df-360">***Decision:*** Not worry about it and just leave it as a wart.</span></span> <span data-ttu-id="149df-361">Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#structs-and-default-implementations>.</span><span class="sxs-lookup"><span data-stu-id="149df-361">See <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#structs-and-default-implementations>.</span></span>

### <a name="base-interface-invocations-closed"></a><span data-ttu-id="149df-362">Appels de l’interface de base (fermé)</span><span class="sxs-lookup"><span data-stu-id="149df-362">Base interface invocations (closed)</span></span>

<span data-ttu-id="149df-363">Le brouillon de spécifications suggère une syntaxe pour les appels de l’interface de base inspirée par Java : `Interface.base.M()`.</span><span class="sxs-lookup"><span data-stu-id="149df-363">The draft spec suggests a syntax for base interface invocations inspired by Java: `Interface.base.M()`.</span></span> <span data-ttu-id="149df-364">Nous devons sélectionner une syntaxe, au moins pour le prototype initial.</span><span class="sxs-lookup"><span data-stu-id="149df-364">We need to select a syntax, at least for the initial prototype.</span></span> <span data-ttu-id="149df-365">Mon favori est `base<Interface>.M()`.</span><span class="sxs-lookup"><span data-stu-id="149df-365">My favorite is `base<Interface>.M()`.</span></span>

> <span data-ttu-id="149df-366">***Problème fermé :*** Quelle est la syntaxe d’un appel de membre de base ?</span><span class="sxs-lookup"><span data-stu-id="149df-366">***Closed Issue:*** What is the syntax for a base member invocation?</span></span>

<span data-ttu-id="149df-367">***Décision :*** La syntaxe est `base(Interface).M()`.</span><span class="sxs-lookup"><span data-stu-id="149df-367">***Decision:*** The syntax is `base(Interface).M()`.</span></span> <span data-ttu-id="149df-368">Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#base-invocation>.</span><span class="sxs-lookup"><span data-stu-id="149df-368">See <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#base-invocation>.</span></span> <span data-ttu-id="149df-369">L’interface ainsi nommée doit être une interface de base, mais elle n’a pas besoin d’être une interface de base directe.</span><span class="sxs-lookup"><span data-stu-id="149df-369">The interface so named must be a base interface, but does not need to be a direct base interface.</span></span>

> <span data-ttu-id="149df-370">***Problème d’ouverture :*** Les appels d’interface de base doivent-ils être autorisés dans les membres de classe ?</span><span class="sxs-lookup"><span data-stu-id="149df-370">***Open Issue:*** Should base interface invocations be permitted in class members?</span></span>

<span data-ttu-id="149df-371">***Décision***: Oui.</span><span class="sxs-lookup"><span data-stu-id="149df-371">***Decision***: Yes.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#base-invocation>

### <a name="overriding-non-public-interface-members-closed"></a><span data-ttu-id="149df-372">Substitution de membres d’interface non publics (fermés)</span><span class="sxs-lookup"><span data-stu-id="149df-372">Overriding non-public interface members (closed)</span></span>

<span data-ttu-id="149df-373">Dans une interface, les membres non publics des interfaces de base sont substitués à l’aide du modificateur `override`.</span><span class="sxs-lookup"><span data-stu-id="149df-373">In an interface, non-public members from base interfaces are overridden using the `override` modifier.</span></span> <span data-ttu-id="149df-374">S’il s’agit d’une substitution « explicite » qui nomme l’interface contenant le membre, le modificateur d’accès est omis.</span><span class="sxs-lookup"><span data-stu-id="149df-374">If it is an "explicit" override that names the interface containing the member, the access modifier is omitted.</span></span>

> <span data-ttu-id="149df-375">***Problème fermé :*** S’il s’agit d’une substitution « implicite » qui ne nomme pas l’interface, le modificateur d’accès doit-il correspondre ?</span><span class="sxs-lookup"><span data-stu-id="149df-375">***Closed Issue:*** If it is an "implicit" override that does not name the interface, does the access modifier have to match?</span></span>

<span data-ttu-id="149df-376">***Décision :*** Seuls les membres publics peuvent être substitués implicitement, et l’accès doit correspondre.</span><span class="sxs-lookup"><span data-stu-id="149df-376">***Decision:*** Only public members may be implicitly overridden, and the access must match.</span></span> <span data-ttu-id="149df-377">Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md#dim-implementing-a-non-public-interface-member-not-in-list>.</span><span class="sxs-lookup"><span data-stu-id="149df-377">See <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md#dim-implementing-a-non-public-interface-member-not-in-list>.</span></span>

> <span data-ttu-id="149df-378">***Problème d’ouverture :*** Le modificateur d’accès est-il requis, facultatif ou omis sur une substitution explicite, telle que `override void IB.M() {}`?</span><span class="sxs-lookup"><span data-stu-id="149df-378">***Open Issue:*** Is the access modifier required, optional, or omitted on an explicit override such as `override void IB.M() {}`?</span></span>

> <span data-ttu-id="149df-379">***Problème d’ouverture :*** La `override` est-elle obligatoire, facultative ou omise sur un remplacement explicite, tel que `void IB.M() {}`?</span><span class="sxs-lookup"><span data-stu-id="149df-379">***Open Issue:*** Is `override` required, optional, or omitted on an explicit override such as `void IB.M() {}`?</span></span>

<span data-ttu-id="149df-380">Comment l’un implémente-t-il un membre d’interface non public dans une classe ?</span><span class="sxs-lookup"><span data-stu-id="149df-380">How does one implement a non-public interface member in a class?</span></span> <span data-ttu-id="149df-381">Peut-être devez-vous le faire explicitement ?</span><span class="sxs-lookup"><span data-stu-id="149df-381">Perhaps it must be done explicitly?</span></span>

```csharp
interface IA
{
    internal void MI();
    protected void MP();
}
class C : IA
{
    // are these implementations?
    internal void MI() {}
    protected void MP() {}
}
```

> <span data-ttu-id="149df-382">***Problème fermé :*** Comment l’un implémente-t-il un membre d’interface non public dans une classe ?</span><span class="sxs-lookup"><span data-stu-id="149df-382">***Closed Issue:*** How does one implement a non-public interface member in a class?</span></span>

<span data-ttu-id="149df-383">***Décision :*** Vous pouvez uniquement implémenter des membres d’interface non publics explicitement.</span><span class="sxs-lookup"><span data-stu-id="149df-383">***Decision:*** You can only implement non-public interface members explicitly.</span></span> <span data-ttu-id="149df-384">Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md#dim-implementing-a-non-public-interface-member-not-in-list>.</span><span class="sxs-lookup"><span data-stu-id="149df-384">See <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md#dim-implementing-a-non-public-interface-member-not-in-list>.</span></span>

<span data-ttu-id="149df-385">***Décision***: aucun mot clé `override` autorisé sur les membres d’interface.</span><span class="sxs-lookup"><span data-stu-id="149df-385">***Decision***: No `override` keyword permitted on interface members.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#does-an-override-in-an-interface-introduce-a-new-member>

### <a name="binary-compatibility-2-closed"></a><span data-ttu-id="149df-386">Compatibilité binaire 2 (fermée)</span><span class="sxs-lookup"><span data-stu-id="149df-386">Binary Compatibility 2 (closed)</span></span>

<span data-ttu-id="149df-387">Considérez le code suivant dans lequel chaque type se trouve dans un assembly distinct.</span><span class="sxs-lookup"><span data-stu-id="149df-387">Consider the following code in which each type is in a separate assembly</span></span>

```csharp
interface I1
{
    void M() { Impl1 }
}
interface I2 : I1
{
    override void M() { Impl2 }
}
interface I3 : I1
{
}
class C : I2, I3
{
}
```

<span data-ttu-id="149df-388">Nous savons que l’implémentation de `I1.M` dans `C` est `I2.M`.</span><span class="sxs-lookup"><span data-stu-id="149df-388">We understand that the implementation of `I1.M` in `C` is `I2.M`.</span></span> <span data-ttu-id="149df-389">Que se passe-vous si l’assembly contenant `I3` est modifié comme suit et recompilé</span><span class="sxs-lookup"><span data-stu-id="149df-389">What if the assembly containing `I3` is changed as follows and recompiled</span></span>

```csharp
interface I3 : I1
{
    override void M() { Impl3 }
}
```

<span data-ttu-id="149df-390">mais `C` n’est pas recompilée.</span><span class="sxs-lookup"><span data-stu-id="149df-390">but `C` is not recompiled.</span></span> <span data-ttu-id="149df-391">Que se passe-t-il lorsque le programme est exécuté ?</span><span class="sxs-lookup"><span data-stu-id="149df-391">What happens when the program is run?</span></span> <span data-ttu-id="149df-392">Un appel de `(C as I1).M()`</span><span class="sxs-lookup"><span data-stu-id="149df-392">An invocation of `(C as I1).M()`</span></span>

1. <span data-ttu-id="149df-393">S’exécute `I1.M`</span><span class="sxs-lookup"><span data-stu-id="149df-393">Runs `I1.M`</span></span>
2. <span data-ttu-id="149df-394">S’exécute `I2.M`</span><span class="sxs-lookup"><span data-stu-id="149df-394">Runs `I2.M`</span></span>
3. <span data-ttu-id="149df-395">S’exécute `I3.M`</span><span class="sxs-lookup"><span data-stu-id="149df-395">Runs `I3.M`</span></span>
4. <span data-ttu-id="149df-396">2 ou 3, déterministe</span><span class="sxs-lookup"><span data-stu-id="149df-396">Either 2 or 3, deterministically</span></span>
5. <span data-ttu-id="149df-397">Lève un type d’exception d’exécution</span><span class="sxs-lookup"><span data-stu-id="149df-397">Throws some kind of runtime exception</span></span>

<span data-ttu-id="149df-398">***Décision***: levez une exception (5).</span><span class="sxs-lookup"><span data-stu-id="149df-398">***Decision***: Throw an exception (5).</span></span> <span data-ttu-id="149df-399">Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#issues-in-default-interface-methods>.</span><span class="sxs-lookup"><span data-stu-id="149df-399">See <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#issues-in-default-interface-methods>.</span></span>

### <a name="permit-partial-in-interface-closed"></a><span data-ttu-id="149df-400">Autoriser `partial` dans l’interface ?</span><span class="sxs-lookup"><span data-stu-id="149df-400">Permit `partial` in interface?</span></span> <span data-ttu-id="149df-401">légendes</span><span class="sxs-lookup"><span data-stu-id="149df-401">(closed)</span></span>

<span data-ttu-id="149df-402">Étant donné que les interfaces peuvent être utilisées de manière analogue à la façon dont les classes abstraites sont utilisées, il peut être utile de les déclarer `partial`.</span><span class="sxs-lookup"><span data-stu-id="149df-402">Given that interfaces may be used in ways analogous to the way abstract classes are used, it may be useful to declare them `partial`.</span></span> <span data-ttu-id="149df-403">Cela serait particulièrement utile pour les générateurs.</span><span class="sxs-lookup"><span data-stu-id="149df-403">This would be particularly useful in the face of generators.</span></span>

> <span data-ttu-id="149df-404">***Proposition :*** Supprimez la restriction de langue pour laquelle les interfaces et les membres d’interfaces ne peuvent pas être déclarés `partial`.</span><span class="sxs-lookup"><span data-stu-id="149df-404">***Proposal:*** Remove the language restriction that interfaces and members of interfaces may not be declared `partial`.</span></span>

<span data-ttu-id="149df-405">***Décision***: Oui.</span><span class="sxs-lookup"><span data-stu-id="149df-405">***Decision***: Yes.</span></span> <span data-ttu-id="149df-406">Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#permit-partial-in-interface>.</span><span class="sxs-lookup"><span data-stu-id="149df-406">See <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#permit-partial-in-interface>.</span></span>

### <a name="main-in-an-interface-closed"></a><span data-ttu-id="149df-407">`Main` dans une interface ?</span><span class="sxs-lookup"><span data-stu-id="149df-407">`Main` in an interface?</span></span> <span data-ttu-id="149df-408">légendes</span><span class="sxs-lookup"><span data-stu-id="149df-408">(closed)</span></span>

> <span data-ttu-id="149df-409">***Problème d’ouverture :*** Une méthode `static Main` dans une interface peut-elle être le point d’entrée du programme ?</span><span class="sxs-lookup"><span data-stu-id="149df-409">***Open Issue:*** Is a `static Main` method in an interface a candidate to be the program's entry point?</span></span>

<span data-ttu-id="149df-410">***Décision***: Oui.</span><span class="sxs-lookup"><span data-stu-id="149df-410">***Decision***: Yes.</span></span> <span data-ttu-id="149df-411">Consultez <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#main-in-an-interface>.</span><span class="sxs-lookup"><span data-stu-id="149df-411">See <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#main-in-an-interface>.</span></span>

### <a name="confirm-intent-to-support-public-non-virtual-methods-closed"></a><span data-ttu-id="149df-412">Confirmer l’intention de prendre en charge les méthodes publiques non virtuelles (fermées)</span><span class="sxs-lookup"><span data-stu-id="149df-412">Confirm intent to support public non-virtual methods (closed)</span></span>

<span data-ttu-id="149df-413">Pouvons-nous confirmer (ou inverser) notre décision d’autoriser les méthodes publiques non virtuelles dans une interface ?</span><span class="sxs-lookup"><span data-stu-id="149df-413">Can we please confirm (or reverse) our decision to permit non-virtual public methods in an interface?</span></span>

```csharp
interface IA
{
    public sealed void M() { }
}
```

> <span data-ttu-id="149df-414">***Problème semi-fermé :*** (2017-04-18) nous pensons qu’il va être utile, mais y revenir.</span><span class="sxs-lookup"><span data-stu-id="149df-414">***Semi-Closed Issue:*** (2017-04-18) We think it is going to be useful, but will come back to it.</span></span> <span data-ttu-id="149df-415">Il s’agit d’un bloc de déclenchement de modèle mental.</span><span class="sxs-lookup"><span data-stu-id="149df-415">This is a mental model tripping block.</span></span>

<span data-ttu-id="149df-416">***Décision***: Oui.</span><span class="sxs-lookup"><span data-stu-id="149df-416">***Decision***: Yes.</span></span> <span data-ttu-id="149df-417"><https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#confirm-that-we-support-public-non-virtual-methods>.</span><span class="sxs-lookup"><span data-stu-id="149df-417"><https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#confirm-that-we-support-public-non-virtual-methods>.</span></span>

### <a name="does-an-override-in-an-interface-introduce-a-new-member-closed"></a><span data-ttu-id="149df-418">Un `override` dans une interface présente-t-il un nouveau membre ?</span><span class="sxs-lookup"><span data-stu-id="149df-418">Does an `override` in an interface introduce a new member?</span></span> <span data-ttu-id="149df-419">légendes</span><span class="sxs-lookup"><span data-stu-id="149df-419">(closed)</span></span>

<span data-ttu-id="149df-420">Il existe plusieurs façons d’observer si une déclaration de remplacement introduit un nouveau membre ou non.</span><span class="sxs-lookup"><span data-stu-id="149df-420">There are a few ways to observe whether an override declaration introduces a new member or not.</span></span>

```csharp
interface IA
{
    void M(int x) { }
}
interface IB : IA
{
    override void M(int y) { }
}
interface IC : IB
{
    static void M2()
    {
        M(y: 3); // permitted?
    }
    override void IB.M(int z) { } // permitted? What does it override?
}
```

> <span data-ttu-id="149df-421">***Problème d’ouverture :*** Une déclaration override dans une interface présente-t-elle un nouveau membre ?</span><span class="sxs-lookup"><span data-stu-id="149df-421">***Open Issue:*** Does an override declaration in an interface introduce a new member?</span></span> <span data-ttu-id="149df-422">légendes</span><span class="sxs-lookup"><span data-stu-id="149df-422">(closed)</span></span>

<span data-ttu-id="149df-423">Dans une classe, une méthode de substitution est « visible » dans certains sens.</span><span class="sxs-lookup"><span data-stu-id="149df-423">In a class, an overriding method is "visible" in some senses.</span></span> <span data-ttu-id="149df-424">Par exemple, les noms de ses paramètres ont la priorité sur les noms des paramètres dans la méthode substituée.</span><span class="sxs-lookup"><span data-stu-id="149df-424">For example, the names of its parameters take precedence over the names of parameters in the overridden method.</span></span> <span data-ttu-id="149df-425">Il peut être possible de dupliquer ce comportement dans les interfaces, car il y a toujours une substitution la plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="149df-425">It may be possible to duplicate that behavior in interfaces, as there is always a most specific override.</span></span> <span data-ttu-id="149df-426">Mais voulez-vous dupliquer ce comportement ?</span><span class="sxs-lookup"><span data-stu-id="149df-426">But do we want to duplicate that behavior?</span></span>

<span data-ttu-id="149df-427">Il est également possible de « remplacer » une méthode override ?</span><span class="sxs-lookup"><span data-stu-id="149df-427">Also, it is possible to "override" an override method?</span></span> <span data-ttu-id="149df-428">Discutable</span><span class="sxs-lookup"><span data-stu-id="149df-428">[Moot]</span></span>

<span data-ttu-id="149df-429">***Décision***: aucun mot clé `override` autorisé sur les membres d’interface.</span><span class="sxs-lookup"><span data-stu-id="149df-429">***Decision***: No `override` keyword permitted on interface members.</span></span> <span data-ttu-id="149df-430"><https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#does-an-override-in-an-interface-introduce-a-new-member>.</span><span class="sxs-lookup"><span data-stu-id="149df-430"><https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#does-an-override-in-an-interface-introduce-a-new-member>.</span></span>

### <a name="properties-with-a-private-accessor-closed"></a><span data-ttu-id="149df-431">Propriétés avec un accesseur private (fermé)</span><span class="sxs-lookup"><span data-stu-id="149df-431">Properties with a private accessor (closed)</span></span>

<span data-ttu-id="149df-432">Nous disons que les membres privés ne sont pas virtuels et que la combinaison de Virtual et Private est interdite.</span><span class="sxs-lookup"><span data-stu-id="149df-432">We say that private members are not virtual, and the combination of virtual and private is disallowed.</span></span> <span data-ttu-id="149df-433">Mais qu’en est-il d’une propriété avec un accesseur private ?</span><span class="sxs-lookup"><span data-stu-id="149df-433">But what about a property with a private accessor?</span></span>

```csharp
interface IA
{
    public virtual int P
    {
        get => 3;
        private set => { }
    }
}
```

<span data-ttu-id="149df-434">Est-ce autorisé ?</span><span class="sxs-lookup"><span data-stu-id="149df-434">Is this allowed?</span></span> <span data-ttu-id="149df-435">L’accesseur `set` est-il `virtual` ou non ?</span><span class="sxs-lookup"><span data-stu-id="149df-435">Is the `set` accessor here `virtual` or not?</span></span> <span data-ttu-id="149df-436">Peut-il être substitué là où il est accessible ?</span><span class="sxs-lookup"><span data-stu-id="149df-436">Can it be overridden where it is accessible?</span></span> <span data-ttu-id="149df-437">Les éléments suivants implémentent-ils implicitement uniquement l’accesseur `get` ?</span><span class="sxs-lookup"><span data-stu-id="149df-437">Does the following implicitly implement only the `get` accessor?</span></span>

```csharp
class C : IA
{
    public int P
    {
        get => 4;
        set { }
    }
}
```

<span data-ttu-id="149df-438">Est probablement une erreur due à l’IA. P. Set n’est pas virtuel et aussi parce qu’il n’est pas accessible ?</span><span class="sxs-lookup"><span data-stu-id="149df-438">Is the following presumably an error because IA.P.set isn't virtual and also because it isn't accessible?</span></span>

```csharp
class C : IA
{
    int IA.P
    {
        get => 4;
        set { }
    }
}
```

<span data-ttu-id="149df-439">***Décision***: le premier exemple semble valide, tandis que le dernier ne le fait pas.</span><span class="sxs-lookup"><span data-stu-id="149df-439">***Decision***: The first example looks valid, while the last does not.</span></span> <span data-ttu-id="149df-440">Cela est résolu de façon similaire à la façon dont il C#fonctionne déjà dans.</span><span class="sxs-lookup"><span data-stu-id="149df-440">This is resolved analogously to how it already works in C#.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#properties-with-a-private-accessor>

### <a name="base-interface-invocations-round-2-closed"></a><span data-ttu-id="149df-441">Appels de l’interface de base, arrondi 2 (fermé)</span><span class="sxs-lookup"><span data-stu-id="149df-441">Base Interface Invocations, round 2 (closed)</span></span>

<span data-ttu-id="149df-442">Notre « résolution » précédente à la manière de gérer les appels de base n’offre pas en fait suffisamment d’expressivité.</span><span class="sxs-lookup"><span data-stu-id="149df-442">Our previous "resolution" to how to handle base invocations doesn't actually provide sufficient expressiveness.</span></span> <span data-ttu-id="149df-443">Il s’avère que dans C# et le CLR, contrairement à Java, vous devez spécifier à la fois l’interface contenant la déclaration de méthode et l’emplacement de l’implémentation que vous souhaitez appeler.</span><span class="sxs-lookup"><span data-stu-id="149df-443">It turns out that in C# and the CLR, unlike Java, you need to specify both the interface containing the method declaration and the location of the implementation you want to invoke.</span></span>

<span data-ttu-id="149df-444">Je propose la syntaxe suivante pour les appels de base dans les interfaces.</span><span class="sxs-lookup"><span data-stu-id="149df-444">I propose the following syntax for base calls in interfaces.</span></span> <span data-ttu-id="149df-445">Je n’aime pas, mais il illustre ce que toute syntaxe doit pouvoir exprimer :</span><span class="sxs-lookup"><span data-stu-id="149df-445">I’m not in love with it, but it illustrates what any syntax must be able to express:</span></span>

```csharp
interface I1 { void M(); }
interface I2 { void M(); }
interface I3 : I1, I2 { void I1.M() { } void I2.M() { } }
interface I4 : I1, I2 { void I1.M() { } void I2.M() { } }
interface I5 : I3, I4
{
    void I1.M()
    {
        base<I3>(I1).M(); // calls I3's implementation of I1.M
        base<I4>(I1).M(); // calls I4's implementation of I1.M
    }
    void I2.M()
    {
        base<I3>(I2).M(); // calls I3's implementation of I2.M
        base<I4>(I2).M(); // calls I4's implementation of I2.M
    }
}
```

<span data-ttu-id="149df-446">S’il n’y a pas d’ambiguïté, vous pouvez l’écrire plus simplement</span><span class="sxs-lookup"><span data-stu-id="149df-446">If there is no ambiguity, you can write it more simply</span></span>

```csharp
interface I1 { void M(); }
interface I3 : I1 { void I1.M() { } }
interface I4 : I1 { void I1.M() { } }
interface I5 : I3, I4
{
    void I1.M()
    {
        base<I3>.M(); // calls I3's implementation of I1.M
        base<I4>.M(); // calls I4's implementation of I1.M
    }
}
```

<span data-ttu-id="149df-447">ou</span><span class="sxs-lookup"><span data-stu-id="149df-447">Or</span></span>

```csharp
interface I1 { void M(); }
interface I2 { void M(); }
interface I3 : I1, I2 { void I1.M() { } void I2.M() { } }
interface I5 : I3
{
    void I1.M()
    {
        base(I1).M(); // calls I3's implementation of I1.M
    }
    void I2.M()
    {
        base(I2).M(); // calls I3's implementation of I2.M
    }
}
```

<span data-ttu-id="149df-448">ou</span><span class="sxs-lookup"><span data-stu-id="149df-448">Or</span></span>

```csharp
interface I1 { void M(); }
interface I3 : I1 { void I1.M() { } }
interface I5 : I3
{
    void I1.M()
    {
        base.M(); // calls I3's implementation of I1.M
    }
}
```

<span data-ttu-id="149df-449">***Décision***: décidée sur `base(N.I1<T>).M(s)`, en concédant que si nous avons une liaison d’appel, il peut y avoir un problème plus tard.</span><span class="sxs-lookup"><span data-stu-id="149df-449">***Decision***: Decided on `base(N.I1<T>).M(s)`, conceding that if we have an invocation binding there may be problem here later on.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-11-14.md#default-interface-implementations>

### <a name="warning-for-struct-not-implementing-default-method-closed"></a><span data-ttu-id="149df-450">Avertissement indiquant que la structure n’implémente pas la méthode par défaut ?</span><span class="sxs-lookup"><span data-stu-id="149df-450">Warning for struct not implementing default method?</span></span> <span data-ttu-id="149df-451">légendes</span><span class="sxs-lookup"><span data-stu-id="149df-451">(closed)</span></span>

<span data-ttu-id="149df-452">@vancem déclare que nous devrions sérieusement envisager de générer un avertissement si une déclaration de type de valeur ne peut pas substituer une méthode d’interface, même si elle hérite d’une implémentation de cette méthode à partir d’une interface.</span><span class="sxs-lookup"><span data-stu-id="149df-452">@vancem asserts that we should seriously consider producing a warning if a value type declaration fails to override some interface method, even if it would inherit an implementation of that method from an interface.</span></span> <span data-ttu-id="149df-453">Dans la mesure où elle provoque des appels de conversion boxing et de sous-mines.</span><span class="sxs-lookup"><span data-stu-id="149df-453">Because it causes boxing and undermines constrained calls.</span></span>

<span data-ttu-id="149df-454">***Décision***: cela semble plus adapté à un analyseur.</span><span class="sxs-lookup"><span data-stu-id="149df-454">***Decision***: This seems like something more suited for an analyzer.</span></span> <span data-ttu-id="149df-455">Il semble également que cet avertissement soit bruyant, car il se déclenche même si la méthode d’interface par défaut n’est jamais appelée et qu’il n’y aura jamais de boxing.</span><span class="sxs-lookup"><span data-stu-id="149df-455">It also seems like this warning could be noisy, since it would fire even if the default interface method is never called and no boxing will ever occur.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#warning-for-struct-not-implementing-default-method>

### <a name="interface-static-constructors-closed"></a><span data-ttu-id="149df-456">Constructeurs statiques d’interface (fermés)</span><span class="sxs-lookup"><span data-stu-id="149df-456">Interface static constructors (closed)</span></span>

<span data-ttu-id="149df-457">Quand les constructeurs statiques d’interface s’exécutent-ils ?</span><span class="sxs-lookup"><span data-stu-id="149df-457">When are interface static constructors run?</span></span>  <span data-ttu-id="149df-458">La version actuelle de l’interface CLI suggère qu’elle se produit lors de l’accès à la première méthode ou au champ statique.</span><span class="sxs-lookup"><span data-stu-id="149df-458">The current CLI draft proposes that it occurs when the first static method or field is accessed.</span></span> <span data-ttu-id="149df-459">S’il n’y en a pas, il n’est peut-être jamais exécuté ?</span><span class="sxs-lookup"><span data-stu-id="149df-459">If there are neither of those then it might never be run??</span></span>

<span data-ttu-id="149df-460">[2018-10-09 l’équipe CLR propose « va mettre en miroir ce que nous faisons pour les points de contrôle de l’accès à chaque méthode d’instance) »]</span><span class="sxs-lookup"><span data-stu-id="149df-460">[2018-10-09 The CLR team proposes "Going to mirror what we do for valuetypes (cctor check on access to each instance method)"]</span></span>

<span data-ttu-id="149df-461">***Décision***: les constructeurs statiques sont également exécutés à l’entrée dans des méthodes d’instance, si le constructeur statique n’a pas été `beforefieldinit`, auquel cas les constructeurs statiques sont exécutés avant l’accès au premier champ statique.</span><span class="sxs-lookup"><span data-stu-id="149df-461">***Decision***: Static constructors are also run on entry to instance methods, if the static constructor was not `beforefieldinit`, in which case static constructors are run before access to the first static field.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#when-are-interface-static-constructors-run>

## <a name="design-meetings"></a><span data-ttu-id="149df-462">Réunions de conception</span><span class="sxs-lookup"><span data-stu-id="149df-462">Design meetings</span></span>

<span data-ttu-id="149df-463">[2017-03-08 notes de réunion ldm](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-08.md)
[2017-03-21 notes de réunion LDM](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-21.md)
[2017-03-23 Réunion "comportement CLR pour les méthodes d’interface par défaut"](https://github.com/dotnet/csharplang/blob/master/meetings/2017/CLR-2017-03-23.md)
[notes de réunion LDM 2017-04-05](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-05.md)
[2017-04-11 notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-11.md) de réunion LDM
2017-04-18 notes de [réunion LDM
](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md) 2017-04-19 [notes de réunion](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md) du ldm
2017-05-17 [Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-17.md) de réunion LDM
2017-05-31 [Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-31.md) de réunion LDM
[2017-06-14 LDM Notes de réunion
](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-14.md) [2018-10-17 notes de réunion ldm](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
[2018-11-14 Notes de réunion LDM](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-11-14.md)</span><span class="sxs-lookup"><span data-stu-id="149df-463">[2017-03-08 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-08.md)
[2017-03-21 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-21.md)
[2017-03-23 meeting "CLR Behavior for Default Interface Methods"](https://github.com/dotnet/csharplang/blob/master/meetings/2017/CLR-2017-03-23.md)
[2017-04-05 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-05.md)
[2017-04-11 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-11.md)
[2017-04-18 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md)
[2017-04-19 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md)
[2017-05-17 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-17.md)
[2017-05-31 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-31.md)
[2017-06-14 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-14.md)
[2018-10-17 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
[2018-11-14 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-11-14.md)</span></span>
