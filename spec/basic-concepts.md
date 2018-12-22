# <a name="basic-concepts"></a>Concepts de base

## <a name="application-startup"></a>Démarrage de l'application

Un assembly qui a un ***point d’entrée*** est appelée un ***application***. Lorsqu’une application est exécutée, un nouveau ***domaine d’application*** est créé. Plusieurs instanciations différentes d’une application peuvent exister sur le même ordinateur en même temps, et chacun possède son propre domaine d’application.

Un domaine d’application permet l’isolement de l’application en agissant en tant que conteneur pour l’état de l’application. Un domaine d’application agit comme un conteneur et les limites pour les types définis dans l’application et il utilise les bibliothèques de classes. Les types chargés dans un domaine d’application sont distincts à partir du même type chargé dans un autre domaine d’application, et les instances d’objets ne sont pas directement partagées entre les domaines d’application. Par exemple, chaque domaine d’application a sa propre copie des variables statiques pour ces types, et un constructeur statique pour un type est exécuté au maximum une fois par domaine d’application. Les implémentations sont libres de fournir des mécanismes ou une stratégie de spécifique à l’implémentation pour la création et la destruction des domaines d’application.

***Démarrage de l’application*** se produit lorsque l’environnement d’exécution appelle une méthode désignée, ce qui correspond au point d’entrée de l’application. Cette méthode de point d’entrée est toujours nommée `Main`et peut prendre l’une des signatures suivantes :

```csharp
static void Main() {...}

static void Main(string[] args) {...}

static int Main() {...}

static int Main(string[] args) {...}
```

