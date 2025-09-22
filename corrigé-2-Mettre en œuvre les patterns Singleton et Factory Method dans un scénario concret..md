Voici deux solutions possibles pour le TP, chacune présentée comme un chapitre distinct.

---

## Chapitre 1 : Implémentations Classiques des Patterns Singleton et Factory Method

Ce chapitre propose une implémentation directe et classique des patterns Singleton et Factory Method, en se basant sur des principes largement reconnus et applicables dans la plupart des langages orientés objet.

### Partie 1 : Gestionnaire de Log (Pattern Singleton)

Le pattern Singleton garantit qu'une classe n'a qu'une seule instance et fournit un point d'accès global à celle-ci. C'est idéal pour un gestionnaire de log centralisé.

#### Code : `LogManager` (Singleton)


```python
from enum import Enum

# Définition de l'énumération LogLevel
class LogLevel(Enum):
    INFO = "INFO"
    WARNING = "WARNING"
    ERROR = "ERROR"

class LogManager:
    _instance = None # Variable statique pour stocker l'instance unique

    # Constructeur privé pour empêcher l'instanciation directe
    def __init__(self):
        if LogManager._instance is not None:
            # Empêche la création d'une nouvelle instance si elle existe déjà
            raise Exception("Cette classe est un Singleton ! Utilisez getInstance() pour obtenir l'instance unique.")
        self.logs = [] # Pour stocker les messages de log (simplifié)
        print("LogManager: Initialisation de l'instance unique.")

    # Méthode statique publique pour obtenir l'instance unique
    @staticmethod
    def getInstance():
        if LogManager._instance is None:
            # Crée l'instance si elle n'existe pas encore
            LogManager._instance = LogManager()
        return LogManager._instance

    # Méthode pour enregistrer un message
    def log(self, message: str, level: LogLevel):
        log_entry = f"[{level.value}] {message}"
        self.logs.append(log_entry)
        print(log_entry)

# --- Test du Singleton ---
if __name__ == "__main__":
    print("--- Test du Singleton LogManager ---")
    logger1 = LogManager.getInstance()
    logger1.log("Application démarrée.", LogLevel.INFO)

    logger2 = LogManager.getInstance()
    logger2.log("Attention : un paramètre est manquant.", LogLevel.WARNING)

    # Vérification que les deux références pointent vers la même instance
    print(f"logger1 est la même instance que logger2 : {logger1 is logger2}")

    # Tenter d'instancier directement (devrait lever une erreur si __init__ est appelé)
    # try:
    #     logger_fail = LogManager()
    # except Exception as e:
    #     print(f"Erreur attendue lors de l'instanciation directe : {e}")

    logger1.log("Une erreur critique est survenue.", LogLevel.ERROR)
    print(f"Tous les logs enregistrés : {logger1.logs}")
    print("\n")
```


#### Explication et Avantages du Singleton

*   **Constructeur Privé (`__init__` avec vérification) :** Empêche la création directe d'instances de `LogManager` depuis l'extérieur de la classe.
*   **Instance Statique Privée (`_instance`) :** Stocke l'unique instance de la classe. Elle est initialisée à `None` et créée seulement lors du premier appel à `getInstance()`.
*   **Méthode Statique Publique (`getInstance()`) :** C'est le point d'accès global. Elle vérifie si une instance existe déjà ; si ce n'est pas le cas, elle la crée, puis la retourne.
*   **Avantages :**
    *   **Contrôle d'Instance Unique :** Garantit qu'une seule instance du gestionnaire de log existe, évitant les conflits et assurant une cohérence globale.
    *   **Accès Global :** Facilement accessible depuis n'importe quelle partie de l'application via `LogManager.getInstance()`.
    *   **Gestion des Ressources :** Utile pour des ressources coûteuses ou partagées (comme une connexion à une base de données ou un fichier de log physique) où une seule instance est suffisante.

### Partie 2 : Création de Documents (Pattern Factory Method)

Le pattern Factory Method délègue la création d'objets à des sous-classes, permettant au code client de créer des objets sans connaître leur type concret.

#### Code : `IDocument` et `DocumentCreator`


