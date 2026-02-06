# Reporting & BI - Règles de gestion

> Règles métier, contraintes et validations propres à ce domaine (CDC section 3.1.7).

## Règles de gestion

| ID | Règle | Description | Entité(s) concernée(s) | Criticité |
|----|-------|-----------|----------------------|-----------|
| RG-RB-01 | Couverture complète | Le reporting opérationnel standard couvre l'ensemble des domaines fonctionnels avec des états paramétrables | État de reporting | Haute |
| RG-RB-02 | Export multi-formats | Les états sont exportables dans différents formats et programmables pour envoi automatique par email | État de reporting | Moyenne |
| RG-RB-03 | Self-service sans IT | Les utilisateurs avancés peuvent créer leurs propres analyses sans intervention technique, via un modèle sémantique métier | Requête analytics, Modèle sémantique | Haute |
| RG-RB-04 | Dashboards prêts à l'emploi | L'ERP propose des tableaux de bord prêts à l'emploi pour les KPI clés de chaque domaine | Tableau de bord | Haute |
| RG-RB-05 | Dashboards multi-supports | Les tableaux de bord sont accessibles et personnalisables sur différents supports (desktop, mobile) | Tableau de bord | Moyenne |
| RG-RB-06 | Alimentation BI performante | L'ERP expose ses données via connecteurs standards ou API dédiées avec des mécanismes d'extraction performants | Flux d'extraction BI | Haute |
| RG-RB-07 | Quasi temps réel BI | La fraîcheur des données dans Tableau Software doit pouvoir atteindre le quasi temps réel pour les indicateurs prioritaires | Flux d'extraction BI | Haute |
| RG-RB-08 | Définition unique des KPI | Chaque indicateur clé est défini de manière unique avec sa formule de calcul dans le dictionnaire de données | Indicateur KPI, Dictionnaire | Haute |
| RG-RB-09 | Auditabilité complète | L'auditabilité permet de remonter de l'indicateur agrégé jusqu'aux transactions élémentaires | Indicateur KPI | Haute |
| RG-RB-10 | IA native | Les fonctionnalités d'analyse prédictive et d'IA (prévision demande, détection anomalies, recommandations) sont disponibles nativement | Alerte IA | Moyenne |

## Contraintes métier

- **Plateforme BI existante** : Tableau Software — l'alimentation doit être fluide, documentée et performante
- **Gouvernance** : dictionnaire de données centralisé indispensable pour la compréhension commune des métriques
- **Auditabilité** : drill-down complet de l'agrégé au détail transactionnel
- **Transversalité** : le reporting exploite les données de TOUS les domaines — la qualité dépend de la cohérence inter-domaines
