# Cours Design Patterns

## Objectifs de la formation

- Comprendre l'importance des Design Patterns dans le développement logiciel.
- Identifier les catégories de Design Patterns (Création, Structure, Comportement).
- Savoir appliquer les Design Patterns les plus courants pour résoudre des problèmes de conception.
- Améliorer la maintenabilité, la flexibilité et la réutilisabilité du code.
## 1: Introduction aux Design Patterns

### Objectifs pédagogiques

- Définir les Design Patterns et comprendre leur utilité.
- Connaître l'historique et la classification des Design Patterns.
### Contenus

#### Partie 1: Qu'est-ce qu'un Design Pattern ?

- Définition formelle et informelle.
- Les problèmes de conception récurrents que les Design Patterns résolvent.
- Avantages de l'utilisation des Design Patterns : réutilisabilité, maintenabilité, flexibilité, communication.
#### Partie 2: Historique et Classification

- Présentation du "Gang of Four" (GoF) et de leur ouvrage fondateur.
- Les trois catégories principales de Design Patterns : Création, Structure, Comportement.
## 2: Design Patterns de Création (Partie 1)

### Objectifs pédagogiques

- Comprendre et appliquer le pattern Singleton.
- Comprendre et appliquer le pattern Factory Method.
### Contenus

#### Partie 1: Singleton

- Définition et intention du pattern : garantir qu'une classe n'a qu'une seule instance.
- Implémentation en Java/C# (Lazy initialization, Thread-safe).
- Cas d'usage typiques (gestionnaire de configuration, pool de connexions) et limites.
#### Partie 2: Factory Method

- Définition et intention du pattern : définir une interface pour créer des objets, mais laisser les sous-classes décider quelle classe instancier.
- Implémentation et diagrammes UML.
- Avantages : découplage entre le client et la création d'objets, extensibilité.
## 3: Design Patterns de Création (Partie 2)

### Objectifs pédagogiques

- Comprendre et appliquer le pattern Abstract Factory.
- Comprendre et appliquer le pattern Builder.
### Contenus

#### Partie 1: Abstract Factory

- Définition et intention : créer des familles d'objets apparentés ou interdépendants sans spécifier leurs classes concrètes.
- Différence avec Factory Method.
- Implémentation et exemple : familles d'éléments d'interface utilisateur multi-plateformes.
#### Partie 2: Builder

- Définition et intention : construire un objet complexe pas à pas.
- Implémentation du "fluent API" (méthodes chaînées).
- Comparaison avec Abstract Factory et cas d'usage (construction d'objets avec de nombreux paramètres optionnels).
## 4: Design Patterns de Structure (Partie 1)

### Objectifs pédagogiques

- Comprendre et appliquer le pattern Adapter.
- Comprendre et appliquer le pattern Decorator.
### Contenus

#### Partie 1: Adapter

- Définition et intention : permettre à des interfaces incompatibles de collaborer.
- Types d'adaptateurs : Classe et Objet.
- Cas d'usage : intégration de bibliothèques tierces, migration de code.
#### Partie 2: Decorator

- Définition et intention : ajouter dynamiquement des responsabilités à un objet.
- Principe d'encapsulation et composition.
- Différence avec l'héritage et avantages en termes de flexibilité.
## 5: Design Patterns de Structure (Partie 2) & Comportement (Partie 1)

### Objectifs pédagogiques

- Comprendre et appliquer les patterns Composite et Facade.
- Comprendre et appliquer le pattern Observer.
### Contenus

#### Partie 1: Composite et Facade

- Composite : Définition et intention (composer des objets en structures arborescentes et traiter les objets individuels et les compositions de manière uniforme).
- Facade : Définition et intention (fournir une interface unifiée pour un ensemble d'interfaces dans un sous-système).
- Cas d'usage pour chacun : systèmes de fichiers, interfaces simplifiées.
#### Partie 2: Observer

- Définition et intention : définir une dépendance un-à-plusieurs entre objets afin que, lorsqu'un objet change d'état, tous ses dépendants soient notifiés et mis à jour automatiquement.
- Implémentation : sujet et observateurs.
- Cas d'usage : systèmes de notification, événements d'interface utilisateur.
