# Journal d'audit

> Traçabilité des actions significatives réalisées durant la préparation du TP.

---

## 2026-02-06 — Création de la structure initiale du projet

**Action** : Mise en place de l'arborescence complète du projet et des templates markdown.

**Fichiers créés** :
- `PROGRESS.md` : suivi de progression par phase
- `00-contexte/glossaire.md` : template glossaire
- `00-contexte/synthese-projet.md` : template synthèse
- `00-contexte/cartographie-existant.md` : template cartographie SI
- `domaines/README.md` : vue d'ensemble des 7 domaines provisoires
- `domaines/{7 domaines}/specifications.md` : templates spécifications
- `domaines/{7 domaines}/entites.md` : templates entités
- `domaines/{7 domaines}/regles-gestion.md` : templates règles de gestion
- `exigences-transverses/non-fonctionnelles.md` : template ENF
- `exigences-transverses/techniques.md` : template exigences techniques
- `exigences-transverses/securite-conformite.md` : template sécurité/RGPD
- `exploration/README.md` : guide des 4 étapes du TP
- `exploration/flux-inter-modules.md` : template étape 1
- `exploration/zones-friction.md` : template étape 2
- `exploration/matrice-dependances.md` : template étape 3
- `exploration/questions-arbitrage.md` : template étape 4

**Décisions** :
- 7 domaines métier provisoires identifiés depuis le sommaire du CDC : gestion-commerciale, achats-approvisionnement, stocks-logistique, finance-comptabilite, relation-client, produits-catalogue, commerce-international
- 3 fichiers par domaine : specifications.md, entites.md, regles-gestion.md
- La liste sera ajustée après analyse de la section 3.1 du CDC

---

## 2026-02-06 — Ajout du CLAUDE.md et du journal d'audit

**Action** : Rédaction du fichier de contexte projet (CLAUDE.md) avec les 2 règles strictes et création du journal d'audit (AUDIT.md).

**Fichiers créés/modifiés** :
- `CLAUDE.md` : contexte projet, livrables, contraintes, règles R1 (commit) et R2 (audit)
- `AUDIT.md` : journal de traçabilité (ce fichier)

**Décisions** :
- R1 : proposer un commit après chaque action significative (sans committer sans confirmation)
- R2 : mettre à jour AUDIT.md avant chaque commit

---

## 2026-02-06 — Analyse du CDC et restructuration des domaines

**Action** : Lecture complète du CDC (190k caractères, 1506 paragraphes). Restructuration des domaines pour correspondre à la section 3.1 du CDC. Découpage du CDC en fichiers séparés par domaine/section.

**Fichiers créés** :
- `cdc-decoupe/` : 14 fichiers découpés du CDC par section (domaines ~2.5k chacun, exigences ~25k chacun)

**Fichiers modifiés** :
- `00-contexte/glossaire.md` : rempli depuis sections 1.4 et Annexe H
- `00-contexte/synthese-projet.md` : rempli depuis sections 1-2
- `00-contexte/cartographie-existant.md` : rempli depuis section 2.3
- `PROGRESS.md` : phases 1 et 2 complétées

**Fichiers supprimés** :
- `domaines/produits-catalogue/` : domaine transversal (PIM Akeneo conservé), pas un domaine CDC
- `domaines/commerce-international/` : ventilé dans achats/finance/logistique, pas un domaine CDC distinct

**Fichiers renommés** :
- `domaines/stocks-logistique/` → `domaines/supply-chain-logistique/` (alignement CDC 3.1.3)
- `domaines/relation-client/` → `domaines/crm-marketing/` (alignement CDC 3.1.6)

**Fichiers ajoutés** :
- `domaines/stocks-entrepots/` (nouveau domaine, CDC 3.1.5)
- `domaines/reporting-bi/` (nouveau domaine, CDC 3.1.7)

