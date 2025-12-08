Voici deux solutions possibles pour refactoriser le code `PromotionManager`, chacune présentée comme un chapitre distinct.

---

## Chapitre 1 : Solution via le Pattern Chain of Responsibility

### 1. Identification des Problèmes

Le code `PromotionManager` initial présente plusieurs "code smells" et problèmes de conception :

*   **Long Method (`applyPromotions`):** La méthode est excessivement longue et contient une logique complexe avec de multiples conditions imbriquées (`if-else if`).
*   **Violation du Principe Ouvert/Fermé (Open/Closed Principle - OCP):** Toute modification ou ajout d'une nouvelle règle promotionnelle nécessite une modification directe de la méthode `applyPromotions`, rendant le code rigide et difficile à maintenir.
*   **Violation du Principe de Responsabilité Unique (Single Responsibility Principle - SRP):** La méthode `applyPromotions` est responsable à la fois de la détermination des conditions d'application et de l'application de la réduction, regroupant des responsabilités distinctes.
*   **Faible Testabilité:** Tester chaque règle promotionnelle individuellement est difficile sans exécuter l'ensemble de la logique de la méthode.
*   **Difficulté d'Évolution:** L'ajout de nouvelles règles devient de plus en plus complexe et risqué à mesure que le nombre de conditions augmente.

### 2. Opportunités de Design Patterns : Chain of Responsibility

Le pattern **Chain of Responsibility** est particulièrement adapté pour gérer une séquence de règles où une seule règle doit être appliquée ou où l'ordre d'application est important. Il permet de découpler l'expéditeur d'une requête de ses récepteurs en donnant à plusieurs objets la possibilité de traiter la requête. La requête est passée le long d'une chaîne d'objets jusqu'à ce qu'un objet la traite. Chaque maillon de la chaîne représente une règle promotionnelle.

*   **Avantages :**
    *   **Découplage :** Chaque règle promotionnelle est encapsulée dans une classe distincte, indépendante des autres.
    *   **Extensibilité :** L'ajout de nouvelles règles se fait en créant de nouveaux maillons et en les insérant dans la chaîne, sans modifier le code existant des autres règles ou du gestionnaire.
    *   **Gestion de l'Ordre :** Le pattern modélise naturellement l'ordre d'application des règles, reproduisant le comportement `if-else if` original où seule la première règle correspondante est appliquée.
    *   **Testabilité :** Chaque maillon de la chaîne peut être testé isolément.

*   **Inconvénients :**
    *   **Complexité Initiale :** Introduit plus de classes, ce qui peut sembler une sur-ingénierie pour un très petit nombre de règles.
    *   **Débogage :** Le flux d'exécution peut être plus difficile à suivre car il passe par plusieurs objets.

### 3. Proposition de Refactorisation

Nous allons introduire une interface `PromotionHandler` et une classe abstraite `AbstractPromotionHandler` pour faciliter l'implémentation. Chaque règle promotionnelle sera une implémentation concrète de `PromotionHandler`. Le `NewPromotionManager` sera responsable de la construction de la chaîne.


