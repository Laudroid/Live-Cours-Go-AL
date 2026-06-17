Voici une solution concise et commentée pour le TP "Fonctions Variadiques et Retours Multiples en Go".

---

### Solution TP : Fonctions Variadiques et Retours Multiples en Go

Pour commencer, créez un nouveau répertoire pour votre projet et initialisez un module Go :


```bash
mkdir go_variadic_multi_return
cd go_variadic_multi_return
go mod init go_variadic_multi_return
```


Ensuite, créez un fichier `main.go` dans ce répertoire. Tout le code des exercices sera placé dans ce fichier.

#### `main.go`


```go
package main

import (
	"errors" // Pour créer des erreurs personnalisées
	"fmt"    // Pour les fonctions d'entrée/sortie formatées
	"math"   // Pour les fonctions mathématiques comme MaxFloat64 et MinFloat64
)

// Exercice 1 : Calcul de Statistiques de Base
// CalculerStatistiquesBase prend un nombre variable d'entiers et retourne leur somme,
// leur nombre total et leur moyenne.
func CalculerStatistiquesBase(nombres ...int) (somme int, count int, moyenne float64) {
	count = len(nombres) // Le nombre d'arguments est la longueur du slice 'nombres'

	if count == 0 {
		// Si aucun nombre n'est fourni, la somme et la moyenne sont 0.
		// Les valeurs de retour nommées (somme, count, moyenne) sont déjà initialisées à leurs "zero values".
		return // Retourne 0, 0, 0.0
	}

	for _, num := range nombres {
		somme += num // Ajoute chaque nombre à la somme
	}

	moyenne = float64(somme) / float64(count) // Calcule la moyenne, en s'assurant que la division est flottante
	return
}

// Exercice 2 : Statistiques Complètes avec Gestion d'Erreurs
// CalculerStatistiquesCompletes prend un nombre variable de float64 et retourne
// le min, max, somme, moyenne, count et une erreur si aucun argument n'est fourni.
func CalculerStatistiquesCompletes(nombres ...float64) (min, max, sum, avg float64, count int, err error) {
	count = len(nombres)

	if count == 0 {
		// Retourne une erreur explicite si aucun argument n'est fourni.
		// Les autres valeurs de retour sont leurs "zero values" par défaut.
		err = errors.New("aucun argument fourni")
		return
	}

	// Initialisation de min et max avec les valeurs extrêmes pour s'assurer que le premier élément
	// sera correctement comparé. Ou simplement initialiser avec le premier élément.
	min = math.MaxFloat64 // La plus grande valeur possible pour un float64
	max = math.SmallestNonzeroFloat64 // La plus petite valeur positive non nulle pour un float64 (ou 0.0)
	// Une alternative plus simple est d'initialiser min et max avec le premier élément.
	// min = nombres[0]
	// max = nombres[0]

	for _, num := range nombres {
		sum += num
		if num < min {
			min = num
		}
		if num > max {
			max = num
		}
	}

	avg = sum / float64(count)
	return
}

// Exercice 3 : Analyse de Données de Capteur
// AnalyserDonneesCapteur filtre les relevés de température valides (> 0.0 et <= 100.0)
// et retourne des statistiques sur ces relevés, ainsi que le compte des relevés valides/invalides.
func AnalyserDonneesCapteur(releves ...float64) (minTemp, maxTemp, avgTemp float64, validCnt, invalidCnt int, err error) {
	var relevesValides []float64 // Slice pour stocker les relevés qui passent le filtre

	for _, temp := range releves {
		if temp > 0.0 && temp <= 100.0 { // Condition de validité pour la température en Celsius
			relevesValides = append(relevesValides, temp)
			validCnt++
		} else {
			invalidCnt++
		}
	}

	if validCnt == 0 {
		// Si aucun relevé valide n'est trouvé après filtrage
		err = errors.New("aucun relevé valide trouvé après filtrage")
		return
	}

	// Utilise la fonction CalculerStatistiquesCompletes sur les relevés valides
	// On ignore les valeurs de retour sum, count et l'erreur car on sait qu'il y a des éléments valides.
	minTemp, maxTemp, _, avgTemp, _, _ = CalculerStatistiquesCompletes(relevesValides...)
	return
}

func main() {
	fmt.Println("--- Exercice 1 : Calcul de Statistiques de Base ---")
	somme, count, moyenne := CalculerStatistiquesBase(10, 20, 30, 40)
	fmt.Printf("Ensemble (10, 20, 30, 40) -> Somme: %d, Count: %d, Moyenne: %.2f\n", somme, count, moyenne)

	sommeUn, countUn, moyenneUn := CalculerStatistiquesBase(50)
	fmt.Printf("Ensemble (50) -> Somme: %d, Count: %d, Moyenne: %.2f\n", sommeUn, countUn, moyenneUn)

	sommeVide, countVide, moyenneVide := CalculerStatistiquesBase()
	fmt.Printf("Ensemble vide -> Somme: %d, Count: %d, Moyenne: %.2f\n", sommeVide, countVide, moyenneVide)
	fmt.Println("")

	fmt.Println("--- Exercice 2 : Statistiques Complètes avec Gestion d'Erreurs ---")
	min, max, sum, avg, count, err := CalculerStatistiquesCompletes(1.5, 2.8, 0.7, 3.1)
	if err != nil {
		fmt.Println("Erreur:", err)
	} else {
		fmt.Printf("Ensemble (1.5, 2.8, 0.7, 3.1) -> Min: %.2f, Max: %.2f, Somme: %.2f, Moyenne: %.2f, Count: %d\n", min, max, sum, avg, count)
	}

	minUn, maxUn, sumUn, avgUn, countUn, errUn := CalculerStatistiquesCompletes(99.9)
	if errUn != nil {
		fmt.Println("Erreur:", errUn)
	} else {
		fmt.Printf("Ensemble (99.9) -> Min: %.2f, Max: %.2f, Somme: %.2f, Moyenne: %.2f, Count: %d\n", minUn, maxUn, sumUn, avgUn, countUn)
	}

	_, _, _, _, _, errVide := CalculerStatistiquesCompletes()
	if errVide != nil {
		fmt.Println("Ensemble vide -> Erreur:", errVide)
	}
	fmt.Println("")

	fmt.Println("--- Exercice 3 : Analyse de Données de Capteur ---")
	minTemp, maxTemp, avgTemp, validCnt, invalidCnt, errAnalyse := AnalyserDonneesCapteur(22.5, 23.1, -5.0, 101.0, 21.9, 0.0, 24.0)
	if errAnalyse != nil {
		fmt.Println("Erreur d'analyse:", errAnalyse)
	} else {
		fmt.Printf("Données (22.5, 23.1, -5.0, 101.0, 21.9, 0.0, 24.0) -> Temp Min: %.2f, Max: %.2f, Moyenne: %.2f, Valides: %d, Invalides: %d\n", minTemp, maxTemp, avgTemp, validCnt, invalidCnt)
	}

	minTemp2, maxTemp2, avgTemp2, validCnt2, invalidCnt2, errAnalyse2 := AnalyserDonneesCapteur(10.0, 20.0, 30.0)
	if errAnalyse2 != nil {
		fmt.Println("Erreur d'analyse:", errAnalyse2)
	} else {
		fmt.Printf("Données (10.0, 20.0, 30.0) -> Temp Min: %.2f, Max: %.2f, Moyenne: %.2f, Valides: %d, Invalides: %d\n", minTemp2, maxTemp2, avgTemp2, validCnt2, invalidCnt2)
	}

	_, _, _, _, _, errToutInvalide := AnalyserDonneesCapteur(-10.0, 105.0, 0.0, 100.1)
	if errToutInvalide != nil {
		fmt.Println("Données toutes invalides -> Erreur:", errToutInvalide)
	}
	fmt.Println("")
}

```


