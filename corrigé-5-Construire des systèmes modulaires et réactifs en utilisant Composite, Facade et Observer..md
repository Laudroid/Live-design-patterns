Voici deux solutions possibles pour ce TP, chacune présentée comme un chapitre distinct. Le langage choisi est Java.

---

## Chapitre 1 : Implémentations Classiques des Patterns Composite, Facade et Observer

Ce chapitre propose une implémentation directe et classique des patterns Composite, Facade et Observer, en se basant sur leurs définitions fondamentales.

### Partie 1 : Structure Documentaire (Pattern Composite)

Le pattern Composite permet de traiter des objets individuels et des compositions d'objets de manière uniforme.

#### 1. Interface `DocumentComponent`


```java
// src/composite/DocumentComponent.java
package composite;

public interface DocumentComponent {
    String getNom();
    int getTaille(); // Taille en Ko pour la simulation
    void afficherDetails(String indent); // Affiche les détails avec indentation
}
```


#### 2. Classe `Fichier` (Leaf)


```java
// src/composite/Fichier.java
package composite;

public class Fichier implements DocumentComponent {
    private String nom;
    private int taille;

    public Fichier(String nom, int taille) {
        this.nom = nom;
        this.taille = taille;
    }

    @Override
    public String getNom() {
        return nom;
    }

    @Override
    public int getTaille() {
        return taille;
    }

    @Override
    public void afficherDetails(String indent) {
        System.out.println(indent + "Fichier: " + nom + " (Taille: " + taille + " Ko)");
    }
}
```


#### 3. Classe `Dossier` (Composite)


```java
// src/composite/Dossier.java
package composite;

import java.util.ArrayList;
import java.util.List;

public class Dossier implements DocumentComponent {
    private String nom;
    private List<DocumentComponent> composants;

    public Dossier(String nom) {
        this.nom = nom;
        this.composants = new ArrayList<>();
    }

    public void ajouter(DocumentComponent composant) {
        composants.add(composant);
    }

    public void retirer(DocumentComponent composant) {
        composants.remove(composant);
    }

    @Override
    public String getNom() {
        return nom;
    }

    @Override
    public int getTaille() {
        int tailleTotale = 0;
        for (DocumentComponent composant : composants) {
            tailleTotale += composant.getTaille();
        }
        return tailleTotale;
    }

    @Override
    public void afficherDetails(String indent) {
        System.out.println(indent + "Dossier: " + nom + " (Taille totale: " + getTaille() + " Ko)");
        for (DocumentComponent composant : composants) {
            composant.afficherDetails(indent + "  "); // Indentation pour les enfants
        }
    }
}
```


#### 4. Démonstration

La démonstration sera incluse dans la section d'intégration finale.

### Partie 2 : Interface de Gestion de Stocks (Pattern Facade)

Le pattern Facade fournit une interface simplifiée vers un ensemble de sous-systèmes complexes.

#### 1. Sous-systèmes Complexes


```java
// src/facade/SystemeInventaire.java
package facade;

public class SystemeInventaire {
    public void verifierStock(String produit) {
        System.out.println("[Inventaire] Vérification du stock pour " + produit + ".");
    }

    public void mettreAJourStock(String produit, int quantite) {
        System.out.println("[Inventaire] Mise à jour du stock pour " + produit + " : " + quantite + " unités.");
    }
}
```



```java
// src/facade/SystemeCommandes.java
package facade;

public class SystemeCommandes {
    public void creerCommande(String produit, int quantite) {
        System.out.println("[Commandes] Création d'une commande de " + quantite + " unités de " + produit + ".");
    }

    public void traiterPaiement() {
        System.out.println("[Commandes] Traitement du paiement de la commande.");
    }
}
```



```java
// src/facade/SystemeFournisseurs.java
package facade;

public class SystemeFournisseurs {
    public void contacterFournisseur(String produit, int quantite) {
        System.out.println("[Fournisseurs] Contact du fournisseur pour " + quantite + " unités de " + produit + ".");
    }
}
```


#### 2. Classe `StockManagerFacade`