```java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

// Classes existantes (inchangées)
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

class Order {
    List<Item> items;
    String customerType;
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

// --- Début de la refactorisation avec Chain of Responsibility ---

// Interface pour un gestionnaire de promotion
interface PromotionHandler {
    void setNextHandler(PromotionHandler nextHandler);
    double applyPromotion(Order order, double currentAmount);
}

// Classe abstraite de base pour les gestionnaires de promotion
abstract class AbstractPromotionHandler implements PromotionHandler {
    protected PromotionHandler nextHandler;

    @Override
    public void setNextHandler(PromotionHandler nextHandler) {
        this.nextHandler = nextHandler;
    }

    // Méthode pour passer la requête au gestionnaire suivant dans la chaîne
    protected double handleNext(Order order, double currentAmount) {
        if (nextHandler != null) {
            return nextHandler.applyPromotion(order, currentAmount);
        }
        return currentAmount; // Fin de la chaîne, aucune autre promotion applicable
    }
}

// Implémentation pour la promotion VIP
class VipPromotionHandler extends AbstractPromotionHandler {
    @Override
    public double applyPromotion(Order order, double currentAmount) {
        if ("VIP".equals(order.getCustomerType())) {
            System.out.println("Promotion VIP appliquée.");
            return currentAmount * 0.85; // Applique la promotion et arrête la chaîne
        }
        return handleNext(order, currentAmount); // Passe au gestionnaire suivant
    }
}

// Implémentation pour la promotion Première Commande
class FirstOrderPromotionHandler extends AbstractPromotionHandler {
    @Override
    public double applyPromotion(Order order, double currentAmount) {
        if (order.isFirstOrder()) {
            System.out.println("Promotion Première Commande appliquée.");
            return currentAmount - 10.0;
        }
        return handleNext(order, currentAmount);
    }
}

// Implémentation pour la promotion Électronique
class ElectronicsPromotionHandler extends AbstractPromotionHandler {
    @Override
    public double applyPromotion(Order order, double currentAmount) {
        if (order.getTotalAmount() > 200.0 && order.getItems().stream().anyMatch(item -> "ELECTRONICS".equals(item.getCategory()))) {
            System.out.println("Promotion Électronique appliquée.");
            return currentAmount * 0.90;
        }
        return handleNext(order, currentAmount);
    }
}

// Implémentation pour la promotion Premium
class PremiumPromotionHandler extends AbstractPromotionHandler {
    @Override
    public double applyPromotion(Order order, double currentAmount) {
        if ("PREMIUM".equals(order.getCustomerType()) && order.getTotalAmount() > 100.0) {
            System.out.println("Promotion Premium appliquée.");
            return currentAmount - 5.0;
        }
        return handleNext(order, currentAmount);
    }
}

// Implémentation pour la promotion Générale
class GeneralPromotionHandler extends AbstractPromotionHandler {
    @Override
    public double applyPromotion(Order order, double currentAmount) {
        // Si cette promotion est atteinte, c'est qu'aucune précédente n'a été appliquée.
        if (currentAmount > 50.0) {
            System.out.println("Promotion Générale appliquée.");
            return currentAmount * 0.95;
        }
        return handleNext(order, currentAmount); // Ou simplement currentAmount si c'est la fin de la chaîne
    }
}

// Nouveau PromotionManager qui construit et utilise la chaîne
class NewPromotionManager {
    private PromotionHandler chain;

    public NewPromotionManager() {
        // Construire la chaîne dans l'ordre de priorité défini par les règles initiales
        PromotionHandler vip = new VipPromotionHandler();
        PromotionHandler firstOrder = new FirstOrderPromotionHandler();
        PromotionHandler electronics = new ElectronicsPromotionHandler();
        PromotionHandler premium = new PremiumPromotionHandler();
        PromotionHandler general = new GeneralPromotionHandler();

        vip.setNextHandler(firstOrder);
        firstOrder.setNextHandler(electronics);
        electronics.setNextHandler(premium);
        premium.setNextHandler(general);
        // La dernière promotion (general) n'a pas de suivant, elle termine la chaîne.

        this.chain = vip; // Le début de la chaîne
    }

    public double applyPromotions(Order order) {
        double initialAmount = order.getTotalAmount();
        return chain.applyPromotion(order, initialAmount);
    }

    public static void main(String[] args) {
        NewPromotionManager manager = new NewPromotionManager();

        // Exemples de test (identiques à l'original)
        Order order1 = new Order("VIP", false);
        order1.addItem(new Item("Laptop", 1200.0, 1, "ELECTRONICS"));
        System.out.println("Montant initial Order 1: " + order1.getTotalAmount());
        System.out.println("Montant final Order 1: " + manager.applyPromotions(order1) + "\n");

        Order order2 = new Order("REGULAR", true);
        order2.addItem(new Item("Book", 25.0, 2, "BOOKS"));
        System.out.println("Montant initial Order 2: " + order2.getTotalAmount());
        System.out.println("Montant final Order 2: " + manager.applyPromotions(order2) + "\n");

        Order order3 = new Order("REGULAR", false);
        order3.addItem(new Item("Smartphone", 300.0, 1, "ELECTRONICS"));
        System.out.println("Montant initial Order 3: " + order3.getTotalAmount());
        System.out.println("Montant final Order 3: " + manager.applyPromotions(order3) + "\n");

        Order order4 = new Order("PREMIUM", false);
        order4.addItem(new Item("Headphones", 80.0, 1, "AUDIO"));
        order4.addItem(new Item("Keyboard", 30.0, 1, "PERIPHERALS"));
        System.out.println("Montant initial Order 4: " + order4.getTotalAmount());
        System.out.println("Montant final Order 4: " + manager.applyPromotions(order4) + "\n");

        Order order5 = new Order("REGULAR", false);
        order5.addItem(new Item("T-Shirt", 30.0, 2, "CLOTHING"));
        System.out.println("Montant initial Order 5: " + order5.getTotalAmount());
        System.out.println("Montant final Order 5: " + manager.applyPromotions(order5) + "\n");
    }
}
```


