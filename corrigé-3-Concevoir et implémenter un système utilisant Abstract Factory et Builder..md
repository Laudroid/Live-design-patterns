Voici deux solutions possibles pour ce TP, chacune présentée comme un chapitre distinct.

---

## Chapitre 1 : Implémentations Classiques des Patterns Abstract Factory et Builder

Ce chapitre propose une implémentation directe et classique des patterns Abstract Factory et Builder en Python, en suivant les définitions fondamentales de ces patrons de conception.

### Partie 1 : Conception et Implémentation de l'Abstract Factory (Interface Utilisateur)

L'Abstract Factory permet de créer des familles d'objets interdépendants sans spécifier leurs classes concrètes. Ici, nous l'utilisons pour gérer les composants d'interface utilisateur spécifiques à chaque système d'exploitation.

#### 1. Interfaces Produit Abstraites


```python
# ui_components.py
from abc import ABC, abstractmethod

class IButton(ABC):
    @abstractmethod
    def render(self):
        pass

class ICheckbox(ABC):
    @abstractmethod
    def render(self):
        pass

# Extension possible
class ITextField(ABC):
    @abstractmethod
    def render(self):
        pass
    @abstractmethod
    def set_text(self, text: str):
        pass
```


#### 2. Interface Abstract Factory


```python
# ui_factories.py
from abc import ABC, abstractmethod
from ui_components import IButton, ICheckbox, ITextField # Assurez-vous que ces imports sont corrects si vous utilisez des fichiers séparés

class IUIFactory(ABC):
    @abstractmethod
    def create_button(self) -> IButton:
        pass

    @abstractmethod
    def create_checkbox(self) -> ICheckbox:
        pass

    @abstractmethod
    def create_text_field(self) -> ITextField:
        pass
```


#### 3. Implémentation des Concret Products


```python
# ui_components.py (suite)
# Composants Windows
class WindowsButton(IButton):
    def render(self):
        print("Rendering a Windows-style Button.")

class WindowsCheckbox(ICheckbox):
    def render(self):
        print("Rendering a Windows-style Checkbox.")

class WindowsTextField(ITextField):
    def __init__(self):
        self._text = ""
    def render(self):
        print(f"Rendering a Windows-style Text Field with text: '{self._text}'.")
    def set_text(self, text: str):
        self._text = text

# Composants macOS
class MacOSButton(IButton):
    def render(self):
        print("Rendering a macOS-style Button.")

class MacOSCheckbox(ICheckbox):
    def render(self):
        print("Rendering a macOS-style Checkbox.")

class MacOSTextField(ITextField):
    def __init__(self):
        self._text = ""
    def render(self):
        print(f"Rendering a macOS-style Text Field with text: '{self._text}'.")
    def set_text(self, text: str):
        self._text = text
```


#### 4. Implémentation des Concret Factories


```python
# ui_factories.py (suite)
from ui_components import (
    IButton, ICheckbox, ITextField,
    WindowsButton, WindowsCheckbox, WindowsTextField,
    MacOSButton, MacOSCheckbox, MacOSTextField
)

class WindowsUIFactory(IUIFactory):
    def create_button(self) -> IButton:
        return WindowsButton()

    def create_checkbox(self) -> ICheckbox:
        return WindowsCheckbox()

    def create_text_field(self) -> ITextField:
        return WindowsTextField()

class MacOSUIFactory(IUIFactory):
    def create_button(self) -> IButton:
        return MacOSButton()

    def create_checkbox(self) -> ICheckbox:
        return MacOSCheckbox()

    def create_text_field(self) -> ITextField:
        return MacOSTextField()
```


#### 5. Application Client


```python
# application.py
from ui_factories import IUIFactory

class Application:
    def __init__(self, factory: IUIFactory):
        self._factory = factory
        self._button = self._factory.create_button()
        self._checkbox = self._factory.create_checkbox()
        self._text_field = self._factory.create_text_field()

    def display_ui(self):
        print("\n--- Affichage de l'interface utilisateur ---")
        self._button.render()
        self._checkbox.render()
        self._text_field.set_text("Hello World")
        self._text_field.render()

    def get_button(self):
        return self._button
```


### Partie 2 : Conception et Implémentation du Builder (Rapports de Performance)

Le Builder permet de construire des objets complexes étape par étape, offrant un contrôle fin sur le processus de construction et la possibilité de créer différentes représentations du même produit.

