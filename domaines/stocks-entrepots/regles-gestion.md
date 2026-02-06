# Stocks & Entrepôts (WMS) - Règles de gestion

> Règles métier, contraintes et validations propres à ce domaine (CDC section 3.1.5).

## Règles de gestion

| ID | Règle | Description | Entité(s) concernée(s) | Criticité |
|----|-------|-----------|----------------------|-----------|
| RG-SE-01 | Visibilité temps réel | Le stock est visible en temps réel à tous les niveaux du réseau (entrepôts, plateformes, magasins, transit) | Stock | Haute |
| RG-SE-02 | Typologie articles | Le modèle de données supporte : articles standards, à variantes, sérialisés, par lot, à date de péremption | Article stock | Haute |
| RG-SE-03 | Statuts de stock | Chaque unité de stock est qualifiée : disponible, réservé, en contrôle qualité, en attente de retour, à détruire | Stock | Haute |
| RG-SE-04 | Traçabilité exhaustive | Chaque mouvement est horodaté et associé à son origine ; l'historique complet est conservé pour les rappels produits | Mouvement de stock | Haute |
| RG-SE-05 | Objectif fiabilité 98% | Le taux de fiabilité des stocks doit être supérieur à 98% | Inventaire | Haute |
| RG-SE-06 | Inventaire multi-modalités | Le système supporte l'inventaire annuel complet, tournant (par zone ou famille) et permanent | Inventaire | Moyenne |
| RG-SE-07 | Écritures auto inventaire | Les écarts d'inventaire validés génèrent automatiquement les écritures de régularisation comptable | Feuille de comptage, Écriture | Haute |
| RG-SE-08 | Intégration WMS temps réel | L'échange avec le WMS Manhattan Associates est en temps réel : stocks, ordres de préparation, confirmations d'expédition | Stock, Mouvement | Haute |
| RG-SE-09 | Réconciliation ERP/WMS | Des mécanismes de réconciliation automatique corrigent les écarts entre ERP et WMS | Stock | Haute |
| RG-SE-10 | ERP comme WMS magasin | Pour les magasins non équipés de WMS, l'ERP constitue l'outil principal de gestion des emplacements | Emplacement, Stock | Moyenne |
| RG-SE-11 | Multi-méthodes valorisation | La valorisation supporte FIFO, CUMP et prix standard selon les besoins comptables | Valorisation stock | Haute |
| RG-SE-12 | Analyse ancienneté | L'analyse de la valeur de stock par ancienneté identifie les articles dormants pour piloter le déstockage | Valorisation stock | Moyenne |
| RG-SE-13 | Écritures variation auto | Les écritures de variation de stock sont générées automatiquement | Valorisation stock, Écriture | Haute |

## Contraintes métier

- **Valeur de stock** : ~280 M€ — la fiabilité et la valorisation des stocks sont des enjeux financiers majeurs
- **WMS existant** : Manhattan Associates en France — l'intégration bidirectionnelle temps réel est non négociable
- **Traçabilité** : obligation de traçabilité complète pour les rappels produits (lots, numéros de série)
- **Multi-sites** : entrepôts centraux + plateformes régionales + magasins + transit — vue unifiée indispensable
