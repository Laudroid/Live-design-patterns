Voici deux solutions possibles pour ce TP, chacune présentée comme un chapitre distinct.

---

## Chapitre 1 : Implémentations Classiques des Patterns Adapter et Decorator

Ce chapitre propose une implémentation directe et classique des patterns Adapter et Decorator en Python, en suivant les définitions fondamentales de ces patrons de conception.

### Partie 1 : Intégration d'une Passerelle de Paiement (Pattern Adapter)

Le pattern Adapter permet à des interfaces incompatibles de travailler ensemble. Il agit comme un traducteur entre deux objets.

#### 1. Définition des Interfaces et Classes Existantes


```python
# payment_interfaces.py
from abc import ABC, abstractmethod

# La nouvelle interface de passerelle de paiement (Target)
class IPaymentGateway(ABC):
    @abstractmethod
    def process_payment(self, amount: float, currency: str, card_number: str, expiry_date: str, cvv: str):
        pass

# L'ancienne librairie de paiement (Adaptee)
class LegacyPaymentProcessor:
    def execute_transaction(self, account_number: str, value: float, transaction_type: str):
        """
        Méthode de l'ancienne librairie avec une signature spécifique.
        """
        print(f"[Legacy] Transaction de {value:.2f} {transaction_type} pour le compte {account_number} traitée.")
        # Simule une logique complexe de l'ancienne librairie...
```


#### 2. Implémentation de l'Adapter


```python
# payment_adapter.py
from payment_interfaces import IPaymentGateway, LegacyPaymentProcessor

class LegacyPaymentAdapter(IPaymentGateway):
    def __init__(self, legacy_processor: LegacyPaymentProcessor):
        self._legacy_processor = legacy_processor

    def process_payment(self, amount: float, currency: str, card_number: str, expiry_date: str, cvv: str):
        """
        Adapte l'appel de la nouvelle interface vers l'ancienne librairie.
        Traduit les paramètres de la nouvelle interface vers ceux attendus par l'ancienne.
        """
        # Pour accountNumber, nous utilisons le numéro de carte comme identifiant de compte simplifié.
        # En réalité, une logique de mapping plus complexe serait nécessaire.
        account_number = card_number
        # Le type de transaction est une valeur par défaut pour l'ancienne librairie.
        transaction_type = "CreditCard" # Ou "DebitCard", etc., selon la logique métier.

        print(f"[Adapter] Conversion de la requête pour LegacyPaymentProcessor...")
        self._legacy_processor.execute_transaction(account_number, amount, transaction_type)
        print(f"[Adapter] Paiement de {amount:.2f} {currency} traité via l'adaptateur.")
```


#### 3. Test et Validation


```python
# client_payment.py
from payment_interfaces import LegacyPaymentProcessor
from payment_adapter import LegacyPaymentAdapter

if __name__ == "__main__":
    print("--- Test du Pattern Adapter ---")

    # Création de l'ancienne librairie
    legacy_processor = LegacyPaymentProcessor()

    # Création de l'adaptateur pour utiliser l'ancienne librairie via la nouvelle interface
    payment_gateway = LegacyPaymentAdapter(legacy_processor)

    # Utilisation de l'adaptateur comme si c'était une nouvelle passerelle de paiement
    print("\nTentative de paiement via l'adaptateur :")
    payment_gateway.process_payment(
        amount=150.75,
        currency="EUR",
        card_number="1234-5678-9012-3456",
        expiry_date="12/25",
        cvv="123"
    )

    print("\nL'adaptateur a permis d'utiliser LegacyPaymentProcessor avec la nouvelle interface IPaymentGateway.")
```


#### Explication et Avantages de l'Adapter

