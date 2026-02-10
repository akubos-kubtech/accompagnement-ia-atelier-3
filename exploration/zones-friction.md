# Etape 2 : Détection des zones de friction

> Croisement systématique des analyses inter-domaines : réconciliation des entités (16 entités, 8 écarts critiques), cartographie des flux (46 flux, 5 absents), règles de gestion (82 RG), exigences non-fonctionnelles et intégrations cibles.

## Synthèse

| Type de friction | Nombre | Bloquants | Majeurs | Mineurs |
|-----------------|:------:|:---------:|:-------:|:-------:|
| 1 — Incohérences de définition | 6 | 1 | 4 | 1 |
| 2 — Flux orphelins | 5 | 1 | 3 | 1 |
| 3 — Hypothèses contradictoires | 5 | 2 | 2 | 1 |
| 4 — Règles de gestion en conflit | 5 | 1 | 3 | 1 |
| 5 — Référentiels non définis | 4 | 1 | 2 | 1 |
| 6 — Séquencement impossible | 4 | 2 | 2 | 0 |
| **Total** | **29** | **8** | **16** | **5** |

---

## Type 1 — Incohérences de définition

> Même entité définie différemment selon les modules.
> Source principale : `exploration/reconciliation-entites.md`

---

### FRIC-101 — Article : deux entités, attributs disjoints

| Propriété | Valeur |
|-----------|--------|
| **Type** | 1 — Incohérence de définition |
| **Modules** | GC, SE (+ AA, SC, FC consommateurs) |
| **Sévérité** | **MAJEUR** |

**Description** : GC définit « Article » (SKU, libellé, catégorie, prix de base, pays). SE définit « Article stock » (SKU, type standard/variante/sérialisé/lot/péremption, unité de gestion). Les attributs sont disjoints : GC ignore les typologies stock, SE ignore les attributs commerciaux. De plus, 4 notions de prix coexistent sans hiérarchie : prix de base (GC), prix d'achat (AA), prix de revient (AA/FC), valeur unitaire stock (SE/FC).

**Impact** : Double maintenance des données article. Risque d'incohérence entre la vue commerciale et la vue stock d'un même produit. Ambiguïté sur le « prix » d'un article selon le contexte.

> Réf. : reconciliation-entites.md §1, écart E1

---

### FRIC-102 — Client : trois modélisations sans modèle canonique

| Propriété | Valeur |
|-----------|--------|
| **Type** | 1 — Incohérence de définition |
| **Modules** | CM, GC, FC |
| **Sévérité** | **MAJEUR** |

**Description** : CM déclare « Client — fiche unifiée, référentiel maître » (N° client, identification, compte, comportement). GC modélise « Client » indépendamment (N° client, type, catégorie, coordonnées — 4 attributs). FC utilise « Compte tiers » (tiers, encours, solde, conditions paiement) qui fusionne conceptuellement Client et Fournisseur. Aucun modèle canonique ne réconcilie ces 3 vues.

**Impact** : Impossible de déterminer le schéma physique de la table Client. Risque de désynchronisation entre les vues vente / fidélité / finance. Le processus de création d'un client (premier achat GC ? inscription fidélité CM ?) n'est pas défini.

> Réf. : reconciliation-entites.md §2, écart E2

---

### FRIC-103 — Stock « disponible » : quatre définitions concurrentes

| Propriété | Valeur |
|-----------|--------|
| **Type** | 1 — Incohérence de définition |
| **Modules** | GC, SE, SC, FC |
| **Sévérité** | **BLOQUANT** |

**Description** : Chaque domaine a sa propre sémantique du stock « disponible » :
- **SE** : statut « disponible » (hors réservé, contrôle, retour, à détruire) — vérité physique
- **GC** : ATP (Available to Promise) = vendable en ligne/magasin — promesse commerciale
- **SC** : stock MRP = disponible - réservations - seuil sécurité + en-cours approvisionnement — planification
- **FC** : stock valorisable = tous statuts sauf « à détruire » — valeur comptable

De plus, le statut « en transit » n'est modélisé dans aucun domaine (reconciliation-entites.md §6) alors que le CDC mentionne le stock en transit dans les spécifications SE.

**Impact** : Sans définition formelle de l'ATP, risque de sur-vente (promesse web > stock réel) ou de sous-exploitation (stock physiquement disponible mais non proposé à la vente). Le MRP peut générer des commandes inutiles si sa vision du stock diverge de la réalité SE.

> Réf. : reconciliation-entites.md §6, écart E3

---

### FRIC-104 — Entrepôt / Site / Magasin : trois noms, zéro définition unifiée

