Bonjour chers apprenants,

Ce TP est conçu pour vous permettre de maîtriser deux caractéristiques puissantes du langage Go : les fonctions variadiques et les fonctions à retours multiples. Ces mécanismes sont essentiels pour écrire du code flexible, expressif et robuste.

---

### TP : Fonctions Variadiques et Retours Multiples en Go

**Objectif du TP :**
Implémenter des fonctions Go qui acceptent un nombre variable d'arguments et retournent plusieurs valeurs, en explorant des cas d'usage pratiques et la gestion des erreurs.

**Contexte :**
En Go, la flexibilité est souvent obtenue par des mécanismes élégants. Les fonctions variadiques permettent de gérer un nombre indéfini d'arguments du même type, simplifiant l'API de vos fonctions. Parallèlement, les retours multiples simplifient la gestion des résultats et des erreurs sans avoir recours à des exceptions ou des structures complexes. Ces deux concepts, combinés, offrent des outils puissants pour écrire du code plus expressif et robuste.

---

#### Exercice 1 : Calcul de Statistiques de Base

**Description :**
Créez une fonction qui prend un nombre variable de nombres entiers et calcule leur somme, leur nombre total et leur moyenne.

**Consignes :**
1.  Définissez une fonction nommée `CalculerStatistiquesBase` qui accepte un nombre variable d'arguments de type `int`.
2.  Cette fonction devra retourner trois valeurs :
    *   La somme de tous les nombres (`int`).
    *   Le nombre total d'arguments passés (`int`).
    *   La moyenne des nombres (`float64` pour une meilleure précision).
3.  Testez votre fonction avec différents ensembles de nombres dans votre fonction `main` :
    *   Un ensemble vide (aucun argument).
    *   Un seul nombre.
    *   Plusieurs nombres.

**Exemple d'appel (dans `main`) :**
```go
somme, count, moyenne := CalculerStatistiquesBase(10, 20, 30, 40)
fmt.Printf("Somme: %d, Count: %d, Moyenne: %.2f\n", somme, count, moyenne)

sommeVide, countVide, moyenneVide := CalculerStatistiquesBase()
fmt.Printf("Somme (vide): %d, Count (vide): %d, Moyenne (vide): %.2f\n", sommeVide, countVide, moyenneVide)
```

---

#### Exercice 2 : Statistiques Complètes avec Gestion d'Erreurs

**Description :**
Étendez la fonctionnalité précédente pour inclure le calcul du minimum et du maximum, et ajoutez une gestion d'erreur explicite pour le cas où aucun argument n'est fourni.

**Consignes :**
1.  Définissez une fonction nommée `CalculerStatistiquesCompletes` qui accepte un nombre variable d'arguments de type `float64`.
2.  Cette fonction devra retourner six valeurs :
    *   Le minimum (`float64`).
    *   Le maximum (`float64`).
    *   La somme (`float64`).
    *   La moyenne (`float64`).
    *   Le nombre total d'arguments (`int`).
    *   Une erreur (`error`).
3.  Si aucun argument n'est passé à la fonction, elle devra retourner des valeurs par défaut (par exemple, 0 pour les nombres, 0 pour le compte) et une erreur explicite (utilisez `errors.New("aucun argument fourni")`).
4.  Testez votre fonction avec les mêmes cas que l'Exercice 1, en vérifiant systématiquement l'erreur retournée.

**Exemple d'appel (dans `main`) :**
```go
min, max, sum, avg, count, err := CalculerStatistiquesCompletes(1.5, 2.8, 0.7, 3.1)
if err != nil {
    fmt.Println("Erreur:", err)
} else {
    fmt.Printf("Min: %.2f, Max: %.2f, Somme: %.2f, Moyenne: %.2f, Count: %d\n", min, max, sum, avg, count)
}

_, _, _, _, _, errVide := CalculerStatistiquesCompletes()
if errVide != nil {
    fmt.Println("Erreur pour arguments vides:", errVide)
}
```

---

#### Exercice 3 : Analyse de Données de Capteur

**Description :**
Mettez en pratique les concepts appris en simulant l'analyse de relevés de capteurs, en filtrant les données invalides et en fournissant des statistiques.

**Consignes :**
1.  Définissez une fonction nommée `AnalyserDonneesCapteur` qui accepte un nombre variable d'arguments de type `float64` (représentant des relevés de température).
2.  Cette fonction devra d'abord filtrer les relevés valides. Pour cet exercice, considérez qu'un relevé est valide s'il est strictement supérieur à 0.0 et inférieur ou égal à 100.0 (températures en Celsius).
3.  Utilisez votre fonction `CalculerStatistiquesCompletes` (ou une version adaptée) sur les relevés *valides* pour obtenir leurs statistiques (min, max, moyenne).
4.  La fonction `AnalyserDonneesCapteur` devra retourner les valeurs suivantes :
    *   Le minimum des relevés valides (`float64`).
    *   Le maximum des relevés valides (`float64`).
    *   La moyenne des relevés valides (`float64`).
    *   Le nombre de relevés *valides* (`int`).
    *   Le nombre de relevés *invalides* (`int`).
    *   Une erreur (`error`) si aucun relevé valide n'est trouvé après le filtrage.
5.  Testez avec des ensembles de données incluant des valeurs valides, invalides, et un cas où toutes les valeurs sont invalides.

**Exemple d'appel (dans `main`) :**
```go
minTemp, maxTemp, avgTemp, validCnt, invalidCnt, err := AnalyserDonneesCapteur(22.5, 23.1, -5.0, 101.0, 21.9, 0.0, 24.0)
if err != nil {
    fmt.Println("Erreur d'analyse:", err)
} else {
    fmt.Printf("Temp Min: %.2f, Max: %.2f, Moyenne: %.2f, Valides: %d, Invalides: %d\n", minTemp, maxTemp, avgTemp, validCnt, invalidCnt)
}

_, _, _, _, _, errToutInvalide := AnalyserDonneesCapteur(-10.0, 105.0, 0.0)
if errToutInvalide != nil {
    fmt.Println("Erreur pour données toutes invalides:", errToutInvalide)
}
```

---

**Rendu :**
Votre code source Go (.go) pour chaque exercice, idéalement dans un seul fichier `main.go` ou des fichiers séparés si vous préférez, avec une fonction `main` qui appelle et teste vos fonctions pour démontrer leur bon fonctionnement.

Bon courage !