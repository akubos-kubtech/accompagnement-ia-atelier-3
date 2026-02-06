# Gestion commerciale - Spécifications fonctionnelles

> Spécifications extraites du CDC section 3.1.1 — Gestion commerciale et des ventes (omnicanal, tarification).

## Périmètre fonctionnel

Le domaine Gestion commerciale couvre le cycle de vente complet, de la prise de commande à l'encaissement, sur l'ensemble des canaux du Groupe Retail Horizon. Il inclut :

- **Gestion des commandes clients** : création, suivi, modification, annulation, tous canaux confondus
- **Tarification et promotions** : prix de base, grilles B2B, promotions, remises conditionnelles, soldes
- **Ventes en magasin** : intégration caisses, disponibilité stock, prix temps réel, programme fidélité
- **Retours et avoirs** : cycle complet du retour (demande, validation, avoir/remboursement, réintégration stock)
- **Devis et commandes spéciales** : clients professionnels, articles sur-mesure, commandes longues
- **Parcours omnicanal** : web-to-store, store-to-web, click & collect, ship from store

## Processus métier

### P1 — Cycle de vente omnicanal
1. Prise de commande (web, magasin, B2B)
2. Vérification disponibilité stock (tous points de stockage)
3. Application des règles tarifaires (prix, promotions, remises)
4. Validation et confirmation de commande
5. Suivi de l'état de commande en temps réel
6. Notification proactive du client
7. Livraison (domicile, click & collect, ship from store)
8. Encaissement

### P2 — Tarification multi-niveaux
1. Définition du prix de base par article et par pays
2. Application des grilles tarifaires B2B par catégorie client
3. Application des promotions temporaires (%, montant fixe, lot, produit offert)
4. Application des remises conditionnelles (volume, fidélité, premier achat)
5. Application des règles de soldes réglementés

### P3 — Gestion des retours
1. Réception de la demande de retour (tous canaux)
2. Validation selon règles métier (délai, état produit, motif)
3. Génération de l'avoir ou du remboursement
4. Réintégration en stock ou orientation vers circuit de déstockage
5. Alimentation des statistiques de retour (produit, motif, canal)

### P4 — Devis et commandes spéciales (B2B / sur-mesure)
1. Création du devis (articles sur-mesure, délais de fabrication)
2. Validation client
3. Suivi de la commande longue (fabrication → livraison)
4. Visibilité complète pour le client et les équipes commerciales

## Cas d'usage principaux

| Ref | Cas d'usage | Acteurs | Canal |
|-----|------------|---------|-------|
| CU-GC-01 | Passer une commande en ligne avec livraison click & collect | Client, Site e-commerce, Magasin | Web → Magasin |
| CU-GC-02 | Consulter la disponibilité stock multi-magasins depuis le POS | Vendeur magasin | Magasin |
| CU-GC-03 | Appliquer une promotion temporaire sur un panier mixte | Système tarification | Tous canaux |
| CU-GC-04 | Traiter un retour produit initié en ligne pour remboursement en magasin | Client, Vendeur, Système retours | Web → Magasin |
| CU-GC-05 | Créer un devis pour un projet d'aménagement professionnel | Commercial B2B, Client pro | B2B |
| CU-GC-06 | Remonter les ventes magasin vers l'ERP en quasi temps réel | POS, ERP | Magasin → ERP |
| CU-GC-07 | Piloter les soldes réglementés avec prix barrés et dates d'application | Responsable pricing | Tous canaux |