| Propriété | Valeur |
|-----------|--------|
| **Type** | 1 — Incohérence de définition |
| **Modules** | SE, AA, SC, GC, FC |
| **Sévérité** | **MAJEUR** |

**Description** : SE définit « Site de stockage » (type : entrepôt central / plateforme / magasin). AA et SC utilisent « Entrepôt ». SC utilise aussi « Magasin » comme entité distincte dans l'Ordre de réapprovisionnement. GC traite le magasin comme un canal de vente (POS). FC l'utilise comme axe analytique. Le « Magasin » — utilisé par 5 domaines — **n'est formellement défini par aucun d'entre eux** (ni attributs, ni référentiel complet).

**Impact** : Impossible de paramétrer les 347 magasins sans un référentiel magasins transversal. La « Plateforme régionale » (mentionnée par SE pour le cross-docking) n'est référencée nulle part ailleurs.

> Réf. : reconciliation-entites.md §9, écart E4

---

### FRIC-105 — Commande fournisseur : frontière floue entre proposition et engagement

| Propriété | Valeur |
|-----------|--------|
| **Type** | 1 — Incohérence de définition |
| **Modules** | SC, AA, FC |
| **Sévérité** | **MAJEUR** |

**Description** : SC crée des « Propositions de commande » (fournisseur, article, quantité, date besoin, statut validation). AA gère des « Commandes fournisseur » (N° commande, date, devise, incoterm, statut, montant). La proposition SC ne porte ni devise, ni incoterm, ni montant — attributs obligatoires en AA (RG-AA-05, RG-AA-06). Le mécanisme de transformation proposition → commande n'est décrit dans aucun domaine.

**Impact** : Sans workflow de conversion, soit les propositions MRP restent lettre morte (pas de commande réelle), soit elles sont converties sans les attributs obligatoires (commandes incomplètes). FC ne peut pas faire de three-way matching sur une proposition.

> Réf. : reconciliation-entites.md §5, écart E5, flux-inter-modules.md FLUX-C02

---

### FRIC-106 — Indicateurs KPI : définitions en silo

| Propriété | Valeur |
|-----------|--------|
| **Type** | 1 — Incohérence de définition |
| **Modules** | SC, FC, RB |
| **Sévérité** | MINEUR |

**Description** : SC définit « Indicateur supply chain » (nom, valeur, objectif, tendance, alerte). RB définit « Indicateur KPI » (nom, formule de calcul, unité, source, domaine, fréquence, objectif). FC définit « Axe analytique » (type, code, libellé) partagé avec RB. RG-RB-08 exige une « définition unique de chaque indicateur » — en contradiction avec le fait que chaque domaine définit ses propres KPI.

**Impact** : Deux domaines pourraient calculer le même indicateur (ex : marge) avec des formules différentes. Risque de reporting incohérent.

> Réf. : reconciliation-entites.md §15, flux-inter-modules.md dépendance #5

---

## Type 2 — Flux orphelins

> Un module émet une donnée que personne ne consomme, ou un module a besoin d'une donnée que personne n'émet.
> Source principale : `exploration/flux-inter-modules.md`

---

### FRIC-201 — Vente POS : flux de sortie stock non modélisé

| Propriété | Valeur |
|-----------|--------|
| **Type** | 2 — Flux orphelin (besoin sans émetteur) |
| **Modules** | GC, SE |
| **Sévérité** | **BLOQUANT** |

**Description** : Les flux inter-modules modélisent le cycle web (commande → réservation → expédition → mouvement stock), mais **la vente POS immédiate n'a aucun flux de sortie stock dans la cartographie**. Le FLUX-A01 (réservation stock) concerne les commandes client, pas les ventes caisse immédiates. Le document `integrations-cibles.md` mentionne « Caisses → ERP : Mouvements de stock magasin (< 5 min) » mais aucun FLUX-xxx ne le modélise côté inter-modules.

De plus, une transaction caisse unique déclenche **simultanément** :
- Remontée de la vente → GC (FLUX-A05)
- Cumul/utilisation points fidélité → CM (FLUX-F04/F05)
- Mouvement de stock → SE (non modélisé)
- Écriture comptable → FC (via GC, FLUX-A05)

**Impact** : Le scénario le plus fréquent du système (vente en magasin, 347 POS) n'a pas de modèle de flux complet. Risque de divergence stock magasin/ERP.

> Réf. : flux-inter-modules.md — absence de flux POS→SE, integrations-cibles.md Cegid §

---

### FRIC-202 — Annulation commande : libération de stock absente

| Propriété | Valeur |
|-----------|--------|
| **Type** | 2 — Flux orphelin (besoin sans émetteur) |
| **Modules** | GC, SE |
| **Sévérité** | **MAJEUR** |

