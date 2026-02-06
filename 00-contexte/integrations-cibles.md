# Cartographie des intégrations cibles

> Vue consolidée des systèmes conservés et de leurs interfaces attendues avec le nouvel ERP. Base de travail pour l'exercice de cartographie des flux inter-modules.

## Principe directeur

Le nouvel ERP remplace **SAP ECC 6.0** (et SAP APO/BW). Les autres briques applicatives sont **conservées** et intégrées via l'**ESB MuleSoft** existant ou via API directes.

## Systèmes conservés et interfaces cibles

### Salesforce Commerce Cloud (e-commerce)

| Direction | Données échangées | Mode | SLA cible |
|-----------|------------------|------|-----------|
| E-commerce → ERP | Commandes clients web | Temps réel (API) | < 2s |
| ERP → E-commerce | Stocks disponibles (tous sites) | Temps réel (API) | < 2s |
| ERP → E-commerce | Prix, promotions, conditions tarifaires | Temps réel / événementiel | < 1 min |
| ERP → E-commerce | Statut commande, tracking livraison | Temps réel (API) | < 2s |
| Bidirectionnel | Données clients | Synchronisation continue | < 5 min |

### Cegid Retail Y2 (caisses magasins France)

| Direction | Données échangées | Mode | SLA cible |
|-----------|------------------|------|-----------|
| ERP → Caisses | Prix, promotions, disponibilité stock | Descente temps réel | < 1 min |
| ERP → Caisses | Programme fidélité (solde points, palier) | Temps réel | < 2s |
| Caisses → ERP | Ventes, encaissements | Quasi temps réel | < 5 min |
| Caisses → ERP | Mouvements de stock magasin | Quasi temps réel | < 5 min |

### Salesforce CRM (Sales & Service)

| Direction | Données échangées | Mode | SLA cible |
|-----------|------------------|------|-----------|
| ERP → CRM | Référentiel client maître (ERP = master) | Bidirectionnel | < 5 min |
| ERP → CRM | Historique commandes, retours, avoirs | Événementiel | < 5 min |
| ERP → CRM | Données fidélité (points, palier) | Temps réel | < 5 min |
| CRM → ERP | Tickets réclamation, interactions client | Événementiel | < 5 min |

### Salesforce Marketing Cloud

| Direction | Données échangées | Mode | SLA cible |
|-----------|------------------|------|-----------|
| ERP → Marketing Cloud | Segments clients (export) | Batch / événementiel | Quotidien à horaire |
| ERP → Marketing Cloud | Données comportementales pour ciblage | Connecteurs natifs | Horaire |
| Marketing Cloud → ERP | Résultats campagnes | Batch | Quotidien |

### Manhattan Associates SCALE (WMS France)

| Direction | Données échangées | Mode | SLA cible |
|-----------|------------------|------|-----------|
| ERP → WMS | Ordres de préparation (picking) | Temps réel (API) | < 1 min |
| ERP → WMS | Réceptions attendues (avis d'arrivée) | Événementiel | < 5 min |
| WMS → ERP | Confirmations d'expédition | Temps réel | < 1 min |
| WMS → ERP | Stocks (synchronisation + réconciliation) | Temps réel + batch réconciliation | < 1 min |
| WMS → ERP | Mouvements de stock entrepôt | Temps réel | < 1 min |

### Tableau Software + Snowflake (BI / Analytics)

| Direction | Données échangées | Mode | SLA cible |
|-----------|------------------|------|-----------|
| ERP → Tableau/Snowflake | Données transactionnelles tous domaines | API / connecteurs standards | Horaire à quasi temps réel |
| ERP → Tableau/Snowflake | Données de référence (articles, clients, fournisseurs) | Événementiel | < 1h |
| ERP → Tableau/Snowflake | KPI prioritaires | Quasi temps réel | < 15 min |

### Akeneo PIM (référentiel produit)

| Direction | Données échangées | Mode | SLA cible |
|-----------|------------------|------|-----------|
| PIM → ERP | Données produit enrichies (descriptions, attributs, médias) | Événementiel | < 1h |
| ERP → PIM | Données produit de base (SKU, prix, stock) | Événementiel | < 1h |

### Banques (EBICS / SWIFT)

| Direction | Données échangées | Mode | SLA cible |
|-----------|------------------|------|-----------|
| ERP → Banques | Ordres de paiement (virements fournisseurs, salaires) | Fichiers (EBICS) | Quotidien |
| Banques → ERP | Relevés bancaires | Fichiers (EBICS) | Quotidien |
| Bidirectionnel | Rapprochement automatisé | Batch | Quotidien |

### Transporteurs

| Direction | Données échangées | Mode | SLA cible |
|-----------|------------------|------|-----------|
| ERP → Transporteurs | Ordres d'expédition | API / EDI | Temps réel |
| Transporteurs → ERP | Tracking, statut livraison | API | Temps réel |
| Transporteurs → ERP | Preuves de livraison | API / fichier | J+1 |

### Fournisseurs (EDI)

| Direction | Données échangées | Mode | SLA cible |
|-----------|------------------|------|-----------|
| ERP → Fournisseurs | Commandes d'achat | EDI (EDIFACT) / API | Événementiel |
| Fournisseurs → ERP | Accusés de réception, avis d'expédition (ASN) | EDI / API | Événementiel |
| Fournisseurs → ERP | Factures fournisseurs | EDI / Factur-X | Événementiel |

### Plateformes fiscales (facturation électronique)

| Direction | Données échangées | Mode | SLA cible |
|-----------|------------------|------|-----------|
| ERP → Plateforme (ex: Chorus Pro) | Factures émises (Factur-X) | API | Événementiel |
| Plateforme → ERP | Factures reçues (structurées) | API | Événementiel |
| ERP → Plateforme | E-reporting B2C | API | Selon réglementation |

## Schéma d'intégration simplifié

```
                          ┌─────────────┐
                          │  Akeneo PIM │
                          └──────┬──────┘
                                 │
┌──────────┐   ┌──────────┐   ┌─┴───────────┐   ┌──────────────┐
│ Cegid POS│◄─►│ SF ComCld│◄─►│  NOUVEL ERP │◄─►│ Manhattan WMS│
└──────────┘   └──────────┘   │  (Phoenix)  │   └──────────────┘
                               │             │
┌──────────┐   ┌──────────┐   │             │   ┌──────────────┐
│ SF CRM   │◄─►│SF MktCld │◄─►│             │◄─►│ Transporteurs│
└──────────┘   └──────────┘   │             │   └──────────────┘
                               │             │
┌──────────┐   ┌──────────┐   │             │   ┌──────────────┐
│Tableau/SF│◄──│          │◄──│             │──►│ Fourniss. EDI│
└──────────┘   │ MuleSoft │   │             │   └──────────────┘
               │   ESB    │◄─►│             │
┌──────────┐   │          │   │             │   ┌──────────────┐
│ Banques  │◄─►│          │◄─►│             │──►│ Plateformes  │
└──────────┘   └──────────┘   └─────────────┘   │ fiscales     │
                                                 └──────────────┘
```

## Points de vigilance pour l'atelier

- **ERP = référentiel maître** pour les clients (vs Salesforce CRM) et les articles (vs Akeneo PIM) — la direction des flux est critique
- **Temps réel vs batch** : identifier quels flux tolèrent du batch et lesquels exigent du temps réel
- **MuleSoft comme hub** : l'ESB est conservé, mais les interfaces point-à-point historiques doivent être rationalisées
- **WMS multi-pays** : Manhattan en France seulement — les autres pays ont des WMS différents, non documentés dans le CDC
