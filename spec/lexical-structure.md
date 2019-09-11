---
ms.openlocfilehash: 5fbe0267b5b33b1a24dbdca493d118c576092573
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876910"
---
# <a name="lexical-structure"></a><span data-ttu-id="b0969-101">Structure lexicale</span><span class="sxs-lookup"><span data-stu-id="b0969-101">Lexical structure</span></span>

## <a name="programs"></a><span data-ttu-id="b0969-102">Programs</span><span class="sxs-lookup"><span data-stu-id="b0969-102">Programs</span></span>

<span data-ttu-id="b0969-103">Un C# ***programme*** se compose d’un ou de plusieurs ***fichiers sources***, connus sous le nom d' ***unités de compilation*** ([unités de compilation](namespaces.md#compilation-units)).</span><span class="sxs-lookup"><span data-stu-id="b0969-103">A C# ***program*** consists of one or more ***source files***, known formally as ***compilation units*** ([Compilation units](namespaces.md#compilation-units)).</span></span> <span data-ttu-id="b0969-104">Un fichier source est une séquence ordonnée de caractères Unicode.</span><span class="sxs-lookup"><span data-stu-id="b0969-104">A source file is an ordered sequence of Unicode characters.</span></span> <span data-ttu-id="b0969-105">Les fichiers sources ont généralement une correspondance un-à-un avec les fichiers dans un système de fichiers, mais cette correspondance n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b0969-105">Source files typically have a one-to-one correspondence with files in a file system, but this correspondence is not required.</span></span> <span data-ttu-id="b0969-106">Pour une portabilité maximale, il est recommandé d’encoder les fichiers d’un système de fichiers avec l’encodage UTF-8.</span><span class="sxs-lookup"><span data-stu-id="b0969-106">For maximal portability, it is recommended that files in a file system be encoded with the UTF-8 encoding.</span></span>

<span data-ttu-id="b0969-107">Conceptuellement, un programme est compilé à l’aide de trois étapes :</span><span class="sxs-lookup"><span data-stu-id="b0969-107">Conceptually speaking, a program is compiled using three steps:</span></span>

1. <span data-ttu-id="b0969-108">Transformation, qui convertit un fichier d’un répertoire de caractères particulier et d’un schéma d’encodage en une séquence de caractères Unicode.</span><span class="sxs-lookup"><span data-stu-id="b0969-108">Transformation, which converts a file from a particular character repertoire and encoding scheme into a sequence of Unicode characters.</span></span>
2. <span data-ttu-id="b0969-109">Analyse lexicale, qui convertit un flux de caractères d’entrée Unicode en un flux de jetons.</span><span class="sxs-lookup"><span data-stu-id="b0969-109">Lexical analysis, which translates a stream of Unicode input characters into a stream of tokens.</span></span>
3. <span data-ttu-id="b0969-110">Analyse syntaxique, qui convertit le flux de jetons en code exécutable.</span><span class="sxs-lookup"><span data-stu-id="b0969-110">Syntactic analysis, which translates the stream of tokens into executable code.</span></span>

## <a name="grammars"></a><span data-ttu-id="b0969-111">Grammaires</span><span class="sxs-lookup"><span data-stu-id="b0969-111">Grammars</span></span>

<span data-ttu-id="b0969-112">Cette spécification présente la syntaxe du langage C# de programmation à l’aide de deux grammaires.</span><span class="sxs-lookup"><span data-stu-id="b0969-112">This specification presents the syntax of the C# programming language using two grammars.</span></span> <span data-ttu-id="b0969-113">La ***grammaire lexicale*** ([grammaire lexicale](lexical-structure.md#lexical-grammar)) définit la combinaison des caractères Unicode avec les indicateurs de fin de ligne, les espaces blancs, les commentaires, les jetons et les directives de pré-traitement.</span><span class="sxs-lookup"><span data-stu-id="b0969-113">The ***lexical grammar*** ([Lexical grammar](lexical-structure.md#lexical-grammar)) defines how Unicode characters are combined to form line terminators, white space, comments, tokens, and pre-processing directives.</span></span> <span data-ttu-id="b0969-114">La ***grammaire syntaxique*** ([syntaxe syntaxique](lexical-structure.md#syntactic-grammar)) définit la façon dont les jetons résultant de la grammaire lexicale sont combinés C# pour former des programmes.</span><span class="sxs-lookup"><span data-stu-id="b0969-114">The ***syntactic grammar*** ([Syntactic grammar](lexical-structure.md#syntactic-grammar)) defines how the tokens resulting from the lexical grammar are combined to form C# programs.</span></span>

### <a name="grammar-notation"></a><span data-ttu-id="b0969-115">Notation grammaticale</span><span class="sxs-lookup"><span data-stu-id="b0969-115">Grammar notation</span></span>

<span data-ttu-id="b0969-116">Les grammaires lexicales et syntaxiques sont présentées dans la forme Backus-Naur à l’aide de la notation de l’outil de grammaire ANTLR.</span><span class="sxs-lookup"><span data-stu-id="b0969-116">The lexical and syntactic grammars are presented in Backus-Naur form using the notation of the ANTLR grammar tool.</span></span>

### <a name="lexical-grammar"></a><span data-ttu-id="b0969-117">Grammaire lexicale</span><span class="sxs-lookup"><span data-stu-id="b0969-117">Lexical grammar</span></span>

<span data-ttu-id="b0969-118">La grammaire lexicale C# de est présentée dans l' [analyse lexicale](lexical-structure.md#lexical-analysis), les [jetons](lexical-structure.md#tokens)et les [directives de pré-traitement](lexical-structure.md#pre-processing-directives).</span><span class="sxs-lookup"><span data-stu-id="b0969-118">The lexical grammar of C# is presented in [Lexical analysis](lexical-structure.md#lexical-analysis), [Tokens](lexical-structure.md#tokens), and [Pre-processing directives](lexical-structure.md#pre-processing-directives).</span></span> <span data-ttu-id="b0969-119">Les symboles terminaux de la grammaire lexicale sont les caractères du jeu de caractères Unicode, tandis que la grammaire lexicale spécifie la manière dont les caractères sont combinés pour former des jetons ([jetons](lexical-structure.md#tokens)), un espace blanc ([espace blanc](lexical-structure.md#white-space)), des commentaires ([Commentaires](lexical-structure.md#comments)) et directives de prétraitement ([directives de pré-traitement](lexical-structure.md#pre-processing-directives)).</span><span class="sxs-lookup"><span data-stu-id="b0969-119">The terminal symbols of the lexical grammar are the characters of the Unicode character set, and the lexical grammar specifies how characters are combined to form tokens ([Tokens](lexical-structure.md#tokens)), white space ([White space](lexical-structure.md#white-space)), comments ([Comments](lexical-structure.md#comments)), and pre-processing directives ([Pre-processing directives](lexical-structure.md#pre-processing-directives)).</span></span>

<span data-ttu-id="b0969-120">Chaque fichier source d’un C# programme doit être conforme à la production *d’entrée* de la grammaire lexicale ([analyse lexicale](lexical-structure.md#lexical-analysis)).</span><span class="sxs-lookup"><span data-stu-id="b0969-120">Every source file in a C# program must conform to the *input* production of the lexical grammar ([Lexical analysis](lexical-structure.md#lexical-analysis)).</span></span>

### <a name="syntactic-grammar"></a><span data-ttu-id="b0969-121">Syntaxe syntaxique</span><span class="sxs-lookup"><span data-stu-id="b0969-121">Syntactic grammar</span></span>

<span data-ttu-id="b0969-122">La grammaire syntaxique de C# est présentée dans les chapitres et les annexes qui suivent ce chapitre.</span><span class="sxs-lookup"><span data-stu-id="b0969-122">The syntactic grammar of C# is presented in the chapters and appendices that follow this chapter.</span></span> <span data-ttu-id="b0969-123">Les symboles terminaux de la grammaire syntaxique sont les jetons définis par la grammaire lexicale, et la grammaire syntaxique spécifie la manière dont les jetons sont C# combinés pour former des programmes.</span><span class="sxs-lookup"><span data-stu-id="b0969-123">The terminal symbols of the syntactic grammar are the tokens defined by the lexical grammar, and the syntactic grammar specifies how tokens are combined to form C# programs.</span></span>

<span data-ttu-id="b0969-124">Chaque fichier source d’un C# programme doit se conformer à la production *compilation_unit* de la grammaire syntaxique ([unités de compilation](namespaces.md#compilation-units)).</span><span class="sxs-lookup"><span data-stu-id="b0969-124">Every source file in a C# program must conform to the *compilation_unit* production of the syntactic grammar ([Compilation units](namespaces.md#compilation-units)).</span></span>

## <a name="lexical-analysis"></a><span data-ttu-id="b0969-125">Analyse lexicale</span><span class="sxs-lookup"><span data-stu-id="b0969-125">Lexical analysis</span></span>

<span data-ttu-id="b0969-126">La production *d’entrée* définit la structure lexicale C# d’un fichier source.</span><span class="sxs-lookup"><span data-stu-id="b0969-126">The *input* production defines the lexical structure of a C# source file.</span></span> <span data-ttu-id="b0969-127">Chaque fichier source d’un C# programme doit se conformer à cette production grammaticale lexicale.</span><span class="sxs-lookup"><span data-stu-id="b0969-127">Each source file in a C# program must conform to this lexical grammar production.</span></span>

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

<span data-ttu-id="b0969-128">Cinq éléments de base composent la structure lexicale d’un C# fichier source : Marques de fin de ligne ([indicateurs de fin de ligne](lexical-structure.md#line-terminators)), espace blanc ([espace blanc](lexical-structure.md#white-space)), commentaires ([Commentaires](lexical-structure.md#comments)), jetons ([jetons](lexical-structure.md#tokens)) et directives de prétraitement ([directives de pré-traitement](lexical-structure.md#pre-processing-directives)).</span><span class="sxs-lookup"><span data-stu-id="b0969-128">Five basic elements make up the lexical structure of a C# source file: Line terminators ([Line terminators](lexical-structure.md#line-terminators)), white space ([White space](lexical-structure.md#white-space)), comments ([Comments](lexical-structure.md#comments)), tokens ([Tokens](lexical-structure.md#tokens)), and pre-processing directives ([Pre-processing directives](lexical-structure.md#pre-processing-directives)).</span></span> <span data-ttu-id="b0969-129">Parmi ces éléments de base, seuls les jetons sont significatifs dans la grammaire syntaxique C# d’un programme (syntaxe[syntaxique](lexical-structure.md#syntactic-grammar)).</span><span class="sxs-lookup"><span data-stu-id="b0969-129">Of these basic elements, only tokens are significant in the syntactic grammar of a C# program ([Syntactic grammar](lexical-structure.md#syntactic-grammar)).</span></span>

<span data-ttu-id="b0969-130">Le traitement lexical d’un C# fichier source consiste à réduire le fichier en une séquence de jetons qui devient l’entrée de l’analyse syntaxique.</span><span class="sxs-lookup"><span data-stu-id="b0969-130">The lexical processing of a C# source file consists of reducing the file into a sequence of tokens which becomes the input to the syntactic analysis.</span></span> <span data-ttu-id="b0969-131">Les indicateurs de fin de ligne, les espaces blancs et les commentaires peuvent servir à séparer les jetons, et les directives de prétraitement peuvent entraîner l’omission des sections du fichier source, mais ces éléments lexicaux n’ont aucun impact sur la structure C# syntaxique d’un programme.</span><span class="sxs-lookup"><span data-stu-id="b0969-131">Line terminators, white space, and comments can serve to separate tokens, and pre-processing directives can cause sections of the source file to be skipped, but otherwise these lexical elements have no impact on the syntactic structure of a C# program.</span></span>

