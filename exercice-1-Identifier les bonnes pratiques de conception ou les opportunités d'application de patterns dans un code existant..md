Voici un sujet de TP conçu pour atteindre vos objectifs, en tenant compte de l'utilisation de l'IA par les apprenants.

---

## TP : Analyse et Refactorisation de Code Existant – Opportunités de Design Patterns

### Objectif du TP

Ce TP vise à développer votre capacité à :
*   Identifier les "code smells" et les problèmes de conception dans un code existant.
*   Reconnaître les opportunités d'application de Design Patterns pour améliorer la robustesse, la maintenabilité et l'extensibilité du code.
*   Discuter et justifier collectivement les choix de refactorisation.

### Contexte

Vous travaillez sur une application de gestion de commandes en ligne. Une partie du code gère l'application de différentes promotions en fonction de critères spécifiques. Ce module a été développé rapidement et commence à montrer des signes de rigidité à chaque nouvelle règle promotionnelle.

Votre mission est d'analyser ce code, d'en discuter les limites et de proposer des améliorations en vous appuyant sur les Design Patterns.

### Code à Analyser

Considérez le code Java suivant pour la gestion des promotions :

```java
import java.util.ArrayList;
import java.util.List;

// Classe représentant un article dans une commande
class Item {
    String name;
    double price;
    int quantity;
    String category;

    public Item(String name, double price, int quantity, String category) {
        this.name = name;
        this.price = price;
        this.quantity = quantity;
        this.category = category;
    }

    public String getName() { return name; }
    public double getPrice() { return price; }
    public int getQuantity() { return quantity; }
    public String getCategory() { return category; }

    public double getTotalItemPrice() {
        return price * quantity;
    }
}

// Classe représentant une commande
class Order {
    List<Item> items;
    String customerType; // Ex: "REGULAR", "PREMIUM", "VIP"
    boolean firstOrder;

    public Order(String customerType, boolean firstOrder) {
        this.items = new ArrayList<>();
        this.customerType = customerType;
        this.firstOrder = firstOrder;
    }

    public void addItem(Item item) {
        this.items.add(item);
    }

    public List<Item> getItems() { return items; }
    public String getCustomerType() { return customerType; }
    public boolean isFirstOrder() { return firstOrder; }

    public double getTotalAmount() {
        return items.stream().mapToDouble(Item::getTotalItemPrice).sum();
    }
}

// Classe de gestion des promotions
class PromotionManager {

    public double applyPromotions(Order order) {
        double finalAmount = order.getTotalAmount();

        // Règle 1: Réduction pour les clients VIP
        if ("VIP".equals(order.getCustomerType())) {
            finalAmount *= 0.85; // 15% de réduction
            System.out.println("Promotion VIP appliquée.");
        }
        // Règle 2: Réduction pour la première commande
        else if (order.isFirstOrder()) {
            finalAmount -= 10.0; // 10€ de réduction
            System.out.println("Promotion Première Commande appliquée.");
        }
        // Règle 3: Réduction sur les articles de catégorie "ELECTRONICS" si le montant total dépasse 200€
        else if (order.getTotalAmount() > 200.0 && order.getItems().stream().anyMatch(item -> "ELECTRONICS".equals(item.getCategory()))) {
            finalAmount *= 0.90; // 10% de réduction
            System.out.println("Promotion Électronique appliquée.");
        }
        // Règle 4: Réduction pour les clients PREMIUM si le montant total dépasse 100€
        else if ("PREMIUM".equals(order.getCustomerType()) && order.getTotalAmount() > 100.0) {
            finalAmount -= 5.0; // 5€ de réduction
            System.out.println("Promotion Premium appliquée.");
        }
        // Règle 5: Réduction générale si aucune autre promotion n'a été appliquée et que le montant est supérieur à 50€
        else if (finalAmount > 50.0) {
            finalAmount *= 0.95; // 5% de réduction
            System.out.println("Promotion Générale appliquée.");
        }
        // ... et d'autres règles sont ajoutées régulièrement ...

        return finalAmount;
    }

    public static void main(String[] args) {
        PromotionManager manager = new PromotionManager();

        // Exemple 1: Client VIP
        Order order1 = new Order("VIP", false);
        order1.addItem(new Item("Laptop", 1200.0, 1, "ELECTRONICS"));
        System.out.println("Montant initial Order 1: " + order1.getTotalAmount());
        System.out.println("Montant final Order 1: " + manager.applyPromotions(order1) + "\n");

        // Exemple 2: Première commande
        Order order2 = new Order("REGULAR", true);
        order2.addItem(new Item("Book", 25.0, 2, "BOOKS"));
        System.out.println("Montant initial Order 2: " + order2.getTotalAmount());
        System.out.println("Montant final Order 2: " + manager.applyPromotions(order2) + "\n");

        // Exemple 3: Électronique > 200€
        Order order3 = new Order("REGULAR", false);
        order3.addItem(new Item("Smartphone", 300.0, 1, "ELECTRONICS"));
        System.out.println("Montant initial Order 3: " + order3.getTotalAmount());
        System.out.println("Montant final Order 3: " + manager.applyPromotions(order3) + "\n");

        // Exemple 4: Client PREMIUM > 100€
        Order order4 = new Order("PREMIUM", false);
        order4.addItem(new Item("Headphones", 80.0, 1, "AUDIO"));
        order4.addItem(new Item("Keyboard", 30.0, 1, "PERIPHERALS"));
        System.out.println("Montant initial Order 4: " + order4.getTotalAmount());
        System.out.println("Montant final Order 4: " + manager.applyPromotions(order4) + "\n");

        // Exemple 5: Promotion Générale
        Order order5 = new Order("REGULAR", false);
        order5.addItem(new Item("T-Shirt", 30.0, 2, "CLOTHING"));
        System.out.println("Montant initial Order 5: " + order5.getTotalAmount());
        System.out.println("Montant final Order 5: " + manager.applyPromotions(order5) + "\n");
    }
}
```

