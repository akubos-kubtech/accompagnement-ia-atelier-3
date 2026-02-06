# Etape 3 : Matrice de dépendances et ordre d'implémentation

> Identifier les prérequis entre modules et proposer un séquencement réaliste.

## Matrice de dépendances

> Lecture : la ligne **dépend de** la colonne. Ex: GC dépend de SE (stock disponible) → case marquée.

| | GC | AA | SC | FC | SE | CM | RB |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| **GC** (Gestion commerciale) | - | | | | **X** | **X** | |
| **AA** (Achats & Approvisionnement) | | - | | | | | |
| **SC** (Supply Chain & Logistique) | | **X** | - | | **X** | | |
| **FC** (Finance & Comptabilité) | **X** | **X** | | - | **X** | | |
| **SE** (Stocks & Entrepôts) | | **X** | | | - | | |
| **CM** (CRM & Marketing) | **X** | | | | | - | |
| **RB** (Reporting & BI) | **X** | **X** | **X** | **X** | **X** | **X** | - |

### Détail des dépendances

| Dépendance | Nature | Criticité | Contournable ? |
|-----------|--------|:---------:|:--------------:|
| GC → SE | Stock disponible (ATP) pour la prise de commande | **Haute** | Non — pas de vente sans visibilité stock |
| GC → CM | Référentiel client, palier fidélité → prix | **Haute** | Partiellement — possible avec client simplifié |
| AA → (aucun) | Autonome pour le cycle achats (fournisseur, commande, réception) | - | - |
| SC → AA | Fournisseurs et conditions pour transformer les propositions MRP en commandes | **Haute** | Non — le MRP produit des propositions, AA les concrétise |
| SC → SE | Positions de stock pour le calcul MRP | **Haute** | Non — le MRP est impossible sans données stock |
| FC → GC | Transactions de vente pour facturation et écritures | **Haute** | Partiellement — saisie manuelle possible mais non viable |
| FC → AA | Commandes/réceptions fournisseur pour three-way matching | **Haute** | Non — rapprochement impossible sans données achats |
| FC → SE | Mouvements de stock pour écritures de variation et valorisation | **Haute** | Non — obligation comptable |
| SE → AA | Réceptions validées pour mise en stock | **Haute** | Non — pas d'entrée stock sans réception |
| CM → GC | Historique d'achat pour fidélité et segmentation | Moyenne | Oui — fidélité dégradée sans historique |
| RB → Tous | Données transactionnelles de tous les domaines | **Haute** | Non — pas de reporting sans données |

## Chemin critique

Le chemin critique d'implémentation suit la chaîne de dépendances la plus longue :

```
Référentiels       AA (Achats)        GC (Commercial)       FC (Finance)        RB (Reporting)
(Article,     →   + SE (Stocks)   →  + CM (CRM)        →   (comptabilité,  →   (BI, KPI,
Fournisseur,       fondations          ventes                 consolidation)      tableaux de bord)
Client, Sites)     opérationnelles     omnicanales
                       │
                       └─── SC (Supply Chain) ──────────────────┘
                            (MRP, transport,
                             réappro magasin)
```

**Longueur du chemin critique** : 4 phases séquentielles minimum

**Goulot d'étranglement** : SE (Stocks & Entrepôts) — 4 domaines en dépendent directement (GC, SC, FC, RB)

## Proposition de séquencement

| Phase | Modules | Périmètre | Justification | Prérequis |
|:-----:|---------|-----------|--------------|-----------|
| **0** | **Référentiels transverses** | Article, Client, Fournisseur, Sites, Plan de comptes, Devises | Fondation de toute l'implémentation — sans données maîtres, aucun module ne fonctionne | Migration données sources (SAP, Salesforce, Akeneo) |
| **1** | **SE** + **AA** | Stocks (positions, mouvements, inventaire, valorisation) + Achats (fournisseurs, commandes, réceptions) | Noyau opérationnel : SE est le goulot d'étranglement (4 domaines en dépendent). AA alimente SE via les réceptions | Phase 0 (référentiels). Intégration WMS Manhattan |
| **2** | **GC** + **CM** | Ventes omnicanales (commandes, tarification, retours) + CRM (client 360°, fidélité Horizon Club) | La vente et le CRM forment un couple fonctionnel. GC a besoin de SE (stock) et CM (client) | Phase 1 (SE opérationnel). Intégration POS Cegid + Salesforce Commerce Cloud + Salesforce CRM |
| **3** | **SC** + **FC** | Supply chain (MRP, transport, réappro, douanes) + Finance (comptabilité, facturation, trésorerie, TVA) | SC a besoin de SE + AA. FC a besoin de GC + AA + SE. Les deux peuvent démarrer en parallèle | Phase 2 (GC pour FC). Intégration Control-M, transporteurs, plateformes bancaires, Chorus Pro/SDI/SII |
| **4** | **RB** | Reporting opérationnel, dashboards, alimentation BI Tableau/Snowflake | Le reporting exploite les données de tous les domaines — il faut des données transactionnelles réelles | Phases 1-3 (tous les domaines opérationnels). Intégration Tableau Software |

### Variante : approche par vagues géographiques

Le CDC prévoit un déploiement multi-pays. Le séquencement ci-dessus s'applique à la **Vague 1 (France)** :

| Vague | Périmètre géo | Modules | Langues | Spécificités |
|:-----:|---------------|---------|:-------:|-------------|
| 1 | France | Tous (phases 0-4) | FR, EN | Factur-X, Manhattan WMS, Cegid POS |
| 2 | Europe Ouest | GC, AA, SE, FC, CM | DE, NL, IT | SDI (Italie), normes comptables locales |
| 3 | Europe Sud + Est | GC, AA, SE, FC | ES, PL, CZ | SII (Espagne), SAF-T (Pologne) |
| 4 | Nordiques | GC, AA, SE, FC | SV, DA, NO, FI | SAF-T, normes nordiques |
| 5 | Asie | GC, SE | TH | Réglementation locale, WMS local |

### Risques liés au séquencement

| # | Risque | Impact | Mitigation |
|:-:|--------|--------|-----------|
| 1 | SE en goulot → tout retard sur les stocks retarde 4 autres domaines | **Critique** | Prioriser les ressources sur SE, POC intégration WMS dès la phase 0 |
| 2 | Intégration POS complexe (3 domaines simultanés) → retard Phase 2 | **Élevé** | Développer un POC POS en phase 1, maquetter les 3 flux en parallèle |
| 3 | FC dépend de 3 flux entrants (GC, AA, SE) → risque d'écritures incomplètes au démarrage | **Élevé** | Démarrer FC en mode « réception » dès phase 1, comptabiliser manuellement les flux manquants |
| 4 | Multi-WMS non documentés → blocage du déploiement international | **Moyen** | Cartographier les WMS par pays dès la phase 0, définir une interface d'intégration générique |
| 5 | RB en dernière phase → pas de visibilité analytique pendant 60-70% du projet | **Moyen** | Déployer un reporting minimal (états standards ERP) dès phase 1, réserver la BI avancée pour phase 4 |
