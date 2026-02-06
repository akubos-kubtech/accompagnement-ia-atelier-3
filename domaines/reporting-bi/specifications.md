# Reporting & BI - Spécifications fonctionnelles

> Spécifications extraites du CDC section 3.1.7 — Reporting et Business Intelligence.

## Périmètre fonctionnel

Le domaine Reporting & BI couvre les capacités de reporting opérationnel intégré à l'ERP et l'alimentation de la plateforme BI groupe. Il inclut :

- **Reporting opérationnel** : états paramétrables par domaine, export multi-formats, envoi automatique programmé
- **Self-service analytics** : générateur de requêtes, modèle sémantique métier, croisement de données
- **Tableaux de bord temps réel** : dashboards prêts à l'emploi, KPI par domaine, multi-supports
- **Alimentation BI externe** : connecteurs/API vers Tableau Software, extraction performante, quasi temps réel
- **Analyse prédictive et IA** : prévision de la demande, détection d'anomalies, recommandation d'actions
- **Gouvernance des données** : dictionnaire de données, définition des indicateurs, auditabilité

**Plateforme BI groupe** : Tableau Software (existant).

## Processus métier

### P1 — Reporting opérationnel standard
1. Paramétrage des états par domaine fonctionnel
2. Exécution à la demande ou programmée
3. Export dans différents formats (PDF, Excel, CSV, etc.)
4. Envoi automatique par email selon fréquence définie

### P2 — Self-service analytics
1. Mise à disposition d'un modèle sémantique métier compréhensible
2. Construction de requêtes par les utilisateurs avancés (sans intervention IT)
3. Croisement des données selon les axes d'analyse souhaités
4. Sauvegarde et partage des analyses personnalisées

### P3 — Tableaux de bord temps réel
1. Configuration des dashboards par domaine (KPI prédéfinis)
2. Personnalisation par l'utilisateur
3. Affichage sur différents supports (desktop, mobile, écran mural)
4. Rafraîchissement en temps réel

### P4 — Alimentation de la plateforme BI (Tableau Software)
1. Exposition des données ERP via connecteurs standards ou API dédiées
2. Extraction performante des données (volumes importants)
3. Gestion de la fraîcheur des données (quasi temps réel pour les indicateurs prioritaires)
4. Documentation des flux de données

### P5 — Analyse prédictive et IA
1. Prévision de la demande assistée par IA
2. Détection automatique d'anomalies dans les données
3. Recommandation d'actions basée sur l'analyse des tendances
4. Intégration native dans les processus métier

### P6 — Gouvernance des données et indicateurs
1. Création et maintenance du dictionnaire de données centralisé
2. Définition unique de chaque indicateur clé avec sa formule de calcul
3. Auditabilité : remontée de l'indicateur agrégé jusqu'aux transactions élémentaires
4. Documentation des flux de données entre ERP et BI

## Cas d'usage principaux

| Ref | Cas d'usage | Acteurs | Contexte |
|-----|------------|---------|----------|
| CU-RB-01 | Générer un état des ventes quotidiennes par magasin et l'envoyer automatiquement aux directeurs | Système, Directeur magasin | Reporting opérationnel |
| CU-RB-02 | Construire une analyse ad hoc croisant les données de vente et de stock sans intervention IT | Utilisateur avancé | Self-service |
| CU-RB-03 | Consulter le tableau de bord temps réel de la performance supply chain | Direction supply chain | Pilotage |
| CU-RB-04 | Alimenter Tableau Software avec les données de vente en quasi temps réel | ETL/API, Tableau Software | BI externe |
| CU-RB-05 | Détecter une anomalie dans les tendances de vente grâce à l'IA | Système IA, Analyste | Prédictif |
| CU-RB-06 | Remonter d'un indicateur de marge agrégé jusqu'aux transactions élémentaires | Contrôleur de gestion | Auditabilité |
| CU-RB-07 | Consulter la définition et la formule de calcul d'un KPI dans le dictionnaire de données | Tout utilisateur | Gouvernance |
