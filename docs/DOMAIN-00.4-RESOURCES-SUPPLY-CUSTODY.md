# DOMAIN-00.4 — Materials / Equipment / Supply / Custody

Status: **validated conceptually**

## 1. Objet

DOMAIN-00.4 définit la réalité physique des ressources mobilisées par un projet :

- ce dont un `WorkPackage` a besoin ;
- ce qui est spécifié techniquement ;
- ce qui est proposé puis éventuellement approuvé ;
- qui fournit la ressource ;
- ce qui est réellement livré et reçu ;
- ce qui est consommé, incorporé, rendu ou perdu ;
- quels équipements sont mis à disposition ;
- qui en a la garde à un instant donné ;
- comment enregistrer casse, perte, disparition ou vol signalé sans attribuer automatiquement une responsabilité.

Principe directeur :

> **`mdtc-domain` décrit le besoin, l'affectation et la réalité chantier des ressources sans devenir un ERP de stock, une plateforme e-commerce ou un moteur de location commerciale.**

Flux général :

```text
WorkPackage
   ↓
ResourceRequirement
   ↓
SupplyAssignment / EquipmentProvision
   ↓
Delivery / Handover
   ↓
Receipt / Custody
   ↓
Usage / Incorporation
   ↓
Return / Discrepancy / ResourceIncident
```

Toutes les étapes ne sont pas obligatoires.

---

## 2. `Material` ≠ `Equipment`

### Material

Ressource physique consommée ou incorporée pendant l'exécution.

Exemples :

```text
béton
parpaing
acier
mortier
visserie
mousse PU
joint
peinture
disque de coupe
```

Nature possible :

```text
INCORPORATED
CONSUMED
```

### Equipment

Moyen d'exécution durable, normalement non incorporé dans l'ouvrage.

Exemples :

```text
perforateur
marteau-piqueur
bétonnière
mini-pelle
échafaudage
laser
nacelle
camion
```

Invariant :

```text
Material ≠ Equipment
```

La classification dépend de l'usage réel : un coffrage perdu peut être traité comme matériau, un coffrage réutilisable comme équipement.

---

## 3. `MaterialSpecification` ≠ produit commercial

Le projet peut exprimer un besoin technique sans avoir sélectionné une référence fabricant.

```text
MaterialSpecification
├── material_spec_id
├── designation
├── technical_requirements[]
├── normative_refs[]
├── contractual_refs[]
├── acceptable_variants?
└── source_refs[]
```

Exemples :

```text
béton C25/30
isolant épaisseur 120 mm
vis inox A4
menuiserie répondant aux performances contractuelles
```

Une référence commerciale reste distincte :

```text
ProductRef
├── manufacturer?
├── product_reference
├── catalog_ref?
└── external_ref?
```

Invariant :

```text
MaterialSpecification ≠ CommercialProduct
```

Le catalogue détaillé peut vivre dans Odoo ou un autre système externe.

---

## 4. `MaterialRequirement`

> Un `MaterialRequirement` représente un besoin matériel identifié pour un scope de travail donné.

```text
MaterialRequirement
├── requirement_id
├── project_ref
├── scope_ref
├── material_spec_ref
├── quantity
├── unit
├── quantity_basis?
├── allowance?
├── required_by?
├── supply_assignment_ref?
└── source_refs[]
```

Le besoin théorique et la quantité effectivement approvisionnée restent distincts :

```text
net requirement
≠ procurement / provision quantity
```

Une marge de pertes ou de coupe peut être explicitée sans être assimilée à une perte anormale.

---

## 5. `EquipmentRequirement`

> Un `EquipmentRequirement` décrit la capacité d'équipement nécessaire à un scope, avant de désigner une machine précise.

```text
EquipmentRequirement
├── requirement_id
├── project_ref
├── scope_ref
├── capability
├── quantity
├── duration_requirement?
├── required_period?
├── constraints[]
└── source_refs[]
```

Exemple :

```text
capability = MINI_EXCAVATOR
```

plutôt que de figer immédiatement une machine ou un numéro de série.

Invariant :

```text
EquipmentRequirement ≠ EquipmentAssignment
```

---

## 6. `MaterialSelection` et substitutions

> `MaterialSelection` relie un besoin/spécification à un produit ou une solution proposée ou retenue.

```text
MaterialSelection
├── selection_id
├── material_requirement_ref
├── product_ref
├── proposed_by_actor_ref?
├── proposed_at
├── approval_required
├── approval_decision_ref?
├── substitution_of_ref?
└── source_refs[]
```

Invariants :

