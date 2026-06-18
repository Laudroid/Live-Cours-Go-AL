Bonjour à toutes et à tous !

Ce TP est conçu pour vous permettre de mettre en pratique des patterns de concurrence fondamentaux en Go. L'objectif est de comprendre comment distribuer des tâches et collecter leurs résultats de manière efficace et robuste.

---

### **TP : Maîtrise des Patterns de Concurrence en Go - Worker Pool et Fan-out/Fan-in**

**Objectif du TP :**
Appliquer et comprendre les patterns de concurrence *Worker Pool* et *Fan-out/Fan-in* en Go pour optimiser l'exécution de tâches intensives.

**Contexte :**
Lorsque des applications doivent traiter un grand volume de tâches indépendantes et potentiellement coûteuses en ressources CPU, la concurrence devient essentielle. Les patterns *Worker Pool* et *Fan-out/Fan-in* sont des approches classiques pour gérer cette complexité, permettant de limiter le nombre de goroutines actives simultanément (Worker Pool) tout en distribuant efficacement le travail et en consolidant les résultats (Fan-out/Fan-in).

**Scénario : Calcul de la Somme des Diviseurs d'un Nombre**

Nous allons simuler une charge de travail en calculant la somme des diviseurs de plusieurs nombres entiers. Par exemple, pour le nombre 6, les diviseurs sont 1, 2, 3, 6, et leur somme est 1 + 2 + 3 + 6 = 12. Cette opération, bien que simple, peut devenir coûteuse si elle est répétée pour un grand nombre d'entiers, surtout si ces entiers sont grands.

Votre tâche sera d'implémenter un système qui :
1.  Génère une série de nombres à traiter.
2.  Distribue ces nombres à un ensemble fixe de "workers" (goroutines).
3.  Chaque worker calcule la somme des diviseurs pour les nombres qu'il reçoit.
4.  Les résultats de tous les workers sont collectés et affichés.

**Éléments Clés à Implémenter :**

*   **`goroutine`** : Pour exécuter des fonctions de manière concurrente.
*   **`channel`** : Pour la communication sécurisée entre goroutines (canaux de tâches et de résultats).
*   **`sync.WaitGroup`** : Pour synchroniser la fin de toutes les goroutines.
*   **`close`** : Pour signaler la fin de l'envoi de données sur un canal.

---

**Instructions Détaillées :**

**1. La Fonction de Calcul (Tâche Individuelle)**

Commencez par implémenter la fonction qui représente la tâche coûteuse.

```go
// sumDivisors calcule la somme de tous les diviseurs d'un nombre n.
// Par exemple, pour n=6, les diviseurs sont 1, 2, 3, 6, et la somme est 12.
func sumDivisors(n int) int {
    sum := 0
    for i := 1; i <= n; i++ {
        if n%i == 0 {
            sum += i
        }
    }
    return sum
}
```
*Testez cette fonction avec quelques valeurs pour vous assurer qu'elle fonctionne correctement.*

**2. Phase 1 : Génération des Tâches (Source Fan-out)**

Créez une goroutine qui sera responsable de générer les nombres à traiter et de les envoyer sur un canal. C'est la source de votre "fan-out".

*   Définissez un canal `jobs` de type `chan int`.
*   Créez une fonction `generateNumbers(numJobs int, jobs chan<- int)` qui prend en paramètre le nombre de tâches à générer et le canal `jobs` (en écriture seule).
*   Dans cette fonction, bouclez de `1` à `numJobs` (ou à une valeur plus grande pour des calculs plus longs, par exemple `numJobs * 1000`) et envoyez chaque nombre sur le canal `jobs`.
*   **Très important :** Une fois tous les nombres envoyés, fermez le canal `jobs` avec `close(jobs)`. Cela signalera aux workers qu'il n'y aura plus de nouvelles tâches.

**3. Phase 2 : Implémentation du Worker Pool**

Créez la logique de vos workers. Un worker est une goroutine qui lit des tâches depuis le canal `jobs`, les traite, puis envoie le résultat sur un canal `results`.

*   Définissez un canal `results` de type `chan struct { number int; sum int }` (ou une struct similaire pour stocker le nombre original et sa somme).
*   Créez une fonction `worker(id int, jobs <-chan int, results chan<- struct { number int; sum int }, wg *sync.WaitGroup)` qui prend en paramètre :
    *   Un `id` pour identifier le worker (utile pour le débogage).
    *   Le canal `jobs` (en lecture seule).
    *   Le canal `results` (en écriture seule).
    *   Un pointeur vers un `sync.WaitGroup`.
*   Dans la fonction `worker` :
    *   Utilisez `defer wg.Done()` pour signaler à la `WaitGroup` que ce worker a terminé son travail, juste avant que la goroutine ne se termine.
    *   Bouclez `for job := range jobs`. Cette boucle se terminera automatiquement lorsque le canal `jobs` sera fermé et vidé.
    *   Pour chaque `job` reçu, appelez `sumDivisors(job)`.
    *   Envoyez le résultat (le nombre original et sa somme) sur le canal `results`.
    *   (Optionnel) Affichez un message pour voir quel worker traite quelle tâche.

**4. Phase 3 : Orchestration (Fan-out/Fan-in)**

Dans votre fonction `main`, mettez tout en place :

*   Définissez le nombre de workers (`numWorkers`, ex: 4) et le nombre de tâches (`numJobs`, ex: 100).
*   Créez les canaux `jobs` et `results` avec une taille de buffer appropriée (ou non bufferisés pour commencer).
*   Initialisez un `sync.WaitGroup`.
*   **Fan-out :**
    *   Lancez la goroutine `generateNumbers`.
    *   Lancez `numWorkers` goroutines `worker`. Pour chaque worker, n'oubliez pas d'appeler `wg.Add(1)` *avant* de lancer la goroutine.
*   **Fan-in :**
    *   Lancez une *nouvelle goroutine* qui attendra que tous les workers aient terminé (`wg.Wait()`) et qui, une fois que c'est le cas, fermera le canal `results` (`close(results)`). C'est crucial pour que la boucle de collecte des résultats puisse se terminer.
    *   Dans la fonction `main`, bouclez `for result := range results` pour collecter et afficher tous les résultats.

**5. Phase 4 : Mesure de Performance (Optionnel mais recommandé)**

*   Utilisez le package `time` pour mesurer le temps d'exécution total de votre programme.
    ```go
    startTime := time.Now()
    // ... votre code de concurrence ...
    duration := time.Since(startTime)
    fmt.Printf("Temps d'exécution total : %s\n", duration)
    ```
*   Expérimentez avec différents nombres de workers (`numWorkers`) et de tâches (`numJobs`). Observez comment le temps d'exécution change.

---

**Consignes Spécifiques :**

*   **Clarté du code :** Votre code doit être lisible, bien structuré et commenté si nécessaire.
*   **Gestion des erreurs :** Pour ce TP, une gestion d'erreurs minimale est suffisante, l'accent est mis sur la concurrence.
*   **Utilisation de l'IA :** L'utilisation d'outils d'IA pour vous aider à comprendre les concepts, à débloquer des points ou à générer des extraits de code est tout à fait encouragée. L'objectif est d'apprendre et de comprendre, pas de réinventer la roue. Assurez-vous simplement de comprendre le code que vous utilisez et de pouvoir l'expliquer.
*   **Expérimentation :** N'hésitez pas à modifier les paramètres (`numWorkers`, `numJobs`) pour observer l'impact sur la performance et la gestion des ressources.

Amusez-vous bien à explorer la puissance de la concurrence en Go !