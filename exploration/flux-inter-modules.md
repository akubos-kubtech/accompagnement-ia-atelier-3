# Etape 1 : Cartographie des flux inter-modules

> Partir des spécifications détaillées pour identifier quels domaines échangent des données.

## Objectif

Faire apparaitre les dépendances entre domaines, y compris les dépendances cachées
(ex: une règle de gestion qui dépend d'une donnée définie dans un autre domaine).

## Flux identifiés

### Flux opérationnels (transactions courantes)

| # | Source | Cible | Donnée échangée | Type de flux | Criticité | Référence |
|:-:|--------|-------|-----------------|:-------------|:---------:|-----------|
| 1 | GC | SE | Commande validée → réservation stock | Temps réel | **Haute** | RG-GC-11, RG-SE-03 |
| 2 | GC | SE | Retour client → contrôle qualité et réintégration | Événementiel | Moyenne | RG-GC-07, RG-GC-08 |
| 3 | GC | FC | Commande livrée → génération facture client | Événementiel | **Haute** | RG-FC-04, RG-FC-08 |
| 4 | GC | CM | Transaction de vente → cumul points fidélité | Temps réel | **Haute** | RG-CM-04 |
| 5 | GC | SC | Commande validée → déclenchement expédition | Événementiel | **Haute** | RG-SC-05 |
| 6 | AA | SE | Réception validée → mise en stock | Temps réel | **Haute** | RG-AA-10 |
| 7 | AA | FC | Facture fournisseur → three-way matching | Événementiel | **Haute** | RG-FC-05 |
| 8 | AA | FC | Prix de revient calculé → alimentation marge | Batch | **Haute** | RG-AA-07 |
| 9 | SC | AA | Proposition de commande MRP → commande fournisseur | Batch | **Haute** | RG-SC-02, RG-AA-03 |
| 10 | SC | SE | Ordre de réappro magasin → transfert inter-sites | Événementiel | **Haute** | RG-SC-06 |
| 11 | SC | GC | Confirmation expédition → mise à jour statut commande | Temps réel | **Haute** | RG-SC-05 |
| 12 | SE | FC | Mouvement de stock → écriture de variation | Événementiel | **Haute** | RG-SE-13, RG-FC-04 |
| 13 | SE | FC | Écart inventaire validé → écriture de régularisation | Événementiel | **Haute** | RG-SE-07 |
| 14 | SE | GC | Stock disponible → disponibilité à la vente (ATP) | Temps réel | **Haute** | RG-SE-01 |
| 15 | SE | SC | Positions de stock → données d'entrée MRP | Batch/Temps réel | **Haute** | RG-SC-02 |
| 16 | CM | GC | Palier fidélité → remise applicable au client | Temps réel | **Haute** | RG-CM-06, RG-GC-03 |
| 17 | FC | AA | Statut paiement fournisseur → mise à jour encours | Événementiel | Moyenne | RG-FC-13 |
| 18 | FC | GC | Avoir émis → mise à jour statut retour | Événementiel | Moyenne | RG-FC-04 |

### Flux référentiels (données maîtres)

| # | Source (maître) | Cibles | Donnée référentielle | Fréquence synchro | Criticité |
|:-:|----------------|--------|---------------------|:------------------|:---------:|
| 19 | CM | GC, FC, RB | Fiche client (identification, type, catégorie) | Temps réel | **Haute** |
| 20 | ERP/PIM | GC, AA, SC, SE, FC, RB | Article (SKU, libellé, famille, prix de base) | Temps réel | **Haute** |
| 21 | AA | SC, FC, SE, RB | Fournisseur (identification, conditions) | Événementiel | **Haute** |
| 22 | SE | GC, SC, RB | Sites de stockage (entrepôts, magasins, capacités) | Batch | Moyenne |
| 23 | GC | CM, RB | Tarifs et promotions | Temps réel | **Haute** |
| 24 | FC | Tous | Plan de comptes, axes analytiques | Batch | Moyenne |

### Flux avec systèmes externes

| # | ERP (domaine) | Système externe | Données échangées | Direction | Type | Criticité |
|:-:|:-------------|:---------------|:-----------------|:---------:|:-----|:---------:|
| 25 | GC | Cegid Y2 (POS) | Prix, promos, stocks ↓ / Ventes, mvts ↑ | Bidirectionnel | Temps réel | **Haute** |
| 26 | GC | Salesforce Commerce Cloud | Commandes web, prix, stock | Bidirectionnel | Temps réel | **Haute** |
| 27 | SE | Manhattan WMS | Ordres prépa, stocks, confirmations | Bidirectionnel | Temps réel | **Haute** |
| 28 | CM | Salesforce CRM | Fiches client, fidélité, tickets | Bidirectionnel | Temps réel | **Haute** |
| 29 | CM | Salesforce Marketing Cloud | Segments, campagnes, résultats | Bidirectionnel | Batch | Moyenne |
| 30 | RB | Tableau / Snowflake | Extractions BI, KPI, dimensions | Sortant | Batch/Near RT | **Haute** |
| 31 | FC | Plateformes bancaires (EBICS/SWIFT) | Virements, relevés, rapprochements | Bidirectionnel | Batch | **Haute** |
| 32 | FC | Chorus Pro / SDI / SII | Factures électroniques | Sortant | Événementiel | **Haute** |
| 33 | Tous | MuleSoft ESB | Hub de routage de tous les flux | Bidirectionnel | Tous | **Haute** |
| 34 | Tous | Apache Kafka | Événements métier (pub/sub) | Bidirectionnel | Événementiel | **Haute** |
| 35 | Batch | Control-M | Ordonnancement des traitements batch | Entrant | Batch | **Haute** |
| 36 | ERP/PIM | Akeneo PIM | Données produit enrichies (media, descriptions) | Bidirectionnel | Batch | Moyenne |

## Diagramme de flux

```
                            ┌─────────────────────────────────┐
                            │        SYSTÈMES EXTERNES        │
                            │  Cegid POS · SFCC · Salesforce  │
                            │  Manhattan · Akeneo · Tableau    │
                            │  Banques · Chorus/SDI/SII       │
                            └────────┬──────────┬─────────────┘
                                     │          │
                          ┌──────────┴──────────┴──────────┐
                          │    MuleSoft ESB + Kafka         │
                          │    (Hub d'intégration)          │
                          └──┬───┬───┬───┬───┬───┬───┬─────┘
                             │   │   │   │   │   │   │
              ┌──────────────┘   │   │   │   │   │   └──────────────┐
              ▼                  ▼   │   ▼   │   ▼                  ▼
     ┌────────────┐    ┌─────────┐  │ ┌────┐ │ ┌──────────┐  ┌──────────┐
     │     GC     │◄──►│   AA    │  │ │ FC │ │ │    CM    │  │    RB    │
     │ Gestion    │    │ Achats  │  │ │Fin.│ │ │  CRM &   │  │Reporting │
     │commerciale │    │ Appro.  │  │ │Cpta│ │ │Marketing │  │   & BI   │
     └──┬──┬──┬───┘    └──┬──┬───┘  │ └─┬──┘ │ └────┬─────┘  └────┬─────┘
        │  │  │           │  │      │   │    │      │              │
        │  │  └───────────┼──┼──────┼───┼────┼──────┘              │
        │  │              │  │      │   │    │     (fidélité→prix)  │
        │  └──────────────┼──┘      │   │    │                     │
        │  (retours)      │(réc.)   │   │    │                     │
        │                 │         │   │    │                     │
        │    ┌────────────┘    ┌────┘   │    │                     │
        │    │  (MRP→cde)      │        │    │                     │
        │    ▼                 ▼        │    │                     │
     ┌──▼────────┐       ┌──────────┐   │    │                     │
     │    SC     │◄─────►│    SE    │◄──┘    │                     │
     │Supply Ch. │       │ Stocks & │        │                     │
     │Logistique │       │Entrepôts │        │                     │
     └───────────┘       └──────────┘        │                     │
                              ▲              │                     │
                              │              │                     │
                              └──────────────┘                     │
                              (stock magasin)                      │
                                                                   │
     ◄─── Tous les domaines alimentent le Reporting ──────────────►│
```

## Dépendances cachées identifiées

| # | Dépendance | Domaines | Pourquoi elle est cachée | Impact |
|:-:|-----------|----------|-------------------------|--------|
| 1 | **Fidélité → Tarification** | CM → GC | Le palier fidélité d'un client (CM) détermine une remise applicable sur ses commandes (GC), mais cette dépendance n'est pas formalisée comme un flux de données | Un client peut se voir appliquer un mauvais prix si le palier fidélité n'est pas synchronisé en temps réel |
| 2 | **Valorisation stock → Marge commerciale** | SE → FC → GC | La méthode de valorisation (FIFO/CUMP/standard) choisie en SE impacte le calcul de marge en FC, qui impacte la rentabilité commerciale vue en GC/RB | Des décisions commerciales basées sur des marges calculées différemment selon la méthode de valorisation |
| 3 | **Réglementation promotionnelle → Campagnes marketing** | GC → CM | Les contraintes réglementaires sur les soldes (dates, prix barré) définies dans GC doivent être respectées par les campagnes créées dans CM/Salesforce | Risque juridique si une campagne marketing enfreint la réglementation des soldes |
| 4 | **Capacité entrepôt → MRP → Achats** | SE → SC → AA | La capacité restante des entrepôts (SE) est une contrainte du MRP (SC) qui impacte les propositions de commande (AA), mais cette chaîne traverse 3 domaines | Surstock physiquement impossible à stocker si la capacité n'est pas prise en compte |
| 5 | **Devise fournisseur → Trésorerie** | AA → FC | Les commandes en devise étrangère (AA) génèrent des besoins de couverture de change (FC), mais cette dépendance n'est pas tracée comme un flux | Exposition au risque de change non anticipée |
| 6 | **Consentements RGPD → Tous les traitements** | CM → Tous | Les consentements collectés en CM conditionnent ce que les autres domaines peuvent faire avec les données personnelles (marketing, fidélité, analytics) | Non-conformité RGPD si un domaine traite des données sans consentement valide |
| 7 | **Évaluation fournisseur → Propositions MRP** | AA → SC | La qualification/scoring d'un fournisseur (AA) devrait influencer le choix du fournisseur dans les propositions MRP (SC), mais le MRP ne consomme pas cette donnée | Commandes passées auprès de fournisseurs sous-performants |
