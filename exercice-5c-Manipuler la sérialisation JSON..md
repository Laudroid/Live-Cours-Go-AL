Bonjour !

Ce TP vous guidera à travers les mécanismes de sérialisation et désérialisation JSON en Go, avec un accent particulier sur l'utilisation des `struct tags`. C'est une compétence fondamentale pour interagir avec des APIs ou stocker des données de manière structurée.

---

## TP : Maîtrise de la Sérialisation JSON en Go avec Struct Tags

### Objectif du TP

*   Comprendre et appliquer les mécanismes de sérialisation (encodage) et désérialisation (décodage) JSON en Go.
*   Maîtriser l'utilisation des `struct tags` pour contrôler le mappage entre les champs d'une struct Go et les clés d'un objet JSON.
*   Gérer les cas particuliers comme l'omission de champs vides ou l'ignorance de certains champs.
*   Apprendre à gérer les erreurs lors de ces opérations.

### Contexte

Le format JSON (JavaScript Object Notation) est omniprésent pour l'échange de données sur le web. Go, avec son package `encoding/json`, offre des outils puissants et flexibles pour travailler avec JSON. Les `struct tags` sont une fonctionnalité clé qui permet de personnaliser la façon dont les structs Go sont converties en JSON et vice-versa, offrant un contrôle fin sur le format de sortie et d'entrée.

### Prérequis

*   Connaissances de base du langage Go (variables, types, structs, fonctions, gestion des erreurs).
*   Compréhension du format JSON.
*   Un environnement de développement Go fonctionnel.

### Conseil pour l'utilisation de l'IA

L'utilisation d'outils d'IA pour vous aider est encouragée. Cependant, l'objectif de ce TP n'est pas de copier-coller une solution générée. Utilisez l'IA pour :
*   **Explorer des concepts** : Demandez des explications sur `omitempty`, `json:"-"`, ou des exemples d'erreurs.
*   **Déboguer** : Si votre code ne fonctionne pas, demandez à l'IA d'identifier des problèmes potentiels.
*   **Vérifier votre compréhension** : Après avoir écrit votre code, demandez à l'IA de l'analyser et de vous expliquer pourquoi il fonctionne (ou non).
*   **Générer des idées** : Pour les exercices plus complexes, demandez des pistes ou des approches.

**Le plus important est de comprendre *pourquoi* une solution fonctionne et d'être capable de l'expliquer.** N'hésitez pas à expérimenter et à modifier les suggestions de l'IA pour voir comment le comportement change.

---

### Exercices

Créez un fichier `main.go` pour tous les exercices.

#### Exercice 1 : Sérialisation Basique

1.  **Définissez une struct** `Personne` avec les champs suivants :
    *   `Nom` (string)
    *   `Age` (int)
    *   `Email` (string)
    *   `Actif` (bool)

2.  **Créez une instance** de `Personne`.
    ```go
    p := Personne{
        Nom:   "Alice Dupont",
        Age:   30,
        Email: "alice.dupont@example.com",
        Actif: true,
    }
    ```

3.  **Sérialisez cette instance** en JSON en utilisant `json.Marshal`.
    *   N'oubliez pas de gérer l'erreur potentielle retournée par `json.Marshal`.
    *   Affichez le JSON résultant sous forme de chaîne de caractères.

4.  **Question :** Observez le nom des clés JSON. Correspondent-elles exactement aux noms des champs de votre struct ? Pourquoi ?

#### Exercice 2 : Maîtrise des Struct Tags

Modifions la struct `Personne` pour contrôler la sortie JSON.

1.  **Modifiez la struct `Personne`** en ajoutant des `struct tags` pour les comportements suivants :
    *   Le champ `Nom` doit apparaître comme `full_name` dans le JSON.
    *   Le champ `Age` doit apparaître comme `age_in_years` dans le JSON.
    *   Le champ `Email` doit apparaître comme `contact_email` dans le JSON, et il doit être **omis si sa valeur est une chaîne vide**.
    *   Le champ `Actif` doit apparaître comme `is_active` dans le JSON.
    *   Ajoutez un nouveau champ `MotDePasse` (string) qui **ne doit jamais être sérialisé** en JSON.

2.  **Créez deux instances** de `Personne` :
    *   Une avec tous les champs remplis (y compris `Email`).
    *   Une autre où le champ `Email` est une chaîne vide.

3.  **Sérialisez ces deux instances** et affichez le JSON résultant pour chacune.

4.  **Questions :**
    *   Comment le tag `omitempty` a-t-il affecté la sortie JSON pour l'instance avec l'email vide ?
    *   Le champ `MotDePasse` est-il présent dans le JSON ? Quel tag avez-vous utilisé pour cela ?

#### Exercice 3 : Désérialisation

Maintenant, nous allons faire l'opération inverse : convertir une chaîne JSON en une struct Go.

1.  **Définissez une nouvelle struct** `Produit` avec les champs suivants et les `struct tags` appropriés :
    *   `ID` (int) -> `product_id`
    *   `Nom` (string) -> `item_name`
    *   `Prix` (float64) -> `unit_price`
    *   `EnStock` (bool) -> `in_stock`