```java
// src/facade/StockManagerFacade.java
package facade;

import observer.ObservateurStock; // Import pour l'intégration Observer
import java.util.ArrayList;
import java.util.List;

public class StockManagerFacade {
    private SystemeInventaire inventaire;
    private SystemeCommandes commandes;
    private SystemeFournisseurs fournisseurs;

    // Pour l'intégration Observer
    private List<ObservateurStock> observateurs;

    public StockManagerFacade() {
        this.inventaire = new SystemeInventaire();
        this.commandes = new SystemeCommandes();
        this.fournisseurs = new SystemeFournisseurs();
        this.observateurs = new ArrayList<>();
    }

    // Méthodes pour l'intégration Observer
    public void ajouterObservateur(ObservateurStock observateur) {
        observateurs.add(observateur);
    }

    public void retirerObservateur(ObservateurStock observateur) {
        observateurs.remove(observateur);
    }

    private void notifierObservateurs(String evenement, String details) {
        for (ObservateurStock obs : observateurs) {
            obs.mettreAJour(evenement, details);
        }
    }

    // Méthodes simplifiées de la façade
    public void ajouterProduit(String nomProduit, int quantite) {
        System.out.println("\n--- Opération : Ajouter " + quantite + " de " + nomProduit + " ---");
        inventaire.mettreAJourStock(nomProduit, quantite);
        notifierObservateurs("STOCK_AJOUT", nomProduit + " : " + quantite + " unités ajoutées.");
    }

    public void retirerProduit(String nomProduit, int quantite) {
        System.out.println("\n--- Opération : Retirer " + quantite + " de " + nomProduit + " ---");
        inventaire.verifierStock(nomProduit); // Une vraie implémentation vérifierait la disponibilité
        inventaire.mettreAJourStock(nomProduit, -quantite);
        notifierObservateurs("STOCK_RETRAIT", nomProduit + " : " + quantite + " unités retirées.");
        // Logique pour notifier si le stock est bas
        if (quantite > 5) { // Exemple de seuil
            notifierObservateurs("ALERTE_STOCK_BAS", nomProduit + " : stock potentiellement bas après retrait.");
        }
    }

    public void passerCommandeFournisseur(String nomProduit, int quantite) {
        System.out.println("\n--- Opération : Commander " + quantite + " de " + nomProduit + " au fournisseur ---");
        fournisseurs.contacterFournisseur(nomProduit, quantite);
        commandes.creerCommande(nomProduit, quantite);
        commandes.traiterPaiement();
        notifierObservateurs("COMMANDE_FOURNISSEUR", nomProduit + " : commande de " + quantite + " unités passée.");
    }

    public void verifierDisponibilite(String nomProduit) {
        System.out.println("\n--- Opération : Vérifier disponibilité de " + nomProduit + " ---");
        inventaire.verifierStock(nomProduit);
        // Une vraie implémentation retournerait la quantité disponible
    }
}
```


### Partie 3 : Surveillance d'Événements (Pattern Observer)

Le pattern Observer définit une dépendance un-à-plusieurs entre des objets, de sorte que lorsque l'un change d'état, tous ses dépendants sont automatiquement notifiés.

#### 1. Interface `ObservateurStock`


```java
// src/observer/ObservateurStock.java
package observer;

public interface ObservateurStock {
    void mettreAJour(String evenement, String details);
}
```


#### 2. Classes `SujetStock` (Intégrée dans `StockManagerFacade`)

Comme demandé, la logique du sujet est intégrée dans `StockManagerFacade`.

#### 3. Implémentations Concrètes d'Observateurs


```java
// src/observer/LoggerStock.java
package observer;

public class LoggerStock implements ObservateurStock {
    @Override
    public void mettreAJour(String evenement, String details) {
        System.out.println("[LoggerStock] Événement : " + evenement + " - Détails : " + details);
    }
}
```



```java
// src/observer/EmailNotifier.java
package observer;

public class EmailNotifier implements ObservateurStock {
    @Override
    public void mettreAJour(String evenement, String details) {
        if (evenement.equals("ALERTE_STOCK_BAS")) {
            System.out.println("[EmailNotifier] Envoi d'un email à l'administrateur : ALERTE - " + details);
        } else {
            System.out.println("[EmailNotifier] Notification par email : " + evenement + " - " + details);
        }
    }
}
```


