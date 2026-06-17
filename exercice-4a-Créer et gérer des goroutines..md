Voici un sujet de Travaux Pratiques (TP) conçu pour vous guider dans la création et la gestion de goroutines en Go, en mettant l'accent sur la synchronisation.

---

## TP : Maîtrise des Goroutines et de la Synchronisation en Go

### Objectif du TP

Créer, lancer et synchroniser l'exécution de multiples goroutines pour réaliser des tâches concurrentes de manière contrôlée.

### Contexte

La concurrence est une pierre angulaire du langage Go, et les goroutines en sont le mécanisme fondamental. Elles permettent d'exécuter des fonctions de manière indépendante et légère. Cependant, pour qu'un programme concurrent fonctionne correctement, il est essentiel de synchroniser ces goroutines, que ce soit pour attendre leur achèvement ou pour échanger des données entre elles.

### Prérequis

*   Installation de Go (version 1.18 ou supérieure recommandée).
*   Connaissance des bases du langage Go (fonctions, boucles, variables, pointeurs).
*   Un éditeur de code (VS Code, GoLand, etc.).

### Consignes Générales

*   Créez un nouveau module Go pour ce TP (`go mod init mon_tp_goroutines`).
*   Organisez votre code dans des fichiers `.go` clairs.
*   Utilisez des noms de variables et de fonctions explicites.
*   Commentez votre code lorsque cela est nécessaire pour expliquer des choix ou des parties complexes.
*   Testez votre code après chaque exercice pour vérifier son comportement.
*   **Utilisation de l'IA :** L'utilisation d'outils d'IA générative (comme ChatGPT, Copilot, etc.) est encouragée pour explorer des solutions, comprendre des concepts ou déboguer. L'objectif reste votre compréhension et votre capacité à expliquer le code que vous produisez. N'hésitez pas à demander à l'IA des explications sur des parties du code Go que vous ne comprenez pas, ou à lui demander des exemples de mise en œuvre.

---

### Exercice 1 : Lancement Simple et le Problème de la Sortie Prématurée

Dans cet exercice, vous allez lancer des goroutines sans mécanisme de synchronisation pour observer un comportement courant.

1.  **Créez une fonction `effectuerTache(id int)`** :
    *   Cette fonction prend un entier `id` en paramètre.
    *   Elle doit afficher un message indiquant qu'elle démarre (ex: "Goroutine %d: Début de la tâche...", avec l'ID).
    *   Elle doit simuler un travail en utilisant `time.Sleep` pendant une durée aléatoire (par exemple, entre 50 et 500 millisecondes). Pour cela, importez le package `math/rand` et `time`. N'oubliez pas d'initialiser le générateur de nombres aléatoires une seule fois dans `main` avec `rand.Seed(time.Now().UnixNano())`.
    *   Elle doit afficher un message indiquant qu'elle a terminé (ex: "Goroutine %d: Tâche terminée.").

2.  **Dans la fonction `main()`** :
    *   Lancez 5 goroutines, chacune appelant `effectuerTache` avec un ID unique (de 1 à 5).
    *   Après avoir lancé toutes les goroutines, la fonction `main` doit afficher "Toutes les goroutines lancées."

3.  **Exécutez votre programme.**
    *   **Question :** Que constatez-vous dans la sortie ? Est-ce que toutes les goroutines terminent leur travail avant que le programme ne s'arrête ? Expliquez pourquoi.

---

### Exercice 2 : Synchronisation avec `sync.WaitGroup`

Pour résoudre le problème de la sortie prématurée du programme, nous allons introduire `sync.WaitGroup`.

1.  **Modifiez votre fonction `main()`** :
    *   Importez le package `sync`.
    *   Déclarez une variable de type `sync.WaitGroup`.
    *   Avant de lancer chaque goroutine, utilisez `wg.Add(1)` pour indiquer que vous attendez une goroutine supplémentaire.
    *   Modifiez la fonction `effectuerTache` pour qu'elle appelle `defer wg.Done()` juste après son démarrage. Cela garantira que `wg.Done()` est appelé lorsque la goroutine se termine, qu'elle se termine normalement ou avec une panique.
    *   Après avoir lancé toutes les goroutines, utilisez `wg.Wait()` pour bloquer l'exécution de `main` jusqu'à ce que toutes les goroutines aient appelé `wg.Done()`.
    *   Ajoutez un message après `wg.Wait()` pour confirmer que "Toutes les goroutines ont terminé leur exécution."

2.  **Exécutez votre programme.**
    *   **Question :** Le comportement du programme a-t-il changé ? Toutes les goroutines terminent-elles maintenant leur travail ?

---

### Exercice 3 : Communication et Récupération de Résultats avec les Canaux

