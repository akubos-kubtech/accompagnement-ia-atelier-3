# Cartographie consolidée de l'intégration ERP Retail

> **Source** : CDC AO-2026-ERP-001 — analyses domaines (7), réconciliation entités (16), cartographie flux (46), frictions (30), matrice dépendances (7×7).
>
> **Audiences** : Tech Lead (TL), Business Analyst (BA), Chef de Projet (CP)

---

## Chiffres clés

| Indicateur | Valeur | Lecture |
|-----------|:------:|---------|
| Modules fonctionnels | 7 | GC, AA, SC, FC, SE, CM, RB |
| Flux inter-modules | 46 | 31 documentés, 10 implicites, **5 absents** |
| Flux HAUTE criticité | 31 | Dont 3 absents et 5 implicites |
| Entités partagées | 16 | 3 cohérentes, 12 partielles, **1 incohérente** |
| Frictions identifiées | 30 | **9 bloquantes**, 16 majeures, 5 mineures |
| Dépendances inter-modules | 26 | 2 structurelles, 24 bouchonnables |
| Phases d'implémentation | 5 | Chemin critique incompressible |

---

## 1. Vue macro

### 1.1 Modules et flux principaux

```
                            ┌─────────────────────────────────────────────────┐
                            │              SYSTÈMES EXTERNES                  │
                            │  Cegid POS · Salesforce (CC/CRM/MC) · Akeneo   │
                            │  Manhattan WMS · Snowflake · MuleSoft ESB       │
                            └──────────┬──────────────────────┬───────────────┘
                                       │                      │
         ┌─────────────────────────────┼──────────────────────┼──────────────────┐
         │                             │   ERP RETAIL          │                  │
         │                             ▼                      ▼                  │
         │  ┌──────────┐  réf.client  ┌──────────┐  réf.article  ┌──────────┐   │
         │  │    CM    │─────────────►│    GC    │◄──────────────│    AA    │   │
         │  │ CRM &    │  fidélité    │ Gestion  │  prix revient │ Achats & │   │
         │  │Marketing │◄─────────────│commerci. │               │ Appro.   │   │
         │  └────┬─────┘              └────┬─────┘               └────┬─────┘   │
         │       │                    ▲    │    ▲                      │         │
         │       │               stock│    │    │tracking              │         │
         │       │                    │    ▼    │                      │         │
         │       │              ┌─────┴────────┴─────┐                │         │
         │       │              │        SE          │◄───────────────┘         │
         │       │              │   Stocks & WMS     │   réception              │
         │       │              └──────┬────┬────────┘                          │
         │       │              picking│    │stock MRP                           │
         │       │                     ▼    ▼                                    │
         │       │              ┌──────────────┐                                │
         │       │              │     SC       │                                │
         │       │              │ Supply Chain │                                │
         │       │              └──────┬───────┘                                │
         │       │                     │                                        │
         │       │     écritures       │  expéditions                           │
         │       │     ┌───────────────┘                                        │
         │       │     ▼                                                        │
         │       │  ┌──────────┐                                                │
         │       │  │    FC    │                                                │
         │       └─►│ Finance &│◄── three-way (AA) + valorisation (SE)          │
         │   client │ Compta.  │                                                │
         │          └────┬─────┘                                                │
         │               │                                                      │
         │               ▼                                                      │
         │          ┌──────────┐                                                │
         │          │    RB    │◄── données de TOUS les modules                 │
         │          │Reporting │                                                │
         │          │   & BI   │                                                │
         │          └──────────┘                                                │
         └──────────────────────────────────────────────────────────────────────┘
```

### 1.2 Profil de chaque module

