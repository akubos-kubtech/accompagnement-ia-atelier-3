# Supply Chain & Logistique - Entités métier

> Entités, attributs clés et relations identifiées pour ce domaine (CDC section 3.1.3).

## Entités principales

| Entité | Description | Attributs clés | Relations |
|--------|-----------|----------------|-----------|
| Prévision de vente | Estimation de la demande future par article/famille | Famille produit, période, quantité prévue, méthode, facteurs exogènes | Article, Plan d'approvisionnement |
| Besoin net (MRP) | Résultat du calcul des besoins après déduction stocks et commandes en cours | Article, quantité brute, stock disponible, en-cours, réservations, besoin net | Prévision, Stock, Commande fournisseur |
| Proposition de commande | Suggestion de commande fournisseur issue du MRP | Fournisseur, article, quantité, date besoin, statut validation | Besoin net, Fournisseur |
| Expédition | Envoi physique de marchandises vers un destinataire | N° expédition, origine, destination, transporteur, statut, date prévue | Commande client, Transporteur, Entrepôt |
| Transporteur | Prestataire de transport | Raison sociale, zone de couverture, tarifs, délais, KPI | Expédition, Tournée |
| Tournée | Regroupement d'expéditions sur un parcours optimisé | N° tournée, date, transporteur, séquence de livraisons, coût | Expédition, Transporteur |
| Ordre de réapprovisionnement magasin | Demande de réapprovisionnement d'un point de vente | Magasin, article, quantité, type (réappro/cross-dock), priorité | Magasin, Article, Entrepôt |
| Document douanier | Pièce liée à un flux import/export | Type document, N° référence, pays origine/destination, nomenclature | Expédition, Article |
| Indicateur supply chain | Métrique de performance logistique | Nom indicateur, valeur, objectif, tendance, alerte | Tableau de bord |

## Modèle conceptuel

```
Prévision de vente ──> Besoin net (MRP) ──> Proposition de commande ──> Fournisseur
                            │
                            └──> Stock disponible
                                    │
Commande client ──> Expédition ──N:1──> Transporteur
                        │
                        └──N:1──> Tournée
                        │
                        └──0:N──> Document douanier

Entrepôt ──1:N──> Ordre de réapprovisionnement magasin ──N:1──> Magasin
```

### Entités partagées avec d'autres domaines

| Entité | Domaines liés | Nature du partage |
|--------|--------------|-------------------|
| Commande fournisseur | Achats & Approvisionnement | Propositions de commandes issues du MRP |
| Stock | Stocks & Entrepôts | Données d'entrée du MRP (stock disponible, réservations) |
| Article | Gestion commerciale, Achats, Stocks | Référentiel article partagé |
| Entrepôt | Stocks & Entrepôts | Sites d'expédition et de stockage |
| Commande client | Gestion commerciale | Déclenchement des expéditions aval |
| Magasin | Gestion commerciale, Stocks | Point de vente à réapprovisionner |
