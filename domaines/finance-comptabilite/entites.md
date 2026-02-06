# Finance & Comptabilité - Entités métier

> Entités, attributs clés et relations identifiées pour ce domaine (CDC section 3.1.4).

## Entités principales

| Entité | Description | Attributs clés | Relations |
|--------|-----------|----------------|-----------|
| Écriture comptable | Enregistrement comptable dans le journal | N° écriture, date, journal, compte débit, compte crédit, montant, libellé, pièce d'origine | Compte, Journal, Flux opérationnel |
| Compte comptable | Poste du plan de comptes | N° compte, libellé, type (général/auxiliaire), société, niveau consolidation | Plan de comptes, Écriture |
| Plan de comptes | Structure hiérarchique des comptes | Société, norme (locale/IFRS), version | Compte comptable |
| Facture | Document de facturation (émise/reçue) | N° facture, type (client/fournisseur), date, montant HT/TVA/TTC, format (Factur-X), statut | Client/Fournisseur, Écriture |
| Règlement | Paiement reçu ou émis | N° règlement, date, mode paiement, montant, devise, compte bancaire | Facture, Compte bancaire |
| Compte tiers | Sous-compte client ou fournisseur | Tiers, encours, solde, conditions de paiement, dernière relance | Client/Fournisseur |
| Déclaration TVA | Document fiscal de TVA par pays/période | Pays, période, régime TVA, montants par taux, statut | Écriture, Facture |
| Axe analytique | Dimension d'analyse de la rentabilité | Type (canal, famille produit, zone géo, magasin), code, libellé | Écriture analytique |
| Budget | Enveloppe prévisionnelle par axe et période | Axe, période, montant prévu, montant réalisé, écart | Axe analytique |
| Position de trésorerie | Solde bancaire par devise | Banque, devise, solde, date valorisation | Compte bancaire, Devise |
| Couverture de change | Instrument de couverture d'un risque de change | Devise, montant couvert, cours garanti, date échéance, contrepartie | Position de trésorerie |

## Modèle conceptuel

```
Plan de comptes ──1:N──> Compte comptable ──1:N──> Écriture comptable
                                                        │
                                                        ├── Facture (client/fournisseur)
                                                        │       │
                                                        │       └──> Règlement
                                                        │
                                                        └── Axe analytique ──> Budget

Déclaration TVA <── Écritures + Factures (par pays/période)

Position de trésorerie ──> Couverture de change
        │
        └──> Rapprochement bancaire
```

### Entités partagées avec d'autres domaines

| Entité | Domaines liés | Nature du partage |
|--------|--------------|-------------------|
| Client | Gestion commerciale, CRM | Compte tiers client (encours, règlements) |
| Fournisseur | Achats & Approvisionnement | Compte tiers fournisseur (three-way matching) |
| Commande client | Gestion commerciale | Source des écritures de vente |
| Commande fournisseur | Achats | Source du rapprochement three-way |
| Réception | Achats | Élément du three-way matching |
| Stock | Stocks & Entrepôts | Valorisation des stocks, écritures de variation |
