Chers apprenants,

Ce TP a pour objectif de vous faire pratiquer deux patterns de conception fondamentaux : **Adapter** et **Decorator**. Vous les appliquerez dans un contexte de mini-projet simulant des défis courants en développement logiciel : l'intégration de systèmes existants et l'extension de fonctionnalités.

---

## TP : Intégration et Extension de Fonctionnalités avec Adapter et Decorator

### Objectifs Pédagogiques

À la fin de ce TP, vous serez capable de :
1.  Comprendre et appliquer le pattern **Adapter** pour rendre compatibles des interfaces hétérogènes.
2.  Comprendre et appliquer le pattern **Decorator** pour ajouter dynamiquement des responsabilités à des objets sans modifier leur structure.
3.  Démontrer la flexibilité et la maintenabilité apportées par ces patterns.

### Contexte du Mini-Projet : Système de Gestion de Transactions Financières

Imaginez que vous travaillez sur un système de gestion de transactions financières. Ce système doit évoluer pour répondre à de nouveaux besoins :
*   **Intégration :** Utiliser une ancienne librairie de paiement qui a sa propre interface, mais via une nouvelle interface de passerelle de paiement standardisée.
*   **Extension :** Ajouter des fonctionnalités de journalisation et de compression à un flux de données utilisé pour les rapports ou l'archivage, sans modifier le code des flux existants.

---

### Partie 1 : Intégration d'une Passerelle de Paiement (Pattern Adapter)

**Problématique :** Votre entreprise a développé une nouvelle interface standardisée pour toutes ses passerelles de paiement. Cependant, une ancienne librairie de paiement, très stable mais avec une interface obsolète, doit continuer à être utilisée pour certaines transactions. Vous ne pouvez pas modifier le code de cette ancienne librairie.

**Votre mission :** Créer un adaptateur pour que l'ancienne librairie puisse être utilisée via la nouvelle interface standard.

#### Étape 1 : Définition des Interfaces et Classes Existantes

1.  **La nouvelle interface de passerelle de paiement (Target) :**
    Créez une interface `IPaymentGateway` qui définit la méthode suivante :
    ```
    void ProcessPayment(double amount, string currency, string cardNumber, string expiryDate, string cvv);
    ```
    *(Adaptez les types de données au langage que vous utilisez.)*

2.  **L'ancienne librairie de paiement (Adaptee) :**
    Simulez l'ancienne librairie en créant une classe `LegacyPaymentProcessor`. Cette classe a une méthode différente et des paramètres spécifiques :
    ```
    class LegacyPaymentProcessor
    {
        public void ExecuteTransaction(string accountNumber, double value, string transactionType)
        {
            Console.WriteLine($"[Legacy] Transaction de {value} pour le compte {accountNumber} ({transactionType}) traitée.");
            // Logique complexe de l'ancienne librairie...
        }
    }
    ```
    *(Notez la différence de signature et de sémantique des paramètres par rapport à `IPaymentGateway`.)*

#### Étape 2 : Implémentation de l'Adapter

1.  **Créez la classe `LegacyPaymentAdapter` :**
    Cette classe doit implémenter l'interface `IPaymentGateway`.
    Elle doit contenir une instance de `LegacyPaymentProcessor` et utiliser cette instance pour implémenter la méthode `ProcessPayment`.
    Vous devrez "traduire" les paramètres de `IPaymentGateway.ProcessPayment` vers les paramètres attendus par `LegacyPaymentProcessor.ExecuteTransaction`.
    *   **Indice :** Pour la `accountNumber`, vous pouvez utiliser `cardNumber` ou une partie de celle-ci. Pour `transactionType`, vous pouvez utiliser une valeur par défaut comme "CreditCard".

#### Étape 3 : Test et Validation

1.  **Écrivez un petit programme client :**
    *   Créez une instance de `LegacyPaymentAdapter`.
    *   Appelez la méthode `ProcessPayment` de cette instance avec des données de test.
    *   Vérifiez que la sortie console indique que la transaction a été traitée par le "Legacy" processeur.

---

### Partie 2 : Extension de Flux de Données (Pattern Decorator)

**Problématique :** Votre système gère des flux de données (par exemple, pour écrire des rapports dans des fichiers). Vous avez besoin d'ajouter des fonctionnalités comme la journalisation des opérations d'écriture et la compression des données, mais sans modifier la classe de base du flux de données. Ces fonctionnalités doivent pouvoir être ajoutées ou retirées dynamiquement, et combinées.

**Votre mission :** Utiliser le pattern Decorator pour ajouter ces fonctionnalités.

#### Étape 1 : Définition de l'Interface de Base et du Composant Concret

