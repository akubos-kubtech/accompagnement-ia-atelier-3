# Supply Chain & Logistique - Spécifications fonctionnelles

> Spécifications extraites du CDC section 3.1.3 — Logistique et gestion de la chaîne d'approvisionnement.

## Périmètre fonctionnel

Le domaine Supply Chain & Logistique couvre le pilotage de bout en bout des flux physiques, depuis l'approvisionnement fournisseur jusqu'à la livraison client finale. Il inclut :

- **Planification de la demande** : prévision des ventes, saisonnalité, tendances, événements promotionnels
- **Planification des approvisionnements** : calcul des besoins nets (MRP), propositions de commandes
- **Gestion des transports** : planification, optimisation tournées, suivi temps réel, analyse coûts
- **Réapprovisionnement magasins** : suggestions optimisées, cross-docking, profils de vente locaux
- **Commerce international** : gestion documentaire douanière, calcul droits et taxes
- **Pilotage performance** : tableau de bord temps réel, alertes automatiques

**Volumétrie** : 7 entrepôts, 320 000 m², 15 millions de commandes/an. Transports = 6,5% du CA.

## Processus métier

### P1 — Planification de la demande
1. Analyse des historiques de vente
2. Intégration de la saisonnalité et des tendances marché
3. Prise en compte des événements promotionnels planifiés
4. Application d'algorithmes de prévision paramétrables par famille de produits
5. Intégration de facteurs exogènes
6. Génération des prévisions de vente

### P2 — Calcul des besoins nets (MRP)
1. Collecte des prévisions de vente validées
2. Intégration des stocks disponibles et commandes en cours
3. Prise en compte des réservations et seuils de sécurité
4. Calcul des besoins nets par article
5. Intégration des contraintes (délai fournisseur, capacité stockage, coûts transport)
6. Génération des propositions de commandes fournisseurs

### P3 — Gestion des transports
1. Planification des transports (amont fournisseur et aval client)
2. Optimisation des tournées de livraison
3. Affectation aux transporteurs
4. Suivi des expéditions en temps réel
5. Analyse des coûts par transporteur et par flux

### P4 — Réapprovisionnement magasins
1. Analyse de la capacité de stockage point de vente
2. Prise en compte du profil de vente local
3. Intégration des contraintes de livraison
4. Génération des suggestions de réapprovisionnement optimisées
5. Gestion du cross-docking pour articles à forte rotation

### P5 — Logistique commerce international
1. Gestion documentaire douanière intégrée au flux logistique
2. Classification des produits par nomenclature douanière
3. Calcul automatique des droits et taxes selon accords commerciaux
4. Fiabilisation des coûts de revient

## Cas d'usage principaux

| Ref | Cas d'usage | Acteurs | Contexte |
|-----|------------|---------|----------|
| CU-SC-01 | Générer les prévisions de vente pour la saison prochaine | Planificateur, Système prévision | Planification |
| CU-SC-02 | Lancer un calcul MRP et générer les propositions de commandes | Approvisionneur, MRP | Réapprovisionnement |
| CU-SC-03 | Optimiser les tournées de livraison aval | Responsable transport, Transporteurs | Transport |
| CU-SC-04 | Suivre une expédition en temps réel | Client, Service client, Transporteur | Livraison |
| CU-SC-05 | Réapprovisionner un magasin en cross-docking | Logisticien, Magasin | Flux tendu |
| CU-SC-06 | Calculer les droits de douane d'un conteneur import | Acheteur, Douanes | Commerce international |
| CU-SC-07 | Piloter la performance supply chain via tableau de bord temps réel | Direction supply chain | Pilotage |