**Décisions** :
- 7 domaines finaux alignés sur le CDC section 3.1 : gestion-commerciale, achats-approvisionnement, supply-chain-logistique, finance-comptabilite, stocks-entrepots, crm-marketing, reporting-bi
- Découpage du CDC en fichiers séparés pour optimiser la fenêtre de contexte lors de l'analyse domaine par domaine
- Sections 4-5 (modalités AO, contrat) conservées mais non prioritaires pour le TP

---

## 2026-02-06 — Analyse complète des 7 domaines métier (Phase 3)

**Action** : Remplissage des 3 fichiers (specifications.md, entites.md, regles-gestion.md) pour chacun des 7 domaines métier à partir des fichiers CDC découpés. Mise à jour du PROGRESS.md après chaque domaine. Mise à jour du README des domaines (qui était obsolète).

**Fichiers modifiés** :
- `domaines/gestion-commerciale/specifications.md` : périmètre, 4 processus, 7 cas d'usage (CDC 3.1.1)
- `domaines/gestion-commerciale/entites.md` : 10 entités, modèle conceptuel, 4 entités partagées
- `domaines/gestion-commerciale/regles-gestion.md` : 11 règles, 4 contraintes métier
- `domaines/achats-approvisionnement/specifications.md` : périmètre, 5 processus, 7 cas d'usage (CDC 3.1.2)
- `domaines/achats-approvisionnement/entites.md` : 10 entités, modèle conceptuel, 4 entités partagées
- `domaines/achats-approvisionnement/regles-gestion.md` : 11 règles, 4 contraintes métier
- `domaines/supply-chain-logistique/specifications.md` : périmètre, 5 processus, 7 cas d'usage (CDC 3.1.3)
- `domaines/supply-chain-logistique/entites.md` : 9 entités, modèle conceptuel, 6 entités partagées
- `domaines/supply-chain-logistique/regles-gestion.md` : 11 règles, 4 contraintes métier
- `domaines/finance-comptabilite/specifications.md` : périmètre, 7 processus, 8 cas d'usage (CDC 3.1.4)
- `domaines/finance-comptabilite/entites.md` : 11 entités, modèle conceptuel, 6 entités partagées
- `domaines/finance-comptabilite/regles-gestion.md` : 14 règles, 5 contraintes métier
- `domaines/stocks-entrepots/specifications.md` : périmètre, 5 processus, 7 cas d'usage (CDC 3.1.5)
- `domaines/stocks-entrepots/entites.md` : 10 entités, modèle conceptuel, 6 entités partagées
- `domaines/stocks-entrepots/regles-gestion.md` : 13 règles, 4 contraintes métier
- `domaines/crm-marketing/specifications.md` : périmètre, 6 processus, 7 cas d'usage (CDC 3.1.6)
- `domaines/crm-marketing/entites.md` : 9 entités, modèle conceptuel, 4 entités partagées
- `domaines/crm-marketing/regles-gestion.md` : 12 règles, 4 contraintes métier
- `domaines/reporting-bi/specifications.md` : périmètre, 6 processus, 7 cas d'usage (CDC 3.1.7)
- `domaines/reporting-bi/entites.md` : 8 entités, modèle conceptuel, 4 entités partagées
- `domaines/reporting-bi/regles-gestion.md` : 10 règles, 4 contraintes métier
- `domaines/README.md` : mis à jour avec les 7 domaines finaux et la structure par domaine
- `PROGRESS.md` : 7 domaines marqués terminés dans la Phase 3

**Décisions** :
- Chaque domaine a été analysé à partir de son fichier CDC découpé correspondant
- Les entités partagées entre domaines ont été identifiées systématiquement pour préparer la Phase 5 (réconciliation)
- Les règles de gestion incluent les volumétries et seuils chiffrés mentionnés dans le CDC
- Les fichiers manquants (stocks-entrepots, reporting-bi) ont été créés car les répertoires étaient vides

**Points d'attention** :
- Les entités partagées (Client, Article, Stock, Fournisseur) apparaissent dans plusieurs domaines — la réconciliation (Phase 5) sera importante
- Le domaine Finance est le plus riche en processus (7) et règles (14) en raison de la complexité multi-pays/multi-normes

