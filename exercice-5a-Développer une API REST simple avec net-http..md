Bonjour ! Ce TP vous guidera dans la création d'une API REST simple en utilisant le package `net/http` de Go.

---

## TP : API REST Simple avec `net/http` en Go

### Objectif du TP
Développer une API REST simple avec le package standard `net/http` de Go, en gérant des routes et des handlers pour des opérations CRUD basiques sur une ressource.

### Prérequis
*   Connaissances de base en Go (variables, fonctions, structs, slices, boucles, gestion des erreurs).
*   Environnement Go configuré et fonctionnel.
*   Familiarité avec les concepts HTTP (méthodes GET, POST, PUT, DELETE, codes de statut).

### Contexte
Le package `net/http` de la bibliothèque standard de Go est la fondation sur laquelle reposent de nombreux frameworks web Go. Comprendre son fonctionnement permet de construire des serveurs HTTP robustes et performants, ainsi que de mieux appréhender les abstractions offertes par des frameworks plus complexes. Ce TP vous donnera une base solide pour interagir avec le protocole HTTP directement en Go.

### Énoncé du TP
Vous allez créer un serveur HTTP qui expose une API REST pour gérer une collection d'éléments (par exemple, des "produits" ou des "tâches"). L'API devra implémenter les fonctionnalités suivantes :

1.  **Récupérer tous les éléments** (`GET /items`).
2.  **Récupérer un élément par son ID** (`GET /items/{id}`).
3.  **Ajouter un nouvel élément** (`POST /items`).
4.  **Mettre à jour un élément existant** (`PUT /items/{id}`).
5.  **Supprimer un élément** (`DELETE /items/{id}`).

Les données seront stockées en mémoire (pas de base de données pour ce TP). Les échanges de données se feront au format JSON.

### Étapes Détaillées

#### 1. Initialisation du Projet

*   Créez un nouveau répertoire pour votre projet.
*   Initialisez un module Go : `go mod init votre_module_api`
*   Créez un fichier `main.go`.

#### 2. Définition de la Structure de Données

*   Définissez une structure `Item` qui représentera un élément de votre collection. Elle devrait contenir au moins un `ID` (string), un `Name` (string) et une `Description` (string).
*   Utilisez les tags `json:"..."` pour spécifier comment les champs doivent être sérialisés/désérialisés en JSON.
    ```go
    type Item struct {
        ID          string `json:"id"`
        Name        string `json:"name"`
        Description string `json:"description"`
    }
    ```
*   Déclarez une variable globale (ou une variable de package) de type `[]Item` qui servira de stockage en mémoire pour vos éléments. Initialisez-la avec quelques données de test.

#### 3. Configuration du Serveur HTTP

*   Dans votre fonction `main`, créez un nouveau multiplexeur de requêtes (`*http.ServeMux`). C'est lui qui va associer les chemins d'URL à vos fonctions de gestion (handlers).
    ```go
    mux := http.NewServeMux()
    // ... enregistrement des handlers ...
    err := http.ListenAndServe(":8080", mux)
    if err != nil {
        // Gérer l'erreur de démarrage du serveur
    }
    ```
*   Faites écouter le serveur sur un port (par exemple, `8080`).

#### 4. Implémentation des Handlers

Pour chaque fonctionnalité de l'API, vous allez créer une fonction handler qui prend `http.ResponseWriter` et `*http.Request` en paramètres.

**Conseils pour les handlers :**
*   Utilisez `w.Header().Set("Content-Type", "application/json")` pour indiquer que la réponse est en JSON.
*   Utilisez `json.NewEncoder(w).Encode(data)` pour envoyer des données JSON.
*   Utilisez `json.NewDecoder(r.Body).Decode(&data)` pour lire des données JSON du corps de la requête.
*   N'oubliez pas de gérer les erreurs de décodage/encodage JSON.
*   Utilisez les codes de statut HTTP appropriés (`http.StatusOK`, `http.StatusCreated`, `http.StatusNotFound`, `http.StatusBadRequest`, `http.StatusInternalServerError`).

---

##### a. `GET /items` : Récupérer tous les éléments

*   Créez une fonction `getItemsHandler(w http.ResponseWriter, r *http.Request)`.
*   Vérifiez que la méthode de la requête est bien `GET`.
*   Encodez votre slice d'éléments en JSON et écrivez-le dans `w`.

##### b. `GET /items/{id}` : Récupérer un élément par son ID