```python
# Interface/Classe abstraite du produit
class IDocument:
    def generate_content(self) -> str:
        raise NotImplementedError
    def save(self, filename: str):
        raise NotImplementedError

# Produits concrets
class PdfDocument(IDocument):
    def generate_content(self) -> str:
        return "Contenu PDF généré."
    def save(self, filename: str):
        print(f"Sauvegarde du document PDF : '{filename}'")

class WordDocument(IDocument):
    def generate_content(self) -> str:
        return "Contenu Word généré."
    def save(self, filename: str):
        print(f"Sauvegarde du document Word : '{filename}'")

# Créateur abstrait (Factory)
class DocumentCreator:
    # La Factory Method abstraite
    def create_document(self) -> IDocument:
        raise NotImplementedError

    # Méthode concrète qui utilise la Factory Method (Template Method)
    def generate_and_save(self, filename: str):
        document = self.create_document() # Appel à la Factory Method
        content = document.generate_content()
        print(f"Génération du contenu : '{content}'")
        document.save(filename)
        # Intégration du Singleton LogManager
        LogManager.getInstance().log(f"Document '{filename}' créé via {self.__class__.__name__}.", LogLevel.INFO)

# Créateurs concrets
class PdfCreator(DocumentCreator):
    def create_document(self) -> IDocument:
        return PdfDocument()

class WordCreator(DocumentCreator):
    def create_document(self) -> IDocument:
        return WordDocument()

# --- Test de la Factory Method ---
if __name__ == "__main__":
    print("--- Test de la Factory Method DocumentCreator ---")
    pdf_creator = PdfCreator()
    pdf_creator.generate_and_save("rapport_annuel.pdf")

    word_creator = WordCreator()
    word_creator.generate_and_save("lettre_client.docx")
    print("\n")
```


#### Explication et Avantages de la Factory Method

*   **`IDocument` (Produit Abstraite) :** Définit l'interface commune pour tous les types de documents que la factory peut créer. Le code client interagit avec cette interface, pas avec les implémentations concrètes.
*   **`PdfDocument`, `WordDocument` (Produits Concrets) :** Implémentent l'interface `IDocument`, fournissant les détails spécifiques à chaque type de document.
*   **`DocumentCreator` (Créateur Abstrait) :** Déclare la `create_document()` comme méthode abstraite (la Factory Method). Il contient également une méthode `generate_and_save()` concrète qui utilise la Factory Method pour obtenir un produit, puis travaille avec ce produit via son interface `IDocument`. C'est un exemple de Template Method.
*   **`PdfCreator`, `WordCreator` (Créateurs Concrets) :** Chaque créateur concret implémente la Factory Method `create_document()` pour retourner une instance spécifique du produit correspondant.
*   **Avantages :**
    *   **Découplage :** Le code client qui utilise `generate_and_save()` n'a pas besoin de connaître les classes concrètes `PdfDocument` ou `WordDocument`. Il travaille avec l'interface `IDocument`.
    *   **Extensibilité :** Pour ajouter un nouveau type de document (ex: `HtmlDocument`), il suffit de créer `HtmlDocument` et `HtmlCreator`, sans modifier le code existant des autres créateurs ou du client.
    *   **Flexibilité :** Permet aux sous-classes de décider quel objet instancier, offrant une grande souplesse dans la gestion de la création d'objets.

### Partie 3 : Intégration et Réflexion

L'intégration du `LogManager` (Singleton) dans les méthodes `generate_and_save` des créateurs concrets a été réalisée dans le code ci-dessus.

*   **Avantages du Singleton dans le gestionnaire de log :**
    *   Assure une source unique et cohérente pour tous les messages de log de l'application.
    *   Facilite la configuration centralisée du système de log (par exemple, changer la destination des logs, le niveau de verbosité).
    *   Évite la duplication de ressources (par exemple, plusieurs gestionnaires de fichiers ou connexions réseau pour les logs).

*   **Avantages de la Factory Method pour la création de documents :**
    *   **Modularité :** Chaque type de document et sa logique de création sont encapsulés.
    *   **Maintenabilité :** Les modifications apportées à la création d'un type de document n'affectent pas les autres types ou le code client.
    *   **Testabilité :** Les créateurs et les documents peuvent être testés indépendamment.

*   **Contribution à la modularité et maintenabilité :** Ces patterns favorisent le principe Ouvert/Fermé (Open/Closed Principle - OCP), où les entités logicielles doivent être ouvertes à l'extension mais fermées à la modification. Ajouter une nouvelle promotion ou un nouveau type de document ne nécessite pas de modifier le code existant, mais d'ajouter de nouvelles classes.

*   **Autres scénarios d'utilisation :**
    *   **Singleton :** Gestionnaire de configuration, pool de connexions à une base de données, cache global, gestionnaire de threads.
    *   **Factory Method :** Création d'objets spécifiques à une plateforme (ex: boutons pour Windows vs. macOS), parsers de différents formats de fichiers, gestionnaires de connexion à différents types de bases de données.

---

## Chapitre 2 : Approches Alternatives et Détails d'Implémentation

Ce chapitre explore des variations d'implémentation pour les patterns Singleton et Factory Method, en mettant l'accent sur des idiomes spécifiques à certains langages ou des considérations de conception plus avancées.

### Partie 1 : Gestionnaire de Log (Pattern Singleton - Approche Python `__new__`)

Pour le Singleton, une approche courante en Python utilise la méthode spéciale `__new__`, qui est appelée avant `__init__` lors de la création d'une instance. C'est une manière élégante de contrôler l'instanciation.

