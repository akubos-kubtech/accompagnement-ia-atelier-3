# Etape 3 : Matrice de dépendances et ordre d'implémentation

> Construit à partir des 46 flux inter-modules (flux-inter-modules.md), des 16 entités partagées (reconciliation-entites.md) et des 30 frictions (zones-friction.md).

## Matrice de dépendances

> Lecture : la ligne **dépend de** la colonne. Ex : GC ligne / SE colonne = GC dépend de SE.

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

```
          Modules dont             Modules qui
          d'autres dépendent       dépendent d'autres

          "On a besoin de moi"     "J'ai besoin des autres"

    SE ██████████████ 5            RB ████████████████████ 6
    GC ██████████████ 5            GC ████████████████ 4
    FC ████████████ 4              AA ████████████ 3 (lire*)
    AA ████████████ 4              SC ████████████████ 4
    SC ██████████ 3                FC ████████████████ 4
    CM ██████████ 3                SE ████████████████ 4
    RB ████ 1                     CM ████ 1
```

---

## Détail de chaque dépendance

### GC (Gestion commerciale) dépend de :

| Fournisseur | Nature | Flux / Réf. | Criticité | Contournable ? |
|-------------|--------|-------------|:---------:|:--------------:|
| **SE** | Stock disponible (ATP) pour vente web et magasin | FLUX-A02 | **Haute** | Partiellement — mode dégradé caisse possible, mais vente web impossible sans stock |
| **CM** | Référentiel client maître (identification, type, catégorie) | FLUX-E01 | **Haute** | Non — pas de commande sans fiche client |
| **CM** | Palier fidélité et avantages actifs pour cascade tarifaire | FLUX-F01 | Moyenne | Oui — la vente fonctionne sans fidélité (prix hors avantages) |
| **SC** | Statut expédition, tracking, date livraison estimée | FLUX-A04, FLUX-C07 | Moyenne | Oui — la commande est prise sans tracking, suivi dégradé |
| **FC** | Encours client pour contrôle commande | FLUX-D06 (absent) | **Haute** | Partiellement — sans contrôle, les commandes passent mais risque financier |
| **FC** | Facture client émise (N° facture, statut) | FLUX-D04 | Moyenne | Oui — l'encaissement ne dépend pas de la facture |

---

### AA (Achats & Approvisionnement) dépend de :

| Fournisseur | Nature | Flux / Réf. | Criticité | Contournable ? |
|-------------|--------|-------------|:---------:|:--------------:|
| **SE** | Franchissement seuil de stock → réappro automatique | FLUX-B05 | **Haute** | Partiellement — commande manuelle possible mais perte de réactivité |
| **SC** | Propositions de commandes MRP | FLUX-C02 | **Haute** | Partiellement — achat sans MRP = gestion manuelle ou seuil uniquement |
| **SC** | Prévisions saisonnières pour achats anticipés | FLUX-C06 | Moyenne | Oui — l'acheteur peut anticiper manuellement |
| **FC** | Confirmation de règlement fournisseur | FLUX-D05 | Moyenne | Oui — suivi encours possible en lisant FC directement |

---

### SC (Supply Chain & Logistique) dépend de :

| Fournisseur | Nature | Flux / Réf. | Criticité | Contournable ? |
|-------------|--------|-------------|:---------:|:--------------:|
| **SE** | Niveaux de stock pour calcul MRP | FLUX-C01 | **Haute** | Non — MRP impossible sans données de stock |
| **SE** | Confirmation d'expédition (qté réelles, date) | FLUX-C04 | **Haute** | Non — suivi expédition impossible sans retour SE/WMS |
| **GC** | Commande client validée → déclenchement expédition | FLUX-A03 | **Haute** | Non — sans commande, pas d'expédition |
| **AA** | Commandes fournisseurs en cours (pour MRP) | Dépendance cachée #2 | Moyenne | Partiellement — MRP fonctionne mais moins précis |
| **RB** | Prévisions de vente IA | FLUX-G04 | Moyenne | Oui — MRP fonctionne avec algorithmes classiques SC |

---

### FC (Finance & Comptabilité) dépend de :

