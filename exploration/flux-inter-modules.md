# Etape 1 : Cartographie des flux inter-modules

> Inventaire systématique des flux de données entre les 7 domaines fonctionnels, extrait des spécifications, entités et règles de gestion (Phase 3).

## Synthèse quantitative

| Catégorie | Nombre de flux | Documentés | Implicites | Absents |
|-----------|:-:|:-:|:-:|:-:|
| A — Cycle de vente | 9 | 6 | 2 | 1 |
| B — Cycle d'achat | 8 | 6 | 1 | 1 |
| C — Supply chain & logistique | 7 | 5 | 2 | 0 |
| D — Comptabilité & finance | 6 | 4 | 1 | 1 |
| E — Synchronisation référentiels | 6 | 4 | 2 | 0 |
| F — CRM & fidélité | 5 | 2 | 2 | 1 |
| G — Reporting & BI | 5 | 4 | 0 | 1 |
| **Total** | **46** | **31** | **10** | **5** |

---

## Diagramme des flux principaux

```
                                    ┌─────────────────┐
                                    │   REPORTING &    │
                                    │       BI (RB)    │
                                    └───────┬─────────┘
                                  prévisions│IA ▲ données tous domaines
                                            │   │
            ┌───────────────────────────────┼───┼──────────────────────────┐
            │                               │   │                          │
            ▼                               ▼   │                          │
  ┌─────────────────┐  propositions  ┌──────────────────┐  commande   ┌────────────────┐
  │   ACHATS &      │◄──────────────│   SUPPLY CHAIN   │  client     │    GESTION      │
  │   APPRO (AA)    │  commande     │   & LOGISTIQUE   │◄────────────│  COMMERCIALE    │
  │                 │               │      (SC)        │  expéditions│    (GC)         │
  └──────┬──────────┘               └──────┬───────────┘  ──────────►│                 │
         │                                 │                          └──────┬──────────┘
         │ réception                       │ ordres                         │
         │ validée                         │ expédition/                    │ vente
         │                                 │ réappro                        │ retour
         │                                 │                                │ avoir
         ▼                                 ▼                                ▼
  ┌────────────────────────────────────────────────────┐          ┌─────────────────┐
  │              STOCKS & ENTREPÔTS (SE)               │          │   FINANCE &     │
  │                                                    │ valoris. │ COMPTABILITÉ    │
  │  stock disponible ──► GC (ATP)                     │─────────►│    (FC)         │
  │  niveaux stock ──► SC (MRP)                        │ écritures│                 │
  │                                                    │          │                 │
  └────────────────────────────────────────────────────┘          └─────────────────┘
                                                                          ▲
         ┌─────────────────┐                                              │
         │   CRM &         │  client maître ──────────► GC ───────────────┘
         │   MARKETING     │  palier fidélité ────────► GC (cascade tarif)
         │   (CM)          │  données client ─────────► FC (compte tiers)
         │                 │◄── historique achats ───── GC
         └─────────────────┘
```

### Flux comptables automatiques (détail)

```
GC ─── vente encaissée ────────────────────────────────────────► FC (écriture vente)
GC ─── avoir émis (retour) ────────────────────────────────────► FC (écriture avoir)
AA ─── commande fournisseur ────┐
AA ─── réception ───────────────┼──► FC (three-way matching) ──► FC (écriture achat)
AA ─── facture fournisseur ─────┘
SE ─── variation stock ────────────────────────────────────────► FC (écriture variation)
SE ─── régularisation inventaire ──────────────────────────────► FC (écriture inventaire)
SE ─── valorisation fin de mois ───────────────────────────────► FC (écriture clôture)
```

---

## A — Flux du cycle de vente

