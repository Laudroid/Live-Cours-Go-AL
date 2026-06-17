Voici une solution concise et commentée pour le TP "Maîtrise des Types, Variables et Constantes en Go".

---

### Solution TP : Maîtrise des Types, Variables et Constantes en Go

Pour commencer, créez un nouveau répertoire pour votre projet et initialisez un module Go :


```bash
mkdir go_types_vars_consts
cd go_types_vars_consts
go mod init go_types_vars_consts
```


Ensuite, créez un fichier `main.go` dans ce répertoire. Tout le code des exercices sera placé dans ce fichier.

#### `main.go`


```go
package main

import "fmt" // Importe le package 'fmt' pour les fonctions de formatage et d'impression

func main() {
	fmt.Println("--- Exercice 1 : Déclaration Explicite de Variables ---")
	// Déclaration explicite de variables avec leur type et initialisation
	var nomUtilisateur string = "Alice Dupont"
	var ageUtilisateur int = 30
	var estConnecte bool = true
	var soldeCompte float64 = 1500.75

	// Affichage des valeurs
	fmt.Println("Nom de l'utilisateur :", nomUtilisateur)
	fmt.Println("Âge de l'utilisateur :", ageUtilisateur)
	fmt.Println("Est connecté :", estConnecte)
	fmt.Println("Solde du compte :", soldeCompte)
	fmt.Println("") // Ligne vide pour la lisibilité

	fmt.Println("--- Exercice 2 : Inférence de Type avec l'Opérateur := ---")
	// Déclaration et initialisation avec inférence de type (opérateur :=)
	villeResidence := "Paris"
	codePostal := 75001
	tauxRemise := 0.15 // Go infère float64 par défaut pour les littéraux décimaux

	// Affichage des valeurs et de leurs types inférés
	fmt.Printf("Ville de résidence : %v (Type : %T)\n", villeResidence, villeResidence)
	fmt.Printf("Code postal : %v (Type : %T)\n", codePostal, codePostal)
	fmt.Printf("Taux de remise : %v (Type : %T)\n", tauxRemise, tauxRemise)
	fmt.Println("")

	fmt.Println("--- Exercice 3 : Manipulation de Constantes ---")
	// Déclaration de constantes
	const PI = 3.14159
	const NOM_APPLICATION = "Gestionnaire Go"
	const ANNEE_LANCEMENT = 2023

	// Utilisation de PI pour calculer la circonférence
	rayon := 10.5
	circonference := 2 * PI * rayon
	fmt.Printf("Circonférence d'un cercle de rayon %.2f : %.2f\n", rayon, circonference)

	// Affichage des constantes
	fmt.Println("Constante PI :", PI)
	fmt.Println("Constante NOM_APPLICATION :", NOM_APPLICATION)
	fmt.Println("Constante ANNEE_LANCEMENT :", ANNEE_LANCEMENT)

	// Tenter de modifier une constante (décommenter la ligne ci-dessous pour voir l'erreur)
	// ANNEE_LANCEMENT = 2024 // Ceci provoquera une erreur de compilation : "cannot assign to ANNEE_LANCEMENT"
	fmt.Println("Tentative de modification de ANNEE_LANCEMENT = 2024 (erreur de compilation attendue si décommenté).")
	fmt.Println("")

	fmt.Println("--- Exercice 4 : Réaffectation et Valeurs par Défaut ---")
	// Modification de la valeur d'une variable
	fmt.Println("Ancien âge utilisateur :", ageUtilisateur)
	ageUtilisateur = 31 // Réaffectation de la variable
	fmt.Println("Nouvel âge utilisateur (après anniversaire) :", ageUtilisateur)

	// Déclaration de variables sans initialisation (illustre les "zero values")
	var message string // Valeur par défaut pour string est "" (chaîne vide)
	var compteur int    // Valeur par défaut pour int est 0
	var estValide bool  // Valeur par défaut pour bool est false
	var prix float64    // Valeur par défaut pour float64 est 0.0

	fmt.Printf("Valeur par défaut de 'message' (string non initialisée) : '%s'\n", message)
	fmt.Printf("Valeur par défaut de 'compteur' (int non initialisé) : %d\n", compteur)
	fmt.Printf("Valeur par défaut de 'estValide' (bool non initialisé) : %t\n", estValide)
	fmt.Printf("Valeur par défaut de 'prix' (float64 non initialisé) : %.1f\n", prix)
	fmt.Println("")

	fmt.Println("--- Aller plus loin (Bonus) ---")
	// Déclaration multiple de variables du même type
	var a, b, c int // Déclare trois entiers non initialisés (valeur par défaut 0)
	a = 10
	b = 20
	c = 30
	fmt.Printf("Variables multiples : a=%d, b=%d, c=%d\n", a, b, c)

	// Constantes énumérées avec iota
	const (
		Lundi = iota // Lundi = 0
		Mardi        // Mardi = 1
		Mercredi     // Mercredi = 2
		Jeudi        // Jeudi = 3
		Vendredi     // Vendredi = 4
		Samedi       // Samedi = 5
		Dimanche     // Dimanche = 6
	)
	fmt.Println("Jours de la semaine avec iota :")
	fmt.Printf("Lundi: %d, Mardi: %d, Mercredi: %d, Jeudi: %d, Vendredi: %d, Samedi: %d, Dimanche: %d\n",
		Lundi, Mardi, Mercredi, Jeudi, Vendredi, Samedi, Dimanche)

	// Conversion de type
	entier := 10
	decimal := 3.14
	// fmt.Println(entier + decimal) // Erreur de compilation : "mismatched types int and float64"
	// Conversion explicite nécessaire
	resultatConversion := float64(entier) + decimal // Convertit 'entier' en float64
	fmt.Printf("Résultat de l'opération avec conversion (int en float64) : %.2f\n", resultatConversion)

	// Conversion de float64 en int (tronque la partie décimale)
	resultatInt := int(decimal)
	fmt.Printf("Conversion de %.2f (float64) en int : %d\n", decimal, resultatInt)
}
```