#### Exécution

Pour exécuter ce code, naviguez dans votre terminal jusqu'au répertoire `go_variadic_multi_return` et lancez :


```bash
go run main.go
```


#### Explications Clés

1.  **Fonctions Variadiques (`...type`) :**
    *   Une fonction variadique accepte un nombre variable d'arguments du même type.
    *   Dans la signature de la fonction (ex: `nombres ...int`), les arguments sont reçus comme un *slice* du type spécifié.
    *   Vous pouvez passer des arguments individuels (ex: `CalculerStatistiquesBase(1, 2, 3)`) ou un slice existant en utilisant l'opérateur `...` (ex: `CalculerStatistiquesCompletes(monSlice...)`).

2.  **Retours Multiples :**
    *   Go permet à une fonction de retourner plusieurs valeurs. C'est couramment utilisé pour retourner un résultat et une erreur (ex: `(resultat type, err error)`).
    *   Les valeurs de retour peuvent être nommées (ex: `(somme int, count int, moyenne float64)`). Si elles sont nommées, elles sont automatiquement initialisées à leur "zero value" et peuvent être retournées avec un simple `return` sans spécifier les variables.
    *   L'ordre des valeurs de retour est important lors de l'affectation.

3.  **Gestion des Erreurs (`error`) :**
    *   En Go, la gestion des erreurs est explicite. Les fonctions qui peuvent échouer retournent généralement une valeur de type `error` comme dernière valeur de retour.
    *   `errors.New("message d'erreur")` est utilisé pour créer une nouvelle erreur simple.
    *   Il est crucial de toujours vérifier la valeur de l'erreur retournée (ex: `if err != nil { ... }`) et de la gérer de manière appropriée.

4.  **Initialisation `min` et `max` :**
    *   Pour trouver le minimum et le maximum dans un ensemble de nombres, il est courant d'initialiser `min` avec la plus grande valeur possible du type (`math.MaxFloat64` pour `float64`) et `max` avec la plus petite (`math.SmallestNonzeroFloat64` ou 0.0 pour `float64`).
    *   Une alternative plus simple, si vous êtes sûr que le slice n'est pas vide, est d'initialiser `min` et `max` avec le premier élément du slice.

5.  **Filtrage et Composition de Fonctions (Exercice 3) :**
    *   L'Exercice 3 montre comment filtrer des données brutes pour ne conserver que les éléments valides.
    *   Il illustre également la composition de fonctions, où `AnalyserDonneesCapteur` réutilise `CalculerStatistiquesCompletes` sur les données filtrées. Cela favorise la réutilisabilité du code et maintient les fonctions concises et dédiées à une tâche spécifique.
    *   L'opérateur `...` est utilisé pour "déballer" le slice `relevesValides` en arguments individuels pour la fonction `CalculerStatistiquesCompletes`.

Ces concepts sont fondamentaux en Go pour écrire des APIs de fonctions flexibles et gérer les cas d'erreur de manière robuste.