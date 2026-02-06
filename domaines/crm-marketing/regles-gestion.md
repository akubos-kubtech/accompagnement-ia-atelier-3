# CRM & Marketing - Règles de gestion

> Règles métier, contraintes et validations propres à ce domaine (CDC section 3.1.6).

## Règles de gestion

| ID | Règle | Description | Entité(s) concernée(s) | Criticité |
|----|-------|-----------|----------------------|-----------|
| RG-CM-01 | ERP référentiel client maître | L'ERP constitue le référentiel client maître, synchronisé avec Salesforce CRM | Client | Haute |
| RG-CM-02 | Vision client 360° | Toutes les données client (identification, compte, comportement, fidélité) sont consolidées et accessibles à tous les collaborateurs | Client | Haute |
| RG-CM-03 | Déduplication obligatoire | La déduplication et la fusion des fiches clients sont des fonctionnalités essentielles pour maintenir la qualité du référentiel | Client | Haute |
| RG-CM-04 | Fidélité temps réel | Le programme Horizon Club fonctionne en temps réel avec les caisses magasins et le site e-commerce (cumul et utilisation de points) | Compte fidélité, Transaction fidélité | Haute |
| RG-CM-05 | Points = moyen de paiement | Les points fidélité peuvent être utilisés comme moyen de paiement | Transaction fidélité | Haute |
| RG-CM-06 | Paliers et avantages | Les paliers de statut sont associés à des avantages spécifiques déclenchés automatiquement au franchissement | Palier fidélité, Compte fidélité | Moyenne |
| RG-CM-07 | Segments dynamiques | Les segments peuvent être statiques ou dynamiques (recalculés automatiquement selon les critères) | Segment client | Moyenne |
| RG-CM-08 | Cohérence promo/tarif | Les offres promotionnelles des campagnes marketing doivent être cohérentes avec les conditions tarifaires appliquées | Campagne, Promotion | Haute |
| RG-CM-09 | Campagnes complexes via Salesforce | Les campagnes multi-canal complexes sont gérées via Salesforce Marketing Cloud avec connecteurs natifs | Campagne | Moyenne |
| RG-CM-10 | SLA réclamations | Les tickets de réclamation sont suivis avec des délais SLA et les motifs de contact alimentent les indicateurs qualité | Ticket réclamation | Moyenne |
| RG-CM-11 | Traçabilité consentements RGPD | Chaque consentement est tracé (date, canal, statut) et les droits des personnes sont exercables à tout moment | Consentement RGPD | Haute |
| RG-CM-12 | Conservation et purge | Des règles de conservation et de purge des données personnelles sont appliquées conformément au RGPD | Client, Consentement | Haute |

## Contraintes métier

- **Volumétrie clients** : 8,5 millions de clients actifs — performance de la déduplication et de la segmentation
- **Coexistence Salesforce** : l'ERP est le référentiel maître, Salesforce reste le CRM principal — la synchronisation bidirectionnelle est critique
- **Temps réel fidélité** : le cumul/utilisation des points doit fonctionner en temps réel sur tous les canaux (caisse, web)
- **RGPD** : obligations légales strictes — registre des traitements, analyses d'impact, exercice des droits
