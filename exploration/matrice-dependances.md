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

## Classification des dépendances

> **Structurelle (S)** : le module ne peut être ni conçu ni paramétré sans cette donnée — non bouchonnable.
> **Transactionnelle (T)** : le module fonctionne en mode dégradé avec un bouchon — intégration réelle nécessaire avant go-live.

| De → Vers | Nature de la dépendance | Type | Bouchon possible |
|-----------|------------------------|:----:|-----------------|
| GC → SE | Stock ATP pour vente web/magasin | T | Mode dégradé caisse (48h autonome), vente web KO |
| GC → CM | Référentiel client maître | **S** | **Non** — pas de commande sans fiche client |
| GC → SC | Tracking, statut livraison | T | Commande prise sans suivi livraison |
| GC → FC | Encours client, facture émise | T | Commande passe sans contrôle encours |
| AA → SE | Seuil stock → réappro automatique | T | Commande fournisseur manuelle |
| AA → SC | Propositions MRP | T | Achat sur seuil ou achat manuel |
| AA → FC | Confirmation règlement fournisseur | T | Suivi encours lecture directe |
| SC → GC | Commande validée → expédition | T | Injection commandes de test |
| SC → AA | Commandes fournisseurs en cours (MRP) | T | MRP moins précis mais fonctionnel |
| SC → SE | Niveaux stock pour MRP + confirmation expédition | **S** | **Non** — MRP impossible sans données stock |
| SC → RB | Prévisions vente IA | T | Algorithmes classiques SC suffisent |
| FC → GC | Données vente → écriture comptable | T | Saisie manuelle en rattrapage |
| FC → AA | Three-way matching (PO + réception + facture) | T | Saisie directe facture sans rapprochement |
| FC → SE | Variation stock, valorisation fin de période | T | Pas de compta stock automatique |
| FC → CM | Données client → compte tiers | T | Création manuelle compte tiers |
| SE → GC | Réservation stock, retours | T | Mouvements de stock manuels |
| SE → AA | Réception validée → entrée stock | T | Entrée stock manuelle |
| SE → SC | Ordre picking / expédition | T | Préparation sur liste manuelle |
| SE → FC | Méthode de valorisation (norme comptable) | T | Pré-configuration paramétrique |
| CM → GC | Historique achats, fidélité | T | Import initial d'historique |
| RB → (6) | Données de tous les modules | T | Dashboards vides puis incrémentaux |

> **Résultat** : sur 26 dépendances, **2 sont structurelles** (GC→CM, SC→SE), **24 sont transactionnelles** (bouchonnables). Le parallélisme est élevé.

---

## Séquencement d'implémentation par phases

> Contraintes appliquées :
> 1. Un module ne peut être implémenté que si ses dépendances structurelles sont en place
> 2. Les dépendances transactionnelles sont bouchonnées temporairement
> 3. Parallélisme maximisé (modules indépendants déployés ensemble)

### Phase 0 — Référentiels transverses

| | |
|---|---|
| **Modules** | Article, Client, Fournisseur, Site/Magasin, Pays, Devise |
| **Prérequis** | Aucun |
| **Justification** | Les 7 modules consomment ces référentiels. Sans modèle de données partagé (identifiants, structures, codification), aucun module ne peut être paramétré. Résout directement FRIC-501 (Magasin sans référentiel), FRIC-503 (Pays absent), FRIC-504 (Devise absente). |
| **Risque** | **Arbitrage Article** : qui est maître entre ERP (GC) et PIM (Akeneo) ? Bloque la définition du modèle (FRIC-101). **Arbitrage Magasin** : 3 noms pour 3 concepts différents (FRIC-501) — nécessite décision client avant toute implémentation. |

### Phase 1 — SE (Stocks) + CM (CRM) `parallèles`

