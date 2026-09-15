# DOMAIN-00.1 — Work Breakdown Structure

Status: `validated`

Validation: Président Olivier — tous les points validés le 2026-09-15.

## 1. Objet

DOMAIN-00.1 fixe la grammaire structurelle du travail dans la noria MDTC.

Hiérarchie adoptée :

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

---

## 2. Program

`Program` regroupe plusieurs `Project` cohérents sans devenir un « mega Project » opérationnel.

Exemple adopté pour un lotissement :

```text
Program — Lotissement Les Oliviers
├── Project — Maison 01
├── Project — Maison 02
├── Project — Maison 03
└── Project — VRD / communs
```

La stratégie par défaut est **une maison = un Project** afin de préserver un périmètre clair pour :

- planning ;
- coûts ;
- photos et preuves ;
- observations ;
- qualité ;
- réserves ;
- responsabilités ;
- périmètres de contremaître.

`Program` sert à agréger et coordonner sans mélanger l'exploitation quotidienne des projets.

---

## 3. WorkLot

### 3.1 Terminologie

Le nom de classe métier adopté est `WorkLot`, afin d'éviter l'ambiguïté avec un lot foncier de lotissement.

```text
WorkLot ≠ Parcel
```

- `WorkLot` : lot technique et/ou contractuel de travaux.
- `Parcel` : parcelle / lot foncier.

L'interface française peut afficher simplement « Lot » lorsque le contexte est non ambigu.

### 3.2 Définition

> Un `WorkLot` est un regroupement explicite de travaux d'un `Project` selon une cohérence technique, contractuelle, ou les deux.

Nature proposée :

```text
TECHNICAL
CONTRACTUAL
BOTH
```

Exemple :

```text
Project — Maison 04
├── WorkLot — Terrassement
├── WorkLot — Gros œuvre
├── WorkLot — Charpente
├── WorkLot — Menuiseries extérieures
└── WorkLot — Électricité
```

`WorkLot` est optionnel. Un petit projet ne crée pas un lot artificiel uniquement pour satisfaire une hiérarchie.

```text
Project
└── WorkPackage — Création d'un muret
```

est parfaitement valide.

---

## 4. WorkPackage

### 4.1 Définition

> Un `WorkPackage` est une unité cohérente de travail suffisamment autonome pour être estimée, planifiée, affectée, suivie et contrôlée.

Le `WorkPackage` est l'unité opérationnelle centrale du domaine MDTC.

Il pourra recevoir, au fil des itérations suivantes :

```text
coût estimé
temps estimé
acteur responsable
participants
matériaux
équipements
dépendances
planning
temps réel
coût réel
photos
observations
qualité
irrégularités
reprises
avancement
contrôles
```

### 4.2 Appartenance

Un `WorkPackage` :

```text
→ appartient à exactement 1 Project
→ appartient à 0..1 WorkLot
```

Un `WorkPackage` ne doit pas appartenir à plusieurs `WorkLot`.

Lorsqu'une activité traverse plusieurs métiers ou lots, le modèle préfère plusieurs `WorkPackage` reliés par des interfaces ou dépendances.

Exemple :

```text
WP — Création de l'ouverture
↕ Interface gros œuvre / menuiserie
WP — Fourniture et pose de la fenêtre
```

plutôt qu'un même `WorkPackage` rattaché à deux lots.

### 4.3 Critères de séparation

Créer plusieurs `WorkPackage` lorsqu'une partie nécessite une gestion indépendante sur au moins un axe significatif :

- acteur responsable différent ;
- corps de métier différent ;
- planning distinct ;
- dépendance importante ;
- coût à suivre séparément ;
- approvisionnement séparé ;
- validation séparée ;
- contrôle ou preuve séparée ;
- réception séparée ;
- changement possible indépendamment.

À l'inverse, ne pas fragmenter le travail en micro-objets sans valeur de coordination.

---

## 5. Task

### 5.1 Définition

> Une `Task` est une étape opérationnelle interne d'un `WorkPackage`, utilisée lorsqu'une décomposition apporte une valeur de coordination ou d'exécution.

Cardinalité :

```text
Task → exactement 1 WorkPackage
WorkPackage.tasks = 0..n
```

Exemple :

```text
WP — Pose Velux
├── Task — Préparation couverture
├── Task — Création chevêtre
├── Task — Pose fenêtre
├── Task — Étanchéité
├── Task — Habillage intérieur
└── Task — Contrôle final
```

Pour une intervention simple, aucune `Task` n'est obligatoire.

### 5.2 Task ≠ checklist

Une `Task` décrit ce qui est exécuté.

Les éléments du type :

