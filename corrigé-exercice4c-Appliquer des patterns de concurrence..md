
### Solution TP : Maîtrise des Patterns de Concurrence en Go - Worker Pool et Fan-out et Fan-in

Pour commencer, créez un nouveau répertoire pour votre projet et initialisez un module Go :




```bash
mkdir go_concurrency_patterns
cd go_concurrency_patterns
go mod init go_concurrency_patterns
```




Ensuite, créez un fichier `main.go` dans ce répertoire. Tout le code des exercices sera placé dans ce fichier.

#### `main.go`




```go
package main

import (
	"fmt"
	"sync" // Pour sync.WaitGroup
	"time" // Pour mesurer le temps d'exécution
)

// Result struct pour stocker le nombre original et la somme de ses diviseurs.
type Result struct {
	Number int
	Sum    int
}

// sumDivisors calcule la somme de tous les diviseurs d'un nombre n.
// Cette fonction représente la "tâche coûteuse".
func sumDivisors(n int) int {
	sum := 0
	for i := 1; i <= n; i++ {
		if n%i == 0 {
			sum += i
		}
	}
	return sum
}

// generateNumbers est une goroutine qui génère des nombres (tâches) et les envoie sur le canal 'jobs'.
// C'est la source du "fan-out".
func generateNumbers(numJobs int, jobs chan<- int) {
	defer close(jobs) // Très important: ferme le canal une fois toutes les tâches envoyées
	fmt.Printf("Générateur: Envoi de %d tâches...\n", numJobs)
	for i := 1; i <= numJobs; i++ {
		jobs <- i // Envoie le nombre comme une tâche
	}
	fmt.Println("Générateur: Toutes les tâches ont été envoyées.")
}

// worker est une goroutine qui lit les tâches depuis le canal 'jobs', les traite,
// et envoie les résultats sur le canal 'results'.
func worker(id int, jobs <-chan int, results chan<- Result, wg *sync.WaitGroup) {
	defer wg.Done() // Signale à la WaitGroup que ce worker a terminé
	fmt.Printf("Worker %d: Démarré.\n", id)
	for job := range jobs { // Boucle tant que le canal 'jobs' est ouvert et non vide
		fmt.Printf("Worker %d: Traitement de la tâche %d...\n", id, job)
		sum := sumDivisors(job) // Exécute la tâche coûteuse
		results <- Result{Number: job, Sum: sum} // Envoie le résultat
		fmt.Printf("Worker %d: Tâche %d terminée (Somme: %d).\n", id, job, sum)
	}
	fmt.Printf("Worker %d: Arrêté (canal de tâches fermé).\n", id)
}

func main() {
	startTime := time.Now() // Démarre le chronomètre

	fmt.Println("--- TP : Maîtrise des Patterns de Concurrence en Go ---")

	const numWorkers = 4  // Nombre de goroutines "worker"
	const numJobs = 100 // Nombre de tâches à traiter

	// Création des canaux
	// Les canaux bufferisés sont souvent préférables dans les pipelines pour éviter les blocages immédiats
	// et permettre une certaine asynchronie entre les étapes.
	jobs := make(chan int, numJobs)       // Canal pour les tâches (bufferisé pour ne pas bloquer le générateur)
	results := make(chan Result, numJobs) // Canal pour les résultats (bufferisé pour ne pas bloquer les workers)

	var wg sync.WaitGroup // WaitGroup pour synchroniser la fin des workers

	// Phase 1 : Génération des Tâches (Source Fan-out)
	go generateNumbers(numJobs, jobs)

	// Phase 2 : Implémentation du Worker Pool (Fan-out)
	fmt.Printf("\nLancement de %d workers...\n", numWorkers)
	for i := 1; i <= numWorkers; i++ {
		wg.Add(1) // Incrémente le compteur de la WaitGroup pour chaque worker
		go worker(i, jobs, results, &wg)
	}

	// Phase 3 : Orchestration (Fan-in)
	// Lance une goroutine pour fermer le canal 'results' une fois que tous les workers ont terminé.
	// C'est crucial pour que la boucle 'for range results' dans main puisse se terminer.
	go func() {
		wg.Wait()      // Attend que tous les workers aient appelé wg.Done()
		close(results) // Ferme le canal de résultats
		fmt.Println("\nOrchestrateur: Canal de résultats fermé.")
	}()

	// Collecte et affichage des résultats (Fan-in)
	fmt.Println("\nCollecte des résultats:")
	for res := range results { // Boucle tant que le canal 'results' est ouvert et non vide
		fmt.Printf("  Résultat final: Nombre %d, Somme des diviseurs: %d\n", res.Number, res.Sum)
	}
	fmt.Println("Orchestrateur: Tous les résultats ont été collectés.")

	duration := time.Since(startTime) // Arrête le chronomètre
	fmt.Printf("\nTemps d'exécution total : %s\n", duration)

	fmt.Println("\n--- Fin du programme ---")
}

```



