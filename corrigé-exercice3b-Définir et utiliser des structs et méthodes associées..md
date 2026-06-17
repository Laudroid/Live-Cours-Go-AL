Voici une solution concise et commentée pour le TP "Modélisation d'Entités avec Structs et Méthodes".

---

### Solution TP : Modélisation d'Entités avec Structs et Méthodes

Pour commencer, créez un nouveau répertoire pour votre projet et initialisez un module Go :



```bash
mkdir go_structs_methods
cd go_structs_methods
go mod init go_structs_methods
```



Ensuite, créez un fichier `main.go` dans ce répertoire. Tout le code des exercices sera placé dans ce fichier.

#### `main.go`



```go
package main

import (
	"errors" // Pour créer des erreurs personnalisées
	"fmt"    // Pour les fonctions d'entrée/sortie formatées
	"math"   // Pour les fonctions mathématiques (Pow, Sqrt, Pi)
)

// --- Exercice 1 : Modélisation d'un Point 2D et d'un Rectangle ---

// Étape 1.1 : Définir la struct Point
type Point struct {
	X, Y float64
}

// Étape 1.2 : Méthode DistanceTo() pour Point
// DistanceTo calcule la distance euclidienne entre deux points.
// C'est une méthode avec un receiver de valeur car elle ne modifie pas le point récepteur.
func (p Point) DistanceTo(other Point) float64 {
	dx := p.X - other.X
	dy := p.Y - other.Y
	return math.Sqrt(math.Pow(dx, 2) + math.Pow(dy, 2))
}

// Étape 1.3 : Définir la struct Rectangle
type Rectangle struct {
	Min Point // Coin inférieur gauche
	Max Point // Coin supérieur droit
}

// Étape 1.4 : Méthodes pour Rectangle

// Width retourne la largeur du rectangle. Receiver de valeur.
func (r Rectangle) Width() float64 {
	return r.Max.X - r.Min.X
}

// Height retourne la hauteur du rectangle. Receiver de valeur.
func (r Rectangle) Height() float64 {
	return r.Max.Y - r.Min.Y
}

// Area retourne la surface du rectangle. Receiver de valeur.
func (r Rectangle) Area() float64 {
	return r.Width() * r.Height()
}

// Perimeter retourne le périmètre du rectangle. Receiver de valeur.
func (r Rectangle) Perimeter() float64 {
	return 2 * (r.Width() + r.Height())
}

// Move déplace le rectangle par dx et dy.
// C'est une méthode avec un receiver de pointeur (*Rectangle) car elle modifie l'état
// (les coordonnées Min et Max) du rectangle récepteur.
func (r *Rectangle) Move(dx, dy float64) {
	r.Min.X += dx
	r.Min.Y += dy
	r.Max.X += dx
	r.Max.Y += dy
}

// --- Exercice 2 : Modélisation d'un Cercle ---

// Étape 2.1 : Définir la struct Circle
type Circle struct {
	Center Point
	Radius float64
}

// Étape 2.2 : Méthodes pour Circle

// Area retourne la surface du cercle. Receiver de valeur.
func (c Circle) Area() float64 {
	return math.Pi * math.Pow(c.Radius, 2)
}

// Circumference retourne la circonférence du cercle. Receiver de valeur.
func (c Circle) Circumference() float64 {
	return 2 * math.Pi * c.Radius
}

// Scale modifie le rayon du cercle par un facteur.
// C'est une méthode avec un receiver de pointeur (*Circle) car elle modifie l'état
// (le champ Radius) du cercle récepteur.
func (c *Circle) Scale(factor float64) {
	if factor > 0 { // Évite les rayons négatifs ou nuls
		c.Radius *= factor
	}
}

// --- Exercice 3 : Améliorations et Réflexion ---

// Étape 3.1 : Méthode String() pour Rectangle
// La méthode String() est une convention en Go. Si elle est définie,
// fmt.Println() l'appellera automatiquement pour afficher l'objet.
func (r Rectangle) String() string {
	return fmt.Sprintf("Rectangle [Min:(%.2f,%.2f), Max:(%.2f,%.2f)] - Largeur: %.2f, Hauteur: %.2f, Surface: %.2f, Périmètre: %.2f",
		r.Min.X, r.Min.Y, r.Max.X, r.Max.Y, r.Width(), r.Height(), r.Area(), r.Perimeter())
}

// Étape 3.1 : Méthode String() pour Circle
func (c Circle) String() string {
	return fmt.Sprintf("Cercle [Centre:(%.2f,%.2f), Rayon:%.2f] - Surface: %.2f, Circonférence: %.2f",
		c.Center.X, c.Center.Y, c.Radius, c.Area(), c.Circumference())
}

// Étape 3.2 : Gestion des entrées invalides (Fonctions de "constructeur")

// NewRectangle est une fonction de constructeur qui valide les entrées.
// Elle retourne un pointeur vers un Rectangle et une erreur si les dimensions sont invalides.
func NewRectangle(minX, minY, maxX, maxY float64) (*Rectangle, error) {
	if maxX < minX || maxY < minY {
		return nil, errors.New("les coordonnées Max doivent être supérieures aux coordonnées Min")
	}
	// On peut aussi vérifier que la largeur/hauteur ne sont pas nulles si c'est une contrainte métier
	if maxX == minX || maxY == minY {
		return nil, errors.New("la largeur ou la hauteur du rectangle ne peut pas être nulle")
	}
	return &Rectangle{
		Min: Point{X: minX, Y: minY},
		Max: Point{X: maxX, Y: maxY},
	}, nil
}

// NewCircle est une fonction de constructeur qui valide les entrées.
func NewCircle(centerX, centerY, radius float64) (*Circle, error) {
	if radius <= 0 {
		return nil, errors.New("le rayon du cercle doit être strictement positif")
	}
	return &Circle{
		Center: Point{X: centerX, Y: centerY},
		Radius: radius,
	}, nil
}

// --- Fonction main pour les tests ---
func main() {
	fmt.Println("--- Exercice 1 : Point et Rectangle ---")

	// Étape 1.5 : Utilisation dans main
	p1 := Point{X: 1.0, Y: 1.0}
	p2 := Point{X: 4.0, Y: 5.0}
	fmt.Printf("Point 1: (%.2f,%.2f), Point 2: (%.2f,%.2f)\n", p1.X, p1.Y, p2.X, p2.Y)
	fmt.Printf("Distance entre p1 et p2: %.2f\n", p1.DistanceTo(p2))

	// Création d'un rectangle avec validation via le constructeur
	rect, err := NewRectangle(0.0, 0.0, 5.0, 3.0)
	if err != nil {
		fmt.Println("Erreur lors de la création du rectangle:", err)
		return // Arrête le programme si le rectangle n'est pas valide
	}
	fmt.Println("Rectangle initial:", rect) // La méthode String() est appelée ici

	fmt.Printf("Largeur du rectangle: %.2f\n", rect.Width())
	fmt.Printf("Hauteur du rectangle: %.2f\n", rect.Height())
	fmt.Printf("Surface du rectangle: %.2f\n", rect.Area())
	fmt.Printf("Périmètre du rectangle: %.2f\n", rect.Perimeter())

	fmt.Println("Déplacement du rectangle de (1.0, 2.0)...")
	rect.Move(1.0, 2.0) // Déplace le rectangle
	fmt.Println("Rectangle après déplacement:", rect)
	fmt.Println("")

	fmt.Println("--- Exercice 2 : Cercle ---")

	// Étape 2.3 : Utilisation dans main
	// Création d'un cercle avec validation via le constructeur
	circle, err := NewCircle(10.0, 10.0, 5.0)
	if err != nil {
		fmt.Println("Erreur lors de la création du cercle:", err)
		return // Arrête le programme si le cercle n'est pas valide
	}
	fmt.Println("Cercle initial:", circle) // La méthode String() est appelée ici

	fmt.Printf("Surface du cercle: %.2f\n", circle.Area())
	fmt.Printf("Circonférence du cercle: %.2f\n", circle.Circumference())

	fmt.Println("Mise à l'échelle du cercle par un facteur de 2.0...")
	circle.Scale(2.0) // Double le rayon
	fmt.Println("Cercle après mise à l'échelle:", circle)
	fmt.Println("")

	fmt.Println("--- Exercice 3 : Réflexion sur les Receivers ---")
	fmt.Println("Étape 3.3 : Réflexion sur les receivers")
	fmt.Println("Différence fondamentale entre un receiver de valeur et un receiver de pointeur:")
	fmt.Println("- **Receiver de valeur (ex: `func (p Point) ...`) :** La méthode reçoit une *copie* de l'instance de la struct. Toute modification apportée aux champs de la struct à l'intérieur de la méthode n'affectera que cette copie et non l'instance originale. C'est idéal pour les méthodes qui lisent les données de la struct sans les modifier.")
	fmt.Println("- **Receiver de pointeur (ex: `func (p *Point) ...`) :** La méthode reçoit un *pointeur* vers l'instance de la struct. Cela signifie que la méthode peut accéder et modifier directement l'instance originale de la struct. C'est nécessaire pour les méthodes qui doivent changer l'état de la struct.")
	fmt.Println("")
	fmt.Println("Justification des choix de receivers:")
	fmt.Println("- **`Move()` et `Scale()` (receivers de pointeur) :** Ces méthodes ont pour objectif de modifier les coordonnées du `Rectangle` ou le rayon du `Circle`. Pour que ces modifications soient persistantes sur l'instance originale de la struct, un receiver de pointeur (`*Rectangle`, `*Circle`) est indispensable. Si un receiver de valeur avait été utilisé, les modifications n'auraient affecté qu'une copie temporaire.")
	fmt.Println("- **`Area()`, `Perimeter()`, `Width()`, `Height()`, `DistanceTo()`, `Circumference()` (receivers de valeur) :** Ces méthodes calculent et retournent une valeur basée sur les données de la struct, sans jamais modifier ces données. Un receiver de valeur est donc approprié et suffisant. Il est généralement préférable d'utiliser un receiver de valeur quand aucune modification n'est nécessaire, car cela évite les risques d'effets de bord inattendus et peut parfois être plus performant pour de petites structs (moins de déréférencement de pointeur).")
}

```



