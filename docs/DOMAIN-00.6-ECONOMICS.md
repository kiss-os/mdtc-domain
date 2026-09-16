# DOMAIN-00.6 — Economics

Status: **validated conceptually**

## 1. Objet

DOMAIN-00.6 définit l'économie chantier sans transformer `mdtc-domain` en logiciel comptable, ERP, paie ou fiscalité.

Le domaine doit pouvoir expliquer :

- d'où vient chaque montant ;
- à quel scope il appartient ;
- s'il s'agit d'une estimation, d'un budget, d'un engagement, d'un coût réellement encouru, d'un revenu contracté, d'une facturation ou d'un mouvement de trésorerie ;
- sur quelle base un coût ou une marge a été calculé ;
- comment les écarts entre estimation, budget, engagements, réel et forecast se forment.

Principe directeur :

> **MDTC doit pouvoir expliquer d'où vient chaque montant, à quel scope il appartient et quelle réalité économique il représente, sans confondre coût, revenu, facturation et trésorerie.**

---

## 2. `COST` ≠ `REVENUE` ≠ `CASH`

Les axes historiques `ESTIMATED / COMMITTED / ACTUAL / BILLED / PAID` restent utiles pour les vues de pilotage, mais ils ne forment pas un cycle de statut unique.

### Côté coûts

```text
ESTIMATED COST
↓
BUDGETED COST
↓
COMMITTED COST
↓
ACTUAL / INCURRED COST
↓
CASH OUT
```

### Côté revenus

```text
ESTIMATED REVENUE
↓
CONTRACTED REVENUE
↓
BILLED REVENUE
↓
CASH IN / COLLECTED
```

Invariants :

```text
COST ≠ REVENUE ≠ CASH
invoice issued ≠ revenue collected
supplier invoice received ≠ cost incurred ≠ supplier paid
```

---

## 3. `CostEstimate` ≠ `InternalBudget` ≠ `CommercialQuote`

### `CostEstimate`

Combien le travail est estimé coûter à MDTC.

### `InternalBudget`

Quelle référence économique interne MDTC décide de retenir.

### `CommercialQuote`

Quel prix MDTC propose au client.

Invariant :

```text
CostEstimate ≠ InternalBudget ≠ CommercialQuote
```

Exemple :

```text
coût estimé MDTC       6 500 EUR
budget interne         6 800 EUR
prix commercial        9 700 EUR
```

La décision déjà adoptée reste valable :

```text
WorkPackage ≠ QuoteLine
```

---

## 4. `PreliminaryEstimate`

> Une `PreliminaryEstimate` est une estimation économique exploratoire pouvant comporter des plages, hypothèses et inconnues, sans constituer un engagement commercial.

```text
PreliminaryEstimate
├── estimate_id
├── project_ref
├── scope_ref
├── estimate_kind
├── calculated_at
├── amount_range?
├── currency
├── assumptions[]
├── unknowns[]
├── confidence?
├── basis_refs[]
└── source_refs[]
```

Pendant la qualification :

```text
Project(stage = QUALIFICATION)
↓
WorkPackageCandidate[]
↓
PreliminaryEstimate
```

Invariant :

```text
PreliminaryEstimate ≠ CommercialQuote ≠ Contract
```

---

## 5. Le domaine économique réutilise le réel opérationnel

DOMAIN-00.6 ne recrée pas son propre chantier.

Il réutilise notamment :

```text
WorkPackage
TimeEntry
MaterialUsageRecord
EquipmentAssignment / EquipmentProvision
ResourceIncident
ProjectActor / subcontractor context
```

Exemples :

```text
TimeEntry
↓
LaborCost calculation

MaterialUsageRecord
↓
MaterialCost calculation

Equipment usage/provision
↓
EquipmentCost calculation

Subcontract commitment
↓
SubcontractCost
```

Ces transformations restent explicites et traçables.

---

## 6. `MoneyValue`

Un montant économique ne doit jamais être stocké sans contexte monétaire.

```text
MoneyValue
├── amount
├── currency
└── tax_basis
```

`amount` doit être représenté avec une précision décimale adaptée, jamais avec une arithmétique flottante implicite.

`tax_basis` :

```text
EXCL_TAX
INCL_TAX
UNKNOWN
```

Invariant :

```text
1500 EUR HT ≠ 1500 EUR TTC
```

