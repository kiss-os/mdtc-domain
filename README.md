# mdtc-domain

> **Contrats de domaine construction de la noria MDTC** — projets, work lots, work packages, acteurs, ressources, planning, économie, exécution, preuves, qualité, changements et responsabilités.

`mdtc-domain` formalise la vérité métier de la noria **MDTC (Méditerranée Construction)**. Le dépôt décrit les objets, invariants, relations et transitions qui doivent rester indépendants de l'interface utilisateur, du moteur documentaire, du CRM/ERP choisi et de l'implémentation des modèles IA.

## Position dans l'architecture

```text
Infrastructure / surface d'exécution
            ↓
          Nestor
socle d'exécution / réceptacle
            ↓
 services transversaux communs
            ↓
        Noria MDTC
            ↓
 ┌──────────┼───────────┐
 │          │           │
mdtc-     mdtc-      interfaces /
domain    docs        intégrations
```

- **Nestor** fournit le socle d'exécution, l'orchestration, la délibération, le contexte utilisateur et les services transversaux.
- **La noria MDTC** ajoute la strate métier propre au BTP et à l'exploitation MDTC.
- **`mdtc-domain`** est une brique de cette noria ; ce dépôt n'est pas la noria complète.
- **`mdtc-docs`** projette des vérités déjà résolues en documents déterministes ; il ne décide pas de la vérité métier.

## Doctrine

Le dépôt doit pouvoir répondre à une question simple :

> **Qu'est-ce qui est vrai dans le métier MDTC, indépendamment de la manière dont cette vérité est affichée, documentée ou analysée ?**

Quelques invariants structurants :

```text
Program ≠ Project
Site ≠ Project
Project ≠ marché signé
Project ≠ chantier actif
WorkLot ≠ Parcel
WorkLot ≠ WorkPackage
WorkPackage ≠ Task
WorkPackage ≠ QuoteLine
Task ≠ TimeEntry
Task ≠ ControlPoint
Contribution ≠ Observation structurée
Evidence ≠ vérité
Observation ≠ causalité
Causalité ≠ responsabilité
Expertise ≠ autorité ≠ permission
Role ≠ Trade ≠ Authority ≠ Permission
Partner ≠ Subcontractor
Supplier ≠ Subcontractor
Decision ≠ Action
Project live state ≠ contractual baseline
Nestor interpretation ≠ MDTC business truth
RAW ≠ INTERPRETED ≠ AUTHOR-CONFIRMED ≠ DOMAIN-VALIDATED
owner ≠ provider ≠ custodian ≠ operator
resource missing ≠ theft established
loss ≠ responsibility
PLANNED ≠ FORECAST ≠ ACTUAL
TimeEntry ≠ CostEntry ≠ PayrollEntry ≠ BillableItem
external calendar ≠ authoritative MDTC schedule
COST ≠ REVENUE ≠ CASH
CostEstimate ≠ InternalBudget ≠ CommercialQuote
ActualCost ≠ SupplierInvoice ≠ CashPayment
Margin ≠ source-of-truth field
fact ≠ causality ≠ responsibility ≠ legal effect
NONCONFORMITY NATURE ≠ NONCONFORMITY IMPACT ≠ NONCONFORMITY DISPOSITION
Rework ≠ Responsibility
EconomicAllocation ≠ ResponsibilityAllocation
```

## Hiérarchie opérationnelle

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

`Project` et `WorkPackage` sont les deux niveaux structurants. `Program`, `WorkLot` et `Task` sont utilisés lorsqu'ils apportent une valeur métier réelle.

`WorkLot` désigne un lot technique et/ou contractuel de travaux. Un lot foncier est modélisé séparément comme `Parcel`.

Voir :

- [`docs/DOMAIN-00.1-WORK-BREAKDOWN.md`](./docs/DOMAIN-00.1-WORK-BREAKDOWN.md) — structure du travail ;
- [`docs/DOMAIN-00.2-ACTORS-ROLES-TRADES.md`](./docs/DOMAIN-00.2-ACTORS-ROLES-TRADES.md) — acteurs, rôles et métiers ;
- [`docs/DOMAIN-00.3-CONTRIBUTIONS-EVIDENCE.md`](./docs/DOMAIN-00.3-CONTRIBUTIONS-EVIDENCE.md) — contributions, preuves, observations et interprétation multilingue ;
- [`docs/DOMAIN-00.4-RESOURCES-SUPPLY-CUSTODY.md`](./docs/DOMAIN-00.4-RESOURCES-SUPPLY-CUSTODY.md) — matériaux, équipements, fourniture, garde et incidents ressources ;
- [`docs/DOMAIN-00.5-PLANNING-TIME-EXECUTION.md`](./docs/DOMAIN-00.5-PLANNING-TIME-EXECUTION.md) — planning, temps, réunions et exécution ;
- [`docs/DOMAIN-00.6-ECONOMICS.md`](./docs/DOMAIN-00.6-ECONOMICS.md) — estimation, budget, engagements, coûts réels, revenus, trésorerie et marges ;
- [`docs/DOMAIN-00.7-QUALITY-CHANGES-RESPONSIBILITY.md`](./docs/DOMAIN-00.7-QUALITY-CHANGES-RESPONSIBILITY.md) — qualité, non-conformités, changements, baselines, causalité et responsabilités ;
- [`missions/contracts/INTERPRET-PROJECT-CONTRIBUTION.md`](./missions/contracts/INTERPRET-PROJECT-CONTRIBUTION.md) — mission Nestor d'interprétation des contributions ;
- [`missions/contracts/ASSESS-PROJECT-QUALITY-CHANGE-RESPONSIBILITY.md`](./missions/contracts/ASSESS-PROJECT-QUALITY-CHANGE-RESPONSIBILITY.md) — mission Nestor d'assessment qualité/changement/responsabilité.