#### 1. Définir le Produit Complexe


```python
# reports.py
class PerformanceReport:
    def __init__(self):
        self.header: str = ""
        self.executive_summary: str = ""
        self.metrics_table: list[str] = []
        self.charts: list[str] = []
        self.recommendations: str = ""
        self.footer: str = ""

    def display_report(self):
        print("\n--- Rapport de Performance ---")
        print(f"Titre: {self.header}")
        if self.executive_summary:
            print(f"Résumé Exécutif:\n{self.executive_summary}")
        if self.metrics_table:
            print("Tableau de Métriques:")
            for item in self.metrics_table:
                print(f"  - {item}")
        if self.charts:
            print("Graphiques Inclus:")
            for chart in self.charts:
                print(f"  - {chart}")
        if self.recommendations:
            print(f"Recommandations:\n{self.recommendations}")
        if self.footer:
            print(f"Pied de page: {self.footer}")
        print("----------------------------")
```


#### 2. Définir l'Interface Builder


```python
# report_builders.py
from abc import ABC, abstractmethod
from reports import PerformanceReport

class IReportBuilder(ABC):
    @abstractmethod
    def build_header(self, title: str):
        pass

    @abstractmethod
    def build_executive_summary(self, summary: str):
        pass

    @abstractmethod
    def add_metrics_table(self, data: list[str]):
        pass

    @abstractmethod
    def add_chart(self, chart_type: str):
        pass

    @abstractmethod
    def add_recommendations(self, text: str):
        pass

    @abstractmethod
    def build_footer(self, author: str):
        pass

    @abstractmethod
    def get_result(self) -> PerformanceReport:
        pass
```


#### 3. Implémenter le Concret Builder


```python
# report_builders.py (suite)
class PerformanceReportBuilder(IReportBuilder):
    def __init__(self):
        self.reset()

    def reset(self):
        self._report = PerformanceReport()

    def build_header(self, title: str):
        self._report.header = title

    def build_executive_summary(self, summary: str):
        self._report.executive_summary = summary

    def add_metrics_table(self, data: list[str]):
        self._report.metrics_table.extend(data)

    def add_chart(self, chart_type: str):
        self._report.charts.append(chart_type)

    def add_recommendations(self, text: str):
        self._report.recommendations = text

    def build_footer(self, author: str):
        self._report.footer = f"Rapport généré par {author}"

    def get_result(self) -> PerformanceReport:
        report = self._report
        self.reset() # Réinitialise le builder pour une nouvelle construction
        return report
```


#### 4. Créer un Directeur (ReportGenerator)


```python
# report_generator.py
from report_builders import IReportBuilder

class ReportGenerator:
    def __init__(self, builder: IReportBuilder):
        self._builder = builder

    def build_standard_report(self, title: str, author: str):
        self._builder.build_header(title)
        self._builder.add_metrics_table(["CPU Usage: 75%", "Memory Usage: 60%"])
        self._builder.build_footer(author)

    def build_detailed_report(self, title: str, author: str, summary: str, recommendations: str):
        self._builder.build_header(title)
        self._builder.build_executive_summary(summary)
        self._builder.add_metrics_table(["CPU Usage: 75%", "Memory Usage: 60%", "Disk I/O: 120MB/s"])
        self._builder.add_chart("Bar Chart - CPU History")
        self._builder.add_chart("Line Chart - Memory Trend")
        self._builder.add_recommendations(recommendations)
        self._builder.build_footer(author)
```


### Partie 3 : Intégration et Application