**Description** : La réservation de stock est documentée (FLUX-A01 : GC→SE à la validation commande). Mais le flux inverse — libération de la réservation en cas d'annulation — n'existe pas (FLUX-A09, identifié comme ABSENT dans la cartographie).

**Impact** : Le stock réservé pour des commandes annulées reste indisponible. Érosion progressive de l'ATP, sur-stockage apparent, divergence stock physique / stock logique.

> Réf. : flux-inter-modules.md FLUX-A09

---

### FRIC-203 — Alertes IA : émises par RB, consommées par personne

| Propriété | Valeur |
|-----------|--------|
| **Type** | 2 — Flux orphelin (émetteur sans consommateur) |
| **Modules** | RB → Tous |
| **Sévérité** | MINEUR |

**Description** : RB spécifie l'analyse prédictive, la détection d'anomalies et les recommandations d'actions (RB spec P5, RG-RB-10, entité « Alerte IA »). Mais **aucun domaine récepteur ne modélise comment il intègre ces alertes** dans ses processus métier. L'alerte est émise dans le vide.

**Impact** : L'IA reste un outil de consultation passive. Les anomalies détectées ne déclenchent aucune action automatisée.

> Réf. : flux-inter-modules.md FLUX-G05

---

### FRIC-204 — Encours client : FC le calcule, GC ne le consomme pas

| Propriété | Valeur |
|-----------|--------|
| **Type** | 2 — Flux orphelin (émetteur sans consommateur modélisé) |
| **Modules** | FC, GC |
| **Sévérité** | **MAJEUR** |

**Description** : FC gère les encours clients et le recouvrement (FC spec P2 : suivi encours, relances). GC gère la prise de commande. Mais aucun flux ne transporte l'encours de FC vers GC pour bloquer les commandes en dépassement (FLUX-D06, identifié comme ABSENT).

**Impact** : Des commandes acceptées pour des clients en impayé. Risque financier non contrôlé.

> Réf. : flux-inter-modules.md FLUX-D06

---

### FRIC-205 — Client CM → Compte tiers FC : flux d'alimentation absent

| Propriété | Valeur |
|-----------|--------|
| **Type** | 2 — Flux orphelin (besoin sans émetteur) |
| **Modules** | CM, FC |
| **Sévérité** | **MAJEUR** |

**Description** : CM est le référentiel client maître (RG-CM-01). FC gère le « Compte tiers » (encours, conditions de paiement, relances). Mais aucun flux ne relie la création d'un client en CM à la création de son compte tiers en FC (FLUX-F03, identifié comme ABSENT).

**Impact** : Création manuelle des comptes tiers en comptabilité. Désynchronisation entre le référentiel client et la comptabilité auxiliaire. Risque d'erreur sur les conditions de paiement.

> Réf. : flux-inter-modules.md FLUX-F03, reconciliation-entites.md §2

---

## Type 3 — Hypothèses contradictoires

> Un module suppose un mode de fonctionnement (synchrone, temps réel, batch) qu'un autre module contredit.
> Sources : flux-inter-modules.md, exigences-transverses/non-fonctionnelles.md, integrations-cibles.md

---

### FRIC-301 — Transaction POS : synchrone (2s) ET asynchrone (5 min) dans le même geste

| Propriété | Valeur |
|-----------|--------|
| **Type** | 3 — Hypothèse contradictoire |
| **Modules** | GC, CM, SE |
| **Sévérité** | **BLOQUANT** |

**Description** : Une seule transaction caisse déclenche des flux avec des SLA incompatibles :

| Flux | SLA | Mode |
|------|-----|------|
| Consultation solde fidélité (CM → POS) | < **2s** | Synchrone, bloquant la transaction |
| Utilisation points comme paiement (CM ↔ POS) | < **2s** | Synchrone, bloquant |
| Remontée vente (POS → GC) | < 5 min | Asynchrone |
| Mouvement stock (POS → SE) | < 5 min | Asynchrone |

Le flux fidélité est **synchrone au milieu de la transaction caisse** (le client attend), tandis que les autres flux sont asynchrones. Cela impose deux patterns d'intégration distincts dans une même opération métier.

**Impact** : Si le service fidélité CM est indisponible (même 2s de timeout), la transaction caisse est bloquée. Or les modes dégradés (ENF : « caisse autonome 48h ») ne mentionnent pas le fonctionnement de la fidélité en mode dégradé.

> Réf. : integrations-cibles.md Cegid §, non-fonctionnelles.md modes dégradés, RG-CM-04, RG-CM-05

---

