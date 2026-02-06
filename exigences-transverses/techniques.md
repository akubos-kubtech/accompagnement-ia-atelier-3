# Exigences techniques et d'architecture

> Extraites de la section 3.3 du CDC — Déploiement, intégration, données, standards.

## Architecture cible

### Modèle de déploiement

| Critère | SaaS Public (PRIVILÉGIÉ) | Cloud Privé / Hybride | On-Premise |
|---------|:-:|:-:|:-:|
| Responsabilité infra | Éditeur | Partagée | Client |
| Mises à jour | Automatiques, incluses | Planifiées par client | À charge client |
| Scalabilité | Élastique, automatique | Manuelle ou semi-auto | Limitée, coûteuse |
| Personnalisation | Configuration, extensions | Large, avec contraintes | Totale |
| Modèle de coût | OPEX (abonnement) | OPEX + CAPEX | CAPEX dominant |
| Time-to-deploy | Rapide (semaines) | Moyen (mois) | Long (6-12 mois) |

### Hébergement

- Datacenters situés dans l'**Union Européenne** (conformité RGPD, performance latence)
- Hyperscaler préféré : **Azure**, **AWS** ou **GCP**
- Certifications datacenter : **Tier III** minimum (Uptime Institute), ISO 27001, SOC 2 Type II
- Multi-tenant acceptable sous réserve d'**isolation stricte** des données (schémas séparés, row-level security ou BDD dédiée, clés de chiffrement par client)
- Protection contre le « noisy neighbor » (réservation ressources, throttling, fair-use)

### Gestion des versions (SaaS)

- Calendrier de releases **prévisible** (idéalement trimestriel)
- Correctifs sécurité/bugs critiques déployables hors cycle
- Préavis minimum **30 jours** (release notes, impacts, actions préparatoires)
- Accès anticipé (**early adopter / preview**) pour tests avant déploiement en production
- Possibilité de **différer** temporairement une mise à jour (opt-out limité) pour périodes critiques
- **Compatibilité ascendante garantie** : API stables, politique de dépréciation >12 mois, personnalisations préservées

### Environnements requis

| Environnement | Usage | Dimensionnement | Données |
|--------------|-------|:-:|---------|
| Production | Exploitation réelle | 100% | Réelles |
| Pré-production | Validation pré-MEP, tests de charge | 80% | Copie anonymisée |
| Recette (UAT) | Tests fonctionnels métier | 50% | Jeu de test |
| Intégration | Tests des interfaces | 30% | Synthétiques |
| Développement | Développements, configurations | 20% | Synthétiques |
| Formation | Formation des utilisateurs | 20% | Pédagogiques |
| Sandbox | POC, expérimentations | À la demande | Vide ou minimal |

- Sandbox créable en **< 24 heures**
- Coût de tous les environnements inclus dans la proposition financière

## Intégration et interopérabilité

### Architecture d'intégration

- Hub central : **ESB MuleSoft Anypoint** (conservé)
- Streaming d'événements : **Apache Kafka**
- Ordonnanceur batch : **Control-M**
- L'ERP s'intègre dans cette architecture, ne la remplace pas

### API RESTful

| Exigence | Standard |
|----------|---------|
| Architecture | REST, verbes HTTP (GET/POST/PUT/PATCH/DELETE) |
| Format d'échange | JSON |
| Documentation | **OpenAPI / Swagger 3.0** (génération auto clients et docs) |
| Versioning | Politique de versioning avec compatibilité ascendante |
| Authentification | **OAuth 2.0** (client credentials M2M, authorization code utilisateur) |
| Tokens | **JWT** (JSON Web Tokens) |
| Chiffrement | **TLS 1.3** obligatoire |
| Protection | Rate limiting (anti-abus, anti-DDoS) |

### Couverture fonctionnelle des API (minimum requis)

- **Référentiels** : articles (tous attributs), clients, fournisseurs, tarifs, structure organisationnelle
- **Transactions** : commandes (clients/fournisseurs), factures, mouvements de stock, écritures comptables
- **Événements** : créations/modifications/suppressions des objets métier, changements d'état significatifs
- **Services métier** : calcul de disponibilité, simulation de prix, validation de commande, calcul frais de port

### Intégration événementielle (event-driven)

