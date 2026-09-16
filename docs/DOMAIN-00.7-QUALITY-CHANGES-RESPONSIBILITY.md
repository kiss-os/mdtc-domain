# DOMAIN-00.7 — Quality / Changes / Responsibility

Status: **validated conceptually**

## 1. Objet

DOMAIN-00.7 relie qualité, contrôle, réception, changement, baseline, causalité, responsabilité et après-chantier sans confondre les niveaux de preuve ou d'autorité.

Principe directeur :

> **Un fait observé n'établit ni sa cause, ni une responsabilité, ni un effet contractuel ou juridique.**

```text
FACT
≠ CAUSALITY
≠ RESPONSIBILITY
≠ AUTHORITY
≠ LEGAL EFFECT
```

Principe qualité :

> **MDTC évalue un travail, une exécution ou un résultat ; le domaine ne produit pas de notation globale des personnes.**

---

## 2. Chaîne qualité

```text
ApplicableQualityRequirement
        ↓
ControlPoint
        ↓
QualityInspection
        ↓
InspectionResult
        ↓
Observation / ExecutionIrregularity
        ↓
Defect? / NonConformity?
        ↓
NonConformityDisposition / Action / Rework
        ↓
verification
        ↓
Acceptance
```

Toutes les étapes ne sont pas obligatoires.

Une observation peut ne jamais devenir une non-conformité et un défaut peut exister sans qu'une non-conformité contractuelle ou normative soit immédiatement établie.

---

## 3. `ApplicableQualityRequirement`

Le domaine ne recopie pas les DTU, règlements, prescriptions fabricant, CCTP ou autres référentiels. Il référence les exigences applicables à un scope donné.

```text
ApplicableQualityRequirement
├── requirement_id
├── project_ref
├── scope_ref
├── requirement_source_ref
├── source_kind
├── applicability_state
├── effective_from?
├── effective_until?
├── baseline_ref?
└── source_refs[]
```

Sources candidates :

```text
CONTRACTUAL
NORMATIVE
MANUFACTURER
PROJECT
DESIGN
OTHER
```

Invariant :

```text
knowledge available
≠ requirement applicable to this Project
```

Les strates de connaissance restent notamment :

```text
NORMATIVE
CONTRACTUAL
PROJECT_FACT
EMPIRICAL
HEURISTIC
```

Une connaissance empirique ou heuristique ne devient pas automatiquement une exigence applicable.

---

## 4. `ControlPoint` et `QualityInspection`

`ControlPoint` décrit un contrôle attendu.

```text
ControlPoint
├── control_point_id
├── project_ref
├── scope_ref
├── requirement_refs[]
├── control_kind
├── verification_method?
├── planned_at?
├── required_before_transition?
├── required_authority_ref?
└── source_refs[]
```

Invariant déjà adopté :

```text
Task ≠ ControlPoint
```

`QualityInspection` représente l'occurrence réelle du contrôle.

```text
QualityInspection
├── inspection_id
├── control_point_ref?
├── project_ref
├── scope_ref
├── inspected_at
├── inspector_actor_ref
├── authority_ref?
├── requirement_refs[]
├── result_refs[]
├── evidence_refs[]
└── source_refs[]
```

Invariant :

```text
ControlPoint ≠ QualityInspection
```

---

## 5. `InspectionResult`

```text
InspectionResult
├── inspection_result_id
├── inspection_ref
├── requirement_ref?
├── result
├── measured_value?
├── expected_value?
├── tolerance?
├── note?
├── evidence_refs[]
└── source_refs[]
```

Résultats initiaux :

```text
PASS
FAIL
CONDITIONAL
NOT_EVALUATED
NOT_APPLICABLE
```

Invariants :

```text
inspection performed ≠ inspection passed
inspection passed ≠ contractual acceptance
```

---

## 6. `ExecutionIrregularity`

> Une `ExecutionIrregularity` décrit une situation anormale ou nécessitant attention pendant l'exécution sans conclure automatiquement à une non-conformité ni à une faute.

```text
ExecutionIrregularity
├── irregularity_id
├── project_ref
├── scope_ref
├── category
├── severity?
├── observed_at
├── observed_by_actor_ref?
├── description
├── evidence_refs[]
├── corrective_action_refs[]
├── resolution_state
└── source_refs[]
```

Invariant :

```text
ExecutionIrregularity
≠ NonConformity
≠ Responsibility
```

