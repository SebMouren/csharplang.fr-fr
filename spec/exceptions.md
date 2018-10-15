# <a name="exceptions"></a>Exceptions

Les exceptions en c# fournissent une manière structurée, uniforme et de type sécurisé de niveau système et niveau de l’application de gestion des conditions d’erreur. Le mécanisme d’exception en c# est très similaire à celui de C++, avec quelques différences importantes :

*  En c#, toutes les exceptions doivent être représentées par une instance d’un type de classe dérivé `System.Exception`. En C++, n’importe quelle valeur de n’importe quel type peut être utilisé pour représenter une exception.
*  En c#, un bloc finally ([l’instruction try](statements.md#the-try-statement)) peut être utilisé pour écrire du code d’arrêt qui s’exécute dans une exécution normale et des conditions exceptionnelles. Ce code est difficile d’écrire en C++ sans duplication de code.
*  En c#, les exceptions au niveau du système telles que le dépassement de capacité, division par zéro et null déréférence ont bien définis des classes d’exception et à concorde avec les conditions d’erreur au niveau de l’application.

## <a name="causes-of-exceptions"></a>Causes des exceptions

Exception peut être levée de deux manières différentes.

*  Un `throw` instruction ([l’instruction throw](statements.md#the-throw-statement)) lève une exception immédiatement et sans condition. Contrôle n’atteint jamais l’instruction qui suit immédiatement la `throw`.
*  Certaines des conditions exceptionnelles qui surviennent pendant le traitement d’instructions c# et expression provoquent une exception dans certaines circonstances, lorsque l’opération ne peut pas être terminée normalement. Par exemple, une opération de division entier ([opérateur de Division](expressions.md#division-operator)) lève une `System.DivideByZeroException` si le dénominateur est égal à zéro. Consultez [Classes d’exceptions courantes](exceptions.md#common-exception-classes) pour obtenir la liste des différentes exceptions qui peuvent se produire de cette façon.

## <a name="the-systemexception-class"></a>La classe System.Exception

Le `System.Exception` classe est le type de base de toutes les exceptions. Cette classe possède quelques propriétés notables qui partagent toutes les exceptions :

*  `Message` est une propriété en lecture seule de type `string` qui contient une description explicite de la raison de l’exception.
*  `InnerException` est une propriété en lecture seule de type `Exception`. Si sa valeur est non null, il fait référence à l’exception qui a provoqué l’exception actuelle, autrement dit, l’exception actuelle a été levée dans un bloc catch gère les `InnerException`. Sinon, sa valeur est null, indiquant que cette exception n’a pas provoquée par une autre exception. Le nombre d’objets d’exception chaînés de cette manière peut être arbitraire.

La valeur de ces propriétés peut être spécifiée dans les appels au constructeur d’instance pour `System.Exception`.

## <a name="how-exceptions-are-handled"></a>Gestion des exceptions

Les exceptions sont gérées par un `try` instruction ([l’instruction try](statements.md#the-try-statement)).

Lorsqu’une exception se produit, le système recherche le plus proche `catch` clause qui peut gérer l’exception, comme déterminé par le type au moment de l’exécution de l’exception. Tout d’abord, la méthode actuelle est recherchée englobante lexicalement `try` instruction et les clauses catch associé de l’instruction try sont considérés comme dans l’ordre. Si cette tentative échoue, la méthode qui a appelé la méthode actuelle est recherchée englobante lexicalement `try` instruction qui comprend le point de l’appel à la méthode actuelle. Cette recherche se poursuit jusqu'à un `catch` clause est trouvée, qui peut gérer l’exception actuelle, en nommant une classe d’exception qui est de la même classe ou une classe de base du type d’exécution de l’exception levée. Un `catch` clause qui ne nomme pas une classe d’exception peut gérer n’importe quelle exception.

Une fois une clause catch correspondante est trouvée, le système se prépare à transférer le contrôle à la première instruction de la clause catch. Avant le début de l’exécution de la clause catch, le système exécute d’abord, dans l’ordre, les `finally` clauses qui ont été associés à des instructions try plus imbriquées que celui qui a intercepté l’exception.

Si aucune clause catch correspondante n’est trouvée, deux choses se produit :

*  Si la recherche d’une clause catch correspondante atteint un constructeur statique ([constructeurs statiques](classes.md#static-constructors)) ou un initialiseur de champ statique, un `System.TypeInitializationException` est levée au point qui a déclenché l’appel du constructeur statique. L’exception interne de la `System.TypeInitializationException` contient l’exception qui a été levée.
*  Si la recherche de clauses catch correspondantes atteint le code qui a initialement a démarré le thread, puis l’exécution du thread est interrompue. L’impact d’un tel arrêt est défini par l’implémentation.

Les exceptions qui se produisent pendant l’exécution du destructeur méritent une mention spéciale. Si une exception se produit pendant l’exécution du destructeur, cette exception n’est pas interceptée, l’exécution de ce destructeur est arrêtée, puis le destructeur de la classe de base (le cas échéant) est appelé. S’il n’existe aucune classe de base (comme dans le cas de la `object` type) ou si il n’existe aucun destructeur de classe de base, l’exception est ignorée.

## <a name="common-exception-classes"></a>Classes d’exceptions courantes

Les exceptions suivantes sont levées par certaines opérations c#.

|                                      |                |
|--------------------------------------|----------------|
| `System.ArithmeticException`         | Classe de base pour les exceptions qui se produisent pendant des opérations arithmétiques, telles que `System.DivideByZeroException` et `System.OverflowException`. | 
| `System.ArrayTypeMismatchException`  | Levée lorsqu’un stockage dans un tableau échoue, car le type réel de l’élément stocké est incompatible avec le type réel du tableau. | 
| `System.DivideByZeroException`       | Levée lorsqu’une tentative de division d’une valeur intégrale par zéro se produit. | 
| `System.IndexOutOfRangeException`    | Levée lorsqu’une tentative d’indexation d’un tableau via un index qui est inférieur à zéro ou en dehors des limites du tableau. | 
| `System.InvalidCastException`        | Levée lorsqu’une conversion explicite d’un type de base ou l’interface en un type dérivé échoue au moment de l’exécution. | 
| `System.NullReferenceException`      | Levée quand un `null` référence est utilisée dans une manière qui rend obligatoire l’objet référencé. | 
| `System.OutOfMemoryException`        | Levée lorsqu’une tentative d’allocation de mémoire (via `new`) échoue. | 
| `System.OverflowException`           | Levée quand une opération dans un contexte `checked` engendre un dépassement. | 
| `System.StackOverflowException`      | Levée lorsque la pile d’exécution est épuisée par un trop grand nombre des appels de méthode en attente ; en général, le signe d’une récurrence très profonde ou non liée. | 
| `System.TypeInitializationException` | Levée lorsqu’un constructeur statique lève une exception et non `catch` clauses existe pour l’intercepter. | 