Pour les analyses internes de coût/marge, MDTC pourra privilégier des valeurs hors taxe, mais cette base ne doit jamais être supposée silencieusement.

---

## 7. Fiscalité hors cœur économique

DOMAIN-00.6 peut transporter ou référencer un résultat fiscal déjà résolu :

```text
net
 tax?
gross?
```

mais ne devient pas le moteur de règle TVA.

Sont distincts :

```text
economic amount
≠ tax rule
≠ tax compliance
≠ tax declaration
```

Les règles fiscales détaillées restent dans les couches financières/compliance appropriées.

---

## 8. `CostEstimate`

```text
CostEstimate
├── cost_estimate_id
├── project_ref
├── scope_ref
├── category_ref
├── quantity?
├── unit?
├── estimated_unit_cost?
├── estimated_amount
├── confidence?
├── assumptions[]
├── basis_refs[]
├── calculated_at
└── source_refs[]
```

Catégories initiales possibles :

```text
LABOUR
MATERIAL
EQUIPMENT
SUBCONTRACT
TRANSPORT
OTHER_DIRECT
ALLOCATED_OVERHEAD
```

La taxonomie doit rester extensible via un `CostCategoryRef` plutôt que dépendre d'un enum métier figé.

---

## 9. Coût direct ≠ frais généraux alloués

```text
DIRECT ≠ ALLOCATED_OVERHEAD
```

Exemples :

```text
ciment                  direct
temps maçon              direct
sous-traitant            direct

assurance entreprise     indirect
loyer bureau              indirect
abonnements logiciels    indirect
```

Une policy peut répartir certains coûts indirects sur les projets.

Invariant :

```text
allocated overhead ≠ directly caused by WorkPackage
```

---

## 10. `InternalBudget` et `BudgetRevision`

Une estimation peut évoluer librement ; un budget devient une référence interne volontairement adoptée.

```text
InternalBudget
├── budget_id
├── project_ref
├── revision_refs[]
└── source_refs[]
```

```text
BudgetRevision
├── budget_revision_id
├── budget_ref
├── revision_number
├── created_at
├── created_by_actor_ref
├── status
├── budget_lines[]
└── source_refs[]
```

États :

```text
DRAFT
APPROVED
SUPERSEDED
```

Une version approuvée ne doit pas être réécrite silencieusement.

Invariant :

```text
approved budget ≠ contractual baseline
```

DOMAIN-00.7 pourra définir si une `ProjectBaseline` référence une version économique approuvée.

---

## 11. `CostCommitment`

> Un `CostCommitment` représente un engagement économique pris envers un tiers ou une ressource externe, avant paiement effectif.

```text
CostCommitment
├── commitment_id
├── project_ref
├── scope_ref
├── category_ref
├── counterparty_actor_ref?
├── committed_amount
├── committed_at
├── commitment_basis_ref
├── external_record_ref?
├── status
└── source_refs[]
```

Exemples :

```text
commande fournisseur      1 800 EUR
sous-traitance acceptée   4 200 EUR
```

Invariant :

```text
COMMITTED COST ≠ ACTUAL COST ≠ CASH OUT
```

---

## 12. `ActualCost`

> Un `ActualCost` représente un coût réellement encouru par le projet selon la politique économique MDTC.

```text
ActualCost
├── actual_cost_id
├── project_ref
├── scope_ref
├── category_ref
├── amount
├── incurred_at
├── source_kind
├── source_refs[]
├── allocation_refs[]
└── calculation_ref?
```

Sources candidates :

```text
LABOUR_TIME
MATERIAL_USAGE
SUPPLIER_CHARGE
SUBCONTRACT
EQUIPMENT
TRANSPORT
OVERHEAD_ALLOCATION
OTHER
```

Invariant :

```text
ActualCost ≠ SupplierInvoice ≠ CashPayment
```

Exemple :

```text
travail sous-traitant terminé   15/09  → coût encouru
facture fournisseur reçue       30/09  → pièce fournisseur
paiement                        15/10  → cash out
```

---

## 13. Travail humain : `TimeEntry + CostRateSnapshot`

DOMAIN-00.5 a établi :

```text
TimeEntry ≠ CostEntry
```

La transformation économique devient :

```text
TimeEntry
+
CostRateSnapshot
↓
LaborCostCalculation
↓
ActualCost
```

```text
CostRateSnapshot
├── rate_snapshot_id
├── rate_kind
├── amount
├── unit
├── currency
├── effective_at
├── source_ref
└── policy_ref?
```

