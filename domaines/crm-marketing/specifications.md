# CRM & Marketing - Spécifications fonctionnelles

> Spécifications extraites du CDC section 3.1.6 — Gestion de la relation client (CRM) et marketing.

## Périmètre fonctionnel

Le domaine CRM & Marketing couvre la gestion de la relation client et les actions marketing opérationnelles. L'ERP est le référentiel client maître, tandis que Salesforce reste la plateforme CRM principale. Il inclut :

- **Référentiel client unifié** : données d'identification, de compte, comportementales et de fidélité
- **Déduplication et qualité** : fusion de fiches, maintien de la qualité du référentiel
- **Programme de fidélité** : « Horizon Club » — cumul points, paliers, avantages, paiement par points
- **Segmentation client** : critères multiples, segments statiques et dynamiques
- **Campagnes marketing** : opérations simples dans l'ERP, campagnes complexes via Salesforce Marketing Cloud
- **Service client** : centre de contact omnicanal, tickets de réclamation, suivi SLA
- **Conformité RGPD** : consentements, droits des personnes, conservation, purge

**Volumétrie** : 8,5 millions de clients actifs. CRM Salesforce existant.

## Processus métier

### P1 — Gestion du référentiel client unifié
1. Création/modification de fiche client (tous canaux)
2. Consolidation des données (identification, compte, comportement, fidélité)
3. Déduplication et fusion des doublons
4. Synchronisation bidirectionnelle avec Salesforce CRM

### P2 — Programme de fidélité « Horizon Club »
1. Inscription du client au programme
2. Cumul automatique des points à chaque achat (tous canaux)
3. Passage de palier et attribution des avantages associés
4. Utilisation des points comme moyen de paiement
5. Intégration temps réel avec caisses magasins et site e-commerce

### P3 — Segmentation et ciblage
1. Définition des critères de segmentation (sociodémo, comportement achat, cycle de vie, valeur client)
2. Création de segments statiques ou dynamiques
3. Export vers les outils de marketing automation (Salesforce Marketing Cloud)
4. Suivi de l'évolution des segments

### P4 — Campagnes marketing opérationnelles
1. Conception de la campagne (offre, cible, canal)
2. Exécution (simple dans ERP, complexe via Salesforce Marketing Cloud)
3. Vérification de cohérence entre offres promotionnelles et conditions tarifaires
4. Mesure des résultats

### P5 — Service client omnicanal
1. Identification du client (vision 360° temps réel)
2. Création du ticket de réclamation
3. Suivi du traitement et des délais SLA
4. Analyse des motifs de contact
5. Alimentation des indicateurs de qualité de service

### P6 — Conformité RGPD
1. Recueil et traçabilité des consentements
2. Exercice des droits des personnes (accès, rectification, suppression, portabilité)
3. Application des règles de conservation et de purge
4. Tenue du registre des traitements et analyses d'impact

## Cas d'usage principaux

| Ref | Cas d'usage | Acteurs | Contexte |
|-----|------------|---------|----------|
| CU-CM-01 | Fusionner deux fiches client doublons détectées | Responsable données, Système | Qualité référentiel |
| CU-CM-02 | Inscrire un client au programme Horizon Club et cumuler ses premiers points | Client, Vendeur/Site web | Fidélité |
| CU-CM-03 | Utiliser des points fidélité comme moyen de paiement en caisse | Client, Vendeur, POS | Fidélité |
| CU-CM-04 | Créer un segment dynamique « clients à risque de churn » | Responsable marketing | Segmentation |
| CU-CM-05 | Lancer une campagne promotionnelle ciblée via Salesforce Marketing Cloud | Marketing, Salesforce | Campagne |
| CU-CM-06 | Traiter une réclamation client avec vision 360° | Conseiller centre de contact | Service client |
| CU-CM-07 | Répondre à une demande d'exercice du droit à l'effacement (RGPD) | DPO, Système | Conformité |
