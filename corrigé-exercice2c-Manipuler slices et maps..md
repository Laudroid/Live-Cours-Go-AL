Voici une solution concise et commentée pour le TP "Maîtrise des Slices et Maps pour la Gestion de Stock".

---

### Solution TP : Maîtrise des Slices et Maps pour la Gestion de Stock

Pour commencer, créez un nouveau répertoire pour votre projet et initialisez un module Go :


```bash
mkdir go_stock_management
cd go_stock_management
go mod init go_stock_management
```


Ensuite, créez un fichier `main.go` dans ce répertoire. Tout le code des exercices sera placé dans ce fichier.

#### `main.go`


```go
package main

import (
	"fmt"
	"math/rand" // Pour générer des données aléatoires
	"time"      // Pour mesurer le temps d'exécution
)

// Définition de la structure Produit
type Produit struct {
	ID        int
	Nom       string
	Prix      float64
	Categorie string
}

func main() {
	fmt.Println("--- Partie 1 : Gestion des Catégories (Slices) ---")

	// 1.1 Initialisation et Ajout
	var categories []string // Déclaration d'un slice de chaînes
	categories = []string{"Électronique", "Vêtements", "Livres"}
	fmt.Println("Catégories initiales:", categories)

	categories = append(categories, "Alimentation", "Maison") // Ajout de nouvelles catégories
	fmt.Println("Catégories après ajout:", categories)
	fmt.Println("")

	// 1.2 Vérification et Suppression
	fmt.Println("Vérification de l'existence de catégories:")
	fmt.Printf("La catégorie 'Vêtements' existe-t-elle ? %t\n", categorieExiste("Vêtements", categories))
	fmt.Printf("La catégorie 'Jouets' existe-t-elle ? %t\n", categorieExiste("Jouets", categories))
	fmt.Println("")

	fmt.Println("Suppression de catégories:")
	categories = supprimerCategorie("Livres", categories)
	fmt.Println("Catégories après suppression de 'Livres':", categories)

	categories = supprimerCategorie("Inexistant", categories) // Tente de supprimer une catégorie inexistante
	fmt.Println("Catégories après tentative de suppression de 'Inexistant':", categories)
	fmt.Println("")

	// 1.3 Capacité et Croissance
	fmt.Printf("Longueur (len) du slice 'categories': %d\n", len(categories))
	fmt.Printf("Capacité (cap) du slice 'categories': %d\n", cap(categories))
	fmt.Println("Explication: La longueur (len) est le nombre d'éléments actuellement présents dans le slice. La capacité (cap) est le nombre maximal d'éléments que le slice peut contenir sans qu'une nouvelle allocation mémoire ne soit nécessaire. Lorsque le slice grandit au-delà de sa capacité, Go alloue un nouveau tableau sous-jacent plus grand et copie les éléments existants, ce qui peut être coûteux.")
	fmt.Println("")

	fmt.Println("--- Partie 2 : Gestion des Produits et du Stock (Maps) ---")

	// 2.1 Définition des Structures (déjà faite en dehors de main)
	inventaireProduits := make(map[int]Produit) // Map pour les détails des produits
	stockProduits := make(map[int]int)          // Map pour les quantités en stock

	// 2.2 Ajout et Modification
	inventaireProduits[101] = Produit{ID: 101, Nom: "Smartphone X", Prix: 799.99, Categorie: "Électronique"}
	stockProduits[101] = 50

	inventaireProduits[102] = Produit{ID: 102, Nom: "T-shirt Coton", Prix: 19.99, Categorie: "Vêtements"}
	stockProduits[102] = 200

	inventaireProduits[103] = Produit{ID: 103, Nom: "Livre Go", Prix: 25.00, Categorie: "Livres"}
	stockProduits[103] = 75

	fmt.Println("Produits initiaux:")
	afficherTousProduits(inventaireProduits, stockProduits)
	fmt.Println("")

	// Modification du prix d'un produit
	p := inventaireProduits[101]
	p.Prix = 749.99 // Modification de la copie de la struct
	inventaireProduits[101] = p // Réaffectation de la struct modifiée
	fmt.Println("Après modification du prix du Smartphone X:")
	afficherTousProduits(inventaireProduits, stockProduits)
	fmt.Println("")

	// Mise à jour de la quantité en stock
	stockProduits[102] = 220
	fmt.Println("Après mise à jour du stock du T-shirt Coton:")
	afficherTousProduits(inventaireProduits, stockProduits)
	fmt.Println("")

	// 2.3 Recherche et Suppression
	fmt.Println("Recherche de produits:")
	prod, qte, existe := obtenirProduit(101, inventaireProduits, stockProduits)
	if existe {
		fmt.Printf("Produit trouvé (ID 101): %+v, Stock: %d\n", prod, qte)
	} else {
		fmt.Println("Produit ID 101 non trouvé.")
	}

	_, _, existe = obtenirProduit(999, inventaireProduits, stockProduits)
	if !existe {
		fmt.Println("Produit ID 999 non trouvé (attendu).")
	}
	fmt.Println("")

	fmt.Println("Suppression de produit (ID 102):")
	delete(inventaireProduits, 102)
	delete(stockProduits, 102)
	fmt.Println("Inventaire après suppression:")
	afficherTousProduits(inventaireProduits, stockProduits)
	fmt.Println("")

	// 2.4 Opérations de Stock
	fmt.Println("Opérations de stock:")
	fmt.Printf("Stock initial Smartphone X (ID 101): %d\n", stockProduits[101])
	if vendreProduit(101, 5, stockProduits) {
		fmt.Printf("Vente de 5 Smartphone X réussie. Nouveau stock: %d\n", stockProduits[101])
	} else {
		fmt.Println("Vente de 5 Smartphone X échouée (stock insuffisant).")
	}

	if vendreProduit(101, 100, stockProduits) { // Tentative de vente excessive
		fmt.Printf("Vente de 100 Smartphone X réussie. Nouveau stock: %d\n", stockProduits[101])
	} else {
		fmt.Printf("Vente de 100 Smartphone X échouée (stock insuffisant). Stock actuel: %d\n", stockProduits[101])
	}

	reapprovisionnerProduit(101, 20, stockProduits)
	fmt.Printf("Réapprovisionnement de 20 Smartphone X. Nouveau stock: %d\n", stockProduits[101])
	fmt.Println("")

	fmt.Println("--- Partie 3 : Combinaison Slices et Maps & Performance ---")

	// 3.1 Indexation par Catégorie
	produitsParCategorie := make(map[string][]int)
	for _, prod := range inventaireProduits {
		produitsParCategorie[prod.Categorie] = append(produitsParCategorie[prod.Categorie], prod.ID)
	}

	fmt.Println("Liste des produits par catégorie 'Électronique':")
	listerProduitsParCategorie("Électronique", inventaireProduits, produitsParCategorie)
	fmt.Println("")

	fmt.Println("Liste des produits par catégorie 'Maison':")
	listerProduitsParCategorie("Maison", inventaireProduits, produitsParCategorie) // Catégorie sans produits
	fmt.Println("")

	// 3.2 Performance des Maps (Grand Volume)
	fmt.Println("Test de performance des Maps avec un grand volume de données...")
	const numProduits = 100000
	rand.Seed(time.Now().UnixNano()) // Initialisation du générateur de nombres aléatoires

	// Test 1: Ajout sans pré-allocation
	fmt.Println("  Test 1: Ajout de", numProduits, "produits sans pré-allocation")
	start := time.Now()
	inventaireSansPrealloc := make(map[int]Produit)
	stockSansPrealloc := make(map[int]int)
	categoriesDisponibles := []string{"Électronique", "Vêtements", "Livres", "Alimentation", "Maison"}

	for i := 0; i < numProduits; i++ {
		id := i + 200 // IDs à partir de 200
		inventaireSansPrealloc[id] = Produit{
			ID:        id,
			Nom:       fmt.Sprintf("Produit_%d", id),
			Prix:      rand.Float64() * 1000,
			Categorie: categoriesDisponibles[rand.Intn(len(categoriesDisponibles))],
		}
		stockSansPrealloc[id] = rand.Intn(500)
	}
	elapsed := time.Since(start)
	fmt.Printf("    Temps d'ajout sans pré-allocation: %s\n", elapsed)

	// Test 2: Ajout avec pré-allocation
	fmt.Println("  Test 2: Ajout de", numProduits, "produits avec pré-allocation (make(..., capacité))")
	start = time.Now()
	inventaireAvecPrealloc := make(map[int]Produit, numProduits) // Pré-allocation
	stockAvecPrealloc := make(map[int]int, numProduits)          // Pré-allocation

	for i := 0; i < numProduits; i++ {
		id := i + 200
		inventaireAvecPrealloc[id] = Produit{
			ID:        id,
			Nom:       fmt.Sprintf("Produit_%d", id),
			Prix:      rand.Float64() * 1000,
			Categorie: categoriesDisponibles[rand.Intn(len(categoriesDisponibles))],
		}
		stockAvecPrealloc[id] = rand.Intn(500)
	}
	elapsed = time.Since(start)
	fmt.Printf("    Temps d'ajout avec pré-allocation: %s\n", elapsed)
	fmt.Println("Explication: La pré-allocation avec `make(map[Key]Value, capacité)` permet d'initialiser la map avec une taille interne suffisante pour accueillir le nombre d'éléments spécifié. Cela réduit le nombre de réallocations et de copies de données sous-jacentes qui se produisent lorsque la map doit agrandir sa capacité, ce qui se traduit par une amélioration significative des performances pour l'ajout d'un grand nombre d'éléments.")
	fmt.Println("")

	// Test 3: Recherche aléatoire
	fmt.Println("  Test 3: Recherche de 10,000 produits aléatoires par ID")
	const numRecherches = 10000
	start = time.Now()
	for i := 0; i < numRecherches; i++ {
		idRecherche := rand.Intn(numProduits) + 200 // ID aléatoire dans la plage des produits ajoutés
		_, _ = inventaireAvecPrealloc[idRecherche]  // Effectue la recherche
	}
	elapsed = time.Since(start)
	fmt.Printf("    Temps de recherche aléatoire: %s\n", elapsed)
	fmt.Println("Explication: Les maps en Go (implémentées comme des tables de hachage) offrent une complexité temporelle moyenne de O(1) pour les opérations de recherche, d'insertion et de suppression. Cela signifie que le temps nécessaire pour ces opérations reste relativement constant, même avec un grand nombre d'éléments.")
	fmt.Println("")

	// Test 4: Itération sur tous les éléments
	fmt.Println("  Test 4: Itération sur tous les", numProduits, "produits")
	start = time.Now()
	countIter := 0
	for _, prod := range inventaireAvecPrealloc {
		_ = prod // Simule une opération sur le produit
		countIter++
	}
	elapsed = time.Since(start)
	fmt.Printf("    Temps d'itération: %s (éléments parcourus: %d)\n", elapsed, countIter)
	fmt.Println("Explication: L'itération sur une map a une complexité temporelle de O(N), où N est le nombre d'éléments. Le temps d'itération est proportionnel au nombre d'éléments dans la map.")
	fmt.Println("")

	fmt.Println("Commentaires sur les performances:")
	fmt.Println("- L'ajout est plus rapide avec une pré-allocation de capacité, car cela minimise les réallocations coûteuses.")
	fmt.Println("- La recherche par clé dans une map est extrêmement rapide (quasi-constante), ce qui est son principal avantage.")
	fmt.Println("- L'itération sur une map est linéaire par rapport au nombre d'éléments.")
}

// --- Fonctions auxiliaires pour les exercices ---

// categorieExiste vérifie si une catégorie est présente dans le slice.
func categorieExiste(nom string, categories []string) bool {
	for _, c := range categories {
		if c == nom {
			return true
		}
	}
	return false
}

// supprimerCategorie supprime une catégorie du slice si elle existe.
func supprimerCategorie(nom string, categories []string) []string {
	for i, c := range categories {
		if c == nom {
			// Crée un nouveau slice en concaténant les parties avant et après l'élément à supprimer.
			// C'est une méthode courante pour supprimer un élément d'un slice en Go.
			return append(categories[:i], categories[i+1:]...)
		}
	}
	return categories // Retourne le slice inchangé si la catégorie n'est pas trouvée
}

// afficherTousProduits affiche les détails de tous les produits avec leur stock.
func afficherTousProduits(inventaire map[int]Produit, stock map[int]int) {
	if len(inventaire) == 0 {
		fmt.Println("Aucun produit dans l'inventaire.")
		return
	}
	for id, prod := range inventaire {
		qte, ok := stock[id]
		if !ok {
			qte = 0 // Si le stock n'est pas trouvé, assume 0
		}
		fmt.Printf("ID: %d, Nom: %s, Prix: %.2f€, Catégorie: %s, Stock: %d\n",
			prod.ID, prod.Nom, prod.Prix, prod.Categorie, qte)
	}
}

// obtenirProduit recherche un produit par ID et retourne ses détails, son stock et un booléen d'existence.
func obtenirProduit(id int, inventaire map[int]Produit, stock map[int]int) (Produit, int, bool) {
	prod, okProd := inventaire[id]
	qte, okStock := stock[id]
	if okProd && okStock {
		return prod, qte, true
	}
	return Produit{}, 0, false // Retourne des "zero values" si non trouvé
}

// vendreProduit décrémente le stock d'un produit.
func vendreProduit(id int, quantite int, stock map[int]int) bool {
	if qte, ok := stock[id]; ok && qte >= quantite {
		stock[id] -= quantite
		return true
	}
	return false
}

// reapprovisionnerProduit incrémente le stock d'un produit.
func reapprovisionnerProduit(id int, quantite int, stock map[int]int) {
	if _, ok := stock[id]; ok {
		stock[id] += quantite
	} else {
		// Si le produit n'était pas dans le stock, l'ajoute
		stock[id] = quantite
	}
}

// listerProduitsParCategorie affiche les produits d'une catégorie donnée.
func listerProduitsParCategorie(categorie string, inventaire map[int]Produit, produitsParCategorie map[string][]int) {
	ids, ok := produitsParCategorie[categorie]
	if !ok || len(ids) == 0 {
		fmt.Printf("Aucun produit trouvé pour la catégorie '%s'.\n", categorie)
		return
	}
	fmt.Printf("Produits dans la catégorie '%s':\n", categorie)
	for _, id := range ids {
		prod, ok := inventaire[id]
		if ok {
			fmt.Printf("  - ID: %d, Nom: %s, Prix: %.2f€\n", prod.ID, prod.Nom, prod.Prix)
		}
	}
}

```


