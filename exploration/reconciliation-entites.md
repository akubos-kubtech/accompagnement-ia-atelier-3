# Réconciliation des entités inter-domaines

> Analyse systématique des entités apparaissant dans **plusieurs modules**, avec comparaison des définitions, attributs et détection des écarts.
> Base : 7 domaines analysés (Phase 3), 21 fichiers `entites.md` / `specifications.md` / `regles-gestion.md`.

---

## Synthèse globale

| Entité | Modules concernés | Cohérent ? | Sévérité écart |
|--------|------------------|:----------:|:--------------:|
| [Article / Article stock](#1--article--article-stock) | GC, AA, SC, SE, FC, RB | **PARTIEL** | Haute |
| [Client / Compte tiers](#2--client--compte-tiers) | GC, CM, FC, RB | **PARTIEL** | Haute |
| [Fournisseur / Compte tiers](#3--fournisseur--compte-tiers-fournisseur) | AA, FC, SC, SE | **PARTIEL** | Moyenne |
| [Commande client](#4--commande-client) | GC, SC, SE, FC, CM | **OUI** | — |
| [Commande fournisseur / Proposition de commande](#5--commande-fournisseur--proposition-de-commande) | AA, SC, FC | **PARTIEL** | Moyenne |
| [Stock](#6--stock) | SE, GC, SC, FC | **PARTIEL** | Haute |
| [Mouvement de stock](#7--mouvement-de-stock) | SE, GC, AA, FC | **OUI** | — |
| [Réception](#8--réception) | AA, SE, FC | **PARTIEL** | Moyenne |
| [Entrepôt / Site de stockage / Magasin](#9--entrepôt--site-de-stockage--magasin) | SE, AA, SC, GC | **NON** | Haute |
| [Retour](#10--retour) | GC, SE | **OUI** | Basse |
| [Promotion / Tarif](#11--promotion--tarif) | GC, CM | **PARTIEL** | Moyenne |
| [Expédition](#12--expédition) | SC, SE | **PARTIEL** | Moyenne |
| [Écriture comptable](#13--écriture-comptable) | FC, SE | **OUI** | — |
| [Facture](#14--facture) | FC, AA, GC | **OUI** | Basse |
| [Indicateur / KPI](#15--indicateur--kpi) | SC, FC, RB | **PARTIEL** | Moyenne |
| [Prévision de vente](#16--prévision-de-vente) | SC, RB | **PARTIEL** | Moyenne |

> **Légende** : GC = Gestion commerciale, AA = Achats & Appro, SC = Supply Chain, FC = Finance, SE = Stocks & Entrepôts, CM = CRM & Marketing, RB = Reporting & BI

---

## Détail par entité

### 1 — Article / Article stock

| | Gestion commerciale | Stocks & Entrepôts |
|--|----|----|
| **Nom de l'entité** | Article | Article stock |
| **Attributs clés** | SKU, libellé, catégorie, prix de base, pays | SKU, type (standard/variante/sérialisé/lot/péremption), unité de gestion |
| **Focus** | Commercial (prix, catégorie, pays) | Physique (type article, traçabilité lot/série, péremption) |

**Autres modules référençant l'article** :
- **AA** : via Conditions commerciales (prix achat, remises), Ligne commande fournisseur (prix unitaire), Prix de revient (coût complet import)
- **SC** : via Prévision de vente (famille produit), Besoin net, Document douanier (nomenclature)
- **FC** : via Valorisation stock (valeur unitaire, méthode FIFO/CUMP)

**Cohérent ?** : **PARTIEL**

**Écarts détectés** :
1. **Nommage différent** — GC définit « Article », SE définit « Article stock ». S'agit-il de la même entité avec des vues différentes ou de deux entités distinctes ?
2. **Attributs disjoints** — GC ne mentionne pas les typologies stock (lot, série, péremption). SE ne mentionne pas les attributs commerciaux (prix, catégorie, pays). Aucun domaine ne porte l'ensemble complet des attributs.
3. **Prix multiples non réconciliés** — L'article a un « prix de base » (GC), un « prix d'achat » (AA), un « prix de revient » (AA/FC), une « valeur unitaire stock » (SE/FC). Ces 4 notions de prix coexistent sans hiérarchie explicite.
4. **Akeneo PIM absent du modèle d'entités** — Le CDC et `referentiels-transverses.md` mentionnent Akeneo comme source de données enrichies (descriptions, médias), mais aucun domaine ne modélise les attributs PIM dans son entité Article.

**Recommandation** : Définir une entité **Article** unique avec des groupes d'attributs par domaine (commercial, achat, stock, finance). Clarifier le périmètre ERP vs Akeneo PIM.

---

### 2 — Client / Compte tiers

| | Gestion commerciale | CRM & Marketing | Finance & Comptabilité |
|--|----|----|----|
| **Nom de l'entité** | Client | Client | Compte tiers |
| **Attributs clés** | N° client, type (B2C/B2B), catégorie, coordonnées | N° client, type (B2C/B2B), données identification, données compte, données comportementales | Tiers, encours, solde, conditions de paiement, dernière relance |
| **Focus** | Acheteur (commandes, devis) | Référentiel maître unifié (fidélité, segmentation, RGPD) | Vue financière (encours, paiement, recouvrement) |

**Cohérent ?** : **PARTIEL**

**Écarts détectés** :
1. **Nommage hétérogène** — GC et CM utilisent « Client ». FC utilise « Compte tiers » qui est une abstraction englobant Client ET Fournisseur. Le « Compte tiers » est un sous-compte comptable, pas un doublon du client — mais la relation entre les deux n'est pas formalisée.
2. **Granularité des attributs** — GC a 4 attributs sommaires. CM détaille identification + compte + comportement + fidélité. FC se concentre sur l'encours et le paiement. Il n'y a pas de modèle canonique unifié.
3. **Propriété du référentiel** — CM déclare « Fiche client unifiée (référentiel maître) » (entites.md) et RG-CM-01 confirme « L'ERP constitue le référentiel client maître ». GC modélise le Client indépendamment sans référence au référentiel maître CRM. **Qui crée un client ? GC (à la première commande) ou CM (inscription fidélité) ?**
4. **Coexistence Salesforce** — Le CDC mentionne une « synchronisation bidirectionnelle ERP↔Salesforce ». Mais aucun domaine ne modélise les attributs qui vivent côté Salesforce vs côté ERP.

**Recommandation** : Formaliser un modèle Client canonique (ERP = maître), avec vues spécialisées par domaine. Clarifier le processus de création du client et les attributs synchronisés avec Salesforce.

---

### 3 — Fournisseur / Compte tiers fournisseur

| | Achats & Approvisionnement | Finance & Comptabilité |
|--|----|----|
| **Nom de l'entité** | Fournisseur | Compte tiers (sous-type fournisseur) |
| **Attributs clés** | N° fournisseur, raison sociale, pays, devise, statut qualification, certifications, agréments | Tiers, encours, solde, conditions de paiement, dernière relance |
| **Focus** | Opérationnel (qualification, RSE, performance) | Financier (encours, règlements, three-way) |

**Autres modules référençant le fournisseur** :
- **SC** : dans Proposition de commande (fournisseur cible)
- **SE** : dans Lot (fournisseur d'origine pour traçabilité)

**Cohérent ?** : **PARTIEL**

**Écarts détectés** :
1. **Même abstraction que Client** — FC utilise « Compte tiers » pour Client et Fournisseur. Il n'est pas clair si c'est la même entité avec un attribut « type » ou deux entités distinctes.
2. **Attributs de qualification absents en FC** — Les attributs de qualification RSE, certifications et agréments (AA) ne sont pas visibles dans la vue Finance. Or, la qualification peut avoir un impact financier (conditions commerciales différentes).
3. **Devise** — AA porte la devise du fournisseur. FC gère la trésorerie multi-devises. La correspondance entre la « devise fournisseur » (AA) et les « 8 devises opérationnelles » (FC) n'est pas formalisée.

**Recommandation** : Distinguer dans le modèle l'entité Fournisseur (AA = maître) du Compte tiers fournisseur (FC = vue comptable). Formaliser le lien entre les deux.

---

### 4 — Commande client

| | Gestion commerciale (Maître) | Autres domaines |
|--|----|----|
| **Attributs** | N° commande, date, canal, statut, mode livraison, montant TTC | Consommateurs uniquement |

**Modules consommateurs** :
- **SC** : déclenchement des expéditions
- **SE** : réservation de stock, préparation, expédition
- **FC** : source des écritures de vente
- **CM** : historique d'achat pour fidélité et segmentation

**Cohérent ?** : **OUI**

GC est clairement le maître. Les autres domaines consomment sans redéfinir. Seule observation : CM utilise le terme raccourci « Commande » (sans « client »), ce qui pourrait créer une confusion avec « Commande fournisseur ».

---

### 5 — Commande fournisseur / Proposition de commande

| | Achats & Approvisionnement | Supply Chain & Logistique |
|--|----|----|
| **Nom de l'entité** | Commande fournisseur | Proposition de commande |
| **Attributs clés** | N° commande, date, fournisseur, devise, incoterm, statut, montant | Fournisseur, article, quantité, date besoin, statut validation |
| **Focus** | Engagement contractuel signé | Suggestion issue du MRP, à valider |

**Cohérent ?** : **PARTIEL**

**Écarts détectés** :
1. **Deux entités distinctes ou deux statuts d'une même entité ?** — SC crée des « Propositions de commande » (MRP) qui doivent devenir des « Commandes fournisseur » (AA). La frontière entre les deux n'est pas modélisée. Qui transforme la proposition en commande ?
2. **Attributs manquants sur la proposition** — La proposition de commande SC ne porte pas devise, incoterm ni montant, alors que ces attributs sont obligatoires (RG-AA-05, RG-AA-06) dès la commande fournisseur.
3. **FC référence uniquement « Commande fournisseur »** — pour le three-way matching. Les propositions non confirmées ne sont pas visibles en finance.

**Recommandation** : Modéliser un cycle de vie unifié : Proposition (SC) → Commande fournisseur (AA), avec enrichissement d'attributs au passage. Clarifier le workflow de validation.

---

### 6 — Stock

| | Stocks & Entrepôts (Maître) | Gestion commerciale | Supply Chain | Finance |
|--|----|----|----|----|
| **Vue** | Stock physique complet | Stock disponible à la vente | Stock pour MRP | Stock valorisé |
| **Attributs** | Article, site, emplacement, quantité, statut (5 statuts) | Disponibilité temps réel | Stock disponible, en-cours, réservations | Méthode (FIFO/CUMP/standard), valeur |
| **Focus** | Vérité physique | Promesse commerciale | Planification | Valeur comptable |

**Cohérent ?** : **PARTIEL**

**Écarts détectés** :
1. **Notion de « disponible » ambiguë** — Pour GC, « disponible » = vendable en ligne/magasin. Pour SE, « disponible » = un des 5 statuts (hors réservé, contrôle, retour, à détruire). Pour SC/MRP, « disponible » = stock - réservations - sécurité. Pour FC, le stock pris en compte pour la valorisation peut inclure tous les statuts sauf « à détruire ». **Chaque domaine a sa propre définition de « stock disponible ».**
2. **Transit non modélisé** — Le CDC mentionne le « stock en transit » dans les spécifications SE. Mais l'entité Stock de SE ne prévoit pas de statut « en transit ». SC a besoin de cette visibilité pour le MRP.
3. **Latence WMS** — GC exige une latence < 5 min (RG-GC-05) pour les ventes. SE mentionne le « temps réel » pour le WMS (RG-SE-08). La différence entre « temps réel WMS » et « quasi temps réel ERP » peut créer un écart de stock visible par le client.

**Recommandation** : Définir formellement les vues de stock par domaine (ATP = Available to Promise pour GC, MRP-disponible pour SC, valorisable pour FC). Ajouter le statut « en transit ».

---

### 7 — Mouvement de stock

| | Stocks & Entrepôts (Maître) |
|--|-----|
| **Attributs** | N° mouvement, type (entrée/sortie/transfert/ajustement), date, heure, quantité, origine |

**Modules déclencheurs** :
- **GC** : sortie (vente), entrée (retour réintégré)
- **AA** : entrée (réception validée)
- **SE** : ajustement (inventaire), transfert inter-sites
- **FC** : consomme pour écritures de variation

**Cohérent ?** : **OUI**

SE est clairement le maître. Les autres domaines déclenchent des mouvements via leurs processus propres. Le champ « origine » permet la traçabilité vers le domaine source.

---

### 8 — Réception

| | Achats & Approvisionnement | Stocks & Entrepôts | Finance & Comptabilité |
|--|----|----|-----|
| **Rôle** | Définit et contrôle la réception | Effectue la mise en stock physique | Utilise pour three-way matching |
| **Attributs** | N° réception, date, fournisseur, entrepôt, statut contrôle | (référence entrée en stock) | (référence pour rapprochement) |

**Cohérent ?** : **PARTIEL**

**Écarts détectés** :
1. **Ambiguïté de propriété physique** — AA définit la Réception (entité avec N° réception). Mais l'acte physique de réception se passe dans l'entrepôt (domaine SE). RG-AA-10 dit « la validation de la réception déclenche la mise en stock via WMS ». Qui porte le processus physique ?
2. **Double comptage des lignes** — AA a « Ligne réception » (quantité commandée, quantité reçue, écart, statut qualité). SE a « Mouvement de stock » (entrée). Les deux enregistrent une information de quantité reçue, potentiellement en doublon.
3. **FC et le three-way** — FC rapproche Commande / Réception / Facture (RG-FC-05). La réception FC est la même entité que AA, mais FC n'y a accès qu'en lecture. Le timing du rapprochement (à la validation AA ? après mise en stock SE ?) n'est pas précisé.

**Recommandation** : Clarifier que AA est maître du processus de réception (contrôle), SE exécute l'entrée physique (mouvement), et FC consomme le résultat. Documenter la séquence : contrôle AA → validation → mouvement SE → disponible pour three-way FC.

---

### 9 — Entrepôt / Site de stockage / Magasin

| | Stocks & Entrepôts | Achats & Appro | Supply Chain | Gestion commerciale |
|--|----|----|----|----|
| **Nom** | Site de stockage | Entrepôt | Entrepôt + Magasin | (Magasin implicite via POS) |
| **Attributs** | Type (entrepôt central/plateforme/magasin), code, capacité, adresse | Lieu de réception (référence) | Sites d'expédition + destinations magasins | Canal de vente |
| **Focus** | Structure physique complète | Point de réception marchandises | Nœud logistique | Point de vente |

**Cohérent ?** : **NON**

**Écarts détectés** :
1. **Trois noms pour des concepts qui se chevauchent** — SE définit « Site de stockage » (englobant entrepôt, plateforme, magasin). AA et SC utilisent « Entrepôt ». SC utilise aussi « Magasin » comme entité distincte dans l'Ordre de réapprovisionnement. GC ne modélise pas le magasin comme entité.
2. **Magasin : entité absente** — Le magasin est un type de Site de stockage (SE), un lieu de réapprovisionnement (SC), un canal de vente (GC) et un axe analytique (FC). Mais **aucun domaine ne le définit formellement comme entité avec tous ses attributs** (code, nom, adresse, capacité stockage, zone géo, directeur, horaires, etc.).
3. **Plateforme régionale** — SE mentionne « plateforme régionale » comme type de site. SC ne modélise pas ce concept alors qu'il est central pour le cross-docking (RG-SC-07).
4. **347 magasins non modélisés** — Le CDC mentionne 347 magasins. Aucun domaine ne porte le référentiel magasins.

**Recommandation** : Créer une entité **Site** unique (référentiel transversal) avec types : entrepôt central, plateforme régionale, magasin, transit. Chaque domaine consomme la vue pertinente. Le **Magasin** mérite probablement une entité spécialisée héritant de Site.

---

### 10 — Retour

| | Gestion commerciale (Maître) | Stocks & Entrepôts |
|--|----|----|
| **Attributs** | N° retour, motif, état produit, canal d'origine, statut | Réintégration ou destruction (mouvement de stock) |

**Cohérent ?** : **OUI**

GC gère le cycle de vie commercial du retour (validation, avoir). SE gère la conséquence physique (mouvement de stock). La frontière est claire. Point mineur : les règles de transition (RG-GC-08 : « réintégré en stock ou orienté vers déstockage ») pourraient être formalisées comme un workflow explicite entre GC et SE.

---

### 11 — Promotion / Tarif

| | Gestion commerciale | CRM & Marketing |
|--|----|----|
| **Entité** | Promotion : type (%, fixe, lot, offert), période, conditions | Campagne marketing → référence « Promotion » (non définie) |
| **Entité** | Tarif : type (base, B2B, promo), montant/%, dates, conditions | — |

**Cohérent ?** : **PARTIEL**

**Écarts détectés** :
1. **Promotion définie en GC, déclenchée par CM** — GC modélise la Promotion comme entité tarifaire. CM crée des Campagnes marketing qui « doivent être cohérentes avec les conditions tarifaires » (RG-CM-08). Mais CM ne modélise pas la Promotion dans ses entités — elle est juste référencée en partage.
2. **Pas d'interface de synchronisation** — Comment une campagne CM déclenche-t-elle la création/activation d'une Promotion GC ? Le CDC dit « vérification de cohérence » mais ne décrit pas le mécanisme.
3. **Points fidélité et tarification** — CM gère les paliers fidélité avec avantages (potentiellement des remises). GC gère la grille tarifaire. Comment les avantages fidélité CM s'intègrent-ils dans la cascade tarifaire GC (RG-GC-03 : prix base → B2B → promo → remise → soldes) ?

**Recommandation** : Ajouter les « avantages fidélité » dans la cascade tarifaire RG-GC-03. Formaliser le flux Campagne (CM) → Promotion (GC) avec un processus de validation croisée.

---

### 12 — Expédition

| | Supply Chain (Maître) | Stocks & Entrepôts |
|--|----|----|
| **Attributs** | N° expédition, origine, destination, transporteur, statut, date prévue | Ordres de préparation, confirmations d'expédition |
| **Focus** | Pilotage logistique (transport, tournées) | Exécution physique (picking, packing, chargement) |

**Cohérent ?** : **PARTIEL**

**Écarts détectés** :
1. **Frontière floue SC/SE** — SC crée l'Expédition (planification, affectation transporteur). SE exécute (picking, emballage, chargement). Mais SE ne modélise pas l'expédition dans ses entités — elle est référencée en partage. Qui met à jour le statut de l'expédition ?
2. **WMS au milieu** — SE interagit avec Manhattan (WMS) pour les ordres de préparation (RG-SE-08). SC suit l'expédition via les transporteurs. Le flux est : SC crée → SE prépare (via WMS) → SC suit (via transporteur). Deux systèmes externes distincts pour une même expédition.

**Recommandation** : Modéliser le cycle de vie complet de l'expédition : planifiée (SC) → en préparation (SE/WMS) → expédiée (SC/transporteur) → livrée. Clarifier les responsabilités de mise à jour du statut.

---

### 13 — Écriture comptable

| | Finance & Comptabilité (Maître) | Stocks & Entrepôts |
|--|----|----|
| **Attributs** | N° écriture, date, journal, compte débit/crédit, montant, libellé, pièce d'origine | Génère des écritures (variation stock, régularisation inventaire) |

**Cohérent ?** : **OUI**

FC est le maître. SE est un producteur d'écritures (via valorisation stock et inventaire). Le mécanisme est clairement documenté (RG-SE-07, RG-SE-13). Pas d'écart significatif.

---

### 14 — Facture

| | Finance & Comptabilité (Maître) | Achats & Appro | Gestion commerciale |
|--|----|----|-----|
| **Attributs** | N° facture, type (client/fournisseur), date, montant HT/TVA/TTC, format Factur-X, statut | Implicite dans three-way matching | Implicite (facture client liée à commande) |

**Cohérent ?** : **OUI**

FC est clairement le maître de la Facture. Ni AA ni GC ne définissent la facture dans leurs entités. Point mineur : le CDC mentionne que la facture client est liée à la Commande (GC) et la facture fournisseur au three-way (AA/SE/FC), mais ces liens ne sont pas formalisés dans les entités GC et AA.

---

### 15 — Indicateur / KPI

| | Supply Chain | Finance & Comptabilité | Reporting & BI |
|--|----|----|----|
| **Nom** | Indicateur supply chain | Axe analytique | Indicateur KPI |
| **Attributs** | Nom, valeur, objectif, tendance, alerte | Type (canal, famille, zone, magasin), code, libellé | Nom, formule de calcul, unité, source, domaine, fréquence, objectif |
| **Focus** | KPI opérationnels SC | Dimensions d'analyse | Définition formelle et gouvernance |

**Cohérent ?** : **PARTIEL**

**Écarts détectés** :
1. **Indicateurs définis en silo** — SC définit « Indicateur supply chain » avec ses propres attributs. RB définit « Indicateur KPI » avec une structure différente (plus riche : formule, unité, source). RG-RB-08 exige une « définition unique de chaque indicateur ». Contradiction : si chaque domaine définit ses propres indicateurs, la définition n'est pas unique.
2. **Axes analytiques FC vs dimensions RB** — FC définit des « Axes analytiques » (canal, famille, zone, magasin). RB expose ces mêmes dimensions dans le « Modèle sémantique ». La gouvernance de ces axes n'est pas assignée (FC ? RB ?).

**Recommandation** : Centraliser la définition des KPI dans le Dictionnaire de données (RB). Les domaines (SC, FC, etc.) définissent les métriques métier, RB les formalise et les gouverne.

---

### 16 — Prévision de vente

| | Supply Chain (Maître) | Reporting & BI |
|--|----|----|
| **Attributs** | Famille produit, période, quantité prévue, méthode, facteurs exogènes | Prévision IA (anomalies, tendances, recommandations) |
| **Focus** | Planification MRP (algorithmes paramétrables) | Analyse prédictive IA native |

**Cohérent ?** : **PARTIEL**

**Écarts détectés** :
1. **Double source de prévision** — SC produit des prévisions via des « algorithmes paramétrables par famille » (RG-SC-01). RB mentionne « prévision de la demande assistée par IA ». Le CDC dit que « les prévisions IA alimentent la planification de la demande ». **Qui produit la prévision finale ? SC (méthodes classiques) ou RB (IA) ? Les deux se nourrissent-ils mutuellement ?**
2. **Pas de modèle d'intégration** — Le flux RB (IA) → SC (MRP) n'est pas formalisé. Comment les prévisions IA sont-elles injectées dans le calcul MRP ?

**Recommandation** : Clarifier que SC est consommateur des prévisions (pour le MRP) et que la source peut être hybride (algorithmes SC classiques enrichis par IA/RB). Documenter l'interface.

---

## Matrice des écarts critiques

| # | Écart | Entités | Domaines | Impact |
|---|-------|---------|----------|--------|
| E1 | Article : nommage et attributs disjoints entre vue commerciale et vue stock | Article / Article stock | GC ↔ SE | Risque de double maintenance, incohérence des données article |
| E2 | Client : 3 modélisations différentes sans modèle canonique | Client / Compte tiers | GC ↔ CM ↔ FC | Risque de désynchronisation du référentiel maître |
| E3 | Stock « disponible » : 4 définitions concurrentes | Stock | GC ↔ SE ↔ SC ↔ FC | Sur-vente (promesse > réalité) ou sous-optimisation MRP |
| E4 | Magasin : entité utilisée par 4 domaines, définie par aucun | Site / Entrepôt / Magasin | SE ↔ SC ↔ GC ↔ FC | Pas de référentiel magasins — bloquant pour le paramétrage |
| E5 | Proposition de commande → Commande fournisseur : lifecycle non modélisé | Proposition / Commande fournisseur | SC ↔ AA | Perte d'information au passage, workflow non défini |
| E6 | Réception : propriété partagée AA/SE sans séquence formalisée | Réception | AA ↔ SE ↔ FC | Risque de double saisie, timing du three-way matching flou |
| E7 | Promotion : déclenchée par CM, définie en GC, sans interface | Promotion / Campagne | GC ↔ CM | Incohérence entre offres marketing et tarification ERP |
| E8 | Prévision : double source (SC classique + RB IA) sans arbitrage | Prévision de vente | SC ↔ RB | Prévisions contradictoires ou non utilisées |

---

## Questions d'arbitrage pour le client

Ces questions découlent directement des écarts identifiés et devront être tranchées lors de l'atelier :

1. **Article unique ou vues multiples ?** — L'article est-il une entité unique avec des attributs enrichis par domaine (ERP + PIM), ou chaque domaine maintient-il sa propre fiche ?

2. **Qui crée le client ?** — Le premier point de contact est-il le magasin (GC), le site web (GC), l'inscription fidélité (CM) ? Quel est le processus de création qui alimente le référentiel maître ?

3. **Quel stock promettre au client ?** — Définir formellement l'ATP (Available to Promise) : stock physique - réservé - seuil sécurité - en transit entrant ? Quelle latence est acceptable entre le stock WMS et le stock promis en ligne ?

4. **Le magasin est-il un domaine ?** — Avec 347 magasins, un référentiel magasins transversal est nécessaire. Quel domaine le porte ? SE (site de stockage), GC (point de vente), ou un nouveau référentiel transverse ?

5. **MRP : qui transforme la proposition en commande ?** — Le passage de la proposition SC à la commande AA est-il automatique (avec enrichissement) ou nécessite-t-il une validation manuelle de l'acheteur ?

6. **Cascade tarifaire et fidélité** — Où s'insèrent les avantages fidélité (CM) dans la cascade tarifaire (GC) : prix base → B2B → promo → **fidélité** → remise → soldes ?

7. **Prévision IA vs prévision classique** — L'IA (RB) remplace-t-elle les algorithmes de prévision SC, les enrichit-elle, ou coexistent-elles avec un mécanisme de réconciliation ?

8. **KPI : gouvernance centralisée ou fédérée ?** — Les indicateurs sont-ils définis par chaque domaine (SC, FC) et formalisés par RB, ou RB impose-t-il les définitions aux domaines ?