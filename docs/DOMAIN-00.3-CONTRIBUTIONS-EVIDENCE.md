# DOMAIN-00.3 — Contributions / faits / preuves

Status: **validated conceptually**

## 1. Objet

DOMAIN-00.3 définit la frontière entre :

- ce qu'un humain, un système ou un capteur verse au projet ;
- ce qui est conservé comme source et preuve potentielle ;
- ce qui est structuré comme observation ou événement métier ;
- ce qui devient issue, blocker, risk, décision ou action ;
- ce que Nestor peut interpréter et proposer sans devenir l'auteur fictif ni l'autorité métier.

Principe directeur :

> **MDTC conserve l'original, la provenance et les relations. Nestor peut interpréter, traduire, structurer et proposer, mais une interprétation dérivée ne se confond jamais avec la contribution source.**

Topologie indicative :

```text
Réalité / humain / système externe
        ↓
ProjectContribution
        ↓
Evidence / source exploitable
        ↓
Observation / ProjectEvent
        ↓
Issue / Blocker / Risk
        ↓
Decision
        ↓
Action
```

Les flèches n'impliquent aucune promotion automatique ni causalité automatique.

---

## 2. `ProjectContribution` — couche source

### Définition

> Une `ProjectContribution` est une contribution originale versée à un `Project` par un acteur ou une source externe, conservée avec sa provenance et sans interprétation métier implicite.

Types initiaux :

```text
COMMENT
PHOTO
VIDEO
DOCUMENT
PLAN
MEASUREMENT
ANNOTATION
FORM_SUBMISSION
EXTERNAL_IMPORT
```

Contrat conceptuel :

```text
ProjectContribution
├── contribution_id
├── project_ref
├── scope_ref?
├── author_actor_ref?
├── origin
├── kind
├── created_at
├── submitted_at?
├── source_language?
├── input_mode?
├── original_content?
├── asset_refs[]
├── thread_ref?
├── reply_to_ref?
├── visibility_policy_ref?
├── supersedes_ref?
└── source_refs[]
```

`scope_ref` réutilise le scope métier adopté en DOMAIN-00.2 et peut viser notamment :

```text
Program
Project
WorkLot
WorkPackage
Task
```

Une contribution n'a pas besoin d'être transformée en objet métier supplémentaire. Un message tel que « merci, reçu » peut rester uniquement une `ProjectContribution`.

---

## 3. Original, dérivés et immutabilité logique

Invariant :

```text
original contribution
≠ derivative
≠ translation
≠ interpretation
```

Une photo originale peut générer :

```text
thumbnail
version compressée
annotation
analyse vision
texte descriptif
```

mais aucun dérivé ne remplace silencieusement l'original.

Une modification substantielle de contribution doit être traçable, par exemple :

```text
Contribution V1
↓ superseded_by
Contribution V2
```

L'UX peut afficher uniquement la version courante, tandis que l'historique reste auditable.

---

## 4. Multilingue : langue source, langue d'interaction et français canonique

MDTC doit permettre à un intervenant de contribuer dans sa langue réelle de travail, y compris lorsqu'il ne maîtrise pas suffisamment le français.

On distingue :

```text
source_language
interaction_language
canonical_language = fr
```

Exemple :

```text
source_language = tr
interaction_language = tr
canonical_language = fr
```

Principes :

- la contribution source reste conservée dans sa langue originale ;
- une transcription vocale ne remplace pas l'audio source lorsqu'un audio est conservé ;
- la traduction française est un dérivé ;
- l'interprétation structurée française est un dérivé ;
- la restitution de contrôle peut être faite dans la langue d'interaction de l'auteur ;
- les objets métier canoniques MDTC utilisent le français comme langue de référence ;
- les documents contractuels et juridiques MDTC sont produits avec une version française de référence selon la policy documentaire/compliance applicable.

Invariant :

```text
texte source
≠ transcription
≠ traduction
≠ reformulation
≠ objet métier canonique
```

---

## 5. UX mobile accessible

Le système doit être utilisable par un intervenant terrain peu à l'aise avec les interfaces numériques ou l'écrit.

Flux privilégié :

```text
[ 🎙 Parler ]
[ 📷 Prendre une photo ]
[ 📎 Ajouter un document ]
        ↓
transcription / upload
        ↓
interprétation Nestor
        ↓
restitution simple dans la langue de l'auteur
        ↓
[ ✓ Confirmer ] [ ✏ Corriger ] [ 🎙 Reprendre ]
```