### 4. Avantages de la Nouvelle Conception

*   **Résolution des Problèmes :**
    *   La méthode `applyPromotions` du `NewPromotionManager` est désormais concise et se contente de déléguer la requête au premier maillon de la chaîne.
    *   Le Principe Ouvert/Fermé est respecté : l'ajout d'une nouvelle règle se fait en créant une nouvelle classe `PromotionHandler` et en l'insérant dans la chaîne, sans modifier les classes existantes.
    *   Le Principe de Responsabilité Unique est respecté : chaque classe `PromotionHandler` est responsable d'une seule règle promotionnelle (vérification de la condition et application).
    *   La testabilité est améliorée car chaque `PromotionHandler` peut être testé indépendamment.

*   **Facilité d'Évolution :**
    *   Pour ajouter une nouvelle règle : créer une nouvelle classe `XyzPromotionHandler` implémentant `PromotionHandler` et l'insérer à l'endroit approprié dans la chaîne lors de sa construction.
    *   Pour modifier une règle existante : modifier uniquement la classe `PromotionHandler` concernée.
    *   Pour supprimer une règle : retirer son `PromotionHandler` de la chaîne.

### 5. Limites et Compromis

*   **Complexité pour les Cas Simples :** Pour un très petit nombre de règles, l'introduction de plusieurs classes peut sembler excessive.
*   **Gestion de l'Ordre :** L'ordre des maillons dans la chaîne est crucial et doit être géré explicitement lors de la construction de la chaîne. Une modification de l'ordre nécessite une modification dans la construction de la chaîne.

---

## Chapitre 2 : Solution via le Pattern Strategy avec un Sélecteur de Stratégie

### 1. Identification des Problèmes

Les problèmes identifiés dans le code `PromotionManager` initial sont les mêmes que pour la solution précédente :

*   **Long Method (`applyPromotions`):** La méthode est trop longue et contient une logique complexe avec de multiples conditions imbriquées.
*   **Violation du Principe Ouvert/Fermé (OCP):** L'ajout ou la modification d'une règle nécessite une modification directe de la méthode.
*   **Violation du Principe de Responsabilité Unique (SRP):** La méthode gère à la fois la logique de sélection et d'application des promotions.
*   **Faible Testabilité:** Difficile de tester les règles individuellement.
*   **Difficulté d'Évolution:** L'ajout de nouvelles règles est risqué et complexe.

### 2. Opportunités de Design Patterns : Strategy Pattern avec un Sélecteur

Le pattern **Strategy** permet de définir une famille d'algorithmes, de les encapsuler chacun dans une classe distincte, et de les rendre interchangeables. Il permet à l'algorithme de varier indépendamment des clients qui l'utilisent. Ici, chaque règle promotionnelle est un algorithme (une stratégie).

Pour gérer le comportement `if-else if` (où seule la première promotion applicable est appliquée), nous combinerons le Strategy Pattern avec un mécanisme de sélection ou un "Promotion Engine" qui itérera sur une liste de stratégies ordonnées et appliquera la première qui correspond.