```text
vérifier niveau
prendre photo
porter EPI
```

relèvent potentiellement de futurs objets comme :

```text
ControlPoint
ChecklistItem
Requirement
EvidenceRequirement
```

Ils ne doivent pas transformer `Task` en fourre-tout.

### 5.3 Task ≠ TimeEntry

`Task` représente le travail à exécuter ; `TimeEntry` représente du temps effectivement attribué à une activité par un acteur.

```text
Task ≠ TimeEntry
```

Un même `Task` peut recevoir plusieurs `TimeEntry`.

---

## 6. WorkPackage ≠ QuoteLine

La structure opérationnelle n'est pas la présentation commerciale.

```text
WorkPackage ≠ QuoteLine
```

Exemples valides :

```text
1 WorkPackage
→ plusieurs QuoteLines
```

ou :

```text
plusieurs WorkPackages
→ 1 QuoteLine commerciale
```

Le devis est une projection commerciale du domaine, pas la source de vérité de la structure opérationnelle.

---

## 7. Qualification numérique et Nestor

Pendant une qualification exploratoire, Nestor ne crée pas immédiatement des `WorkPackage` autoritatifs.

```text
Project(stage = QUALIFICATION)
        ↓
ScopeCandidate
        ↓
WorkPackageCandidate[]
```

Un candidat peut porter notamment :

```text
confidence
unknowns
assumptions
```

Puis, après matérialisation / validation selon le gate approprié :

```text
WorkPackageCandidate
        ↓
WorkPackage
```

Invariant :

```text
Nestor suggestion ≠ authoritative WorkPackage
```

---

## 8. Corps de métier

Ne pas encoder un corps de métier comme identité absolue d'un `WorkLot`.

Relations prévues :

```text
WorkLot
→ trade_refs[]

WorkPackage
→ primary_trade_ref?
→ supporting_trade_refs[]
```

Un `WorkLot` peut couvrir plusieurs disciplines. Un `WorkPackage` peut avoir un métier principal et des métiers supports sans appartenir à plusieurs lots.

La définition complète de `Trade` appartient à DOMAIN-00.2.

---

## 9. Cardinalités validées

```text
Program
└── Project [1..n]

Project
├── Program [0..1]
├── WorkLot [0..n]
└── WorkPackage [0..n]

WorkLot
├── Project [1]
└── WorkPackage [1..n]

WorkPackage
├── Project [1]
├── WorkLot [0..1]
└── Task [0..n]

Task
└── WorkPackage [1]
```

Un `Project` peut temporairement avoir `0 WorkPackage` pendant une qualification très précoce.

---

## 10. Contrat conceptuel initial

Sans figer encore l'implémentation technique :

```text
WorkLot
├── work_lot_id
├── project_ref
├── reference
├── designation
├── kind
├── trade_refs[]
├── source_refs[]
└── work_package_refs[]

WorkPackage
├── work_package_id
├── project_ref
├── work_lot_ref?
├── reference
├── title
├── description?
├── primary_trade_ref?
├── supporting_trade_refs[]
├── dependency_refs[]
├── requirement_refs[]
└── task_refs[]

Task
├── task_id
├── work_package_ref
├── reference?
├── title
├── description?
├── dependency_refs[]
└── requirement_refs[]
```

Coût, temps, état détaillé et responsabilité ne sont volontairement pas figés ici. Ces dimensions se brancheront sur ces objets dans les itérations dédiées.

---

## 11. Invariants validés

```text
Program ≠ Project
WorkLot ≠ Parcel
WorkLot ≠ WorkPackage
WorkPackage ≠ Task
WorkPackage ≠ QuoteLine
Task ≠ TimeEntry
Task ≠ ControlPoint
```

```text
WorkLot est optionnel.
Task est optionnelle.
WorkPackage est l'unité opérationnelle centrale.
```

```text
Un WorkPackage appartient à un seul Project
et au maximum à un WorkLot.
```

```text
Un besoin multi-lots est préférentiellement décomposé
en plusieurs WorkPackages reliés par Interface / Dependency.
```

```text
WorkPackageCandidate ≠ WorkPackage
```

---

## 12. Suite

DOMAIN-00.1 est considéré comme **validé au niveau conceptuel**.

La prochaine itération du fil rouge est :

**DOMAIN-00.2 — Actors / Organizations / Roles / Trades**.

Les points à y arbitrer incluront notamment :

- personne vs organisation ;
- acteur projet ;
- rôle contextuel ;
- métier / corps d'état ;
- contremaître ;
- salarié ;
- sous-traitant ;
- partenaire ;
- BET ;
- client ;
- expertise vs autorité vs permission vs responsabilité.
