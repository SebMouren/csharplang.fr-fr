# <a name="lexical-structure"></a>Structure lexicale

## <a name="programs"></a>Programs

C# ***programme*** se compose d’une ou plusieurs ***fichiers sources***, appelées formellement ***unités de compilation*** ([unités de Compilation](namespaces.md#compilation-units)). Un fichier source est une séquence ordonnée de caractères Unicode. Fichiers sources ont généralement une correspondance avec les fichiers dans un système de fichiers, mais cette correspondance n’est pas obligatoire. Pour une portabilité maximale, il est recommandé que les fichiers dans un système de fichiers être encodés avec UTF-8 de codage.

Point de vue conceptuel, un programme est compilé à l’aide de trois étapes :

1. Transformation, qui convertit un fichier à partir d’un répertoire de caractères particulier et un schéma d’encodage en une séquence de caractères Unicode.
2. Analyse lexicale, qui convertit un flux de caractères d’entrée Unicode dans un flux de jetons.
3. Analyse syntaxique, qui convertit le flux de jetons en code exécutable.

## <a name="grammars"></a>Grammaires

Cette spécification présente la syntaxe de programmation c# à l’aide de deux grammaires. Le ***grammaire lexicale*** ([grammaire lexicale](lexical-structure.md#lexical-grammar)) définit comment les caractères Unicode sont combinés pour les terminateurs de ligne de formulaire, les espaces blancs, les commentaires, les jetons et les directives de prétraitement. Le ***grammaire syntaxique*** ([grammaire syntaxique](lexical-structure.md#syntactic-grammar)) définit la façon dont les jetons résultant de la grammaire lexicale sont combinées pour former c# des programmes.

### <a name="grammar-notation"></a>Notation de la grammaire

Les grammaires lexicales et syntaxiques sont présentées sous forme Backus-Naur à l’aide de la notation de l’outil de grammaire ANTLR.

### <a name="lexical-grammar"></a>Grammaire lexicale

La grammaire lexicale de c# est présentée dans [analyse lexicale](lexical-structure.md#lexical-analysis), [jetons](lexical-structure.md#tokens), et [directives de prétraitement](lexical-structure.md#pre-processing-directives). Les symboles terminaux de la grammaire lexicale sont les caractères du jeu de caractères Unicode et la grammaire lexicale spécifie la façon dont les caractères sont combinées aux jetons de formulaire ([jetons](lexical-structure.md#tokens)), un espace blanc ([blancs](lexical-structure.md#white-space)), commentaires ([commentaires](lexical-structure.md#comments)) et les directives de prétraitement ([directives de prétraitement](lexical-structure.md#pre-processing-directives)).

Chaque fichier source dans un programme c# doit être conforme à la *d’entrée* production de la grammaire lexicale ([analyse lexicale](lexical-structure.md#lexical-analysis)).

### <a name="syntactic-grammar"></a>Grammaire syntaxique

La grammaire syntaxique de c# est présentée dans les chapitres et les annexes qui suivent ce chapitre. Les symboles terminaux de la grammaire syntaxique sont les jetons définis par la grammaire lexicale et la grammaire syntaxique spécifie la façon dont les jetons sont combinées pour former des programmes c#.

Chaque fichier source dans un C# programme doit être conforme à la *compilation_unit* production de la grammaire syntaxique ([unités de Compilation](namespaces.md#compilation-units)).

## <a name="lexical-analysis"></a>Analyse lexicale

Le *d’entrée* production définit la structure lexicale d’un fichier source c#. Chaque fichier source dans un programme c# doit être conforme à la production de cette grammaire lexicale.

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

Cinq éléments de base constituent la structure lexicale d’un C# fichier source : Terminateurs de ligne ([terminateurs de ligne](lexical-structure.md#line-terminators)), un espace blanc ([espace blanc](lexical-structure.md#white-space)), commentaires ([commentaires](lexical-structure.md#comments)), jetons ([jetons](lexical-structure.md#tokens)), et directives de prétraitement ([directives de prétraitement](lexical-structure.md#pre-processing-directives)). Ces éléments de base, seuls les jetons sont importants dans la grammaire syntaxique d’un programme c# ([grammaire syntaxique](lexical-structure.md#syntactic-grammar)).

Le traitement lexical d’un fichier source c# consiste à réduire le fichier en une séquence de jetons qui devient l’entrée de l’analyse syntaxique. Terminateurs de ligne, les espaces blancs et les commentaires peuvent servir à séparer les jetons et les directives de prétraitement peuvent provoquer des sections du fichier source doit être ignorée, mais sinon ces éléments lexicaux n’ont aucun impact sur la structure syntaxique d’un programme c#.

