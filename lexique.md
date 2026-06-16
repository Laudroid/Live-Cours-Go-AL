**Absence de classes** : Caractéristique de Go où le concept de classes, tel qu'il existe dans d'autres langages orientés objet, est remplacé par l'usage des structs et des interfaces.

**API REST** : Interface de programmation applicative utilisant les principes REST, souvent développée en Go avec 'net/http' ou des frameworks comme Gin.

**Applications backend** : Applications côté serveur, développées en Go pour gérer des requêtes, interagir avec des bases de données et fournir des services, incluant API REST, tests et logging.

**Bases de données** : Systèmes de stockage et d'accès aux données, gérés en Go via 'database/sql' ou des ORM tels que sqlx ou GORM.

**Channels** : Mécanisme de communication typé en Go pour synchroniser et échanger des données entre goroutines de manière sécurisée.

**Channels buffered** : Channels Go avec une capacité de stockage limitée, permettant à l'émetteur d'envoyer sans attendre un récepteur immédiat tant que le buffer n'est pas plein.

**Channels non-buffered** : Channels Go qui nécessitent qu'un émetteur et un récepteur soient prêts simultanément pour échanger une valeur (communication synchrone).

**Composition (vs héritage)** : Approche privilégiée en Go pour la réutilisation de code et l'agencement de comportements, par opposition à l'héritage classique des langages orientés objet.

**Concurrence** : Capacité du langage Go à exécuter plusieurs tâches logiquement indépendantes en même temps, facilitée par les goroutines et les channels.

**Constantes** : Valeurs immuables dont la valeur est fixée à la compilation en Go.

**Context** : Package Go utilisé pour gérer les annulations, les délais d'attente (timeout) et la propagation de valeurs à travers les appels de fonctions et les goroutines.

**database/sql** : Package standard de Go fournissant une interface générique pour l'accès aux bases de données SQL.

**defer** : Instruction Go qui reporte l'exécution d'une fonction jusqu'à ce que la fonction englobante se termine, souvent utilisée pour libérer des ressources.

**Duck typing** : Principe appliqué aux interfaces en Go : un type satisfait implicitement une interface s'il implémente toutes les méthodes définies par celle-ci, sans déclaration explicite.

**Écosystème (de Go)** : Ensemble des outils, bibliothèques, conventions et ressources gravitant autour du langage Go.

**Embedding** : Mécanisme Go de composition de structs qui permet de 'promouvoir' les champs et méthodes d'une struct imbriquée vers la struct englobante, remplaçant l'héritage.

**errors.As** : Fonction du package 'errors' de Go pour décompresser une erreur et vérifier si elle est d'un type spécifique, souvent utilisée pour les erreurs personnalisées.

**errors.Is** : Fonction du package 'errors' de Go pour vérifier si une erreur donnée correspond ou encapsule une erreur cible spécifique.

**Erreurs idiomatiques** : Manière standard et recommandée de gérer les erreurs en Go, souvent par le retour d'une valeur 'error' en plus du résultat d'une fonction.

**Fan-in** : Pattern de concurrence où les résultats de plusieurs goroutines sont regroupés et agrégés dans un seul channel.

**Fan-out** : Pattern de concurrence où une tâche est distribuée à plusieurs goroutines pour un traitement parallèle.

**Fonctions** : Blocs de code réutilisables en Go, pouvant retourner plusieurs valeurs, accepter un nombre variable d'arguments (variadiques) et être assignées à des variables.

**for** : Structure de contrôle de boucle principale en Go, utilisée pour l'itération.

**Framework léger Gin** : Framework web populaire en Go, reconnu pour sa rapidité et sa simplicité, souvent utilisé pour développer des API REST.

**Gestion des erreurs** : Manière standardisée en Go de signaler et de traiter les problèmes survenus lors de l'exécution d'un programme, basée sur le pattern 'error'.

