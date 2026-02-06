# Référentiels transverses consolidés

> Vue croisée des entités partagées entre domaines, extraite de l'analyse des 7 domaines (Phase 3) et réconciliée en Phase 5. Base de travail pour la détection des zones de friction et la réconciliation inter-domaines.

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
| **Domaine maître** | Transversal (ERP + Akeneo PIM pour les données enrichies) |
| **Domaines consommateurs** | Gestion commerciale, Achats, Supply Chain, Stocks & Entrepôts, Finance, Reporting |
| **Volumétrie** | 218 000 références actives (SKU) |
| **Typologies** | Standard, à variantes, sérialisé, par lot, à date de péremption |
| **Données partagées** | SKU, libellé, catégorie/famille, prix de base, attributs stock, fournisseur principal, nomenclature douanière |
| **Points de friction potentiels** | Partage des responsabilités ERP/Akeneo PIM, cohérence prix entre domaines commercial/achats/finance, gestion des variantes |

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
| **Article** | R | R | R | R | R | | R |
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
| **Réception** | | **M** | | R | R | | R |
| **Retour / Avoir** | **M** | | | R | R | | R |
| **Magasin / Point de vente** | R | | R | | R | R | R |
| **Prix de revient** | | **M** | R | R | | | R |
| **Lot / N° de série** | R | R | | | **M** | | |