*   Créez une fonction `getItemHandler(w http.ResponseWriter, r *http.Request)`.
*   Vérifiez que la méthode de la requête est bien `GET`.
*   Extrayez l'ID de l'URL. Avec `net/http` pur, cela implique de manipuler la chaîne `r.URL.Path` (par exemple, `strings.TrimPrefix` et `strings.Split`).
*   Recherchez l'élément correspondant dans votre slice.
*   Si l'élément n'est pas trouvé, renvoyez un code `http.StatusNotFound`.

##### c. `POST /items` : Ajouter un nouvel élément

*   Créez une fonction `createItemHandler(w http.ResponseWriter, r *http.Request)`.
*   Vérifiez que la méthode de la requête est bien `POST`.
*   Décodez le corps de la requête (qui contient le nouvel élément en JSON) dans une nouvelle variable `Item`.
*   **Générez un ID unique** pour le nouvel élément (vous pouvez utiliser `github.com/google/uuid` pour cela : `go get github.com/google/uuid`).
*   Ajoutez le nouvel élément à votre slice.
*   Renvoyez le nouvel élément créé avec un code `http.StatusCreated`.

##### d. `PUT /items/{id}` : Mettre à jour un élément existant

*   Créez une fonction `updateItemHandler(w http.ResponseWriter, r *http.Request)`.
*   Vérifiez que la méthode de la requête est bien `PUT`.
*   Extrayez l'ID de l'URL.
*   Décodez le corps de la requête (qui contient les données de mise à jour) dans une variable `Item`.
*   Recherchez l'élément par son ID dans votre slice.
*   Si trouvé, mettez à jour ses champs (Name, Description).
*   Si non trouvé, renvoyez `http.StatusNotFound`.
*   Renvoyez l'élément mis à jour avec `http.StatusOK`.

##### e. `DELETE /items/{id}` : Supprimer un élément

*   Créez une fonction `deleteItemHandler(w http.ResponseWriter, r *http.Request)`.
*   Vérifiez que la méthode de la requête est bien `DELETE`.
*   Extrayez l'ID de l'URL.
*   Recherchez l'index de l'élément dans votre slice.
*   Si trouvé, supprimez l'élément de la slice (attention à la manipulation des slices en Go pour la suppression).
*   Si non trouvé, renvoyez `http.StatusNotFound`.
*   Renvoyez un code `http.StatusNoContent` (204) ou `http.StatusOK` sans corps de réponse.

#### 5. Enregistrement des Handlers

*   Dans votre fonction `main`, utilisez `mux.HandleFunc()` pour associer chaque chemin d'URL à son handler respectif.
    ```go
    mux.HandleFunc("/items", itemsHandler) // Un handler peut gérer plusieurs méthodes
    mux.HandleFunc("/items/", itemByIDHandler) // Attention à l'ordre et à la spécificité des chemins
    ```
    *Note sur les chemins :* Pour les chemins avec ID (`/items/{id}`), `net/http` ne gère pas les paramètres de chemin directement comme certains frameworks. Vous devrez utiliser un chemin générique comme `/items/` et ensuite analyser `r.URL.Path` dans votre handler pour extraire l'ID. Un seul handler peut gérer `/items` et `/items/{id}` en fonction de la présence ou non d'un ID dans le chemin.

#### 6. Test de l'API

*   Démarrez votre serveur : `go run main.go`
*   Utilisez un outil comme `curl`, Postman, Insomnia ou Thunder Client (VS Code) pour tester vos endpoints :
    *   `GET http://localhost:8080/items`
    *   `GET http://localhost:8080/items/votre_id`
    *   `POST http://localhost:8080/items` avec un corps JSON
    *   `PUT http://localhost:8080/items/votre_id` avec un corps JSON
    *   `DELETE http://localhost:8080/items/votre_id`

---

### Consignes Générales et Conseils

*   **Gestion des Erreurs :** Une bonne API gère les erreurs de manière élégante. Pensez à ce qui se passe si :
    *   Le corps de la requête JSON est mal formé.
    *   Un ID n'existe pas.
    *   Une méthode HTTP non autorisée est utilisée.
*   **Clarté du Code :** Organisez votre code en fonctions claires et bien nommées.
*   **Simplicité :** Pour ce TP, l'accent est mis sur la compréhension de `net/http`. Ne vous souciez pas de la persistance des données (la mémoire est suffisante) ni d'une architecture complexe.

### Ressources Utiles

*   Documentation officielle `net/http` : [https://pkg.go.dev/net/http](https://pkg.go.dev/net/http)
*   Documentation `encoding/json` : [https://pkg.go.dev/encoding/json](https://pkg.go.dev/encoding/json)
*   Package `github.com/google/uuid` : [https://pkg.go.dev/github.com/google/uuid](https://pkg.go.dev/github.com/google/uuid)

Bon courage pour ce TP !