# Stocks & Entrepôts (WMS) - Entités métier

> Entités, attributs clés et relations identifiées pour ce domaine (CDC section 3.1.5).

## Entités principales

| Entité | Description | Attributs clés | Relations |
|--------|-----------|----------------|-----------|
| Stock | Position de stock d'un article dans un site | Article, site, emplacement, quantité, statut (disponible/réservé/contrôle/retour/à détruire) | Article, Site de stockage, Mouvement |
| Article stock | Référence produit avec ses caractéristiques stock | SKU, type (standard/variante/sérialisé/lot/péremption), unité de gestion | Stock, Mouvement |
| Site de stockage | Lieu physique de stockage | Type (entrepôt central/plateforme/magasin), code, capacité, adresse | Stock, Emplacement |
| Emplacement | Position de rangement dans un site | Code emplacement, zone, allée, niveau, capacité, type produit autorisé | Site, Stock |
| Mouvement de stock | Opération modifiant la position de stock | N° mouvement, type (entrée/sortie/transfert/ajustement), date, heure, quantité, origine | Stock, Article |
| Inventaire | Session de comptage physique | N° inventaire, type (annuel/tournant/permanent), périmètre, date, statut | Feuille de comptage |
| Feuille de comptage | Détail du comptage d'un inventaire | Emplacement, article, stock théorique, stock compté, écart, statut validation | Inventaire, Emplacement |
| Lot | Regroupement d'articles de même provenance | N° lot, article, date fabrication, date péremption, fournisseur, statut qualité | Article stock, Mouvement |
| Numéro de série | Identifiant unique d'un article sérialisé | N° série, article, statut, historique (vente, retour, SAV) | Article stock |
| Valorisation stock | Valeur comptable d'un stock | Article, site, méthode (FIFO/CUMP/prix standard), valeur unitaire, valeur totale, ancienneté | Stock, Écriture comptable |

## Modèle conceptuel

```
Site de stockage ──1:N──> Emplacement ──1:N──> Stock ──N:1──> Article stock
                                                 │                  │
                                                 │                  ├── Lot
                                                 │                  └── Numéro de série
                                                 │
                                                 └──1:N──> Mouvement de stock

Inventaire ──1:N──> Feuille de comptage ──N:1──> Emplacement

Stock ──> Valorisation stock ──> Écriture comptable
```

### Entités partagées avec d'autres domaines

| Entité | Domaines liés | Nature du partage |
|--------|--------------|-------------------|
| Article | Gestion commerciale, Achats | Référentiel article (SKU, caractéristiques) |
| Réception | Achats & Approvisionnement | Entrée en stock après réception validée |
| Commande client | Gestion commerciale | Réservation de stock, préparation et expédition |
| Retour | Gestion commerciale | Réintégration ou destruction de stock retourné |
| Écriture comptable | Finance & Comptabilité | Écritures de variation de stock et régularisation inventaire |
| Expédition | Supply Chain | Ordres de préparation et confirmations d'expédition |