*   **`IPaymentGateway` (Target) :** Représente l'interface que le client attend.
*   **`LegacyPaymentProcessor` (Adaptee) :** La classe existante avec une interface incompatible.
*   **`LegacyPaymentAdapter` (Adapter) :** Implémente `IPaymentGateway` et contient une instance de `LegacyPaymentProcessor`. Sa méthode `process_payment` "traduit" les appels vers la méthode `execute_transaction` de l'adaptee.
*   **Avantages :**
    *   **Réutilisation de code :** Permet de réutiliser des classes existantes (l'adaptee) sans les modifier, même si leurs interfaces ne correspondent pas.
    *   **Découplage :** Le code client interagit uniquement avec l'interface `IPaymentGateway`, restant découplé des détails d'implémentation de `LegacyPaymentProcessor`.
    *   **Flexibilité :** Facilite l'intégration de systèmes hétérogènes et la migration progressive vers de nouvelles interfaces.

### Partie 2 : Extension de Flux de Données (Pattern Decorator)

Le pattern Decorator permet d'ajouter dynamiquement de nouvelles responsabilités à un objet sans modifier sa structure. Il est flexible et permet de combiner des comportements.

#### 1. Définition de l'Interface de Base et du Composant Concret


```python
# data_streams.py
from abc import ABC, abstractmethod
import os

# L'interface de base du flux de données (Component)
class IDataStream(ABC):
    @abstractmethod
    def write(self, data: str):
        pass

    @abstractmethod
    def read(self) -> str:
        pass

# Le composant concret (Concrete Component)
class FileStream(IDataStream):
    def __init__(self, filename: str = "temp_data.txt"):
        self._filename = filename
        # Assurez-vous que le fichier est vide au démarrage pour la simulation
        with open(self._filename, "w") as f:
            f.write("")
        print(f"[FileStream] Initialisé pour le fichier '{self._filename}'.")

    def write(self, data: str):
        with open(self._filename, "a") as f: # 'a' pour append
            f.write(data + "\n")
        print(f"[FileStream] Écrit : '{data}' dans '{self._filename}'.")

    def read(self) -> str:
        if not os.path.exists(self._filename):
            return ""
        with open(self._filename, "r") as f:
            content = f.read().strip()
        print(f"[FileStream] Lu : '{content}' depuis '{self._filename}'.")
        return content
```


#### 2. Implémentation des Décorateurs


```python
# stream_decorators.py
from data_streams import IDataStream

# La classe abstraite StreamDecorator (Decorator)
class StreamDecorator(IDataStream, ABC):
    def __init__(self, wrapped_stream: IDataStream):
        self._wrapped_stream = wrapped_stream

    def write(self, data: str):
        self._wrapped_stream.write(data)

    def read(self) -> str:
        return self._wrapped_stream.read()

# Le décorateur de journalisation (Concrete Decorator)
class LoggingStreamDecorator(StreamDecorator):
    def __init__(self, wrapped_stream: IDataStream):
        super().__init__(wrapped_stream)
        print("[LoggingStreamDecorator] Initialisé.")

    def write(self, data: str):
        print(f"[LogDecorator] Avant écriture : '{data}'")
        super().write(data)
        print(f"[LogDecorator] Après écriture.")

    def read(self) -> str:
        print("[LogDecorator] Avant lecture.")
        data = super().read()
        print(f"[LogDecorator] Après lecture : '{data}'")
        return data

# Le décorateur de compression (Concrete Decorator)
class CompressionStreamDecorator(StreamDecorator):
    _COMPRESSION_PREFIX = "[COMPRESSED]"

    def __init__(self, wrapped_stream: IDataStream):
        super().__init__(wrapped_stream)
        print("[CompressionStreamDecorator] Initialisé.")

    def write(self, data: str):
        compressed_data = f"{self._COMPRESSION_PREFIX}{data}"
        print(f"[CompressionDecorator] Compression de '{data}' en '{compressed_data}'")
        super().write(compressed_data)

    def read(self) -> str:
        data = super().read()
        if data.startswith(self._COMPRESSION_PREFIX):
            decompressed_data = data[len(self._COMPRESSION_PREFIX):]
            print(f"[CompressionDecorator] Décompression de '{data}' en '{decompressed_data}'")
            return decompressed_data
        print(f"[CompressionDecorator] Aucune décompression nécessaire pour '{data}'")
        return data
```


#### 3. Test et Validation


```python
# client_stream.py
from data_streams import FileStream
from stream_decorators import LoggingStreamDecorator, CompressionStreamDecorator

if __name__ == "__main__":
    print("--- Test du Pattern Decorator ---")

    # 1. Utilisation d'un FileStream seul
    print("\n--- FileStream seul ---")
    plain_stream = FileStream("plain.txt")
    plain_stream.write("Données brutes")
    _ = plain_stream.read()

    # 2. FileStream avec Logging
    print("\n--- FileStream avec Logging ---")
    logged_stream = LoggingStreamDecorator(FileStream("logged.txt"))
    logged_stream.write("Données journalisées")
    _ = logged_stream.read()

    # 3. FileStream avec Compression
    print("\n--- FileStream avec Compression ---")
    compressed_stream = CompressionStreamDecorator(FileStream("compressed.txt"))
    compressed_stream.write("Données à compresser")
    _ = compressed_stream.read()

    # 4. Le plus important : FileStream avec Logging ET Compression
    # L'ordre des décorateurs est important !
    # Ici: Logging enveloppe Compression, qui enveloppe FileStream.
    # Écriture: Log -> Compression -> FileStream
    # Lecture: FileStream -> Compression -> Log
    print("\n--- FileStream avec Logging ET Compression ---")
    # Création de la chaîne de décorateurs
    # Le FileStream est le composant de base
    base_stream = FileStream("logged_compressed.txt")
    # Le CompressionStreamDecorator enveloppe le FileStream
    compressed_then_logged_stream = CompressionStreamDecorator(base_stream)
    # Le LoggingStreamDecorator enveloppe le CompressionStreamDecorator
    full_decorated_stream = LoggingStreamDecorator(compressed_then_logged_stream)

    full_decorated_stream.write("Données importantes et confidentielles")
    read_data = full_decorated_stream.read()
    print(f"Données finales lues : '{read_data}'")

    print("\nLe pattern Decorator permet d'ajouter des fonctionnalités de manière flexible et combinable.")
```


#### Explication et Avantages du Decorator

*   **`IDataStream` (Component) :** L'interface commune pour le composant de base et tous les décorateurs.
*   **`FileStream` (Concrete Component) :** L'objet de base auquel nous voulons ajouter des fonctionnalités.
*   **`StreamDecorator` (Decorator) :** Une classe abstraite qui implémente `IDataStream` et maintient une référence à un `IDataStream` (le composant enveloppé). Elle délègue les appels à ce composant.
*   **`LoggingStreamDecorator`, `CompressionStreamDecorator` (Concrete Decorators) :** Héritent de `StreamDecorator` et ajoutent des comportements spécifiques avant ou après avoir délégué l'appel au composant enveloppé.
*   **Avantages :**
    *   **Extension dynamique :** Les fonctionnalités peuvent être ajoutées ou retirées à un objet à l'exécution.
    *   **Flexibilité :** Permet de combiner plusieurs décorateurs dans n'importe quel ordre, créant ainsi de nouvelles combinaisons de comportements.
    *   **Alternative à l'héritage :** Évite l'explosion de classes qui se produirait avec l'héritage si l'on devait créer une classe pour chaque combinaison de fonctionnalités (ex: `LoggedCompressedFileStream`).
    *   **Respect du OCP :** Le code existant (`FileStream`) n'a pas besoin d'être modifié pour ajouter de nouvelles fonctionnalités.

---

## Chapitre 2 : Approches Alternatives et Améliorations (Adapter avec Factory, Decorator avec un Décorateur Supplémentaire)

Ce chapitre explore des variations et des améliorations pour les patterns Adapter et Decorator, en mettant l'accent sur une sélection de l'adaptateur via une factory et l'ajout d'un décorateur supplémentaire pour illustrer la composabilité.

### Partie 1 : Intégration d'une Passerelle de Paiement (Pattern Adapter avec une Factory)

Pour gérer plusieurs types d'anciennes librairies de paiement, une Factory Method pourrait être utilisée pour créer l'adaptateur approprié. Ici, nous nous concentrons sur un seul adaptateur, mais l'idée est d'introduire un point de création centralisé.

#### 1. Définition des Interfaces et Classes Existantes
(Identiques à la Solution 1 - `payment_interfaces.py`)

#### 2. Implémentation de l'Adapter
(Identiques à la Solution 1 - `payment_adapter.py`)

#### 3. Création d'une Factory pour les Adaptateurs (Optionnel mais illustratif)


```python
# payment_factory.py
from payment_interfaces import IPaymentGateway, LegacyPaymentProcessor
from payment_adapter import LegacyPaymentAdapter

class PaymentGatewayFactory:
    @staticmethod
    def get_gateway(gateway_type: str) -> IPaymentGateway:
        """
        Crée et retourne une instance de IPaymentGateway basée sur le type demandé.
        En cas de "legacy", elle crée et configure l'adaptateur.
        """
        if gateway_type.lower() == "legacy":
            # La factory est responsable de l'instanciation et de la configuration de l'adaptee
            # et de l'adaptateur.
            legacy_processor = LegacyPaymentProcessor()
            return LegacyPaymentAdapter(legacy_processor)
        elif gateway_type.lower() == "new_stripe":
            # Ici, on retournerait une implémentation directe de IPaymentGateway pour Stripe
            # return StripePaymentGateway()
            print("Nouvelle passerelle Stripe non implémentée, utilisant la passerelle par défaut.")
            return LegacyPaymentAdapter(LegacyPaymentProcessor()) # Fallback pour l'exemple
        else:
            raise ValueError(f"Type de passerelle '{gateway_type}' non supporté.")
```


#### 4. Test et Validation (avec Factory)


```python
# client_payment_factory.py
from payment_factory import PaymentGatewayFactory

if __name__ == "__main__":
    print("--- Test du Pattern Adapter avec Factory ---")

    # Utilisation de la factory pour obtenir une passerelle de paiement "legacy"
    print("\nObtention d'une passerelle 'legacy' via la factory :")
    legacy_payment_gateway = PaymentGatewayFactory.get_gateway("legacy")
    legacy_payment_gateway.process_payment(
        amount=200.00,
        currency="USD",
        card_number="9876-5432-1098-7654",
        expiry_date="06/26",
        cvv="456"
    )

    # Tentative d'obtenir une autre passerelle (non implémentée pour l'exemple)
    print("\nTentative d'obtenir une passerelle 'new_stripe' via la factory :")
    new_payment_gateway = PaymentGatewayFactory.get_gateway("new_stripe")
    new_payment_gateway.process_payment(
        amount=50.50,
        currency="GBP",
        card_number="1111-2222-3333-4444",
        expiry_date="01/24",
        cvv="789"
    )

    print("\nLa factory permet de choisir dynamiquement l'adaptateur ou l'implémentation directe.")
```


#### Explication et Avantages de l'Adapter avec Factory

*   **`PaymentGatewayFactory` :** Centralise la logique de création des passerelles de paiement. Elle décide quel adaptateur (ou quelle implémentation directe de `IPaymentGateway`) instancier en fonction d'un critère (ici, `gateway_type`).
*   **Avantages :**
    *   **Découplage accru :** Le client n'a même plus besoin de connaître la classe `LegacyPaymentAdapter`. Il demande simplement un type de passerelle à la factory.
    *   **Gestion de multiples adapteurs :** Facilite l'ajout de nouveaux adaptateurs pour d'autres systèmes legacy ou de nouvelles implémentations directes de `IPaymentGateway`.
    *   **Configuration simplifiée :** La logique de sélection de l'adaptateur peut être basée sur des fichiers de configuration ou des variables d'environnement.

### Partie 2 : Extension de Flux de Données (Pattern Decorator avec Décorateur Supplémentaire)

Nous allons ajouter un décorateur d'encryption pour illustrer davantage la composabilité et l'ordre d'application des décorateurs.

#### 1. Définition de l'Interface de Base et du Composant Concret
(Identiques à la Solution 1 - `data_streams.py`)

#### 2. Implémentation des Décorateurs (avec Encryption)


```python
# stream_decorators_extended.py
from data_streams import IDataStream
from stream_decorators import StreamDecorator # Réutilise la classe de base Decorator

# Le décorateur de journalisation (identique)
class LoggingStreamDecorator(StreamDecorator):
    def __init__(self, wrapped_stream: IDataStream):
        super().__init__(wrapped_stream)
        print("[LogDecorator] Initialisé.")

    def write(self, data: str):
        print(f"[LogDecorator] Avant écriture : '{data}'")
        super().write(data)
        print(f"[LogDecorator] Après écriture.")

    def read(self) -> str:
        print("[LogDecorator] Avant lecture.")
        data = super().read()
        print(f"[LogDecorator] Après lecture : '{data}'")
        return data

# Le décorateur de compression (identique)
class CompressionStreamDecorator(StreamDecorator):
    _COMPRESSION_PREFIX = "[COMPRESSED]"

    def __init__(self, wrapped_stream: IDataStream):
        super().__init__(wrapped_stream)
        print("[CompressionDecorator] Initialisé.")

    def write(self, data: str):
        compressed_data = f"{self._COMPRESSION_PREFIX}{data}"
        print(f"[CompressionDecorator] Compression de '{data}' en '{compressed_data}'")
        super().write(compressed_data)

    def read(self) -> str:
        data = super().read()
        if data.startswith(self._COMPRESSION_PREFIX):
            decompressed_data = data[len(self._COMPRESSION_PREFIX):]
            print(f"[CompressionDecorator] Décompression de '{data}' en '{decompressed_data}'")
            return decompressed_data
        print(f"[CompressionDecorator] Aucune décompression nécessaire pour '{data}'")
        return data

# Nouveau décorateur d'encryption (Concrete Decorator)
class EncryptionStreamDecorator(StreamDecorator):
    _ENCRYPTION_PREFIX = "[ENCRYPTED]"

    def __init__(self, wrapped_stream: IDataStream):
        super().__init__(wrapped_stream)
        print("[EncryptionStreamDecorator] Initialisé.")

    def write(self, data: str):
        encrypted_data = f"{self._ENCRYPTION_PREFIX}{data[::-1]}" # Simule l'encryption par inversion + préfixe
        print(f"[EncryptionDecorator] Encryption de '{data}' en '{encrypted_data}'")
        super().write(encrypted_data)

    def read(self) -> str:
        data = super().read()
        if data.startswith(self._ENCRYPTION_PREFIX):
            decrypted_data = data[len(self._ENCRYPTION_PREFIX):][::-1] # Simule la décryption
            print(f"[EncryptionDecorator] Décryption de '{data}' en '{decrypted_data}'")
            return decrypted_data
        print(f"[EncryptionDecorator] Aucune décryption nécessaire pour '{data}'")
        return data
```


#### 3. Test et Validation (avec Encryption et Ordre des Décorateurs)


```python
# client_stream_extended.py
from data_streams import FileStream
from stream_decorators_extended import LoggingStreamDecorator, CompressionStreamDecorator, EncryptionStreamDecorator

if __name__ == "__main__":
    print("--- Test du Pattern Decorator (Étendu) ---")

    # Scénario 1: Log -> Compression -> Encryption -> FileStream
    # Ordre d'application (de l'intérieur vers l'extérieur): FileStream -> Encryption -> Compression -> Logging
    print("\n--- Log -> Compression -> Encryption ---")
    base_stream_1 = FileStream("full_stack_1.txt")
    encrypted_stream_1 = EncryptionStreamDecorator(base_stream_1)
    compressed_stream_1 = CompressionStreamDecorator(encrypted_stream_1)
    full_decorated_stream_1 = LoggingStreamDecorator(compressed_stream_1)

    full_decorated_stream_1.write("Message secret et optimisé")
    read_data_1 = full_decorated_stream_1.read()
    print(f"Données finales lues : '{read_data_1}'")

    # Scénario 2: Encryption -> Compression -> Log -> FileStream
    # Ordre d'application (de l'intérieur vers l'extérieur): FileStream -> Logging -> Compression -> Encryption
    print("\n--- Encryption -> Compression -> Log ---")
    base_stream_2 = FileStream("full_stack_2.txt")
    logged_stream_2 = LoggingStreamDecorator(base_stream_2)
    compressed_stream_2 = CompressionStreamDecorator(logged_stream_2)
    full_decorated_stream_2 = EncryptionStreamDecorator(compressed_stream_2)

    full_decorated_stream_2.write("Un autre message confidentiel")
    read_data_2 = full_decorated_stream_2.read()
    print(f"Données finales lues : '{read_data_2}'")

    print("\nL'ajout d'un nouveau décorateur et la modification de l'ordre montrent la puissance et la flexibilité du pattern.")
```


#### Explication et Avantages du Decorator Étendu

*   **`EncryptionStreamDecorator` :** Un nouveau décorateur qui simule l'encryption et la décryption. Il s'intègre parfaitement dans la hiérarchie existante sans modifier les autres classes.
*   **Ordre des décorateurs :** Le test client met en évidence l'importance de l'ordre dans lequel les décorateurs sont empilés. L'ordre d'application des transformations (encryption, compression, logging) dépend de la manière dont les objets sont enveloppés.
    *   Lors de l'écriture, les décorateurs les plus externes agissent en premier.
    *   Lors de la lecture, les décorateurs les plus internes (ceux qui ont été appliqués en dernier lors de l'écriture) agissent en premier.
*   **Avantages :**
    *   **Modularité accrue :** Chaque fonctionnalité (logging, compression, encryption) est une unité autonome qui peut être ajoutée ou retirée indépendamment.
    *   **Composabilité :** La capacité à combiner ces fonctionnalités dans n'importe quel ordre pour créer des comportements complexes et personnalisés.
    *   **Maintenabilité :** Les modifications d'une fonctionnalité n'affectent pas les autres, et l'ajout de nouvelles fonctionnalités est simple.