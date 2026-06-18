Voici une solution concise et commentée pour le TP "Maîtrise de la Sérialisation JSON en Go avec Struct Tags".

---

### Solution TP : Maîtrise de la Sérialisation JSON en Go avec Struct Tags

Pour commencer, créez un nouveau répertoire pour votre projet et initialisez un module Go :



```bash
mkdir go_json_struct_tags
cd go_json_struct_tags
go mod init go_json_struct_tags
```



Ensuite, créez un fichier `main.go` dans ce répertoire. Tout le code des exercices sera placé dans ce fichier.

#### `main.go`



```go
package main

import (
	"encoding/json" // Package pour la sérialisation/désérialisation JSON
	"fmt"           // Package pour l l'affichage formaté
	"time"          // Pour le type time.Time dans l'exercice 5
)

// --- Exercice 1 : Sérialisation Basique ---

// Personne struct sans tags JSON pour l'exercice 1
type Personne struct {
	Nom   string
	Age   int
	Email string
	Actif bool
}

// --- Exercice 2 : Maîtrise des Struct Tags ---

// PersonneTagged struct avec tags JSON pour l'exercice 2
type PersonneTagged struct {
	FullName    string `json:"full_name"`              // Renomme le champ en "full_name"
	AgeInYears  int    `json:"age_in_years"`           // Renomme le champ en "age_in_years"
	ContactEmail string `json:"contact_email,omitempty"` // Renomme et omet si vide
	IsActive    bool   `json:"is_active"`
	Password    string `json:"-"`                      // Ignore ce champ lors de la sérialisation/désérialisation
}

// --- Exercice 3 : Désérialisation ---

// Produit struct avec tags JSON pour l'exercice 3
type Produit struct {
	ID      int     `json:"product_id"`
	Nom     string  `json:"item_name"`
	Prix    float64 `json:"unit_price"`
	EnStock bool    `json:"in_stock"`
}

// --- Exercice 5 : Scénario Complet et Réflexion ---

// Livre struct pour l'exercice 5
type Livre struct {
	ID              int      `json:"book_id"`
	Titre           string   `json:"title"`
	Auteur          string   `json:"author_name"`
	AnneePublication int      `json:"publication_year"`
	Genres          []string `json:"genres,omitempty"`    // Omet si la liste est vide
	ISBN            string   `json:"isbn_code,omitempty"` // Omet si la chaîne est vide
	EstDisponible   bool     `json:"is_available"`
}

// PublisherInfo struct pour la question 1 du défi de réflexion
type PublisherInfo struct {
	Name     string `json:"name"`
	Location string `json:"location"`
}

// LivreAvecEditeur struct pour la question 1 du défi de réflexion
type LivreAvecEditeur struct {
	ID              int           `json:"book_id"`
	Titre           string        `json:"title"`
	Auteur          string        `json:"author_name"`
	AnneePublication int           `json:"publication_year"`
	Genres          []string      `json:"genres,omitempty"`
	ISBN            string        `json:"isbn_code,omitempty"`
	EstDisponible   bool          `json:"is_available"`
	Editeur         PublisherInfo `json:"publisher_info"` // Champ pour l'objet imbriqué
}

// UnixTime est un type personnalisé pour gérer la sérialisation/désérialisation de time.Time en timestamp Unix.
type UnixTime time.Time

// MarshalJSON implémente l'interface json.Marshaler pour UnixTime.
func (t UnixTime) MarshalJSON() ([]byte, error) {
	// Convertit time.Time en timestamp Unix (int64) et le sérialise en JSON.
	return json.Marshal(time.Time(t).Unix())
}

// UnmarshalJSON implémente l'interface json.Unmarshaler pour UnixTime.
func (t *UnixTime) UnmarshalJSON(data []byte) error {
	var timestamp int64
	if err := json.Unmarshal(data, &timestamp); err != nil {
		return err
	}
	// Convertit le timestamp Unix en time.Time et l'assigne au pointeur *UnixTime.
	*t = UnixTime(time.Unix(timestamp, 0))
	return nil
}

// LivreAvecDateAjout struct pour la question 2 du défi de réflexion
type LivreAvecDateAjout struct {
	ID        int      `json:"book_id"`
	Titre     string   `json:"title"`
	DateAjout UnixTime `json:"date_ajout"` // Utilise notre type personnalisé
}

func main() {
	fmt.Println("--- Exercice 1 : Sérialisation Basique ---")
	p := Personne{
		Nom:   "Alice Dupont",
		Age:   30,
		Email: "alice.dupont@example.com",
		Actif: true,
	}

	jsonData, err := json.Marshal(p)
	if err != nil {
		fmt.Printf("Erreur de sérialisation: %v\n", err)
	} else {
		fmt.Printf("JSON Personne basique: %s\n", jsonData)
	}

	fmt.Println("\nQuestion : Observez le nom des clés JSON. Correspondent-elles exactement aux noms des champs de votre struct ? Pourquoi ?")
	fmt.Println("Réponse : Oui, elles correspondent exactement aux noms des champs de la struct (Nom, Age, Email, Actif). Par défaut, `json.Marshal` utilise les noms des champs de la struct comme clés JSON. Les champs doivent commencer par une majuscule pour être exportés et donc sérialisables.")

	fmt.Println("\n--- Exercice 2 : Maîtrise des Struct Tags ---")
	p1 := PersonneTagged{
		FullName:    "Bob Martin",
		AgeInYears:  45,
		ContactEmail: "bob.martin@example.com",
		IsActive:    true,
		Password:    "secret123", // Ce champ ne doit pas apparaître
	}
	p2 := PersonneTagged{
		FullName:    "Charlie Brown",
		AgeInYears:  22,
		ContactEmail: "", // Email vide
		IsActive:    false,
		Password:    "topsecret",
	}

	jsonData1, err := json.Marshal(p1)
	if err != nil {
		fmt.Printf("Erreur de sérialisation p1: %v\n", err)
	} else {
		fmt.Printf("JSON PersonneTagged (email rempli): %s\n", jsonData1)
	}

	jsonData2, err := json.Marshal(p2)
	if err != nil {
		fmt.Printf("Erreur de sérialisation p2: %v\n", err)
	} else {
		fmt.Printf("JSON PersonneTagged (email vide): %s\n", jsonData2)
	}

	fmt.Println("\nQuestions :")
	fmt.Println("1. Comment le tag `omitempty` a-t-il affecté la sortie JSON pour l'instance avec l'email vide ?")
	fmt.Println("   Réponse : Le tag `omitempty` a fait en sorte que la clé `contact_email` n'apparaisse pas du tout dans le JSON de l'instance `p2` (où `ContactEmail` est une chaîne vide).")
	fmt.Println("2. Le champ `MotDePasse` est-il présent dans le JSON ? Quel tag avez-vous utilisé pour cela ?")
	fmt.Println("   Réponse : Non, le champ `Password` n'est pas présent dans le JSON. J'ai utilisé le tag `json:\"-\"` pour l'ignorer lors de la sérialisation.")

	fmt.Println("\n--- Exercice 3 : Désérialisation ---")
	jsonString := `{
        "product_id": 101,
        "item_name": "Clavier Mécanique",
        "unit_price": 79.99,
        "in_stock": true,
        "description": "Un clavier de haute qualité"
    }` // Ajout d'une clé inconnue pour la question

	var produit Produit
	err = json.Unmarshal([]byte(jsonString), &produit)
	if err != nil {
		fmt.Printf("Erreur de désérialisation: %v\n", err)
	} else {
		fmt.Println("Produit désérialisé:")
		fmt.Printf("  ID: %d\n", produit.ID)
		fmt.Printf("  Nom: %s\n", produit.Nom)
		fmt.Printf("  Prix: %.2f\n", produit.Prix)
		fmt.Printf("  En stock: %t\n", produit.EnStock)
	}

	fmt.Println("\nQuestions :")
	fmt.Println("1. Que se passerait-il si la chaîne JSON contenait une clé `description` qui n'a pas de champ correspondant dans votre struct `Produit` (même sans tag) ?")
	fmt.Println("   Réponse : `json.Unmarshal` ignorera simplement les clés JSON qui n'ont pas de champ correspondant (exporté) dans la struct cible. Il n'y aura pas d'erreur par défaut. Pour obtenir une erreur dans ce cas, il faudrait utiliser `json.NewDecoder(r.Body).DisallowUnknownFields().Decode(&produit)`.")
	fmt.Println("2. Que se passerait-il si la valeur de `unit_price` était une chaîne de caractères (`\"79.99\"`) au lieu d'un nombre dans le JSON ?")
	fmt.Println("   Réponse : `json.Unmarshal` retournerait une erreur de type (`json: cannot unmarshal string into Go struct field Produit.unit_price of type float64`). La désérialisation échouerait car le type JSON ne correspond pas au type Go attendu.")

	fmt.Println("\n--- Exercice 4 : Gestion des Erreurs ---")

	fmt.Println("Tentative de désérialisation de JSON malformé:")
	malformedJSON := `{
        "product_id": 102,
        "item_name": "Souris Gaming",
        "unit_price": 49.99,
        "in_stock": true,
        ` // Manque le '}' final
	var pMalformed Produit
	err = json.Unmarshal([]byte(malformedJSON), &pMalformed)
	if err != nil {
		fmt.Printf("  Erreur détectée (JSON malformé): %v\n", err)
	} else {
		fmt.Println("  Désérialisation réussie de JSON malformé (ce qui est inattendu).")
	}

	fmt.Println("\nTentative de désérialisation de JSON avec type de données incorrect:")
	wrongTypeJSON := `{
        "product_id": "103",
        "item_name": "Écran UltraWide",
        "unit_price": 399.99,
        "in_stock": true
    }` // product_id est une chaîne au lieu d'un nombre
	var pWrongType Produit
	err = json.Unmarshal([]byte(wrongTypeJSON), &pWrongType)
	if err != nil {
		fmt.Printf("  Erreur détectée (type de données incorrect): %v\n", err)
	} else {
		fmt.Println("  Désérialisation réussie de JSON avec type incorrect (ce qui est inattendu).")
	}

	fmt.Println("\nQuestion : Pourquoi est-il important de toujours vérifier l'erreur retournée par `json.Marshal` et `json.Unmarshal` ?")
	fmt.Println("Réponse : Il est crucial de vérifier les erreurs car les opérations JSON peuvent échouer pour diverses raisons : JSON malformé, types de données incompatibles, problèmes d'encodage/décodage, etc. Ignorer ces erreurs peut entraîner des paniques à l'exécution, des données corrompues ou des comportements imprévisibles de l'application. Une bonne gestion des erreurs permet de réagir de manière robuste et d'informer l'utilisateur ou de logguer le problème.")

	fmt.Println("\n--- Exercice 5 : Scénario Complet et Réflexion (Défi IA) ---")

	// Partie A : Sérialisation
	fmt.Println("\nPartie A : Sérialisation de Livre")
	livre1 := Livre{
		ID:              1,
		Titre:           "Le Seigneur des Anneaux",
		Auteur:          "J.R.R. Tolkien",
		AnneePublication: 1954,
		Genres:          []string{"Fantasy", "Aventure"},
		ISBN:            "978-0618053267",
		EstDisponible:   true,
	}
	livre2 := Livre{
		ID:              2,
		Titre:           "Go Programming Language",
		Auteur:          "Alan A. A. Donovan",
		AnneePublication: 2015,
		Genres:          []string{}, // Liste vide
		ISBN:            "",         // Chaîne vide
		EstDisponible:   true,
	}

	jsonLivre1, err := json.MarshalIndent(livre1, "", "  ") // MarshalIndent pour une sortie lisible
	if err != nil {
		fmt.Printf("Erreur sérialisation livre1: %v\n", err)
	} else {
		fmt.Println("JSON Livre 1 (tous champs):")
		fmt.Println(string(jsonLivre1))
	}

	jsonLivre2, err := json.MarshalIndent(livre2, "", "  ")
	if err != nil {
		fmt.Printf("Erreur sérialisation livre2: %v\n", err)
	} else {
		fmt.Println("\nJSON Livre 2 (genres et ISBN vides):")
		fmt.Println(string(jsonLivre2))
	}
	fmt.Println("Vérification : `genres` et `isbn_code` sont bien omis quand ils sont vides grâce au tag `omitempty`.")

	// Partie B : Désérialisation
	fmt.Println("\nPartie B : Désérialisation de Livre")
	var deserializedLivre1 Livre
	err = json.Unmarshal(jsonLivre1, &deserializedLivre1)
	if err != nil {
		fmt.Printf("Erreur désérialisation jsonLivre1: %v\n", err)
	} else {
		fmt.Println("Livre 1 désérialisé:")
		fmt.Printf("  ID: %d, Titre: %s, Auteur: %s, Année: %d, Genres: %v, ISBN: %s, Disponible: %t\n",
			deserializedLivre1.ID, deserializedLivre1.Titre, deserializedLivre1.Auteur,
			deserializedLivre1.AnneePublication, deserializedLivre1.Genres, deserializedLivre1.ISBN,
			deserializedLivre1.EstDisponible)
	}

	var deserializedLivre2 Livre
	err = json.Unmarshal(jsonLivre2, &deserializedLivre2)
	if err != nil {
		fmt.Printf("Erreur désérialisation jsonLivre2: %v\n", err)
	} else {
		fmt.Println("\nLivre 2 désérialisé:")
		fmt.Printf("  ID: %d, Titre: %s, Auteur: %s, Année: %d, Genres: %v, ISBN: %s, Disponible: %t\n",
			deserializedLivre2.ID, deserializedLivre2.Titre, deserializedLivre2.Auteur,
			deserializedLivre2.AnneePublication, deserializedLivre2.Genres, deserializedLivre2.ISBN,
			deserializedLivre2.EstDisponible)
	}

	fmt.Println("\n--- Défi de Réflexion (Utilisez l'IA pour explorer, pas pour copier) ---")

	fmt.Println("\nQuestion 1 : Gérer un objet imbriqué `publisher_info`")
	fmt.Println("Réponse : Pour gérer un objet imbriqué, on définirait une struct pour cet objet (`PublisherInfo`) et on l'inclurait comme champ dans la struct principale (`LivreAvecEditeur`).")
	fmt.Println("Exemple de struct (voir définitions `PublisherInfo` et `LivreAvecEditeur` ci-dessus):")
	fmt.Println("```
go")
	fmt.Println("type PublisherInfo struct {")
	fmt.Println("    Name     string `json:\"name\"`")
	fmt.Println("    Location string `json:\"location\"`")
	fmt.Println("}")
	fmt.Println("type LivreAvecEditeur struct {")
	fmt.Println("    // ... autres champs ...")
	fmt.Println("    Editeur PublisherInfo `json:\"publisher_info\"`")
	fmt.Println("}")
	fmt.Println("
```")
	fmt.Println("Exemple d'utilisation:")
	livreEditeur := LivreAvecEditeur{
		ID:    3,
		Titre: "The Hitchhiker's Guide to the Galaxy",
		Auteur: "Douglas Adams",
		Editeur: PublisherInfo{Name: "Pan Books", Location: "London"},
	}
	jsonLivreEditeur, _ := json.MarshalIndent(livreEditeur, "", "  ")
	fmt.Println("JSON Livre avec éditeur imbriqué:")
	fmt.Println(string(jsonLivreEditeur))

	fmt.Println("\nQuestion 2 : Sérialiser `time.Time` en timestamp Unix")
	fmt.Println("Réponse : Par défaut, `time.Time` est sérialisé en chaîne ISO 8601. Pour un timestamp Unix (nombre entier), on peut implémenter les interfaces `json.Marshaler` et `json.Unmarshaler` pour un type personnalisé basé sur `time.Time`.")
	fmt.Println("Exemple de type `UnixTime` et de ses méthodes (voir définitions `UnixTime` et `LivreAvecDateAjout` ci-dessus):")
	fmt.Println("```
go")
	fmt.Println("type UnixTime time.Time")
	fmt.Println("func (t UnixTime) MarshalJSON() ([]byte, error) { ... }")
	fmt.Println("func (t *UnixTime) UnmarshalJSON(data []byte) error { ... }")
	fmt.Println("type LivreAvecDateAjout struct {")
	fmt.Println("    // ...")
	fmt.Println("    DateAjout UnixTime `json:\"date_ajout\"`")
	fmt.Println("}")
	fmt.Println("
```")
	fmt.Println("Exemple d'utilisation:")
	livreDate := LivreAvecDateAjout{
		ID:    4,
		Titre: "Clean Code",
		DateAjout: UnixTime(time.Date(2008, time.August, 1, 0, 0, 0, 0, time.UTC)),
	}
	jsonLivreDate, _ := json.MarshalIndent(livreDate, "", "  ")
	fmt.Println("JSON Livre avec date en timestamp Unix:")
	fmt.Println(string(jsonLivreDate))
	fmt.Println("Désérialisation pour vérification:")
	var deserializedLivreDate LivreAvecDateAjout
	json.Unmarshal(jsonLivreDate, &deserializedLivreDate)
	fmt.Printf("  Date désérialisée: %s (timestamp: %d)\n", time.Time(deserializedLivreDate.DateAjout).Format(time.RFC3339), time.Time(deserializedLivreDate.DateAjout).Unix())
}

```



#### Exécution

Pour exécuter ce code, naviguez dans votre terminal jusqu'au répertoire `go_json_struct_tags` et lancez :



```bash
go run main.go
```



#### Explications Clés

1.  **Sérialisation (`json.Marshal`) :**
    *   Convertit une valeur Go (struct, slice, map, type primitif) en un tableau d'octets JSON.
    *   Par défaut, les champs exportés (commençant par une majuscule) de la struct sont utilisés comme clés JSON.

2.  **Désérialisation (`json.Unmarshal`) :**
    *   Convertit un tableau d'octets JSON en une valeur Go.
    *   Elle tente de faire correspondre les clés JSON aux noms des champs exportés de la struct cible (en tenant compte des tags).

3.  **`struct tags` (`json:"key,options"`) :**
    *   **`json:"key_name"` :** Renomme le champ Go en `key_name` dans le JSON.
    *   **`json:"-,omitempty"` :** Omet le champ du JSON si sa valeur est la "zero value" de son type (vide pour string, 0 pour int/float, false pour bool, nil pour slice/map/pointeur).
    *   **`json:"-"` :** Ignore complètement le champ lors de la sérialisation et de la désérialisation.

4.  **Gestion des Erreurs :**
    *   `json.Marshal` et `json.Unmarshal` retournent toujours une `error`. Il est impératif de la vérifier.
    *   Les erreurs peuvent survenir pour diverses raisons : JSON malformé, types de données incompatibles (ex: chaîne dans JSON pour un `int` Go), tentatives de sérialiser/désérialiser des champs non exportés, etc.

5.  **Objets Imbriqués (Exercice 5, Q1) :**
    *   Pour désérialiser un objet JSON imbriqué, on définit simplement une autre `struct` pour cet objet et on l'utilise comme type de champ dans la `struct` principale. Les tags JSON fonctionnent de manière récursive.

6.  **Formatage Personnalisé (`json.Marshaler`, `json.Unmarshaler`) (Exercice 5, Q2) :**
    *   Pour un contrôle fin sur la façon dont un type Go est sérialisé ou désérialisé, vous pouvez implémenter les interfaces `json.Marshaler` et `json.Unmarshaler`.
    *   L'exemple `UnixTime` montre comment convertir `time.Time` en un timestamp Unix (entier) pour le JSON, et inversement.

Ce TP vous a permis de maîtriser les outils essentiels du package `encoding/json` en Go, en particulier l'utilisation des `struct tags` pour un contrôle précis du mappage JSON, et la gestion des erreurs, compétences fondamentales pour toute application Go interagissant avec des données JSON.