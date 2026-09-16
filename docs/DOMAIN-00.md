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
DOMAIN-00.4 — Ressources / supply / custody
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

Status: **validated conceptually**

Document détaillé : [`DOMAIN-00.2-ACTORS-ROLES-TRADES.md`](./DOMAIN-00.2-ACTORS-ROLES-TRADES.md)

Invariants :

```text
Role ≠ Trade ≠ Authority ≠ Permission
Expertise ≠ Authority ≠ Permission ≠ Responsibility
```

---

## DOMAIN-00.3 — Contributions / faits / preuves

Status: **validated conceptually**

Document détaillé : [`DOMAIN-00.3-CONTRIBUTIONS-EVIDENCE.md`](./DOMAIN-00.3-CONTRIBUTIONS-EVIDENCE.md)

Mission Nestor associée : [`../missions/contracts/INTERPRET-PROJECT-CONTRIBUTION.md`](../missions/contracts/INTERPRET-PROJECT-CONTRIBUTION.md)

Noyau adopté :

```text
ProjectContribution
ContributionInterpretation
ContributorConfirmation
Evidence
EvidenceLink
Observation
ProjectEvent
Issue
Blocker
Risk
Decision
Action
```

Doctrine de mission Nestor :

> **Une interprétation prudente, traçable et fidèle est préférable à une reformulation élégante mais plus forte que ce que la source permet d'affirmer.**

---

## DOMAIN-00.4 — Materials / Equipment / Supply / Custody

Status: **validated conceptually**

Document détaillé : [`DOMAIN-00.4-RESOURCES-SUPPLY-CUSTODY.md`](./DOMAIN-00.4-RESOURCES-SUPPLY-CUSTODY.md)

Noyau adopté :

```text
MaterialSpecification
MaterialRequirement
MaterialSelection
EquipmentRequirement
EquipmentAssignment
EquipmentProvision
EquipmentHandover
EquipmentReturn
SupplyAssignment
ProcurementNeed
ResourceDelivery
DeliveredResourceItem
ResourceReceipt
ResourceAcceptance
MaterialUsageRecord
ResourceIncident
```

Invariants principaux :

```text
MaterialSpecification ≠ CommercialProduct
ResourceRequirement ≠ ProcurementNeed
EquipmentRequirement ≠ EquipmentAssignment
EquipmentAssignment ≠ EquipmentHandover
owner ≠ provider ≠ custodian ≠ operator
resource missing ≠ theft established
loss ≠ responsibility
```

---

## DOMAIN-00.5 — Planning / Time / Meetings / Execution

Status: **validated conceptually**

Document détaillé : [`DOMAIN-00.5-PLANNING-TIME-EXECUTION.md`](./DOMAIN-00.5-PLANNING-TIME-EXECUTION.md)

Noyau adopté :

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

Invariants principaux :

```text
WorkPackage ≠ ScheduleActivity
PLANNED ≠ FORECAST ≠ ACTUAL
published schedule ≠ contractual baseline
scheduled ≠ ready
Dependency ≠ Interface
planned milestone ≠ achieved milestone
BLOCKED ≠ SUSPENDED
```

```text
Blocker ≠ ScheduleVariance
Blocker ≠ Delay
schedule revised ≠ project delayed
ScheduleVariance ≠ Cause ≠ Responsibility
```

```text
SiteVisit ≠ TimeEntry
SiteMeeting ≠ TimeEntry ≠ BillableItem
MeetingAttendance ≠ TimeEntry
TimeEntry ≠ CostEntry ≠ PayrollEntry ≠ BillableItem
```

```text
external calendar ≠ authoritative MDTC schedule
external mutation → candidate → policy/gate → ScheduleRevision
external timesheet → provenance-preserving import → MDTC TimeEntry
integration failure ≠ domain state mutation
```

---

## DOMAIN-00.6 — Economics

Status: **validated conceptually**

Document détaillé : [`DOMAIN-00.6-ECONOMICS.md`](./DOMAIN-00.6-ECONOMICS.md)

Noyau adopté :

```text
MoneyValue
PreliminaryEstimate
CostEstimate
InternalBudget
BudgetRevision
CostCommitment
ActualCost
CostRateSnapshot
RevenueEstimate
ContractedRevenue
BilledRevenue
Settlement
EconomicAllocation
EconomicForecast
MarginProjection
```

Invariants principaux :

```text
COST ≠ REVENUE ≠ CASH
CostEstimate ≠ InternalBudget ≠ CommercialQuote
PreliminaryEstimate ≠ CommercialQuote ≠ Contract
```