Exemple :

```text
32.40 EUR / hour
```

Le snapshot évite de recalculer l'histoire avec un taux courant différent.

```text
historical TimeEntry
+
historical CostRateSnapshot
=
stable historical labour cost
```

---

## 14. `CostRate` ≠ salaire

Un coût horaire interne peut, selon policy, intégrer par exemple :

```text
salaire
charges
assurances
outillage
véhicule
overhead
```

Invariant :

```text
CostRate ≠ SalaryRate
```

DOMAIN-00.6 transporte le résultat, la période de validité et la provenance de la policy ; il ne devient pas un moteur RH/paie.

---

## 15. Validation du temps avant coût autoritatif

Selon la policy MDTC :

```text
TimeEntry SUBMITTED
↓
CostCandidate
```

puis par exemple :

```text
TimeEntry CONFIRMED
↓
ActualCost
```

Invariant :

```text
unconfirmed TimeEntry ≠ authoritative labour cost
```

La policy exacte de promotion pourra être implémentée ultérieurement sans modifier l'invariant.

---

## 16. Coût matériel et base de valorisation

```text
MaterialUsageRecord
+
MaterialCostBasis
↓
MaterialCost calculation
↓
ActualCost
```

```text
MaterialCostBasis
├── unit_cost
├── quantity
├── acquisition_ref?
├── valuation_method?
└── captured_at
```

Le domaine doit pouvoir expliquer sur quelle base le coût a été calculé.

En revanche, FIFO/LIFO, comptabilité de stock et valorisation réglementaire exhaustive restent du ressort d'un ERP/comptable externe.

---

## 17. `ResourceIncident` ≠ coût automatique

Exemple :

```text
ResourceIncident
kind = MISSING
```

peut produire :

```text
CostImpactCandidate
```

mais ne produit pas automatiquement un `ActualCost` ni une responsabilité.

Invariant :

```text
ResourceIncident ≠ ActualCost ≠ Responsibility
```

Un coût réel apparaît lorsqu'une conséquence économique est effectivement constatée ou validée, par exemple le remplacement de la ressource.

---

## 18. `RevenueEstimate`

```text
RevenueEstimate
├── revenue_estimate_id
├── project_ref
├── scope_ref
├── estimated_amount
├── calculated_at
├── assumptions[]
├── confidence?
└── source_refs[]
```

Invariant :

```text
RevenueEstimate ≠ CommercialQuote
```

Il peut exister avant toute proposition commerciale formelle.

---

## 19. `ContractedRevenue`

Lorsqu'un devis, contrat ou avenant devient réellement engageant :

```text
CommercialQuote / Contract / ChangeOrder
↓ acceptance / authority gate
ContractedRevenue
```

```text
ContractedRevenue
├── contracted_revenue_id
├── project_ref
├── scope_ref?
├── amount
├── commercial_basis_ref
├── effective_at
└── source_refs[]
```

Sources possibles :

```text
accepted_quote_ref
contract_ref
change_order_ref
```

Invariant :

```text
quoted revenue ≠ contracted revenue
```

---

## 20. Impact économique d'un changement

Une demande de travaux supplémentaires ou un impact calculé ne modifie pas le revenu contracté.

```text
ChangeRequest
↓
ImpactAssessment
↓
Decision
↓
accepted ChangeOrder
↓
ContractedRevenue adjustment
```

Invariant :

```text
ChangeRequest economic impact ≠ ContractedRevenue change
```

La mécanique détaillée de changement et d'autorité relève de DOMAIN-00.7.

---

## 21. `BilledRevenue`

```text
BilledRevenue
├── billing_ref
├── project_ref
├── scope_refs[]
├── amount
├── issued_at
├── invoice_ref
└── source_refs[]
```

Le domaine référence la réalité économique de facturation sans recopier le modèle documentaire/fiscal de PRINT-01.

```text
mdtc-domain
→ resolved business references

mdtc-docs
→ deterministic invoice rendering
```

Invariant :

```text
BILLED ≠ COLLECTED
```

---

## 22. `Settlement`

> Un `Settlement` représente un mouvement de trésorerie pertinent pour le projet, sans devenir le grand livre bancaire/comptable.

```text
Settlement
├── settlement_id
├── project_ref?
├── direction
├── amount
├── occurred_at
├── counterparty_ref?
├── external_payment_ref?
├── allocation_refs[]
└── source_refs[]
```

