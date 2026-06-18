Voici une solution concise et commentée pour le TP "Gestion de l'Annulation et du Timeout avec `context` en Go".

---

### Solution TP : Gestion de l'Annulation et du Timeout avec `context` en Go

Pour commencer, créez un nouveau répertoire pour votre projet et initialisez un module Go :


```bash
mkdir go_context_tp
cd go_context_tp
go mod init go_context_tp
```


Ensuite, créez un fichier `main.go` dans ce répertoire. Tout le code des exercices sera placé dans ce fichier.

#### `main.go`


```go
package main

import (
	"context" // Le package context pour la gestion de l'annulation et des délais
	"fmt"
	"time" // Pour simuler des délais
)

// effectuerOperationLongue simule une tâche qui prend du temps et peut être annulée via le contexte.
func effectuerOperationLongue(ctx context.Context, id string) error {
	fmt.Printf("[%s] Début de l'opération...\n", id)

	for i := 1; i <= 5; i++ { // Simule 5 étapes de travail
		select {
		case <-ctx.Done(): // Écoute le canal d'annulation du contexte
			// Si ctx.Done() reçoit un signal, cela signifie que le contexte a été annulé.
			fmt.Printf("[%s] Opération annulée : %v\n", id, ctx.Err())
			return ctx.Err() // Retourne l'erreur d'annulation (context.Canceled ou context.DeadlineExceeded)
		case <-time.After(500 * time.Millisecond): // Simule le travail de l'étape
			// Cette clause est exécutée si le délai de 500ms s'écoule avant l'annulation du contexte.
			fmt.Printf("[%s] Traitement étape %d...\n", id, i)
		}
	}

	fmt.Printf("[%s] Opération terminée avec succès.\n", id)
	return nil // Retourne nil si l'opération se termine sans annulation
}

func main() {
	fmt.Println("Démarrage du programme principal.")

	// Partie 2 : Le Programme Principal (main)

	// Crée un contexte avec un timeout de 2 secondes.
	// L'opération longue prendrait 5 * 500ms = 2.5 secondes, donc le timeout devrait se déclencher.
	ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
	// defer cancel() est crucial pour libérer les ressources associées au contexte
	// une fois que la fonction main se termine, que ce soit normalement ou via un return anticipé.
	defer cancel()

	// resultChan est un canal pour que la goroutine effectuerOperationLongue puisse
	// communiquer son résultat (une erreur ou nil) à la fonction main.
	// Le buffer de 1 assure que l'envoi ne bloque pas si main n'est pas prêt immédiatement.
	resultChan := make(chan error, 1)

	// Lance effectuerOperationLongue dans une goroutine pour qu'elle s'exécute concurremment.
	go func() {
		err := effectuerOperationLongue(ctx, "Ma Tâche")
		resultChan <- err // Envoie le résultat de l'opération longue au canal
	}()

	// Utilise select pour attendre soit la fin de l'opération longue, soit l'expiration du timeout.
	select {
	case err := <-resultChan: // Reçoit le résultat de la goroutine effectuerOperationLongue
		if err != nil {
			fmt.Printf("Main: L'opération s'est terminée avec une erreur : %v\n", err)
		} else {
			fmt.Println("Main: L'opération s'est terminée avec succès avant le timeout.")
		}
	case <-ctx.Done(): // Le canal ctx.Done() est fermé lorsque le contexte est annulé ou le délai expiré
		// Cela signifie que le timeout a été atteint ou qu'une annulation explicite a eu lieu.
		fmt.Printf("Main: Timeout atteint ou annulation : %v\n", ctx.Err())
	}

	fmt.Println("Fin du programme principal.")

	// Pour tester l'opération se terminant avec succès avant le timeout:
	// Décommentez le bloc ci-dessous et commentez le bloc principal ci-dessus.
	/*
		fmt.Println("\n--- Test avec timeout plus long (opération réussie) ---")
		ctxSuccess, cancelSuccess := context.WithTimeout(context.Background(), 3*time.Second) // 3s > 2.5s
		defer cancelSuccess()

		resultChanSuccess := make(chan error, 1)
		go func() {
			err := effectuerOperationLongue(ctxSuccess, "Tâche Réussie")
			resultChanSuccess <- err
		}()

		select {
		case err := <-resultChanSuccess:
			if err != nil {
				fmt.Printf("Main: L'opération 'Tâche Réussie' s'est terminée avec une erreur : %v\n", err)
			} else {
				fmt.Println("Main: L'opération 'Tâche Réussie' s'est terminée avec succès avant le timeout.")
			}
		case <-ctxSuccess.Done():
			fmt.Printf("Main: Timeout atteint ou annulation pour 'Tâche Réussie' : %v\n", ctxSuccess.Err())
		}
		fmt.Println("Fin du test de succès.")
	*/
}

```


#### Exécution

Pour exécuter ce code, naviguez dans votre terminal jusqu'au répertoire `go_context_tp` et lancez :


```bash
go run main.go
```