1.  **L'interface de base du flux de données (Component) :**
    Créez une interface `IDataStream` avec les méthodes suivantes :
    ```
    void Write(string data);
    string Read();
    ```

2.  **Le composant concret (Concrete Component) :**
    Implémentez une classe `FileStream` qui implémente `IDataStream`.
    *   La méthode `Write` devrait simuler l'écriture dans un fichier (par exemple, afficher un message console ou écrire réellement dans un fichier temporaire).
    *   La méthode `Read` devrait simuler la lecture (par exemple, retourner une chaîne de caractères prédéfinie ou le contenu du fichier temporaire).

#### Étape 2 : Implémentation des Décorateurs

1.  **La classe abstraite `StreamDecorator` (Decorator) :**
    Créez une classe abstraite `StreamDecorator` qui implémente `IDataStream`.
    Elle doit contenir une référence à un `IDataStream` (le composant qu'elle enveloppe).
    Ses méthodes `Write` et `Read` doivent simplement déléguer l'appel au composant enveloppé.

2.  **Le décorateur de journalisation (Concrete Decorator) :**
    Créez une classe `LoggingStreamDecorator` qui hérite de `StreamDecorator`.
    *   Dans sa méthode `Write`, ajoutez un message de journalisation *avant* et *après* l'appel à la méthode `Write` du composant enveloppé.
    *   Dans sa méthode `Read`, ajoutez un message de journalisation *avant* et *après* l'appel à la méthode `Read` du composant enveloppé.

3.  **Le décorateur de compression (Concrete Decorator) :**
    Créez une classe `CompressionStreamDecorator` qui hérite de `StreamDecorator`.
    *   Dans sa méthode `Write`, simulez une compression : par exemple, ajoutez un préfixe `[COMPRESSED]` aux données avant de les passer au composant enveloppé.
    *   Dans sa méthode `Read`, simulez une décompression : par exemple, retirez le préfixe `[COMPRESSED]` des données reçues du composant enveloppé avant de les retourner.

#### Étape 3 : Test et Validation

1.  **Écrivez un petit programme client :**
    *   Démontrez l'utilisation d'un `FileStream` seul.
    *   Démontrez l'utilisation d'un `FileStream` enveloppé par un `LoggingStreamDecorator`.
    *   Démontrez l'utilisation d'un `FileStream` enveloppé par un `CompressionStreamDecorator`.
    *   **Le plus important :** Démontrez l'utilisation d'un `FileStream` enveloppé par un `LoggingStreamDecorator`, qui est lui-même enveloppé par un `CompressionStreamDecorator`. Écrivez des données et lisez-les pour observer l'ordre d'application des décorateurs.

---

### Consignes Générales

*   **Langage :** Choisissez le langage de programmation de votre choix (Java, C#, Python, TypeScript, etc.).
*   **Structure du code :** Organisez votre code de manière claire et modulaire (par exemple, un fichier par classe/interface, ou des sections bien définies).
*   **Commentaires :** Ajoutez des commentaires pertinents pour expliquer vos choix de conception et les parties complexes.
*   **Tests :** Les petits programmes clients demandés sont vos tests. Assurez-vous qu'ils couvrent les scénarios principaux.
*   **Utilisation de l'IA :** L'utilisation d'outils d'IA générative (ChatGPT, Copilot, etc.) est encouragée pour vous aider dans la rédaction du code ou la compréhension des concepts. Cependant, il est **impératif** que vous soyez capable d'expliquer **chaque ligne** de votre code et de justifier vos choix de conception. Le but n'est pas de copier-coller, mais d'apprendre et de comprendre. Soyez prêt à discuter de vos implémentations.

### Rendu Attendu

Un dépôt Git (GitHub, GitLab, etc.) contenant :
1.  Le code source complet de votre solution, organisé et commenté.
2.  Un fichier `README.md` à la racine du dépôt expliquant :
    *   Comment compiler et exécuter votre code.
    *   Vos choix de conception pour chaque pattern (pourquoi vous avez implémenté l'Adapter/Decorator de cette manière).
    *   Les résultats observés lors de l'exécution de vos tests.

### Critères d'Évaluation

*   **Correction fonctionnelle :** Le code compile et s'exécute sans erreur, et les fonctionnalités attendues sont présentes.
*   **Adhérence aux patterns :** Les patterns Adapter et Decorator sont correctement appliqués et respectent leurs principes.
*   **Qualité du code :** Le code est clair, lisible, bien structuré et commenté.
*   **Compréhension :** Votre capacité à expliquer vos choix de conception et le fonctionnement des patterns.
*   **Tests :** La pertinence et la clarté des scénarios de test.

N'hésitez pas à expérimenter et à poser des questions si vous rencontrez des difficultés. Bon courage !