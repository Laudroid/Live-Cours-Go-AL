Voici un sujet de Travaux Pratiques (TP) conçu pour vous guider dans l'accès aux bases de données en Go, en utilisant `database/sql`, `sqlx` et `GORM`.

---

## TP: Accès aux Bases de Données en Go avec `database/sql`, `sqlx` et `GORM`

### Objectif du TP

Ce TP vise à vous permettre de :
*   Établir une connexion à une base de données SQLite en Go.
*   Maîtriser les opérations CRUD (Create, Read, Update, Delete) sur des données.
*   Comprendre et appliquer l'utilisation du package standard `database/sql`.
*   Découvrir les avantages de `sqlx` pour simplifier les interactions avec `database/sql`.
*   Appréhender l'utilisation d'un ORM (Object-Relational Mapper) comme `GORM` pour une abstraction accrue.

### Contexte

Vous allez développer un programme Go qui gère une simple entité `User` (utilisateur) dans une base de données SQLite. SQLite est choisi pour sa simplicité de mise en œuvre, ne nécessitant pas de serveur de base de données externe.

### Prérequis

*   Go (version 1.18 ou supérieure) installé et configuré.
*   Connaissances de base en SQL (syntaxe `CREATE TABLE`, `INSERT`, `SELECT`, `UPDATE`, `DELETE`).
*   Familiarité avec les modules Go (`go mod`).

### Mise en place du projet

1.  Créez un nouveau répertoire pour votre projet :
    ```bash
    mkdir go-db-tp
    cd go-db-tp
    ```
2.  Initialisez un nouveau module Go :
    ```bash
    go mod init go-db-tp
    ```
3.  Installez les dépendances nécessaires :
    *   **Driver SQLite pour `database/sql` et `sqlx` :**
        ```bash
        go get github.com/mattn/go-sqlite3
        ```
    *   **Package `sqlx` :**
        ```bash
        go get github.com/jmoiron/sqlx
        ```
    *   **GORM et son driver SQLite :**
        ```bash
        go get gorm.io/gorm gorm.io/driver/sqlite
        ```

### Structure de données

Pour ce TP, nous allons utiliser une structure `User` simple :

```go
package main

// User représente un utilisateur dans la base de données
type User struct {
	ID    int    `db:"id" gorm:"primaryKey;autoIncrement"`
	Name  string `db:"name"`
	Email string `db:"email"`
}
```
*   `db:"..."` : Tags utilisés par `sqlx` pour mapper les colonnes SQL aux champs de la structure.
*   `gorm:"..."` : Tags utilisés par `GORM` pour définir le comportement du modèle (clé primaire, auto-incrément, etc.).

### Exercice 1: `database/sql` et `sqlx`

Dans cette partie, vous allez implémenter les opérations CRUD en utilisant le package standard `database/sql` et ensuite en l'améliorant avec `sqlx`.

#### 1.1. Connexion et Initialisation de la base de données

1.  Créez un fichier `main.go`.
2.  Établissez une connexion à une base de données SQLite nommée `test.db`.
3.  Assurez-vous de fermer la connexion à la base de données à la fin de l'exécution du programme (`defer db.Close()`).
4.  Créez la table `users` si elle n'existe pas. La table doit avoir les colonnes `id` (INTEGER PRIMARY KEY AUTOINCREMENT), `name` (TEXT NOT NULL), et `email` (TEXT UNIQUE NOT NULL).

    ```go
    // Exemple de connexion (à intégrer dans votre main.go)
    import (
        "database/sql"
        _ "github.com/mattn/go-sqlite3" // Le underscore indique que le package est importé pour ses effets secondaires (enregistrement du driver)
        "log"
    )

    func main() {
        db, err := sql.Open("sqlite3", "./test.db")
        if err != nil {
            log.Fatal(err)
        }
        defer db.Close()

        // Vérifier la connexion
        err = db.Ping()
        if err != nil {
            log.Fatal(err)
        }
        log.Println("Connecté à la base de données SQLite.")

        // TODO: Créer la table users
    }
    ```

#### 1.2. Opérations CRUD avec `database/sql`

Implémentez les fonctions suivantes en utilisant *uniquement* `database/sql` :

*   **`CreateUserSQL(db *sql.DB, user User) (int64, error)`**
    *   Insère un nouvel utilisateur dans la table `users`.
    *   Retourne l'ID du nouvel utilisateur inséré.
*   **`GetUsersSQL(db *sql.DB) ([]User, error)`**
    *   Récupère tous les utilisateurs de la table `users`.
    *   Retourne une slice de `User`.
*   **`GetUserByIDSQL(db *sql.DB, id int) (*User, error)`**
    *   Récupère un utilisateur spécifique par son ID.
    *   Retourne un pointeur vers `User` ou `nil` si non trouvé.
*   **`UpdateUserSQL(db *sql.DB, user User) error`**
    *   Met à jour le nom et l'email d'un utilisateur existant (identifié par son ID).
*   **`DeleteUserSQL(db *sql.DB, id int) error`**
    *   Supprime un utilisateur par son ID.

Testez chacune de ces fonctions dans votre `main.go`.

#### 1.3. Amélioration avec `sqlx`

Maintenant, refactorisez les fonctions CRUD précédentes en utilisant `sqlx`. `sqlx` est une extension de `database/sql` qui simplifie grandement le mapping des résultats de requêtes SQL vers des structs Go.

