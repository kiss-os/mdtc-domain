# DOMAIN-00.5 — Planning / Time / Meetings / Execution

Status: **validated conceptually**

## 1. Objet

DOMAIN-00.5 sépare explicitement :

- la structure du travail ;
- ce qui est planifié ;
- ce qui est prévu à date comme forecast ;
- ce qui s'est réellement produit ;
- le temps humain effectivement déclaré ;
- les visites et réunions ;
- l'état opérationnel d'exécution ;
- les écarts de planning.

Principe directeur :

> **La structure du travail dit quoi ; le planning dit quand ; les `TimeEntry` disent combien de temps a réellement été passé ; l'exécution dit ce qui s'est réellement produit.**

```text
WorkPackage / Task
        │
        ├── Schedule → quand cela doit arriver
        ├── Execution → ce qui arrive réellement
        ├── TimeEntry → temps humain réellement déclaré
        └── ProjectEvent → événements significatifs
```

---

## 2. `WorkPackage` ≠ `ScheduleActivity`

Les dates principales ne doivent pas être figées comme vérité unique directement dans `WorkPackage`.

Un même `WorkPackage` peut :

- être planifié plusieurs fois ;
- être découpé en plusieurs séquences ;
- être replanifié ;
- être suspendu puis repris.

Invariant :

```text
WorkPackage ≠ ScheduleActivity
```

Un `WorkPackage` peut être lié à `0..n ScheduleActivity`.

---

## 3. `Schedule` et `ScheduleRevision`

```text
Schedule
├── schedule_id
├── project_ref
├── name
├── timezone
└── revision_refs[]
```

Les révisions doivent préserver l'historique.

```text
ScheduleRevision
├── revision_id
├── schedule_ref
├── revision_number
├── created_at
├── created_by_actor_ref?
├── status
├── activity_refs[]
├── milestone_refs[]
└── source_refs[]
```

États initiaux :

```text
DRAFT
PUBLISHED
SUPERSEDED
```

Une révision publiée ne doit pas être réécrite silencieusement.

Invariant :

```text
published schedule ≠ contractual baseline
```

Une baseline contractuelle pourra référencer une révision de planning en DOMAIN-00.7.

---

## 4. `PLANNED` ≠ `FORECAST` ≠ `ACTUAL`

Ces trois temporalités sont distinctes :

```text
PLANNED  = ce qui était prévu
FORECAST = ce qu'on estime aujourd'hui probable
ACTUAL   = ce qui s'est réellement produit
```

Exemple :

```text
PLANNED  10 → 12 octobre
FORECAST 12 → 14 octobre
ACTUAL   13 → 15 octobre
```

Une nouvelle prévision ne doit jamais remplacer rétroactivement le plan initial.

---

## 5. `ScheduleActivity`

> Une `ScheduleActivity` est une projection temporelle d'un scope opérationnel.

```text
ScheduleActivity
├── schedule_activity_id
├── schedule_revision_ref
├── scope_ref
├── activity_kind
├── title
├── planned_start?
├── planned_finish?
├── planned_duration?
├── constraint_refs[]
├── dependency_refs[]
└── source_refs[]
```

`scope_ref` peut viser notamment :

```text
Project
WorkLot
WorkPackage
Task
```

Les barres de synthèse Project/WorkLot peuvent être des projections calculées et ne doivent pas nécessairement devenir des objets de planning canoniques supplémentaires.

---

## 6. `Dependency` ≠ `Interface`

```text
Dependency
├── dependency_id
├── predecessor_ref
├── successor_ref
├── relation_type
├── lag?
├── constraint_kind?
└── source_refs[]
```

Relations candidates :

```text
FINISH_TO_START
START_TO_START
FINISH_TO_FINISH
START_TO_FINISH
```

Invariant :

```text
Dependency = contrainte d'enchaînement
Interface  = point de coordination entre scopes / métiers
```

Les dépendances inter-projets sont autorisées lorsqu'elles sont explicitement cohérentes avec un `Program` ou un contexte autorisé.

