# Design Patterns de Création (Partie 1)  
## Singleton : Implémentation en Java/C# (Lazy initialization, Thread-safe)

Le **Singleton** garantit qu’une classe ne sera instanciée qu’une seule fois tout en fournissant un point d’accès global à cette instance. Une implémentation efficace doit être à la fois **lazy** (initialisée à la demande) et **thread-safe** (sécurisée en environnement multithread).

---

## Concept de Lazy Initialization

La lazy initialization signifie que l'objet Singleton n'est créé qu'au moment où il est demandé la première fois, optimisant les ressources et évitant des constructions inutiles.

---

## Implémentations en Java

### 1. Lazy Initialization simple (non thread-safe)

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() { }

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

**Limite :** Cette version n'est pas thread-safe. En environnement multithread, plusieurs instances peuvent être créées simultanément.

---

### 2. Thread-safe avec synchronisation

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() { }

    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

**Avantage :** Garantit qu’une seule instance sera créée même avec de multiples threads.  
**Inconvénient :** La méthode `getInstance()` est synchronisée, ce qui peut engendrer un surcoût en performance.

---

### 3. Double-checked locking (Optimisé)

```java
public class Singleton {
    private static volatile Singleton instance;

    private Singleton() { }

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized(Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

- L'utilisation de `volatile` garantit la visibilité des modifications de l'instance entre threads.  
- La double vérification minimise la synchronisation au strict nécessaire (uniquement la première création).

---

### 4. Singleton avec Holder idiom (Java)

```java
public class Singleton {
    private Singleton() { }

    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
}
```

**Avantages :**  
- Initialisation lazy garantie par la classe interne.  
- Thread-safe sans synchronisation explicite.  
- Performance optimale.

---

## Implémentations en C#

### 1. Lazy<T> : Pattern moderne thread-safe

```csharp
public sealed class Singleton {
    private static readonly Lazy<Singleton> lazy =
        new Lazy<Singleton>(() => new Singleton());

    public static Singleton Instance { get { return lazy.Value; } }

    private Singleton() { }
}
```

- `Lazy<T>` gère la synchronisation et la lazy initialization automatiquement.  
- Syntaxe concise et recommandée en versions modernes de C#.

---

### 2. Version simple thread-safe classique

```csharp
public sealed class Singleton {
    private static Singleton instance = null;
    private static readonly object padlock = new object();

    Singleton() { }

    public static Singleton Instance {
        get {
            lock(padlock) {
                if (instance == null) {
                    instance = new Singleton();
                }
                return instance;
            }
        }
    }
}
```

---

## Synthèse – diagramme Mermaid du flux d'accès Singleton (avec double-check locking)

```mermaid
flowchart TD
    Start[Appel getInstance()] --> CheckNull{instance == null ?}
    CheckNull -- No --> ReturnInstance[Retourne instance]
    CheckNull -- Yes --> Lock[Synchronise sur la classe]
    Lock --> CheckNull2{instance == null ?}
    CheckNull2 -- Yes --> CreateInstance[Crée l'instance Singleton]
    CheckNull2 --> ReturnInstance2[Retourne instance]
    CreateInstance --> ReturnInstance2
```

---

## Sources

- [Refactoring.Guru - Singleton](https://refactoring.guru/design-patterns/singleton/java/example)  
- [Microsoft Docs - Lazy Initialization](https://learn.microsoft.com/en-us/dotnet/api/system.lazy-1)  
- [Java Concurrency in Practice - Double-Checked Locking](https://jcip.net/)  
- [Wikipedia - Singleton pattern](https://en.wikipedia.org/wiki/Singleton_pattern)  

---

Ces implémentations démontrent comment allier **contrôle de l'instanciation, performance** et **sécurité en multithread**, conditions essentielles pour un Singleton efficace dans les applications modernes.