Comme indiqué, le point d’entrée peut éventuellement retourner un `int` valeur. Cela retourne la valeur est utilisée dans l’arrêt de l’application ([arrêt d’une Application](basic-concepts.md#application-termination)).

Le point d’entrée peut éventuellement inclure un paramètre formel. Le paramètre peut avoir n’importe quel nom, mais le type du paramètre doit être `string[]`. Si le paramètre formel est présent, l’environnement d’exécution crée et passe un `string[]` argument contenant les arguments de ligne de commande qui ont été spécifié quand l’application a été démarrée. Le `string[]` argument n’est jamais null, mais cela peut avoir une longueur de zéro si aucun argument de ligne de commande a été spécifiée.

Étant donné que c# prend en charge de la surcharge de méthode, une classe ou un struct peut contenir plusieurs définitions de certaines méthodes, fourni chacun comporte une signature différente. Toutefois, au sein d’un programme unique, aucune classe ou un struct ne peut contenir des plus d’une méthode appelée `Main` dont la définition est éligible pour être utilisé comme point d’entrée d’application. Autres versions surchargées de `Main` sont autorisés, toutefois, s’ils ont plusieurs paramètres, ou son seul paramètre est autre que de type `string[]`.

Une application peut être constituée de plusieurs classes ou structs. Il est possible pour plusieurs de ces classes ou structs peuvent contenir une méthode appelée `Main` dont la définition est éligible pour être utilisé comme point d’entrée d’application. Dans ce cas, un mécanisme externe (par exemple, une option du compilateur de ligne de commande) doit être utilisé pour sélectionner l’une de ces `Main` méthodes comme point d’entrée.

En c#, chaque méthode doit être définie en tant que membre d’une classe ou un struct. En règle générale, l’accessibilité déclarée ([accessibilité déclarée](basic-concepts.md#declared-accessibility)) d’une méthode est déterminée par les modificateurs d’accès ([modificateurs d’accès](classes.md#access-modifiers)) spécifié dans sa déclaration et même déclaré accessibilité d’un type est déterminée par les modificateurs d’accès spécifiés dans sa déclaration. Pour qu’une méthode donnée d’un type donné peut être appelé, le type et le membre doivent être accessibles. Toutefois, le point d’entrée de l’application est un cas spécial. Plus précisément, l’environnement d’exécution peut accéder aux point d’entrée de l’application, quel que soit son accessibilité déclarée et quel que soit l’accessibilité déclarée de ses déclarations de type englobant.

Méthode de point d’entrée de l’application ne peut pas être dans une déclaration de classe générique.

Dans tous les autres égards, les méthodes de point d’entrée se comportent comme ceux qui ne sont pas des points d’entrée.

## <a name="application-termination"></a>Arrêt de l’application

***Arrêt de l’application*** retourne le contrôle à l’environnement d’exécution.

Si le type de retour de l’application ***point d’entrée*** méthode est `int`, la valeur retournée sert de l’application ***code d’état arrêt***. L’objectif de ce code consiste à autoriser la communication de la réussite ou l’échec de l’environnement d’exécution.

Si le type de retour de la méthode de point d’entrée est `void`, atteindre l’accolade fermante (`}`) qui termine que méthode ou l’exécution d’un `return` instruction qui ne possède aucune expression, entraîne un code d’état d’arrêt `0`.

Avant l’arrêt d’une application, les destructeurs pour tous ses objets qui n’ont pas encore été garbage collecté sont appelés, à moins que le nettoyage de ce type a été supprimé (par un appel à la méthode de bibliothèque `GC.SuppressFinalize`, par exemple).

## <a name="declarations"></a>Déclarations

Les déclarations dans un programme c# définissent les éléments constitutifs du programme. Les programmes c# sont organisés à l’aide d’espaces de noms ([espaces de noms](namespaces.md)), qui peut contenir le type de déclarations et des déclarations d’espace de noms imbriqué. Les déclarations de type ([les déclarations de Type](namespaces.md#type-declarations)) sont utilisés pour définir les classes ([Classes](classes.md)), structs ([Structs](structs.md)), interfaces ([Interfaces](interfaces.md) ), enums ([Enums](enums.md)) et les délégués ([délégués](delegates.md)). Les types de membres autorisés dans une déclaration de type dépendent de la forme de la déclaration de type. Par exemple, les déclarations de classe peuvent contenir les déclarations des constantes ([constantes](classes.md#constants)), les champs ([champs](classes.md#fields)), méthodes ([méthodes](classes.md#methods)), propriétés ([ Propriétés](classes.md#properties)), événements ([événements](classes.md#events)), les indexeurs ([indexeurs](classes.md#indexers)), opérateurs ([opérateurs](classes.md#operators)), constructeurs d’instance ([ Constructeurs d’instance](classes.md#instance-constructors)), les constructeurs statiques ([constructeurs statiques](classes.md#static-constructors)), destructeurs ([destructeurs](classes.md#destructors)) et les types imbriqués ([detypesimbriqués](classes.md#nested-types)).

Une déclaration définit un nom dans la ***espace de déclaration*** auquel appartient la déclaration. À l’exception de surchargé membres ([Signatures et surcharge](basic-concepts.md#signatures-and-overloading)), c’est une erreur de compilation d’avoir deux déclarations ou plus introduisent des membres portant le même nom dans un espace de déclaration. Il n’est jamais possible pour un déclaration d’espace pour contenir différents types de membres portant le même nom. Par exemple, un espace de déclaration peut jamais contenir un champ et une méthode portant le même nom.

Il existe différents types d’espaces de déclaration, comme décrit dans le code suivant.

*  Dans tous les fichiers source d’un programme, *namespace_member_declaration*s aucun englobante *namespace_declaration* sont membres d’un espace de déclaration combiné unique appelé le ***global espace de déclaration***.
*  Dans tous les fichiers source d’un programme, *namespace_member_declaration*s dans *namespace_declaration*s qui ont le même nom d’espace de noms complet sont membres d’une déclaration combinée unique espace.
*  Chaque classe, un struct ou une déclaration d’interface crée un nouvel espace de déclaration. Les noms sont introduits dans cet espace de déclaration via *class_member_declaration*s, *struct_member_declaration*s, *interface_member_declaration*s, ou *type_parameter*s. À l’exception de constructeur d’instance surchargée déclarations et un constructeur statique déclarations, une classe ou un struct ne peut pas contenir une déclaration de membre avec le même nom que la classe ou structure. Une classe, un struct ou une interface permet la déclaration de méthodes surchargées et d’indexeurs. En outre, une classe ou un struct permet la déclaration des constructeurs d’instance surchargée et des opérateurs. Par exemple, une classe, un struct ou une interface peut contenir plusieurs déclarations de méthode portant le même nom, condition ces déclarations de méthode diffèrent par leur signature ([Signatures et surcharge](basic-concepts.md#signatures-and-overloading)). Notez que les classes de base ne contribuent pas à l’espace de déclaration d’une classe, et interfaces de base ne contribuent pas à l’espace de déclaration d’une interface. Par conséquent, une classe dérivée ou une interface est autorisé à déclarer un membre portant le même nom qu’un membre hérité. Ce membre est dite ***masquer*** le membre hérité.
*  Chaque déclaration de délégué crée un nouvel espace de déclaration. Les noms sont introduits dans cet espace de déclaration par le biais des paramètres formels (*fixed_parameter*s et *parameter_array*s) et *type_parameter*s.
*  Chaque déclaration d’énumération crée un nouvel espace de déclaration. Les noms sont introduits dans cet espace de déclaration via *enum_member_declarations*.
*  Chaque déclaration de méthode, la déclaration d’indexeur, la déclaration d’opérateur, la déclaration de constructeur d’instance et le fonction anonyme crée un nouvel espace de déclaration appelé un ***espace de déclaration de variable locale***. Les noms sont introduits dans cet espace de déclaration par le biais des paramètres formels (*fixed_parameter*s et *parameter_array*s) et *type_parameter*s. Le corps de la fonction membre ou une fonction anonyme, le cas échéant, est censé être imbriqué dans l’espace de déclaration de variable locale. C’est une erreur pour un espace de déclaration de variable locale et un espace de déclaration de variable locale imbriquée pour contenir les éléments portant le même nom. Par conséquent, au sein d’un espace de déclaration imbriqués qu’il n’est pas possible de déclarer une variable locale, variable ou constante portant le même nom qu’une variable locale ou constante dans un espace de déclaration englobante. Il est possible pour les deux espaces de déclaration contenir les éléments portant le même nom tant que ni espace de déclaration contient l’autre.
*  Chaque *bloc* ou *switch_block* , ainsi qu’un *pour*, *foreach* et *à l’aide de* instruction, crée un espace de déclaration de variable locale pour les variables locales et les constantes locales. Les noms sont introduits dans cet espace de déclaration via *local_variable_declaration*s et *local_constant_declaration*s. Notez que les blocs qui se produisent en tant qu’ou dans le corps d’une fonction membre ou une fonction anonyme sont imbriqués dans l’espace de déclaration de variable locale déclarée par ces fonctions pour leurs paramètres. Par conséquent, il est une erreur d’avoir par exemple, une méthode avec une variable locale et un paramètre du même nom.
*  Chaque *bloc* ou *switch_block* crée un espace de déclaration distincte pour les étiquettes. Les noms sont introduits dans cet espace de déclaration via *labeled_statement*s et les noms sont référencés par le biais *goto_statement*s. Le ***espace de déclaration de l’étiquette*** d’un bloc inclut tous les blocs imbriqués. Par conséquent, au sein d’un bloc imbriqué qu’il n’est pas possible de déclarer une étiquette avec le même nom qu’une étiquette dans un bloc englobant.

L’ordre textuel dans lequel les noms sont déclarés est généralement sans signification. En particulier, l’ordre textuel n’est pas significatif pour la déclaration et l’utilisation des espaces de noms, constantes, méthodes, propriétés, événements, indexeurs, opérateurs, constructeurs d’instance, destructeurs, constructeurs statiques et les types. Ordre de déclaration est significatif comme suit :

*  Ordre de déclaration des déclarations de champ et des déclarations de variables locales détermine l’ordre dans lequel sont exécutés leurs initialiseurs (le cas échéant).
*  Les variables locales doivent être définies avant d’être utilisés ([étendues](basic-concepts.md#scopes)).
*  Ordre de déclaration des déclarations de membre enum ([membres Enum](enums.md#enum-members)) est important quand *constant_expression* les valeurs sont omises.

L’espace de déclaration d’un espace de noms est « ouvert » et espace de noms deux déclarations portant le même nom qualifié complet contribuent au même espace de déclaration. Exemple :
```csharp
namespace Megacorp.Data
{
    class Customer
    {
        ...
    }
}

namespace Megacorp.Data
{
    class Order
    {
        ...
    }
}
```

les deux déclarations d’espace de noms contribuent au même espace de déclaration, dans ce cas déclare deux classes dont les noms qualifiés complets `Megacorp.Data.Customer` et `Megacorp.Data.Order`. Étant donné que les deux déclarations contribuent au même espace de déclaration, elle aurait entraîné une erreur de compilation si chacune contenait une déclaration d’une classe portant le même nom.

Comme indiqué ci-dessus, l’espace de déclaration d’un bloc inclut tous les blocs imbriqués. Par conséquent, dans l’exemple suivant, le `F` et `G` méthodes provoque une erreur de compilation, car le nom `i` est déclaré dans le bloc externe et ne peut pas être redéclaré dans le bloc interne. Toutefois, le `H` et `I` méthodes sont valides car les deux `i`de sont déclarés dans des blocs non imbriqués distincts.

```csharp
class A
{
    void F() {
        int i = 0;
        if (true) {
            int i = 1;            
        }
    }

    void G() {
        if (true) {
            int i = 0;
        }
        int i = 1;                
    }

    void H() {
        if (true) {
            int i = 0;
        }
        if (true) {
            int i = 1;
        }
    }

    void I() {
        for (int i = 0; i < 10; i++)
            H();
        for (int i = 0; i < 10; i++)
            H();
    }
}
```

## <a name="members"></a>Membres

Espaces de noms et les types ont ***membres***. Les membres d’une entité sont généralement disponibles via l’utilisation d’un nom qualifié qui commence avec une référence à l’entité, suivi par un «`.`» jeton, suivi du nom du membre.

Membres d’un type sont soit déclarés dans la déclaration de type ou ***héritée*** à partir de la classe de base du type. Lorsqu’un type hérite d’une classe de base, tous les membres de la classe de base, à l’exception des constructeurs d’instance, destructeurs et des constructeurs statiques, deviennent membres du type dérivé. L’accessibilité déclarée d’un membre de classe de base ne contrôle pas si le membre est hérité — l’héritage s’étend à tout membre qui n’est pas un constructeur d’instance, un constructeur statique ou un destructeur. Toutefois, un membre hérité ne peut pas être accessible dans un type dérivé, soit en raison de son accessibilité déclarée ([accessibilité déclarée](basic-concepts.md#declared-accessibility)) ou parce qu’il est masqué par une déclaration dans le type lui-même ([masquage par l’héritage](basic-concepts.md#hiding-through-inheritance)).

### <a name="namespace-members"></a>Membres de Namespace

Espaces de noms et les types qui n’ont aucun espace de noms englobant sont des membres de la ***espace de noms global***. Cela correspond directement aux noms déclarés dans l’espace de déclaration globale.

Espaces de noms et les types déclarés dans un espace de noms sont membres de cet espace de noms. Cela correspond directement aux noms déclarés dans l’espace de déclaration de l’espace de noms.

Les espaces de noms ne présentent aucune limitation d’accès. Il n’est pas possible de déclarer des espaces de noms privé, protégé ou interne, et les noms d’espace de noms sont toujours accessibles publiquement.

### <a name="struct-members"></a>Membres de struct

Les membres d’un struct sont les membres déclarés dans la structure et les membres hérités de la classe de base directe du struct `System.ValueType` et la classe de base indirecte `object`.

Les membres d’un type simple correspondent directement aux membres de l’alias de type struct par le type simple :

*  Les membres de `sbyte` sont les membres de la `System.SByte` struct.
*  Les membres de `byte` sont les membres de la `System.Byte` struct.
*  Les membres de `short` sont les membres de la `System.Int16` struct.
*  Les membres de `ushort` sont les membres de la `System.UInt16` struct.
*  Les membres de `int` sont les membres de la `System.Int32` struct.
*  Les membres de `uint` sont les membres de la `System.UInt32` struct.
*  Les membres de `long` sont les membres de la `System.Int64` struct.
*  Les membres de `ulong` sont les membres de la `System.UInt64` struct.
*  Les membres de `char` sont les membres de la `System.Char` struct.
*  Les membres de `float` sont les membres de la `System.Single` struct.
*  Les membres de `double` sont les membres de la `System.Double` struct.
*  Les membres de `decimal` sont les membres de la `System.Decimal` struct.
*  Les membres de `bool` sont les membres de la `System.Boolean` struct.

### <a name="enumeration-members"></a>Membres de l’énumération

Les membres d’une énumération sont les constantes déclarées dans l’énumération et les membres hérités de la classe de base directe de l’énumération `System.Enum` et les classes de base indirectes `System.ValueType` et `object`.

### <a name="class-members"></a>Membres de classe

Les membres d’une classe sont les membres déclarés dans la classe et les membres hérités de la classe de base (à l’exception de classe `object` qui dispose d’aucune classe de base). Les membres hérités de la classe de base incluent les constantes, champs, méthodes, propriétés, événements, indexeurs, opérateurs et types de la classe de base, mais pas les constructeurs d’instance, les destructeurs et les constructeurs statiques de la classe de base. Les membres de classe de base sont hérités sans tenir compte de leur accessibilité.

Une déclaration de classe peut contenir les déclarations de constantes, champs, méthodes, propriétés, événements, indexeurs, opérateurs, constructeurs d’instance, destructeurs, constructeurs statiques et types.

Les membres de `object` et `string` correspondent directement aux membres des types classe ils alias :

*  Les membres de `object` sont les membres de la `System.Object` classe.
*  Les membres de `string` sont les membres de la `System.String` classe.

### <a name="interface-members"></a>Membres de l’interface

Les membres d’une interface sont les membres déclarés dans l’interface et dans toutes les interfaces de base de l’interface. Les membres de classe `object` ne sont pas, strictement parlant, les membres de n’importe quelle interface ([membres de l’Interface](interfaces.md#interface-members)). Toutefois, les membres de classe `object` sont disponibles via la recherche de membres dans n’importe quel type d’interface ([recherche de membres](expressions.md#member-lookup)).

### <a name="array-members"></a>Membres de tableau

Les membres d’un tableau sont les membres hérités de la classe `System.Array`.

### <a name="delegate-members"></a>Membres de délégué

Les membres d’un délégué sont les membres hérités de la classe `System.Delegate`.

## <a name="member-access"></a>Accès au membre

Déclarations des membres permettent de contrôler l’accès au membre. L’accessibilité d’un membre est établie par l’accessibilité déclarée ([accessibilité déclarée](basic-concepts.md#declared-accessibility)) du membre combinée avec l’accessibilité du type conteneur immédiat, le cas échéant.

Lorsque l’accès à un membre particulier est autorisé, le membre est dit être ***accessible***. À l’inverse, lors de l’accès à un membre particulier n’est pas autorisée, le membre est dite ***inaccessible***. L’accès à un membre est autorisé lorsque l’emplacement textuel dans lequel l’accès a lieu est inclus dans le domaine d’accessibilité ([domaines d’accessibilité](basic-concepts.md#accessibility-domains)) du membre.

### <a name="declared-accessibility"></a>Accessibilité déclarée

Le ***accessibilité déclarée*** d’un membre peut prendre l’une des opérations suivantes :

*  Public, ce qui est sélectionnée en incluant un `public` modificateur dans la déclaration de membre. La signification intuitive de `public` est « accès non limité ».
*  Protégé, qui est sélectionnée en incluant un `protected` modificateur dans la déclaration de membre. La signification intuitive de `protected` est « accès limité à la classe conteneur ou aux types dérivés de la classe conteneur ».
*  Interne, qui est sélectionnée en incluant un `internal` modificateur dans la déclaration de membre. La signification intuitive de `internal` est « accès limité à ce programme ».
*  Protégée interne (ce qui signifie protected ou internal), qui est sélectionnée en incluant à la fois un `protected` et un `internal` modificateur dans la déclaration de membre. La signification intuitive de `protected internal` est « accès limité à ce programme ou aux types dérivés de la classe conteneur ».
*  Privé, ce qui est sélectionnée en incluant un `private` modificateur dans la déclaration de membre. La signification intuitive de `private` est « accès limité au type conteneur ».

Selon le contexte dans lequel une déclaration de membre, seuls certains types d’accessibilité déclarée sont autorisés. En outre, lorsqu’une déclaration de membre n’inclut pas les modificateurs d’accès, le contexte dans lequel la déclaration a lieu détermine l’accessibilité déclarée par défaut.

*  Espaces de noms ont implicitement `public` accessibilité déclarée. Aucun modificateur d’accès n’est autorisés dans les déclarations d’espace de noms.
*  Les types déclarés dans les unités de compilation ou d’espaces de noms peuvent avoir `public` ou `internal` déclaré l’accessibilité et la valeur par défaut `internal` accessibilité déclarée.
*  Membres de classe peuvent avoir un des cinq types d’accessibilité déclarée et la valeur par défaut `private` accessibilité déclarée. (Notez qu’un type déclaré comme un membre d’une classe peut avoir un des cinq types d’accessibilité déclarée, alors qu’un type déclaré comme un membre d’un espace de noms peut avoir uniquement `public` ou `internal` accessibilité déclarée.)
*  Les membres de struct peuvent avoir `public`, `internal`, ou `private` déclaré l’accessibilité et la valeur par défaut `private` accessibilité déclarée, car les structs sont implicitement sealed. Les membres struct introduits dans un struct (c'est-à-dire, non hérité de ce struct) ne peut pas avoir `protected` ou `protected internal` accessibilité déclarée. (Notez qu’un type déclaré comme un membre d’un struct peut avoir `public`, `internal`, ou `private` déclaré d’accessibilité, alors qu’un type déclaré comme un membre d’un espace de noms peut avoir uniquement `public` ou `internal` accessibilité déclarée.)
*  Membres d’interface ont implicitement `public` accessibilité déclarée. Aucun modificateur d’accès n’est autorisés dans les déclarations de membre d’interface.
*  Membres d’énumération ont implicitement `public` accessibilité déclarée. Aucun modificateur d’accès n’est autorisées sur les déclarations de membre d’énumération.

### <a name="accessibility-domains"></a>Domaines d’accessibilité

Le ***domaine d’accessibilité*** d’un membre se compose des sections (éventuellement séparées) du texte de programme dans lequel l’accès au membre est autorisé. Pour des raisons de définir le domaine d’accessibilité d’un membre, un membre est dite ***niveau supérieur*** si elle n’est pas déclarée dans un type et un membre est dite ***imbriquée*** s’il est déclaré dans un autre type. En outre, le ***programme texte*** d’un programme est défini comme tout programme texte contenu dans tous les fichiers source du programme, et le texte du programme d’un type est défini comme tout programme texte contenu dans le *type_declaration*s de ce type (y compris, éventuellement, les types qui sont imbriqués dans le type).

Le domaine d’accessibilité d’un type prédéfini (tel que `object`, `int`, ou `double`) est illimitée.

Le domaine d’accessibilité d’un niveau supérieur indépendant type `T` ([liés et indépendants des types](types.md#bound-and-unbound-types)) qui est déclaré dans un programme `P` est défini comme suit :

*  Si l’accessibilité déclarée de `T` est `public`, le domaine d’accessibilité `T` est le texte de programme `P` et tout autre programme qui fait référence à `P`.
*  Si l'accessibilité déclarée de `T` est `internal`, le domaine d'accessibilité de `T` correspond au texte de programme de `P`.

À partir de ces définitions, il s’ensuit que le domaine d’accessibilité d’un type indépendant de niveau supérieur est au minimum le texte de programme du programme dans lequel ce type est déclaré.

Le domaine d’accessibilité pour un type construit `T<A1, ..., An>` correspond à l’intersection du domaine d’accessibilité du type générique indépendant `T` et les domaines d’accessibilité des arguments de type `A1, ..., An`.

Le domaine d’accessibilité d’un membre imbriqué `M` déclaré dans un type `T` au sein d’un programme `P` est défini comme suit (en notant que `M` peut éventuellement être lui-même un type) :

*  Si l'accessibilité déclarée de `M` est `public`, le domaine d'accessibilité de `M` correspond à celui de `T`.
*  Si l’accessibilité déclarée de `M` est `protected internal`, let `D` est l’union du texte du programme `P` et le texte de programme de n’importe quel type dérivé `T`, qui est déclaré en dehors de `P`. Le domaine d’accessibilité `M` correspond à l’intersection du domaine d’accessibilité de `T` avec `D`.
*  Si l’accessibilité déclarée de `M` est `protected`, let `D` est l’union du texte du programme `T` et le texte de programme de n’importe quel type dérivé `T`. Le domaine d’accessibilité `M` correspond à l’intersection du domaine d’accessibilité de `T` avec `D`.
*  Si l'accessibilité déclarée de `M` est `internal`, le domaine d'accessibilité de `M` correspond à l'intersection du domaine d'accessibilité de `T` avec le texte de programme de `P`.
*  Si l'accessibilité déclarée de `M` est `private`, le domaine d'accessibilité de `M` correspond au texte de programme de `T`.

À partir de ces définitions, il s’ensuit que le domaine d’accessibilité d’un membre imbriqué est toujours au moins le texte de programme du type dans lequel le membre est déclaré. En outre, il s’ensuit que le domaine d’accessibilité d’un membre n’est jamais plus inclusif que le domaine d’accessibilité du type dans lequel le membre est déclaré.

En d’autres termes, quand un type ou membre `M` est accessible, les étapes suivantes sont évaluées pour vous assurer que l’accès est autorisé :

*  Premier, si `M` est déclaré dans un type (par opposition à une unité de compilation ou d’un espace de noms), une erreur de compilation se produit si ce type n’est pas accessible.
*  Ensuite, si `M` est `public`, l’accès est autorisé.
*  Sinon, si `M` est `protected internal`, l’accès est autorisé si elle se produit dans le programme dans lequel `M` est déclaré, ou si elle se produit dans une classe dérivée de la classe dans laquelle `M` est déclaré et a lieu via la dérivée type de classe ([accès protégé par exemple membres](basic-concepts.md#protected-access-for-instance-members)).
*  Sinon, si `M` est `protected`, l’accès est autorisé si elle se produit au sein de la classe dans laquelle `M` est déclaré, ou si elle se produit dans une classe dérivée de la classe dans laquelle `M` est déclaré et a lieu via la dérivée type de classe ([accès protégé par exemple membres](basic-concepts.md#protected-access-for-instance-members)).
*  Sinon, si `M` est `internal`, l’accès est autorisé si elle se produit dans le programme dans lequel `M` est déclaré.
*  Sinon, si `M` est `private`, l’accès est autorisé si elle se produit dans le type dans lequel `M` est déclaré.
*  Sinon, le type ou membre n’est pas accessible, et une erreur de compilation se produit.

Dans l’exemple
```csharp
public class A
{
    public static int X;
    internal static int Y;
    private static int Z;
}

internal class B
{
    public static int X;
    internal static int Y;
    private static int Z;

    public class C
    {
        public static int X;
        internal static int Y;
        private static int Z;
    }

    private class D
    {
        public static int X;
        internal static int Y;
        private static int Z;
    }
}
```
les classes et les membres ont les domaines d’accessibilité suivants :

*  Le domaine d’accessibilité `A` et `A.X` est illimité.
*  Le domaine d’accessibilité `A.Y`, `B`, `B.X`, `B.Y`, `B.C`, `B.C.X`, et `B.C.Y` est le texte de programme du programme qui le contient.
*  Le domaine d’accessibilité `A.Z` est le texte de programme `A`.
*  Le domaine d’accessibilité `B.Z` et `B.D` est le texte de programme `B`, y compris le texte de programme `B.C` et `B.D`.
*  Le domaine d’accessibilité `B.C.Z` est le texte de programme `B.C`.
*  Le domaine d’accessibilité `B.D.X` et `B.D.Y` est le texte de programme `B`, y compris le texte de programme `B.C` et `B.D`.
*  Le domaine d’accessibilité `B.D.Z` est le texte de programme `B.D`.

Comme l’illustre l’exemple, le domaine d’accessibilité d’un membre n’est jamais supérieur à celle d’un type conteneur. Par exemple, même si tous les `X` membres ont une accessibilité déclarée public, tout sauf `A.X` ont des domaines d’accessibilité qui sont restreints par un type contenant.

Comme décrit dans [membres](basic-concepts.md#members), tous les membres de classe de base, sauf par exemple des constructeurs, des destructeurs et des constructeurs statiques, sont héritées par les types dérivés. Cela inclut même les membres privés d’une classe de base. Toutefois, le domaine d’accessibilité d’un membre privé inclut uniquement le texte de programme du type dans lequel le membre est déclaré. Dans l’exemple
```csharp
class A
{
    int x;

    static void F(B b) {
        b.x = 1;        // Ok
    }
}

class B: A
{
    static void F(B b) {
        b.x = 1;        // Error, x not accessible
    }
}
```
le `B` classe hérite du membre privé `x` à partir de la `A` classe. Étant donné que le membre est privé, il est uniquement accessible dans le *class_body* de `A`. Par conséquent, l’accès à `b.x` réussit au `A.F` (méthode), mais échoue dans le `B.F` (méthode).

### <a name="protected-access-for-instance-members"></a>Accès protégé pour les membres d’instance

Quand un `protected` membre d’instance est accessible en dehors du texte de programme de la classe dans laquelle elle est déclarée, et à quel moment un `protected internal` membre d’instance est accessible en dehors du texte de programme du programme dans lequel il est déclaré, l’accès doit avoir lieu au sein d’un déclaration de classe qui dérive de la classe dans laquelle elle est déclarée. En outre, l’accès est nécessaire pour avoir lieu via une instance de ce type de classe dérivée ou d’un type de classe construite à partir de celui-ci. Cette restriction empêche une classe dérivée de l’accès aux membres protégés des autres classes dérivées, même lorsque les membres sont hérités de la même classe de base.

Laisser `B` être une classe de base qui déclare un membre d’instance protégée `M`et laissez `D` une classe qui dérive de `B`. Dans le *class_body* de `D`, l’accès à `M` peut prendre l’une des formes suivantes :

*  Un non qualifié *type_name* ou *primary_expression* du formulaire `M`.
*  Un *primary_expression* du formulaire `E.M`, à condition que le type de `E` est `T` ou une classe dérivée de `T`, où `T` est le type de classe `D`, ou un type de classe construit à partir de `D`
*  Un *primary_expression* du formulaire `base.M`.

En plus de ces formes d’accès, une classe dérivée peut accéder à un constructeur d’instance protégée d’une classe de base dans un *constructor_initializer* ([initialiseurs de constructeur](classes.md#constructor-initializers)).

Dans l’exemple
```csharp
public class A
{
    protected int x;

    static void F(A a, B b) {
        a.x = 1;        // Ok
        b.x = 1;        // Ok
    }
}

public class B: A
{
    static void F(A a, B b) {
        a.x = 1;        // Error, must access through instance of B
        b.x = 1;        // Ok
    }
}
```
dans `A`, il est possible d’accéder à `x` via des instances des deux `A` et `B`, étant donné que dans les deux cas, l’accès s’effectue via une instance de `A` ou une classe dérivée de `A`. Toutefois, dans `B`, il n’est pas possible d’accéder aux `x` via une instance de `A`, étant donné que `A` ne dérive pas de `B`.

Dans l’exemple
```csharp
class C<T>
{
    protected T x;
}

class D<T>: C<T>
{
    static void F() {
        D<T> dt = new D<T>();
        D<int> di = new D<int>();
        D<string> ds = new D<string>();
        dt.x = default(T);
        di.x = 123;
        ds.x = "test";
    }
}
```
les affectations de trois à `x` sont autorisées, car elles ont lieu via des instances de types de classe construites à partir du type générique.

### <a name="accessibility-constraints"></a>Contraintes d’accessibilité

Plusieurs constructions de langage c# nécessitent un type à être ***au moins aussi accessibles que*** un membre ou un autre type. Un type `T` est dit être au moins aussi accessible en tant que membre ou type `M` si le domaine d’accessibilité `T` est un sur-ensemble du domaine d’accessibilité de `M`. En d’autres termes, `T` est au moins aussi accessibles que `M` si `T` est accessible dans tous les contextes dans lesquels `M` est accessible.

Les contraintes d’accessibilité suivantes existent :

*  La classe de base directe d’un type de classe doit être au moins aussi accessible que le type de classe lui-même.
*  Les interfaces de base explicites d’un type d’interface doivent être au moins aussi accessibles que le type d’interface lui-même.
*  Le type de retour et les types de paramètres d’un type délégué doivent être au moins aussi accessibles que le type délégué lui-même.
*  Le type d’une constante doit être au moins aussi accessible que la constante elle-même.
*  Le type d’un champ doit être au moins aussi accessible que le champ lui-même.
*  Le type de retour et les types de paramètres d’une méthode doivent être au moins aussi accessibles que la méthode elle-même.
*  Le type d’une propriété doit être au moins aussi accessible que la propriété elle-même.
*  Le type d’un événement doit être au moins aussi accessible que l’événement lui-même.
*  Le type et les types de paramètres d’un indexeur doivent être au moins aussi accessibles que l’indexeur lui-même.
*  Le type de retour et les types de paramètres d’un opérateur doivent être au moins aussi accessibles que l’opérateur lui-même.
*  Les types de paramètres d’un constructeur d’instance doivent être au moins aussi accessibles que le constructeur d’instance.

Dans l’exemple
```csharp
class A {...}

public class B: A {...}
```
le `B` classe génère une erreur de compilation, car `A` n’est pas au moins aussi accessibles que `B`.

De même, dans l’exemple
```csharp
class A {...}

public class B
{
    A F() {...}

    internal A G() {...}

    public A H() {...}
}
```
le `H` méthode dans `B` génère une erreur de compilation, car le type de retour `A` n’est pas au moins aussi accessibles que la méthode.

## <a name="signatures-and-overloading"></a>Signatures et surcharge

Méthodes, constructeurs d’instance, indexeurs et opérateurs sont caractérisés par leurs ***signatures***:

*  La signature d’une méthode se compose du nom de la méthode, le nombre de paramètres de type et le type et le genre (valeur, référence ou sortie) de chacun de ses paramètres formels, prises en compte dans l’ordre de gauche à droite. À ces fins, n’importe quel paramètre de type de la méthode qui se produit dans le type d’un paramètre formel est identifié pas par son nom, mais par sa position ordinale dans la liste d’arguments de type de la méthode. Spécifiquement, la signature d’une méthode n’inclut pas de type de retour, le `params` modificateur qui peut être spécifié pour le paramètre le plus à droite, ni les contraintes de paramètre de type facultatif.
*  La signature d’un constructeur d’instance se compose du type et du genre (valeur, référence ou sortie) de chacun de ses paramètres formels, prises en compte dans l’ordre de gauche à droite. La signature d’un constructeur d’instance n’inclut pas spécifiquement le `params` modificateur qui peut être spécifié pour le paramètre le plus à droite.
*  La signature d’un indexeur se compose du type de chacun de ses paramètres formels, prises en compte dans l’ordre de gauche à droite. La signature d’un indexeur spécifiquement n’inclut pas le type d’élément, ni le le `params` modificateur qui peut être spécifié pour le paramètre le plus à droite.
*  La signature d’un opérateur se compose du nom de l’opérateur et le type de chacun de ses paramètres formels, prises en compte dans l’ordre de gauche à droite. Plus précisément, la signature d’un opérateur n’inclut pas le type de résultat.

Les signatures sont les mécanismes d’activation pour ***surcharge*** de membres de classes, structures et interfaces :

*  La surcharge des méthodes permet à une classe, un struct ou une interface de déclarer plusieurs méthodes portant le même nom, sous réserve que leurs signatures sont uniques au sein de cette classe, un struct ou une interface.
*  La surcharge des constructeurs d’instance permet une classe ou un struct de déclarer plusieurs constructeurs d’instance, sous réserve que leurs signatures sont uniques au sein de cette classe ou struct.
*  La surcharge des indexeurs permet une classe, un struct ou une interface de déclarer plusieurs indexeurs, sous réserve que leurs signatures sont uniques au sein de cette classe, un struct ou une interface.
*  La surcharge des opérateurs permet à une classe ou un struct de déclarer plusieurs opérateurs avec le même nom, sous réserve que leurs signatures sont uniques au sein de cette classe ou struct.

Bien que `out` et `ref` modificateurs de paramètre sont considérées comme partie d’une signature, de membres déclarés dans un type unique ne peuvent pas différer uniquement par dans signature `ref` et `out`. Une erreur de compilation se produit si les deux membres sont déclarés dans le même type avec des signatures qui serait le même si tous les paramètres dans les deux méthodes avec `out` modificateurs ont été remplacées par `ref` modificateurs. À d’autres fins de correspondance de signature (par exemple, masquage ou la substitution), `ref` et `out` sont considérées comme partie de la signature et ne correspondent pas mutuellement. (Cette restriction consiste à autoriser C# programmes peut être traduit facilement pour s’exécuter sur le Common Language Infrastructure (CLI), qui ne fournit pas un moyen de définir des méthodes qui diffèrent uniquement en `ref` et `out`.)

Dans le cadre des signatures, les types `object` et `dynamic` sont considérés comme identiques. Membres déclarés dans un seul type peuvent par conséquent pas diffèrent dans la signature uniquement par `object` et `dynamic`.

L’exemple suivant montre un ensemble de déclarations de méthode surchargée, ainsi que leurs signatures.
```csharp
interface ITest
{
    void F();                        // F()

    void F(int x);                   // F(int)

    void F(ref int x);               // F(ref int)

    void F(out int x);               // F(out int)      error

    void F(int x, int y);            // F(int, int)

    int F(string s);                 // F(string)

    int F(int x);                    // F(int)          error

    void F(string[] a);              // F(string[])

    void F(params string[] a);       // F(string[])     error
}
```

Notez que toute `ref` et `out` modificateurs de paramètre ([paramètres de méthode](classes.md#method-parameters)) font partie d’une signature. Par conséquent, `F(int)` et `F(ref int)` sont des signatures uniques. Toutefois, `F(ref int)` et `F(out int)` ne peut pas être déclaré au sein de la même interface, car leurs signatures diffèrent uniquement par `ref` et `out`. Notez également que le type de retour et les `params` modificateur ne font pas partie d’une signature, il est donc pas possible de surcharger uniquement en fonction de type de retour ou sur l’inclusion ou exclusion de la `params` modificateur. Par conséquent, les déclarations des méthodes `F(int)` et `F(params string[])` identifiés ci-dessus entraînent une erreur de compilation.

## <a name="scopes"></a>Portées

Le ***étendue*** d’un nom est la zone de texte de programme dans lequel il est possible de faire référence à l’entité déclarée par le nom sans qualification du nom. Les étendues peuvent être ***imbriquée***, et une portée interne peut déclarer la signification d’un nom à partir d’une portée externe (cela ne supprime pas le cas, toutefois, la restriction imposée par [déclarations](basic-concepts.md#declarations) que dans un bloc imbriqué n’est pas possible de déclarer une variable locale avec le même nom qu’une variable locale dans un bloc englobant). Le nom de la portée externe est ensuite dite ***masqué*** dans la région du programme texte couverte par l’étendue interne et l’accès au nom externe est uniquement possible en qualifiant le nom.

*  La portée d’un membre de l’espace de noms déclaré par un *namespace_member_declaration* ([Namespace membres](namespaces.md#namespace-members)) aucun englobante *namespace_declaration* est l’ensemble du programme texte.
*  La portée d’un membre de l’espace de noms déclaré par un *namespace_member_declaration* au sein d’un *namespace_declaration* dont le nom qualifié complet est `N` est la *namespace_body*  de chaque *namespace_declaration* dont le nom qualifié complet est `N` ou commence par `N`, suivi par un point.
*  La portée de nom défini par un *extern_alias_directive* s’étend sur la *using_directive*s, *global_attributes* et *namespace_member_ déclaration*s de son corps d’unité ou un espace de noms compilation conteneur immédiat. Un *extern_alias_directive* ne contribue pas tous les nouveaux membres à l’espace de déclaration sous-jacent. En d’autres termes, une *extern_alias_directive* n’est pas transitive, mais, au lieu de cela, affecte uniquement la compilation unité ou un espace de noms du corps dans lequel il se produit.
*  L’étendue d’un nom défini ou importé par un *using_directive* ([à l’aide de directives](namespaces.md#using-directives)) s’étend sur la *namespace_member_declaration*s de la  *compilation_unit* ou *namespace_body* dans lequel le *using_directive* se produit. Un *using_directive* peut proposer zéro ou plusieurs noms d’espace de noms, type ou membre au sein d’un particulier *compilation_unit* ou *namespace_body*, mais pas fournit de nouveaux membres à l’espace de déclaration sous-jacent. En d’autres termes, une *using_directive* n’est pas transitive, mais au lieu de cela affecte uniquement le *compilation_unit* ou *namespace_body* dans lequel elle apparaît.
*  La portée d’un paramètre de type déclaré par un *type_parameter_list* sur un *class_declaration* ([déclarations de classe](classes.md#class-declarations)) est la *class_base*, *type_parameter_constraints_clause*s, et *class_body* de qui *class_declaration*.
*  La portée d’un paramètre de type déclaré par un *type_parameter_list* sur un *struct_declaration* ([déclarations de Struct](structs.md#struct-declarations)) est la *struct_interfaces* , *type_parameter_constraints_clause*s, et *struct_body* de qui *struct_declaration*.
*  La portée d’un paramètre de type déclaré par un *type_parameter_list* sur un *interface_declaration* ([déclarations d’Interface](interfaces.md#interface-declarations)) est la *interface_base* , *type_parameter_constraints_clause*s, et *interface_body* de qui *interface_declaration*.
*  La portée d’un paramètre de type déclaré par un *type_parameter_list* sur un *delegate_declaration* ([déclarations Delegate](delegates.md#delegate-declarations)) est le *return_type*, *formal_parameter_list*, et *type_parameter_constraints_clause*s cela *delegate_declaration*.
*  La portée d’un membre déclaré par une *class_member_declaration* ([classe corps](classes.md#class-body)) est la *class_body* dans lequel la déclaration se produit. En outre, la portée d’un membre de classe s’étend à la *class_body* de ces classes dérivées qui sont inclus dans le domaine d’accessibilité ([domaines d’accessibilité](basic-concepts.md#accessibility-domains)) du membre.
*  La portée d’un membre déclaré par une *struct_member_declaration* ([les membres de Struct](structs.md#struct-members)) est la *struct_body* dans lequel la déclaration se produit.
*  La portée d’un membre déclaré par une *enum_member_declaration* ([membres Enum](enums.md#enum-members)) est la *enum_body* dans lequel la déclaration se produit.
*  La portée d’un paramètre déclaré dans un *method_declaration* ([méthodes](classes.md#methods)) est la *method_body* de qui *method_declaration*.
*  La portée d’un paramètre déclaré dans un *indexer_declaration* ([indexeurs](classes.md#indexers)) est la *accessor_declarations* de qui *indexer_declaration*.
*  La portée d’un paramètre déclaré dans un *operator_declaration* ([opérateurs](classes.md#operators)) est la *bloc* de qui *operator_declaration*.
*  La portée d’un paramètre déclaré dans un *constructor_declaration* ([constructeurs d’Instance](classes.md#instance-constructors)) est la *constructor_initializer* et *bloc* de qui *constructor_declaration*.
*  La portée d’un paramètre déclaré dans un *lambda_expression* ([expressions de fonction anonyme](expressions.md#anonymous-function-expressions)) est la *anonymous_function_body* de qui *lambda_ expression*
*  La portée d’un paramètre déclaré dans un *anonymous_method_expression* ([expressions de fonction anonyme](expressions.md#anonymous-function-expressions)) est la *bloc* de qui *anonymous_method _expression*.
*  La portée d’une étiquette déclarée dans un *labeled_statement* ([intitulée instructions](statements.md#labeled-statements)) est la *bloc* dans lequel la déclaration se produit.
*  La portée d’une variable locale déclarée dans un *local_variable_declaration* ([déclarations de variables locales](statements.md#local-variable-declarations)) est le bloc dans lequel la déclaration se produit.
*  La portée d’une variable locale déclarée dans un *switch_block* d’un `switch` instruction ([l’instruction switch](statements.md#the-switch-statement)) est la *switch_block*.
*  La portée d’une variable locale déclarée dans un *for_initializer* d’un `for` instruction ([le pour instruction](statements.md#the-for-statement)) est la *for_initializer*, le  *for_condition*, le *for_iterator*et la relation contenant-contenu *instruction* de la `for` instruction.
*  La portée d’une constante locale déclarée dans un *local_constant_declaration* ([déclarations de constante locale](statements.md#local-constant-declarations)) est le bloc dans lequel la déclaration se produit. Il s’agit d’une erreur de compilation pour faire référence à une constante locale dans une position du texte qui précède sa *constant_declarator*.
*  La portée d’une variable déclarée en tant que partie d’un *foreach_statement*, *using_statement*, *lock_statement* ou *query_expression* est déterminé par l’expansion de la construction donnée.

Dans l’étendue d’un membre de l’espace de noms, classe, struct ou d’énumération, il est possible de faire référence au membre dans une position du texte qui précède la déclaration du membre. Exemple :
```csharp
class A
{
    void F() {
        i = 1;
    }

    int i = 0;
}
```
Ici, il est valide pour `F` pour faire référence à `i` avant qu’elle est déclarée.

Dans l’étendue d’une variable locale, il est une erreur de compilation pour faire référence à la variable locale dans une position du texte qui précède le *local_variable_declarator* de la variable locale. Exemple :
```csharp
class A
{
    int i = 0;

    void F() {
        i = 1;                  // Error, use precedes declaration
        int i;
        i = 2;
    }

    void G() {
        int j = (j = 1);        // Valid
    }

    void H() {
        int a = 1, b = ++a;    // Valid
    }
}
```

Dans le `F` méthode ci-dessus, la première assignation à `i` en particulier, ne fait pas référence au champ déclaré dans la portée externe. Au lieu de cela, il fait référence à la variable locale et il en résulte une erreur de compilation, car il le précède la déclaration de la variable. Dans le `G` (méthode), l’utilisation de `j` dans l’initialiseur pour la déclaration de `j` est valide, car l’utilisation ne précède pas la *local_variable_declarator*. Dans le `H` (méthode), une ultérieure *local_variable_declarator* correctement fait référence à une variable locale déclarée dans une version *local_variable_declarator* au sein du même  *local_variable_declaration*.

Les règles de portée pour les variables locales sont conçus pour garantir que la signification d’un nom utilisé dans un contexte de l’expression est toujours identique au sein d’un bloc. Si l’étendue d’une variable locale étendre uniquement à partir de sa déclaration à la fin du bloc, puis dans l’exemple ci-dessus, la première assignation voulez-vous affecter à la variable d’instance et la deuxième assignation affecteriez à la variable locale, qui pourra éventuellement provoquer erreurs de compilation si les instructions du bloc ont été par la suite pour être réorganisées.

La signification d’un nom au sein d’un bloc peut différer en fonction du contexte dans lequel le nom est utilisé. Dans l’exemple
```csharp
using System;

class A {}

class Test
{
    static void Main() {
        string A = "hello, world";
        string s = A;                            // expression context

        Type t = typeof(A);                      // type context

        Console.WriteLine(s);                    // writes "hello, world"
        Console.WriteLine(t);                    // writes "A"
    }
}
```
le nom `A` est utilisé dans le contexte d’une expression pour faire référence à la variable locale `A` et dans un contexte de type pour faire référence à la classe `A`.

### <a name="name-hiding"></a>Masquage de nom

L’étendue d’une entité englobe généralement plus de texte de programme à l’espace de déclaration de l’entité. En particulier, l’étendue d’une entité peut inclure des déclarations qui introduisent de nouveaux espaces de déclaration contenant des entités du même nom. Ces déclarations provoquent l’entité d’origine devenir ***masqué***. À l’inverse, une entité est dite ***visible*** quand il n’est pas masqué.

Masquage de nom se produit lorsque les étendues se chevauchent par imbrication et quand les étendues se chevauchent par héritage. Les caractéristiques des deux types de masquage sont décrites dans les sections suivantes.

#### <a name="hiding-through-nesting"></a>Masquage par imbrication

Masquage de nom par imbrication peut se produire suite à l’imbrication des espaces de noms ou des types au sein d’espaces de noms, à la suite d’imbrication de types dans des classes ou structs et à la suite de paramètre et les déclarations de variables locales.

Dans l’exemple
```csharp
class A
{
    int i = 0;

    void F() {
        int i = 1;
    }

    void G() {
        i = 1;
    }
}
```
dans le `F` (méthode), la variable d’instance `i` est masquée par la variable locale `i`, mais dans le `G` (méthode), `i` fait toujours référence à la variable d’instance.

Lorsqu’un nom dans une portée interne masque un nom dans une portée externe, il masque toutes les occurrences surchargées de ce nom. Dans l’exemple
```csharp
class Outer
{
    static void F(int i) {}

    static void F(string s) {}

    class Inner
    {
        void G() {
            F(1);              // Invokes Outer.Inner.F
            F("Hello");        // Error
        }

        static void F(long l) {}
    }
}
```
l’appel `F(1)` appelle le `F` déclaré dans `Inner` , car toutes les occurrences externes de `F` sont masqués par la déclaration interne. Pour la même raison, l’appel `F("Hello")` provoque une erreur de compilation.

#### <a name="hiding-through-inheritance"></a>Masquage par héritage

Masquage de nom par héritage se produit lorsque les classes ou structs redéclarer des noms qui ont été héritées des classes de base. Ce type de masquage de nom prend l’une des formes suivantes :

*  Constante, champ, propriété, événement ou type introduit dans une classe ou un struct masque tous les membres de classe de base avec le même nom.
*  Une méthode introduite dans une classe ou un struct masque tous les membres de classe de base non méthode portant le même nom et toutes les méthodes de classe de base avec la même signature (nom de la méthode et le nombre de paramètres, modificateurs et types).
*  Un indexeur introduit dans une classe ou un struct masque tous les indexeurs de la classe de base avec la même signature (nombre de paramètres et types).

Les règles régissant les déclarations d’opérateur ([opérateurs](classes.md#operators)) rend impossible la pour une classe dérivée pour déclarer un opérateur avec la même signature qu’un opérateur dans une classe de base. Par conséquent, les opérateurs jamais masquent les uns des autres.

Contrairement au masquage d’un nom à partir d’une portée externe, le masquage d’un nom accessible à partir d’une portée héritée provoque un avertissement doit être signalée. Dans l’exemple
```csharp
class Base
{
    public void F() {}
}

class Derived: Base
{
    public void F() {}        // Warning, hiding an inherited name
}
```
la déclaration de `F` dans `Derived` provoque un avertissement doit être signalée. Masquage d’un nom hérité n’est pas spécifiquement une erreur, dans la mesure où cela interdirait l’évolution séparée des classes de base. Par exemple, cette situation peut avoir survenir car une version ultérieure de `Base` a introduit une `F` méthode qui n’était pas présent dans une version antérieure de la classe. Si la situation constituait une erreur, puis toute modification apportée à une classe de base dans une bibliothèque de classes avec versions distinctes peut éventuellement provoquer des classes dérivées deviennent non valides.

L’avertissement provoqué par le masquage d’un nom hérité peut être éliminé par le biais de la `new` modificateur :
```csharp
class Base
{
    public void F() {}
}

class Derived: Base
{
    new public void F() {}
}
```

Le `new` modificateur indique que le `F` dans `Derived` est « nouveau », et qu’il est effectivement destiné à masquer le membre hérité.

Une déclaration d’un nouveau membre masque un membre hérité uniquement dans l’étendue du nouveau membre.

```csharp
class Base
{
    public static void F() {}
}

class Derived: Base
{
    new private static void F() {}    // Hides Base.F in Derived only
}

class MoreDerived: Derived
{
    static void G() { F(); }          // Invokes Base.F
}
```

Dans l’exemple ci-dessus, la déclaration de `F` dans `Derived` masque la `F` qui a été héritée `Base`, mais, car la nouvelle `F` dans `Derived` possède un accès privé, sa portée ne s’étend pas à `MoreDerived` . Par conséquent, l’appel `F()` dans `MoreDerived.G` est valide et appellera `Base.F`.

## <a name="namespace-and-type-names"></a>Noms de type Namespace

Dans différents contextes un C# programme nécessitent un *nom_espace_de_noms* ou un *type_name* être spécifié.

```antlr
namespace_name
    : namespace_or_type_name
    ;

type_name
    : namespace_or_type_name
    ;

namespace_or_type_name
    : identifier type_argument_list?
    | namespace_or_type_name '.' identifier type_argument_list?
    | qualified_alias_member
    ;
```

Un *nom_espace_de_noms* est un *namespace_or_type_name* qui fait référence à un espace de noms. Après la résolution comme décrit ci-dessous, le *namespace_or_type_name* d’un *nom_espace_de_noms* doit faire référence à un espace de noms, ou sinon une erreur de compilation se produit. Aucun argument de type ([arguments de Type](types.md#type-arguments)) peuvent être présents dans un *nom_espace_de_noms* (seules les types peuvent avoir des arguments de type).

Un *type_name* est un *namespace_or_type_name* qui fait référence à un type. Après la résolution comme décrit ci-dessous, le *namespace_or_type_name* d’un *type_name* doit faire référence à un type, ou sinon une erreur de compilation se produit.

Si le *namespace_or_type_name* est qualifié-alias-membre sa signification est décrit dans [qualificateurs d’alias Namespace](namespaces.md#namespace-alias-qualifiers). Sinon, un *namespace_or_type_name* a l’une des quatre formes différentes :

*  `I`
*  `I<A1, ..., Ak>`
*  `N.I`
*  `N.I<A1, ..., Ak>`

où `I` est un identificateur unique, `N` est un *namespace_or_type_name* et `<A1, ..., Ak>` est facultative *type_argument_list*. En cas de non *type_argument_list* est spécifié, envisagez `k` à zéro.

La signification d’un *namespace_or_type_name* est déterminée comme suit :

*   Si le *namespace_or_type_name* est au format `I` ou sous la forme `I<A1, ..., Ak>`:
    * Si `K` est égal à zéro et le *namespace_or_type_name* s’affiche dans une déclaration de méthode générique ([méthodes](classes.md#methods)) et si cette déclaration inclut un paramètre de type ([Type paramètres](classes.md#type-parameters)) avec le nom `I`, puis le *namespace_or_type_name* fait référence à ce paramètre de type.
    * Sinon, si le *namespace_or_type_name* s’affiche dans une déclaration de type, puis pour chaque type d’instance `T` ([le type d’instance](classes.md#the-instance-type)), en commençant par le type d’instance de ce type déclaration et en continuant avec le type d’instance de chaque déclaration de classe ou struct englobante (le cas échéant) :
        * Si `K` est zéro et la déclaration de `T` inclut un paramètre de type avec nom `I`, puis le *namespace_or_type_name* fait référence à ce paramètre de type.
        * Sinon, si le *namespace_or_type_name* s’affiche dans le corps de la déclaration de type, et `T` ou un de ses types de base contient un type imbriqué accessible, ayant le nom `I` et `K`  paramètres de type, puis le *namespace_or_type_name* fait référence à ce type construit avec les arguments de type donné. S’il existe plus d’un tel type, le type déclaré dans le type plus dérivé est sélectionné. Notez que les membres sans type (constantes, champs, méthodes, propriétés, indexeurs, opérateurs, constructeurs d’instance, destructeurs et constructeurs statiques) et les membres de type avec un nombre différent de paramètres de type sont ignorés lors de la détermination de la signification de la *namespace_or_type_name*.
    * Si les étapes précédentes ont échoué, puis, pour chaque espace de noms `N`, en commençant par l’espace de noms dans lequel le *namespace_or_type_name* se produit, en continuant avec chaque espace de noms englobant (le cas échéant) et se terminant par le espace de noms global, les étapes suivantes sont évaluées jusqu'à ce qu’une entité se trouve :
        * Si `K` est égal à zéro et `I` est le nom d’un espace de noms `N`, puis :
            * Si l’emplacement où le *namespace_or_type_name* se produit est délimitée par une déclaration d’espace de noms pour `N` et la déclaration d’espace de noms contient un *extern_alias_directive* ou *using_alias_directive* qui associe le nom `I` avec un espace de noms ou un type, puis le *namespace_or_type_name* est ambigu et une erreur de compilation se produit.
            * Sinon, le *namespace_or_type_name* fait référence à l’espace de noms nommé `I` dans `N`.
        * Sinon, si `N` contient un nom de type accessible `I` et `K`  paramètres de type, puis :
            * Si `K` est égal à zéro et l’emplacement où le *namespace_or_type_name* se produit est délimitée par une déclaration d’espace de noms pour `N` et la déclaration d’espace de noms contient un *extern_alias_directive*  ou *using_alias_directive* qui associe le nom `I` avec un espace de noms ou un type, puis le *namespace_or_type_name* est ambigu et un moment de la compilation erreur se produit.
            * Sinon, le *namespace_or_type_name* fait référence au type construit avec les arguments de type donné.
        * Sinon, si l’emplacement où le *namespace_or_type_name* se produit est délimitée par une déclaration d’espace de noms pour `N`:
            * Si `K` est égal à zéro et la déclaration d’espace de noms contient un *extern_alias_directive* ou *using_alias_directive* qui associe le nom `I` avec un espace de noms importé ou type, puis le *namespace_or_type_name* fait référence à cet espace de noms ou un type.
            * Sinon, si les déclarations d’espaces de noms et le type importé par le *using_namespace_directive*s et *using_alias_directive*s de la déclaration d’espace de noms contiennent exactement un type accessible nom `I` et `K`  paramètres de type, puis le *namespace_or_type_name* fait référence à ce type construit avec les arguments de type donné.
            * Sinon, si les déclarations d’espaces de noms et le type importé par le *using_namespace_directive*s et *using_alias_directive*s de la déclaration d’espace de noms contiennent plus d’un type accessible nom `I` et `K`  paramètres de type, puis le *namespace_or_type_name* est ambigu et une erreur se produit.
    * Sinon, le *namespace_or_type_name* est non défini et une erreur de compilation se produit.
*  Sinon, le *namespace_or_type_name* est au format `N.I` ou sous la forme `N.I<A1, ..., Ak>`. `N` est d’abord résolu en tant qu’un *namespace_or_type_name*. Si la résolution de `N` n’aboutit pas, une erreur de compilation se produit. Sinon, `N.I` ou `N.I<A1, ..., Ak>` est résolu comme suit :
    * Si `K` est égal à zéro et `N` fait référence à un espace de noms et `N` contient un espace de noms imbriqué avec le nom `I`, puis le *namespace_or_type_name* fait référence à cet espace de noms imbriqué.
    * Sinon, si `N` fait référence à un espace de noms et `N` contient un nom de type accessible `I` et `K`  paramètres de type, puis le *namespace_or_type_name* fait référence à ce type construit avec les arguments de type donné.
    * Sinon, si `N` fait référence à un type de classe ou un struct (éventuellement construit) et `N` ou une de ses classes de base contient un type imbriqué accessible, ayant le nom `I` et `K`  tapez des paramètres, puis le *namespace_or_type_name* fait référence à ce type construit avec les arguments de type donné. S’il existe plus d’un tel type, le type déclaré dans le type plus dérivé est sélectionné. Notez que si la signification de `N.I` est déterminée dans le cadre de la résolution de la spécification de la classe de base de `N` ensuite la classe de base directe de `N` est considéré comme étant l’objet ([classes de Base](classes.md#base-classes)).
    * Sinon, `N.I` est non valide *namespace_or_type_name*, et une erreur de compilation se produit.

Un *namespace_or_type_name* est autorisé à faire référence à une classe statique ([classes statiques](classes.md#static-classes)) uniquement si

*  Le *namespace_or_type_name* est la `T` dans un *namespace_or_type_name* du formulaire `T.I`, ou
*  Le *namespace_or_type_name* est la `T` dans un *typeof_expression* ([listes d’arguments](expressions.md#argument-lists)1) sous la forme `typeof(T)`.

### <a name="fully-qualified-names"></a>Noms qualifiés complets

Chaque espace de noms et le type ont un ***nom qualifié complet***, qui identifie de façon unique l’espace de noms ou le type parmi tous les autres. Le nom qualifié complet d’un espace de noms ou le type `N` est déterminée comme suit :

*  Si `N` est un membre de l’espace de noms global, son nom qualifié complet est `N`.
*  Sinon, son nom qualifié complet est `S.N`, où `S` est le nom qualifié complet de l’espace de noms ou le type dans lequel `N` est déclaré.

En d’autres termes, le nom qualifié complet de `N` est le chemin d’accès hiérarchique complet des identificateurs qui mènent à `N`, en commençant à partir de l’espace de noms global. Étant donné que chaque membre d’un espace de noms ou un type doit avoir un nom unique, il s’ensuit que le nom qualifié complet d’un espace de noms ou le type est toujours unique.

L’exemple ci-dessous montre plusieurs déclarations d’espace de noms et type, ainsi que leurs noms qualifiés complets associés.
```csharp
class A {}                // A

namespace X               // X
{
    class B               // X.B
    {
        class C {}        // X.B.C
    }

    namespace Y           // X.Y
    {
        class D {}        // X.Y.D
    }
}

namespace X.Y             // X.Y
{
    class E {}            // X.Y.E
}
```

## <a name="automatic-memory-management"></a>Gestion automatique de la mémoire

C# utilise la gestion automatique de la mémoire, ce qui évite aux développeurs d’allouer et libérer la mémoire occupée par les objets manuellement. Stratégies de gestion automatique de la mémoire sont implémentées par un ***RÉCUPÉRATEUR de mémoire***. Le cycle de vie de gestion de mémoire d’un objet est comme suit :

1. Lorsque l’objet est créé, la mémoire est allouée pour elle, le constructeur est exécuté et l’objet est considéré comme en direct.
2. Si l’objet, ou n’importe quelle partie de celui-ci, ne sont pas accessibles par toute poursuite de l’exécution, autre que l’exécution de destructeurs, l’objet est considéré comme n’est plus en cours d’utilisation, et il devient éligible pour la destruction. Le compilateur c# et le garbage collector peuvent choisir d’analyser le code pour déterminer les références à un objet peuvent être utilisées à l’avenir. Par exemple, si une variable locale qui est dans la portée est la seule référence existante à un objet, mais cette variable locale est jamais appelée point aucune poursuite de l’exécution à partir de l’exécution en cours dans la procédure, le garbage collector peut (mais n’est pas requis pour) traiter comme n’étant plus de l’objet en cours d’utilisation.
3. Une fois que l’objet peut être détruit, à certains non spécifié ultérieurement moment, le destructeur ([destructeurs](classes.md#destructors)) (le cas échéant) pour l’exécution de l’objet. Dans des circonstances normales le destructeur de l’objet est exécuté une seule fois, bien que les API spécifiques à l’implémentation peuvent autoriser ce comportement être substituée.
4. Une fois que le destructeur pour un objet est exécuté, si cet objet, ou n’importe quelle partie de celui-ci, n’est pas accessible par toute poursuite de l’exécution, y compris l’exécution de destructeurs, l’objet est considéré comme inaccessible et l’objet devient éligible pour la collection.
5. Enfin, à un moment une fois que l’objet devient éligible pour la collection, le garbage collector libère la mémoire associée à cet objet.

Le garbage collector conserve des informations sur l’utilisation des objets et utilise ces informations pour prendre des décisions de gestion, de mémoire telles que where dans la mémoire pour localiser un objet nouvellement créé, pour déplacer un objet et lorsqu’un objet n’est plus en cours d’utilisation ou inaccessible.

Comme les autres langages qui supposent l’existence d’un garbage collector, c# est conçu afin que le garbage collector peut implémenter une grande variété de stratégies de gestion de mémoire. Par exemple, c# ne nécessite pas que les destructeurs être exécutée ou que les objets sont collectés dès qu’ils sont éligibles, ou que les destructeurs être exécuté dans un ordre particulier, ou sur n’importe quel thread particulier.

Le comportement du garbage collector peut être contrôlé, dans une certaine mesure, par le biais de méthodes statiques sur la classe `System.GC`. Cette classe peut être utilisée pour demander une collection se produisent, les destructeurs pour être s’exécuter (ou non) et ainsi de suite.

Étant donné que le garbage collector est autorisé à toute latitude pour déterminer quand collecter des objets et exécuter des destructeurs, une implémentation conforme peut produire un résultat différent de celui indiqué par le code suivant. Le programme
```csharp
using System;

class A
{
    ~A() {
        Console.WriteLine("Destruct instance of A");
    }
}

class B
{
    object Ref;

    public B(object o) {
        Ref = o;
    }

    ~B() {
        Console.WriteLine("Destruct instance of B");
    }
}

class Test
{
    static void Main() {
        B b = new B(new A());
        b = null;
        GC.Collect();
        GC.WaitForPendingFinalizers();
    }
}
```
Crée une instance de classe `A` et une instance de la classe `B`. Ces objets deviennent éligibles pour le garbage collection lorsque la variable `b` reçoit la valeur `null`, car après ce délai, il est impossible pour tout code écrit par l’utilisateur pour y accéder. Le résultat pourrait être
```
Destruct instance of A
Destruct instance of B
```
ou
```
Destruct instance of B
Destruct instance of A
```
Étant donné que le langage n’impose pas de contraintes sur l’ordre dans lequel les objets sont nettoyées.

Dans certains cas subtils, la distinction entre « peut être détruit » et « éligibles pour la collection » peut être importante. Par exemple :
```csharp
using System;

class A
{
    ~A() {
        Console.WriteLine("Destruct instance of A");
    }

    public void F() {
        Console.WriteLine("A.F");
        Test.RefA = this;
    }
}

class B
{
    public A Ref;

    ~B() {
        Console.WriteLine("Destruct instance of B");
        Ref.F();
    }
}

class Test
{
    public static A RefA;
    public static B RefB;

    static void Main() {
        RefB = new B();
        RefA = new A();
        RefB.Ref = RefA;
        RefB = null;
        RefA = null;

        // A and B now eligible for destruction
        GC.Collect();
        GC.WaitForPendingFinalizers();

        // B now eligible for collection, but A is not
        if (RefA != null)
            Console.WriteLine("RefA is not null");
    }
}
```

Dans le programme ci-dessus, si le garbage collector choisit d’exécuter le destructeur de `A` avant le destructeur de `B`, puis la sortie de ce programme peut être :
```
Destruct instance of A
Destruct instance of B
A.F
RefA is not null
```

Notez que même si l’instance de `A` n’était pas en cours d’utilisation et `A`du destructeur a été exécuté, il est toujours possible pour les méthodes de `A` (dans ce cas, `F`) soit appelée à partir d’un autre destructeur. En outre, notez que l’exécution d’un destructeur peut entraîner un objet utilisable à partir du programme principal à nouveau. Dans ce cas, l’exécution de `B`du destructeur a provoqué une instance de `A` qui a été précédemment pas en cours d’utilisation devenir accessible à partir de la référence en temps réel `Test.RefA`. Après l’appel à `WaitForPendingFinalizers`, l’instance de `B` est éligible pour la collection, mais l’instance de `A` n’est pas, en raison de la référence `Test.RefA`.

Pour éviter toute confusion et un comportement inattendu, il est généralement judicieux pour les destructeurs pour procéder au nettoyage des données stockées dans leurs champs de l’objet uniquement et ne pas à effectuer toutes les actions sur les objets référencés ou les champs statiques.

Alternative à l’utilisation de destructeurs consiste à laisser à une classe d’implémenter le `System.IDisposable` interface. Cela permet au client de l’objet déterminer le moment libérer les ressources de l’objet, généralement en accédant à l’objet en tant que ressource dans un `using` instruction ([l’instruction using](statements.md#the-using-statement)).

## <a name="execution-order"></a>Ordre d’exécution

L’exécution d’un programme c# se déroule telles que les effets secondaires de chaque thread en cours d’exécution sont conservés à des points d’exécution critiques. Un ***effet secondaire*** est défini comme une lecture ou d’écriture d’un champ volatil, une écriture dans une variable non volatile, une écriture dans une ressource externe et la levée d’une exception. Les points d’exécution critiques au niveau duquel l’ordre de ces effets secondaires doit être conservé sont des références à des champs volatiles ([des champs volatiles](classes.md#volatile-fields)), `lock` instructions ([l’instruction lock](statements.md#the-lock-statement)), et la création de threads et l’arrêt. L’environnement d’exécution est libre de changer l’ordre d’exécution d’un programme c#, soumis aux contraintes suivantes :

*  Dépendance des données est conservée dans un thread d’exécution. Autrement dit, la valeur de chaque variable est calculée comme si toutes les instructions dans le thread ont été exécutées dans l’ordre de programme d’origine.
*  L’initialisation règles régissant l’ordre ([initialisation de champ](classes.md#field-initialization) et [initialiseurs de Variable](classes.md#variable-initializers)).
*  L’ordre des effets secondaires est conservé en ce qui concerne les volatiles lectures et écritures ([des champs volatiles](classes.md#volatile-fields)). En outre, l’environnement d’exécution doive évaluer pas partie d’une expression si elle peut déduire que la valeur de cette expression n’est pas utilisé et qu’aucun effet secondaire nécessaires ne sont produits (y compris ceux dû au fait appel à une méthode ou l’accès à un champ volatil). Lors de l’exécution du programme est interrompue par un événement asynchrone (par exemple, une exception levée par un autre thread), n’est pas obligatoirement que les effets secondaires observables sont visibles dans l’ordre de programme d’origine.