```python
# main.py
from ui_factories import WindowsUIFactory, MacOSUIFactory, IUIFactory
from application import Application
from report_builders import PerformanceReportBuilder
from report_generator import ReportGenerator
import os

class DashboardApp:
    def __init__(self, os_type: str):
        self._ui_factory: IUIFactory
        if os_type.lower() == "windows":
            self._ui_factory = WindowsUIFactory()
        elif os_type.lower() == "macos":
            self._ui_factory = MacOSUIFactory()
        else:
            raise ValueError("Type d'OS non supporté. Choisissez 'windows' ou 'macos'.")

        self._app_ui = Application(self._ui_factory)
        self._report_builder = PerformanceReportBuilder()
        self._report_generator = ReportGenerator(self._report_builder)

    def run(self):
        print(f"Démarrage de l'application sur {os.name} (simulé par {self._ui_factory.__class__.__name__}).")
        self._app_ui.display_ui()

        # Simuler le clic sur le bouton "Générer Rapport"
        print("\n--- Simulation : Clic sur le bouton 'Générer Rapport' ---")
        self._app_ui.get_button().render() # Affiche le bouton qui est "cliqué"

        # Options de rapport simulées
        include_summary = True
        include_charts = True
        include_recommendations = True

        print("\n--- Génération d'un rapport personnalisé ---")
        builder = PerformanceReportBuilder() # Utilisation directe du builder pour plus de flexibilité
        builder.build_header("Rapport de Performance du Serveur Alpha")
        builder.add_metrics_table(["Uptime: 99.9%", "Latence Réseau: 10ms"])

        if include_summary:
            builder.build_executive_summary("Ce rapport présente un aperçu des performances du serveur Alpha sur le dernier mois, avec des métriques clés et des recommandations.")
        if include_charts:
            builder.add_chart("Graphique d'utilisation CPU")
            builder.add_chart("Graphique de trafic réseau")
        if include_recommendations:
            builder.add_recommendations("Il est recommandé d'optimiser les requêtes de base de données pour réduire la charge CPU.")
        builder.build_footer("Équipe de Monitoring")

        custom_report = builder.get_result()
        custom_report.display_report()

        print("\n--- Génération d'un rapport détaillé via le Directeur ---")
        self._report_generator.build_detailed_report(
            "Rapport Détaillé du Serveur Beta",
            "Admin Tech",
            "Analyse approfondie des performances du serveur Beta, identifiant les goulots d'étranglement potentiels.",
            "Mettre à jour le système d'exploitation et les pilotes pour améliorer la stabilité."
        )
        detailed_report = self._report_builder.get_result()
        detailed_report.display_report()

if __name__ == "__main__":
    # Déterminez le type d'OS au démarrage (peut venir d'une config ou d'une variable d'environnement)
    # Pour ce TP, nous le fixons pour la démonstration.
    current_os = "windows" # Changez à "macos" pour tester l'autre UI

    app = DashboardApp(current_os)
    app.run()
```


#### Réflexion

*   **Avantages de l'Abstract Factory (UI) :**
    *   **Découplage :** L'application cliente (`Application`) est découplée des classes concrètes des composants UI. Elle ne dépend que des interfaces `IButton`, `ICheckbox`, etc., et de `IUIFactory`.
    *   **Extensibilité :** L'ajout d'un nouveau système d'exploitation (ex: Linux) ne nécessite que la création d'une `LinuxUIFactory` et de ses produits concrets, sans modifier le code existant de l'application.
    *   **Cohérence :** Garantit que tous les composants créés par une même factory appartiennent à la même "famille" (ex: tous les composants Windows).

*   **Avantages du Builder (Rapports) :**
    *   **Construction Complexe :** Permet de construire des objets `PerformanceReport` très complexes avec de nombreuses options de manière structurée et lisible, évitant les constructeurs surchargés.
    *   **Séparation des Responsabilités :** Le `PerformanceReportBuilder` est responsable de la construction du rapport, tandis que le `PerformanceReport` est le produit final. Le `ReportGenerator` (Directeur) orchestre le processus de construction.
    *   **Flexibilité :** Permet de créer différentes représentations du rapport (standard, détaillé, résumé) en utilisant le même processus de construction.

*   **Contribution à la modularité et maintenabilité :** Ces patterns respectent le principe Ouvert/Fermé (Open/Closed Principle - OCP). L'ajout de nouveaux OS ou de nouveaux types de rapports ne modifie pas le code existant, mais étend le système avec de nouvelles implémentations. Ils favorisent également le Principe de Responsabilité Unique (Single Responsibility Principle - SRP) en attribuant des responsabilités claires à chaque classe.

*   **Autres scénarios :**
    *   **Abstract Factory :** Création de kits de développement logiciel (SDK) pour différentes plateformes, gestion de différentes bases de données (SQL, NoSQL) avec des factories spécifiques, création de thèmes visuels.
    *   **Builder :** Construction de requêtes SQL complexes, configuration d'objets avec de nombreux paramètres optionnels, création de messages réseau avec des en-têtes et corps variés.

---

## Chapitre 2 : Approches Alternatives et Améliorations (Fluent Builder et Sélection Dynamique de Factory)

