# Supply Chain & Logistique - Règles de gestion

> Règles métier, contraintes et validations propres à ce domaine (CDC section 3.1.3).

## Règles de gestion

| ID | Règle | Description | Entité(s) concernée(s) | Criticité |
|----|-------|-----------|----------------------|-----------|
| RG-SC-01 | Prévision paramétrable par famille | Les algorithmes de prévision sont paramétrables par famille de produits et intègrent des facteurs exogènes | Prévision de vente | Haute |
| RG-SC-02 | MRP intègre stocks et en-cours | Le calcul des besoins nets intègre : stocks disponibles, commandes en cours, réservations et seuils de sécurité | Besoin net | Haute |
| RG-SC-03 | Optimisation coûts transport | La planification des approvisionnements tient compte des contraintes de coûts de transport pour optimiser les commandes | Proposition de commande, Transporteur | Haute |
| RG-SC-04 | Capacité stockage en contrainte | Les propositions de commande et réapprovisionnements tiennent compte de la capacité de stockage disponible | Proposition de commande, Entrepôt | Moyenne |
| RG-SC-05 | Suivi temps réel expéditions | Chaque expédition doit être suivie en temps réel via intégration avec les systèmes transporteurs | Expédition, Transporteur | Haute |
| RG-SC-06 | Réappro magasin optimisé | Les suggestions de réapprovisionnement magasin tiennent compte : capacité stockage PdV, profil de vente local, contraintes livraison | Ordre de réapprovisionnement | Haute |
| RG-SC-07 | Cross-docking articles forte rotation | Les articles à forte rotation peuvent être traités en cross-docking pour accélérer les flux en transit | Ordre de réapprovisionnement | Moyenne |
| RG-SC-08 | Calcul auto droits de douane | Les droits et taxes sont calculés automatiquement selon la nomenclature douanière et les accords commerciaux en vigueur | Document douanier | Haute |
| RG-SC-09 | Documentation douanière intégrée | La gestion documentaire douanière est intégrée au processus logistique (pas de traitement séparé) | Document douanier, Expédition | Moyenne |
| RG-SC-10 | Alertes dérive performance | Des alertes automatiques signalent les dérives par rapport aux objectifs définis sur les KPI supply chain | Indicateur supply chain | Moyenne |
| RG-SC-11 | Intégration transporteurs | L'intégration avec les systèmes des transporteurs est indispensable pour offrir une visibilité complète | Transporteur, Expédition | Haute |

## Contraintes métier

- **Volumétrie réseau** : 7 entrepôts, 320 000 m², 15 millions de commandes/an
- **Coûts transport** : 6,5% du CA — l'optimisation des transports est un enjeu économique majeur
- **KPI temps réel** : taux de service client, taux de disponibilité produit, délai moyen livraison, coût logistique unitaire, productivité entrepôts
- **Multi-pays** : les flux de commerce international imposent une gestion documentaire douanière rigoureuse