- Publication d'événements métier vers **Apache Kafka**
- Consommation d'événements externes depuis Kafka
- Événements versionnés et documentés
- Architecture découplée (scalabilité, évolution indépendante)

### Intégrations fichiers (batch)

- Formats supportés : CSV, XML, JSON, **EDIFACT**
- Outils de mapping et transformation intégrés
- Déclenchement via ordonnanceur externe (**Control-M**)
- Monitoring exécution et notification statut (succès/échec/warning)
- Dépôt fichiers sur espaces sécurisés (**SFTP**, stockage Cloud)

### Supervision des flux

- Monitoring API natif : volumétrie par endpoint, temps de réponse, taux d'erreur, identification consommateurs
- Métriques exposées au format **Prometheus** ou **OpenTelemetry**
- Journal des échanges : horodatage, ID corrélation, source/cible, payload (masquage données sensibles), code retour
- Rétention journal : **90 jours** minimum (+ archivage audit)
- Alertes anomalies intégrées **Datadog** / **PagerDuty**

## Gestion des données

### Migration

| Domaine | Volume estimé | Historique requis | Source principale |
|---------|:------------:|:-:|-----------------|
| Articles et variantes | 450 000 fiches | Actifs + 2 ans archivés | SAP ECC (MM) + PIM |
| Clients B2C | 8,5 millions | Actifs (achat < 3 ans) | Salesforce CRM |
| Clients B2B | 12 000 | Tous actifs | SAP ECC (SD) + CRM |
| Fournisseurs | 850 actifs | Tous + historique | SAP ECC (MM) |
| Tarifs et conditions | 2 millions lignes | Actifs uniquement | SAP ECC (SD) |
| Commandes clients | 45 millions | 3 ans (opérationnel) | SAP ECC + e-commerce |
| Mouvements de stock | 200 millions | 2 ans | SAP ECC + WMS |
| Écritures comptables | 150 millions | 10 ans (légal) | SAP ECC (FI) |
| Points fidélité | 25 millions mvts | 5 ans | Salesforce CRM |

### Qualité des données

- Audit de qualité des données sources en amont (complétude, unicité, validité, cohérence)
- Chantier de **data quality** en parallèle de la conception
- Contrôles natifs à la saisie (format, cohérence, complétude)
- Détection continue des anomalies (doublons, incohérences, dérives)
- Tableaux de bord de qualité des données

### Gouvernance des données

- Modèle de **data ownership** par domaine (propriétaire métier identifié)
- Workflows de validation pour création/modification des données maîtres (multi-étapes, tracés)
- **Dictionnaire de données** centralisé (définition, format, propriétaire, sources, consommateurs, rétention)
- ERP = **system of record** pour articles, clients, fournisseurs (en attente déploiement MDM)
- Synchronisation vers systèmes consommateurs avec gestion des conflits et traçabilité

### Confidentialité et chiffrement

- Habilitations granulaires sur données sensibles (coûts achat/marge → acheteurs/CdG, données perso → service client/marketing)
- Chiffrement au repos (**AES-256**) et en transit (**TLS 1.3**)
- Gestion des clés : stockage HSM, rotation périodique, séparation des responsabilités
- Anonymisation/pseudonymisation des données en environnements hors production

### Traçabilité

- Journalisation complète et inaltérable : consultation données sensibles, création/modification/suppression
- Identification utilisateur + horodatage + données avant/après
- Rétention logs audit : **12 mois** en ligne + archivage long terme (légal)
- Accès aux logs d'audit restreint et lui-même tracé

## Standards techniques

| Domaine | Standard requis |
|---------|----------------|
| API | REST, JSON, OpenAPI 3.0, OAuth 2.0, JWT, TLS 1.3 |
| Événements | Apache Kafka |
| Intégration | MuleSoft ESB, EDIFACT (EDI) |
| Ordonnancement | Control-M |
| Monitoring | Datadog, Prometheus/OpenTelemetry |
| Alertes | PagerDuty |
| Identité | Microsoft Entra ID (SSO) |
| Cloud | Azure / AWS / GCP |
| Chiffrement | AES-256 (repos), TLS 1.3 (transit), HSM (clés) |
| Accessibilité | WCAG 2.1 AA |
| Encodage | UTF-8 |
