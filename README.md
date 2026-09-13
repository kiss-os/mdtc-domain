# mdtc-domain

> **Contrats de domaine construction de la noria MDTC** — projets, lots, work packages, acteurs, ressources, planning, économie, exécution, preuves et responsabilités.

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
Lot ≠ WorkPackage
WorkPackage ≠ Task
WorkPackage ≠ QuoteLine
Task ≠ TimeEntry
Contribution ≠ Observation validée
Observation ≠ causalité
Causalité ≠ responsabilité
Expertise ≠ autorité ≠ permission
Decision ≠ Action
Project live state ≠ contractual baseline
Nestor interpretation ≠ MDTC business truth
```

## Hiérarchie opérationnelle

La hiérarchie actuellement adoptée est :

```text
Program
   ↓
Project
   ↓
Lot
   ↓
WorkPackage
   ↓
Task
```

Tous les niveaux ne sont pas obligatoires.

Un petit chantier peut être modélisé directement comme :

```text
Project
└── WorkPackage
    └── Tasks
```

Une opération multi-projets, par exemple un lotissement, peut être structurée comme :

```text
Program — Lotissement Les Oliviers
├── Project — Maison 01
├── Project — Maison 02
├── Project — Maison 03
└── Project — VRD / communs
```

Le choix **une maison = un `Project`** permet de préserver un périmètre clair pour le planning, les observations, les preuves, les coûts, la qualité et les responsabilités, tout en laissant `Program` agréger l'ensemble.

## Définition actuelle de `Project`

Un `Project` est l'enveloppe persistante d'une intention de travail suffisamment identifiée, depuis sa première qualification numérique ou terrain jusqu'à sa clôture, qu'un marché soit finalement remporté ou non.

Un projet peut donc représenter :

- une qualification numérique ou une estimation exploratoire ;
- une visite ou un constat sur site ;
- un travail simple comme un muret ou une pose de fenêtre ;
- une rénovation ou une extension ;
- une construction complexe ;
- un projet appartenant à un `Program` plus large ;
- un dossier non remporté mais ayant généré un travail MDTC traçable.

Le **chantier** est une phase possible d'un `Project`, pas sa définition.

## Cycle projet — cadrage courant

Le cycle macro envisagé reste volontairement compact :

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

Cela évite de mélanger état métier, issue commerciale et état d'exécution.

## Qualification numérique

Une demande client peut devenir un `Project` dès lors qu'elle déclenche un véritable travail MDTC/Nestor : qualification, décomposition du besoin, estimation, identification d'inconnues ou préparation d'une visite.

```text
ClientRequest
    ↓
QualificationSession
    ↓
Project(stage = QUALIFICATION)
```

Une estimation préliminaire reste distincte d'un devis commercial :

```text
PreliminaryEstimate ≠ CommercialQuote
```

Les quotas éventuels d'estimations gratuites relèvent d'une policy de produit / entitlement et ne doivent pas être codés dans `Project` ni dans le frontend comme autorité.

## Frontières de responsabilité

`mdtc-domain` **doit** contenir les contrats métier relatifs notamment à :

- Program / Project / Site ;
- Lot / WorkPackage / Task ;
- acteurs, organisations, rôles et corps de métier ;
- matériaux, équipements, approvisionnement et fourniture ;
- planning, temps et exécution ;
- économie chantier ;
- observations, décisions, actions, risques et changements ;
- preuves et contributions terrain ;
- responsabilité et contexte de garantie ;
- baselines contractuelles et projections opérationnelles.

`mdtc-domain` **ne doit pas** devenir :

- un CRM ou ERP complet ;
- un moteur de rendu PDF ;
- un routeur LLM ;
- un stockage de prompts ;
- le moteur de délibération Nestor ;
- une duplication de la CCU, de Chronos ou du graphe de connaissance commun ;
- une implémentation d'interface React.

Voir [`SCOPE.md`](./SCOPE.md) pour le périmètre détaillé.

## DOMAIN-00

Le chantier de fondation est **DOMAIN-00 — ontologie fondamentale du chantier**.

Roadmap actuelle :

1. **DOMAIN-00.1** — Project / Program / Work Breakdown / WorkPackage
2. **DOMAIN-00.2** — Actors / Organizations / Roles / Trades
3. **DOMAIN-00.3** — Materials / Equipment / Supply / Procurement
4. **DOMAIN-00.4** — Economics: estimated / committed / actual / billed / paid / margin
5. **DOMAIN-00.5** — Planning / TimeEntry / Meetings / execution lifecycle
6. **DOMAIN-00.6** — Changes / Baselines / Decisions / Actions / Impacts
7. **DOMAIN-00.7** — Responsibility / Warranty context / Client-supplied items / Evidence
8. **DOMAIN-00.8** — Consolidation / event model / projections

Le cadrage vivant de DOMAIN-00 est documenté dans [`docs/DOMAIN-00.md`](./docs/DOMAIN-00.md).

## Statut

Le dépôt est en phase de **fondation ontologique**. Les contrats sont ajoutés progressivement après validation métier ; une présence dans le dépôt ne doit pas être interprétée comme une implémentation complète du backend MDTC.