| Fournisseur | Nature | Flux / Réf. | Criticité | Contournable ? |
|-------------|--------|-------------|:---------:|:--------------:|
| **GC** | Données de vente pour écriture comptable | FLUX-A05 | **Haute** | Non — pas d'écriture de vente sans flux GC |
| **GC** | Données détaillées pour facture Factur-X | FRIC-206 | **Haute** | Non — obligation légale, facture impossible sans données ligne par ligne |
| **GC** | Avoir émis (retour) → écriture comptable | FLUX-A08 | Moyenne | Partiellement — saisie manuelle possible en rattrapage |
| **AA** | Commande fournisseur pour three-way matching | FLUX-B02 | **Haute** | Non — rapprochement impossible sans les 3 documents |
| **AA** | Réception pour three-way matching | FLUX-B03 | **Haute** | Non — idem |
| **AA** | Facture fournisseur pour three-way matching | FLUX-B07 | **Haute** | Non — idem |
| **AA** | Prix de revient complet (marge) | FLUX-B04 | **Haute** | Partiellement — marge approximative sans prix de revient complet |
| **SE** | Écritures de variation de stock | FLUX-D01 | **Haute** | Non — la compta stock est automatique ou fausse |
| **SE** | Écritures de régularisation inventaire | FLUX-D02 | **Haute** | Non — les écarts doivent être comptabilisés |
| **SE** | Valorisation stock fin de période | FLUX-D03 | **Haute** | Non — clôture impossible sans valorisation |
| **CM** | Données client pour compte tiers | FLUX-F03 (absent) | Moyenne | Partiellement — création manuelle du compte tiers |

---

### SE (Stocks & Entrepôts) dépend de :

| Fournisseur | Nature | Flux / Réf. | Criticité | Contournable ? |
|-------------|--------|-------------|:---------:|:--------------:|
| **GC** | Réservation de stock (commande validée) | FLUX-A01 | **Haute** | Partiellement — sans réservation, risque de sur-vente |
| **GC** | Libération stock (annulation commande) | FLUX-A09 (absent) | **Haute** | Non — stock bloqué indéfiniment sinon |
| **GC** | Retour validé → mouvement stock | FLUX-A07 | Moyenne | Partiellement — réintégration manuelle |
| **AA** | Réception validée → entrée en stock | FLUX-B01 | **Haute** | Non — pas d'entrée en stock sans validation réception |
| **AA** | Avis de réception attendue | FLUX-B06 | Moyenne | Oui — le WMS peut recevoir sans avis préalable |
| **SC** | Ordre d'expédition / picking | FLUX-C03 | **Haute** | Non — pas de préparation sans ordre |
| **SC** | Ordre de réapprovisionnement magasin | FLUX-C05 | Moyenne | Partiellement — réappro magasin manuel possible |
| **FC** | Méthode de valorisation (norme comptable locale) | FRIC-403 | Moyenne | Non — la méthode est structurante pour le paramétrage |

---

### CM (CRM & Marketing) dépend de :

| Fournisseur | Nature | Flux / Réf. | Criticité | Contournable ? |
|-------------|--------|-------------|:---------:|:--------------:|
| **GC** | Historique commandes et retours (par client) | FLUX-E06 | **Haute** | Non — pas de segmentation ni fidélité sans historique d'achat |
| **GC** | Points fidélité à cumuler (montant, canal, articles) | FLUX-F04 | **Haute** | Non — le programme fidélité dépend des transactions GC |
| **GC** | Utilisation de points comme paiement | FLUX-F05 | **Haute** | Non — la déduction de points nécessite la transaction GC |
| **GC** | Tarifs et promotions (pour cohérence campagnes) | FLUX-E05 | Moyenne | Partiellement — campagne possible mais risque d'incohérence tarifaire |

---

### RB (Reporting & BI) dépend de :

| Fournisseur | Nature | Flux / Réf. | Criticité | Contournable ? |
|-------------|--------|-------------|:---------:|:--------------:|
| **GC** | Données transactionnelles de vente | FLUX-G01 | **Haute** | Non — pas de reporting vente sans données |
| **AA** | Données transactionnelles achats | FLUX-G01 | **Haute** | Non — pas de reporting achats sans données |
| **SC** | KPI supply chain | FLUX-G03 | Moyenne | Partiellement — KPI calculables depuis données brutes |
| **FC** | Axes analytiques et données financières | FLUX-G02, FLUX-G01 | **Haute** | Non — reporting financier essentiel |
| **SE** | Données stock | FLUX-G01 | **Haute** | Non — pas d'analyse stock sans données |
| **CM** | Données clients et fidélité | FLUX-G01 | Moyenne | Partiellement — reporting possible sans données CRM |

---

## Analyse structurelle

### Profil de chaque module