### FRIC-302 — Stock web : promesse < 2s, réalité WMS < 1 min

| Propriété | Valeur |
|-----------|--------|
| **Type** | 3 — Hypothèse contradictoire |
| **Modules** | GC, SE |
| **Sévérité** | **BLOQUANT** |

**Description** : Le site e-commerce affiche la disponibilité stock en temps réel (integrations-cibles : ERP → e-commerce, SLA < 2s). Mais le stock ERP est synchronisé avec le WMS avec une latence < 1 min (integrations-cibles : WMS → ERP). Et la synchronisation stock globale admet une latence de 5 min en normal, 15 min en pic (ENF traitements batch).

Cela crée une **fenêtre d'incohérence** : le stock affiché au client web peut être en retard de 1 à 15 min sur la réalité physique. Pendant un pic (Black Friday, charge x3), un article très demandé peut être « disponible » en ligne alors qu'il est physiquement épuisé.

**Impact** : Sur-vente en période de pic. Commandes acceptées pour des articles indisponibles. Expérience client dégradée (annulation post-commande).

> Réf. : integrations-cibles.md SF Commerce Cloud § + Manhattan §, non-fonctionnelles.md pics + traitements batch

---

### FRIC-303 — Réapprovisionnement : seuil temps réel vs MRP batch, double déclenchement

| Propriété | Valeur |
|-----------|--------|
| **Type** | 3 — Hypothèse contradictoire |
| **Modules** | AA, SC |
| **Sévérité** | **MAJEUR** |

**Description** : Deux mécanismes de réapprovisionnement coexistent avec des temporalités incompatibles :
- **RG-AA-03** : réapprovisionnement automatique au franchissement du seuil → **temps réel**, article par article
- **RG-SC-02** : calcul des besoins nets MRP → **batch quotidien** (ENF : terminé avant 6h00), toutes références

Rien n'empêche les deux mécanismes de déclencher une commande pour le même article au même moment : le seuil SE déclenche un réappro AA, et le MRP SC génère une proposition pour le même article.

**Impact** : Double commande fournisseur pour un même besoin. Sur-stock. Surcoût d'approvisionnement.

> Réf. : RG-AA-03, RG-SC-02, non-fonctionnelles.md batch MRP, flux-inter-modules.md FLUX-B05 + FLUX-C02

---

### FRIC-304 — Mode dégradé magasin : 48h autonome, mais fidélité muette

| Propriété | Valeur |
|-----------|--------|
| **Type** | 3 — Hypothèse contradictoire |
| **Modules** | GC, CM |
| **Sévérité** | **MAJEUR** |

**Description** : Les exigences non-fonctionnelles définissent un mode dégradé magasin : « caisse autonome, transactions locales, synchronisation différée — 48 heures minimum ». Mais RG-CM-04 exige que la fidélité fonctionne « en temps réel avec les caisses magasins ». En mode dégradé, la caisse n'a plus accès au solde de points du client, ne peut pas cumuler de points, et ne peut pas accepter le paiement par points (RG-CM-05).

Aucun mode dégradé spécifique à la fidélité n'est documenté.

**Impact** : Pendant une panne de 48h (scénario prévu par les ENF), les 347 magasins fonctionnent sans programme fidélité. Les clients fidèles ne peuvent pas utiliser leurs points. Les points des ventes réalisées pendant la panne sont perdus ou doivent être rétro-crédités — processus non documenté.

> Réf. : non-fonctionnelles.md modes dégradés, RG-CM-04, RG-CM-05

---

### FRIC-305 — Prévision IA (RB) vs MRP batch (SC) : timing incompatible

| Propriété | Valeur |
|-----------|--------|
| **Type** | 3 — Hypothèse contradictoire |
| **Modules** | RB, SC |
| **Sévérité** | MINEUR |

**Description** : Le MRP tourne en batch « terminé avant 6h00 » (ENF). RB produit des prévisions IA (RB spec P5, FLUX-G04) mais ne spécifie ni la fréquence de production ni le timing par rapport au MRP. Si les prévisions IA sont produites après 6h, elles ne sont intégrées qu'au cycle MRP suivant (J+1).

**Impact** : Latence d'un jour entre une prévision IA et son exploitation par le MRP. Pertinence réduite des prévisions pour les articles à forte rotation.

> Réf. : non-fonctionnelles.md batch MRP, flux-inter-modules.md FLUX-G04

---

## Type 4 — Règles de gestion en conflit

> La règle d'un module contredit ou ignore celle d'un autre.
> Sources : regles-gestion.md des 7 domaines, reconciliation-entites.md

---

### FRIC-401 — Cascade tarifaire GC incomplète : avantages fidélité CM absents

