# Cartographie de l'existant

> Description du SI actuel, de l'ERP en place et des points de friction identifiés (section 2.3 du CDC).

## Infrastructure actuelle

- **Modèle** : Hybride (datacenter privé + Cloud Azure/AWS)
- **Datacenter principal** : Région parisienne (applications critiques dont ERP)
- **Site de secours** : Province (continuité d'activité)
- **Services Cloud** : Microsoft Azure et AWS (apps web, dev, analytics)

## Cartographie applicative

| Domaine | Solution | Éditeur | Évolution prévue |
|---------|---------|---------|-----------------|
| ERP central | SAP ECC 6.0 (déployé 2011) | SAP | **Remplacement** |
| E-commerce | Salesforce Commerce Cloud | Salesforce | Conservation |
| Caisses magasin FR | Cegid Retail Y2 | Cegid | Conservation |
| CRM | Salesforce Sales & Service | Salesforce | Conservation |
| WMS Entrepôts | Manhattan SCALE | Manhattan Associates | Conservation |
| BI / Analytics | Tableau + Snowflake | Tableau / Snowflake | Conservation |
| SIRH | Workday | Workday | Conservation |
| ESB / Intégration | MuleSoft Anypoint (depuis 2020) | Salesforce | Conservation |
| PIM | Akeneo PIM | Akeneo | Conservation |

## Périmètre SAP ECC actuel

- **Modules** : Finance (FI/CO), Achats (MM), Ventes (SD), Logistique (partiel)
- **Couverture** : Entités françaises (~60% des flux transactionnels consolidés)
- **Filiales internationales** : Systèmes hérités des acquisitions, partiellement interfacés avec SAP
- **Planification** : SAP APO (usage limité faute de compétences internes)
- **BI** : SAP BW + Tableau (mise à jour nocturne J-1)

## Historique des acquisitions

| Année | Acquisition | Marché |
|-------|-----------|--------|
| 2010 | HomeDeco Benelux | Pays-Bas, Belgique |
| 2013 | Casa Bella Italie | Italie |
| 2016 | Nordic Living Scandinavie | Suède, Danemark, Norvège, Finlande |
| 2019 | Heim & Stil | Allemagne, Autriche |
| 2022 | Filiales Asie | Singapour, Thaïlande, Vietnam |

## Flux d'intégration existants

| Application | Données échangées | Mode | SLA |
|------------|------------------|------|-----|
| Salesforce Commerce Cloud | Commandes, stocks, prix, clients | Temps réel (API) | < 2s |
| Cegid Retail Y2 (caisses) | Ventes, stocks, fidélité | Quasi temps réel | < 5 min |
| Manhattan WMS | Ordres, réceptions, expéditions | Temps réel (API) | < 1 min |
| Salesforce CRM | Clients, historique, fidélité | Bidirectionnel (API) | < 5 min |
| Tableau (BI) | Données analytiques | Batch/API | Horaire à temps réel |
| Banques | Relevés, paiements | Fichiers (EBICS) | Quotidien |
| Transporteurs | Expéditions, tracking | API / EDI | Temps réel |
| Fournisseurs (EDI) | Commandes, factures, ASN | Fichiers (EDIFACT) | Plusieurs fois/jour |

## Points de friction identifiés

### 1. Obsolescence technologique
- SAP ECC 6.0 : fin de support mainstream 2027, extension possible jusqu'en 2030 (coûts majorés)
- Architecture technique de 20+ ans, ne répond plus aux standards actuels

### 2. Hétérogénéité des systèmes
- Patchwork applicatif issu des acquisitions successives
- Processus non harmonisés, référentiels éclatés
- Consolidation financière mensuelle avec travaux manuels importants

### 3. Manque de réactivité décisionnelle
- Données de vente disponibles en J+1 uniquement
- Analyses ad hoc = extractions manuelles + retraitements Excel
- Pas de pilotage temps réel des performances commerciales

### 4. Rigidité fonctionnelle
- Time-to-market des évolutions en mois/années
- Chaque initiative (canal, service, promo) = développements longs et coûteux

### 5. Expérience utilisateur dégradée
- Interface SAP ECC datée et peu ergonomique
- Taux de satisfaction utilisateur : **42%**
- Shadow IT généralisé (fichiers Excel parallèles)

### 6. Coûts de possession élevés
- Budget annuel maintenance/évolution SI : **18 M€** (dont 40% pour SAP)
- Compétences SAP rares et coûteuses
- Dette technique accumulée rendant chaque évolution plus risquée

### 7. Lacunes du référentiel produits
- Attributs enrichis insuffisants pour le e-commerce
- Incohérences visibles par les clients entre canaux

### 8. Conformité réglementaire complexe
- Facturation électronique obligatoire France 2026
- Multiples réglementations fiscales par pays
- Architecture actuelle rend les mises en conformité coûteuses
