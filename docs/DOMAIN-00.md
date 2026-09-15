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

## Fil rouge

Le plan reste vivant et amendable au fur et à mesure des arbitrages métier.

```text
DOMAIN-00.0 — Fondations / frontières
DOMAIN-00.1 — Structure du travail
DOMAIN-00.2 — Acteurs / organisations / rôles / métiers
DOMAIN-00.3 — Contributions / faits / preuves
DOMAIN-00.4 — Ressources / supply / procurement
DOMAIN-00.5 — Planning / temps / exécution
DOMAIN-00.6 — Économie chantier
DOMAIN-00.7 — Qualité / changements / responsabilités
DOMAIN-00.8 — Consolidation Nestor / event model / projections
DOMAIN-00.9 — Spécimens / validation
DOMAIN-00.10 — Consolidation technique
```

Pour chaque étape :

```text
Discussion métier
↓
cas réels / contre-exemples
↓
schéma Excalidraw
↓
invariants
↓
contrat proposé
↓
validation Président
↓
écriture dans mdtc-domain
↓
spécimens / tests
```

---

## DOMAIN-00.1 — Project / Program / Work Breakdown / WorkPackage

Status: **validated conceptually**

Document détaillé : [`DOMAIN-00.1-WORK-BREAKDOWN.md`](./DOMAIN-00.1-WORK-BREAKDOWN.md)

Hiérarchie validée :

```text
Program?
   ↓
Project
   ↓
WorkLot?
   ↓
WorkPackage
   ↓
Task?
```

Décisions adoptées :

- `Program` est le niveau standard de regroupement multi-projets ;
- un lotissement est par défaut un `Program` contenant un `Project` par maison, plus éventuellement des projets dédiés aux communs / VRD ;
- `Project` ne signifie ni marché signé ni chantier actif ;
- une qualification numérique peut déjà produire un `Project` ;
- une visite ou un constat peut être un `Project` autonome ;
- les vues Qualification / Opérations / Historique sont des projections, pas des modèles distincts ;
- `Site ≠ Project` ;
- `WorkLot` remplace le terme générique `Lot` dans le contrat de domaine pour éviter l'ambiguïté avec `Parcel` ;
- `WorkLot` est optionnel ;
- `Task` est optionnelle ;
- `WorkPackage` est l'unité opérationnelle centrale ;
- un `WorkPackage` appartient à exactement un `Project` et au maximum un `WorkLot` ;
- un besoin multi-lots est préférentiellement décomposé en plusieurs `WorkPackage` reliés par `Interface` / `Dependency` ;
- `Task ≠ TimeEntry` ;
- `Task ≠ ControlPoint` ;
- `WorkPackage ≠ QuoteLine` ;
- pendant la qualification, `WorkPackageCandidate ≠ WorkPackage`.

Définition de travail :

> Un `Project` est l'enveloppe persistante d'une intention de travail suffisamment identifiée, depuis sa première qualification numérique ou terrain jusqu'à sa clôture, qu'un marché soit finalement remporté ou non.

Définition de `WorkPackage` :

> Un `WorkPackage` est une unité cohérente de travail suffisamment autonome pour être estimée, planifiée, affectée, suivie et contrôlée.

---

## DOMAIN-00.2 — Actors / Organizations / Roles / Trades

À cadrer :

- personnes ;
- organisations ;
- acteurs projet ;
- rôles contextuels ;
- corps de métier ;
- partenaires ;
- sous-traitants ;
- BET ;
- contremaître ;
- salariés ;
- client ;
- autorités et responsabilités ;
- expertise contextualisée.

Invariant :

```text
expertise ≠ authority ≠ permission ≠ responsibility
```

---

## DOMAIN-00.3 — Contributions / faits / preuves

À cadrer :

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

Entrées typiques :

```text
commentaire
photo
plan
document
mesure
annotation
```

Séparation à préserver :

```text
ProjectContribution
        ↓ analyse Nestor
Candidate
        ↓ validation / gate
Domain object
```

Un message ou une analyse IA ne devient pas automatiquement une observation, une décision, une cause ou une responsabilité.

---

## DOMAIN-00.4 — Materials / Equipment / Supply / Procurement

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

---

## DOMAIN-00.5 — Planning / Time / Meetings / Execution

À cadrer :

- planning ;
- `TimeEntry` ;
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

---

## DOMAIN-00.6 — Economics

Axes :

```text
ESTIMATED
COMMITTED
ACTUAL
BILLED
PAID
```

Le modèle doit permettre l'analyse des écarts et marges sans confondre coût réel et facturation.

---

## DOMAIN-00.7 — Quality / Changes / Responsibility

À cadrer notamment :

```text
WorkExecutionAssessment
ExecutionIrregularity
Rework
QualityAssessment
CustomerFeedback
ChangeRequest
ImpactAssessment
DecisionRecord
ChangeOrder
ProjectBaseline
ResponsibilityAllocation
```

Une baseline acceptée ne doit pas être réécrite.

Le domaine décrit les faits nécessaires au raisonnement juridique sans inventer automatiquement la règle de droit.

Invariant :

```text
fact
≠ causality
≠ responsibility
≠ legal effect
```

---

## DOMAIN-00.8 — Consolidation Nestor / event model / projections

Objectif : consolider l'ontologie, les événements et les projections consommées par :

- frontend MDTC ;
- `mdtc-docs` ;
- Nestor ;
- intégrations externes ;
- analytics / knowledge mining.

Le domaine peut référencer :

```text
deliberation_ref
mission_ref
knowledge_ref
actor_ref
```

mais ne réimplémente pas le moteur de délibération, Chronos, CCU, IAM ou le routage modèles.

---

## DOMAIN-00.9 — Spécimens

Cas minimum visés :

```text
muret simple
pose fenêtre
constat / visite sans marché remporté
rénovation multi-lots
maison individuelle
lotissement / Program multi-Projects
qualification numérique non convertie
```

Le but est de vérifier que le domaine reste proportionné aussi bien pour une petite intervention que pour une opération complexe.

---

## DOMAIN-00.10 — Consolidation technique

À ce stade seulement :

- packages ;
- IDs ;
- types ;
- relations ;
- événements ;
- invariants exécutables ;
- migrations ;
- tests ;
- compatibilité avec les projections documentaires et frontend.

---

## Invariants transversaux

```text
Program ≠ Project
Site ≠ Project
WorkLot ≠ Parcel
WorkLot ≠ WorkPackage
WorkPackage ≠ Task
WorkPackage ≠ QuoteLine
Task ≠ TimeEntry
Task ≠ ControlPoint
```

```text
Contribution ≠ Observation validée
Observation ≠ causalité
Causalité ≠ responsabilité
Expertise ≠ autorité ≠ permission
Decision ≠ Action
Project live state ≠ contractual baseline
Nestor interpretation ≠ MDTC business truth
```

## Projection UX

Principe :

> **capturer beaucoup en profondeur, afficher peu en surface**.

Exemples :

```text
Qualification
Opérations
Historique
Program overview
Mes chantiers
Mes WorkPackages
```

Ces vues sont des projections sur les mêmes objets et doivent être filtrables selon le périmètre de responsabilité effectif de l'acteur.

## Prochaine décision

DOMAIN-00.1 étant validé conceptuellement, la prochaine discussion porte sur :

**DOMAIN-00.2 — Actors / Organizations / Roles / Trades**.