<span data-ttu-id="b0969-132">Dans le cas de littéraux de chaîne interpolés ([littéraux de chaîne interpolés](lexical-structure.md#interpolated-string-literals)), un seul jeton est initialement généré par l’analyse lexicale, mais il est divisé en plusieurs éléments d’entrée qui sont soumis à l’analyse lexicale de manière répétée jusqu’à ce que toutes les interpolations les littéraux de chaîne ont été résolus.</span><span class="sxs-lookup"><span data-stu-id="b0969-132">In the case of interpolated string literals ([Interpolated string literals](lexical-structure.md#interpolated-string-literals)) a single token is initially produced by lexical analysis, but is broken up into several input elements which are repeatedly subjected to lexical analysis until all interpolated string literals have been resolved.</span></span> <span data-ttu-id="b0969-133">Les jetons résultants servent ensuite d’entrée à l’analyse syntaxique.</span><span class="sxs-lookup"><span data-stu-id="b0969-133">The resulting tokens then serve as input to the syntactic analysis.</span></span>

<span data-ttu-id="b0969-134">Lorsque plusieurs productions grammaticales lexicales correspondent à une séquence de caractères dans un fichier source, le traitement lexical forme toujours l’élément lexical le plus long possible.</span><span class="sxs-lookup"><span data-stu-id="b0969-134">When several lexical grammar productions match a sequence of characters in a source file, the lexical processing always forms the longest possible lexical element.</span></span> <span data-ttu-id="b0969-135">Par exemple, la séquence `//` de caractères est traitée comme le début d’un commentaire sur une seule ligne, car cet élément lexical est plus long qu’un seul `/` jeton.</span><span class="sxs-lookup"><span data-stu-id="b0969-135">For example, the character sequence `//` is processed as the beginning of a single-line comment because that lexical element is longer than a single `/` token.</span></span>

### <a name="line-terminators"></a><span data-ttu-id="b0969-136">Marques de fin de ligne</span><span class="sxs-lookup"><span data-stu-id="b0969-136">Line terminators</span></span>

<span data-ttu-id="b0969-137">Les marques de fin de ligne divisent les caractères d’un C# fichier source en lignes.</span><span class="sxs-lookup"><span data-stu-id="b0969-137">Line terminators divide the characters of a C# source file into lines.</span></span>

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

<span data-ttu-id="b0969-138">Pour la compatibilité avec les outils d’édition de code source qui ajoutent des marqueurs de fin de fichier et pour permettre à un fichier source d’être affiché sous la forme d’une séquence de lignes correctement terminées, les transformations suivantes sont appliquées, C# dans l’ordre, à chaque fichier source d’un programme :</span><span class="sxs-lookup"><span data-stu-id="b0969-138">For compatibility with source code editing tools that add end-of-file markers, and to enable a source file to be viewed as a sequence of properly terminated lines, the following transformations are applied, in order, to every source file in a C# program:</span></span>

*  <span data-ttu-id="b0969-139">Si le dernier caractère du fichier source est un caractère de contrôle Z (`U+001A`), ce caractère est supprimé.</span><span class="sxs-lookup"><span data-stu-id="b0969-139">If the last character of the source file is a Control-Z character (`U+001A`), this character is deleted.</span></span>
*  <span data-ttu-id="b0969-140">Un caractère de retour chariot (`U+000D`) est ajouté à la fin du fichier source si ce fichier source n’est pas vide et si le dernier caractère du fichier source n’est pas un retour chariot (`U+000D`), un saut de ligne (`U+000A`), un séparateur de ligne (`U+2028`) ou un séparateur de paragraphe (`U+2029`).</span><span class="sxs-lookup"><span data-stu-id="b0969-140">A carriage-return character (`U+000D`) is added to the end of the source file if that source file is non-empty and if the last character of the source file is not a carriage return (`U+000D`), a line feed (`U+000A`), a line separator (`U+2028`), or a paragraph separator (`U+2029`).</span></span>

### <a name="comments"></a><span data-ttu-id="b0969-141">Commentaires</span><span class="sxs-lookup"><span data-stu-id="b0969-141">Comments</span></span>

<span data-ttu-id="b0969-142">Deux formes de commentaires sont pris en charge : commentaires sur une seule ligne et commentaires délimités.</span><span class="sxs-lookup"><span data-stu-id="b0969-142">Two forms of comments are supported: single-line comments and delimited comments.</span></span> <span data-ttu-id="b0969-143">Les ***Commentaires sur une seule ligne*** commencent par `//` les caractères et s’étendent jusqu’à la fin de la ligne source.</span><span class="sxs-lookup"><span data-stu-id="b0969-143">***Single-line comments*** start with the characters `//` and extend to the end of the source line.</span></span> <span data-ttu-id="b0969-144">Les ***Commentaires délimités*** commencent par `/*` les caractères et se terminent par les caractères. `*/`</span><span class="sxs-lookup"><span data-stu-id="b0969-144">***Delimited comments*** start with the characters `/*` and end with the characters `*/`.</span></span> <span data-ttu-id="b0969-145">Les commentaires délimités peuvent s’étendre sur plusieurs lignes.</span><span class="sxs-lookup"><span data-stu-id="b0969-145">Delimited comments may span multiple lines.</span></span>

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

<span data-ttu-id="b0969-146">Les commentaires ne sont pas imbriqués.</span><span class="sxs-lookup"><span data-stu-id="b0969-146">Comments do not nest.</span></span> <span data-ttu-id="b0969-147">`/*` Les séquences de caractères `*/` et n’ont aucune signification particulière `//` dans un commentaire, et les séquences `/*` `//` de caractères et n’ont aucune signification particulière dans un commentaire délimité.</span><span class="sxs-lookup"><span data-stu-id="b0969-147">The character sequences `/*` and `*/` have no special meaning within a `//` comment, and the character sequences `//` and `/*` have no special meaning within a delimited comment.</span></span>

<span data-ttu-id="b0969-148">Les commentaires ne sont pas traités dans des littéraux de chaîne et de caractère.</span><span class="sxs-lookup"><span data-stu-id="b0969-148">Comments are not processed within character and string literals.</span></span>

<span data-ttu-id="b0969-149">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0969-149">The example</span></span>
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
<span data-ttu-id="b0969-150">comprend un commentaire délimité.</span><span class="sxs-lookup"><span data-stu-id="b0969-150">includes a delimited comment.</span></span>

<span data-ttu-id="b0969-151">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0969-151">The example</span></span>
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
<span data-ttu-id="b0969-152">affiche plusieurs commentaires sur une seule ligne.</span><span class="sxs-lookup"><span data-stu-id="b0969-152">shows several single-line comments.</span></span>

### <a name="white-space"></a><span data-ttu-id="b0969-153">Espace blanc</span><span class="sxs-lookup"><span data-stu-id="b0969-153">White space</span></span>

<span data-ttu-id="b0969-154">Un espace blanc est défini comme n’importe quel caractère avec la classe Unicode Zs (qui comprend le caractère espace), ainsi que le caractère de tabulation horizontale, le caractère de tabulation verticale et le caractère de saut de forme.</span><span class="sxs-lookup"><span data-stu-id="b0969-154">White space is defined as any character with Unicode class Zs (which includes the space character) as well as the horizontal tab character, the vertical tab character, and the form feed character.</span></span>

```antlr
whitespace
    : '<Any character with Unicode class Zs>'
    | '<Horizontal tab character (U+0009)>'
    | '<Vertical tab character (U+000B)>'
    | '<Form feed character (U+000C)>'
    ;
```

## <a name="tokens"></a><span data-ttu-id="b0969-155">jetons</span><span class="sxs-lookup"><span data-stu-id="b0969-155">Tokens</span></span>

<span data-ttu-id="b0969-156">Il existe plusieurs genres de jetons : les identificateurs, les mots clés, les littéraux, les opérateurs et les signes de ponctuation.</span><span class="sxs-lookup"><span data-stu-id="b0969-156">There are several kinds of tokens: identifiers, keywords, literals, operators, and punctuators.</span></span> <span data-ttu-id="b0969-157">Les espaces blancs et les commentaires ne sont pas des jetons, bien qu’ils agissent comme séparateurs pour les jetons.</span><span class="sxs-lookup"><span data-stu-id="b0969-157">White space and comments are not tokens, though they act as separators for tokens.</span></span>

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

### <a name="unicode-character-escape-sequences"></a><span data-ttu-id="b0969-158">Séquences d’échappement de caractères Unicode</span><span class="sxs-lookup"><span data-stu-id="b0969-158">Unicode character escape sequences</span></span>

<span data-ttu-id="b0969-159">Une séquence d’échappement de caractère Unicode représente un caractère Unicode.</span><span class="sxs-lookup"><span data-stu-id="b0969-159">A Unicode character escape sequence represents a Unicode character.</span></span> <span data-ttu-id="b0969-160">Les séquences d’échappement de caractères Unicode sont traitées dans les identificateurs ([identificateurs](lexical-structure.md#identifiers)), les littéraux de caractère ([littéraux de caractère](lexical-structure.md#character-literals)) et les littéraux de chaîne normaux ([littéraux de chaîne](lexical-structure.md#string-literals)).</span><span class="sxs-lookup"><span data-stu-id="b0969-160">Unicode character escape sequences are processed in identifiers ([Identifiers](lexical-structure.md#identifiers)), character literals ([Character literals](lexical-structure.md#character-literals)), and regular string literals ([String literals](lexical-structure.md#string-literals)).</span></span> <span data-ttu-id="b0969-161">Un caractère d’échappement Unicode n’est pas traité à un autre emplacement (par exemple, pour former un opérateur, un signe de ponctuation ou un mot clé).</span><span class="sxs-lookup"><span data-stu-id="b0969-161">A Unicode character escape is not processed in any other location (for example, to form an operator, punctuator, or keyword).</span></span>

```antlr
unicode_escape_sequence
    : '\\u' hex_digit hex_digit hex_digit hex_digit
    | '\\U' hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit
    ;
```

<span data-ttu-id="b0969-162">Une séquence d’échappement Unicode représente le caractère Unicode unique formé par le nombre hexadécimal suivant les`\u`caractères « »`\U`ou « ».</span><span class="sxs-lookup"><span data-stu-id="b0969-162">A Unicode escape sequence represents the single Unicode character formed by the hexadecimal number following the "`\u`" or "`\U`" characters.</span></span> <span data-ttu-id="b0969-163">Étant C# donné que utilise un encodage 16 bits de points de code Unicode en caractères et des valeurs de chaîne, un caractère Unicode dans la plage comprise entre u + 10000 et u + 10FFFF n’est pas autorisé dans un littéral de caractère et est représenté à l’aide d’une paire de substitution Unicode dans un littéral de chaîne.</span><span class="sxs-lookup"><span data-stu-id="b0969-163">Since C# uses a 16-bit encoding of Unicode code points in characters and string values, a Unicode character in the range U+10000 to U+10FFFF is not permitted in a character literal and is represented using a Unicode surrogate pair in a string literal.</span></span> <span data-ttu-id="b0969-164">Les caractères Unicode avec des points de code au-dessus de 0x10FFFF ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="b0969-164">Unicode characters with code points above 0x10FFFF are not supported.</span></span>

<span data-ttu-id="b0969-165">Plusieurs traductions ne sont pas effectuées.</span><span class="sxs-lookup"><span data-stu-id="b0969-165">Multiple translations are not performed.</span></span> <span data-ttu-id="b0969-166">Par exemple, le littéral de chaîne`\u005Cu005C`« » est équivalent à`\u005C`« » plutôt qu'`\`à «».</span><span class="sxs-lookup"><span data-stu-id="b0969-166">For instance, the string literal "`\u005Cu005C`" is equivalent to "`\u005C`" rather than "`\`".</span></span> <span data-ttu-id="b0969-167">La valeur `\u005C` Unicode est le caractère «`\`».</span><span class="sxs-lookup"><span data-stu-id="b0969-167">The Unicode value `\u005C` is the character "`\`".</span></span>

<span data-ttu-id="b0969-168">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0969-168">The example</span></span>
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
<span data-ttu-id="b0969-169">montre plusieurs utilisations de `\u0066`, qui est la séquence d’échappement pour la lettre`f`«».</span><span class="sxs-lookup"><span data-stu-id="b0969-169">shows several uses of `\u0066`, which is the escape sequence for the letter "`f`".</span></span> <span data-ttu-id="b0969-170">Le programme est équivalent à</span><span class="sxs-lookup"><span data-stu-id="b0969-170">The program is equivalent to</span></span>
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

### <a name="identifiers"></a><span data-ttu-id="b0969-171">Identificateurs</span><span class="sxs-lookup"><span data-stu-id="b0969-171">Identifiers</span></span>

<span data-ttu-id="b0969-172">Les règles relatives aux identificateurs fournis dans cette section correspondent exactement à celles recommandées par la norme Unicode annexe 31, sauf que le trait de soulignement est autorisé comme caractère initial (comme c’est le cas dans le langage de programmation C), les séquences d’échappement Unicode sont autorisé dans les identificateurs, et le`@`caractère «» est autorisé en tant que préfixe pour permettre l’utilisation de mots clés comme identificateurs.</span><span class="sxs-lookup"><span data-stu-id="b0969-172">The rules for identifiers given in this section correspond exactly to those recommended by the Unicode Standard Annex 31, except that underscore is allowed as an initial character (as is traditional in the C programming language), Unicode escape sequences are permitted in identifiers, and the "`@`" character is allowed as a prefix to enable keywords to be used as identifiers.</span></span>

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

<span data-ttu-id="b0969-173">Pour plus d’informations sur les classes de caractères Unicode mentionnées ci-dessus, consultez la norme Unicode, version 3,0, section 4,5.</span><span class="sxs-lookup"><span data-stu-id="b0969-173">For information on the Unicode character classes mentioned above, see The Unicode Standard, Version 3.0, section 4.5.</span></span>

<span data-ttu-id="b0969-174">«`identifier1`», «`_identifier2`» Et «`@if`» sont des exemples d’identificateurs valides.</span><span class="sxs-lookup"><span data-stu-id="b0969-174">Examples of valid identifiers include "`identifier1`", "`_identifier2`", and "`@if`".</span></span>

<span data-ttu-id="b0969-175">Un identificateur dans un programme conforme doit être au format canonique défini par le formulaire de normalisation Unicode C, comme défini par la norme Unicode annexe 15.</span><span class="sxs-lookup"><span data-stu-id="b0969-175">An identifier in a conforming program must be in the canonical format defined by Unicode Normalization Form C, as defined by Unicode Standard Annex 15.</span></span> <span data-ttu-id="b0969-176">Le comportement lors de la rencontre d’un identificateur qui ne figure pas dans le formulaire de normalisation C est défini par l’implémentation ; Toutefois, aucun diagnostic n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b0969-176">The behavior when encountering an identifier not in Normalization Form C is implementation-defined; however, a diagnostic is not required.</span></span>

<span data-ttu-id="b0969-177">Le préfixe`@`«» permet d’utiliser des mots clés en tant qu’identificateurs, ce qui est utile lors de l’interfaçage avec d’autres langages de programmation.</span><span class="sxs-lookup"><span data-stu-id="b0969-177">The prefix "`@`" enables the use of keywords as identifiers, which is useful when interfacing with other programming languages.</span></span> <span data-ttu-id="b0969-178">Le caractère `@` ne faisant pas réellement partie de l’identificateur, l’identificateur peut être vu dans d’autres langages comme un identificateur normal, sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="b0969-178">The character `@` is not actually part of the identifier, so the identifier might be seen in other languages as a normal identifier, without the prefix.</span></span> <span data-ttu-id="b0969-179">Un identificateur avec un `@` préfixe est appelé ***identificateur textuel***.</span><span class="sxs-lookup"><span data-stu-id="b0969-179">An identifier with an `@` prefix is called a ***verbatim identifier***.</span></span> <span data-ttu-id="b0969-180">L’utilisation du `@` préfixe pour les identificateurs qui ne sont pas des mots clés est autorisée, mais elle est fortement déconseillée comme une question de style.</span><span class="sxs-lookup"><span data-stu-id="b0969-180">Use of the `@` prefix for identifiers that are not keywords is permitted, but strongly discouraged as a matter of style.</span></span>

<span data-ttu-id="b0969-181">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="b0969-181">The example:</span></span>
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
<span data-ttu-id="b0969-182">définit une classe nommée «`class`» avec une méthode statique nommée «`static`» qui prend un paramètre nommé «`bool`».</span><span class="sxs-lookup"><span data-stu-id="b0969-182">defines a class named "`class`" with a static method named "`static`" that takes a parameter named "`bool`".</span></span> <span data-ttu-id="b0969-183">Notez que dans la mesure où les échappements Unicode ne sont pas autorisés dans`cl\u0061ss`les mots clés, le jeton « » est un identificateur et est le`@class`même identificateur que « ».</span><span class="sxs-lookup"><span data-stu-id="b0969-183">Note that since Unicode escapes are not permitted in keywords, the token "`cl\u0061ss`" is an identifier, and is the same identifier as "`@class`".</span></span>

<span data-ttu-id="b0969-184">Deux identificateurs sont considérés comme identiques s’ils sont identiques après l’application des transformations suivantes, dans l’ordre :</span><span class="sxs-lookup"><span data-stu-id="b0969-184">Two identifiers are considered the same if they are identical after the following transformations are applied, in order:</span></span>

*  <span data-ttu-id="b0969-185">Le préfixe`@`«», s’il est utilisé, est supprimé.</span><span class="sxs-lookup"><span data-stu-id="b0969-185">The prefix "`@`", if used, is removed.</span></span>
*  <span data-ttu-id="b0969-186">Chaque *unicode_escape_sequence* est transformée en son caractère Unicode correspondant.</span><span class="sxs-lookup"><span data-stu-id="b0969-186">Each *unicode_escape_sequence* is transformed into its corresponding Unicode character.</span></span>
*  <span data-ttu-id="b0969-187">Tous les *formatting_character*sont supprimés.</span><span class="sxs-lookup"><span data-stu-id="b0969-187">Any *formatting_character*s are removed.</span></span>

<span data-ttu-id="b0969-188">Les identificateurs contenant deux traits de soulignement consécutifs (`U+005F`) sont réservés pour une utilisation par l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="b0969-188">Identifiers containing two consecutive underscore characters (`U+005F`) are reserved for use by the implementation.</span></span> <span data-ttu-id="b0969-189">Par exemple, une implémentation peut fournir des mots clés étendus qui commencent par deux traits de soulignement.</span><span class="sxs-lookup"><span data-stu-id="b0969-189">For example, an implementation might provide extended keywords that begin with two underscores.</span></span>

### <a name="keywords"></a><span data-ttu-id="b0969-190">Mots clés</span><span class="sxs-lookup"><span data-stu-id="b0969-190">Keywords</span></span>

<span data-ttu-id="b0969-191">Un ***mot clé*** est une séquence de caractères de type identificateur, qui est réservée et ne peut pas être utilisée en tant qu’identificateur, sauf s’il `@` est précédé du caractère.</span><span class="sxs-lookup"><span data-stu-id="b0969-191">A ***keyword*** is an identifier-like sequence of characters that is reserved, and cannot be used as an identifier except when prefaced by the `@` character.</span></span>

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

<span data-ttu-id="b0969-192">À certains endroits de la grammaire, les identificateurs spécifiques ont une signification particulière, mais ne sont pas des mots clés.</span><span class="sxs-lookup"><span data-stu-id="b0969-192">In some places in the grammar, specific identifiers have special meaning, but are not keywords.</span></span> <span data-ttu-id="b0969-193">Ces identificateurs sont parfois appelés « Mots-clés contextuels ».</span><span class="sxs-lookup"><span data-stu-id="b0969-193">Such identifiers are sometimes referred to as "contextual keywords".</span></span> <span data-ttu-id="b0969-194">Par exemple, dans une déclaration de propriété, les`get`identificateurs «`set`» et «» ont une signification spéciale ([accesseurs](classes.md#accessors)).</span><span class="sxs-lookup"><span data-stu-id="b0969-194">For example, within a property declaration, the "`get`" and "`set`" identifiers have special meaning ([Accessors](classes.md#accessors)).</span></span> <span data-ttu-id="b0969-195">Comme un identificateur autre que `get` ou `set` n’est jamais autorisé dans ces emplacements, cette utilisation n’est pas en conflit avec l’utilisation de ces mots comme identificateurs.</span><span class="sxs-lookup"><span data-stu-id="b0969-195">An identifier other than `get` or `set` is never permitted in these locations, so this use does not conflict with a use of these words as identifiers.</span></span> <span data-ttu-id="b0969-196">Dans d’autres cas, comme avec l’identificateur «`var`» dans les déclarations de variables locales implicitement typées (déclarations de[variables locales](statements.md#local-variable-declarations)), un mot clé contextuel peut être en conflit avec des noms déclarés.</span><span class="sxs-lookup"><span data-stu-id="b0969-196">In other cases, such as with the identifier "`var`" in implicitly typed local variable declarations ([Local variable declarations](statements.md#local-variable-declarations)), a contextual keyword can conflict with declared names.</span></span> <span data-ttu-id="b0969-197">Dans ce cas, le nom déclaré est prioritaire par rapport à l’utilisation de l’identificateur en tant que mot clé contextuel.</span><span class="sxs-lookup"><span data-stu-id="b0969-197">In such cases, the declared name takes precedence over the use of the identifier as a contextual keyword.</span></span>

### <a name="literals"></a><span data-ttu-id="b0969-198">Littéraux</span><span class="sxs-lookup"><span data-stu-id="b0969-198">Literals</span></span>

<span data-ttu-id="b0969-199">Un ***littéral*** est une représentation du code source d’une valeur.</span><span class="sxs-lookup"><span data-stu-id="b0969-199">A ***literal*** is a source code representation of a value.</span></span>

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

#### <a name="boolean-literals"></a><span data-ttu-id="b0969-200">Littéraux booléens</span><span class="sxs-lookup"><span data-stu-id="b0969-200">Boolean literals</span></span>

<span data-ttu-id="b0969-201">Il existe deux valeurs littérales booléennes `false`: `true` et.</span><span class="sxs-lookup"><span data-stu-id="b0969-201">There are two boolean literal values: `true` and `false`.</span></span>

```antlr
boolean_literal
    : 'true'
    | 'false'
    ;
```

<span data-ttu-id="b0969-202">Le type d’un *boolean_literal* est `bool`.</span><span class="sxs-lookup"><span data-stu-id="b0969-202">The type of a *boolean_literal* is `bool`.</span></span>

#### <a name="integer-literals"></a><span data-ttu-id="b0969-203">Littéraux d'entier</span><span class="sxs-lookup"><span data-stu-id="b0969-203">Integer literals</span></span>

<span data-ttu-id="b0969-204">Les littéraux entiers sont utilisés pour écrire des `int`valeurs `uint`de `long`types, `ulong`, et.</span><span class="sxs-lookup"><span data-stu-id="b0969-204">Integer literals are used to write values of types `int`, `uint`, `long`, and `ulong`.</span></span> <span data-ttu-id="b0969-205">Les littéraux entiers ont deux formes possibles : decimal et hexadécimal.</span><span class="sxs-lookup"><span data-stu-id="b0969-205">Integer literals have two possible forms: decimal and hexadecimal.</span></span>

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

<span data-ttu-id="b0969-206">Le type d’un littéral entier est déterminé comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0969-206">The type of an integer literal is determined as follows:</span></span>

*  <span data-ttu-id="b0969-207">Si le littéral n’a pas de suffixe, il a le premier de ces types dans lequel sa valeur peut `int`être `uint`représentée : `ulong`,, `long`,.</span><span class="sxs-lookup"><span data-stu-id="b0969-207">If the literal has no suffix, it has the first of these types in which its value can be represented: `int`, `uint`, `long`, `ulong`.</span></span>
*  <span data-ttu-id="b0969-208">Si le littéral est suivi d’un `U` suffixe par ou `u`, il a le premier de ces types dans lequel sa valeur peut `uint`être `ulong`représentée :,.</span><span class="sxs-lookup"><span data-stu-id="b0969-208">If the literal is suffixed by `U` or `u`, it has the first of these types in which its value can be represented: `uint`, `ulong`.</span></span>
*  <span data-ttu-id="b0969-209">Si le littéral est suivi d’un `L` suffixe par ou `l`, il a le premier de ces types dans lequel sa valeur peut `long`être `ulong`représentée :,.</span><span class="sxs-lookup"><span data-stu-id="b0969-209">If the literal is suffixed by `L` or `l`, it has the first of these types in which its value can be represented: `long`, `ulong`.</span></span>
*  <span data-ttu-id="b0969-210">Si le littéral est précédé `UL`d’un suffixe, `ul` `Ul`, `LU` `uL`, `Lu`, `lU`,, `lu`ou, il est de `ulong`type.</span><span class="sxs-lookup"><span data-stu-id="b0969-210">If the literal is suffixed by `UL`, `Ul`, `uL`, `ul`, `LU`, `Lu`, `lU`, or `lu`, it is of type `ulong`.</span></span>

<span data-ttu-id="b0969-211">Si la valeur représentée par un littéral entier est en dehors de la plage `ulong` du type, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0969-211">If the value represented by an integer literal is outside the range of the `ulong` type, a compile-time error occurs.</span></span>

<span data-ttu-id="b0969-212">En guise de style, il est suggéré d’utiliser`L`« » à la place de`l`« » lors de l’écriture de `long`littéraux de type, car il est facile de confondre la lettre «`1``l`» avec le chiffre «».</span><span class="sxs-lookup"><span data-stu-id="b0969-212">As a matter of style, it is suggested that "`L`" be used instead of "`l`" when writing literals of type `long`, since it is easy to confuse the letter "`l`" with the digit "`1`".</span></span>

<span data-ttu-id="b0969-213">Pour que les valeurs `int` et `long` les plus petites soient écrites en tant que littéraux décimaux entiers, les deux règles suivantes existent :</span><span class="sxs-lookup"><span data-stu-id="b0969-213">To permit the smallest possible `int` and `long` values to be written as decimal integer literals, the following two rules exist:</span></span>

* <span data-ttu-id="b0969-214">Lorsqu’un *decimal_integer_literal* avec la valeur 2147483648 (2 ^ 31) et qu’aucun *integer_type_suffix* n’apparaît en tant que jeton qui suit immédiatement un jeton d’opérateur moins unaire ([opérateur moins unaire](expressions.md#unary-minus-operator)), le résultat est une constante de type `int`avec la valeur-2147483648 (-2 ^ 31).</span><span class="sxs-lookup"><span data-stu-id="b0969-214">When a *decimal_integer_literal* with the value 2147483648 (2^31) and no *integer_type_suffix* appears as the token immediately following a unary minus operator token ([Unary minus operator](expressions.md#unary-minus-operator)), the result is a constant of type `int` with the value -2147483648 (-2^31).</span></span> <span data-ttu-id="b0969-215">Dans toutes les autres situations, une telle *decimal_integer_literal* est de `uint`type.</span><span class="sxs-lookup"><span data-stu-id="b0969-215">In all other situations, such a *decimal_integer_literal* is of type `uint`.</span></span>
* <span data-ttu-id="b0969-216">Lorsqu’un *decimal_integer_literal* avec la valeur 9223372036854775808 (2 ^ 63) et aucun *integer_type_suffix* ou *integer_type_suffix* `L` ou `l` apparaît comme jeton immédiatement après un signe moins unaire jeton d’opérateur ([opérateur moins unaire](expressions.md#unary-minus-operator)), le résultat est une constante `long` de type avec la valeur-9223372036854775808 (-2 ^ 63).</span><span class="sxs-lookup"><span data-stu-id="b0969-216">When a *decimal_integer_literal* with the value 9223372036854775808 (2^63) and no *integer_type_suffix* or the *integer_type_suffix* `L` or `l` appears as the token immediately following a unary minus operator token ([Unary minus operator](expressions.md#unary-minus-operator)), the result is a constant of type `long` with the value -9223372036854775808 (-2^63).</span></span> <span data-ttu-id="b0969-217">Dans toutes les autres situations, une telle *decimal_integer_literal* est de `ulong`type.</span><span class="sxs-lookup"><span data-stu-id="b0969-217">In all other situations, such a *decimal_integer_literal* is of type `ulong`.</span></span>

#### <a name="real-literals"></a><span data-ttu-id="b0969-218">Littéraux réels</span><span class="sxs-lookup"><span data-stu-id="b0969-218">Real literals</span></span>

<span data-ttu-id="b0969-219">Les littéraux réels sont utilisés pour écrire des valeurs `float`de `double`types, `decimal`et.</span><span class="sxs-lookup"><span data-stu-id="b0969-219">Real literals are used to write values of types `float`, `double`, and `decimal`.</span></span>

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

<span data-ttu-id="b0969-220">Si aucun *real_type_suffix* n’est spécifié, le type du littéral réel est `double`.</span><span class="sxs-lookup"><span data-stu-id="b0969-220">If no *real_type_suffix* is specified, the type of the real literal is `double`.</span></span> <span data-ttu-id="b0969-221">Dans le cas contraire, le suffixe de type réel détermine le type du littéral réel, comme suit :</span><span class="sxs-lookup"><span data-stu-id="b0969-221">Otherwise, the real type suffix determines the type of the real literal, as follows:</span></span>

*  <span data-ttu-id="b0969-222">Littéral réel dont le suffixe `F` est `f` ou est de `float`type.</span><span class="sxs-lookup"><span data-stu-id="b0969-222">A real literal suffixed by `F` or `f` is of type `float`.</span></span> <span data-ttu-id="b0969-223">Par exemple, les littéraux `1f`, `1.5f`, `1e10f`et `123.456F` sont tous de type `float`.</span><span class="sxs-lookup"><span data-stu-id="b0969-223">For example, the literals `1f`, `1.5f`, `1e10f`, and `123.456F` are all of type `float`.</span></span>
*  <span data-ttu-id="b0969-224">Littéral réel dont le suffixe `D` est `d` ou est de `double`type.</span><span class="sxs-lookup"><span data-stu-id="b0969-224">A real literal suffixed by `D` or `d` is of type `double`.</span></span> <span data-ttu-id="b0969-225">Par exemple, les littéraux `1d`, `1.5d`, `1e10d`et `123.456D` sont tous de type `double`.</span><span class="sxs-lookup"><span data-stu-id="b0969-225">For example, the literals `1d`, `1.5d`, `1e10d`, and `123.456D` are all of type `double`.</span></span>
*  <span data-ttu-id="b0969-226">Littéral réel dont le suffixe `M` est `m` ou est de `decimal`type.</span><span class="sxs-lookup"><span data-stu-id="b0969-226">A real literal suffixed by `M` or `m` is of type `decimal`.</span></span> <span data-ttu-id="b0969-227">Par exemple, les littéraux `1m`, `1.5m`, `1e10m`et `123.456M` sont tous de type `decimal`.</span><span class="sxs-lookup"><span data-stu-id="b0969-227">For example, the literals `1m`, `1.5m`, `1e10m`, and `123.456M` are all of type `decimal`.</span></span> <span data-ttu-id="b0969-228">Ce littéral est converti en une `decimal` valeur en acceptant la valeur exacte et, si nécessaire, en arrondissant à la valeur représentable la plus proche à l’aide de l’arrondi de Banker ([type décimal](types.md#the-decimal-type)).</span><span class="sxs-lookup"><span data-stu-id="b0969-228">This literal is converted to a `decimal` value by taking the exact value, and, if necessary, rounding to the nearest representable value using banker's rounding ([The decimal type](types.md#the-decimal-type)).</span></span> <span data-ttu-id="b0969-229">Toute échelle apparente dans le littéral est conservée, sauf si la valeur est arrondie ou si la valeur est égale à zéro (dans ce dernier cas, le signe et l’échelle sont 0).</span><span class="sxs-lookup"><span data-stu-id="b0969-229">Any scale apparent in the literal is preserved unless the value is rounded or the value is zero (in which latter case the sign and scale will be 0).</span></span> <span data-ttu-id="b0969-230">Par conséquent, le `2.900m` littéral sera analysé pour former la décimale avec le `0`signe, le `2900`coefficient et `3`l’échelle.</span><span class="sxs-lookup"><span data-stu-id="b0969-230">Hence, the literal `2.900m` will be parsed to form the decimal with sign `0`, coefficient `2900`, and scale `3`.</span></span>

<span data-ttu-id="b0969-231">Si le littéral spécifié ne peut pas être représenté dans le type indiqué, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0969-231">If the specified literal cannot be represented in the indicated type, a compile-time error occurs.</span></span>

<span data-ttu-id="b0969-232">La valeur d’un littéral réel de type `float` ou `double` est déterminée à l’aide du mode IEEE « arrondi au plus proche ».</span><span class="sxs-lookup"><span data-stu-id="b0969-232">The value of a real literal of type `float` or `double` is determined by using the IEEE "round to nearest" mode.</span></span>

<span data-ttu-id="b0969-233">Notez que dans un littéral réel, les chiffres décimaux sont toujours requis après la virgule décimale.</span><span class="sxs-lookup"><span data-stu-id="b0969-233">Note that in a real literal, decimal digits are always required after the decimal point.</span></span> <span data-ttu-id="b0969-234">Par exemple, `1.3F` est un littéral réel, `1.F` mais pas.</span><span class="sxs-lookup"><span data-stu-id="b0969-234">For example, `1.3F` is a real literal but `1.F` is not.</span></span>

#### <a name="character-literals"></a><span data-ttu-id="b0969-235">Littéraux de caractère</span><span class="sxs-lookup"><span data-stu-id="b0969-235">Character literals</span></span>

<span data-ttu-id="b0969-236">Un littéral de caractère représente un caractère unique et se compose généralement d’un caractère entre guillemets, `'a'`comme dans.</span><span class="sxs-lookup"><span data-stu-id="b0969-236">A character literal represents a single character, and usually consists of a character in quotes, as in `'a'`.</span></span>

<span data-ttu-id="b0969-237">Remarque : La notation de la grammaire ANTLR rend ce qui est confus.</span><span class="sxs-lookup"><span data-stu-id="b0969-237">Note: The ANTLR grammar notation makes the following confusing!</span></span> <span data-ttu-id="b0969-238">Dans antlr, lorsque vous écrivez `\'` , il s’agit d’un `'`guillemet simple.</span><span class="sxs-lookup"><span data-stu-id="b0969-238">In ANTLR, when you write `\'` it stands for a single quote `'`.</span></span> <span data-ttu-id="b0969-239">Lorsque vous écrivez `\\` , il s’agit d’une barre `\`oblique inverse unique.</span><span class="sxs-lookup"><span data-stu-id="b0969-239">And when you write `\\` it stands for a single backslash `\`.</span></span> <span data-ttu-id="b0969-240">Par conséquent, la première règle pour un littéral de caractère signifie qu’il commence par un guillemet simple, puis par un caractère, puis par un guillemet simple.</span><span class="sxs-lookup"><span data-stu-id="b0969-240">Therefore the first rule for a character literal means it starts with a single quote, then a character, then a single quote.</span></span> <span data-ttu-id="b0969-241">Et les onze séquences d’échappement simples possibles `\'`sont `\"`, `\\`, `\0`, `\a`, `\b`, `\f`, `\n`, `\r`, `\t`, ,`\v`.</span><span class="sxs-lookup"><span data-stu-id="b0969-241">And the eleven possible simple escape sequences are `\'`, `\"`, `\\`, `\0`, `\a`, `\b`, `\f`, `\n`, `\r`, `\t`, `\v`.</span></span>

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

<span data-ttu-id="b0969-242">Un caractère qui suit une barre oblique inverse`\`() dans *un caractère* doit être l’un des caractères suivants `'`: `"`, `\`, `0`, `a`, `b`, `f` , , `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span><span class="sxs-lookup"><span data-stu-id="b0969-242">A character that follows a backslash character (`\`) in a *character* must be one of the following characters: `'`, `"`, `\`, `0`, `a`, `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span></span> <span data-ttu-id="b0969-243">Sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0969-243">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="b0969-244">Une séquence d’échappement hexadécimale représente un caractère Unicode unique, avec la valeur formée par le nombre hexadécimal`\x`suivant «».</span><span class="sxs-lookup"><span data-stu-id="b0969-244">A hexadecimal escape sequence represents a single Unicode character, with the value formed by the hexadecimal number following "`\x`".</span></span>

<span data-ttu-id="b0969-245">Si la valeur représentée par un littéral de caractère est supérieure `U+FFFF`à, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0969-245">If the value represented by a character literal is greater than `U+FFFF`, a compile-time error occurs.</span></span>

<span data-ttu-id="b0969-246">Une séquence d’échappement de caractère Unicode ([séquences d’échappement de caractère Unicode](lexical-structure.md#unicode-character-escape-sequences)) dans un littéral de caractère `U+0000` doit `U+FFFF`être comprise dans la plage de.</span><span class="sxs-lookup"><span data-stu-id="b0969-246">A Unicode character escape sequence ([Unicode character escape sequences](lexical-structure.md#unicode-character-escape-sequences)) in a character literal must be in the range `U+0000` to `U+FFFF`.</span></span>

<span data-ttu-id="b0969-247">Une séquence d’échappement simple représente un encodage de caractères Unicode, comme décrit dans le tableau ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b0969-247">A simple escape sequence represents a Unicode character encoding, as described in the table below.</span></span>


| <span data-ttu-id="b0969-248">__Séquence d’échappement__</span><span class="sxs-lookup"><span data-stu-id="b0969-248">__Escape sequence__</span></span> | <span data-ttu-id="b0969-249">__Nom de caractère__</span><span class="sxs-lookup"><span data-stu-id="b0969-249">__Character name__</span></span> | <span data-ttu-id="b0969-250">__Encodage Unicode__</span><span class="sxs-lookup"><span data-stu-id="b0969-250">__Unicode encoding__</span></span> |
|---------------------|--------------------|----------------------|
| `\'`                | <span data-ttu-id="b0969-251">Guillemet simple</span><span class="sxs-lookup"><span data-stu-id="b0969-251">Single quote</span></span>       | `0x0027`             | 
| `\"`                | <span data-ttu-id="b0969-252">Guillemet double</span><span class="sxs-lookup"><span data-stu-id="b0969-252">Double quote</span></span>       | `0x0022`             | 
| <span data-ttu-id="b0969-253">`\\`| Barre oblique inverse |`0x005C`</span><span class="sxs-lookup"><span data-stu-id="b0969-253">`\\`                | Backslash          | `0x005C`</span></span>             | 
| `\0`                | <span data-ttu-id="b0969-254">Null</span><span class="sxs-lookup"><span data-stu-id="b0969-254">Null</span></span>               | `0x0000`             | 
| `\a`                | <span data-ttu-id="b0969-255">Alerte</span><span class="sxs-lookup"><span data-stu-id="b0969-255">Alert</span></span>              | `0x0007`             | 
| `\b`                | <span data-ttu-id="b0969-256">Retour arrière</span><span class="sxs-lookup"><span data-stu-id="b0969-256">Backspace</span></span>          | `0x0008`             | 
| `\f`                | <span data-ttu-id="b0969-257">Saut de page</span><span class="sxs-lookup"><span data-stu-id="b0969-257">Form feed</span></span>          | `0x000C`             | 
| `\n`                | <span data-ttu-id="b0969-258">Nouvelle ligne</span><span class="sxs-lookup"><span data-stu-id="b0969-258">New line</span></span>           | `0x000A`             | 
| `\r`                | <span data-ttu-id="b0969-259">Retour chariot</span><span class="sxs-lookup"><span data-stu-id="b0969-259">Carriage return</span></span>    | `0x000D`             | 
| `\t`                | <span data-ttu-id="b0969-260">Tabulation horizontale</span><span class="sxs-lookup"><span data-stu-id="b0969-260">Horizontal tab</span></span>     | `0x0009`             | 
| `\v`                | <span data-ttu-id="b0969-261">Tabulation verticale</span><span class="sxs-lookup"><span data-stu-id="b0969-261">Vertical tab</span></span>       | `0x000B`             | 

<span data-ttu-id="b0969-262">Le type d’un *character_literal* est `char`.</span><span class="sxs-lookup"><span data-stu-id="b0969-262">The type of a *character_literal* is `char`.</span></span>

#### <a name="string-literals"></a><span data-ttu-id="b0969-263">Littéraux de chaîne</span><span class="sxs-lookup"><span data-stu-id="b0969-263">String literals</span></span>

<span data-ttu-id="b0969-264">C#prend en charge deux formes de littéraux de chaîne : les ***littéraux*** de chaîne normaux et les ***littéraux de chaîne Verbatim***.</span><span class="sxs-lookup"><span data-stu-id="b0969-264">C# supports two forms of string literals: ***regular string literals*** and ***verbatim string literals***.</span></span>

<span data-ttu-id="b0969-265">Un littéral de chaîne standard se compose de zéro, un ou plusieurs caractères placés entre guillemets doubles, comme dans `"hello"`, et peut inclure à la fois des séquences d’échappement simples (comme `\t` pour le caractère de tabulation) et des séquences d’échappement hexadécimales et Unicode.</span><span class="sxs-lookup"><span data-stu-id="b0969-265">A regular string literal consists of zero or more characters enclosed in double quotes, as in `"hello"`, and may include both simple escape sequences (such as `\t` for the tab character), and hexadecimal and Unicode escape sequences.</span></span>

<span data-ttu-id="b0969-266">Un littéral de chaîne Verbatim se compose `@` d’un caractère suivi d’un guillemet double, de zéro, d’un ou de plusieurs caractères et d’un guillemet double fermant.</span><span class="sxs-lookup"><span data-stu-id="b0969-266">A verbatim string literal consists of an `@` character followed by a double-quote character, zero or more characters, and a closing double-quote character.</span></span> <span data-ttu-id="b0969-267">Un exemple simple est `@"hello"`.</span><span class="sxs-lookup"><span data-stu-id="b0969-267">A simple example is `@"hello"`.</span></span> <span data-ttu-id="b0969-268">Dans un littéral de chaîne Verbatim, les caractères entre les délimiteurs sont interprétés textuellement, la seule exception étant *quote_escape_sequence*.</span><span class="sxs-lookup"><span data-stu-id="b0969-268">In a verbatim string literal, the characters between the delimiters are interpreted verbatim, the only exception being a *quote_escape_sequence*.</span></span> <span data-ttu-id="b0969-269">En particulier, les séquences d’échappement simples et les séquences d’échappement hexadécimales et Unicode ne sont pas traitées dans des littéraux de chaîne Verbatim.</span><span class="sxs-lookup"><span data-stu-id="b0969-269">In particular, simple escape sequences, and hexadecimal and Unicode escape sequences are not processed in verbatim string literals.</span></span> <span data-ttu-id="b0969-270">Un littéral de chaîne Verbatim peut s’étendre sur plusieurs lignes.</span><span class="sxs-lookup"><span data-stu-id="b0969-270">A verbatim string literal may span multiple lines.</span></span>

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

<span data-ttu-id="b0969-271">Un caractère qui suit une barre oblique inverse`\`() dans un *regular_string_literal_character* doit être l’un des caractères suivants `'`: `"`, `\`, `0`, `a`, `b` , , `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span><span class="sxs-lookup"><span data-stu-id="b0969-271">A character that follows a backslash character (`\`) in a *regular_string_literal_character* must be one of the following characters: `'`, `"`, `\`, `0`, `a`, `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span></span> <span data-ttu-id="b0969-272">Sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0969-272">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="b0969-273">L’exemple</span><span class="sxs-lookup"><span data-stu-id="b0969-273">The example</span></span>
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
<span data-ttu-id="b0969-274">affiche divers littéraux de chaîne.</span><span class="sxs-lookup"><span data-stu-id="b0969-274">shows a variety of string literals.</span></span> <span data-ttu-id="b0969-275">Le dernier littéral de chaîne `j`,, est un littéral de chaîne Verbatim qui s’étend sur plusieurs lignes.</span><span class="sxs-lookup"><span data-stu-id="b0969-275">The last string literal, `j`, is a verbatim string literal that spans multiple lines.</span></span> <span data-ttu-id="b0969-276">Les caractères entre guillemets, y compris les espaces blancs tels que les caractères de nouvelle ligne, sont conservés textuellement.</span><span class="sxs-lookup"><span data-stu-id="b0969-276">The characters between the quotation marks, including white space such as new line characters, are preserved verbatim.</span></span>

<span data-ttu-id="b0969-277">Comme une séquence d’échappement hexadécimale peut avoir un nombre variable de chiffres hexadécimaux, le `"\x123"` littéral de chaîne contient un caractère unique avec la valeur hexadécimale 123.</span><span class="sxs-lookup"><span data-stu-id="b0969-277">Since a hexadecimal escape sequence can have a variable number of hex digits, the string literal `"\x123"` contains a single character with hex value 123.</span></span> <span data-ttu-id="b0969-278">Pour créer une chaîne contenant le caractère avec la valeur hexadécimale 12 suivi du caractère 3, il est `"\x00123"` possible `"\x12" + "3"` d’écrire ou à la place.</span><span class="sxs-lookup"><span data-stu-id="b0969-278">To create a string containing the character with hex value 12 followed by the character 3, one could write `"\x00123"` or `"\x12" + "3"` instead.</span></span>

<span data-ttu-id="b0969-279">Le type d’un *string_literal* est `string`.</span><span class="sxs-lookup"><span data-stu-id="b0969-279">The type of a *string_literal* is `string`.</span></span>

<span data-ttu-id="b0969-280">Chaque littéral de chaîne ne produit pas nécessairement une nouvelle instance de chaîne.</span><span class="sxs-lookup"><span data-stu-id="b0969-280">Each string literal does not necessarily result in a new string instance.</span></span> <span data-ttu-id="b0969-281">Lorsque deux littéraux de chaîne ou plus qui sont équivalents en fonction de l’opérateur d’égalité de chaînes ([opérateurs d’égalité de chaînes](expressions.md#string-equality-operators)) apparaissent dans le même programme, ces littéraux de chaîne font référence à la même instance de chaîne.</span><span class="sxs-lookup"><span data-stu-id="b0969-281">When two or more string literals that are equivalent according to the string equality operator ([String equality operators](expressions.md#string-equality-operators)) appear in the same program, these string literals refer to the same string instance.</span></span> <span data-ttu-id="b0969-282">Par exemple, la sortie produite par</span><span class="sxs-lookup"><span data-stu-id="b0969-282">For instance, the output produced by</span></span>
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
<span data-ttu-id="b0969-283">est `True` dû au fait que les deux littéraux font référence à la même instance de chaîne.</span><span class="sxs-lookup"><span data-stu-id="b0969-283">is `True` because the two literals refer to the same string instance.</span></span>

#### <a name="interpolated-string-literals"></a><span data-ttu-id="b0969-284">Littéraux de chaîne interpolés</span><span class="sxs-lookup"><span data-stu-id="b0969-284">Interpolated string literals</span></span>

<span data-ttu-id="b0969-285">Les littéraux de chaîne interpolés sont similaires aux littéraux de chaîne, mais contiennent des `{` trous `}`délimités par et, où les expressions peuvent se produire.</span><span class="sxs-lookup"><span data-stu-id="b0969-285">Interpolated string literals are similar to string literals, but contain holes delimited by `{` and `}`, wherein expressions can occur.</span></span> <span data-ttu-id="b0969-286">Lors de l’exécution, les expressions sont évaluées dans le but de faire en sorte que leurs formes textuelles se substituent à la chaîne à l’endroit où se trouve le trou.</span><span class="sxs-lookup"><span data-stu-id="b0969-286">At runtime, the expressions are evaluated with the purpose of having their textual forms substituted into the string at the place where the hole occurs.</span></span> <span data-ttu-id="b0969-287">La syntaxe et la sémantique de l’interpolation de chaîne sont décrites dans la section ([chaînes interpolées](expressions.md#interpolated-strings)).</span><span class="sxs-lookup"><span data-stu-id="b0969-287">The syntax and semantics of string interpolation are described in section ([Interpolated strings](expressions.md#interpolated-strings)).</span></span>

<span data-ttu-id="b0969-288">À l’instar des littéraux de chaîne, les littéraux de chaîne interpolés peuvent être normaux ou textuels.</span><span class="sxs-lookup"><span data-stu-id="b0969-288">Like string literals, interpolated string literals can be either regular or verbatim.</span></span> <span data-ttu-id="b0969-289">Les littéraux de chaîne réguliers interpolés sont délimités `$"` par et `"`, et les littéraux de chaîne Verbatim interpolés `"`sont délimités par `$@"` et.</span><span class="sxs-lookup"><span data-stu-id="b0969-289">Interpolated regular string literals are delimited by `$"` and `"`, and interpolated verbatim string literals are delimited by `$@"` and `"`.</span></span>

<span data-ttu-id="b0969-290">Comme pour les autres littéraux, l’analyse lexicale d’un littéral de chaîne interpolée produit initialement un jeton unique, conformément à la grammaire ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b0969-290">Like other literals, lexical analysis of an interpolated string literal initially results in a single token, as per the grammar below.</span></span> <span data-ttu-id="b0969-291">Toutefois, avant l’analyse syntaxique, le jeton unique d’un littéral de chaîne interpolé est divisé en plusieurs jetons pour les parties de la chaîne qui entourent les trous, et les éléments d’entrée qui se produisent dans les trous sont analysés de façon lexicale.</span><span class="sxs-lookup"><span data-stu-id="b0969-291">However, before syntactic analysis, the single token of an interpolated string literal is broken into several tokens for the parts of the string enclosing the holes, and the input elements occurring in the holes are lexically analysed again.</span></span> <span data-ttu-id="b0969-292">Cela peut, à son tour, générer davantage de littéraux de chaîne interpolés à traiter, mais, s’il est correct, finit par entraîner une séquence de jetons pour l’analyse syntaxique.</span><span class="sxs-lookup"><span data-stu-id="b0969-292">This may in turn produce more interpolated string literals to be processed, but, if lexically correct, will eventually lead to a sequence of tokens for syntactic analysis to process.</span></span>

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

<span data-ttu-id="b0969-293">Un jeton *interpolated_string_literal* est réinterprété comme plusieurs jetons et autres éléments d’entrée comme suit, dans l’ordre des occurrences dans le *interpolated_string_literal*:</span><span class="sxs-lookup"><span data-stu-id="b0969-293">An *interpolated_string_literal* token is reinterpreted as multiple tokens and other input elements as follows, in order of occurrence in the *interpolated_string_literal*:</span></span>

* <span data-ttu-id="b0969-294">Les occurrences des éléments suivants sont réinterprétées comme des jetons individuels distincts : `$` le signe de début, *interpolated_regular_string_whole*, *interpolated_regular_string_start*, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_whole*, *interpolated_verbatim_string_start*, *interpolated_verbatim_string_mid* et *interpolated_verbatim_string_end*.</span><span class="sxs-lookup"><span data-stu-id="b0969-294">Occurrences of the following are reinterpreted as separate individual tokens: the leading `$` sign, *interpolated_regular_string_whole*, *interpolated_regular_string_start*, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_whole*, *interpolated_verbatim_string_start*, *interpolated_verbatim_string_mid* and *interpolated_verbatim_string_end*.</span></span>
* <span data-ttu-id="b0969-295">Les occurrences de *regular_balanced_text* et *verbatim_balanced_text* entre celles-ci sont retraitées comme un *input_section* ([analyse lexicale](lexical-structure.md#lexical-analysis)) et sont réinterprétées comme la séquence résultante d’éléments d’entrée.</span><span class="sxs-lookup"><span data-stu-id="b0969-295">Occurrences of *regular_balanced_text* and *verbatim_balanced_text* between these are reprocessed as an *input_section* ([Lexical analysis](lexical-structure.md#lexical-analysis)) and are reinterpreted as the resulting sequence of input elements.</span></span> <span data-ttu-id="b0969-296">Elles peuvent à leur tour inclure des jetons de littéral de chaîne interpolés à réinterpréter.</span><span class="sxs-lookup"><span data-stu-id="b0969-296">These may in turn include interpolated string literal tokens to be reinterpreted.</span></span>

<span data-ttu-id="b0969-297">L’analyse syntaxique réassociera les jetons en un *interpolated_string_expression* ([chaînes interpolées](expressions.md#interpolated-strings)).</span><span class="sxs-lookup"><span data-stu-id="b0969-297">Syntactic analysis will recombine the tokens into an *interpolated_string_expression* ([Interpolated strings](expressions.md#interpolated-strings)).</span></span>

<span data-ttu-id="b0969-298">Exemples TODO</span><span class="sxs-lookup"><span data-stu-id="b0969-298">Examples TODO</span></span>


#### <a name="the-null-literal"></a><span data-ttu-id="b0969-299">Littéral null</span><span class="sxs-lookup"><span data-stu-id="b0969-299">The null literal</span></span>

```antlr
null_literal
    : 'null'
    ;
```

<span data-ttu-id="b0969-300">Le *null_literal* peut être converti implicitement en un type référence ou un type Nullable.</span><span class="sxs-lookup"><span data-stu-id="b0969-300">The  *null_literal* can be implicitly converted to a reference type or nullable type.</span></span>

### <a name="operators-and-punctuators"></a><span data-ttu-id="b0969-301">Opérateurs et signes de ponctuation</span><span class="sxs-lookup"><span data-stu-id="b0969-301">Operators and punctuators</span></span>

<span data-ttu-id="b0969-302">Il existe plusieurs types d’opérateurs et de signes de ponctuation.</span><span class="sxs-lookup"><span data-stu-id="b0969-302">There are several kinds of operators and punctuators.</span></span> <span data-ttu-id="b0969-303">Les opérateurs sont utilisés dans les expressions pour décrire les opérations impliquant un ou plusieurs opérandes.</span><span class="sxs-lookup"><span data-stu-id="b0969-303">Operators are used in expressions to describe operations involving one or more operands.</span></span> <span data-ttu-id="b0969-304">Par exemple, l’expression `a + b` utilise l' `+` opérateur pour `a` ajouter les deux opérandes et `b`.</span><span class="sxs-lookup"><span data-stu-id="b0969-304">For example, the expression `a + b` uses the `+` operator to add the two operands `a` and `b`.</span></span> <span data-ttu-id="b0969-305">Les signes de ponctuation sont destinés au regroupement et à la séparation.</span><span class="sxs-lookup"><span data-stu-id="b0969-305">Punctuators are for grouping and separating.</span></span>

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

<span data-ttu-id="b0969-306">La barre verticale dans les productions *right_shift* et *right_shift_assignment* est utilisée pour indiquer que, contrairement aux autres productions dans la syntaxe syntaxique, aucun caractère de tout type (pas même espace blanc) n’est autorisé entre les jetons.</span><span class="sxs-lookup"><span data-stu-id="b0969-306">The vertical bar in the *right_shift* and *right_shift_assignment* productions are used to indicate that, unlike other productions in the syntactic grammar, no characters of any kind (not even whitespace) are allowed between the tokens.</span></span> <span data-ttu-id="b0969-307">Ces productions sont traitées de façon spéciale pour permettre la gestion correcte des *type_parameter_list*s ([paramètres de type](classes.md#type-parameters)).</span><span class="sxs-lookup"><span data-stu-id="b0969-307">These productions are treated specially in order to enable the correct  handling of *type_parameter_list*s ([Type parameters](classes.md#type-parameters)).</span></span>

## <a name="pre-processing-directives"></a><span data-ttu-id="b0969-308">Directives de prétraitement</span><span class="sxs-lookup"><span data-stu-id="b0969-308">Pre-processing directives</span></span>

<span data-ttu-id="b0969-309">Les directives de prétraitement offrent la possibilité d’ignorer conditionnellement des sections de fichiers sources, de signaler des conditions d’erreur et d’avertissement, et de détourer des régions distinctes du code source.</span><span class="sxs-lookup"><span data-stu-id="b0969-309">The pre-processing directives provide the ability to conditionally skip sections of source files, to report error and warning conditions, and to delineate distinct regions of source code.</span></span> <span data-ttu-id="b0969-310">Le terme « directives de prétraitement » est utilisé uniquement à des fins de cohérence avec C++ les langages C et de programmation.</span><span class="sxs-lookup"><span data-stu-id="b0969-310">The term "pre-processing directives" is used only for consistency with the C and C++ programming languages.</span></span> <span data-ttu-id="b0969-311">Dans C#, il n’y a pas d’étape de pré-traitement distincte ; les directives de pré-traitement sont traitées dans le cadre de la phase d’analyse lexicale.</span><span class="sxs-lookup"><span data-stu-id="b0969-311">In C#, there is no separate pre-processing step; pre-processing directives are processed as part of the lexical analysis phase.</span></span>

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

<span data-ttu-id="b0969-312">Les directives de pré-traitement suivantes sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="b0969-312">The following pre-processing directives are available:</span></span>

*  <span data-ttu-id="b0969-313">`#define`et `#undef`, qui sont utilisés pour définir et annuler la définition, respectivement, des symboles de compilation conditionnelle ([directives de déclaration](lexical-structure.md#declaration-directives)).</span><span class="sxs-lookup"><span data-stu-id="b0969-313">`#define` and `#undef`, which are used to define and undefine, respectively, conditional compilation symbols ([Declaration directives](lexical-structure.md#declaration-directives)).</span></span>
*  <span data-ttu-id="b0969-314">`#if`, `#elif`, `#else` et`#endif`, qui sont utilisés pour ignorer de manière conditionnelle les sections du code source ([directives de compilation conditionnelle](lexical-structure.md#conditional-compilation-directives)).</span><span class="sxs-lookup"><span data-stu-id="b0969-314">`#if`, `#elif`, `#else`, and `#endif`, which are used to conditionally skip sections of source code ([Conditional compilation directives](lexical-structure.md#conditional-compilation-directives)).</span></span>
*  <span data-ttu-id="b0969-315">`#line`, qui est utilisé pour contrôler les numéros de ligne émis pour les erreurs et les avertissements ([directives de ligne](lexical-structure.md#line-directives)).</span><span class="sxs-lookup"><span data-stu-id="b0969-315">`#line`, which is used to control line numbers emitted for errors and warnings ([Line directives](lexical-structure.md#line-directives)).</span></span>
*  <span data-ttu-id="b0969-316">`#error`et `#warning`, qui sont utilisés pour émettre des erreurs et des avertissements, respectivement ([directives de diagnostic](lexical-structure.md#diagnostic-directives)).</span><span class="sxs-lookup"><span data-stu-id="b0969-316">`#error` and `#warning`, which are used to issue errors and warnings, respectively ([Diagnostic directives](lexical-structure.md#diagnostic-directives)).</span></span>
*  <span data-ttu-id="b0969-317">`#region`et `#endregion`, qui sont utilisés pour marquer explicitement les sections du code source ([directives de région](lexical-structure.md#region-directives)).</span><span class="sxs-lookup"><span data-stu-id="b0969-317">`#region` and `#endregion`, which are used to explicitly mark sections of source code ([Region directives](lexical-structure.md#region-directives)).</span></span>
*  <span data-ttu-id="b0969-318">`#pragma`, qui est utilisé pour spécifier des informations contextuelles facultatives pour le compilateur ([directives pragma](lexical-structure.md#pragma-directives)).</span><span class="sxs-lookup"><span data-stu-id="b0969-318">`#pragma`, which is used to specify optional contextual information to the compiler ([Pragma directives](lexical-structure.md#pragma-directives)).</span></span>

<span data-ttu-id="b0969-319">Une directive de prétraitement occupe toujours une ligne distincte de code source et commence toujours par un `#` caractère et un nom de directive de prétraitement.</span><span class="sxs-lookup"><span data-stu-id="b0969-319">A pre-processing directive always occupies a separate line of source code and always begins with a `#` character and a pre-processing directive name.</span></span> <span data-ttu-id="b0969-320">Un espace blanc peut se produire `#` avant le caractère et `#` entre le caractère et le nom de la directive.</span><span class="sxs-lookup"><span data-stu-id="b0969-320">White space may occur before the `#` character and between the `#` character and the directive name.</span></span>

<span data-ttu-id="b0969-321">Une ligne source contenant une `#define`directive `#undef`, `#if` `#elif` `#else` ,,`#endif` ,`#endregion` ,, ou peut se terminer par un commentaire sur une seule ligne. `#line`</span><span class="sxs-lookup"><span data-stu-id="b0969-321">A source line containing a `#define`, `#undef`, `#if`, `#elif`, `#else`, `#endif`, `#line`, or `#endregion` directive may end with a single-line comment.</span></span> <span data-ttu-id="b0969-322">Les commentaires délimités `/* */` (style de commentaires) ne sont pas autorisés sur les lignes sources contenant des directives de prétraitement.</span><span class="sxs-lookup"><span data-stu-id="b0969-322">Delimited comments (the `/* */` style of comments) are not permitted on source lines containing pre-processing directives.</span></span>

<span data-ttu-id="b0969-323">Les directives de pré-traitement ne sont pas des jetons et ne font pas partie de C#la syntaxe syntaxique de.</span><span class="sxs-lookup"><span data-stu-id="b0969-323">Pre-processing directives are not tokens and are not part of the syntactic grammar of C#.</span></span> <span data-ttu-id="b0969-324">Toutefois, les directives de prétraitement peuvent être utilisées pour inclure ou exclure des séquences de jetons et peuvent, de cette façon, affecter la C# signification d’un programme.</span><span class="sxs-lookup"><span data-stu-id="b0969-324">However, pre-processing directives can be used to include or exclude sequences of tokens and can in that way affect the meaning of a C# program.</span></span> <span data-ttu-id="b0969-325">Par exemple, une fois compilé, le programme :</span><span class="sxs-lookup"><span data-stu-id="b0969-325">For example, when compiled, the program:</span></span>
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
<span data-ttu-id="b0969-326">produit exactement la même séquence de jetons que le programme :</span><span class="sxs-lookup"><span data-stu-id="b0969-326">results in the exact same sequence of tokens as the program:</span></span>
```csharp
class C
{
    void F() {}
    void I() {}
}
```

<span data-ttu-id="b0969-327">Par conséquent, contrairement à l’lexicale, les deux programmes sont très différents, syntaxiquement identiques.</span><span class="sxs-lookup"><span data-stu-id="b0969-327">Thus, whereas lexically, the two programs are quite different, syntactically, they are identical.</span></span>

### <a name="conditional-compilation-symbols"></a><span data-ttu-id="b0969-328">Symboles de compilation conditionnelle</span><span class="sxs-lookup"><span data-stu-id="b0969-328">Conditional compilation symbols</span></span>

<span data-ttu-id="b0969-329">La fonctionnalité de compilation conditionnelle fournie par `#if`les `#elif`directives `#else`,, `#endif` et est contrôlée par le biais d’expressions de prétraitement ([expressions de pré-traitement](lexical-structure.md#pre-processing-expressions)) et conditionnelle symboles de compilation.</span><span class="sxs-lookup"><span data-stu-id="b0969-329">The conditional compilation functionality provided by the `#if`, `#elif`, `#else`, and `#endif` directives is controlled through pre-processing expressions ([Pre-processing expressions](lexical-structure.md#pre-processing-expressions)) and conditional compilation symbols.</span></span>

```antlr
conditional_symbol
    : '<Any identifier_or_keyword except true or false>'
    ;
```

<span data-ttu-id="b0969-330">Un symbole de compilation conditionnelle a deux États possibles : ***défini*** ou ***non défini***.</span><span class="sxs-lookup"><span data-stu-id="b0969-330">A conditional compilation symbol has two possible states: ***defined*** or ***undefined***.</span></span> <span data-ttu-id="b0969-331">Au début du traitement lexical d’un fichier source, un symbole de compilation conditionnelle n’est pas défini, sauf s’il a été défini explicitement par un mécanisme externe (tel qu’une option de compilateur de ligne de commande).</span><span class="sxs-lookup"><span data-stu-id="b0969-331">At the beginning of the lexical processing of a source file, a conditional compilation symbol is undefined unless it has been explicitly defined by an external mechanism (such as a command-line compiler option).</span></span> <span data-ttu-id="b0969-332">Quand une `#define` directive est traitée, le symbole de compilation conditionnelle nommé dans cette directive devient défini dans ce fichier source.</span><span class="sxs-lookup"><span data-stu-id="b0969-332">When a `#define` directive is processed, the conditional compilation symbol named in that directive becomes defined in that source file.</span></span> <span data-ttu-id="b0969-333">Le symbole reste défini jusqu’à `#undef` ce qu’une directive pour ce même symbole soit traitée ou jusqu’à ce que la fin du fichier source soit atteinte.</span><span class="sxs-lookup"><span data-stu-id="b0969-333">The symbol remains defined until an `#undef` directive for that same symbol is processed, or until the end of the source file is reached.</span></span> <span data-ttu-id="b0969-334">L’une des conséquences est que `#define` les `#undef` directives et dans un fichier source n’ont aucun effet sur les autres fichiers sources du même programme.</span><span class="sxs-lookup"><span data-stu-id="b0969-334">An implication of this is that `#define` and `#undef` directives in one source file have no effect on other source files in the same program.</span></span>

<span data-ttu-id="b0969-335">Lorsqu’il est référencé dans une expression de pré-traitement, un symbole de compilation conditionnelle défini a `true`la valeur booléenne et un symbole de compilation conditionnelle non défini a la `false`valeur booléenne.</span><span class="sxs-lookup"><span data-stu-id="b0969-335">When referenced in a pre-processing expression, a defined conditional compilation symbol has the boolean value `true`, and an undefined conditional compilation symbol has the boolean value `false`.</span></span> <span data-ttu-id="b0969-336">Il n’est pas nécessaire que les symboles de compilation conditionnelle soient explicitement déclarés avant d’être référencés dans des expressions de prétraitement.</span><span class="sxs-lookup"><span data-stu-id="b0969-336">There is no requirement that conditional compilation symbols be explicitly declared before they are referenced in pre-processing expressions.</span></span> <span data-ttu-id="b0969-337">Au lieu de cela, les symboles non déclarés sont simplement non définis et `false`ont donc la valeur.</span><span class="sxs-lookup"><span data-stu-id="b0969-337">Instead, undeclared symbols are simply undefined and thus have the value `false`.</span></span>

<span data-ttu-id="b0969-338">L’espace de noms pour les symboles de compilation conditionnelle est distinct et distinct de toutes les autres C# entités nommées dans un programme.</span><span class="sxs-lookup"><span data-stu-id="b0969-338">The name space for conditional compilation symbols is distinct and separate from all other named entities in a C# program.</span></span> <span data-ttu-id="b0969-339">Les symboles de compilation conditionnelle peuvent uniquement être référencés `#undef` dans `#define` les directives et et dans les expressions de prétraitement.</span><span class="sxs-lookup"><span data-stu-id="b0969-339">Conditional compilation symbols can only be referenced in `#define` and `#undef` directives and in pre-processing expressions.</span></span>

### <a name="pre-processing-expressions"></a><span data-ttu-id="b0969-340">Expressions de pré-traitement</span><span class="sxs-lookup"><span data-stu-id="b0969-340">Pre-processing expressions</span></span>

<span data-ttu-id="b0969-341">Les expressions de prétraitement peuvent se `#if` produire `#elif` dans les directives et.</span><span class="sxs-lookup"><span data-stu-id="b0969-341">Pre-processing expressions can occur in `#if` and `#elif` directives.</span></span> <span data-ttu-id="b0969-342">Les opérateurs `!`, `==`, `!=` et`||`sont autorisés dans les expressions de prétraitement, et les parenthèses peuvent être utilisées pour le regroupement. `&&`</span><span class="sxs-lookup"><span data-stu-id="b0969-342">The operators `!`, `==`, `!=`, `&&` and `||` are permitted in pre-processing expressions, and parentheses may be used for grouping.</span></span>

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

<span data-ttu-id="b0969-343">Lorsqu’il est référencé dans une expression de pré-traitement, un symbole de compilation conditionnelle défini a `true`la valeur booléenne et un symbole de compilation conditionnelle non défini a la `false`valeur booléenne.</span><span class="sxs-lookup"><span data-stu-id="b0969-343">When referenced in a pre-processing expression, a defined conditional compilation symbol has the boolean value `true`, and an undefined conditional compilation symbol has the boolean value `false`.</span></span>

<span data-ttu-id="b0969-344">L’évaluation d’une expression de prétraitement génère toujours une valeur booléenne.</span><span class="sxs-lookup"><span data-stu-id="b0969-344">Evaluation of a pre-processing expression always yields a boolean value.</span></span> <span data-ttu-id="b0969-345">Les règles d’évaluation pour une expression de prétraitement sont les mêmes que celles d’une expression constante ([expressions constantes](expressions.md#constant-expressions)), sauf que les seules entités définies par l’utilisateur qui peuvent être référencées sont des symboles de compilation conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="b0969-345">The rules of evaluation for a pre-processing expression are the same as those for a constant expression ([Constant expressions](expressions.md#constant-expressions)), except that the only user-defined entities that can be referenced are conditional compilation symbols.</span></span>

### <a name="declaration-directives"></a><span data-ttu-id="b0969-346">Directives de déclaration</span><span class="sxs-lookup"><span data-stu-id="b0969-346">Declaration directives</span></span>

<span data-ttu-id="b0969-347">Les directives de déclaration sont utilisées pour définir ou annuler la définition des symboles de compilation conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="b0969-347">The declaration directives are used to define or undefine conditional compilation symbols.</span></span>

```antlr
pp_declaration
    : whitespace? '#' whitespace? 'define' whitespace conditional_symbol pp_new_line
    | whitespace? '#' whitespace? 'undef' whitespace conditional_symbol pp_new_line
    ;

pp_new_line
    : whitespace? single_line_comment? new_line
    ;
```

<span data-ttu-id="b0969-348">Le traitement d’une `#define` directive a pour effet de définir le symbole de compilation conditionnelle donné, en commençant par la ligne source qui suit la directive.</span><span class="sxs-lookup"><span data-stu-id="b0969-348">The processing of a `#define` directive causes the given conditional compilation symbol to become defined, starting with the source line that follows the directive.</span></span> <span data-ttu-id="b0969-349">De même, le traitement d' `#undef` une directive rend le symbole de compilation conditionnelle donné non défini, en commençant par la ligne source qui suit la directive.</span><span class="sxs-lookup"><span data-stu-id="b0969-349">Likewise, the processing of an `#undef` directive causes the given conditional compilation symbol to become undefined, starting with the source line that follows the directive.</span></span>

<span data-ttu-id="b0969-350">Les `#define` directives `#undef` et dans un fichier source doivent se trouver avant le premier *jeton* ([jetons](lexical-structure.md#tokens)) dans le fichier source ; sinon, une erreur de compilation se produit.</span><span class="sxs-lookup"><span data-stu-id="b0969-350">Any `#define` and `#undef` directives in a source file must occur before the first *token* ([Tokens](lexical-structure.md#tokens)) in the source file; otherwise a compile-time error occurs.</span></span> <span data-ttu-id="b0969-351">En termes intuitifs `#define` , `#undef` les directives et doivent précéder tout « code réel » dans le fichier source.</span><span class="sxs-lookup"><span data-stu-id="b0969-351">In intuitive terms, `#define` and `#undef` directives must precede any "real code" in the source file.</span></span>

<span data-ttu-id="b0969-352">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="b0969-352">The example:</span></span>
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
<span data-ttu-id="b0969-353">est valide, car `#define` les directives précèdent le premier `namespace` jeton (le mot clé) dans le fichier source.</span><span class="sxs-lookup"><span data-stu-id="b0969-353">is valid because the `#define` directives precede the first token (the `namespace` keyword) in the source file.</span></span>

<span data-ttu-id="b0969-354">L’exemple suivant génère une erreur au moment de la compilation, `#define` car un code réel suit :</span><span class="sxs-lookup"><span data-stu-id="b0969-354">The following example results in a compile-time error because a `#define` follows real code:</span></span>
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

<span data-ttu-id="b0969-355">Un `#define` peut définir un symbole de compilation conditionnelle qui est déjà défini, sans qu’il n’y `#undef` ait d’intermédiaire pour ce symbole.</span><span class="sxs-lookup"><span data-stu-id="b0969-355">A `#define` may define a conditional compilation symbol that is already defined, without there being any intervening `#undef` for that symbol.</span></span> <span data-ttu-id="b0969-356">L’exemple ci-dessous définit un symbole `A` de compilation conditionnelle, puis le définit à nouveau.</span><span class="sxs-lookup"><span data-stu-id="b0969-356">The example below defines a conditional compilation symbol `A` and then defines it again.</span></span>
```csharp
#define A
#define A
```

<span data-ttu-id="b0969-357">Un `#undef` peut « annuler la définition » d’un symbole de compilation conditionnelle qui n’est pas défini.</span><span class="sxs-lookup"><span data-stu-id="b0969-357">A `#undef` may "undefine" a conditional compilation symbol that is not defined.</span></span> <span data-ttu-id="b0969-358">L’exemple ci-dessous définit un symbole `A` de compilation conditionnelle, puis l’annule deux fois ; `#undef` bien que le second n’ait aucun effet, il est toujours valide.</span><span class="sxs-lookup"><span data-stu-id="b0969-358">The example below defines a conditional compilation symbol `A` and then undefines it twice; although the second `#undef` has no effect, it is still valid.</span></span>
```csharp
#define A
#undef A
#undef A
```

### <a name="conditional-compilation-directives"></a><span data-ttu-id="b0969-359">Directives de compilation conditionnelle</span><span class="sxs-lookup"><span data-stu-id="b0969-359">Conditional compilation directives</span></span>

<span data-ttu-id="b0969-360">Les directives de compilation conditionnelle permettent d’inclure ou d’exclure de manière conditionnelle des parties d’un fichier source.</span><span class="sxs-lookup"><span data-stu-id="b0969-360">The conditional compilation directives are used to conditionally include or exclude portions of a source file.</span></span>

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

<span data-ttu-id="b0969-361">Comme indiqué par la syntaxe, les directives de compilation conditionnelle doivent être écrites sous la forme de jeux composés de `#if` , dans l’ordre, d’une directive, de `#else` zéro, d’une `#endif` ou de plusieurs `#elif` directives, de zéro ou d’une directive et d’une directive.</span><span class="sxs-lookup"><span data-stu-id="b0969-361">As indicated by the syntax, conditional compilation directives must be written as sets consisting of, in order, an `#if` directive, zero or more `#elif` directives, zero or one `#else` directive, and an `#endif` directive.</span></span> <span data-ttu-id="b0969-362">Les sections conditionnelles du code source sont placées entre les directives.</span><span class="sxs-lookup"><span data-stu-id="b0969-362">Between the directives are conditional sections of source code.</span></span> <span data-ttu-id="b0969-363">Chaque section est contrôlée par la directive qui précède immédiatement.</span><span class="sxs-lookup"><span data-stu-id="b0969-363">Each section is controlled by the immediately preceding directive.</span></span> <span data-ttu-id="b0969-364">Une section conditionnelle peut elle-même contenir des directives de compilation conditionnelle imbriquées, à condition que ces directives forment des jeux complets.</span><span class="sxs-lookup"><span data-stu-id="b0969-364">A conditional section may itself contain nested conditional compilation directives provided these directives form complete sets.</span></span>

<span data-ttu-id="b0969-365">Un *pp_conditional* sélectionne au plus l’un des *conditional_section*contenus dans le traitement lexical normal :</span><span class="sxs-lookup"><span data-stu-id="b0969-365">A *pp_conditional* selects at most one of the contained *conditional_section*s for normal lexical processing:</span></span>

*  <span data-ttu-id="b0969-366">Les *pp_expression*s des directives `#if` et `#elif` sont `true`évaluées dans l’ordre jusqu’à ce que l’un d’eux produise.</span><span class="sxs-lookup"><span data-stu-id="b0969-366">The *pp_expression*s of the `#if` and `#elif` directives are evaluated in order until one yields `true`.</span></span> <span data-ttu-id="b0969-367">Si une expression génère `true`, le *conditional_section* de la directive correspondante est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="b0969-367">If an expression yields `true`, the *conditional_section* of the corresponding directive is selected.</span></span>
*  <span data-ttu-id="b0969-368">Si tous les *pp_expression*s `false`sont générés et `#else` , si une directive est présente, le `#else` *conditional_section* de la directive est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="b0969-368">If all *pp_expression*s yield `false`, and if an `#else` directive is present, the *conditional_section* of the `#else` directive is selected.</span></span>
*  <span data-ttu-id="b0969-369">Dans le cas contraire, aucun *conditional_section* n’est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="b0969-369">Otherwise, no *conditional_section* is selected.</span></span>

<span data-ttu-id="b0969-370">Le *conditional_section*sélectionné, le cas échéant, est traité comme un *input_section*normal : le code source contenu dans la section doit adhérer à la grammaire lexicale. les jetons sont générés à partir du code source dans la section. et les directives de pré-traitement dans la section ont les effets imposés.</span><span class="sxs-lookup"><span data-stu-id="b0969-370">The selected *conditional_section*, if any, is processed as a normal *input_section*: the source code contained in the section must adhere to the lexical grammar; tokens are generated from the source code in the section; and pre-processing directives in the section have the prescribed effects.</span></span>

<span data-ttu-id="b0969-371">Les *conditional_section*restants, le cas échéant, sont traités comme des *skipped_section*s : à l’exception des directives de prétraitement, le code source de la section ne doit pas être conforme à la grammaire lexicale. aucun jeton n’est généré à partir du code source dans la section ; et les directives de prétraitement dans la section doivent être lexicalement correctes, mais elles ne sont pas traitées autrement.</span><span class="sxs-lookup"><span data-stu-id="b0969-371">The remaining *conditional_section*s, if any, are processed as *skipped_section*s: except for pre-processing directives, the source code in the section need not adhere to the lexical grammar; no tokens are generated from the source code in the section; and pre-processing directives in the section must be lexically correct but are not otherwise processed.</span></span> <span data-ttu-id="b0969-372">Au sein d’un *conditional_section* qui est traité en tant que *skipped_section*, tous les *conditional_section*imbriqués `#if`(contenues dans les objets imbriqués... `#endif` et`#region`... les constructions) sont également traitées en tant que skipped_section s. `#endregion`</span><span class="sxs-lookup"><span data-stu-id="b0969-372">Within a *conditional_section* that is being processed as a *skipped_section*, any nested *conditional_section*s (contained in nested `#if`...`#endif` and `#region`...`#endregion` constructs) are also processed as *skipped_section*s.</span></span>

<span data-ttu-id="b0969-373">L’exemple suivant illustre la façon dont les directives de compilation conditionnelle peuvent imbriquer :</span><span class="sxs-lookup"><span data-stu-id="b0969-373">The following example illustrates how conditional compilation directives can nest:</span></span>
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

<span data-ttu-id="b0969-374">À l’exception des directives de pré-traitement, le code source ignoré n’est pas soumis à l’analyse lexicale.</span><span class="sxs-lookup"><span data-stu-id="b0969-374">Except for pre-processing directives, skipped source code is not subject to lexical analysis.</span></span> <span data-ttu-id="b0969-375">Par exemple, le code suivant est valide malgré le Commentaire inachevé dans la `#else` section :</span><span class="sxs-lookup"><span data-stu-id="b0969-375">For example, the following is valid despite the unterminated comment in the `#else` section:</span></span>
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

<span data-ttu-id="b0969-376">Notez, toutefois, que les directives de pré-traitement doivent être correctes lexicalement, même dans les sections ignorées du code source.</span><span class="sxs-lookup"><span data-stu-id="b0969-376">Note, however, that pre-processing directives are required to be lexically correct even in skipped sections of source code.</span></span>

<span data-ttu-id="b0969-377">Les directives de pré-traitement ne sont pas traitées quand elles apparaissent dans des éléments d’entrée multiligne.</span><span class="sxs-lookup"><span data-stu-id="b0969-377">Pre-processing directives are not processed when they appear inside multi-line input elements.</span></span> <span data-ttu-id="b0969-378">Par exemple, le programme :</span><span class="sxs-lookup"><span data-stu-id="b0969-378">For example, the program:</span></span>
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
<span data-ttu-id="b0969-379">génère la sortie :</span><span class="sxs-lookup"><span data-stu-id="b0969-379">results in the output:</span></span>
```
hello,
#if Debug
        world
#else
        Nebraska
#endif
```

<span data-ttu-id="b0969-380">Dans les cas particuliers, l’ensemble de directives de prétraitement qui est traité peut dépendre de l’évaluation de *pp_expression*.</span><span class="sxs-lookup"><span data-stu-id="b0969-380">In peculiar cases, the set of pre-processing directives that is processed might depend on the evaluation of the *pp_expression*.</span></span> <span data-ttu-id="b0969-381">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="b0969-381">The example:</span></span>
```csharp
#if X
    /*
#else
    /* */ class Q { }
#endif
```
<span data-ttu-id="b0969-382">produit toujours le même flux de jetons`class` ( `}` `Q` `{` ), qu’il soit défini `X` ou non.</span><span class="sxs-lookup"><span data-stu-id="b0969-382">always produces the same token stream (`class` `Q` `{` `}`), regardless of whether or not `X` is defined.</span></span> <span data-ttu-id="b0969-383">Si `X` est défini, les seules directives traitées sont `#if` et `#endif`, en raison du commentaire sur plusieurs lignes.</span><span class="sxs-lookup"><span data-stu-id="b0969-383">If `X` is defined, the only processed directives are `#if` and `#endif`, due to the multi-line comment.</span></span> <span data-ttu-id="b0969-384">Si `X` n’est pas défini, alors trois directives (`#if`, `#else`, `#endif`) font partie de la directive définie.</span><span class="sxs-lookup"><span data-stu-id="b0969-384">If `X` is undefined, then three directives (`#if`, `#else`, `#endif`) are part of the directive set.</span></span>

### <a name="diagnostic-directives"></a><span data-ttu-id="b0969-385">Directives de diagnostic</span><span class="sxs-lookup"><span data-stu-id="b0969-385">Diagnostic directives</span></span>

<span data-ttu-id="b0969-386">Les directives de diagnostic sont utilisées pour générer explicitement des messages d’erreur et d’avertissement signalés de la même façon que d’autres erreurs et avertissements au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="b0969-386">The diagnostic directives are used to explicitly generate error and warning messages that are reported in the same way as other compile-time errors and warnings.</span></span>

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

<span data-ttu-id="b0969-387">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="b0969-387">The example:</span></span>
```csharp
#warning Code review needed before check-in

#if Debug && Retail
    #error A build can't be both debug and retail
#endif

class Test {...}
```
<span data-ttu-id="b0969-388">génère toujours un avertissement (« révision du code nécessaire avant l’archivage ») et génère une erreur au moment de la compilation (« une build ne peut pas être à la fois Debug et Retail `Retail` ») si les symboles `Debug` conditionnels et sont tous deux définis.</span><span class="sxs-lookup"><span data-stu-id="b0969-388">always produces a warning ("Code review needed before check-in"), and produces a compile-time error ("A build can't be both debug and retail") if the conditional symbols `Debug` and `Retail` are both defined.</span></span> <span data-ttu-id="b0969-389">Notez qu’un *pp_message* peut contenir du texte arbitraire. plus précisément, il n’a pas besoin de contenir des jetons correctement formés, comme indiqué par le guillemet simple `can't`dans le mot.</span><span class="sxs-lookup"><span data-stu-id="b0969-389">Note that a *pp_message* can contain arbitrary text; specifically, it need not contain well-formed tokens, as shown by the single quote in the word `can't`.</span></span>

### <a name="region-directives"></a><span data-ttu-id="b0969-390">Directives de région</span><span class="sxs-lookup"><span data-stu-id="b0969-390">Region directives</span></span>

<span data-ttu-id="b0969-391">Les directives de région sont utilisées pour marquer explicitement les zones de code source.</span><span class="sxs-lookup"><span data-stu-id="b0969-391">The region directives are used to explicitly mark regions of source code.</span></span>

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

<span data-ttu-id="b0969-392">Aucune signification sémantique n’est attachée à une région ; les régions sont destinées à être utilisées par le programmeur ou par des outils automatisés pour marquer une section du code source.</span><span class="sxs-lookup"><span data-stu-id="b0969-392">No semantic meaning is attached to a region; regions are intended for use by the programmer or by automated tools to mark a section of source code.</span></span> <span data-ttu-id="b0969-393">Le message spécifié dans une `#region` directive `#endregion` ou n’a pas la même signification sémantique ; il sert simplement à identifier la région.</span><span class="sxs-lookup"><span data-stu-id="b0969-393">The message specified in a `#region` or `#endregion` directive likewise has no semantic meaning; it merely serves to identify the region.</span></span> <span data-ttu-id="b0969-394">Les directives `#endregion` et peuvent avoir différents *pp_message*s. `#region`</span><span class="sxs-lookup"><span data-stu-id="b0969-394">Matching `#region` and `#endregion` directives may have different *pp_message*s.</span></span>

<span data-ttu-id="b0969-395">Traitement lexical d’une région :</span><span class="sxs-lookup"><span data-stu-id="b0969-395">The lexical processing of a region:</span></span>
```csharp
#region
...
#endregion
```
<span data-ttu-id="b0969-396">correspond exactement au traitement lexical d’une directive de compilation conditionnelle au format suivant :</span><span class="sxs-lookup"><span data-stu-id="b0969-396">corresponds exactly to the lexical processing of a conditional compilation directive of the form:</span></span>
```csharp
#if true
...
#endif
```

### <a name="line-directives"></a><span data-ttu-id="b0969-397">Directives de ligne</span><span class="sxs-lookup"><span data-stu-id="b0969-397">Line directives</span></span>

<span data-ttu-id="b0969-398">Les directives de ligne peuvent être utilisées pour modifier les numéros de ligne et les noms de fichiers sources qui sont signalés par le compilateur dans la sortie, tels que les avertissements et les erreurs, et qui sont utilisés par les attributs d’informations de l’appelant ([attributs des informations](attributes.md#caller-info-attributes)de l’appelant).</span><span class="sxs-lookup"><span data-stu-id="b0969-398">Line directives may be used to alter the line numbers and source file names that are reported by the compiler in output such as warnings and errors, and that are used by caller info attributes ([Caller info attributes](attributes.md#caller-info-attributes)).</span></span>

<span data-ttu-id="b0969-399">Les directives de ligne sont le plus souvent utilisées dans les outils de C# méta-programmation qui génèrent du code source à partir d’autres entrées de texte.</span><span class="sxs-lookup"><span data-stu-id="b0969-399">Line directives are most commonly used in meta-programming tools that generate C# source code from some other text input.</span></span>

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

<span data-ttu-id="b0969-400">Quand aucune `#line` directive n’est présente, le compilateur signale les véritables numéros de ligne et les noms de fichiers sources dans sa sortie.</span><span class="sxs-lookup"><span data-stu-id="b0969-400">When no `#line` directives are present, the compiler reports true line numbers and source file names in its output.</span></span> <span data-ttu-id="b0969-401">Lors du traitement `#line` d’une directive qui comprend un *line_indicator* qui `default`n’est pas, le compilateur traite la ligne après la directive comme ayant le numéro de ligne donné (et le nom de fichier, si spécifié).</span><span class="sxs-lookup"><span data-stu-id="b0969-401">When processing a `#line` directive that includes a *line_indicator* that is not `default`, the compiler treats the line after the directive as having the given line number (and file name, if specified).</span></span>

<span data-ttu-id="b0969-402">Une `#line default` directive inverse l’effet de toutes les directives de #line précédentes.</span><span class="sxs-lookup"><span data-stu-id="b0969-402">A `#line default` directive reverses the effect of all preceding #line directives.</span></span> <span data-ttu-id="b0969-403">Le compilateur signale des informations de ligne vraies pour les lignes suivantes, `#line` exactement comme si aucune directive n’avait été traitée.</span><span class="sxs-lookup"><span data-stu-id="b0969-403">The compiler reports true line information for subsequent lines, precisely as if no `#line` directives had been processed.</span></span>

<span data-ttu-id="b0969-404">Une `#line hidden` directive n’a aucun effet sur le fichier et les numéros de ligne signalés dans les messages d’erreur, mais affecte le débogage au niveau de la source.</span><span class="sxs-lookup"><span data-stu-id="b0969-404">A `#line hidden` directive has no effect on the file and line numbers reported in error messages, but does affect source level debugging.</span></span> <span data-ttu-id="b0969-405">Lors du débogage, toutes les lignes entre `#line hidden` une directive et la `#line` directive suivante (qui n' `#line hidden`est pas) n’ont pas d’informations sur le numéro de ligne.</span><span class="sxs-lookup"><span data-stu-id="b0969-405">When debugging, all lines between a `#line hidden` directive and the subsequent `#line` directive (that is not `#line hidden`) have no line number information.</span></span> <span data-ttu-id="b0969-406">Quand vous exécutez le code pas à pas dans le débogueur, ces lignes sont entièrement ignorées.</span><span class="sxs-lookup"><span data-stu-id="b0969-406">When stepping through code in the debugger, these lines will be skipped entirely.</span></span>

<span data-ttu-id="b0969-407">Notez qu’un *nom_fichier* diffère d’un littéral de chaîne standard en ce que les caractères d’échappement ne sont pas traités ; le caractère`\`«» désigne simplement un caractère barre oblique inverse ordinaire dans un *nom_fichier*.</span><span class="sxs-lookup"><span data-stu-id="b0969-407">Note that a *file_name* differs from a regular string literal in that escape characters are not processed; the "`\`" character simply designates an ordinary backslash character within a *file_name*.</span></span>

### <a name="pragma-directives"></a><span data-ttu-id="b0969-408">Directives pragma</span><span class="sxs-lookup"><span data-stu-id="b0969-408">Pragma directives</span></span>

<span data-ttu-id="b0969-409">La `#pragma` directive de prétraitement est utilisée pour spécifier des informations contextuelles facultatives pour le compilateur.</span><span class="sxs-lookup"><span data-stu-id="b0969-409">The `#pragma` preprocessing directive is used to specify optional contextual information to the compiler.</span></span> <span data-ttu-id="b0969-410">Les informations fournies dans une `#pragma` directive ne modifient jamais la sémantique du programme.</span><span class="sxs-lookup"><span data-stu-id="b0969-410">The information supplied in a `#pragma` directive will never change program semantics.</span></span>

```antlr
pp_pragma
    : whitespace? '#' whitespace? 'pragma' whitespace pragma_body pp_new_line
    ;

pragma_body
    : pragma_warning_body
    ;
```

<span data-ttu-id="b0969-411">C#fournit `#pragma` des directives pour contrôler les avertissements du compilateur.</span><span class="sxs-lookup"><span data-stu-id="b0969-411">C# provides `#pragma` directives to control compiler warnings.</span></span> <span data-ttu-id="b0969-412">Les futures versions du langage peuvent inclure des `#pragma` directives supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="b0969-412">Future versions of the language may include additional `#pragma` directives.</span></span> <span data-ttu-id="b0969-413">Pour garantir l’interopérabilité avec C# d’autres compilateurs, le C# compilateur Microsoft n’émet pas d’erreurs de `#pragma` compilation pour les directives inconnues ; ces directives génèrent toutefois des avertissements.</span><span class="sxs-lookup"><span data-stu-id="b0969-413">To ensure interoperability with other C# compilers, the Microsoft C# compiler does not issue compilation errors for unknown `#pragma` directives; such directives do however generate warnings.</span></span>

#### <a name="pragma-warning"></a><span data-ttu-id="b0969-414">pragma warning</span><span class="sxs-lookup"><span data-stu-id="b0969-414">Pragma warning</span></span>

<span data-ttu-id="b0969-415">La `#pragma warning` directive est utilisée pour désactiver ou restaurer tout ou un ensemble particulier de messages d’avertissement lors de la compilation du texte de programme suivant.</span><span class="sxs-lookup"><span data-stu-id="b0969-415">The `#pragma warning` directive is used to disable or restore all or a particular set of warning messages during compilation of the subsequent program text.</span></span>

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

<span data-ttu-id="b0969-416">Une `#pragma warning` directive qui omet la liste d’avertissements affecte tous les avertissements.</span><span class="sxs-lookup"><span data-stu-id="b0969-416">A `#pragma warning` directive that omits the warning list affects all warnings.</span></span> <span data-ttu-id="b0969-417">Une `#pragma warning` directive qui comprend une liste d’avertissements affecte uniquement les avertissements qui sont spécifiés dans la liste.</span><span class="sxs-lookup"><span data-stu-id="b0969-417">A `#pragma warning` directive that includes a warning list affects only those warnings that are specified in the list.</span></span>

<span data-ttu-id="b0969-418">Une `#pragma warning disable` directive désactive tout ou l’ensemble d’avertissements donné.</span><span class="sxs-lookup"><span data-stu-id="b0969-418">A `#pragma warning disable` directive disables all or the given set of warnings.</span></span>

<span data-ttu-id="b0969-419">Une `#pragma warning restore` directive restaure tout ou partie des avertissements donnés à l’État qui était en vigueur au début de l’unité de compilation.</span><span class="sxs-lookup"><span data-stu-id="b0969-419">A `#pragma warning restore` directive restores all or the given set of warnings to the state that was in effect at the beginning of the compilation unit.</span></span> <span data-ttu-id="b0969-420">Notez que si un avertissement particulier a été désactivé en externe, `#pragma warning restore` a (que ce soit pour tous ou l’avertissement spécifique) ne réactivera pas cet avertissement.</span><span class="sxs-lookup"><span data-stu-id="b0969-420">Note that if a particular warning was disabled externally, a `#pragma warning restore` (whether for all or the specific warning) will not re-enable that warning.</span></span>

<span data-ttu-id="b0969-421">L’exemple suivant illustre l’utilisation `#pragma warning` de pour désactiver temporairement l’avertissement signalé lorsque des membres obsolètes sont référencés, à l’aide du numéro C# d’avertissement du compilateur Microsoft.</span><span class="sxs-lookup"><span data-stu-id="b0969-421">The following example shows use of `#pragma warning` to temporarily disable the warning reported when obsoleted members are referenced, using the warning number from the Microsoft C# compiler.</span></span>
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
