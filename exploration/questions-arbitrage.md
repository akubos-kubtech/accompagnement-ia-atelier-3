# Etape 4 : Questions d'arbitrage pour le client

> Compilation de toutes les questions nécessitant une décision client, extraites de la réconciliation des entités (16 entités), la cartographie des flux (46 flux), les zones de friction (30 frictions) et la matrice de dépendances (26 dépendances).
>
> **P1** = bloquant (empêche la conception) · **P2** = majeur (dégrade la conception) · **P3** = mineur (optimisation)

## Synthèse

| Catégorie | P1 | P2 | P3 | Total |
|-----------|:--:|:--:|:--:|:-----:|
| 1 — Incohérences entités | 3 | 7 | 2 | **12** |
| 2 — Flux manquants | 2 | 6 | 3 | **11** |
| 3 — Hypothèses à valider | 4 | 7 | 0 | **11** |
| **Total** | **9** | **20** | **5** | **34** |

---

## 1. Incohérences entités

> Même entité définie différemment selon les domaines — nécessite un arbitrage sur le modèle canonique.

### P1 — Bloquant

---

#### ARB-E01 — Stock « disponible » : quelle définition par contexte ?

| | |
|---|---|
| **Criticité** | **P1** |
| **Modules** | GC, SE, SC, FC |
| **Le problème** | 4 définitions concurrentes du stock « disponible » : SE = statut physique (hors réservé, contrôle, retour, à détruire). GC = ATP (Available to Promise, vendable en ligne). SC = MRP-disponible (stock - réservations - sécurité + en-cours). FC = valorisable (tous statuts sauf à détruire). Le statut « en transit » n'est modélisé dans aucun domaine. |
| **Impact si non résolu** | Sur-vente web (promesse > réalité), MRP qui commande trop ou pas assez, valorisation comptable faussée. **Impossible de paramétrer le moteur de vente ni le MRP sans cette définition.** |
| **Réf.** | FRIC-103, reconciliation-entites §6, écart E3 |

**Question client** : Pour chacun des 4 contextes (vente web, vente POS, calcul MRP, valorisation comptable), quelle est la formule exacte du stock disponible ? Le stock en transit est-il inclus dans l'ATP ?

---

#### ARB-E02 — Magasin : qui porte le référentiel ?

| | |
|---|---|
| **Criticité** | **P1** |
| **Modules** | SE, SC, GC, FC, AA |
| **Le problème** | « Magasin » est utilisé par 5 domaines mais défini par aucun. SE l'appelle « Site de stockage » (type magasin), SC l'utilise comme destination de réappro, GC comme point de vente POS, FC comme axe analytique, AA comme lieu de réception. 347 magasins (600 en 2030) sans référentiel : pas de code unifié, pas d'attributs partagés (adresse, surface, zone géo, directeur, capacité, profil de vente). |
| **Impact si non résolu** | **Bloquant pour le paramétrage initial.** Chaque domaine crée sa propre fiche magasin partielle. Impossible de déployer les 347 magasins de manière cohérente. |
| **Réf.** | FRIC-501, FRIC-104, reconciliation-entites §9, écart E4 |

**Question client** : Quel domaine porte le référentiel magasin ? Quels attributs sont structurants (code, adresse, zone, capacité, profil, WMS local) ? Le Magasin est-il un sous-type de Site (avec Entrepôt et Plateforme) ?

---

#### ARB-E03 — Facture : qui la génère, qui l'alimente ?

| | |
|---|---|
| **Criticité** | **P1** |
| **Modules** | GC, FC |
| **Le problème** | FC possède l'entité Facture et porte Factur-X (RG-FC-08, obligation légale). Mais GC — qui détient les données commerciales nécessaires (lignes, articles, prix unitaires, TVA par taux, adresse client) — ne modélise pas la facture et n'a aucun processus de facturation. Le FLUX-A05 transporte des données pour écriture comptable (agrégé), pas pour facture Factur-X (détail ligne par ligne). |
| **Impact si non résolu** | **Non-conformité réglementaire** (Factur-X obligatoire en France, SDI en Italie, SII en Espagne). Pas de facture légale émise pour les ventes. |
| **Réf.** | FRIC-206, reconciliation-entites §14 |

**Question client** : Quel événement déclenche la facturation (encaissement ? livraison ? batch quotidien ?) ? GC doit-il pousser le détail ligne par ligne vers FC, ou FC vient-il lire les données dans GC ?