### Intégration et Démonstration Finale


```java
// src/main/SGIRApp.java
package main;

import composite.Dossier;
import composite.Fichier;
import facade.StockManagerFacade;
import observer.EmailNotifier;
import observer.LoggerStock;

public class SGIRApp {
    public static void main(String[] args) {
        System.out.println("--- Démarrage du Système de Gestion Intégrée de Ressources (SGIR) ---");

        // 1. Initialisation de la structure de documents (Composite)
        System.out.println("\n--- Partie 1: Gestion Documentaire (Composite) ---");
        Dossier racine = new Dossier("SGIR_Racine");

        Dossier rapportsAnnuels = new Dossier("Rapports Annuels");
        rapportsAnnuels.ajouter(new Fichier("Rapport_2022.pdf", 1500));
        rapportsAnnuels.ajouter(new Fichier("Rapport_2023.pdf", 1800));
        racine.ajouter(rapportsAnnuels);

        Dossier facturesClients = new Dossier("Factures Clients");
        Dossier clientA = new Dossier("Client A");
        clientA.ajouter(new Fichier("Facture_A_01.pdf", 250));
        facturesClients.ajouter(clientA);
        facturesClients.ajouter(new Fichier("Facture_B_01.pdf", 300));
        racine.ajouter(facturesClients);

        racine.afficherDetails("");
        System.out.println("Taille totale de la racine : " + racine.getTaille() + " Ko");

        // 2. Initialisation de la StockManagerFacade
        System.out.println("\n--- Partie 2 & 3: Gestion de Stocks (Facade & Observer) ---");
        StockManagerFacade stockFacade = new StockManagerFacade();

        // 3. Enregistrement des observateurs
        LoggerStock logger = new LoggerStock();
        EmailNotifier emailNotifier = new EmailNotifier();

        stockFacade.ajouterObservateur(logger);
        stockFacade.ajouterObservateur(emailNotifier);

        // 4. Effectuer des opérations de stock via la façade et observer les notifications
        stockFacade.ajouterProduit("Ordinateur Portable", 10);
        stockFacade.retirerProduit("Ordinateur Portable", 8); // Devrait déclencher une alerte de stock bas
        stockFacade.passerCommandeFournisseur("Souris", 50);
        stockFacade.verifierDisponibilite("Ordinateur Portable");

        // 5. Simuler la génération d'un nouveau rapport de stock
        System.out.println("\n--- Simulation: Ajout d'un nouveau rapport de stock ---");
        rapportsAnnuels.ajouter(new Fichier("Rapport_Stock_Q1_2024.xlsx", 700));
        racine.afficherDetails("");
        System.out.println("Taille totale de la racine mise à jour : " + racine.getTaille() + " Ko");

        System.out.println("\n--- Fin de la démonstration SGIR ---");
    }
}
```


#### Explication des choix de conception

*   **Composite :**
    *   `DocumentComponent` est l'interface commune pour les feuilles (`Fichier`) et les composites (`Dossier`).
    *   `Fichier` est la feuille, représentant un élément individuel.
    *   `Dossier` est le composite, gérant une collection de `DocumentComponent` et déléguant les opérations à ses enfants.
    *   L'opération `afficherDetails` utilise une indentation pour visualiser la hiérarchie, et `getTaille` calcule la somme récursive.
    *   **Avantages :** Permet de traiter des structures arborescentes de manière uniforme, simplifiant le code client qui n'a pas à distinguer les fichiers des dossiers.

*   **Facade :**
    *   `StockManagerFacade` fournit une interface simplifiée pour interagir avec les sous-systèmes `SystemeInventaire`, `SystemeCommandes`, `SystemeFournisseurs`.
    *   Le client n'a pas besoin de connaître les détails de ces sous-systèmes ni comment ils interagissent.
    *   **Avantages :** Réduit la complexité du système pour le client, améliore le découplage entre le client et les sous-systèmes, et facilite la maintenance des sous-systèmes.

