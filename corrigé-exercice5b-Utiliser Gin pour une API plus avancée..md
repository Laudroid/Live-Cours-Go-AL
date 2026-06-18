Voici une solution concise et commentée pour le TP "API REST Avancée avec Gin".

---

### Solution TP : API REST Avancée avec Gin

Pour commencer, créez un nouveau répertoire pour votre projet et initialisez un module Go. N'oubliez pas d'installer les packages `gin` et `uuid` :


```bash
mkdir gin-todo-api
cd gin-todo-api
go mod init gin-todo-api
go get github.com/gin-gonic/gin
go get github.com/google/uuid
```


Ensuite, créez un fichier `main.go` dans ce répertoire.

#### `main.go`


```go
package main

import (
	"log"
	"net/http"
	"sync" // Pour sync.RWMutex afin de protéger la ressource partagée
	"time" // Pour le middleware de logging

	"github.com/gin-gonic/gin"
	"github.com/google/uuid" // Pour générer des IDs uniques
)

// Task représente une seule tâche de la liste.
type Task struct {
	ID          string `json:"id"`
	Title       string `json:"title" binding:"required"` // 'binding:"required"' pour la validation automatique
	Description string `json:"description"`
	Done        bool   `json:"done"`
}

// tasks est notre stockage en mémoire pour les tâches.
// Utilisation d'une map pour faciliter la recherche, mise à jour et suppression par ID.
// Protégée par un RWMutex pour un accès concurrentiel sûr (lecture/écriture).
var (
	tasks      = make(map[string]Task)
	tasksMutex sync.RWMutex // RWMutex pour de meilleures performances avec de nombreuses lectures
)

// API_KEY est la clé secrète pour l'authentification simple.
const API_KEY = "super-secret-key"

// init est appelée avant la fonction main. Elle initialise des données fictives.
func init() {
	tasksMutex.Lock() // Verrouille pour l'écriture
	defer tasksMutex.Unlock()

	tasks["1"] = Task{ID: "1", Title: "Apprendre Go", Description: "Terminer le TP Gin", Done: false}
	tasks["2"] = Task{ID: "2", Title: "Construire une API", Description: "Créer une API RESTful avec Gin", Done: true}
	tasks["3"] = Task{ID: "3", Title: "Écrire des tests", Description: "Ajouter des tests unitaires pour l'API", Done: false}
}

// LoggerMiddleware est un middleware personnalisé pour logger chaque requête.
func LoggerMiddleware() gin.HandlerFunc {
	return func(c *gin.Context) {
		startTime := time.Now() // Enregistre l'heure de début de la requête
		c.Next()                // Traite la requête en appelant le prochain handler/middleware

		duration := time.Since(startTime) // Calcule le temps de traitement
		log.Printf("Requête - Méthode: %s, Chemin: %s, IP: %s, Statut: %d, Durée: %v",
			c.Request.Method, c.Request.URL.Path, c.ClientIP(), c.Writer.Status(), duration)
	}
}

// AuthMiddleware est un middleware personnalisé pour une authentification simple par clé API.
func AuthMiddleware() gin.HandlerFunc {
	return func(c *gin.Context) {
		apiKey := c.GetHeader("X-API-KEY") // Récupère l'en-tête X-API-KEY
		if apiKey == "" {
			// Si la clé API est absente, renvoie une erreur 401 et interrompt le traitement
			c.AbortWithStatusJSON(http.StatusUnauthorized, gin.H{"error": "Clé API requise"})
			return
		}
		if apiKey != API_KEY {
			// Si la clé API est invalide, renvoie une erreur 401 et interrompt le traitement
			c.AbortWithStatusJSON(http.StatusUnauthorized, gin.H{"error": "Clé API invalide"})
			return
		}
		c.Next() // Si la clé est valide, continue le traitement de la requête
	}
}

// GetTasks gère la route GET /tasks pour récupérer toutes les tâches.
func GetTasks(c *gin.Context) {
	tasksMutex.RLock() // Verrouille en lecture pour accéder à la map 'tasks'
	defer tasksMutex.RUnlock()

	// Convertit la map en slice pour la réponse JSON
	var taskList []Task
	for _, task := range tasks {
		taskList = append(taskList, task)
	}
	c.JSON(http.StatusOK, taskList) // Renvoie la liste des tâches avec un statut 200 OK
}

// GetTaskByID gère la route GET /tasks/:id pour récupérer une tâche spécifique.
func GetTaskByID(c *gin.Context) {
	id := c.Param("id") // Récupère l'ID de la tâche depuis les paramètres d'URL

	tasksMutex.RLock()
	defer tasksMutex.RUnlock()

	task, exists := tasks[id]
	if !exists {
		// Si la tâche n'est pas trouvée, renvoie une erreur 404 Not Found
		c.JSON(http.StatusNotFound, gin.H{"error": "Tâche non trouvée"})
		return
	}
	c.JSON(http.StatusOK, task) // Renvoie la tâche trouvée avec un statut 200 OK
}

// CreateTask gère la route POST /tasks pour créer une nouvelle tâche.
func CreateTask(c *gin.Context) {
	var newTask Task
	// Tente de lier le corps de la requête JSON à la structure newTask.
	// Gin gère automatiquement la validation grâce au tag 'binding:"required"'.
	if err := c.ShouldBindJSON(&newTask); err != nil {
		// Si le JSON est mal formé ou la validation échoue, renvoie une erreur 400 Bad Request
		c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		return
	}

	newTask.ID = uuid.New().String() // Génère un ID unique pour la nouvelle tâche
	newTask.Done = false             // Une nouvelle tâche n'est pas terminée par défaut

	tasksMutex.Lock() // Verrouille en écriture pour modifier la map 'tasks'
	defer tasksMutex.Unlock()

	tasks[newTask.ID] = newTask
	c.JSON(http.StatusCreated, newTask) // Renvoie la tâche créée avec un statut 201 Created
}

// UpdateTask gère la route PUT /tasks/:id pour mettre à jour une tâche existante.
func UpdateTask(c *gin.Context) {
	id := c.Param("id") // Récupère l'ID de la tâche depuis les paramètres d'URL

	tasksMutex.Lock()
	defer tasksMutex.Unlock()

	existingTask, exists := tasks[id]
	if !exists {
		c.JSON(http.StatusNotFound, gin.H{"error": "Tâche non trouvée"})
		return
	}

	var updateData Task
	// Tente de lier le corps de la requête JSON à la structure updateData.
	// Note: Pour une gestion robuste des mises à jour partielles de booléens,
	// il faudrait utiliser des pointeurs (*bool) dans la structure updateData
	// ou une logique de décodage plus complexe pour distinguer 'false' de 'non présent'.
	// Pour ce TP, nous simplifions : si un champ est présent dans le JSON, il est mis à jour.
	// Si 'Done' est omis, il sera 'false' par défaut dans updateData, ce qui le mettra à jour à 'false'.
	if err := c.ShouldBindJSON(&updateData); err != nil {
		c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		return
	}

	// Met à jour les champs de la tâche existante uniquement s'ils sont fournis
	// (non vides pour les chaînes, ou explicitement définis pour les booléens).
	if updateData.Title != "" {
		existingTask.Title = updateData.Title
	}
	if updateData.Description != "" {
		existingTask.Description = updateData.Description
	}
	// Pour 'Done', si le champ est présent dans le JSON, il est mis à jour.
	// Si le JSON contient {"done": true} ou {"done": false}, cela sera appliqué.
	// Si le JSON omet "done", updateData.Done sera false, ce qui écrasera la valeur précédente.
	// Une solution plus avancée utiliserait un champ `*bool` pour `Done` dans `updateData`.
	existingTask.Done = updateData.Done 

	tasks[id] = existingTask
	c.JSON(http.StatusOK, existingTask) // Renvoie la tâche mise à jour avec un statut 200 OK
}

// DeleteTask gère la route DELETE /tasks/:id pour supprimer une tâche existante.
func DeleteTask(c *gin.Context) {
	id := c.Param("id") // Récupère l'ID de la tâche depuis les paramètres d'URL

	tasksMutex.Lock()
	defer tasksMutex.Unlock()

	_, exists := tasks[id]
	if !exists {
		c.JSON(http.StatusNotFound, gin.H{"error": "Tâche non trouvée"})
		return
	}

	delete(tasks, id) // Supprime la tâche de la map
	c.Status(http.StatusNoContent) // Renvoie un statut 204 No Content (pas de corps de réponse)
}

func main() {
	// gin.SetMode(gin.ReleaseMode) // Décommenter pour le mode production
	router := gin.Default() // Initialise un routeur Gin avec les middlewares Logger et Recovery par défaut

	// Applique le middleware de logging personnalisé globalement
	router.Use(LoggerMiddleware())

	// Routes publiques (lecture)
	router.GET("/tasks", GetTasks)
	router.GET("/tasks/:id", GetTaskByID)

	// Groupe de routes nécessitant une authentification (écriture/suppression)
	authRequired := router.Group("/") // Crée un groupe de routes
	authRequired.Use(AuthMiddleware()) // Applique le middleware d'authentification à ce groupe
	{
		authRequired.POST("/tasks", CreateTask)
		authRequired.PUT("/tasks/:id", UpdateTask)
		authRequired.DELETE("/tasks/:id", DeleteTask)
	}

	// Démarre le serveur HTTP sur le port 8080
	log.Fatal(router.Run(":8080")) // log.Fatal arrête le programme en cas d'erreur de démarrage
}

```