**go test** : Commande standard en Go pour exécuter les tests unitaires et les benchmarks d'un projet.

**go.mod** : Fichier texte qui définit un module Go et liste ses dépendances et leurs versions.

**Go** : Langage de programmation créé par Google en 2009, connu pour sa simplicité, sa performance et sa gestion de la concurrence.

**GOPATH** : Ancienne variable d'environnement Go qui définissait le répertoire de travail pour les projets (largement supplantée par les modules).

**GOROOT** : Variable d'environnement Go qui pointe vers le répertoire d'installation du SDK Go.

**Goroutines** : Fonctions concurrentes légères en Go, gérées par le scheduler Go, permettant l'exécution simultanée de nombreuses opérations.

**GORM** : Un Object-Relational Mapper (ORM) populaire en Go pour interagir avec les bases de données en mappant les structs Go aux tables de la base de données.

**Handlers (API REST)** : Fonctions en Go qui gèrent les requêtes HTTP spécifiques et construisent les réponses pour une API REST.

**Hello World** : Programme simple servant de premier exemple dans un nouveau langage, affichant le message 'Hello World'.

**if** : Structure de contrôle conditionnelle en Go.

**Inférence de type** : Capacité du compilateur Go à déterminer automatiquement le type d'une variable ou d'une expression à partir de son contexte.

**Interface vide** : L'interface 'interface{}' en Go, qui ne déclare aucune méthode et peut donc contenir n'importe quel type de valeur.

**Interfaces** : Type de Go qui définit un ensemble de comportements (méthodes) que tout autre type peut implémenter implicitement pour être compatible avec cette interface.

**Logging** : Processus d'enregistrement des événements, des erreurs et des informations de l'application, souvent structuré en Go avec 'slog'.

**Logging structuré** : Enregistrement des informations de log dans un format structuré (par exemple, JSON) pour faciliter l'analyse, la recherche et l'agrégation, comme avec 'slog' en Go.

**Maps** : Collections non ordonnées en Go, associant des clés à des valeurs.

**Méthodes (associées aux structs)** : Fonctions définies avec un 'receiver' (récepteur) qui les lie à un type struct particulier, permettant d'opérer sur des instances de ce struct.

**Middleware (API REST)** : Fonctions Go exécutées avant ou après le handler principal d'une requête HTTP, permettant d'ajouter des fonctionnalités transversales (authentification, logging, etc.) à une API REST.

**Mocking** : Technique de test où des objets (mocks) sont utilisés pour simuler le comportement de dépendances réelles d'une unité de code, souvent en utilisant des interfaces en Go.

**Modules** : Unité de gestion des dépendances et de versionnement en Go, définie par un fichier 'go.mod'.

**M:N threading** : Modèle de gestion des threads par le scheduler Go où M goroutines sont multiplexées sur N threads du système d'exploitation.

**net/http** : Package standard de Go fournissant des fonctionnalités pour implémenter des clients et serveurs HTTP, essentiel pour les API REST.

**Packages** : Unités d'organisation du code en Go, regroupant des fonctions, types et variables liés, similaires aux bibliothèques.

**Paradoxe de Go** : Concept lié aux compromis et choix de conception du langage Go, notamment entre performance et simplicité.

**Pattern error** : Le modèle de gestion des erreurs en Go où les fonctions retournent généralement deux valeurs : le résultat et une valeur d'erreur potentielle.

**Patterns de concurrence** : Modèles de conception pour utiliser efficacement les goroutines et channels, tels que fan-out, fan-in, pipeline et worker pool.

**Philosophie (du langage Go)** : Principes de conception de Go par Google (Rob Pike, Ken Thompson), axés sur la simplicité, l'efficacité, la compilation rapide et la concurrence native.

**Pipeline** : Pattern de concurrence où les données passent séquentiellement à travers plusieurs étapes de traitement, chaque étape étant une goroutine communiquant via channels.