*   **Observer :**
    *   `ObservateurStock` est l'interface des observateurs.
    *   `StockManagerFacade` agit comme le sujet, maintenant une liste d'observateurs et les notifiant lors d'événements importants.
    *   `LoggerStock` et `EmailNotifier` sont des observateurs concrets qui réagissent aux notifications.
    *   **Avantages :** Permet un découplage entre le sujet et les observateurs. Le sujet n'a pas besoin de connaître les classes concrètes des observateurs, seulement leur interface. Facilite l'ajout ou la suppression d'observateurs dynamiquement.

---

## Chapitre 2 : Approches Alternatives et Améliorations

Ce chapitre explore des variations et des améliorations pour les patterns Composite, Facade et Observer, en mettant l'accent sur des détails d'implémentation et une flexibilité accrue.

### Partie 1 : Structure Documentaire (Pattern Composite - Avec Chemin et Indentation Améliorée)

Nous allons améliorer la méthode `afficherDetails` pour une meilleure visualisation et ajouter une méthode `getPath`.

#### 1. Interface `DocumentComponent` (Modifiée)


```java
// src/composite_alt/DocumentComponent.java
package composite_alt;

public interface DocumentComponent {
    String getNom();
    int getTaille();
    void afficherDetails(String indent);
    String getPath(); // Nouvelle méthode pour obtenir le chemin complet
}
```


#### 2. Classe `Fichier` (Leaf - Modifiée)


```java
// src/composite_alt/Fichier.java
package composite_alt;

public class Fichier implements DocumentComponent {
    private String nom;
    private int taille;
    private Dossier parent; // Référence au parent pour le chemin

    public Fichier(String nom, int taille, Dossier parent) {
        this.nom = nom;
        this.taille = taille;
        this.parent = parent;
    }

    @Override
    public String getNom() {
        return nom;
    }

    @Override
    public int getTaille() {
        return taille;
    }

    @Override
    public void afficherDetails(String indent) {
        System.out.println(indent + "📄 Fichier: " + nom + " (Taille: " + taille + " Ko)");
    }

    @Override
    public String getPath() {
        if (parent == null) {
            return nom;
        }
        return parent.getPath() + "/" + nom;
    }
}
```


#### 3. Classe `Dossier` (Composite - Modifiée)


```java
// src/composite_alt/Dossier.java
package composite_alt;

import java.util.ArrayList;
import java.util.List;

public class Dossier implements DocumentComponent {
    private String nom;
    private List<DocumentComponent> composants;
    private Dossier parent; // Référence au parent pour le chemin

    public Dossier(String nom) {
        this(nom, null);
    }

    public Dossier(String nom, Dossier parent) {
        this.nom = nom;
        this.composants = new ArrayList<>();
        this.parent = parent;
    }

    public void ajouter(DocumentComponent composant) {
        if (composant instanceof Fichier) {
            ((Fichier) composant).parent = this; // Met à jour le parent du fichier
        } else if (composant instanceof Dossier) {
            ((Dossier) composant).parent = this; // Met à jour le parent du dossier
        }
        composants.add(composant);
    }

    public void retirer(DocumentComponent composant) {
        composants.remove(composant);
        // En réalité, il faudrait aussi mettre à jour le parent du composant retiré à null
    }

    @Override
    public String getNom() {
        return nom;
    }

    @Override
    public int getTaille() {
        int tailleTotale = 0;
        for (DocumentComponent composant : composants) {
            tailleTotale += composant.getTaille();
        }
        return tailleTotale;
    }

    @Override
    public void afficherDetails(String indent) {
        System.out.println(indent + "📁 Dossier: " + nom + " (Taille totale: " + getTaille() + " Ko)");
        for (DocumentComponent composant : composants) {
            composant.afficherDetails(indent + "  ");
        }
    }

    @Override
    public String getPath() {
        if (parent == null) {
            return nom;
        }
        return parent.getPath() + "/" + nom;
    }
}
```


### Partie 2 : Interface de Gestion de Stocks (Pattern Facade - Avec Configuration des Sous-systèmes)

La façade peut être construite avec des sous-systèmes injectés, permettant une plus grande flexibilité et testabilité.

#### 1. Sous-systèmes Complexes (Identiques à la Solution 1)

#### 2. Classe `StockManagerFacade` (Modifiée)