---

## 2026-02-06 — Enrichissement du contexte général (4 artefacts complémentaires)

**Action** : Création de 4 fichiers de contexte complémentaires pour combler les lacunes identifiées avant la Phase 4. Ces artefacts consolident des informations disséminées dans le CDC et dans les 7 analyses de domaine.

**Fichiers créés** :
- `00-contexte/acteurs-roles.md` : gouvernance projet, directions métier, 18 rôles opérationnels, acteurs externes, matrice acteurs × domaines
- `00-contexte/volumetries-cles.md` : chiffres clés consolidés (groupe, transactionnels, stocks, achats, clients, finance, IT)
- `00-contexte/integrations-cibles.md` : 10 systèmes conservés avec détail des flux, modes et SLA cibles, schéma d'architecture simplifié
- `00-contexte/referentiels-transverses.md` : 4 entités maîtresses détaillées, matrice de partage (12 entités × 7 domaines), 5 flux inter-entités critiques, 8 zones de friction anticipées

**Décisions** :
- Ces artefacts complètent la Phase 1 (contexte général) qui était insuffisante pour les besoins du TP
- Les zones de friction anticipées (référentiels-transverses.md) alimenteront directement les exercices d'exploration (Phase 6)
- L'ERP est confirmé comme référentiel maître pour Client (vs Salesforce) et Article (vs Akeneo) — cette convention est structurante

**Points d'attention** :
- WMS multi-pays : seul Manhattan Associates (France) est documenté dans le CDC — les WMS des filiales internationales ne sont pas précisés
- 14 langues à supporter — potentiel impact sur le paramétrage et les tests
- La matrice acteurs × domaines pourra servir de base pour l'atelier (identification des parties prenantes par flux)

---

## 2026-02-06 — Extraction des exigences transverses (Phase 4)

**Action** : Remplissage des 3 fichiers d'exigences transverses à partir des sections 3.2, 3.3 et 3.4 du CDC.

**Fichiers modifiés** :
- `exigences-transverses/non-fonctionnelles.md` : performance (temps de réponse, batch, pics), disponibilité (SLA 99,9%, PCA/PRA, RTO 4h/RPO 1h, modes dégradés), ergonomie (UX, personnalisation, accessibilité WCAG 2.1 AA, responsive, 14 langues), scalabilité (x2 à 2030), maintenabilité
- `exigences-transverses/techniques.md` : architecture cible (SaaS Cloud UE, 7 environnements), intégration (API REST/OAuth/Kafka/MuleSoft, supervision flux), gestion données (migration ~450M enregistrements, qualité, gouvernance, chiffrement AES-256), standards techniques consolidés
- `exigences-transverses/securite-conformite.md` : sécurité infra/applicative (Security by Design, pentests, SIEM), IAM (Entra ID SSO+MFA, SoD), RGPD natif, conformité fiscale multi-pays (Factur-X, SDI, SII, SAF-T, ViDA), certifications (ISO 27001, SOC 2, ISO 22301)
- `PROGRESS.md` : Phase 4 complétée

**Décisions** :
- Structure des fichiers adaptée pour être exploitable rapidement par les participants du TP (tableaux synthétiques plutôt que texte narratif)
- Le fichier sécurité-conformité inclut une synthèse des exigences clés en tableau récapitulatif
- Les standards techniques sont consolidés dans un tableau unique dans techniques.md

**Points d'attention** :
- La migration de données est massive (~450M enregistrements au total, 10 ans d'écritures comptables)
- Le CDC mentionne Apache Kafka et Control-M comme composants d'intégration existants — information structurante pour l'architecture cible
- Les obligations de facturation électronique varient par pays (Factur-X FR, SDI IT, SII ES, SAF-T nordiques) — complexité multi-pays significative

---

## 2026-02-10 — Ajout FRIC-206 Facture et correction reconciliation §14

**Action** : Ajout d'une friction bloquante manquante (FRIC-206 — Facture : FC la possède, GC ne l'alimente pas) identifiée par revue croisée. Correction de l'évaluation initiale de la Facture dans la réconciliation des entités (OUI → PARTIEL).

