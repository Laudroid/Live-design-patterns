Bonjour à toutes et à tous,

Ce TP a pour objectif de vous immerger dans la conception et l'implémentation de deux patrons de conception fondamentaux : **Abstract Factory** et **Builder**. Vous appliquerez ces patrons pour structurer un système modulaire et flexible.

---

### **TP : Conception de Systèmes Modulaires avec Abstract Factory et Builder**

**Objectif du TP :**
Concevoir et implémenter un système utilisant le patron de conception Abstract Factory pour la création de familles d'objets interdépendants, et le patron Builder pour la construction pas à pas d'objets complexes.

**Contexte du Mini-Projet : Outil de Gestion de Performance Multi-Plateforme**

Imaginez que vous développez un outil de gestion et de monitoring de performance pour une entreprise. Cet outil doit permettre :
1.  De présenter une interface utilisateur cohérente, quel que soit le système d'exploitation (OS) sur lequel il est exécuté (Windows, macOS).
2.  De générer des rapports de performance complexes et hautement configurables, avec des sections optionnelles.

Votre tâche est de concevoir et d'implémenter les parties clés de ce système en utilisant les patrons Abstract Factory et Builder.

---

### **Partie 1 : Conception et Implémentation de l'Abstract Factory (Interface Utilisateur)**

**Problématique :**
L'outil doit fonctionner sur différents OS, chacun ayant son propre "look and feel" pour les composants d'interface utilisateur (boutons, cases à cocher, etc.). Nous voulons éviter de dupliquer le code pour chaque OS et permettre une extension facile à de nouveaux OS ou composants.

**Tâches :**

1.  **Définir les Interfaces Produit Abstraites :**
    *   Créez une interface `IButton` avec une méthode `render()` (ou `onClick()`).
    *   Créez une interface `ICheckbox` avec une méthode `render()` (ou `check()`).
    *   *Extension possible :* Ajoutez d'autres composants comme `ITextField` ou `IWindow`.

2.  **Définir l'Interface Abstract Factory :**
    *   Créez une interface `IUIFactory` avec des méthodes pour créer chaque type de composant abstrait (ex: `createButton()`, `createCheckbox()`).

3.  **Implémenter les Concret Factories :**
    *   Créez une `WindowsUIFactory` qui implémente `IUIFactory` et retourne des implémentations de composants spécifiques à Windows (ex: `WindowsButton`, `WindowsCheckbox`).
    *   Créez une `MacOSUIFactory` qui implémente `IUIFactory` et retourne des implémentations de composants spécifiques à macOS (ex: `MacOSButton`, `MacOSCheckbox`).

4.  **Implémenter les Concret Products :**
    *   Créez les classes `WindowsButton`, `WindowsCheckbox`, `MacOSButton`, `MacOSCheckbox` qui implémentent leurs interfaces respectives et affichent un message simple indiquant leur type et leur OS (ex: "Rendering a Windows Button").

5.  **Créer une Application Client :**
    *   Développez une classe `Application` qui prend en paramètre une `IUIFactory`.
    *   Cette classe doit utiliser la factory pour créer un bouton et une case à cocher, puis appeler leurs méthodes `render()`.
    *   Démontrez comment l'application peut être configurée pour utiliser soit la `WindowsUIFactory`, soit la `MacOSUIFactory` au démarrage, et afficher les composants correspondants.

---

### **Partie 2 : Conception et Implémentation du Builder (Rapports de Performance)**

**Problématique :**
Les rapports de performance peuvent être très variés. Certains nécessitent un résumé exécutif, d'autres des graphiques détaillés, d'autres encore des sections de recommandations. La construction d'un rapport complet avec toutes ses options peut devenir complexe et difficile à gérer avec un simple constructeur.

**Tâches :**

1.  **Définir le Produit Complexe :**
    *   Créez une classe `PerformanceReport` qui contiendra les différentes sections du rapport (ex: `header`, `executiveSummary`, `metricsTable`, `charts`, `recommendations`, `footer`). Ces sections peuvent être de simples chaînes de caractères ou des objets plus complexes.
    *   Ajoutez une méthode `displayReport()` qui affiche le contenu du rapport.

