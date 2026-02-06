# Domaines métier

> Vue d'ensemble des domaines fonctionnels identifiés dans le CDC ERP Retail (section 3.1).

## Domaines identifiés

| Domaine | Description | Section CDC | Statut |
|---------|-----------|-------------|--------|
| [Gestion commerciale](gestion-commerciale/) | Ventes omnicanal, tarification, promotions, retours | 3.1.1 | Analysé |
| [Achats & Approvisionnement](achats-approvisionnement/) | Fournisseurs, commandes, réceptions, litiges | 3.1.2 | Analysé |
| [Supply Chain & Logistique](supply-chain-logistique/) | Prévisions, MRP, transports, réappro magasins | 3.1.3 | Analysé |
| [Finance & Comptabilité](finance-comptabilite/) | Comptabilité multi-pays, TVA, trésorerie, facturation | 3.1.4 | Analysé |
| [Stocks & Entrepôts (WMS)](stocks-entrepots/) | Stock temps réel, mouvements, inventaires, intégration WMS | 3.1.5 | Analysé |
| [CRM & Marketing](crm-marketing/) | Référentiel client, fidélité, segmentation, RGPD | 3.1.6 | Analysé |
| [Reporting & BI](reporting-bi/) | Reporting opérationnel, dashboards, alimentation Tableau | 3.1.7 | Analysé |

## Structure par domaine

Chaque domaine contient 3 fichiers :
- `specifications.md` : périmètre fonctionnel, processus métier, cas d'usage
- `entites.md` : entités, attributs, modèle conceptuel, entités partagées
- `regles-gestion.md` : règles de gestion, contraintes métier
