Voici une solution concise et commentée pour le TP "Synchronisation de Goroutines avec Mutex et WaitGroup".

---

### Solution TP : Synchronisation de Goroutines avec Mutex et WaitGroup

Pour commencer, créez un nouveau répertoire pour votre projet et initialisez un module Go :



```bash
mkdir go_mutex_waitgroup_tp
cd go_mutex_waitgroup_tp
go mod init go_mutex_waitgroup_tp
```



Ensuite, créez un fichier `main.go` dans ce répertoire. Tout le code des exercices sera placé dans ce fichier.

#### `main.go`



```go
package main

import (
	"fmt"
	"sync"       // Pour sync.Mutex et sync.WaitGroup
	"sync/atomic" // Pour l'Exercice 3 (Bonus)
	"time"       // Pour mesurer le temps d'exécution
)

// Variables globales pour les compteurs et le mutex
var (
	compteur int
	mu       sync.Mutex // Mutex pour protéger l'accès à 'compteur'
)

// Constantes pour la configuration du test
const (
	nbGoroutines        = 100
	incrementsParGoroutine = 10000 // Augmenté pour mieux observer la race condition
)

// incrementerCompteurNonSynchro incrémente le compteur sans synchronisation.
// C'est pour démontrer la race condition.
func incrementerCompteurNonSynchro(wg *sync.WaitGroup) {
	defer wg.Done() // Décrémente le compteur du WaitGroup à la fin de la goroutine
	for i := 0; i < incrementsParGoroutine; i++ {
		compteur++ // Accès non protégé à la ressource partagée
	}
}

// incrementerCompteurSynchro incrémente le compteur en utilisant un Mutex pour la synchronisation.
func incrementerCompteurSynchro(wg *sync.WaitGroup) {
	defer wg.Done() // Décrémente le compteur du WaitGroup à la fin de la goroutine
	for i := 0; i < incrementsParGoroutine; i++ {
		mu.Lock()   // Verrouille le mutex avant d'accéder à 'compteur'
		compteur++  // Section critique : accès protégé
		mu.Unlock() // Déverrouille le mutex après l'accès
		// Bonne pratique: defer mu.Unlock() juste après mu.Lock() est aussi possible si la section critique est courte
		// et qu'il n'y a pas de code entre Lock et Unlock qui pourrait paniquer avant Unlock.
		// Pour une boucle, Lock/Unlock à chaque itération est plus courant.
	}
}

// incrementerCompteurAtomic incrémente le compteur en utilisant des opérations atomiques.
// Pour l'Exercice 3 (Bonus).
func incrementerCompteurAtomic(wg *sync.WaitGroup, atomicCompteur *int64) {
	defer wg.Done()
	for i := 0; i < incrementsParGoroutine; i++ {
		atomic.AddInt64(atomicCompteur, 1) // Incrémentation atomique
	}
}

func main() {
	fmt.Println("--- TP : Synchronisation de Goroutines avec Mutex et WaitGroup ---")

	// --- Étape 1 : Le Compteur Non-Synchronisé (Démonstration du Problème) ---
	fmt.Println("\n--- Étape 1 : Compteur Non-Synchronisé ---")
	compteur = 0 // Réinitialisation
	var wgNonSynchro sync.WaitGroup

	startTimeNonSynchro := time.Now() // Démarre le chronomètre

	for i := 0; i < nbGoroutines; i++ {
		wgNonSynchro.Add(1) // Incrémente le compteur du WaitGroup
		go incrementerCompteurNonSynchro(&wgNonSynchro)
	}

	wgNonSynchro.Wait() // Attend que toutes les goroutines non synchronisées aient terminé
	durationNonSynchro := time.Since(startTimeNonSynchro)

	fmt.Printf("Valeur finale du compteur (non synchronisé): %d\n", compteur)
	fmt.Printf("Temps d'exécution (non synchronisé): %s\n", durationNonSynchro)
	fmt.Println("Valeur attendue:", nbGoroutines*incrementsParGoroutine)

	// Observation Étape 1:
	// Le résultat final du compteur est presque toujours inférieur à la valeur attendue
	// (nbGoroutines * incrementsParGoroutine) et varie à chaque exécution.
	// Ceci est dû à une "condition de concurrence" (race condition).
	// Lorsque plusieurs goroutines tentent de lire, modifier et écrire la variable `compteur`
	// simultanément, l'opération `compteur++` (qui est en réalité trois opérations: lire, incrémenter, écrire)
	// peut être interrompue. Par exemple, deux goroutines lisent la même valeur de `compteur`,
	// l'incrémentent chacune, puis écrivent toutes les deux leur nouvelle valeur,
	// ce qui fait qu'une incrémentation est perdue.

	// --- Étape 2 : Synchronisation avec Mutex ---
	fmt.Println("\n--- Étape 2 : Compteur Synchronisé avec Mutex ---")
	compteur = 0 // Réinitialisation
	var wgSynchro sync.WaitGroup

	startTimeSynchro := time.Now() // Démarre le chronomètre

	for i := 0; i < nbGoroutines; i++ {
		wgSynchro.Add(1) // Incrémente le compteur du WaitGroup
		go incrementerCompteurSynchro(&wgSynchro)
	}

	wgSynchro.Wait() // Attend que toutes les goroutines synchronisées aient terminé
	durationSynchro := time.Since(startTimeSynchro)

	fmt.Printf("Valeur finale du compteur (synchronisé avec Mutex): %d\n", compteur)
	fmt.Printf("Temps d'exécution (synchronisé avec Mutex): %s\n", durationSynchro)
	fmt.Println("Valeur attendue:", nbGoroutines*incrementsParGoroutine)

	// Observation Étape 2:
	// Le résultat final du compteur est maintenant toujours correct et égal à
	// nbGoroutines * incrementsParGoroutine.
	// L'utilisation de `mu.Lock()` et `mu.Unlock()` crée une "section critique".
	// Seule une goroutine à la fois peut détenir le verrou (`mu.Lock()`).
	// Les autres goroutines qui tentent d'acquérir le verrou sont bloquées jusqu'à ce que
	// le verrou soit libéré (`mu.Unlock()`). Cela garantit que l'opération `compteur++`
	// est atomique et que les incrémentations ne sont pas perdues.

	// --- Étape 3 : Réflexion et Amélioration (Optionnel) ---

	fmt.Println("\n--- Étape 3 : Réflexion et Amélioration ---")

	fmt.Println("1. Performance :")
	fmt.Println("   L'utilisation du mutex introduit une surcharge (overhead) car les goroutines doivent attendre leur tour pour accéder à la section critique. Le temps d'exécution de la version synchronisée est généralement plus long que celui de la version non synchronisée (qui est plus rapide car elle ne fait pas le travail correctement).")
	fmt.Printf("   Comparaison des temps: Non-synchro: %s, Synchro Mutex: %s\n", durationNonSynchro, durationSynchro)
	fmt.Println("   L'impact sur les performances serait plus significatif si la section critique est longue (beaucoup de travail à faire sous le verrou) ou si le nombre de goroutines en concurrence pour le même verrou est très élevé. Pour des sections critiques très courtes ou peu de concurrence, l'impact peut être minime.")

	fmt.Println("\n2. Alternatives (sync/atomic) :")
	fmt.Println("   Pour des opérations atomiques simples comme l'incrémentation d'un entier, le package `sync/atomic` est une alternative plus performante que `sync.Mutex`.")
	var atomicCompteur int64 = 0 // atomic.AddInt64 fonctionne sur des int64
	var wgAtomic sync.WaitGroup

	startTimeAtomic := time.Now()
	for i := 0; i < nbGoroutines; i++ {
		wgAtomic.Add(1)
		go incrementerCompteurAtomic(&wgAtomic, &atomicCompteur)
	}
	wgAtomic.Wait()
	durationAtomic := time.Since(startTimeAtomic)

	fmt.Printf("   Valeur finale du compteur (synchronisé avec atomic.AddInt64): %d\n", atomicCompteur)
	fmt.Printf("   Temps d'exécution (synchronisé avec atomic.AddInt64): %s\n", durationAtomic)
	fmt.Printf("   Comparaison des temps: Synchro Mutex: %s, Synchro Atomic: %s\n", durationSynchro, durationAtomic)
	fmt.Println("   Avantages de `sync/atomic` : Plus rapide que `sync.Mutex` pour des opérations simples car il utilise des instructions CPU de bas niveau qui ne nécessitent pas de basculement de contexte. Moins de contention.")
	fmt.Println("   Inconvénients de `sync/atomic` : Limité à un ensemble spécifique d'opérations (Add, CompareAndSwap, Load, Store pour différents types). Ne peut pas protéger des blocs de code complexes ou des structures de données entières. Plus difficile à utiliser correctement pour des opérations non triviales.")
	fmt.Println("   `sync.Mutex` est plus généraliste et peut protéger n'importe quelle section de code, mais avec une surcharge plus élevée.")

	fmt.Println("\n3. Gestion de multiples ressources :")
	fmt.Println("   Si vous aviez deux compteurs distincts (`compteurA` et `compteurB`), il serait préférable d'utiliser **un mutex par compteur**.")
	fmt.Println("   Justification :")
	fmt.Println("   - **Granularité :** Un mutex par ressource permet une granularité de verrouillage plus fine. Une goroutine qui veut incrémenter `compteurA` ne bloquera pas une autre goroutine qui veut incrémenter `compteurB`. Cela maximise le parallélisme.")
	fmt.Println("   - **Moins de contention :** Si un seul mutex protégeait les deux, toute opération sur `compteurA` ou `compteurB` bloquerait l'accès à l'autre, même si les opérations sont indépendantes. Cela créerait un goulot d'étranglement inutile.")
	fmt.Println("   - **Clarté :** C'est plus clair de voir quel mutex protège quelle ressource.")
	fmt.Println("   Cependant, si les deux compteurs sont toujours modifiés ensemble dans une transaction atomique, ou si leur cohérence mutuelle est critique, un seul mutex pourrait être justifié pour garantir cette cohérence. Mais pour des compteurs indépendants, un mutex par compteur est la meilleure approche.")
}

```



