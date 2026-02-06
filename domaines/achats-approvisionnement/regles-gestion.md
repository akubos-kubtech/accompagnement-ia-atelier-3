# Achats & Approvisionnement - Règles de gestion

> Règles métier, contraintes et validations propres à ce domaine (CDC section 3.1.2).

## Règles de gestion

| ID | Règle | Description | Entité(s) concernée(s) | Criticité |
|----|-------|-----------|----------------------|-----------|
| RG-AA-01 | Qualification fournisseur obligatoire | Tout fournisseur doit être qualifié (critères qualité et RSE) avant de pouvoir recevoir des commandes | Fournisseur | Haute |
| RG-AA-02 | Requalification périodique | Les fournisseurs font l'objet d'une requalification périodique paramétrable selon les exigences du Groupe | Fournisseur, Évaluation | Moyenne |
| RG-AA-03 | Réappro automatique sur seuil | Le mode principal de réapprovisionnement pour les articles à rotation régulière est le déclenchement automatique au franchissement du seuil | Commande fournisseur, Article | Haute |
| RG-AA-04 | Achats sur prévision | Les besoins saisonniers et promotionnels sont approvisionnés par anticipation sur base des plans de vente | Commande fournisseur | Moyenne |
| RG-AA-05 | Commande en devise fournisseur | Les commandes internationales sont libellées dans la devise du fournisseur | Commande fournisseur | Haute |
| RG-AA-06 | Incoterms obligatoires | Chaque commande import doit préciser l'incoterm définissant la répartition responsabilités/coûts transport | Commande fournisseur | Haute |
| RG-AA-07 | Prix de revient complet | Le système calcule le prix de revient intégrant : prix d'achat + frais transport + droits de douane + taxes à l'importation | Prix de revient | Haute |
| RG-AA-08 | Réception avec/sans commande | La réception doit supporter l'entrée de marchandises avec ou sans commande préalable | Réception | Moyenne |
| RG-AA-09 | Contrôle à réception | Chaque réception fait l'objet d'un contrôle quantitatif et qualitatif ; les écarts déclenchent un processus de litige | Réception, Litige fournisseur | Haute |
| RG-AA-10 | Intégration WMS à réception | La validation de la réception déclenche automatiquement la mise en stock via l'intégration WMS | Réception, Stock | Haute |
| RG-AA-11 | KPI fournisseurs | L'évaluation repose sur 4 indicateurs objectifs : taux de service, taux de ponctualité, taux de qualité, compétitivité tarifaire | Évaluation fournisseur | Moyenne |

## Contraintes métier

- **Portefeuille fournisseurs étendu** : 850+ fournisseurs actifs dans 35 pays sur 4 continents
- **Multi-devises** : les conditions commerciales et commandes doivent supporter les devises fournisseurs
- **Traçabilité complète** : chaque étape du cycle (commande → réception → mise en stock) doit être tracée
- **RSE** : les critères de qualification fournisseur incluent des exigences de responsabilité sociale et environnementale