```text
COMMITTED COST ≠ ACTUAL COST ≠ CASH OUT
quoted revenue ≠ contracted revenue ≠ billed revenue ≠ collected cash
ActualCost ≠ SupplierInvoice ≠ CashPayment
```

```text
TimeEntry ≠ ActualCost
CostRate ≠ SalaryRate
ResourceIncident ≠ ActualCost ≠ Responsibility
```

```text
CashIn ≠ Revenue
CashOut ≠ Cost
EconomicAllocation ≠ additional cost
EconomicAllocation ≠ causal attribution
```

```text
Margin = derived projection ≠ mutable source-of-truth field
AI estimate ≠ approved budget ≠ commercial price ≠ contractual commitment
```

---

## DOMAIN-00.7 — Quality / Changes / Responsibility

Status: **validated conceptually**

Document détaillé : [`DOMAIN-00.7-QUALITY-CHANGES-RESPONSIBILITY.md`](./DOMAIN-00.7-QUALITY-CHANGES-RESPONSIBILITY.md)

Mission Nestor associée : [`../missions/contracts/ASSESS-PROJECT-QUALITY-CHANGE-RESPONSIBILITY.md`](../missions/contracts/ASSESS-PROJECT-QUALITY-CHANGE-RESPONSIBILITY.md)

Noyau adopté :

```text
ApplicableQualityRequirement
ControlPoint
QualityInspection
InspectionResult
ExecutionIrregularity
Defect
NonConformity
NonConformityDisposition
WorkExecutionAssessment
QualityAssessment
Rework
CustomerFeedback
AcceptanceRecord
Reservation

ChangeRequest
ImpactAssessment
DecisionRecord
ChangeOrder
ProjectBaseline

ResponsibilityAssignment
CausalityAssessment
ResponsibilityAssessment
ResponsibilityAllocation
WarrantyClaim
WarrantyCoverageAssessment
```

Décisions adoptées :

- `fact ≠ causality ≠ responsibility ≠ legal effect` ;
- une exigence connue n'est pas automatiquement applicable au projet ;
- `Task ≠ ControlPoint` et `ControlPoint ≠ QualityInspection` ;
- une inspection effectuée n'est pas une inspection réussie et une inspection réussie n'est pas une réception contractuelle ;
- `Observation`, `ExecutionIrregularity`, `Defect` et `NonConformity` restent distincts ;
- une non-conformité sépare obligatoirement sa `nature`, ses impacts et sa disposition ;
- les natures initiales incluent `TECHNICAL`, `COSMETIC`, `OPERATIONAL`, `SAFETY`, `DOCUMENTARY`, `OTHER` ;
- une non-conformité technique n'établit pas automatiquement garantie, assurance ou responsabilité ;
- une non-conformité cosmétique peut être explicitement acceptée par l'autorité compétente sans effacer l'écart historique ;
- une non-conformité opératoire peut être corrigée ou ne pas avoir d'impact significatif sur le livrable sans pour autant être confondue avec une conformité historique ;
- après correction vérifiée, le scope courant peut redevenir conforme mais l'historique de la non-conformité reste immuable ;
- `client acceptance ≠ universal technical / contractual / regulatory / insurance authority` ;
- `WorkExecutionAssessment` porte sur l'exécution / le WorkPackage, jamais sur une note globale d'une personne ;
- `CustomerFeedback ≠ TechnicalQuality` ;
- un événement sécurité critique n'est jamais moyenné dans une note qualité ;
- `Rework ≠ Responsibility` et `rework cost ≠ liability` ;
- `Execution COMPLETED ≠ technical acceptance ≠ contractual acceptance` ;
- `Reservation ≠ NonConformity` ;
- `ChangeRequest ≠ approved change` et `ImpactAssessment ≠ approved change` ;
- une nouvelle révision documentaire produit un impact candidate, pas une mutation silencieuse ;
- `unknown impact ≠ zero impact` ;
- `internally approved change ≠ client-accepted ChangeOrder` ;
- une action d'urgence sécurité ne dépend pas d'un avenant commercial accepté ;
- une `ProjectBaseline` acceptée est une référence historique immuable ; une nouvelle baseline la supersède sans la réécrire ;
- `Project live state ≠ ProjectBaseline` ;
- `ResponsibilityAssignment ≠ RoleAssignment ≠ AuthorityAssignment ≠ Permission` ;
- une responsabilité assignée n'est pas un blâme ;
- temporalité et corrélation n'établissent pas la causalité ; causalité et responsabilité restent distinctes ;
- `ResponsibilityAssessment ≠ ResponsibilityAllocation` ;
- `EconomicAllocation ≠ ResponsibilityAllocation` ;
- une allocation métier de responsabilité n'est pas automatiquement un jugement juridique final ;
- `WarrantyClaim ≠ verified defect ≠ accepted coverage ≠ responsibility` ;
- Nestor produit des candidats et assessments traçables, jamais une responsabilité ou conclusion juridique autoritative sans gate compétent.

