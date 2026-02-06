# CRM & Marketing - Entités métier

> Entités, attributs clés et relations identifiées pour ce domaine (CDC section 3.1.6).

## Entités principales

| Entité | Description | Attributs clés | Relations |
|--------|-----------|----------------|-----------|
| Client | Fiche client unifiée (référentiel maître) | N° client, type (B2C/B2B), données identification, données compte, données comportementales | Compte fidélité, Commande, Ticket réclamation |
| Compte fidélité | Adhésion au programme Horizon Club | N° adhérent, points cumulés, palier actuel, avantages actifs, date inscription | Client, Transaction fidélité |
| Transaction fidélité | Opération de cumul ou d'utilisation de points | Type (cumul/utilisation), date, points, canal, montant associé | Compte fidélité, Commande |
| Palier fidélité | Niveau de statut dans le programme | Nom palier, seuil de points, avantages associés | Compte fidélité |
| Segment client | Groupe de clients partageant des caractéristiques | Nom, type (statique/dynamique), critères, nombre de clients, date mise à jour | Client, Campagne |
| Campagne marketing | Action marketing ciblée | N° campagne, nom, type, canal, cible (segment), date début/fin, statut, résultats | Segment, Promotion |
| Ticket réclamation | Demande ou plainte d'un client | N° ticket, canal d'origine, motif, priorité, statut, SLA, date résolution | Client, Conseiller |
| Consentement RGPD | Accord du client pour un traitement de données | Type de traitement, statut (accordé/refusé/retiré), date, canal de recueil | Client |
| Registre des traitements | Documentation des traitements de données personnelles | N° traitement, finalité, base légale, durée conservation, analyse d'impact | Consentement |

## Modèle conceptuel

```
Client ──1:1──> Compte fidélité ──1:N──> Transaction fidélité
  │                  │
  │                  └──N:1──> Palier fidélité
  │
  ├──N:M──> Segment client ──1:N──> Campagne marketing
  │
  ├──1:N──> Ticket réclamation
  │
  └──1:N──> Consentement RGPD ──> Registre des traitements
```

### Entités partagées avec d'autres domaines

| Entité | Domaines liés | Nature du partage |
|--------|--------------|-------------------|
| Client | Gestion commerciale | Référentiel client maître partagé (commandes, devis) |
| Client | Finance & Comptabilité | Compte tiers client (encours, règlements) |
| Commande | Gestion commerciale | Historique d'achat pour la fidélité et la segmentation |
| Promotion | Gestion commerciale | Cohérence offres promotionnelles / conditions tarifaires |
