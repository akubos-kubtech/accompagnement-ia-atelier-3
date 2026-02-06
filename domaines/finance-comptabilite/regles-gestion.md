# Finance & Comptabilité - Règles de gestion

> Règles métier, contraintes et validations propres à ce domaine (CDC section 3.1.4).

## Règles de gestion

| ID | Règle | Description | Entité(s) concernée(s) | Criticité |
|----|-------|-----------|----------------------|-----------|
| RG-FC-01 | Conformité normes locales | L'ERP garantit la conformité aux normes comptables locales de chacun des 15 pays d'implantation | Plan de comptes, Écriture | Haute |
| RG-FC-02 | Consolidation IFRS | Les comptes sont consolidables aux normes IFRS pour le reporting groupe | Plan de comptes | Haute |
| RG-FC-03 | Plan de comptes harmonisé | Un plan de comptes groupe harmonisé est décliné selon les spécificités locales | Plan de comptes, Compte | Haute |
| RG-FC-04 | Génération auto écritures | Les écritures comptables sont générées automatiquement à partir des flux opérationnels (ventes, achats, stocks) | Écriture comptable | Haute |
| RG-FC-05 | Three-way matching | Le rapprochement fournisseur s'effectue sur 3 éléments : commande, réception, facture | Facture fournisseur, Commande, Réception | Haute |
| RG-FC-06 | TVA multi-régimes | Le système gère les différents régimes de TVA, les règles de territorialité, l'autoliquidation et la TVA sur marge | Déclaration TVA | Haute |
| RG-FC-07 | OSS e-commerce | Le régime OSS (One Stop Shop) pour le e-commerce transfrontalier européen doit être supporté nativement | Déclaration TVA | Haute |
| RG-FC-08 | Facturation électronique | Les factures sont émises/reçues au format Factur-X et transmises via les plateformes agréées | Facture | Haute |
| RG-FC-09 | E-reporting B2C | Les transactions B2C font l'objet d'un e-reporting conforme aux obligations légales | Facture | Haute |
| RG-FC-10 | Axes analytiques flexibles | Le modèle analytique est flexible et supporte les axes : canal, famille produit, zone géographique, magasin | Axe analytique | Moyenne |
| RG-FC-11 | Ventilation charges indirectes | Les charges indirectes sont ventilées selon des clés de répartition pour le calcul des coûts complets | Axe analytique, Budget | Moyenne |
| RG-FC-12 | Trésorerie 8 devises | La gestion de trésorerie supporte les 8 devises opérationnelles du groupe | Position de trésorerie | Haute |
| RG-FC-13 | Intégration bancaire | L'intégration avec les plateformes bancaires s'effectue via EBICS et SWIFT | Règlement, Rapprochement | Haute |
| RG-FC-14 | Rapprochement bancaire auto | Le rapprochement bancaire est automatisé avec des mécanismes de réconciliation | Règlement, Position | Moyenne |

## Contraintes métier

- **Multi-pays / multi-normes** : 15 pays, normes comptables locales + consolidation IFRS
- **Multi-devises** : 8 devises opérationnelles, nécessité de couvertures de change
- **Conformité réglementaire** : veille active attendue de l'éditeur sur les évolutions fiscales (TVA, facturation électronique)
- **Auditabilité** : traçabilité complète des écritures depuis le flux opérationnel source
- **Automatisation** : génération automatique des écritures, déclarations TVA, rapprochements bancaires
