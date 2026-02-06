# Gestion commerciale - Entités métier

> Entités, attributs clés et relations identifiées pour ce domaine (CDC section 3.1.1).

## Entités principales

| Entité | Description | Attributs clés | Relations |
|--------|-----------|----------------|-----------|
| Commande client | Commande passée par un client sur n'importe quel canal | N° commande, date, canal, statut, mode livraison, montant TTC | Client, Ligne de commande, Adresse livraison |
| Ligne de commande | Détail d'un article dans une commande | Article, quantité, prix unitaire, remise appliquée, montant ligne | Commande client, Article, Promotion |
| Devis | Proposition commerciale (B2B, sur-mesure) | N° devis, date, validité, statut, montant HT/TTC | Client, Ligne de devis, Commercial |
| Article | Référence produit vendu | SKU, libellé, catégorie, prix de base, pays | Stock, Ligne de commande, Tarif |
| Tarif | Règle de prix applicable | Type (base, B2B, promo), montant/%, date début, date fin, conditions | Article, Catégorie client |
| Promotion | Mécanique promotionnelle temporaire | Type (%, fixe, lot, offert), période, conditions d'application | Article, Catégorie produit |
| Retour | Demande de retour d'un article | N° retour, motif, état produit, canal d'origine, statut | Commande client, Article, Avoir |
| Avoir | Document financier lié à un retour ou une correction | N° avoir, montant, date, mode remboursement | Retour, Client, Commande |
| Client | Acheteur (particulier ou professionnel) | N° client, type (B2C/B2B), catégorie, coordonnées | Commande, Devis, Programme fidélité |
| Grille tarifaire B2B | Conditions tarifaires par catégorie de client professionnel | Catégorie client, famille article, remise/prix spécifique | Client B2B, Article |

## Modèle conceptuel

```
Client ──1:N──> Commande client ──1:N──> Ligne de commande ──N:1──> Article
  │                  │                                                 │
  │                  └──0:N──> Retour ──1:1──> Avoir                   │
  │                                                                    │
  └──1:N──> Devis                                          Tarif ──N:1─┘
                                                             │
                                                    Promotion ┘
```

### Entités partagées avec d'autres domaines

| Entité | Domaines liés | Nature du partage |
|--------|--------------|-------------------|
| Client | CRM & Marketing | Référentiel client unifié (fidélité, segmentation) |
| Article | Stocks & Entrepôts, Achats | Référentiel article partagé |
| Stock | Stocks & Entrepôts, Supply Chain | Disponibilité temps réel pour les ventes |
| Mouvement de stock | Stocks & Entrepôts | Retours, expéditions, réintégrations |
