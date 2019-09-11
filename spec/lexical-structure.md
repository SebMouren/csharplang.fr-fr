---
ms.openlocfilehash: 5fbe0267b5b33b1a24dbdca493d118c576092573
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876910"
---
# <a name="lexical-structure"></a>Structure lexicale

## <a name="programs"></a>Programs

Un C# ***programme*** se compose d’un ou de plusieurs ***fichiers sources***, connus sous le nom d' ***unités de compilation*** ([unités de compilation](namespaces.md#compilation-units)). Un fichier source est une séquence ordonnée de caractères Unicode. Les fichiers sources ont généralement une correspondance un-à-un avec les fichiers dans un système de fichiers, mais cette correspondance n’est pas nécessaire. Pour une portabilité maximale, il est recommandé d’encoder les fichiers d’un système de fichiers avec l’encodage UTF-8.

Conceptuellement, un programme est compilé à l’aide de trois étapes :

1. Transformation, qui convertit un fichier d’un répertoire de caractères particulier et d’un schéma d’encodage en une séquence de caractères Unicode.
2. Analyse lexicale, qui convertit un flux de caractères d’entrée Unicode en un flux de jetons.
3. Analyse syntaxique, qui convertit le flux de jetons en code exécutable.

## <a name="grammars"></a>Grammaires

Cette spécification présente la syntaxe du langage C# de programmation à l’aide de deux grammaires. La ***grammaire lexicale*** ([grammaire lexicale](lexical-structure.md#lexical-grammar)) définit la combinaison des caractères Unicode avec les indicateurs de fin de ligne, les espaces blancs, les commentaires, les jetons et les directives de pré-traitement. La ***grammaire syntaxique*** ([syntaxe syntaxique](lexical-structure.md#syntactic-grammar)) définit la façon dont les jetons résultant de la grammaire lexicale sont combinés C# pour former des programmes.

### <a name="grammar-notation"></a>Notation grammaticale

Les grammaires lexicales et syntaxiques sont présentées dans la forme Backus-Naur à l’aide de la notation de l’outil de grammaire ANTLR.

### <a name="lexical-grammar"></a>Grammaire lexicale

La grammaire lexicale C# de est présentée dans l' [analyse lexicale](lexical-structure.md#lexical-analysis), les [jetons](lexical-structure.md#tokens)et les [directives de pré-traitement](lexical-structure.md#pre-processing-directives). Les symboles terminaux de la grammaire lexicale sont les caractères du jeu de caractères Unicode, tandis que la grammaire lexicale spécifie la manière dont les caractères sont combinés pour former des jetons ([jetons](lexical-structure.md#tokens)), un espace blanc ([espace blanc](lexical-structure.md#white-space)), des commentaires ([Commentaires](lexical-structure.md#comments)) et directives de prétraitement ([directives de pré-traitement](lexical-structure.md#pre-processing-directives)).

Chaque fichier source d’un C# programme doit être conforme à la production *d’entrée* de la grammaire lexicale ([analyse lexicale](lexical-structure.md#lexical-analysis)).

### <a name="syntactic-grammar"></a>Syntaxe syntaxique

La grammaire syntaxique de C# est présentée dans les chapitres et les annexes qui suivent ce chapitre. Les symboles terminaux de la grammaire syntaxique sont les jetons définis par la grammaire lexicale, et la grammaire syntaxique spécifie la manière dont les jetons sont C# combinés pour former des programmes.

Chaque fichier source d’un C# programme doit se conformer à la production *compilation_unit* de la grammaire syntaxique ([unités de compilation](namespaces.md#compilation-units)).

## <a name="lexical-analysis"></a>Analyse lexicale

La production *d’entrée* définit la structure lexicale C# d’un fichier source. Chaque fichier source d’un C# programme doit se conformer à cette production grammaticale lexicale.

```antlr
input
    : input_section?
    ;

input_section
    : input_section_part+
    ;

input_section_part
    : input_element* new_line
    | pp_directive
    ;

input_element
    : whitespace
    | comment
    | token
    ;
```

Cinq éléments de base composent la structure lexicale d’un C# fichier source : Marques de fin de ligne ([indicateurs de fin de ligne](lexical-structure.md#line-terminators)), espace blanc ([espace blanc](lexical-structure.md#white-space)), commentaires ([Commentaires](lexical-structure.md#comments)), jetons ([jetons](lexical-structure.md#tokens)) et directives de prétraitement ([directives de pré-traitement](lexical-structure.md#pre-processing-directives)). Parmi ces éléments de base, seuls les jetons sont significatifs dans la grammaire syntaxique C# d’un programme (syntaxe[syntaxique](lexical-structure.md#syntactic-grammar)).

Le traitement lexical d’un C# fichier source consiste à réduire le fichier en une séquence de jetons qui devient l’entrée de l’analyse syntaxique. Les indicateurs de fin de ligne, les espaces blancs et les commentaires peuvent servir à séparer les jetons, et les directives de prétraitement peuvent entraîner l’omission des sections du fichier source, mais ces éléments lexicaux n’ont aucun impact sur la structure C# syntaxique d’un programme.