Ce chapitre explore des variations et des améliorations pour les patterns Abstract Factory et Builder, en mettant l'accent sur une interface plus fluide pour le Builder et une sélection plus dynamique de la Factory.

### Partie 1 : Conception et Implémentation de l'Abstract Factory (Interface Utilisateur - Sélection Dynamique)

La structure de l'Abstract Factory reste similaire, mais nous allons ajouter un mécanisme pour choisir la factory à utiliser de manière plus flexible, par exemple via une fonction utilitaire.

#### 1. Interfaces Produit Abstraites et Concret Products
(Identiques à la Solution 1 - `ui_components.py`)

#### 2. Interface Abstract Factory et Concret Factories
(Identiques à la Solution 1 - `ui_factories.py`)

#### 3. Application Client avec Sélection Dynamique


```python
# app_config.py
from ui_factories import IUIFactory, WindowsUIFactory, MacOSUIFactory

def get_ui_factory(os_type: str) -> IUIFactory:
    """Retourne la factory UI appropriée en fonction du type d'OS."""
    if os_type.lower() == "windows":
        return WindowsUIFactory()
    elif os_type.lower() == "macos":
        return MacOSUIFactory()
    else:
        raise ValueError(f"Type d'OS '{os_type}' non supporté pour la création d'UI.")

# application_dynamic.py
from ui_factories import IUIFactory
from app_config import get_ui_factory

class ApplicationDynamic:
    def __init__(self, os_type: str):
        self._factory = get_ui_factory(os_type) # Sélection dynamique de la factory
        self._button = self._factory.create_button()
        self._checkbox = self._factory.create_checkbox()
        self._text_field = self._factory.create_text_field()

    def display_ui(self):
        print("\n--- Affichage de l'interface utilisateur (Dynamique) ---")
        self._button.render()
        self._checkbox.render()
        self._text_field.set_text("Bienvenue !")
        self._text_field.render()

    def get_button(self):
        return self._button
```


### Partie 2 : Conception et Implémentation du Builder (Rapports de Performance - Fluent Interface)

Nous allons modifier le `PerformanceReportBuilder` pour qu'il utilise une "fluent interface" (ou chaînage de méthodes), rendant la construction des rapports plus expressive et lisible.

#### 1. Définir le Produit Complexe
(Identique à la Solution 1 - `reports.py`)

#### 2. Définir l'Interface Builder
(Identique à la Solution 1 - `report_builders.py`)

#### 3. Implémenter le Concret Builder (Fluent)


```python
# report_builders_fluent.py
from reports import PerformanceReport
from report_builders import IReportBuilder # Pour l'interface

class PerformanceReportFluentBuilder(IReportBuilder):
    def __init__(self):
        self.reset()

    def reset(self):
        self._report = PerformanceReport()

    def build_header(self, title: str) -> 'PerformanceReportFluentBuilder':
        self._report.header = title
        return self # Retourne l'instance du builder pour le chaînage

    def build_executive_summary(self, summary: str) -> 'PerformanceReportFluentBuilder':
        self._report.executive_summary = summary
        return self

    def add_metrics_table(self, data: list[str]) -> 'PerformanceReportFluentBuilder':
        self._report.metrics_table.extend(data)
        return self

    def add_chart(self, chart_type: str) -> 'PerformanceReportFluentBuilder':
        self._report.charts.append(chart_type)
        return self

    def add_recommendations(self, text: str) -> 'PerformanceReportFluentBuilder':
        self._report.recommendations = text
        return self

    def build_footer(self, author: str) -> 'PerformanceReportFluentBuilder':
        self._report.footer = f"Rapport généré par {author}"
        return self

    def get_result(self) -> PerformanceReport:
        report = self._report
        self.reset()
        return report
```


#### 4. Créer un Directeur (ReportGenerator - Utilisation du Fluent Builder)


```python
# report_generator_fluent.py
from report_builders_fluent import PerformanceReportFluentBuilder
from reports import PerformanceReport

class ReportGeneratorFluent:
    def __init__(self, builder: PerformanceReportFluentBuilder):
        self._builder = builder

    def build_standard_report(self, title: str, author: str) -> PerformanceReport:
        return self._builder \
            .build_header(title) \
            .add_metrics_table(["CPU: 80%", "RAM: 70%"]) \
            .build_footer(author) \
            .get_result()

    def build_detailed_report(self, title: str, author: str, summary: str, recommendations: str) -> PerformanceReport:
        return self._builder \
            .build_header(title) \
            .build_executive_summary(summary) \
            .add_metrics_table(["CPU: 80%", "RAM: 70%", "Disk: 150MB/s"]) \
            .add_chart("Histogramme CPU") \
            .add_chart("Graphique de charge réseau") \
            .add_recommendations(recommendations) \
            .build_footer(author) \
            .get_result()
```