2.  **Utilisez la chaîne JSON suivante** :
    ```json
    jsonString := `{
        "product_id": 101,
        "item_name": "Clavier Mécanique",
        "unit_price": 79.99,
        "in_stock": true
    }`
    ```

3.  **Désérialisez cette chaîne JSON** dans une instance de votre struct `Produit` en utilisant `json.Unmarshal`.
    *   N'oubliez pas de gérer l'erreur potentielle.
    *   Affichez les valeurs de chaque champ de la struct `Produit` après la désérialisation.

4.  **Questions :**
    *   Que se passerait-il si la chaîne JSON contenait une clé `description` qui n'a pas de champ correspondant dans votre struct `Produit` (même sans tag) ?
    *   Que se passerait-il si la valeur de `unit_price` était une chaîne de caractères (`"79.99"`) au lieu d'un nombre dans le JSON ?

#### Exercice 4 : Gestion des Erreurs

La gestion des erreurs est cruciale.

1.  **Prenez la struct `Produit` de l'Exercice 3.**

2.  **Essayez de désérialiser les chaînes JSON suivantes** et observez les erreurs retournées par `json.Unmarshal` :
    *   **JSON malformé :**
        ```json
        malformedJSON := `{
            "product_id": 102,
            "item_name": "Souris Gaming",
            "unit_price": 49.99,
            "in_stock": true,
        ` // Manque le '}' final
        ```
    *   **JSON avec type de données incorrect :**
        ```json
        wrongTypeJSON := `{
            "product_id": "103",
            "item_name": "Écran UltraWide",
            "unit_price": 399.99,
            "in_stock": true
        }` // product_id est une chaîne au lieu d'un nombre
        ```

3.  **Pour chaque cas, affichez l'erreur** de manière explicite.

4.  **Question :** Pourquoi est-il important de toujours vérifier l'erreur retournée par `json.Marshal` et `json.Unmarshal` ?

#### Exercice 5 : Scénario Complet et Réflexion (Défi IA)

Imaginez que vous travaillez sur une API de gestion de livres.

1.  **Définissez une struct `Livre`** qui doit représenter les informations suivantes :
    *   Un identifiant unique (entier) qui doit être sérialisé/désérialisé sous la clé `book_id`.
    *   Un titre (chaîne de caractères) qui doit être sérialisé/désérialisé sous la clé `title`.
    *   Un auteur (chaîne de caractères) qui doit être sérialisé/désérialisé sous la clé `author_name`.
    *   Une année de publication (entier) qui doit être sérialisée/désérialisée sous la clé `publication_year`.
    *   Une liste de genres (slice de chaînes de caractères) qui doit être sérialisée/désérialisée sous la clé `genres`, et **omis si la liste est vide**.
    *   Un champ `ISBN` (chaîne de caractères) qui doit être sérialisé/désérialisé sous la clé `isbn_code`, mais qui **ne doit pas être présent si sa valeur est vide**.
    *   Un champ `EstDisponible` (booléen) qui doit être sérialisé/désérialisé sous la clé `is_available`.

2.  **Implémentation :**
    *   **Partie A : Sérialisation**
        *   Créez une instance de `Livre` avec tous les champs remplis.
        *   Créez une deuxième instance de `Livre` où `genres` est vide et `ISBN` est vide.
        *   Sérialisez les deux instances et affichez le JSON. Vérifiez que `genres` et `isbn_code` sont bien omis quand ils sont vides.
    *   **Partie B : Désérialisation**
        *   Prenez le JSON généré par la première instance (tous les champs remplis) et désérialisez-le dans une nouvelle struct `Livre`. Affichez les champs pour vérifier.
        *   Prenez le JSON généré par la deuxième instance (genres et ISBN vides) et désérialisez-le. Affichez les champs.

3.  **Défi de Réflexion (Utilisez l'IA pour explorer, pas pour copier) :**
    *   **Question 1 :** Imaginez que vous recevez un JSON d'une source externe qui contient une clé `publisher_info` avec un objet imbriqué (`{"name": "...", "location": "..."}`). Comment modifieriez-vous votre struct `Livre` (ou en ajouteriez-vous une nouvelle) pour pouvoir désérialiser cette information ? Donnez un exemple de struct et de tags.
    *   **Question 2 :** Si vous vouliez qu'un champ `DateAjout` (de type `time.Time`) soit sérialisé en JSON sous forme de timestamp Unix (nombre entier) plutôt qu'une chaîne de caractères ISO 8601 par défaut, comment feriez-vous ? (Indice : explorez les interfaces `json.Marshaler` et `json.Unmarshaler` ou des solutions plus simples si vous en trouvez).

---

### Validation / Livrables

*   Un fichier `main.go` contenant le code de tous les exercices.
*   Les sorties console pour chaque étape de sérialisation et désérialisation.
*   Les réponses aux questions posées dans chaque exercice.

### Ressources Utiles

*   [Documentation officielle du package `encoding/json`](https://pkg.go.dev/encoding/json)
*   [Go Playground](https://go.dev/play/) pour tester rapidement des extraits de code.

Excellent travail ! N'hésitez pas à expérimenter et à poser des questions si vous rencontrez des difficultés.