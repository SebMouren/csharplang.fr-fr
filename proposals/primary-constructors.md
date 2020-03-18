---
ms.openlocfilehash: 77c9ffda3a59cc5f3dcc3ec9848600c6c5e03eed
ms.sourcegitcommit: 27487fa0294f4cdb7e5f2478884149e05314fd8a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "79484971"
---
# <a name="primary-constructors"></a>Constructeurs principaux

* [x] proposé
* [] Prototype : non démarré
* [] Implémentation : non démarrée
* [] Spécification : non démarrée

## <a name="summary"></a>Résumé
[summary]: #summary

Les classes peuvent avoir une liste de paramètres et, le cas échéant, la spécification de la classe de base peut avoir une liste d’arguments.
Les paramètres de constructeur principal sont dans la portée tout au long de la déclaration de classe, et s’ils sont capturés par un membre de fonction ou une fonction anonyme, ils sont stockés en tant que champs privés dans la classe.

## <a name="motivation"></a>Motivation
[motivation]: #motivation

Il est courant d’avoir beaucoup de réutilisables dans le code d’initialisation du programme. Dans le cas général, un élément de données donné `x` est mentionné plusieurs fois :

- En tant que champ privé `_x`
- En tant que paramètre `x` à un constructeur
- Dans une `_x = x;` d’assignation du champ à partir du paramètre dans le constructeur
- En tant que propriété `X`
- Dans l’accesseur Set de propriété `x = value;`
- Dans l’accesseur Get de propriété `return x;`

``` c#
class C
{
    private string _x;
    
    public C(string x)
    {
        _x = x;
    }
    public string X
    {
        get => _x;
        set { if (value == null) throw new NullArgumentException(nameof(X)); _x = value; }
    }
}
```

Pour les propriétés qui ne nécessitent pas de validation ou de calcul, la caractère fastidieux peut être réduite à l’aide de propriétés automatiques, ce qui réduit la nécessité de déclarer un champ de stockage explicite pour la propriété. Toutefois, si votre propriété nécessite une sorte de logique allant au-delà de ce qu’une propriété automatique fournit, la méthode ci-dessus est la meilleure solution.

À la place, les constructeurs principaux réduisent la surcharge en plaçant des arguments de constructeur directement dans la portée de la classe, à nouveau évite la nécessité de déclarer explicitement un champ de stockage. Ainsi, l’exemple ci-dessus deviendrait :

``` c#
class C(string x)
{
    public string X
    {
        get => x;
        set { if (value == null) throw new NullArgumentException(nameof(X)); x = value; }
    }
}
```

Dans cet exemple, le constructeur principal réduit le nombre d’entités nommées pour `x` de trois à deux, évite le `_x` champ de stockage. Elle supprime deux déclarations de membre sur trois (en gardant uniquement la déclaration de propriété elle-même) et réduit le nombre total de mentions de `x`/`_x`/`X` de huit à cinq.


## <a name="detailed-design"></a>Conception détaillée
[design]: #detailed-design

Les classes peuvent avoir une liste de paramètres :

``` c#
public class C(int i, string s)
{
    ...
}
```

La liste de paramètres entraîne la déclaration implicite d’un constructeur pour la classe, avec la même accessibilité que la classe elle-même.

``` c#
new C(5, "Hello");
```

Les paramètres de constructeur principal sont dans la portée dans le corps de la classe. Si elles sont capturées par un membre de fonction ou une fonction anonyme, elles sont stockées en tant que champs privés dans la classe. S’ils sont utilisés uniquement pendant l’initialisation, ils ne sont pas stockés dans l’objet.

``` c#
public class C(int i, string s)
{
    int[] a = new int[i]; // i not captured
    public int S => s;    // s captured
}
```

Si une classe avec un constructeur principal a une spécification de classe de base, celle-ci peut avoir une liste d’arguments. Cela sert de liste d’arguments à un initialiseur `base(...)` du constructeur déclaré implicitement. Si aucune liste d’arguments n’est fournie, une liste vide est utilisée.

