# mdtc-domain

> **Contrats de domaine construction de la noria MDTC** — projets, work lots, work packages, acteurs, ressources, planning, économie, exécution, preuves et responsabilités.

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
- [`missions/contracts/INTERPRET-PROJECT-CONTRIBUTION.md`](./missions/contracts/INTERPRET-PROJECT-CONTRIBUTION.md) — contrat de mission Nestor associé à DOMAIN-00.3.

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

Le noyau de DOMAIN-00.5 comprend :

```text
Schedule
ScheduleRevision
ScheduleActivity
Dependency
Milestone
ReadinessAssessment
ExecutionStateProjection
ProgressAssessment
ScheduleVariance
TimeEntry
SiteVisit
SiteMeeting
MeetingAttendance
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

L'architecture détaillée des providers, MCP, FastMCP, discovery et fédération est différée à DOMAIN-00.8.

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
- risques, changements et qualité ;
- responsabilité et contexte de garantie ;
- baselines contractuelles et projections opérationnelles.

`mdtc-domain` **ne doit pas** devenir :

- un CRM ou ERP complet ;
- un WMS complet ;
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
6. **DOMAIN-00.6** — Economics: estimated / committed / actual / billed / paid / margin
7. **DOMAIN-00.7** — Quality / Changes / Responsibility
8. **DOMAIN-00.8** — Consolidation Nestor / event model / projections
9. **DOMAIN-00.9** — Spécimens / validation
10. **DOMAIN-00.10** — Consolidation technique

Le cadrage vivant de DOMAIN-00 est documenté dans [`docs/DOMAIN-00.md`](./docs/DOMAIN-00.md).

## Statut

Le dépôt est en phase de **fondation ontologique**. Les contrats sont ajoutés progressivement après validation métier ; une présence dans le dépôt ne doit pas être interprétée comme une implémentation complète du backend MDTC.
