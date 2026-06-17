Voici une solution concise et commentée pour le TP "Premiers Pas avec Go - Installation et 'Hello World'".

---

### Solution TP : Premiers Pas avec Go - Installation et "Hello World"

#### Étape 1 : Téléchargement et Installation de Go

1.  **Téléchargement :**
    Rendez-vous sur le site officiel de Go : `https://go.dev/dl/`.
    Téléchargez l'installeur correspondant à votre système d'exploitation (Windows, macOS, Linux). Choisissez la version stable la plus récente.

2.  **Installation :**

    *   **Windows :** Exécutez le fichier `.msi` téléchargé et suivez l'assistant. Les options par défaut sont généralement suffisantes.
    *   **macOS :** Exécutez le package `.pkg` téléchargé et suivez l'assistant.
    *   **Linux (méthode recommandée pour la dernière version stable) :**
        *   Supprimez toute installation Go existante et extrayez l'archive téléchargée dans `/usr/local`. Remplacez `<VERSION>` par la version téléchargée (ex: `go1.22.4`).
        
```bash
        sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go<VERSION>.linux-amd64.tar.gz
        ```

        *   Ajoutez le chemin des exécutables Go à votre variable d'environnement `PATH`. Modifiez votre fichier de profil shell (ex: `~/.profile`, `~/.bashrc`, ou `~/.zshrc`) :
        
```bash
        echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.profile
        source ~/.profile # Applique les changements
        ```

        *   *Note :* Pour une installation rapide mais potentiellement avec une version plus ancienne, utilisez le gestionnaire de paquets de votre distribution (ex: `sudo apt install golang-go` pour Debian/Ubuntu).

#### Étape 2 : Vérification de l'Installation

1.  **Ouvrez un nouveau terminal** pour que les modifications du `PATH` soient prises en compte.
2.  **Vérifiez la version de Go :**
    
```bash
    go version
    ```

    *Exemple de sortie attendue :* `go version go1.22.4 linux/amd64` (la version peut varier).

#### Étape 3 : Configuration de l'Environnement Go

Go gère désormais la plupart des aspects de l'environnement via les modules Go, rendant `GOPATH` moins critique pour les nouveaux projets. Cependant, il est utile de connaître sa valeur par défaut.

1.  **Vérifiez la valeur de `GOPATH` :**
    
```bash
    go env GOPATH
    ```

    *Exemple de sortie attendue :* `/home/votreutilisateur/go` (Linux/macOS) ou `C:\Users\VotreNomUtilisateur\go` (Windows).
    *Note :* Pour la plupart des développements modernes avec Go Modules, vous n'aurez pas besoin de définir `GOPATH` manuellement. Go créera un répertoire `go` dans votre dossier utilisateur par défaut.

#### Étape 4 : Création et Exécution du Programme "Hello World"

Pour une approche moderne et recommandée avec Go Modules :

1.  **Créez un répertoire pour votre projet et initialisez un module Go :**
    
```bash
    # Crée un répertoire pour le projet
    mkdir my-hello-app
    cd my-hello-app

    # Initialise un nouveau module Go. Remplacez 'example.com/my-hello-app' par un chemin de module pertinent.
    go mod init example.com/my-hello-app
    ```

    *Explication :* `go mod init` crée un fichier `go.mod` qui gère les dépendances de votre projet, une pratique standard en Go.

2.  **Créez le fichier `main.go` :**
    Dans le répertoire `my-hello-app`, créez un fichier nommé `main.go` avec le contenu suivant :
    
```go
    package main // Déclare que ce fichier est le point d'entrée d'un programme exécutable

    import "fmt" // Importe le package 'fmt' pour les fonctions de formatage et d'impression

    func main() { // La fonction 'main' est le point de départ de l'exécution du programme
        fmt.Println("Hello, Go World!") // Affiche la chaîne de caractères sur la console
    }
    ```


3.  **Exécutez le programme :**
    Depuis le répertoire `my-hello-app` :
    
```bash
    go run main.go
    ```

    *Sortie attendue :*
    
```
    Hello, Go World!
    ```


4.  **Compilez le programme (pour créer un exécutable autonome) :**
    
```bash
    go build -o hello-app main.go
    ```

    *Explication :* Cette commande compile votre code source en un fichier exécutable nommé `hello-app` (ou `hello-app.exe` sur Windows) dans le répertoire courant.

5.  **Exécutez l'exécutable compilé :**
    
```bash
    # Pour Linux/macOS
    ./hello-app

    # Pour Windows
    .\hello-app.exe
    ```

    *Sortie attendue :*
    
```
    Hello, Go World!
    ```


---