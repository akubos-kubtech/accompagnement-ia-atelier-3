# Progression de l'analyse du CDC ERP Retail

> **Source** : CDC_ERP_Retail_Partie1.docx (AO-2026-ERP-001)
> **Statut global** : En cours - Phase d'analyse par domaine

---

## Phase 1 : Extraction du contexte général

- [x] Glossaire des termes et acronymes
- [x] Synthèse du projet (vision, objectifs stratégiques)
- [x] Cartographie de l'existant (SI actuel, points de friction)

## Phase 2 : Identification et découpage des domaines métiers

- [x] Identification des domaines depuis le périmètre fonctionnel (section 3.1)
- [x] Validation de la liste des domaines (7 domaines alignés sur le CDC)
- [x] Création des répertoires par domaine
- [x] Découpage du CDC en fichiers séparés par domaine (`cdc-decoupe/`)

## Phase 3 : Analyse par domaine métier

> Pour chaque domaine : spécifications, entités, règles de gestion

| Domaine | Spécifications | Entités | Règles de gestion | Statut |
|---------|:-:|:-:|:-:|--------|
| Gestion commerciale | [x] | [x] | [x] | **Terminé** |
| Achats & approvisionnement | [x] | [x] | [x] | **Terminé** |
| Supply chain & logistique | [x] | [x] | [x] | **Terminé** |
| Finance & comptabilité | [x] | [x] | [x] | **Terminé** |
| Stocks & entrepôts (WMS) | [x] | [x] | [x] | **Terminé** |
| CRM & marketing | [x] | [x] | [x] | **Terminé** |
| Reporting & BI | [x] | [x] | [x] | **Terminé** |

## Phase 4 : Exigences transverses

- [ ] Exigences non-fonctionnelles (performance, disponibilité, UX)
- [ ] Exigences techniques et d'architecture
- [ ] Exigences sécurité et conformité (RGPD, etc.)

## Phase 5 : Réconciliation et cohérence

- [ ] Vérification des entités partagées entre domaines
- [ ] Identification des incohérences inter-domaines
- [ ] Consolidation du glossaire

## Phase 6 : Préparation de la zone d'exploration (TP)

- [ ] Template flux inter-modules
- [ ] Template zones de friction
- [ ] Template matrice de dépendances
- [ ] Template questions d'arbitrage