| | |
|---|---|
| **Modules** | SE (Stocks & Entrepôts), CM (CRM & Marketing) — en parallèle |
| **Prérequis** | Phase 0 (référentiels définis) |
| **Justification** | **SE** est fondation données : stock ATP consommé par 5 modules (GC, SC, AA, FC, RB). **CM** est fondation référentiel : le client maître est une **dépendance structurelle** de GC (seule dep. structurelle avec SC→SE). SE et CM sont **mutuellement indépendants** dans la matrice (aucun X à l'intersection) → parallélisables. |
| **Bouchons nécessaires** | SE bouchonne : réservations (GC), réceptions (AA), picking (SC), méthode valorisation (FC = pré-config). CM bouchonne : historique achats (GC = import initial). |
| **Risque** | **SE** : intégration Manhattan WMS multi-sites, 4 définitions de stock « disponible » (FRIC-103) à résoudre. **CM** : intégration Salesforce CRM, le programme fidélité ne sera testable qu'après GC (Phase 2) — FRIC-601 (dépendance circulaire CM↔GC) latent. |

### Phase 2 — GC (Gestion commerciale) + AA (Achats) `parallèles`

| | |
|---|---|
| **Modules** | GC (Gestion commerciale), AA (Achats & Approvisionnement) — en parallèle |
| **Prérequis** | Phase 1 — SE fournit le stock réel, CM fournit le client réel |
| **Justification** | **GC** : ses 2 dépendances structurelles sont satisfaites (CM client = Phase 1, SE stock = Phase 1). GC est le producteur d'événements métier dont 5 modules dépendent — le déployer tôt débloque toute la chaîne aval. **AA** : ne dépend structurellement que des référentiels (Phase 0) et bénéficie du stock réel SE (Phase 1) pour les seuils de réappro. **GC et AA sont mutuellement indépendants** dans la matrice (aucun X à leur intersection) → parallélisables. |
| **Bouchons nécessaires** | GC bouchonne : tracking (SC), encours client (FC). AA bouchonne : propositions MRP (SC), confirmation règlement (FC). |
| **Risque** | **GC** : intégration POS Cegid + e-commerce Salesforce = point d'intégration le plus complexe du projet. FRIC-301 (POS synchrone + asynchrone) et FRIC-601 (fidélité circulaire CM↔GC) émergent ici. **AA** : sans MRP (SC en Phase 3), les achats sont manuels ou sur seuil uniquement — risque de rupture si la phase dure longtemps. |

### Phase 3 — FC (Finance) + SC (Supply Chain) `parallèles`

| | |
|---|---|
| **Modules** | FC (Finance & Comptabilité), SC (Supply Chain & Logistique) — en parallèle |
| **Prérequis** | Phase 2 — GC fournit les données de vente, AA fournit les données d'achat |
| **Justification** | **FC** : toutes ses dépendances sont satisfaites — GC (écritures de vente, Factur-X), AA (three-way matching), SE (valorisation stock), CM (comptes tiers). **SC** : toutes ses dépendances sont satisfaites — GC (commandes → expédition), AA (PO en cours → MRP), SE (stock → MRP). Sa seule dépendance structurelle (SE) est disponible depuis Phase 1. **FC et SC sont mutuellement indépendants** dans la matrice → parallélisables. |
| **Bouchons retirés** | Tous les bouchons des Phases 1-2 sont remplacés par des flux réels. Seul bouchon restant : SC bouchonne les prévisions IA (RB). |
| **Risque** | **FC** : le flux Factur-X GC→FC (FRIC-206) doit être construit — il n'existe pas dans les SF actuelles. Three-way matching multi-devises (8 devises, 15 pays) = complexité élevée. **SC** : le MRP nécessite de l'historique de vente GC (Phase 2) — si la Phase 2 a été courte, les prévisions seront pauvres. Le cross-docking (FRIC-602) nécessite un orchestrateur non défini dans le CDC. |

### Phase 4 — RB (Reporting & BI)

| | |
|---|---|
| **Modules** | RB (Reporting & BI) |
| **Prérequis** | Phase 3 — les 6 modules opérationnels sont en place |
| **Justification** | RB dépend de **6 modules sur 6** (consommateur pur). Il ne peut être complet qu'en dernier. |
| **Bouchons retirés** | SC retire le bouchon RB → les prévisions IA deviennent réelles. |
| **Risque** | **Qualité des données** : RB est le premier endroit où les incohérences d'intégration deviennent visibles. Si les phases précédentes ont produit des données non réconciliées (stock, écritures comptables), RB les exposera. **Volumétrie** : Snowflake doit absorber les données de 7 domaines (estimé > 500M lignes). |

### Livraison incrémentale de RB

Bien que RB soit en Phase 4, des dashboards partiels peuvent être livrés plus tôt :

| Disponible à partir de | Dashboards |
|:-:|---|
| Phase 1 | Stock temps réel (SE), base clients (CM) |
| Phase 2 | Ventes et CA (GC), suivi achats (AA) |
| Phase 3 | Reporting financier (FC), KPI supply chain (SC) |
| Phase 4 | Reporting complet + prévisions IA |

---

## Visualisation

```
Phase 0 ─── Référentiels (Article, Client, Fournisseur, Site, Pays, Devise)
               │
Phase 1 ──┬── SE (Stocks & Entrepôts)       ← fondation données (stock)
           └── CM (CRM & Marketing)          ← fondation référentiel (client)
               │
Phase 2 ──┬── GC (Gestion commerciale)      ← pivot événementiel
           └── AA (Achats & Appro)           ← cycle d'achat
               │
Phase 3 ──┬── FC (Finance & Comptabilité)   ← intégrateur comptable
           └── SC (Supply Chain)             ← orchestrateur logistique
               │
Phase 4 ───── RB (Reporting & BI)           ← consommateur final (incrémental)
```

**5 phases, 3 paliers de parallélisme, 24 bouchons temporaires retirés progressivement.**

---

## Chemin critique

Le chemin critique est la **séquence la plus longue de dépendances non-réductibles** qui détermine la durée minimale du projet.

### Chemin 1 : Référentiel client → Vente → Comptabilité

```
Référentiels ──► CM (client maître) ──► GC (événements vente) ──► FC (écritures + Factur-X) ──► RB
  Phase 0           Phase 1                Phase 2                    Phase 3                  Phase 4
```

Justification de chaque maillon :
1. **Réf → CM** : CM a besoin du modèle Client défini en Phase 0
2. **CM → GC** : dépendance **structurelle** — pas de commande sans fiche client
3. **GC → FC** : FC ne peut produire d'écritures comptables ni de Factur-X sans données de vente réelles (bouchon intenable pour go-live — obligation légale)
4. **FC → RB** : le reporting financier est réglementaire (pas de bouchon acceptable)

### Chemin 2 : Stock → Vente → Supply Chain

```
Référentiels ──► SE (stock) ──► GC (réservation + vente) ──► SC (MRP + expédition) ──► RB
  Phase 0         Phase 1           Phase 2                      Phase 3               Phase 4
```

Justification :
1. **Réf → SE** : SE a besoin du modèle Article et Site
2. **SE → SC** : dépendance **structurelle** — MRP impossible sans données stock
3. **GC → SC** : SC ne peut expédier sans commandes validées
4. **SC → RB** : les KPI supply chain alimentent le reporting

### Conclusion

Les deux chemins ont la **même longueur : 5 phases (incompressible)**. Ils convergent sur GC (Phase 2) qui est le point de jonction des deux chaînes de dépendances structurelles.

```
     CM (S)──►─┐
               ├──► GC ──► FC ──► RB
     SE (S)──►─┘     │
               ▲     └──► SC ──► RB
               │
          2 dépendances
          structurelles
          convergent sur GC
```

**GC est le nœud critique du projet** : il se trouve à l'intersection des deux seules dépendances structurelles de la matrice. Tout retard sur GC décale mécaniquement les Phases 3 et 4.

---

## Frictions bloquantes par phase

Les 9 frictions BLOQUANT doivent être résolues **AVANT** la phase concernée :

| Phase | Frictions à résoudre | Arbitrage nécessaire |
|:-----:|---------------------|---------------------|
| 0 | FRIC-501 (Magasin sans référentiel), FRIC-503 (Pays absent), FRIC-504 (Devise absente), FRIC-101 (Article 4 noms) | Qui est maître pour Article ? Quelle granularité pour Site/Magasin ? |
| 1 | FRIC-103 (stock « disponible » 4 définitions) | Quelle définition par contexte (ATP, réservé, physique, transit) ? |
| 2 | FRIC-301 (POS sync + async), FRIC-601 (fidélité circulaire), FRIC-201 (flux POS→SE absent) | Le POS interroge-t-il le stock central ou local ? La fidélité est-elle synchrone ou asynchrone ? |
| 3 | FRIC-206 (Factur-X GC→FC), FRIC-602 (cross-docking sans orchestrateur) | Qui génère la facture Factur-X ? Qui orchestre le cross-docking ? |
