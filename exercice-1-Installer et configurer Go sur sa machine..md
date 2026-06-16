Bonjour ! Ce TP est conçu pour vous aider à démarrer rapidement avec le langage Go. Nous allons installer Go sur votre machine, configer l'environnement et exécuter votre premier programme.

---

### TP : Premiers Pas avec Go - Installation et "Hello World"

**Objectif du TP :** Installer et configurer Go sur sa machine.

**Énoncé du TP :** Installer Go, configurer l'environnement et exécuter un programme 'Hello World'.

---

**Prérequis :**
*   Connaissances de base de votre système d'exploitation (Windows, macOS, Linux).
*   Savoir utiliser un terminal ou une invite de commande.

**Matériel :**
*   Un ordinateur connecté à internet.

**Durée Estimée :** 30-45 minutes

---

**Consignes Générales :**

Ce TP est conçu pour vous guider pas à pas. N'hésitez pas à utiliser une IA générative (ChatGPT, Bard, Copilot, etc.) comme un assistant personnel tout au long de cet exercice. Elle peut vous aider à :
*   Trouver les commandes spécifiques à votre version exacte de système d'exploitation si celles fournies ici ne correspondent pas.
*   Dépanner des erreurs que vous pourriez rencontrer (copiez-collez le message d'erreur).
*   Expliquer plus en détail certains concepts (comme les variables d'environnement).

**Attention :** Vérifiez toujours la pertinence et la sécurité des informations fournies par l'IA, surtout pour les commandes système. Prenez des notes des commandes et des configurations que vous effectuez.

---

#### Étapes du TP

**Étape 1 : Téléchargement et Installation de Go**

1.  **Rendez-vous sur le site officiel :**
    Ouvrez votre navigateur et accédez à `https://go.dev/dl/`

2.  **Téléchargez l'installeur :**
    Identifiez et téléchargez le fichier d'installation correspondant à votre système d'exploitation.

3.  **Procédez à l'installation :**

    *   **Pour Windows :**
        *   Exécutez le fichier `.msi` téléchargé.
        *   Suivez les instructions de l'assistant d'installation. Les options par défaut sont généralement suffisantes.

    *   **Pour macOS :**
        *   Exécutez le package `.pkg` téléchargé.
        *   Suivez les instructions de l'assistant d'installation.

    *   **Pour Linux (ex: Debian/Ubuntu) :**
        *   Vous pouvez utiliser le gestionnaire de paquets de votre distribution pour une installation simple :
            ```bash
            sudo apt update
            sudo apt install golang-go
            ```
        *   Alternativement, pour une version plus récente ou si vous préférez l'installation manuelle :
            *   Extrayez l'archive téléchargée dans `/usr/local` :
                ```bash
                sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go<VERSION>.linux-amd64.tar.gz
                ```
                (Remplacez `<VERSION>` par la version téléchargée, ex: `go1.22.4`)
            *   Ajoutez Go à votre `PATH` en ajoutant la ligne suivante à votre fichier `~/.profile` ou `~/.bashrc` (ou `~/.zshrc` si vous utilisez Zsh) :
                ```bash
                export PATH=$PATH:/usr/local/go/bin
                ```
            *   Appliquez les changements : `source ~/.profile` (ou le fichier que vous avez modifié).

    *   **Pour les autres distributions Linux ou si vous rencontrez des difficultés :**
        *   Consultez la documentation officielle de Go ou demandez à votre IA les commandes spécifiques pour votre distribution.

**Étape 2 : Vérification de l'Installation**

1.  **Ouvrez un nouveau terminal ou une nouvelle invite de commande.**
    (Il est important d'ouvrir une nouvelle fenêtre pour que les changements de `PATH` soient pris en compte).

2.  **Vérifiez la version de Go :**
    Tapez la commande suivante et appuyez sur Entrée :
    ```bash
    go version
    ```
    Vous devriez voir s'afficher la version de Go installée, par exemple : `go version go1.22.4 linux/amd64`.

**Étape 3 : Configuration de l'Environnement Go**

Go configure généralement les variables d'environnement essentielles (`GOROOT`, `PATH`) automatiquement lors de l'installation. Cependant, il est bon de connaître et de vérifier `GOPATH`.

1.  **Comprendre `GOPATH` :**
    `GOPATH` est votre espace de travail Go. C'est là que Go s'attend à trouver le code source de vos projets, les paquets tiers que vous téléchargez, et les exécutables que vous compilez.

2.  **Vérifiez la valeur de `GOPATH` :**
    Tapez la commande suivante :
    ```bash
    go env GOPATH
    ```
    Par défaut, `GOPATH` est souvent `~/go` sur Linux/macOS ou `%USERPROFILE%\go` sur Windows.

3.  **Si `GOPATH` n'est pas défini ou si vous souhaitez le modifier :**
    Bien que cela soit moins courant avec les versions récentes de Go (qui peuvent gérer les modules en dehors du `GOPATH`), si vous avez besoin de le définir explicitement :
    *   **Pour Linux/macOS :** Ajoutez la ligne suivante à votre fichier `~/.profile` ou `~/.bashrc` (ou `~/.zshrc`) :
        ```bash
        export GOPATH=$HOME/go
        export PATH=$PATH:$GOPATH/bin
        ```
        Puis appliquez les changements : `source ~/.profile`.
    *   **Pour Windows :**
        *   Recherchez "Variables d'environnement" dans le menu Démarrer.
        *   Cliquez sur "Modifier les variables d'environnement système".
        *   Dans l'onglet "Avancé", cliquez sur "Variables d'environnement...".
        *   Dans la section "Variables utilisateur", cliquez sur "Nouvelle..." et ajoutez :
            *   Nom de la variable : `GOPATH`
            *   Valeur de la variable : `C:\Users\VotreNomUtilisateur\go` (adaptez le chemin si nécessaire).
        *   Assurez-vous que `%GOPATH%\bin` est également ajouté à votre variable `Path` utilisateur.

**Étape 4 : Création et Exécution du Programme "Hello World"**

1.  **Créez un répertoire pour votre premier projet Go :**
    Naviguez vers votre `GOPATH` (ou un autre répertoire de votre choix si vous utilisez les modules Go, mais pour ce TP, restons simple).
    ```bash
    # Exemple pour Linux/macOS
    mkdir -p ~/go/src/hello
    cd ~/go/src/hello

    # Exemple pour Windows
    mkdir %USERPROFILE%\go\src\hello
    cd %USERPROFILE%\go\src\hello
    ```
    Le répertoire `src` est une convention pour le code source.

2.  **Créez un fichier `main.go` :**
    Utilisez votre éditeur de texte préféré (VS Code, Sublime Text, Notepad++, Vim, Nano, etc.) pour créer un fichier nommé `main.go` dans le répertoire `hello`.

3.  **Collez le code suivant dans `main.go` :**
    ```go
    package main

    import "fmt"

    func main() {
        fmt.Println("Hello, Go World!")
    }
    ```
    *   `package main` : Déclare que ce fichier est un programme exécutable.
    *   `import "fmt"` : Importe le package `fmt` qui fournit des fonctions de formatage d'entrée/sortie (comme l'impression à l'écran).
    *   `func main()` : C'est la fonction principale, le point d'entrée de votre programme.
    *   `fmt.Println(...)` : Affiche la chaîne de caractères sur la console, suivie d'un saut de ligne.

4.  **Enregistrez le fichier.**

5.  **Exécutez le programme :**
    Dans votre terminal, assurez-vous d'être dans le répertoire `~/go/src/hello` (ou l'équivalent Windows) et tapez :
    ```bash
    go run main.go
    ```
    Vous devriez voir s'afficher :
    ```
    Hello, Go World!
    ```

6.  **Compilez le programme (optionnel mais recommandé) :**
    Pour créer un exécutable autonome de votre programme :
    ```bash
    go build main.go
    ```
    Cette commande va créer un fichier exécutable nommé `main` (sur Linux/macOS) ou `main.exe` (sur Windows) dans le répertoire courant.

7.  **Exécutez l'exécutable compilé :**
    ```bash
    # Pour Linux/macOS
    ./main

    # Pour Windows
    .\main.exe
    ```
    Vous devriez voir le même message : `Hello, Go World!`.

---

**Rendu / Validation :**

Pour valider votre TP, fournissez une capture d'écran de votre terminal montrant :
1.  La commande `go version` et sa sortie.
2.  La commande `go run main.go` et l'affichage de "Hello, Go World!".
3.  (Optionnel) La commande `go build main.go` suivie de l'exécution de l'exécutable (`./main` ou `.\main.exe`).

---

**Pour Aller Plus Loin :**

*   Explorez la documentation officielle de Go (`https://go.dev/doc/`).
*   Essayez de modifier le message "Hello, Go World!" et d'ajouter d'autres lignes d'impression.
*   Demandez à votre IA de vous expliquer la structure d'un programme Go (`package main`, `import`, `func main`) avec des exemples.
*   Installez un environnement de développement intégré (IDE) comme VS Code et l'extension Go pour une meilleure expérience de codage.