---

## 7. `Defect`

> Un `Defect` décrit une déficience physique ou fonctionnelle identifiée dans un ouvrage ou résultat exécuté.

```text
Defect
├── defect_id
├── project_ref
├── scope_ref
├── detected_at
├── description
├── verification_state
├── evidence_refs[]
├── related_irregularity_refs[]
└── source_refs[]
```

Invariant :

```text
Defect ≠ automatically contractual nonconformity
```

Une déficience observée doit encore être confrontée aux exigences réellement applicables.

---

## 8. `NonConformity`

> Une `NonConformity` est une détermination validée qu'un résultat ou une situation ne respecte pas une exigence applicable identifiée.

```text
NonConformity
├── nonconformity_id
├── project_ref
├── scope_ref
├── requirement_ref
├── nature
├── observation_refs[]
├── defect_refs[]
├── evidence_refs[]
├── determined_at
├── determined_by_actor_ref
├── authority_ref?
├── severity?
├── state
└── source_refs[]
```

Natures initiales :

```text
TECHNICAL
COSMETIC
OPERATIONAL
SAFETY
DOCUMENTARY
OTHER
```

États possibles :

```text
OPEN
CORRECTED
VERIFIED_CLOSED
DISPUTED
SUPERSEDED
```

Invariants :

```text
Observation
≠ Defect
≠ NonConformity
```

```text
suspected deviation ≠ validated nonconformity
```

---

## 9. Nature, impact et disposition sont trois dimensions distinctes

La nature d'une non-conformité ne détermine pas seule son impact ni son traitement.

```text
NONCONFORMITY NATURE
≠ NONCONFORMITY IMPACT
≠ NONCONFORMITY DISPOSITION
```

Exemples :

- une non-conformité `TECHNICAL` peut avoir un impact sur la durabilité, la sécurité, la conformité contractuelle ou nécessiter une analyse garantie/assurance ;
- une non-conformité `COSMETIC` peut être sans impact technique significatif et être acceptée en l'état par l'autorité compétente ;
- une non-conformité `OPERATIONAL` peut concerner une méthode, une séquence ou un état transitoire, être corrigée avant livraison ou ne pas affecter significativement le livrable ;
- une non-conformité `SAFETY` reste traitée comme événement/gate critique et n'est pas moyennée dans une note qualité.

Invariants :

```text
technical nonconformity
≠ automatic warranty / insurance applicability
≠ automatic responsibility
```

```text
operational deviation ≠ defective deliverable
```

```text
correctable deviation ≠ accepted deviation
```

---

## 10. `NonConformityDisposition`

> Une `NonConformityDisposition` représente la décision de traitement d'une non-conformité, sans réécrire son existence historique.

```text
NonConformityDisposition
├── disposition_id
├── nonconformity_ref
├── disposition_kind
├── decided_at
├── decided_by_actor_ref
├── authority_ref
├── impact_assessment_refs[]
├── conditions[]
├── residual_risk_refs[]
├── corrective_action_refs[]
├── evidence_refs[]
└── source_refs[]
```

Types initiaux :

```text
CORRECTION_REQUIRED
CORRECTION_PLANNED
CORRECTED_PENDING_VERIFICATION
ACCEPTED_AS_IS
ACCEPTED_WITH_CONDITIONS
ACCEPTED_WITH_RESERVATION
MONITOR
DEFERRED
REJECTED
SUPERSEDED
```

Flux :

```text
NonConformity
↓
ImpactAssessment
↓
Disposition
├── correction
├── acceptation
├── acceptation conditionnelle
├── surveillance
└── refus
```

Invariants :

```text
accepted deviation ≠ historical conformity
```

```text
corrected + verified nonconformity
→ current scope may become conforming

but

historical nonconformity
→ remains immutable
```

---

## 11. Autorité d'acceptation

Toute acceptation doit conserver l'autorité sur laquelle elle repose.

```text
client acceptance
≠ technical authority acceptance
≠ regulatory acceptance
≠ insurer acceptance
```

Une variation cosmétique peut, selon le contexte, être acceptée par le client. Une acceptation client ne suffit pas à elle seule à neutraliser une exigence technique, contractuelle, réglementaire, assurantielle ou de sécurité hors de son autorité.

Invariant :

```text
acceptance by an actor
≠ universal conformity waiver
```

---

## 12. `WorkExecutionAssessment`

