Bonjour !

Ce TP est conçu pour vous guider dans la définition et l'utilisation des `structs` et de leurs `méthodes` associées en Go. C'est un concept fondamental pour organiser votre code et modéliser des entités du monde réel.

N'hésitez pas à utiliser les ressources à votre disposition, y compris l'IA, pour vous aider à comprendre les concepts et à rédiger le code. L'objectif est d'apprendre et de maîtriser ces notions.

---

## TP : Modélisation d'Entités avec Structs et Méthodes

**Objectif du TP :** Définir et utiliser des `structs` et des `méthodes` associées pour encapsuler des données et des comportements.

**Contexte :** En Go, les `structs` permettent de regrouper des champs de données de différents types sous un même nom. Les `méthodes` sont des fonctions associées à un type spécifique (ici, une `struct`), permettant d'ajouter des comportements à ces types. Ensemble, ils forment une base solide pour la programmation orientée objet en Go.

**Prérequis :** Connaissance de base de la syntaxe Go (déclaration de variables, fonctions, types).

---

### Exercice 1 : Modélisation d'un Point 2D et d'un Rectangle

Dans cet exercice, nous allons créer des `structs` pour représenter un point dans un plan 2D et un rectangle, puis leur associer des méthodes pour effectuer des opérations.

#### Étape 1.1 : Définir la struct `Point`

1.  Créez une `struct` nommée `Point` qui aura deux champs de type `float64` : `X` et `Y`. Ces champs représenteront les coordonnées du point.

#### Étape 1.2 : Méthode `DistanceTo()` pour `Point`

1.  Ajoutez une méthode à la `struct Point` nommée `DistanceTo`.
2.  Cette méthode prendra un autre `Point` en paramètre.
3.  Elle devra retourner la distance euclidienne entre le `Point` récepteur et le `Point` passé en paramètre.
    *   Rappel : `distance = sqrt((x2 - x1)^2 + (y2 - y1)^2)`
    *   Utilisez le package `math` (`math.Pow` et `math.Sqrt`).

#### Étape 1.3 : Définir la struct `Rectangle`

1.  Créez une `struct` nommée `Rectangle`.
2.  Cette `struct` aura deux champs de type `Point` : `Min` et `Max`.
    *   `Min` représentera le coin inférieur gauche du rectangle.
    *   `Max` représentera le coin supérieur droit du rectangle.

#### Étape 1.4 : Méthodes pour `Rectangle`

1.  **Méthode `Width()` :**
    *   Ajoutez une méthode à la `struct Rectangle` nommée `Width`.
    *   Cette méthode ne prendra aucun paramètre et retournera la largeur du rectangle (différence entre les coordonnées X de `Max` et `Min`).
2.  **Méthode `Height()` :**
    *   Ajoutez une méthode à la `struct Rectangle` nommée `Height`.
    *   Cette méthode ne prendra aucun paramètre et retournera la hauteur du rectangle (différence entre les coordonnées Y de `Max` et `Min`).
3.  **Méthode `Area()` :**
    *   Ajoutez une méthode à la `struct Rectangle` nommée `Area`.
    *   Cette méthode ne prendra aucun paramètre et retournera la surface du rectangle.
4.  **Méthode `Perimeter()` :**
    *   Ajoutez une méthode à la `struct Rectangle` nommée `Perimeter`.
    *   Cette méthode ne prendra aucun paramètre et retournera le périmètre du rectangle.
5.  **Méthode `Move(dx, dy float64)` :**
    *   Ajoutez une méthode à la `struct Rectangle` nommée `Move`.
    *   Cette méthode prendra deux paramètres `dx` et `dy` (déplacements sur X et Y).
    *   Elle devra **modifier** les coordonnées `Min` et `Max` du rectangle pour le déplacer.
    *   **Réfléchissez bien au type de *receiver* à utiliser ici : valeur ou pointeur ? Pourquoi ?**

#### Étape 1.5 : Utilisation dans `main`

