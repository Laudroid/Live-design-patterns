# Design Patterns de Création (Partie 2)  
## Builder : Implémentation du "Fluent API" (méthodes chaînées)

Le pattern **Builder** facilite la construction d’objets complexes et lorsqu’il est combiné avec une **implémentation fluide** (Fluent API), il permet d’écrire du code clair, lisible et expressif grâce à des méthodes chaînées.

---

## Qu’est-ce que le Fluent API dans le Builder ?

Un Fluent API est une technique qui consiste à retourner une référence à l’instance courante (`this`) depuis chaque méthode de configuration du builder. Cela permet de chaîner les appels de méthodes, rendant le processus de construction très naturel, proche du langage naturel.

---

## Avantages du Fluent Builder

- **Lisibilité améliorée** : le code ressemble à un « mini-langage » spécifique à la construction de l’objet.  
- **Facilité d’utilisation** : on peut configurer l’objet en une seule expression sans utiliser plusieurs appels séparés.  
- **Immutabilité souvent prise en charge** : permet de construire des objets immuables avec des setters encapsulés dans le builder.

---

## Exemple simple en Java

### Classe produit

```java
public class Computer {
    private String CPU;
    private int RAM;
    private int storage;

    private Computer() {}  // Constructeur privé

    public static class Builder {
        private String CPU;
        private int RAM;
        private int storage;

        public Builder withCPU(String CPU) {
            this.CPU = CPU;
            return this;
        }

        public Builder withRAM(int RAM) {
            this.RAM = RAM;
            return this;
        }

        public Builder withStorage(int storage) {
            this.storage = storage;
            return this;
        }

        public Computer build() {
            Computer computer = new Computer();
            computer.CPU = this.CPU;
            computer.RAM = this.RAM;
            computer.storage = this.storage;
            return computer;
        }
    }

    @Override
    public String toString() {
        return "Computer [CPU=" + CPU + ", RAM=" + RAM + "GB, Storage=" + storage + "GB]";
    }
}
```

### Utilisation

```java
public class Client {
    public static void main(String[] args) {
        Computer myPC = new Computer.Builder()
            .withCPU("Intel i7")
            .withRAM(16)
            .withStorage(512)
            .build();

        System.out.println(myPC);
    }
}
```

Le code est expressif, simple à lire et évite la confusion habituelle des multiples arguments dans le constructeur.

---

## Exemple

```mermaid
classDiagram
    class Computer {
        - CPU : String
        - RAM : int
        - storage : int
        + toString() : String
        class Builder {
            - CPU : String
            - RAM : int
            - storage : int
            + withCPU(String) Builder
            + withRAM(int) Builder
            + withStorage(int) Builder
            + build() Computer
        }
    }
    Computer "1" o-- "1" Computer.Builder : contains
```

---

## Points importants pour un Fluent Builder

- Chaque méthode de configuration retourne `this` (le builder lui-même).  
- L’objet final est construit via une méthode dédiée `build()`.  
- Le constructeur de l’objet final est généralement privé ou package-private pour forcer l’utilisation du builder.  
- La méthode `build()` peut vérifier la validité des paramètres avant la création.

---

## Sources

- [Refactoring.Guru – Builder Pattern](https://refactoring.guru/design-patterns/builder)  
- [Effective Java, 3rd Edition, Joshua Bloch](https://www.oreilly.com/library/view/effective-java-3rd/9780134686097/) (chapitre sur le pattern Builder et Fluent API)  
- [Wikipedia – Builder pattern](https://en.wikipedia.org/wiki/Builder_pattern)  

---

Le Builder avec Fluent API améliore la construction d’objets complexes en rendant le code plus lisible, maintenable et sans risque d’erreurs liées aux nombreux paramètres dans les constructeurs classiques.