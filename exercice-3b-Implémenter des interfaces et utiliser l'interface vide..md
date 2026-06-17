Bonjour à toutes et à tous !

Ce TP est conçu pour vous permettre de manipuler les interfaces en Go, un concept fondamental pour écrire du code flexible et modulaire. Nous explorerons comment définir des interfaces, les implémenter de manière implicite, et comment l'interface vide (`interface{}`) offre une flexibilité unique.

---

### **TP : Interfaces Go - Flexibilité et Interface Vide**

**Objectif du TP :** Implémenter des interfaces et utiliser l'interface vide.

**Énoncé du TP :** Définir des interfaces, les implémenter implicitement et exploiter l'interface vide.

---

#### **Contexte**

Imaginez que vous développez un système de gestion de notifications. Ce système doit être capable d'envoyer différents types de messages (e-mail, SMS, notifications console) et de traiter des données variées avant l'envoi. Les interfaces en Go sont parfaitement adaptées pour modéliser ce type de comportement polymorphique.

---

#### **Partie 1 : Définition et Implémentation Implicite d'Interfaces**

Dans cette partie, vous allez créer une interface pour les systèmes de notification et plusieurs types concrets qui l'implémenteront.

**Exercice 1.1 : Définition de l'Interface `Notifier`**

1.  Définissez une interface nommée `Notifier`.
2.  Cette interface doit déclarer une méthode `Send(message string) error`. Cette méthode sera responsable de l'envoi d'un message et retournera une erreur si l'opération échoue. Pour ce TP, vous pouvez retourner `nil` pour simuler un succès.

**Exercice 1.2 : Implémentation de `EmailNotifier`**

1.  Créez une structure `EmailNotifier` qui aura les champs `Recipient` (string) et `Sender` (string).
2.  Implémentez la méthode `Send` pour `EmailNotifier`. Cette implémentation devra afficher un message dans la console indiquant qu'un e-mail est envoyé, par exemple : `"[EMAIL] De %s à %s : %s\n"`.

**Exercice 1.3 : Implémentation de `SMSNotifier`**

1.  Créez une structure `SMSNotifier` avec un champ `PhoneNumber` (string).
2.  Implémentez la méthode `Send` pour `SMSNotifier`. Affichez un message comme : `"[SMS] Envoi à %s : %s\n"`.

**Exercice 1.4 : Implémentation de `ConsoleNotifier`**

1.  Créez une structure `ConsoleNotifier` (elle peut être vide, `struct{}`).
2.  Implémentez la méthode `Send` pour `ConsoleNotifier`. Affichez un message comme : `"[CONSOLE] Message : %s\n"`.

**Exercice 1.5 : Utilisation Polymorphique**

1.  Dans la fonction `main`, créez une tranche (`slice`) de l'interface `Notifier`.
2.  Ajoutez-y des instances de `EmailNotifier`, `SMSNotifier` et `ConsoleNotifier`.
3.  Parcourez cette tranche et appelez la méthode `Send` sur chaque élément avec un message de votre choix.
4.  Vérifiez que chaque type de notificateur affiche son message spécifique.

---

#### **Partie 2 : L'Interface Vide (`interface{}`)**

L'interface vide est un type spécial qui peut contenir n'importe quelle valeur. Elle est souvent utilisée pour des fonctions génériques ou pour des collections de types hétérogènes.

**Exercice 2.1 : Fonction `processData` avec `interface{}`**

1.  Créez une fonction nommée `processData` qui accepte un seul argument de type `interface{}`.
2.  À l'intérieur de cette fonction, utilisez une instruction `switch` avec une assertion de type (`switch v := data.(type)`) pour déterminer le type réel de la valeur passée.
3.  Gérez au moins les cas suivants :
    *   `int`: Affichez "Donnée de type entier : %d\n".
    *   `string`: Affichez "Donnée de type chaîne : %s\n".
    *   `bool`: Affichez "Donnée de type booléen : %t\n".
    *   `*EmailNotifier` (un pointeur vers votre structure `EmailNotifier`): Affichez "Donnée de type EmailNotifier pour %s\n".
    *   `default`: Affichez "Type de donnée inconnu : %T\n".

**Exercice 2.2 : Appel de `processData`**

1.  Dans la fonction `main`, appelez `processData` avec différentes valeurs :
    *   Un entier (`42`).
    *   Une chaîne de caractères (`"Bonjour le monde"`).
    *   Un booléen (`true`).
    *   Une instance de `EmailNotifier` (passée par adresse, donc `&EmailNotifier{...}`).
    *   Une tranche d'entiers (`[]int{1, 2, 3}`).
    *   N'importe quelle autre valeur de votre choix.
2.  Observez les sorties pour chaque appel.

---

#### **Partie 3 : Intégration et Réflexion**

**Exercice 3.1 : Un "Smart Notifier"**

1.  Créez une nouvelle structure `User` avec les champs `Name` (string), `Email` (string) et `Phone` (string).
2.  Créez une fonction `sendSmartNotification(data interface{}, message string) error`.
3.  Cette fonction doit utiliser une assertion de type pour :
    *   Si `data` est de type `User` :
        *   Si l'utilisateur a un `Email`, utilisez un `EmailNotifier` pour lui envoyer le `message`.
        *   Sinon, si l'utilisateur a un `Phone`, utilisez un `SMSNotifier` pour lui envoyer le `message`.
        *   Sinon, utilisez un `ConsoleNotifier` pour indiquer qu'aucune méthode de contact n'est disponible.
    *   Si `data` est de type `string` :
        *   Utilisez un `ConsoleNotifier` pour envoyer le `message` en préfixant "Message générique : ".
    *   Pour tout autre type, affichez un message d'erreur indiquant que le type n'est pas supporté.
4.  Dans `main`, testez `sendSmartNotification` avec :
    *   Une `User` avec email et téléphone.
    *   Une `User` avec seulement un téléphone.
    *   Une `User` sans contact.
    *   Une simple chaîne de caractères.
    *   Un entier.

**Exercice 3.2 : Questions de Réflexion**

Répondez brièvement aux questions suivantes (vous pouvez les écrire en commentaires dans votre code ou dans un fichier texte séparé) :

1.  Quel est l'avantage principal de l'implémentation implicite des interfaces en Go par rapport à d'autres langages qui nécessitent une déclaration explicite (par exemple, `class Foo implements Bar`) ?
2.  Dans quels scénarios l'utilisation de `interface{}` est-elle appropriée, et quels sont les inconvénients potentiels ?
3.  Comment les interfaces contribuent-elles à la modularité et à la testabilité du code en Go ?

---

#### **Conseils**

*   N'oubliez pas d'utiliser `go run votre_fichier.go` pour exécuter votre code.
*   Pensez à `go fmt` pour formater votre code de manière standard.
*   Si vous utilisez un IDE comme VS Code, il peut vous aider avec l'autocomplétion et la détection d'erreurs.
*   N'hésitez pas à consulter la documentation officielle de Go si vous avez un doute sur la syntaxe ou le comportement.

Bon courage pour ce TP ! J'espère qu'il vous aidera à mieux appréhender la puissance des interfaces en Go.