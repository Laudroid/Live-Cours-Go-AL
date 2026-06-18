## Travaux Pratiques : API REST Avancée avec Gin

### Objectif du TP

Ce TP vise à vous familiariser avec les fonctionnalités avancées du framework Gin pour la création d'APIs RESTful en Go. Vous apprendrez à structurer une API, à gérer les requêtes et réponses JSON, à implémenter des middlewares personnalisés et à gérer les erreurs de manière robuste.

### Contexte

Gin est un framework web léger et performant pour Go, idéal pour construire des microservices et des APIs REST. Maîtriser ses concepts fondamentaux, y compris la gestion des middlewares et le traitement des données JSON, est essentiel pour développer des applications web modernes et efficaces.

### Prérequis

*   Connaissance de base du langage Go.
*   Connaissance des principes des APIs REST (méthodes HTTP, codes de statut).
*   Go installé sur votre machine.
*   Un éditeur de code (VS Code, GoLand, etc.).
*   Un outil pour tester les APIs (Postman, Insomnia, curl, Thunder Client pour VS Code).

### Énoncé Détaillé du TP

Vous allez construire une API de gestion de tâches (Todo List) simple mais complète.

#### 1. Initialisation du Projet

1.  Créez un nouveau répertoire pour votre projet (ex: `gin-todo-api`).
2.  Initialisez un module Go : `go mod init gin-todo-api` (adaptez le nom du module si besoin).
3.  Installez Gin : `go get github.com/gin-gonic/gin`

#### 2. Modèle de Données

Définissez la structure d'une tâche. Utilisez des tags `json` pour la sérialisation/désérialisation.

```go
// Dans main.go ou un fichier models/task.go
type Task struct {
    ID          string `json:"id"`
    Title       string `json:"title" binding:"required"` // 'binding:"required"' pour la validation
    Description string `json:"description"`
    Done        bool   `json:"done"`
}

// Stockage en mémoire pour ce TP. Dans une application réelle, ce serait une base de données.
var tasks = make(map[string]Task) // Utilisation d'une map pour faciliter la recherche, mise à jour et suppression par ID
```

Initialisez quelques tâches fictives dans votre `main` pour avoir des données de départ.

#### 3. Configuration de l'API et Routes de Base

1.  Dans `main.go`, initialisez un routeur Gin.
2.  Créez les routes suivantes :

    *   **`GET /tasks`** : Récupère toutes les tâches.
        *   Retourne un tableau JSON de toutes les tâches existantes.
        *   Code de statut : `200 OK`.

    *   **`GET /tasks/:id`** : Récupère une tâche spécifique par son ID.
        *   L'ID sera passé en paramètre d'URL.
        *   Si la tâche est trouvée, retourne l'objet JSON de la tâche.
        *   Si la tâche n'est pas trouvée, retourne un message d'erreur JSON et un code de statut `404 Not Found`.

#### 4. Création et Mise à Jour de Tâches (Traitement JSON en entrée)

1.  **`POST /tasks`** : Crée une nouvelle tâche.
    *   Accepte un objet JSON dans le corps de la requête (contenant `title` et `description`).
    *   Utilisez `c.ShouldBindJSON()` pour lier le JSON entrant à une nouvelle instance de `Task`.
    *   Générez un `ID` unique pour la nouvelle tâche (vous pouvez utiliser `github.com/google/uuid` ou une simple chaîne aléatoire).
    *   Ajoutez la tâche à votre stockage en mémoire.
    *   Retourne la tâche créée avec son nouvel ID et un code de statut `201 Created`.
    *   **Gestion d'erreur :** Si le JSON est mal formé ou si le `title` est manquant (grâce au tag `binding:"required"`), retournez un code `400 Bad Request` avec un message d'erreur explicite.

2.  **`PUT /tasks/:id`** : Met à jour une tâche existante.
    *   L'ID de la tâche à mettre à jour est dans l'URL.
    *   Accepte un objet JSON dans le corps de la requête. Les champs (`title`, `description`, `done`) sont optionnels : seuls les champs présents dans le JSON doivent être mis à jour.
    *   Si la tâche n'est pas trouvée, retournez un `404 Not Found`.
    *   Si le JSON est mal formé, retournez un `400 Bad Request`.
    *   Retourne la tâche mise à jour et un code de statut `200 OK`.

#### 5. Suppression de Tâches

1.  **`DELETE /tasks/:id`** : Supprime une tâche existante.
    *   L'ID de la tâche à supprimer est dans l'URL.
    *   Si la tâche n'est pas trouvée, retournez un `404 Not Found`.
    *   Si la suppression est réussie, retournez un code de statut `204 No Content` (sans corps de réponse).

#### 6. Implémentation de Middlewares Personnalisés

Créez et appliquez les middlewares suivants :

1.  **`LoggerMiddleware`** :
    *   Ce middleware doit logguer chaque requête entrante.
    *   Pour chaque requête, affichez dans la console : l'heure de la requête, la méthode HTTP, le chemin de la requête, l'adresse IP du client, et le temps de traitement de la requête.
    *   Appliquez ce middleware globalement à toutes les routes.

2.  **`AuthMiddleware` (simple)** :
    *   Ce middleware doit vérifier la présence d'un en-tête `X-API-KEY` dans la requête.
    *   Si l'en-tête est absent ou si sa valeur ne correspond pas à une clé prédéfinie (ex: `"super-secret-key"`), la requête doit être bloquée avec un code de statut `401 Unauthorized` et un message d'erreur JSON.
    *   Si la clé est valide, la requête doit continuer son traitement.
    *   Appliquez ce middleware uniquement aux routes `POST`, `PUT` et `DELETE` (vous pouvez utiliser un `router.Group()` pour cela).

#### 7. Gestion des Erreurs Centralisée (Optionnel mais recommandé)

Bien que Gin gère déjà certaines erreurs, vous pouvez explorer des approches pour centraliser la gestion des erreurs ou personnaliser les messages d'erreur pour des cas spécifiques (ex: erreurs de validation plus détaillées).

### Consignes Générales

*   **Structure du code :** Pour ce TP, un seul fichier `main.go` est acceptable. Pour des projets plus importants, pensez à organiser votre code en packages (ex: `models`, `handlers`, `middleware`).
*   **Tests :** Testez chaque endpoint avec votre outil préféré (Postman, Insomnia, curl) pour vérifier son bon fonctionnement.
*   **Utilisation de l'IA :** L'utilisation d'outils d'IA pour vous aider à générer du code ou à comprendre des concepts est encouragée. Cependant, assurez-vous de **comprendre le code généré** et de pouvoir l'expliquer. Le but est d'apprendre, pas seulement de copier-coller.
*   **Clarté et lisibilité :** Écrivez un code propre, commenté si nécessaire, et facile à lire.

### Ressources Utiles

*   Documentation officielle de Gin : [https://gin-gonic.com/docs/](https://gin-gonic.com/docs/)
*   Package `uuid` pour Go : [https://github.com/google/uuid](https://github.com/google/uuid)

### Bonus (Pour les plus rapides ou les plus curieux)

*   **Validation plus avancée :** Explorez le package `validator` de Go pour des règles de validation plus complexes (longueur minimale, format d'email, etc.).
*   **Gestion des versions d'API :** Implémentez un préfixe de version pour vos routes (ex: `/api/v1/tasks`).
*   **Configuration externe :** Utilisez un fichier de configuration (ex: `.env`, `config.json`) pour la clé API secrète au lieu de la coder en dur.

Bon courage pour ce TP ! N'hésitez pas à expérimenter et à poser des questions si vous rencontrez des difficultés.