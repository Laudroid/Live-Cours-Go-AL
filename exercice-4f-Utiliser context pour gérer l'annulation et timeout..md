Ce TP vise à vous familiariser avec l'utilisation du package `context` en Go pour gérer l'annulation et les délais d'exécution (timeouts) dans des opérations concurrentes.

---

### TP : Gestion de l'Annulation et du Timeout avec `context` en Go

**Objectif du TP :** Utiliser le package `context` pour gérer l'annulation et les timeouts.

**Contexte :**
Dans les applications Go modernes, la gestion des opérations concurrentes et des ressources est essentielle. Le package `context` fournit un moyen standardisé de propager des signaux d'annulation, des délais (deadlines) et d'autres valeurs à travers les limites des API et les goroutines. Il est particulièrement utile pour éviter les fuites de goroutines et gérer proprement la fin d'opérations potentiellement longues.

**Prérequis :**
*   Installation de Go (version 1.16 ou supérieure recommandée).
*   Connaissances de base des goroutines et des channels.

**Énoncé de l'Exercice :**
Créez un programme Go qui simule une opération longue et qui est capable de l'annuler si un délai imparti (timeout) est dépassé. L'opération longue doit pouvoir réagir à l'annulation du contexte.

---

**Instructions Détaillées :**

Votre programme devra être composé de deux parties principales : une fonction simulant une opération longue et la fonction `main` qui orchestre son exécution avec un timeout.

**Partie 1 : La Fonction d'Opération Longue**

1.  Créez une fonction nommée `effectuerOperationLongue` qui prend en paramètre un `context.Context` et un identifiant de chaîne de caractères (par exemple, "Tâche 1").
2.  Cette fonction doit simuler un travail en plusieurs étapes. Pour chaque étape, utilisez `time.Sleep` pour introduire un délai (par exemple, 500 millisecondes par étape).
3.  **Crucial :** À chaque étape (ou après chaque `time.Sleep`), la fonction doit vérifier si le contexte a été annulé. Pour cela, utilisez une instruction `select` qui écoute sur le canal `ctx.Done()`.
    *   Si `ctx.Done()` reçoit un signal, cela signifie que le contexte a été annulé (par un timeout ou une annulation explicite). La fonction doit alors afficher un message indiquant que l'opération a été annulée et retourner une erreur (par exemple, `ctx.Err()`).
    *   Si le contexte n'est pas annulé, l'opération continue normalement.
4.  Si l'opération se termine sans être annulée, elle doit afficher un message de succès et retourner `nil` pour l'erreur.

**Exemple de structure pour `effectuerOperationLongue` :**

```go
func effectuerOperationLongue(ctx context.Context, id string) error {
    fmt.Printf("[%s] Début de l'opération...\n", id)
    for i := 1; i <= 5; i++ { // Simule 5 étapes
        select {
        case <-ctx.Done():
            fmt.Printf("[%s] Opération annulée : %v\n", id, ctx.Err())
            return ctx.Err()
        case <-time.After(500 * time.Millisecond): // Simule le travail de l'étape
            fmt.Printf("[%s] Traitement étape %d...\n", id, i)
        }
    }
    fmt.Printf("[%s] Opération terminée avec succès.\n", id)
    return nil
}
```

**Partie 2 : Le Programme Principal (`main`)**

1.  Dans la fonction `main`, créez un contexte avec un timeout en utilisant `context.WithTimeout`. Choisissez un délai court (par exemple, 2 secondes) pour vous assurer que le timeout se déclenche avant la fin naturelle de l'opération longue (qui prendrait 5 * 500ms = 2.5 secondes).
2.  N'oubliez pas d'appeler `cancel()` via un `defer` pour libérer les ressources associées au contexte.
3.  Lancez `effectuerOperationLongue` dans une goroutine, en lui passant le contexte créé.
4.  Utilisez un `select` dans la fonction `main` pour attendre soit la fin du contexte (c'est-à-dire le timeout), soit la fin de l'opération longue (si elle se termine avant le timeout).
    *   Pour attendre la fin de l'opération longue, vous devrez utiliser un channel pour que la goroutine puisse communiquer son résultat à `main`.
    *   Si `ctx.Done()` est déclenché, affichez un message indiquant que le timeout a été atteint.
    *   Si l'opération longue se termine avant le timeout, affichez son résultat.

**Exemple de structure pour `main` :**

```go
func main() {
    fmt.Println("Démarrage du programme principal.")

    // Crée un contexte avec un timeout de 2 secondes
    ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
    defer cancel() // Très important pour libérer les ressources du contexte

    resultChan := make(chan error, 1) // Canal pour recevoir le résultat de l'opération longue

    go func() {
        err := effectuerOperationLongue(ctx, "Ma Tâche")
        resultChan <- err
    }()

    select {
    case err := <-resultChan:
        if err != nil {
            fmt.Printf("Main: L'opération s'est terminée avec une erreur : %v\n", err)
        } else {
            fmt.Println("Main: L'opération s'est terminée avec succès avant le timeout.")
        }
    case <-ctx.Done():
        fmt.Printf("Main: Timeout atteint ou annulation : %v\n", ctx.Err())
    }

    fmt.Println("Fin du programme principal.")
}
```

**Critères de Réussite :**

*   Le code compile et s'exécute sans erreur.
*   La fonction `effectuerOperationLongue` prend bien un `context.Context` en paramètre.
*   `effectuerOperationLongue` vérifie `ctx.Done()` et s'arrête proprement si le contexte est annulé.
*   Le programme principal utilise `context.WithTimeout` et gère le `defer cancel()`.
*   Le programme principal observe le timeout et affiche le message approprié lorsque l'opération est annulée par le timeout.
*   (Optionnel) Testez avec un timeout plus long (par exemple, 3 secondes) pour voir l'opération se terminer avec succès.

---

**Pistes de Réflexion (pour aller plus loin) :**

*   Que se passerait-il si la fonction `effectuerOperationLongue` ne vérifiait pas `ctx.Done()` ? (Indice : fuite de goroutine).
*   Expérimentez avec `context.WithCancel` pour une annulation manuelle, et `context.WithDeadline` pour un délai absolu.
*   Comment pourriez-vous propager des valeurs (comme un ID de transaction) via le contexte en utilisant `context.WithValue` ?
*   Si vous utilisez un assistant IA pour vous aider, assurez-vous de bien comprendre chaque ligne de code générée. Posez-lui des questions sur les choix faits (par exemple, pourquoi utiliser un `select` avec `ctx.Done()` et `time.After` dans la goroutine, ou pourquoi le `defer cancel()` est important). L'objectif est d'apprendre, pas seulement de copier-coller.

---