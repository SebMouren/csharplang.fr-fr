---
ms.openlocfilehash: adf81842e3c763c7bbdd3f10bb884dc1207b9099
ms.sourcegitcommit: 0489cb64b7dfb328813d757f4d447a15b85a5851
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70912434"
---
# <a name="documentation-comments"></a>Commentaires de documentation

C#fournit un mécanisme permettant aux programmeurs de documenter leur code à l’aide d’une syntaxe de commentaire spéciale qui contient du texte XML. Dans les fichiers de code source, les commentaires ayant un certain format peuvent être utilisés pour indiquer à un outil de produire du code XML à partir de ces commentaires et des éléments de code source, qu’ils précèdent. Les commentaires utilisant une telle syntaxe sont appelés ***Commentaires de documentation***. Ils doivent précéder immédiatement un type défini par l’utilisateur (par exemple, une classe, un délégué ou une interface) ou un membre (par exemple, un champ, un événement, une propriété ou une méthode). L’outil de génération XML est appelé le ***Générateur de documentation***. (Ce générateur peut être, mais il n’est pas nécessaire C# , le compilateur lui-même.) La sortie produite par le générateur de documentation porte le nom de ***fichier de documentation***. Un fichier de documentation est utilisé comme entrée dans une ***visionneuse de documentation***. outil destiné à produire une sorte d’affichage visuel des informations de type et de la documentation associée.

Cette spécification suggère un ensemble de balises à utiliser dans les commentaires de documentation, mais l’utilisation de ces balises n’est pas obligatoire, et d’autres balises peuvent être utilisées si vous le souhaitez, dans la mesure où les règles de XML bien formé sont suivies.

## <a name="introduction"></a>Introduction

