Absolument ! Voici une proposition de TP axé sur la manipulation et la compréhension des slices et maps en Go, avec une approche pragmatique et bienveillante.

---

## TP Go : Maîtrise des Slices et Maps pour la Gestion de Stock

### Objectif du TP

Ce TP vise à vous familiariser avec la création, la modification, l'itération et la compréhension des performances des slices et des maps en Go, à travers un cas pratique de gestion de stock.

### Contexte

Vous êtes chargé de développer une partie du système de gestion d'inventaire pour une petite boutique en ligne. Ce système doit permettre de suivre les produits, leurs quantités en stock et de les organiser par catégorie.

### Consignes Générales

*   Créez un nouveau module Go pour ce TP.
*   Organisez votre code de manière claire, en utilisant des fonctions pour chaque opération logique.
*   N'hésitez pas à utiliser l'IA comme un assistant pour générer des bouts de code, explorer des syntaxes ou obtenir des explications. L'objectif reste votre compréhension et votre capacité à justifier vos choix.

### Exercices

#### Partie 1 : Gestion des Catégories (Slices)

Les catégories de produits sont des chaînes de caractères simples (ex: "Électronique", "Vêtements", "Livres").

1.  **Initialisation et Ajout :**
    *   Déclarez un slice de chaînes de caractères (`[]string`) nommé `categories`.
    *   Initialisez-le avec au moins trois catégories de votre choix.
    *   Ajoutez deux nouvelles catégories à ce slice.
    *   Affichez toutes les catégories.

2.  **Vérification et Suppression :**
    *   Écrivez une fonction `categorieExiste(nom string, categories []string) bool` qui vérifie si une catégorie donnée existe dans le slice.
    *   Utilisez cette fonction pour vérifier l'existence d'une catégorie que vous avez ajoutée et d'une catégorie inexistante.
    *   Écrivez une fonction `supprimerCategorie(nom string, categories []string) []string` qui supprime une catégorie du slice si elle existe.
    *   Supprimez une catégorie existante et une catégorie inexistante (vérifiez que le slice ne change pas dans ce dernier cas).
    *   Affichez le slice de catégories après chaque suppression.

3.  **Capacité et Croissance :**
    *   Après avoir effectué les opérations précédentes, affichez la longueur (`len`) et la capacité (`cap`) de votre slice `categories`.
    *   Expliquez brièvement ce que représentent ces deux valeurs et comment elles ont évolué au cours des opérations.

#### Partie 2 : Gestion des Produits et du Stock (Maps)

Un produit sera défini par un identifiant unique (entier), un nom (chaîne), un prix (flottant) et une catégorie (chaîne). Le stock sera géré séparément par identifiant de produit.

1.  **Définition des Structures :**
    *   Définissez une `struct` nommée `Produit` avec les champs `ID int`, `Nom string`, `Prix float64`, `Categorie string`.
    *   Déclarez une map `map[int]Produit` nommée `inventaireProduits` pour stocker les détails des produits (clé : ID du produit, valeur : `Produit`).
    *   Déclarez une map `map[int]int` nommée `stockProduits` pour stocker les quantités en stock (clé : ID du produit, valeur : quantité).

2.  **Ajout et Modification :**
    *   Ajoutez au moins trois produits à `inventaireProduits` et leur quantité initiale à `stockProduits`.
    *   Modifiez le prix d'un produit existant.
    *   Mettez à jour la quantité en stock d'un produit.
    *   Affichez les détails complets (ID, Nom, Prix, Catégorie, Stock) de tous les produits.

3.  **Recherche et Suppression :**
    *   Écrivez une fonction `obtenirProduit(id int, inventaire map[int]Produit, stock map[int]int) (Produit, int, bool)` qui retourne un produit, sa quantité en stock et un booléen indiquant si le produit existe.
    *   Utilisez cette fonction pour rechercher un produit existant et un produit inexistant.
    *   Supprimez un produit de `inventaireProduits` et de `stockProduits` en utilisant son ID.
    *   Vérifiez que le produit n'est plus présent.

4.  **Opérations de Stock :**
    *   Écrivez une fonction `vendreProduit(id int, quantite int, stock map[int]int) bool` qui décrémente le stock d'un produit. La fonction doit retourner `true` si la vente est possible (stock suffisant) et `false` sinon.
    *   Écrivez une fonction `reapprovisionnerProduit(id int, quantite int, stock map[int]int)` qui incrémente le stock d'un produit.
    *   Simulez quelques ventes et réapprovisionnements, en affichant le stock avant et après chaque opération.

#### Partie 3 : Combinaison Slices et Maps & Performance

Cette partie explore comment utiliser les deux structures ensemble et évalue leurs performances.

1.  **Indexation par Catégorie :**
    *   Créez une map `map[string][]int` nommée `produitsParCategorie`. La clé sera le nom de la catégorie, et la valeur sera un slice d'IDs de produits appartenant à cette catégorie.
    *   Populez cette map en itérant sur `inventaireProduits`.
    *   Écrivez une fonction `listerProduitsParCategorie(categorie string, inventaire map[int]Produit, produitsParCategorie map[string][]int)` qui affiche tous les produits d'une catégorie donnée.
    *   Testez cette fonction avec plusieurs catégories.

2.  **Performance des Maps (Grand Volume) :**
    *   Générez un grand nombre de produits (par exemple, 100 000) avec des IDs uniques et des données aléatoires pour le nom, le prix et la catégorie.
    *   Mesurez le temps nécessaire pour ajouter ces 100 000 produits à `inventaireProduits` et `stockProduits`. Utilisez le package `time` de Go.
    *   Répétez l'opération en initialisant les maps avec `make(map[int]Produit, 100000)` et `make(map[int]int, 100000)`.
    *   Comparez les temps d'exécution. Expliquez pourquoi l'utilisation de `make` avec une capacité initiale peut améliorer les performances pour l'ajout d'un grand nombre d'éléments.
    *   Mesurez le temps nécessaire pour effectuer 10 000 recherches aléatoires de produits par ID dans `inventaireProduits`.
    *   Mesurez le temps nécessaire pour itérer sur tous les éléments de `inventaireProduits`.
    *   Commentez les performances observées pour l'ajout, la recherche et l'itération dans les maps.

#### Bonus (Facultatif)

*   Implémentez une fonction de tri des produits par prix croissant ou décroissant (cela nécessitera de convertir la map en un slice de produits temporaire pour le tri).
*   Ajoutez une gestion d'erreurs plus robuste pour les fonctions (ex: retourner des erreurs plutôt que des booléens simples).
*   Créez une fonction qui calcule la valeur totale du stock pour une catégorie donnée.

---

### Critères de Réussite

*   Le code compile et s'exécute sans erreur.
*   Toutes les fonctionnalités demandées sont implémentées.
*   Le code est lisible et bien organisé (fonctions, commentaires si nécessaire).
*   Les explications sur la capacité des slices et les performances des maps sont pertinentes.

Amusez-vous bien avec ce TP ! C'est une excellente occasion de solidifier vos connaissances sur ces structures de données fondamentales en Go.