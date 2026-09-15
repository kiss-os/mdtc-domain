# DOMAIN-00.2 — Actors / Organizations / Roles / Trades

Status: **validated conceptually**

## Objectif

DOMAIN-00.2 définit comment la noria MDTC représente les participants d'un projet sans confondre identité, rôle métier, corps de métier, autorité, permission ou responsabilité.

Principe directeur :

> **Client, sous-traitant, BET, contremaître, fournisseur ou partenaire ne sont pas des types d'identité ; ce sont des rôles contextualisés dans un projet.**

Architecture conceptuelle :

```text
Person / Organization
        ↓
      PartyRef
        ↓
   ProjectActor
        ↓
RoleAssignment / TradeAssignment / RepresentationAssignment / AuthorityAssignment
```

---

## 1. PartyRef

`mdtc-domain` ne recrée pas le registre d'identités du socle Nestor / IAM.

Il référence des identités externes au domaine :

```text
PartyRef
├── party_ref
├── kind = PERSON | ORGANIZATION
└── source_ref
```

Invariant :

```text
Person ≠ Organization
```

Le domaine conserve uniquement les références nécessaires à l'activité MDTC.

---

## 2. ProjectActor

Un `ProjectActor` représente la participation contextualisée d'une personne ou d'une organisation à un `Project`.

```text
ProjectActor
├── project_actor_id
├── project_ref
├── party_ref
├── active_from?
├── active_until?
├── organization_context_ref?
└── assignments[]
```

Le même `party_ref` peut participer à plusieurs projets avec des rôles différents.

Exemple : une entreprise peut être partenaire sur un projet, sous-traitante sur un autre et fournisseur sur un troisième.

---

## 3. Quatre dimensions séparées

Invariant central :

```text
Role
≠ Trade
≠ Authority
≠ Permission
```

Et :

```text
Expertise ≠ Authority ≠ Permission ≠ Responsibility
```

Exemple :

```text
role       = SUBCONTRACTOR
trade      = ELECTRICAL
authority  = EXECUTION_ONLY
permission = résolue par Nestor / CCU / Mithra
```

Le rôle métier n'accorde jamais implicitement une permission système.

---

## 4. ProjectRole

`ProjectRole` décrit la fonction contextuelle exercée dans l'opération.

Taxonomie initiale candidate :

```text
CLIENT
OWNER
MAIN_CONTRACTOR
PROJECT_MANAGER
FOREMAN
SITE_MANAGER
SUBCONTRACTOR
PARTNER
SUPPLIER
ARCHITECT
DESIGN_OFFICE
ECONOMIST
CONTROL_OFFICE
SPS_COORDINATOR
WORKER
TECHNICIAN
INSPECTOR
OTHER
```

Cette taxonomie reste amendable. Le point important est la sémantique :

```text
ProjectRole = fonction contextuelle dans l'opération
```

et non une identité permanente.

---

## 5. RoleAssignment

Un rôle n'est pas un simple tableau de chaînes. Il est contextualisé dans un périmètre et éventuellement dans le temps.

```text
RoleAssignment
├── role
├── scope_ref
├── valid_from?
├── valid_until?
├── contractual_basis_ref?
├── authority_ref?
└── source_refs[]
```

Exemple :

> Entreprise X intervient comme sous-traitante sur le lot gros œuvre à compter du 12 octobre.

---

## 6. Trade

`Trade` représente le corps de métier ou la discipline technique.

Exemples :

```text
MASONRY
EARTHWORKS
CARPENTRY
ROOFING
WATERPROOFING
EXTERIOR_JOINERY
ELECTRICAL
PLUMBING
HVAC
DRYWALL
TILING
PAINTING
STRUCTURAL_ENGINEERING
THERMAL_ENGINEERING
...
```

Le `Trade` répond à la question :

> **Dans quelle discipline technique cet acteur intervient-il ?**

Il ne remplace pas le rôle contractuel ou organisationnel.

---

## 7. TradeAssignment

Une personne ou organisation peut couvrir plusieurs métiers.

```text
TradeAssignment
├── trade_ref
├── scope_ref
├── primary
├── valid_from?
└── valid_until?
```

Exemple :

```text
Company A
role = SUBCONTRACTOR
trades = [MASONRY, EARTHWORKS]
```

ou :

```text
BET B
role = DESIGN_OFFICE
trades = [ELECTRICAL, HVAC, THERMAL_ENGINEERING]
```

---

## 8. WorkScopeRef

Le scope d'un rôle ou d'un métier doit être de première classe.

