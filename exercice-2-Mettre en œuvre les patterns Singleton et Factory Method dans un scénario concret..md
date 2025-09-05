## TP : Conception Modulaire avec Singleton et Factory Method

### Objectifs du TP

À l'issue de ce TP, vous serez capable de :
*   Mettre en œuvre le pattern Singleton pour garantir une instance unique d'une classe.
*   Appliquer le pattern Factory Method pour créer des objets de manière flexible et découplée.
*   Comprendre les cas d'usage pertinents de ces deux patterns dans une architecture logicielle.

### Contexte du mini-projet

Vous travaillez sur une application de gestion qui nécessite deux fonctionnalités principales :
1.  **Gestion centralisée des logs :** Tous les événements importants de l'application (erreurs, informations, avertissements) doivent être enregistrés via un point d'accès unique, garantissant que toutes les parties de l'application écrivent dans le même flux de log.
2.  **Génération de documents divers :** L'application doit pouvoir générer différents types de documents (par exemple, des rapports au format PDF ou des lettres au format Word) sans que le code client qui demande la génération n'ait à connaître les détails spécifiques de la création de chaque type de document.

Ce TP vous guidera dans l'implémentation de ces fonctionnalités en utilisant les patterns Singleton et Factory Method.

### Prérequis

*   Connaissances de base en programmation orientée objet (classes, interfaces, héritage, polymorphisme).
*   Maîtrise d'un langage de programmation orienté objet de votre choix (Java, C#, Python, PHP, TypeScript, etc.).

---

### Partie 1 : Gestionnaire de Log (Pattern Singleton)

Le pattern Singleton est idéal pour les situations où une seule instance d'une classe est nécessaire et doit être accessible globalement. C'est le cas pour un gestionnaire de log.

#### Problématique

Comment s'assurer qu'il n'existe qu'une seule instance de notre gestionnaire de log à travers toute l'application, et que toutes les requêtes de log passent par cette instance unique ?

#### Tâches à réaliser

1.  **Définition du service de log :**
    *   Créez une classe nommée `LogManager`.
    *   Cette classe doit contenir une méthode `log(message: string, level: LogLevel)` où `LogLevel` est une énumération (ou un ensemble de constantes) avec des valeurs comme `INFO`, `WARNING`, `ERROR`.
    *   Pour simplifier, l'implémentation de `log` peut simplement afficher le message sur la console avec le niveau correspondant (ex: `[INFO] Mon message` ou `[ERROR] Une erreur est survenue`).

2.  **Implémentation du Singleton :**
    *   Modifiez la classe `LogManager` pour qu'elle respecte le pattern Singleton. Cela implique généralement :
        *   Un constructeur privé pour empêcher l'instanciation directe.
        *   Une variable statique privée pour stocker l'unique instance.
        *   Une méthode statique publique (souvent nommée `getInstance()`) qui retourne cette instance unique, la créant si elle n'existe pas encore.

3.  **Test du Singleton :**
    *   Dans votre code principal (ou une méthode de test), tentez de créer plusieurs "instances" de `LogManager` en appelant `LogManager.getInstance()` plusieurs fois.
    *   Vérifiez que toutes les références pointent vers la même instance (par exemple, en comparant les objets ou en affichant leur identifiant unique si votre langage le permet).
    *   Appelez la méthode `log` depuis différentes parties de votre application de test pour démontrer que tous les messages passent par la même instance de `LogManager`.

---

### Partie 2 : Création de Documents (Pattern Factory Method)

Le pattern Factory Method permet de déléguer la création d'objets à des sous-classes, offrant ainsi une grande flexibilité et un découplage entre le code client et les classes concrètes des produits.

#### Problématique

Comment permettre à notre application de générer différents types de documents (PDF, Word) sans que le code qui demande la génération n'ait à connaître les détails d'implémentation de `PdfDocument` ou `WordDocument` ?

#### Tâches à réaliser