2.  **Définir l'Interface Builder :**
    *   Créez une interface `IReportBuilder` avec des méthodes pour construire les différentes parties du rapport (ex: `buildHeader(String title)`, `buildExecutiveSummary(String summary)`, `addMetricsTable(List<String> data)`, `addChart(String chartType)`, `addRecommendations(String text)`, `buildFooter(String author)`).
    *   Ajoutez une méthode `getResult()` qui retourne l'objet `PerformanceReport` construit.

3.  **Implémenter le Concret Builder :**
    *   Créez une classe `PerformanceReportBuilder` qui implémente `IReportBuilder`.
    *   Elle doit maintenir une instance de `PerformanceReport` et ajouter les sections au fur et à mesure que les méthodes `build...` sont appelées.

4.  **Créer un Directeur (Optionnel mais Recommandé) :**
    *   Créez une classe `ReportGenerator` (le Directeur) qui prend un `IReportBuilder` en paramètre.
    *   Cette classe aura des méthodes pour construire différents types de rapports prédéfinis (ex: `buildStandardReport()`, `buildDetailedReport()`, `buildSummaryReport()`). Ces méthodes orchestreront les appels aux méthodes du builder dans un ordre spécifique.

5.  **Utilisation du Builder :**
    *   Démontrez comment créer différents rapports (un rapport simple, un rapport détaillé avec toutes les sections optionnelles) en utilisant le `PerformanceReportBuilder` (et potentiellement le `ReportGenerator`).

---

### **Partie 3 : Intégration et Application**

**Tâches :**

1.  **Lier l'UI et la Génération de Rapports :**
    *   Modifiez votre classe `Application` (ou créez une nouvelle classe `DashboardApp`).
    *   Cette application devrait :
        *   Permettre à l'utilisateur de choisir le type d'OS (ex: via une variable de configuration ou une entrée simple). Cela déterminera quelle `IUIFactory` sera utilisée.
        *   Afficher un bouton "Générer Rapport" (créé via l'Abstract Factory choisie).
        *   Quand ce bouton est cliqué, déclencher la génération d'un rapport.
        *   Permettre à l'utilisateur de spécifier des options pour le rapport (ex: inclure un résumé exécutif, inclure des graphiques) – cela peut être simulé par des variables booléennes ou des entrées simples.
        *   Utiliser le `PerformanceReportBuilder` (et le `ReportGenerator`) pour construire le rapport selon les options choisies et l'afficher dans la console.

---

### **Consignes Générales et Évaluation :**

*   **Langage de Programmation :** Choisissez un langage orienté objet de votre choix (Java, C#, Python, C++, TypeScript, etc.).
*   **Structure du Projet :** Organisez votre code de manière claire avec des packages/dossiers appropriés pour les interfaces, les implémentations concrètes, etc.
*   **Utilisation de l'IA :** L'utilisation d'outils d'IA générative est encouragée comme aide à la conception, à la rédaction de code ou à la compréhension des concepts. Cependant, il est impératif de comprendre le code généré, de pouvoir l'expliquer et de l'adapter. Le simple copier-coller sans compréhension ne sera pas suffisant. Soyez prêt à justifier vos choix de conception et la logique de votre code.
*   **Qualité du Code :** Le code doit être lisible, bien commenté et respecter les bonnes pratiques de programmation.
*   **Documentation (Facultatif mais apprécié) :** Un petit fichier `README.md` expliquant votre approche, les choix de conception et comment exécuter le code serait un plus. Des diagrammes UML (classes, objets) peuvent également être inclus pour illustrer votre design.

**Critères d'évaluation :**
*   Correcte implémentation des patrons Abstract Factory et Builder.
*   Clarté et modularité du code.
*   Capacité à intégrer les deux patrons de manière fonctionnelle.
*   Compréhension des principes sous-jacents des patrons de conception.

---

N'hésitez pas à explorer et à rendre votre solution élégante. Amusez-vous bien avec ce défi !