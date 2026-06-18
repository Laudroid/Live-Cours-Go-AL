Voici une solution concise et commentée pour le TP "Gestion d'Événements Multiples avec `select`".

---

### Solution TP : Gestion d'Événements Multiples avec `select`

Pour commencer, créez un nouveau répertoire pour votre projet et initialisez un module Go :


```bash
mkdir go_select_events
cd go_select_events
go mod init go_select_events
```


Ensuite, créez un fichier `main.go` dans ce répertoire. Tout le code des exercices sera placé dans ce fichier.

#### `main.go`


```go
package main

import (
	"fmt"
	"math/rand" // Pour générer des délais aléatoires
	"time"      // Pour time.Sleep et time.NewTicker
)

// dataProducer envoie des mesures régulières sur dataChan.
// Il écoute aussi sur quitChan pour s'arrêter proprement.
func dataProducer(dataChan chan<- string, quitChan <-chan struct{}) {
	defer fmt.Println("[PRODUCER DATA] Arrêt de la goroutine de production de données.")
	for {
		select {
		case <-quitChan: // Reçoit le signal d'arrêt
			return // Quitte la goroutine
		case <-time.After(time.Duration(rand.Intn(3)+1) * time.Second): // Envoie toutes les 1 à 3 secondes
			dataChan <- fmt.Sprintf("Température: %.1f°C (Humidité: %.2f%%)", 20.0+rand.Float64()*10, 50.0+rand.Float64()*20)
		}
	}
}

// alertProducer envoie des alertes critiques sur alertChan.
// Il écoute aussi sur quitChan pour s'arrêter proprement.
func alertProducer(alertChan chan<- string, quitChan <-chan struct{}) {
	defer fmt.Println("[PRODUCER ALERT] Arrêt de la goroutine de production d'alertes.")
	for {
		select {
		case <-quitChan: // Reçoit le signal d'arrêt
			return // Quitte la goroutine
		case <-time.After(time.Duration(rand.Intn(6)+5) * time.Second): // Envoie toutes les 5 à 10 secondes
			alertChan <- "Niveau critique atteint! Intervention requise!"
		}
	}
}

func main() {
	rand.Seed(time.Now().UnixNano()) // Initialisation du générateur de nombres aléatoires

	fmt.Println("--- Système de Surveillance Démarré ---")

	// Étape 1 : Définition des Channels
	dataChannel := make(chan string)
	alertChannel := make(chan string)
	quitChannel := make(chan struct{}) // Channel pour signaler l'arrêt aux producteurs et au moniteur

	// time.NewTicker crée un Ticker qui envoie des signaux sur son channel .C à intervalles réguliers.
	ticker := time.NewTicker(2 * time.Second) // Pour les vérifications de statut périodiques
	defer ticker.Stop()                       // S'assure que le ticker est arrêté à la fin de main pour libérer les ressources

	// Étape 2 : Goroutines Productrices d'Événements
	go dataProducer(dataChannel, quitChannel)
	go alertProducer(alertChannel, quitChannel)

	// Étape 4 : Déclenchement de l'Arrêt
	// Goroutine pour envoyer le signal d'arrêt après un délai (ici 15 secondes)
	go func() {
		time.Sleep(15 * time.Second)
		fmt.Println("\n[MONITOR] Envoi du signal d'arrêt aux producteurs et au moniteur...")
		// Fermer le canal est une manière idiomatique de signaler la fin.
		// Tous les récepteurs qui écoutent ce canal avec `<-chan` ou `for range`
		// recevront la "zero value" une fois le canal vide et fermé, ou sortiront de la boucle `for range`.
		close(quitChannel)
	}()

	// Étape 3 : La Boucle select du Moniteur
	fmt.Println("[MONITOR] En attente d'événements...")
	for { // Boucle infinie pour écouter les événements continuellement
		select {
		case data := <-dataChannel: // Un message est reçu sur dataChannel
			fmt.Printf("[MESURE] %s\n", data)
		case alert := <-alertChannel: // Un message est reçu sur alertChannel
			fmt.Printf("[ALERTE CRITIQUE] !!! %s !!!\n", alert)
		case <-ticker.C: // Un signal est reçu du ticker (vérification de statut)
			fmt.Println("[STATUS] Vérification système...")
		case <-quitChannel: // Un signal est reçu sur quitChannel
			fmt.Println("[MONITOR] Signal d'arrêt reçu. Arrêt du système.")
			return // Sort de la boucle et de la fonction main, terminant le programme
		}
	}
}

/*
--- Pour Aller Plus Loin (Optionnel) ---

1. Le cas `default` :
   Si vous ajoutez un `default` case à l'instruction `select`, le `select` ne bloquera jamais.
   Si aucun autre `case` n'est prêt à communiquer, le `default` case est exécuté immédiatement.
   Exemple d'utilisation dans la boucle `select`:
   ```
go
   select {
   case data := <-dataChannel:
       // ...
   case <-quitChannel:
       // ...
   default:
       // Ce bloc s'exécute si aucun autre case n'est prêt.
       // Utile pour effectuer des tâches non bloquantes ou sonder les channels sans attendre.
       // Attention: sans un time.Sleep, cela peut consommer 100% du CPU.
       fmt.Println("[MONITOR] Aucune activité, le moniteur est en attente active.")
       time.Sleep(100 * time.Millisecond) // Pour éviter une boucle trop rapide
   }
   