| Propriété | Valeur |
|-----------|--------|
| **Type** | 4 — RG en conflit |
| **Modules** | GC, CM |
| **Sévérité** | **BLOQUANT** |

**Description** : RG-GC-03 définit la cascade tarifaire : `prix de base → grille B2B → promotion → remise conditionnelle → soldes`. RG-CM-06 définit des paliers fidélité associés à des « avantages spécifiques déclenchés automatiquement ». Ces avantages (potentiellement des remises) **ne figurent pas dans la cascade tarifaire GC**. Aucune règle ne précise :
- À quel niveau de la cascade les avantages fidélité s'appliquent
- Si les avantages fidélité sont cumulables avec les promotions et remises
- Le mécanisme d'intégration CM→GC pour injecter les avantages dans le calcul du prix

**Impact** : Les avantages fidélité sont soit ignorés par le moteur de prix (client lésé), soit appliqués en plus de tout le reste (marge érodée), soit appliqués de manière incohérente selon le canal.

> Réf. : RG-GC-03, RG-CM-06, flux-inter-modules.md FLUX-F01 + dépendance cachée #1

---

### FRIC-402 — Réappro seuil (AA) vs MRP (SC) : périmètres non arbitrés

| Propriété | Valeur |
|-----------|--------|
| **Type** | 4 — RG en conflit |
| **Modules** | AA, SC |
| **Sévérité** | **MAJEUR** |

**Description** : RG-AA-03 dit : « le mode principal de réapprovisionnement pour les articles à rotation régulière est le déclenchement automatique au franchissement du seuil ». RG-SC-02 dit : « le calcul des besoins nets intègre stocks, commandes en cours, réservations et seuils de sécurité ». Les deux règles couvrent le même besoin (réapprovisionnement) pour le même périmètre (articles à rotation régulière) sans arbitrage :
- RG-AA-03 est réactif (stock franchit un seuil → commande)
- RG-SC-02 est prédictif (MRP calcule un besoin net → proposition)

**Impact** : Double commande fournisseur (cf. FRIC-303). Incertitude pour les acheteurs : doivent-ils valider les propositions MRP si le réappro auto a déjà commandé ?

> Réf. : RG-AA-03, RG-SC-02, FRIC-303

---

### FRIC-403 — Méthode de valorisation : décision SE ou FC ?

| Propriété | Valeur |
|-----------|--------|
| **Type** | 4 — RG en conflit |
| **Modules** | SE, FC |
| **Sévérité** | **MAJEUR** |

**Description** : RG-SE-11 dit : « la valorisation supporte FIFO, CUMP et prix standard selon les besoins comptables ». L'entité « Valorisation stock » est définie en SE avec l'attribut « méthode ». RG-FC-01 dit : « l'ERP garantit la conformité aux normes comptables locales de chacun des 15 pays ». La méthode de valorisation est imposée par la norme comptable locale (ex : CUMP en France, FIFO dans d'autres pays). **Qui porte la règle de choix de la méthode** : SE (qui exécute le calcul) ou FC (qui connaît les normes locales) ?

**Impact** : Si SE choisit la méthode, il doit connaître les normes comptables par pays. Si FC impose la méthode, il doit paramétrer SE. La frontière de responsabilité n'est pas tracée.

> Réf. : RG-SE-11, RG-FC-01, reconciliation-entites.md §6

---

### FRIC-404 — Promotions : cohérence centralisée (GC) vs campagnes externes (CM/Salesforce)

| Propriété | Valeur |
|-----------|--------|
| **Type** | 4 — RG en conflit |
| **Modules** | GC, CM |
| **Sévérité** | **MAJEUR** |

**Description** : RG-GC-04 exige une « gestion centralisée des règles tarifaires, appliquées de manière cohérente sur tous les canaux ». RG-CM-09 dit que « les campagnes multi-canal complexes sont gérées via Salesforce Marketing Cloud ». RG-CM-08 mentionne une « vérification de cohérence entre offres promotionnelles et conditions tarifaires » sans décrire le mécanisme.

Si une campagne marketing crée une promotion dans Salesforce (hors ERP), le moteur de prix centralisé GC n'en a pas connaissance. Le CDC décrit le besoin de cohérence mais pas le moyen de l'assurer.

**Impact** : Un client peut se voir appliquer une promotion Salesforce non reconnue par l'ERP (prix différent entre le mail marketing et la caisse). Ou inversement, une promotion ERP peut contredire l'offre marketing.

> Réf. : RG-GC-04, RG-CM-08, RG-CM-09, flux-inter-modules.md FLUX-F02

---

### FRIC-405 — Référentiel client maître : CM déclare, GC modélise indépendamment

