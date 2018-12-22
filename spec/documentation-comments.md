# <a name="documentation-comments"></a><span data-ttu-id="209bb-101">Commentaires de documentation</span><span class="sxs-lookup"><span data-stu-id="209bb-101">Documentation comments</span></span>

<span data-ttu-id="209bb-102">C# fournit un mécanisme pour les programmeurs de documenter leur code à l’aide d’une syntaxe de commentaire spéciale qui contient le texte XML.</span><span class="sxs-lookup"><span data-stu-id="209bb-102">C# provides a mechanism for programmers to document their code using a special comment syntax that contains XML text.</span></span> <span data-ttu-id="209bb-103">Dans les fichiers de code source, les commentaires d’un format particulier peuvent être utilisés pour diriger un outil pour produire du code XML à partir de ces commentaires et les éléments de code source, ils précèdent.</span><span class="sxs-lookup"><span data-stu-id="209bb-103">In source code files, comments having a certain form can be used to direct a tool to produce XML from those comments and the source code elements, which they precede.</span></span> <span data-ttu-id="209bb-104">Des commentaires à l’aide de ce type de syntaxe sont appelés ***les commentaires de documentation***.</span><span class="sxs-lookup"><span data-stu-id="209bb-104">Comments using such syntax are called ***documentation comments***.</span></span> <span data-ttu-id="209bb-105">Il doivent précéder immédiatement un type défini par l’utilisateur (par exemple, une classe, un délégué ou une interface) ou un membre (par exemple, un champ, événement, propriété ou méthode).</span><span class="sxs-lookup"><span data-stu-id="209bb-105">They must immediately precede a user-defined type (such as a class, delegate, or interface) or a member (such as a field, event, property, or method).</span></span> <span data-ttu-id="209bb-106">L’outil de génération XML est appelé le ***Générateur de documentation***.</span><span class="sxs-lookup"><span data-stu-id="209bb-106">The XML generation tool is called the ***documentation generator***.</span></span> <span data-ttu-id="209bb-107">(Ce générateur peut être, mais ne sont pas nécessairement, le compilateur c# lui-même). La sortie produite par le Générateur de documentation est appelée le ***fichier de documentation***.</span><span class="sxs-lookup"><span data-stu-id="209bb-107">(This generator could be, but need not be, the C# compiler itself.) The output produced by the documentation generator is called the ***documentation file***.</span></span> <span data-ttu-id="209bb-108">Un fichier de documentation est utilisé comme entrée pour un ***visionneuse de documentation***; un outil destiné à produire une sorte de visualisation des informations de type et sa documentation associée.</span><span class="sxs-lookup"><span data-stu-id="209bb-108">A documentation file is used as input to a ***documentation viewer***; a tool intended to produce some sort of visual display of type information and its associated documentation.</span></span>

<span data-ttu-id="209bb-109">Cette spécification suggère un ensemble de balises à utiliser dans les commentaires de documentation, mais l’utilisation de ces balises n’est pas nécessaire et autres balises peuvent être utilisées si vous le souhaitez, comme la durée pendant laquelle les règles de code XML bien formé sont suivies.</span><span class="sxs-lookup"><span data-stu-id="209bb-109">This specification suggests a set of tags to be used in documentation comments, but use of these tags is not required, and other tags may be used if desired, as long the rules of well-formed XML are followed.</span></span>

## <a name="introduction"></a><span data-ttu-id="209bb-110">Introduction</span><span class="sxs-lookup"><span data-stu-id="209bb-110">Introduction</span></span>