> Un `WorkExecutionAssessment` évalue une exécution contextualisée, jamais la valeur globale d'une personne.

```text
WorkExecutionAssessment
├── assessment_id
├── project_ref
├── work_package_ref
├── execution_ref?
├── assessor_actor_ref
├── assessed_at
├── dimensions[]
├── rework_required
├── irregularity_refs[]
├── defect_refs[]
├── evidence_refs[]
├── note?
└── confidence?
```

Dimensions candidates :

```text
CONFORMITY
FINISH
COMPLETENESS
EXECUTION_QUALITY
PROTECTION
CLEANLINESS
OTHER
```

La grille reste contextualisable et n'impose pas une note universelle.

Invariants :

```text
WorkExecutionAssessment
→ WorkPackage / Execution

NOT
→ global worker rating
```

```text
execution quality ≠ intrinsic quality of person
```

---

## 13. `QualityAssessment`

```text
QualityAssessment
├── quality_assessment_id
├── project_ref
├── scope_ref
├── assessed_at
├── assessment_state
├── requirement_refs[]
├── inspection_refs[]
├── defect_refs[]
├── nonconformity_refs[]
├── evidence_refs[]
├── assumptions[]
└── confidence?
```

États candidats :

```text
ACCEPTABLE
CONDITIONAL
UNACCEPTABLE
UNKNOWN
```

Invariant :

```text
QualityAssessment ACCEPTABLE ≠ contractual acceptance
```

---

## 14. Sécurité critique

Un événement sécurité critique ne devient pas une simple composante d'une note moyenne.

```text
ProjectEvent
+
ExecutionIrregularity / NonConformity
+
Action
+
gate éventuel
```

peut conduire notamment à :

```text
WORK_SUSPENDED
```

Invariant :

```text
safety-critical event ≠ averaged quality score
```

---

## 15. `Rework`

> `Rework` relie une anomalie, un défaut ou une non-conformité au travail réellement engagé pour corriger la situation.

```text
Rework
├── rework_id
├── project_ref
├── origin_refs[]
├── corrective_scope_ref
├── created_at
├── required_by_ref?
├── verification_ref?
└── source_refs[]
```

Flux :

```text
NonConformity / Defect / Irregularity
↓
Rework
↓
WorkPackage / Task correctif
↓
TimeEntry / MaterialUsage / Equipment
↓
ActualCost
```

Invariants :

```text
Rework ≠ Responsibility
rework cost ≠ liability
```

---

## 16. `CustomerFeedback`

```text
CustomerFeedback
├── feedback_id
├── project_ref
├── scope_ref?
├── contributor_actor_ref
├── submitted_at
├── feedback_kind
├── structured_feedback?
├── comment?
├── contribution_ref?
└── source_refs[]
```

Kinds :

```text
PRAISE
COMPLAINT
SUGGESTION
SATISFACTION
OTHER
```

Invariant :

```text
CustomerFeedback ≠ TechnicalQuality
```

Une satisfaction client élevée ne prouve pas la conformité technique ; une insatisfaction ne prouve pas non plus une non-conformité.

---

## 17. `AcceptanceRecord`

DOMAIN-00.5 a établi :

```text
Execution COMPLETED ≠ ACCEPTED
```

`AcceptanceRecord` représente une décision explicite d'acceptation d'un scope.

```text
AcceptanceRecord
├── acceptance_id
├── project_ref
├── scope_ref
├── acceptance_kind
├── performed_at
├── state
├── accepted_by_actor_refs[]
├── authority_refs[]
├── reservation_refs[]
├── inspection_refs[]
├── evidence_refs[]
├── document_refs[]
└── source_refs[]
```

Kinds possibles :

```text
INTERNAL_TECHNICAL
CLIENT
HANDOVER
CONTRACTUAL_RECEPTION
OTHER
```

États :

```text
ACCEPTED
ACCEPTED_WITH_RESERVATIONS
REJECTED
PENDING
```

---

## 18. `Reservation`

```text
Reservation
├── reservation_id
├── acceptance_ref
├── project_ref
├── scope_ref
├── raised_by_actor_ref
├── raised_at
├── description
├── issue_ref?
├── defect_ref?
├── nonconformity_ref?
├── evidence_refs[]
├── state
└── source_refs[]
```

États :

```text
OPEN
LIFTED
DISPUTED
CANCELLED
```

Invariants :