*   **Avantages :**
    *   **Découplage :** Chaque règle promotionnelle est isolée dans sa propre classe, améliorant la clarté et la maintenabilité.
    *   **Extensibilité :** L'ajout de nouvelles promotions se fait en créant de nouvelles classes de stratégie et en les ajoutant à la liste du sélecteur, sans modifier le code existant.
    *   **Testabilité :** Chaque stratégie peut être testée indépendamment.
    *   **Flexibilité :** Le sélecteur peut être configuré pour appliquer la première stratégie, toutes les stratégies applicables, ou des stratégies basées sur des critères plus complexes.

*   **Inconvénients :**
    *   **Complexité Accrue :** Introduit plus de classes pour chaque promotion (une interface, plusieurs implémentations concrètes).
    *   **Gestion Externe de l'Ordre :** L'ordre dans lequel les stratégies sont présentées au sélecteur est crucial si seule la première promotion applicable doit être choisie.

### 3. Proposition de Refactorisation

Nous allons introduire une interface `PromotionStrategy` avec des méthodes pour vérifier l'applicabilité et appliquer la promotion. Un `NewPromotionManagerWithStrategy` maintiendra une liste ordonnée de ces stratégies.


```java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

// Classes existantes (inchangées)
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

class Order {
    List<Item> items;
    String customerType;
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

// --- Début de la refactorisation avec Strategy Pattern ---

// Interface pour une stratégie de promotion
interface PromotionStrategy {
    boolean isApplicable(Order order);
    double apply(Order order, double currentAmount);
    String getName(); // Pour l'affichage du nom de la promotion
}

// Implémentation pour la promotion VIP
class VipPromotionStrategy implements PromotionStrategy {
    @Override
    public boolean isApplicable(Order order) {
        return "VIP".equals(order.getCustomerType());
    }

    @Override
    public double apply(Order order, double currentAmount) {
        return currentAmount * 0.85;
    }

    @Override
    public String getName() {
        return "Promotion VIP";
    }
}

// Implémentation pour la promotion Première Commande
class FirstOrderPromotionStrategy implements PromotionStrategy {
    @Override
    public boolean isApplicable(Order order) {
        return order.isFirstOrder();
    }

    @Override
    public double apply(Order order, double currentAmount) {
        return currentAmount - 10.0;
    }

    @Override
    public String getName() {
        return "Promotion Première Commande";
    }
}

// Implémentation pour la promotion Électronique
class ElectronicsPromotionStrategy implements PromotionStrategy {
    @Override
    public boolean isApplicable(Order order) {
        return order.getTotalAmount() > 200.0 && order.getItems().stream().anyMatch(item -> "ELECTRONICS".equals(item.getCategory()));
    }

    @Override
    public double apply(Order order, double currentAmount) {
        return currentAmount * 0.90;
    }

    @Override
    public String getName() {
        return "Promotion Électronique";
    }
}

// Implémentation pour la promotion Premium
class PremiumPromotionStrategy implements PromotionStrategy {
    @Override
    public boolean isApplicable(Order order) {
        return "PREMIUM".equals(order.getCustomerType()) && order.getTotalAmount() > 100.0;
    }

    @Override
    public double apply(Order order, double currentAmount) {
        return currentAmount - 5.0;
    }

    @Override
    public String getName() {
        return "Promotion Premium";
    }
}

// Implémentation pour la promotion Générale
class GeneralPromotionStrategy implements PromotionStrategy {
    @Override
    public boolean isApplicable(Order order) {
        // Cette stratégie sera évaluée si les précédentes n'étaient pas applicables.
        return order.getTotalAmount() > 50.0;
    }

    @Override
    public double apply(Order order, double currentAmount) {
        return currentAmount * 0.95;
    }

    @Override
    public String getName() {
        return "Promotion Générale";
    }
}

// Nouveau PromotionManager qui utilise les stratégies
class NewPromotionManagerWithStrategy {
    private List<PromotionStrategy> strategies;

    public NewPromotionManagerWithStrategy() {
        this.strategies = new ArrayList<>();
        // Ajouter les stratégies dans l'ordre de priorité souhaité
        strategies.add(new VipPromotionStrategy());
        strategies.add(new FirstOrderPromotionStrategy());
        strategies.add(new ElectronicsPromotionStrategy());
        strategies.add(new PremiumPromotionStrategy());
        strategies.add(new GeneralPromotionStrategy());
    }

    public double applyPromotions(Order order) {
        double finalAmount = order.getTotalAmount();
        for (PromotionStrategy strategy : strategies) {
            if (strategy.isApplicable(order)) {
                finalAmount = strategy.apply(order, finalAmount);
                System.out.println(strategy.getName() + " appliquée.");
                break; // Applique la première promotion trouvée et s'arrête (comportement if-else if)
            }
        }
        return finalAmount;
    }

    public static void main(String[] args) {
        NewPromotionManagerWithStrategy manager = new NewPromotionManagerWithStrategy();

        // Exemples de test (identiques à l'original)
        Order order1 = new Order("VIP", false);
        order1.addItem(new Item("Laptop", 1200.0, 1, "ELECTRONICS"));
        System.out.println("Montant initial Order 1: " + order1.getTotalAmount());
        System.out.println("Montant final Order 1: " + manager.applyPromotions(order1) + "\n");

        Order order2 = new Order("REGULAR", true);
        order2.addItem(new Item("Book", 25.0, 2, "BOOKS"));
        System.out.println("Montant initial Order 2: " + order2.getTotalAmount());
        System.out.println("Montant final Order 2: " + manager.applyPromotions(order2) + "\n");

        Order order3 = new Order("REGULAR", false);
        order3.addItem(new Item("Smartphone", 300.0, 1, "ELECTRONICS"));
        System.out.println("Montant initial Order 3: " + order3.getTotalAmount());
        System.out.println("Montant final Order 3: " + manager.applyPromotions(order3) + "\n");

        Order order4 = new Order("PREMIUM", false);
        order4.addItem(new Item("Headphones", 80.0, 1, "AUDIO"));
        order4.addItem(new Item("Keyboard", 30.0, 1, "PERIPHERALS"));
        System.out.println("Montant initial Order 4: " + order4.getTotalAmount());
        System.out.println("Montant final Order 4: " + manager.applyPromotions(order4) + "\n");

        Order order5 = new Order("REGULAR", false);
        order5.addItem(new Item("T-Shirt", 30.0, 2, "CLOTHING"));
        System.out.println("Montant initial Order 5: " + order5.getTotalAmount());
        System.out.println("Montant final Order 5: " + manager.applyPromotions(order5) + "\n");
    }
}
```