```java
// src/facade_alt/StockManagerFacade.java
package facade_alt;

import facade.SystemeInventaire;
import facade.SystemeCommandes;
import facade.SystemeFournisseurs;
import observer_alt.ObservateurStock; // Import pour l'intégration Observer
import observer_alt.StockEvent; // Import pour l'événement structuré

import java.util.ArrayList;
import java.util.List;
import java.util.HashMap;
import java.util.Map;

public class StockManagerFacade {
    private SystemeInventaire inventaire;
    private SystemeCommandes commandes;
    private SystemeFournisseurs fournisseurs;

    private List<ObservateurStock> observateurs;
    private Map<String, Integer> stockActuel; // Pour simuler le stock et les seuils

    // Injection des sous-systèmes via le constructeur
    public StockManagerFacade(SystemeInventaire inventaire, SystemeCommandes commandes, SystemeFournisseurs fournisseurs) {
        this.inventaire = inventaire;
        this.commandes = commandes;
        this.fournisseurs = fournisseurs;
        this.observateurs = new ArrayList<>();
        this.stockActuel = new HashMap<>();
    }

    public void ajouterObservateur(ObservateurStock observateur) {
        observateurs.add(observateur);
    }

    public void retirerObservateur(ObservateurStock observateur) {
        observateurs.remove(observateur);
    }

    private void notifierObservateurs(StockEvent event) {
        for (ObservateurStock obs : observateurs) {
            obs.mettreAJour(event);
        }
    }

    public void ajouterProduit(String nomProduit, int quantite) {
        System.out.println("\n--- Opération : Ajouter " + quantite + " de " + nomProduit + " ---");
        inventaire.mettreAJourStock(nomProduit, quantite);
        stockActuel.put(nomProduit, stockActuel.getOrDefault(nomProduit, 0) + quantite);
        notifierObservateurs(new StockEvent("STOCK_AJOUT", nomProduit, quantite, stockActuel.get(nomProduit)));
    }

    public void retirerProduit(String nomProduit, int quantite) {
        System.out.println("\n--- Opération : Retirer " + quantite + " de " + nomProduit + " ---");
        inventaire.verifierStock(nomProduit);
        int current = stockActuel.getOrDefault(nomProduit, 0);
        if (current >= quantite) {
            inventaire.mettreAJourStock(nomProduit, -quantite);
            stockActuel.put(nomProduit, current - quantite);
            notifierObservateurs(new StockEvent("STOCK_RETRAIT", nomProduit, -quantite, stockActuel.get(nomProduit)));
            if (stockActuel.get(nomProduit) < 5) { // Seuil critique
                notifierObservateurs(new StockEvent("ALERTE_STOCK_BAS", nomProduit, 0, stockActuel.get(nomProduit)));
            }
        } else {
            System.out.println("[Facade] Erreur: Stock insuffisant pour " + nomProduit + ". Disponible: " + current);
            notifierObservateurs(new StockEvent("ERREUR_STOCK", nomProduit, -quantite, current));
        }
    }

    public void passerCommandeFournisseur(String nomProduit, int quantite) {
        System.out.println("\n--- Opération : Commander " + quantite + " de " + nomProduit + " au fournisseur ---");
        fournisseurs.contacterFournisseur(nomProduit, quantite);
        commandes.creerCommande(nomProduit, quantite);
        commandes.traiterPaiement();
        notifierObservateurs(new StockEvent("COMMANDE_FOURNISSEUR", nomProduit, quantite, 0)); // 0 car pas encore en stock
    }

    public int verifierDisponibilite(String nomProduit) {
        System.out.println("\n--- Opération : Vérifier disponibilité de " + nomProduit + " ---");
        inventaire.verifierStock(nomProduit);
        int available = stockActuel.getOrDefault(nomProduit, 0);
        System.out.println("[Facade] " + nomProduit + " disponible: " + available + " unités.");
        return available;
    }
}
```


### Partie 3 : Surveillance d'Événements (Pattern Observer - Avec Objet Événement et Observateur Supplémentaire)

Nous allons utiliser un objet `StockEvent` pour des notifications plus structurées et ajouter un `DashboardUpdater`.

#### 1. Interface `ObservateurStock` (Modifiée)