#### Exécution

Pour exécuter ce code, naviguez dans votre terminal jusqu'au répertoire `go_stock_management` et lancez :


```bash
go run main.go
```


#### Explications Clés

*   **Slices (`[]type`) :**
    *   Les slices sont des vues dynamiques sur des tableaux. Ils sont plus flexibles que les tableaux car leur taille peut varier.
    *   `len(slice)` retourne le nombre d'éléments dans le slice.
    *   `cap(slice)` retourne la capacité du tableau sous-jacent (le nombre d'éléments que le slice peut contenir avant une réallocation).
    *   `append()` est la fonction clé pour ajouter des éléments à un slice. Si la capacité est dépassée, un nouveau tableau sous-jacent plus grand est alloué et les éléments sont copiés.
    *   La suppression d'un élément d'un slice implique généralement de créer un nouveau slice ou de manipuler les indices pour exclure l'élément, car les slices n'ont pas de méthode `delete` intégrée comme les maps.

*   **Maps (`map[KeyType]ValueType`) :**
    *   Les maps sont des collections non ordonnées de paires clé-valeur. Elles sont utilisées pour stocker des données où chaque élément est associé à une clé unique.
    *   `make(map[KeyType]ValueType)` crée une map vide. `make(map[KeyType]ValueType, capacité)` pré-alloue de la mémoire pour une meilleure performance lors de l'ajout d'un grand nombre d'éléments.
    *   L'accès et la modification des éléments se font via la syntaxe `maMap[clé]`.
    *   L'idiome "comma ok" (`valeur, ok := maMap[clé]`) est essentiel pour vérifier si une clé existe réellement dans la map ou si la valeur retournée est la "zero value" du type.
    *   `delete(maMap, clé)` supprime une paire clé-valeur de la map.
    *   Les maps sont des types de référence ; les passer à des fonctions ne crée pas de copie.

*   **`struct` :**
    *   Les structures (`struct`) permettent de regrouper des champs de différents types sous un même nom, créant ainsi des types de données composites. Elles sont utilisées ici pour représenter un `Produit`.

*   **Performance :**
    *   **Pré-allocation (`make` avec capacité) :** Pour les maps et les slices, spécifier une capacité initiale lors de la création avec `make` peut améliorer considérablement les performances lors de l'ajout d'un grand nombre d'éléments. Cela réduit les coûts liés aux réallocations mémoire successives.
    *   **Maps (recherche) :** Les maps en Go sont implémentées comme des tables de hachage, offrant une complexité temporelle moyenne de O(1) pour les opérations de recherche, d'insertion et de suppression. Elles sont donc extrêmement rapides pour retrouver des éléments par leur clé.
    *   **Maps (itération) :** L'itération sur une map a une complexité temporelle de O(N), où N est le nombre d'éléments. L'ordre d'itération n'est pas garanti et peut varier d'une exécution à l'autre.

Ce TP vous a permis de manipuler les slices et les maps, deux des structures de données les plus fondamentales et les plus utilisées en Go, dans un contexte pratique de gestion de stock. La compréhension de leurs comportements et de leurs implications en termes de performance est cruciale pour écrire du code Go efficace.