1.  **Définition de l'interface/classe abstraite du produit :**
    *   Créez une interface ou une classe abstraite nommée `IDocument` (ou `Document`).
    *   Cette interface/classe doit déclarer des méthodes communes à tous les documents, par exemple : `generateContent(): string` et `save(filename: string)`.

2.  **Implémentation des produits concrets :**
    *   Créez deux classes concrètes qui implémentent `IDocument` : `PdfDocument` et `WordDocument`.
    *   Pour simplifier, les méthodes `generateContent` peuvent retourner une chaîne de caractères simple (ex: "Contenu PDF généré") et `save` peut afficher un message sur la console (ex: "Document PDF 'rapport.pdf' sauvegardé").

3.  **Définition du créateur abstrait (Factory) :**
    *   Créez une classe abstraite nommée `DocumentCreator`.
    *   Cette classe doit déclarer une méthode abstraite `createDocument(): IDocument`. C'est la "Factory Method".
    *   Ajoutez une méthode concrète `generateAndSave(filename: string)` dans `DocumentCreator`. Cette méthode utilisera `createDocument()` pour obtenir un document, puis appellera `generateContent()` et `save(filename)` sur ce document. C'est le "template method" qui utilise la factory method.

4.  **Implémentation des créateurs concrets :**
    *   Créez deux classes concrètes qui héritent de `DocumentCreator` : `PdfCreator` et `WordCreator`.
    *   Chaque créateur concret doit implémenter la méthode `createDocument()` pour retourner l'instance du document correspondant (`PdfDocument` pour `PdfCreator`, `WordDocument` pour `WordCreator`).

5.  **Test de la Factory Method :**
    *   Dans votre code principal, créez des instances de `PdfCreator` et `WordCreator`.
    *   Utilisez la méthode `generateAndSave()` de chaque créateur pour générer et sauvegarder différents types de documents.
    *   Observez comment le code client n'a pas besoin de savoir *comment* un `PdfDocument` ou `WordDocument` est créé, seulement *quel* créateur utiliser.

---

### Partie 3 : Intégration et Réflexion

1.  **Intégration des patterns :**
    *   Modifiez les méthodes `generateAndSave` de vos créateurs concrets (`PdfCreator`, `WordCreator`) pour qu'elles utilisent le `LogManager` (Singleton) afin d'enregistrer les événements de création de documents.
    *   Exemple : `LogManager.getInstance().log('Document PDF créé : ' + filename, LogLevel.INFO);`

2.  **Réflexion (à préparer pour la présentation) :**
    *   Quels sont les avantages du Singleton dans le cas du gestionnaire de log ?
    *   Quels sont les avantages de la Factory Method pour la création de documents ?
    *   Comment ces patterns contribuent-ils à la modularité et à la maintenabilité de l'application ?
    *   Dans quels autres scénarios pourriez-vous envisager d'utiliser ces patterns ?

---

### Consignes Générales et Conseils

*   **Liberté de langage :** Choisissez le langage de programmation orientée objet avec lequel vous êtes le plus à l'aise.
*   **Utilisation de l'IA :** N'hésitez pas à utiliser des outils d'IA (ChatGPT, Copilot, etc.) pour vous aider dans la rédaction du code, la compréhension des concepts ou la recherche de solutions. L'objectif est d'apprendre et de comprendre, pas de réinventer la roue. Assurez-vous de *comprendre* le code généré et de pouvoir l'expliquer. Ne vous contentez pas d'un copier-coller aveugle.
*   **Clarté du code :** Votre code doit être lisible, bien structuré, et si nécessaire, commenté pour expliquer vos choix d'implémentation.
*   **Tests :** Assurez-vous que vos implémentations fonctionnent comme prévu et démontrent clairement l'application de chaque pattern.

### Rendu

Préparez-vous à présenter votre code et à expliquer vos choix d'implémentation. Vous devrez notamment démontrer le fonctionnement de chaque pattern et discuter des points de réflexion mentionnés ci-dessus.