| Module | Rôle dans le système | Dépendent de moi | Je dépends de | Profil |
|:------:|---------------------|:---:|:---:|---------|
| **SE** | Fournit la donnée stock (ATP) — la plus temps-sensible du SI | 5 | 4 | **FONDATION DONNÉES** |
| **GC** | Produit les événements métier (vente, commande, retour) | 5 | 4 | **FONDATION ÉVÉNEMENTS** |
| **FC** | Consolide les flux financiers de tous les domaines | 4 | 4 | **INTÉGRATEUR** |
| **AA** | Gère le cycle d'achat et alimente FC (three-way) | 4 | 3 | **FONDATION SECONDAIRE** |
| **SC** | Orchestre les flux physiques (MRP, expédition, transport) | 3 | 4 | **ORCHESTRATEUR** |
| **CM** | Référentiel client maître, fidélité | 3 | 1 | **FONDATION AUTONOME** |
| **RB** | Consomme les données de tous les domaines | 1 | 6 | **CONSOMMATEUR** |

---

## 2. Entités partagées

> **Lecture BA** : les entités NON ou PARTIEL nécessitent un arbitrage client avant implémentation.
> **Lecture TL** : les entités Haute sévérité impactent le modèle de données canonical.

| # | Entité | Modules | Cohérent ? | Sévérité | Problème principal |
|:-:|--------|---------|:----------:|:--------:|-------------------|
| 1 | Article / Article stock | GC, AA, SC, SE, FC, RB | PARTIEL | **Haute** | 4 notions de prix sans hiérarchie, PIM Akeneo absent du modèle |
| 2 | Client / Compte tiers | GC, CM, FC, RB | PARTIEL | **Haute** | 3 modélisations (CM réf., GC indépendant, FC compte tiers), processus création flou |
| 3 | Fournisseur | AA, FC, SC, SE | PARTIEL | Moyenne | Évaluation (AA) non partagée avec FC/SC |
| 4 | Commande client | GC, SC, SE, FC, CM | OUI | — | — |
| 5 | Commande fournisseur | AA, SC, FC | PARTIEL | Moyenne | Frontière floue Proposition (SC) vs Engagement (AA) |
| 6 | Stock | SE, GC, SC, FC | PARTIEL | **Haute** | 4 définitions de « disponible » (ATP, physique, MRP, valorisable) |
| 7 | Mouvement de stock | SE, GC, AA, FC | OUI | — | — |
| 8 | Réception | AA, SE, FC | PARTIEL | Moyenne | Réception = contrôle (AA) ou mise en stock (SE) ? |
| 9 | Entrepôt / Site / Magasin | SE, AA, SC, GC, FC | **NON** | **Haute** | 3 noms, 3 concepts, aucun référentiel — 347 magasins orphelins |
| 10 | Retour | GC, SE | OUI | Basse | Cohérent (GC maître, SE consommateur) |
| 11 | Promotion / Tarif | GC, CM | PARTIEL | Moyenne | ERP (RG-GC-04) vs Salesforce MC (RG-CM-09), cohérence non assurée |
| 12 | Expédition | SC, SE | PARTIEL | Moyenne | Expédition (SC logique) vs Préparation (SE physique) mal articulées |
| 13 | Écriture comptable | FC, SE | OUI | — | — |
| 14 | Facture | FC, AA, GC | PARTIEL | **Haute** | FC possède, GC ne l'alimente pas — Factur-X non couvert |
| 15 | Indicateur / KPI | SC, FC, RB | PARTIEL | Moyenne | Pas de catalogue KPI unifié |
| 16 | Prévision de vente | SC, RB | PARTIEL | Moyenne | SC calcule (MRP), RB calcule (IA) — double moteur |

**Synthèse** : 3 cohérentes (OUI), 12 partielles (PARTIEL), **1 incohérente (NON)**. 5 entités à sévérité Haute nécessitent un arbitrage client avant conception.

---

## 3. Flux critiques (HAUTE criticité)

> **31 flux HAUTE** sur 46 total. Classés par statut de documentation — les absents et implicites sont les plus risqués.

### 3.1 Flux ABSENTS (non documentés mais nécessaires)

| ID | Source → Dest | Données | Pourquoi critique |
|---|---|---|---|
| **FLUX-A09** | GC → SE | Libération stock réservé | Annulation commande = stock bloqué indéfiniment sans ce flux |
| **FLUX-D06** | FC → GC | Encours client (solde, limite) | Sans contrôle encours, commandes en dépassement acceptées |

