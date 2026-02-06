# Finance & Comptabilité - Spécifications fonctionnelles

> Spécifications extraites du CDC section 3.1.4 — Gestion financière et comptable (normes multi-pays).

## Périmètre fonctionnel

Le domaine Finance & Comptabilité couvre l'ensemble des fonctions financières et comptables pour un groupe international opérant dans 15 pays avec 8 devises. Il inclut :

- **Comptabilité générale** : plan de comptes groupe harmonisé, multi-sociétés, génération automatique d'écritures
- **Comptabilité auxiliaire** : comptes clients (encours, encaissements, recouvrement) et fournisseurs (three-way matching, acomptes)
- **TVA multi-pays** : régimes de TVA, territorialité, autoliquidation, TVA sur marge, OSS e-commerce
- **Facturation électronique** : Factur-X, plateformes de dématérialisation, e-reporting B2C
- **Comptabilité analytique** : rentabilité par canal, famille produit, zone géographique, magasin
- **Gestion budgétaire** : planification, suivi des écarts, contrôle de gestion
- **Trésorerie multi-devises** : positions, prévisions de flux, couvertures de change, rapprochement bancaire

**Volumétrie** : 15 pays, 8 devises, consolidation IFRS groupe.

## Processus métier

### P1 — Comptabilité générale multi-sociétés
1. Paramétrage du plan de comptes groupe harmonisé
2. Déclinaison selon les spécificités locales par entité juridique
3. Génération automatique des écritures à partir des flux opérationnels
4. Gestion des exercices comptables par entité
5. Consolidation aux normes IFRS pour le reporting groupe

### P2 — Comptabilité auxiliaire clients
1. Suivi des encours clients
2. Gestion des encaissements multi-modes de paiement
3. Lettrage automatique
4. Relance et recouvrement des impayés

### P3 — Comptabilité auxiliaire fournisseurs
1. Rapprochement three-way matching (commande / réception / facture)
2. Gestion des acomptes et avoirs fournisseurs
3. Respect des délais de paiement négociés
4. Préparation des campagnes de règlement

### P4 — TVA et conformité fiscale
1. Gestion des différents régimes de TVA par pays
2. Application des règles de territorialité
3. Gestion de l'autoliquidation et de la TVA sur marge
4. Génération automatique des déclarations de TVA
5. Support du régime OSS pour le e-commerce transfrontalier européen

### P5 — Facturation électronique
1. Émission de factures au format Factur-X
2. Réception et intégration de factures structurées
3. Transmission via plateformes de dématérialisation agréées
4. E-reporting des transactions B2C

### P6 — Comptabilité analytique et contrôle de gestion
1. Définition des axes d'analyse (canal, famille produit, zone géo, magasin)
2. Ventilation des charges indirectes
3. Calcul des coûts complets
4. Analyse de marge et aide au pricing
5. Élaboration budgétaire collaborative
6. Suivi en cours d'année et calcul des écarts

### P7 — Trésorerie multi-devises
1. Suivi des positions de trésorerie par devise
2. Prévisions de flux de trésorerie
3. Gestion des couvertures de change
4. Rapprochement bancaire automatisé (EBICS / SWIFT)

## Cas d'usage principaux

| Ref | Cas d'usage | Acteurs | Contexte |
|-----|------------|---------|----------|
| CU-FC-01 | Générer automatiquement les écritures comptables d'une vente omnicanale | Système, Comptabilité | Flux opérationnel |
| CU-FC-02 | Rapprocher une facture fournisseur avec la commande et la réception (three-way) | Comptable fournisseurs | Cycle achats |
| CU-FC-03 | Produire la déclaration de TVA pour une filiale européenne | Comptable fiscal | Conformité |
| CU-FC-04 | Émettre une facture électronique Factur-X via plateforme agréée | Facturation | Obligation légale |
| CU-FC-05 | Analyser la rentabilité par canal de vente et par famille de produits | Contrôleur de gestion | Pilotage |
| CU-FC-06 | Effectuer le rapprochement bancaire automatisé multi-banques | Trésorier | Trésorerie |
| CU-FC-07 | Consolider les comptes groupe aux normes IFRS | Direction financière | Reporting groupe |
| CU-FC-08 | Mettre en place une couverture de change sur une devise d'achat | Trésorier | Gestion des risques |
