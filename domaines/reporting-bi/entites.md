# Reporting & BI - Entités métier

> Entités, attributs clés et relations identifiées pour ce domaine (CDC section 3.1.7).

## Entités principales

| Entité | Description | Attributs clés | Relations |
|--------|-----------|----------------|-----------|
| État de reporting | Rapport opérationnel paramétrable | Nom, domaine fonctionnel, paramètres, format sortie, fréquence, destinataires | Domaine fonctionnel |
| Requête analytics | Analyse construite par un utilisateur avancé | Auteur, axes d'analyse, filtres, métriques, date création | Modèle sémantique |
| Modèle sémantique | Couche d'abstraction métier sur les données techniques | Nom, domaine, entités exposées, relations, mesures, dimensions | Données ERP |
| Tableau de bord | Dashboard temps réel de KPI | Nom, domaine, KPI affichés, fréquence rafraîchissement, supports | Indicateur KPI |
| Indicateur KPI | Métrique de performance définie de manière unique | Nom, formule de calcul, unité, source, domaine, fréquence, objectif | Dictionnaire de données |
| Dictionnaire de données | Référentiel centralisé des définitions de données et indicateurs | Entrée, définition, formule, source, responsable, date mise à jour | Indicateur KPI, Modèle sémantique |
| Flux d'extraction BI | Pipeline d'alimentation de la plateforme BI externe | Source ERP, destination (Tableau), fréquence, latence, volume, connecteur/API | Données ERP, Tableau Software |
| Alerte IA | Notification issue de l'analyse prédictive | Type (anomalie/prévision/recommandation), sévérité, données source, action suggérée | Indicateur KPI |

## Modèle conceptuel

```
Modèle sémantique ──> Requête analytics (self-service)
       │
       └──> État de reporting (standard)

Indicateur KPI ──N:1──> Dictionnaire de données
       │
       └──> Tableau de bord

Données ERP ──> Flux d'extraction BI ──> Tableau Software (externe)

Indicateur KPI ──> Alerte IA (anomalie / prévision / recommandation)
```

### Entités partagées avec d'autres domaines

| Entité | Domaines liés | Nature du partage |
|--------|--------------|-------------------|
| Toutes les données transactionnelles | Tous les domaines | Le reporting exploite les données de tous les domaines (ventes, achats, stocks, finance, CRM) |
| Axe analytique | Finance & Comptabilité | Les axes d'analyse (canal, produit, zone géo) sont partagés avec la comptabilité analytique |
| Indicateur supply chain | Supply Chain & Logistique | Les KPI supply chain alimentent les tableaux de bord |
| Prévision de vente | Supply Chain | Les prévisions IA alimentent la planification de la demande |