**Fichiers modifiés** :
- `exploration/zones-friction.md` : ajout FRIC-206 (type 2 — flux orphelin, BLOQUANT), mise à jour synthèse (30 frictions, 9 bloquantes), matrice et compteurs domaines
- `exploration/reconciliation-entites.md` : §14 Facture réévalué de OUI/Basse à PARTIEL/Haute, ajout de 3 écarts détectés et recommandation

**Justification** :
- FC définit l'entité Facture et porte Factur-X (RG-FC-08) — obligation légale
- GC détient les données commerciales nécessaires (lignes, articles, prix, TVA par taux) mais ne modélise pas la facture
- Le FLUX-A05 (GC→FC) transporte des données pour écriture comptable, pas pour facture Factur-X — objet distinct
- L'évaluation initiale sous-estimait l'écart en raisonnant uniquement sur la propriété de l'entité, sans analyser l'alimentation et le déclenchement

---

## 2026-02-10 — Détection des zones de friction (Exploration étape 2)

**Action** : Croisement systématique de toutes les analyses inter-domaines (réconciliation entités, cartographie flux, règles de gestion, ENF, intégrations cibles) pour identifier les zones de friction.

**Fichiers modifiés** :
- `exploration/zones-friction.md` : remplacé le template vide par l'analyse complète des 29 frictions

**Résultats clés** :
- 29 frictions identifiées : 8 bloquantes, 16 majeures, 5 mineures
- 6 catégories : incohérences de définition (6), flux orphelins (5), hypothèses contradictoires (5), RG en conflit (5), référentiels non définis (4), séquencement impossible (4)
- Domaines les plus exposés : GC (18 frictions, 4 bloquantes), SE (14, 4 bloquantes)
- Couples les plus en friction : GC↔CM (7), GC↔SE (7)
- Frictions bloquantes majeures : stock « disponible » (4 définitions), vente POS non modélisée, transaction POS synchrone+async, cascade tarifaire incomplète (fidélité absente), cross-docking sans orchestrateur, paiement par points (dépendance circulaire), magasin sans référentiel

**Sources croisées** :
- `exploration/reconciliation-entites.md` (type 1)
- `exploration/flux-inter-modules.md` (types 2, 3, 6)
- `domaines/*/regles-gestion.md` (type 4)
- `exigences-transverses/non-fonctionnelles.md` (type 3)
- `00-contexte/integrations-cibles.md` (types 2, 3)

---

## 2026-02-10 — Cartographie des flux inter-modules (Exploration étape 1)

**Action** : Identification systématique de tous les flux de données entre les 7 domaines fonctionnels à partir des spécifications, entités, règles de gestion et intégrations cibles.

**Fichiers modifiés** :
- `exploration/flux-inter-modules.md` : remplacé le template vide par la cartographie complète

**Résultats clés** :
- 46 flux identifiés au total, répartis en 7 catégories (vente, achat, supply chain, finance, référentiels, CRM, reporting)
- 31 flux documentés dans les SF, 10 implicites (déduits par croisement), 5 absents (non documentés mais nécessaires)
- 5 flux absents critiques : libération stock sur annulation (HAUTE), contrôle encours client (HAUTE), retour règlement fournisseur, alimentation compte tiers client, intégration alertes IA
- 7 dépendances cachées entre règles de gestion et données d'autres domaines
- 3 chronologies détaillées (commande web, réception fournisseur, clôture mensuelle)
- Matrice des flux inter-domaines : couples les plus chargés = GC↔SE (4), AA→FC (4), GC↔CM (5)

**Décisions** :
- Numérotation par catégorie (FLUX-A01 à FLUX-G05) pour faciliter les références croisées
- Les flux vers le Reporting (RB) sont consolidés dans un flux unique (FLUX-G01) car tous les domaines alimentent la BI
- 5 questions d'arbitrage formulées pour l'atelier