| Module | Dépendent de moi | Je dépends de | Solde | Profil |
|--------|:----------------:|:-------------:|:-----:|--------|
| **SE** | 5 | 4 | +1 | **FONDATION** — fournit la donnée la plus critique (stock) à 5 modules |
| **GC** | 5 | 4 | +1 | **FONDATION** — produit les événements métier qui alimentent toute la chaîne |
| **FC** | 4 | 4 | 0 | **INTÉGRATEUR** — consomme de tous, alimente en retour (encours, factures) |
| **AA** | 4 | 3 | +1 | **FONDATION SECONDAIRE** — alimente FC (three-way) et SE (réceptions) |
| **SC** | 3 | 4 | -1 | **ORCHESTRATEUR** — dépend de beaucoup pour piloter les flux physiques |
| **CM** | 3 | 1 | +2 | **FONDATION AUTONOME** — référentiel client, ne dépend que de GC |
| **RB** | 1 | 6 | -5 | **CONSOMMATEUR** — dépend de tous, personne ne dépend de lui (sauf SC/IA) |

### Modules FONDATIONS

Les modules fondations sont ceux dont le plus de domaines dépendent et qui dépendent eux-mêmes de peu (solde positif) :

**1. SE (Stocks & Entrepôts)** — 5 modules en dépendent
- Fournit le stock (ATP) à GC, les niveaux MRP à SC, le seuil réappro à AA, la valorisation à FC, les données à RB
- La donnée stock est la plus **temps-sensible** du système (latence < 1 min WMS)
- Si SE est indisponible : pas de vente web, pas de MRP, pas de valorisation

**2. GC (Gestion commerciale)** — 5 modules en dépendent
- Produit les **événements métier** qui déclenchent toute la chaîne : vente, retour, commande
- Alimente FC (écritures, factures), SE (réservations), SC (expéditions), CM (fidélité), RB (données vente)
- Si GC est indisponible : plus de transactions, plus d'alimentation aval

**3. CM (CRM & Marketing)** — solde +2, le plus autonome
- Ne dépend que de GC (historique achats, transactions fidélité)
- Fournit le référentiel client maître à GC, FC et RB
- Si CM est indisponible : vente possible (mode dégradé sans fidélité), mais perte du référentiel client

### Module CONSOMMATEUR

**RB (Reporting & BI)** — dépend de 6 modules sur 6
- Consomme les données de tous les domaines sans exception
- Un seul module dépend de lui (SC, pour les prévisions IA)
- RB est le **dernier maillon de la chaîne** : il ne bloque personne mais a besoin de tout le monde

### GOULOT D'ÉTRANGLEMENT

```
                    ┌─────┐
            ┌──────►│ RB  │ (consomme tout)
            │       └─────┘
            │
     ┌──────┴──────┐         ┌─────┐
     │             │◄────────│ CM  │ (réf. client maître)
     │    ┌───┐    │         └──┬──┘
     │    │SE │◄───┼────┐      │
     │    └─┬─┘    │    │      │ ne dépend que de GC
     │      │      │    │      ▼
     │      │stock │    │   ┌─────┐
     │      ▼      │    └───│ GC  │ (événements métier)
     │   ┌─────┐   │        └──┬──┘
     │   │ FC  │◄──┤           │
     │   └─────┘   │    ┌─────┘
     │      ▲      │    │
     │      │      │    ▼
     │   ┌──┴──┐   │ ┌─────┐
     │   │ AA  │◄──┼─│ SC  │
     │   └─────┘   │ └─────┘
     │             │
     └─────────────┘

  ═══ SE et GC sont les deux goulots ═══
```

**SE** et **GC** sont les deux goulots d'étranglement du système, avec chacun **5 modules dépendants**. Mais leur nature est différente :

| Critère | SE | GC |
|---------|----|----|
| **Nature** | Fournisseur de **données fondamentales** (stock) | Producteur d'**événements métier** (ventes, commandes) |
| **Temporalité** | Temps réel (< 1 min WMS, < 2s web) | Événementiel (chaque transaction déclenche des flux) |
| **Mode dégradé** | Caisse autonome 48h (ENF), mais web KO | Aucun — sans GC, aucune transaction |
| **Complexité technique** | WMS Manhattan + multi-sites + réconciliation | POS Cegid + e-commerce + B2B + omnicanal |
| **Impact indisponibilité** | 5 modules impactés, valorisation FC bloquée | 5 modules impactés, plus aucun flux aval |

**GC est le goulot le plus critique** : son indisponibilité arrête toute la chaîne car aucun événement métier n'est produit. SE est le goulot le plus **techniquement sensible** car la latence de ses données (stock) conditionne la qualité de service de 5 modules.

---

## Graphe de dépendances et chemins critiques

### Chemin critique 1 : Cycle de vente (web)