#### Exécution

Pour exécuter ce code, naviguez dans votre terminal jusqu'au répertoire `go_mutex_waitgroup_tp` et lancez :



```bash
go run main.go
```



#### Explications Clés

1.  **Conditions de Concurrence (Race Conditions) :**
    *   L'Étape 1 démontre une condition de concurrence. Lorsque plusieurs goroutines tentent de modifier la même variable (`compteur`) sans protection, l'ordre des opérations de lecture, modification et écriture n'est pas garanti. Cela peut entraîner la perte de mises à jour et un résultat final incorrect et non déterministe.

2.  **`sync.Mutex` (Étape 2) :**
    *   Un `Mutex` (Mutual Exclusion) est un mécanisme de verrouillage.
    *   `mu.Lock()` : Acquiert le verrou. Si le verrou est déjà détenu par une autre goroutine, la goroutine appelante est bloquée jusqu'à ce que le verrou soit libéré.
    *   `mu.Unlock()` : Libère le verrou.
    *   L'utilisation de `mu.Lock()` et `mu.Unlock()` autour de l'accès à `compteur` crée une "section critique". Seule une goroutine à la fois peut exécuter le code dans cette section, garantissant ainsi l'intégrité des données.
    *   Le résultat final est désormais correct et cohérent.

3.  **`sync.WaitGroup` :**
    *   Un `WaitGroup` est utilisé pour attendre qu'un ensemble de goroutines se termine.
    *   `wg.Add(n)` : Incrémente le compteur du `WaitGroup` de `n`. Appelé *avant* de lancer les goroutines.
    *   `defer wg.Done()` : Décrémente le compteur du `WaitGroup`. Placé avec `defer` à la fin de la fonction de la goroutine pour s'assurer qu'il est appelé même en cas de panique.
    *   `wg.Wait()` : Bloque l'exécution de la goroutine appelante (ici `main`) jusqu'à ce que le compteur du `WaitGroup` atteigne zéro. Cela garantit que `main` ne se termine pas prématurément avant que toutes les goroutines n'aient fini leur travail.