```
   Utilité : Le `default` case est utile lorsque le moniteur a d'autres tâches à effectuer en l'absence de communication sur les channels, sans vouloir bloquer.

2. Priorité des `case` :
   Si plusieurs channels sont prêts à communiquer en même temps (par exemple, `dataChannel` et `alertChannel` reçoivent des messages simultanément), Go ne garantit pas un ordre spécifique d'exécution des `case`. La spécification de Go indique que le `select` choisira un `case` de manière pseudo-aléatoire parmi ceux qui sont prêts. Il n'y a pas de priorité implicite basée sur l'ordre d'écriture des `case` dans le `select`.

3. Fermeture des channels :
   Dans ce TP, la fermeture de `quitChannel` est suffisante pour l'arrêt propre du programme, car elle signale aux producteurs de s'arrêter et au moniteur de sortir de sa boucle.
   Dans des scénarios plus complexes, la fermeture des channels de données (`dataChannel`, `alertChannel`) peut être importante pour signaler aux consommateurs qu'il n'y aura plus de données à traiter.
   - Un producteur est responsable de fermer le channel qu'il gère *une fois qu'il a fini d'y envoyer toutes les données*.
   - Un consommateur peut alors utiliser `for range` sur ce channel pour lire toutes les données restantes et savoir quand le channel est définitivement vide et fermé.
   - Tenter d'envoyer sur un channel fermé provoque une panique. Recevoir d'un channel fermé et vide retourne la "zero value" du type du channel.

4. Utilisation du package `context` :
   Pour une gestion d'annulation plus robuste et hiérarchique dans des applications réelles, le package `context` est la solution idiomatique en Go.
   Au lieu d'un simple `quitChannel`, on utiliserait `context.WithCancel`.
   ```
go
   // Dans main:
   // Crée un contexte qui peut être annulé
   ctx, cancel := context.WithCancel(context.Background())
   defer cancel() // S'assure que le contexte est annulé à la fin de main

   // Les producteurs recevraient le contexte:
   go dataProducerWithContext(ctx, dataChannel)
   go alertProducerWithContext(ctx, alertChannel)

   // La goroutine d'arrêt appellerait cancel():
   go func() {
       time.Sleep(15 * time.Second)
       fmt.Println("\n[MONITOR] Annulation du contexte...")
       cancel() // Annule le contexte, signalant l'arrêt à toutes les goroutines qui l'écoutent
   }()

   // Dans les producteurs (exemple pour dataProducerWithContext):
   func dataProducerWithContext(ctx context.Context, dataChan chan<- string) {
       defer fmt.Println("[PRODUCER DATA] Arrêt par contexte.")
       for {
           select {
           case <-ctx.Done(): // Écoute le signal d'annulation du contexte
               return // Le contexte a été annulé, quitte la goroutine
           case <-time.After(time.Duration(rand.Intn(3)+1) * time.Second):
               dataChan <- fmt.Sprintf("Température: %.1f°C", 20.0+rand.Float64()*10)
           }
       }
   }
   // Le moniteur écouterait aussi <-ctx.Done() dans son select pour s'arrêter.
   
```
   `context` permet de propager les signaux d'annulation à travers une arborescence d'appels de fonctions et de goroutines, ce qui est essentiel pour les applications complexes et la gestion des délais (`context.WithTimeout`) ou des valeurs (`context.WithValue`).
*/
```


#### Exécution

Pour exécuter ce code, naviguez dans votre terminal jusqu'au répertoire `go_select_events` et lancez :


```bash
go run main.go
```


#### Explications Clés

1.  **`select` : Le Cœur du Moniteur**
    *   L'instruction `select` permet à une goroutine d'attendre des opérations sur plusieurs channels.
    *   Elle bloque jusqu'à ce qu'une des opérations de `case` soit prête à s'exécuter.
    *   Si plusieurs `case` sont prêts, `select` en choisit un de manière pseudo-aléatoire.
    *   C'est l'outil idéal pour implémenter des boucles d'événements ou des multiplexeurs en Go.

2.  **Channels (`chan`) : Les Canaux de Communication**
    *   `dataChannel` et `alertChannel` sont utilisés pour la communication unidirectionnelle des producteurs vers le moniteur.
    *   `quitChannel` est utilisé pour envoyer un signal d'arrêt à toutes les goroutines concernées. La fermeture du canal (`close(quitChannel)`) est une manière idiomatique de signaler la fin, car les récepteurs peuvent détecter cette fermeture.

3.  **`time.NewTicker` : Événements Périodiques**
    *   `time.NewTicker(duration)` crée un objet `Ticker` qui envoie des valeurs sur son channel `C` à intervalles réguliers.
    *   `defer ticker.Stop()` est crucial pour libérer les ressources associées au ticker lorsque la fonction `main` se termine.

4.  **`time.After` : Délais Non-Bloquants dans `select`**
    *   `<-time.After(duration)` crée un channel qui enverra une seule valeur après la `duration` spécifiée.
    *   Utilisé dans les producteurs (`dataProducer`, `alertProducer`) pour générer des événements avec des délais aléatoires sans bloquer la goroutine si un signal d'arrêt arrive.

5.  **Arrêt Propre :**
    *   La goroutine `main` sort de sa boucle `for {}` et se termine lorsque `<-quitChannel` est activé.
    *   Les goroutines `dataProducer` et `alertProducer` écoutent également `<-quitChannel` dans leurs propres boucles `select`. Lorsqu'elles reçoivent le signal (via la fermeture du canal), elles retournent, ce qui garantit qu'elles s'arrêtent proprement et ne restent pas bloquées indéfiniment.
    *   `defer fmt.Println(...)` dans les producteurs permet de confirmer leur arrêt.

Ce TP vous a permis de comprendre comment `select` est un outil puissant pour orchestrer des interactions complexes entre plusieurs goroutines, en réagissant de manière flexible et contrôlée à différents types d'événements concurrents. La gestion propre de l'arrêt est une compétence essentielle pour tout programme Go concurrent.