---

## 7. `Milestone`

> Un `Milestone` est un point temporel significatif sans durée propre.

```text
Milestone
├── milestone_id
├── project_ref
├── scope_ref?
├── title
├── milestone_kind
├── target_at?
├── requirement_refs[]
└── source_refs[]
```

Invariant :

```text
planned milestone ≠ achieved milestone
```

L'atteinte réelle est représentée par un `ProjectEvent` tel que `MILESTONE_REACHED`.

---

## 8. `ReadinessAssessment`

Un scope peut être planifié sans être prêt à démarrer.

```text
ReadinessAssessment
├── assessment_id
├── scope_ref
├── assessed_at
├── assessed_by_actor_ref?
├── state
├── unmet_requirement_refs[]
├── blocker_refs[]
└── source_refs[]
```

États :

```text
READY
CONDITIONAL
NOT_READY
```

Invariant :

```text
scheduled ≠ ready
```

---

## 9. Exécution et projection d'état

Les événements métier alimentent l'état opérationnel :

```text
WORK_STARTED
WORK_SUSPENDED
WORK_RESUMED
WORK_COMPLETED
```

Une projection dérivée peut exposer :

```text
ExecutionStateProjection
NOT_STARTED
READY
ACTIVE
BLOCKED
SUSPENDED
COMPLETED
CANCELLED
```

Invariant :

```text
BLOCKED ≠ SUSPENDED
```

- `BLOCKED` : une condition empêche réellement la progression ;
- `SUSPENDED` : le travail a explicitement été mis en pause.

Le `Blocker` adopté en DOMAIN-00.3 est réutilisé ; il n'est pas recréé ici.

---

## 10. `ScheduleVariance`

Le retard ne doit pas être un simple booléen.

```text
ScheduleVariance
├── variance_id
├── project_ref
├── scope_ref
├── reference_schedule_ref
├── reference_date_or_window
├── observed_or_forecast_date
├── variance_duration
├── recorded_at
└── source_refs[]
```

Invariants :

```text
Blocker ≠ ScheduleVariance
Blocker ≠ Delay
ScheduleVariance ≠ Cause ≠ Responsibility
schedule revised ≠ project delayed
```

La qualification d'un retard dépend toujours du référentiel auquel on compare.

---

## 11. `TimeEntry`

> Un `TimeEntry` représente du temps effectivement déclaré ou enregistré pour un acteur dans un contexte de projet.

```text
TimeEntry
├── time_entry_id
├── project_ref
├── scope_ref
├── actor_ref
├── activity_kind
├── started_at?
├── ended_at?
├── duration?
├── break_duration?
├── entry_mode
├── source_kind
├── validation_state
├── supersedes_ref?
├── related_event_refs[]
├── blocker_refs[]
└── source_refs[]
```

Modes de saisie :

```text
INTERVAL
DURATION
```

Origines candidates :

```text
MANUAL
MOBILE_TIMER
IMPORTED
SUPERVISOR_ENTRY
OTHER
```

États :

```text
SUBMITTED
CONFIRMED
DISPUTED
VOID
```

Un `TimeEntry` est un fait opérationnel déclaré ; il n'est pas automatiquement une vérité économique, de paie ou de facturation.

---

## 12. Activités temporelles

Classification légère :

```text
EXECUTION
PREPARATION
TRAVEL
SITE_VISIT
MEETING
WAITING
ADMIN
OTHER
```

`WAITING` peut référencer un `Blocker`.

Invariant :

```text
WAITING ≠ unproductive employee
```

Une attente peut résulter d'une cause totalement externe à l'acteur.

---

## 13. Temps ≠ économie

Invariant majeur pour DOMAIN-00.6 :

```text
TimeEntry
≠ CostEntry
≠ PayrollEntry
≠ BillableItem
```

Le coût, la paie et la facturation sont des projections ou traitements économiques distincts.

---

## 14. `SiteVisit`