| Propriété | Valeur |
|-----------|--------|
| **Type** | 4 — RG en conflit |
| **Modules** | CM, GC |
| **Sévérité** | MINEUR |

**Description** : RG-CM-01 déclare « l'ERP constitue le référentiel client maître, synchronisé avec Salesforce CRM ». Mais GC modélise l'entité « Client » dans son propre domaine (entites.md) avec ses propres attributs, sans référence au référentiel maître CM. GC pourrait créer un client à la première commande web sans passer par le processus CM.

**Impact** : Risque de création de clients en dehors du processus de qualité CM (déduplication, RGPD). Doublons potentiels.

> Réf. : RG-CM-01, reconciliation-entites.md §2

---

## Type 5 — Référentiels non définis

> Données supposées existantes par plusieurs modules mais jamais décrites formellement.
> Sources : reconciliation-entites.md, flux-inter-modules.md, domaines/*.md

---

### FRIC-501 — Magasin : référentiel absent, utilisé par 5 domaines

| Propriété | Valeur |
|-----------|--------|
| **Type** | 5 — Référentiel non défini |
| **Modules** | SE, SC, GC, FC, AA |
| **Sévérité** | **BLOQUANT** |

**Description** : Le CDC mentionne 347 magasins (600 en cible 2030). Chaque domaine utilise le « Magasin » :
- **SE** : type de « Site de stockage » (capacité, emplacements)
- **SC** : destination des « Ordres de réapprovisionnement » (profil vente local, contraintes livraison)
- **GC** : point de vente / canal (POS, click & collect, ship from store)
- **FC** : axe analytique (RG-FC-10 : « magasin » est un axe d'analyse)
- **AA** : lieu de réception (certains magasins reçoivent en direct)

**Aucun domaine ne porte le référentiel magasins** avec l'ensemble des attributs : code, nom, adresse, surface, zone géographique, directeur, horaires, capacité stockage, profil de vente, WMS local (oui/non), etc.

**Impact** : Bloquant pour le paramétrage initial (347 magasins à configurer). Sans référentiel, chaque domaine maintient sa propre « fiche magasin » partielle.

> Réf. : reconciliation-entites.md §9, écart E4

---

### FRIC-502 — Transaction POS : modèle absent

| Propriété | Valeur |
|-----------|--------|
| **Type** | 5 — Référentiel non défini |
| **Modules** | GC, SE, CM, FC |
| **Sévérité** | **MAJEUR** |

**Description** : La vente en magasin (POS) est le scénario transactionnel le plus fréquent (347 magasins, majorité du CA). Pourtant :
- GC modélise la « Commande client » (web, B2B), pas la vente POS immédiate
- Aucune entité « Ticket de caisse » ou « Transaction POS » n'existe dans les domaines
- Les flux POS sont documentés dans `integrations-cibles.md` (interface Cegid) mais pas dans la cartographie inter-modules
- Le processus complet d'une vente POS (scan → prix → fidélité → paiement → stock → comptabilité) n'apparaît dans aucune spécification fonctionnelle

**Impact** : Le scénario le plus fréquent n'a pas de modèle de données ni de séquence de flux. Risque d'oubli fonctionnel majeur lors de l'implémentation.

> Réf. : flux-inter-modules.md — absence de chronologie POS, FRIC-201, FRIC-301

---

### FRIC-503 — Pays : référentiel structurant absent

| Propriété | Valeur |
|-----------|--------|
| **Type** | 5 — Référentiel non défini |
| **Modules** | GC, FC, AA, SC |
| **Sévérité** | **MAJEUR** |

**Description** : Le « Pays » conditionne :
- **GC** : prix de base par pays (entités Article)
- **FC** : normes comptables locales (RG-FC-01), régimes TVA (RG-FC-06), devises (RG-FC-12), Factur-X / SDI / SII / SAF-T selon pays
- **AA** : fournisseurs par pays (35 pays), devise fournisseur
- **SC** : nomenclature douanière, accords commerciaux, droits de douane (RG-SC-08)

Le CDC mentionne 15 pays actuels (25 en 2030), 8 devises, 14 langues. Pourtant, aucun domaine ne modélise un référentiel « Pays » avec ses attributs structurants (devise, langue, norme comptable, régime TVA, plateforme fiscale, WMS utilisé, etc.).

**Impact** : Le déploiement multi-pays est un enjeu stratégique. Sans référentiel pays, chaque domaine doit paramétrer indépendamment les spécificités nationales — risque d'incohérence.

> Réf. : RG-FC-01, RG-FC-06, RG-SC-08, non-fonctionnelles.md multilingue

---

### FRIC-504 — Devise : référentiel opérationnel non modélisé

| Propriété | Valeur |
|-----------|--------|
| **Type** | 5 — Référentiel non défini |
| **Modules** | AA, FC, GC |
| **Sévérité** | MINEUR |

**Description** : AA mentionne la « devise fournisseur » (RG-AA-05). FC gère « 8 devises opérationnelles » (RG-FC-12) et les couvertures de change. GC mentionne les « prix par pays ». Mais aucun référentiel devise (code, taux de change, date de cours, source du cours) n'est modélisé.

**Impact** : Risque mineur (les devises sont généralement gérées nativement par les ERP). Mais le mécanisme de mise à jour des taux de change et le lien avec les couvertures FC méritent d'être documentés.

> Réf. : RG-AA-05, RG-FC-12

---

## Type 6 — Séquencement impossible

> Un module a besoin d'une donnée qu'un autre module ne produit qu'après, ou dépendance circulaire.
> Sources : flux-inter-modules.md chronologies, regles-gestion.md

---

### FRIC-601 — Paiement par points fidélité : dépendance circulaire CM ↔ GC

| Propriété | Valeur |
|-----------|--------|
| **Type** | 6 — Séquencement impossible |
| **Modules** | GC, CM |
| **Sévérité** | **BLOQUANT** |

**Description** : Lors d'un paiement par points en caisse :
1. **GC a besoin du solde de points** (CM) pour proposer l'option de paiement par points → GC dépend de CM
2. **CM a besoin du montant final de la transaction** (GC, après cascade tarifaire) pour calculer les points à débiter → CM dépend de GC
3. **GC a besoin de la confirmation de débit des points** (CM) pour finaliser l'encaissement → GC dépend de CM à nouveau

La séquence est : GC interroge CM (solde) → GC calcule le prix → client choisit de payer en points → GC demande à CM de débiter → CM confirme → GC finalise. **Cela impose 3 aller-retours synchrones GC↔CM dans une seule transaction**, chacun en < 2s (SLA fidélité).

**Impact** : Si un seul aller-retour échoue ou dépasse le timeout, la transaction est bloquée. Scénario fréquent (8,5M de clients fidèles). Aucun mécanisme de compensation (rollback du débit de points si l'encaissement échoue ensuite) n'est documenté.

> Réf. : RG-CM-04, RG-CM-05, integrations-cibles.md Cegid SLA < 2s

---

### FRIC-602 — Cross-docking : 3 domaines synchrones sans orchestrateur

| Propriété | Valeur |
|-----------|--------|
| **Type** | 6 — Séquencement impossible |
| **Modules** | AA, SE, SC |
| **Sévérité** | **BLOQUANT** |

**Description** : Le cross-docking (RG-SC-07) implique qu'un article en forte rotation passe directement de la réception fournisseur à l'expédition magasin sans stockage intermédiaire. La séquence requise :
1. **AA** : valide la réception → FLUX-B01 → SE
2. **SE** : enregistre l'entrée en stock... mais l'article ne doit PAS être rangé en emplacement
3. **SC** : doit avoir PRÉ-PLANIFIÉ l'expédition vers le magasin (FLUX-C05) AVANT l'arrivée de la marchandise
4. **SE** : exécute directement le picking et le chargement (FLUX-C03)

**Problème** : SC doit planifier le réapprovisionnement magasin **avant** que AA ne valide la réception. Mais la date exacte d'arrivée dépend du fournisseur (délai variable). Et SE doit savoir que cet article est en cross-docking **au moment de la réception** pour ne pas le ranger.

**Impact** : Sans orchestrateur cross-docking (qui coordonne AA, SE, SC), l'article est rangé en emplacement par défaut → perte du bénéfice du cross-docking (rapidité, coût).

> Réf. : RG-SC-07, flux-inter-modules.md FLUX-B01 + FLUX-C03 + FLUX-C05

---

### FRIC-603 — Three-way matching : timing des 3 documents non garanti

| Propriété | Valeur |
|-----------|--------|
| **Type** | 6 — Séquencement impossible |
| **Modules** | AA, SE, FC |
| **Sévérité** | **MAJEUR** |

**Description** : Le rapprochement three-way (RG-FC-05) exige 3 documents :
- **Commande fournisseur** (AA → FC, FLUX-B02) : disponible à l'émission
- **Réception** (AA → FC, FLUX-B03) : disponible à la validation de réception
- **Facture fournisseur** (externe → FC, FLUX-B07) : disponible à la réception de la facture

L'ordre d'arrivée n'est **pas garanti** : la facture peut arriver avant la réception (pré-facturation), après (facturation classique), ou bien la réception peut être partielle (livraison en plusieurs fois). Aucune tolérance n'est définie :
- Tolérance d'écart quantité entre commande et réception ?
- Tolérance d'écart montant entre commande et facture ?
- Délai maximum d'attente d'un des 3 documents avant blocage ?

**Impact** : Sans règles de tolérance, le rapprochement bloque sur le moindre écart. Avec des tolérances trop larges, des erreurs passent inaperçues. L'absence de règle est aussi problématique que des règles en conflit.

> Réf. : RG-FC-05, flux-inter-modules.md FLUX-B02 + FLUX-B03 + FLUX-B07

---

### FRIC-604 — Réservation stock vs picking : fenêtre de divergence

| Propriété | Valeur |
|-----------|--------|
| **Type** | 6 — Séquencement impossible |
| **Modules** | GC, SE, SC |
| **Sévérité** | **MAJEUR** |

**Description** : La commande web déclenche une réservation (FLUX-A01 à t0) puis un picking (FLUX-C03 à t4, cf. chronologie « Commande client web validée »). Entre t0 et t4, le stock physique peut changer :
- Un inventaire tournant découvre un écart (article cassé, vol)
- Un autre client réserve les dernières unités
- L'emplacement est réorganisé

Si le stock physique réel à t4 ne correspond plus à la réservation de t0, le picking échoue. Aucun processus de « dé-réservation + notification client + recherche alternative (ship from store, autre entrepôt) » n'est documenté dans les SF.

**Impact** : Commande web confirmée mais non livrable. Mauvaise expérience client (annulation tardive). Le cas est d'autant plus fréquent en période de pic (soldes, Black Friday).

> Réf. : flux-inter-modules.md chronologie commande web (t0→t4), FLUX-A01, FLUX-C03

---

## Matrice de synthèse : frictions × domaines impactés

| Friction | GC | AA | SC | FC | SE | CM | RB |
|:--------:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| FRIC-101 | X | | | | X | | |
| FRIC-102 | X | | | X | | X | |
| FRIC-103 | X | | X | X | X | | |
| FRIC-104 | X | X | X | X | X | | |
| FRIC-105 | | X | X | X | | | |
| FRIC-106 | | | X | X | | | X |
| FRIC-201 | X | | | | X | | |
| FRIC-202 | X | | | | X | | |
| FRIC-203 | | | | | | | X |
| FRIC-204 | X | | | X | | | |
| FRIC-205 | | | | X | | X | |
| FRIC-301 | X | | | | X | X | |
| FRIC-302 | X | | | | X | | |
| FRIC-303 | | X | X | | | | |
| FRIC-304 | X | | | | | X | |
| FRIC-305 | | | X | | | | X |
| FRIC-401 | X | | | | | X | |
| FRIC-402 | | X | X | | | | |
| FRIC-403 | | | | X | X | | |
| FRIC-404 | X | | | | | X | |
| FRIC-405 | X | | | | | X | |
| FRIC-501 | X | X | X | X | X | | |
| FRIC-502 | X | | | | X | X | |
| FRIC-503 | | X | X | X | | | |
| FRIC-504 | X | X | | X | | | |
| FRIC-601 | X | | | | | X | |
| FRIC-602 | | X | X | | X | | |
| FRIC-603 | | X | | X | X | | |
| FRIC-604 | X | | X | | X | | |
| **Total** | **18** | **9** | **12** | **13** | **14** | **9** | **3** |

### Domaines les plus exposés

| Rang | Domaine | Frictions | Bloquantes |
|:----:|---------|:---------:|:----------:|
| 1 | **GC** (Gestion commerciale) | 18 | 4 |
| 2 | **SE** (Stocks & Entrepôts) | 14 | 4 |
| 3 | **FC** (Finance & Comptabilité) | 13 | 1 |
| 4 | **SC** (Supply Chain) | 12 | 2 |
| 5 | **CM** (CRM & Marketing) | 9 | 3 |
| 6 | **AA** (Achats & Appro) | 9 | 2 |
| 7 | **RB** (Reporting & BI) | 3 | 0 |

### Couples de domaines les plus en friction

| Couple | Frictions communes | Principales |
|--------|:-----------------:|-------------|
| **GC ↔ CM** | 7 | Fidélité/tarification, client, promotions, POS |
| **GC ↔ SE** | 7 | Stock disponible, vente POS, réservation/picking |
| **AA ↔ SC** | 4 | Réappro seuil/MRP, proposition→commande |
| **SE ↔ FC** | 4 | Valorisation, inventaire, stock disponible |
| **AA ↔ FC** | 3 | Three-way matching, prix de revient |
