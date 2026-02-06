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