| ID | Source | Destination | Donnée échangée | Déclencheur | Fréquence | Documenté ? | Criticité |
|----|--------|------------|-----------------|-------------|-----------|:-----------:|:---------:|
| FLUX-A01 | GC | SE | Réservation de stock (article, qté, site) | Validation commande client | Temps réel | OUI — GC spec P1 étape 2 | **HAUTE** |
| FLUX-A02 | SE | GC | Stock disponible (ATP par article/site) | Consultation disponibilité ou modification stock | Temps réel | OUI — GC spec P1 étape 2, RG-SE-01 | **HAUTE** |
| FLUX-A03 | GC | SC | Commande client validée (N° commande, lignes, mode livraison, adresse) | Validation commande client | Temps réel | OUI — SC entités : « Commande client → déclenchement des expéditions » | **HAUTE** |
| FLUX-A04 | SC | GC | Statut expédition, tracking transporteur, date livraison estimée | Changement statut expédition | Temps réel | OUI — GC spec P1 étapes 5-6 : « suivi état commande, notification proactive » | **HAUTE** |
| FLUX-A05 | GC | FC | Données de vente pour écriture comptable (montant, TVA, compte, pièce) | Encaissement / confirmation vente | Temps réel | OUI — RG-FC-04 : « écritures générées auto depuis flux opérationnels » | **HAUTE** |
| FLUX-A06 | GC | CM | Transaction d'achat (montant, articles, canal) pour cumul fidélité | Encaissement | Temps réel | OUI — CM spec P2, RG-CM-04 : « cumul auto points à chaque achat » | **HAUTE** |
| FLUX-A07 | GC | SE | Retour validé → mouvement stock (réintégration ou orientation déstockage) | Validation retour | Événementiel | OUI — RG-GC-08, GC spec P3 étape 4 | MOYENNE |
| FLUX-A08 | GC | FC | Avoir émis → écriture comptable (montant, mode remboursement) | Génération avoir suite retour | Événementiel | IMPLICITE — déduit de RG-FC-04 (flux opérationnels → écritures) | MOYENNE |
| FLUX-A09 | GC | SE | Libération de stock réservé | Annulation commande client | Temps réel | ABSENT — non documenté mais logiquement nécessaire | **HAUTE** |

---

## B — Flux du cycle d'achat

| ID | Source | Destination | Donnée échangée | Déclencheur | Fréquence | Documenté ? | Criticité |
|----|--------|------------|-----------------|-------------|-----------|:-----------:|:---------:|
| FLUX-B01 | AA | SE | Réception validée → entrée en stock (article, qté, lot, emplacement) | Validation contrôle réception | Temps réel | OUI — RG-AA-10 : « réception déclenche auto mise en stock via WMS » | **HAUTE** |
| FLUX-B02 | AA | FC | Commande fournisseur (pour rapprochement three-way) | Émission commande fournisseur | Événementiel | OUI — RG-FC-05, FC spec P3 | **HAUTE** |
| FLUX-B03 | AA | FC | Réception (qté reçues, écarts) pour rapprochement three-way | Validation réception | Événementiel | OUI — RG-FC-05, FC spec P3 | **HAUTE** |
| FLUX-B04 | AA | FC | Prix de revient complet (prix achat + transport + douane + taxes) | Calcul prix de revient | Événementiel | OUI — AA entités : « Prix de revient → FC, alimentation calculs de marge » | **HAUTE** |
| FLUX-B05 | SE | AA | Franchissement seuil de réapprovisionnement (article, site, stock actuel) | Stock passe sous le seuil | Temps réel | OUI — RG-AA-03 : « déclenchement auto au franchissement du seuil » | **HAUTE** |
| FLUX-B06 | AA | SE | Avis de réception attendue (commande, articles, qté, date prévue) | Confirmation commande par le fournisseur | Événementiel | IMPLICITE — déduit de integrations-cibles (ERP → WMS : « Réceptions attendues ») | MOYENNE |
| FLUX-B07 | AA | FC | Facture fournisseur (montant, devise, TVA) pour three-way matching | Réception facture fournisseur | Événementiel | OUI — RG-FC-05 | **HAUTE** |
| FLUX-B08 | FC | AA | Règlement fournisseur émis (montant, date, références) | Campagne de règlement | Batch quotidien | ABSENT — non documenté mais nécessaire pour mettre à jour l'encours fournisseur AA | BASSE |