#### Exécution

Pour exécuter ce code, naviguez dans votre terminal jusqu'au répertoire `go_types_vars_consts` et lancez :


```bash
go run main.go
```


#### Explications Clés

*   **Déclaration explicite (`var nom Type = valeur`) :** Permet de définir précisément le type d'une variable. C'est utile pour la clarté ou lorsque la valeur initiale n'est pas immédiatement disponible.
*   **Inférence de type (`nom := valeur`) :** L'opérateur `:=` est une forme courte de déclaration et d'initialisation. Go déduit automatiquement le type de la variable à partir de la valeur assignée. C'est la méthode la plus courante en Go pour les nouvelles variables dans une fonction.
*   **Constantes (`const NOM = valeur`) :** Les constantes sont des valeurs immuables, définies à la compilation. Elles ne peuvent pas être modifiées après leur déclaration. Elles sont idéales pour des valeurs fixes comme `PI` ou des paramètres de configuration.
*   **Valeurs par défaut (Zero Values) :** En Go, toutes les variables sont automatiquement initialisées avec une "zero value" si aucune valeur n'est spécifiée. Pour les types numériques, c'est `0` (ou `0.0`), pour les booléens c'est `false`, et pour les chaînes de caractères c'est `""` (une chaîne vide). Cela évite les erreurs de variables non initialisées.
*   **`fmt.Printf` et `%T` :** La fonction `fmt.Printf` permet un formatage de sortie plus complexe. Le verbe de format `%T` est particulièrement utile pour afficher le type d'une variable, ce qui aide à comprendre l'inférence de type.
*   **`iota` :** C'est un générateur de constantes séquentielles, utilisé dans les blocs `const`. À chaque nouvelle ligne dans un bloc `const`, `iota` s'incrémente automatiquement, permettant de définir facilement des énumérations.
*   **Conversion de type :** Go est un langage fortement typé. Les opérations entre types différents nécessitent une conversion explicite (ex: `float64(entier)`). Cela garantit que le développeur est conscient des implications de la conversion (comme la perte de précision lors de la conversion d'un `float64` en `int`).

Ce TP vous a permis de manipuler les briques fondamentales de la gestion des données en Go. La compréhension de ces concepts est essentielle pour écrire du code robuste et maintenable.