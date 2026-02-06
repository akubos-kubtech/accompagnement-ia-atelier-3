# Exigences de sécurité et conformité

> Extraites de la section 3.4 du CDC — Sécurité infrastructure/applicative, IAM, conformité réglementaire.

## Sécurité des infrastructures

### Sécurité physique et réseau

- Datacenters certifiés (Tier III, ISO 27001)
- Segmentation réseau
- Firewalls et WAF (Web Application Firewall)
- IDS/IPS (détection et prévention d'intrusion)
- Protection **DDoS**
- Rapports d'audit de sécurité fournis **annuellement**

### Sécurité applicative (Security by Design)

- Processus de développement sécurisé intégré (SDLC sécurisé)
- Revue de code systématique
- Tests **SAST** (analyse statique) et **DAST** (analyse dynamique)
- Gestion proactive des vulnérabilités
- Formation développeurs aux bonnes pratiques **OWASP**
- Failles critiques corrigées **immédiatement**

### Chiffrement

| Contexte | Standard | Détail |
|----------|---------|--------|
| Données en transit | **TLS 1.3** | Toutes les communications (API, interfaces, accès utilisateur) |
| Données au repos | **AES-256** | Base de données et fichiers |
| Gestion des clés | **HSM** | Stockage sécurisé, rotation régulière, séparation des responsabilités |

### Gestion des vulnérabilités

- Veille proactive des vulnérabilités
- Plan de réponse aux incidents documenté
- Correctifs critiques appliqués dans les plus brefs délais
- Information du Groupe selon procédure définie en cas d'incident

### Tests de sécurité

| Type | Fréquence | Réalisation |
|------|-----------|------------|
| Pentest | **Annuel** | Prestataire indépendant |
| Audit de sécurité (droit du Groupe) | Sur demande | Le Groupe peut mener ses propres audits |
| Scans de vulnérabilité | **Mensuel** | Éditeur |

### Journalisation sécurité

- Tous les événements significatifs tracés
- Exportable vers le **SIEM** du Groupe
- Rétention **12 mois** minimum
- Alimentation des capacités de détection (SOC)

## Gestion des identités et des accès (IAM)

### Authentification

| Exigence | Détail |
|----------|--------|
| Annuaire | Intégration **Microsoft Entra ID** (ex Azure AD) — obligatoire |
| SSO | Authentification unique avec identifiants d'entreprise |
| MFA | **Multifacteur obligatoire** (Microsoft Authenticator, SMS, token physique) |
| Accès non maîtrisés | Contrôles renforcés depuis réseaux externes |

### Modèle d'habilitation

- Principe du **moindre privilège**
- Rôles applicatifs prédéfinis **personnalisables**
- Droits attribuables par : fonctionnalité, données, action (lecture/écriture/suppression)
- Habilitations granulaires sur données sensibles (coûts, marges, données perso, rémunération)

### Provisioning / Deprovisioning

- Automatisation via intégration **systèmes RH + annuaire**
- Gestion automatique : arrivée, départ, mutation
- Élimination des risques liés aux oublis (comptes orphelins)

### Comptes à privilèges

- Accès strictement limité et tracé
- Comptes **nominatifs** (pas de comptes génériques)
- Sessions enregistrées pour les actions sensibles
- Revue trimestrielle des habilitations privilégiées

### Séparation des tâches (SoD)

- Règles identifiant les **combinaisons de droits incompatibles**
- Alerte en cas de violation lors de l'attribution des droits
- Rapports d'anomalies SoD générés automatiquement

## Conformité RGPD

### Fonctionnalités natives requises

| Exigence RGPD | Fonctionnalité ERP attendue |
|---------------|----------------------------|
| Registre des traitements | Tenu et maintenu dans l'ERP |
| Gestion des consentements | Recueil, traçabilité (date, canal, statut) |
| Exercice des droits | Accès, rectification, effacement, portabilité, limitation |
| Notification des violations | Procédure intégrée de notification (72h) |
| Analyse d'impact (DPIA) | Support documentaire dans l'ERP |
| Durées de conservation | Paramétrage des règles de rétention et purge par type de données |
| Anonymisation / Pseudonymisation | Pour environnements hors production |

- L'éditeur est qualifié de **sous-traitant** au sens du RGPD
- DPA (Data Processing Agreement) conforme à l'article 28

## Conformité réglementaire multi-pays

### Obligations fiscales numériques

| Pays/Zone | Dispositif | Exigence |
|-----------|-----------|----------|
| France | **Factur-X** + Chorus Pro | Facturation électronique obligatoire 2026 |
| Espagne | **SII** (Suministro Inmediato de Información) | Reporting TVA temps réel |
| Italie | **SDI** (Sistema di Interscambio) | Facturation électronique obligatoire |
| Pays nordiques + Pologne | **SAF-T** (Standard Audit File for Tax) | Fichier d'audit fiscal standardisé |
| Europe (futur) | **ViDA** (VAT in the Digital Age) | E-reporting européen en préparation |

- Veille réglementaire **active** attendue de l'éditeur
- Mises à jour réglementaires intégrées dans les releases

### Normes comptables

- Conformité aux normes comptables **locales** de chacun des 15 pays
- Consolidation aux normes **IFRS** pour le reporting groupe
- Intégration des évolutions réglementaires dans les mises à jour

### Réglementations douanières

- Nomenclatures douanières paramétrables
- Calcul automatique des droits et taxes
- Génération des documents douaniers
- Suivi des régimes particuliers
- Mises à jour des réglementations par l'éditeur

### Réglementations sectorielles retail

- Soldes et promotions réglementés (dates, prix de référence, affichage)
- Affichage des prix (conformité par pays)
- Garanties légales et droit de rétractation
- Éco-contributions
- Traçabilité produits

## Audit et traçabilité

### Pistes d'audit

- Historique **complet et inaltérable** des transactions
- Horodatage et identification de l'auteur
- Données avant/après modification
- Pistes d'audit **exportables** (pour auditeurs et commissaires aux comptes)

### Contrôles automatisés

- Contrôles paramétrables sur les processus sensibles
- Détection des anomalies et des violations de règles
- Rapports d'audit planifiés

### Certifications requises

| Certification | Périmètre | Obligatoire |
|--------------|-----------|:-:|
| **ISO 27001** | Management de la sécurité de l'information | Oui |
| **SOC 2 Type II** | Sécurité, disponibilité, intégrité, confidentialité, vie privée | Oui |
| **ISO 22301** | Management de la continuité d'activité | Oui |
| Tier III (Uptime Institute) | Disponibilité datacenter | Oui |

## Synthèse des exigences clés

| Domaine | Exigence clé | Niveau requis |
|---------|-------------|:-------------:|
| Performance | Temps de réponse | < 2s (95% transactions) |
| Disponibilité | Taux de disponibilité | 99,9% (6h-23h) |
| Reprise d'activité | RTO / RPO | 4h / 1h |
| Sécurité | Certifications | ISO 27001, SOC 2 Type II |
| Authentification | SSO + MFA | Microsoft Entra ID |
| Multilingue | Langues supportées | 14 langues |
| Volumétrie | Commandes annuelles | 15,2 M → cible 30 M (2030) |
| Hébergement | Localisation | Union Européenne |
| Conformité | RGPD | Natif (sous-traitant) |
| Facturation | E-invoicing | Multi-pays (Factur-X, SDI, SII, SAF-T) |