### 3.2 Flux IMPLICITES (déduits par croisement, non explicités dans les SF)

| ID | Source → Dest | Données | Source de déduction |
|---|---|---|---|
| **FLUX-C07** | SC → GC | Date livraison estimée | GC spec P1 étape 5 : « suivi état en temps réel » |
| **FLUX-E05** | GC → CM, RB | Tarifs et promotions | RG-CM-08 : « cohérence offres promo et conditions tarifaires » |
| **FLUX-E06** | GC → CM | Historique commandes/retours | CM entités : « Historique d'achat pour fidélité et segmentation » |
| **FLUX-F01** | CM → GC | Palier fidélité et avantages actifs | Flux Client→Fidélité→Tarification (referentiels-transverses) |

### 3.3 Flux DOCUMENTÉS — haute criticité (23 flux)

| ID | Source → Dest | Données | Déclencheur |
|---|---|---|---|
| FLUX-A01 | GC → SE | Réservation stock (article, qté, site) | Validation commande |
| FLUX-A02 | SE → GC | Stock disponible (ATP par article/site) | Consultation / modif. stock |
| FLUX-A03 | GC → SC | Commande validée (lignes, mode livraison) | Validation commande |
| FLUX-A04 | SC → GC | Statut expédition, tracking, date livraison | Changement statut |
| FLUX-A05 | GC → FC | Données vente → écriture comptable | Encaissement |
| FLUX-A06 | GC → CM | Transaction achat → cumul fidélité | Encaissement |
| FLUX-B01 | AA → SE | Réception validée → entrée stock | Validation contrôle |
| FLUX-B02 | AA → FC | Commande fournisseur (three-way) | Émission commande |
| FLUX-B03 | AA → FC | Réception (qté, écarts) → three-way | Validation réception |
| FLUX-B04 | AA → FC | Prix de revient complet | Calcul prix de revient |
| FLUX-B05 | SE → AA | Franchissement seuil réappro | Stock < seuil |
| FLUX-B07 | AA → FC | Facture fournisseur → three-way | Réception facture |
| FLUX-C01 | SE → SC | Niveaux stock pour MRP | Avant calcul MRP |
| FLUX-C02 | SC → AA | Propositions commandes MRP | Exécution MRP |
| FLUX-C03 | SC → SE | Ordre expédition → picking | Planification expédition |
| FLUX-C04 | SE → SC | Confirmation expédition (qté, date) | Fin préparation |
| FLUX-D01 | SE → FC | Écritures variation stock | Mouvement de stock |
| FLUX-D02 | SE → FC | Écritures régularisation inventaire | Validation écarts |
| FLUX-D03 | SE → FC | Valorisation stock fin de période | Clôture mensuelle |
| FLUX-E01 | CM → GC, FC, RB | Référentiel client maître | Création/modif. client |
| FLUX-E02 | GC → tous | Référentiel article | Création/modif. article |
| FLUX-E03 | AA → SC, FC, SE, RB | Référentiel fournisseur | Création/modif. fournisseur |
| FLUX-F04 | GC → CM | Points fidélité à cumuler | Encaissement |
| FLUX-F05 | GC → CM | Utilisation points comme paiement | Paiement caisse/web |
| FLUX-G01 | Tous → RB | Données transactionnelles | Continu / programmé |

---

## 4. Frictions majeures

> **Lecture CP** : les BLOQUANT doivent être résolues avant la phase concernée — elles sont sur le chemin critique.
> **Lecture BA** : chaque friction = une question d'arbitrage à instruire avec le client.
> **Lecture TL** : les frictions de type 3 et 6 impactent l'architecture technique (patterns d'intégration).

### 4.1 Frictions BLOQUANTES (9)