```text
Reservation ≠ automatically NonConformity
reservation lifted ≠ historical reservation deleted
```

---

# Partie II — Changes

## 19. `ChangeRequest`

```text
ChangeRequest
├── change_request_id
├── project_ref
├── scope_ref?
├── requested_by_actor_ref
├── requested_at
├── change_kind
├── description
├── reason?
├── source_refs[]
├── evidence_refs[]
└── state
```

Kinds candidats :

```text
CLIENT_REQUEST
TECHNICAL
DESIGN
OPERATIONAL
CONTRACTUAL
COMMERCIAL
SAFETY
CORRECTION
OTHER
```

Invariant :

```text
ChangeRequest ≠ approved change
```

---

## 20. Révision documentaire ≠ changement accepté

Une nouvelle version de plan ou document technique doit générer une analyse d'impact, pas une mutation silencieuse du projet.

```text
TechnicalDocument revision
↓
ImpactCandidate
```

Invariants :

```text
document uploaded
≠ reviewed
≠ approved
≠ baseline
```

```text
new technical document revision ≠ accepted Project change
```

---

## 21. `ImpactAssessment`

```text
ImpactAssessment
├── impact_assessment_id
├── subject_ref
├── assessed_at
├── assessed_by_actor_ref?
├── impact_items[]
├── assumptions[]
├── unknowns[]
├── evidence_refs[]
├── confidence?
└── source_refs[]
```

`subject_ref` peut viser notamment une `ChangeRequest`, `NonConformity`, `Defect`, nouvelle révision documentaire ou autre événement pertinent.

Dimensions candidates :

```text
SCOPE
SCHEDULE
COST
REVENUE
RESOURCE
QUALITY
DURABILITY
FUNCTIONAL
SAFETY
DOCUMENT
CONTRACTUAL
WARRANTY_INSURANCE_CANDIDATE
OTHER
```

États d'un impact :

```text
KNOWN
ESTIMATED
UNKNOWN
NOT_APPLICABLE
```

Invariant :

```text
unknown impact ≠ zero impact
```

---

## 22. Impact estimé ≠ changement approuvé

```text
ImpactAssessment
"coût supplémentaire estimé 2 000 EUR"
```

ne modifie ni le scope autoritatif ni le revenu contracté.

Invariant repris de DOMAIN-00.6 :

```text
ChangeRequest economic impact ≠ ContractedRevenue change
```

---

## 23. `DecisionRecord`

`DecisionRecord` conserve la structure des décisions importantes et le désaccord éventuel.

```text
DecisionRecord
├── decision_record_id
├── decision_ref
├── project_ref
├── scope_ref?
├── question
├── context_refs[]
├── claim_refs[]
├── evidence_refs[]
├── objections[]
├── assumptions[]
├── selected_option
├── rejected_alternatives[]
├── rationale
├── confidence?
├── decided_by
├── authority_ref
├── decided_at
├── followup_refs[]
└── supersedes_ref?
```

Invariants :

```text
decided_by ≠ authority_ref
```

```text
decision_basis ≠ cause
```

Le désaccord, les hypothèses et les objections ne doivent pas être effacés pour simplifier l'historique.

---

## 24. `ChangeOrder`

Tous les changements ne nécessitent pas un avenant. Lorsqu'un changement devient contractuel ou commercial :

```text
ChangeRequest
↓
ImpactAssessment
↓
DecisionRecord
↓
ChangeOrder
```

```text
ChangeOrder
├── change_order_id
├── project_ref
├── change_request_ref
├── decision_ref
├── scope_changes[]
├── schedule_impact_ref?
├── economic_impact_ref?
├── commercial_document_ref?
├── accepted_by_actor_refs[]
├── authority_refs[]
├── effective_at
└── source_refs[]
```

Invariant :

```text
internally approved change ≠ client-accepted ChangeOrder
```

---

## 25. Urgence / sécurité

Une action nécessaire pour sécuriser une situation peut être engagée avant la finalisation d'un changement commercial.

```text
danger / safety condition
↓
Action / ProjectEvent / authority gate
↓
immediate protective or corrective action
```

puis éventuellement :

```text
ImpactAssessment
↓
ChangeRequest / ChangeOrder
```

Invariant :

```text
EmergencyAction ≠ accepted ChangeOrder
```

La sécurité ne doit pas être bloquée par le workflow commercial.

---

## 26. `ProjectBaseline`