Le frontend peut s'appuyer sur la dictée/transcription du téléphone ou sur un service de transcription résolu par Nestor. Le domaine ne dépend pas de l'implémentation choisie.

Une option de lecture vocale de la restitution doit pouvoir être proposée :

```text
🔊 Lire à voix haute
```

Objectif UX :

> **L'utilisateur décrit ce qu'il voit naturellement ; le système absorbe la complexité ontologique.**

Le formulaire ne doit pas exiger de l'utilisateur qu'il sache choisir lui-même entre `Observation`, `Issue`, `Risk`, `Blocker` ou `Action`.

---

## 6. Validation interprétée en deux passes

Pour les contributions nécessitant une compréhension structurée, le flux adopté est :

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
ObservationCandidate / IssueCandidate / ActionCandidate / ...
        ↓ gate éventuel
objet métier autoritatif
```

Point essentiel :

> **L'auteur confirme que Nestor a correctement compris son message ; il ne certifie pas par cette action la vérité technique ou juridique du fait interprété.**

Invariant :

```text
AUTHOR-CONFIRMED
≠
DOMAIN-VALIDATED
```

---

## 7. Niveaux épistémiques de l'interprétation

Quatre niveaux sont distingués :

```text
L0 — RAW
ProjectContribution

L1 — INTERPRETED
ContributionInterpretation

L2 — AUTHOR-CONFIRMED
ContributorConfirmation / ConfirmedInterpretation

L3 — DOMAIN-VALIDATED
Observation / ProjectEvent / Issue / ...
```

Invariant :

```text
RAW
≠ INTERPRETED
≠ AUTHOR-CONFIRMED
≠ DOMAIN-VALIDATED
```

---

## 8. `ContributionInterpretation`

### Définition

> Une `ContributionInterpretation` est une reformulation structurée et traçable d'une contribution source, produite sans altérer l'original et sans transformer les inférences en faits.

Contrat conceptuel :

```text
ContributionInterpretation
├── interpretation_id
├── contribution_ref
├── source_language
├── canonical_language
├── interaction_language
├── canonical_summary_fr
├── stated_claims[]
├── observed_elements[]
├── inferred_elements[]
├── unknowns[]
├── candidate_relations[]
├── model_run_ref
├── confidence?
└── created_at
```

La séparation suivante est obligatoire :

```text
STATED_BY_CONTRIBUTOR
OBSERVED_FROM_EVIDENCE
INFERRED
UNKNOWN
```

Exemple :

```text
STATED_BY_CONTRIBUTOR
"le passage est trop petit pour le tuyau"

OBSERVED_FROM_EVIDENCE
"une réservation et un réseau sont visibles sur la photo"

UNKNOWN
"dimension exacte de la réservation"
"diamètre exact du réseau"

INFERRED
"un contrôle de cote pourrait être nécessaire"
```

---

## 9. `ContributorConfirmation`

Contrat conceptuel :

```text
ContributorConfirmation
├── confirmation_id
├── interpretation_ref
├── actor_ref
├── interaction_language
├── state
├── corrections?
├── confirmed_at?
└── source_refs[]
```

États :

```text
PENDING
CONFIRMED
CORRECTED
REJECTED
NEEDS_ASSISTANCE
```

Une correction produit une nouvelle interprétation ou une nouvelle révision traçable ; elle ne modifie pas rétroactivement la contribution originale.

---

## 10. Nestor ne devient pas l'auteur fictif

Si un humain écrit ou dicte :

> « La réservation paraît trop petite. »

la contribution conserve l'auteur humain.

Nestor produit séparément une `ContributionInterpretation` et éventuellement des candidats métier.

Invariant :

```text
human contribution
≠ Nestor analysis
```

L'analyse peut référencer :

```text
analysis_ref
mission_ref
model_run_ref
deliberation_ref?
```

mais ne remplace pas la source.

La mission Nestor de référence est documentée dans :

[`../missions/contracts/INTERPRET-PROJECT-CONTRIBUTION.md`](../missions/contracts/INTERPRET-PROJECT-CONTRIBUTION.md)

---

## 11. `Evidence`

### Définition

> `Evidence` est un élément susceptible d'étayer, documenter, mesurer, illustrer ou contredire un objet métier.

Le terme `Evidence` est préféré à `Proof` car une photo, un plan ou une mesure ne constitue pas automatiquement une preuve concluante.

Contrat conceptuel :

```text
Evidence
├── evidence_id
├── project_ref
├── source_contribution_ref?
├── asset_ref?
├── kind
├── captured_at?
├── captured_by_actor_ref?
├── locator?
├── provenance_ref
└── integrity_ref?
```

Types initiaux :

```text
PHOTO
VIDEO
DOCUMENT
PLAN
MEASUREMENT
SCAN
SENSOR_READING
EXTERNAL_RECORD
```

Relation :

```text
EvidenceLink
├── evidence_ref
├── target_ref
└── relation
```

Relations candidates :

```text
SUPPORTS
CONTRADICTS
DOCUMENTS
ILLUSTRATES
MEASURES
CONTEXTUALIZES
```

Invariants :

```text
Evidence ≠ vérité
Evidence ≠ Observation
Evidence ≠ causalité
Evidence ≠ responsabilité
```

---

## 12. `Observation`

### Définition

> Une `Observation` est une assertion contextualisée concernant un état constaté ou rapporté dans le `Project`.

Contrat conceptuel :

```text
Observation
├── observation_id
├── project_ref
├── scope_ref
├── category?
├── statement
├── epistemic_kind
├── observed_at?
├── observer_actor_ref?
├── source_contribution_refs[]
├── evidence_refs[]
├── validation_state
└── source_refs[]
```

États initiaux :

```text
REPORTED
CORROBORATED
VERIFIED
DISPUTED
RETRACTED
```

`REPORTED` permet de conserver immédiatement une information sans la présenter comme techniquement vérifiée.

Nature :

```text
FACTUAL
ASSESSMENT
```

Exemples :

```text
FACTUAL
"Largeur mesurée : 181 mm"