| ID | Type | Modules | Description | Phase à résoudre |
|---|---|---|---|:---:|
| FRIC-103 | Incohérence définition | GC, SE, SC, FC | **Stock « disponible »** : 4 définitions concurrentes (ATP, physique, MRP-disponible, valorisable). En-transit non modélisé. | 1 |
| FRIC-201 | Flux orphelin | GC, SE | **Vente POS → stock** : aucun flux POS→SE dans la cartographie. 347 magasins, majorité du CA, ignorés. | 2 |
| FRIC-206 | Flux orphelin | GC, FC | **Facture Factur-X** : FC possède l'entité, GC ne l'alimente pas. FLUX-A05 transporte des écritures comptables, pas une facture. Obligation légale non couverte. | 3 |
| FRIC-301 | Hypothèse contradictoire | GC, CM, SE | **Transaction POS** : fidélité synchrone (< 2s) ET vente asynchrone (< 5 min). Deux patterns d'intégration incompatibles dans la même transaction caisse. | 2 |
| FRIC-302 | Hypothèse contradictoire | GC, SE | **Stock web** : promesse < 2s, WMS réalité < 1 min, batch 5-15 min. Fenêtre d'incohérence → sur-vente en pic (Black Friday x3). | 2 |
| FRIC-401 | RG en conflit | GC, CM | **Cascade tarifaire** : RG-GC-03 décrit la cascade mais n'inclut pas les avantages fidélité CM. Pas d'intégration CM→GC dans le moteur de prix. | 2 |
| FRIC-501 | Référentiel non défini | SE, SC, GC, FC, AA | **Magasin** : utilisé par 5 domaines, défini par aucun. 347 magasins sans référentiel. | 0 |
| FRIC-601 | Séquencement impossible | GC, CM | **Paiement par points** : dépendance circulaire GC↔CM en 3 aller-retours synchrones < 2s. Pas de mécanisme de compensation (rollback). | 2 |
| FRIC-602 | Séquencement impossible | AA, SE, SC | **Cross-docking** : 3 domaines synchrones sans orchestrateur. SC doit planifier AVANT réception AA. Aucun modèle d'orchestration défini. | 3 |

### 4.2 Frictions MAJEURES (16)

| ID | Type | Modules | Description |
|---|---|---|---|
| FRIC-101 | Incohérence définition | GC, SE | **Article** : 2 entités (Article GC / Article stock SE), attributs disjoints, 4 notions prix sans hiérarchie, PIM Akeneo absent |
| FRIC-102 | Incohérence définition | GC, CM, FC | **Client** : 3 modélisations (CM réf., GC indépendant, FC compte tiers), processus création flou |
| FRIC-104 | Incohérence définition | SE, AA, SC, GC, FC | **Entrepôt/Site/Magasin** : 3 noms pour concepts qui se chevauchent, plateforme régionale non modélisée |
| FRIC-105 | Incohérence définition | SC, AA, FC | **Commande fournisseur** : frontière floue Proposition (SC) vs Engagement (AA), attributs manquants (devise, incoterm) |
| FRIC-202 | Flux orphelin | GC, SE | **Annulation → stock** : FLUX-A09 absent, stock réservé reste bloqué indéfiniment |
| FRIC-204 | Flux orphelin | GC, FC | **Encours client** : FC calcule, GC ne consomme pas (FLUX-D06 absent), commandes en surencours acceptées |
| FRIC-205 | Flux orphelin | CM, FC | **Client → Compte tiers** : flux alimentation absent (FLUX-F03), création manuelle comptes tiers |
| FRIC-303 | Hypothèse contradictoire | AA, SC | **Réappro** : seuil temps réel (AA) vs MRP batch quotidien (SC), double déclenchement possible |
| FRIC-304 | Hypothèse contradictoire | GC, CM | **Mode dégradé** : 48h autonome mais fidélité muette, perte de points |
| FRIC-402 | RG en conflit | AA, SC | **Seuil vs MRP** : deux mécanismes couvrent le même besoin sans arbitrage, double commande fournisseur possible |
| FRIC-403 | RG en conflit | SE, FC | **Valorisation** : SE exécute, FC connaît les normes — frontière responsabilité non tracée |
| FRIC-404 | RG en conflit | GC, CM | **Promotions** : ERP centralise (RG-GC-04) vs Salesforce MC (RG-CM-09), cohérence non assurée |
| FRIC-502 | Référentiel non défini | GC, SE, CM, FC | **Transaction POS** : modèle absent, scénario le plus fréquent (347 magasins) sans entité ni flux |
| FRIC-503 | Référentiel non défini | AA, SC, FC, GC | **Pays** : conditionne normes comptables, TVA, devise, nomenclature douanière — 15 pays, aucun référentiel |
| FRIC-603 | Séquencement impossible | AA, FC, SE | **Three-way matching** : timing des 3 documents non garanti, aucune tolérance d'écart définie |
| FRIC-604 | Séquencement impossible | GC, SE, SC | **Réservation vs picking** : fenêtre de divergence sans processus de dé-réservation documenté |