```java
// src/observer_alt/ObservateurStock.java
package observer_alt;

public interface ObservateurStock {
    void mettreAJour(StockEvent event);
}
```


#### 2. Classe `StockEvent` (Nouvelle)


```java
// src/observer_alt/StockEvent.java
package observer_alt;

public class StockEvent {
    private String type;
    private String produitNom;
    private int quantiteModifiee; // Positive pour ajout, négative pour retrait
    private int stockActuel;

    public StockEvent(String type, String produitNom, int quantiteModifiee, int stockActuel) {
        this.type = type;
        this.produitNom = produitNom;
        this.quantiteModifiee = quantiteModifiee;
        this.stockActuel = stockActuel;
    }

    public String getType() {
        return type;
    }

    public String getProduitNom() {
        return produitNom;
    }

    public int getQuantiteModifiee() {
        return quantiteModifiee;
    }

    public int getStockActuel() {
        return stockActuel;
    }

    @Override
    public String toString() {
        return "Type: " + type + ", Produit: " + produitNom + ", Qte Modifiée: " + quantiteModifiee + ", Stock Actuel: " + stockActuel;
    }
}
```


#### 3. Implémentations Concrètes d'Observateurs (Modifiées et Nouvelles)


```java
// src/observer_alt/LoggerStock.java
package observer_alt;

public class LoggerStock implements ObservateurStock {
    @Override
    public void mettreAJour(StockEvent event) {
        System.out.println("[LoggerStock] Log de l'événement : " + event.toString());
    }
}
```



```java
// src/observer_alt/EmailNotifier.java
package observer_alt;

public class EmailNotifier implements ObservateurStock {
    @Override
    public void mettreAJour(StockEvent event) {
        if (event.getType().equals("ALERTE_STOCK_BAS")) {
            System.out.println("[EmailNotifier] 📧 ALERTE : Stock bas pour " + event.getProduitNom() + ". Stock actuel: " + event.getStockActuel() + ".");
        } else if (event.getType().equals("ERREUR_STOCK")) {
            System.out.println("[EmailNotifier] 📧 ERREUR : Tentative de retrait de stock insuffisant pour " + event.getProduitNom() + ". Disponible: " + event.getStockActuel() + ".");
        } else {
            System.out.println("[EmailNotifier] 📧 Notification : " + event.getType() + " pour " + event.getProduitNom() + ".");
        }
    }
}
```



```java
// src/observer_alt/DashboardUpdater.java
package observer_alt;

public class DashboardUpdater implements ObservateurStock {
    @Override
    public void mettreAJour(StockEvent event) {
        System.out.println("[DashboardUpdater] 📊 Mise à jour du tableau de bord pour l'événement : " + event.getType() + " sur " + event.getProduitNom() + ".");
        // Logique pour mettre à jour l'UI du tableau de bord
    }
}
```


### Intégration et Démonstration Finale