Vous devriez observer que l'opération est annulée par le timeout de 2 secondes avant qu'elle ne puisse terminer ses 5 étapes (qui prendraient 2.5 secondes).

Si vous décommentez le bloc de test "Test avec timeout plus long" et commentez le bloc principal, vous verrez l'opération se terminer avec succès.

#### Explications Clés

1.  **`context.Context` :**
    *   C'est une interface qui transporte des délais, des signaux d'annulation et d'autres valeurs à travers les limites des API et les goroutines.
    *   Le canal `ctx.Done()` est un signal : il est fermé lorsque le contexte est annulé ou que son délai est dépassé.

2.  **`context.WithTimeout(parent Context, timeout time.Duration)` :**
    *   Crée un nouveau contexte enfant qui est automatiquement annulé après la `timeout` spécifiée.
    *   Il retourne le nouveau `Context` et une fonction `cancel()`.

3.  **`cancel()` et `defer cancel()` :**
    *   La fonction `cancel()` est utilisée pour annuler le contexte manuellement.
    *   Il est **crucial** d'appeler `defer cancel()` juste après la création du contexte. Cela garantit que les ressources associées au contexte sont libérées, même si la fonction se termine prématurément. Ne pas le faire peut entraîner des fuites de goroutines et de ressources.

4.  **`select` avec `ctx.Done()` et `time.After()` dans la goroutine :**
    *   Dans `effectuerOperationLongue`, le `select` est utilisé pour écouter deux événements concurrents :
        *   `<-ctx.Done()` : Le signal d'annulation du contexte. Si ce `case` est prêt, l'opération s'arrête.
        *   `<-time.After(...)` : Un délai pour simuler le travail. Si ce `case` est prêt, l'opération continue à l'étape suivante.
    *   Cette structure permet à la goroutine de réagir de manière réactive à l'annulation sans bloquer indéfiniment sur `time.Sleep`.

5.  **Communication avec `resultChan` :**
    *   La goroutine `effectuerOperationLongue` envoie son résultat (succès ou erreur) sur `resultChan`.
    *   La fonction `main` utilise un `select` pour attendre soit ce résultat, soit l'expiration du contexte (`<-ctx.Done()`). Cela permet à `main` de réagir à l'événement qui se produit en premier.

#### Pistes de Réflexion

1.  **Que se passerait-il si la fonction `effectuerOperationLongue` ne vérifiait pas `ctx.Done()` ?**
    *   Si `effectuerOperationLongue` ne vérifiait pas `ctx.Done()`, elle continuerait à s'exécuter jusqu'à sa fin naturelle (les 2.5 secondes). Même si le contexte est annulé par le timeout de `main`, la goroutine `effectuerOperationLongue` ne serait pas au courant et ne s'arrêterait pas. Cela entraînerait une **fuite de goroutine** : la goroutine continuerait à consommer des ressources CPU et mémoire inutilement après que `main` ait décidé de l'annuler.

2.  **Expérimentez avec `context.WithCancel` et `context.WithDeadline` :**
    *   **`context.WithCancel` :** Permet une annulation manuelle. Au lieu d'un timeout, vous appelleriez `cancel()` explicitement à un moment donné (par exemple, après un événement utilisateur ou une autre condition).
        
```go
        // Dans main:
        ctx, cancel := context.WithCancel(context.Background())
        defer cancel()
        // ...
        // Dans une autre goroutine ou après un certain temps:
        // cancel() // Déclenche l'annulation
        ```

    *   **`context.WithDeadline` :** Similaire à `WithTimeout`, mais spécifie un point dans le temps absolu (`time.Time`) après lequel le contexte est annulé, plutôt qu'une durée relative.
        
```go
        // Dans main:
        deadline := time.Now().Add(2 * time.Second)
        ctx, cancel := context.WithDeadline(context.Background(), deadline)
        defer cancel()
        // ...
        ```


3.  **Comment propager des valeurs via le contexte avec `context.WithValue` ?**
    *   `context.WithValue(parent Context, key, val interface{})` permet d'associer une paire clé-valeur au contexte.
    *   Ces valeurs sont immuables et peuvent être récupérées par n'importe quelle goroutine qui a accès à ce contexte ou à un de ses enfants.
    *   Exemple :
        
```go
        type contextKey string // Définir un type pour la clé pour éviter les collisions
        const transactionIDKey contextKey = "transactionID"

        // Dans main:
        ctxWithVal := context.WithValue(context.Background(), transactionIDKey, "TXN-12345")

        // Dans effectuerOperationLongue:
        if txID := ctx.Value(transactionIDKey); txID != nil {
            fmt.Printf("[%s] ID de transaction: %s\n", id, txID)
        }
        ```

    *   C'est utile pour propager des informations de requête (ID de trace, informations d'authentification) à travers une chaîne d'appels de fonctions et de goroutines.

Ce TP a mis en lumière l'importance du package `context` pour la gestion des opérations concurrentes en Go, en particulier pour la gestion des annulations et des délais, ce qui est fondamental pour écrire des applications robustes et sans fuites de ressources.