### 4.3 Matrice d'exposition par domaine

| Domaine | BLOQUANT | MAJEUR | Total | Couples les plus en friction |
|---------|:--------:|:------:|:-----:|---|
| **GC** | 5 | 9 | 14 | GC↔SE (7), GC↔CM (7) |
| **SE** | 4 | 7 | 11 | SE↔GC (7), SE↔FC (3) |
| **CM** | 3 | 4 | 7 | CM↔GC (7) |
| **FC** | 2 | 6 | 8 | FC↔GC (3), FC↔AA (3) |
| **AA** | 1 | 5 | 6 | AA↔SC (3), AA↔FC (3) |
| **SC** | 2 | 5 | 7 | SC↔SE (3), SC↔AA (3) |
| **RB** | 0 | 0 | 0 | — |

---

## 5. Matrice de dépendances

### 5.1 Matrice 7×7

> Lecture : la ligne **dépend de** la colonne.

| Dépend de → | GC | AA | SC | FC | SE | CM | RB | **Total** |
|:-----------:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:---------:|
| **GC** | — | | X | X | X | X | | **4** |
| **AA** | | — | X | X | X | | | **3** |
| **SC** | X | X | — | | X | | X | **4** |
| **FC** | X | X | | — | X | X | | **4** |
| **SE** | X | X | X | X | — | | | **4** |
| **CM** | X | | | | | — | | **1** |
| **RB** | X | X | X | X | X | X | — | **6** |
| **Dépendent de moi** | **5** | **4** | **3** | **4** | **5** | **3** | **1** | |

### 5.2 Classification structurelle vs transactionnelle

Sur 26 dépendances : **2 structurelles** (non bouchonnables), **24 transactionnelles** (bouchonnables).

| Dépendance | Type | Justification |
|-----------|:----:|--------------|
| **GC → CM** | **S** | Pas de commande client sans fiche client maître |
| **SC → SE** | **S** | MRP impossible sans données de stock |
| Les 24 autres | T | Fonctionnement en mode dégradé possible avec bouchon |

Les 2 dépendances structurelles convergent sur **GC** (Phase 2) — nœud critique du projet.

---

## 6. Ordre d'implémentation

### 6.1 Séquencement en 5 phases

```
Phase 0 ─── Référentiels (Article, Client, Fournisseur, Site, Pays, Devise)
               │
Phase 1 ──┬── SE (Stocks & Entrepôts)       ← fondation données
           └── CM (CRM & Marketing)          ← fondation référentiel
               │
Phase 2 ──┬── GC (Gestion commerciale)      ← pivot événementiel
           └── AA (Achats & Appro)           ← cycle d'achat
               │
Phase 3 ──┬── FC (Finance & Comptabilité)   ← intégrateur comptable
           └── SC (Supply Chain)             ← orchestrateur logistique
               │
Phase 4 ───── RB (Reporting & BI)           ← consommateur final (incrémental)
```

### 6.2 Détail par phase

