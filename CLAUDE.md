# Contexte projet

## Objectif

Préparer un support de TP pour un atelier d'exploration architecturale chez un client.
Le client possède uniquement des SFD (Spécifications Fonctionnelles Détaillées) et cet atelier
vise à les accompagner dans l'analyse et la structuration de leur projet ERP Retail.

## Input

- `CDC_ERP_Retail_Partie1.docx` : Cahier des charges pour l'implémentation d'un ERP chez un client retail (AO-2026-ERP-001)

## Livrables attendus

Le travail ici consiste à produire les artefacts amont nécessaires au TP :

1. **Contexte général** (`00-contexte/`) : glossaire, synthèse projet, cartographie existant
2. **Analyse par domaine métier** (`domaines/`) : spécifications, entités, règles de gestion par domaine
3. **Exigences transverses** (`exigences-transverses/`) : non-fonctionnelles, techniques, sécurité
4. **Zone d'exploration** (`exploration/`) : templates pour les 4 étapes du TP

Les participants de l'atelier utiliseront ces artefacts comme base pour prompter l'IA et réaliser :
- Cartographie des flux inter-modules
- Détection des zones de friction
- Matrice de dépendances et séquencement
- Questions d'arbitrage pour le client

## Suivi

- `PROGRESS.md` : suivi de progression par phase et par domaine
- `AUDIT.md` : journal de traçabilité des actions significatives

## Contraintes techniques

- Format exclusif : Markdown
- Structure organisée par domaine métier
- Les domaines sont provisoires et seront ajustés après analyse du CDC section 3.1

---

# Regles

## R1 - Commit après action significative

Après toute action significative (création de structure, remplissage d'un domaine, extraction
d'exigences, réconciliation, etc.), tu DOIS proposer un commit avec un message clair et descriptif.
Ne committe pas sans demander confirmation. Une action significative inclut :
- Création ou modification de la structure de répertoires
- Remplissage ou mise à jour substantielle d'un ou plusieurs fichiers
- Phase d'analyse complétée (ex: glossaire terminé, domaine analysé)
- Réconciliation ou correction inter-domaines

## R2 - Mise à jour du journal d'audit

Après toute action significative, tu DOIS mettre à jour `AUDIT.md` AVANT de proposer le commit.
Chaque entrée doit contenir :
- Horodatage
- Description de l'action réalisée
- Fichiers impactés
- Décisions prises ou points d'attention