---

### P2 — Majeur

---

#### ARB-E04 — Article : entité unique ou vues par domaine ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | GC, SE, AA, SC, FC |
| **Le problème** | GC définit « Article » (commercial : SKU, catégorie, prix de base, pays). SE définit « Article stock » (physique : type lot/série/péremption, unité de gestion). Attributs disjoints. 4 notions de prix coexistent sans hiérarchie (prix de base GC, prix d'achat AA, prix de revient AA/FC, valeur unitaire SE/FC). Akeneo PIM (descriptions, médias) absent du modèle d'entités. |
| **Impact si non résolu** | Double maintenance des données article. Incohérence entre vue commerciale et vue stock. Ambiguïté du « prix » d'un article selon le contexte. |
| **Réf.** | FRIC-101, reconciliation-entites §1, écart E1 |

**Question client** : L'article est-il une entité unique avec des groupes d'attributs par domaine, ou chaque domaine maintient-il sa propre fiche ? Quel est le périmètre ERP (référentiel maître) vs Akeneo PIM (données enrichies) ?

---

#### ARB-E05 — Client : qui crée, quel modèle canonique ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | GC, CM, FC |
| **Le problème** | CM déclare le « référentiel client maître ». GC modélise le Client indépendamment. FC utilise « Compte tiers » (fusion Client + Fournisseur). 3 vues, pas de modèle canonique. Le processus de création est flou : premier achat web (GC) ? inscription fidélité (CM) ? achat magasin sans carte (GC, client anonyme) ? |
| **Impact si non résolu** | Doublons clients, désynchronisation vente/fidélité/compta, perte RGPD (pas de point de création unique). |
| **Réf.** | FRIC-102, FRIC-405, reconciliation-entites §2, écart E2 |

**Question client** : Quel est le processus de création d'un client (premier contact GC, inscription CM, ou les deux) ? Le Compte tiers FC est-il créé automatiquement depuis le Client CM ?

---

#### ARB-E06 — Commande fournisseur : proposition SC → engagement AA, quel workflow ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | SC, AA, FC |
| **Le problème** | SC produit des « Propositions de commande » (MRP). AA gère des « Commandes fournisseur ». La proposition ne porte ni devise, ni incoterm, ni montant — attributs obligatoires de la commande (RG-AA-05, RG-AA-06). Le mécanisme de conversion n'est décrit dans aucun domaine. FC ne peut faire de three-way matching que sur une Commande fournisseur (pas une proposition). |
| **Impact si non résolu** | Propositions MRP inutilisées (pas de conversion en commande réelle), ou commandes incomplètes (sans devise/incoterm). |
| **Réf.** | FRIC-105, reconciliation-entites §5, écart E5 |

**Question client** : La conversion est-elle automatique (avec enrichissement des attributs obligatoires) ou nécessite-t-elle une validation manuelle par l'acheteur ? Quels sont les critères d'enrichissement automatique (fournisseur préféré, devise par défaut, incoterm par défaut) ?

---

#### ARB-E07 — Transaction POS : quel modèle de données ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | GC, SE, CM, FC |
| **Le problème** | La vente en magasin (347 POS, majorité du CA) n'a pas d'entité modélisée. GC modélise la « Commande client » (web, B2B), pas la vente caisse immédiate. Aucune entité « Ticket de caisse » ou « Transaction POS » n'existe. Le processus complet (scan → prix → fidélité → paiement → stock → comptabilité) n'apparaît dans aucune spécification fonctionnelle. |
| **Impact si non résolu** | Le scénario le plus fréquent n'a pas de modèle. Risque d'oubli fonctionnel majeur à l'implémentation. |
| **Réf.** | FRIC-502, FRIC-201 |

**Question client** : La vente POS est-elle une Commande client de type « caisse » (même entité, statut différent) ou une entité distincte « Transaction POS » ? Le ticket de caisse est-il un objet ERP ou reste-t-il côté Cegid ?

---

#### ARB-E08 — Pays : quel référentiel pour le multi-pays ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | GC, FC, AA, SC |
| **Le problème** | Le « Pays » conditionne : normes comptables (FC), régimes TVA (FC), devises (FC, AA), prix de base (GC), nomenclature douanière (SC), plateforme fiscale (Factur-X/SDI/SII/SAF-T). 15 pays actuels, 25 en 2030, 8 devises, 14 langues. Aucun domaine ne modélise un référentiel Pays avec ses attributs structurants. |
| **Impact si non résolu** | Chaque domaine paramètre indépendamment les spécificités nationales — incohérence entre la TVA vue par FC et la TVA vue par GC pour un même pays. |
| **Réf.** | FRIC-503 |

**Question client** : Quels attributs portent le référentiel Pays (devise, langue, norme comptable, régime TVA, plateforme fiscale, WMS local) ? Ce référentiel est-il un paramétrage ERP natif ou un objet à modéliser ?

---

#### ARB-E09 — Réception : qui porte le processus physique ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | AA, SE, FC |
| **Le problème** | AA définit la Réception (contrôle quanti/quali). Mais l'acte physique se passe dans l'entrepôt (SE/WMS). AA et SE enregistrent chacun une information de quantité reçue (ligne réception AA, mouvement stock SE). FC consomme le résultat pour le three-way matching — mais le timing du rapprochement (à la validation AA ? après mise en stock SE ?) n'est pas précisé. |
| **Impact si non résolu** | Double saisie, timing du three-way matching flou, écarts entre quantité reçue (AA) et quantité stockée (SE). |
| **Réf.** | reconciliation-entites §8, écart E6 |

**Question client** : AA contrôle, puis SE exécute l'entrée physique — est-ce bien la séquence ? Le three-way matching FC se déclenche-t-il à la validation AA ou après confirmation SE ?

---

#### ARB-E10 — Promotion : comment se coordonnent GC et CM ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | GC, CM |
| **Le problème** | GC modélise la Promotion (entité tarifaire : type, période, conditions). CM crée des Campagnes marketing qui « doivent être cohérentes » (RG-CM-08) mais ne modélise pas la Promotion. Le mécanisme de synchronisation n'existe pas. De plus, RG-GC-04 centralise dans l'ERP tandis que RG-CM-09 décrit Salesforce Marketing Cloud — deux systèmes sans pont. |
| **Impact si non résolu** | Prix différent entre l'email marketing et la caisse. Campagne Salesforce non reconnue par l'ERP. |
| **Réf.** | FRIC-404, reconciliation-entites §11, écart E7 |

**Question client** : Les promotions sont-elles créées dans l'ERP (GC) et poussées vers Salesforce, ou l'inverse ? Quel est le processus de validation croisée ?

---

### P3 — Mineur

---

#### ARB-E11 — KPI : gouvernance centralisée ou fédérée ?

| | |
|---|---|
| **Criticité** | **P3** |
| **Modules** | SC, FC, RB |
| **Le problème** | SC définit ses KPI (nom, valeur, objectif). RB définit les KPI avec une structure plus riche (formule, unité, source, fréquence). FC définit des axes analytiques partagés avec RB. RG-RB-08 exige une « définition unique de chaque indicateur » — en contradiction avec le fait que chaque domaine définit les siens. |
| **Impact si non résolu** | Deux domaines calculent le même indicateur (ex : marge) avec des formules différentes. Reporting incohérent. |
| **Réf.** | FRIC-106, reconciliation-entites §15 |

**Question client** : Les KPI sont-ils définis par chaque domaine métier et formalisés par RB, ou RB impose-t-il les définitions aux domaines ? Qui est maître des axes analytiques : FC ou RB ?

---

#### ARB-E12 — Prévision de vente : SC classique, RB IA, ou hybride ?

| | |
|---|---|
| **Criticité** | **P3** |
| **Modules** | SC, RB |
| **Le problème** | SC produit des prévisions via des « algorithmes paramétrables par famille » (RG-SC-01). RB mentionne une « prévision de la demande assistée par IA ». Le CDC dit que « les prévisions IA alimentent la planification de la demande ». Qui produit la prévision finale ? Le flux RB→SC (FLUX-G04) n'est pas formalisé. |
| **Impact si non résolu** | Prévisions contradictoires entre les deux moteurs. MRP alimenté par des données incohérentes. |
| **Réf.** | reconciliation-entites §16, écart E8 |

**Question client** : L'IA (RB) remplace-t-elle les algorithmes SC, les enrichit-elle (correction IA sur base SC), ou les deux coexistent-ils avec un mécanisme de réconciliation ?

---

## 2. Flux manquants

> Flux non documentés dans les SF mais logiquement nécessaires au fonctionnement de l'ERP.

### P1 — Bloquant

---

#### ARB-F01 — Vente POS → mouvement de stock magasin : flux absent

| | |
|---|---|
| **Criticité** | **P1** |
| **Modules** | GC, SE |
| **Le problème** | La cartographie modélise le cycle web (commande → réservation → expédition → mouvement stock), mais la vente POS immédiate n'a aucun flux de sortie stock. Le FLUX-A01 (réservation) concerne les commandes, pas les ventes caisse. `integrations-cibles.md` mentionne « Caisses → ERP : Mouvements de stock magasin (< 5 min) » mais aucun FLUX-xxx ne le modélise entre les modules ERP. |
| **Impact si non résolu** | 347 magasins produisent des ventes sans déclencher de mouvement stock. **Divergence stock magasin / ERP croissante et non contrôlée.** |
| **Réf.** | FRIC-201, absence de flux POS→SE dans flux-inter-modules.md |

**Question client** : Le mouvement stock POS est-il un flux POS→SE direct (via WMS local) ou un flux POS→GC→SE (via l'ERP) ? Le stock magasin est-il géré par le WMS Manhattan ou par le POS Cegid ?

---

#### ARB-F02 — Factur-X : données détaillées GC → FC non modélisées

| | |
|---|---|
| **Criticité** | **P1** |
| **Modules** | GC, FC |
| **Le problème** | Le FLUX-A05 (GC→FC) transporte des données pour écriture comptable (montant, TVA, compte, pièce — objet agrégé). La facture Factur-X est un objet différent : document légal structuré avec détail ligne par ligne (articles, quantités, prix unitaires, TVA par taux, coordonnées client). **Le flux actuel est insuffisant pour la facturation électronique.** |
| **Impact si non résolu** | Pas de facture légale émise. Non-conformité Factur-X (FR), SDI (IT), SII (ES). Le e-reporting B2C (RG-FC-09) nécessite les données de chaque transaction — y compris POS. |
| **Réf.** | FRIC-206, FLUX-A05, RG-FC-08, RG-FC-09 |

**Question client** : Faut-il créer un second flux GC→FC dédié à la facturation (détail ligne par ligne), ou enrichir le FLUX-A05 existant pour porter les deux objets (écriture + facture) ? Le déclenchement est-il à l'encaissement, à la livraison, ou en batch ?

---

### P2 — Majeur

---

#### ARB-F03 — Libération de stock sur annulation commande

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | GC, SE |
| **Le problème** | La réservation est documentée (FLUX-A01 : GC→SE à la validation commande). Le flux inverse — libération de la réservation en cas d'annulation — n'existe pas (FLUX-A09, identifié comme ABSENT). |
| **Impact si non résolu** | Le stock réservé pour des commandes annulées reste bloqué indéfiniment. Érosion progressive de l'ATP. Divergence stock physique / logique. |
| **Réf.** | FLUX-A09, FRIC-202 |

**Question client** : L'annulation libère-t-elle automatiquement le stock, ou nécessite-t-elle une validation manuelle (ex : article déjà en picking) ? Quels statuts de commande déclenchent la libération (annulée, expirée, refusée) ?

---

#### ARB-F04 — Encours client : contrôle bloquant ou informatif ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | FC, GC |
| **Le problème** | FC gère les encours et le recouvrement. GC gère la prise de commande. Aucun flux ne transporte l'encours de FC vers GC pour contrôler les commandes en dépassement (FLUX-D06, identifié comme ABSENT). |
| **Impact si non résolu** | Des commandes acceptées pour des clients en impayé. Risque financier non contrôlé, augmentation des créances douteuses. |
| **Réf.** | FLUX-D06, FRIC-204 |

**Question client** : Le contrôle d'encours doit-il être bloquant en temps réel (la commande est refusée) ou informatif en batch (alerte quotidienne, commande acceptée) ? Seuil de déclenchement par catégorie client ?

---

#### ARB-F05 — Client CM → Compte tiers FC : alimentation absente

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | CM, FC |
| **Le problème** | CM est le référentiel client maître (RG-CM-01). FC gère le Compte tiers (encours, conditions de paiement, relances). Aucun flux ne relie la création/modification d'un client en CM à la création/mise à jour de son compte tiers en FC. |
| **Impact si non résolu** | Création manuelle des comptes tiers en comptabilité. Désynchronisation client/compta. Erreurs sur les conditions de paiement. |
| **Réf.** | FLUX-F03, FRIC-205 |

**Question client** : La création du compte tiers FC est-elle automatique à la création client CM ? Quels attributs sont propagés (conditions de paiement, catégorie, limite d'encours) ?

---

#### ARB-F06 — Avantages fidélité → moteur de prix GC : intégration absente

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | CM, GC |
| **Le problème** | RG-GC-03 définit la cascade tarifaire : prix base → B2B → promo → remise → soldes. Les avantages fidélité CM (paliers, remises automatiques) ne figurent pas dans cette cascade. Le flux CM→GC (FLUX-F01) transporte le palier fidélité, mais aucune règle ne décrit comment ce palier s'injecte dans le calcul du prix. |
| **Impact si non résolu** | Les avantages fidélité sont ignorés par le moteur de prix (client lésé), ou appliqués sans règle (marge érodée), ou incohérents selon le canal. |
| **Réf.** | FRIC-401, dépendance cachée #1, FLUX-F01 |

**Question client** : À quel niveau de la cascade tarifaire s'insèrent les avantages fidélité ? Les avantages sont-ils cumulables avec les promotions et remises conditionnelles ?

---

#### ARB-F07 — Capacité de stockage SE → contrainte MRP non explicite

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | SE, SC |
| **Le problème** | RG-SC-04 intègre la « capacité de stockage » comme contrainte MRP. Le flux SE→SC (FLUX-C01) transporte les niveaux de stock, mais pas la capacité restante des sites. SC doit-il lire la capacité en direct dans SE ou recevoir un flux dédié ? |
| **Impact si non résolu** | Le MRP commande sans tenir compte des limites physiques. Livraisons non réceptionnables (entrepôt plein). |
| **Réf.** | dépendance cachée #3, RG-SC-04 |

**Question client** : La capacité de stockage est-elle un paramètre statique (configuré une fois) ou un flux dynamique (rafraîchi régulièrement) ? Le MRP doit-il bloquer une proposition si la capacité cible est insuffisante ?

---

#### ARB-F08 — Plans promotionnels CM → achats anticipés AA non connectés

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | CM, AA, SC |
| **Le problème** | RG-AA-04 mentionne les « besoins saisonniers approvisionnés sur base plans de vente ». CM détient les plans promotionnels (campagnes, périodes de pointe). Mais aucun flux CM→AA ou CM→SC ne transporte les prévisions d'impact promotionnel pour anticiper les achats. |
| **Impact si non résolu** | Ruptures de stock en période promotionnelle. Les achats anticipés sont faits « au feeling » de l'acheteur, sans donnée marketing. |
| **Réf.** | dépendance cachée #4, FLUX-C06 (implicite SC→AA) |

**Question client** : Les campagnes marketing CM doivent-elles alimenter automatiquement le MRP (via SC) pour anticiper les volumes, ou l'anticipation reste-t-elle manuelle ?

---

### P3 — Mineur

---

#### ARB-F09 — Règlement fournisseur FC → AA : retour d'information

| | |
|---|---|
| **Criticité** | **P3** |
| **Modules** | FC, AA |
| **Le problème** | FC émet les règlements fournisseur (spec P3 étape 4). AA gère la relation fournisseur et l'évaluation. Aucun retour automatique d'information vers AA (FLUX-B08, identifié comme ABSENT). |
| **Impact si non résolu** | Décalage encours fournisseur côté AA. L'info est lisible directement en FC — impact faible. |
| **Réf.** | FLUX-B08 |

**Question client** : Le retour de règlement est-il un flux automatique (FC pousse vers AA) ou AA consulte-t-il FC directement ?

---

#### ARB-F10 — Alertes IA : émises par RB, consommées par personne

| | |
|---|---|
| **Criticité** | **P3** |
| **Modules** | RB, Tous |
| **Le problème** | RB spécifie des alertes IA (anomalies, prévisions, recommandations). Mais aucun domaine récepteur ne modélise comment il intègre ces alertes dans ses processus métier. L'alerte est émise dans le vide. |
| **Impact si non résolu** | L'IA reste un outil de consultation passive. Pas d'impact automatisé sur les processus. |
| **Réf.** | FLUX-G05, FRIC-203 |

**Question client** : Les alertes IA doivent-elles déclencher des actions automatiques (ex : alerte rupture → proposition MRP) ou rester informatives (dashboard, notification) ?

---

#### ARB-F11 — Traçabilité lot : lien fournisseur non documenté

| | |
|---|---|
| **Criticité** | **P3** |
| **Modules** | SE, AA |
| **Le problème** | SE modélise le Lot (entité avec fournisseur d'origine pour traçabilité). AA gère le Fournisseur. Le lien entre Lot (SE) et Fournisseur (AA) est modélisé dans les entités mais le flux d'alimentation n'est pas documenté. |
| **Impact si non résolu** | En cas de rappel produit, la traçabilité lot → fournisseur nécessite une recherche manuelle croisée. |
| **Réf.** | dépendance cachée #7, RG-SE-04 |

**Question client** : Le numéro de lot est-il renseigné à la réception (AA pousse vers SE) ou à l'entrée en stock (SE lit la commande AA) ?

---

## 3. Hypothèses à valider

> Suppositions implicites du CDC sur des modes de fonctionnement, des temporalités ou des architectures — nécessitent confirmation ou infirmation par le client.

### P1 — Bloquant

---

#### ARB-H01 — Transaction POS : synchrone ET asynchrone dans le même geste ?

| | |
|---|---|
| **Criticité** | **P1** |
| **Modules** | GC, CM, SE |
| **Le problème** | Le CDC suppose qu'une transaction caisse peut être simultanément synchrone (fidélité < 2s, bloquant la transaction) et asynchrone (vente + stock < 5 min). Cela impose deux patterns d'intégration incompatibles dans une même opération métier. Si le service fidélité CM est indisponible (même 2s de timeout), la transaction est bloquée. |
| **Impact si non résolu** | Architecture d'intégration POS impossible à concevoir. Risque de blocage caisse en cas d'indisponibilité CM. **Le mode dégradé caisse (48h autonome) ne couvre pas la fidélité.** |
| **Réf.** | FRIC-301, FRIC-304, integrations-cibles Cegid § |

**Question client** : La fidélité est-elle vraiment synchrone (bloquante) en caisse, ou peut-elle être asynchrone (cumul différé, pas de paiement par points si hors ligne) ? Quel est le comportement si le service fidélité est indisponible ?

---

#### ARB-H02 — Stock web : quelle latence acceptable ?

| | |
|---|---|
| **Criticité** | **P1** |
| **Modules** | GC, SE |
| **Le problème** | Le CDC suppose que le stock web peut être affiché en < 2s (SLA e-commerce) alors que le WMS synchronise en < 1 min et le batch stock prend 5-15 min en pic. Fenêtre d'incohérence : un article peut être affiché « disponible » en ligne alors qu'il est physiquement épuisé depuis 1 à 15 minutes. En Black Friday (charge x3), le risque de sur-vente est élevé. |
| **Impact si non résolu** | Sur-vente en pic → commandes non honorées → annulations → dégradation expérience client. |
| **Réf.** | FRIC-302, integrations-cibles SF Commerce Cloud + Manhattan WMS |

**Question client** : Quelle latence de stock est acceptable pour le web (temps réel WMS, quasi temps réel ERP, cache < 5 min) ? Un mécanisme de sur-réservation (overbooking stock) est-il envisagé pour les pics ?

---

#### ARB-H03 — Paiement par points : 3 aller-retours synchrones < 2s ?

| | |
|---|---|
| **Criticité** | **P1** |
| **Modules** | GC, CM |
| **Le problème** | Le paiement par points fidélité impose une dépendance circulaire GC↔CM : (1) GC interroge CM (solde points) → (2) GC calcule le prix → client choisit points → (3) GC demande à CM de débiter → (4) CM confirme → (5) GC finalise. 3 aller-retours synchrones, chacun < 2s. Aucun mécanisme de compensation (rollback si l'encaissement échoue après débit des points). |
| **Impact si non résolu** | Transaction caisse bloquée si un aller-retour échoue. Points débités mais vente non finalisée (perte de points). Scénario fréquent : 8,5M de clients fidèles. |
| **Réf.** | FRIC-601, RG-CM-04, RG-CM-05 |

**Question client** : Le paiement par points est-il un cas d'usage fréquent ou marginal ? Un mécanisme de compensation (rollback des points) est-il nécessaire ? Le débit peut-il être asynchrone (confirmation différée) ?

---

#### ARB-H04 — Cross-docking : qui orchestre les 3 domaines ?

| | |
|---|---|
| **Criticité** | **P1** |
| **Modules** | AA, SE, SC |
| **Le problème** | Le cross-docking (RG-SC-07) suppose que SC planifie l'expédition magasin AVANT que AA ne valide la réception. Or la date d'arrivée dépend du fournisseur (variable). SE doit savoir que l'article est en cross-docking au moment de la réception (pour ne pas le ranger). Trois domaines doivent se synchroniser sans orchestrateur défini. |
| **Impact si non résolu** | L'article est rangé en emplacement par défaut → perte du bénéfice du cross-docking (rapidité, coût). |
| **Réf.** | FRIC-602, RG-SC-07 |

**Question client** : Le cross-docking est-il un cas d'usage fréquent ou limité à certains articles ? Qui porte le rôle d'orchestrateur : SC (planification), SE (WMS Manhattan), ou un processus dédié ?

---

### P2 — Majeur

---

#### ARB-H05 — Réapprovisionnement : seuil temps réel ET MRP batch, qui prime ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | AA, SC |
| **Le problème** | Le CDC décrit deux mécanismes couvrant le même besoin : réappro automatique au seuil (AA, temps réel, article par article) et MRP (SC, batch quotidien avant 6h, toutes références). Rien n'empêche les deux de déclencher une commande pour le même article au même moment. Le périmètre de chacun n'est pas arbitré. |
| **Impact si non résolu** | Double commande fournisseur. Sur-stock. Surcoût d'approvisionnement. Confusion des acheteurs (valider les propositions MRP si le seuil a déjà commandé ?). |
| **Réf.** | FRIC-303, FRIC-402, RG-AA-03, RG-SC-02 |

**Question client** : Quel mécanisme s'applique à quel périmètre ? Le seuil pour les articles à rotation rapide (réactif) et le MRP pour les articles saisonniers/planifiés (prédictif) ? Ou le MRP remplace-t-il complètement le seuil ?

---

#### ARB-H06 — Mode dégradé magasin : que devient la fidélité ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | GC, CM |
| **Le problème** | Les ENF définissent un mode dégradé magasin : « caisse autonome 48h, transactions locales, synchronisation différée ». Mais RG-CM-04 exige la fidélité « en temps réel avec les caisses ». En mode dégradé, la caisse n'a plus accès au solde de points. Les points des ventes réalisées pendant la panne sont perdus ou doivent être rétro-crédités — processus non documenté. |
| **Impact si non résolu** | Perte de points pendant les pannes (client insatisfait). Pas de paiement par points pendant 48h. |
| **Réf.** | FRIC-304, non-fonctionnelles.md modes dégradés |

**Question client** : En mode dégradé, la fidélité est-elle désactivée (pas de cumul ni utilisation) ou fonctionnelle en local (cache des soldes, cumul différé) ? Les points sont-ils rétro-crédités à la reconnexion ?

---

#### ARB-H07 — Méthode de valorisation stock : décision SE ou FC ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | SE, FC |
| **Le problème** | SE exécute la valorisation (FIFO, CUMP, prix standard — RG-SE-11). FC connaît les normes comptables locales qui imposent la méthode (ex : CUMP en France — RG-FC-01). La frontière de responsabilité n'est pas tracée : qui porte la règle de choix de la méthode par pays ? |
| **Impact si non résolu** | Valorisation incorrecte si SE choisit une méthode non conforme à la norme locale. |
| **Réf.** | FRIC-403, RG-SE-11, RG-FC-01 |

**Question client** : FC paramètre-t-il la méthode de valorisation par pays/entité juridique, et SE l'exécute sans décision ? Ou SE dispose-t-il d'une autonomie de choix ?

---

#### ARB-H08 — Promotions : ERP centralise ET Salesforce gère, qui est maître ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | GC, CM |
| **Le problème** | RG-GC-04 : « gestion centralisée des règles tarifaires dans l'ERP, cohérente sur tous les canaux ». RG-CM-09 : « campagnes multi-canal via Salesforce Marketing Cloud ». Si une campagne Salesforce crée une promotion hors ERP, le moteur de prix centralisé n'en a pas connaissance. |
| **Impact si non résolu** | Prix incohérent entre le canal marketing (email, app) et la caisse/web. |
| **Réf.** | FRIC-404, RG-GC-04, RG-CM-09 |

**Question client** : L'ERP est-il le seul système autorisé à créer des promotions (Salesforce consomme) ? Ou Salesforce peut-il créer des promotions qui sont ensuite synchronisées vers l'ERP ?

---

#### ARB-H09 — Three-way matching : quelles tolérances ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | AA, FC, SE |
| **Le problème** | Le rapprochement three-way (RG-FC-05) exige 3 documents (commande, réception, facture). L'ordre d'arrivée n'est pas garanti (pré-facturation, réceptions partielles). Aucune tolérance n'est définie : écart quantité commande/réception ? Écart montant commande/facture ? Délai maximum d'attente d'un document ? |
| **Impact si non résolu** | Sans tolérances, blocage au moindre écart. Avec tolérances trop larges, erreurs non détectées. |
| **Réf.** | FRIC-603, RG-FC-05 |

**Question client** : Quelles tolérances d'écart (quantité en %, montant en € ou %) ? Quel délai maximum avant de bloquer le rapprochement ? Le rapprochement partiel (2 documents sur 3) est-il autorisé ?

---

#### ARB-H10 — Réservation stock → picking : que faire si le stock a changé ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | GC, SE, SC |
| **Le problème** | Entre la réservation (FLUX-A01 à t0) et le picking (FLUX-C03 à t4), le stock physique peut changer (inventaire, vol, réorganisation). Si le picking échoue, aucun processus de dé-réservation + notification client + recherche alternative (ship from store, autre entrepôt) n'est documenté. |
| **Impact si non résolu** | Commande web confirmée mais non livrable. Annulation tardive. Mauvaise expérience client en pic. |
| **Réf.** | FRIC-604, chronologie commande web |

**Question client** : En cas d'échec picking, le système recherche-t-il automatiquement dans un autre site (ship from store, transfert inter-entrepôts) ? Quel délai avant notification client ?

---

#### ARB-H11 — Référentiel client : GC peut-il créer un client hors processus CM ?

| | |
|---|---|
| **Criticité** | **P2** |
| **Modules** | GC, CM |
| **Le problème** | CM déclare le référentiel maître. Mais GC modélise le Client dans son propre domaine avec ses propres attributs, sans référence à CM. GC pourrait créer un client à la première commande web sans passer par le processus de qualité CM (déduplication, RGPD). |
| **Impact si non résolu** | Doublons clients. Non-conformité RGPD (pas de consentement tracé). |
| **Réf.** | FRIC-405, RG-CM-01 |

**Question client** : Toute création de client passe-t-elle obligatoirement par CM (processus unique), ou GC peut-il créer un client minimal (guest checkout) enrichi ensuite par CM ?

---

## Matrice de synthèse : questions × phases d'implémentation

> Chaque question doit être tranchée **AVANT** la phase concernée (cf. matrice-dependances.md).

| Phase | Questions à trancher | Nb |
|:-----:|---------------------|:--:|
| **0** (Réf.) | ARB-E01 (stock dispo), ARB-E02 (magasin), ARB-E04 (article), ARB-E08 (pays) | 4 |
| **1** (SE+CM) | ARB-E05 (client), ARB-H06 (mode dégradé fidélité), ARB-E12 (prévision), ARB-E11 (KPI) | 4 |
| **2** (GC+AA) | ARB-E03 (facture), ARB-E07 (transaction POS), ARB-F01 (POS→stock), ARB-H01 (POS sync/async), ARB-H02 (stock web latence), ARB-H03 (paiement points), ARB-F06 (fidélité→cascade), ARB-E10 (promo GC/CM), ARB-H08 (promo ERP/SF), ARB-F03 (libération stock), ARB-F04 (encours client), ARB-E06 (cmd fournisseur), ARB-H05 (seuil vs MRP), ARB-H11 (client hors CM) | 14 |
| **3** (FC+SC) | ARB-F02 (Factur-X), ARB-H04 (cross-docking), ARB-H07 (valorisation), ARB-H09 (three-way), ARB-H10 (réservation/picking), ARB-F05 (client→compte tiers), ARB-F07 (capacité SE→MRP), ARB-F08 (plans promo→achats), ARB-E09 (réception) | 9 |
| **4** (RB) | ARB-F10 (alertes IA), ARB-F09 (règlement fournisseur), ARB-F11 (traçabilité lot) | 3 |
| **Total** | | **34** |

> **Phase 2 concentre 14 questions** — c'est le pivot du projet (GC nœud critique). Un atelier client dédié à la Phase 2 est recommandé avant le démarrage de l'implémentation.