``` c#
public class C(int i, string s) : B(s)
{
    ...
}
```
La classe peut également avoir des constructeurs explicitement définis, mais ceux-ci doivent tous utiliser un initialiseur de `this(...)`. Cela garantit que le constructeur principal est toujours appelé lorsqu’une nouvelle instance est construite.

Tous les initialiseurs dans le corps de la classe deviennent des assignations dans le constructeur généré. Cela signifie que, contrairement à d’autres classes, les initialiseurs s’exécuteront *après* l’appel du constructeur de base, et non avant. En outre, la classe générée contiendra des assignations pour initialiser tous les champs privés qui ont été générés pour stocker les paramètres du constructeur principal capturés par les membres. Ces membres sont réécrits pour utiliser le champ privé au lieu du paramètre de la même façon que les fermetures pour les expressions lambda. Les champs primaires générés sont d’abord initialisés, puis les assignations générées par l’initialiseur sont exécutées dans l’ordre d’apparition dans la classe.

Dans l’exemple ci-dessus, l’effet de la déclaration de classe est comme si elle était réécrite comme suit :

``` c#
public class C : B
{
    public C(int i, string s) : base(s)
    {
        __s = s;        // store parameter s for captured use
        a = new int[i]; // initialize a
    }
    int __s; // generated field for capture of s
    
    int[] a;
    public int S => __s; // s replaced with captured __s
}
```

La capture présente des restrictions similaires à la capture de variables locales par des expressions lambda. Par exemple, les paramètres `ref` et `out` sont autorisés dans les constructeurs principaux, mais ils ne peuvent pas être capturés dans mes corps membres.


## <a name="drawbacks"></a>Inconvénients
[drawbacks]: #drawbacks

Dans un ordre approximatif de précision.

* La proposition utilise une syntaxe qui a également été proposée pour les enregistrements positionnels. Si nous souhaitons ces deux fonctionnalités, un hébergement est nécessaire. Par exemple, un modificateur de `data` a été proposé sur les enregistrements.
* La taille d’allocation des objets construits est moins évidente, car le compilateur détermine s’il faut allouer un champ pour un paramètre de constructeur principal en fonction du texte complet de la classe. Ce risque est similaire à la capture implicite de variables par des expressions lambda.
* Une tentation courante (ou un modèle accidentel) peut consister à capturer le paramètre « identique » à plusieurs niveaux d’héritage, car il est passé en haut de la chaîne du constructeur au lieu de l’allouer explicitement à un champ protégé au niveau de la classe de base, ce qui aboutit à des allocations dupliquées pour les mêmes données dans les objets. Cela est très similaire au risque actuel de substitution des propriétés automatiques avec des propriétés automatiques. 
* Comme nous l’avons proposé ci-dessus, il n’existe pas de place pour une logique supplémentaire qui peut généralement être exprimée dans des corps de constructeur. L’extension « corps du constructeur principal » ci-dessous résout le.
* Comme nous l’avons proposé, la sémantique d’ordre d’exécution est légèrement différente de celle des constructeurs ordinaires. Cela peut probablement être résolu, mais au détriment de certaines des propositions d’extension (notamment les « corps de constructeur principal »).
* La proposition ne fonctionne que lorsqu’un seul constructeur peut être désigné comme principal.
* Il n’existe aucun moyen d’avoir une accessibilité distincte de la classe et du constructeur principal. Par exemple, dans les situations où les constructeurs publics délèguent tous à un constructeur « Build-it-all » privé qui serait nécessaire. Si nécessaire, la syntaxe peut être proposée ultérieurement.


## <a name="alternatives"></a>Autres solutions
[alternatives]: #alternatives

Les enregistrements positionnels complets peuvent être une alternative ou coexister avec les constructeurs principaux, selon les spécificités. Ils permettent d' *obtenir plus* d’abréviation dans un plus *petit* nombre de scénarios. Les deux sont potentiellement utiles, mais ils peuvent être excessifs, sauf s’ils peuvent être facilement intégrés entre eux.