> Une `ProjectBaseline` représente un état de référence explicitement accepté du projet à un instant donné.

```text
ProjectBaseline
├── baseline_id
├── project_ref
├── baseline_number
├── status
├── accepted_at?
├── accepted_by_actor_refs[]
├── authority_refs[]
├── component_refs[]
├── source_refs[]
└── supersedes_ref?
```

Composants possibles :

```text
scope snapshot
schedule revision
budget revision
commercial basis
technical document set
quality requirement set
other accepted components
```

Une baseline acceptée est immuable.

```text
Baseline v3
+
accepted Change
↓
Baseline v4
```

```text
v4 supersedes v3
```

sans modifier ni supprimer v3.

Invariants :

```text
accepted baseline = immutable historical reference
```

```text
Project live state ≠ ProjectBaseline
```

Le projet vivant continue de porter observations, actuals, coûts, forecast, issues et événements sans réécrire sa baseline.

---

# Partie III — Responsibility

## 27. `ResponsibilityAssignment`

`ResponsibilityAssignment` représente un devoir ou une responsabilité opérationnelle contextualisée assignée avant ou indépendamment d'un incident.

```text
ResponsibilityAssignment
├── responsibility_assignment_id
├── project_ref
├── scope_ref
├── actor_ref
├── responsibility_kind
├── obligation_ref?
├── basis_ref
├── effective_from
├── effective_until?
├── authority_ref?
└── source_refs[]
```

Invariants :

```text
ResponsibilityAssignment
≠ RoleAssignment
≠ AuthorityAssignment
≠ Permission
```

```text
assigned responsibility ≠ blame
```

---

## 28. `CausalityAssessment`

```text
CausalityAssessment
├── causality_assessment_id
├── project_ref
├── subject_ref
├── effect_ref
├── causal_claims[]
├── alternative_hypotheses[]
├── evidence_refs[]
├── assumptions[]
├── assessed_by_actor_ref?
├── assessed_at
├── confidence?
├── state
└── source_refs[]
```

États :

```text
CANDIDATE
REVIEWED
ACCEPTED
DISPUTED
REJECTED
```

Invariants :

```text
temporal sequence ≠ causality
correlation ≠ causality
observation ≠ causality
causality ≠ responsibility
```

Le modèle doit permettre des causes multiples et des hypothèses concurrentes.

---

## 29. `ResponsibilityAssessment`

```text
ResponsibilityAssessment
├── assessment_id
├── project_ref
├── subject_ref
├── responsibility_kind
├── candidate_actor_refs[]
├── responsibility_assignment_refs[]
├── causality_assessment_refs[]
├── contractual_basis_refs[]
├── evidence_refs[]
├── objections[]
├── assessed_at
├── assessed_by_actor_ref?
├── confidence?
└── state
```

Invariant :

```text
ResponsibilityAssessment ≠ authoritative ResponsibilityAllocation
```

Une analyse peut rester contestée ou incertaine.

---

## 30. `ResponsibilityAllocation`

Une allocation de responsabilité devient un objet autoritatif seulement après décision par une autorité compétente.

```text
ResponsibilityAllocation
├── allocation_id
├── project_ref
├── subject_ref
├── actor_ref
├── responsibility_kind
├── share?
├── basis_refs[]
├── decision_ref
├── authority_ref
├── effective_at
├── dispute_state?
└── source_refs[]
```

Kinds candidats :

```text
OPERATIONAL
REMEDIATION
CONTRACTUAL
COST_BEARING
OTHER
```

Un partage numérique ne doit être renseigné que s'il a réellement été décidé.

Invariant :

```text
ResponsibilityAssessment ≠ ResponsibilityAllocation
```

---

## 31. `EconomicAllocation` ≠ `ResponsibilityAllocation`

DOMAIN-00.6 utilise `EconomicAllocation` pour répartir analytiquement une valeur existante.

```text
EconomicAllocation
= analytical allocation of an amount
```

DOMAIN-00.7 utilise `ResponsibilityAllocation` pour enregistrer une décision explicite de responsabilité.

```text
ResponsibilityAllocation
= authoritative business responsibility allocation
```

Invariant :

```text
EconomicAllocation ≠ ResponsibilityAllocation
```

Une charge imputée à un WorkPackage ou acteur ne prouve jamais qui est responsable de l'avoir provoquée.

---

## 32. Responsabilité métier ≠ jugement juridique final

