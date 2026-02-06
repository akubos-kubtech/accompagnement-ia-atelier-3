# Achats & Approvisionnement - Spécifications fonctionnelles

> Spécifications extraites du CDC section 3.1.2 — Gestion des achats et des approvisionnements.

## Périmètre fonctionnel

Le domaine Achats & Approvisionnement couvre l'ensemble du cycle d'approvisionnement du Groupe Retail Horizon, depuis l'expression du besoin jusqu'à la réception des marchandises. Il inclut :

- **Référentiel fournisseurs** : gestion administrative, conditions commerciales, certifications, qualification
- **Processus d'approvisionnement** : réapprovisionnement automatique, achats sur prévision, achats ponctuels
- **Commandes fournisseurs** : création, suivi, commerce international (devises, incoterms)
- **Réception des marchandises** : contrôle quantitatif/qualitatif, gestion des écarts, litiges
- **Évaluation fournisseurs** : indicateurs de performance, revues périodiques, appels d'offres

**Volumétrie** : 850+ fournisseurs actifs, 35 pays, 4 continents.

## Processus métier

### P1 — Cycle d'approvisionnement principal
1. Expression du besoin (automatique sur seuil, prévisionnel, ponctuel)
2. Sélection du/des fournisseur(s) selon référentiel
3. Négociation des conditions (prix, délais, incoterms)
4. Émission de la commande fournisseur (devise fournisseur)
5. Suivi des engagements contractuels
6. Réception des marchandises
7. Contrôle et validation
8. Mise en stock (via intégration WMS)

### P2 — Qualification et gestion des fournisseurs
1. Création de la fiche fournisseur (données admin, conditions commerciales, certifications)
2. Qualification initiale selon exigences qualité et RSE
3. Suivi de la relation (historique, incidents)
4. Requalification périodique paramétrable
5. Évaluation de performance (taux service, ponctualité, qualité, compétitivité)

### P3 — Réapprovisionnement automatique sur seuil
1. Détection du franchissement du seuil de réapprovisionnement
2. Calcul de la quantité à commander (stock sécurité, lot économique)
3. Génération de la proposition de commande
4. Validation (automatique ou manuelle selon paramétrage)
5. Envoi au fournisseur

### P4 — Appel d'offres fournisseurs
1. Identification de la famille d'achats stratégique
2. Constitution du cahier des charges
3. Mise en concurrence des fournisseurs
4. Analyse comparative des offres
5. Décision de référencement

### P5 — Gestion des litiges fournisseurs
1. Constat d'écart à la réception (quantité, qualité)
2. Création du dossier de litige
3. Communication fournisseur
4. Suivi de la résolution
5. Impact sur l'évaluation fournisseur

## Cas d'usage principaux

| Ref | Cas d'usage | Acteurs | Contexte |
|-----|------------|---------|----------|
| CU-AA-01 | Déclencher un réapprovisionnement automatique sur seuil | Système, Approvisionneur | Stock régulier |
| CU-AA-02 | Passer une commande import avec incoterms et calcul prix de revient complet | Acheteur, Fournisseur international | Commerce international |
| CU-AA-03 | Qualifier un nouveau fournisseur selon les critères qualité/RSE | Acheteur, Qualité | Référencement |
| CU-AA-04 | Réceptionner une livraison avec contrôle quantitatif et qualitatif | Réceptionnaire, WMS | Entrepôt |
| CU-AA-05 | Ouvrir un litige fournisseur suite à un écart de réception | Réceptionnaire, Acheteur | Qualité |
| CU-AA-06 | Lancer un appel d'offres sur une famille d'achats stratégique | Acheteur, Direction achats | Mise en concurrence |
| CU-AA-07 | Anticiper les achats saisonniers sur base des plans de vente | Approvisionneur, Planificateur | Prévision |
