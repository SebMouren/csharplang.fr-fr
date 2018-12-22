# <a name="lexical-structure"></a><span data-ttu-id="2064a-101">Structure lexicale</span><span class="sxs-lookup"><span data-stu-id="2064a-101">Lexical structure</span></span>

## <a name="programs"></a><span data-ttu-id="2064a-102">Programs</span><span class="sxs-lookup"><span data-stu-id="2064a-102">Programs</span></span>

<span data-ttu-id="2064a-103">C# ***programme*** se compose d’une ou plusieurs ***fichiers sources***, appelées formellement ***unités de compilation*** ([unités de Compilation](namespaces.md#compilation-units)).</span><span class="sxs-lookup"><span data-stu-id="2064a-103">A C# ***program*** consists of one or more ***source files***, known formally as ***compilation units*** ([Compilation units](namespaces.md#compilation-units)).</span></span> <span data-ttu-id="2064a-104">Un fichier source est une séquence ordonnée de caractères Unicode.</span><span class="sxs-lookup"><span data-stu-id="2064a-104">A source file is an ordered sequence of Unicode characters.</span></span> <span data-ttu-id="2064a-105">Fichiers sources ont généralement une correspondance avec les fichiers dans un système de fichiers, mais cette correspondance n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="2064a-105">Source files typically have a one-to-one correspondence with files in a file system, but this correspondence is not required.</span></span> <span data-ttu-id="2064a-106">Pour une portabilité maximale, il est recommandé que les fichiers dans un système de fichiers être encodés avec UTF-8 de codage.</span><span class="sxs-lookup"><span data-stu-id="2064a-106">For maximal portability, it is recommended that files in a file system be encoded with the UTF-8 encoding.</span></span>

<span data-ttu-id="2064a-107">Point de vue conceptuel, un programme est compilé à l’aide de trois étapes :</span><span class="sxs-lookup"><span data-stu-id="2064a-107">Conceptually speaking, a program is compiled using three steps:</span></span>

1. <span data-ttu-id="2064a-108">Transformation, qui convertit un fichier à partir d’un répertoire de caractères particulier et un schéma d’encodage en une séquence de caractères Unicode.</span><span class="sxs-lookup"><span data-stu-id="2064a-108">Transformation, which converts a file from a particular character repertoire and encoding scheme into a sequence of Unicode characters.</span></span>
2. <span data-ttu-id="2064a-109">Analyse lexicale, qui convertit un flux de caractères d’entrée Unicode dans un flux de jetons.</span><span class="sxs-lookup"><span data-stu-id="2064a-109">Lexical analysis, which translates a stream of Unicode input characters into a stream of tokens.</span></span>
3. <span data-ttu-id="2064a-110">Analyse syntaxique, qui convertit le flux de jetons en code exécutable.</span><span class="sxs-lookup"><span data-stu-id="2064a-110">Syntactic analysis, which translates the stream of tokens into executable code.</span></span>

## <a name="grammars"></a><span data-ttu-id="2064a-111">Grammaires</span><span class="sxs-lookup"><span data-stu-id="2064a-111">Grammars</span></span>

<span data-ttu-id="2064a-112">Cette spécification présente la syntaxe de programmation c# à l’aide de deux grammaires.</span><span class="sxs-lookup"><span data-stu-id="2064a-112">This specification presents the syntax of the C# programming language using two grammars.</span></span> <span data-ttu-id="2064a-113">Le ***grammaire lexicale*** ([grammaire lexicale](lexical-structure.md#lexical-grammar)) définit comment les caractères Unicode sont combinés pour les terminateurs de ligne de formulaire, les espaces blancs, les commentaires, les jetons et les directives de prétraitement.</span><span class="sxs-lookup"><span data-stu-id="2064a-113">The ***lexical grammar*** ([Lexical grammar](lexical-structure.md#lexical-grammar)) defines how Unicode characters are combined to form line terminators, white space, comments, tokens, and pre-processing directives.</span></span> <span data-ttu-id="2064a-114">Le ***grammaire syntaxique*** ([grammaire syntaxique](lexical-structure.md#syntactic-grammar)) définit la façon dont les jetons résultant de la grammaire lexicale sont combinées pour former c# des programmes.</span><span class="sxs-lookup"><span data-stu-id="2064a-114">The ***syntactic grammar*** ([Syntactic grammar](lexical-structure.md#syntactic-grammar)) defines how the tokens resulting from the lexical grammar are combined to form C# programs.</span></span>

### <a name="grammar-notation"></a><span data-ttu-id="2064a-115">Notation de la grammaire</span><span class="sxs-lookup"><span data-stu-id="2064a-115">Grammar notation</span></span>

<span data-ttu-id="2064a-116">Les grammaires lexicales et syntaxiques sont présentées sous forme Backus-Naur à l’aide de la notation de l’outil de grammaire ANTLR.</span><span class="sxs-lookup"><span data-stu-id="2064a-116">The lexical and syntactic grammars are presented in Backus-Naur form using the notation of the ANTLR grammar tool.</span></span>

### <a name="lexical-grammar"></a><span data-ttu-id="2064a-117">Grammaire lexicale</span><span class="sxs-lookup"><span data-stu-id="2064a-117">Lexical grammar</span></span>

<span data-ttu-id="2064a-118">La grammaire lexicale de c# est présentée dans [analyse lexicale](lexical-structure.md#lexical-analysis), [jetons](lexical-structure.md#tokens), et [directives de prétraitement](lexical-structure.md#pre-processing-directives).</span><span class="sxs-lookup"><span data-stu-id="2064a-118">The lexical grammar of C# is presented in [Lexical analysis](lexical-structure.md#lexical-analysis), [Tokens](lexical-structure.md#tokens), and [Pre-processing directives](lexical-structure.md#pre-processing-directives).</span></span> <span data-ttu-id="2064a-119">Les symboles terminaux de la grammaire lexicale sont les caractères du jeu de caractères Unicode et la grammaire lexicale spécifie la façon dont les caractères sont combinées aux jetons de formulaire ([jetons](lexical-structure.md#tokens)), un espace blanc ([blancs](lexical-structure.md#white-space)), commentaires ([commentaires](lexical-structure.md#comments)) et les directives de prétraitement ([directives de prétraitement](lexical-structure.md#pre-processing-directives)).</span><span class="sxs-lookup"><span data-stu-id="2064a-119">The terminal symbols of the lexical grammar are the characters of the Unicode character set, and the lexical grammar specifies how characters are combined to form tokens ([Tokens](lexical-structure.md#tokens)), white space ([White space](lexical-structure.md#white-space)), comments ([Comments](lexical-structure.md#comments)), and pre-processing directives ([Pre-processing directives](lexical-structure.md#pre-processing-directives)).</span></span>

<span data-ttu-id="2064a-120">Chaque fichier source dans un programme c# doit être conforme à la *d’entrée* production de la grammaire lexicale ([analyse lexicale](lexical-structure.md#lexical-analysis)).</span><span class="sxs-lookup"><span data-stu-id="2064a-120">Every source file in a C# program must conform to the *input* production of the lexical grammar ([Lexical analysis](lexical-structure.md#lexical-analysis)).</span></span>

### <a name="syntactic-grammar"></a><span data-ttu-id="2064a-121">Grammaire syntaxique</span><span class="sxs-lookup"><span data-stu-id="2064a-121">Syntactic grammar</span></span>

<span data-ttu-id="2064a-122">La grammaire syntaxique de c# est présentée dans les chapitres et les annexes qui suivent ce chapitre.</span><span class="sxs-lookup"><span data-stu-id="2064a-122">The syntactic grammar of C# is presented in the chapters and appendices that follow this chapter.</span></span> <span data-ttu-id="2064a-123">Les symboles terminaux de la grammaire syntaxique sont les jetons définis par la grammaire lexicale et la grammaire syntaxique spécifie la façon dont les jetons sont combinées pour former des programmes c#.</span><span class="sxs-lookup"><span data-stu-id="2064a-123">The terminal symbols of the syntactic grammar are the tokens defined by the lexical grammar, and the syntactic grammar specifies how tokens are combined to form C# programs.</span></span>

<span data-ttu-id="2064a-124">Chaque fichier source dans un C# programme doit être conforme à la *compilation_unit* production de la grammaire syntaxique ([unités de Compilation](namespaces.md#compilation-units)).</span><span class="sxs-lookup"><span data-stu-id="2064a-124">Every source file in a C# program must conform to the *compilation_unit* production of the syntactic grammar ([Compilation units](namespaces.md#compilation-units)).</span></span>

## <a name="lexical-analysis"></a><span data-ttu-id="2064a-125">Analyse lexicale</span><span class="sxs-lookup"><span data-stu-id="2064a-125">Lexical analysis</span></span>

<span data-ttu-id="2064a-126">Le *d’entrée* production définit la structure lexicale d’un fichier source c#.</span><span class="sxs-lookup"><span data-stu-id="2064a-126">The *input* production defines the lexical structure of a C# source file.</span></span> <span data-ttu-id="2064a-127">Chaque fichier source dans un programme c# doit être conforme à la production de cette grammaire lexicale.</span><span class="sxs-lookup"><span data-stu-id="2064a-127">Each source file in a C# program must conform to this lexical grammar production.</span></span>

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

<span data-ttu-id="2064a-128">Cinq éléments de base constituent la structure lexicale d’un C# fichier source : Terminateurs de ligne ([terminateurs de ligne](lexical-structure.md#line-terminators)), un espace blanc ([espace blanc](lexical-structure.md#white-space)), commentaires ([commentaires](lexical-structure.md#comments)), jetons ([jetons](lexical-structure.md#tokens)), et directives de prétraitement ([directives de prétraitement](lexical-structure.md#pre-processing-directives)).</span><span class="sxs-lookup"><span data-stu-id="2064a-128">Five basic elements make up the lexical structure of a C# source file: Line terminators ([Line terminators](lexical-structure.md#line-terminators)), white space ([White space](lexical-structure.md#white-space)), comments ([Comments](lexical-structure.md#comments)), tokens ([Tokens](lexical-structure.md#tokens)), and pre-processing directives ([Pre-processing directives](lexical-structure.md#pre-processing-directives)).</span></span> <span data-ttu-id="2064a-129">Ces éléments de base, seuls les jetons sont importants dans la grammaire syntaxique d’un programme c# ([grammaire syntaxique](lexical-structure.md#syntactic-grammar)).</span><span class="sxs-lookup"><span data-stu-id="2064a-129">Of these basic elements, only tokens are significant in the syntactic grammar of a C# program ([Syntactic grammar](lexical-structure.md#syntactic-grammar)).</span></span>

<span data-ttu-id="2064a-130">Le traitement lexical d’un fichier source c# consiste à réduire le fichier en une séquence de jetons qui devient l’entrée de l’analyse syntaxique.</span><span class="sxs-lookup"><span data-stu-id="2064a-130">The lexical processing of a C# source file consists of reducing the file into a sequence of tokens which becomes the input to the syntactic analysis.</span></span> <span data-ttu-id="2064a-131">Terminateurs de ligne, les espaces blancs et les commentaires peuvent servir à séparer les jetons et les directives de prétraitement peuvent provoquer des sections du fichier source doit être ignorée, mais sinon ces éléments lexicaux n’ont aucun impact sur la structure syntaxique d’un programme c#.</span><span class="sxs-lookup"><span data-stu-id="2064a-131">Line terminators, white space, and comments can serve to separate tokens, and pre-processing directives can cause sections of the source file to be skipped, but otherwise these lexical elements have no impact on the syntactic structure of a C# program.</span></span>

