# <a name="documentation-comments"></a>Commentaires de documentation

C# fournit un mécanisme pour les programmeurs de documenter leur code à l’aide d’une syntaxe de commentaire spéciale qui contient le texte XML. Dans les fichiers de code source, les commentaires d’un format particulier peuvent être utilisés pour diriger un outil pour produire du code XML à partir de ces commentaires et les éléments de code source, ils précèdent. Des commentaires à l’aide de ce type de syntaxe sont appelés ***les commentaires de documentation***. Il doivent précéder immédiatement un type défini par l’utilisateur (par exemple, une classe, un délégué ou une interface) ou un membre (par exemple, un champ, événement, propriété ou méthode). L’outil de génération XML est appelé le ***Générateur de documentation***. (Ce générateur peut être, mais ne sont pas nécessairement, le compilateur c# lui-même). La sortie produite par le Générateur de documentation est appelée le ***fichier de documentation***. Un fichier de documentation est utilisé comme entrée pour un ***visionneuse de documentation***; un outil destiné à produire une sorte de visualisation des informations de type et sa documentation associée.

Cette spécification suggère un ensemble de balises à utiliser dans les commentaires de documentation, mais l’utilisation de ces balises n’est pas nécessaire et autres balises peuvent être utilisées si vous le souhaitez, comme la durée pendant laquelle les règles de code XML bien formé sont suivies.

## <a name="introduction"></a>Introduction

