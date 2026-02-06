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