**Pointeurs** : Variables en Go qui stockent l'adresse mémoire d'une autre variable, permettant des opérations de passage par référence.

**Polymorphisme par interface implicite** : Capacité en Go pour un type de satisfaire une interface sans déclaration explicite, simplement en implémentant toutes les méthodes requises par cette interface.

**Programmation orientée structure** : Approche de programmation en Go axée sur l'utilisation des structs et des interfaces pour organiser le code, en l'absence de classes classiques.

**Routage (API REST)** : Processus de direction des requêtes HTTP entrantes vers les handlers appropriés en fonction de leur URL et méthode HTTP dans une API REST.

**Scheduler Go** : Composant du runtime Go qui gère l'exécution des goroutines sur les threads du système d'exploitation.

**Select** : Instruction Go utilisée pour écouter et réagir au premier channel parmi plusieurs qui est prêt à communiquer.

**Sérialisation JSON** : Processus de conversion de structures de données Go en format JSON (et inversement) pour l'échange de données, utilisant 'encoding/json'.

**Slices** : Références dynamiques à des tableaux sous-jacents en Go, offrant une taille flexible et des fonctionnalités de manipulation puissantes.

**slog** : Package standard de Go (depuis la version 1.21) pour un logging structuré et performant.

**sqlx** : Extension de 'database/sql' pour Go, simplifiant l'interaction avec les bases de données, notamment pour mapper les résultats de requêtes à des structs Go.

**Struct tags** : Métadonnées ajoutées aux champs des structs en Go, utilisées par des packages comme 'encoding/json' pour contrôler la sérialisation/désérialisation.

**Structs** : Types composites en Go qui regroupent des champs de différents types sous un même nom, fondamentaux pour la modélisation des données.

**Structures (de données)** : Manières d'organiser les données en Go, telles que les tableaux, slices et maps.

**Structures de contrôle** : Instructions Go (if, for, switch, defer) qui dictent le flux d'exécution d'un programme.

**switch** : Structure de contrôle de sélection en Go, utilisée pour exécuter différents blocs de code en fonction de la valeur d'une expression.

**sync.Mutex** : Mécanisme de synchronisation en Go (du package 'sync') pour protéger l'accès concurrentiel à une ressource partagée, garantissant qu'une seule goroutine y accède à la fois.

**sync.Once** : Mécanisme de synchronisation en Go (du package 'sync') pour garantir qu'une action donnée n'est exécutée qu'une seule fois, même en présence de multiples goroutines.

**sync.WaitGroup** : Mécanisme de synchronisation en Go (du package 'sync') utilisé pour attendre qu'un groupe de goroutines ait terminé son exécution.

**Synchronisation** : Ensemble de mécanismes en Go (comme Mutex, WaitGroup, Once) pour coordonner l'exécution de goroutines et gérer l'accès aux ressources partagées.

**Table-driven tests** : Pattern de tests en Go où plusieurs scénarios de test sont définis dans une table de données, et une boucle exécute la logique de test pour chaque entrée de la table.

**Tableaux** : Collections d'éléments de même type et de taille fixe en Go.

**Tests** : Processus de vérification du bon fonctionnement du code Go, incluant les tests unitaires et le mocking.

**Tests unitaires** : Tests Go qui vérifient le comportement de la plus petite unité logique de code (une fonction ou une méthode) de manière isolée.

**Types (de base)** : Types de données fondamentaux en Go, incluant les entiers, les flottants, les booléens et les chaînes de caractères.

**Variables** : Emplacements nommés en mémoire pour stocker des données, pouvant être déclarées avec ou sans inférence de type en Go.

**Worker pool** : Pattern de concurrence où un ensemble fixe de goroutines (les 'workers') attend et traite des tâches provenant d'un channel commun.

**Workspace** : Environnement de développement Go qui peut contenir plusieurs modules et est défini par la variable d'environnement GOPATH.