ASSESSMENT
"La finition paraît irrégulière"
```

Invariant :

```text
Contribution ≠ Observation structurée
Observation REPORTED ≠ Observation VERIFIED
```

---

## 13. `ProjectEvent`

### Définition

> Un `ProjectEvent` est un événement métier significatif effectivement survenu dans la vie du `Project`.

Exemples :

```text
SITE_VISIT_OCCURRED
DELIVERY_RECEIVED
WORK_STARTED
WORK_SUSPENDED
DOCUMENT_RECEIVED
MEETING_HELD
MILESTONE_REACHED
```

Contrat conceptuel :

```text
ProjectEvent
├── event_id
├── project_ref
├── scope_ref?
├── event_type
├── occurred_at
├── recorded_at
├── actor_refs[]
├── source_contribution_refs[]
├── evidence_refs[]
└── source_refs[]
```

Invariant :

```text
ProjectEvent ≠ technical event bus envelope
```

L'enveloppe technique des événements sera consolidée en DOMAIN-00.8.

---

## 14. Document reçu, revu, approuvé et baseline

Le simple versement d'un document n'implique aucune validation contractuelle.

```text
uploaded
≠ received
≠ reviewed
≠ approved
≠ contractual baseline
```

Exemple :

```text
ProjectContribution
PLAN_R4.pdf
        ↓
ProjectEvent
DOCUMENT_RECEIVED
```

ne signifie ni que R4 est approuvé, ni qu'il remplace une baseline.

La taxonomie détaillée des `TechnicalDocument` est différée.

---

## 15. `Issue`

### Définition

> Une `Issue` est un problème présent et identifié qui nécessite un traitement, une clarification ou une décision.

```text
Issue
├── issue_id
├── project_ref
├── scope_ref
├── category
├── severity?
├── description
├── observation_refs[]
├── evidence_refs[]
├── status
└── source_refs[]
```

Exemple :

```text
Observation
"réservation mesurée à 181 mm"