*   Modifiez la connexion pour utiliser `sqlx.Open` et obtenir un `*sqlx.DB`.
*   **`CreateUserSQLX(db *sqlx.DB, user User) (int64, error)`**
    *   Utilisez `db.Exec` comme précédemment, car l'insertion est similaire.
*   **`GetUsersSQLX(db *sqlx.DB) ([]User, error)`**
    *   Utilisez `db.Select` pour récupérer tous les utilisateurs directement dans une slice de `User`.
*   **`GetUserByIDSQLX(db *sqlx.DB, id int) (*User, error)`**
    *   Utilisez `db.Get` pour récupérer un utilisateur unique directement dans une struct `User`.
*   **`UpdateUserSQLX(db *sqlx.DB, user User) error`**
    *   Utilisez `db.Exec`.
*   **`DeleteUserSQLX(db *sqlx.DB, id int) error`**
    *   Utilisez `db.Exec`.

Testez ces nouvelles fonctions dans votre `main.go` et comparez la lisibilité et la concision du code par rapport à la version `database/sql` pure.

### Exercice 2: `GORM`

Dans cette partie, vous allez implémenter les mêmes opérations CRUD en utilisant l'ORM `GORM`.

#### 2.1. Connexion et Auto-migration

1.  Établissez une connexion à une base de données SQLite *différente* pour éviter les conflits avec l'exercice précédent, par exemple `gorm_test.db`.
2.  Utilisez `gorm.Open` avec le driver `sqlite.Open`.
3.  Utilisez `db.AutoMigrate(&User{})` pour que GORM crée ou mette à jour la table `users` automatiquement en fonction de la structure `User`.

    ```go
    // Exemple de connexion GORM (à intégrer dans votre main.go)
    import (
        "gorm.io/driver/sqlite"
        "gorm.io/gorm"
        "log"
    )

    func main() {
        // ... code précédent ...

        gormDB, err := gorm.Open(sqlite.Open("gorm_test.db"), &gorm.Config{})
        if err != nil {
            log.Fatal("Échec de la connexion à la base de données GORM:", err)
        }
        log.Println("Connecté à la base de données SQLite avec GORM.")

        // Auto-migration
        err = gormDB.AutoMigrate(&User{})
        if err != nil {
            log.Fatal("Échec de l'auto-migration GORM:", err)
        }
        log.Println("Auto-migration GORM effectuée.")

        // TODO: Appeler les fonctions CRUD GORM
    }
    ```

#### 2.2. Opérations CRUD avec `GORM`

Implémentez les fonctions suivantes en utilisant `GORM` :

*   **`CreateUserGORM(db *gorm.DB, user User) error`**
    *   Insère un nouvel utilisateur. L'ID sera automatiquement mis à jour dans la struct `user` après l'insertion.
*   **`GetUsersGORM(db *gorm.DB) ([]User, error)`**
    *   Récupère tous les utilisateurs.
*   **`GetUserByIDGORM(db *gorm.DB, id int) (*User, error)`**
    *   Récupère un utilisateur spécifique par son ID.
*   **`UpdateUserGORM(db *gorm.DB, user User) error`**
    *   Met à jour un utilisateur existant.
*   **`DeleteUserGORM(db *gorm.DB, id int) error`**
    *   Supprime un utilisateur par son ID.

Testez chacune de ces fonctions dans votre `main.go`.

### Conseils pour l'utilisation de l'IA

L'IA est un outil puissant. Voici comment l'utiliser efficacement pour ce TP :

*   **Demandez des explications, pas seulement du code :** Si vous ne comprenez pas une partie du code ou un concept (ex: "Qu'est-ce que `db.Ping()` fait exactement ?", "Quelle est la différence entre `db.Exec` et `db.Query` ?"), interrogez l'IA.
*   **Générez des exemples ciblés :** Au lieu de demander "tout le code du TP", demandez "Comment insérer un utilisateur avec `sqlx` ?" ou "Donne-moi un exemple de `db.Select` avec une struct `User`".
*   **Validez le code généré :** L'IA peut faire des erreurs ou générer du code qui ne correspond pas exactement à vos besoins. Lisez attentivement le code, testez-le et assurez-vous de le comprendre avant de l'intégrer.
*   **Explorez les alternatives :** Demandez à l'IA "Y a-t-il d'autres façons de faire ceci avec GORM ?" ou "Quels sont les pièges courants avec `database/sql` ?".
*   **Demandez de l'aide pour le débogage :** Si vous rencontrez une erreur spécifique, copiez le message d'erreur et le code concerné à l'IA pour obtenir des pistes de résolution.
*   **N'oubliez pas les tests :** Demandez à l'IA de vous aider à écrire des tests unitaires simples pour vos fonctions CRUD. C'est une excellente pratique.

### Points d'attention et d'évaluation

*   **Gestion des erreurs :** Chaque opération de base de données peut échouer. Assurez-vous de vérifier systématiquement les erreurs et de les gérer de manière appropriée (par exemple, `log.Fatal` pour les erreurs critiques, ou retourner l'erreur pour un traitement ultérieur).
*   **Clarté du code :** Votre code doit être lisible, bien structuré et commenté si nécessaire.
*   **Compréhension des différences :** Soyez capable d'expliquer les avantages et inconvénients de chaque approche (`database/sql` pur, `sqlx`, `GORM`) et dans quels contextes l'une pourrait être préférée à l'autre.
*   **Nettoyage :** Pensez à supprimer les fichiers `.db` entre les exécutions si vous voulez repartir d'une base de données vierge.

Ce TP vous donne une base solide pour interagir avec les bases de données en Go. Bon courage !