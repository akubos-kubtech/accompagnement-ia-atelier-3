# Gestion commerciale - Règles de gestion

> Règles métier, contraintes et validations propres à ce domaine (CDC section 3.1.1).

## Règles de gestion

| ID | Règle | Description | Entité(s) concernée(s) | Criticité |
|----|-------|-----------|----------------------|-----------|
| RG-GC-01 | Volumétrie pic commandes | Le système doit traiter jusqu'à 150 000 commandes/jour en période de pic (soldes, fêtes) sans dégradation | Commande client | Haute |
| RG-GC-02 | Lignes par commande | Une commande peut contenir de 1 à 50 lignes articles avec des modes de livraison panachés | Commande, Ligne de commande | Moyenne |
| RG-GC-03 | Tarification multi-niveaux | Le prix final résulte de l'application séquentielle : prix de base → grille B2B → promotion → remise conditionnelle → soldes | Tarif, Promotion, Ligne de commande | Haute |
| RG-GC-04 | Cohérence tarifaire centralisée | Les règles tarifaires sont gérées de manière centralisée et appliquées de manière cohérente sur tous les canaux | Tarif, Promotion | Haute |
| RG-GC-05 | Latence remontée ventes | Les ventes magasin doivent remonter dans l'ERP avec une latence maximale de 5 minutes | Commande client | Haute |
| RG-GC-06 | Intégration caisses bidirectionnelle | L'interfaçage POS-ERP est bidirectionnel : descente prix/promos/stocks, remontée ventes/mouvements | Commande, Article, Stock | Haute |
| RG-GC-07 | Validation retour | Un retour est validé selon 3 critères : délai de retour respecté, état du produit conforme, motif recevable | Retour | Moyenne |
| RG-GC-08 | Orientation post-retour | Après validation, l'article retourné est soit réintégré en stock (vendable), soit orienté vers le circuit de déstockage | Retour, Stock | Moyenne |
| RG-GC-09 | Statistiques retours | Les retours alimentent des statistiques par produit, par motif et par canal pour l'analyse qualité | Retour | Basse |
| RG-GC-10 | Suivi commandes longues | Les commandes spéciales/sur-mesure doivent être suivies de la validation du devis jusqu'à la livraison avec visibilité client | Devis, Commande client | Moyenne |
| RG-GC-11 | Parcours omnicanal | Le système doit gérer nativement les parcours web-to-store, store-to-web, click & collect et ship from store | Commande client | Haute |

## Contraintes métier

- **Soldes réglementés** : les promotions de type "soldes" doivent respecter la réglementation locale (dates, affichage prix barré, prix de référence)
- **Cohérence prix temps réel** : le prix affiché au client en magasin (via POS) doit être identique à celui calculé par l'ERP, y compris les promotions en cours
- **Multi-pays** : la tarification doit supporter les prix de base par article ET par pays
- **Modes de livraison panachés** : une même commande peut combiner livraison domicile, click & collect et retrait magasin sur des lignes différentes