### Questions pour l'Analyse et la Discussion

En groupe, analysez le code `PromotionManager` et discutez des points suivants :

1.  **Identification des Problèmes :**
    *   Quels sont les "code smells" ou les problèmes de conception que vous identifiez dans la méthode `applyPromotions` ? (Pensez aux principes SOLID, à la maintenabilité, à la testabilité, etc.)
    *   Quelles difficultés rencontreriez-vous si vous deviez ajouter une nouvelle règle promotionnelle ou modifier une règle existante ?

2.  **Opportunités de Design Patterns :**
    *   Quels Design Patterns de conception pourraient être pertinents pour améliorer la structure et le comportement de ce module ? Justifiez vos choix.
    *   Y a-t-il plusieurs patterns possibles ? Si oui, quels sont les avantages et inconvénients de chacun dans ce contexte ?

3.  **Proposition de Refactorisation :**
    *   Proposez une refactorisation concrète du code en appliquant le ou les patterns choisis.
    *   Décrivez les nouvelles classes/interfaces que vous introduiriez et comment elles interagiraient. (Un pseudo-code ou une description architecturale est suffisant, pas besoin d'implémenter tout le code si le temps est limité).

4.  **Avantages de la Nouvelle Conception :**
    *   Comment votre proposition résout-elle les problèmes identifiés à la question 1 ?
    *   Comment la nouvelle structure facilite-t-elle l'ajout de nouvelles règles promotionnelles ou la modification de règles existantes ?

5.  **Limites et Compromis :**
    *   Y a-t-il des inconvénients ou des compromis à votre nouvelle conception (par exemple, complexité accrue pour des cas très simples, performance, etc.) ?

### Consignes pour l'Utilisation de l'IA

L'utilisation d'outils d'IA (ChatGPT, Copilot, etc.) est autorisée et même encouragée pour ce TP. Cependant, gardez à l'esprit que l'IA est un assistant, pas un remplaçant de votre réflexion critique.

*   **Utilisez l'IA pour :**
    *   **Brainstorming :** Demandez-lui des idées de patterns pour un problème donné.
    *   **Explication de concepts :** Si un pattern n'est pas clair, demandez-lui des explications ou des exemples.
    *   **Génération de code :** Une fois que vous avez une idée claire de la solution, demandez-lui de générer un squelette de code pour vous aider à démarrer.
    *   **Analyse initiale :** Vous pouvez lui demander d'identifier des "code smells" dans le code fourni.

*   **Ne vous contentez pas de :**
    *   **Copier-coller aveuglément :** L'objectif est de comprendre et de justifier vos choix. Le code généré par l'IA doit être compris, critiqué et adapté par vous.
    *   **Laisser l'IA faire tout le travail :** La discussion de groupe et la justification de vos décisions sont au cœur de ce TP.

*   **Soyez critique :** Les réponses de l'IA ne sont pas toujours parfaites ou optimales. Discutez de ses suggestions en groupe et évaluez leur pertinence.

### Livrables

À la fin du TP, chaque groupe devra présenter :

1.  Un résumé des problèmes identifiés dans le code initial.
2.  Le ou les Design Patterns choisis, avec une justification claire.
3.  Une description architecturale (diagrammes simples, pseudo-code ou description textuelle) de la refactorisation proposée.
4.  Une discussion des avantages et inconvénients de la nouvelle conception, y compris la manière dont elle gère l'extensibilité.
5.  Un bref retour sur la manière dont l'IA a été utilisée par le groupe, et si elle a été utile ou non pour certaines étapes.

Bon travail à toutes et à tous !