```java
// src/main/SGIRAppAlt.java
package main;

import composite_alt.Dossier;
import composite_alt.Fichier;
import facade.SystemeCommandes;
import facade.SystemeFournisseurs;
import facade.SystemeInventaire;
import facade_alt.StockManagerFacade;
import observer_alt.DashboardUpdater;
import observer_alt.EmailNotifier;
import observer_alt.LoggerStock;

public class SGIRAppAlt {
    public static void main(String[] args) {
        System.out.println("--- Démarrage du Système de Gestion Intégrée de Ressources (SGIR) - Solution Alternative ---");

        // 1. Initialisation de la structure de documents (Composite)
        System.out.println("\n--- Partie 1: Gestion Documentaire (Composite Amélioré) ---");
        Dossier racine = new Dossier("SGIR_Racine");

        Dossier rapportsAnnuels = new Dossier("Rapports Annuels", racine);
        rapportsAnnuels.ajouter(new Fichier("Rapport_2022.pdf", 1500, rapportsAnnuels));
        rapportsAnnuels.ajouter(new Fichier("Rapport_2023.pdf", 1800, rapportsAnnuels));
        racine.ajouter(rapportsAnnuels);

        Dossier facturesClients = new Dossier("Factures Clients", racine);
        Dossier clientA = new Dossier("Client A", facturesClients);
        clientA.ajouter(new Fichier("Facture_A_01.pdf", 250, clientA));
        facturesClients.ajouter(clientA);
        facturesClients.ajouter(new Fichier("Facture_B_01.pdf", 300, facturesClients));
        racine.ajouter(facturesClients);

        racine.afficherDetails("");
        System.out.println("Taille totale de la racine : " + racine.getTaille() + " Ko");
        System.out.println("Chemin d'un fichier : " + ((Fichier) ((Dossier) racine.getComposants().get(0)).getComposants().get(0)).getPath());


        // 2. Initialisation de la StockManagerFacade avec injection (Facade)
        System.out.println("\n--- Partie 2 & 3: Gestion de Stocks (Facade & Observer Améliorés) ---");
        SystemeInventaire inventaire = new SystemeInventaire();
        SystemeCommandes commandes = new SystemeCommandes();
        SystemeFournisseurs fournisseurs = new SystemeFournisseurs();
        StockManagerFacade stockFacade = new StockManagerFacade(inventaire, commandes, fournisseurs);

        // 3. Enregistrement des observateurs
        LoggerStock logger = new LoggerStock();
        EmailNotifier emailNotifier = new EmailNotifier();
        DashboardUpdater dashboardUpdater = new DashboardUpdater();

        stockFacade.ajouterObservateur(logger);
        stockFacade.ajouterObservateur(emailNotifier);
        stockFacade.ajouterObservateur(dashboardUpdater);

        // 4. Effectuer des opérations de stock via la façade et observer les notifications
        stockFacade.ajouterProduit("Ordinateur Portable", 10);
        stockFacade.retirerProduit("Ordinateur Portable", 8); // Devrait déclencher une alerte de stock bas
        stockFacade.retirerProduit("Clavier", 2); // Produit non existant, devrait gérer l'erreur
        stockFacade.passerCommandeFournisseur("Souris", 50);
        stockFacade.verifierDisponibilite("Ordinateur Portable");

        // 5. Simuler la génération d'un nouveau rapport de stock
        System.out.println("\n--- Simulation: Ajout d'un nouveau rapport de stock ---");
        rapportsAnnuels.ajouter(new Fichier("Rapport_Stock_Q1_2024.xlsx", 700, rapportsAnnuels));
        racine.afficherDetails("");
        System.out.println("Taille totale de la racine mise à jour : " + racine.getTaille() + " Ko");

        System.out.println("\n--- Fin de la démonstration SGIR ---");
    }
}
```


#### Explication des choix de conception

*   **Composite (Amélioré) :**
    *   Ajout d'une référence `parent` dans `Fichier` et `Dossier` pour permettre la construction du chemin complet (`getPath()`).
    *   La méthode `ajouter` dans `Dossier` met à jour la référence `parent` des composants ajoutés.
    *   Amélioration de l'affichage avec des emojis pour une meilleure lisibilité visuelle.
    *   **Avantages :** Fournit des fonctionnalités plus riches pour la navigation et l'affichage de la structure hiérarchique.

*   **Facade (Avec Injection) :**
    *   Le constructeur de `StockManagerFacade` prend maintenant les instances des sous-systèmes en paramètres. C'est une forme d'injection de dépendances.
    *   La façade maintient un `stockActuel` simulé pour une logique de seuil plus réaliste.
    *   **Avantages :** Améliore la testabilité de la façade (on peut injecter des mocks des sous-systèmes) et rend la façade plus flexible si les implémentations des sous-systèmes changent.

*   **Observer (Avec Objet Événement et Observateur Supplémentaire) :**
    *   Introduction de la classe `StockEvent` pour encapsuler les informations de l'événement. Cela rend les notifications plus structurées, moins sujettes aux erreurs de typographie de chaînes de caractères, et plus faciles à étendre.
    *   Ajout de `DashboardUpdater` comme observateur concret, illustrant la diversité des réactions possibles à un événement.
    *   **Avantages :** Les observateurs reçoivent des données d'événement riches et typées, ce qui simplifie leur logique de traitement. Le système est plus robuste et plus facile à maintenir et à étendre avec de nouveaux types d'événements ou de nouveaux observateurs.