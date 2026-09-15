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

Décisions adoptées :

- `Person` et `Organization` restent des identités du socle ; `mdtc-domain` les référence via `PartyRef` ;
- `ProjectActor` représente la participation contextualisée d'une personne ou organisation à un `Project` ;
- `RoleAssignment` décrit la fonction exercée dans un scope donné ;
- `TradeAssignment` décrit la discipline / le corps de métier dans un scope donné ;
- `RepresentationAssignment` relie une personne à l'organisation au nom de laquelle elle agit ;
- `AuthorityAssignment` représente une autorité métier contextualisée sans devenir une permission système ;
- `WorkScopeRef` permet de porter le scope au niveau `Program`, `Project`, `WorkLot`, `WorkPackage` ou `Task` ;
- l'expertise globale/calibrée reste dans Nestor / CCU ; le domaine peut seulement la référencer ;
- les permissions effectives restent résolues par Nestor / CCU / Mithra ;
- la responsabilité détaillée est reportée à DOMAIN-00.7 ;
- `PARTNER ≠ SUBCONTRACTOR` ;
- `SUPPLIER ≠ SUBCONTRACTOR`.

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

Chaîne de traitement multilingue adoptée :

```text
ProjectContribution originale
        ↓
Nestor interprète
        ↓
ContributionInterpretation
        ↓
restitution dans interaction_language
        ↓
ContributorConfirmation
        ↓
ConfirmedInterpretation
        ↓
candidats métier
        ↓ gate éventuel
objet métier autoritatif
```

Niveaux épistémiques :

```text
L0 — RAW
L1 — INTERPRETED
L2 — AUTHOR-CONFIRMED
L3 — DOMAIN-VALIDATED
```

Décisions adoptées :

- l'original est conservé et n'est jamais remplacé silencieusement par un dérivé ;
- `original ≠ transcription ≠ traduction ≠ interprétation` ;
- les contributions peuvent être produites dans la langue réelle de l'intervenant ;
- `source_language`, `interaction_language` et `canonical_language` sont distinguées ;
- le français est la langue canonique de référence pour les objets métier MDTC ;
- la restitution utilisateur peut être faite dans sa langue d'interaction, par exemple en turc ;
- l'UX mobile doit privilégier voix, photo, correction et confirmation simple ;
- une lecture vocale de la restitution doit pouvoir être proposée ;
- l'auteur confirme que Nestor a correctement compris son propos, pas que le fait est techniquement vérifié ;
- `ContributionInterpretation` sépare obligatoirement `STATED_BY_CONTRIBUTOR`, `OBSERVED_FROM_EVIDENCE`, `INFERRED` et `UNKNOWN` ;
- Nestor ne doit pas embellir, durcir ou accroître la certitude du propos source ;
- Nestor ne devient jamais l'auteur fictif d'une contribution humaine ;
- `Evidence` documente ou étaye mais n'est pas assimilée à la vérité ;
- une `Observation` peut être `REPORTED`, `CORROBORATED`, `VERIFIED`, `DISPUTED` ou `RETRACTED` ;
- `ProjectEvent` représente un événement métier, pas l'event bus technique ;
- document reçu, revu, approuvé et baseline sont distincts ;
- `Issue`, `Blocker`, `Delay` et `Risk` restent distincts ;
- `Decision ≠ Action` ;
- `Action COMPLETED ≠ Issue RESOLVED` ;
- une contribution banale peut rester uniquement une contribution ;
- les actes à enjeu juridique, contractuel, financier, sécurité ou responsabilité nécessitent un gate d'autorité supplémentaire.

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

Décisions adoptées :

- `Material ≠ Equipment` ;
- la spécification technique reste distincte du produit commercial ;
- le besoin ressource reste distinct du besoin d'achat ;
- une proposition de produit ou substitution n'est jamais assimilée à une approbation ;
- `supplied_by`, `paid_by`, `installed_by`, `owned_by` et `liable_for` sont des dimensions distinctes ;
- une fourniture client ne détermine pas automatiquement la responsabilité d'un défaut ;
- l'affectation d'un équipement ne signifie pas qu'il a été physiquement remis à un acteur ;
- `owner`, `provider`, `custodian` et `operator` restent distincts ;
- les équipements MDTC, sous-traitants ou partenaires peuvent être mis à disposition et remis physiquement avec état/accessoires/evidence ;
- un retour peut enregistrer état et accessoires manquants sans conclure à une faute ;
- `ResourceIncident` couvre disparition, shortage, casse, dommage, perte, usage non autorisé, gaspillage inattendu et vol signalé ;
- `resource missing ≠ theft established ≠ perpetrator identified ≠ responsibility established` ;
- `damage ≠ negligence`, `unexpected waste ≠ fraud`, `variance ≠ fraud` ;
- la causalité et la responsabilité restent reportées à DOMAIN-00.7 ;
- le WMS complet, la comptabilité fournisseur et la transaction commerciale de location restent hors scope ;
- une machine louée extérieurement redevient pertinente dès qu'elle entre dans la réalité opérationnelle du chantier ;
- `rental transaction ≠ project equipment custody`.

Invariants principaux :

```text
MaterialSpecification ≠ CommercialProduct
ResourceRequirement ≠ ProcurementNeed
EquipmentRequirement ≠ EquipmentAssignment
EquipmentAssignment ≠ EquipmentHandover
```

```text
owner ≠ provider ≠ custodian ≠ operator
```

```text
ORDERED ≠ DELIVERED ≠ RECEIVED ≠ INSPECTED ≠ ACCEPTED ≠ INCORPORATED
```

```text
WASTED ≠ abnormal waste ≠ negligence ≠ responsibility
resource missing ≠ theft established
reported theft ≠ verified theft
```

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
```

## Prochaine décision

DOMAIN-00.4 étant validé conceptuellement, la prochaine discussion porte sur :

**DOMAIN-00.5 — Planning / Time / Meetings / Execution**.