```text
product proposed ≠ product approved
similar product ≠ equivalent product
similar product ≠ approved substitution
equivalent candidate ≠ approved substitution
```

Nestor peut produire un `MaterialSelectionCandidate` ou `SubstitutionCandidate`, mais une substitution nécessitant validation technique ou contractuelle doit passer par l'autorité/gate approprié.

---

## 7. `SupplyAssignment` — qui fournit ?

> `SupplyAssignment` alloue explicitement la mise à disposition d'une ressource demandée.

```text
SupplyAssignment
├── supply_assignment_id
├── requirement_ref
├── supplying_actor_ref
├── provisioning_mode
├── due_condition?
├── contractual_basis_ref?
└── source_refs[]
```

Modes candidats :

```text
MDTC_SUPPLIED
CLIENT_SUPPLIED
SUBCONTRACTOR_SUPPLIED
SUPPLIER_DIRECT
OWNER_PROVIDED
OTHER
```

La source de fourniture ne permet aucune conclusion automatique sur paiement, pose ou responsabilité.

Invariant :

```text
supplied_by
≠ paid_by
≠ installed_by
≠ owned_by
≠ liable_for
```

En particulier :

```text
client-supplied ≠ client-responsible-for-defect
```

---

## 8. Besoin ressource ≠ besoin d'achat

```text
ResourceRequirement
≠ ProcurementNeed
```

Un besoin peut être satisfait par :

- une ressource déjà disponible ;
- un stock externe ;
- le client ;
- un sous-traitant ;
- une autre organisation ;
- un achat ;
- une location ;
- un transfert.

`ProcurementNeed` n'existe donc que lorsqu'une acquisition ou un sourcing doit réellement être déclenché.

```text
ProcurementNeed
├── procurement_need_id
├── project_ref
├── requirement_refs[]
├── procurement_kind
├── required_by?
├── supplier_actor_ref?
├── external_procurement_ref?
├── status
└── source_refs[]
```

Kinds candidats :

```text
PURCHASE
EXTERNAL_SUPPLY
TRANSFER
OTHER
```

Pour une location, le domaine peut conserver une référence externe et la réalité opérationnelle de l'équipement, mais ne porte pas le commerce de location lui-même.

---

## 9. Frontière ERP / e-commerce / location

Sont hors scope de `mdtc-domain` :

```text
catalogue loueur
réservation commerciale
contrat de location
caution
tarification
paiement
facture fournisseur
prolongation commerciale
workflow e-commerce
stock ledger / WMS complet
```

Ces éléments peuvent être gérés par Odoo, une plateforme e-commerce ou le système du fournisseur/loueur.

En revanche, une fois l'équipement présent dans le contexte opérationnel du chantier, MDTC doit pouvoir représenter :

```text
EquipmentProvision
EquipmentAssignment
EquipmentHandover
EquipmentReturn
ResourceIncident
```

Frontière adoptée :

```text
rental transaction
≠ project equipment custody
```

---

## 10. Livraison, réception et acceptation

Invariant :

```text
ORDERED
≠ DELIVERED
≠ RECEIVED
≠ INSPECTED
≠ ACCEPTED
≠ INCORPORATED
```

### `ResourceDelivery`

```text
ResourceDelivery
├── delivery_id
├── project_ref
├── supplier_actor_ref?
├── destination_ref
├── delivered_at
├── delivered_items[]
├── delivery_document_refs[]
├── evidence_refs[]
└── source_refs[]
```

### `DeliveredResourceItem`

```text
DeliveredResourceItem
├── requirement_ref?
├── product_ref?
├── quantity
├── unit
├── batch_ref?
├── serial_refs[]
└── traceability_refs[]
```

### `ResourceReceipt`

> Réception physique déclarée par un acteur du projet ; elle ne constitue pas automatiquement une conformité technique.

```text
ResourceReceipt
├── delivery_ref
├── received_by_actor_ref
├── received_at
├── quantity_check
├── visible_condition
├── reservation_notes?
└── evidence_refs[]
```

### `ResourceAcceptance`

```text
ResourceAcceptance
├── receipt_ref
├── decision_ref?
├── accepted_quantity?
├── rejected_quantity?
├── reason?
└── evidence_refs[]
```

---

## 11. Usage réel des matériaux

```text
MaterialUsageRecord
├── usage_id
├── project_ref
├── scope_ref
├── material_requirement_ref?
├── product_ref?
├── quantity
├── unit
├── usage_kind
├── recorded_at
├── recorded_by_actor_ref?
└── evidence_refs[]
```

Kinds :

```text
INCORPORATED
CONSUMED
WASTED
RETURNED
```

