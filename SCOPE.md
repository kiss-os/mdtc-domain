# Scope — mdtc-domain

## 1. Mission du dépôt

`mdtc-domain` définit les **contrats métier canoniques** de la noria MDTC.

Le dépôt doit permettre de représenter de manière explicite, stable et traçable :

- la structure d'un programme et de ses projets ;
- la décomposition opérationnelle des travaux ;
- les acteurs et leurs rôles contextuels ;
- les ressources, matériaux et équipements ;
- le temps, le planning et l'exécution ;
- l'économie chantier ;
- les observations, décisions, risques, changements et preuves ;
- les responsabilités et allocations associées ;
- les états vivants d'un projet et ses baselines immuables.

Le domaine doit rester exploitable par plusieurs projections :

```text
mdtc-domain
   ├── frontend / dashboards
   ├── mdtc-docs
   ├── intégrations ERP / CRM
   ├── Nestor / missions
   └── analytics / knowledge mining
```

Aucune projection ne doit devenir l'autorité métier par accident.

---

## 2. Périmètre inclus

### 2.1 Structure métier

```text
Program
Project
Site / Location
Lot
WorkPackage
Task
Requirement
Dependency
Interface
```

### 2.2 Contributions et faits terrain

```text
ProjectContribution
ProjectEvent
Observation
Evidence
Issue
Blocker
Risk
Action
Decision
```

### 2.3 Acteurs

```text
Person / Organization refs
ProjectActor
ProjectRole
ContractualRole
Trade
ResponsibilityAssignment
```

Le domaine MDTC référence les identités et autorités résolues par le socle ; il ne remplace pas l'IAM commun.

### 2.4 Ressources

```text
Material
MaterialRequirement
Equipment
EquipmentRequirement
SupplyResponsibility
ProcurementNeed
DeliveryEvent
```

### 2.5 Planning et temps

```text
PlannedActivity
TimeEntry
SiteVisit
SiteMeeting
Milestone
Dependency
ExecutionState
```

### 2.6 Économie chantier

Le domaine doit pouvoir distinguer au minimum :

```text
ESTIMATED
COMMITTED
ACTUAL
BILLED
PAID
```

ainsi que les marges et écarts qui en dérivent.

### 2.7 Qualité et exécution

Les évaluations doivent porter en priorité sur une intervention ou un résultat de travail, pas sur une note globale d'une personne.

Exemples :

```text
WorkExecutionAssessment
ExecutionIrregularity
Rework
QualityAssessment
CustomerFeedback
```

Les faits bruts restent séparés de l'interprétation et de l'analytique.

### 2.8 Changements et baselines

```text
ProjectBaseline
ChangeRequest
ChangeOrder
ImpactAssessment
DecisionRecord
```

Une baseline acceptée est immuable ; un changement crée une nouvelle relation ou une nouvelle baseline au lieu de réécrire l'histoire.

### 2.9 Responsabilité et garantie

Le domaine décrit les faits et allocations de responsabilité sans inventer de conclusion juridique automatique.

Exemple : un matériau fourni par le client ne signifie pas automatiquement « aucune garantie ».

```text
supply fact
≠ causality
≠ responsibility
≠ legal effect
```

---

## 3. Périmètre exclu

### 3.1 Nestor Core

Ne doivent pas être réimplémentés ici :

- orchestration générale ;
- sélection / routage des modèles ;
- moteur de délibération ;
- Chronos ;
- CCU / gestion utilisateur commune ;
- IAM ;
- graph knowledge commun ;
- catalog / resolver de services ;
- policy commune de Nestor.

Le domaine peut conserver des **références** vers ces objets, par exemple :

```text
deliberation_ref
mission_ref
knowledge_ref
actor_ref
```

mais pas recopier leur logique.

### 3.2 CRM / ERP

`mdtc-domain` ne doit pas devenir le système de prospection général.

Leads, campagnes, nurturing, relances marketing et pipeline commercial général peuvent vivre dans un outil externe.

Le domaine doit seulement conserver les références nécessaires :

```text
ExternalCommercialSystemRef
```

### 3.3 Documents rendus

La logique de mise en page, PDF, Factur-X, e-signature et composition documentaire appartient à `mdtc-docs` ou aux services dédiés.

Le domaine fournit les vérités résolues nécessaires aux documents.

### 3.4 Frontend

Le domaine ne doit pas contenir :

- composants React ;
- navigation ;
- règles de visibilité codées dans l'UI ;
- quotas de produit appliqués uniquement côté client.

Les interfaces consomment des projections et capacités calculées côté serveur.

---

## 4. Frontières avec Nestor et la noria MDTC

Architecture de référence :

```text
Surface d'exécution
        ↓
      Nestor
        ↓
Services transversaux
        ↓
    Noria MDTC
        ↓
   mdtc-domain
```

Nestor est le **socle d'exécution / réceptacle**.

La noria MDTC est la **strate métier BTP**.

`mdtc-domain` est le **contrat de domaine** de cette strate.

---

## 5. Invariants de conception

### 5.1 Séparation des concepts

```text
Program ≠ Project
Site ≠ Project
Lot ≠ WorkPackage
WorkPackage ≠ Task
WorkPackage ≠ QuoteLine
Task ≠ TimeEntry
```

### 5.2 Séparation épistémique

```text
Contribution ≠ Observation validée
Observation ≠ causalité
Causalité ≠ responsabilité
Expertise ≠ autorité
Autorité ≠ permission
Decision ≠ Action
```

### 5.3 État vivant vs histoire contractuelle

```text
current project state
≠
accepted contractual baseline
```

### 5.4 IA

```text
Nestor interpretation
≠
MDTC business truth
```

Une analyse IA peut produire un candidat, une hypothèse, une recommandation ou une relation proposée. Le domaine doit préserver le statut épistémique et le gate de validation requis.

### 5.5 UX

Le modèle peut être riche sans imposer une interface riche.

Principe :

> capturer beaucoup en profondeur, afficher peu en surface.

Les vues Qualification, Opérations, Historique ou Program sont des **projections** sur les mêmes objets, pas des modèles métier parallèles.

---

## 6. Décisions déjà adoptées

### Project

Un `Project` est l'enveloppe persistante d'une intention de travail suffisamment identifiée, depuis la qualification numérique ou terrain jusqu'à sa clôture, marché remporté ou non.

### Program

`Program` est adopté comme niveau standard de regroupement multi-projets.

Pour un lotissement, la stratégie par défaut est :

```text
Program
├── Project — Maison 01
├── Project — Maison 02
├── ...
└── Project — Commun / VRD
```

### Qualification numérique

Une demande d'estimation suffisamment qualifiée peut créer un `Project` avant toute visite ou contractualisation.

### UX

Les projets de qualification restent visibles dans une section dédiée et ne polluent pas la vue quotidienne des chantiers actifs.

### ERP / CRM

Le choix d'un outil externe reste ouvert. Le domaine ne doit dépendre ni d'Odoo, ni de Mautic, ni d'un autre produit particulier.

---

## 7. Principe d'évolution

Les contrats sont introduits progressivement à partir de cas réels et de spécimens représentatifs.

Une abstraction ne doit pas être ajoutée uniquement parce qu'elle « pourrait servir ».

Priorité :

```text
réalité métier
→ invariant
→ contrat de domaine
→ projection
→ implémentation
```