Issue
"réservation potentiellement insuffisante pour les réseaux prévus"
```

L'observation reste historiquement vraie ou contestable même lorsque l'Issue est résolue.

---

## 16. `Blocker`

### Définition

> Un `Blocker` est une condition présente empêchant actuellement un scope identifié de progresser.

```text
Blocker
├── blocker_id
├── project_ref
├── blocked_scope_ref
├── reason
├── since
├── source_refs[]
└── resolution_condition?
```

Invariants :

```text
Issue ≠ Blocker
Blocker ≠ Delay
```

Un blocker peut provoquer un retard, mais il peut aussi être absorbé par le planning sans dérive de délai.

La variance temporelle sera traitée en DOMAIN-00.5.

---

## 17. `Risk`

### Définition

> Un `Risk` porte sur une occurrence ou une condition future incertaine susceptible d'avoir un impact.

```text
Risk
├── risk_id
├── project_ref
├── scope_ref?
├── description
├── likelihood?
├── impact_domains[]
├── source_refs[]
└── status
```

Invariant :

```text
Risk = futur incertain
Issue = problème présent identifié
```

Si un risque se matérialise, un nouvel `Issue` ou `ProjectEvent` peut être créé sans réécrire l'historique du risque.

---

## 18. `Decision`

### Définition

> Une `Decision` est un choix métier explicite rendu par une autorité identifiée dans un contexte donné.

```text
Decision
├── decision_id
├── project_ref
├── scope_ref?
├── question
├── selected_option
├── rationale?
├── decided_by_actor_ref
├── authority_ref?
├── decided_at
├── evidence_refs[]
├── observation_refs[]
├── deliberation_ref?
├── supersedes_ref?
└── source_refs[]
```

`DecisionRecord` pourra enrichir ce contrat en DOMAIN-00.7 avec alternatives rejetées, objections, hypothèses et conditions de révision.

Invariant :

```text
Decision ≠ Observation
Decision ≠ Action
```

---

## 19. `Action`

### Définition

> Une `Action` est un travail ou suivi explicitement demandé à un acteur ou une équipe.

```text
Action
├── action_id
├── project_ref
├── scope_ref
├── title
├── description?
├── assigned_actor_refs[]
├── created_from_ref?
├── due_at?
├── status
└── source_refs[]
```

Invariant :

```text
Action COMPLETED
≠
Issue RESOLVED
```

Une action de reprise peut être terminée alors qu'un contrôle reste nécessaire avant résolution de l'Issue.

---

## 20. Exemple multilingue terrain — sous-traitant turc

Un maçon dicte en turc sur son téléphone et joint deux photos.

```text
L0 RAW
ProjectContribution
source_language = tr
input_mode = voice_transcription
assets = [photo_1, photo_2]
```

Nestor produit :

```text
L1 INTERPRETED
ContributionInterpretation

STATED_BY_CONTRIBUTOR
- l'ouverture à droite du mur est trop petite pour le tuyau

OBSERVED_FROM_EVIDENCE
- une réservation est visible
- un réseau est visible

UNKNOWN
- cote exacte de la réservation
- diamètre exact du réseau

INFERRED
- un contrôle de cote peut être nécessaire
```

L'interface restitue l'interprétation en turc, de manière courte et éventuellement vocale.

L'auteur choisit :

```text
[ CONFIRMER ]
[ CORRIGER ]
[ REPARLER ]
```

Après confirmation :

```text
L2 AUTHOR-CONFIRMED
```

Nestor peut alors produire :

```text
ObservationCandidate
IssueCandidate
ActionCandidate
```

La confirmation ne transforme pas ces candidats en objets autoritatifs.

---

## 21. Documents et actes à enjeu élevé

Pour les objets à portée contractuelle, juridique ou de responsabilité élevée, la confirmation linguistique de l'auteur n'est pas un gate suffisant.

Exemples :

```text
avenant
réception
réserve formelle
ordre de travaux
acceptation technique engageante
reconnaissance de responsabilité
```

Flux cible :

```text
Contribution multilingue
↓
Interpretation
↓
ContributorConfirmation
↓
version française de référence
↓
gate MDTC / autorité compétente
↓
objet ou document autoritatif
```

---

## 22. Invariants adoptés

```text
ProjectContribution ≠ Nestor analysis
original ≠ derivative ≠ translation ≠ interpretation
Contribution ≠ Observation structurée
Evidence ≠ vérité
Evidence ≠ causalité
Evidence ≠ responsabilité
Observation REPORTED ≠ Observation VERIFIED
ProjectEvent ≠ event bus technique
document reçu ≠ document approuvé
plan révisé ≠ baseline modifiée
Issue ≠ Blocker
Issue ≠ Risk
Blocker ≠ Delay
Decision ≠ Action
Action COMPLETED ≠ Issue RESOLVED
Nestor candidate ≠ authoritative domain object
AUTHOR-CONFIRMED ≠ DOMAIN-VALIDATED
```

Invariant linguistique :

```text
source_language
≠ interaction_language
≠ canonical_language
```

Ces valeurs peuvent être identiques dans certains cas, mais elles représentent trois responsabilités distinctes.

---

## 23. Hors périmètre de DOMAIN-00.3

Sont volontairement différés :

- taxonomie détaillée des documents et plans ;
- causalité ;
- responsabilité juridique ;
- impacts coût / délai ;
- moteur global de confiance, expertise ou crédibilité Nestor ;
- event envelope technique complet ;
- routage vers un fournisseur ou modèle LLM précis ;
- implémentation frontend de transcription ou TTS.

Le domaine exprime les contrats et exigences ; Nestor et la noria résolvent les capacités techniques compatibles.