Le domaine peut enregistrer des décisions métier, contractuelles ou opérationnelles avec leur autorité et leurs sources.

Il ne se substitue pas à une autorité juridique, assurantielle, expertale ou administrative externe.

Invariant :

```text
business responsibility allocation
≠ automatic final legal liability judgment
```

Une décision externe peut être référencée via un objet de référence adapté, sans être réinventée par `mdtc-domain`.

---

## 33. `WarrantyClaim`

```text
WarrantyClaim
├── claim_id
├── project_ref
├── scope_ref?
├── claimant_actor_ref
├── claimed_at
├── description
├── contribution_ref?
├── evidence_refs[]
├── acceptance_ref?
└── source_refs[]
```

Invariant :

```text
WarrantyClaim
≠ verified defect
≠ accepted coverage
≠ responsibility
```

Une réclamation est d'abord une contribution et une demande à analyser.

---

## 34. `WarrantyCoverageAssessment`

```text
WarrantyCoverageAssessment
├── assessment_id
├── warranty_claim_ref
├── coverage_state
├── basis_refs[]
├── defect_refs[]
├── causality_refs[]
├── assumptions[]
├── assessed_by_actor_ref?
├── authority_ref?
├── decision_ref?
└── source_refs[]
```

États candidats :

```text
PENDING
IN_SCOPE
OUT_OF_SCOPE
PARTIAL
DISPUTED
```

Invariants :

```text
coverage assessment ≠ causality
coverage assessment ≠ legal judgment
```

Les questions d'assurance ou de garantie à enjeu élevé nécessitent les sources et gates d'autorité appropriés.

---

## 35. Couches épistémiques

```text
RAW FACTS
────────────────────
Observation
InspectionResult
Defect
ExecutionIrregularity
Evidence
TimeEntry
MaterialUsage

        ↓

ASSESSMENTS
────────────────────
WorkExecutionAssessment
QualityAssessment
ImpactAssessment
CausalityAssessment
ResponsibilityAssessment
WarrantyCoverageAssessment

        ↓

AUTHORITATIVE BUSINESS DECISIONS
────────────────────
DecisionRecord
AcceptanceRecord
NonConformityDisposition
ChangeOrder
ResponsibilityAllocation
ProjectBaseline

        ↓

ANALYTICS / KNOWLEDGE
────────────────────
QualityTrend
ReworkTrend
CostOfQuality
BestPracticeCandidate
RiskPattern
EmpiricalKnowledgeCandidate
```

Les analytics et promotions de connaissance seront consolidés avec Nestor en DOMAIN-00.8.

---

## 36. Nestor et DOMAIN-00.7

Mission associée :

[`../missions/contracts/ASSESS-PROJECT-QUALITY-CHANGE-RESPONSIBILITY.md`](../missions/contracts/ASSESS-PROJECT-QUALITY-CHANGE-RESPONSIBILITY.md)

Nestor peut produire notamment :

```text
IrregularityCandidate
DefectCandidate
NonConformityCandidate
ImpactAssessmentCandidate
NonConformityDispositionCandidate
ReworkCandidate
CausalityHypothesis
ResponsibilityAssessmentCandidate
ChangeRequestCandidate
ChangeOrderCandidate
```

Mais :

```text
AI observation ≠ verified fact
AI nonconformity candidate ≠ authoritative nonconformity
AI causal hypothesis ≠ accepted cause
AI responsibility assessment ≠ ResponsibilityAllocation
AI change candidate ≠ approved change
```

Les conclusions à enjeu sécurité, structure, conformité réglementaire, réception, garantie/assurance, responsabilité ou impact financier significatif nécessitent un gate d'autorité adapté.

---

## 37. Exemple — non-conformité cosmétique acceptée

```text
Inspection / Observation
↓
variation esthétique identifiée
↓
NonConformity
nature = COSMETIC
↓
ImpactAssessment
technical impact = NOT_APPLICABLE or not identified
cosmetic impact = KNOWN
↓
NonConformityDisposition
ACCEPTED_AS_IS
by authorized actor
```

Le domaine conserve :

```text
historical nonconformity
+
explicit acceptance
```

et ne réécrit pas :

```text
historical state = conforming
```

---

## 38. Exemple — non-conformité opératoire corrigée

```text
ExecutionIrregularity
↓
NonConformity
nature = OPERATIONAL
↓
CORRECTION_REQUIRED
↓
Rework / corrective Action
↓
QualityInspection
↓
PASS
```