### Partie 3 : Intégration et Application


```python
# main_fluent.py
from application_dynamic import ApplicationDynamic
from report_builders_fluent import PerformanceReportFluentBuilder
from report_generator_fluent import ReportGeneratorFluent
import os

class DashboardAppFluent:
    def __init__(self, os_type: str):
        self._app_ui = ApplicationDynamic(os_type) # Utilise l'appli avec sélection dynamique
        self._report_builder = PerformanceReportFluentBuilder()
        self._report_generator = ReportGeneratorFluent(self._report_builder)

    def run(self):
        print(f"Démarrage de l'application sur {os.name} (simulé avec UI dynamique).")
        self._app_ui.display_ui()

        print("\n--- Simulation : Clic sur le bouton 'Générer Rapport' ---")
        self._app_ui.get_button().render()

        # Options de rapport simulées
        include_summary = True
        include_charts = True
        include_recommendations = False # Changement pour montrer la flexibilité

        print("\n--- Génération d'un rapport personnalisé (Fluent Builder) ---")
        custom_report = PerformanceReportFluentBuilder() \
            .build_header("Rapport de Performance du Serveur Gamma") \
            .add_metrics_table(["Disponibilité: 99.99%", "Temps de réponse: 50ms"])
        
        if include_summary:
            custom_report.build_executive_summary("Synthèse des performances du serveur Gamma, avec un focus sur la disponibilité.")
        if include_charts:
            custom_report.add_chart("Graphique de latence")
        if include_recommendations:
            custom_report.add_recommendations("Optimiser les caches pour améliorer le temps de réponse.")
        
        custom_report.build_footer("Équipe DevOps").get_result().display_report()


        print("\n--- Génération d'un rapport standard via le Directeur (Fluent) ---")
        standard_report = self._report_generator.build_standard_report(
            "Rapport Standard du Serveur Delta",
            "Support Technique"
        )
        standard_report.display_report()

if __name__ == "__main__":
    current_os = "macos" # Changez à "windows" pour tester l'autre UI
    app = DashboardAppFluent(current_os)
    app.run()
```


#### Réflexion

*   **Avantages de la Sélection Dynamique de Factory :**
    *   **Flexibilité accrue :** Le code client (`ApplicationDynamic`) est encore plus agnostique quant à la factory concrète utilisée. La décision est déléguée à une fonction ou un module de configuration (`app_config`), ce qui facilite la gestion des environnements et des déploiements.
    *   **Configuration simplifiée :** Le type d'OS peut être lu depuis un fichier de configuration, une variable d'environnement, ou même détecté automatiquement, rendant l'application plus adaptable.

*   **Avantages du Fluent Builder :**
    *   **Lisibilité et Expressivité :** Le chaînage de méthodes rend le code de construction plus proche du langage naturel, améliorant la lisibilité et la compréhension du processus.
    *   **Facilité d'utilisation :** L'IDE peut suggérer les méthodes suivantes dans la chaîne, ce qui améliore l'expérience du développeur.
    *   **Immuabilité (potentielle) :** Bien que notre exemple modifie l'objet interne, un fluent builder peut être conçu pour retourner de nouvelles instances à chaque étape, favorisant l'immuabilité si nécessaire.

*   **Contribution à la modularité et maintenabilité :** Ces améliorations renforcent les principes de conception. La sélection dynamique de la factory améliore le découplage et la configurabilité. Le fluent builder rend la construction d'objets complexes plus agréable et moins sujette aux erreurs, tout en maintenant la séparation des responsabilités.

*   **Autres scénarios :**
    *   **Sélection Dynamique de Factory :** Utile dans les applications multi-locataires où la configuration peut varier par client, ou dans les systèmes qui doivent s'adapter à différents fournisseurs de services (ex: différentes API de paiement).
    *   **Fluent Builder :** Très populaire pour la construction de requêtes (SQL, API REST), la configuration d'objets complexes dans les frameworks, ou la création d'objets de test dans les tests unitaires.