```text
WorkScopeRef
├── PROGRAM?
├── PROJECT
├── WORK_LOT?
├── WORK_PACKAGE?
└── TASK?
```

Un acteur peut ainsi intervenir :

```text
sur tout un Program
sur un Project
sur un WorkLot
sur un WorkPackage
ou sur une Task précise
```

Ce périmètre pourra être réutilisé par les projections UX, le planning, les affectations et les contrôles d'autorité.

---

## 9. RepresentationAssignment

Une personne peut participer au projet au nom d'une organisation.

```text
RepresentationAssignment
├── person_actor_ref
├── organization_actor_ref
├── capacity
├── valid_from?
└── valid_until?
```

Exemple :

```text
Thomas Martin
acts_for
BET Fluides SARL

capacity = PROJECT_ENGINEER
```

Cette distinction permet de conserver :

- l'auteur humain réel d'une contribution ;
- l'organisation représentée ;
- la capacité dans laquelle il agit ;
- la période de validité.

---

## 10. Expertise

L'expertise globale ou calibrée d'un utilisateur appartient au socle Nestor / CCU et ne doit pas être dupliquée dans `mdtc-domain`.

Le domaine peut référencer :

```text
expertise_profile_ref
```

mais ne doit pas porter un score global de crédibilité du type :

```text
ProjectActor.credibility = 0.92
```

Les contributions gardent leur auteur, puis Nestor peut produire un `EpistemicAssessment` contextualisé.

---

## 11. AuthorityAssignment

L'autorité métier doit être explicitement représentable sans être confondue avec la permission système.

```text
AuthorityAssignment
├── authority_kind
├── scope_ref
├── source_ref
├── valid_from?
└── valid_until?
```

Exemples d'autorités métier possibles :

```text
APPROVE_TECHNICAL_VARIANT
VALIDATE_WORK_PACKAGE
ACCEPT_DELIVERABLE
AUTHORIZE_SITE_CHANGE
```

La permission effective reste une résolution du socle :

```text
AuthorityAssignment
+
CCU / Mithra / policy
↓
effective permission
```

Invariant :

```text
Authority ≠ Permission
```

---

## 12. Responsibility

La responsabilité détaillée n'est pas traitée dans DOMAIN-00.2.

Un objet métier peut porter une affectation simple telle que :

```text
assigned_actor_ref
responsible_actor_ref
```

mais la modélisation complète de :

```text
execution responsibility
supply responsibility
approval responsibility
contractual responsibility
warranty context
```

est reportée à DOMAIN-00.7.

---

## 13. Partner / Subcontractor / Supplier

Ces rôles sont explicitement distincts :

```text
PARTNER ≠ SUBCONTRACTOR
SUPPLIER ≠ SUBCONTRACTOR
```

Une même organisation peut exercer plusieurs rôles selon le projet ou le scope, mais aucune relation contractuelle ne doit être déduite implicitement d'un autre rôle.

---

## 14. Exemple

```text
Project — Extension maison Durand

ProjectActor A1
party = Méditerranée Construction
role = MAIN_CONTRACTOR

ProjectActor A2
party = Ahmet
role = FOREMAN
acts_for = MDTC
scope = PROJECT

ProjectActor A3
party = BET Structure Alpha
role = DESIGN_OFFICE
trade = STRUCTURAL_ENGINEERING

ProjectActor A4
party = Marc
role = PROJECT_ENGINEER
acts_for = BET Structure Alpha

ProjectActor A5
party = Maçonnerie Sud
role = SUBCONTRACTOR
trade = MASONRY
scope = WorkLot Gros œuvre

ProjectActor A6
party = Matériaux 11
role = SUPPLIER
```

---

## 15. Invariants validés

```text
PartyRef
= référence Person ou Organization externe au domaine

ProjectActor
= participation contextualisée au Project

RoleAssignment
= fonction exercée dans un scope donné

TradeAssignment
= discipline / corps de métier dans un scope donné

RepresentationAssignment
= personne agissant au nom d'une organisation

AuthorityAssignment
= autorité métier déclarée/contextualisée
```

Et :

```text
Role ≠ Trade ≠ Authority ≠ Permission
Expertise ≠ Authority
Partner ≠ Subcontractor
Supplier ≠ Subcontractor
```

Les permissions effectives restent résolues par Nestor / CCU / Mithra.

La responsabilité détaillée est reportée à DOMAIN-00.7.

---

## 16. État de validation

Tous les principes de ce document ont été validés conceptuellement le 2026-09-15.

La prochaine étape du fil rouge est :

**DOMAIN-00.3 — Contributions / faits / preuves**.
