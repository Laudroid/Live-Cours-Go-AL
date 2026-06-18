Bonjour !

Ce TP est conçu pour vous aider à maîtriser la gestion de la concurrence avec `select` en Go. L'objectif est de simuler un scénario où un programme doit réagir à différents types d'événements provenant de sources distinctes.

---

## TP : Gestion d'Événements Multiples avec `select`

### Objectif du TP
Utiliser l'instruction `select` de Go pour écouter et réagir à des communications sur plusieurs channels simultanément, permettant ainsi une gestion efficace des événements concurrents.

### Contexte
Dans de nombreuses applications concurrentes, un composant central (un "orchestrateur" ou un "moniteur") doit souvent attendre des messages de diverses sources sans savoir laquelle sera la première à envoyer un signal. `select` est l'outil idéal en Go pour ce type de scénario.

### Énoncé Détaillé

Vous allez développer un programme Go qui simule un "Système de Surveillance" simple. Ce système doit être capable de :

1.  **Recevoir des mesures régulières** : Des données de capteurs, par exemple.
2.  **Recevoir des alertes critiques** : Des événements urgents qui nécessitent une réaction immédiate.
3.  **Effectuer des vérifications de statut périodiques** : Un "battement de cœur" du système.
4.  **Recevoir un signal d'arrêt** : Pour terminer le programme de manière propre.

Votre programme principal (la goroutine `main`) agira comme le moniteur, utilisant `select` pour écouter ces différents types d'événements.

#### Instructions

**Étape 1 : Définition des Channels**
Créez les channels suivants :
*   `dataChannel` (type `chan string`) : Pour les mesures régulières.
*   `alertChannel` (type `chan string`) : Pour les alertes critiques.
*   `quitChannel` (type `chan struct{}`) : Pour signaler l'arrêt du système.
*   Utilisez `time.NewTicker` pour créer un channel qui enverra un signal à intervalles réguliers (par exemple, toutes les 2 secondes) pour simuler les vérifications de statut. N'oubliez pas de `defer ticker.Stop()` pour libérer les ressources.

**Étape 2 : Goroutines Productrices d'Événements**
Lancez des goroutines distinctes pour simuler l'envoi de messages sur `dataChannel` et `alertChannel` :
*   **`dataProducer`** : Une goroutine qui envoie une chaîne de caractères (ex: "Température: 25°C") sur `dataChannel` toutes les 1 à 3 secondes de manière aléatoire.
*   **`alertProducer`** : Une goroutine qui envoie une chaîne de caractères (ex: "Niveau critique atteint!") sur `alertChannel` moins fréquemment, par exemple toutes les 5 à 10 secondes de manière aléatoire.
*   Utilisez `time.Sleep` pour simuler les délais entre les envois.

**Étape 3 : La Boucle `select` du Moniteur**
Dans votre fonction `main` :
*   Mettez en place une boucle infinie (`for {}`).
*   À l'intérieur de cette boucle, utilisez l'instruction `select` pour écouter les channels :
    *   Si un message est reçu sur `dataChannel`, affichez "[MESURE] " suivi du message.
    *   Si un message est reçu sur `alertChannel`, affichez "[ALERTE CRITIQUE] " suivi du message (mettez-le en évidence si vous le souhaitez).
    *   Si le `ticker` envoie un signal, affichez "[STATUS] Vérification système...".
    *   Si un signal est reçu sur `quitChannel`, affichez "Signal d'arrêt reçu. Arrêt du système." et **sortez de la boucle** (et donc de la fonction `main`) pour terminer le programme proprement.

**Étape 4 : Déclenchement de l'Arrêt**
Lancez une goroutine supplémentaire qui, après un certain délai (par exemple, 15 secondes), enverra un signal sur `quitChannel` pour initier l'arrêt du système.

### Critères d'Évaluation et Points d'Attention

*   **Clarté du code** : Le code doit être lisible, bien structuré et commenté si nécessaire.
*   **Gestion de la concurrence** : Les goroutines et les channels doivent être utilisés correctement pour simuler les producteurs d'événements.
*   **Arrêt propre** : Le programme doit se terminer de manière contrôlée lorsque le signal `quitChannel` est reçu, sans laisser de goroutines bloquées indéfiniment.
*   **Réactivité** : Le moniteur doit réagir immédiatement à l'événement le plus rapide, démontrant l'efficacité de `select`.
*   **Utilisation de `time.NewTicker`** : Assurez-vous de bien utiliser `time.NewTicker` pour les événements périodiques et de le stopper correctement.

### Pour Aller Plus Loin (Optionnel)

*   **Le cas `default`** : Modifiez votre `select` pour inclure un cas `default`. Observez et expliquez le comportement du programme. Quand est-ce utile ?
*   **Priorité des `case`** : Que se passe-t-il si plusieurs channels sont prêts à communiquer en même temps ? Go garantit-il un ordre ? (Indice : la spécification de Go est votre amie).
*   **Fermeture des channels** : Dans ce TP, la fermeture de `quitChannel` est suffisante pour l'arrêt. Dans des scénarios plus complexes, la fermeture des channels de données peut être importante. Réfléchissez à quand et pourquoi vous pourriez vouloir fermer `dataChannel` ou `alertChannel`.
*   **Utilisation du package `context`** : Pour une gestion d'annulation plus robuste dans des applications réelles, le package `context` est souvent préféré à un simple `quitChannel`. Faites une petite recherche sur `context.WithCancel` et comment il pourrait être utilisé ici.

---

Bon courage pour ce TP ! N'hésitez pas à expérimenter et à observer attentivement le comportement de votre programme. C'est en manipulant que l'on comprend le mieux les subtilités de la concurrence en Go.