#### Code : `LogManager` (Singleton avec `__new__`)


```python
from enum import Enum

class LogLevel(Enum):
    INFO = "INFO"
    WARNING = "WARNING"
    ERROR = "ERROR"

class LogManager:
    _instance = None # Variable statique pour stocker l'instance unique

    # Utilisation de __new__ pour contrôler l'instanciation
    def __new__(cls):
        if cls._instance is None:
            # Crée l'instance via la méthode __new__ de la classe parente
            cls._instance = super(LogManager, cls).__new__(cls)
            # Initialise les attributs de l'instance ici, car __init__ ne sera appelé qu'une fois
            cls._instance.logs = []
            print("LogManager: Initialisation de l'instance unique via __new__.")
        return cls._instance

    # __init__ sera appelé à chaque fois qu'une "nouvelle" instance est demandée,
    # mais l'instance retournée par __new__ sera toujours la même.
    # Il est souvent préférable de ne pas avoir de logique dans __init__ pour un Singleton basé sur __new__
    # ou de s'assurer qu'elle est idempotente.
    def __init__(self):
        # Cette partie est appelée à chaque "instanciation", mais l'objet est le même.
        # On peut y mettre des initialisations qui doivent être re-exécutées ou des vérifications.
        pass

    def log(self, message: str, level: LogLevel):
        log_entry = f"[{level.value}] {message}"
        self.logs.append(log_entry)
        print(log_entry)

# --- Test du Singleton ---
if __name__ == "__main__":
    print("--- Test du Singleton LogManager (avec __new__) ---")
    logger1 = LogManager() # Appel direct, mais __new__ gère l'instance unique
    logger1.log("Application démarrée (via __new__).", LogLevel.INFO)

    logger2 = LogManager()
    logger2.log("Attention : un paramètre est manquant (via __new__).", LogLevel.WARNING)

    print(f"logger1 est la même instance que logger2 : {logger1 is logger2}")
    logger1.log("Une erreur critique est survenue (via __new__).", LogLevel.ERROR)
    print(f"Tous les logs enregistrés : {logger1.logs}")
    print("\n")
```


#### Explication et Avantages de l'approche `__new__`

*   **Méthode `__new__` :** En Python, `__new__` est la méthode responsable de la création et du retour d'une nouvelle instance de la classe. En la surchargeant, nous pouvons intercepter le processus de création et retourner l'instance unique si elle existe déjà.
*   **Initialisation :** L'initialisation des attributs de l'instance (`self.logs = []`) doit être faite dans `__new__` (après la création de la première instance) car `__init__` sera appelé à chaque fois que `LogManager()` est appelé, même si `__new__` retourne une instance existante.
*   **Avantages :**
    *   **Plus Pythonique :** Pour les développeurs Python, cette approche est souvent considérée comme plus idiomatique que la méthode `getInstance()`.
    *   **Transparence :** Le code client peut appeler `LogManager()` comme s'il créait une nouvelle instance, sans avoir à connaître la méthode statique `getInstance()`.
    *   **Thread-Safety (avec précautions) :** Bien que l'exemple ci-dessus ne soit pas thread-safe par défaut, l'approche `__new__` peut être rendue thread-safe plus facilement avec des verrous (locks) au niveau de la création de l'instance.

*   **Note sur Java :** En Java, l'approche la plus robuste et simple pour un Singleton est souvent l'utilisation d'une `enum` à un seul élément, qui gère naturellement la thread-safety et la sérialisation.

### Partie 2 : Création de Documents (Pattern Factory Method - Détails du Template Method)

La structure du Factory Method reste fondamentalement la même, car elle est bien définie. Cependant, nous pouvons mettre davantage l'accent sur le rôle de la méthode `generate_and_save` comme un "Template Method" et ajouter des détails d'implémentation pour illustrer la flexibilité.

#### Code : `IDocument` et `DocumentCreator` (avec Template Method accentué)


