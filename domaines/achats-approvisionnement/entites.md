# Achats & Approvisionnement - Entités métier

> Entités, attributs clés et relations identifiées pour ce domaine (CDC section 3.1.2).

## Entités principales

| Entité | Description | Attributs clés | Relations |
|--------|-----------|----------------|-----------|
| Fournisseur | Partenaire commercial approvisionnant le Groupe | N° fournisseur, raison sociale, pays, devise, statut qualification, certifications, agréments | Commande fournisseur, Conditions commerciales, Évaluation |
| Conditions commerciales | Termes négociés avec un fournisseur | Fournisseur, famille article, prix, remises, délai de paiement, incoterm, devise | Fournisseur, Article |
| Commande fournisseur | Ordre d'achat émis vers un fournisseur | N° commande, date, fournisseur, devise, incoterm, statut, montant | Fournisseur, Ligne commande fournisseur |
| Ligne commande fournisseur | Détail article d'une commande fournisseur | Article, quantité, prix unitaire, date livraison prévue | Commande fournisseur, Article |
| Réception | Entrée de marchandises en entrepôt | N° réception, date, fournisseur, entrepôt, statut contrôle | Commande fournisseur, Ligne réception, Entrepôt |
| Ligne réception | Détail d'un article réceptionné | Article, quantité commandée, quantité reçue, écart, statut qualité | Réception, Article |
| Litige fournisseur | Dossier de contestation suite à un écart | N° litige, type (quantité/qualité), statut, montant, résolution | Fournisseur, Réception |
| Évaluation fournisseur | Mesure de performance d'un fournisseur | Fournisseur, période, taux service, taux ponctualité, taux qualité, compétitivité | Fournisseur |
| Appel d'offres | Mise en concurrence sur une famille d'achats | N° AO, famille achat, date ouverture, date clôture, statut | Fournisseur, Offre fournisseur |
| Prix de revient | Coût complet d'un article importé | Article, fournisseur, prix achat, frais transport, droits douane, taxes import | Article, Fournisseur |

## Modèle conceptuel

```
Fournisseur ──1:N──> Conditions commerciales ──N:1──> Article
     │
     ├──1:N──> Commande fournisseur ──1:N──> Ligne commande fournisseur ──N:1──> Article
     │              │
     │              └──1:N──> Réception ──1:N──> Ligne réception
     │                            │
     │                            └──0:N──> Litige fournisseur
     │
     └──N:M──> Appel d'offres
```

### Entités partagées avec d'autres domaines

| Entité | Domaines liés | Nature du partage |
|--------|--------------|-------------------|
| Article | Gestion commerciale, Stocks & Entrepôts | Référentiel article partagé |
| Entrepôt | Stocks & Entrepôts, Supply Chain | Lieu de réception des marchandises |
| Stock | Stocks & Entrepôts | Mise à jour du stock après réception validée |
| Prix de revient | Finance & Comptabilité | Alimentation des calculs de marge |