Le scope courant peut redevenir conforme, tout en conservant l'historique de l'écart et de sa correction.

---

## 39. Exemple — infiltration

```text
ProjectContribution + photo
↓
Observation
↓
Defect
↓
ApplicableQualityRequirement
↓
QualityInspection = FAIL
↓
NonConformity
↓
CausalityAssessment
├── hypothesis A
├── hypothesis B
└── unknowns
↓
Rework
↓
TimeEntry / MaterialUsage
↓
ActualCost
```

Toujours :

```text
rework cost ≠ responsibility
```

Une éventuelle allocation de responsabilité nécessite :

```text
ResponsibilityAssessment
↓
DecisionRecord
↓ authority gate
ResponsibilityAllocation
```

---

## 40. Exemple — demande client modifiant le projet

```text
ChangeRequest
CLIENT_REQUEST
↓
ImpactAssessment
├── scope
├── schedule
├── cost
├── revenue
└── documents
↓
DecisionRecord
↓
ChangeOrder accepted
↓
WorkPackage / Schedule / Commercial revisions
↓
ProjectBaseline v4
supersedes
ProjectBaseline v3
```

L'historique de la baseline précédente reste intact.

---

## 41. Noyau conceptuel adopté

### Quality

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
```

### Changes

```text
ChangeRequest
ImpactAssessment
DecisionRecord
ChangeOrder
ProjectBaseline
```

### Responsibility / aftercare

```text
ResponsibilityAssignment
CausalityAssessment
ResponsibilityAssessment
ResponsibilityAllocation
WarrantyClaim
WarrantyCoverageAssessment
```

---

## 42. Invariants adoptés

```text
fact ≠ causality ≠ responsibility ≠ legal effect
```

```text
Task ≠ ControlPoint
ControlPoint ≠ QualityInspection
inspection performed ≠ inspection passed
inspection passed ≠ contractual acceptance
```

```text
Observation ≠ ExecutionIrregularity ≠ Defect ≠ NonConformity
suspected deviation ≠ validated nonconformity
```

```text
NONCONFORMITY NATURE
≠ NONCONFORMITY IMPACT
≠ NONCONFORMITY DISPOSITION
```

```text
technical nonconformity
≠ automatic warranty / insurance applicability
≠ automatic responsibility
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
WorkExecutionAssessment → execution / WorkPackage
WorkExecutionAssessment ≠ global person rating
```

```text
CustomerFeedback ≠ TechnicalQuality
safety-critical event ≠ averaged quality score
```

```text
Rework ≠ Responsibility
rework cost ≠ liability
```

```text
Execution COMPLETED
≠ technical acceptance
≠ contractual acceptance
```

```text
Reservation ≠ NonConformity
reservation lifted ≠ historical reservation deleted
```

```text
ChangeRequest ≠ approved change
ImpactAssessment ≠ approved change
new technical document revision ≠ accepted Project change
unknown impact ≠ zero impact
```

```text
internally approved change ≠ client-accepted ChangeOrder
EmergencyAction ≠ accepted ChangeOrder
```

```text
accepted baseline = immutable historical reference
Project live state ≠ ProjectBaseline
```

```text
ResponsibilityAssignment
≠ RoleAssignment
≠ AuthorityAssignment
≠ Permission
assigned responsibility ≠ blame
```

```text
temporal sequence ≠ causality
correlation ≠ causality
causality ≠ responsibility
```

```text
ResponsibilityAssessment ≠ ResponsibilityAllocation
EconomicAllocation ≠ ResponsibilityAllocation
```

```text
business responsibility allocation
≠ automatic final legal liability judgment
```

```text
WarrantyClaim
≠ verified defect
≠ accepted coverage
≠ responsibility
```

```text
AI causal hypothesis ≠ accepted cause
AI responsibility assessment ≠ authoritative ResponsibilityAllocation
```

---

## 43. Hors périmètre de DOMAIN-00.7

Restent hors de ce contrat :

- détermination automatique d'une règle de droit ;
- décision judiciaire ou assurantielle ;
- moteur de sinistre assureur ;
- calcul exhaustif de couverture de police ;
- notation globale des personnes ;
- notification/audience/routage technique — DOMAIN-00.8 ;
- promotion automatique de connaissances — DOMAIN-00.8 ;
- implémentation technique des invariants et migrations — DOMAIN-00.10.