Invariants principaux :

```text
fact ≠ causality ≠ responsibility ≠ legal effect
```

```text
NONCONFORMITY NATURE
≠ NONCONFORMITY IMPACT
≠ NONCONFORMITY DISPOSITION
```

```text
accepted deviation ≠ historical conformity
corrected + verified nonconformity may restore current conformity
historical nonconformity remains immutable
```

```text
client acceptance
≠ universal technical / contractual / regulatory / insurance authority
```

```text
Rework ≠ Responsibility
rework cost ≠ liability
```

```text
ChangeRequest ≠ approved change
ImpactAssessment ≠ approved change
new technical document revision ≠ accepted Project change
unknown impact ≠ zero impact
```

```text
accepted baseline = immutable historical reference
Project live state ≠ ProjectBaseline
```

```text
ResponsibilityAssessment ≠ ResponsibilityAllocation
EconomicAllocation ≠ ResponsibilityAllocation
```

---

## DOMAIN-00.8 — Consolidation Nestor / event model / projections

Objectif : consolider l'ontologie, les événements et les projections consommées par :

- frontend MDTC ;
- `mdtc-docs` ;
- Nestor ;
- intégrations externes ;
- analytics / knowledge mining.

À reprendre explicitement :

```text
capability discovery
noria requires / exposes
FastMCP actuel
Langflow candidat / hypothèse
providers externes
onboarding partenaires
matrix d'autorité des systèmes externes
fédération inter-Nestor
local-first / nest0r.ai optionnel
pattern mining / promotion de connaissance
notification / audience / projections
```

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
contribution terrain multilingue voix + photos
mise à disposition équipement MDTC → salarié/sous-traitant
équipement fourni par un sous-traitant
perte/casse/disparition de ressource sans attribution automatique de faute
planning révisé sans écrasement de l'historique
timesheet externe avec provenance
calendrier externe projeté sans autorité sur le planning
budget interne distinct du devis
engagement fournisseur avant facture
coût réel avant paiement
paiement partiel avec allocation
forecast EAC / marge finale
non-conformité cosmétique acceptée par autorité compétente
non-conformité opératoire corrigée avant livraison
non-conformité technique avec impact garantie/assurance à évaluer
rework avec coût mais sans responsabilité établie
responsabilité contestée / causalité multihypothèse
nouvelle révision technique sans mutation automatique de baseline
client change request avec impact économique mais sans ChangeOrder accepté
```

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
Contribution ≠ Observation structurée
Observation ≠ causalité
Causalité ≠ responsabilité
Expertise ≠ autorité ≠ permission
Decision ≠ Action
Project live state ≠ contractual baseline
Nestor interpretation ≠ MDTC business truth
```

```text
Role ≠ Trade ≠ Authority ≠ Permission
Partner ≠ Subcontractor
Supplier ≠ Subcontractor
```

```text
RAW ≠ INTERPRETED ≠ AUTHOR-CONFIRMED ≠ DOMAIN-VALIDATED
source_language ≠ interaction_language ≠ canonical_language
```

```text
owner ≠ provider ≠ custodian ≠ operator
resource missing ≠ theft established
loss ≠ responsibility
```

```text
PLANNED ≠ FORECAST ≠ ACTUAL
TimeEntry ≠ CostEntry ≠ PayrollEntry ≠ BillableItem
external calendar ≠ authoritative MDTC schedule
```

```text
COST ≠ REVENUE ≠ CASH
CostEstimate ≠ InternalBudget ≠ CommercialQuote
ActualCost ≠ SupplierInvoice ≠ CashPayment
Margin ≠ source-of-truth field
```

```text
fact ≠ causality ≠ responsibility ≠ legal effect
NONCONFORMITY NATURE ≠ NONCONFORMITY IMPACT ≠ NONCONFORMITY DISPOSITION
Rework ≠ Responsibility
EconomicAllocation ≠ ResponsibilityAllocation
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
Contribution terrain mobile
Matériel mis à disposition
Retours / anomalies ressources
Planning projet
Mes heures
Réunions / visites
Budget chantier
Coûts engagés / réels
Facturé / encaissé
Forecast coût final / marge
Contrôles qualité
Non-conformités / dispositions
Réserves / réception
Changements / impacts
Responsabilités / causalité
AFTERCARE / réclamations
```

## Prochaine décision

DOMAIN-00.7 étant validé conceptuellement, la prochaine discussion porte sur :

**DOMAIN-00.8 — Consolidation Nestor / event model / projections**.