> **M** = Maître (domaine qui crée/gère l'entité) | **R** = Reader/consommateur

### Entités ajoutées en Phase 5 (réconciliation)

| Entité | Justification |
|--------|--------------|
| **Réception** | Partagée entre AA (maître), SE (mise en stock), FC (three-way matching) — absente de la matrice initiale |
| **Retour / Avoir** | Partagée entre GC (maître), FC (écritures), SE (réintégration stock) |
| **Magasin / Point de vente** | Référencée dans GC (canal vente), SC (réappro), SE (stock magasin), CM (fidélité) — entité transverse non identifiée initialement |
| **Prix de revient** | Partagé entre AA (calcul), SC (optimisation transport), FC (calcul marge) |
| **Lot / N° de série** | Porté par SE (maître), avec implications pour GC (retours) et AA (traçabilité fournisseur) |

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

### 6. Retour → Stock → Finance (ajouté Phase 5)
```
Retour client (GC) → Contrôle qualité (SE) → Réintégration ou destruction (SE) → Avoir (GC) → Écriture comptable (FC)
```

## Incohérences inter-domaines identifiées

### Incohérences de nommage

| Entité dans un domaine | Entité dans un autre domaine | Nature de l'incohérence |
|------------------------|----------------------------|------------------------|
| **Article** (GC, AA, SC) | **Article stock** (SE) | Même référentiel, noms différents — SE ajoute un qualificatif « stock » qui peut prêter à confusion |
| **Commande** (CM) | **Commande client** (GC) | CM fait référence à « Commande » dans ses entités partagées, GC utilise « Commande client » — à unifier |
| **Stock** (GC, AA, SC, FC) | **Stock** (SE) | Même nom mais granularité différente : vue agrégée (disponibilité) pour GC/SC vs vue détaillée (emplacement, statut, lot) pour SE |
| **Facture** (FC) | Non modélisée dans GC/AA | FC modélise la facture (émise/reçue), mais GC ne la liste pas dans ses entités — pourtant les ventes génèrent des factures |
| **Entrepôt** (AA, SC) | **Site de stockage** (SE) | Même concept, noms différents — SE utilise un terme plus générique qui englobe entrepôts, plateformes et magasins |

### Références croisées asymétriques

| Domaine A | Mentionne | Domaine B | Ne mentionne pas |
|-----------|-----------|-----------|-------------------|
| GC | Mouvement de stock → SE | SE | GC comme consommateur de mouvements |
| SC | Commande fournisseur → AA | AA | SC comme consommateur de commandes |
| CM | Promotion → GC | GC | CM comme consommateur de promotions/tarifs |
| SE | Expédition → SC | SC | SE comme contributeur d'expéditions |
| AA | Prix de revient → FC | FC | Prix de revient comme entrée du calcul de marge |

### Conflits ou chevauchements entre règles de gestion

| Règle A | Règle B | Nature du conflit |
|---------|---------|-------------------|
| **RG-GC-03** (tarification multi-niveaux) | **RG-CM-08** (cohérence promo/tarif) | Les deux traitent de la cohérence des prix/promotions mais depuis des angles différents — pas de règle unique de résolution |
| **RG-GC-05** (latence ventes ≤ 5 min) | **ENF** (latence synchro stock ≤ 5 min normal, ≤ 15 min pic) | La remontée des ventes (5 min) peut entrer en conflit avec la latence stock en pic (15 min) — risque de stock vendu mais pas encore décrémenté |
| **RG-SE-07** (écritures auto inventaire) | **RG-FC-04** (génération auto écritures) | Même mécanisme décrit depuis 2 domaines sans règle de gouvernance unique — qui déclenche, qui contrôle ? |
| **RG-SE-11** (valorisation FIFO/CUMP/standard) | Aucune règle FC correspondante | SE mentionne « selon les besoins comptables » mais FC n'explicite pas quelle méthode par entité juridique/pays |
| **RG-SE-08** (intégration WMS Manhattan) | **RG-SC-11** (intégration transporteurs) | SE ne mentionne que Manhattan Associates (France) — les WMS des filiales internationales ne sont pas documentés |
| **RG-GC-06** (intégration POS bidirectionnelle) | **RG-CM-04** (fidélité temps réel sur caisses) | Deux règles de deux domaines décrivent l'intégration POS sans cohérence croisée — le POS doit gérer les deux simultanément |

### Entités implicites non modélisées

| Entité implicite | Où elle apparaît | Problème |
|-----------------|-----------------|---------|
| **Magasin / Point de vente** | RG-GC-06 (POS), RG-SC-06 (réappro), RG-SE-10 (WMS magasin), RG-CM-04 (fidélité) | Entité transverse utilisée par 4 domaines, jamais modélisée explicitement dans aucun fichier entites.md |
| **Canal de vente** | RG-GC-11 (omnicanal), FC (axe analytique), CM (campagnes) | Concept structurant pour le retail omnicanal, absent comme entité formelle |
| **Devise / Taux de change** | RG-AA-05 (commande en devise), RG-FC-12 (trésorerie 8 devises) | Référentiel critique pour le multi-pays, non modélisé |
| **Société / Entité juridique** | FC (plan de comptes par société), SE (valorisation par entité) | Structure organisationnelle implicite, jamais définie |

## Zones de friction anticipées (consolidées)

| # | Zone | Domaines impactés | Nature du risque | Sévérité |
|:-:|------|------------------|-----------------|:--------:|
| 1 | Référentiel client unique | CM, GC, FC | ERP maître vs Salesforce CRM — qui écrit quoi ? Déduplication cross-canal | **Haute** |
| 2 | Référentiel article | GC, AA, SC, SE, FC | Partage responsabilités ERP/Akeneo PIM. Nommage « Article » vs « Article stock ». Attributs de base vs enrichis | **Haute** |
| 3 | Disponibilité stock temps réel | GC, SE, SC | Promesse de stock omnicanal vs stock physique réel. Latence ERP↔WMS. Conflit latence ventes (5 min) vs stock en pic (15 min) | **Haute** |
| 4 | Prix de revient complet | AA, FC, SC | Calcul intégrant prix achat + transport + douane + taxes — pas de règle FC explicite pour consommer le prix de revient AA | **Haute** |
| 5 | Cohérence tarifaire | GC, CM | Promotions marketing (CM) vs conditions tarifaires ERP (GC) — deux domaines, deux règles, pas d'arbitrage défini | **Haute** |
| 6 | Valorisation stock multi-méthodes | SE, FC | FIFO/CUMP/standard « selon les besoins comptables » — mais aucune règle FC ne définit le choix par pays/entité | **Moyenne** |
| 7 | Three-way matching | AA, SE, FC | Rapprochement commande/réception/facture — gestion des écarts et tolérances entre 3 domaines | **Haute** |
| 8 | Intégration WMS multi-pays | SE, SC | Manhattan en France, WMS non documentés ailleurs — interfaces hétérogènes potentielles | **Moyenne** |
| 9 | Intégration POS bidirectionnelle | GC, CM, SE | Le POS doit servir 3 domaines simultanément (ventes, fidélité, stock) — pas de vision unifiée de l'interface | **Haute** |
| 10 | Magasin comme entité transverse | GC, SC, SE, CM | Entité référencée par 4 domaines mais jamais modélisée — risque de définitions divergentes | **Moyenne** |
| 11 | Génération automatique d'écritures | SE, FC | Deux domaines décrivent le même mécanisme — absence de gouvernance sur le déclencheur et le contrôleur | **Moyenne** |
| 12 | Devise et taux de change | AA, FC | Référentiel devise implicite, critique pour le multi-pays (8 devises, 35 pays fournisseurs) | **Moyenne** |