Les commentaires ayant une forme spéciale peuvent être utilisés pour indiquer à un outil de produire du code XML à partir de ces commentaires et des éléments de code source, qu’ils précèdent. Ces commentaires sont des commentaires sur une seule ligne qui commencent par trois barres`///`obliques () ou des commentaires délimités qui commencent par une barre`/**`oblique et deux étoiles (). Ils doivent précéder immédiatement un type défini par l’utilisateur (par exemple, une classe, un délégué ou une interface) ou un membre (par exemple, un champ, un événement, une propriété ou une méthode) qu’ils annotent. Les sections d’attributs ([spécification d’attribut](attributes.md#attribute-specification)) sont considérées comme faisant partie des déclarations. les commentaires de documentation doivent donc précéder les attributs appliqués à un type ou à un membre.

__Stockéesyntaxe__

```antlr
single_line_doc_comment
    : '///' input_character*
    ;

delimited_doc_comment
    : '/**' delimited_comment_section* asterisk+ '/'
    ;
```

Dans un *single_line_doc_comment*, s’il y a un *espace blanc* qui `///` suit les caractères sur chacun des *single_line_doc_comment*s adjacents au *single_line_doc_comment*actuel, alors celale caractère d’espace blanc n’est pas inclus dans la sortie XML.

Dans un document de commentaire délimité, si le premier caractère autre qu’un espace blanc sur la deuxième ligne est un astérisque et le même modèle de caractères d’espace blanc facultatifs et qu’un astérisque est répété au début de chaque ligne dans le document délimité-doc-comment, les caractères du modèle répété ne sont pas inclus dans la sortie XML. Le modèle peut inclure des caractères d’espacement après, ainsi qu’avant, le caractère astérisque.

__Exemple :__

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

Le texte des commentaires de documentation doit être correctement formé conformément aux règles de XML (https://www.w3.org/TR/REC-xml). Si le code XML est incorrect, un avertissement est généré et le fichier de documentation contient un commentaire indiquant qu’une erreur a été rencontrée.

Bien que les développeurs soient libres de créer leur propre ensemble de balises, un ensemble recommandé est défini dans les [Balises recommandées](documentation-comments.md#recommended-tags). Certaines des balises recommandées ont des significations spéciales :

*  La `<param>` balise est utilisée pour décrire les paramètres. Si une telle balise est utilisée, le générateur de documentation doit vérifier que le paramètre spécifié existe et que tous les paramètres sont décrits dans commentaires de documentation. En cas d’échec de cette vérification, le générateur de documentation émet un avertissement.
*  L’attribut `cref` peut être joint à n’importe quelle balise pour fournir une référence à un élément de code. Le générateur de documentation doit vérifier que cet élément de code existe. Si la vérification échoue, le générateur de documentation émet un avertissement. Lors de la recherche d’un nom décrit `cref` dans un attribut, le générateur de documentation doit respecter la `using` visibilité des espaces de noms en fonction des instructions figurant dans le code source. Pour les éléments de code génériques, la syntaxe générique normale (autrement dit, «`List<T>`») ne peut pas être utilisée car elle génère du code XML non valide. Les accolades peuvent être utilisées à la place des crochets (autrement`List{T}`dit, « »), ou la syntaxe d’échappement XML peut être utilisée (`List&lt;T&gt;`autrement dit, « »).
*  La `<summary>` balise est destinée à être utilisée par une visionneuse de documentation pour afficher des informations supplémentaires sur un type ou un membre.
*  La `<include>` balise contient des informations provenant d’un fichier XML externe.

Notez soigneusement que le fichier de documentation ne fournit pas d’informations complètes sur le type et les membres (par exemple, il ne contient pas d’informations de type). Pour obtenir de telles informations sur un type ou un membre, le fichier de documentation doit être utilisé conjointement avec la réflexion sur le type ou le membre réel.

## <a name="recommended-tags"></a>Étiquettes recommandées

Le générateur de documentation doit accepter et traiter les balises valides conformément aux règles XML. Les balises suivantes fournissent des fonctionnalités couramment utilisées dans la documentation utilisateur. (Bien sûr, d’autres balises sont possibles.)


| __Référence__          | __Section__                                            | __Fonction__                                            |
|------------------|--------------------------------------------------------|--------------------------------------------------------|
| `<c>`            | [`<c>`](documentation-comments.md#c)                   | Définir du texte dans une police de type code                           | 
| `<code>`         | [`<code>`](documentation-comments.md#code)             | Définir une ou plusieurs lignes de code source ou de sortie du programme |
| `<example>`      | [`<example>`](documentation-comments.md#example)       | Indiquer un exemple                                    |
| `<exception>`    | [`<exception>`](documentation-comments.md#exception)   | Identifie les exceptions qu’une méthode peut lever           |
| `<include>`      | [`<include>`](documentation-comments.md#include)       | Contient du code XML à partir d’un fichier externe                     |
| `<list>`         | [`<list>`](documentation-comments.md#list)             | Créer une liste ou une table                                 |
| `<para>`         | [`<para>`](documentation-comments.md#para)             | Autoriser l’ajout de la structure au texte                   |
| `<param>`        | [`<param>`](documentation-comments.md#param)           | Décrire un paramètre pour une méthode ou un constructeur       |
| `<paramref>`     | [`<paramref>`](documentation-comments.md#paramref)     | Identifier qu’un mot est un nom de paramètre               |
| `<permission>`   | [`<permission>`](documentation-comments.md#permission) | Documenter l’accessibilité de sécurité d’un membre        |
| `<remarks>`      | [`<remarks>`](documentation-comments.md#remarks)       | Décrire des informations supplémentaires sur un type           |
| `<returns>`      | [`<returns>`](documentation-comments.md#returns)       | Décrire la valeur de retour d’une méthode                  |
| `<see>`          | [`<see>`](documentation-comments.md#see)               | Spécifier un lien                                         |
| `<seealso>`      | [`<seealso>`](documentation-comments.md#seealso)       | Générer une entrée Voir également                              |
| `<summary>`      | [`<summary>`](documentation-comments.md#summary)       | Décrire un type ou un membre d’un type                  |
| `<value>`        | [`<value>`](documentation-comments.md#value)           | Décrire une propriété                                    |
| `<typeparam>`    |                                                        | Décrire un paramètre de type générique                      |
| `<typeparamref>` |                                                        | Identifier qu’un mot est un nom de paramètre de type          |

### `<c>`

Cette balise fournit un mécanisme pour indiquer qu’un fragment de texte d’une description doit être défini dans une police spéciale telle que celle utilisée pour un bloc de code. Pour les lignes de code réel, `<code>` utilisez[`<code>`](documentation-comments.md#code)().

__Stockéesyntaxe__

```xml
<c>text</c>
```

__Exemple :__

```csharp
/// <summary>Class <c>Point</c> models a point in a two-dimensional
/// plane.</summary>

public class Point 
{
    // ...
}
```

### `<code>`

Cette balise est utilisée pour définir une ou plusieurs lignes de code source ou de sortie de programme dans une police spéciale. Pour les petits fragments de code dans la `<c>` narration[`<c>`](documentation-comments.md#c), utilisez ().

__Stockéesyntaxe__

```xml
<code>source code or program output</code>
```

__Exemple :__

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

Cette balise permet d’obtenir un exemple de code dans un commentaire, afin de spécifier comment une méthode ou un autre membre de bibliothèque peut être utilisé. En règle générale, cela implique également l’utilisation de la `<code>` balise ([`<code>`](documentation-comments.md#code)).

__Stockéesyntaxe__

```xml
<example>description</example>
```

__Exemple :__

Pour `<code>` obtenir[`<code>`](documentation-comments.md#code)un exemple, consultez ().

### `<exception>`

Cette balise permet de documenter les exceptions qu’une méthode peut lever.

__Stockéesyntaxe__

```xml
<exception cref="member">description</exception>
```

où

* `member`nom d’un membre. Le générateur de documentation vérifie que le membre donné existe et `member` se traduit par le nom d’élément canonique dans le fichier de documentation.
* `description`est une description des circonstances dans lesquelles l’exception est levée.

__Exemple :__

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

Cette balise permet d’inclure des informations à partir d’un document XML qui est externe au fichier de code source. Le fichier externe doit être un document XML bien formé et une expression XPath est appliquée à ce document pour spécifier le code XML de ce document à inclure. La `<include>` balise est ensuite remplacée par le XML sélectionné du document externe.

__Stockéesyntaxe__

```
<include file="filename" path="xpath" />
```

où

* `filename`nom de fichier d’un fichier XML externe. Le nom du fichier est interprété par rapport au fichier contenant la balise Include.
* `xpath`expression XPath qui sélectionne des données XML dans le fichier XML externe.

__Exemple :__

Si le code source contenait une déclaration comme :

```csharp
/// <include file="docs.xml" path='extradoc/class[@name="IntList"]/*' />
public class IntList { ... }
```

et le fichier externe « docs. XML » a le contenu suivant :

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

la même documentation est ensuite générée comme si le code source contenait :

```csharp
/// <summary>
///    Contains a list of integers.
/// </summary>
public class IntList { ... }
```

### `<list>`

Cette balise est utilisée pour créer une liste ou une table d’éléments. Elle peut contenir un `<listheader>` bloc pour définir la ligne d’en-tête d’une table ou d’une liste de définitions. (Lors de la définition d’une table, seule `term` une entrée pour dans l’en-tête doit être fournie.)

Chaque élément de la liste est spécifié avec un `<item>` bloc. Lors de la création d’une liste `term` de `description` définitions, et doivent être spécifiés. Toutefois, pour une table, une liste à puces ou une liste numérotée `description` , seul doit être spécifié.

__Stockéesyntaxe__

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

où

* `term`terme à définir, dont la définition se trouve dans `description`.
* `description`est un élément dans une liste à puces ou une liste numérotée, ou la `term`définition d’un.

__Exemple :__

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

Cette balise est destinée à être utilisée dans d’autres `<summary>` balises, `<returns>` telles[`<returns>`](documentation-comments.md#returns)que ([`<remarks>`](documentation-comments.md#remarks)) ou (), et permet l’ajout de la structure au texte.

__Stockéesyntaxe__

```xml
<para>content</para>
```

où `content` est le texte du paragraphe.

__Exemple :__

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

Cette balise est utilisée pour décrire un paramètre d’une méthode, d’un constructeur ou d’un indexeur.

__Stockéesyntaxe__

```xml
<param name="name">description</param>
```

où

* `name`nom du paramètre.
* `description`Description du paramètre.

__Exemple :__

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

Cette balise est utilisée pour indiquer qu’un mot est un paramètre. Le fichier de documentation peut être traité pour mettre en forme ce paramètre de manière distincte.

__Stockéesyntaxe__

```xml
<paramref name="name"/>
```

où `name` est le nom du paramètre.

__Exemple :__

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

Cette balise permet de documenter l’accessibilité de sécurité d’un membre.

__Stockéesyntaxe__

```xml
<permission cref="member">description</permission>
```

où

* `member`nom d’un membre. Le générateur de documentation vérifie que l’élément de code donné existe et traduit le *membre* en nom d’élément canonique dans le fichier de documentation.
* `description`Description de l’accès au membre.

__Exemple :__

```csharp
/// <permission cref="System.Security.PermissionSet">Everyone can
/// access this method.</permission>

public static void Test() {
    // ...
}
```

### `<remarks>`

Cette balise est utilisée pour spécifier des informations supplémentaires sur un type. (Utilisez `<summary>` ([`<summary>`](documentation-comments.md#summary)) pour décrire le type lui-même et les membres d’un type.)

__Stockéesyntaxe__

```xml
<remarks>description</remarks>
```

où `description` est le texte de la remarque.

__Exemple :__

```csharp
/// <summary>Class <c>Point</c> models a point in a 
/// two-dimensional plane.</summary>
/// <remarks>Uses polar coordinates</remarks>
public class Point 
{
    // ...
}
```

### `<returns>`

Cette balise est utilisée pour décrire la valeur de retour d’une méthode.

__Stockéesyntaxe__

```xml
<returns>description</returns>
```

où `description` est une description de la valeur de retour.

__Exemple :__

```csharp
/// <summary>Report a point's location as a string.</summary>
/// <returns>A string representing a point's location, in the form (x,y),
///    without any leading, trailing, or embedded whitespace.</returns>
public override string ToString() {
    return "(" + X + "," + Y + ")";
}
```

### `<see>`

Cette balise permet de spécifier un lien dans le texte. Utilisez `<seealso>` [(`<seealso>`](documentation-comments.md#seealso)) pour indiquer le texte qui doit apparaître dans une section Voir aussi.

__Stockéesyntaxe__

```xml
<see cref="member"/>
```

où `member` est le nom d’un membre. Le générateur de documentation vérifie que l’élément de code donné existe et change de *membre* en nom d’élément dans le fichier de documentation généré.

__Exemple :__

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

Cette balise permet de générer une entrée pour la section Voir aussi. Utilisez `<see>` [(`<see>`](documentation-comments.md#see)) pour spécifier un lien à partir de texte.

__Stockéesyntaxe__

```xml
<seealso cref="member"/>
```

où `member` est le nom d’un membre. Le générateur de documentation vérifie que l’élément de code donné existe et change de *membre* en nom d’élément dans le fichier de documentation généré.

__Exemple :__

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

Cette balise peut être utilisée pour décrire un type ou un membre d’un type. Utilisez `<remarks>` [(`<remarks>`](documentation-comments.md#remarks)) pour décrire le type lui-même.

__Stockéesyntaxe__

```xml
<summary>description</summary>
```

où `description` est un résumé du type ou du membre.

__Exemple :__

```csharp
/// <summary>This constructor initializes the new Point to (0,0).</summary>
public Point() : this(0,0) {
}
```

### `<value>`

Cette balise permet de décrire une propriété.

__Stockéesyntaxe__

```xml
<value>property description</value>
```

où `property description` est une description de la propriété.

__Exemple :__

```csharp
/// <value>Property <c>X</c> represents the point's x-coordinate.</value>
public int X
{
    get { return x; }
    set { x = value; }
}
```

### `<typeparam>`

Cette balise est utilisée pour décrire un paramètre de type générique pour une classe, un struct, une interface, un délégué ou une méthode.

__Stockéesyntaxe__

```xml
<typeparam name="name">description</typeparam>
```

où `name` est le nom du paramètre de type et `description` est sa description.

__Exemple :__

```csharp
/// <summary>A generic list class.</summary>
/// <typeparam name="T">The type stored by the list.</typeparam>
public class MyList<T> {
    ...
}
```

### `<typeparamref>`

Cette balise est utilisée pour indiquer qu’un mot est un paramètre de type. Le fichier de documentation peut être traité pour mettre en forme ce paramètre de type de manière distincte.

__Stockéesyntaxe__

```xml
<typeparamref name="name"/>
```

où `name` est le nom du paramètre de type.

__Exemple :__

```csharp
/// <summary>This method fetches data and returns a list of <typeparamref name="T"/>.</summary>
/// <param name="query">query to execute</param>
public List<T> FetchData<T>(string query) {
    ...
}
```

## <a name="processing-the-documentation-file"></a>Traitement du fichier de documentation

Le générateur de documentation génère une chaîne d’ID pour chaque élément du code source qui est balisé avec un commentaire de documentation. Cette chaîne d’ID identifie de façon unique un élément source. Une visionneuse de documentation peut utiliser une chaîne d’ID pour identifier l’élément de métadonnées/réflexion correspondant auquel la documentation s’applique.

Le fichier de documentation n’est pas une représentation hiérarchique du code source ; au lieu de cela, il s’agit d’une liste plate avec une chaîne d’ID générée pour chaque élément.

### <a name="id-string-format"></a>Format de chaîne d’ID

Le générateur de documentation respecte les règles suivantes lorsqu’il génère les chaînes d’ID :

*  La chaîne ne contient aucun espace blanc.

*  La première partie de la chaîne identifie le type de membre documenté, par le biais d’un caractère unique suivi d’un signe deux-points. Les genres de membres suivants sont définis :

   | __Caractère__ | __Description__                                             |
   |---------------|-------------------------------------------------------------|
   | E             | Événement                                                       |
   | F             | Champ                                                       |
   | M             | Méthode (y compris les constructeurs, les destructeurs et les opérateurs) |
   | N             | Espace de noms                                                   |
   | P             | Propriété (y compris les indexeurs)                               |
   | T             | Type (par exemple Class, Delegate, enum, interface et struct) |
   | !             | Chaîne d’erreur ; le reste de la chaîne fournit des informations sur l’erreur. Par exemple, le générateur de documentation génère des informations d’erreur pour les liens qui ne peuvent pas être résolus. |

*  La deuxième partie de la chaîne est le nom qualifié complet de l’élément, en commençant à la racine de l’espace de noms. Le nom de l’élément, ses types englobants et l’espace de noms sont séparés par des points. Si le nom de l’élément lui-même comporte des points, ils `#(U+0023)` sont remplacés par des caractères. (Il est supposé qu’aucun élément ne possède ce caractère dans son nom.)
*  Pour les méthodes et les propriétés avec arguments, la liste d’arguments suit, placée entre parenthèses. Pour ceux sans arguments, les parenthèses sont omises. Les arguments sont séparés par des virgules. L’encodage de chaque argument est identique à une signature CLI, comme suit :
   *  Les arguments sont représentés par leur nom de documentation, qui est basé sur leur nom complet, modifié comme suit :
      * Les arguments qui représentent des types génériques ont un `` ` `` caractère ajouté (Tops) suivi du nombre de paramètres de type
      * Les arguments ayant `out` le `ref` modificateur ou ont `@` un nom de type suivant. Les arguments passés par valeur ou `params` via n’ont pas de notation spéciale.
      * Les arguments qui sont des tableaux sont représentés `[lowerbound:size, ... , lowerbound:size]` par le fait que le nombre de virgules est le rang moins un, et les limites inférieures et la taille de chaque dimension, si elles sont connues, sont représentées au format décimal. Si une limite inférieure ou une taille n’est pas spécifiée, elle est omise. Si la limite inférieure et la taille d’une dimension particulière sont omises, `:` le est également omis. Les tableaux en escalier sont représentés par un `[]` niveau par niveau.
      * Les arguments qui ont des types pointeur autres que void sont représentés `*` à l’aide d’un qui suit le nom de type. Un pointeur void est représenté à l’aide d’un `System.Void`nom de type.
      * Les arguments qui font référence aux paramètres de type générique définis sur les types sont encodés à l’aide du `` ` `` caractère (battement) suivi de l’index de base zéro du paramètre de type.
      * Les arguments qui utilisent des paramètres de type générique définis dans les méthodes utilisent un ``` `` ``` double-cycle `` ` `` à la place du utilisé pour les types.
      * Les arguments qui font référence aux types génériques construits sont encodés à l’aide du type `{`générique, suivi de, puis d’une liste séparée par des virgules `}`d’arguments de type, suivie de.

### <a name="id-string-examples"></a>Exemples de chaînes d’ID

Les exemples suivants montrent chacun un fragment de C# code, ainsi que la chaîne d’ID produite à partir de chaque élément source, susceptible d’avoir un commentaire de documentation :

*  Les types sont représentés à l’aide de leur nom qualifié complet et sont complétés par des informations génériques :

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

*  Les champs sont représentés par leur nom complet :

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

*  Des constructeurs.

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

*  Destructeurs.

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

*  Leurs.

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

*  Propriétés et indexeurs.

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

*  Événements.

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

*  Les opérateurs unaires.

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

   L’ensemble complet des noms de fonction d’opérateur unaire utilisés est `op_UnaryPlus`le `op_UnaryNegation`suivant : `op_OnesComplement`, `op_Increment`, `op_Decrement` `op_LogicalNot`, `op_True`,, `op_False`, et.

*  Opérateurs binaires.

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

   Le jeu complet de noms de fonctions d’opérateur binaires utilisé est `op_Addition`le `op_Subtraction`suivant : `op_Division`, `op_Modulus`, `op_BitwiseAnd` `op_Multiply`,, `op_ExclusiveOr`, `op_LeftShift`, `op_RightShift` `op_BitwiseOr`,,,, `op_Equality` ,`op_Inequality`, ,,`op_LessThan`et .`op_GreaterThanOrEqual` `op_LessThanOrEqual` `op_GreaterThan`

*  Les opérateurs de conversion ont un «`~`» de fin suivi du type de retour.

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

## <a name="an-example"></a>Exemple

### <a name="c-source-code"></a>C#code source

L’exemple suivant montre le code source d’une `Point` classe :

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

### <a name="resulting-xml"></a>XML résultant

Voici la sortie produite par un générateur de documentation en fonction du code source de la `Point`classe, comme indiqué ci-dessus :

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