---

## C — Flux supply chain & logistique

| ID | Source | Destination | Donnée échangée | Déclencheur | Fréquence | Documenté ? | Criticité |
|----|--------|------------|-----------------|-------------|-----------|:-----------:|:---------:|
| FLUX-C01 | SE | SC | Niveaux de stock (disponible, réservé, en-cours, seuils sécurité) pour MRP | Avant calcul MRP ou en continu | Batch / temps réel | OUI — SC entités : « Données d'entrée du MRP » | **HAUTE** |
| FLUX-C02 | SC | AA | Propositions de commandes fournisseurs (article, qté, fournisseur, date besoin) | Exécution du calcul MRP | Batch | OUI — SC entités : « Propositions de commandes issues du MRP » | **HAUTE** |
| FLUX-C03 | SC | SE | Ordre d'expédition → ordre de préparation picking (articles, qté, destination) | Planification expédition | Temps réel | OUI — SE entités : « Ordres de préparation et confirmations d'expédition » | **HAUTE** |
| FLUX-C04 | SE | SC | Confirmation d'expédition (qté réellement expédiées, date, colis) | Fin de préparation physique | Temps réel | OUI — SE entités, RG-SE-08 | **HAUTE** |
| FLUX-C05 | SC | SE | Ordre de réapprovisionnement magasin (article, qté, magasin, type réappro/cross-dock) | Calcul suggestion réappro | Événementiel | OUI — SC spec P4, SC entités | MOYENNE |
| FLUX-C06 | SC | AA | Prévisions saisonnières et promotionnelles pour achats anticipés | Plan de vente validé | Batch périodique | IMPLICITE — déduit de RG-AA-04 : « besoins saisonniers approvisionnés sur base plans de vente » | MOYENNE |
| FLUX-C07 | SC | GC | Date de livraison estimée (par commande) | Planification transport, changement statut | Temps réel | IMPLICITE — déduit de GC spec P1 étape 5 : « suivi état en temps réel » | **HAUTE** |

---

## D — Flux comptables et financiers

| ID | Source | Destination | Donnée échangée | Déclencheur | Fréquence | Documenté ? | Criticité |
|----|--------|------------|-----------------|-------------|-----------|:-----------:|:---------:|
| FLUX-D01 | SE | FC | Écritures de variation de stock (article, valeur, méthode FIFO/CUMP/standard) | Mouvement de stock (entrée, sortie, transfert, ajustement) | Événementiel | OUI — RG-SE-13, SE entités | **HAUTE** |
| FLUX-D02 | SE | FC | Écritures de régularisation inventaire (écarts comptage, valeur) | Validation des écarts d'inventaire | Événementiel | OUI — RG-SE-07 : « écarts validés génèrent auto écritures régularisation » | **HAUTE** |
| FLUX-D03 | SE | FC | Valorisation stock fin de période (valeur totale par article/site, ancienneté) | Clôture mensuelle / trimestrielle | Batch mensuel | OUI — SE spec P5 : « valorisation selon méthode comptable applicable » | **HAUTE** |
| FLUX-D04 | FC | GC | Facture client émise (N° facture, montant, statut) | Génération facture | Événementiel | IMPLICITE — déduit de FC spec P5, lien Facture → Commande client | MOYENNE |
| FLUX-D05 | FC | AA | Règlement fournisseur (N° virement, montant, références) pour rapprochement | Exécution campagne de paiement | Batch quotidien | OUI — FC spec P3 étape 4 : « préparation campagnes de règlement » | MOYENNE |
| FLUX-D06 | FC | GC | Encours client (solde, limite, statut) pour contrôle commande | Mise à jour encours (encaissement, impayé, relance) | Temps réel | ABSENT — non documenté mais classiquement nécessaire pour bloquer les commandes en dépassement d'encours | **HAUTE** |

