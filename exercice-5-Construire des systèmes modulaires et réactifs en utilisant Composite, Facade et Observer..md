Bonjour !

Ce TP est conçu pour vous permettre d'explorer et de maîtriser trois Design Patterns fondamentaux : Composite, Facade et Observer. L'objectif est de construire un système intégré, modulaire et réactif, en appliquant ces patterns à un cas d'usage concret.

---

## Sujet du TP : Système de Gestion Intégrée de Ressources (SGIR)

**Objectif du TP :** Construire des systèmes modulaires et réactifs en utilisant les Design Patterns Composite, Facade et Observer.

**Contexte du Projet :**
Vous êtes chargé de développer un "Système de Gestion Intégrée de Ressources (SGIR)" pour une entreprise. Ce système doit gérer à la fois des documents (rapports, factures, fiches produits) et les opérations de stock, tout en notifiant les parties prenantes des événements importants.

---

### Consignes Générales

*   **Langage de Programmation :** Choisissez entre Java ou C#.
*   **Structure du Code :** Organisez votre code en packages (Java) ou namespaces (C#) pertinents pour chaque pattern et chaque partie du système.
*   **Qualité du Code :** Visez un code propre, lisible et bien structuré.
*   **Tests :** Implémentez quelques tests unitaires simples pour valider le bon fonctionnement de chaque pattern.
*   **Documentation :** Utilisez les commentaires (Javadoc ou XML comments) pour expliquer les interfaces, classes et méthodes clés.

**Utilisation de l'IA :**
L'IA est un outil puissant. N'hésitez pas à l'utiliser pour :
*   Générer le code boilerplate (constructeurs, getters/setters, implémentations de base d'interfaces).
*   Obtenir des rappels syntaxiques ou des exemples d'utilisation de fonctionnalités du langage.
*   Proposer une première ébauche de la structure d'un pattern.

Cependant, l'intégration des patterns, les choix de conception spécifiques à votre contexte, la logique métier et la réflexion sur la manière dont les patterns interagissent sont le cœur de cet exercice et doivent rester votre responsabilité. Utilisez l'IA comme un assistant, pas comme un substitut à votre compréhension et à votre créativité.

---

### Partie 1 : Structure Documentaire (Pattern Composite)

Le SGIR doit pouvoir organiser et manipuler des documents et des dossiers de manière uniforme.

**Problématique :**
Représenter une structure hiérarchique de fichiers et de répertoires virtuels. Les opérations (comme l'affichage du contenu ou la taille) doivent s'appliquer indifféremment à un fichier ou à un dossier contenant d'autres fichiers/dossiers.

**Tâches à réaliser :**

1.  **Interface `DocumentComponent` :**
    *   Définissez une interface ou une classe abstraite `DocumentComponent` (ou `RessourceSysteme`) qui déclare les opérations communes à tous les éléments de la structure (fichiers et dossiers).
    *   Exemples d'opérations : `afficherDetails()`, `getNom()`, `getTaille()`.

2.  **Classe `Fichier` (Leaf) :**
    *   Créez une classe `Fichier` qui implémente `DocumentComponent`.
    *   Un fichier aura un nom et une taille.
    *   Implémentez les opérations définies dans `DocumentComponent`.

3.  **Classe `Dossier` (Composite) :**
    *   Créez une classe `Dossier` qui implémente `DocumentComponent`.
    *   Un dossier aura un nom et contiendra une liste de `DocumentComponent` (fichiers ou autres dossiers).
    *   Implémentez les opérations définies dans `DocumentComponent`. Pour `getTaille()`, un dossier doit calculer la somme des tailles de tous ses composants. Pour `afficherDetails()`, il doit afficher ses propres détails puis ceux de ses enfants.
    *   Ajoutez des méthodes spécifiques pour gérer les enfants : `ajouter(DocumentComponent composant)`, `retirer(DocumentComponent composant)`.

4.  **Démonstration :**
    *   Dans une méthode `main` ou un test, créez une structure de dossiers et de fichiers virtuels.
    *   Exemple :
        *   Un dossier "Rapports Annuels" contenant "Rapport_2022.pdf" et "Rapport_2023.pdf".
        *   Un dossier "Factures Clients" contenant un sous-dossier "Client A" (avec "Facture_A_01.pdf") et un fichier "Facture_B_01.pdf".
        *   Affichez les détails et la taille totale de la racine de cette structure.

---

### Partie 2 : Interface de Gestion de Stocks (Pattern Facade)

Le SGIR doit interagir avec un système de gestion de stocks existant, qui est complexe et composé de plusieurs sous-systèmes.

**Problématique :**
Simplifier l'accès à un système de gestion de stocks complexe, composé de plusieurs modules (gestion des inventaires, traitement des commandes, gestion des fournisseurs), pour les utilisateurs ou d'autres parties du SGIR.

**Tâches à réaliser :**

1.  **Sous-systèmes Complexes :**
    *   Créez des classes représentant des sous-systèmes complexes (simulés par des méthodes simples affichant des messages) :
        *   `SystemeInventaire` : Méthodes `verifierStock(String produit)`, `mettreAJourStock(String produit, int quantite)`.
        *   *Optionnel :* `SystemeCommandes` : Méthodes `creerCommande(String produit, int quantite)`, `traiterPaiement()`.
        *   *Optionnel :* `SystemeFournisseurs` : Méthodes `contacterFournisseur(String produit, int quantite)`.

2.  **Classe `StockManagerFacade` :**
    *   Créez une classe `StockManagerFacade` (ou `GestionnaireStockFacade`) qui encapsule les interactions avec les sous-systèmes.
    *   Cette façade doit avoir des méthodes simplifiées pour les opérations courantes, par exemple :
        *   `ajouterProduit(String nomProduit, int quantite)`
        *   `retirerProduit(String nomProduit, int quantite)`
        *   `passerCommandeFournisseur(String nomProduit, int quantite)`
        *   `verifierDisponibilite(String nomProduit)`

3.  **Démonstration :**
    *   Dans une méthode `main` ou un test, utilisez la `StockManagerFacade` pour effectuer des opérations de stock sans interagir directement avec les sous-systèmes.
    *   Exemple : `facade.ajouterProduit("Ordinateur Portable", 10);`, `facade.passerCommandeFournisseur("Souris", 50);`.

---

### Partie 3 : Surveillance d'Événements (Pattern Observer)

Le SGIR doit notifier les utilisateurs ou d'autres modules des changements importants dans le système de stock.

**Problématique :**
Permettre à plusieurs "observateurs" d'être automatiquement informés et de réagir lorsqu'un "sujet" (ici, le système de gestion de stocks) subit un changement d'état.

**Tâches à réaliser :**

1.  **Interface `ObservateurStock` :**
    *   Définissez une interface `ObservateurStock` (ou `StockObserver`) avec une méthode `mettreAJour(String evenement, String details)`.

2.  **Classes `SujetStock` (Subject) :**
    *   Modifiez la classe `StockManagerFacade` (ou créez une classe `SujetStock` qu'elle utilisera) pour qu'elle puisse gérer une liste d'observateurs.
    *   Ajoutez les méthodes : `ajouterObservateur(ObservateurStock observateur)`, `retirerObservateur(ObservateurStock observateur)`, `notifierObservateurs(String evenement, String details)`.
    *   Chaque fois qu'une opération de stock importante est effectuée via la façade (ex: `ajouterProduit`, `retirerProduit` si le stock passe sous un seuil critique, `passerCommandeFournisseur`), la façade doit appeler `notifierObservateurs()`.

3.  **Implémentations Concrètes d'Observateurs :**
    *   Créez au moins deux classes concrètes qui implémentent `ObservateurStock` :
        *   `LoggerStock` : Affiche simplement l'événement et les détails dans la console (simule un système de log).
        *   `EmailNotifier` : Affiche un message simulant l'envoi d'un e-mail (ex: "Envoi d'un email à l'administrateur : [événement] - [détails]").
        *   *Optionnel :* `DashboardUpdater` : Simule la mise à jour d'un tableau de bord.

4.  **Démonstration :**
    *   Dans une méthode `main` ou un test :
        *   Créez une instance de `StockManagerFacade`.
        *   Créez plusieurs instances de vos observateurs (`LoggerStock`, `EmailNotifier`).
        *   Enregistrez ces observateurs auprès de la façade.
        *   Effectuez des opérations de stock via la façade et observez comment les observateurs sont notifiés.
        *   Exemple : `facade.retirerProduit("Ordinateur Portable", 8);` (si le stock initial était 10, cela pourrait déclencher une alerte de stock bas).

---

### Intégration et Démonstration Finale

Dans votre méthode `main` principale, montrez comment les trois patterns peuvent coexister :

1.  Initialisez votre structure de documents (Composite) avec quelques rapports de stock générés.
2.  Initialisez votre `StockManagerFacade`.
3.  Enregistrez vos `ObservateurStock` auprès de la façade.
4.  Effectuez des opérations de stock via la façade, en observant les notifications.
5.  Simulez la génération d'un nouveau rapport de stock (un nouveau `Fichier` dans un `Dossier` existant) et affichez la structure mise à jour.

---

### Évaluation

Votre travail sera évalué sur :
*   La correcte implémentation de chaque Design Pattern.
*   La clarté et la modularité du code.
*   La pertinence des choix de conception.
*   La capacité à intégrer les différents patterns de manière cohérente.
*   La démonstration fonctionnelle du système.

Bon courage et amusez-vous à construire ce système !