## Contributions terrain multilingues

Les intervenants peuvent contribuer dans leur langue réelle de travail via texte, dictée, photo ou document.

```text
source_language
interaction_language
canonical_language = fr
```

Flux adopté :

```text
ProjectContribution originale
↓
ContributionInterpretation
↓
restitution dans la langue de l'auteur
↓
ContributorConfirmation
↓
candidats métier
↓
gate
↓
objet métier autoritatif
```

Doctrine Nestor :

> **Une interprétation prudente, traçable et fidèle est préférable à une reformulation élégante mais plus forte que ce que la source permet d'affirmer.**

## Ressources, équipements et garde

```text
MaterialSpecification
→ MaterialRequirement
→ MaterialSelection
```

```text
EquipmentRequirement
→ EquipmentAssignment
→ EquipmentProvision
→ EquipmentHandover
→ EquipmentReturn
```

La transaction commerciale d'une location reste hors scope ; la réalité chantier de la machine louée reste dans le domaine.

```text
rental transaction ≠ project equipment custody
```

Les incidents ressources restent factuels :

```text
resource missing
≠ theft established
≠ perpetrator identified
≠ responsibility established
```

## Planning, temps et exécution

Le domaine sépare structure du travail, planification, forecast, réel et temps humain déclaré.

```text
WorkPackage / Task
        │
        ├── Schedule → quand cela doit arriver
        ├── Execution → ce qui arrive réellement
        ├── TimeEntry → temps humain réellement déclaré
        └── ProjectEvent → événements significatifs
```

Principes :

```text
WorkPackage ≠ ScheduleActivity
PLANNED ≠ FORECAST ≠ ACTUAL
scheduled ≠ ready
BLOCKED ≠ SUSPENDED
Blocker ≠ ScheduleVariance ≠ Cause ≠ Responsibility
SiteMeeting ≠ TimeEntry ≠ BillableItem
Execution COMPLETED ≠ ACCEPTED ≠ CLOSEOUT
```

Les calendriers et timesheets externes peuvent être projetés/importés avec provenance, mais ils ne deviennent jamais implicitement l'autorité du domaine.

```text
external calendar ≠ authoritative MDTC schedule
external mutation → candidate → policy/gate → ScheduleRevision
external timesheet → provenance-preserving import → MDTC TimeEntry
integration failure ≠ domain state mutation
```

## Économie chantier

DOMAIN-00.6 sépare coût, revenu et trésorerie.

```text
COST ≠ REVENUE ≠ CASH
```

Côté coûts :

```text
CostEstimate
↓
InternalBudget
↓
CostCommitment
↓
ActualCost
↓
CashOut
```

Côté revenus :

```text
RevenueEstimate
↓
ContractedRevenue
↓
BilledRevenue
↓
CashIn / Collected
```

Principes :

```text
CostEstimate ≠ InternalBudget ≠ CommercialQuote
PreliminaryEstimate ≠ CommercialQuote ≠ Contract
COMMITTED COST ≠ ACTUAL COST ≠ CASH OUT
quoted revenue ≠ contracted revenue ≠ billed revenue ≠ collected cash
ActualCost ≠ SupplierInvoice ≠ CashPayment
CashIn ≠ Revenue
CashOut ≠ Cost
```

Le coût travail est dérivé de `TimeEntry` via un `CostRateSnapshot` traçable ; le domaine ne devient pas un moteur RH/paie.

Les allocations économiques répartissent des valeurs existantes et ne doivent ni créer de double comptage ni être assimilées à une causalité.

La marge est une projection explicable et datée :

```text
Margin = derived projection ≠ mutable source-of-truth field
```

## Qualité, changements et responsabilité

DOMAIN-00.7 impose la séparation :

```text
FACT
≠ CAUSALITY
≠ RESPONSIBILITY
≠ AUTHORITY
≠ LEGAL EFFECT
```

La qualité suit un enchaînement explicable :

```text
ApplicableQualityRequirement
↓
ControlPoint
↓
QualityInspection
↓
InspectionResult
↓
Observation / Irregularity / Defect
↓
NonConformity?
↓
Disposition / Rework / Acceptance
```

La non-conformité elle-même sépare trois dimensions :

```text
NONCONFORMITY NATURE
≠ NONCONFORMITY IMPACT
≠ NONCONFORMITY DISPOSITION
```