> Une `SiteVisit` représente une visite terrain réellement effectuée.

```text
SiteVisit
├── site_visit_id
├── project_ref
├── site_ref
├── purpose
├── planned_activity_ref?
├── started_at
├── ended_at?
├── participant_actor_refs[]
├── contribution_refs[]
├── observation_refs[]
├── evidence_refs[]
└── source_refs[]
```

Purposes candidats :

```text
QUALIFICATION
MEASUREMENT
SUPERVISION
INSPECTION
HANDOVER
AFTERCARE
OTHER
```

Invariant :

```text
SiteVisit ≠ TimeEntry
```

Une visite unique peut générer plusieurs `TimeEntry`, un par participant.

---

## 15. `SiteMeeting`

> Une `SiteMeeting` représente une réunion de chantier ou de projet effectivement tenue.

```text
SiteMeeting
├── meeting_id
├── project_ref
├── scope_ref?
├── planned_activity_ref?
├── meeting_kind
├── started_at
├── ended_at?
├── location_ref?
├── attendance[]
├── agenda_refs[]
├── contribution_refs[]
├── observation_refs[]
├── decision_refs[]
├── action_refs[]
├── minutes_ref?
└── source_refs[]
```

```text
MeetingAttendance
├── actor_ref
├── attendance_state
├── joined_at?
└── left_at?
```

États :

```text
ATTENDED
PARTIAL
ABSENT
EXCUSED
```

Invariants :

```text
MeetingAttendance ≠ TimeEntry
SiteMeeting ≠ TimeEntry ≠ BillableItem
```

Le compte rendu généré ou résumé par un LLM ne devient jamais automatiquement le procès-verbal approuvé.

```text
LLM meeting summary ≠ approved meeting minutes
```

Une réunion peut produire des `Observation`, `Decision`, `Action`, `Risk`, `Issue` ou `ChangeRequestCandidate`, mais aucun de ces objets n'est créé automatiquement sans gate approprié.

---

## 16. `ProgressAssessment`

Un pourcentage d'avancement doit conserver sa méthode de calcul.

```text
ProgressAssessment
├── progress_assessment_id
├── scope_ref
├── assessed_at
├── assessed_by_actor_ref?
├── method
├── value
├── basis_refs[]
└── source_refs[]
```

Méthodes candidates :

```text
QUANTITY_BASED
MILESTONE_BASED
MANUAL_ESTIMATE
TASK_COMPLETION
OTHER
```

Invariants :

```text
Progress 100%
≠ quality accepted
≠ technical acceptance
≠ contractual acceptance
```

```text
Execution COMPLETED
≠ ACCEPTED
≠ CLOSEOUT
```

---

## 17. Program et coordination multi-projets

Chaque `Project` conserve son propre `Schedule`.

Un `Program` peut fournir une projection transverse de coordination :

```text
ProgramScheduleProjection
```

Elle peut agréger :

- jalons communs ;
- dépendances inter-projets ;
- contraintes partagées ;
- ressources critiques.

Le domaine évite de fusionner tous les plannings en une unique structure obligatoire.

---

## 18. Calendriers et applications externes

Les calendriers externes sont des projections ou sources candidates, jamais l'autorité implicite du planning MDTC.

Sortie :

```text
MDTC Schedule
↓
CalendarProjection
↓
Google / Outlook / Odoo / autre
```

Entrée :

```text
ExternalCalendarEvent
↓
ScheduleChangeCandidate / MeetingCandidate
↓
policy / gate
↓
ScheduleRevision / SiteMeeting
```

Invariants :

```text
external calendar ≠ authoritative MDTC schedule
external calendar event ≠ direct ScheduleActivity mutation
CalendarProjection ≠ schedule source of truth
integration failure ≠ domain state mutation
```

Les applications candidates sont des providers/adapters, pas des concepts du domaine.

```text
Google / Outlook / Odoo / others
= adapters, not domain concepts
```

---

## 19. Timesheets externes