`sync.WaitGroup` permet d'attendre la fin des goroutines. Les canaux (`chan`) permettent en plus de communiquer des données entre elles.

1.  **Modifiez votre fonction `effectuerTache(id int, resultChan chan string)`** :
    *   La fonction doit maintenant prendre un paramètre supplémentaire : un canal de type `chan string`.
    *   Après avoir terminé sa tâche (et avant d'appeler `wg.Done()`), la goroutine doit envoyer un message de résultat au canal. Par exemple : "Goroutine %d a terminé avec succès.".

2.  **Modifiez votre fonction `main()`** :
    *   Créez un canal de type `chan string` (non-bufferisé ou bufferisé, à vous de choisir et d'observer la différence si vous expérimentez).
    *   Passez ce canal à chaque goroutine lors de son lancement.
    *   **Après** l'appel à `wg.Wait()` :
        *   Fermez le canal de résultats en utilisant `close(resultChan)`. C'est une étape cruciale pour signaler qu'aucune autre donnée ne sera envoyée sur ce canal.
    *   **Ensuite**, lisez tous les messages du canal jusqu'à ce qu'il soit fermé. Vous pouvez utiliser une boucle `for range` sur le canal pour cela. Affichez chaque message reçu.

3.  **Exécutez votre programme.**
    *   **Question :** Quel est l'ordre d'affichage des messages de fin de tâche et des messages de résultats ? Est-ce que l'ordre des résultats correspond à l'ordre des IDs des goroutines ? Expliquez pourquoi.

---

### Exercice 4 : Gestion d'un Pool de Travailleurs (Optionnel / Pour aller plus loin)

Cet exercice est plus avancé et vise à simuler un scénario où un nombre limité de goroutines (travailleurs) traitent un ensemble de tâches.

1.  **Créez une fonction `travailleur(id int, taches <-chan int, resultats chan<- string, wg *sync.WaitGroup)`** :
    *   `id`: ID du travailleur.
    *   `taches`: Un canal en lecture seule (`<-chan int`) d'où le travailleur recevra les IDs de tâche à traiter.
    *   `resultats`: Un canal en écriture seule (`chan<- string`) où le travailleur enverra ses résultats.
    *   `wg`: Un pointeur vers un `sync.WaitGroup` pour la synchronisation.
    *   Chaque travailleur doit boucler indéfiniment, lisant les IDs de tâche depuis le canal `taches`.
    *   Pour chaque tâche reçue, il doit simuler un travail (comme dans l'Exercice 1) et envoyer un message de résultat au canal `resultats`.
    *   La boucle doit se terminer lorsque le canal `taches` est fermé. N'oubliez pas d'appeler `wg.Done()` à la fin de la fonction `travailleur`.

2.  **Modifiez votre fonction `main()`** :
    *   Déclarez un `sync.WaitGroup`.
    *   Créez deux canaux : `taches chan int` et `resultats chan string`.
    *   Lancez un nombre fixe de goroutines "travailleurs" (par exemple, 3 travailleurs). Chaque travailleur doit être ajouté au `WaitGroup`.
    *   Dans une goroutine séparée (ou après avoir lancé les travailleurs), envoyez un certain nombre de tâches (par exemple, 10 tâches avec des IDs de 1 à 10) au canal `taches`.
    *   **Important :** Après avoir envoyé toutes les tâches, fermez le canal `taches` pour signaler aux travailleurs qu'il n'y aura plus de nouvelles tâches.
    *   Attendez que tous les travailleurs aient terminé avec `wg.Wait()`.
    *   Fermez le canal `resultats`.
    *   Lisez et affichez tous les résultats du canal `resultats` (comme dans l'Exercice 3).

3.  **Exécutez votre programme.**
    *   **Question :** Observez l'ordre dans lequel les tâches sont traitées et les résultats sont affichés. Comment le nombre de travailleurs affecte-t-il le temps total d'exécution ?

---

### Rendu

Le rendu attendu est un ou plusieurs fichiers `.go` contenant le code source de votre solution pour chaque exercice. Assurez-vous que le code est fonctionnel et respecte les consignes.

### Critères d'Évaluation

*   **Fonctionnalité :** Le programme compile et s'exécute correctement, produisant les sorties attendues pour chaque exercice.
*   **Compréhension :** Les mécanismes de goroutines, `WaitGroup` et canaux sont utilisés de manière appropriée.
*   **Clarté du code :** Le code est lisible, bien structuré et commenté si nécessaire.
*   **Réponses aux questions :** Les explications fournies pour chaque question sont pertinentes et démontrent une bonne compréhension des concepts.

Amusez-vous bien avec la concurrence en Go !