Directions :

```text
INCOMING
OUTGOING
```

Il peut représenter notamment :

- acompte client ;
- paiement de facture ;
- remboursement ;
- paiement fournisseur.

Invariants :

```text
CashIn ≠ Revenue
CashOut ≠ Cost
```

---

## 23. Paiement partiel et `EconomicAllocation`

Un paiement ou une charge peut être réparti entre plusieurs objets économiques.

```text
EconomicAllocation
├── allocation_id
├── source_ref
├── target_ref
├── scope_ref?
├── amount
├── allocation_basis
└── source_refs[]
```

Cas d'usage :

- paiement réparti sur plusieurs factures ;
- facture fournisseur répartie sur plusieurs `WorkPackage` ;
- coût global réparti sur plusieurs projets ;
- frais généraux répartis sur plusieurs scopes.

Invariants :

```text
EconomicAllocation ≠ additional cost
EconomicAllocation ≠ causal attribution
```

Une allocation répartit une valeur existante ; elle ne doit pas créer un double comptage.

---

## 24. Invariant anti-double-comptage

Exemple :

```text
Supplier charge = 1 000 EUR
```

répartie :

```text
WP-A = 600 EUR
WP-B = 400 EUR
```

La projection Project ne doit jamais calculer :

```text
1 000 + 600 + 400 = 2 000
```

Les allocations sont des répartitions, pas de nouveaux coûts.

Cet invariant devra être rendu exécutable en DOMAIN-00.10.

---

## 25. `EconomicForecast`

Lorsque le domaine connaît :

```text
budget
actual
commitments
remaining work
```

il peut produire un forecast explicable.

```text
EconomicForecast
├── forecast_id
├── project_ref
├── scope_ref?
├── as_of
├── forecast_cost_to_complete
├── forecast_total_cost
├── forecast_revenue?
├── assumptions[]
├── basis_refs[]
└── confidence?
```

Notions utiles :

```text
ETC — Estimate To Complete
= coût restant prévu

EAC — Estimate At Completion
= actual + coût restant prévu
```

Le domaine peut conserver les composants et bases de calcul sans imposer ces acronymes dans l'UX.

---

## 26. `MarginProjection`

La marge est une projection dérivée, jamais un champ mutable unique du `Project`.

```text
MarginProjection
├── project_ref
├── scope_ref?
├── as_of
├── revenue_basis
├── cost_basis
├── revenue_amount
├── cost_amount
├── margin_amount
├── margin_ratio
└── basis_refs[]
```

Plusieurs marges peuvent coexister :

```text
ESTIMATED MARGIN
COMMERCIAL / BUDGET MARGIN
ACTUAL-TO-DATE MARGIN
FORECAST FINAL MARGIN
```

Invariant :

```text
Margin = derived projection ≠ mutable source-of-truth field
```

Une marge n'a de sens que si sa base de revenu, sa base de coût et sa date sont explicites.

---

## 27. Qualité, rework et responsabilité

L'analyse future pourra croiser :

```text
TIME × COST × QUALITY × RELIABILITY
```

mais DOMAIN-00.6 n'invente pas de conversion comptable arbitraire depuis une qualité ou une note.

DOMAIN-00.7 introduira notamment `Rework`.

Économiquement :

```text
Rework
↓
additional TimeEntry
additional MaterialUsage
additional subcontract/equipment cost
↓
ActualCost
```

Invariant :

```text
rework cost ≠ liability
```

Le coût réel d'une reprise peut être mesuré sans décider automatiquement qui doit juridiquement ou contractuellement le supporter.

---

## 28. Exemple sous-traitant

```text
sous-traitance acceptée
4 000 EUR
↓
CostCommitment
```

Puis :

```text
travail réalisé
↓
ActualCost
```

Puis :

```text
facture reçue
↓
ExternalSupplierInvoiceRef
```

Puis :

```text
paiement
↓
Settlement OUTGOING
```

Ces étapes peuvent avoir des dates et des montants différents.

---

## 29. Nestor et estimation économique

Nestor peut exploiter les données historiques et courantes :

```text
WorkPackage
+ dimensions
+ resource requirements
+ MaterialUsage history
+ TimeEntry history
+ ActualCost history
+ quality/rework context
↓
CostEstimateCandidate
```

Mais :

```text
AI cost estimate ≠ approved internal budget
AI cost estimate ≠ commercial price
AI cost estimate ≠ contractual commitment
```