1.  Dans la fonction `main()` :
    *   Créez deux instances de `Point` (par exemple, `p1` et `p2`).
    *   Calculez et affichez la distance entre `p1` et `p2` en utilisant la méthode `DistanceTo()`.
    *   Créez une instance de `Rectangle` en utilisant des `Point` pour `Min` et `Max`.
    *   Affichez la largeur, la hauteur, la surface et le périmètre de ce rectangle en utilisant ses méthodes.
    *   Appelez la méthode `Move()` sur votre rectangle pour le déplacer.
    *   Après le déplacement, affichez à nouveau les coordonnées `Min` et `Max` du rectangle pour vérifier que le déplacement a bien eu lieu.

---

### Exercice 2 : Modélisation d'un Cercle

Continuons avec une autre forme géométrique.

#### Étape 2.1 : Définir la struct `Circle`

1.  Créez une `struct` nommée `Circle` qui aura deux champs :
    *   `Center` de type `Point` (utilisez la `struct Point` définie précédemment).
    *   `Radius` de type `float64`.

#### Étape 2.2 : Méthodes pour `Circle`

1.  **Méthode `Area()` :**
    *   Ajoutez une méthode à la `struct Circle` nommée `Area`.
    *   Elle retournera la surface du cercle.
    *   Rappel : `surface = π * rayon^2` (utilisez `math.Pi`).
2.  **Méthode `Circumference()` :**
    *   Ajoutez une méthode à la `struct Circle` nommée `Circumference`.
    *   Elle retournera la circonférence du cercle.
    *   Rappel : `circonférence = 2 * π * rayon`.
3.  **Méthode `Scale(factor float64)` :**
    *   Ajoutez une méthode à la `struct Circle` nommée `Scale`.
    *   Cette méthode prendra un `factor` (facteur d'échelle) en paramètre.
    *   Elle devra **modifier** le `Radius` du cercle en le multipliant par le `factor`.
    *   **Comme pour `Move` du `Rectangle`, quel type de *receiver* est approprié ici et pourquoi ?**

#### Étape 2.3 : Utilisation dans `main`

1.  Dans la fonction `main()` (à la suite de l'exercice 1) :
    *   Créez une instance de `Circle` (par exemple, avec un `Point` pour le centre et un rayon).
    *   Affichez sa surface et sa circonférence.
    *   Appelez la méthode `Scale()` sur votre cercle pour modifier son rayon.
    *   Après l'échelle, affichez à nouveau le rayon, la surface et la circonférence pour vérifier les modifications.

---

### Exercice 3 : Améliorations et Réflexion

Pour aller plus loin et solidifier votre compréhension.

#### Étape 3.1 : Méthode `String()` ou `Describe()`

1.  Ajoutez une méthode `String()` (ou `Describe()`) à la `struct Rectangle` et à la `struct Circle`.
2.  Cette méthode ne prendra pas de paramètre et retournera une chaîne de caractères décrivant l'objet de manière lisible (par exemple, "Rectangle de largeur X, hauteur Y" ou "Cercle de centre (X,Y) et rayon R").
    *   **Astuce :** Si vous nommez la méthode `String() string`, elle sera automatiquement appelée par `fmt.Println()` lorsque vous passerez une instance de votre `struct`.

#### Étape 3.2 : Gestion des entrées invalides

1.  Comment pourriez-vous empêcher la création de rectangles ou de cercles avec des dimensions négatives (largeur, hauteur, rayon) ?
2.  Proposez une approche (sans nécessairement l'implémenter complètement) pour valider les données lors de la création d'une nouvelle instance de `Rectangle` ou `Circle`. Par exemple, une fonction de "constructeur" qui retourne l'objet et une erreur.

#### Étape 3.3 : Réflexion sur les *receivers*

1.  Expliquez en quelques phrases la différence fondamentale entre un *receiver* de valeur et un *receiver* de pointeur pour une méthode en Go.
2.  Justifiez pourquoi vous avez utilisé un *receiver* de pointeur pour les méthodes `Move()` et `Scale()`, et un *receiver* de valeur pour les méthodes `Area()`, `Perimeter()`, `Width()`, `Height()`, `DistanceTo()` et `Circumference()`.

---

### Consignes Générales :

*   Organisez votre code dans un seul fichier `main.go`.
*   Utilisez des noms de variables et de fonctions clairs et significatifs.
*   Commentez votre code si nécessaire pour expliquer des choix ou des parties complexes.
*   N'oubliez pas d'importer les packages nécessaires (`fmt`, `math`).

Bon courage !