```python
# Interface/Classe abstraite du produit
class IDocument:
    def generate_content(self) -> str:
        raise NotImplementedError
    def save(self, filename: str):
        raise NotImplementedError
    def get_document_type(self) -> str: # Nouvelle méthode pour plus de détails
        raise NotImplementedError

# Produits concrets
class PdfDocument(IDocument):
    def generate_content(self) -> str:
        return "Contenu PDF formaté pour impression."
    def save(self, filename: str):
        print(f"Sauvegarde du document PDF compressé : '{filename}'")
    def get_document_type(self) -> str:
        return "PDF"

class WordDocument(IDocument):
    def generate_content(self) -> str:
        return "Contenu Word avec styles et mise en page."
    def save(self, filename: str):
        print(f"Sauvegarde du document Word éditable : '{filename}'")
    def get_document_type(self) -> str:
        return "Word"

# Créateur abstrait (Factory)
class DocumentCreator:
    # La Factory Method abstraite
    def create_document(self) -> IDocument:
        raise NotImplementedError

    # Méthode concrète qui est une Template Method
    def generate_and_save(self, filename: str):
        # Étape 1: Création du document (déléguée à la Factory Method)
        document = self.create_document()
        LogManager().log(f"Préparation à la création d'un document de type {document.get_document_type()}.", LogLevel.INFO)

        # Étape 2: Génération du contenu
        content = document.generate_content()
        LogManager().log(f"Contenu généré pour '{filename}'.", LogLevel.INFO)
        # Ici, on pourrait ajouter une étape de traitement du contenu si nécessaire

        # Étape 3: Sauvegarde du document
        document.save(filename)
        LogManager().log(f"Document '{filename}' de type {document.get_document_type()} sauvegardé avec succès.", LogLevel.INFO)

# Créateurs concrets
class PdfCreator(DocumentCreator):
    def create_document(self) -> IDocument:
        return PdfDocument()

class WordCreator(DocumentCreator):
    def create_document(self) -> IDocument:
        return WordDocument()

# --- Test de la Factory Method ---
if __name__ == "__main__":
    print("--- Test de la Factory Method DocumentCreator (avec Template Method accentué) ---")
    pdf_creator = PdfCreator()
    pdf_creator.generate_and_save("rapport_financier.pdf")

    word_creator = WordCreator()
    word_creator.generate_and_save("memo_interne.docx")
    print("\n")
```


#### Explication et Avantages du Template Method dans la Factory

*   **`generate_and_save` comme Template Method :** La méthode `generate_and_save` dans `DocumentCreator` est un excellent exemple de Template Method. Elle définit l'algorithme squelette pour la création et la sauvegarde d'un document, mais délègue certaines étapes (comme `create_document`, `generate_content`, `save`) à des sous-classes ou à des objets produits.
*   **Détails d'Implémentation :** Les méthodes `generate_content` et `save` des produits concrets peuvent avoir des logiques plus complexes et spécifiques (ex: compression pour PDF, gestion des styles pour Word). La Factory Method permet de masquer ces complexités au client.
*   **Nouvelle méthode `get_document_type()` :** Ajoutée à `IDocument` pour permettre aux produits de se décrire, ce qui est utile pour le logging ou d'autres logiques conditionnelles dans le créateur.
*   **Avantages :**
    *   **Cohérence de l'Algorithme :** Garantit que toutes les étapes de création et de sauvegarde sont exécutées dans le bon ordre, tout en permettant la variation des détails.
    *   **Flexibilité Accrue :** Les sous-classes peuvent implémenter les "étapes" spécifiques sans modifier la structure globale de l'algorithme.
    *   **Logging Détaillé :** L'intégration du `LogManager` à différentes étapes de la `Template Method` permet un suivi plus granulaire du processus de création de documents.

### Partie 3 : Intégration et Réflexion

L'intégration du `LogManager` (Singleton) dans les méthodes `generate_and_save` des créateurs concrets est réalisée en appelant `LogManager()` directement (grâce à l'approche `__new__` du Singleton).

*   **Avantages de l'approche `__new__` pour Singleton :** Offre une syntaxe d'utilisation plus naturelle (`LogManager()`) et est souvent préférée en Python pour sa clarté et sa capacité à gérer l'instanciation de manière plus fondamentale.

*   **Avantages de la Factory Method avec Template Method :**
    *   Renforce le découplage en séparant clairement les étapes génériques de l'algorithme des implémentations spécifiques.
    *   Permet une personnalisation fine des étapes de création et de sauvegarde sans altérer le flux global.
    *   Facilite l'ajout de nouvelles étapes ou de logiques transversales (comme le logging) au sein de l'algorithme sans toucher aux produits concrets.

*   **Contribution à la modularité et maintenabilité :** Ces implémentations continuent de respecter les principes SOLID, notamment le Principe Ouvert/Fermé. L'ajout d'un nouveau type de document ou une modification de la logique de log n'entraîne pas de modifications en cascade, mais plutôt l'ajout de nouvelles classes ou la modification d'une seule entité.

*   **Autres scénarios d'utilisation :**
    *   **Singleton (`__new__` ou Enum) :** Particulièrement pertinent dans des environnements où la gestion des ressources est critique et où une instance unique est une garantie de performance ou de cohérence (ex: gestionnaire de configuration d'une application web, pool de threads).
    *   **Factory Method (avec Template Method) :** Idéal pour les frameworks où les utilisateurs doivent implémenter des étapes spécifiques d'un processus générique (ex: un framework de traitement de données où l'utilisateur fournit des "étapes" de transformation, un framework de jeu où les "créateurs" de personnages ou d'ennemis sont définis par les développeurs de jeux).