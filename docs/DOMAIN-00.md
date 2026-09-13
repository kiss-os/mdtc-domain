# DOMAIN-00 — Ontologie fondamentale du chantier

Status: `in_design`

## Objectif

DOMAIN-00 établit les contrats métier fondamentaux nécessaires à la noria MDTC avant de poursuivre les projections documentaires de type devis, CCTP, facture ou rapport.

Le domaine doit pouvoir représenter aussi bien :

- une qualification numérique exploratoire ;
- une visite ou un constat sur site ;
- une intervention simple ;
- un chantier multi-lots ;
- un programme multi-projets comme un lotissement ;
- une opération non remportée mais ayant généré du travail, des preuves et des connaissances réutilisables.

## Roadmap

### DOMAIN-00.1 — Project / Program / Work Breakdown / WorkPackage

À stabiliser :

```text
Program
Project
Site / Location
Lot
WorkPackage
Task
ProjectContribution
ProjectEvent
Requirement
Dependency
Interface
Evidence
Baseline
```

Décisions adoptées :

- `Program` est le niveau standard de regroupement multi-projets.
- un lotissement est par défaut un `Program` contenant un `Project` par maison, plus éventuellement des projets dédiés aux communs / VRD ;
- `Project` ne signifie ni marché signé ni chantier actif ;
- une qualification numérique peut déjà produire un `Project` ;
- une visite ou un constat peut être un `Project` autonome ;
- les vues Qualification / Opérations / Historique sont des projections, pas des modèles distincts ;
- `Site ≠ Project` ;
- tous les niveaux `Program → Project → Lot → WorkPackage → Task` ne sont pas obligatoires.

Définition de travail :

> Un `Project` est l'enveloppe persistante d'une intention de travail suffisamment identifiée, depuis sa première qualification numérique ou terrain jusqu'à sa clôture, qu'un marché soit finalement remporté ou non.

### DOMAIN-00.2 — Actors / Organizations / Roles / Trades

À cadrer :

- personnes ;
- organisations ;
- acteurs projet ;
- rôles contextuels ;
- corps de métier ;
- partenaires ;
- sous-traitants ;
- BET ;
- autorités et responsabilités.

Invariant :

```text
expertise ≠ authority ≠ permission ≠ responsibility
```

### DOMAIN-00.3 — Materials / Equipment / Supply / Procurement

À cadrer :

- matériaux incorporés ;
- équipements / moyens d'exécution ;
- location ;
- besoins ;
- fourniture client ;
- achat ;
- livraison ;
- approbation ;
- provenance.

### DOMAIN-00.4 — Economics

Axes :

```text
ESTIMATED
COMMITTED
ACTUAL
BILLED
PAID
```

Le modèle doit permettre l'analyse des écarts et marges sans confondre coût réel et facturation.

### DOMAIN-00.5 — Planning / Time / Meetings / Execution

À cadrer :

- planning ;
- TimeEntry ;
- visite ;
- réunion ;
- jalon ;
- dépendances ;
- blocage ;
- suspension ;
- état d'exécution.

Invariant :

```text
SiteMeeting ≠ TimeEntry ≠ BillableItem
```

### DOMAIN-00.6 — Changes / Baselines / Decisions / Actions / Impacts

Flux candidat :

```text
Observation / SiteMeeting
        ↓
ChangeRequest
        ↓
ImpactAssessment
        ↓
Decision
        ↓
ChangeOrder / nouvelle baseline
```

Une baseline acceptée ne doit pas être réécrite.

### DOMAIN-00.7 — Responsibility / Warranty context / Evidence

Le domaine décrit les faits nécessaires au raisonnement juridique sans inventer automatiquement la règle de droit.

Invariant :

```text
fact
≠ causality
≠ responsibility
≠ legal effect
```

### DOMAIN-00.8 — Consolidation / event model / projections

Objectif : consolider l'ontologie, les événements et les projections consommées par :

- frontend MDTC ;
- mdtc-docs ;
- Nestor ;
- intégrations externes ;
- analytics / knowledge mining.

---

## Contributions humaines et Nestor

Le domaine doit accepter des contributions provenant de :

```text
commentaire
photo
plan
document
mesure
annotation
```

mais préserver la séparation :

```text
ProjectContribution
        ↓ analyse Nestor
Candidate
        ↓ validation / gate
Domain object
```

Un message ou une analyse IA ne devient pas automatiquement une observation, une décision, une cause ou une responsabilité.

## Qualité et apprentissage

Le domaine doit permettre de conserver les faits nécessaires à des analyses ultérieures :

```text
estimated labour
actual labour
quality assessment
rework
irregularities
customer feedback
evidence
```

La connaissance globale et les patterns ne sont pas promus directement par `mdtc-domain`. Ils alimentent les mécanismes communs Nestor / Chronos / graph après les gates appropriés.

## Projection UX

Principe :

> **capturer beaucoup en profondeur, afficher peu en surface**.

Exemple de projections :

```text
Qualification
Opérations
Historique
Program overview
Mes chantiers
Mes WorkPackages
```

Ces vues doivent être filtrables selon le périmètre de responsabilité effectif de l'acteur.

## Prochaine décision

La prochaine étape de DOMAIN-00.1 est de formaliser précisément :

```text
Lot
WorkPackage
Task
```

et leurs relations avec :

```text
Trade
QuoteLine
TimeEntry
Requirement
Dependency
Responsibility
```