#### Exécution

Pour exécuter ce code, naviguez dans votre terminal jusqu'au répertoire `go_structs_methods` et lancez :



```bash
go run main.go
```



#### Explications Clés

1.  **`struct` (Structures) :**
    *   Les `structs` sont des types composites qui regroupent des champs (variables) de différents types sous un même nom. Elles sont utilisées pour modéliser des entités complexes.
    *   Exemple : `type Point struct { X, Y float64 }`

2.  **Méthodes :**
    *   Une méthode est une fonction associée à un type spécifique (le `receiver`).
    *   La syntaxe `func (receiver Type) NomMethode(...)` définit une méthode.
    *   **Receiver de valeur (`(p Point)`) :** La méthode opère sur une *copie* de la struct. Les modifications à l'intérieur de la méthode n'affectent pas l'instance originale. Idéal pour les méthodes qui ne font que lire ou calculer.
    *   **Receiver de pointeur (`(p *Point)`) :** La méthode opère sur l'instance originale de la struct via son pointeur. Les modifications à l'intérieur de la méthode affectent l'instance originale. Indispensable pour les méthodes qui doivent modifier l'état de la struct.

3.  **Package `math` :**
    *   Fournit des fonctions mathématiques courantes comme `math.Pow(base, exposant)` pour la puissance, `math.Sqrt(x)` pour la racine carrée, et la constante `math.Pi`.

4.  **Méthode `String() string` :**
    *   C'est une convention en Go. Si une `struct` implémente cette méthode, `fmt.Println()` et `fmt.Printf("%v", ...)` l'appelleront automatiquement pour obtenir une représentation textuelle de l'objet, rendant l'affichage plus lisible.

5.  **Fonctions de "Constructeur" (`New...`) :**
    *   Bien que Go n'ait pas de constructeurs au sens strict des langages orientés objet classiques, il est courant de définir des fonctions préfixées par `New` (ex: `NewRectangle`, `NewCircle`).
    *   Ces fonctions sont utilisées pour créer et initialiser des instances de `struct`, souvent en incluant une logique de validation des données.
    *   Elles retournent généralement un pointeur vers la `struct` nouvellement créée et une `error` si la validation échoue, permettant une gestion explicite des erreurs.

Ce TP vous a permis de comprendre comment les `structs` et les `méthodes` en Go sont utilisées pour créer des types de données personnalisés avec des comportements associés, ce qui est une pierre angulaire de la conception de logiciels modulaires et maintenables en Go.