## <a name="possible-extensions"></a>Extensions possibles
[extensions]: #possible-extensions

Il s’agit de variations ou d’ajouts à la proposition principale qui peuvent être considérées conjointement avec elle, ou à un moment ultérieur si elles sont jugées utiles.

### <a name="primary-constructor-bodies"></a>Corps du constructeur principal

Les constructeurs eux-mêmes contiennent souvent une logique de validation de paramètre ou un autre code d’initialisation non trivial qui ne peut pas être exprimé comme des initialiseurs.

Les constructeurs principaux peuvent être étendus pour permettre aux blocs d’instructions d’apparaître directement dans le corps de la classe. Ces instructions seraient insérées dans le constructeur généré au point où elles apparaissent entre l’initialisation des assignations, et seraient donc exécutées avec des initialiseurs. Exemple :

``` c#
public class C(int i, string s) : B(s)
{
    {
        if (i < 0) throw new ArgumentOutOfRangeException(nameof(i));
    }
    int[] a = new int[i];
    public int S => s;
}
```

### <a name="initializer-fields-and-initializer-functions"></a>Champs d’initialiseur et fonctions d’initialiseur

Dans une classe avec un constructeur principal, nous pourrions considérer les déclarations de champ et de méthode sans modificateurs d’accessibilité comme des variables locales et des fonctions locales :

* Tout comme les paramètres de constructeur principal, les « champs d’initialiseur » ne sont capturés dans un champ privé réel que s’ils ont été utilisés dans les fonctions membres.
* Les « fonctions d’initialiseur » ne sont prises en compte que pour capturer les paramètres de constructeur principal et les champs d’initialiseur s’ils étaient eux-mêmes utilisés dans d’autres fonctions membres. Si elles ne sont pas capturées, elles peuvent être générées de façon plus optimale, comme les fonctions locales.
* Tout comme les paramètres du constructeur principal, ils ne sont pas disponibles via l’accès aux membres, mais uniquement en tant que nom simple.

Cela peut être utilisé pour les variables temporaires et les fonctions d’assistance qui ne concernent que l’initialisation :

``` c#
public class C(string s)
{
    int size = s.Length;             // not captured
    int[] Create() => new int[size]; // not captured
    int[] a = Create();
    ...
}
```

Cela peut être trop subtil, en particulier dans la mesure où l’absence de modificateurs d’accessibilité ne signifie simplement `private`. 

### <a name="initializer-statements"></a>Instructions d’initialiseur

Une combinaison radicale des extensions ci-dessus consiste à autoriser simplement les instructions directement dans le corps de la classe. Ces instructions sont exactement les mêmes que les corps de constructeur intercalés proposés ci-dessus, sauf qu’ils n’ont pas besoin d’être placés dans `{ }`. Pour que cela soit suffisamment utile, les variables « locales » et les fonctions d’assistance doivent également être exprimables au niveau supérieur de la classe, de la même façon que dans l’extension « champs d’initialiseur et fonctions d’initialiseur » ci-dessus.


### <a name="member-access"></a>Accès au membre

La proposition principale traite les paramètres de constructeur principal comme des paramètres qui peuvent uniquement être appelés noms simples. Une alternative consiste à les autoriser à être référencés comme s’il s’agissait de champs privés, par exemple avec un accès de membre, *même* s’ils ne sont parfois pas générés en tant que champs. Cela leur permet d’être référencés en tant que `this.x` lorsqu’ils sont masqués par des variables locales et qu’ils sont accessibles à partir d’une autre instance comme `other.x`.

En cas d’application à l’extension « champs d’initialiseur et fonctions d’initialiseur », cela réduirait également le degré de différence entre ces derniers et les membres privés ordinaires. La seule différence est que le compilateur est libre de les elider à partir de l’objet s’il n’est utilisé qu’au cours de l’initialisation.

