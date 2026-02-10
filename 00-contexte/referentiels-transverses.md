# Référentiels transverses consolidés

> Vue croisée des entités partagées entre domaines, extraite de l'analyse des 7 domaines (Phase 3). Base de travail pour la détection des zones de friction et la réconciliation inter-domaines.

## Entités maîtresses (référentiels partagés)

### Client

| Propriété | Valeur |
|-----------|--------|
| **Domaine maître** | CRM & Marketing (ERP = référentiel maître, synchronisé avec Salesforce) |
| **Domaines consommateurs** | Gestion commerciale, Finance & Comptabilité, Reporting & BI |
| **Volumétrie** | 8,5 millions de clients actifs |
| **Types** | B2C (particuliers), B2B (professionnels) |
| **Données partagées** | Identification, coordonnées, type/catégorie, historique achat, fidélité, encours financier, consentements RGPD |
| **Points de friction potentiels** | Déduplication multi-canal, synchronisation ERP↔Salesforce, cohérence des données entre vente/fidélité/finance |

### Article / Produit

| Propriété | Valeur |
|-----------|--------|
| **Domaine maître** | Gestion commerciale (ERP = référentiel maître pour les données de base). Akeneo PIM fournit les données enrichies (descriptions, attributs marketing, médias) |
| **Domaines consommateurs** | Achats, Supply Chain, Stocks & Entrepôts, Finance, Reporting |
| **Volumétrie** | 218 000 références actives (SKU) |
| **Typologies** | Standard, à variantes, sérialisé, par lot, à date de péremption |
| **Données partagées** | SKU, libellé, catégorie/famille, prix de base, attributs stock, fournisseur principal, nomenclature douanière |
| **Points de friction potentiels** | Gouvernance des attributs entre ERP (données de base : SKU, prix, catégorie) et Akeneo PIM (données enrichies : descriptions, médias), cohérence prix entre domaines commercial/achats/finance, gestion des variantes |

### Fournisseur

| Propriété | Valeur |
|-----------|--------|
| **Domaine maître** | Achats & Approvisionnement |
| **Domaines consommateurs** | Supply Chain, Finance & Comptabilité, Stocks & Entrepôts |
| **Volumétrie** | 850+ fournisseurs actifs, 35 pays |
| **Données partagées** | Identification, pays, devise, conditions commerciales, évaluation performance, encours fournisseur |
| **Points de friction potentiels** | Cohérence prix de revient entre achats et finance, qualification RSE partagée, intégration EDI multi-formats |

### Stock

| Propriété | Valeur |
|-----------|--------|
| **Domaine maître** | Stocks & Entrepôts |
| **Domaines consommateurs** | Gestion commerciale (disponibilité), Supply Chain (MRP), Finance (valorisation) |
| **Volumétrie** | ~280 M€, multi-sites (7 entrepôts + 347 magasins + transit) |
| **Données partagées** | Quantité par article/site/statut, mouvements, valorisation |
| **Points de friction potentiels** | Synchronisation ERP↔WMS, stock disponible à la vente vs stock physique, multi-méthodes de valorisation (FIFO/CUMP/standard) |

## Matrice de partage des entités

| Entité | GC | AA | SC | FC | SE | CM | RB |
|--------|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| **Client** | R | | | R | | **M** | R |
| **Article** | **M** | R | R | R | R | | R |
| **Fournisseur** | | **M** | R | R | R | | R |
| **Stock** | R | | R | R | **M** | | R |
| **Commande client** | **M** | | R | R | R | R | R |
| **Commande fournisseur** | | **M** | R | R | R | | R |
| **Tarif / Promotion** | **M** | | | | | R | R |
| **Entrepôt / Site** | | R | R | | **M** | | R |
| **Mouvement de stock** | R | R | | R | **M** | | R |
| **Écriture comptable** | | | | **M** | R | | R |
| **Facture** | R | R | | **M** | | | R |
| **Expédition** | R | | **M** | | R | | R |

> **M** = Maître (domaine qui crée/gère l'entité) | **R** = Reader/consommateur

## Flux inter-entités critiques

### 1. Cycle de vente → Finance
```
Commande client (GC) → Facture client (FC) → Écriture comptable (FC) → Mouvement stock (SE)
```

### 2. Cycle d'achat → Stock → Finance
```
Commande fournisseur (AA) → Réception (AA/SE) → Mise en stock (SE) → Three-way matching (FC) → Règlement (FC)
```

### 3. Prévision → Approvisionnement → Stock
```
Prévision de vente (SC) → Besoin net MRP (SC) → Proposition commande (SC) → Commande fournisseur (AA) → Réception → Stock (SE)
```

### 4. Client → Fidélité → Tarification
```
Client (CM) → Compte fidélité (CM) → Palier/Avantages (CM) → Remise applicable (GC) → Prix final (GC)
```

### 5. Stock → Valorisation → Comptabilité
```
Mouvement stock (SE) → Valorisation (SE) → Écriture de variation (FC) → Bilan (FC)
```

## Zones de friction anticipées

| Zone | Domaines impactés | Nature du risque |
|------|------------------|-----------------|
| Référentiel client unique | CRM, Gestion commerciale, Finance | ERP maître vs Salesforce CRM — qui écrit quoi ? Déduplication cross-canal |
| Référentiel article | Tous sauf CRM | Partage de responsabilité ERP/Akeneo PIM. Attributs de base vs enrichis |
| Disponibilité stock temps réel | GC, SE, SC | Promesse de stock pour la vente omnicanale vs stock physique réel (latence WMS) |
| Prix de revient complet | AA, FC, SC | Calcul intégrant prix achat + transport + douane + taxes — cohérence entre domaines |
| Cohérence tarifaire | GC, CM | Promotions marketing vs conditions tarifaires ERP — risque de conflit |
| Valorisation stock multi-méthodes | SE, FC | FIFO/CUMP/standard selon entité juridique — impact sur la marge calculée |
| Three-way matching | AA, SE, FC | Rapprochement commande/réception/facture — gestion des écarts et tolérances |
| Intégration WMS multi-pays | SE, SC | Manhattan en France, WMS différents ailleurs — interfaces hétérogènes |