Dans le cas des littéraux de chaîne interpolée ([interpolées littéraux de chaîne](lexical-structure.md#interpolated-string-literals)) un jeton unique est généré initialement par l’analyse lexicale, mais il est divisé en plusieurs éléments d’entrée qui sont soumis à plusieurs reprises à l’analyse lexicale jusqu'à ce que tous les littéraux de chaîne interpolée ont été résolus. Les jetons générés puis de fournir en tant qu’entrée de l’analyse syntaxique.

Lorsque plusieurs productions grammaire lexicale correspondent à une séquence de caractères dans un fichier source, le traitement lexical forme toujours l’élément de lexicale la plus longue possible. Par exemple, la séquence de caractères `//` est traitée comme le début d’un commentaire sur une ligne, car cet élément lexical est plus long que d’un seul `/` jeton.

### <a name="line-terminators"></a>Terminateurs de ligne

Indicateurs de fin de ligne divisent les caractères d’un fichier source c# en lignes.

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

Pour la compatibilité avec la source des outils d’édition qui ajoutent des marqueurs de fin de fichier de code, et pour activer une source de fichier à afficher sous forme de séquence de correctement arrêtée lignes, les transformations suivantes sont appliquées dans l’ordre, pour chaque fichier source dans un programme c# :

*  Si le dernier caractère du fichier source est un caractère CTRL + Z (`U+001A`), ce caractère est supprimé.
*  Un caractère de retour chariot (`U+000D`) est ajouté à la fin du fichier source si ce fichier source n’est pas vide et si le dernier caractère du fichier source n’est pas un retour chariot (`U+000D`), un saut de ligne (`U+000A`), un séparateur de ligne (`U+2028`), ou un séparateur de paragraphe (`U+2029`).

### <a name="comments"></a>Commentaires

Deux formes de commentaires sont pris en charge : commentaires à ligne unique et les commentaires délimités. ***Commentaires à ligne unique*** commencer par les caractères `//` et d’étendre à la fin de la ligne source. ***Délimitée par des commentaires*** commencer par les caractères `/*` et se terminent par les caractères `*/`. Commentaires délimités peuvent s’étendre sur plusieurs lignes.

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
    : '/*' delimited_comment_section* asterisk* '/'
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

Ne pas imbriquent les commentaires. Les séquences de caractères `/*` et `*/` n’ont aucune signification spéciale dans un `//` commentaire et les séquences de caractères `//` et `/*` n’ont aucune signification spéciale dans un commentaire délimité.

Commentaires ne sont pas traités dans les littéraux de caractère et de chaîne.

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
contient un commentaire délimité.

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
montre plusieurs commentaires à ligne unique.

### <a name="white-space"></a>Espace blanc

Espace blanc est défini comme caractère de saut de n’importe quel caractère de la classe Unicode Zs (qui inclut le caractère espace), ainsi que le caractère de tabulation horizontale, le caractère de tabulation verticale et le formulaire.

```antlr
whitespace
    : '<Any character with Unicode class Zs>'
    | '<Horizontal tab character (U+0009)>'
    | '<Vertical tab character (U+000B)>'
    | '<Form feed character (U+000C)>'
    ;
```

## <a name="tokens"></a>jetons

Il existe plusieurs types de jetons : identificateurs, les mots clés, les littéraux, les opérateurs et les signes de ponctuation. Espace blanc et les commentaires ne sont pas des jetons, car ils font Office de séparateurs de jetons.

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

### <a name="unicode-character-escape-sequences"></a>Séquences d’échappement de caractère Unicode

Une séquence d’échappement de caractères Unicode représente un caractère Unicode. Séquences d’échappement de caractères Unicode sont traités dans les identificateurs ([identificateurs](lexical-structure.md#identifiers)), littéraux de caractère ([littéraux de caractère](lexical-structure.md#character-literals)) et les littéraux de chaîne classique ([delittérauxdechaîne](lexical-structure.md#string-literals)). Un caractère d’échappement Unicode n’est pas traitée dans tout autre emplacement (par exemple, pour former un opérateur, un signe de ponctuation ou un mot clé).

```antlr
unicode_escape_sequence
    : '\\u' hex_digit hex_digit hex_digit hex_digit
    | '\\U' hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit
    ;
```

Une séquence d’échappement Unicode représente le caractère Unicode formé par le nombre hexadécimal qui suit le «`\u`« ou »`\U`» caractères. Étant donné que c# utilise un codage 16 bits des points de code Unicode en caractères et les valeurs de chaîne, un caractère Unicode dans la plage U + 10000 à U + 10FFFF n’est pas autorisé dans un littéral de caractère et est représenté à l’aide d’une paire de substitution Unicode dans un littéral de chaîne. Les caractères Unicode avec des points de code supérieurs à 0x10FFFF ne sont pas pris en charge.

Plusieurs traductions ne sont pas effectuées. Par exemple, le littéral de chaîne «`\u005Cu005C`« équivaut à »`\u005C`« plutôt que »`\`». La valeur Unicode `\u005C` est le caractère «`\`».

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
illustre plusieurs utilisations de `\u0066`, qui est la séquence d’échappement pour la lettre «`f`». Le programme est équivalent à
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

Les règles des identificateurs fournies dans cette section correspondent exactement à ces recommandée par le Unicode Standard à l’annexe 31, sauf qu’un trait de soulignement est autorisé comme caractère initial (tel quel traditionnel dans le langage de programmation C), sont des séquences de la séquence d’échappement Unicode autorisées dans les identificateurs et le «`@`« caractère est autorisé en tant que préfixe pour activer des mots clés pour être utilisés comme identificateurs.

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

Pour plus d’informations sur les classes de caractères Unicode mentionnées ci-dessus, consultez la norme Unicode, Version 3.0, section 4.5.

Exemples d’identificateurs valides »`identifier1`«, »`_identifier2`», et «`@if`».

Un identificateur dans un programme conforme doit être au format canonique défini par le formulaire de normalisation Unicode C, tel que défini par la norme Unicode annexe 15. Le comportement lorsqu’il rencontre un identificateur pas dans le formulaire de normalisation C est défini par l’implémentation ; Toutefois, un diagnostic n’est pas nécessaire.

Le préfixe «`@`» permet d’utiliser des mots clés comme identificateurs, ce qui est utile lors de l’interfaçage avec d’autres langages de programmation. Le caractère `@` ne faisant pas partie de l’identificateur, l’identificateur peut être constaté dans d’autres langages comme identificateur normal, sans le préfixe. Un identificateur avec un `@` préfixe est appelé un ***identificateur textuel***. Utilisation de la `@` préfixe pour les identificateurs qui ne sont pas des mots clés est autorisée, mais fortement déconseillée en tant qu’une question de style.

L’exemple :
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
définit une classe nommée «`class`« avec une méthode statique nommée »`static`« qui accepte un paramètre nommé »`bool`». Notez que dans la mesure où échappements Unicode ne sont pas autorisés dans les mots clés, le jeton «`cl\u0061ss`« est un identificateur, et est le même identificateur que »`@class`».

Deux identificateurs sont considérés comme identiques s’ils sont identiques, une fois que les transformations suivantes sont appliquées, dans l’ordre :

*  Le préfixe «`@`», le cas échéant, est supprimé.
*  Chaque *unicode_escape_sequence* est transformée en son caractère Unicode correspondant.
*  N’importe quel *formatting_character*sont supprimées.

Les identificateurs contenant deux consécutifs les traits de soulignement (`U+005F`) sont réservés pour une utilisation par l’implémentation. Par exemple, une implémentation peut fournir des mots clés étendus qui commencent par deux traits de soulignement.

### <a name="keywords"></a>Mots clés

Un ***mot clé*** est une séquence de caractères similaire à l’identificateur qui est réservé et ne peut pas être utilisée en tant qu’identificateur, sauf si elle est précédée du `@` caractère.

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

Dans certains endroits dans la grammaire, des identificateurs spécifiques ont une signification particulière, mais ne sont pas des mots clés. Ces identificateurs sont parfois appelés « mots clés contextuels ». Par exemple, dans une déclaration de propriété, le «`get`« et »`set`« identificateurs ont une signification spéciale ([accesseurs](classes.md#accessors)). Un identificateur autre que `get` ou `set` n’est jamais autorisé à ces emplacements, donc cette utilisation n’est pas en conflit avec une utilisation de ces mots comme identificateurs. Dans d’autres cas, comme avec l’identificateur «`var`» dans les déclarations de variables locales implicitement typées ([déclarations de variables locales](statements.md#local-variable-declarations)), un mot clé contextuel peut entrer en conflit avec les noms déclarés. Dans ce cas, le nom déclaré est prioritaire sur l’utilisation de l’identificateur comme un mot clé contextuel.

### <a name="literals"></a>Littéraux

Un ***littéral*** est une représentation sous forme de code source d’une valeur.

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

Il existe deux valeurs de littéral booléens : `true` et `false`.

```antlr
boolean_literal
    : 'true'
    | 'false'
    ;
```

Le type d’un *boolean_literal* est `bool`.

#### <a name="integer-literals"></a>Littéraux d'entier

Les littéraux entiers sont utilisées pour écrire des valeurs de types `int`, `uint`, `long`, et `ulong`. Les littéraux entiers prendre deux formes : décimal et hexadécimal.

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

*  Si le littéral n’a aucun suffixe, il a le premier de ces types dans lesquels sa valeur peut être représentée : `int`, `uint`, `long`, `ulong`.
*  Si le littéral est suivi du suffixe `U` ou `u`, il prend le premier de ces types dans lesquels sa valeur peut être représentée : `uint`, `ulong`.
*  Si le littéral est suivi du suffixe `L` ou `l`, il prend le premier de ces types dans lesquels sa valeur peut être représentée : `long`, `ulong`.
*  Si le littéral est suivi du suffixe `UL`, `Ul`, `uL`, `ul`, `LU`, `Lu`, `lU`, ou `lu`, il est de type `ulong`.

Si la valeur représentée par un littéral entier est en dehors de la plage de la `ulong` type, une erreur de compilation se produit.

Comme une question de style, il est conseillé qui «`L`« être utilisé au lieu de »`l`» lors de l’écriture des littéraux de type `long`, car il est facile de confondre la lettre «`l`« avec le chiffre »`1`».

Pour autoriser la plus petite possible `int` et `long` valeurs soient écrites comme des littéraux d’entier décimal, les deux règles suivantes existent :

* Quand un *decimal_integer_literal* ayant la valeur 2147483648 (2 ^ 31) et aucun *integer_type_suffix* apparaît en tant que le jeton qui suit immédiatement un jeton d’opérateur unaire ([moins unaire opérateur](expressions.md#unary-minus-operator)), le résultat est une constante de type `int` avec la valeur -2147483648 (-2 ^ 31). Dans toutes les autres situations, tel un *decimal_integer_literal* est de type `uint`.
* Quand un *decimal_integer_literal* avec la valeur 9223372036854775808 (2 ^ 63) et aucun *integer_type_suffix* ou *integer_type_suffix* `L` ou `l` apparaît en tant que le jeton qui suit immédiatement un jeton d’opérateur unaire ([opérateur moins unaire](expressions.md#unary-minus-operator)), le résultat est une constante de type `long` avec la valeur -9223372036854775808 (-2 ^ 63). Dans toutes les autres situations, tel un *decimal_integer_literal* est de type `ulong`.

#### <a name="real-literals"></a>Littéraux réels

Littéraux réels sont utilisées pour écrire des valeurs de types `float`, `double`, et `decimal`.

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

Si aucun *real_type_suffix* est spécifié, le type du littéral réel est `double`. Le cas contraire, le suffixe de type réel détermine le type du littéral réel, comme suit :

*  Un littéral réel suivi du suffixe `F` ou `f` est de type `float`. Par exemple, les littéraux `1f`, `1.5f`, `1e10f`, et `123.456F` sont tous du type `float`.
*  Un littéral réel suivi du suffixe `D` ou `d` est de type `double`. Par exemple, les littéraux `1d`, `1.5d`, `1e10d`, et `123.456D` sont tous du type `double`.
*  Un littéral réel suivi du suffixe `M` ou `m` est de type `decimal`. Par exemple, les littéraux `1m`, `1.5m`, `1e10m`, et `123.456M` sont tous du type `decimal`. Ce littéral est converti en un `decimal` valeur en prenant la valeur exacte et, si nécessaire, arrondi à la valeur représentable le plus proche à l’aide de l’arrondi bancaire ([type décimal](types.md#the-decimal-type)). N’importe quelle échelle apparente dans le littéral est conservée, sauf si la valeur est arrondie ou la valeur est zéro (auquel cas le signe et la mise à l’échelle seront égal à 0). Par conséquent, le littéral `2.900m` est analysé afin de former la décimale avec le signe `0`, coefficient `2900`et l’échelle `3`.

Si le littéral spécifié ne peut pas être représenté dans le type indiqué, une erreur de compilation se produit.

La valeur d’un littéral de type réel `float` ou `double` est déterminé à l’aide de l’IEEE « arrondi au plus près » mode.

Notez que dans un littéral réel, des chiffres décimaux sont toujours nécessaires après la virgule décimale. Par exemple, `1.3F` est un littéral réel mais `1.F` n’est pas.

#### <a name="character-literals"></a>Littéraux de caractère

Un littéral de caractère représente un caractère unique et se compose généralement d’un caractère entre guillemets, comme dans `'a'`.

Remarque : La notation de grammaire ANTLR, les opérations suivantes à confusion ! Dans ANTLR, lorsque vous écrivez `\'` il représente un seul guillemet `'`. Et lorsque vous écrivez `\\` il représente une barre oblique inverse unique `\`. Par conséquent la première règle pour un littéral de caractère signifie qu’il commence par un guillemet, puis un caractère, puis un seul guillemet. Et les séquences d’échappement simples possibles onze sont `\'`, `\"`, `\\`, `\0`, `\a`, `\b`, `\f`, `\n`, `\r`, `\t`, `\v`.

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

Un caractère qui suit une barre oblique inverse (`\`) dans un *caractère* doit être un des caractères suivants : `'`, `"`, `\`, `0`, `a`, `b` , `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`. Sinon, une erreur de compilation se produit.

Une séquence d’échappement hexadécimale représente un caractère Unicode, avec la valeur est formée par le nombre hexadécimal qui suit «`\x`».

Si la valeur représentée par un littéral de caractère est supérieure à `U+FFFF`, une erreur de compilation se produit.

Une séquence d’échappement de caractères Unicode ([séquences d’échappement de caractère Unicode](lexical-structure.md#unicode-character-escape-sequences)) dans un littéral de caractère doit être comprise entre `U+0000` à `U+FFFF`.

Une séquence d’échappement simple représente un encodage de caractères Unicode, comme décrit dans le tableau ci-dessous.


| __Séquence d’échappement__ | __Nom de caractère__ | __Encodage Unicode__ |
|---------------------|--------------------|----------------------|
| `\'`                | Guillemet simple       | `0x0027`             | 
| `\"`                | Guillemet double       | `0x0022`             | 
| `\\`                | Barre oblique inverse          | `0x005C`             | 
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

C# prend en charge deux formes de littéraux de chaîne : ***littéraux de chaîne classique*** et ***littéraux de chaîne textuelle***.

Un littéral de chaîne standard se compose de zéro ou plusieurs caractères placée entourés guillemets doubles, comme dans `"hello"`et peut inclure les deux séquences d’échappement simples (telles que `\t` pour le caractère de tabulation), hexadécimales et des séquences d’échappement Unicode.

Un littéral de chaîne textuelle se compose d’un `@` caractère suivi d’un caractère de guillemet double, zéro ou plusieurs caractères et un caractère de guillemet fermant. Voici un exemple simple `@"hello"`. Dans un littéral de chaîne textuelle, les caractères entre les délimiteurs sont interprétés textuellement, la seule exception qui est un *quote_escape_sequence*. En particulier, les séquences d’échappement simples, hexadécimales et des séquences d’échappement Unicode ne sont pas traités dans les littéraux de chaîne textuelle. Un littéral de chaîne textuelle peut-être s’étendre sur plusieurs lignes.

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

Un caractère qui suit une barre oblique inverse (`\`) dans un *regular_string_literal_character* doit être un des caractères suivants : `'`, `"`, `\`, `0`, `a` , `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`. Sinon, une erreur de compilation se produit.

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
présente les divers littéraux de chaîne. La dernière chaîne littérale, `j`, est un littéral de chaîne textuelle qui s’étend sur plusieurs lignes. Les caractères entre guillemets, y compris les espaces blancs tels que des caractères de nouvelle ligne, sont textuelles préservés.

Dans la mesure où une séquence d’échappement hexadécimale peut avoir un nombre variable de chiffres hexadécimaux, le littéral de chaîne `"\x123"` contient un caractère unique avec une valeur hexadécimale 123. Pour créer une chaîne contenant le caractère à la valeur hexadécimale 12 suivie du caractère 3, écrire `"\x00123"` ou `"\x12" + "3"` à la place.

Le type d’un *string_literal* est `string`.

Chaque littéral de chaîne n’entraîne pas nécessairement dans une nouvelle instance de chaîne. Lorsque deux ou plusieurs littéraux de chaîne qui sont équivalentes en fonction de l’opérateur d’égalité de chaînes ([opérateurs d’égalité de chaîne](expressions.md#string-equality-operators)) apparaissent dans le même programme, ces littéraux de chaîne font référence à la même instance de chaîne. Par exemple, la sortie produite par
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
est `True` , car les deux littéraux font référence à la même instance de chaîne.

#### <a name="interpolated-string-literals"></a>Littéraux de chaîne interpolée

Littéraux de chaîne interpolée sont similaires aux littéraux de chaîne, mais contient des trous délimitées par `{` et `}`, dans laquelle les expressions peuvent se produire. Lors de l’exécution, les expressions sont évaluées dans le but d’avoir leurs formes textuelles substituées dans la chaîne à l’endroit où se produit le trou. La syntaxe et la sémantique d’interpolation de chaîne est décrites dans la section ([chaînes interpolées](expressions.md#interpolated-strings)).

Comme les littéraux de chaîne, les littéraux de chaîne interpolée peuvent être régulier ou textuelle. Littéraux de chaîne classique interpolées sont délimitées par `$"` et `"`, et les littéraux de chaîne textuelle interpolée par `$@"` et `"`.

Comme les autres littéraux, analyse lexicale d’un littéral de chaîne interpolée entraîne initialement un jeton unique, conformément à la grammaire suivante. Toutefois, avant l’analyse syntaxique, le jeton unique d’un littéral de chaîne interpolé est divisé en plusieurs jetons pour les parties de la chaîne englobant les trous et les éléments d’entrée qui se produisent dans les trous sont analysés lexicalement à nouveau. Cela peut produire à son tour plus les littéraux de chaîne interpolée pour être traitée, mais, si lexicalement corriger, peut qu’aboutir à une séquence de jetons pour l’analyse syntaxique à traiter.

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

Un *interpolated_string_literal* jeton est réinterprété comme plusieurs jetons et autres éléments d’entrée comme suit, dans l’ordre d’occurrence dans la *interpolated_string_literal*:

* Occurrences des éléments suivants sont réinterprétées en tant que jetons individuels distincts : le caractère de début `$` signe, *interpolated_regular_string_whole*, *interpolated_regular_string_start*, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_whole*,  *interpolated_verbatim_string_start*, *interpolated_verbatim_string_mid* et *interpolated_verbatim_string_end*.
* Occurrences de *regular_balanced_text* et *verbatim_balanced_text* entre ceux-ci sont traités de nouveau en tant qu’un *input_section* ([analyse lexicale ](lexical-structure.md#lexical-analysis)) et sont réinterprétée comme la séquence résultante des éléments d’entrée. Il peut s’agir à son tour les jetons de littéral de chaîne interpolée d’être réinterprétée.

L’analyse syntaxique sera recombiner les jetons dans un *interpolated_string_expression* ([chaînes interpolées](expressions.md#interpolated-strings)).

Exemples TODO


#### <a name="the-null-literal"></a>Le littéral null

```antlr
null_literal
    : 'null'
    ;
```

Le *null_literal* peut être implicitement converti en un type référence ou un type nullable.

### <a name="operators-and-punctuators"></a>Opérateurs et signes de ponctuation

Il existe plusieurs types d’opérateurs et signes de ponctuation. Opérateurs sont utilisés dans les expressions pour décrire les opérations impliquant un ou plusieurs opérandes. Par exemple, l’expression `a + b` utilise le `+` opérateur pour ajouter les deux opérandes `a` et `b`. Signes de ponctuation sont pour le regroupement et la séparation.

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

La barre verticale de la *right_shift* et *right_shift_assignment* productions sont utilisées pour indiquer que, contrairement à d’autres productions dans la grammaire syntaxique, aucun caractère de n’importe quel type (pas même espace blanc) sont autorisés entre les jetons. Celles-ci est traités spécialement afin d’activer la gestion correcte de *type_parameter_list*s ([paramètres de Type](classes.md#type-parameters)).

## <a name="pre-processing-directives"></a>Directives de prétraitement

Les directives de prétraitement permettent d’ignorer sous certaines conditions des sections des fichiers sources, de rapport conditions d’erreur et avertissement et pour délimiter des régions distinctes du code source. Le terme « directives de prétraitement » est utilisé uniquement pour des raisons de cohérence avec les langages de programmation C et C++. En c#, il n’existe aucune étape de prétraitement distincte ; directives de prétraitement sont traitées dans le cadre de la phase d’analyse lexicale.

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

Les directives de prétraitement suivantes sont disponibles :

*  `#define` et `#undef`, qui sont utilisés pour définir et annuler la définition, respectivement, les symboles de compilation conditionnelle ([directives de déclaration](lexical-structure.md#declaration-directives)).
*  `#if`, `#elif`, `#else`, et `#endif`, qui sont utilisées pour ignorer sous certaines conditions des sections de code source ([directives de compilation conditionnelle](lexical-structure.md#conditional-compilation-directives)).
*  `#line`, qui est utilisé pour contrôler les numéros de ligne émis pour les erreurs et avertissements ([directives de ligne](lexical-structure.md#line-directives)).
*  `#error` et `#warning`, qui sont utilisées pour émettre des erreurs et avertissements, respectivement ([directives Diagnostic](lexical-structure.md#diagnostic-directives)).
*  `#region` et `#endregion`, qui sont utilisées pour marquer explicitement les sections de code source ([directives Region](lexical-structure.md#region-directives)).
*  `#pragma`, qui sert à spécifier des informations contextuelles supplémentaires au compilateur ([directives Pragma](lexical-structure.md#pragma-directives)).

Une directive de prétraitement occupe toujours une ligne distincte de code source et commence toujours par un `#` caractère et un nom de directive de prétraitement. Espace blanc peut se produire avant la `#` caractère et entre le `#` caractère et le nom de la directive.

Une ligne source qui contient un `#define`, `#undef`, `#if`, `#elif`, `#else`, `#endif`, `#line`, ou `#endregion` directive peut se terminer par un commentaire sur une ligne. Commentaires délimités (le `/* */` style des commentaires) ne sont pas autorisées sur les lignes de code source contenant des directives de prétraitement.

Directives de prétraitement ne sont pas des jetons et ne font pas partie de la grammaire syntaxique de c#. Toutefois, les directives de prétraitement peuvent être utilisées pour inclure ou exclure des séquences de jetons et pouvant ainsi affecter la signification d’un programme c#. Par exemple, lors de la compilation, le programme :
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
résultats dans exactement la même séquence de jetons que le programme :
```csharp
class C
{
    void F() {}
    void I() {}
}
```

Par conséquent, tandis que lexicalement, les deux programmes sont très différentes, syntaxiquement, ils sont identiques.

### <a name="conditional-compilation-symbols"></a>Symboles de compilation conditionnelle

La fonctionnalité de compilation conditionnelle fournie par le `#if`, `#elif`, `#else`, et `#endif` directives est contrôlé par le biais des expressions de prétraitement ([expressions de pré-traitement](lexical-structure.md#pre-processing-expressions)) et les symboles de compilation conditionnelle.

```antlr
conditional_symbol
    : '<Any identifier_or_keyword except true or false>'
    ;
```

Un symbole de compilation conditionnelle a deux états possibles : ***défini*** ou ***undefined***. Au début du traitement lexical d’un fichier source, un symbole de compilation conditionnelle n’est pas défini, sauf si elle a été définie explicitement par un mécanisme externe (par exemple, une option du compilateur de ligne de commande). Quand un `#define` directive est traitée, le symbole de compilation conditionnelle nommé dans cette directive devient défini dans ce fichier source. Le symbole reste défini jusqu'à un `#undef` directive pour ce même symbole est traité, ou jusqu'à la fin du fichier source. Une conséquence de cela est que `#define` et `#undef` directives dans un fichier source n’ont aucun effet sur les autres fichiers sources dans le même programme.

Lorsque référencés dans une expression de pré-traitement, un symbole de compilation conditionnelle définie a la valeur booléenne `true`, et un symbole de compilation conditionnelle non défini a la valeur booléenne `false`. Il n’est pas nécessaire que les symboles de compilation conditionnelle soient explicitement déclarés avant d’être référencés dans les expressions de pré-traitement. Au lieu de cela, des symboles non déclarées sont simplement non définis et n’ont donc la valeur `false`.

L’espace de noms pour les symboles de compilation conditionnelle est distinct et indépendant de tous les autres entités nommées dans un programme c#. Symboles de compilation conditionnelle peuvent uniquement être référencés dans `#define` et `#undef` directives et dans les expressions de pré-traitement.

### <a name="pre-processing-expressions"></a>Expressions de pré-traitement

Expressions de pré-traitement peuvent se produire dans `#if` et `#elif` directives. Les opérateurs `!`, `==`, `!=`, `&&` et `||` sont autorisés dans les expressions, de pré-traitement et de parenthèses peuvent être utilisées pour le regroupement.

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

Lorsque référencés dans une expression de pré-traitement, un symbole de compilation conditionnelle définie a la valeur booléenne `true`, et un symbole de compilation conditionnelle non défini a la valeur booléenne `false`.

L’évaluation d’une expression de pré-traitement toujours génère une valeur booléenne. Les règles d’évaluation pour une expression de prétraitement sont les mêmes que celles d’une expression constante ([expressions constantes](expressions.md#constant-expressions)), sauf que les entités définies par l’utilisateur uniquement peuvent être référencées sont des symboles de compilation conditionnelle .

### <a name="declaration-directives"></a>Directives de déclaration

Les directives de déclaration sont utilisés pour définir ou annuler la définition de symboles de compilation conditionnelle.

```antlr
pp_declaration
    : whitespace? '#' whitespace? 'define' whitespace conditional_symbol pp_new_line
    | whitespace? '#' whitespace? 'undef' whitespace conditional_symbol pp_new_line
    ;

pp_new_line
    : whitespace? single_line_comment? new_line
    ;
```

Le traitement d’un `#define` directive provoque le symbole de compilation conditionnelle sont définis, en commençant par la ligne source qui suit la directive. De même, le traitement d’un `#undef` directive provoque le symbole de compilation conditionnelle devienne indéfini, en commençant par la ligne source qui suit la directive.

N’importe quel `#define` et `#undef` directives dans un fichier source doivent se produire avant la première *jeton* ([jetons](lexical-structure.md#tokens)) dans le fichier source ; sinon une erreur de compilation se produit. En d’autres termes, `#define` et `#undef` directives doivent précéder les « code réel » dans le fichier source.

L’exemple :
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
est valide, car le `#define` directives précèdent le premier jeton (le `namespace` mot clé) dans le fichier source.

L’exemple suivant génère une erreur de compilation, car un `#define` suit le code réel :
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

Un `#define` peut définir un symbole de compilation conditionnelle est déjà défini, sans être n’importe quel intervenant `#undef` pour ce symbole. L’exemple ci-dessous définit un symbole de compilation conditionnelle `A` et définit ensuite de nouveau.
```csharp
#define A
#define A
```

Un `#undef` peut « annuler la définition « un symbole de compilation conditionnelle n’est pas défini. L’exemple ci-dessous définit un symbole de compilation conditionnelle `A` et puis annule la définition il à deux reprises ; bien que la seconde `#undef` n’a aucun effet, il est encore valide.
```csharp
#define A
#undef A
#undef A
```

### <a name="conditional-compilation-directives"></a>Directives de compilation conditionnelle

Les directives de compilation conditionnelle sont utilisés pour inclure ou exclure certaines parties d’un fichier source de manière conditionnelle.

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

Comme indiqué par la syntaxe, les directives de compilation conditionnelle doivent être écrite sous forme de jeux comportant, dans l’ordre, un `#if` directive, zéro ou plusieurs `#elif` directives, zéro ou un `#else` directive et un `#endif` directive. Entre les directives sont des sections conditionnelles du code source. Chaque section est contrôlée par la directive immédiatement précédente. Une section conditionnelle peut elle-même contenir des directives de compilation conditionnelle imbriquées fourni ces directives forment des jeux complets.

Un *pp_conditional* sélectionne au moins l’une de la relation contenant-contenu *conditional_section*s pour le traitement lexical normal :

*  Le *pp_expression*s de la `#if` et `#elif` directives sont évaluées dans l’ordre jusqu'à ce qu’un produit `true`. Si une expression produit `true`, le *conditional_section* de la directive correspondante est sélectionnée.
*  Si tous les *pp_expression*yield de s `false`et si un `#else` directive est présente, le *conditional_section* de la `#else` directive est sélectionnée.
*  Sinon, non *conditional_section* est sélectionné.

Le texte sélectionné *conditional_section*, le cas échéant, est traité comme un élément normal *input_section*: du code source contenu dans la section doit adhérer à la grammaire lexicale ; jetons sont générés à partir de la source code de la section ; et directives de prétraitement dans la section ont les effets prescrits.

Les autres *conditional_section*s, le cas échéant, sont traités en tant que *skipped_section*s: à l’exception des directives de prétraitement, le code source dans la section ne s’appliquent pas à la lexicale grammaire ; non les jetons sont générés à partir du code source dans la section ; et directives de prétraitement dans la section doivent être lexicalement corrects mais ne sont pas traités dans le cas contraire. Au sein d’un *conditional_section* qui est en cours de traitement en tant qu’un *skipped_section*, toute imbriquée *conditional_section*s (contenues dans imbriquée `#if`... `#endif` et `#region`... `#endregion` construit) sont également traités en tant que *skipped_section*s.

L’exemple suivant illustre la compilation conditionnelle comment imbriquer des directives :
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

À l’exception des directives de préprocesseur, code source ignoré n’est pas soumis à une analyse lexicale. Par exemple, ce qui suit est valide en dépit du commentaire non terminé dans le `#else` section :
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

Toutefois, notez que les directives de prétraitement sont requis pour être lexicalement correcte, même dans les sections ignorées du code source.

Directives de prétraitement ne sont pas traités lorsqu’ils apparaissent à l’intérieur d’éléments d’entrée multilignes. Par exemple, le programme :
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
résultats dans la sortie :
```
hello,
#if Debug
        world
#else
        Nebraska
#endif
```

Dans ces cas particuliers, l’ensemble des directives de prétraitement qui est traitée peut dépendre de l’évaluation de la *pp_expression*. L’exemple :
```csharp
#if X
    /*
#else
    /* */ class Q { }
#endif
```
produit toujours le même flux de jetons (`class` `Q` `{` `}`), quelle que soit ou non `X` est défini. Si `X` est défini, le sont les seules directives traitées `#if` et `#endif`en raison du commentaire multiligne. Si `X` est non défini, puis trois directives (`#if`, `#else`, `#endif`) font partie de l’ensemble de directive.

### <a name="diagnostic-directives"></a>Directives de diagnostic

Les directives de diagnostics sont utilisées pour générer explicitement des messages d’erreur et avertissement qui sont signalées dans la même façon que d’autres erreurs de compilation et les avertissements.

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

L’exemple :
```csharp
#warning Code review needed before check-in

#if Debug && Retail
    #error A build can't be both debug and retail
#endif

class Test {...}
```
toujours génère un avertissement (« révision du Code nécessaire avant l’archivage ») et génère une erreur de compilation (« une build ne peut pas être débogage et vente au détail ») si les symboles conditionnels `Debug` et `Retail` sont tous deux définis. Notez qu’un *pp_message* peuvent contenir du texte arbitraire ; plus précisément, elle pas besoin de contenir des jetons correct, comme indiqué par le guillemet simple dans le mot `can't`.

### <a name="region-directives"></a>Directives de région

Les directives de région sont utilisées pour marquer explicitement les régions du code source.

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

Aucune signification sémantique n’est attachée à une région ; régions sont prévues pour une utilisation par le programmeur ou par des outils automatisés pour marquer une section de code source. Le message spécifié dans un `#region` ou `#endregion` même directive n’a aucune signification sémantique ; il sert surtout à identifier la région. Mise en correspondance `#region` et `#endregion` directives peuvent avoir différents *pp_message*s.

Le traitement lexical d’une région :
```csharp
#region
...
#endregion
```
correspond exactement au traitement lexical d’une directive de compilation conditionnelle du formulaire :
```csharp
#if true
...
#endif
```

### <a name="line-directives"></a>Directives de ligne

Directives de ligne peuvent être utilisés pour modifier les numéros de ligne et les noms de fichiers sources qui sont signalés par le compilateur de sortie tels que les erreurs et avertissements, et qui sont utilisés par les attributs des informations de l’appelant ([attributs d’informations appelant](attributes.md#caller-info-attributes)).

Directives de ligne sont couramment utilisés dans les outils de métaprogrammation qui génèrent le code source c# à partir d’un autre texte d’entrée.

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

En cas de non `#line` directives sont présents, le compilateur signale les numéros de ligne true et noms de fichiers sources dans sa sortie. Lors du traitement d’un `#line` directive inclut un *line_indicator* qui n’est pas `default`, le compilateur traite la ligne après la directive comme ayant le numéro de ligne donné (et le nom de fichier, si spécifié).

Un `#line default` directive inverse l’effet de toutes les directives #line précédentes. Le compilateur signale les informations de ligne true pour les lignes suivantes, exactement comme si aucune `#line` directives avaient été traités.

Un `#line hidden` directive n’a aucun effet sur le fichier et les numéros de ligne indiqué dans l’erreur des messages, mais affectent le débogage au niveau du code source. Lors du débogage, toutes les lignes entre un `#line hidden` directive et le `#line` directive (qui n’est pas `#line hidden`) ne possèdent aucune information de numéro de ligne. Lorsque vous parcourez le code dans le débogueur, ces lignes seront ignorées entièrement.

Notez qu’un *file_name* diffère de littéral de chaîne standard caractères d’échappement ne sont pas traités ; le «`\`» caractère désigne simplement un caractère de barre oblique inverse ordinaire dans un *file_name*.

### <a name="pragma-directives"></a>Directives pragma

Le `#pragma` directive de prétraitement est utilisée pour spécifier des informations contextuelles supplémentaires pour le compilateur. Les informations fournies dans un `#pragma` directive ne changera jamais sémantique du programme.

```antlr
pp_pragma
    : whitespace? '#' whitespace? 'pragma' whitespace pragma_body pp_new_line
    ;

pragma_body
    : pragma_warning_body
    ;
```

C# fournit `#pragma` directives pour contrôler les avertissements du compilateur. Les versions futures du langage peuvent inclure supplémentaires `#pragma` directives. Pour garantir l’interopérabilité avec d’autres compilateurs c#, le compilateur Microsoft C# n’émet pas d’erreurs de compilation pour inconnu `#pragma` directives ; de ce type ne de directives toutefois générer des avertissements.

#### <a name="pragma-warning"></a>Avertissement de pragma

Le `#pragma warning` directive est utilisée pour désactiver ou de restaurer toutes ou un ensemble particulier d’avertissement messages pendant la compilation du texte de programme ultérieures.

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

Un `#pragma warning` directive qui omet la liste d’avertissements affecte tous les avertissements. Un `#pragma warning` directive l’inclut une liste d’avertissements affecte uniquement les avertissements qui sont spécifiés dans la liste.

Un `#pragma warning disable` directive désactive tous les ou l’ensemble donné d’avertissements.

Un `#pragma warning restore` directive restaure tous les ou l’ensemble donné d’avertissements à l’état qui était en vigueur au début de l’unité de compilation. Notez que si un avertissement particulier a été désactivé en externe, un `#pragma warning restore` (s’il pour tous ou l’avertissement spécifique) n’est pas réactiver cet avertissement.

L’exemple suivant illustre l’utilisation de `#pragma warning` pour désactiver temporairement l’avertissement a signalé lorsque obsolète membres sont référencés, à l’aide du numéro d’avertissement du compilateur Microsoft C#.
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