#### Exécution

1.  **Compilez et exécutez votre serveur Go :**
    
```bash
    go run main.go
    ```

    Vous devriez voir des messages de démarrage du serveur Gin.

2.  **Testez l'API avec `curl` (ou Postman/Insomnia/Thunder Client) :**

    *   **GET toutes les tâches :**
        
```bash
        curl http://localhost:8080/tasks
        ```

        *Sortie attendue :* Un tableau JSON des tâches initiales.

    *   **GET une tâche par ID (existant) :**
        
```bash
        curl http://localhost:8080/tasks/1
        ```

        *Sortie attendue :* L'objet JSON de la tâche avec l'ID "1".

    *   **GET une tâche par ID (inexistant) :**
        
```bash
        curl http://localhost:8080/tasks/999
        ```

        *Sortie attendue :* `{"error":"Tâche non trouvée"}` avec un statut 404.

    *   **POST une nouvelle tâche (avec authentification) :**
        
```bash
        curl -X POST -H "Content-Type: application/json" -H "X-API-KEY: super-secret-key" -d '{"title":"Faire les courses", "description":"Acheter du pain et du lait"}' http://localhost:8080/tasks
        ```

        *Sortie attendue :* La tâche créée avec un nouvel ID généré et un statut 201.

    *   **POST sans clé API (erreur d'authentification) :**
        
```bash
        curl -X POST -H "Content-Type: application/json" -d '{"title":"Tâche non autorisée"}' http://localhost:8080/tasks
        ```

        *Sortie attendue :* `{"error":"Clé API requise"}` avec un statut 401.

    *   **POST avec clé API invalide (erreur d'authentification) :**
        
```bash
        curl -X POST -H "Content-Type: application/json" -H "X-API-KEY: wrong-key" -d '{"title":"Tâche non autorisée"}' http://localhost:8080/tasks
        ```

        *Sortie attendue :* `{"error":"Clé API invalide"}` avec un statut 401.

    *   **POST avec titre manquant (erreur de validation) :**
        
```bash
        curl -X POST -H "Content-Type: application/json" -H "X-API-KEY: super-secret-key" -d '{"description":"Description sans titre"}' http://localhost:8080/tasks
        ```

        *Sortie attendue :* `{"error":"Key: 'Task.Title' Error:Field validation for 'Title' failed on the 'required' tag"}` avec un statut 400.

    *   **PUT (Mettre à jour) une tâche (utilisez un ID existant, ex: "1") :**
        
```bash
        curl -X PUT -H "Content-Type: application/json" -H "X-API-KEY: super-secret-key" -d '{"title":"Apprendre Gin Avancé", "done":true}' http://localhost:8080/tasks/1
        ```

        *Sortie attendue :* La tâche mise à jour avec un statut 200.

    *   **DELETE une tâche (utilisez un ID existant, ex: "2") :**
        
```bash
        curl -X DELETE -H "X-API-KEY: super-secret-key" http://localhost:8080/tasks/2
        ```

        *Sortie attendue :* (aucune sortie, statut 204 No Content).

---

#### Explications Clés

1.  **Initialisation de Gin (`gin.Default()`) :**
    *   `gin.Default()` crée un routeur Gin avec des middlewares par défaut inclus : `Logger` (pour le logging des requêtes) et `Recovery` (pour récupérer des paniques).

2.  **Modèle de Données (`Task` struct) :**
    *   Les tags `json:"..."` sont utilisés par le package `encoding/json` (que Gin utilise en interne) pour mapper les champs de la struct aux clés JSON.
    *   Le tag `binding:"required"` est une fonctionnalité de Gin qui utilise le package `go-playground/validator`. Il indique que le champ `Title` doit être présent et non vide dans le JSON de la requête entrante pour que `c.ShouldBindJSON()` réussisse.

3.  **Gestion des Routes :**
    *   `router.GET`, `router.POST`, `router.PUT`, `router.DELETE` : Méthodes pour définir les handlers pour les verbes HTTP correspondants.
    *   `c.Param("id")` : Récupère les paramètres de chemin définis dans l'URL (ex: `:id`).
    *   `c.JSON(statusCode, data)` : La méthode idiomatique de Gin pour renvoyer une réponse JSON avec le code de statut HTTP spécifié.

4.  **Traitement JSON en Entrée (`c.ShouldBindJSON()`) :**
    *   `c.ShouldBindJSON(&newTask)` : Tente de décoder le corps de la requête JSON dans la variable `newTask`.
    *   Si le JSON est mal formé ou si les règles de `binding` (comme `required`) ne sont pas respectées, `ShouldBindJSON` retourne une erreur, et Gin peut automatiquement générer une réponse `400 Bad Request`.

5.  **Middlewares :**
    *   Les middlewares sont des fonctions qui s'exécutent avant ou après les handlers de route.
    *   `router.Use(MiddlewareFunc())` : Applique un middleware globalement à toutes les routes.
    *   `router.Group("/")` : Permet de créer un groupe de routes et d'appliquer des middlewares spécifiques à ce groupe. Dans notre cas, `AuthMiddleware` est appliqué uniquement aux routes de modification (`POST`, `PUT`, `DELETE`).
    *   `c.Next()` : Dans un middleware, `c.Next()` passe le contrôle au prochain middleware ou au handler de route.
    *   `c.AbortWithStatusJSON()` : Dans un middleware, cette méthode interrompt la chaîne de traitement de la requête et renvoie une réponse JSON avec le statut spécifié.

6.  **Gestion de la Concurrence (`sync.RWMutex`) :**
    *   `tasks` est une ressource partagée (variable globale). Sans synchronisation, des conditions de concurrence (race conditions) pourraient survenir si plusieurs requêtes tentent de modifier `tasks` simultanément.
    *   `sync.RWMutex` (Read-Write Mutex) est utilisé :
        *   `tasksMutex.RLock()` / `tasksMutex.RUnlock()` : Pour les opérations de lecture (`GET`), plusieurs goroutines peuvent lire simultanément.
        *   `tasksMutex.Lock()` / `tasksMutex.Unlock()` : Pour les opérations d'écriture (`POST`, `PUT`, `DELETE`), une seule goroutine peut écrire à la fois. Cela est plus performant qu'un simple `sync.Mutex` si les lectures sont beaucoup plus fréquentes que les écritures.

7.  **Génération d'ID (`github.com/google/uuid`) :**
    *   Le package `uuid` est utilisé pour générer des identifiants uniques universellement (UUID) pour les nouvelles tâches, une bonne pratique pour les ressources d'API.

Ce TP vous a permis d'explorer les fonctionnalités clés de Gin pour construire une API RESTful, y compris la gestion des routes, le traitement JSON, l'implémentation de middlewares et la gestion de la concurrence, jetant ainsi les bases pour des applications web Go plus complexes.