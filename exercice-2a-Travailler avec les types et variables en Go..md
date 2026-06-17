Absolument ! Voici un sujet de TP conçu pour aborder les types, variables et constantes en Go, en tenant compte de l'utilisation de l'IA par les apprenants.

---

## TP : Maîtrise des Types, Variables et Constantes en Go

### Objectif du TP
Travailler avec les types, les variables et les constantes en Go, en explorant la déclaration explicite, l'inférence de type et les propriétés des constantes.

### Prérequis
*   Un environnement Go fonctionnel (Go installé et configuré).
*   Connaissance de base de la structure d'un programme Go (`package main`, `func main`).
*   Savoir créer et exécuter un fichier `.go`.
*   Savoir afficher du texte dans la console (`fmt.Println`, `fmt.Printf`).

### Contexte
Vous êtes chargé de développer un module de gestion de données pour une petite application. Ce module doit stocker et manipuler diverses informations, allant des détails d'un utilisateur à des paramètres de configuration fixes. Ce TP vous guidera à travers les différentes manières de déclarer et d'utiliser ces données en Go.

---

### Exercices

#### Exercice 1 : Déclaration Explicite de Variables

Dans cet exercice, vous allez déclarer des variables en spécifiant explicitement leur type.

1.  Créez un nouveau fichier Go (par exemple, `main.go`).
2.  Dans la fonction `main`, déclarez les variables suivantes en spécifiant leur type et en les initialisant avec des valeurs de votre choix :
    *   `nomUtilisateur` (chaîne de caractères)
    *   `ageUtilisateur` (nombre entier)
    *   `estConnecte` (booléen)
    *   `soldeCompte` (nombre décimal, type `float64`)
3.  Affichez la valeur de chacune de ces variables dans la console.

#### Exercice 2 : Inférence de Type avec l'Opérateur `:=`

Go permet d'inférer le type d'une variable lors de sa déclaration et initialisation.

1.  À la suite de l'Exercice 1, déclarez et initialisez les variables suivantes en utilisant l'opérateur de déclaration courte `:=` :
    *   `villeResidence` (chaîne de caractères)
    *   `codePostal` (nombre entier)
    *   `tauxRemise` (nombre décimal)
2.  Pour chacune de ces variables, affichez sa valeur *et* son type inféré.
    *   *Indice :* La fonction `fmt.Printf` avec le verbe de format `%T` est utile pour cela.

#### Exercice 3 : Manipulation de Constantes

Les constantes sont des valeurs qui ne peuvent pas être modifiées après leur déclaration.

1.  Déclarez les constantes suivantes :
    *   `PI` (valeur `3.14159`)
    *   `NOM_APPLICATION` (valeur `"Gestionnaire Go"`)
    *   `ANNEE_LANCEMENT` (valeur `2023`)
2.  Utilisez la constante `PI` pour calculer la circonférence d'un cercle dont le rayon est une variable que vous déclarerez et initialiserez (par exemple, `rayon := 10.5`). Affichez le résultat.
3.  Affichez la valeur de toutes les constantes déclarées.
4.  Tentez de modifier la valeur de la constante `ANNEE_LANCEMENT` après sa déclaration (par exemple, `ANNEE_LANCEMENT = 2024`). Observez l'erreur de compilation et comprenez pourquoi elle se produit.

#### Exercice 4 : Réaffectation et Valeurs par Défaut

Cet exercice explore la modification des variables et le comportement des variables non initialisées.

1.  Modifiez la valeur de la variable `ageUtilisateur` (déclarée à l'Exercice 1) pour simuler un anniversaire. Affichez l'ancienne et la nouvelle valeur.
2.  Déclarez une nouvelle variable `message` de type `string` sans l'initialiser. Affichez sa valeur. Qu'observez-vous ? (Ceci illustre la "zero value" de Go).
3.  Déclarez une variable `compteur` de type `int` sans l'initialiser. Affichez sa valeur. Qu'observez-vous ?

---

### Aller plus loin (Bonus)

*   **Déclaration multiple :** Déclarez plusieurs variables du même type sur une seule ligne (ex: `var a, b, c int`).
*   **Constantes énumérées avec `iota` :** Recherchez et utilisez `iota` pour déclarer une série de constantes liées (par exemple, les jours de la semaine ou des statuts d'erreur).
*   **Conversion de type :** Créez une variable de type `int` et une autre de type `float64`. Tentez d'effectuer une opération arithmétique entre elles sans conversion, puis avec une conversion explicite.

---

Bon courage et amusez-vous bien avec Go !