Un système externe peut être source de temps, à condition de préserver la provenance.

```text
ExternalTimesheet
↓ adapter
ExternalTimeEntrySource
↓
TimeEntry MDTC
```

Invariant :

```text
external timesheet
→ provenance-preserving import
→ MDTC TimeEntry
```

Une intégration externe ne peut pas modifier silencieusement l'état métier du projet.

---

## 20. Frontière Nestor / intégrations — DEFERRED vers DOMAIN-00.8

La noria MDTC doit rester indépendante du provider d'intégration.

Elle exprime des besoins/capacités et Nestor résout les providers disponibles.

```text
Noria requirement
↓
Nestor capability resolution
↓
local service / FastMCP / API / provider / future federated peer
```

L'architecture détaillée suivante est volontairement différée à DOMAIN-00.8 :

```text
Nestor transverse integration fabric
- capability discovery
- noria requires/exposes declarations
- FastMCP current implementation
- Langflow candidate / hypothesis
- external provider adapters
- partner onboarding
- external-system authority matrix
- federation / inter-Nestor discovery
```

Principes déjà fixés :

```text
Nestor Core = autonomous / local-first
nest0r.ai = optional ecosystem
A Noria may depend on nest0r.ai without making Nestor depend on nest0r.ai.
```

Pour MDTC, les systèmes externes restent interchangeables et ne définissent pas l'ontologie métier.

---

## 21. Timezone et horodatage

Le planning et les événements doivent conserver un contexte de fuseau explicite.

Conceptuellement :

```text
unambiguous instant
+
project / site timezone context
```

L'objectif est d'éviter les ambiguïtés liées notamment aux changements heure d'été / heure d'hiver.

---

## 22. Nestor et planning

Nestor peut produire des candidats à partir de :

```text
WorkPackages
Dependencies
Resource requirements
Constraints
Historical productivity
Execution events
TimeEntries
Blockers
```

Sorties candidates :

```text
ScheduleCandidate
DependencyCandidate
ScheduleRiskCandidate
ForecastCandidate
```

Invariants :

```text
AI-generated schedule ≠ approved schedule
AI forecast ≠ schedule commitment ≠ contractual baseline
```

Nestor ne doit pas :

- replanifier silencieusement ;
- inventer des dates réelles ;
- transformer un blocker en faute ;
- transformer un forecast en engagement ;
- modifier une baseline ;
- attribuer la responsabilité d'un retard.

---

## 23. Noyau conceptuel adopté

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

Réutilise de DOMAIN-00.3 :

```text
ProjectEvent
Blocker
Issue
Risk
Decision
Action
```

---

## 24. Invariants adoptés

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
```

```text
TimeEntry ≠ CostEntry ≠ PayrollEntry ≠ BillableItem
```

```text
Progress 100% ≠ quality accepted ≠ technical acceptance ≠ contractual acceptance
Execution COMPLETED ≠ ACCEPTED ≠ CLOSEOUT
```

```text
external calendar ≠ authoritative MDTC schedule
external mutation → candidate → policy/gate → ScheduleRevision
external timesheet → provenance-preserving import → MDTC TimeEntry
integration failure ≠ domain state mutation
```

```text
AI-generated schedule ≠ approved schedule
AI forecast ≠ schedule commitment ≠ contractual baseline
```

---

## 25. Hors périmètre de DOMAIN-00.5

Sont reportés :

- taux horaires et coût du temps — DOMAIN-00.6 ;
- paie / RH — système externe ;
- facturation du temps — DOMAIN-00.6 / projections documentaires ;
- qualification d'un retard imputable à un acteur — DOMAIN-00.7 ;
- pénalités de retard — DOMAIN-00.7 / couche contractuelle-économique ;
- qualité de l'exécution — DOMAIN-00.7 ;
- modification de baseline — DOMAIN-00.7 ;
- notification / audience / diffusion — DOMAIN-00.8 ;
- architecture détaillée MCP / provider discovery / federation — DOMAIN-00.8.