Nestor ne doit jamais inventer silencieusement un taux absent.

```text
unknown rate → UNKNOWN
```

Un benchmark peut être utilisé seulement avec provenance et hypothèses explicites.

---

## 30. `PricingPolicy` ≠ vérité économique

Le prix commercial peut être produit à partir d'une économie réelle et d'une politique commerciale.

```text
CostEstimate
↓
PricingPolicy
↓
CommercialQuote
```

Une `PricingPolicy` peut intégrer notamment :

- marge cible ;
- remise ;
- positionnement commercial ;
- risque ;
- contexte client.

Invariant :

```text
CostEstimate ≠ CommercialQuote
```

La stratégie de prix ne doit pas réécrire le coût estimé.

---

## 31. Frontière ERP / comptabilité

Un ERP ou outil comptable externe peut rester autorité pour :

```text
general ledger
supplier invoices
bank reconciliation
tax accounting
payroll
customer accounting
```

`mdtc-domain` conserve uniquement les vérités et références nécessaires au pilotage économique du chantier.

```text
external record
↓ provenance / import / reference
MDTC economic object or projection
```

Invariant :

```text
mdtc-domain ≠ general ledger
```

Le domaine ne dépend d'aucun produit ERP particulier.

---

## 32. Projection économique de dashboard

Les anciens axes deviennent une vue structurée plutôt qu'un état unique :

```text
PROJECT ECONOMIC VIEW

COST
Estimated     5 300 EUR
Budgeted      5 500 EUR
Committed     4 800 EUR
Actual        3 200 EUR
Cash out      2 600 EUR

REVENUE
Estimated     8 500 EUR
Contracted    8 500 EUR
Billed        5 000 EUR
Collected     3 000 EUR
```

Cette vue est une projection des objets économiques ; elle n'est pas une nouvelle source de vérité.

---

## 33. Exemple chantier simple

```text
CommercialQuote accepté
8 500 EUR HT
```

Budget :

```text
Labor          2 000
Material       1 800
Equipment        400
Subcontract      500
Overhead alloc.  600
────────────────────
Budget cost     5 300
```

Marge budgétée :

```text
8 500 - 5 300 = 3 200 EUR
```

À mi-chantier :

```text
Actual labour       1 200
Actual material     1 500
Commitment restant    900
Forecast remaining  2 100
```

Le domaine peut alors dériver :

```text
EAC = 5 700 EUR
Forecast final margin = 2 800 EUR
```

sans attendre la fin du chantier.

---

## 34. Noyau conceptuel adopté

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

Références candidates :

```text
CostCategoryRef
ExternalAccountingRef
ExternalProcurementRef
ExternalPaymentRef
```

---

## 35. Invariants adoptés

```text
COST ≠ REVENUE ≠ CASH
```

```text
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
unconfirmed TimeEntry ≠ authoritative labour cost
CostRate ≠ SalaryRate
```

```text
ResourceIncident ≠ ActualCost ≠ Responsibility
ChangeRequest economic impact ≠ ContractedRevenue change
rework cost ≠ liability
```

```text
CashIn ≠ Revenue
CashOut ≠ Cost
```

```text
EconomicAllocation ≠ additional cost
EconomicAllocation ≠ causal attribution
```

```text
Margin = derived projection ≠ mutable source-of-truth field
```

```text
AI estimate ≠ approved budget ≠ commercial price ≠ contractual commitment
```

---

## 36. Hors périmètre de DOMAIN-00.6

Sont externalisés ou reportés :

```text
general ledger
bank reconciliation
payroll
tax declaration
supplier accounting
full accounts receivable / payable
stock valuation accounting
depreciation
corporate P&L
```

Vers DOMAIN-00.7 :

```text
change orders
economic responsibility
penalties
rework liability
warranty responsibility
contractual baselines
```

---

## 37. Conséquence pour PRINT-02

Le futur devis ne doit pas inventer son propre modèle économique.

```text
Project
↓
WorkPackages
↓
CostEstimate / ResourceRequirements / time estimate
↓
PricingPolicy
↓
CommercialQuote
↓
mdtc-docs
↓
PDF devis
```

Puis, après acceptation :

```text
CommercialQuote
↓ accepted / authority gate
ContractedRevenue
```

Ainsi PRINT-02 reste une projection commerciale du domaine réel du chantier, et non un système économique parallèle.