<span data-ttu-id="2064a-132">Dans le cas des littéraux de chaîne interpolée ([interpolées littéraux de chaîne](lexical-structure.md#interpolated-string-literals)) un jeton unique est généré initialement par l’analyse lexicale, mais il est divisé en plusieurs éléments d’entrée qui sont soumis à plusieurs reprises à l’analyse lexicale jusqu'à ce que tous les littéraux de chaîne interpolée ont été résolus.</span><span class="sxs-lookup"><span data-stu-id="2064a-132">In the case of interpolated string literals ([Interpolated string literals](lexical-structure.md#interpolated-string-literals)) a single token is initially produced by lexical analysis, but is broken up into several input elements which are repeatedly subjected to lexical analysis until all interpolated string literals have been resolved.</span></span> <span data-ttu-id="2064a-133">Les jetons générés puis de fournir en tant qu’entrée de l’analyse syntaxique.</span><span class="sxs-lookup"><span data-stu-id="2064a-133">The resulting tokens then serve as input to the syntactic analysis.</span></span>

<span data-ttu-id="2064a-134">Lorsque plusieurs productions grammaire lexicale correspondent à une séquence de caractères dans un fichier source, le traitement lexical forme toujours l’élément de lexicale la plus longue possible.</span><span class="sxs-lookup"><span data-stu-id="2064a-134">When several lexical grammar productions match a sequence of characters in a source file, the lexical processing always forms the longest possible lexical element.</span></span> <span data-ttu-id="2064a-135">Par exemple, la séquence de caractères `//` est traitée comme le début d’un commentaire sur une ligne, car cet élément lexical est plus long que d’un seul `/` jeton.</span><span class="sxs-lookup"><span data-stu-id="2064a-135">For example, the character sequence `//` is processed as the beginning of a single-line comment because that lexical element is longer than a single `/` token.</span></span>

### <a name="line-terminators"></a><span data-ttu-id="2064a-136">Terminateurs de ligne</span><span class="sxs-lookup"><span data-stu-id="2064a-136">Line terminators</span></span>

<span data-ttu-id="2064a-137">Indicateurs de fin de ligne divisent les caractères d’un fichier source c# en lignes.</span><span class="sxs-lookup"><span data-stu-id="2064a-137">Line terminators divide the characters of a C# source file into lines.</span></span>

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

<span data-ttu-id="2064a-138">Pour la compatibilité avec la source des outils d’édition qui ajoutent des marqueurs de fin de fichier de code, et pour activer une source de fichier à afficher sous forme de séquence de correctement arrêtée lignes, les transformations suivantes sont appliquées dans l’ordre, pour chaque fichier source dans un programme c# :</span><span class="sxs-lookup"><span data-stu-id="2064a-138">For compatibility with source code editing tools that add end-of-file markers, and to enable a source file to be viewed as a sequence of properly terminated lines, the following transformations are applied, in order, to every source file in a C# program:</span></span>

*  <span data-ttu-id="2064a-139">Si le dernier caractère du fichier source est un caractère CTRL + Z (`U+001A`), ce caractère est supprimé.</span><span class="sxs-lookup"><span data-stu-id="2064a-139">If the last character of the source file is a Control-Z character (`U+001A`), this character is deleted.</span></span>
*  <span data-ttu-id="2064a-140">Un caractère de retour chariot (`U+000D`) est ajouté à la fin du fichier source si ce fichier source n’est pas vide et si le dernier caractère du fichier source n’est pas un retour chariot (`U+000D`), un saut de ligne (`U+000A`), un séparateur de ligne (`U+2028`), ou un séparateur de paragraphe (`U+2029`).</span><span class="sxs-lookup"><span data-stu-id="2064a-140">A carriage-return character (`U+000D`) is added to the end of the source file if that source file is non-empty and if the last character of the source file is not a carriage return (`U+000D`), a line feed (`U+000A`), a line separator (`U+2028`), or a paragraph separator (`U+2029`).</span></span>

### <a name="comments"></a><span data-ttu-id="2064a-141">Commentaires</span><span class="sxs-lookup"><span data-stu-id="2064a-141">Comments</span></span>

<span data-ttu-id="2064a-142">Deux formes de commentaires sont pris en charge : commentaires à ligne unique et les commentaires délimités.</span><span class="sxs-lookup"><span data-stu-id="2064a-142">Two forms of comments are supported: single-line comments and delimited comments.</span></span> <span data-ttu-id="2064a-143">***Commentaires à ligne unique*** commencer par les caractères `//` et d’étendre à la fin de la ligne source.</span><span class="sxs-lookup"><span data-stu-id="2064a-143">***Single-line comments*** start with the characters `//` and extend to the end of the source line.</span></span> <span data-ttu-id="2064a-144">***Délimitée par des commentaires*** commencer par les caractères `/*` et se terminent par les caractères `*/`.</span><span class="sxs-lookup"><span data-stu-id="2064a-144">***Delimited comments*** start with the characters `/*` and end with the characters `*/`.</span></span> <span data-ttu-id="2064a-145">Commentaires délimités peuvent s’étendre sur plusieurs lignes.</span><span class="sxs-lookup"><span data-stu-id="2064a-145">Delimited comments may span multiple lines.</span></span>

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

<span data-ttu-id="2064a-146">Ne pas imbriquent les commentaires.</span><span class="sxs-lookup"><span data-stu-id="2064a-146">Comments do not nest.</span></span> <span data-ttu-id="2064a-147">Les séquences de caractères `/*` et `*/` n’ont aucune signification spéciale dans un `//` commentaire et les séquences de caractères `//` et `/*` n’ont aucune signification spéciale dans un commentaire délimité.</span><span class="sxs-lookup"><span data-stu-id="2064a-147">The character sequences `/*` and `*/` have no special meaning within a `//` comment, and the character sequences `//` and `/*` have no special meaning within a delimited comment.</span></span>

<span data-ttu-id="2064a-148">Commentaires ne sont pas traités dans les littéraux de caractère et de chaîne.</span><span class="sxs-lookup"><span data-stu-id="2064a-148">Comments are not processed within character and string literals.</span></span>

<span data-ttu-id="2064a-149">L’exemple</span><span class="sxs-lookup"><span data-stu-id="2064a-149">The example</span></span>
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
<span data-ttu-id="2064a-150">contient un commentaire délimité.</span><span class="sxs-lookup"><span data-stu-id="2064a-150">includes a delimited comment.</span></span>

<span data-ttu-id="2064a-151">L’exemple</span><span class="sxs-lookup"><span data-stu-id="2064a-151">The example</span></span>
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
<span data-ttu-id="2064a-152">montre plusieurs commentaires à ligne unique.</span><span class="sxs-lookup"><span data-stu-id="2064a-152">shows several single-line comments.</span></span>

### <a name="white-space"></a><span data-ttu-id="2064a-153">Espace blanc</span><span class="sxs-lookup"><span data-stu-id="2064a-153">White space</span></span>

<span data-ttu-id="2064a-154">Espace blanc est défini comme caractère de saut de n’importe quel caractère de la classe Unicode Zs (qui inclut le caractère espace), ainsi que le caractère de tabulation horizontale, le caractère de tabulation verticale et le formulaire.</span><span class="sxs-lookup"><span data-stu-id="2064a-154">White space is defined as any character with Unicode class Zs (which includes the space character) as well as the horizontal tab character, the vertical tab character, and the form feed character.</span></span>

```antlr
whitespace
    : '<Any character with Unicode class Zs>'
    | '<Horizontal tab character (U+0009)>'
    | '<Vertical tab character (U+000B)>'
    | '<Form feed character (U+000C)>'
    ;
```

## <a name="tokens"></a><span data-ttu-id="2064a-155">jetons</span><span class="sxs-lookup"><span data-stu-id="2064a-155">Tokens</span></span>

<span data-ttu-id="2064a-156">Il existe plusieurs types de jetons : identificateurs, les mots clés, les littéraux, les opérateurs et les signes de ponctuation.</span><span class="sxs-lookup"><span data-stu-id="2064a-156">There are several kinds of tokens: identifiers, keywords, literals, operators, and punctuators.</span></span> <span data-ttu-id="2064a-157">Espace blanc et les commentaires ne sont pas des jetons, car ils font Office de séparateurs de jetons.</span><span class="sxs-lookup"><span data-stu-id="2064a-157">White space and comments are not tokens, though they act as separators for tokens.</span></span>

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

### <a name="unicode-character-escape-sequences"></a><span data-ttu-id="2064a-158">Séquences d’échappement de caractère Unicode</span><span class="sxs-lookup"><span data-stu-id="2064a-158">Unicode character escape sequences</span></span>

<span data-ttu-id="2064a-159">Une séquence d’échappement de caractères Unicode représente un caractère Unicode.</span><span class="sxs-lookup"><span data-stu-id="2064a-159">A Unicode character escape sequence represents a Unicode character.</span></span> <span data-ttu-id="2064a-160">Séquences d’échappement de caractères Unicode sont traités dans les identificateurs ([identificateurs](lexical-structure.md#identifiers)), littéraux de caractère ([littéraux de caractère](lexical-structure.md#character-literals)) et les littéraux de chaîne classique ([delittérauxdechaîne](lexical-structure.md#string-literals)).</span><span class="sxs-lookup"><span data-stu-id="2064a-160">Unicode character escape sequences are processed in identifiers ([Identifiers](lexical-structure.md#identifiers)), character literals ([Character literals](lexical-structure.md#character-literals)), and regular string literals ([String literals](lexical-structure.md#string-literals)).</span></span> <span data-ttu-id="2064a-161">Un caractère d’échappement Unicode n’est pas traitée dans tout autre emplacement (par exemple, pour former un opérateur, un signe de ponctuation ou un mot clé).</span><span class="sxs-lookup"><span data-stu-id="2064a-161">A Unicode character escape is not processed in any other location (for example, to form an operator, punctuator, or keyword).</span></span>

```antlr
unicode_escape_sequence
    : '\\u' hex_digit hex_digit hex_digit hex_digit
    | '\\U' hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit
    ;
```

<span data-ttu-id="2064a-162">Une séquence d’échappement Unicode représente le caractère Unicode formé par le nombre hexadécimal qui suit le «`\u`« ou »`\U`» caractères.</span><span class="sxs-lookup"><span data-stu-id="2064a-162">A Unicode escape sequence represents the single Unicode character formed by the hexadecimal number following the "`\u`" or "`\U`" characters.</span></span> <span data-ttu-id="2064a-163">Étant donné que c# utilise un codage 16 bits des points de code Unicode en caractères et les valeurs de chaîne, un caractère Unicode dans la plage U + 10000 à U + 10FFFF n’est pas autorisé dans un littéral de caractère et est représenté à l’aide d’une paire de substitution Unicode dans un littéral de chaîne.</span><span class="sxs-lookup"><span data-stu-id="2064a-163">Since C# uses a 16-bit encoding of Unicode code points in characters and string values, a Unicode character in the range U+10000 to U+10FFFF is not permitted in a character literal and is represented using a Unicode surrogate pair in a string literal.</span></span> <span data-ttu-id="2064a-164">Les caractères Unicode avec des points de code supérieurs à 0x10FFFF ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="2064a-164">Unicode characters with code points above 0x10FFFF are not supported.</span></span>

<span data-ttu-id="2064a-165">Plusieurs traductions ne sont pas effectuées.</span><span class="sxs-lookup"><span data-stu-id="2064a-165">Multiple translations are not performed.</span></span> <span data-ttu-id="2064a-166">Par exemple, le littéral de chaîne «`\u005Cu005C`« équivaut à »`\u005C`« plutôt que »`\`».</span><span class="sxs-lookup"><span data-stu-id="2064a-166">For instance, the string literal "`\u005Cu005C`" is equivalent to "`\u005C`" rather than "`\`".</span></span> <span data-ttu-id="2064a-167">La valeur Unicode `\u005C` est le caractère «`\`».</span><span class="sxs-lookup"><span data-stu-id="2064a-167">The Unicode value `\u005C` is the character "`\`".</span></span>

<span data-ttu-id="2064a-168">L’exemple</span><span class="sxs-lookup"><span data-stu-id="2064a-168">The example</span></span>
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
<span data-ttu-id="2064a-169">illustre plusieurs utilisations de `\u0066`, qui est la séquence d’échappement pour la lettre «`f`».</span><span class="sxs-lookup"><span data-stu-id="2064a-169">shows several uses of `\u0066`, which is the escape sequence for the letter "`f`".</span></span> <span data-ttu-id="2064a-170">Le programme est équivalent à</span><span class="sxs-lookup"><span data-stu-id="2064a-170">The program is equivalent to</span></span>
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

### <a name="identifiers"></a><span data-ttu-id="2064a-171">Identificateurs</span><span class="sxs-lookup"><span data-stu-id="2064a-171">Identifiers</span></span>

<span data-ttu-id="2064a-172">Les règles des identificateurs fournies dans cette section correspondent exactement à ces recommandée par le Unicode Standard à l’annexe 31, sauf qu’un trait de soulignement est autorisé comme caractère initial (tel quel traditionnel dans le langage de programmation C), sont des séquences de la séquence d’échappement Unicode autorisées dans les identificateurs et le «`@`« caractère est autorisé en tant que préfixe pour activer des mots clés pour être utilisés comme identificateurs.</span><span class="sxs-lookup"><span data-stu-id="2064a-172">The rules for identifiers given in this section correspond exactly to those recommended by the Unicode Standard Annex 31, except that underscore is allowed as an initial character (as is traditional in the C programming language), Unicode escape sequences are permitted in identifiers, and the "`@`" character is allowed as a prefix to enable keywords to be used as identifiers.</span></span>

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

<span data-ttu-id="2064a-173">Pour plus d’informations sur les classes de caractères Unicode mentionnées ci-dessus, consultez la norme Unicode, Version 3.0, section 4.5.</span><span class="sxs-lookup"><span data-stu-id="2064a-173">For information on the Unicode character classes mentioned above, see The Unicode Standard, Version 3.0, section 4.5.</span></span>

<span data-ttu-id="2064a-174">Exemples d’identificateurs valides »`identifier1`«, »`_identifier2`», et «`@if`».</span><span class="sxs-lookup"><span data-stu-id="2064a-174">Examples of valid identifiers include "`identifier1`", "`_identifier2`", and "`@if`".</span></span>

<span data-ttu-id="2064a-175">Un identificateur dans un programme conforme doit être au format canonique défini par le formulaire de normalisation Unicode C, tel que défini par la norme Unicode annexe 15.</span><span class="sxs-lookup"><span data-stu-id="2064a-175">An identifier in a conforming program must be in the canonical format defined by Unicode Normalization Form C, as defined by Unicode Standard Annex 15.</span></span> <span data-ttu-id="2064a-176">Le comportement lorsqu’il rencontre un identificateur pas dans le formulaire de normalisation C est défini par l’implémentation ; Toutefois, un diagnostic n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="2064a-176">The behavior when encountering an identifier not in Normalization Form C is implementation-defined; however, a diagnostic is not required.</span></span>

<span data-ttu-id="2064a-177">Le préfixe «`@`» permet d’utiliser des mots clés comme identificateurs, ce qui est utile lors de l’interfaçage avec d’autres langages de programmation.</span><span class="sxs-lookup"><span data-stu-id="2064a-177">The prefix "`@`" enables the use of keywords as identifiers, which is useful when interfacing with other programming languages.</span></span> <span data-ttu-id="2064a-178">Le caractère `@` ne faisant pas partie de l’identificateur, l’identificateur peut être constaté dans d’autres langages comme identificateur normal, sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="2064a-178">The character `@` is not actually part of the identifier, so the identifier might be seen in other languages as a normal identifier, without the prefix.</span></span> <span data-ttu-id="2064a-179">Un identificateur avec un `@` préfixe est appelé un ***identificateur textuel***.</span><span class="sxs-lookup"><span data-stu-id="2064a-179">An identifier with an `@` prefix is called a ***verbatim identifier***.</span></span> <span data-ttu-id="2064a-180">Utilisation de la `@` préfixe pour les identificateurs qui ne sont pas des mots clés est autorisée, mais fortement déconseillée en tant qu’une question de style.</span><span class="sxs-lookup"><span data-stu-id="2064a-180">Use of the `@` prefix for identifiers that are not keywords is permitted, but strongly discouraged as a matter of style.</span></span>

<span data-ttu-id="2064a-181">L’exemple :</span><span class="sxs-lookup"><span data-stu-id="2064a-181">The example:</span></span>
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
<span data-ttu-id="2064a-182">définit une classe nommée «`class`« avec une méthode statique nommée »`static`« qui accepte un paramètre nommé »`bool`».</span><span class="sxs-lookup"><span data-stu-id="2064a-182">defines a class named "`class`" with a static method named "`static`" that takes a parameter named "`bool`".</span></span> <span data-ttu-id="2064a-183">Notez que dans la mesure où échappements Unicode ne sont pas autorisés dans les mots clés, le jeton «`cl\u0061ss`« est un identificateur, et est le même identificateur que »`@class`».</span><span class="sxs-lookup"><span data-stu-id="2064a-183">Note that since Unicode escapes are not permitted in keywords, the token "`cl\u0061ss`" is an identifier, and is the same identifier as "`@class`".</span></span>

<span data-ttu-id="2064a-184">Deux identificateurs sont considérés comme identiques s’ils sont identiques, une fois que les transformations suivantes sont appliquées, dans l’ordre :</span><span class="sxs-lookup"><span data-stu-id="2064a-184">Two identifiers are considered the same if they are identical after the following transformations are applied, in order:</span></span>

*  <span data-ttu-id="2064a-185">Le préfixe «`@`», le cas échéant, est supprimé.</span><span class="sxs-lookup"><span data-stu-id="2064a-185">The prefix "`@`", if used, is removed.</span></span>
*  <span data-ttu-id="2064a-186">Chaque *unicode_escape_sequence* est transformée en son caractère Unicode correspondant.</span><span class="sxs-lookup"><span data-stu-id="2064a-186">Each *unicode_escape_sequence* is transformed into its corresponding Unicode character.</span></span>
*  <span data-ttu-id="2064a-187">N’importe quel *formatting_character*sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="2064a-187">Any *formatting_character*s are removed.</span></span>

<span data-ttu-id="2064a-188">Les identificateurs contenant deux consécutifs les traits de soulignement (`U+005F`) sont réservés pour une utilisation par l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="2064a-188">Identifiers containing two consecutive underscore characters (`U+005F`) are reserved for use by the implementation.</span></span> <span data-ttu-id="2064a-189">Par exemple, une implémentation peut fournir des mots clés étendus qui commencent par deux traits de soulignement.</span><span class="sxs-lookup"><span data-stu-id="2064a-189">For example, an implementation might provide extended keywords that begin with two underscores.</span></span>

### <a name="keywords"></a><span data-ttu-id="2064a-190">Mots clés</span><span class="sxs-lookup"><span data-stu-id="2064a-190">Keywords</span></span>

<span data-ttu-id="2064a-191">Un ***mot clé*** est une séquence de caractères similaire à l’identificateur qui est réservé et ne peut pas être utilisée en tant qu’identificateur, sauf si elle est précédée du `@` caractère.</span><span class="sxs-lookup"><span data-stu-id="2064a-191">A ***keyword*** is an identifier-like sequence of characters that is reserved, and cannot be used as an identifier except when prefaced by the `@` character.</span></span>

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

<span data-ttu-id="2064a-192">Dans certains endroits dans la grammaire, des identificateurs spécifiques ont une signification particulière, mais ne sont pas des mots clés.</span><span class="sxs-lookup"><span data-stu-id="2064a-192">In some places in the grammar, specific identifiers have special meaning, but are not keywords.</span></span> <span data-ttu-id="2064a-193">Ces identificateurs sont parfois appelés « mots clés contextuels ».</span><span class="sxs-lookup"><span data-stu-id="2064a-193">Such identifiers are sometimes referred to as "contextual keywords".</span></span> <span data-ttu-id="2064a-194">Par exemple, dans une déclaration de propriété, le «`get`« et »`set`« identificateurs ont une signification spéciale ([accesseurs](classes.md#accessors)).</span><span class="sxs-lookup"><span data-stu-id="2064a-194">For example, within a property declaration, the "`get`" and "`set`" identifiers have special meaning ([Accessors](classes.md#accessors)).</span></span> <span data-ttu-id="2064a-195">Un identificateur autre que `get` ou `set` n’est jamais autorisé à ces emplacements, donc cette utilisation n’est pas en conflit avec une utilisation de ces mots comme identificateurs.</span><span class="sxs-lookup"><span data-stu-id="2064a-195">An identifier other than `get` or `set` is never permitted in these locations, so this use does not conflict with a use of these words as identifiers.</span></span> <span data-ttu-id="2064a-196">Dans d’autres cas, comme avec l’identificateur «`var`» dans les déclarations de variables locales implicitement typées ([déclarations de variables locales](statements.md#local-variable-declarations)), un mot clé contextuel peut entrer en conflit avec les noms déclarés.</span><span class="sxs-lookup"><span data-stu-id="2064a-196">In other cases, such as with the identifier "`var`" in implicitly typed local variable declarations ([Local variable declarations](statements.md#local-variable-declarations)), a contextual keyword can conflict with declared names.</span></span> <span data-ttu-id="2064a-197">Dans ce cas, le nom déclaré est prioritaire sur l’utilisation de l’identificateur comme un mot clé contextuel.</span><span class="sxs-lookup"><span data-stu-id="2064a-197">In such cases, the declared name takes precedence over the use of the identifier as a contextual keyword.</span></span>

### <a name="literals"></a><span data-ttu-id="2064a-198">Littéraux</span><span class="sxs-lookup"><span data-stu-id="2064a-198">Literals</span></span>

<span data-ttu-id="2064a-199">Un ***littéral*** est une représentation sous forme de code source d’une valeur.</span><span class="sxs-lookup"><span data-stu-id="2064a-199">A ***literal*** is a source code representation of a value.</span></span>

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

#### <a name="boolean-literals"></a><span data-ttu-id="2064a-200">Littéraux booléens</span><span class="sxs-lookup"><span data-stu-id="2064a-200">Boolean literals</span></span>

<span data-ttu-id="2064a-201">Il existe deux valeurs de littéral booléens : `true` et `false`.</span><span class="sxs-lookup"><span data-stu-id="2064a-201">There are two boolean literal values: `true` and `false`.</span></span>

```antlr
boolean_literal
    : 'true'
    | 'false'
    ;
```

<span data-ttu-id="2064a-202">Le type d’un *boolean_literal* est `bool`.</span><span class="sxs-lookup"><span data-stu-id="2064a-202">The type of a *boolean_literal* is `bool`.</span></span>

#### <a name="integer-literals"></a><span data-ttu-id="2064a-203">Littéraux d'entier</span><span class="sxs-lookup"><span data-stu-id="2064a-203">Integer literals</span></span>

<span data-ttu-id="2064a-204">Les littéraux entiers sont utilisées pour écrire des valeurs de types `int`, `uint`, `long`, et `ulong`.</span><span class="sxs-lookup"><span data-stu-id="2064a-204">Integer literals are used to write values of types `int`, `uint`, `long`, and `ulong`.</span></span> <span data-ttu-id="2064a-205">Les littéraux entiers prendre deux formes : décimal et hexadécimal.</span><span class="sxs-lookup"><span data-stu-id="2064a-205">Integer literals have two possible forms: decimal and hexadecimal.</span></span>

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

<span data-ttu-id="2064a-206">Le type d’un littéral entier est déterminé comme suit :</span><span class="sxs-lookup"><span data-stu-id="2064a-206">The type of an integer literal is determined as follows:</span></span>

*  <span data-ttu-id="2064a-207">Si le littéral n’a aucun suffixe, il a le premier de ces types dans lesquels sa valeur peut être représentée : `int`, `uint`, `long`, `ulong`.</span><span class="sxs-lookup"><span data-stu-id="2064a-207">If the literal has no suffix, it has the first of these types in which its value can be represented: `int`, `uint`, `long`, `ulong`.</span></span>
*  <span data-ttu-id="2064a-208">Si le littéral est suivi du suffixe `U` ou `u`, il prend le premier de ces types dans lesquels sa valeur peut être représentée : `uint`, `ulong`.</span><span class="sxs-lookup"><span data-stu-id="2064a-208">If the literal is suffixed by `U` or `u`, it has the first of these types in which its value can be represented: `uint`, `ulong`.</span></span>
*  <span data-ttu-id="2064a-209">Si le littéral est suivi du suffixe `L` ou `l`, il prend le premier de ces types dans lesquels sa valeur peut être représentée : `long`, `ulong`.</span><span class="sxs-lookup"><span data-stu-id="2064a-209">If the literal is suffixed by `L` or `l`, it has the first of these types in which its value can be represented: `long`, `ulong`.</span></span>
*  <span data-ttu-id="2064a-210">Si le littéral est suivi du suffixe `UL`, `Ul`, `uL`, `ul`, `LU`, `Lu`, `lU`, ou `lu`, il est de type `ulong`.</span><span class="sxs-lookup"><span data-stu-id="2064a-210">If the literal is suffixed by `UL`, `Ul`, `uL`, `ul`, `LU`, `Lu`, `lU`, or `lu`, it is of type `ulong`.</span></span>

<span data-ttu-id="2064a-211">Si la valeur représentée par un littéral entier est en dehors de la plage de la `ulong` type, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="2064a-211">If the value represented by an integer literal is outside the range of the `ulong` type, a compile-time error occurs.</span></span>

<span data-ttu-id="2064a-212">Comme une question de style, il est conseillé qui «`L`« être utilisé au lieu de »`l`» lors de l’écriture des littéraux de type `long`, car il est facile de confondre la lettre «`l`« avec le chiffre »`1`».</span><span class="sxs-lookup"><span data-stu-id="2064a-212">As a matter of style, it is suggested that "`L`" be used instead of "`l`" when writing literals of type `long`, since it is easy to confuse the letter "`l`" with the digit "`1`".</span></span>

<span data-ttu-id="2064a-213">Pour autoriser la plus petite possible `int` et `long` valeurs soient écrites comme des littéraux d’entier décimal, les deux règles suivantes existent :</span><span class="sxs-lookup"><span data-stu-id="2064a-213">To permit the smallest possible `int` and `long` values to be written as decimal integer literals, the following two rules exist:</span></span>

* <span data-ttu-id="2064a-214">Quand un *decimal_integer_literal* ayant la valeur 2147483648 (2 ^ 31) et aucun *integer_type_suffix* apparaît en tant que le jeton qui suit immédiatement un jeton d’opérateur unaire ([moins unaire opérateur](expressions.md#unary-minus-operator)), le résultat est une constante de type `int` avec la valeur -2147483648 (-2 ^ 31).</span><span class="sxs-lookup"><span data-stu-id="2064a-214">When a *decimal_integer_literal* with the value 2147483648 (2^31) and no *integer_type_suffix* appears as the token immediately following a unary minus operator token ([Unary minus operator](expressions.md#unary-minus-operator)), the result is a constant of type `int` with the value -2147483648 (-2^31).</span></span> <span data-ttu-id="2064a-215">Dans toutes les autres situations, tel un *decimal_integer_literal* est de type `uint`.</span><span class="sxs-lookup"><span data-stu-id="2064a-215">In all other situations, such a *decimal_integer_literal* is of type `uint`.</span></span>
* <span data-ttu-id="2064a-216">Quand un *decimal_integer_literal* avec la valeur 9223372036854775808 (2 ^ 63) et aucun *integer_type_suffix* ou *integer_type_suffix* `L` ou `l` apparaît en tant que le jeton qui suit immédiatement un jeton d’opérateur unaire ([opérateur moins unaire](expressions.md#unary-minus-operator)), le résultat est une constante de type `long` avec la valeur -9223372036854775808 (-2 ^ 63).</span><span class="sxs-lookup"><span data-stu-id="2064a-216">When a *decimal_integer_literal* with the value 9223372036854775808 (2^63) and no *integer_type_suffix* or the *integer_type_suffix* `L` or `l` appears as the token immediately following a unary minus operator token ([Unary minus operator](expressions.md#unary-minus-operator)), the result is a constant of type `long` with the value -9223372036854775808 (-2^63).</span></span> <span data-ttu-id="2064a-217">Dans toutes les autres situations, tel un *decimal_integer_literal* est de type `ulong`.</span><span class="sxs-lookup"><span data-stu-id="2064a-217">In all other situations, such a *decimal_integer_literal* is of type `ulong`.</span></span>

#### <a name="real-literals"></a><span data-ttu-id="2064a-218">Littéraux réels</span><span class="sxs-lookup"><span data-stu-id="2064a-218">Real literals</span></span>

<span data-ttu-id="2064a-219">Littéraux réels sont utilisées pour écrire des valeurs de types `float`, `double`, et `decimal`.</span><span class="sxs-lookup"><span data-stu-id="2064a-219">Real literals are used to write values of types `float`, `double`, and `decimal`.</span></span>

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

<span data-ttu-id="2064a-220">Si aucun *real_type_suffix* est spécifié, le type du littéral réel est `double`.</span><span class="sxs-lookup"><span data-stu-id="2064a-220">If no *real_type_suffix* is specified, the type of the real literal is `double`.</span></span> <span data-ttu-id="2064a-221">Le cas contraire, le suffixe de type réel détermine le type du littéral réel, comme suit :</span><span class="sxs-lookup"><span data-stu-id="2064a-221">Otherwise, the real type suffix determines the type of the real literal, as follows:</span></span>

*  <span data-ttu-id="2064a-222">Un littéral réel suivi du suffixe `F` ou `f` est de type `float`.</span><span class="sxs-lookup"><span data-stu-id="2064a-222">A real literal suffixed by `F` or `f` is of type `float`.</span></span> <span data-ttu-id="2064a-223">Par exemple, les littéraux `1f`, `1.5f`, `1e10f`, et `123.456F` sont tous du type `float`.</span><span class="sxs-lookup"><span data-stu-id="2064a-223">For example, the literals `1f`, `1.5f`, `1e10f`, and `123.456F` are all of type `float`.</span></span>
*  <span data-ttu-id="2064a-224">Un littéral réel suivi du suffixe `D` ou `d` est de type `double`.</span><span class="sxs-lookup"><span data-stu-id="2064a-224">A real literal suffixed by `D` or `d` is of type `double`.</span></span> <span data-ttu-id="2064a-225">Par exemple, les littéraux `1d`, `1.5d`, `1e10d`, et `123.456D` sont tous du type `double`.</span><span class="sxs-lookup"><span data-stu-id="2064a-225">For example, the literals `1d`, `1.5d`, `1e10d`, and `123.456D` are all of type `double`.</span></span>
*  <span data-ttu-id="2064a-226">Un littéral réel suivi du suffixe `M` ou `m` est de type `decimal`.</span><span class="sxs-lookup"><span data-stu-id="2064a-226">A real literal suffixed by `M` or `m` is of type `decimal`.</span></span> <span data-ttu-id="2064a-227">Par exemple, les littéraux `1m`, `1.5m`, `1e10m`, et `123.456M` sont tous du type `decimal`.</span><span class="sxs-lookup"><span data-stu-id="2064a-227">For example, the literals `1m`, `1.5m`, `1e10m`, and `123.456M` are all of type `decimal`.</span></span> <span data-ttu-id="2064a-228">Ce littéral est converti en un `decimal` valeur en prenant la valeur exacte et, si nécessaire, arrondi à la valeur représentable le plus proche à l’aide de l’arrondi bancaire ([type décimal](types.md#the-decimal-type)).</span><span class="sxs-lookup"><span data-stu-id="2064a-228">This literal is converted to a `decimal` value by taking the exact value, and, if necessary, rounding to the nearest representable value using banker's rounding ([The decimal type](types.md#the-decimal-type)).</span></span> <span data-ttu-id="2064a-229">N’importe quelle échelle apparente dans le littéral est conservée, sauf si la valeur est arrondie ou la valeur est zéro (auquel cas le signe et la mise à l’échelle seront égal à 0).</span><span class="sxs-lookup"><span data-stu-id="2064a-229">Any scale apparent in the literal is preserved unless the value is rounded or the value is zero (in which latter case the sign and scale will be 0).</span></span> <span data-ttu-id="2064a-230">Par conséquent, le littéral `2.900m` est analysé afin de former la décimale avec le signe `0`, coefficient `2900`et l’échelle `3`.</span><span class="sxs-lookup"><span data-stu-id="2064a-230">Hence, the literal `2.900m` will be parsed to form the decimal with sign `0`, coefficient `2900`, and scale `3`.</span></span>

<span data-ttu-id="2064a-231">Si le littéral spécifié ne peut pas être représenté dans le type indiqué, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="2064a-231">If the specified literal cannot be represented in the indicated type, a compile-time error occurs.</span></span>

<span data-ttu-id="2064a-232">La valeur d’un littéral de type réel `float` ou `double` est déterminé à l’aide de l’IEEE « arrondi au plus près » mode.</span><span class="sxs-lookup"><span data-stu-id="2064a-232">The value of a real literal of type `float` or `double` is determined by using the IEEE "round to nearest" mode.</span></span>

<span data-ttu-id="2064a-233">Notez que dans un littéral réel, des chiffres décimaux sont toujours nécessaires après la virgule décimale.</span><span class="sxs-lookup"><span data-stu-id="2064a-233">Note that in a real literal, decimal digits are always required after the decimal point.</span></span> <span data-ttu-id="2064a-234">Par exemple, `1.3F` est un littéral réel mais `1.F` n’est pas.</span><span class="sxs-lookup"><span data-stu-id="2064a-234">For example, `1.3F` is a real literal but `1.F` is not.</span></span>

#### <a name="character-literals"></a><span data-ttu-id="2064a-235">Littéraux de caractère</span><span class="sxs-lookup"><span data-stu-id="2064a-235">Character literals</span></span>

<span data-ttu-id="2064a-236">Un littéral de caractère représente un caractère unique et se compose généralement d’un caractère entre guillemets, comme dans `'a'`.</span><span class="sxs-lookup"><span data-stu-id="2064a-236">A character literal represents a single character, and usually consists of a character in quotes, as in `'a'`.</span></span>

<span data-ttu-id="2064a-237">Remarque : La notation de grammaire ANTLR, les opérations suivantes à confusion !</span><span class="sxs-lookup"><span data-stu-id="2064a-237">Note: The ANTLR grammar notation makes the following confusing!</span></span> <span data-ttu-id="2064a-238">Dans ANTLR, lorsque vous écrivez `\'` il représente un seul guillemet `'`.</span><span class="sxs-lookup"><span data-stu-id="2064a-238">In ANTLR, when you write `\'` it stands for a single quote `'`.</span></span> <span data-ttu-id="2064a-239">Et lorsque vous écrivez `\\` il représente une barre oblique inverse unique `\`.</span><span class="sxs-lookup"><span data-stu-id="2064a-239">And when you write `\\` it stands for a single backslash `\`.</span></span> <span data-ttu-id="2064a-240">Par conséquent la première règle pour un littéral de caractère signifie qu’il commence par un guillemet, puis un caractère, puis un seul guillemet.</span><span class="sxs-lookup"><span data-stu-id="2064a-240">Therefore the first rule for a character literal means it starts with a single quote, then a character, then a single quote.</span></span> <span data-ttu-id="2064a-241">Et les séquences d’échappement simples possibles onze sont `\'`, `\"`, `\\`, `\0`, `\a`, `\b`, `\f`, `\n`, `\r`, `\t`, `\v`.</span><span class="sxs-lookup"><span data-stu-id="2064a-241">And the eleven possible simple escape sequences are `\'`, `\"`, `\\`, `\0`, `\a`, `\b`, `\f`, `\n`, `\r`, `\t`, `\v`.</span></span>

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

<span data-ttu-id="2064a-242">Un caractère qui suit une barre oblique inverse (`\`) dans un *caractère* doit être un des caractères suivants : `'`, `"`, `\`, `0`, `a`, `b` , `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span><span class="sxs-lookup"><span data-stu-id="2064a-242">A character that follows a backslash character (`\`) in a *character* must be one of the following characters: `'`, `"`, `\`, `0`, `a`, `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span></span> <span data-ttu-id="2064a-243">Sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="2064a-243">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="2064a-244">Une séquence d’échappement hexadécimale représente un caractère Unicode, avec la valeur est formée par le nombre hexadécimal qui suit «`\x`».</span><span class="sxs-lookup"><span data-stu-id="2064a-244">A hexadecimal escape sequence represents a single Unicode character, with the value formed by the hexadecimal number following "`\x`".</span></span>

<span data-ttu-id="2064a-245">Si la valeur représentée par un littéral de caractère est supérieure à `U+FFFF`, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="2064a-245">If the value represented by a character literal is greater than `U+FFFF`, a compile-time error occurs.</span></span>

<span data-ttu-id="2064a-246">Une séquence d’échappement de caractères Unicode ([séquences d’échappement de caractère Unicode](lexical-structure.md#unicode-character-escape-sequences)) dans un littéral de caractère doit être comprise entre `U+0000` à `U+FFFF`.</span><span class="sxs-lookup"><span data-stu-id="2064a-246">A Unicode character escape sequence ([Unicode character escape sequences](lexical-structure.md#unicode-character-escape-sequences)) in a character literal must be in the range `U+0000` to `U+FFFF`.</span></span>

<span data-ttu-id="2064a-247">Une séquence d’échappement simple représente un encodage de caractères Unicode, comme décrit dans le tableau ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="2064a-247">A simple escape sequence represents a Unicode character encoding, as described in the table below.</span></span>


| <span data-ttu-id="2064a-248">__Séquence d’échappement__</span><span class="sxs-lookup"><span data-stu-id="2064a-248">__Escape sequence__</span></span> | <span data-ttu-id="2064a-249">__Nom de caractère__</span><span class="sxs-lookup"><span data-stu-id="2064a-249">__Character name__</span></span> | <span data-ttu-id="2064a-250">__Encodage Unicode__</span><span class="sxs-lookup"><span data-stu-id="2064a-250">__Unicode encoding__</span></span> |
|---------------------|--------------------|----------------------|
| `\'`                | <span data-ttu-id="2064a-251">Guillemet simple</span><span class="sxs-lookup"><span data-stu-id="2064a-251">Single quote</span></span>       | `0x0027`             | 
| `\"`                | <span data-ttu-id="2064a-252">Guillemet double</span><span class="sxs-lookup"><span data-stu-id="2064a-252">Double quote</span></span>       | `0x0022`             | 
| `\\`                | <span data-ttu-id="2064a-253">Barre oblique inverse</span><span class="sxs-lookup"><span data-stu-id="2064a-253">Backslash</span></span>          | `0x005C`             | 
| `\0`                | <span data-ttu-id="2064a-254">Null</span><span class="sxs-lookup"><span data-stu-id="2064a-254">Null</span></span>               | `0x0000`             | 
| `\a`                | <span data-ttu-id="2064a-255">Alerte</span><span class="sxs-lookup"><span data-stu-id="2064a-255">Alert</span></span>              | `0x0007`             | 
| `\b`                | <span data-ttu-id="2064a-256">Retour arrière</span><span class="sxs-lookup"><span data-stu-id="2064a-256">Backspace</span></span>          | `0x0008`             | 
| `\f`                | <span data-ttu-id="2064a-257">Saut de page</span><span class="sxs-lookup"><span data-stu-id="2064a-257">Form feed</span></span>          | `0x000C`             | 
| `\n`                | <span data-ttu-id="2064a-258">Nouvelle ligne</span><span class="sxs-lookup"><span data-stu-id="2064a-258">New line</span></span>           | `0x000A`             | 
| `\r`                | <span data-ttu-id="2064a-259">Retour chariot</span><span class="sxs-lookup"><span data-stu-id="2064a-259">Carriage return</span></span>    | `0x000D`             | 
| `\t`                | <span data-ttu-id="2064a-260">Tabulation horizontale</span><span class="sxs-lookup"><span data-stu-id="2064a-260">Horizontal tab</span></span>     | `0x0009`             | 
| `\v`                | <span data-ttu-id="2064a-261">Tabulation verticale</span><span class="sxs-lookup"><span data-stu-id="2064a-261">Vertical tab</span></span>       | `0x000B`             | 

<span data-ttu-id="2064a-262">Le type d’un *character_literal* est `char`.</span><span class="sxs-lookup"><span data-stu-id="2064a-262">The type of a *character_literal* is `char`.</span></span>

#### <a name="string-literals"></a><span data-ttu-id="2064a-263">Littéraux de chaîne</span><span class="sxs-lookup"><span data-stu-id="2064a-263">String literals</span></span>

<span data-ttu-id="2064a-264">C# prend en charge deux formes de littéraux de chaîne : ***littéraux de chaîne classique*** et ***littéraux de chaîne textuelle***.</span><span class="sxs-lookup"><span data-stu-id="2064a-264">C# supports two forms of string literals: ***regular string literals*** and ***verbatim string literals***.</span></span>

<span data-ttu-id="2064a-265">Un littéral de chaîne standard se compose de zéro ou plusieurs caractères placée entourés guillemets doubles, comme dans `"hello"`et peut inclure les deux séquences d’échappement simples (telles que `\t` pour le caractère de tabulation), hexadécimales et des séquences d’échappement Unicode.</span><span class="sxs-lookup"><span data-stu-id="2064a-265">A regular string literal consists of zero or more characters enclosed in double quotes, as in `"hello"`, and may include both simple escape sequences (such as `\t` for the tab character), and hexadecimal and Unicode escape sequences.</span></span>

<span data-ttu-id="2064a-266">Un littéral de chaîne textuelle se compose d’un `@` caractère suivi d’un caractère de guillemet double, zéro ou plusieurs caractères et un caractère de guillemet fermant.</span><span class="sxs-lookup"><span data-stu-id="2064a-266">A verbatim string literal consists of an `@` character followed by a double-quote character, zero or more characters, and a closing double-quote character.</span></span> <span data-ttu-id="2064a-267">Voici un exemple simple `@"hello"`.</span><span class="sxs-lookup"><span data-stu-id="2064a-267">A simple example is `@"hello"`.</span></span> <span data-ttu-id="2064a-268">Dans un littéral de chaîne textuelle, les caractères entre les délimiteurs sont interprétés textuellement, la seule exception qui est un *quote_escape_sequence*.</span><span class="sxs-lookup"><span data-stu-id="2064a-268">In a verbatim string literal, the characters between the delimiters are interpreted verbatim, the only exception being a *quote_escape_sequence*.</span></span> <span data-ttu-id="2064a-269">En particulier, les séquences d’échappement simples, hexadécimales et des séquences d’échappement Unicode ne sont pas traités dans les littéraux de chaîne textuelle.</span><span class="sxs-lookup"><span data-stu-id="2064a-269">In particular, simple escape sequences, and hexadecimal and Unicode escape sequences are not processed in verbatim string literals.</span></span> <span data-ttu-id="2064a-270">Un littéral de chaîne textuelle peut-être s’étendre sur plusieurs lignes.</span><span class="sxs-lookup"><span data-stu-id="2064a-270">A verbatim string literal may span multiple lines.</span></span>

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

<span data-ttu-id="2064a-271">Un caractère qui suit une barre oblique inverse (`\`) dans un *regular_string_literal_character* doit être un des caractères suivants : `'`, `"`, `\`, `0`, `a` , `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span><span class="sxs-lookup"><span data-stu-id="2064a-271">A character that follows a backslash character (`\`) in a *regular_string_literal_character* must be one of the following characters: `'`, `"`, `\`, `0`, `a`, `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span></span> <span data-ttu-id="2064a-272">Sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="2064a-272">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="2064a-273">L’exemple</span><span class="sxs-lookup"><span data-stu-id="2064a-273">The example</span></span>
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
<span data-ttu-id="2064a-274">présente les divers littéraux de chaîne.</span><span class="sxs-lookup"><span data-stu-id="2064a-274">shows a variety of string literals.</span></span> <span data-ttu-id="2064a-275">La dernière chaîne littérale, `j`, est un littéral de chaîne textuelle qui s’étend sur plusieurs lignes.</span><span class="sxs-lookup"><span data-stu-id="2064a-275">The last string literal, `j`, is a verbatim string literal that spans multiple lines.</span></span> <span data-ttu-id="2064a-276">Les caractères entre guillemets, y compris les espaces blancs tels que des caractères de nouvelle ligne, sont textuelles préservés.</span><span class="sxs-lookup"><span data-stu-id="2064a-276">The characters between the quotation marks, including white space such as new line characters, are preserved verbatim.</span></span>

<span data-ttu-id="2064a-277">Dans la mesure où une séquence d’échappement hexadécimale peut avoir un nombre variable de chiffres hexadécimaux, le littéral de chaîne `"\x123"` contient un caractère unique avec une valeur hexadécimale 123.</span><span class="sxs-lookup"><span data-stu-id="2064a-277">Since a hexadecimal escape sequence can have a variable number of hex digits, the string literal `"\x123"` contains a single character with hex value 123.</span></span> <span data-ttu-id="2064a-278">Pour créer une chaîne contenant le caractère à la valeur hexadécimale 12 suivie du caractère 3, écrire `"\x00123"` ou `"\x12" + "3"` à la place.</span><span class="sxs-lookup"><span data-stu-id="2064a-278">To create a string containing the character with hex value 12 followed by the character 3, one could write `"\x00123"` or `"\x12" + "3"` instead.</span></span>

<span data-ttu-id="2064a-279">Le type d’un *string_literal* est `string`.</span><span class="sxs-lookup"><span data-stu-id="2064a-279">The type of a *string_literal* is `string`.</span></span>

<span data-ttu-id="2064a-280">Chaque littéral de chaîne n’entraîne pas nécessairement dans une nouvelle instance de chaîne.</span><span class="sxs-lookup"><span data-stu-id="2064a-280">Each string literal does not necessarily result in a new string instance.</span></span> <span data-ttu-id="2064a-281">Lorsque deux ou plusieurs littéraux de chaîne qui sont équivalentes en fonction de l’opérateur d’égalité de chaînes ([opérateurs d’égalité de chaîne](expressions.md#string-equality-operators)) apparaissent dans le même programme, ces littéraux de chaîne font référence à la même instance de chaîne.</span><span class="sxs-lookup"><span data-stu-id="2064a-281">When two or more string literals that are equivalent according to the string equality operator ([String equality operators](expressions.md#string-equality-operators)) appear in the same program, these string literals refer to the same string instance.</span></span> <span data-ttu-id="2064a-282">Par exemple, la sortie produite par</span><span class="sxs-lookup"><span data-stu-id="2064a-282">For instance, the output produced by</span></span>
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
<span data-ttu-id="2064a-283">est `True` , car les deux littéraux font référence à la même instance de chaîne.</span><span class="sxs-lookup"><span data-stu-id="2064a-283">is `True` because the two literals refer to the same string instance.</span></span>

#### <a name="interpolated-string-literals"></a><span data-ttu-id="2064a-284">Littéraux de chaîne interpolée</span><span class="sxs-lookup"><span data-stu-id="2064a-284">Interpolated string literals</span></span>

<span data-ttu-id="2064a-285">Littéraux de chaîne interpolée sont similaires aux littéraux de chaîne, mais contient des trous délimitées par `{` et `}`, dans laquelle les expressions peuvent se produire.</span><span class="sxs-lookup"><span data-stu-id="2064a-285">Interpolated string literals are similar to string literals, but contain holes delimited by `{` and `}`, wherein expressions can occur.</span></span> <span data-ttu-id="2064a-286">Lors de l’exécution, les expressions sont évaluées dans le but d’avoir leurs formes textuelles substituées dans la chaîne à l’endroit où se produit le trou.</span><span class="sxs-lookup"><span data-stu-id="2064a-286">At runtime, the expressions are evaluated with the purpose of having their textual forms substituted into the string at the place where the hole occurs.</span></span> <span data-ttu-id="2064a-287">La syntaxe et la sémantique d’interpolation de chaîne est décrites dans la section ([chaînes interpolées](expressions.md#interpolated-strings)).</span><span class="sxs-lookup"><span data-stu-id="2064a-287">The syntax and semantics of string interpolation are described in section ([Interpolated strings](expressions.md#interpolated-strings)).</span></span>

<span data-ttu-id="2064a-288">Comme les littéraux de chaîne, les littéraux de chaîne interpolée peuvent être régulier ou textuelle.</span><span class="sxs-lookup"><span data-stu-id="2064a-288">Like string literals, interpolated string literals can be either regular or verbatim.</span></span> <span data-ttu-id="2064a-289">Littéraux de chaîne classique interpolées sont délimitées par `$"` et `"`, et les littéraux de chaîne textuelle interpolée par `$@"` et `"`.</span><span class="sxs-lookup"><span data-stu-id="2064a-289">Interpolated regular string literals are delimited by `$"` and `"`, and interpolated verbatim string literals are delimited by `$@"` and `"`.</span></span>

<span data-ttu-id="2064a-290">Comme les autres littéraux, analyse lexicale d’un littéral de chaîne interpolée entraîne initialement un jeton unique, conformément à la grammaire suivante.</span><span class="sxs-lookup"><span data-stu-id="2064a-290">Like other literals, lexical analysis of an interpolated string literal initially results in a single token, as per the grammar below.</span></span> <span data-ttu-id="2064a-291">Toutefois, avant l’analyse syntaxique, le jeton unique d’un littéral de chaîne interpolé est divisé en plusieurs jetons pour les parties de la chaîne englobant les trous et les éléments d’entrée qui se produisent dans les trous sont analysés lexicalement à nouveau.</span><span class="sxs-lookup"><span data-stu-id="2064a-291">However, before syntactic analysis, the single token of an interpolated string literal is broken into several tokens for the parts of the string enclosing the holes, and the input elements occurring in the holes are lexically analysed again.</span></span> <span data-ttu-id="2064a-292">Cela peut produire à son tour plus les littéraux de chaîne interpolée pour être traitée, mais, si lexicalement corriger, peut qu’aboutir à une séquence de jetons pour l’analyse syntaxique à traiter.</span><span class="sxs-lookup"><span data-stu-id="2064a-292">This may in turn produce more interpolated string literals to be processed, but, if lexically correct, will eventually lead to a sequence of tokens for syntactic analysis to process.</span></span>

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

<span data-ttu-id="2064a-293">Un *interpolated_string_literal* jeton est réinterprété comme plusieurs jetons et autres éléments d’entrée comme suit, dans l’ordre d’occurrence dans la *interpolated_string_literal*:</span><span class="sxs-lookup"><span data-stu-id="2064a-293">An *interpolated_string_literal* token is reinterpreted as multiple tokens and other input elements as follows, in order of occurrence in the *interpolated_string_literal*:</span></span>

* <span data-ttu-id="2064a-294">Occurrences des éléments suivants sont réinterprétées en tant que jetons individuels distincts : le caractère de début `$` signe, *interpolated_regular_string_whole*, *interpolated_regular_string_start*, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_whole*,  *interpolated_verbatim_string_start*, *interpolated_verbatim_string_mid* et *interpolated_verbatim_string_end*.</span><span class="sxs-lookup"><span data-stu-id="2064a-294">Occurrences of the following are reinterpreted as separate individual tokens: the leading `$` sign, *interpolated_regular_string_whole*, *interpolated_regular_string_start*, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_whole*, *interpolated_verbatim_string_start*, *interpolated_verbatim_string_mid* and *interpolated_verbatim_string_end*.</span></span>
* <span data-ttu-id="2064a-295">Occurrences de *regular_balanced_text* et *verbatim_balanced_text* entre ceux-ci sont traités de nouveau en tant qu’un *input_section* ([analyse lexicale ](lexical-structure.md#lexical-analysis)) et sont réinterprétée comme la séquence résultante des éléments d’entrée.</span><span class="sxs-lookup"><span data-stu-id="2064a-295">Occurrences of *regular_balanced_text* and *verbatim_balanced_text* between these are reprocessed as an *input_section* ([Lexical analysis](lexical-structure.md#lexical-analysis)) and are reinterpreted as the resulting sequence of input elements.</span></span> <span data-ttu-id="2064a-296">Il peut s’agir à son tour les jetons de littéral de chaîne interpolée d’être réinterprétée.</span><span class="sxs-lookup"><span data-stu-id="2064a-296">These may in turn include interpolated string literal tokens to be reinterpreted.</span></span>

<span data-ttu-id="2064a-297">L’analyse syntaxique sera recombiner les jetons dans un *interpolated_string_expression* ([chaînes interpolées](expressions.md#interpolated-strings)).</span><span class="sxs-lookup"><span data-stu-id="2064a-297">Syntactic analysis will recombine the tokens into an *interpolated_string_expression* ([Interpolated strings](expressions.md#interpolated-strings)).</span></span>

<span data-ttu-id="2064a-298">Exemples TODO</span><span class="sxs-lookup"><span data-stu-id="2064a-298">Examples TODO</span></span>


#### <a name="the-null-literal"></a><span data-ttu-id="2064a-299">Le littéral null</span><span class="sxs-lookup"><span data-stu-id="2064a-299">The null literal</span></span>

```antlr
null_literal
    : 'null'
    ;
```

<span data-ttu-id="2064a-300">Le *null_literal* peut être implicitement converti en un type référence ou un type nullable.</span><span class="sxs-lookup"><span data-stu-id="2064a-300">The  *null_literal* can be implicitly converted to a reference type or nullable type.</span></span>

### <a name="operators-and-punctuators"></a><span data-ttu-id="2064a-301">Opérateurs et signes de ponctuation</span><span class="sxs-lookup"><span data-stu-id="2064a-301">Operators and punctuators</span></span>

<span data-ttu-id="2064a-302">Il existe plusieurs types d’opérateurs et signes de ponctuation.</span><span class="sxs-lookup"><span data-stu-id="2064a-302">There are several kinds of operators and punctuators.</span></span> <span data-ttu-id="2064a-303">Opérateurs sont utilisés dans les expressions pour décrire les opérations impliquant un ou plusieurs opérandes.</span><span class="sxs-lookup"><span data-stu-id="2064a-303">Operators are used in expressions to describe operations involving one or more operands.</span></span> <span data-ttu-id="2064a-304">Par exemple, l’expression `a + b` utilise le `+` opérateur pour ajouter les deux opérandes `a` et `b`.</span><span class="sxs-lookup"><span data-stu-id="2064a-304">For example, the expression `a + b` uses the `+` operator to add the two operands `a` and `b`.</span></span> <span data-ttu-id="2064a-305">Signes de ponctuation sont pour le regroupement et la séparation.</span><span class="sxs-lookup"><span data-stu-id="2064a-305">Punctuators are for grouping and separating.</span></span>

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

<span data-ttu-id="2064a-306">La barre verticale de la *right_shift* et *right_shift_assignment* productions sont utilisées pour indiquer que, contrairement à d’autres productions dans la grammaire syntaxique, aucun caractère de n’importe quel type (pas même espace blanc) sont autorisés entre les jetons.</span><span class="sxs-lookup"><span data-stu-id="2064a-306">The vertical bar in the *right_shift* and *right_shift_assignment* productions are used to indicate that, unlike other productions in the syntactic grammar, no characters of any kind (not even whitespace) are allowed between the tokens.</span></span> <span data-ttu-id="2064a-307">Celles-ci est traités spécialement afin d’activer la gestion correcte de *type_parameter_list*s ([paramètres de Type](classes.md#type-parameters)).</span><span class="sxs-lookup"><span data-stu-id="2064a-307">These productions are treated specially in order to enable the correct  handling of *type_parameter_list*s ([Type parameters](classes.md#type-parameters)).</span></span>

## <a name="pre-processing-directives"></a><span data-ttu-id="2064a-308">Directives de prétraitement</span><span class="sxs-lookup"><span data-stu-id="2064a-308">Pre-processing directives</span></span>

<span data-ttu-id="2064a-309">Les directives de prétraitement permettent d’ignorer sous certaines conditions des sections des fichiers sources, de rapport conditions d’erreur et avertissement et pour délimiter des régions distinctes du code source.</span><span class="sxs-lookup"><span data-stu-id="2064a-309">The pre-processing directives provide the ability to conditionally skip sections of source files, to report error and warning conditions, and to delineate distinct regions of source code.</span></span> <span data-ttu-id="2064a-310">Le terme « directives de prétraitement » est utilisé uniquement pour des raisons de cohérence avec les langages de programmation C et C++.</span><span class="sxs-lookup"><span data-stu-id="2064a-310">The term "pre-processing directives" is used only for consistency with the C and C++ programming languages.</span></span> <span data-ttu-id="2064a-311">En c#, il n’existe aucune étape de prétraitement distincte ; directives de prétraitement sont traitées dans le cadre de la phase d’analyse lexicale.</span><span class="sxs-lookup"><span data-stu-id="2064a-311">In C#, there is no separate pre-processing step; pre-processing directives are processed as part of the lexical analysis phase.</span></span>

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

<span data-ttu-id="2064a-312">Les directives de prétraitement suivantes sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="2064a-312">The following pre-processing directives are available:</span></span>

*  <span data-ttu-id="2064a-313">`#define` et `#undef`, qui sont utilisés pour définir et annuler la définition, respectivement, les symboles de compilation conditionnelle ([directives de déclaration](lexical-structure.md#declaration-directives)).</span><span class="sxs-lookup"><span data-stu-id="2064a-313">`#define` and `#undef`, which are used to define and undefine, respectively, conditional compilation symbols ([Declaration directives](lexical-structure.md#declaration-directives)).</span></span>
*  <span data-ttu-id="2064a-314">`#if`, `#elif`, `#else`, et `#endif`, qui sont utilisées pour ignorer sous certaines conditions des sections de code source ([directives de compilation conditionnelle](lexical-structure.md#conditional-compilation-directives)).</span><span class="sxs-lookup"><span data-stu-id="2064a-314">`#if`, `#elif`, `#else`, and `#endif`, which are used to conditionally skip sections of source code ([Conditional compilation directives](lexical-structure.md#conditional-compilation-directives)).</span></span>
*  <span data-ttu-id="2064a-315">`#line`, qui est utilisé pour contrôler les numéros de ligne émis pour les erreurs et avertissements ([directives de ligne](lexical-structure.md#line-directives)).</span><span class="sxs-lookup"><span data-stu-id="2064a-315">`#line`, which is used to control line numbers emitted for errors and warnings ([Line directives](lexical-structure.md#line-directives)).</span></span>
*  <span data-ttu-id="2064a-316">`#error` et `#warning`, qui sont utilisées pour émettre des erreurs et avertissements, respectivement ([directives Diagnostic](lexical-structure.md#diagnostic-directives)).</span><span class="sxs-lookup"><span data-stu-id="2064a-316">`#error` and `#warning`, which are used to issue errors and warnings, respectively ([Diagnostic directives](lexical-structure.md#diagnostic-directives)).</span></span>
*  <span data-ttu-id="2064a-317">`#region` et `#endregion`, qui sont utilisées pour marquer explicitement les sections de code source ([directives Region](lexical-structure.md#region-directives)).</span><span class="sxs-lookup"><span data-stu-id="2064a-317">`#region` and `#endregion`, which are used to explicitly mark sections of source code ([Region directives](lexical-structure.md#region-directives)).</span></span>
*  <span data-ttu-id="2064a-318">`#pragma`, qui sert à spécifier des informations contextuelles supplémentaires au compilateur ([directives Pragma](lexical-structure.md#pragma-directives)).</span><span class="sxs-lookup"><span data-stu-id="2064a-318">`#pragma`, which is used to specify optional contextual information to the compiler ([Pragma directives](lexical-structure.md#pragma-directives)).</span></span>

<span data-ttu-id="2064a-319">Une directive de prétraitement occupe toujours une ligne distincte de code source et commence toujours par un `#` caractère et un nom de directive de prétraitement.</span><span class="sxs-lookup"><span data-stu-id="2064a-319">A pre-processing directive always occupies a separate line of source code and always begins with a `#` character and a pre-processing directive name.</span></span> <span data-ttu-id="2064a-320">Espace blanc peut se produire avant la `#` caractère et entre le `#` caractère et le nom de la directive.</span><span class="sxs-lookup"><span data-stu-id="2064a-320">White space may occur before the `#` character and between the `#` character and the directive name.</span></span>

<span data-ttu-id="2064a-321">Une ligne source qui contient un `#define`, `#undef`, `#if`, `#elif`, `#else`, `#endif`, `#line`, ou `#endregion` directive peut se terminer par un commentaire sur une ligne.</span><span class="sxs-lookup"><span data-stu-id="2064a-321">A source line containing a `#define`, `#undef`, `#if`, `#elif`, `#else`, `#endif`, `#line`, or `#endregion` directive may end with a single-line comment.</span></span> <span data-ttu-id="2064a-322">Commentaires délimités (le `/* */` style des commentaires) ne sont pas autorisées sur les lignes de code source contenant des directives de prétraitement.</span><span class="sxs-lookup"><span data-stu-id="2064a-322">Delimited comments (the `/* */` style of comments) are not permitted on source lines containing pre-processing directives.</span></span>

<span data-ttu-id="2064a-323">Directives de prétraitement ne sont pas des jetons et ne font pas partie de la grammaire syntaxique de c#.</span><span class="sxs-lookup"><span data-stu-id="2064a-323">Pre-processing directives are not tokens and are not part of the syntactic grammar of C#.</span></span> <span data-ttu-id="2064a-324">Toutefois, les directives de prétraitement peuvent être utilisées pour inclure ou exclure des séquences de jetons et pouvant ainsi affecter la signification d’un programme c#.</span><span class="sxs-lookup"><span data-stu-id="2064a-324">However, pre-processing directives can be used to include or exclude sequences of tokens and can in that way affect the meaning of a C# program.</span></span> <span data-ttu-id="2064a-325">Par exemple, lors de la compilation, le programme :</span><span class="sxs-lookup"><span data-stu-id="2064a-325">For example, when compiled, the program:</span></span>
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
<span data-ttu-id="2064a-326">résultats dans exactement la même séquence de jetons que le programme :</span><span class="sxs-lookup"><span data-stu-id="2064a-326">results in the exact same sequence of tokens as the program:</span></span>
```csharp
class C
{
    void F() {}
    void I() {}
}
```

<span data-ttu-id="2064a-327">Par conséquent, tandis que lexicalement, les deux programmes sont très différentes, syntaxiquement, ils sont identiques.</span><span class="sxs-lookup"><span data-stu-id="2064a-327">Thus, whereas lexically, the two programs are quite different, syntactically, they are identical.</span></span>

### <a name="conditional-compilation-symbols"></a><span data-ttu-id="2064a-328">Symboles de compilation conditionnelle</span><span class="sxs-lookup"><span data-stu-id="2064a-328">Conditional compilation symbols</span></span>

<span data-ttu-id="2064a-329">La fonctionnalité de compilation conditionnelle fournie par le `#if`, `#elif`, `#else`, et `#endif` directives est contrôlé par le biais des expressions de prétraitement ([expressions de pré-traitement](lexical-structure.md#pre-processing-expressions)) et les symboles de compilation conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="2064a-329">The conditional compilation functionality provided by the `#if`, `#elif`, `#else`, and `#endif` directives is controlled through pre-processing expressions ([Pre-processing expressions](lexical-structure.md#pre-processing-expressions)) and conditional compilation symbols.</span></span>

```antlr
conditional_symbol
    : '<Any identifier_or_keyword except true or false>'
    ;
```

<span data-ttu-id="2064a-330">Un symbole de compilation conditionnelle a deux états possibles : ***défini*** ou ***undefined***.</span><span class="sxs-lookup"><span data-stu-id="2064a-330">A conditional compilation symbol has two possible states: ***defined*** or ***undefined***.</span></span> <span data-ttu-id="2064a-331">Au début du traitement lexical d’un fichier source, un symbole de compilation conditionnelle n’est pas défini, sauf si elle a été définie explicitement par un mécanisme externe (par exemple, une option du compilateur de ligne de commande).</span><span class="sxs-lookup"><span data-stu-id="2064a-331">At the beginning of the lexical processing of a source file, a conditional compilation symbol is undefined unless it has been explicitly defined by an external mechanism (such as a command-line compiler option).</span></span> <span data-ttu-id="2064a-332">Quand un `#define` directive est traitée, le symbole de compilation conditionnelle nommé dans cette directive devient défini dans ce fichier source.</span><span class="sxs-lookup"><span data-stu-id="2064a-332">When a `#define` directive is processed, the conditional compilation symbol named in that directive becomes defined in that source file.</span></span> <span data-ttu-id="2064a-333">Le symbole reste défini jusqu'à un `#undef` directive pour ce même symbole est traité, ou jusqu'à la fin du fichier source.</span><span class="sxs-lookup"><span data-stu-id="2064a-333">The symbol remains defined until an `#undef` directive for that same symbol is processed, or until the end of the source file is reached.</span></span> <span data-ttu-id="2064a-334">Une conséquence de cela est que `#define` et `#undef` directives dans un fichier source n’ont aucun effet sur les autres fichiers sources dans le même programme.</span><span class="sxs-lookup"><span data-stu-id="2064a-334">An implication of this is that `#define` and `#undef` directives in one source file have no effect on other source files in the same program.</span></span>

<span data-ttu-id="2064a-335">Lorsque référencés dans une expression de pré-traitement, un symbole de compilation conditionnelle définie a la valeur booléenne `true`, et un symbole de compilation conditionnelle non défini a la valeur booléenne `false`.</span><span class="sxs-lookup"><span data-stu-id="2064a-335">When referenced in a pre-processing expression, a defined conditional compilation symbol has the boolean value `true`, and an undefined conditional compilation symbol has the boolean value `false`.</span></span> <span data-ttu-id="2064a-336">Il n’est pas nécessaire que les symboles de compilation conditionnelle soient explicitement déclarés avant d’être référencés dans les expressions de pré-traitement.</span><span class="sxs-lookup"><span data-stu-id="2064a-336">There is no requirement that conditional compilation symbols be explicitly declared before they are referenced in pre-processing expressions.</span></span> <span data-ttu-id="2064a-337">Au lieu de cela, des symboles non déclarées sont simplement non définis et n’ont donc la valeur `false`.</span><span class="sxs-lookup"><span data-stu-id="2064a-337">Instead, undeclared symbols are simply undefined and thus have the value `false`.</span></span>

<span data-ttu-id="2064a-338">L’espace de noms pour les symboles de compilation conditionnelle est distinct et indépendant de tous les autres entités nommées dans un programme c#.</span><span class="sxs-lookup"><span data-stu-id="2064a-338">The name space for conditional compilation symbols is distinct and separate from all other named entities in a C# program.</span></span> <span data-ttu-id="2064a-339">Symboles de compilation conditionnelle peuvent uniquement être référencés dans `#define` et `#undef` directives et dans les expressions de pré-traitement.</span><span class="sxs-lookup"><span data-stu-id="2064a-339">Conditional compilation symbols can only be referenced in `#define` and `#undef` directives and in pre-processing expressions.</span></span>

### <a name="pre-processing-expressions"></a><span data-ttu-id="2064a-340">Expressions de pré-traitement</span><span class="sxs-lookup"><span data-stu-id="2064a-340">Pre-processing expressions</span></span>

<span data-ttu-id="2064a-341">Expressions de pré-traitement peuvent se produire dans `#if` et `#elif` directives.</span><span class="sxs-lookup"><span data-stu-id="2064a-341">Pre-processing expressions can occur in `#if` and `#elif` directives.</span></span> <span data-ttu-id="2064a-342">Les opérateurs `!`, `==`, `!=`, `&&` et `||` sont autorisés dans les expressions, de pré-traitement et de parenthèses peuvent être utilisées pour le regroupement.</span><span class="sxs-lookup"><span data-stu-id="2064a-342">The operators `!`, `==`, `!=`, `&&` and `||` are permitted in pre-processing expressions, and parentheses may be used for grouping.</span></span>

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

<span data-ttu-id="2064a-343">Lorsque référencés dans une expression de pré-traitement, un symbole de compilation conditionnelle définie a la valeur booléenne `true`, et un symbole de compilation conditionnelle non défini a la valeur booléenne `false`.</span><span class="sxs-lookup"><span data-stu-id="2064a-343">When referenced in a pre-processing expression, a defined conditional compilation symbol has the boolean value `true`, and an undefined conditional compilation symbol has the boolean value `false`.</span></span>

<span data-ttu-id="2064a-344">L’évaluation d’une expression de pré-traitement toujours génère une valeur booléenne.</span><span class="sxs-lookup"><span data-stu-id="2064a-344">Evaluation of a pre-processing expression always yields a boolean value.</span></span> <span data-ttu-id="2064a-345">Les règles d’évaluation pour une expression de prétraitement sont les mêmes que celles d’une expression constante ([expressions constantes](expressions.md#constant-expressions)), sauf que les entités définies par l’utilisateur uniquement peuvent être référencées sont des symboles de compilation conditionnelle .</span><span class="sxs-lookup"><span data-stu-id="2064a-345">The rules of evaluation for a pre-processing expression are the same as those for a constant expression ([Constant expressions](expressions.md#constant-expressions)), except that the only user-defined entities that can be referenced are conditional compilation symbols.</span></span>

### <a name="declaration-directives"></a><span data-ttu-id="2064a-346">Directives de déclaration</span><span class="sxs-lookup"><span data-stu-id="2064a-346">Declaration directives</span></span>

<span data-ttu-id="2064a-347">Les directives de déclaration sont utilisés pour définir ou annuler la définition de symboles de compilation conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="2064a-347">The declaration directives are used to define or undefine conditional compilation symbols.</span></span>

```antlr
pp_declaration
    : whitespace? '#' whitespace? 'define' whitespace conditional_symbol pp_new_line
    | whitespace? '#' whitespace? 'undef' whitespace conditional_symbol pp_new_line
    ;

pp_new_line
    : whitespace? single_line_comment? new_line
    ;
```

<span data-ttu-id="2064a-348">Le traitement d’un `#define` directive provoque le symbole de compilation conditionnelle sont définis, en commençant par la ligne source qui suit la directive.</span><span class="sxs-lookup"><span data-stu-id="2064a-348">The processing of a `#define` directive causes the given conditional compilation symbol to become defined, starting with the source line that follows the directive.</span></span> <span data-ttu-id="2064a-349">De même, le traitement d’un `#undef` directive provoque le symbole de compilation conditionnelle devienne indéfini, en commençant par la ligne source qui suit la directive.</span><span class="sxs-lookup"><span data-stu-id="2064a-349">Likewise, the processing of an `#undef` directive causes the given conditional compilation symbol to become undefined, starting with the source line that follows the directive.</span></span>

<span data-ttu-id="2064a-350">N’importe quel `#define` et `#undef` directives dans un fichier source doivent se produire avant la première *jeton* ([jetons](lexical-structure.md#tokens)) dans le fichier source ; sinon une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="2064a-350">Any `#define` and `#undef` directives in a source file must occur before the first *token* ([Tokens](lexical-structure.md#tokens)) in the source file; otherwise a compile-time error occurs.</span></span> <span data-ttu-id="2064a-351">En d’autres termes, `#define` et `#undef` directives doivent précéder les « code réel » dans le fichier source.</span><span class="sxs-lookup"><span data-stu-id="2064a-351">In intuitive terms, `#define` and `#undef` directives must precede any "real code" in the source file.</span></span>

<span data-ttu-id="2064a-352">L’exemple :</span><span class="sxs-lookup"><span data-stu-id="2064a-352">The example:</span></span>
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
<span data-ttu-id="2064a-353">est valide, car le `#define` directives précèdent le premier jeton (le `namespace` mot clé) dans le fichier source.</span><span class="sxs-lookup"><span data-stu-id="2064a-353">is valid because the `#define` directives precede the first token (the `namespace` keyword) in the source file.</span></span>

<span data-ttu-id="2064a-354">L’exemple suivant génère une erreur de compilation, car un `#define` suit le code réel :</span><span class="sxs-lookup"><span data-stu-id="2064a-354">The following example results in a compile-time error because a `#define` follows real code:</span></span>
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

<span data-ttu-id="2064a-355">Un `#define` peut définir un symbole de compilation conditionnelle est déjà défini, sans être n’importe quel intervenant `#undef` pour ce symbole.</span><span class="sxs-lookup"><span data-stu-id="2064a-355">A `#define` may define a conditional compilation symbol that is already defined, without there being any intervening `#undef` for that symbol.</span></span> <span data-ttu-id="2064a-356">L’exemple ci-dessous définit un symbole de compilation conditionnelle `A` et définit ensuite de nouveau.</span><span class="sxs-lookup"><span data-stu-id="2064a-356">The example below defines a conditional compilation symbol `A` and then defines it again.</span></span>
```csharp
#define A
#define A
```

<span data-ttu-id="2064a-357">Un `#undef` peut « annuler la définition « un symbole de compilation conditionnelle n’est pas défini.</span><span class="sxs-lookup"><span data-stu-id="2064a-357">A `#undef` may "undefine" a conditional compilation symbol that is not defined.</span></span> <span data-ttu-id="2064a-358">L’exemple ci-dessous définit un symbole de compilation conditionnelle `A` et puis annule la définition il à deux reprises ; bien que la seconde `#undef` n’a aucun effet, il est encore valide.</span><span class="sxs-lookup"><span data-stu-id="2064a-358">The example below defines a conditional compilation symbol `A` and then undefines it twice; although the second `#undef` has no effect, it is still valid.</span></span>
```csharp
#define A
#undef A
#undef A
```

### <a name="conditional-compilation-directives"></a><span data-ttu-id="2064a-359">Directives de compilation conditionnelle</span><span class="sxs-lookup"><span data-stu-id="2064a-359">Conditional compilation directives</span></span>

<span data-ttu-id="2064a-360">Les directives de compilation conditionnelle sont utilisés pour inclure ou exclure certaines parties d’un fichier source de manière conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="2064a-360">The conditional compilation directives are used to conditionally include or exclude portions of a source file.</span></span>

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

<span data-ttu-id="2064a-361">Comme indiqué par la syntaxe, les directives de compilation conditionnelle doivent être écrite sous forme de jeux comportant, dans l’ordre, un `#if` directive, zéro ou plusieurs `#elif` directives, zéro ou un `#else` directive et un `#endif` directive.</span><span class="sxs-lookup"><span data-stu-id="2064a-361">As indicated by the syntax, conditional compilation directives must be written as sets consisting of, in order, an `#if` directive, zero or more `#elif` directives, zero or one `#else` directive, and an `#endif` directive.</span></span> <span data-ttu-id="2064a-362">Entre les directives sont des sections conditionnelles du code source.</span><span class="sxs-lookup"><span data-stu-id="2064a-362">Between the directives are conditional sections of source code.</span></span> <span data-ttu-id="2064a-363">Chaque section est contrôlée par la directive immédiatement précédente.</span><span class="sxs-lookup"><span data-stu-id="2064a-363">Each section is controlled by the immediately preceding directive.</span></span> <span data-ttu-id="2064a-364">Une section conditionnelle peut elle-même contenir des directives de compilation conditionnelle imbriquées fourni ces directives forment des jeux complets.</span><span class="sxs-lookup"><span data-stu-id="2064a-364">A conditional section may itself contain nested conditional compilation directives provided these directives form complete sets.</span></span>

<span data-ttu-id="2064a-365">Un *pp_conditional* sélectionne au moins l’une de la relation contenant-contenu *conditional_section*s pour le traitement lexical normal :</span><span class="sxs-lookup"><span data-stu-id="2064a-365">A *pp_conditional* selects at most one of the contained *conditional_section*s for normal lexical processing:</span></span>

*  <span data-ttu-id="2064a-366">Le *pp_expression*s de la `#if` et `#elif` directives sont évaluées dans l’ordre jusqu'à ce qu’un produit `true`.</span><span class="sxs-lookup"><span data-stu-id="2064a-366">The *pp_expression*s of the `#if` and `#elif` directives are evaluated in order until one yields `true`.</span></span> <span data-ttu-id="2064a-367">Si une expression produit `true`, le *conditional_section* de la directive correspondante est sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="2064a-367">If an expression yields `true`, the *conditional_section* of the corresponding directive is selected.</span></span>
*  <span data-ttu-id="2064a-368">Si tous les *pp_expression*yield de s `false`et si un `#else` directive est présente, le *conditional_section* de la `#else` directive est sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="2064a-368">If all *pp_expression*s yield `false`, and if an `#else` directive is present, the *conditional_section* of the `#else` directive is selected.</span></span>
*  <span data-ttu-id="2064a-369">Sinon, non *conditional_section* est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="2064a-369">Otherwise, no *conditional_section* is selected.</span></span>

<span data-ttu-id="2064a-370">Le texte sélectionné *conditional_section*, le cas échéant, est traité comme un élément normal *input_section*: du code source contenu dans la section doit adhérer à la grammaire lexicale ; jetons sont générés à partir de la source code de la section ; et directives de prétraitement dans la section ont les effets prescrits.</span><span class="sxs-lookup"><span data-stu-id="2064a-370">The selected *conditional_section*, if any, is processed as a normal *input_section*: the source code contained in the section must adhere to the lexical grammar; tokens are generated from the source code in the section; and pre-processing directives in the section have the prescribed effects.</span></span>

<span data-ttu-id="2064a-371">Les autres *conditional_section*s, le cas échéant, sont traités en tant que *skipped_section*s: à l’exception des directives de prétraitement, le code source dans la section ne s’appliquent pas à la lexicale grammaire ; non les jetons sont générés à partir du code source dans la section ; et directives de prétraitement dans la section doivent être lexicalement corrects mais ne sont pas traités dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="2064a-371">The remaining *conditional_section*s, if any, are processed as *skipped_section*s: except for pre-processing directives, the source code in the section need not adhere to the lexical grammar; no tokens are generated from the source code in the section; and pre-processing directives in the section must be lexically correct but are not otherwise processed.</span></span> <span data-ttu-id="2064a-372">Au sein d’un *conditional_section* qui est en cours de traitement en tant qu’un *skipped_section*, toute imbriquée *conditional_section*s (contenues dans imbriquée `#if`... `#endif` et `#region`... `#endregion` construit) sont également traités en tant que *skipped_section*s.</span><span class="sxs-lookup"><span data-stu-id="2064a-372">Within a *conditional_section* that is being processed as a *skipped_section*, any nested *conditional_section*s (contained in nested `#if`...`#endif` and `#region`...`#endregion` constructs) are also processed as *skipped_section*s.</span></span>

<span data-ttu-id="2064a-373">L’exemple suivant illustre la compilation conditionnelle comment imbriquer des directives :</span><span class="sxs-lookup"><span data-stu-id="2064a-373">The following example illustrates how conditional compilation directives can nest:</span></span>
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

<span data-ttu-id="2064a-374">À l’exception des directives de préprocesseur, code source ignoré n’est pas soumis à une analyse lexicale.</span><span class="sxs-lookup"><span data-stu-id="2064a-374">Except for pre-processing directives, skipped source code is not subject to lexical analysis.</span></span> <span data-ttu-id="2064a-375">Par exemple, ce qui suit est valide en dépit du commentaire non terminé dans le `#else` section :</span><span class="sxs-lookup"><span data-stu-id="2064a-375">For example, the following is valid despite the unterminated comment in the `#else` section:</span></span>
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

<span data-ttu-id="2064a-376">Toutefois, notez que les directives de prétraitement sont requis pour être lexicalement correcte, même dans les sections ignorées du code source.</span><span class="sxs-lookup"><span data-stu-id="2064a-376">Note, however, that pre-processing directives are required to be lexically correct even in skipped sections of source code.</span></span>

<span data-ttu-id="2064a-377">Directives de prétraitement ne sont pas traités lorsqu’ils apparaissent à l’intérieur d’éléments d’entrée multilignes.</span><span class="sxs-lookup"><span data-stu-id="2064a-377">Pre-processing directives are not processed when they appear inside multi-line input elements.</span></span> <span data-ttu-id="2064a-378">Par exemple, le programme :</span><span class="sxs-lookup"><span data-stu-id="2064a-378">For example, the program:</span></span>
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
<span data-ttu-id="2064a-379">résultats dans la sortie :</span><span class="sxs-lookup"><span data-stu-id="2064a-379">results in the output:</span></span>
```
hello,
#if Debug
        world
#else
        Nebraska
#endif
```

<span data-ttu-id="2064a-380">Dans ces cas particuliers, l’ensemble des directives de prétraitement qui est traitée peut dépendre de l’évaluation de la *pp_expression*.</span><span class="sxs-lookup"><span data-stu-id="2064a-380">In peculiar cases, the set of pre-processing directives that is processed might depend on the evaluation of the *pp_expression*.</span></span> <span data-ttu-id="2064a-381">L’exemple :</span><span class="sxs-lookup"><span data-stu-id="2064a-381">The example:</span></span>
```csharp
#if X
    /*
#else
    /* */ class Q { }
#endif
```
<span data-ttu-id="2064a-382">produit toujours le même flux de jetons (`class` `Q` `{` `}`), quelle que soit ou non `X` est défini.</span><span class="sxs-lookup"><span data-stu-id="2064a-382">always produces the same token stream (`class` `Q` `{` `}`), regardless of whether or not `X` is defined.</span></span> <span data-ttu-id="2064a-383">Si `X` est défini, le sont les seules directives traitées `#if` et `#endif`en raison du commentaire multiligne.</span><span class="sxs-lookup"><span data-stu-id="2064a-383">If `X` is defined, the only processed directives are `#if` and `#endif`, due to the multi-line comment.</span></span> <span data-ttu-id="2064a-384">Si `X` est non défini, puis trois directives (`#if`, `#else`, `#endif`) font partie de l’ensemble de directive.</span><span class="sxs-lookup"><span data-stu-id="2064a-384">If `X` is undefined, then three directives (`#if`, `#else`, `#endif`) are part of the directive set.</span></span>

### <a name="diagnostic-directives"></a><span data-ttu-id="2064a-385">Directives de diagnostic</span><span class="sxs-lookup"><span data-stu-id="2064a-385">Diagnostic directives</span></span>

<span data-ttu-id="2064a-386">Les directives de diagnostics sont utilisées pour générer explicitement des messages d’erreur et avertissement qui sont signalées dans la même façon que d’autres erreurs de compilation et les avertissements.</span><span class="sxs-lookup"><span data-stu-id="2064a-386">The diagnostic directives are used to explicitly generate error and warning messages that are reported in the same way as other compile-time errors and warnings.</span></span>

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

<span data-ttu-id="2064a-387">L’exemple :</span><span class="sxs-lookup"><span data-stu-id="2064a-387">The example:</span></span>
```csharp
#warning Code review needed before check-in

#if Debug && Retail
    #error A build can't be both debug and retail
#endif

class Test {...}
```
<span data-ttu-id="2064a-388">toujours génère un avertissement (« révision du Code nécessaire avant l’archivage ») et génère une erreur de compilation (« une build ne peut pas être débogage et vente au détail ») si les symboles conditionnels `Debug` et `Retail` sont tous deux définis.</span><span class="sxs-lookup"><span data-stu-id="2064a-388">always produces a warning ("Code review needed before check-in"), and produces a compile-time error ("A build can't be both debug and retail") if the conditional symbols `Debug` and `Retail` are both defined.</span></span> <span data-ttu-id="2064a-389">Notez qu’un *pp_message* peuvent contenir du texte arbitraire ; plus précisément, elle pas besoin de contenir des jetons correct, comme indiqué par le guillemet simple dans le mot `can't`.</span><span class="sxs-lookup"><span data-stu-id="2064a-389">Note that a *pp_message* can contain arbitrary text; specifically, it need not contain well-formed tokens, as shown by the single quote in the word `can't`.</span></span>

### <a name="region-directives"></a><span data-ttu-id="2064a-390">Directives de région</span><span class="sxs-lookup"><span data-stu-id="2064a-390">Region directives</span></span>

<span data-ttu-id="2064a-391">Les directives de région sont utilisées pour marquer explicitement les régions du code source.</span><span class="sxs-lookup"><span data-stu-id="2064a-391">The region directives are used to explicitly mark regions of source code.</span></span>

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

<span data-ttu-id="2064a-392">Aucune signification sémantique n’est attachée à une région ; régions sont prévues pour une utilisation par le programmeur ou par des outils automatisés pour marquer une section de code source.</span><span class="sxs-lookup"><span data-stu-id="2064a-392">No semantic meaning is attached to a region; regions are intended for use by the programmer or by automated tools to mark a section of source code.</span></span> <span data-ttu-id="2064a-393">Le message spécifié dans un `#region` ou `#endregion` même directive n’a aucune signification sémantique ; il sert surtout à identifier la région.</span><span class="sxs-lookup"><span data-stu-id="2064a-393">The message specified in a `#region` or `#endregion` directive likewise has no semantic meaning; it merely serves to identify the region.</span></span> <span data-ttu-id="2064a-394">Mise en correspondance `#region` et `#endregion` directives peuvent avoir différents *pp_message*s.</span><span class="sxs-lookup"><span data-stu-id="2064a-394">Matching `#region` and `#endregion` directives may have different *pp_message*s.</span></span>

<span data-ttu-id="2064a-395">Le traitement lexical d’une région :</span><span class="sxs-lookup"><span data-stu-id="2064a-395">The lexical processing of a region:</span></span>
```csharp
#region
...
#endregion
```
<span data-ttu-id="2064a-396">correspond exactement au traitement lexical d’une directive de compilation conditionnelle du formulaire :</span><span class="sxs-lookup"><span data-stu-id="2064a-396">corresponds exactly to the lexical processing of a conditional compilation directive of the form:</span></span>
```csharp
#if true
...
#endif
```

### <a name="line-directives"></a><span data-ttu-id="2064a-397">Directives de ligne</span><span class="sxs-lookup"><span data-stu-id="2064a-397">Line directives</span></span>

<span data-ttu-id="2064a-398">Directives de ligne peuvent être utilisés pour modifier les numéros de ligne et les noms de fichiers sources qui sont signalés par le compilateur de sortie tels que les erreurs et avertissements, et qui sont utilisés par les attributs des informations de l’appelant ([attributs d’informations appelant](attributes.md#caller-info-attributes)).</span><span class="sxs-lookup"><span data-stu-id="2064a-398">Line directives may be used to alter the line numbers and source file names that are reported by the compiler in output such as warnings and errors, and that are used by caller info attributes ([Caller info attributes](attributes.md#caller-info-attributes)).</span></span>

<span data-ttu-id="2064a-399">Directives de ligne sont couramment utilisés dans les outils de métaprogrammation qui génèrent le code source c# à partir d’un autre texte d’entrée.</span><span class="sxs-lookup"><span data-stu-id="2064a-399">Line directives are most commonly used in meta-programming tools that generate C# source code from some other text input.</span></span>

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

<span data-ttu-id="2064a-400">En cas de non `#line` directives sont présents, le compilateur signale les numéros de ligne true et noms de fichiers sources dans sa sortie.</span><span class="sxs-lookup"><span data-stu-id="2064a-400">When no `#line` directives are present, the compiler reports true line numbers and source file names in its output.</span></span> <span data-ttu-id="2064a-401">Lors du traitement d’un `#line` directive inclut un *line_indicator* qui n’est pas `default`, le compilateur traite la ligne après la directive comme ayant le numéro de ligne donné (et le nom de fichier, si spécifié).</span><span class="sxs-lookup"><span data-stu-id="2064a-401">When processing a `#line` directive that includes a *line_indicator* that is not `default`, the compiler treats the line after the directive as having the given line number (and file name, if specified).</span></span>

<span data-ttu-id="2064a-402">Un `#line default` directive inverse l’effet de toutes les directives #line précédentes.</span><span class="sxs-lookup"><span data-stu-id="2064a-402">A `#line default` directive reverses the effect of all preceding #line directives.</span></span> <span data-ttu-id="2064a-403">Le compilateur signale les informations de ligne true pour les lignes suivantes, exactement comme si aucune `#line` directives avaient été traités.</span><span class="sxs-lookup"><span data-stu-id="2064a-403">The compiler reports true line information for subsequent lines, precisely as if no `#line` directives had been processed.</span></span>

<span data-ttu-id="2064a-404">Un `#line hidden` directive n’a aucun effet sur le fichier et les numéros de ligne indiqué dans l’erreur des messages, mais affectent le débogage au niveau du code source.</span><span class="sxs-lookup"><span data-stu-id="2064a-404">A `#line hidden` directive has no effect on the file and line numbers reported in error messages, but does affect source level debugging.</span></span> <span data-ttu-id="2064a-405">Lors du débogage, toutes les lignes entre un `#line hidden` directive et le `#line` directive (qui n’est pas `#line hidden`) ne possèdent aucune information de numéro de ligne.</span><span class="sxs-lookup"><span data-stu-id="2064a-405">When debugging, all lines between a `#line hidden` directive and the subsequent `#line` directive (that is not `#line hidden`) have no line number information.</span></span> <span data-ttu-id="2064a-406">Lorsque vous parcourez le code dans le débogueur, ces lignes seront ignorées entièrement.</span><span class="sxs-lookup"><span data-stu-id="2064a-406">When stepping through code in the debugger, these lines will be skipped entirely.</span></span>

<span data-ttu-id="2064a-407">Notez qu’un *file_name* diffère de littéral de chaîne standard caractères d’échappement ne sont pas traités ; le «`\`» caractère désigne simplement un caractère de barre oblique inverse ordinaire dans un *file_name*.</span><span class="sxs-lookup"><span data-stu-id="2064a-407">Note that a *file_name* differs from a regular string literal in that escape characters are not processed; the "`\`" character simply designates an ordinary backslash character within a *file_name*.</span></span>

### <a name="pragma-directives"></a><span data-ttu-id="2064a-408">Directives pragma</span><span class="sxs-lookup"><span data-stu-id="2064a-408">Pragma directives</span></span>

<span data-ttu-id="2064a-409">Le `#pragma` directive de prétraitement est utilisée pour spécifier des informations contextuelles supplémentaires pour le compilateur.</span><span class="sxs-lookup"><span data-stu-id="2064a-409">The `#pragma` preprocessing directive is used to specify optional contextual information to the compiler.</span></span> <span data-ttu-id="2064a-410">Les informations fournies dans un `#pragma` directive ne changera jamais sémantique du programme.</span><span class="sxs-lookup"><span data-stu-id="2064a-410">The information supplied in a `#pragma` directive will never change program semantics.</span></span>

```antlr
pp_pragma
    : whitespace? '#' whitespace? 'pragma' whitespace pragma_body pp_new_line
    ;

pragma_body
    : pragma_warning_body
    ;
```

<span data-ttu-id="2064a-411">C# fournit `#pragma` directives pour contrôler les avertissements du compilateur.</span><span class="sxs-lookup"><span data-stu-id="2064a-411">C# provides `#pragma` directives to control compiler warnings.</span></span> <span data-ttu-id="2064a-412">Les versions futures du langage peuvent inclure supplémentaires `#pragma` directives.</span><span class="sxs-lookup"><span data-stu-id="2064a-412">Future versions of the language may include additional `#pragma` directives.</span></span> <span data-ttu-id="2064a-413">Pour garantir l’interopérabilité avec d’autres compilateurs c#, le compilateur Microsoft C# n’émet pas d’erreurs de compilation pour inconnu `#pragma` directives ; de ce type ne de directives toutefois générer des avertissements.</span><span class="sxs-lookup"><span data-stu-id="2064a-413">To ensure interoperability with other C# compilers, the Microsoft C# compiler does not issue compilation errors for unknown `#pragma` directives; such directives do however generate warnings.</span></span>

#### <a name="pragma-warning"></a><span data-ttu-id="2064a-414">Avertissement de pragma</span><span class="sxs-lookup"><span data-stu-id="2064a-414">Pragma warning</span></span>

<span data-ttu-id="2064a-415">Le `#pragma warning` directive est utilisée pour désactiver ou de restaurer toutes ou un ensemble particulier d’avertissement messages pendant la compilation du texte de programme ultérieures.</span><span class="sxs-lookup"><span data-stu-id="2064a-415">The `#pragma warning` directive is used to disable or restore all or a particular set of warning messages during compilation of the subsequent program text.</span></span>

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

<span data-ttu-id="2064a-416">Un `#pragma warning` directive qui omet la liste d’avertissements affecte tous les avertissements.</span><span class="sxs-lookup"><span data-stu-id="2064a-416">A `#pragma warning` directive that omits the warning list affects all warnings.</span></span> <span data-ttu-id="2064a-417">Un `#pragma warning` directive l’inclut une liste d’avertissements affecte uniquement les avertissements qui sont spécifiés dans la liste.</span><span class="sxs-lookup"><span data-stu-id="2064a-417">A `#pragma warning` directive the includes a warning list affects only those warnings that are specified in the list.</span></span>

<span data-ttu-id="2064a-418">Un `#pragma warning disable` directive désactive tous les ou l’ensemble donné d’avertissements.</span><span class="sxs-lookup"><span data-stu-id="2064a-418">A `#pragma warning disable` directive disables all or the given set of warnings.</span></span>

<span data-ttu-id="2064a-419">Un `#pragma warning restore` directive restaure tous les ou l’ensemble donné d’avertissements à l’état qui était en vigueur au début de l’unité de compilation.</span><span class="sxs-lookup"><span data-stu-id="2064a-419">A `#pragma warning restore` directive restores all or the given set of warnings to the state that was in effect at the beginning of the compilation unit.</span></span> <span data-ttu-id="2064a-420">Notez que si un avertissement particulier a été désactivé en externe, un `#pragma warning restore` (s’il pour tous ou l’avertissement spécifique) n’est pas réactiver cet avertissement.</span><span class="sxs-lookup"><span data-stu-id="2064a-420">Note that if a particular warning was disabled externally, a `#pragma warning restore` (whether for all or the specific warning) will not re-enable that warning.</span></span>

<span data-ttu-id="2064a-421">L’exemple suivant illustre l’utilisation de `#pragma warning` pour désactiver temporairement l’avertissement a signalé lorsque obsolète membres sont référencés, à l’aide du numéro d’avertissement du compilateur Microsoft C#.</span><span class="sxs-lookup"><span data-stu-id="2064a-421">The following example shows use of `#pragma warning` to temporarily disable the warning reported when obsoleted members are referenced, using the warning number from the Microsoft C# compiler.</span></span>
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