| Phase | Modules | Prérequis | Justification | Bouchons | Risque principal |
|:-----:|---------|-----------|--------------|----------|-----------------|
| **0** | Référentiels transverses | Aucun | Toutes les fondations en dépendent. Résout FRIC-501/503/504. | — | Arbitrage Article (ERP vs PIM), Magasin (3 concepts) |
| **1** | **SE** + **CM** (parallèles) | Phase 0 | SE = stock ATP pour 5 modules. CM = client maître (dep. structurelle de GC). Mutuellement indépendants. | SE : réservations, réceptions, picking. CM : historique achats. | FRIC-103 (stock 4 définitions), intégration Manhattan + Salesforce CRM |
| **2** | **GC** + **AA** (parallèles) | Phase 1 | GC : deps structurelles satisfaites (CM+SE). Producteur d'événements pour 5 modules. AA : ne dépend que des réf. + SE. Mutuellement indépendants. | GC : tracking, encours. AA : MRP, règlement. | FRIC-301 (POS sync/async), FRIC-601 (fidélité circulaire), intégration Cegid POS |
| **3** | **FC** + **SC** (parallèles) | Phase 2 | FC : toutes deps satisfaites (GC+AA+SE+CM). SC : idem (GC+AA+SE). Mutuellement indépendants. | SC : prévisions IA (RB). Tous les autres bouchons retirés. | FRIC-206 (Factur-X), FRIC-602 (cross-docking), three-way multi-devises (8 devises, 15 pays) |
| **4** | **RB** | Phase 3 | Dépend de 6/6 modules. Consommateur pur. Livraison incrémentale possible dès Phase 2. | — | Qualité données (premier endroit où les incohérences apparaissent) |

### 6.3 Chemin critique

Deux chemins de **longueur 5 (incompressible)** convergent sur GC :

```
     CM ──(S)──► GC ──► FC ──► RB        Chaîne référentiel client
                  ▲
     SE ──(S)──► (GC) ──► SC ──► RB      Chaîne stock / supply chain
```

**(S)** = dépendance structurelle (non bouchonnable)

**GC est le nœud critique** : tout retard sur GC décale mécaniquement les Phases 3 et 4.

### 6.4 Frictions bloquantes à résoudre par phase

| Phase | Frictions | Arbitrage client requis |
|:-----:|-----------|------------------------|
| 0 | FRIC-501 (Magasin), FRIC-503 (Pays), FRIC-101 (Article) | Qui est maître pour Article ? Quelle granularité Site/Magasin ? |
| 1 | FRIC-103 (stock « disponible ») | Quelle définition par contexte (ATP, réservé, physique, transit) ? |
| 2 | FRIC-301 (POS sync+async), FRIC-601 (fidélité circulaire), FRIC-201 (POS→SE), FRIC-302 (stock web latence), FRIC-401 (cascade tarifaire) | Le POS interroge-t-il le stock central ou local ? La fidélité est-elle synchrone ou asynchrone ? |
| 3 | FRIC-206 (Factur-X), FRIC-602 (cross-docking) | Qui génère la facture ? Qui orchestre le cross-docking ? |

### 6.5 Livraison incrémentale de RB

| Dès | Dashboards disponibles |
|:---:|---|
| Phase 1 | Stock temps réel (SE), base clients (CM) |
| Phase 2 | Ventes et CA (GC), suivi achats (AA) |
| Phase 3 | Reporting financier (FC), KPI supply chain (SC) |
| Phase 4 | Reporting complet + prévisions IA |

---

## Annexe : Références croisées

| Document source | Contenu | Localisation |
|----------------|---------|-------------|
| Réconciliation des entités | 16 entités, écarts détaillés, 8 questions d'arbitrage | `exploration/reconciliation-entites.md` |
| Cartographie des flux | 46 flux, chronologies, dépendances cachées | `exploration/flux-inter-modules.md` |
| Zones de friction | 30 frictions, matrice domaines × frictions | `exploration/zones-friction.md` |
| Matrice de dépendances | 7×7, classification, séquencement détaillé | `exploration/matrice-dependances.md` |
| Spécifications par domaine | 7 × 3 fichiers (specs, entités, règles) | `domaines/*/` |
| Exigences transverses | Non-fonctionnelles, techniques, sécurité | `exigences-transverses/` |
| Contexte général | Glossaire, synthèse, acteurs, volumétries, intégrations | `00-contexte/` |
