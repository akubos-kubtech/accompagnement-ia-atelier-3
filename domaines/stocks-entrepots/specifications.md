# Stocks & Entrepôts (WMS) - Spécifications fonctionnelles

> Spécifications extraites du CDC section 3.1.5 — Gestion des stocks et des entrepôts.

## Périmètre fonctionnel

Le domaine Stocks & Entrepôts couvre la gestion du stock physique à tous les niveaux du réseau, de la visibilité temps réel à la valorisation comptable. Il inclut :

- **Vision stock unifiée** : visibilité temps réel sur entrepôts centraux, plateformes régionales, magasins et stock en transit
- **Modèle de données stock** : articles standards, à variantes, sérialisés, par lot, à date de péremption
- **Statuts stock** : disponible, réservé, en contrôle qualité, en attente de retour, à détruire
- **Gestion des emplacements** : structure des sites de stockage, règles d'affectation
- **Mouvements de stock** : traçabilité exhaustive, historique horodaté
- **Inventaires** : annuel complet, tournant, permanent
- **Intégration WMS** : échange temps réel avec Manhattan Associates (France)
- **Valorisation** : FIFO, CUMP, prix standard, analyse par ancienneté

**Volumétrie** : valeur de stock ~280 M€, objectif fiabilité >98%.

## Processus métier

### P1 — Gestion du stock multi-sites
1. Suivi en temps réel des niveaux de stock par site (entrepôt, plateforme, magasin, transit)
2. Qualification de chaque unité de stock par statut (disponible, réservé, contrôle qualité, etc.)
3. Gestion des différentes typologies (standard, variante, sérialisé, lot, péremption)
4. Consolidation de la vue stock réseau

### P2 — Mouvements de stock
1. Enregistrement du mouvement (entrée, sortie, transfert, ajustement)
2. Horodatage et association à l'origine (commande, retour, inventaire, etc.)
3. Traçabilité complète pour rappels produits éventuels
4. Alimentation des analyses de rotation

### P3 — Inventaire
1. Planification de l'inventaire (annuel complet, tournant par zone/famille, permanent)
2. Génération des feuilles de comptage
3. Saisie des comptages et calcul des écarts
4. Workflow de validation des écarts
5. Génération automatique des écritures de régularisation comptable

### P4 — Intégration WMS
1. Synchronisation temps réel des stocks entre ERP et WMS (Manhattan Associates)
2. Transmission des ordres de préparation (picking)
3. Réception des confirmations d'expédition
4. Réconciliation automatique des écarts ERP/WMS

### P5 — Valorisation des stocks
1. Valorisation selon la méthode comptable applicable (FIFO, CUMP, prix standard)
2. Analyse de la valeur par ancienneté (identification articles dormants)
3. Pilotage des actions de déstockage
4. Génération automatique des écritures de variation de stock

## Cas d'usage principaux

| Ref | Cas d'usage | Acteurs | Contexte |
|-----|------------|---------|----------|
| CU-SE-01 | Consulter le stock disponible d'un article sur l'ensemble du réseau en temps réel | Vendeur, E-commerce, Supply chain | Disponibilité omnicanale |
| CU-SE-02 | Enregistrer un mouvement de stock suite à une réception fournisseur | Réceptionnaire, WMS | Entrée marchandise |
| CU-SE-03 | Réaliser un inventaire tournant sur une zone d'entrepôt | Magasinier, Responsable stock | Fiabilité stock |
| CU-SE-04 | Synchroniser les stocks ERP-WMS après une préparation de commande | ERP, WMS Manhattan | Intégration |
| CU-SE-05 | Identifier les articles dormants et lancer une action de déstockage | Contrôleur de gestion, Commercial | Optimisation |
| CU-SE-06 | Tracer un lot produit pour un rappel qualité | Qualité, Logistique | Traçabilité |
| CU-SE-07 | Valoriser le stock de fin de mois en CUMP | Comptabilité, Gestion stocks | Clôture comptable |