---

## 2026-02-10 — Réconciliation des entités inter-domaines (Phase 5)

**Action** : Analyse systématique des 16 entités apparaissant dans plusieurs modules. Comparaison des définitions, attributs et détection des écarts. Production du document de réconciliation.

**Fichiers créés** :
- `exploration/reconciliation-entites.md` : analyse complète avec tableau de synthèse, détail par entité (16 fiches), matrice des 8 écarts critiques, 8 questions d'arbitrage pour le client

**Fichiers modifiés** :
- `PROGRESS.md` : Phase 5 — vérification entités partagées et identification incohérences marquées terminées

**Résultats clés** :
- 16 entités partagées identifiées sur les 7 domaines
- 4 entités jugées cohérentes (OUI) : Commande client, Mouvement de stock, Écriture comptable, Facture
- 11 entités partiellement cohérentes (PARTIEL) : Article, Client, Fournisseur, Commande fournisseur, Stock, Réception, Promotion, Expédition, Indicateurs/KPI, Prévision de vente
- 1 entité incohérente (NON) : Entrepôt/Site de stockage/Magasin (3 noms, aucune définition unifiée)
- 8 écarts critiques identifiés (E1-E8), dont 4 de sévérité haute

**Décisions** :
- L'analyse s'appuie sur les 21 fichiers d'analyse de domaine (entites.md, specifications.md, regles-gestion.md) et le référentiel transverses existant
- Les questions d'arbitrage (8) sont formulées pour alimenter directement l'atelier d'exploration

---

## 2026-02-10 — Cartographie consolidée de l'intégration ERP

**Action** : Création d'un document de synthèse consolidant l'ensemble des analyses d'exploration (entités, flux, frictions, dépendances, séquencement) en un seul artefact lisible par 3 audiences (Tech Lead, BA, Chef de Projet).

**Fichiers créés** :
- `exploration/cartographie-consolidee.md` : cartographie consolidée en 6 sections + annexe

**Contenu** :
1. Vue macro : diagramme ASCII des modules et flux principaux, profil de chaque module
2. Entités partagées : 16 entités avec statut de cohérence et problème principal
3. Flux critiques : 31 flux HAUTE criticité (2 absents, 4 implicites, 25 documentés)
4. Frictions majeures : 9 BLOQUANT + 16 MAJEUR avec matrice d'exposition par domaine
5. Matrice de dépendances : 7×7 avec classification (2 structurelles / 24 transactionnelles)
6. Ordre d'implémentation : 5 phases, chemin critique, frictions à résoudre par phase, livraison incrémentale RB

**Décisions** :
- Document conçu comme point d'entrée unique — chaque section renvoie aux documents détaillés en annexe
- Les flux critiques sont triés par statut de documentation (absents → implicites → documentés) pour prioriser l'attention
- La matrice d'exposition domaine × frictions donne une vue CP des risques par périmètre

---

## 2026-02-10 — Séquencement d'implémentation par phases (Exploration étape 3 bis)

**Action** : Refonte complète du séquencement d'implémentation dans matrice-dependances.md. Classification rigoureuse des 26 dépendances (structurelles vs transactionnelles/bouchonnables), séquencement en 5 phases (vs 6 précédemment) avec parallélisme maximisé, chemin critique formalisé.

**Fichiers modifiés** :
- `exploration/matrice-dependances.md` : remplacement des sections "Graphe de dépendances" et "Proposition de séquencement" par 6 nouvelles sections (classification, 5 phases détaillées, visualisation, chemin critique, frictions par phase)