---

## E — Flux de synchronisation des référentiels

| ID | Source | Destination | Donnée échangée | Déclencheur | Fréquence | Documenté ? | Criticité |
|----|--------|------------|-----------------|-------------|-----------|:-----------:|:---------:|
| FLUX-E01 | CM | GC, FC, RB | Référentiel client maître (identification, type, coordonnées, catégorie) | Création / modification fiche client | Temps réel | OUI — RG-CM-01 : « ERP référentiel client maître » | **HAUTE** |
| FLUX-E02 | GC | AA, SC, SE, FC, RB | Référentiel article (SKU, libellé, catégorie, famille, prix de base, pays) | Création / modification article | Événementiel | OUI — Matrice de partage : Article M=GC, R=AA,SC,SE,FC,RB | **HAUTE** |
| FLUX-E03 | AA | SC, FC, SE, RB | Référentiel fournisseur (identification, pays, devise, conditions, évaluation) | Création / modification / évaluation fournisseur | Événementiel | OUI — Matrice de partage : Fournisseur M=AA | **HAUTE** |
| FLUX-E04 | SE | AA, SC, GC, RB | Référentiel sites de stockage (code, type, capacité, adresse) | Création / modification site | À la demande | OUI — Matrice de partage : Entrepôt/Site M=SE | MOYENNE |
| FLUX-E05 | GC | CM, RB | Tarifs et promotions (prix, conditions, périodes, règles) | Création / modification / activation tarif ou promotion | Événementiel | IMPLICITE — déduit de RG-CM-08 : « cohérence offres promo et conditions tarifaires » | **HAUTE** |
| FLUX-E06 | GC | CM | Historique commandes et retours (par client) | Chaque vente / retour | Temps réel | IMPLICITE — déduit de CM entités : « Historique d'achat pour fidélité et segmentation » | **HAUTE** |

---

## F — Flux CRM & fidélité

| ID | Source | Destination | Donnée échangée | Déclencheur | Fréquence | Documenté ? | Criticité |
|----|--------|------------|-----------------|-------------|-----------|:-----------:|:---------:|
| FLUX-F01 | CM | GC | Palier fidélité client et avantages actifs (remise, offre) | Franchissement palier ou achat en cours | Temps réel | IMPLICITE — déduit du flux Client→Fidélité→Tarification (referentiels-transverses) | **HAUTE** |
| FLUX-F02 | CM | GC | Activation / désactivation promotion liée à une campagne marketing | Lancement campagne | Événementiel | IMPLICITE — déduit de RG-CM-08 et CM spec P4 étape 3 | MOYENNE |
| FLUX-F03 | CM | FC | Données client pour compte tiers (encours, conditions paiement) | Création / modification client | Événementiel | ABSENT — non documenté mais nécessaire pour alimenter la compta auxiliaire | MOYENNE |
| FLUX-F04 | GC | CM | Points fidélité à cumuler (montant achat, canal, articles) | Encaissement | Temps réel | OUI — RG-CM-04, CM spec P2 | **HAUTE** |
| FLUX-F05 | GC | CM | Utilisation de points comme moyen de paiement (points déduits, montant) | Paiement en caisse / web | Temps réel | OUI — RG-CM-05 : « points = moyen de paiement » | **HAUTE** |

---

## G — Flux reporting & BI