#### Exécution

Pour exécuter ce code, naviguez dans votre terminal jusqu'au répertoire `go_concurrency_patterns` et lancez :




```bash
go run main.go
```



#### Explications Clés

1.  **`sumDivisors(n int)` : La Tâche**
    *   Cette fonction simule une opération qui prend du temps. Dans un scénario réel, cela pourrait être une requête réseau, un calcul complexe, une opération de base de données, etc.
    *   C'est la granularité de travail que nous voulons distribuer.

2.  **`generateNumbers(numJobs int, jobs chan<- int)` : Le Générateur (Source Fan-out)**
    *   C'est une goroutine qui produit les "tâches" (ici, des nombres entiers) et les envoie sur le canal `jobs`.
    *   `chan<- int` indique un canal en écriture seule, ce qui est une bonne pratique pour la clarté et la sécurité de type.
    *   `defer close(jobs)` est essentiel. Il garantit que le canal `jobs` sera fermé une fois que toutes les tâches auront été envoyées. Cette fermeture est le signal pour les workers qu'il n'y aura plus de travail à venir, leur permettant de terminer leurs boucles `for range`.

3.  **`worker(id int, jobs <-chan int, results chan<- Result, wg *sync.WaitGroup)` : Le Worker Pool**
    *   Chaque `worker` est une goroutine indépendante.
    *   `<-chan int` indique un canal en lecture seule pour les tâches.
    *   `chan<- Result` indique un canal en écriture seule pour les résultats.
    *   `defer wg.Done()` : Chaque worker décrémente le compteur de la `WaitGroup` lorsqu'il se termine.
    *   `for job := range jobs` : C'est la boucle idiomatique pour qu'un worker lise les tâches d'un canal. La boucle se termine automatiquement lorsque le canal `jobs` est fermé et qu'il n'y a plus de valeurs à lire.
    *   Les workers traitent les tâches de manière concurrente. Le nombre de workers (`numWorkers`) contrôle le degré de parallélisme.

4.  **`main()` : L'Orchestrateur (Fan-out / Fan-in)**
    *   **Fan-out :** Le générateur envoie les tâches sur le canal `jobs`. Plusieurs workers lisent simultanément depuis ce même canal `jobs`. Les tâches sont "distribuées" aux workers disponibles.
    *   **Worker Pool :** Le nombre de goroutines `worker` est fixe (`numWorkers`). Cela permet de contrôler la consommation de ressources et d'éviter de lancer trop de goroutines pour des tâches intensives.
    *   **Fan-in :** Tous les workers envoient leurs résultats sur le même canal `results`. La boucle `for res := range results` dans `main` collecte tous ces résultats.
    *   **Synchronisation avec `sync.WaitGroup` :**
        *   `wg.Add(1)` est appelé pour chaque worker *avant* son lancement.
        *   `wg.Wait()` est appelé dans une goroutine séparée. Cette goroutine attend que tous les workers aient terminé, puis ferme le canal `results`. Cette fermeture est vitale pour que la boucle `for res := range results` dans `main` puisse se terminer.

5.  **Mesure de Performance (`time`) :**
    *   Mesurer le temps d'exécution permet d'évaluer l'efficacité de l'approche concurrente.
    *   En expérimentant avec `numWorkers` et `numJobs`, on peut observer comment le parallélisme affecte le temps total. Généralement, augmenter le nombre de workers jusqu'au nombre de cœurs CPU peut réduire le temps d'exécution pour les tâches liées au CPU. Au-delà, les gains diminuent et peuvent même se transformer en pertes à cause de la surcharge de gestion des goroutines.

Ce TP a démontré comment les patterns *Worker Pool* et *Fan-out/Fan-in*, combinés avec les goroutines, les channels et `sync.WaitGroup`, permettent de construire des systèmes concurrents robustes et performants en Go pour la distribution et la collecte de résultats de tâches.