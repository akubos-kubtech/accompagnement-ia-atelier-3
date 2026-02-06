# Acteurs et rôles

> Vue consolidée des acteurs internes, externes et de la gouvernance projet, extraits du CDC et de l'analyse des 7 domaines.

## Gouvernance projet Phoenix

| Rôle | Personne / Entité | Responsabilité |
|------|-------------------|----------------|
| Directeur de Programme ERP | Jean-Philippe MARTIN | Contact référent AO, pilotage du programme |
| Comité de direction projet | DG + DAF + DSI + Dir. Supply Chain + Dir. Commerciale | Pilotage stratégique, vision transverse |
| Cabinet d'accompagnement | Deloitte Digital | Structuration AO, évaluation des réponses |
| Équipe projet | Représentants métiers + architectes SI + experts sécu + juristes IT | Analyse et évaluation des propositions |

## Directions métier

| Direction | Périmètre | Domaines ERP concernés |
|-----------|-----------|----------------------|
| Direction Générale | Vision stratégique, validation investissements | Transversal |
| Direction Financière (DAF) | Comptabilité, trésorerie, contrôle de gestion, budget | Finance & Comptabilité, Reporting |
| Direction Commerciale | Ventes, pricing, marketing, relation client | Gestion commerciale, CRM & Marketing |
| Direction Supply Chain | Logistique, transports, approvisionnements, entrepôts | Supply Chain, Stocks & Entrepôts, Achats |
| Direction des Systèmes d'Information (DSI) | Architecture SI, intégrations, sécurité, infrastructure | Transversal (technique) |
| Direction des Achats | Sourcing, négociation fournisseurs, qualité | Achats & Approvisionnement |

## Rôles opérationnels internes

| Rôle | Domaine(s) principal(aux) | Activités clés |
|------|--------------------------|----------------|
| Vendeur magasin | Gestion commerciale, CRM | Vente, conseil client, consultation stock, fidélité |
| Directeur de magasin | Gestion commerciale, Stocks | Pilotage PdV, reporting, gestion stock local |
| Commercial B2B | Gestion commerciale | Devis, commandes spéciales, suivi clients pro |
| Responsable pricing | Gestion commerciale | Tarification, promotions, soldes |
| Acheteur | Achats & Approvisionnement | Sourcing, négociation, référencement fournisseurs |
| Approvisionneur | Achats, Supply Chain | Réapprovisionnement, suivi commandes fournisseurs |
| Réceptionnaire | Achats, Stocks | Contrôle réception, gestion écarts |
| Planificateur / Demand planner | Supply Chain | Prévisions de vente, planification approvisionnements |
| Responsable transport | Supply Chain | Optimisation tournées, suivi expéditions |
| Logisticien / Magasinier | Stocks & Entrepôts | Mouvements de stock, inventaires, préparation |
| Comptable (général, auxiliaire, fiscal) | Finance & Comptabilité | Écritures, rapprochements, déclarations TVA |
| Contrôleur de gestion | Finance, Reporting | Analyse rentabilité, budgets, suivi écarts |
| Trésorier | Finance & Comptabilité | Positions de trésorerie, rapprochements bancaires, change |
| Responsable marketing | CRM & Marketing | Campagnes, segmentation, programme fidélité |
| Conseiller centre de contact | CRM & Marketing | Réclamations, vision 360° client, tickets |
| Responsable données / DPO | CRM & Marketing | Qualité référentiel, RGPD, consentements |
| Analyste BI / Utilisateur avancé | Reporting & BI | Analyses ad hoc, dashboards, self-service |
| Key user métier | Transversal | Paramétrage ERP, référent fonctionnel, formation |

## Acteurs externes

| Acteur | Volumétrie | Interactions avec l'ERP |
|--------|-----------|----------------------|
| Clients B2C | 8,5 millions actifs | Commandes (web/magasin), retours, fidélité, réclamations |
| Clients B2B / professionnels | Sous-ensemble des 8,5M | Devis, commandes spéciales, grilles tarifaires dédiées |
| Fournisseurs | 850+ dans 35 pays | Commandes, réceptions, litiges, évaluation, EDI |
| Transporteurs | Multiples prestataires | Expéditions, tracking temps réel, coûts |
| Banques | Multi-banques, multi-devises | Règlements, relevés, rapprochement (EBICS/SWIFT) |
| Douanes | Multi-pays (import/export) | Documentation douanière, droits et taxes |
| Plateformes fiscales | Par pays (ex: Chorus Pro FR) | Facturation électronique, e-reporting |
| Auditeurs / Commissaires aux comptes | Annuel | Accès aux données financières, traçabilité |

## Équipe IT et intégration

| Rôle | Effectif | Responsabilité |
|------|----------|----------------|
| Équipes digitales et IT | 700 personnes | Développement, intégration, support, infrastructure |
| Équipe intégration (ESB) | Sous-ensemble IT | 200+ flux MuleSoft, interfaces inter-applicatives |
| Architectes SI | Sous-ensemble IT | Architecture cible, API, connecteurs |

## Matrice acteurs × domaines

| Rôle | GC | AA | SC | FC | SE | CM | RB |
|------|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| Vendeur magasin | x | | | | | x | |
| Commercial B2B | x | | | | | | |
| Responsable pricing | x | | | | | | |
| Acheteur | | x | | | | | |
| Approvisionneur | | x | x | | | | |
| Réceptionnaire | | x | | | x | | |
| Planificateur | | | x | | | | |
| Resp. transport | | | x | | | | |
| Logisticien | | | x | | x | | |
| Comptable | | | | x | | | |
| Contrôleur de gestion | | | | x | | | x |
| Trésorier | | | | x | | | |
| Resp. marketing | | | | | | x | |
| Conseiller contact | | | | | | x | |
| DPO | | | | | | x | |
| Analyste BI | | | | | | | x |
| Key user métier | x | x | x | x | x | x | x |
| Direction | x | x | x | x | x | x | x |

> Légende : GC = Gestion commerciale, AA = Achats, SC = Supply Chain, FC = Finance, SE = Stocks, CM = CRM, RB = Reporting