| ID | Source | Destination | Donnée échangée | Déclencheur | Fréquence | Documenté ? | Criticité |
|----|--------|------------|-----------------|-------------|-----------|:-----------:|:---------:|
| FLUX-G01 | GC, AA, SC, FC, SE, CM | RB | Données transactionnelles tous domaines | Continu / programmé | Quasi temps réel à batch | OUI — RB spec P4, RG-RB-06, RG-RB-07 | **HAUTE** |
| FLUX-G02 | FC | RB | Axes analytiques et données budgétaires | Modification paramétrage analytique | Événementiel | OUI — RB entités : « Axe analytique partagé avec comptabilité analytique » | MOYENNE |
| FLUX-G03 | SC | RB | KPI supply chain (taux service, délai, coût logistique, productivité) | Calcul périodique / événement | Temps réel | OUI — RB entités : « KPI supply chain alimentent les tableaux de bord » | MOYENNE |
| FLUX-G04 | RB | SC | Prévisions de vente IA (demande prédite, anomalies détectées) | Exécution modèle prédictif | Batch | OUI — RB entités : « Prévisions IA alimentent planification demande » | MOYENNE |
| FLUX-G05 | RB | Tous | Alertes IA (anomalies, recommandations d'actions) | Détection automatique | Événementiel | ABSENT — documenté côté RB (spec P5) mais aucun domaine récepteur ne modélise l'intégration des alertes IA | BASSE |

---

## Matrice des flux inter-domaines

> Nombre de flux identifiés par couple source → destination. Lecture : ligne = source, colonne = destination.

| Source ↓ \ Dest → | GC | AA | SC | FC | SE | CM | RB |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **GC** | — | | 1 | 3 | 3 | 3 | ↘ |
| **AA** | | — | | 4 | 2 | | ↘ |
| **SC** | 1 | 2 | — | | 2 | | ↘ |
| **FC** | 2 | 1 | | — | | | ↘ |
| **SE** | 1 | 1 | 2 | 3 | — | | ↘ |
| **CM** | 2 | | | 1 | | — | ↘ |
| **RB** | | | 1 | | | | — |

> Les flux vers RB (↘) sont consolidés dans FLUX-G01 car tous les domaines alimentent le reporting.

### Couples les plus chargés

| Rang | Couple | Flux | Détail |
|:----:|--------|:----:|--------|
| 1 | GC ↔ SE | 4 | Réservation, ATP, retour, libération stock |
| 2 | AA → FC | 4 | Three-way matching (commande + réception + facture) + prix de revient |
| 3 | GC → FC | 3 | Écriture vente, écriture avoir, facture |
| 4 | SE → FC | 3 | Variation stock, régularisation inventaire, valorisation |
| 5 | GC ↔ CM | 5 | Client maître, fidélité (cumul/utilisation), historique, promotions |

---

## Flux non documentés (ABSENTS) — analyse détaillée

Ces 5 flux ne figurent dans aucune spécification fonctionnelle mais sont logiquement indispensables au fonctionnement de l'ERP :

### FLUX-A09 — Libération de stock sur annulation commande

| Propriété | Valeur |
|-----------|--------|
| **Source** | GC |
| **Destination** | SE |
| **Donnée** | Libération de la réservation stock (article, qté, site) |
| **Justification** | La réservation est documentée (FLUX-A01) mais l'annulation ne l'est pas. Sans ce flux, le stock réservé pour des commandes annulées resterait indisponible. |
| **Impact de l'absence** | Érosion progressive du stock disponible (ATP), sur-stockage apparent, divergence stock physique/logique |
| **Criticité** | **HAUTE** |

### FLUX-D06 — Contrôle d'encours client sur commande

| Propriété | Valeur |
|-----------|--------|
| **Source** | FC |
| **Destination** | GC |
| **Donnée** | Encours client (solde, limite crédit, statut blocage) |
| **Justification** | FC gère les encours et le recouvrement (FC spec P2). GC gère la prise de commande. Sans ce flux, des commandes peuvent être acceptées pour des clients en dépassement d'encours. |
| **Impact de l'absence** | Risque financier non contrôlé, augmentation des impayés, créances douteuses |
| **Criticité** | **HAUTE** |

### FLUX-B08 — Retour de règlement fournisseur vers achats

| Propriété | Valeur |
|-----------|--------|
| **Source** | FC |
| **Destination** | AA |
| **Donnée** | Confirmation de règlement (montant, date, références) |
| **Justification** | FC émet les règlements (spec P3 étape 4). AA gère la relation fournisseur et l'évaluation. Sans retour d'information, AA ne peut pas suivre l'encours fournisseur. |
| **Impact de l'absence** | Décalage encours fournisseur, difficulté de reporting achats, double saisie |
| **Criticité** | BASSE (l'information peut être lue directement en FC) |

### FLUX-F03 — Alimentation compte tiers client

| Propriété | Valeur |
|-----------|--------|
| **Source** | CM |
| **Destination** | FC |
| **Donnée** | Données client pour la comptabilité auxiliaire (conditions paiement, catégorie) |
| **Justification** | CM est le référentiel client maître (RG-CM-01). FC gère le compte tiers (encours, relances). Le lien entre « Client CM » et « Compte tiers FC » n'est documenté nulle part. |
| **Impact de l'absence** | Création manuelle des comptes tiers, désynchronisation client/compta |
| **Criticité** | MOYENNE |

### FLUX-G05 — Intégration des alertes IA dans les processus métier

| Propriété | Valeur |
|-----------|--------|
| **Source** | RB |
| **Destination** | Tous les domaines |
| **Donnée** | Alertes IA (anomalies, prévisions, recommandations d'actions) |
| **Justification** | RB spécifie l'analyse prédictive et la détection d'anomalies (spec P5, RG-RB-10). Mais aucun domaine récepteur ne décrit comment il intègre ces alertes dans ses processus. |
| **Impact de l'absence** | L'IA reste un outil de consultation passive, sans impact sur les processus opérationnels |
| **Criticité** | BASSE (fonctionnalité avancée, pas bloquante au MVP) |

---

## Dépendances cachées identifiées

Au-delà des flux explicites, l'analyse révèle des **dépendances implicites** entre domaines, où une règle de gestion d'un domaine dépend d'une donnée détenue par un autre sans que le flux soit modélisé :

| # | Domaine de la règle | Règle | Donnée nécessaire | Domaine détenteur | Flux manquant |
|---|-----|------|-------|-------|--------|
| 1 | GC — RG-GC-03 | Cascade tarifaire (prix base → B2B → promo → remise → soldes) | Avantages du palier fidélité client | CM | Les avantages fidélité ne figurent pas dans la cascade |
| 2 | SC — RG-SC-02 | MRP intègre stocks et en-cours | Commandes fournisseurs en cours (qté, date livraison) | AA | Le flux AA→SC n'est pas documenté (SC accède-t-il à AA ou AA pousse vers SC ?) |
| 3 | SC — RG-SC-04 | Capacité stockage en contrainte MRP | Capacité restante des sites de stockage | SE | Le flux SE→SC pour la capacité (pas seulement les niveaux de stock) n'est pas explicite |
| 4 | AA — RG-AA-04 | Achats sur prévision (saisonnier) | Prévisions de vente et plans promotionnels | SC + CM | Le flux CM→AA (plans promotionnels à anticiper en achats) est absent |
| 5 | FC — RG-FC-10 | Axes analytiques flexibles (canal, famille, zone, magasin) | Paramétrage des axes partagé avec BI | RB | Qui est maître de la définition des axes : FC ou RB ? |
| 6 | GC — RG-GC-06 | Intégration POS bidirectionnelle (prix/promos/stocks) | Données fidélité client (solde points, palier) en temps réel au POS | CM | Le flux CM→POS (via GC ?) pour les données fidélité en caisse n'est pas tracé |
| 7 | SE — RG-SE-04 | Traçabilité exhaustive pour rappels produits | Données d'origine lot : fournisseur, date fabrication | AA | Le lien entre le Lot (SE) et le Fournisseur (AA) est modélisé dans les entités mais le flux d'alimentation n'est pas documenté |

---

## Chronologie des flux sur les événements métier clés

### Événement : Commande client web validée

```
t0  GC : commande validée
    ├─► FLUX-A01  GC→SE : réservation stock
    ├─► FLUX-A06  GC→CM : cumul points fidélité
    └─► FLUX-E06  GC→CM : historique commande mis à jour
t1  SE : stock réservé confirmé
    └─► FLUX-A02  SE→GC : ATP mis à jour (stock disponible web rafraîchi)
t2  GC→SC : FLUX-A03  commande transmise pour expédition
t3  SC : planification transport, affectation tournée
    └─► FLUX-C07  SC→GC : date livraison estimée
t4  SC→SE : FLUX-C03  ordre de préparation picking
t5  SE : préparation physique (WMS Manhattan)
t6  SE→SC : FLUX-C04  confirmation expédition
t7  SC : suivi temps réel transporteur
    └─► FLUX-A04  SC→GC : tracking mis à jour → notification client
t8  GC : livraison confirmée → encaissement
    └─► FLUX-A05  GC→FC : écriture comptable vente
    └─► FLUX-D04  FC→GC : facture émise
```

### Événement : Réception fournisseur en entrepôt

```
t0  AA : marchandise arrivée, contrôle quanti/quali lancé
t1  AA : réception validée (ou litige ouvert si écart)
    ├─► FLUX-B01  AA→SE : entrée en stock
    ├─► FLUX-B03  AA→FC : réception pour three-way matching
    └─► FLUX-D01  SE→FC : écriture de variation stock
t2  FC : rapprochement commande (FLUX-B02) + réception (FLUX-B03) + facture (FLUX-B07)
t3  FC : règlement fournisseur à échéance
    └─► FLUX-D05  FC→AA : confirmation règlement
```

### Événement : Clôture comptable mensuelle

```
t0  SE : lancement valorisation stock
    └─► FLUX-D03  SE→FC : valorisation par article/site/méthode
t1  SE : inventaire tournant (si planifié)
    └─► FLUX-D02  SE→FC : écritures de régularisation
t2  FC : consolidation écritures
    ├─ Écritures de vente (FLUX-A05) déjà intégrées
    ├─ Écritures d'achat (three-way) déjà intégrées
    └─ Écritures de variation stock (FLUX-D01) déjà intégrées
t3  FC : déclaration TVA par pays (RG-FC-06)
t4  FC : rapprochement bancaire (RG-FC-14)
t5  FC→RB : FLUX-G02  données financières clôturées → tableaux de bord
```

---

## Questions d'arbitrage liées aux flux

| # | Question | Flux concernés | Domaines |
|---|----------|---------------|----------|
| 1 | **Le contrôle d'encours client doit-il être bloquant en temps réel ?** Si oui, FLUX-D06 est critique et doit être synchrone. Si non, un contrôle batch quotidien suffit. | FLUX-D06 | FC → GC |
| 2 | **Le MRP accède-t-il aux commandes fournisseurs en cours (AA) ou AA pousse-t-il les données ?** Pull (SC lit AA) ou push (AA notifie SC) — impact sur la fraîcheur des données MRP. | Dépendance #2 | AA ↔ SC |
| 3 | **Comment les avantages fidélité s'intègrent-ils dans la cascade tarifaire ?** Le flux CM→GC (FLUX-F01) doit être intégré dans RG-GC-03 — à quel niveau de la cascade ? | FLUX-F01, Dépendance #1 | CM → GC |
| 4 | **La proposition de commande SC se transforme-t-elle automatiquement en commande AA ?** Si oui, quel enrichissement automatique (devise, incoterm) ? Si non, quel workflow de validation ? | FLUX-C02 | SC → AA |
| 5 | **Qui est maître des définitions de KPI et d'axes analytiques : FC ou RB ?** Impact sur FLUX-G02 (direction) et la gouvernance des indicateurs. | FLUX-G02, Dépendance #5 | FC ↔ RB |