Cela permettra ultérieurement de comparer :

```text
estimated
provided / procured
incorporated
normal waste
unexpected waste
returned
missing
```

Invariant :

```text
WASTED
≠ abnormal waste
≠ negligence
≠ fraud
≠ responsibility
```

---

## 12. `EquipmentAssignment`

> L'affectation relie un besoin d'équipement à une ressource concrète utilisée pour le scope.

```text
EquipmentAssignment
├── assignment_id
├── equipment_requirement_ref
├── equipment_ref
├── provision_mode
├── provider_actor_ref?
├── external_rental_ref?
├── valid_from?
├── valid_until?
└── source_refs[]
```

Invariant :

```text
EquipmentAssignment ≠ EquipmentUsageTime
```

Le temps effectif d'utilisation relève de DOMAIN-00.5.

---

## 13. `EquipmentProvision` — mise à disposition

L'affectation à un besoin ne suffit pas à décrire la propriété, la mise à disposition ou la garde physique.

> `EquipmentProvision` décrit la mise à disposition opérationnelle d'un équipement dans le contexte d'un projet.

```text
EquipmentProvision
├── provision_id
├── equipment_ref
├── project_ref
├── scope_ref?
├── owner_actor_ref?
├── provider_actor_ref
├── recipient_actor_ref?
├── provision_kind
├── available_from?
├── available_until?
└── source_refs[]
```

Le modèle couvre notamment :

```text
MDTC → salarié
MDTC → sous-traitant
sous-traitant → son équipe
sous-traitant → MDTC
partenaire → chantier
équipement externe → projet
```

Invariant :

```text
owner ≠ provider ≠ custodian ≠ operator
```

Une même personne peut cumuler plusieurs de ces positions, mais elles ne doivent pas être déduites l'une de l'autre.

---

## 14. `EquipmentHandover` et garde physique

> `EquipmentHandover` enregistre une remise physique traçable d'un équipement à un acteur.

```text
EquipmentHandover
├── handover_id
├── equipment_ref
├── project_ref
├── scope_ref?
├── from_actor_ref?
├── to_actor_ref
├── handed_over_at
├── condition_at_handover?
├── accessories[]
├── evidence_refs[]
├── expected_return_at?
└── source_refs[]
```

Une remise permet d'établir un fait de garde sans attribuer par avance les conséquences de tout incident futur.

Invariant :

```text
equipment assigned
≠ equipment physically handed over
```

---

## 15. `EquipmentReturn`

```text
EquipmentReturn
├── return_id
├── handover_ref
├── returned_at
├── received_by_actor_ref
├── condition_at_return?
├── missing_accessories[]
├── evidence_refs[]
├── notes?
└── source_refs[]
```

Exemple :

```text
Perforateur
remis avec 3 burins
↓
retour avec 2 burins
```

Le retour enregistre la divergence observée ; il ne conclut pas automatiquement à une faute ou à un vol.

---

## 16. `ResourceIncident`

Les pertes, dommages, disparitions et anomalies de ressources ne doivent pas être réduits à `MaterialUsageRecord.WASTED`.

> `ResourceIncident` représente un événement ou une anomalie constatée concernant une ressource matérielle ou un équipement.

```text
ResourceIncident
├── incident_id
├── project_ref
├── scope_ref?
├── resource_ref?
├── equipment_ref?
├── material_ref?
├── incident_kind
├── observed_at
├── reported_by_actor_ref?
├── involved_actor_refs[]
├── quantity?
├── description
├── evidence_refs[]
├── verification_state
└── source_refs[]
```

Kinds candidats :

```text
MISSING
SHORTAGE
DAMAGE
BREAKAGE
UNEXPECTED_WASTE
UNAUTHORIZED_USE
LOSS
THEFT_REPORTED
OTHER
```

Le terme `THEFT_REPORTED` est intentionnel : une disparition peut être signalée comme vol sans que le domaine transforme cette déclaration en fait juridiquement établi.

Invariants :

```text
resource missing
≠ theft established
≠ perpetrator identified
≠ responsibility established
```

```text
reported theft ≠ verified theft
```

---

## 17. Négligence, casse et causalité

Exemple : des sacs de ciment sont retrouvés détériorés après exposition à la pluie.

Le domaine peut enregistrer :

```text
ResourceIncident
kind = DAMAGE / UNEXPECTED_WASTE
```

et une observation :

```text
Observation
"les sacs étaient stockés sans protection au moment du constat"
```

Il ne doit pas produire automatiquement :

```text
negligent_actor = X
```