<span data-ttu-id="209bb-111">Des commentaires d’une forme spéciale peuvent être utilisés pour diriger un outil pour produire du code XML à partir de ces commentaires et les éléments de code source, ils précèdent.</span><span class="sxs-lookup"><span data-stu-id="209bb-111">Comments having a special form can be used to direct a tool to produce XML from those comments and the source code elements, which they precede.</span></span> <span data-ttu-id="209bb-112">Ces commentaires sont des commentaires à ligne unique qui commencent par trois barres obliques (`///`), ou délimité par des commentaires qui commencent par une barre oblique et deux étoiles (`/**`).</span><span class="sxs-lookup"><span data-stu-id="209bb-112">Such comments are single-line comments that start with three slashes (`///`), or delimited comments that start with a slash and two stars (`/**`).</span></span> <span data-ttu-id="209bb-113">Il doivent précéder immédiatement un type défini par l’utilisateur (par exemple, une classe, un délégué ou une interface) ou un membre (par exemple, un champ, événement, propriété ou méthode) qu’elles annotent.</span><span class="sxs-lookup"><span data-stu-id="209bb-113">They must immediately precede a user-defined type (such as a class, delegate, or interface) or a member (such as a field, event, property, or method) that they annotate.</span></span> <span data-ttu-id="209bb-114">Attribut de sections ([spécification des attributs](attributes.md#attribute-specification)) font partie des déclarations, donc les commentaires de documentation doivent précéder les attributs appliqués à un type ou membre.</span><span class="sxs-lookup"><span data-stu-id="209bb-114">Attribute sections ([Attribute specification](attributes.md#attribute-specification)) are considered part of declarations, so documentation comments must precede attributes applied to a type or member.</span></span>

<span data-ttu-id="209bb-115">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-115">__Syntax:__</span></span>

```antlr
single_line_doc_comment
    : '///' input_character*
    ;

delimited_doc_comment
    : '/**' delimited_comment_section* asterisk+ '/'
    ;
```

<span data-ttu-id="209bb-116">Dans un *single_line_doc_comment*, s’il existe un *espace blanc* caractère qui suit le `///` caractères sur chacun de la *single_line_doc_comment*s adjacents actuel *single_line_doc_comment*, puis qui *espace blanc* caractère n’est pas inclus dans la sortie XML.</span><span class="sxs-lookup"><span data-stu-id="209bb-116">In a *single_line_doc_comment*, if there is a *whitespace* character following the `///` characters on each of the *single_line_doc_comment*s adjacent to the current *single_line_doc_comment*, then that *whitespace* character is not included in the XML output.</span></span>

<span data-ttu-id="209bb-117">Dans un commentaire-doc-délimités, si le premier caractère non-espace blanc sur la deuxième ligne est un astérisque et le même modèle de caractères d’espace blanc facultatif et un astérisque est répétée au début de chaque ligne dans le commentaire-doc-délimités, puis les caractères de la série répétée ne sont pas inclus dans la sortie XML.</span><span class="sxs-lookup"><span data-stu-id="209bb-117">In a delimited-doc-comment, if the first non-whitespace character on the second line is an asterisk and the same pattern of optional whitespace characters and an asterisk character is repeated at the beginning of each of the line within the delimited-doc-comment, then the characters of the repeated pattern are not included in the XML output.</span></span> <span data-ttu-id="209bb-118">Le modèle peut inclure des caractères d’espace blanc après, et avant le caractère astérisque.</span><span class="sxs-lookup"><span data-stu-id="209bb-118">The pattern may include whitespace characters after, as well as before, the asterisk character.</span></span>

<span data-ttu-id="209bb-119">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-119">__Example:__</span></span>

```csharp
/// <summary>Class <c>Point</c> models a point in a two-dimensional
/// plane.</summary>
///
public class Point 
{
    /// <summary>method <c>draw</c> renders the point.</summary>
    void draw() {...}
}
```

<span data-ttu-id="209bb-120">Le texte dans les commentaires de documentation doit être correctement formé conformément aux règles de XML (http://www.w3.org/TR/REC-xml).</span><span class="sxs-lookup"><span data-stu-id="209bb-120">The text within documentation comments must be well formed according to the rules of XML (http://www.w3.org/TR/REC-xml).</span></span> <span data-ttu-id="209bb-121">Si le code XML est incorrect, un avertissement est généré et le fichier de documentation contiendra un commentaire indiquant qu’une erreur s’est produite.</span><span class="sxs-lookup"><span data-stu-id="209bb-121">If the XML is ill formed, a warning is generated and the documentation file will contain a comment saying that an error was encountered.</span></span>

<span data-ttu-id="209bb-122">Bien que les développeurs sont libres de créer leur propre jeu de balises, un jeu recommandé est défini dans [balises recommandées](documentation-comments.md#recommended-tags).</span><span class="sxs-lookup"><span data-stu-id="209bb-122">Although developers are free to create their own set of tags, a recommended set is defined in [Recommended tags](documentation-comments.md#recommended-tags).</span></span> <span data-ttu-id="209bb-123">Certaines des balises recommandées ont des significations spéciales :</span><span class="sxs-lookup"><span data-stu-id="209bb-123">Some of the recommended tags have special meanings:</span></span>

*  <span data-ttu-id="209bb-124">Le `<param>` balise est utilisée pour décrire les paramètres.</span><span class="sxs-lookup"><span data-stu-id="209bb-124">The `<param>` tag is used to describe parameters.</span></span> <span data-ttu-id="209bb-125">Si une telle balise est utilisée, le Générateur de documentation doit vérifier que le paramètre spécifié existe et que tous les paramètres sont décrits dans les commentaires de documentation.</span><span class="sxs-lookup"><span data-stu-id="209bb-125">If such a tag is used, the documentation generator must verify that the specified parameter exists and that all parameters are described in documentation comments.</span></span> <span data-ttu-id="209bb-126">Si cette vérification échoue, le Générateur de documentation émet un avertissement.</span><span class="sxs-lookup"><span data-stu-id="209bb-126">If such verification fails, the documentation generator issues a warning.</span></span>
*  <span data-ttu-id="209bb-127">L’attribut `cref` peut être joint à n’importe quelle balise pour fournir une référence à un élément de code.</span><span class="sxs-lookup"><span data-stu-id="209bb-127">The `cref` attribute can be attached to any tag to provide a reference to a code element.</span></span> <span data-ttu-id="209bb-128">Le Générateur de documentation doit vérifier l’existence de cet élément de code.</span><span class="sxs-lookup"><span data-stu-id="209bb-128">The documentation generator must verify that this code element exists.</span></span> <span data-ttu-id="209bb-129">Si la vérification échoue, le Générateur de documentation émet un avertissement.</span><span class="sxs-lookup"><span data-stu-id="209bb-129">If the verification fails, the documentation generator issues a warning.</span></span> <span data-ttu-id="209bb-130">Lors de la recherche pour un nom décrit dans un `cref` attribut, le Générateur de documentation doit respecter la visibilité de l’espace de noms en fonction de `using` les instructions situées dans le code source.</span><span class="sxs-lookup"><span data-stu-id="209bb-130">When looking for a name described in a `cref` attribute, the documentation generator must respect namespace visibility according to `using` statements appearing within the source code.</span></span> <span data-ttu-id="209bb-131">Pour les éléments de code qui sont génériques, la syntaxe générique normale (c'est-à-dire «`List<T>`») ne peut pas être utilisé car il génère le code XML non valide.</span><span class="sxs-lookup"><span data-stu-id="209bb-131">For code elements that are generic, the normal generic syntax (ie "`List<T>`") cannot be used because it produces invalid XML.</span></span> <span data-ttu-id="209bb-132">Accolades peuvent être utilisées au lieu de crochets (ie «`List{T}`»), ou la syntaxe d’échappement XML peut être utilisée (ie «`List&lt;T&gt;`»).</span><span class="sxs-lookup"><span data-stu-id="209bb-132">Braces can be used instead of brackets (ie "`List{T}`"), or the XML escape syntax can be used (ie "`List&lt;T&gt;`").</span></span>
*  <span data-ttu-id="209bb-133">Le `<summary>` balise est destinée à être utilisée par une visionneuse de documentation pour afficher des informations supplémentaires sur un type ou membre.</span><span class="sxs-lookup"><span data-stu-id="209bb-133">The `<summary>` tag is intended to be used by a documentation viewer to display additional information about a type or member.</span></span>
*  <span data-ttu-id="209bb-134">Le `<include>` balise inclut des informations à partir d’un fichier XML externe.</span><span class="sxs-lookup"><span data-stu-id="209bb-134">The `<include>` tag includes information from an external XML file.</span></span>

<span data-ttu-id="209bb-135">Notez bien que le fichier de documentation ne fournit pas d’informations complètes sur le type et les membres (par exemple, il ne contient aucune information de type).</span><span class="sxs-lookup"><span data-stu-id="209bb-135">Note carefully that the documentation file does not provide full information about the type and members (for example, it does not contain any type information).</span></span> <span data-ttu-id="209bb-136">Pour obtenir des informations complètes sur un type ou membre, le fichier de documentation doit être utilisé conjointement avec la réflexion sur le type réel ou le membre.</span><span class="sxs-lookup"><span data-stu-id="209bb-136">To get such information about a type or member, the documentation file must be used in conjunction with reflection on the actual type or member.</span></span>

## <a name="recommended-tags"></a><span data-ttu-id="209bb-137">Balises recommandées</span><span class="sxs-lookup"><span data-stu-id="209bb-137">Recommended tags</span></span>

<span data-ttu-id="209bb-138">Le Générateur de documentation doit accepter et traiter n’importe quelle balise est valide selon les règles de XML.</span><span class="sxs-lookup"><span data-stu-id="209bb-138">The documentation generator must accept and process any tag that is valid according to the rules of XML.</span></span> <span data-ttu-id="209bb-139">Les balises suivantes fournissent des fonctionnalités couramment utilisées dans la documentation utilisateur.</span><span class="sxs-lookup"><span data-stu-id="209bb-139">The following tags provide commonly used functionality in user documentation.</span></span> <span data-ttu-id="209bb-140">(Bien entendu, autres balises sont possibles.)</span><span class="sxs-lookup"><span data-stu-id="209bb-140">(Of course, other tags are possible.)</span></span>


| <span data-ttu-id="209bb-141">__Balise__</span><span class="sxs-lookup"><span data-stu-id="209bb-141">__Tag__</span></span>          | <span data-ttu-id="209bb-142">__Section__</span><span class="sxs-lookup"><span data-stu-id="209bb-142">__Section__</span></span>                                            | <span data-ttu-id="209bb-143">__Fonction__</span><span class="sxs-lookup"><span data-stu-id="209bb-143">__Purpose__</span></span>                                            |
|------------------|--------------------------------------------------------|--------------------------------------------------------|
| `<c>`            | [`<c>`](documentation-comments.md#c)                   | <span data-ttu-id="209bb-144">Définir le texte dans une police de type code</span><span class="sxs-lookup"><span data-stu-id="209bb-144">Set text in a code-like font</span></span>                           | 
| `<code>`         | [`<code>`](documentation-comments.md#code)             | <span data-ttu-id="209bb-145">Définir une ou plusieurs lignes de sortie de code ou de programme source</span><span class="sxs-lookup"><span data-stu-id="209bb-145">Set one or more lines of source code or program output</span></span> |
| `<example>`      | [`<example>`](documentation-comments.md#example)       | <span data-ttu-id="209bb-146">Indiquer un exemple</span><span class="sxs-lookup"><span data-stu-id="209bb-146">Indicate an example</span></span>                                    |
| `<exception>`    | [`<exception>`](documentation-comments.md#exception)   | <span data-ttu-id="209bb-147">Identifie les exceptions qu'une méthode peut lever.</span><span class="sxs-lookup"><span data-stu-id="209bb-147">Identifies the exceptions a method can throw</span></span>           |
| `<include>`      | [`<include>`](documentation-comments.md#include)       | <span data-ttu-id="209bb-148">Inclut le code XML à partir d’un fichier externe</span><span class="sxs-lookup"><span data-stu-id="209bb-148">Includes XML from an external file</span></span>                     |
| `<list>`         | [`<list>`](documentation-comments.md#list)             | <span data-ttu-id="209bb-149">Créer une liste ou une table</span><span class="sxs-lookup"><span data-stu-id="209bb-149">Create a list or table</span></span>                                 |
| `<para>`         | [`<para>`](documentation-comments.md#para)             | <span data-ttu-id="209bb-150">Autoriser la structure à ajouter au texte</span><span class="sxs-lookup"><span data-stu-id="209bb-150">Permit structure to be added to text</span></span>                   |
| `<param>`        | [`<param>`](documentation-comments.md#param)           | <span data-ttu-id="209bb-151">Décrire un paramètre pour une méthode ou un constructeur</span><span class="sxs-lookup"><span data-stu-id="209bb-151">Describe a parameter for a method or constructor</span></span>       |
| `<paramref>`     | [`<paramref>`](documentation-comments.md#paramref)     | <span data-ttu-id="209bb-152">Déterminer si un mot est un nom de paramètre</span><span class="sxs-lookup"><span data-stu-id="209bb-152">Identify that a word is a parameter name</span></span>               |
| `<permission>`   | [`<permission>`](documentation-comments.md#permission) | <span data-ttu-id="209bb-153">L’accessibilité de sécurité d’un membre de document</span><span class="sxs-lookup"><span data-stu-id="209bb-153">Document the security accessibility of a member</span></span>        |
| `<remark>`       | [`<remark>`](documentation-comments.md#remark)         | <span data-ttu-id="209bb-154">Décrire des informations supplémentaires sur un type</span><span class="sxs-lookup"><span data-stu-id="209bb-154">Describe additional information about a type</span></span>           |
| `<returns>`      | [`<returns>`](documentation-comments.md#returns)       | <span data-ttu-id="209bb-155">Décrire la valeur de retour d’une méthode</span><span class="sxs-lookup"><span data-stu-id="209bb-155">Describe the return value of a method</span></span>                  |
| `<see>`          | [`<see>`](documentation-comments.md#see)               | <span data-ttu-id="209bb-156">Spécifier un lien</span><span class="sxs-lookup"><span data-stu-id="209bb-156">Specify a link</span></span>                                         |
| `<seealso>`      | [`<seealso>`](documentation-comments.md#seealso)       | <span data-ttu-id="209bb-157">Générer une entrée Voir aussi</span><span class="sxs-lookup"><span data-stu-id="209bb-157">Generate a See Also entry</span></span>                              |
| `<summary>`      | [`<summary>`](documentation-comments.md#summary)       | <span data-ttu-id="209bb-158">Décrire un type ou un membre d’un type</span><span class="sxs-lookup"><span data-stu-id="209bb-158">Describe a type or a member of a type</span></span>                  |
| `<value>`        | [`<value>`](documentation-comments.md#value)           | <span data-ttu-id="209bb-159">Décrire une propriété</span><span class="sxs-lookup"><span data-stu-id="209bb-159">Describe a property</span></span>                                    |
| `<typeparam>`    |                                                        | <span data-ttu-id="209bb-160">Décrire un paramètre de type générique</span><span class="sxs-lookup"><span data-stu-id="209bb-160">Describe a generic type parameter</span></span>                      |
| `<typeparamref>` |                                                        | <span data-ttu-id="209bb-161">Déterminer si un mot est un nom de paramètre de type</span><span class="sxs-lookup"><span data-stu-id="209bb-161">Identify that a word is a type parameter name</span></span>          |

### `<c>`

<span data-ttu-id="209bb-162">Cette balise fournit un mécanisme pour indiquer qu’un fragment de texte dans une description doit être défini dans une police spéciale tel que celui utilisé pour un bloc de code.</span><span class="sxs-lookup"><span data-stu-id="209bb-162">This tag provides a mechanism to indicate that a fragment of text within a description should be set in a special font such as that used for a block of code.</span></span> <span data-ttu-id="209bb-163">Pour les lignes de code réel, utilisez `<code>` ([`<code>`](documentation-comments.md#code)).</span><span class="sxs-lookup"><span data-stu-id="209bb-163">For lines of actual code, use `<code>` ([`<code>`](documentation-comments.md#code)).</span></span>

<span data-ttu-id="209bb-164">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-164">__Syntax:__</span></span>

```xml
<c>text</c>
```

<span data-ttu-id="209bb-165">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-165">__Example:__</span></span>

```csharp
/// <summary>Class <c>Point</c> models a point in a two-dimensional
/// plane.</summary>

public class Point 
{
    // ...
}
```

### `<code>`

<span data-ttu-id="209bb-166">Cette balise est utilisée pour définir une ou plusieurs lignes de sortie de code ou le programme source dans une police particulière.</span><span class="sxs-lookup"><span data-stu-id="209bb-166">This tag is used to set one or more lines of source code or program output in some special font.</span></span> <span data-ttu-id="209bb-167">Pour les petits extraits de code dans la narration, utilisez `<c>` ([`<c>`](documentation-comments.md#c)).</span><span class="sxs-lookup"><span data-stu-id="209bb-167">For small code fragments in narrative, use `<c>` ([`<c>`](documentation-comments.md#c)).</span></span>

<span data-ttu-id="209bb-168">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-168">__Syntax:__</span></span>

```xml
<code>source code or program output</code>
```

<span data-ttu-id="209bb-169">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-169">__Example:__</span></span>

```csharp
/// <summary>This method changes the point's location by
///    the given x- and y-offsets.
/// <example>For example:
/// <code>
///    Point p = new Point(3,5);
///    p.Translate(-1,3);
/// </code>
/// results in <c>p</c>'s having the value (2,8).
/// </example>
/// </summary>

public void Translate(int xor, int yor) {
    X += xor;
    Y += yor;
}   
```

### `<example>`

<span data-ttu-id="209bb-170">Cette balise permet l’exemple de code dans un commentaire, pour spécifier comment une méthode ou autres membres de la bibliothèque peut être utilisée.</span><span class="sxs-lookup"><span data-stu-id="209bb-170">This tag allows example code within a comment, to specify how a method or other library member may be used.</span></span> <span data-ttu-id="209bb-171">En règle générale, cela implique également l’utilisation de la balise `<code>` ([`<code>`](documentation-comments.md#code)) également.</span><span class="sxs-lookup"><span data-stu-id="209bb-171">Ordinarily, this would also involve use of the tag `<code>` ([`<code>`](documentation-comments.md#code)) as well.</span></span>

<span data-ttu-id="209bb-172">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-172">__Syntax:__</span></span>

```xml
<example>description</example>
```

<span data-ttu-id="209bb-173">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-173">__Example:__</span></span>

<span data-ttu-id="209bb-174">Consultez `<code>` ([`<code>`](documentation-comments.md#code)) pour obtenir un exemple.</span><span class="sxs-lookup"><span data-stu-id="209bb-174">See `<code>` ([`<code>`](documentation-comments.md#code)) for an example.</span></span>

### `<exception>`

<span data-ttu-id="209bb-175">Cette balise permet de documenter les exceptions qu'une méthode peut lever.</span><span class="sxs-lookup"><span data-stu-id="209bb-175">This tag provides a way to document the exceptions a method can throw.</span></span>

<span data-ttu-id="209bb-176">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-176">__Syntax:__</span></span>

```xml
<exception cref="member">description</exception>
```

<span data-ttu-id="209bb-177">où</span><span class="sxs-lookup"><span data-stu-id="209bb-177">where</span></span>

* <span data-ttu-id="209bb-178">`member` est le nom d’un membre.</span><span class="sxs-lookup"><span data-stu-id="209bb-178">`member` is the name of a member.</span></span> <span data-ttu-id="209bb-179">Le Générateur de documentation vérifie que le membre donné existe et traduit `member` au nom d’élément canonique dans le fichier de documentation.</span><span class="sxs-lookup"><span data-stu-id="209bb-179">The documentation generator checks that the given member exists and translates `member` to the canonical element name in the documentation file.</span></span>
* <span data-ttu-id="209bb-180">`description` est une description des circonstances dans lesquelles l’exception est levée.</span><span class="sxs-lookup"><span data-stu-id="209bb-180">`description` is a description of the circumstances in which the exception is thrown.</span></span>

<span data-ttu-id="209bb-181">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-181">__Example:__</span></span>

```csharp
public class DataBaseOperations
{
    /// <exception cref="MasterFileFormatCorruptException"></exception>
    /// <exception cref="MasterFileLockedOpenException"></exception>
    public static void ReadRecord(int flag) {
        if (flag == 1)
            throw new MasterFileFormatCorruptException();
        else if (flag == 2)
            throw new MasterFileLockedOpenException();
        // ...
    } 
}
```

### `<include>`

<span data-ttu-id="209bb-182">Cette balise permet notamment d’informations à partir d’un document XML qui est externe au fichier de code source.</span><span class="sxs-lookup"><span data-stu-id="209bb-182">This tag allows including information from an XML document that is external to the source code file.</span></span> <span data-ttu-id="209bb-183">Le fichier externe doit être un document XML bien formé, et une expression XPath est appliquée à ce document pour spécifier quel code XML à partir de ce document à inclure.</span><span class="sxs-lookup"><span data-stu-id="209bb-183">The external file must be a well-formed XML document, and an XPath expression is applied to that document to specify what XML from that document to include.</span></span> <span data-ttu-id="209bb-184">Le `<include>` balise est ensuite remplacée par le XML à partir du document externe.</span><span class="sxs-lookup"><span data-stu-id="209bb-184">The `<include>` tag is then replaced with the selected XML from the external document.</span></span>

<span data-ttu-id="209bb-185">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-185">__Syntax:__</span></span>

```
<include file="filename" path="xpath" />
```

<span data-ttu-id="209bb-186">où</span><span class="sxs-lookup"><span data-stu-id="209bb-186">where</span></span>

* <span data-ttu-id="209bb-187">`filename` est le nom de fichier d’un fichier XML externe.</span><span class="sxs-lookup"><span data-stu-id="209bb-187">`filename` is the file name of an external XML file.</span></span> <span data-ttu-id="209bb-188">Le nom de fichier est interprété relatif au fichier qui contient la balise include.</span><span class="sxs-lookup"><span data-stu-id="209bb-188">The file name is interpreted relative to the file that contains the include tag.</span></span>
* <span data-ttu-id="209bb-189">`xpath` est une expression XPath qui sélectionne une partie du code XML dans le fichier XML externe.</span><span class="sxs-lookup"><span data-stu-id="209bb-189">`xpath` is an XPath expression that selects some of the XML in the external XML file.</span></span>

<span data-ttu-id="209bb-190">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-190">__Example:__</span></span>

<span data-ttu-id="209bb-191">Si le code source contenait une déclaration telle que :</span><span class="sxs-lookup"><span data-stu-id="209bb-191">If the source code contained a declaration like:</span></span>

```csharp
/// <include file="docs.xml" path='extradoc/class[@name="IntList"]/*' />
public class IntList { ... }
```

<span data-ttu-id="209bb-192">et le fichier externe « docs.xml » contenait avait le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="209bb-192">and the external file "docs.xml" had the following contents:</span></span>

```xml
<?xml version="1.0"?>
<extradoc>
  <class name="IntList">
     <summary>
        Contains a list of integers.
     </summary>
  </class>
  <class name="StringList">
     <summary>
        Contains a list of integers.
     </summary>
  </class>
</extradoc>
```

<span data-ttu-id="209bb-193">puis la documentation même est sortie comme si le code source contenu :</span><span class="sxs-lookup"><span data-stu-id="209bb-193">then the same documentation is output as if the source code contained:</span></span>

```csharp
/// <summary>
///    Contains a list of integers.
/// </summary>
public class IntList { ... }
```

### `<list>`

<span data-ttu-id="209bb-194">Cette balise est utilisée pour créer une liste ou un tableau d’éléments.</span><span class="sxs-lookup"><span data-stu-id="209bb-194">This tag is used to create a list or table of items.</span></span> <span data-ttu-id="209bb-195">Il peut contenir un `<listheader>` bloc pour définir la ligne d’en-tête d’une table ou une définition de liste.</span><span class="sxs-lookup"><span data-stu-id="209bb-195">It may contain a `<listheader>` block to define the heading row of either a table or definition list.</span></span> <span data-ttu-id="209bb-196">(Lors de la définition d’une table, une entrée pour `term` dans l’en-tête doivent être fournies.)</span><span class="sxs-lookup"><span data-stu-id="209bb-196">(When defining a table, only an entry for `term` in the heading need be supplied.)</span></span>

<span data-ttu-id="209bb-197">Chaque élément dans la liste est spécifié avec un `<item>` bloc.</span><span class="sxs-lookup"><span data-stu-id="209bb-197">Each item in the list is specified with an `<item>` block.</span></span> <span data-ttu-id="209bb-198">Lors de la création d’une liste de définitions, les deux `term` et `description` doit être spécifié.</span><span class="sxs-lookup"><span data-stu-id="209bb-198">When creating a definition list, both `term` and `description` must be specified.</span></span> <span data-ttu-id="209bb-199">Toutefois, pour une table, une liste à puces ou une liste numérotée `description` besoin d’être spécifiés.</span><span class="sxs-lookup"><span data-stu-id="209bb-199">However, for a table, bulleted list, or numbered list, only `description` need be specified.</span></span>

<span data-ttu-id="209bb-200">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-200">__Syntax:__</span></span>

```xml
<list type="bullet" | "number" | "table">
   <listheader>
      <term>term</term>
      <description>*description*</description>
   </listheader>
   <item>
      <term>term</term>
      <description>*description*</description>
   </item>
    ...
   <item>
      <term>term</term>
      <description>description</description>
   </item>
</list>
```

<span data-ttu-id="209bb-201">où</span><span class="sxs-lookup"><span data-stu-id="209bb-201">where</span></span>

* <span data-ttu-id="209bb-202">`term` est le terme à définir, dont la définition est dans `description`.</span><span class="sxs-lookup"><span data-stu-id="209bb-202">`term` is the term to define, whose definition is in `description`.</span></span>
* <span data-ttu-id="209bb-203">`description` est un élément dans une liste à puces ou une liste numérotée, ou la définition d’un `term`.</span><span class="sxs-lookup"><span data-stu-id="209bb-203">`description` is either an item in a bullet or numbered list, or the definition of a `term`.</span></span>

<span data-ttu-id="209bb-204">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-204">__Example:__</span></span>

```csharp
public class MyClass
{
    /// <summary>Here is an example of a bulleted list:
    /// <list type="bullet">
    /// <item>
    /// <description>Item 1.</description>
    /// </item>
    /// <item>
    /// <description>Item 2.</description>
    /// </item>
    /// </list>
    /// </summary>
    public static void Main () {
        // ...
    }
}
```

### `<para>`

<span data-ttu-id="209bb-205">Cette balise est pour une utilisation dans d’autres balises, tel que `<summary>` ([`<remark>`](documentation-comments.md#remark)) ou `<returns>` ([`<returns>`](documentation-comments.md#returns)) et autorise la structure à ajouter au texte.</span><span class="sxs-lookup"><span data-stu-id="209bb-205">This tag is for use inside other tags, such as `<summary>` ([`<remark>`](documentation-comments.md#remark)) or `<returns>` ([`<returns>`](documentation-comments.md#returns)), and permits structure to be added to text.</span></span>

<span data-ttu-id="209bb-206">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-206">__Syntax:__</span></span>

```xml
<para>content</para>
```

<span data-ttu-id="209bb-207">où `content` est le texte du paragraphe.</span><span class="sxs-lookup"><span data-stu-id="209bb-207">where `content` is the text of the paragraph.</span></span>

<span data-ttu-id="209bb-208">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-208">__Example:__</span></span>

```csharp
/// <summary>This is the entry point of the Point class testing program.
/// <para>This program tests each method and operator, and
/// is intended to be run after any non-trivial maintenance has
/// been performed on the Point class.</para></summary>
public static void Main() {
    // ...
}
```

### `<param>`

<span data-ttu-id="209bb-209">Cette balise est utilisée pour décrire un paramètre pour une méthode, un constructeur ou un indexeur.</span><span class="sxs-lookup"><span data-stu-id="209bb-209">This tag is used to describe a parameter for a method, constructor, or indexer.</span></span>

<span data-ttu-id="209bb-210">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-210">__Syntax:__</span></span>

```xml
<param name="name">description</param>
```

<span data-ttu-id="209bb-211">où</span><span class="sxs-lookup"><span data-stu-id="209bb-211">where</span></span>

* <span data-ttu-id="209bb-212">`name` Est le nom du paramètre.</span><span class="sxs-lookup"><span data-stu-id="209bb-212">`name` is the name of the parameter.</span></span>
* <span data-ttu-id="209bb-213">`description` est une description du paramètre.</span><span class="sxs-lookup"><span data-stu-id="209bb-213">`description` is a description of the parameter.</span></span>

<span data-ttu-id="209bb-214">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-214">__Example:__</span></span>

```csharp
/// <summary>This method changes the point's location to
///    the given coordinates.</summary>
/// <param name="xor">the new x-coordinate.</param>
/// <param name="yor">the new y-coordinate.</param>
public void Move(int xor, int yor) {
    X = xor;
    Y = yor;
}
```

### `<paramref>`

<span data-ttu-id="209bb-215">Cette balise est utilisée pour indiquer qu’un mot est un paramètre.</span><span class="sxs-lookup"><span data-stu-id="209bb-215">This tag is used to indicate that a word is a parameter.</span></span> <span data-ttu-id="209bb-216">Le fichier de documentation peut être traité pour mettre en forme ce paramètre d’une manière distincte.</span><span class="sxs-lookup"><span data-stu-id="209bb-216">The documentation file can be processed to format this parameter in some distinct way.</span></span>

<span data-ttu-id="209bb-217">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-217">__Syntax:__</span></span>

```xml
<paramref name="name"/>
```

<span data-ttu-id="209bb-218">où `name` est le nom du paramètre.</span><span class="sxs-lookup"><span data-stu-id="209bb-218">where `name` is the name of the parameter.</span></span>

<span data-ttu-id="209bb-219">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-219">__Example:__</span></span>

```csharp
/// <summary>This constructor initializes the new Point to
///    (<paramref name="xor"/>,<paramref name="yor"/>).</summary>
/// <param name="xor">the new Point's x-coordinate.</param>
/// <param name="yor">the new Point's y-coordinate.</param>

public Point(int xor, int yor) {
    X = xor;
    Y = yor;
}
```

### `<permission>`

<span data-ttu-id="209bb-220">Cette balise permet l’accessibilité de sécurité d’un membre à documenter.</span><span class="sxs-lookup"><span data-stu-id="209bb-220">This tag allows the security accessibility of a member to be documented.</span></span>

<span data-ttu-id="209bb-221">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-221">__Syntax:__</span></span>

```xml
<permission cref="member">description</permission>
```

<span data-ttu-id="209bb-222">où</span><span class="sxs-lookup"><span data-stu-id="209bb-222">where</span></span>

* <span data-ttu-id="209bb-223">`member` est le nom d’un membre.</span><span class="sxs-lookup"><span data-stu-id="209bb-223">`member` is the name of a member.</span></span> <span data-ttu-id="209bb-224">Le Générateur de documentation vérifie que l’élément de code donné existe et traduit *membre* au nom d’élément canonique dans le fichier de documentation.</span><span class="sxs-lookup"><span data-stu-id="209bb-224">The documentation generator checks that the given code element exists and translates *member* to the canonical element name in the documentation file.</span></span>
* <span data-ttu-id="209bb-225">`description` est une description de l’accès au membre.</span><span class="sxs-lookup"><span data-stu-id="209bb-225">`description` is a description of the access to the member.</span></span>

<span data-ttu-id="209bb-226">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-226">__Example:__</span></span>

```csharp
/// <permission cref="System.Security.PermissionSet">Everyone can
/// access this method.</permission>

public static void Test() {
    // ...
}
```

### `<remark>`

<span data-ttu-id="209bb-227">Cette balise est utilisée pour spécifier des informations supplémentaires sur un type.</span><span class="sxs-lookup"><span data-stu-id="209bb-227">This tag is used to specify extra information about a type.</span></span> <span data-ttu-id="209bb-228">(Utilisez `<summary>` ([`<summary>`](documentation-comments.md#summary)) pour décrire le type lui-même et les membres d’un type.)</span><span class="sxs-lookup"><span data-stu-id="209bb-228">(Use `<summary>` ([`<summary>`](documentation-comments.md#summary)) to describe the type itself and the members of a type.)</span></span>

<span data-ttu-id="209bb-229">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-229">__Syntax:__</span></span>

```xml
<remark>description</remark>
```

<span data-ttu-id="209bb-230">où `description` est le texte de la Remarque.</span><span class="sxs-lookup"><span data-stu-id="209bb-230">where `description` is the text of the remark.</span></span>

<span data-ttu-id="209bb-231">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-231">__Example:__</span></span>

```csharp
/// <summary>Class <c>Point</c> models a point in a 
/// two-dimensional plane.</summary>
/// <remark>Uses polar coordinates</remark>
public class Point 
{
    // ...
}
```

### `<returns>`

<span data-ttu-id="209bb-232">Cette balise est utilisée pour décrire la valeur de retour d’une méthode.</span><span class="sxs-lookup"><span data-stu-id="209bb-232">This tag is used to describe the return value of a method.</span></span>

<span data-ttu-id="209bb-233">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-233">__Syntax:__</span></span>

```xml
<returns>description</returns>
```

<span data-ttu-id="209bb-234">où `description` est une description de la valeur de retour.</span><span class="sxs-lookup"><span data-stu-id="209bb-234">where `description` is a description of the return value.</span></span>

<span data-ttu-id="209bb-235">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-235">__Example:__</span></span>

```csharp
/// <summary>Report a point's location as a string.</summary>
/// <returns>A string representing a point's location, in the form (x,y),
///    without any leading, trailing, or embedded whitespace.</returns>
public override string ToString() {
    return "(" + X + "," + Y + ")";
}
```

### `<see>`

<span data-ttu-id="209bb-236">Cette balise permet un lien à l’intérieur de texte.</span><span class="sxs-lookup"><span data-stu-id="209bb-236">This tag allows a link to be specified within text.</span></span> <span data-ttu-id="209bb-237">Utilisez `<seealso>` ([`<seealso>`](documentation-comments.md#seealso)) pour indiquer le texte qui doit apparaître dans une section Voir aussi.</span><span class="sxs-lookup"><span data-stu-id="209bb-237">Use `<seealso>` ([`<seealso>`](documentation-comments.md#seealso)) to indicate text that is to appear in a See Also section.</span></span>

<span data-ttu-id="209bb-238">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-238">__Syntax:__</span></span>

```xml
<see cref="member"/>
```

<span data-ttu-id="209bb-239">où `member` est le nom d’un membre.</span><span class="sxs-lookup"><span data-stu-id="209bb-239">where `member` is the name of a member.</span></span> <span data-ttu-id="209bb-240">Le Générateur de documentation vérifie que l’élément de code donné existe et qu’il modifie *membre* au nom d’élément dans le fichier de documentation générées.</span><span class="sxs-lookup"><span data-stu-id="209bb-240">The documentation generator checks that the given code element exists and changes *member* to the element name in the generated documentation file.</span></span>

<span data-ttu-id="209bb-241">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-241">__Example:__</span></span>

```csharp
/// <summary>This method changes the point's location to
///    the given coordinates.</summary>
/// <see cref="Translate"/>
public void Move(int xor, int yor) {
    X = xor;
    Y = yor;
}

/// <summary>This method changes the point's location by
///    the given x- and y-offsets.
/// </summary>
/// <see cref="Move"/>
public void Translate(int xor, int yor) {
    X += xor;
    Y += yor;
}
```

### `<seealso>`

<span data-ttu-id="209bb-242">Cette balise permet une entrée doit être généré pour la section Voir aussi.</span><span class="sxs-lookup"><span data-stu-id="209bb-242">This tag allows an entry to be generated for the See Also section.</span></span> <span data-ttu-id="209bb-243">Utilisez `<see>` ([`<see>`](documentation-comments.md#see)) pour spécifier un lien à partir du texte.</span><span class="sxs-lookup"><span data-stu-id="209bb-243">Use `<see>` ([`<see>`](documentation-comments.md#see)) to specify a link from within text.</span></span>

<span data-ttu-id="209bb-244">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-244">__Syntax:__</span></span>

```xml
<seealso cref="member"/>
```

<span data-ttu-id="209bb-245">où `member` est le nom d’un membre.</span><span class="sxs-lookup"><span data-stu-id="209bb-245">where `member` is the name of a member.</span></span> <span data-ttu-id="209bb-246">Le Générateur de documentation vérifie que l’élément de code donné existe et qu’il modifie *membre* au nom d’élément dans le fichier de documentation générées.</span><span class="sxs-lookup"><span data-stu-id="209bb-246">The documentation generator checks that the given code element exists and changes *member* to the element name in the generated documentation file.</span></span>

<span data-ttu-id="209bb-247">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-247">__Example:__</span></span>

```csharp
/// <summary>This method determines whether two Points have the same
///    location.</summary>
/// <seealso cref="operator=="/>
/// <seealso cref="operator!="/>
public override bool Equals(object o) {
    // ...
}
```

### `<summary>`

Cette balise peut être utilisée pour décrire un type ou un membre d’un type. <span data-ttu-id="209bb-249">Utilisez `<remark>` ([`<remark>`](documentation-comments.md#remark)) pour décrire le type lui-même.</span><span class="sxs-lookup"><span data-stu-id="209bb-249">Use `<remark>` ([`<remark>`](documentation-comments.md#remark)) to describe the type itself.</span></span>

<span data-ttu-id="209bb-250">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-250">__Syntax:__</span></span>

```xml
<summary>description</summary>
```

<span data-ttu-id="209bb-251">où `description` est un résumé du type ou du membre.</span><span class="sxs-lookup"><span data-stu-id="209bb-251">where `description` is a summary of the type or member.</span></span>

<span data-ttu-id="209bb-252">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-252">__Example:__</span></span>

```csharp
/// <summary>This constructor initializes the new Point to (0,0).</summary>
public Point() : this(0,0) {
}
```

### `<value>`

<span data-ttu-id="209bb-253">Cette balise permet à une propriété à décrire.</span><span class="sxs-lookup"><span data-stu-id="209bb-253">This tag allows a property to be described.</span></span>

<span data-ttu-id="209bb-254">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-254">__Syntax:__</span></span>

```xml
<value>property description</value>
```

<span data-ttu-id="209bb-255">où `property description` est une description pour la propriété.</span><span class="sxs-lookup"><span data-stu-id="209bb-255">where `property description` is a description for the property.</span></span>

<span data-ttu-id="209bb-256">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-256">__Example:__</span></span>

```csharp
/// <value>Property <c>X</c> represents the point's x-coordinate.</value>
public int X
{
    get { return x; }
    set { x = value; }
}
```

### `<typeparam>`

<span data-ttu-id="209bb-257">Cette balise est utilisée pour décrire un paramètre de type générique pour une classe, un struct, une interface, un délégué ou une méthode.</span><span class="sxs-lookup"><span data-stu-id="209bb-257">This tag is used to describe a generic type parameter for a class, struct, interface, delegate, or method.</span></span>

<span data-ttu-id="209bb-258">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-258">__Syntax:__</span></span>

```xml
<typeparam name="name">description</typeparam>
```

<span data-ttu-id="209bb-259">où `name` est le nom du paramètre de type, et `description` est sa description.</span><span class="sxs-lookup"><span data-stu-id="209bb-259">where `name` is the name of the type parameter, and `description` is its description.</span></span>

<span data-ttu-id="209bb-260">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-260">__Example:__</span></span>

```csharp
/// <summary>A generic list class.</summary>
/// <typeparam name="T">The type stored by the list.</typeparam>
public class MyList<T> {
    ...
}
```

### `<typeparamref>`

<span data-ttu-id="209bb-261">Cette balise est utilisée pour indiquer qu’un mot est un paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="209bb-261">This tag is used to indicate that a word is a type parameter.</span></span> <span data-ttu-id="209bb-262">Le fichier de documentation peut être traité pour mettre en forme ce paramètre de type d’une manière distincte.</span><span class="sxs-lookup"><span data-stu-id="209bb-262">The documentation file can be processed to format this type parameter in some distinct way.</span></span>

<span data-ttu-id="209bb-263">__Syntaxe :__</span><span class="sxs-lookup"><span data-stu-id="209bb-263">__Syntax:__</span></span>

```xml
<typeparamref name="name"/>
```

<span data-ttu-id="209bb-264">où `name` est le nom du paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="209bb-264">where `name` is the name of the type parameter.</span></span>

<span data-ttu-id="209bb-265">__Exemple :__</span><span class="sxs-lookup"><span data-stu-id="209bb-265">__Example:__</span></span>

```csharp
/// <summary>This method fetches data and returns a list of <typeparamref name="T"/>.</summary>
/// <param name="query">query to execute</param>
public List<T> FetchData<T>(string query) {
    ...
}
```

## <a name="processing-the-documentation-file"></a><span data-ttu-id="209bb-266">Traitement du fichier de documentation</span><span class="sxs-lookup"><span data-stu-id="209bb-266">Processing the documentation file</span></span>

<span data-ttu-id="209bb-267">Le Générateur de documentation génère une chaîne d’ID pour chaque élément dans le code source qui est marqué avec un commentaire de documentation.</span><span class="sxs-lookup"><span data-stu-id="209bb-267">The documentation generator generates an ID string for each element in the source code that is tagged with a documentation comment.</span></span> <span data-ttu-id="209bb-268">Cette chaîne d’ID identifie de façon unique un élément source.</span><span class="sxs-lookup"><span data-stu-id="209bb-268">This ID string uniquely identifies a source element.</span></span> <span data-ttu-id="209bb-269">Une visionneuse de documentation peut utiliser une chaîne d’ID pour identifier l’élément de métadonnées/réflexion correspondant auquel s’applique la documentation.</span><span class="sxs-lookup"><span data-stu-id="209bb-269">A documentation viewer can use an ID string to identify the corresponding metadata/reflection item to which the documentation applies.</span></span>

<span data-ttu-id="209bb-270">Le fichier de documentation n’est pas une représentation hiérarchique du code source ; au lieu de cela, il est une liste plate avec une chaîne d’ID générée pour chaque élément.</span><span class="sxs-lookup"><span data-stu-id="209bb-270">The documentation file is not a hierarchical representation of the source code; rather, it is a flat list with a generated ID string for each element.</span></span>

### <a name="id-string-format"></a><span data-ttu-id="209bb-271">Format de chaîne d’ID</span><span class="sxs-lookup"><span data-stu-id="209bb-271">ID string format</span></span>

<span data-ttu-id="209bb-272">Le Générateur de documentation respecte les règles suivantes lorsqu’il génère les chaînes d’ID :</span><span class="sxs-lookup"><span data-stu-id="209bb-272">The documentation generator observes the following rules when it generates the ID strings:</span></span>

*  <span data-ttu-id="209bb-273">La chaîne ne contient aucun espace blanc.</span><span class="sxs-lookup"><span data-stu-id="209bb-273">No white space is placed in the string.</span></span>

*  <span data-ttu-id="209bb-274">La première partie de la chaîne identifie le type de membre documenté, à l’aide d’un caractère unique suivi de deux-points.</span><span class="sxs-lookup"><span data-stu-id="209bb-274">The first part of the string identifies the kind of member being documented, via a single character followed by a colon.</span></span> <span data-ttu-id="209bb-275">Les types de membres suivants sont définis :</span><span class="sxs-lookup"><span data-stu-id="209bb-275">The following kinds of members are defined:</span></span>

   | <span data-ttu-id="209bb-276">__Caractère__</span><span class="sxs-lookup"><span data-stu-id="209bb-276">__Character__</span></span> | <span data-ttu-id="209bb-277">__Description__</span><span class="sxs-lookup"><span data-stu-id="209bb-277">__Description__</span></span>                                             |
   |---------------|-------------------------------------------------------------|
   | <span data-ttu-id="209bb-278">E</span><span class="sxs-lookup"><span data-stu-id="209bb-278">E</span></span>             | <span data-ttu-id="209bb-279">Événement</span><span class="sxs-lookup"><span data-stu-id="209bb-279">Event</span></span>                                                       |
   | <span data-ttu-id="209bb-280">F</span><span class="sxs-lookup"><span data-stu-id="209bb-280">F</span></span>             | <span data-ttu-id="209bb-281">Champ</span><span class="sxs-lookup"><span data-stu-id="209bb-281">Field</span></span>                                                       |
   | <span data-ttu-id="209bb-282">M</span><span class="sxs-lookup"><span data-stu-id="209bb-282">M</span></span>             | <span data-ttu-id="209bb-283">Méthode (y compris les constructeurs, destructeurs et opérateurs)</span><span class="sxs-lookup"><span data-stu-id="209bb-283">Method (including constructors, destructors, and operators)</span></span> |
   | <span data-ttu-id="209bb-284">N</span><span class="sxs-lookup"><span data-stu-id="209bb-284">N</span></span>             | <span data-ttu-id="209bb-285">Espace de noms</span><span class="sxs-lookup"><span data-stu-id="209bb-285">Namespace</span></span>                                                   |
   | <span data-ttu-id="209bb-286">P</span><span class="sxs-lookup"><span data-stu-id="209bb-286">P</span></span>             | <span data-ttu-id="209bb-287">Propriété (y compris les indexeurs)</span><span class="sxs-lookup"><span data-stu-id="209bb-287">Property (including indexers)</span></span>                               |
   | <span data-ttu-id="209bb-288">T</span><span class="sxs-lookup"><span data-stu-id="209bb-288">T</span></span>             | <span data-ttu-id="209bb-289">Type (par exemple, la classe délégué, enum, interface et struct)</span><span class="sxs-lookup"><span data-stu-id="209bb-289">Type (such as class, delegate, enum, interface, and struct)</span></span> |
   | <span data-ttu-id="209bb-290">!</span><span class="sxs-lookup"><span data-stu-id="209bb-290">!</span></span>             | <span data-ttu-id="209bb-291">Chaîne d’erreur ; le reste de la chaîne fournit des informations sur l’erreur.</span><span class="sxs-lookup"><span data-stu-id="209bb-291">Error string; the rest of the string provides information about the error.</span></span> <span data-ttu-id="209bb-292">Par exemple, le Générateur de documentation génère des informations d’erreur pour les liens qui ne peut pas être résolus.</span><span class="sxs-lookup"><span data-stu-id="209bb-292">For example, the documentation generator generates error information for links that cannot be resolved.</span></span> |

*  <span data-ttu-id="209bb-293">La deuxième partie de la chaîne est le nom qualifié complet de l’élément, en commençant à la racine de l’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="209bb-293">The second part of the string is the fully qualified name of the element, starting at the root of the namespace.</span></span> <span data-ttu-id="209bb-294">Le nom de l’élément, son type (s) d’englobante et un espace de noms sont séparés par des points.</span><span class="sxs-lookup"><span data-stu-id="209bb-294">The name of the element, its enclosing type(s), and namespace are separated by periods.</span></span> <span data-ttu-id="209bb-295">Si le nom de l’élément lui-même comporte des points, ils sont remplacés par `#(U+0023)` caractères.</span><span class="sxs-lookup"><span data-stu-id="209bb-295">If the name of the item itself has periods, they are replaced by `#(U+0023)` characters.</span></span> <span data-ttu-id="209bb-296">(Il est supposé qu’aucun élément n’a ce caractère dans son nom.)</span><span class="sxs-lookup"><span data-stu-id="209bb-296">(It is assumed that no element has this character in its name.)</span></span>
*  <span data-ttu-id="209bb-297">Pour les méthodes et propriétés avec des arguments, l’argument liste suit, placés entre parenthèses.</span><span class="sxs-lookup"><span data-stu-id="209bb-297">For methods and properties with arguments, the argument list follows, enclosed in parentheses.</span></span> <span data-ttu-id="209bb-298">Pour ceux sans arguments, les parenthèses sont omises.</span><span class="sxs-lookup"><span data-stu-id="209bb-298">For those without arguments, the parentheses are omitted.</span></span> <span data-ttu-id="209bb-299">Les arguments sont séparés par des virgules.</span><span class="sxs-lookup"><span data-stu-id="209bb-299">The arguments are separated by commas.</span></span> <span data-ttu-id="209bb-300">L’encodage de chaque argument est identique à une signature CLI, comme suit :</span><span class="sxs-lookup"><span data-stu-id="209bb-300">The encoding of each argument is the same as a CLI signature, as follows:</span></span>
   *  <span data-ttu-id="209bb-301">Arguments sont représentés par leur nom de la documentation, qui est basé sur leur nom qualifié complet, modifié comme suit :</span><span class="sxs-lookup"><span data-stu-id="209bb-301">Arguments are represented by their documentation name, which is based on their fully qualified name, modified as follows:</span></span>
      * <span data-ttu-id="209bb-302">Les arguments qui représentent des types génériques ont ajoutée `` ` `` (accent grave) suivi par le nombre de paramètres de type</span><span class="sxs-lookup"><span data-stu-id="209bb-302">Arguments that represent generic types have an appended `` ` `` (backtick) character followed by the number of type parameters</span></span>
      * <span data-ttu-id="209bb-303">Arguments ayant la `out` ou `ref` modificateur ont un `@` suivant leur nom de type.</span><span class="sxs-lookup"><span data-stu-id="209bb-303">Arguments having the `out` or `ref` modifier have an `@` following their type name.</span></span> <span data-ttu-id="209bb-304">Arguments passés par valeur ou par le biais de `params` n’ont aucun notation spéciale.</span><span class="sxs-lookup"><span data-stu-id="209bb-304">Arguments passed by value or via `params` have no special notation.</span></span>
      * <span data-ttu-id="209bb-305">Les arguments qui sont des tableaux sont représentés en tant que `[lowerbound:size, ... , lowerbound:size]` où le nombre de virgules correspond au rang moins un, et les limites inférieure et la taille de chaque dimension, s’il est connu, sont représentées sous forme décimale.</span><span class="sxs-lookup"><span data-stu-id="209bb-305">Arguments that are arrays are represented as `[lowerbound:size, ... , lowerbound:size]` where the number of commas is the rank less one, and the lower bounds and size of each dimension, if known, are represented in decimal.</span></span> <span data-ttu-id="209bb-306">Si une limite inférieure ou une taille n’est pas spécifié, il est omis.</span><span class="sxs-lookup"><span data-stu-id="209bb-306">If a lower bound or size is not specified, it is omitted.</span></span> <span data-ttu-id="209bb-307">Si la limite inférieure et la taille d’une dimension particulière sont omises, le `:` est également omis.</span><span class="sxs-lookup"><span data-stu-id="209bb-307">If the lower bound and size for a particular dimension are omitted, the `:` is omitted as well.</span></span> <span data-ttu-id="209bb-308">Tableaux en escalier sont représentés par une `[]` par niveau.</span><span class="sxs-lookup"><span data-stu-id="209bb-308">Jagged arrays are represented by one `[]` per level.</span></span>
      * <span data-ttu-id="209bb-309">Les arguments qui ont des types de pointeur autre que void sont représentés en utilisant un `*` après le nom de type.</span><span class="sxs-lookup"><span data-stu-id="209bb-309">Arguments that have pointer types other than void are represented using a `*` following the type name.</span></span> <span data-ttu-id="209bb-310">Un pointeur void est représenté à l’aide d’un nom de type de `System.Void`.</span><span class="sxs-lookup"><span data-stu-id="209bb-310">A void pointer is represented using a type name of `System.Void`.</span></span>
      * <span data-ttu-id="209bb-311">Les arguments qui font référence aux paramètres de type générique définies sur les types sont encodés à l’aide de la `` ` `` (accent grave) suivi par l’index de base zéro du paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="209bb-311">Arguments that refer to generic type parameters defined on types are encoded using the `` ` `` (backtick) character followed by the zero-based index of the type parameter.</span></span>
      * <span data-ttu-id="209bb-312">Les arguments qui utilisent des paramètres de type générique définies dans les méthodes utilisent un double-backtick ``` `` ``` au lieu du `` ` `` utilisés pour les types.</span><span class="sxs-lookup"><span data-stu-id="209bb-312">Arguments that use generic type parameters defined in methods use a double-backtick ``` `` ``` instead of the `` ` `` used for types.</span></span>
      * <span data-ttu-id="209bb-313">Les arguments qui font référence aux types génériques construits sont encodés à l’aide de type générique, suivi par `{`, suivi d’une liste séparée par des virgules d’arguments de type, suivi par `}`.</span><span class="sxs-lookup"><span data-stu-id="209bb-313">Arguments that refer to constructed generic types are encoded using the generic type, followed by `{`, followed by a comma-separated list of type arguments, followed by `}`.</span></span>

### <a name="id-string-examples"></a><span data-ttu-id="209bb-314">Exemples de chaînes d’ID</span><span class="sxs-lookup"><span data-stu-id="209bb-314">ID string examples</span></span>

<span data-ttu-id="209bb-315">Les exemples suivants montrent chacun un fragment de code c#, ainsi que la chaîne d’ID produite à partir de chaque élément source capable d’avoir un commentaire de documentation :</span><span class="sxs-lookup"><span data-stu-id="209bb-315">The following examples each show a fragment of C# code, along with the ID string produced from each source element capable of having a documentation comment:</span></span>

*  <span data-ttu-id="209bb-316">Les types sont représentés à l’aide de leur nom qualifié complet, laquelle s’ajoutent les informations génériques :</span><span class="sxs-lookup"><span data-stu-id="209bb-316">Types are represented using their fully qualified name, augmented with generic information:</span></span>

   ```csharp
   enum Color { Red, Blue, Green }

   namespace Acme
   {
       interface IProcess {...}

       struct ValueType {...}

       class Widget: IProcess
       {
           public class NestedClass {...}
           public interface IMenuItem {...}
           public delegate void Del(int i);
           public enum Direction { North, South, East, West }
       }

       class MyList<T>
       {
           class Helper<U,V> {...}
       }
   }

   "T:Color"
   "T:Acme.IProcess"
   "T:Acme.ValueType"
   "T:Acme.Widget"
   "T:Acme.Widget.NestedClass"
   "T:Acme.Widget.IMenuItem"
   "T:Acme.Widget.Del"
   "T:Acme.Widget.Direction"
   "T:Acme.MyList`1"
   "T:Acme.MyList`1.Helper`2"
   ```

*  <span data-ttu-id="209bb-317">Les champs sont représentés par leur nom qualifié complet :</span><span class="sxs-lookup"><span data-stu-id="209bb-317">Fields are represented by their fully qualified name:</span></span>

   ```csharp
   namespace Acme
   {
       struct ValueType
       {
           private int total;
       }
   
       class Widget: IProcess
       {
           public class NestedClass
           {
               private int value;
           }
   
           private string message;
           private static Color defaultColor;
           private const double PI = 3.14159;
           protected readonly double monthlyAverage;
           private long[] array1;
           private Widget[,] array2;
           private unsafe int *pCount;
           private unsafe float **ppValues;
       }
   }

   "F:Acme.ValueType.total"
   "F:Acme.Widget.NestedClass.value"
   "F:Acme.Widget.message"
   "F:Acme.Widget.defaultColor"
   "F:Acme.Widget.PI"
   "F:Acme.Widget.monthlyAverage"
   "F:Acme.Widget.array1"
   "F:Acme.Widget.array2"
   "F:Acme.Widget.pCount"
   "F:Acme.Widget.ppValues"
   ```

*  <span data-ttu-id="209bb-318">Des constructeurs.</span><span class="sxs-lookup"><span data-stu-id="209bb-318">Constructors.</span></span>

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           static Widget() {...}
           public Widget() {...}
           public Widget(string s) {...}
       }
   }

   "M:Acme.Widget.#cctor"
   "M:Acme.Widget.#ctor"
   "M:Acme.Widget.#ctor(System.String)"
   ```

*  <span data-ttu-id="209bb-319">Destructeurs.</span><span class="sxs-lookup"><span data-stu-id="209bb-319">Destructors.</span></span>

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           ~Widget() {...}
       }
   }
   
   "M:Acme.Widget.Finalize"
   ```

*  <span data-ttu-id="209bb-320">Méthodes.</span><span class="sxs-lookup"><span data-stu-id="209bb-320">Methods.</span></span>

   ```csharp
   namespace Acme
   {
       struct ValueType
       {
           public void M(int i) {...}
       }

       class Widget: IProcess
       {
           public class NestedClass
           {
               public void M(int i) {...}
           }

           public static void M0() {...}
           public void M1(char c, out float f, ref ValueType v) {...}
           public void M2(short[] x1, int[,] x2, long[][] x3) {...}
           public void M3(long[][] x3, Widget[][,,] x4) {...}
           public unsafe void M4(char *pc, Color **pf) {...}
           public unsafe void M5(void *pv, double *[][,] pd) {...}
           public void M6(int i, params object[] args) {...}
       }

       class MyList<T>
       {
           public void Test(T t) { }
       }

       class UseList
       {
           public void Process(MyList<int> list) { }
           public MyList<T> GetValues<T>(T inputValue) { return null; }
       }
   }

   "M:Acme.ValueType.M(System.Int32)"
   "M:Acme.Widget.NestedClass.M(System.Int32)"
   "M:Acme.Widget.M0"
   "M:Acme.Widget.M1(System.Char,System.Single@,Acme.ValueType@)"
   "M:Acme.Widget.M2(System.Int16[],System.Int32[0:,0:],System.Int64[][])"
   "M:Acme.Widget.M3(System.Int64[][],Acme.Widget[0:,0:,0:][])"
   "M:Acme.Widget.M4(System.Char*,Color**)"
   "M:Acme.Widget.M5(System.Void*,System.Double*[0:,0:][])"
   "M:Acme.Widget.M6(System.Int32,System.Object[])"
   "M:Acme.MyList`1.Test(`0)"
   "M:Acme.UseList.Process(Acme.MyList{System.Int32})"
   "M:Acme.UseList.GetValues``(``0)"
   ```

*  <span data-ttu-id="209bb-321">Propriétés et indexeurs.</span><span class="sxs-lookup"><span data-stu-id="209bb-321">Properties and indexers.</span></span>

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           public int Width { get {...} set {...} }
           public int this[int i] { get {...} set {...} }
           public int this[string s, int i] { get {...} set {...} }
       }
   }

   "P:Acme.Widget.Width"
   "P:Acme.Widget.Item(System.Int32)"
   "P:Acme.Widget.Item(System.String,System.Int32)"
   ```

*  <span data-ttu-id="209bb-322">événements.</span><span class="sxs-lookup"><span data-stu-id="209bb-322">Events.</span></span>

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           public event Del AnEvent;
       }
   }

   "E:Acme.Widget.AnEvent"
   ```

*  <span data-ttu-id="209bb-323">Les opérateurs unaires.</span><span class="sxs-lookup"><span data-stu-id="209bb-323">Unary operators.</span></span>

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           public static Widget operator+(Widget x) {...}
       }
   }

   "M:Acme.Widget.op_UnaryPlus(Acme.Widget)"
   ```

   <span data-ttu-id="209bb-324">L’ensemble complet des noms de fonctions d’opérateur unaire utilisé est le suivant : `op_UnaryPlus`, `op_UnaryNegation`, `op_LogicalNot`, `op_OnesComplement`, `op_Increment`, `op_Decrement`, `op_True`, et `op_False`.</span><span class="sxs-lookup"><span data-stu-id="209bb-324">The complete set of unary operator function names used is as follows: `op_UnaryPlus`, `op_UnaryNegation`, `op_LogicalNot`, `op_OnesComplement`, `op_Increment`, `op_Decrement`, `op_True`, and `op_False`.</span></span>

*  <span data-ttu-id="209bb-325">Opérateurs binaires.</span><span class="sxs-lookup"><span data-stu-id="209bb-325">Binary operators.</span></span>

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           public static Widget operator+(Widget x1, Widget x2) {...}
       }
   }

   "M:Acme.Widget.op_Addition(Acme.Widget,Acme.Widget)"
   ```

   <span data-ttu-id="209bb-326">L’ensemble complet des noms de fonctions d’opérateur binaire utilisé est le suivant : `op_Addition`, `op_Subtraction`, `op_Multiply`, `op_Division`, `op_Modulus`, `op_BitwiseAnd`, `op_BitwiseOr`, `op_ExclusiveOr`, `op_LeftShift`, `op_RightShift`, `op_Equality`, `op_Inequality`, `op_LessThan`, `op_LessThanOrEqual`, `op_GreaterThan`, et `op_GreaterThanOrEqual`.</span><span class="sxs-lookup"><span data-stu-id="209bb-326">The complete set of binary operator function names used is as follows: `op_Addition`, `op_Subtraction`, `op_Multiply`, `op_Division`, `op_Modulus`, `op_BitwiseAnd`, `op_BitwiseOr`, `op_ExclusiveOr`, `op_LeftShift`, `op_RightShift`, `op_Equality`, `op_Inequality`, `op_LessThan`, `op_LessThanOrEqual`, `op_GreaterThan`, and `op_GreaterThanOrEqual`.</span></span>

*  <span data-ttu-id="209bb-327">Opérateurs de conversion ont une fin «`~`» suivi du type de retour.</span><span class="sxs-lookup"><span data-stu-id="209bb-327">Conversion operators have a trailing "`~`" followed by the return type.</span></span>

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           public static explicit operator int(Widget x) {...}
           public static implicit operator long(Widget x) {...}
       }
   }

   "M:Acme.Widget.op_Explicit(Acme.Widget)~System.Int32"
   "M:Acme.Widget.op_Implicit(Acme.Widget)~System.Int64"
   ```

## <a name="an-example"></a><span data-ttu-id="209bb-328">Exemple</span><span class="sxs-lookup"><span data-stu-id="209bb-328">An example</span></span>

### <a name="c-source-code"></a><span data-ttu-id="209bb-329">Code source c#</span><span class="sxs-lookup"><span data-stu-id="209bb-329">C# source code</span></span>

<span data-ttu-id="209bb-330">L’exemple suivant montre le code source d’un `Point` classe :</span><span class="sxs-lookup"><span data-stu-id="209bb-330">The following example shows the source code of a `Point` class:</span></span>

```csharp
namespace Graphics
{

/// <summary>Class <c>Point</c> models a point in a two-dimensional plane.
/// </summary>
public class Point 
{

    /// <summary>Instance variable <c>x</c> represents the point's
    ///    x-coordinate.</summary>
    private int x;

    /// <summary>Instance variable <c>y</c> represents the point's
    ///    y-coordinate.</summary>
    private int y;

    /// <value>Property <c>X</c> represents the point's x-coordinate.</value>
    public int X
    {
        get { return x; }
        set { x = value; }
    }

    /// <value>Property <c>Y</c> represents the point's y-coordinate.</value>
    public int Y
    {
        get { return y; }
        set { y = value; }
    }

    /// <summary>This constructor initializes the new Point to
    ///    (0,0).</summary>
    public Point() : this(0,0) {}

    /// <summary>This constructor initializes the new Point to
    ///    (<paramref name="xor"/>,<paramref name="yor"/>).</summary>
    /// <param><c>xor</c> is the new Point's x-coordinate.</param>
    /// <param><c>yor</c> is the new Point's y-coordinate.</param>
    public Point(int xor, int yor) {
        X = xor;
        Y = yor;
    }

    /// <summary>This method changes the point's location to
    ///    the given coordinates.</summary>
    /// <param><c>xor</c> is the new x-coordinate.</param>
    /// <param><c>yor</c> is the new y-coordinate.</param>
    /// <see cref="Translate"/>
    public void Move(int xor, int yor) {
        X = xor;
        Y = yor;
    }

    /// <summary>This method changes the point's location by
    ///    the given x- and y-offsets.
    /// <example>For example:
    /// <code>
    ///    Point p = new Point(3,5);
    ///    p.Translate(-1,3);
    /// </code>
    /// results in <c>p</c>'s having the value (2,8).
    /// </example>
    /// </summary>
    /// <param><c>xor</c> is the relative x-offset.</param>
    /// <param><c>yor</c> is the relative y-offset.</param>
    /// <see cref="Move"/>
    public void Translate(int xor, int yor) {
        X += xor;
        Y += yor;
    }

    /// <summary>This method determines whether two Points have the same
    ///    location.</summary>
    /// <param><c>o</c> is the object to be compared to the current object.
    /// </param>
    /// <returns>True if the Points have the same location and they have
    ///    the exact same type; otherwise, false.</returns>
    /// <seealso cref="operator=="/>
    /// <seealso cref="operator!="/>
    public override bool Equals(object o) {
        if (o == null) {
            return false;
        }

        if (this == o) {
            return true;
        }

        if (GetType() == o.GetType()) {
            Point p = (Point)o;
            return (X == p.X) && (Y == p.Y);
        }
        return false;
    }

    /// <summary>Report a point's location as a string.</summary>
    /// <returns>A string representing a point's location, in the form (x,y),
    ///    without any leading, training, or embedded whitespace.</returns>
    public override string ToString() {
        return "(" + X + "," + Y + ")";
    }

    /// <summary>This operator determines whether two Points have the same
    ///    location.</summary>
    /// <param><c>p1</c> is the first Point to be compared.</param>
    /// <param><c>p2</c> is the second Point to be compared.</param>
    /// <returns>True if the Points have the same location and they have
    ///    the exact same type; otherwise, false.</returns>
    /// <seealso cref="Equals"/>
    /// <seealso cref="operator!="/>
    public static bool operator==(Point p1, Point p2) {
        if ((object)p1 == null || (object)p2 == null) {
            return false;
        }

        if (p1.GetType() == p2.GetType()) {
            return (p1.X == p2.X) && (p1.Y == p2.Y);
        }

        return false;
    }

    /// <summary>This operator determines whether two Points have the same
    ///    location.</summary>
    /// <param><c>p1</c> is the first Point to be compared.</param>
    /// <param><c>p2</c> is the second Point to be compared.</param>
    /// <returns>True if the Points do not have the same location and the
    ///    exact same type; otherwise, false.</returns>
    /// <seealso cref="Equals"/>
    /// <seealso cref="operator=="/>
    public static bool operator!=(Point p1, Point p2) {
        return !(p1 == p2);
    }

    /// <summary>This is the entry point of the Point class testing
    /// program.
    /// <para>This program tests each method and operator, and
    /// is intended to be run after any non-trivial maintenance has
    /// been performed on the Point class.</para></summary>
    public static void Main() {
        // class test code goes here
    }
}
}
```

### <a name="resulting-xml"></a><span data-ttu-id="209bb-331">Données XML résultantes</span><span class="sxs-lookup"><span data-stu-id="209bb-331">Resulting XML</span></span>

<span data-ttu-id="209bb-332">Voici la sortie produite par un générateur de documentation donné le code source pour la classe `Point`, comme indiqué ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="209bb-332">Here is the output produced by one documentation generator when given the source code for class `Point`, shown above:</span></span>

```xml
<?xml version="1.0"?>
<doc>
    <assembly>
        <name>Point</name>
    </assembly>
    <members>
        <member name="T:Graphics.Point">
            <summary>Class <c>Point</c> models a point in a two-dimensional
            plane.
            </summary>
        </member>

        <member name="F:Graphics.Point.x">
            <summary>Instance variable <c>x</c> represents the point's
            x-coordinate.</summary>
        </member>

        <member name="F:Graphics.Point.y">
            <summary>Instance variable <c>y</c> represents the point's
            y-coordinate.</summary>
        </member>

        <member name="M:Graphics.Point.#ctor">
            <summary>This constructor initializes the new Point to
        (0,0).</summary>
        </member>

        <member name="M:Graphics.Point.#ctor(System.Int32,System.Int32)">
            <summary>This constructor initializes the new Point to
            (<paramref name="xor"/>,<paramref name="yor"/>).</summary>
            <param><c>xor</c> is the new Point's x-coordinate.</param>
            <param><c>yor</c> is the new Point's y-coordinate.</param>
        </member>

        <member name="M:Graphics.Point.Move(System.Int32,System.Int32)">
            <summary>This method changes the point's location to
            the given coordinates.</summary>
            <param><c>xor</c> is the new x-coordinate.</param>
            <param><c>yor</c> is the new y-coordinate.</param>
            <see cref="M:Graphics.Point.Translate(System.Int32,System.Int32)"/>
        </member>

        <member
            name="M:Graphics.Point.Translate(System.Int32,System.Int32)">
            <summary>This method changes the point's location by
            the given x- and y-offsets.
            <example>For example:
            <code>
            Point p = new Point(3,5);
            p.Translate(-1,3);
            </code>
            results in <c>p</c>'s having the value (2,8).
            </example>
            </summary>
            <param><c>xor</c> is the relative x-offset.</param>
            <param><c>yor</c> is the relative y-offset.</param>
            <see cref="M:Graphics.Point.Move(System.Int32,System.Int32)"/>
        </member>

        <member name="M:Graphics.Point.Equals(System.Object)">
            <summary>This method determines whether two Points have the same
            location.</summary>
            <param><c>o</c> is the object to be compared to the current
            object.
            </param>
            <returns>True if the Points have the same location and they have
            the exact same type; otherwise, false.</returns>
            <seealso
      cref="M:Graphics.Point.op_Equality(Graphics.Point,Graphics.Point)"/>
            <seealso
      cref="M:Graphics.Point.op_Inequality(Graphics.Point,Graphics.Point)"/>
        </member>

        <member name="M:Graphics.Point.ToString">
            <summary>Report a point's location as a string.</summary>
            <returns>A string representing a point's location, in the form
            (x,y),
            without any leading, training, or embedded whitespace.</returns>
        </member>

        <member
       name="M:Graphics.Point.op_Equality(Graphics.Point,Graphics.Point)">
            <summary>This operator determines whether two Points have the
            same
            location.</summary>
            <param><c>p1</c> is the first Point to be compared.</param>
            <param><c>p2</c> is the second Point to be compared.</param>
            <returns>True if the Points have the same location and they have
            the exact same type; otherwise, false.</returns>
            <seealso cref="M:Graphics.Point.Equals(System.Object)"/>
            <seealso
     cref="M:Graphics.Point.op_Inequality(Graphics.Point,Graphics.Point)"/>
        </member>

        <member
      name="M:Graphics.Point.op_Inequality(Graphics.Point,Graphics.Point)">
            <summary>This operator determines whether two Points have the
            same
            location.</summary>
            <param><c>p1</c> is the first Point to be compared.</param>
            <param><c>p2</c> is the second Point to be compared.</param>
            <returns>True if the Points do not have the same location and
            the
            exact same type; otherwise, false.</returns>
            <seealso cref="M:Graphics.Point.Equals(System.Object)"/>
            <seealso
      cref="M:Graphics.Point.op_Equality(Graphics.Point,Graphics.Point)"/>
        </member>

        <member name="M:Graphics.Point.Main">
            <summary>This is the entry point of the Point class testing
            program.
            <para>This program tests each method and operator, and
            is intended to be run after any non-trivial maintenance has
            been performed on the Point class.</para></summary>
        </member>

        <member name="P:Graphics.Point.X">
            <value>Property <c>X</c> represents the point's
            x-coordinate.</value>
        </member>

        <member name="P:Graphics.Point.Y">
            <value>Property <c>Y</c> represents the point's
            y-coordinate.</value>
        </member>
    </members>
</doc>
```