4.  **Performance et Alternatives (`sync/atomic`) :**
    *   L'utilisation d'un `Mutex` introduit une surcharge car les goroutines doivent attendre. Pour des opérations très simples comme l'incrémentation d'un entier, `sync/atomic` offre une alternative plus performante.
    *   `atomic.AddInt64(&variable, 1)` effectue une incrémentation de manière atomique (en une seule opération indivisible au niveau du CPU), sans avoir besoin de verrous. C'est plus rapide car cela évite les basculements de contexte et la gestion des files d'attente des verrous.
    *   Cependant, `sync/atomic` est limité à des opérations de bas niveau sur des types spécifiques, tandis que `sync.Mutex` est plus généraliste et peut protéger des blocs de code arbitraires.

5.  **Granularité des Mutex :**
    *   Pour des ressources indépendantes (ex: `compteurA` et `compteurB`), il est préférable d'utiliser un mutex par ressource. Cela permet une granularité de verrouillage plus fine, maximisant le parallélisme car une opération sur `compteurA` ne bloque pas une opération sur `compteurB`.

Ce TP vous a permis de comprendre les dangers des conditions de concurrence et d'appliquer les outils fondamentaux de Go (`sync.Mutex` et `sync.WaitGroup`) pour écrire du code concurrent sûr, fiable et performant.