```
CM (client) ──► GC (commande) ──► SE (réservation) ──► SC (expédition) ──► SE (picking)
                    │                                       │
                    └──► FC (écriture vente + facture)       └──► GC (tracking → client)
```
**6 modules impliqués, 8 flux, tous critiques.** Défaillance de n'importe quel maillon = commande non honorée.

### Chemin critique 2 : Cycle d'achat

```
SC (MRP) ──► AA (commande fournisseur) ──► SE (réception) ──► FC (three-way matching)
                                               │
                                               └──► FC (écriture variation stock)
```
**4 modules impliqués, 6 flux.** Le MRP (SC) est le point d'entrée, le three-way (FC) est le point de sortie.

### Chemin critique 3 : Valorisation et clôture

```
SE (mouvements) ──► SE (valorisation) ──► FC (écritures) ──► FC (clôture) ──► RB (reporting)
```
**3 modules, séquentiel strict.** Pas de parallélisation possible.

---

## Proposition de séquencement d'implémentation

> Basé sur les dépendances : implémenter d'abord les fondations, puis les consommateurs.

| Phase | Modules | Justification | Prérequis |
|:-----:|---------|--------------|-----------|
| **0** | **Référentiels transverses** : Article, Client, Fournisseur, Site/Magasin, Pays, Devise | Toutes les fondations dépendent de ces données de base (FRIC-501, FRIC-503, FRIC-504). Sans référentiels, aucun module ne peut être paramétré. | Aucun |
| **1** | **SE** (Stocks) + **CM** (CRM) | SE est fondation (stock ATP pour GC, niveaux pour SC, valorisation pour FC). CM est le référentiel client maître (ne dépend que de GC, qui n'existe pas encore — démarrage avec import). Les deux sont indépendants l'un de l'autre → parallélisables. | Phase 0 |
| **2** | **GC** (Gestion commerciale) | GC dépend de SE (stock) et CM (client), les deux étant disponibles en phase 1. GC est le producteur d'événements métier dont 5 modules ont besoin. | Phase 1 (SE + CM) |
| **3** | **AA** (Achats) + **FC** (Finance) | AA dépend de SE (réception → stock). FC dépend de GC (écritures vente), AA (three-way) et SE (valorisation) — tous disponibles. AA et FC sont partiellement parallélisables (FC peut démarrer avec les écritures de vente pendant qu'AA se met en place). | Phase 2 (GC) |
| **4** | **SC** (Supply Chain) | SC dépend de GC (commandes), SE (stock MRP), AA (commandes fournisseurs en cours). Tout est disponible après phase 3. Le MRP est le processus le plus complexe et nécessite des données historiques. | Phase 3 (AA) |
| **5** | **RB** (Reporting & BI) | RB dépend de tous les modules (6/6). Il ne peut être déployé complètement qu'en dernier. Des dashboards partiels peuvent être livrés dès la phase 2 (données de vente). | Phase 4 (tous modules) |

### Visualisation du séquencement

```
Phase 0 ─── Référentiels (Article, Client, Fournisseur, Site, Pays)
               │
Phase 1 ──┬── SE (Stocks & Entrepôts)
           └── CM (CRM & Marketing)          ← parallèles
               │
Phase 2 ───── GC (Gestion commerciale)       ← pivot central
               │
Phase 3 ──┬── AA (Achats & Appro)
           └── FC (Finance & Comptabilité)   ← partiellement parallèles
               │
Phase 4 ───── SC (Supply Chain & Logistique)
               │
Phase 5 ───── RB (Reporting & BI)            ← livraison incrémentale possible
```

### Points d'attention pour le séquencement

1. **Phase 0 est bloquante** — Les 4 référentiels absents (FRIC-501/503/504 + Article unifié FRIC-101) doivent être définis avant tout. C'est un travail de conception, pas d'implémentation.

2. **GC (phase 2) est le pivot** — Tant que GC n'est pas en place, aucun événement métier ne circule. Les phases 3-5 ne peuvent pas démarrer.

3. **RB peut livrer de manière incrémentale** — Dashboards de vente dès la phase 2, dashboards stock + fidélité dès la phase 2, reporting financier dès la phase 3, reporting complet en phase 5.

4. **La fidélité (CM↔GC) est un risque d'intégration majeur** — CM est déployé en phase 1 mais la fidélité ne fonctionne qu'après GC (phase 2). Les FRIC-301/601 (POS synchrone, dépendance circulaire) doivent être résolues à ce moment.

5. **Le MRP (SC, phase 4) nécessite de l'historique** — Pour que les prévisions soient pertinentes, SC a besoin de plusieurs mois de données de vente (GC). Prévoir une période de constitution d'historique après la phase 2.