**Changements clés vs version précédente** :
- **AA avancé de Phase 3 à Phase 2** (parallèle avec GC) — AA ne dépend pas structurellement de GC, seulement des référentiels (Phase 0) et du stock SE (Phase 1)
- **SC avancé de Phase 4 à Phase 3** (parallèle avec FC) — conséquence logique de l'avancement de AA
- **Résultat** : 5 phases au lieu de 6, parallélisme accru (2 modules par phase aux Phases 1-3)
- Classification explicite : 2 dépendances structurelles (GC→CM, SC→SE), 24 transactionnelles bouchonnables
- Chemin critique formalisé : 2 chemins de longueur 5 convergent sur GC (nœud critique)
- Mapping des 9 frictions BLOQUANT sur les phases où elles doivent être résolues

**Justification de la refonte** :
- Le séquencement précédent plaçait AA après GC (Phase 3) sans justification dans la matrice — AA et GC n'ont aucune dépendance mutuelle
- La distinction structurelle/transactionnelle n'était pas formalisée, rendant les choix de séquencement opaques

---

## 2026-02-10 — Matrice de dépendances inter-modules (Exploration étape 3)

**Action** : Construction de la matrice de dépendances 7×7 à partir des flux inter-modules (46 flux), des entités partagées (16) et des zones de friction (30). Analyse structurelle complète avec profils de modules, chemins critiques et proposition de séquencement.

**Fichiers modifiés** :
- `exploration/matrice-dependances.md` : remplacé le template vide par la matrice complète

**Résultats clés** :
- Matrice 7×7 avec détail de chaque dépendance (nature, flux référencé, criticité, contournabilité)
- 2 goulots d'étranglement : SE (5 dépendants, donnée stock temps-sensible) et GC (5 dépendants, producteur d'événements métier)
- 1 module consommateur : RB (dépend de 6/6, personne ne dépend de lui sauf SC/IA)
- 1 module autonome : CM (solde +2, ne dépend que de GC)
- 3 chemins critiques identifiés : cycle de vente web (6 modules, 8 flux), cycle d'achat (4 modules, 6 flux), valorisation/clôture (3 modules, séquentiel strict)
- Séquencement en 6 phases proposé : référentiels → SE+CM → GC (pivot) → AA+FC → SC → RB

**Sources croisées** :
- `exploration/flux-inter-modules.md` (46 flux, références FLUX-xxx)
- `exploration/reconciliation-entites.md` (16 entités partagées)
- `exploration/zones-friction.md` (30 frictions, références FRIC-xxx)
- `exigences-transverses/non-fonctionnelles.md` (SLA, modes dégradés)
- `00-contexte/integrations-cibles.md` (WMS Manhattan, POS Cegid)

**Décisions** :
- GC identifié comme goulot le plus critique (arrêt total de la chaîne si indisponible) vs SE comme goulot le plus techniquement sensible (latence stock)
- Phase 0 (référentiels transverses) ajoutée comme prérequis absolu — les 4 référentiels manquants (FRIC-501/503/504 + Article unifié) doivent être définis avant tout
- SE et CM proposés en parallèle en phase 1 car indépendants l'un de l'autre

---

## 2026-02-10 — Correction du référentiel Article dans referentiels-transverses.md

**Action** : Correction de deux erreurs identifiées par traçage des sources CDC dans `00-contexte/referentiels-transverses.md`.

**Fichiers modifiés** :
- `00-contexte/referentiels-transverses.md` : section « Article / Produit » et matrice de partage des entités

**Corrections** :
1. **Matrice de partage** : Article passé de R (reader) à **M** (maître) dans la colonne GC (Gestion commerciale)
2. **Section Article / Produit** : domaine maître corrigé de « Transversal (ERP + Akeneo PIM) » à « Gestion commerciale (ERP = référentiel maître) ». Akeneo PIM repositionné comme source complémentaire pour les données enrichies. Mention erronée de SE comme « source » retirée.

**Sources CDC ayant motivé la correction** :
- `cdc-decoupe/integrations-cibles` → « ERP = référentiel maître pour les articles (vs Akeneo PIM) »
- `cdc-decoupe/11-exigences-techniques.txt:147` → migration articles depuis « SAP ECC (MM) + PIM »
- Flux Akeneo : ERP → PIM (données de base : SKU, prix), PIM → ERP (données enrichies : descriptions, médias)