Dans le cas de littéraux de chaîne interpolés ([littéraux de chaîne interpolés](lexical-structure.md#interpolated-string-literals)), un seul jeton est initialement généré par l’analyse lexicale, mais il est divisé en plusieurs éléments d’entrée qui sont soumis à l’analyse lexicale de manière répétée jusqu’à ce que toutes les interpolations les littéraux de chaîne ont été résolus. Les jetons résultants servent ensuite d’entrée à l’analyse syntaxique.

Lorsque plusieurs productions grammaticales lexicales correspondent à une séquence de caractères dans un fichier source, le traitement lexical forme toujours l’élément lexical le plus long possible. Par exemple, la séquence `//` de caractères est traitée comme le début d’un commentaire sur une seule ligne, car cet élément lexical est plus long qu’un seul `/` jeton.

### <a name="line-terminators"></a>Marques de fin de ligne

Les marques de fin de ligne divisent les caractères d’un C# fichier source en lignes.

```antlr
new_line
    : '<Carriage return character (U+000D)>'
    | '<Line feed character (U+000A)>'
    | '<Carriage return character (U+000D) followed by line feed character (U+000A)>'
    | '<Next line character (U+0085)>'
    | '<Line separator character (U+2028)>'
    | '<Paragraph separator character (U+2029)>'
    ;
```

Pour la compatibilité avec les outils d’édition de code source qui ajoutent des marqueurs de fin de fichier et pour permettre à un fichier source d’être affiché sous la forme d’une séquence de lignes correctement terminées, les transformations suivantes sont appliquées, C# dans l’ordre, à chaque fichier source d’un programme :

*  Si le dernier caractère du fichier source est un caractère de contrôle Z (`U+001A`), ce caractère est supprimé.
*  Un caractère de retour chariot (`U+000D`) est ajouté à la fin du fichier source si ce fichier source n’est pas vide et si le dernier caractère du fichier source n’est pas un retour chariot (`U+000D`), un saut de ligne (`U+000A`), un séparateur de ligne (`U+2028`) ou un séparateur de paragraphe (`U+2029`).

### <a name="comments"></a>Commentaires

Deux formes de commentaires sont pris en charge : commentaires sur une seule ligne et commentaires délimités. Les ***Commentaires sur une seule ligne*** commencent par `//` les caractères et s’étendent jusqu’à la fin de la ligne source. Les ***Commentaires délimités*** commencent par `/*` les caractères et se terminent par les caractères. `*/` Les commentaires délimités peuvent s’étendre sur plusieurs lignes.

```antlr
comment
    : single_line_comment
    | delimited_comment
    ;

single_line_comment
    : '//' input_character*
    ;

input_character
    : '<Any Unicode character except a new_line_character>'
    ;

new_line_character
    : '<Carriage return character (U+000D)>'
    | '<Line feed character (U+000A)>'
    | '<Next line character (U+0085)>'
    | '<Line separator character (U+2028)>'
    | '<Paragraph separator character (U+2029)>'
    ;

delimited_comment
    : '/*' delimited_comment_section* asterisk+ '/'
    ;

delimited_comment_section
    : '/'
    | asterisk* not_slash_or_asterisk
    ;

asterisk
    : '*'
    ;

not_slash_or_asterisk
    : '<Any Unicode character except / or *>'
    ;
```

Les commentaires ne sont pas imbriqués. `/*` Les séquences de caractères `*/` et n’ont aucune signification particulière `//` dans un commentaire, et les séquences `/*` `//` de caractères et n’ont aucune signification particulière dans un commentaire délimité.

Les commentaires ne sont pas traités dans des littéraux de chaîne et de caractère.

L’exemple
```csharp
/* Hello, world program
   This program writes "hello, world" to the console
*/
class Hello
{
    static void Main() {
        System.Console.WriteLine("hello, world");
    }
}
```
comprend un commentaire délimité.

L’exemple
```csharp
// Hello, world program
// This program writes "hello, world" to the console
//
class Hello // any name will do for this class
{
    static void Main() { // this method must be named "Main"
        System.Console.WriteLine("hello, world");
    }
}
```
affiche plusieurs commentaires sur une seule ligne.

### <a name="white-space"></a>Espace blanc

Un espace blanc est défini comme n’importe quel caractère avec la classe Unicode Zs (qui comprend le caractère espace), ainsi que le caractère de tabulation horizontale, le caractère de tabulation verticale et le caractère de saut de forme.

```antlr
whitespace
    : '<Any character with Unicode class Zs>'
    | '<Horizontal tab character (U+0009)>'
    | '<Vertical tab character (U+000B)>'
    | '<Form feed character (U+000C)>'
    ;
```

## <a name="tokens"></a>jetons

Il existe plusieurs genres de jetons : les identificateurs, les mots clés, les littéraux, les opérateurs et les signes de ponctuation. Les espaces blancs et les commentaires ne sont pas des jetons, bien qu’ils agissent comme séparateurs pour les jetons.

```antlr
token
    : identifier
    | keyword
    | integer_literal
    | real_literal
    | character_literal
    | string_literal
    | interpolated_string_literal
    | operator_or_punctuator
    ;
```

### <a name="unicode-character-escape-sequences"></a>Séquences d’échappement de caractères Unicode

Une séquence d’échappement de caractère Unicode représente un caractère Unicode. Les séquences d’échappement de caractères Unicode sont traitées dans les identificateurs ([identificateurs](lexical-structure.md#identifiers)), les littéraux de caractère ([littéraux de caractère](lexical-structure.md#character-literals)) et les littéraux de chaîne normaux ([littéraux de chaîne](lexical-structure.md#string-literals)). Un caractère d’échappement Unicode n’est pas traité à un autre emplacement (par exemple, pour former un opérateur, un signe de ponctuation ou un mot clé).

```antlr
unicode_escape_sequence
    : '\\u' hex_digit hex_digit hex_digit hex_digit
    | '\\U' hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit
    ;
```

Une séquence d’échappement Unicode représente le caractère Unicode unique formé par le nombre hexadécimal suivant les`\u`caractères « »`\U`ou « ». Étant C# donné que utilise un encodage 16 bits de points de code Unicode en caractères et des valeurs de chaîne, un caractère Unicode dans la plage comprise entre u + 10000 et u + 10FFFF n’est pas autorisé dans un littéral de caractère et est représenté à l’aide d’une paire de substitution Unicode dans un littéral de chaîne. Les caractères Unicode avec des points de code au-dessus de 0x10FFFF ne sont pas pris en charge.

Plusieurs traductions ne sont pas effectuées. Par exemple, le littéral de chaîne`\u005Cu005C`« » est équivalent à`\u005C`« » plutôt qu'`\`à «». La valeur `\u005C` Unicode est le caractère «`\`».

L’exemple
```csharp
class Class1
{
    static void Test(bool \u0066) {
        char c = '\u0066';
        if (\u0066)
            System.Console.WriteLine(c.ToString());
    }        
}
```
montre plusieurs utilisations de `\u0066`, qui est la séquence d’échappement pour la lettre`f`«». Le programme est équivalent à
```csharp
class Class1
{
    static void Test(bool f) {
        char c = 'f';
        if (f)
            System.Console.WriteLine(c.ToString());
    }        
}
```

### <a name="identifiers"></a>Identificateurs

Les règles relatives aux identificateurs fournis dans cette section correspondent exactement à celles recommandées par la norme Unicode annexe 31, sauf que le trait de soulignement est autorisé comme caractère initial (comme c’est le cas dans le langage de programmation C), les séquences d’échappement Unicode sont autorisé dans les identificateurs, et le`@`caractère «» est autorisé en tant que préfixe pour permettre l’utilisation de mots clés comme identificateurs.

```antlr
identifier
    : available_identifier
    | '@' identifier_or_keyword
    ;

available_identifier
    : '<An identifier_or_keyword that is not a keyword>'
    ;

identifier_or_keyword
    : identifier_start_character identifier_part_character*
    ;

identifier_start_character
    : letter_character
    | '_'
    ;

identifier_part_character
    : letter_character
    | decimal_digit_character
    | connecting_character
    | combining_character
    | formatting_character
    ;

letter_character
    : '<A Unicode character of classes Lu, Ll, Lt, Lm, Lo, or Nl>'
    | '<A unicode_escape_sequence representing a character of classes Lu, Ll, Lt, Lm, Lo, or Nl>'
    ;

combining_character
    : '<A Unicode character of classes Mn or Mc>'
    | '<A unicode_escape_sequence representing a character of classes Mn or Mc>'
    ;

decimal_digit_character
    : '<A Unicode character of the class Nd>'
    | '<A unicode_escape_sequence representing a character of the class Nd>'
    ;

connecting_character
    : '<A Unicode character of the class Pc>'
    | '<A unicode_escape_sequence representing a character of the class Pc>'
    ;

formatting_character
    : '<A Unicode character of the class Cf>'
    | '<A unicode_escape_sequence representing a character of the class Cf>'
    ;
```

Pour plus d’informations sur les classes de caractères Unicode mentionnées ci-dessus, consultez la norme Unicode, version 3,0, section 4,5.

«`identifier1`», «`_identifier2`» Et «`@if`» sont des exemples d’identificateurs valides.

Un identificateur dans un programme conforme doit être au format canonique défini par le formulaire de normalisation Unicode C, comme défini par la norme Unicode annexe 15. Le comportement lors de la rencontre d’un identificateur qui ne figure pas dans le formulaire de normalisation C est défini par l’implémentation ; Toutefois, aucun diagnostic n’est nécessaire.

Le préfixe`@`«» permet d’utiliser des mots clés en tant qu’identificateurs, ce qui est utile lors de l’interfaçage avec d’autres langages de programmation. Le caractère `@` ne faisant pas réellement partie de l’identificateur, l’identificateur peut être vu dans d’autres langages comme un identificateur normal, sans le préfixe. Un identificateur avec un `@` préfixe est appelé ***identificateur textuel***. L’utilisation du `@` préfixe pour les identificateurs qui ne sont pas des mots clés est autorisée, mais elle est fortement déconseillée comme une question de style.

L’exemple suivant :
```csharp
class @class
{
    public static void @static(bool @bool) {
        if (@bool)
            System.Console.WriteLine("true");
        else
            System.Console.WriteLine("false");
    }    
}

class Class1
{
    static void M() {
        cl\u0061ss.st\u0061tic(true);
    }
}
```
définit une classe nommée «`class`» avec une méthode statique nommée «`static`» qui prend un paramètre nommé «`bool`». Notez que dans la mesure où les échappements Unicode ne sont pas autorisés dans`cl\u0061ss`les mots clés, le jeton « » est un identificateur et est le`@class`même identificateur que « ».

Deux identificateurs sont considérés comme identiques s’ils sont identiques après l’application des transformations suivantes, dans l’ordre :

*  Le préfixe`@`«», s’il est utilisé, est supprimé.
*  Chaque *unicode_escape_sequence* est transformée en son caractère Unicode correspondant.
*  Tous les *formatting_character*sont supprimés.

Les identificateurs contenant deux traits de soulignement consécutifs (`U+005F`) sont réservés pour une utilisation par l’implémentation. Par exemple, une implémentation peut fournir des mots clés étendus qui commencent par deux traits de soulignement.

### <a name="keywords"></a>Mots clés

Un ***mot clé*** est une séquence de caractères de type identificateur, qui est réservée et ne peut pas être utilisée en tant qu’identificateur, sauf s’il `@` est précédé du caractère.

```antlr
keyword
    : 'abstract' | 'as'       | 'base'       | 'bool'      | 'break'
    | 'byte'     | 'case'     | 'catch'      | 'char'      | 'checked'
    | 'class'    | 'const'    | 'continue'   | 'decimal'   | 'default'
    | 'delegate' | 'do'       | 'double'     | 'else'      | 'enum'
    | 'event'    | 'explicit' | 'extern'     | 'false'     | 'finally'
    | 'fixed'    | 'float'    | 'for'        | 'foreach'   | 'goto'
    | 'if'       | 'implicit' | 'in'         | 'int'       | 'interface'
    | 'internal' | 'is'       | 'lock'       | 'long'      | 'namespace'
    | 'new'      | 'null'     | 'object'     | 'operator'  | 'out'
    | 'override' | 'params'   | 'private'    | 'protected' | 'public'
    | 'readonly' | 'ref'      | 'return'     | 'sbyte'     | 'sealed'
    | 'short'    | 'sizeof'   | 'stackalloc' | 'static'    | 'string'
    | 'struct'   | 'switch'   | 'this'       | 'throw'     | 'true'
    | 'try'      | 'typeof'   | 'uint'       | 'ulong'     | 'unchecked'
    | 'unsafe'   | 'ushort'   | 'using'      | 'virtual'   | 'void'
    | 'volatile' | 'while'
    ;
```

À certains endroits de la grammaire, les identificateurs spécifiques ont une signification particulière, mais ne sont pas des mots clés. Ces identificateurs sont parfois appelés « Mots-clés contextuels ». Par exemple, dans une déclaration de propriété, les`get`identificateurs «`set`» et «» ont une signification spéciale ([accesseurs](classes.md#accessors)). Comme un identificateur autre que `get` ou `set` n’est jamais autorisé dans ces emplacements, cette utilisation n’est pas en conflit avec l’utilisation de ces mots comme identificateurs. Dans d’autres cas, comme avec l’identificateur «`var`» dans les déclarations de variables locales implicitement typées (déclarations de[variables locales](statements.md#local-variable-declarations)), un mot clé contextuel peut être en conflit avec des noms déclarés. Dans ce cas, le nom déclaré est prioritaire par rapport à l’utilisation de l’identificateur en tant que mot clé contextuel.

### <a name="literals"></a>Littéraux

Un ***littéral*** est une représentation du code source d’une valeur.

```antlr
literal
    : boolean_literal
    | integer_literal
    | real_literal
    | character_literal
    | string_literal
    | null_literal
    ;
```

#### <a name="boolean-literals"></a>Littéraux booléens

Il existe deux valeurs littérales booléennes `false`: `true` et.

```antlr
boolean_literal
    : 'true'
    | 'false'
    ;
```

Le type d’un *boolean_literal* est `bool`.

#### <a name="integer-literals"></a>Littéraux d'entier

Les littéraux entiers sont utilisés pour écrire des `int`valeurs `uint`de `long`types, `ulong`, et. Les littéraux entiers ont deux formes possibles : decimal et hexadécimal.

```antlr
integer_literal
    : decimal_integer_literal
    | hexadecimal_integer_literal
    ;

decimal_integer_literal
    : decimal_digit+ integer_type_suffix?
    ;

decimal_digit
    : '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9'
    ;

integer_type_suffix
    : 'U' | 'u' | 'L' | 'l' | 'UL' | 'Ul' | 'uL' | 'ul' | 'LU' | 'Lu' | 'lU' | 'lu'
    ;

hexadecimal_integer_literal
    : '0x' hex_digit+ integer_type_suffix?
    | '0X' hex_digit+ integer_type_suffix?
    ;

hex_digit
    : '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9'
    | 'A' | 'B' | 'C' | 'D' | 'E' | 'F' | 'a' | 'b' | 'c' | 'd' | 'e' | 'f';
```

Le type d’un littéral entier est déterminé comme suit :

*  Si le littéral n’a pas de suffixe, il a le premier de ces types dans lequel sa valeur peut `int`être `uint`représentée : `ulong`,, `long`,.
*  Si le littéral est suivi d’un `U` suffixe par ou `u`, il a le premier de ces types dans lequel sa valeur peut `uint`être `ulong`représentée :,.
*  Si le littéral est suivi d’un `L` suffixe par ou `l`, il a le premier de ces types dans lequel sa valeur peut `long`être `ulong`représentée :,.
*  Si le littéral est précédé `UL`d’un suffixe, `ul` `Ul`, `LU` `uL`, `Lu`, `lU`,, `lu`ou, il est de `ulong`type.

Si la valeur représentée par un littéral entier est en dehors de la plage `ulong` du type, une erreur de compilation se produit.

En guise de style, il est suggéré d’utiliser`L`« » à la place de`l`« » lors de l’écriture de `long`littéraux de type, car il est facile de confondre la lettre «`1``l`» avec le chiffre «».

Pour que les valeurs `int` et `long` les plus petites soient écrites en tant que littéraux décimaux entiers, les deux règles suivantes existent :

* Lorsqu’un *decimal_integer_literal* avec la valeur 2147483648 (2 ^ 31) et qu’aucun *integer_type_suffix* n’apparaît en tant que jeton qui suit immédiatement un jeton d’opérateur moins unaire ([opérateur moins unaire](expressions.md#unary-minus-operator)), le résultat est une constante de type `int`avec la valeur-2147483648 (-2 ^ 31). Dans toutes les autres situations, une telle *decimal_integer_literal* est de `uint`type.
* Lorsqu’un *decimal_integer_literal* avec la valeur 9223372036854775808 (2 ^ 63) et aucun *integer_type_suffix* ou *integer_type_suffix* `L` ou `l` apparaît comme jeton immédiatement après un signe moins unaire jeton d’opérateur ([opérateur moins unaire](expressions.md#unary-minus-operator)), le résultat est une constante `long` de type avec la valeur-9223372036854775808 (-2 ^ 63). Dans toutes les autres situations, une telle *decimal_integer_literal* est de `ulong`type.

#### <a name="real-literals"></a>Littéraux réels

Les littéraux réels sont utilisés pour écrire des valeurs `float`de `double`types, `decimal`et.

```antlr
real_literal
    : decimal_digit+ '.' decimal_digit+ exponent_part? real_type_suffix?
    | '.' decimal_digit+ exponent_part? real_type_suffix?
    | decimal_digit+ exponent_part real_type_suffix?
    | decimal_digit+ real_type_suffix
    ;

exponent_part
    : 'e' sign? decimal_digit+
    | 'E' sign? decimal_digit+
    ;

sign
    : '+'
    | '-'
    ;

real_type_suffix
    : 'F' | 'f' | 'D' | 'd' | 'M' | 'm'
    ;
```

Si aucun *real_type_suffix* n’est spécifié, le type du littéral réel est `double`. Dans le cas contraire, le suffixe de type réel détermine le type du littéral réel, comme suit :

*  Littéral réel dont le suffixe `F` est `f` ou est de `float`type. Par exemple, les littéraux `1f`, `1.5f`, `1e10f`et `123.456F` sont tous de type `float`.
*  Littéral réel dont le suffixe `D` est `d` ou est de `double`type. Par exemple, les littéraux `1d`, `1.5d`, `1e10d`et `123.456D` sont tous de type `double`.
*  Littéral réel dont le suffixe `M` est `m` ou est de `decimal`type. Par exemple, les littéraux `1m`, `1.5m`, `1e10m`et `123.456M` sont tous de type `decimal`. Ce littéral est converti en une `decimal` valeur en acceptant la valeur exacte et, si nécessaire, en arrondissant à la valeur représentable la plus proche à l’aide de l’arrondi de Banker ([type décimal](types.md#the-decimal-type)). Toute échelle apparente dans le littéral est conservée, sauf si la valeur est arrondie ou si la valeur est égale à zéro (dans ce dernier cas, le signe et l’échelle sont 0). Par conséquent, le `2.900m` littéral sera analysé pour former la décimale avec le `0`signe, le `2900`coefficient et `3`l’échelle.

Si le littéral spécifié ne peut pas être représenté dans le type indiqué, une erreur de compilation se produit.

La valeur d’un littéral réel de type `float` ou `double` est déterminée à l’aide du mode IEEE « arrondi au plus proche ».

Notez que dans un littéral réel, les chiffres décimaux sont toujours requis après la virgule décimale. Par exemple, `1.3F` est un littéral réel, `1.F` mais pas.

#### <a name="character-literals"></a>Littéraux de caractère

Un littéral de caractère représente un caractère unique et se compose généralement d’un caractère entre guillemets, `'a'`comme dans.

Remarque : La notation de la grammaire ANTLR rend ce qui est confus. Dans antlr, lorsque vous écrivez `\'` , il s’agit d’un `'`guillemet simple. Lorsque vous écrivez `\\` , il s’agit d’une barre `\`oblique inverse unique. Par conséquent, la première règle pour un littéral de caractère signifie qu’il commence par un guillemet simple, puis par un caractère, puis par un guillemet simple. Et les onze séquences d’échappement simples possibles `\'`sont `\"`, `\\`, `\0`, `\a`, `\b`, `\f`, `\n`, `\r`, `\t`, ,`\v`.

```antlr
character_literal
    : '\'' character '\''
    ;

character
    : single_character
    | simple_escape_sequence
    | hexadecimal_escape_sequence
    | unicode_escape_sequence
    ;

single_character
    : '<Any character except \' (U+0027), \\ (U+005C), and new_line_character>'
    ;

simple_escape_sequence
    : '\\\'' | '\\"' | '\\\\' | '\\0' | '\\a' | '\\b' | '\\f' | '\\n' | '\\r' | '\\t' | '\\v'
    ;

hexadecimal_escape_sequence
    : '\\x' hex_digit hex_digit? hex_digit? hex_digit?;
```

Un caractère qui suit une barre oblique inverse`\`() dans *un caractère* doit être l’un des caractères suivants `'`: `"`, `\`, `0`, `a`, `b`, `f` , , `n`, `r`, `t`, `u`, `U`, `x`, `v`. Sinon, une erreur de compilation se produit.

Une séquence d’échappement hexadécimale représente un caractère Unicode unique, avec la valeur formée par le nombre hexadécimal`\x`suivant «».

Si la valeur représentée par un littéral de caractère est supérieure `U+FFFF`à, une erreur de compilation se produit.

Une séquence d’échappement de caractère Unicode ([séquences d’échappement de caractère Unicode](lexical-structure.md#unicode-character-escape-sequences)) dans un littéral de caractère `U+0000` doit `U+FFFF`être comprise dans la plage de.

Une séquence d’échappement simple représente un encodage de caractères Unicode, comme décrit dans le tableau ci-dessous.


| __Séquence d’échappement__ | __Nom de caractère__ | __Encodage Unicode__ |
|---------------------|--------------------|----------------------|
| `\'`                | Guillemet simple       | `0x0027`             | 
| `\"`                | Guillemet double       | `0x0022`             | 
| `\\`| Barre oblique inverse |`0x005C`             | 
| `\0`                | Null               | `0x0000`             | 
| `\a`                | Alerte              | `0x0007`             | 
| `\b`                | Retour arrière          | `0x0008`             | 
| `\f`                | Saut de page          | `0x000C`             | 
| `\n`                | Nouvelle ligne           | `0x000A`             | 
| `\r`                | Retour chariot    | `0x000D`             | 
| `\t`                | Tabulation horizontale     | `0x0009`             | 
| `\v`                | Tabulation verticale       | `0x000B`             | 

Le type d’un *character_literal* est `char`.

#### <a name="string-literals"></a>Littéraux de chaîne

C#prend en charge deux formes de littéraux de chaîne : les ***littéraux*** de chaîne normaux et les ***littéraux de chaîne Verbatim***.

Un littéral de chaîne standard se compose de zéro, un ou plusieurs caractères placés entre guillemets doubles, comme dans `"hello"`, et peut inclure à la fois des séquences d’échappement simples (comme `\t` pour le caractère de tabulation) et des séquences d’échappement hexadécimales et Unicode.

Un littéral de chaîne Verbatim se compose `@` d’un caractère suivi d’un guillemet double, de zéro, d’un ou de plusieurs caractères et d’un guillemet double fermant. Un exemple simple est `@"hello"`. Dans un littéral de chaîne Verbatim, les caractères entre les délimiteurs sont interprétés textuellement, la seule exception étant *quote_escape_sequence*. En particulier, les séquences d’échappement simples et les séquences d’échappement hexadécimales et Unicode ne sont pas traitées dans des littéraux de chaîne Verbatim. Un littéral de chaîne Verbatim peut s’étendre sur plusieurs lignes.

```antlr
string_literal
    : regular_string_literal
    | verbatim_string_literal
    ;

regular_string_literal
    : '"' regular_string_literal_character* '"'
    ;

regular_string_literal_character
    : single_regular_string_literal_character
    | simple_escape_sequence
    | hexadecimal_escape_sequence
    | unicode_escape_sequence
    ;

single_regular_string_literal_character
    : '<Any character except " (U+0022), \\ (U+005C), and new_line_character>'
    ;

verbatim_string_literal
    : '@"' verbatim_string_literal_character* '"'
    ;

verbatim_string_literal_character
    : single_verbatim_string_literal_character
    | quote_escape_sequence
    ;

single_verbatim_string_literal_character
    : '<any character except ">'
    ;

quote_escape_sequence
    : '""'
    ;
```

Un caractère qui suit une barre oblique inverse`\`() dans un *regular_string_literal_character* doit être l’un des caractères suivants `'`: `"`, `\`, `0`, `a`, `b` , , `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`. Sinon, une erreur de compilation se produit.

L’exemple
```csharp
string a = "hello, world";                   // hello, world
string b = @"hello, world";                  // hello, world

string c = "hello \t world";                 // hello      world
string d = @"hello \t world";                // hello \t world

string e = "Joe said \"Hello\" to me";       // Joe said "Hello" to me
string f = @"Joe said ""Hello"" to me";      // Joe said "Hello" to me

string g = "\\\\server\\share\\file.txt";    // \\server\share\file.txt
string h = @"\\server\share\file.txt";       // \\server\share\file.txt

string i = "one\r\ntwo\r\nthree";
string j = @"one
two
three";
```
affiche divers littéraux de chaîne. Le dernier littéral de chaîne `j`,, est un littéral de chaîne Verbatim qui s’étend sur plusieurs lignes. Les caractères entre guillemets, y compris les espaces blancs tels que les caractères de nouvelle ligne, sont conservés textuellement.

Comme une séquence d’échappement hexadécimale peut avoir un nombre variable de chiffres hexadécimaux, le `"\x123"` littéral de chaîne contient un caractère unique avec la valeur hexadécimale 123. Pour créer une chaîne contenant le caractère avec la valeur hexadécimale 12 suivi du caractère 3, il est `"\x00123"` possible `"\x12" + "3"` d’écrire ou à la place.

Le type d’un *string_literal* est `string`.

Chaque littéral de chaîne ne produit pas nécessairement une nouvelle instance de chaîne. Lorsque deux littéraux de chaîne ou plus qui sont équivalents en fonction de l’opérateur d’égalité de chaînes ([opérateurs d’égalité de chaînes](expressions.md#string-equality-operators)) apparaissent dans le même programme, ces littéraux de chaîne font référence à la même instance de chaîne. Par exemple, la sortie produite par
```csharp
class Test
{
    static void Main() {
        object a = "hello";
        object b = "hello";
        System.Console.WriteLine(a == b);
    }
}
```
est `True` dû au fait que les deux littéraux font référence à la même instance de chaîne.

#### <a name="interpolated-string-literals"></a>Littéraux de chaîne interpolés

Les littéraux de chaîne interpolés sont similaires aux littéraux de chaîne, mais contiennent des `{` trous `}`délimités par et, où les expressions peuvent se produire. Lors de l’exécution, les expressions sont évaluées dans le but de faire en sorte que leurs formes textuelles se substituent à la chaîne à l’endroit où se trouve le trou. La syntaxe et la sémantique de l’interpolation de chaîne sont décrites dans la section ([chaînes interpolées](expressions.md#interpolated-strings)).

À l’instar des littéraux de chaîne, les littéraux de chaîne interpolés peuvent être normaux ou textuels. Les littéraux de chaîne réguliers interpolés sont délimités `$"` par et `"`, et les littéraux de chaîne Verbatim interpolés `"`sont délimités par `$@"` et.

Comme pour les autres littéraux, l’analyse lexicale d’un littéral de chaîne interpolée produit initialement un jeton unique, conformément à la grammaire ci-dessous. Toutefois, avant l’analyse syntaxique, le jeton unique d’un littéral de chaîne interpolé est divisé en plusieurs jetons pour les parties de la chaîne qui entourent les trous, et les éléments d’entrée qui se produisent dans les trous sont analysés de façon lexicale. Cela peut, à son tour, générer davantage de littéraux de chaîne interpolés à traiter, mais, s’il est correct, finit par entraîner une séquence de jetons pour l’analyse syntaxique.

```antlr
interpolated_string_literal
    : '$' interpolated_regular_string_literal
    | '$' interpolated_verbatim_string_literal
    ;

interpolated_regular_string_literal
    : interpolated_regular_string_whole
    | interpolated_regular_string_start  interpolated_regular_string_literal_body interpolated_regular_string_end
    ;

interpolated_regular_string_literal_body
    : regular_balanced_text
    | interpolated_regular_string_literal_body interpolated_regular_string_mid regular_balanced_text
    ;

interpolated_regular_string_whole
    : '"' interpolated_regular_string_character* '"'
    ;

interpolated_regular_string_start
    : '"' interpolated_regular_string_character* '{'
    ;

interpolated_regular_string_mid
    : interpolation_format? '}' interpolated_regular_string_characters_after_brace? '{'
    ;

interpolated_regular_string_end
    : interpolation_format? '}' interpolated_regular_string_characters_after_brace? '"'
    ;

interpolated_regular_string_characters_after_brace
    : interpolated_regular_string_character_no_brace
    | interpolated_regular_string_characters_after_brace interpolated_regular_string_character
    ;

interpolated_regular_string_character
    : single_interpolated_regular_string_character
    | simple_escape_sequence
    | hexadecimal_escape_sequence
    | unicode_escape_sequence
    | open_brace_escape_sequence
    | close_brace_escape_sequence
    ;

interpolated_regular_string_character_no_brace
    : '<Any interpolated_regular_string_character except close_brace_escape_sequence and any hexadecimal_escape_sequence or unicode_escape_sequence designating } (U+007D)>'
    ;

single_interpolated_regular_string_character
    : '<Any character except \" (U+0022), \\ (U+005C), { (U+007B), } (U+007D), and new_line_character>'
    ;

open_brace_escape_sequence
    : '{{'
    ;

close_brace_escape_sequence
    : '}}'
    ;
    
regular_balanced_text
    : regular_balanced_text_part+
    ;

regular_balanced_text_part
    : single_regular_balanced_text_character
    | delimited_comment
    | '@' identifier_or_keyword
    | string_literal
    | interpolated_string_literal
    | '(' regular_balanced_text ')'
    | '[' regular_balanced_text ']'
    | '{' regular_balanced_text '}'
    ;
    
single_regular_balanced_text_character
    : '<Any character except / (U+002F), @ (U+0040), \" (U+0022), $ (U+0024), ( (U+0028), ) (U+0029), [ (U+005B), ] (U+005D), { (U+007B), } (U+007D) and new_line_character>'
    | '</ (U+002F), if not directly followed by / (U+002F) or * (U+002A)>'
    ;
    
interpolation_format
    : interpolation_format_character+
    ;
    
interpolation_format_character
    : '<Any character except \" (U+0022), : (U+003A), { (U+007B) and } (U+007D)>'
    ;
    
interpolated_verbatim_string_literal
    : interpolated_verbatim_string_whole
    | interpolated_verbatim_string_start interpolated_verbatim_string_literal_body interpolated_verbatim_string_end
    ;

interpolated_verbatim_string_literal_body
    : verbatim_balanced_text
    | interpolated_verbatim_string_literal_body interpolated_verbatim_string_mid verbatim_balanced_text
    ;
    
interpolated_verbatim_string_whole
    : '@"' interpolated_verbatim_string_character* '"'
    ;
    
interpolated_verbatim_string_start
    : '@"' interpolated_verbatim_string_character* '{'
    ;
    
interpolated_verbatim_string_mid
    : interpolation_format? '}' interpolated_verbatim_string_characters_after_brace? '{'
    ;
    
interpolated_verbatim_string_end
    : interpolation_format? '}' interpolated_verbatim_string_characters_after_brace? '"'
    ;
    
interpolated_verbatim_string_characters_after_brace
    : interpolated_verbatim_string_character_no_brace
    | interpolated_verbatim_string_characters_after_brace interpolated_verbatim_string_character
    ;
    
interpolated_verbatim_string_character
    : single_interpolated_verbatim_string_character
    | quote_escape_sequence
    | open_brace_escape_sequence
    | close_brace_escape_sequence
    ;
    
interpolated_verbatim_string_character_no_brace
    : '<Any interpolated_verbatim_string_character except close_brace_escape_sequence>'
    ;
    
single_interpolated_verbatim_string_character
    : '<Any character except \" (U+0022), { (U+007B) and } (U+007D)>'
    ;
    
verbatim_balanced_text
    : verbatim_balanced_text_part+
    ;

verbatim_balanced_text_part
    : single_verbatim_balanced_text_character
    | comment
    | '@' identifier_or_keyword
    | string_literal
    | interpolated_string_literal
    | '(' verbatim_balanced_text ')'
    | '[' verbatim_balanced_text ']'
    | '{' verbatim_balanced_text '}'
    ;
    
single_verbatim_balanced_text_character
    : '<Any character except / (U+002F), @ (U+0040), \" (U+0022), $ (U+0024), ( (U+0028), ) (U+0029), [ (U+005B), ] (U+005D), { (U+007B) and } (U+007D)>'
    | '</ (U+002F), if not directly followed by / (U+002F) or * (U+002A)>'
    ;
```

Un jeton *interpolated_string_literal* est réinterprété comme plusieurs jetons et autres éléments d’entrée comme suit, dans l’ordre des occurrences dans le *interpolated_string_literal*:

* Les occurrences des éléments suivants sont réinterprétées comme des jetons individuels distincts : `$` le signe de début, *interpolated_regular_string_whole*, *interpolated_regular_string_start*, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_whole*, *interpolated_verbatim_string_start*, *interpolated_verbatim_string_mid* et *interpolated_verbatim_string_end*.
* Les occurrences de *regular_balanced_text* et *verbatim_balanced_text* entre celles-ci sont retraitées comme un *input_section* ([analyse lexicale](lexical-structure.md#lexical-analysis)) et sont réinterprétées comme la séquence résultante d’éléments d’entrée. Elles peuvent à leur tour inclure des jetons de littéral de chaîne interpolés à réinterpréter.

L’analyse syntaxique réassociera les jetons en un *interpolated_string_expression* ([chaînes interpolées](expressions.md#interpolated-strings)).

Exemples TODO


#### <a name="the-null-literal"></a>Littéral null

```antlr
null_literal
    : 'null'
    ;
```

Le *null_literal* peut être converti implicitement en un type référence ou un type Nullable.

### <a name="operators-and-punctuators"></a>Opérateurs et signes de ponctuation

Il existe plusieurs types d’opérateurs et de signes de ponctuation. Les opérateurs sont utilisés dans les expressions pour décrire les opérations impliquant un ou plusieurs opérandes. Par exemple, l’expression `a + b` utilise l' `+` opérateur pour `a` ajouter les deux opérandes et `b`. Les signes de ponctuation sont destinés au regroupement et à la séparation.

```antlr
operator_or_punctuator
    : '{'  | '}'  | '['  | ']'  | '('   | ')'  | '.'  | ','  | ':'  | ';'
    | '+'  | '-'  | '*'  | '/'  | '%'   | '&'  | '|'  | '^'  | '!'  | '~'
    | '='  | '<'  | '>'  | '?'  | '??'  | '::' | '++' | '--' | '&&' | '||'
    | '->' | '==' | '!=' | '<=' | '>='  | '+=' | '-=' | '*=' | '/=' | '%='
    | '&=' | '|=' | '^=' | '<<' | '<<=' | '=>'
    ;

right_shift
    : '>>'
    ;

right_shift_assignment
    : '>>='
    ;
```

La barre verticale dans les productions *right_shift* et *right_shift_assignment* est utilisée pour indiquer que, contrairement aux autres productions dans la syntaxe syntaxique, aucun caractère de tout type (pas même espace blanc) n’est autorisé entre les jetons. Ces productions sont traitées de façon spéciale pour permettre la gestion correcte des *type_parameter_list*s ([paramètres de type](classes.md#type-parameters)).

## <a name="pre-processing-directives"></a>Directives de prétraitement

Les directives de prétraitement offrent la possibilité d’ignorer conditionnellement des sections de fichiers sources, de signaler des conditions d’erreur et d’avertissement, et de détourer des régions distinctes du code source. Le terme « directives de prétraitement » est utilisé uniquement à des fins de cohérence avec C++ les langages C et de programmation. Dans C#, il n’y a pas d’étape de pré-traitement distincte ; les directives de pré-traitement sont traitées dans le cadre de la phase d’analyse lexicale.

```antlr
pp_directive
    : pp_declaration
    | pp_conditional
    | pp_line
    | pp_diagnostic
    | pp_region
    | pp_pragma
    ;
```

Les directives de pré-traitement suivantes sont disponibles :

*  `#define`et `#undef`, qui sont utilisés pour définir et annuler la définition, respectivement, des symboles de compilation conditionnelle ([directives de déclaration](lexical-structure.md#declaration-directives)).
*  `#if`, `#elif`, `#else` et`#endif`, qui sont utilisés pour ignorer de manière conditionnelle les sections du code source ([directives de compilation conditionnelle](lexical-structure.md#conditional-compilation-directives)).
*  `#line`, qui est utilisé pour contrôler les numéros de ligne émis pour les erreurs et les avertissements ([directives de ligne](lexical-structure.md#line-directives)).
*  `#error`et `#warning`, qui sont utilisés pour émettre des erreurs et des avertissements, respectivement ([directives de diagnostic](lexical-structure.md#diagnostic-directives)).
*  `#region`et `#endregion`, qui sont utilisés pour marquer explicitement les sections du code source ([directives de région](lexical-structure.md#region-directives)).
*  `#pragma`, qui est utilisé pour spécifier des informations contextuelles facultatives pour le compilateur ([directives pragma](lexical-structure.md#pragma-directives)).

Une directive de prétraitement occupe toujours une ligne distincte de code source et commence toujours par un `#` caractère et un nom de directive de prétraitement. Un espace blanc peut se produire `#` avant le caractère et `#` entre le caractère et le nom de la directive.

Une ligne source contenant une `#define`directive `#undef`, `#if` `#elif` `#else` ,,`#endif` ,`#endregion` ,, ou peut se terminer par un commentaire sur une seule ligne. `#line` Les commentaires délimités `/* */` (style de commentaires) ne sont pas autorisés sur les lignes sources contenant des directives de prétraitement.

Les directives de pré-traitement ne sont pas des jetons et ne font pas partie de C#la syntaxe syntaxique de. Toutefois, les directives de prétraitement peuvent être utilisées pour inclure ou exclure des séquences de jetons et peuvent, de cette façon, affecter la C# signification d’un programme. Par exemple, une fois compilé, le programme :
```csharp
#define A
#undef B

class C
{
#if A
    void F() {}
#else
    void G() {}
#endif

#if B
    void H() {}
#else
    void I() {}
#endif
}
```
produit exactement la même séquence de jetons que le programme :
```csharp
class C
{
    void F() {}
    void I() {}
}
```

Par conséquent, contrairement à l’lexicale, les deux programmes sont très différents, syntaxiquement identiques.

### <a name="conditional-compilation-symbols"></a>Symboles de compilation conditionnelle

La fonctionnalité de compilation conditionnelle fournie par `#if`les `#elif`directives `#else`,, `#endif` et est contrôlée par le biais d’expressions de prétraitement ([expressions de pré-traitement](lexical-structure.md#pre-processing-expressions)) et conditionnelle symboles de compilation.

```antlr
conditional_symbol
    : '<Any identifier_or_keyword except true or false>'
    ;
```

Un symbole de compilation conditionnelle a deux États possibles : ***défini*** ou ***non défini***. Au début du traitement lexical d’un fichier source, un symbole de compilation conditionnelle n’est pas défini, sauf s’il a été défini explicitement par un mécanisme externe (tel qu’une option de compilateur de ligne de commande). Quand une `#define` directive est traitée, le symbole de compilation conditionnelle nommé dans cette directive devient défini dans ce fichier source. Le symbole reste défini jusqu’à `#undef` ce qu’une directive pour ce même symbole soit traitée ou jusqu’à ce que la fin du fichier source soit atteinte. L’une des conséquences est que `#define` les `#undef` directives et dans un fichier source n’ont aucun effet sur les autres fichiers sources du même programme.

Lorsqu’il est référencé dans une expression de pré-traitement, un symbole de compilation conditionnelle défini a `true`la valeur booléenne et un symbole de compilation conditionnelle non défini a la `false`valeur booléenne. Il n’est pas nécessaire que les symboles de compilation conditionnelle soient explicitement déclarés avant d’être référencés dans des expressions de prétraitement. Au lieu de cela, les symboles non déclarés sont simplement non définis et `false`ont donc la valeur.

L’espace de noms pour les symboles de compilation conditionnelle est distinct et distinct de toutes les autres C# entités nommées dans un programme. Les symboles de compilation conditionnelle peuvent uniquement être référencés `#undef` dans `#define` les directives et et dans les expressions de prétraitement.

### <a name="pre-processing-expressions"></a>Expressions de pré-traitement

Les expressions de prétraitement peuvent se `#if` produire `#elif` dans les directives et. Les opérateurs `!`, `==`, `!=` et`||`sont autorisés dans les expressions de prétraitement, et les parenthèses peuvent être utilisées pour le regroupement. `&&`

```antlr
pp_expression
    : whitespace? pp_or_expression whitespace?
    ;

pp_or_expression
    : pp_and_expression
    | pp_or_expression whitespace? '||' whitespace? pp_and_expression
    ;

pp_and_expression
    : pp_equality_expression
    | pp_and_expression whitespace? '&&' whitespace? pp_equality_expression
    ;

pp_equality_expression
    : pp_unary_expression
    | pp_equality_expression whitespace? '==' whitespace? pp_unary_expression
    | pp_equality_expression whitespace? '!=' whitespace? pp_unary_expression
    ;

pp_unary_expression
    : pp_primary_expression
    | '!' whitespace? pp_unary_expression
    ;

pp_primary_expression
    : 'true'
    | 'false'
    | conditional_symbol
    | '(' whitespace? pp_expression whitespace? ')'
    ;
```

Lorsqu’il est référencé dans une expression de pré-traitement, un symbole de compilation conditionnelle défini a `true`la valeur booléenne et un symbole de compilation conditionnelle non défini a la `false`valeur booléenne.

L’évaluation d’une expression de prétraitement génère toujours une valeur booléenne. Les règles d’évaluation pour une expression de prétraitement sont les mêmes que celles d’une expression constante ([expressions constantes](expressions.md#constant-expressions)), sauf que les seules entités définies par l’utilisateur qui peuvent être référencées sont des symboles de compilation conditionnelle.

### <a name="declaration-directives"></a>Directives de déclaration

Les directives de déclaration sont utilisées pour définir ou annuler la définition des symboles de compilation conditionnelle.

```antlr
pp_declaration
    : whitespace? '#' whitespace? 'define' whitespace conditional_symbol pp_new_line
    | whitespace? '#' whitespace? 'undef' whitespace conditional_symbol pp_new_line
    ;

pp_new_line
    : whitespace? single_line_comment? new_line
    ;
```

Le traitement d’une `#define` directive a pour effet de définir le symbole de compilation conditionnelle donné, en commençant par la ligne source qui suit la directive. De même, le traitement d' `#undef` une directive rend le symbole de compilation conditionnelle donné non défini, en commençant par la ligne source qui suit la directive.

Les `#define` directives `#undef` et dans un fichier source doivent se trouver avant le premier *jeton* ([jetons](lexical-structure.md#tokens)) dans le fichier source ; sinon, une erreur de compilation se produit. En termes intuitifs `#define` , `#undef` les directives et doivent précéder tout « code réel » dans le fichier source.

L’exemple suivant :
```csharp
#define Enterprise

#if Professional || Enterprise
    #define Advanced
#endif

namespace Megacorp.Data
{
    #if Advanced
    class PivotTable {...}
    #endif
}
```
est valide, car `#define` les directives précèdent le premier `namespace` jeton (le mot clé) dans le fichier source.

L’exemple suivant génère une erreur au moment de la compilation, `#define` car un code réel suit :
```csharp
#define A
namespace N
{
    #define B
    #if B
    class Class1 {}
    #endif
}
```

Un `#define` peut définir un symbole de compilation conditionnelle qui est déjà défini, sans qu’il n’y `#undef` ait d’intermédiaire pour ce symbole. L’exemple ci-dessous définit un symbole `A` de compilation conditionnelle, puis le définit à nouveau.
```csharp
#define A
#define A
```

Un `#undef` peut « annuler la définition » d’un symbole de compilation conditionnelle qui n’est pas défini. L’exemple ci-dessous définit un symbole `A` de compilation conditionnelle, puis l’annule deux fois ; `#undef` bien que le second n’ait aucun effet, il est toujours valide.
```csharp
#define A
#undef A
#undef A
```

### <a name="conditional-compilation-directives"></a>Directives de compilation conditionnelle

Les directives de compilation conditionnelle permettent d’inclure ou d’exclure de manière conditionnelle des parties d’un fichier source.

```antlr
pp_conditional
    : pp_if_section pp_elif_section* pp_else_section? pp_endif
    ;

pp_if_section
    : whitespace? '#' whitespace? 'if' whitespace pp_expression pp_new_line conditional_section?
    ;

pp_elif_section
    : whitespace? '#' whitespace? 'elif' whitespace pp_expression pp_new_line conditional_section?
    ;

pp_else_section:
    | whitespace? '#' whitespace? 'else' pp_new_line conditional_section?
    ;

pp_endif
    : whitespace? '#' whitespace? 'endif' pp_new_line
    ;

conditional_section
    : input_section
    | skipped_section
    ;

skipped_section
    : skipped_section_part+
    ;

skipped_section_part
    : skipped_characters? new_line
    | pp_directive
    ;

skipped_characters
    : whitespace? not_number_sign input_character*
    ;

not_number_sign
    : '<Any input_character except #>'
    ;
```

Comme indiqué par la syntaxe, les directives de compilation conditionnelle doivent être écrites sous la forme de jeux composés de `#if` , dans l’ordre, d’une directive, de `#else` zéro, d’une `#endif` ou de plusieurs `#elif` directives, de zéro ou d’une directive et d’une directive. Les sections conditionnelles du code source sont placées entre les directives. Chaque section est contrôlée par la directive qui précède immédiatement. Une section conditionnelle peut elle-même contenir des directives de compilation conditionnelle imbriquées, à condition que ces directives forment des jeux complets.

Un *pp_conditional* sélectionne au plus l’un des *conditional_section*contenus dans le traitement lexical normal :

*  Les *pp_expression*s des directives `#if` et `#elif` sont `true`évaluées dans l’ordre jusqu’à ce que l’un d’eux produise. Si une expression génère `true`, le *conditional_section* de la directive correspondante est sélectionné.
*  Si tous les *pp_expression*s `false`sont générés et `#else` , si une directive est présente, le `#else` *conditional_section* de la directive est sélectionné.
*  Dans le cas contraire, aucun *conditional_section* n’est sélectionné.

Le *conditional_section*sélectionné, le cas échéant, est traité comme un *input_section*normal : le code source contenu dans la section doit adhérer à la grammaire lexicale. les jetons sont générés à partir du code source dans la section. et les directives de pré-traitement dans la section ont les effets imposés.

Les *conditional_section*restants, le cas échéant, sont traités comme des *skipped_section*s : à l’exception des directives de prétraitement, le code source de la section ne doit pas être conforme à la grammaire lexicale. aucun jeton n’est généré à partir du code source dans la section ; et les directives de prétraitement dans la section doivent être lexicalement correctes, mais elles ne sont pas traitées autrement. Au sein d’un *conditional_section* qui est traité en tant que *skipped_section*, tous les *conditional_section*imbriqués `#if`(contenues dans les objets imbriqués... `#endif` et`#region`... les constructions) sont également traitées en tant que skipped_section s. `#endregion`

L’exemple suivant illustre la façon dont les directives de compilation conditionnelle peuvent imbriquer :
```csharp
#define Debug       // Debugging on
#undef Trace        // Tracing off

class PurchaseTransaction
{
    void Commit() {
        #if Debug
            CheckConsistency();
            #if Trace
                WriteToLog(this.ToString());
            #endif
        #endif
        CommitHelper();
    }
}
```

À l’exception des directives de pré-traitement, le code source ignoré n’est pas soumis à l’analyse lexicale. Par exemple, le code suivant est valide malgré le Commentaire inachevé dans la `#else` section :
```csharp
#define Debug        // Debugging on

class PurchaseTransaction
{
    void Commit() {
        #if Debug
            CheckConsistency();
        #else
            /* Do something else
        #endif
    }
}
```

Notez, toutefois, que les directives de pré-traitement doivent être correctes lexicalement, même dans les sections ignorées du code source.

Les directives de pré-traitement ne sont pas traitées quand elles apparaissent dans des éléments d’entrée multiligne. Par exemple, le programme :
```csharp
class Hello
{
    static void Main() {
        System.Console.WriteLine(@"hello, 
#if Debug
        world
#else
        Nebraska
#endif
        ");
    }
}
```
génère la sortie :
```
hello,
#if Debug
        world
#else
        Nebraska
#endif
```

Dans les cas particuliers, l’ensemble de directives de prétraitement qui est traité peut dépendre de l’évaluation de *pp_expression*. L’exemple suivant :
```csharp
#if X
    /*
#else
    /* */ class Q { }
#endif
```
produit toujours le même flux de jetons`class` ( `}` `Q` `{` ), qu’il soit défini `X` ou non. Si `X` est défini, les seules directives traitées sont `#if` et `#endif`, en raison du commentaire sur plusieurs lignes. Si `X` n’est pas défini, alors trois directives (`#if`, `#else`, `#endif`) font partie de la directive définie.

### <a name="diagnostic-directives"></a>Directives de diagnostic

Les directives de diagnostic sont utilisées pour générer explicitement des messages d’erreur et d’avertissement signalés de la même façon que d’autres erreurs et avertissements au moment de la compilation.

```antlr
pp_diagnostic
    : whitespace? '#' whitespace? 'error' pp_message
    | whitespace? '#' whitespace? 'warning' pp_message
    ;

pp_message
    : new_line
    | whitespace input_character* new_line
    ;
```

L’exemple suivant :
```csharp
#warning Code review needed before check-in

#if Debug && Retail
    #error A build can't be both debug and retail
#endif

class Test {...}
```
génère toujours un avertissement (« révision du code nécessaire avant l’archivage ») et génère une erreur au moment de la compilation (« une build ne peut pas être à la fois Debug et Retail `Retail` ») si les symboles `Debug` conditionnels et sont tous deux définis. Notez qu’un *pp_message* peut contenir du texte arbitraire. plus précisément, il n’a pas besoin de contenir des jetons correctement formés, comme indiqué par le guillemet simple `can't`dans le mot.

### <a name="region-directives"></a>Directives de région

Les directives de région sont utilisées pour marquer explicitement les zones de code source.

```antlr
pp_region
    : pp_start_region conditional_section? pp_end_region
    ;

pp_start_region
    : whitespace? '#' whitespace? 'region' pp_message
    ;

pp_end_region
    : whitespace? '#' whitespace? 'endregion' pp_message
    ;
```

Aucune signification sémantique n’est attachée à une région ; les régions sont destinées à être utilisées par le programmeur ou par des outils automatisés pour marquer une section du code source. Le message spécifié dans une `#region` directive `#endregion` ou n’a pas la même signification sémantique ; il sert simplement à identifier la région. Les directives `#endregion` et peuvent avoir différents *pp_message*s. `#region`

Traitement lexical d’une région :
```csharp
#region
...
#endregion
```
correspond exactement au traitement lexical d’une directive de compilation conditionnelle au format suivant :
```csharp
#if true
...
#endif
```

### <a name="line-directives"></a>Directives de ligne

Les directives de ligne peuvent être utilisées pour modifier les numéros de ligne et les noms de fichiers sources qui sont signalés par le compilateur dans la sortie, tels que les avertissements et les erreurs, et qui sont utilisés par les attributs d’informations de l’appelant ([attributs des informations](attributes.md#caller-info-attributes)de l’appelant).

Les directives de ligne sont le plus souvent utilisées dans les outils de C# méta-programmation qui génèrent du code source à partir d’autres entrées de texte.

```antlr
pp_line
    : whitespace? '#' whitespace? 'line' whitespace line_indicator pp_new_line
    ;

line_indicator
    : decimal_digit+ whitespace file_name
    | decimal_digit+
    | 'default'
    | 'hidden'
    ;

file_name
    : '"' file_name_character+ '"'
    ;

file_name_character
    : '<Any input_character except ">'
    ;
```

Quand aucune `#line` directive n’est présente, le compilateur signale les véritables numéros de ligne et les noms de fichiers sources dans sa sortie. Lors du traitement `#line` d’une directive qui comprend un *line_indicator* qui `default`n’est pas, le compilateur traite la ligne après la directive comme ayant le numéro de ligne donné (et le nom de fichier, si spécifié).

Une `#line default` directive inverse l’effet de toutes les directives de #line précédentes. Le compilateur signale des informations de ligne vraies pour les lignes suivantes, `#line` exactement comme si aucune directive n’avait été traitée.

Une `#line hidden` directive n’a aucun effet sur le fichier et les numéros de ligne signalés dans les messages d’erreur, mais affecte le débogage au niveau de la source. Lors du débogage, toutes les lignes entre `#line hidden` une directive et la `#line` directive suivante (qui n' `#line hidden`est pas) n’ont pas d’informations sur le numéro de ligne. Quand vous exécutez le code pas à pas dans le débogueur, ces lignes sont entièrement ignorées.

Notez qu’un *nom_fichier* diffère d’un littéral de chaîne standard en ce que les caractères d’échappement ne sont pas traités ; le caractère`\`«» désigne simplement un caractère barre oblique inverse ordinaire dans un *nom_fichier*.

### <a name="pragma-directives"></a>Directives pragma

La `#pragma` directive de prétraitement est utilisée pour spécifier des informations contextuelles facultatives pour le compilateur. Les informations fournies dans une `#pragma` directive ne modifient jamais la sémantique du programme.

```antlr
pp_pragma
    : whitespace? '#' whitespace? 'pragma' whitespace pragma_body pp_new_line
    ;

pragma_body
    : pragma_warning_body
    ;
```

C#fournit `#pragma` des directives pour contrôler les avertissements du compilateur. Les futures versions du langage peuvent inclure des `#pragma` directives supplémentaires. Pour garantir l’interopérabilité avec C# d’autres compilateurs, le C# compilateur Microsoft n’émet pas d’erreurs de `#pragma` compilation pour les directives inconnues ; ces directives génèrent toutefois des avertissements.

#### <a name="pragma-warning"></a>pragma warning

La `#pragma warning` directive est utilisée pour désactiver ou restaurer tout ou un ensemble particulier de messages d’avertissement lors de la compilation du texte de programme suivant.

```antlr
pragma_warning_body
    : 'warning' whitespace warning_action
    | 'warning' whitespace warning_action whitespace warning_list
    ;

warning_action
    : 'disable'
    | 'restore'
    ;

warning_list
    : decimal_digit+ (whitespace? ',' whitespace? decimal_digit+)*
    ;
```

Une `#pragma warning` directive qui omet la liste d’avertissements affecte tous les avertissements. Une `#pragma warning` directive qui comprend une liste d’avertissements affecte uniquement les avertissements qui sont spécifiés dans la liste.

Une `#pragma warning disable` directive désactive tout ou l’ensemble d’avertissements donné.

Une `#pragma warning restore` directive restaure tout ou partie des avertissements donnés à l’État qui était en vigueur au début de l’unité de compilation. Notez que si un avertissement particulier a été désactivé en externe, `#pragma warning restore` a (que ce soit pour tous ou l’avertissement spécifique) ne réactivera pas cet avertissement.

L’exemple suivant illustre l’utilisation `#pragma warning` de pour désactiver temporairement l’avertissement signalé lorsque des membres obsolètes sont référencés, à l’aide du numéro C# d’avertissement du compilateur Microsoft.
```csharp
using System;

class Program
{
    [Obsolete]
    static void Foo() {}

    static void Main() {
#pragma warning disable 612
    Foo();
#pragma warning restore 612
    }
}
```