La causalité et l'allocation de responsabilité seront traitées en DOMAIN-00.7.

Invariants :

```text
damage ≠ negligence
loss ≠ responsibility
unexpected waste ≠ fraud
variance ≠ fraud
```

---

## 18. Cas client-supplied

Exemple : les menuiseries sont fournies par le client mais posées par MDTC.

```text
MaterialRequirement
menuiseries

SupplyAssignment
supplying_actor = CLIENT
```

Puis :

```text
ResourceDelivery
↓
ResourceReceipt
↓
Observation / ResourceIncident éventuel
```

La fourniture client ne détermine pas automatiquement la cause ou la responsabilité d'un défaut ultérieur.

---

## 19. Cas équipement d'un sous-traitant

```text
EquipmentProvision
├── equipment = bétonnière B-12
├── owner = Sous-traitant X
├── provider = Sous-traitant X
└── scope = WorkPackage Gros œuvre
```

Si elle est ensuite remise à un autre acteur :

```text
EquipmentHandover
from = Sous-traitant X
to = Actor Y
```

Le domaine peut donc répondre distinctement à :

- qui possède l'équipement ?
- qui l'a mis à disposition ?
- à quel scope est-il affecté ?
- qui en a reçu physiquement la garde ?
- dans quel état a-t-il été remis ?
- dans quel état a-t-il été restitué ?

sans conclure automatiquement à la responsabilité d'un incident.

---

## 20. Stock et inventaire

`mdtc-domain` ne devient pas un WMS.

Références externes possibles :

```text
InventoryRef
AvailabilityRef
StorageLocationRef
```

Le domaine a besoin de l'allocation projet, pas nécessairement du ledger complet du stock.

Invariant :

```text
AVAILABLE
≠ RESERVED
≠ ALLOCATED
≠ USED
```

Une disponibilité globale dans l'ERP ne signifie pas que la ressource est disponible pour un projet donné.

---

## 21. Nestor et candidats ressources

Nestor peut proposer à partir du scope :

```text
MaterialRequirementCandidate
EquipmentRequirementCandidate
MaterialSelectionCandidate
SubstitutionCandidate
```

Mais :

```text
AI-estimated requirement ≠ validated requirement
LLM-selected product ≠ approved product
LLM substitution candidate ≠ approved substitution
```

Nestor doit conserver hypothèses, incertitudes et provenance des données utilisées pour calculer ou suggérer les besoins.

---

## 22. Noyau conceptuel adopté

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

Références externes typiques :

```text
ProductRef
EquipmentRef
InventoryRef
ExternalProcurementRef
ExternalRentalRef
```

---

## 23. Invariants adoptés

```text
Material ≠ Equipment
MaterialSpecification ≠ CommercialProduct
MaterialRequirement ≠ MaterialSelection
ResourceRequirement ≠ ProcurementNeed
EquipmentRequirement ≠ EquipmentAssignment
```

```text
supplied_by
≠ paid_by
≠ installed_by
≠ owned_by
≠ liable_for
```

```text
owner ≠ provider ≠ custodian ≠ operator
equipment assigned ≠ equipment physically handed over
```

```text
product proposed ≠ product approved
similar product ≠ equivalent product ≠ approved substitution
```

```text
ORDERED
≠ DELIVERED
≠ RECEIVED
≠ INSPECTED
≠ ACCEPTED
≠ INCORPORATED
```

```text
stock available ≠ stock reserved ≠ project allocated
```

```text
WASTED ≠ abnormal waste ≠ negligence ≠ responsibility
resource missing ≠ theft established
reported theft ≠ verified theft
damage ≠ negligence
loss ≠ responsibility
unexpected waste ≠ fraud
variance ≠ fraud
```

```text
rental transaction ≠ project equipment custody
AI resource candidate ≠ authoritative requirement
LLM substitution candidate ≠ approved substitution
```

---

## 24. Hors périmètre de DOMAIN-00.4

Sont différés ou externalisés :

- comptabilité fournisseur ;
- factures d'achat ;
- catalogue commercial exhaustif ;
- WMS / stock ledger complet ;
- réservation et tarification commerciale de location ;
- contrat/caution/paiement de location ;
- coût réel et marge — DOMAIN-00.6 ;
- planning détaillé et temps d'usage — DOMAIN-00.5 ;
- causalité, faute, vol établi et responsabilité — DOMAIN-00.7 ;
- garantie et effet juridique — DOMAIN-00.7.

La transaction commerciale peut vivre dans Odoo ou un système externe ; la réalité opérationnelle de la ressource sur chantier reste dans `mdtc-domain`.
