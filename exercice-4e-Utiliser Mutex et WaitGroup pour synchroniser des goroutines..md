Bonjour !

Ce TP est conçu pour vous guider à travers les mécanismes fondamentaux de synchronisation en Go : `sync.Mutex` et `sync.WaitGroup`. Ces outils sont essentiels pour écrire des programmes concurrents robustes et sans erreurs.

---

## TP : Synchronisation de Goroutines avec Mutex et WaitGroup

### Contexte

La concurrence est une force puissante en Go, permettant d'exécuter plusieurs tâches simultanément via les goroutines. Cependant, lorsque plusieurs goroutines accèdent et modifient les mêmes données (ressources partagées), des problèmes de cohérence peuvent survenir, connus sous le nom de "conditions de concurrence" (race conditions). Pour éviter ces situations et garantir l'intégrité des données, des mécanismes de synchronisation sont nécessaires. De plus, il est souvent utile de savoir quand un ensemble de goroutines a terminé son travail.

### Objectifs Pédagogiques

*   Comprendre et identifier les conditions de concurrence.
*   Utiliser `sync.Mutex` pour protéger l'accès aux ressources partagées.
*   Utiliser `sync.WaitGroup` pour attendre la fin d'un groupe de goroutines.
*   Appliquer ces concepts pour écrire du code concurrent sûr et prévisible.

### Prérequis

*   Connaissance des bases du langage Go (variables, fonctions, boucles, structures).
*   Compréhension du concept de goroutine et de canal (channel) de base.

---

### Exercice : Gestion d'un Compteur Concurrent

**Mise en Situation :**

Imaginez un système où plusieurs "travailleurs" (goroutines) doivent mettre à jour un compteur global, par exemple, le nombre total de requêtes traitées, le nombre d'articles vendus, ou des points dans un jeu. Chaque travailleur effectue un certain nombre d'opérations d'incrémentation sur ce compteur.

**Problématique :**

Si plusieurs goroutines tentent d'incrémenter un même compteur simultanément sans protection, le résultat final sera souvent incorrect. C'est une condition de concurrence classique. De plus, pour vérifier le résultat final, nous devons nous assurer que *tous* les travailleurs ont terminé leurs opérations.

---

### Tâches à Réaliser

#### Étape 1 : Le Compteur Non-Synchronisé (Démonstration du Problème)

1.  **Initialisation :**
    *   Déclarez une variable globale `compteur` de type `int` initialisée à 0.
    *   Définissez une constante `nbGoroutines` (par exemple, 100) et une constante `incrementsParGoroutine` (par exemple, 1000).

2.  **Fonction d'Incrémentation :**
    *   Créez une fonction `incrementerCompteurNonSynchro()` qui prend un `*sync.WaitGroup` en argument.
    *   Dans cette fonction, utilisez une boucle pour incrémenter la variable `compteur` `incrementsParGoroutine` fois.
    *   N'oubliez pas d'appeler `defer wg.Done()` à la fin de cette fonction.

3.  **Lancement des Goroutines :**
    *   Dans votre fonction `main`, créez une instance de `sync.WaitGroup`.
    *   Utilisez une boucle pour lancer `nbGoroutines` instances de `incrementerCompteurNonSynchro()` comme goroutines.
    *   Pour chaque goroutine lancée, appelez `wg.Add(1)`.

4.  **Attente et Vérification :**
    *   Après avoir lancé toutes les goroutines, appelez `wg.Wait()` pour attendre leur achèvement.
    *   Affichez la valeur finale de `compteur`.

5.  **Observation :**
    *   Exécutez le programme plusieurs fois. Que constatez-vous ? Le résultat est-il toujours le même ? Est-il égal à `nbGoroutines * incrementsParGoroutine` ? Expliquez pourquoi.

#### Étape 2 : Synchronisation avec Mutex

Maintenant, nous allons corriger la condition de concurrence en utilisant `sync.Mutex`.

1.  **Ajout du Mutex :**
    *   Déclarez une variable globale `mu` de type `sync.Mutex`.

2.  **Modification de la Fonction d'Incrémentation :**
    *   Créez une nouvelle fonction `incrementerCompteurSynchro()` qui prend également un `*sync.WaitGroup` en argument.
    *   À l'intérieur de la boucle d'incrémentation, avant d'accéder à `compteur`, appelez `mu.Lock()`.
    *   Après avoir incrémenté `compteur`, appelez `mu.Unlock()`.
    *   **Bonne pratique :** Utilisez `defer mu.Unlock()` juste après `mu.Lock()` pour vous assurer que le mutex est toujours déverrouillé, même en cas d'erreur ou de `panic`.

3.  **Lancement et Vérification :**
    *   Dans votre fonction `main`, commentez ou supprimez le code de l'Étape 1.
    *   Réinitialisez `compteur` à 0.
    *   Répétez les étapes de lancement des goroutines et d'attente avec `wg.Wait()`, mais cette fois en utilisant `incrementerCompteurSynchro()`.
    *   Affichez la valeur finale de `compteur`.

4.  **Observation :**
    *   Exécutez le programme plusieurs fois. Le résultat est-il maintenant correct et cohérent ? Expliquez pourquoi l'utilisation du mutex résout le problème.

#### Étape 3 : Réflexion et Amélioration (Optionnel)

1.  **Performance :**
    *   Comment l'utilisation du mutex affecte-t-elle les performances par rapport à la version non synchronisée ? (Pensez en termes de temps d'exécution).
    *   Dans quel cas l'impact serait-il plus ou moins significatif ?

2.  **Alternatives :**
    *   Pour une simple incrémentation atomique (qui ne nécessite pas de logique complexe à l'intérieur de la section critique), Go offre le package `sync/atomic`. Recherchez comment `atomic.AddInt64` pourrait être utilisé pour résoudre ce problème et comparez-le à `sync.Mutex`. Quels sont les avantages et inconvénients de chaque approche ?

3.  **Gestion de multiples ressources :**
    *   Si vous aviez deux compteurs distincts (`compteurA` et `compteurB`) que différentes goroutines devaient incrémenter, utiliseriez-vous un seul mutex pour les deux, ou un mutex par compteur ? Justifiez votre réponse.

---

### Conseils et Bonnes Pratiques

*   **`defer mu.Unlock()` :** C'est une pratique idiomatique et sûre en Go. Elle garantit que le mutex est libéré à la fin de la fonction, même si celle-ci se termine prématurément.
*   **Minimiser la section critique :** Verrouillez le mutex uniquement pendant le temps strictement nécessaire pour accéder ou modifier la ressource partagée. Plus la section critique est courte, moins les goroutines attendent.
*   **`WaitGroup` :**
    *   `wg.Add(n)` : Incrémente le compteur du `WaitGroup` de `n`. Appelez-le *avant* de lancer les goroutines.
    *   `wg.Done()` : Décrémente le compteur du `WaitGroup`. Appelez-le à la fin de chaque goroutine (souvent avec `defer`).
    *   `wg.Wait()` : Bloque l'exécution jusqu'à ce que le compteur du `WaitGroup` atteigne zéro.
*   **Utilisation de l'IA :** L'utilisation d'outils d'IA pour vous aider à rédiger le code ou à comprendre les concepts est encouragée. Cependant, assurez-vous de bien comprendre *pourquoi* la solution fonctionne et quels sont les principes sous-jacents. N'hésitez pas à poser des questions à l'IA pour explorer différentes approches ou pour clarifier des points. L'objectif est votre apprentissage et votre compréhension.

---

Bon courage pour ce TP ! La maîtrise de la synchronisation est une étape clé pour devenir un développeur Go compétent.