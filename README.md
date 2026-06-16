# Cours Langage Go

## Objectifs de la formation

- Acquérir une compréhension approfondie du langage Go, de sa philosophie et de son écosystème.
- Maîtriser les fondamentaux du langage Go, incluant types, structures, fonctions et gestion des erreurs.
- Comprendre et appliquer la programmation orientée structure avec Go, en utilisant interfaces, structs et gestion d'erreurs idiomatique.
- Développer des compétences avancées en concurrence avec Go, notamment l'utilisation de goroutines, channels et synchronisation.
- Savoir développer des applications backend robustes et performantes avec Go, incluant API REST, accès aux bases de données, tests et logging.
## Séance 1 : Introduction à Go et philosophie du langage

**Durée :** 3 heures

### Objectifs pédagogiques

- Comprendre le contexte de création et la philosophie du langage Go.
- Appréhender le positionnement de Go par rapport à d'autres langages système et programmations courantes.
- Installer et configurer l'environnement Go pour démarrer le développement.
- Explorer la structure d'un projet Go : packages, modules et workspace.
### Contenus

#### Contexte de création de Go

- Google, Rob Pike, Ken Thompson – pourquoi Go en 2009.
#### Positionnement par rapport à C, Java, Python, Rust

- Compromis entre performance et simplicité, avantages et limites.
#### Installation et premiers programmes

- Téléchargement, installation, configuration GOPATH, GOROOT.
- Premier programme 'Hello World' en Go.
#### Structure d'un projet Go

- Packages, modules, workspace avec go.mod et commandes associées.
## Séance 2 : Fondamentaux du langage

**Durée :** 4 heures

### Objectifs pédagogiques

- Maîtriser les types de base, variables, constantes et inférence de type.
- Savoir utiliser les structures de contrôle (if, for, switch, defer).
- Savoir définir et utiliser les fonctions en Go.
- Comprendre l'usage des pointeurs et des structures de données comme tableaux, slices et maps.
### Contenus

#### Types de base, variables, constantes

- Types entiers, floats, bool, string, inférence automatique.
#### Structures de contrôle

- if, for, switch, defer et leurs usages.
#### Fonctions

- Fonctions avec valeurs multiples, variadiques, fonctions comme variables.
#### Pointeurs en Go

- Usage des pointeurs, passage par référence, limites et différences avec C.
#### Tableaux, slices et maps

- Manipulation, dimensionnement, allocation, pièges et optimisations.
## Séance 3 : Programmation orientée structure en Go

**Durée :** 4 heures

### Objectifs pédagogiques

- Comprendre l'usage des structs et méthodes associées.
- Maîtriser les interfaces et le polymorphisme par interface implícite.
- Utiliser l'embedding pour la composition et comprendre la différence avec l'héritage.
- Gérer les erreurs de manière idiomatique en Go.
- Appréhender l'absence de classes et penser en termes de comportements.
### Contenus

#### Structs

- Définition, composition, méthodes associées et receiver.
#### Interfaces

- Duck typing, interface vide, satisfaction implicite d'interfaces.
#### Embedding

- Composition vs héritage, promotion de méthodes, usages.
#### Gestion des erreurs

- Pattern error, errors.Is / errors.As, erreurs personnalisées.
#### Paradigme Go

- Absence de classes, penser en termes de comportements et interfaces.
## Séance 4 : Concurrence en Go

**Durée :** 4 heures

### Objectifs pédagogiques

- Comprendre la gestion des goroutines et le scheduler Go.
- Maîtriser l'utilisation des channels non-buffered et buffered.
- Appliquer des patterns de concurrence : fan-out, fan-in, pipeline, worker pool.
- Utiliser select pour le multiplexage de channels.
- Apprendre la synchronisation avec Mutex, WaitGroup, Once.
- Gérer le contexte d'annulation, timeout et propagation.
### Contenus

#### Goroutines et scheduler

- Création, cycle de vie, fonctionnement M:N threading.
#### Channels

- Unbuffered, buffered, directionnels, usage typique.
#### Patterns de concurrence

- Fan-out, fan-in, pipeline, worker pool.
#### Select

- Multiplexage de channels utilisé pour gérer la concurrence.
#### Synchronisation bas niveau

- sync.Mutex, sync.WaitGroup, sync.Once.
#### Context

- Annulation, timeout, propagation dans services.
## Séance 5 : Développement backend et exposition de services

**Durée :** 3 heures

### Objectifs pédagogiques

- Développer des API REST avec net/http et un framework léger Gin.
- Gérer la sérialisation JSON et struct tags.
- Accéder aux bases de données avec database/sql et sqlx ou GORM.
- Tester les applications Go avec go test et mocking.
- Mettre en place un logging structuré avec slog.
### Contenus

#### Développement d'API REST

- Utilisation de net/http, routage, middleware, handlers.
#### Framework Gin

- Introduction au framework Gin pour simplifier le développement API.
#### Sérialisation JSON

- Package encoding/json et struct tags pour contrôler la sérialisation.
#### Accès aux bases de données

- database/sql, introduction à sqlx ou GORM pour ORM.
#### Tests en Go

- Tests unitaires avec go test, table-driven tests, et mocking avec interfaces.
#### Logging structuré

- Utilisation de slog du stdlib Go 1.21+ pour logging structuré et performant.