Des commentaires d’une forme spéciale peuvent être utilisés pour diriger un outil pour produire du code XML à partir de ces commentaires et les éléments de code source, ils précèdent. Ces commentaires sont des commentaires à ligne unique qui commencent par trois barres obliques (`///`), ou délimité par des commentaires qui commencent par une barre oblique et deux étoiles (`/**`). Il doivent précéder immédiatement un type défini par l’utilisateur (par exemple, une classe, un délégué ou une interface) ou un membre (par exemple, un champ, événement, propriété ou méthode) qu’elles annotent. Attribut de sections ([spécification des attributs](attributes.md#attribute-specification)) font partie des déclarations, donc les commentaires de documentation doivent précéder les attributs appliqués à un type ou membre.

__Syntaxe :__

```antlr
single_line_doc_comment
    : '///' input_character*
    ;

delimited_doc_comment
    : '/**' delimited_comment_section* asterisk+ '/'
    ;
```

Dans un *single_line_doc_comment*, s’il existe un *espace blanc* caractère qui suit le `///` caractères sur chacun de la *single_line_doc_comment*s adjacents actuel *single_line_doc_comment*, puis qui *espace blanc* caractère n’est pas inclus dans la sortie XML.

Dans un commentaire-doc-délimités, si le premier caractère non-espace blanc sur la deuxième ligne est un astérisque et le même modèle de caractères d’espace blanc facultatif et un astérisque est répétée au début de chaque ligne dans le commentaire-doc-délimités, puis les caractères de la série répétée ne sont pas inclus dans la sortie XML. Le modèle peut inclure des caractères d’espace blanc après, et avant le caractère astérisque.

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

Le texte dans les commentaires de documentation doit être correctement formé conformément aux règles de XML (http://www.w3.org/TR/REC-xml). Si le code XML est incorrect, un avertissement est généré et le fichier de documentation contiendra un commentaire indiquant qu’une erreur s’est produite.

Bien que les développeurs sont libres de créer leur propre jeu de balises, un jeu recommandé est défini dans [balises recommandées](documentation-comments.md#recommended-tags). Certaines des balises recommandées ont des significations spéciales :

*  Le `<param>` balise est utilisée pour décrire les paramètres. Si une telle balise est utilisée, le Générateur de documentation doit vérifier que le paramètre spécifié existe et que tous les paramètres sont décrits dans les commentaires de documentation. Si cette vérification échoue, le Générateur de documentation émet un avertissement.
*  L’attribut `cref` peut être joint à n’importe quelle balise pour fournir une référence à un élément de code. Le Générateur de documentation doit vérifier l’existence de cet élément de code. Si la vérification échoue, le Générateur de documentation émet un avertissement. Lors de la recherche pour un nom décrit dans un `cref` attribut, le Générateur de documentation doit respecter la visibilité de l’espace de noms en fonction de `using` les instructions situées dans le code source. Pour les éléments de code qui sont génériques, la syntaxe générique normale (c'est-à-dire «`List<T>`») ne peut pas être utilisé car il génère le code XML non valide. Accolades peuvent être utilisées au lieu de crochets (ie «`List{T}`»), ou la syntaxe d’échappement XML peut être utilisée (ie «`List&lt;T&gt;`»).
*  Le `<summary>` balise est destinée à être utilisée par une visionneuse de documentation pour afficher des informations supplémentaires sur un type ou membre.
*  Le `<include>` balise inclut des informations à partir d’un fichier XML externe.

Notez bien que le fichier de documentation ne fournit pas d’informations complètes sur le type et les membres (par exemple, il ne contient aucune information de type). Pour obtenir des informations complètes sur un type ou membre, le fichier de documentation doit être utilisé conjointement avec la réflexion sur le type réel ou le membre.

## <a name="recommended-tags"></a>Balises recommandées

Le Générateur de documentation doit accepter et traiter n’importe quelle balise est valide selon les règles de XML. Les balises suivantes fournissent des fonctionnalités couramment utilisées dans la documentation utilisateur. (Bien entendu, autres balises sont possibles.)


| __Balise__          | __Section__                                            | __Fonction__                                            |
|------------------|--------------------------------------------------------|--------------------------------------------------------|
| `<c>`            | [`<c>`](documentation-comments.md#c)                   | Définir le texte dans une police de type code                           | 
| `<code>`         | [`<code>`](documentation-comments.md#code)             | Définir une ou plusieurs lignes de sortie de code ou de programme source |
| `<example>`      | [`<example>`](documentation-comments.md#example)       | Indiquer un exemple                                    |
| `<exception>`    | [`<exception>`](documentation-comments.md#exception)   | Identifie les exceptions qu'une méthode peut lever.           |
| `<include>`      | [`<include>`](documentation-comments.md#include)       | Inclut le code XML à partir d’un fichier externe                     |
| `<list>`         | [`<list>`](documentation-comments.md#list)             | Créer une liste ou une table                                 |
| `<para>`         | [`<para>`](documentation-comments.md#para)             | Autoriser la structure à ajouter au texte                   |
| `<param>`        | [`<param>`](documentation-comments.md#param)           | Décrire un paramètre pour une méthode ou un constructeur       |
| `<paramref>`     | [`<paramref>`](documentation-comments.md#paramref)     | Déterminer si un mot est un nom de paramètre               |
| `<permission>`   | [`<permission>`](documentation-comments.md#permission) | L’accessibilité de sécurité d’un membre de document        |
| `<remark>`       | [`<remark>`](documentation-comments.md#remark)         | Décrire des informations supplémentaires sur un type           |
| `<returns>`      | [`<returns>`](documentation-comments.md#returns)       | Décrire la valeur de retour d’une méthode                  |
| `<see>`          | [`<see>`](documentation-comments.md#see)               | Spécifier un lien                                         |
| `<seealso>`      | [`<seealso>`](documentation-comments.md#seealso)       | Générer une entrée Voir aussi                              |
| `<summary>`      | [`<summary>`](documentation-comments.md#summary)       | Décrire un type ou un membre d’un type                  |
| `<value>`        | [`<value>`](documentation-comments.md#value)           | Décrire une propriété                                    |
| `<typeparam>`    |                                                        | Décrire un paramètre de type générique                      |
| `<typeparamref>` |                                                        | Déterminer si un mot est un nom de paramètre de type          |

### `<c>`

Cette balise fournit un mécanisme pour indiquer qu’un fragment de texte dans une description doit être défini dans une police spéciale tel que celui utilisé pour un bloc de code. Pour les lignes de code réel, utilisez `<code>` ([`<code>`](documentation-comments.md#code)).

__Syntaxe :__

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

Cette balise est utilisée pour définir une ou plusieurs lignes de sortie de code ou le programme source dans une police particulière. Pour les petits extraits de code dans la narration, utilisez `<c>` ([`<c>`](documentation-comments.md#c)).

__Syntaxe :__

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

Cette balise permet l’exemple de code dans un commentaire, pour spécifier comment une méthode ou autres membres de la bibliothèque peut être utilisée. En règle générale, cela implique également l’utilisation de la balise `<code>` ([`<code>`](documentation-comments.md#code)) également.

__Syntaxe :__

```xml
<example>description</example>
```

__Exemple :__

Consultez `<code>` ([`<code>`](documentation-comments.md#code)) pour obtenir un exemple.

### `<exception>`

Cette balise permet de documenter les exceptions qu'une méthode peut lever.

__Syntaxe :__

```xml
<exception cref="member">description</exception>
```

où

* `member` est le nom d’un membre. Le Générateur de documentation vérifie que le membre donné existe et traduit `member` au nom d’élément canonique dans le fichier de documentation.
* `description` est une description des circonstances dans lesquelles l’exception est levée.

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

Cette balise permet notamment d’informations à partir d’un document XML qui est externe au fichier de code source. Le fichier externe doit être un document XML bien formé, et une expression XPath est appliquée à ce document pour spécifier quel code XML à partir de ce document à inclure. Le `<include>` balise est ensuite remplacée par le XML à partir du document externe.

__Syntaxe :__

```
<include file="filename" path="xpath" />
```

où

* `filename` est le nom de fichier d’un fichier XML externe. Le nom de fichier est interprété relatif au fichier qui contient la balise include.
* `xpath` est une expression XPath qui sélectionne une partie du code XML dans le fichier XML externe.

__Exemple :__

Si le code source contenait une déclaration telle que :

```csharp
/// <include file="docs.xml" path='extradoc/class[@name="IntList"]/*' />
public class IntList { ... }
```

et le fichier externe « docs.xml » contenait avait le contenu suivant :

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

puis la documentation même est sortie comme si le code source contenu :

```csharp
/// <summary>
///    Contains a list of integers.
/// </summary>
public class IntList { ... }
```

### `<list>`

Cette balise est utilisée pour créer une liste ou un tableau d’éléments. Il peut contenir un `<listheader>` bloc pour définir la ligne d’en-tête d’une table ou une définition de liste. (Lors de la définition d’une table, une entrée pour `term` dans l’en-tête doivent être fournies.)

Chaque élément dans la liste est spécifié avec un `<item>` bloc. Lors de la création d’une liste de définitions, les deux `term` et `description` doit être spécifié. Toutefois, pour une table, une liste à puces ou une liste numérotée `description` besoin d’être spécifiés.

__Syntaxe :__

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

* `term` est le terme à définir, dont la définition est dans `description`.
* `description` est un élément dans une liste à puces ou une liste numérotée, ou la définition d’un `term`.

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

Cette balise est pour une utilisation dans d’autres balises, tel que `<summary>` ([`<remark>`](documentation-comments.md#remark)) ou `<returns>` ([`<returns>`](documentation-comments.md#returns)) et autorise la structure à ajouter au texte.

__Syntaxe :__

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

Cette balise est utilisée pour décrire un paramètre pour une méthode, un constructeur ou un indexeur.

__Syntaxe :__

```xml
<param name="name">description</param>
```

où

* `name` Est le nom du paramètre.
* `description` est une description du paramètre.

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

Cette balise est utilisée pour indiquer qu’un mot est un paramètre. Le fichier de documentation peut être traité pour mettre en forme ce paramètre d’une manière distincte.

__Syntaxe :__

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

Cette balise permet l’accessibilité de sécurité d’un membre à documenter.

__Syntaxe :__

```xml
<permission cref="member">description</permission>
```

où

* `member` est le nom d’un membre. Le Générateur de documentation vérifie que l’élément de code donné existe et traduit *membre* au nom d’élément canonique dans le fichier de documentation.
* `description` est une description de l’accès au membre.

__Exemple :__

```csharp
/// <permission cref="System.Security.PermissionSet">Everyone can
/// access this method.</permission>

public static void Test() {
    // ...
}
```

### `<remark>`

Cette balise est utilisée pour spécifier des informations supplémentaires sur un type. (Utilisez `<summary>` ([`<summary>`](documentation-comments.md#summary)) pour décrire le type lui-même et les membres d’un type.)

__Syntaxe :__

```xml
<remark>description</remark>
```

où `description` est le texte de la Remarque.

__Exemple :__

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

Cette balise est utilisée pour décrire la valeur de retour d’une méthode.

__Syntaxe :__

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

Cette balise permet un lien à l’intérieur de texte. Utilisez `<seealso>` ([`<seealso>`](documentation-comments.md#seealso)) pour indiquer le texte qui doit apparaître dans une section Voir aussi.

__Syntaxe :__

```xml
<see cref="member"/>
```

où `member` est le nom d’un membre. Le Générateur de documentation vérifie que l’élément de code donné existe et qu’il modifie *membre* au nom d’élément dans le fichier de documentation générées.

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

Cette balise permet une entrée doit être généré pour la section Voir aussi. Utilisez `<see>` ([`<see>`](documentation-comments.md#see)) pour spécifier un lien à partir du texte.

__Syntaxe :__

```xml
<seealso cref="member"/>
```

où `member` est le nom d’un membre. Le Générateur de documentation vérifie que l’élément de code donné existe et qu’il modifie *membre* au nom d’élément dans le fichier de documentation générées.

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

Cette balise peut être utilisée pour décrire un type ou un membre d’un type. Utilisez `<remark>` ([`<remark>`](documentation-comments.md#remark)) pour décrire le type lui-même.

__Syntaxe :__

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

Cette balise permet à une propriété à décrire.

__Syntaxe :__

```xml
<value>property description</value>
```

où `property description` est une description pour la propriété.

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

__Syntaxe :__

```xml
<typeparam name="name">description</typeparam>
```

où `name` est le nom du paramètre de type, et `description` est sa description.

__Exemple :__

```csharp
/// <summary>A generic list class.</summary>
/// <typeparam name="T">The type stored by the list.</typeparam>
public class MyList<T> {
    ...
}
```

### `<typeparamref>`

Cette balise est utilisée pour indiquer qu’un mot est un paramètre de type. Le fichier de documentation peut être traité pour mettre en forme ce paramètre de type d’une manière distincte.

__Syntaxe :__

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

Le Générateur de documentation génère une chaîne d’ID pour chaque élément dans le code source qui est marqué avec un commentaire de documentation. Cette chaîne d’ID identifie de façon unique un élément source. Une visionneuse de documentation peut utiliser une chaîne d’ID pour identifier l’élément de métadonnées/réflexion correspondant auquel s’applique la documentation.

Le fichier de documentation n’est pas une représentation hiérarchique du code source ; au lieu de cela, il est une liste plate avec une chaîne d’ID générée pour chaque élément.

### <a name="id-string-format"></a>Format de chaîne d’ID

Le Générateur de documentation respecte les règles suivantes lorsqu’il génère les chaînes d’ID :

*  La chaîne ne contient aucun espace blanc.

*  La première partie de la chaîne identifie le type de membre documenté, à l’aide d’un caractère unique suivi de deux-points. Les types de membres suivants sont définis :

   | __Caractère__ | __Description__                                             |
   |---------------|-------------------------------------------------------------|
   | E             | Événement                                                       |
   | F             | Champ                                                       |
   | M             | Méthode (y compris les constructeurs, destructeurs et opérateurs) |
   | N             | Espace de noms                                                   |
   | P             | Propriété (y compris les indexeurs)                               |
   | T             | Type (par exemple, la classe délégué, enum, interface et struct) |
   | !             | Chaîne d’erreur ; le reste de la chaîne fournit des informations sur l’erreur. Par exemple, le Générateur de documentation génère des informations d’erreur pour les liens qui ne peut pas être résolus. |

*  La deuxième partie de la chaîne est le nom qualifié complet de l’élément, en commençant à la racine de l’espace de noms. Le nom de l’élément, son type (s) d’englobante et un espace de noms sont séparés par des points. Si le nom de l’élément lui-même comporte des points, ils sont remplacés par `#(U+0023)` caractères. (Il est supposé qu’aucun élément n’a ce caractère dans son nom.)
*  Pour les méthodes et propriétés avec des arguments, l’argument liste suit, placés entre parenthèses. Pour ceux sans arguments, les parenthèses sont omises. Les arguments sont séparés par des virgules. L’encodage de chaque argument est identique à une signature CLI, comme suit :
   *  Arguments sont représentés par leur nom de la documentation, qui est basé sur leur nom qualifié complet, modifié comme suit :
      * Les arguments qui représentent des types génériques ont ajoutée « ' » suivi par le nombre de paramètres de type
      * Arguments ayant la `out` ou `ref` modificateur ont un `@` suivant leur nom de type. Arguments passés par valeur ou par le biais de `params` n’ont aucun notation spéciale.
      * Les arguments qui sont des tableaux sont représentés en tant que `[lowerbound:size, ... , lowerbound:size]` où le nombre de virgules correspond au rang moins un, et les limites inférieure et la taille de chaque dimension, s’il est connu, sont représentées sous forme décimale. Si une limite inférieure ou une taille n’est pas spécifié, il est omis. Si la limite inférieure et la taille d’une dimension particulière sont omises, le «`:`» est également omis. Tableaux en escalier sont représentés par une «`[]`» par niveau.
      * Les arguments qui ont des types de pointeur autre que void sont représentés en utilisant un `*` après le nom de type. Un pointeur void est représenté à l’aide d’un nom de type de `System.Void`.
      * Les arguments qui font référence aux paramètres de type générique définies sur les types sont encodés à l’aide de la « ' » suivi par l’index de base zéro du paramètre de type.
      * Les arguments qui utilisent des paramètres de type générique définies dans les méthodes utilisent un double-accent grave »\`\`» au lieu de la «\`» utilisé pour les types.
      * Arguments qui font référence aux types génériques construits sont encodés à l’aide de type générique, suivi par « { », suivi d’une liste séparée par des virgules d’arguments de type, suivi par «} ».

### <a name="id-string-examples"></a>Exemples de chaînes d’ID

Les exemples suivants montrent chacun un fragment de code c#, ainsi que la chaîne d’ID produite à partir de chaque élément source capable d’avoir un commentaire de documentation :

*  Les types sont représentés à l’aide de leur nom qualifié complet, laquelle s’ajoutent les informations génériques :

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

*  Les champs sont représentés par leur nom qualifié complet :

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

*  Méthodes.

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

*  événements.

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

   L’ensemble complet des noms de fonctions d’opérateur unaire utilisé est le suivant : `op_UnaryPlus`, `op_UnaryNegation`, `op_LogicalNot`, `op_OnesComplement`, `op_Increment`, `op_Decrement`, `op_True`, et `op_False`.

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

   L’ensemble complet des noms de fonctions d’opérateur binaire utilisé est le suivant : `op_Addition`, `op_Subtraction`, `op_Multiply`, `op_Division`, `op_Modulus`, `op_BitwiseAnd`, `op_BitwiseOr`, `op_ExclusiveOr`, `op_LeftShift`, `op_RightShift`, `op_Equality`, `op_Inequality`, `op_LessThan`, `op_LessThanOrEqual`, `op_GreaterThan`, et `op_GreaterThanOrEqual`.

*  Opérateurs de conversion ont une fin «`~`» suivi du type de retour.

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

## <a name="an-example"></a>Un exemple

### <a name="c-source-code"></a>Code source c#

L’exemple suivant montre le code source d’un `Point` classe :

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

### <a name="resulting-xml"></a>Données XML résultantes

Voici la sortie produite par un générateur de documentation donné le code source pour la classe `Point`, comme indiqué ci-dessus :

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