Une variation esthétique peut donc être une non-conformité réelle mais acceptée en l'état par l'autorité compétente ; une non-conformité opératoire peut être corrigée ou rester sans impact significatif sur le livrable ; une non-conformité technique peut nécessiter analyse de durabilité, garantie ou assurance sans établir automatiquement couverture ou responsabilité.

```text
accepted deviation ≠ historical conformity
corrected + verified nonconformity may restore current conformity
historical nonconformity remains immutable
```

Les décisions d'acceptation conservent leur autorité :

```text
client acceptance
≠ universal technical / contractual / regulatory / insurance authority
```

Le domaine n'établit aucune note globale des personnes :

```text
WorkExecutionAssessment → execution / WorkPackage
WorkExecutionAssessment ≠ global person rating
```

Changements :

```text
ChangeRequest
↓
ImpactAssessment
↓
DecisionRecord
↓
ChangeOrder?
↓
ProjectBaseline revision?
```

Une baseline acceptée est immuable :

```text
accepted baseline = immutable historical reference
Project live state ≠ ProjectBaseline
```

Responsabilité :

```text
ResponsibilityAssignment
≠ ResponsibilityAssessment
≠ ResponsibilityAllocation
```

et :

```text
EconomicAllocation ≠ ResponsibilityAllocation
Rework ≠ Responsibility
rework cost ≠ liability
```

Nestor peut proposer des assessments et candidats mais conserve les gates humains/autoritatifs pour les décisions à enjeu.

## Définition actuelle de `Project`

Un `Project` est l'enveloppe persistante d'une intention de travail suffisamment identifiée, depuis sa première qualification numérique ou terrain jusqu'à sa clôture, qu'un marché soit finalement remporté ou non.

Le **chantier** est une phase possible d'un `Project`, pas sa définition.

## Cycle projet — cadrage courant

```text
QUALIFICATION
    ↓
PROPOSAL
    ↓
CONTRACTED
    ↓
PREPARATION
    ↓
EXECUTION
    ↓
ACCEPTANCE
    ↓
CLOSEOUT
    ↓
AFTERCARE
    ↓
CLOSED
```

Le résultat commercial est orthogonal au cycle :

```text
OPEN | WON | LOST | WITHDRAWN | EXPIRED | NOT_APPLICABLE
```

## Frontières de responsabilité

`mdtc-domain` **doit** contenir les contrats métier relatifs notamment à :

- Program / Project / Site ;
- WorkLot / WorkPackage / Task ;
- acteurs, organisations, rôles et corps de métier ;
- contributions terrain, preuves, observations, événements et décisions ;
- matériaux, équipements, approvisionnement, mise à disposition, garde et incidents ressources ;
- planning, temps, réunions et exécution ;
- économie chantier ;
- exigences applicables, contrôles, non-conformités et dispositions ;
- changements, baselines et réception ;
- causalité, responsabilité et contexte de garantie ;
- projections opérationnelles.

`mdtc-domain` **ne doit pas** devenir :

- un CRM ou ERP complet ;
- un WMS complet ;
- un grand livre comptable ;
- un moteur de paie ;
- un moteur juridique ou assurantiel autonome ;
- une plateforme e-commerce ou un moteur commercial de location ;
- un moteur de rendu PDF ;
- un routeur LLM ;
- un stockage de prompts spécifiques à des modèles ;
- le moteur de délibération Nestor ;
- une duplication de la CCU, de Chronos ou du graphe de connaissance commun ;
- une implémentation d'interface React.

Les `Mission Contracts` décrivent des objectifs, gates et contraintes de capacités sans hardcoder un fournisseur ou modèle.

## DOMAIN-00

Roadmap actuelle :

1. **DOMAIN-00.1** — Project / Program / Work Breakdown / WorkPackage — **validé conceptuellement**
2. **DOMAIN-00.2** — Actors / Organizations / Roles / Trades — **validé conceptuellement**
3. **DOMAIN-00.3** — Contributions / faits / preuves — **validé conceptuellement**
4. **DOMAIN-00.4** — Materials / Equipment / Supply / Custody — **validé conceptuellement**
5. **DOMAIN-00.5** — Planning / TimeEntry / Meetings / execution lifecycle — **validé conceptuellement**
6. **DOMAIN-00.6** — Economics: estimated / committed / actual / billed / paid / margin — **validé conceptuellement**
7. **DOMAIN-00.7** — Quality / Changes / Responsibility — **validé conceptuellement**
8. **DOMAIN-00.8** — Consolidation Nestor / event model / projections
9. **DOMAIN-00.9** — Spécimens / validation
10. **DOMAIN-00.10** — Consolidation technique

Le cadrage vivant de DOMAIN-00 est documenté dans [`docs/DOMAIN-00.md`](./docs/DOMAIN-00.md).

## Statut

Le dépôt est en phase de **fondation ontologique**. Les contrats sont ajoutés progressivement après validation métier ; une présence dans le dépôt ne doit pas être interprétée comme une implémentation complète du backend MDTC.