### 4. Avantages de la Nouvelle Conception

*   **Résolution des Problèmes :**
    *   La méthode `applyPromotions` du `NewPromotionManagerWithStrategy` est courte et se concentre sur l'orchestration des stratégies.
    *   Le Principe Ouvert/Fermé est respecté : l'ajout d'une nouvelle règle se fait en créant une nouvelle classe `PromotionStrategy` et en l'ajoutant à la liste des stratégies, sans modifier les classes existantes.
    *   Le Principe de Responsabilité Unique est respecté : chaque classe `PromotionStrategy` est responsable d'une seule règle promotionnelle (vérification de la condition et application).
    *   La testabilité est améliorée car chaque `PromotionStrategy` peut être testée indépendamment.

*   **Facilité d'Évolution :**
    *   Pour ajouter une nouvelle règle : créer une nouvelle classe `XyzPromotionStrategy`, l'implémenter, et l'ajouter à la liste `strategies` dans le `NewPromotionManagerWithStrategy` à la position souhaitée pour gérer la priorité.
    *   Pour modifier une règle existante : modifier uniquement la classe `PromotionStrategy` concernée.
    *   Pour supprimer une règle : retirer sa `PromotionStrategy` de la liste.

### 5. Limites et Compromis

*   **Gestion Externe de l'Ordre :** L'ordre des stratégies dans la liste est critique pour reproduire le comportement `if-else if` et doit être maintenu manuellement lors de l'initialisation.
*   **Redondance `isApplicable` :** Chaque stratégie doit implémenter `isApplicable`, ce qui peut sembler redondant si la logique de condition est très simple.
*   **Complexité pour les Cas Simples :** Comme pour la chaîne de responsabilité, l'introduction de plusieurs classes peut être perçue comme une sur-ingénierie pour un très petit nombre de règles.