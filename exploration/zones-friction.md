# Etape 2 : Détection des zones de friction

> Identifier les entités partagées, les données référentielles mal définies
> et les points d'intégration implicites.

## Entités partagées entre domaines

| # | Entité | Domaine A (vision) | Domaine B (vision) | Divergences | Impact |
|:-:|--------|-------------------|-------------------|-------------|--------|
| 1 | **Client** | GC : acheteur avec coordonnées, type B2C/B2B, catégorie | CM : fiche unifiée 360° avec données comportementales, fidélité, consentements RGPD | GC a une vue transactionnelle minimale, CM a la vue complète — qui enrichit quoi ? | Données client incomplètes dans un domaine, déduplication impossible si pas de référentiel unique |
| 2 | **Article** | GC : SKU, libellé, prix de base, catégorie | SE : « Article stock » avec type (sérialisé, lot, péremption), unité de gestion, emplacement | Nommage différent (Article vs Article stock), attributs différents selon l'usage | Confusion sur l'entité de référence, risque de désynchronisation des attributs |
| 3 | **Article** | AA : article avec fournisseur principal, prix d'achat, conditions | GC : article avec prix de vente, promotions | Le même article a un « prix » différent selon le domaine — prix d'achat (AA) vs prix de vente (GC) | Incohérence de marge si les prix ne sont pas reliés via le prix de revient |
| 4 | **Stock** | GC : quantité disponible à la vente (vue agrégée, booléen « dispo / non dispo ») | SE : quantité par site/emplacement/statut (disponible, réservé, contrôle, retour, à détruire) | Granularité radicalement différente — GC veut un chiffre simple, SE gère la complexité physique | Promesse de stock au client incohérente avec le stock réel |
| 5 | **Entrepôt** | AA : lieu de réception des marchandises | SE : « Site de stockage » avec emplacements, zones, capacités | Même concept, noms différents (Entrepôt vs Site de stockage), SE beaucoup plus détaillé | Ambiguïté dans les spécifications, risque de double modélisation |
| 6 | **Facture** | FC : entité explicite (émise/reçue), avec format Factur-X, statut | GC : non modélisée dans les entités, mais les ventes génèrent des factures | GC ne liste pas la facture comme entité alors que le flux GC→FC est critique | Zone grise : qui déclenche la facturation ? Qui porte la logique de génération ? |
| 7 | **Promotion** | GC : mécanique promotionnelle (%, fixe, lot, offert) avec période et conditions | CM : offre marketing associée à une campagne et un segment | Deux visions de la même mécanique — tarifaire (GC) vs marketing (CM) | Conflit si une promo marketing et une condition tarifaire s'appliquent simultanément au même article |

## Données référentielles transverses

| # | Donnée référentielle | Domaines consommateurs | Définition cohérente ? | Problème identifié |
|:-:|----------------------|----------------------|:----------------------:|-------------------|
| 1 | **Client** | GC, FC, CM, RB | Partiellement | ERP = maître, Salesforce = CRM principal. Deux systèmes écrivent sur le client. Règle RG-CM-01 dit « ERP maître » mais Salesforce conserve des données comportementales exclusives |
| 2 | **Article** | GC, AA, SC, SE, FC, RB | Non | Triple source : ERP (données de base), Akeneo PIM (données enrichies), SE (données stock). Pas de règle claire sur qui écrit quel attribut |
| 3 | **Fournisseur** | AA, SC, FC, SE | Oui | AA est clairement maître. Mais les critères RSE et la qualification ne sont pas consommés par SC (MRP) |
| 4 | **Magasin / PdV** | GC, SC, SE, CM | Non | Entité utilisée par 4 domaines mais jamais modélisée explicitement. Chaque domaine a sa propre vision (canal de vente, lieu de réappro, site de stock, point fidélité) |
| 5 | **Devise / Taux de change** | AA, FC | Non | Référentiel implicite. AA utilise la devise fournisseur, FC gère 8 devises opérationnelles et les couvertures de change. Pas de source unique définie |
| 6 | **Société / Entité juridique** | FC, SE | Non | Structure organisationnelle implicite. FC a des plans de comptes par société, SE a des méthodes de valorisation par entité. Jamais définie comme référentiel |
| 7 | **Canal de vente** | GC, FC (analytique), CM, RB | Non | Concept structurant du retail omnicanal mais absent comme entité formelle. Chaque domaine utilise une notion de « canal » sans définition partagée |

## Points d'intégration implicites

> Intégrations nécessaires mais jamais formalisées dans les SF.

| # | Intégration implicite | Domaines concernés | Pourquoi elle est nécessaire | Risque si non traitée |
|:-:|----------------------|-------------------|----------------------------|----------------------|
| 1 | **POS unifié (ventes + fidélité + stock)** | GC, CM, SE | Le POS Cegid doit simultanément : remonter les ventes (GC), cumuler/utiliser les points fidélité (CM), et déclencher les mouvements de stock (SE). Trois flux vers trois domaines dans une même transaction caisse | Transaction en caisse incomplète ou incohérente — ex: vente enregistrée mais points non cumulés |
| 2 | **Stock « Available-To-Promise » (ATP)** | SE, GC, SC | La promesse de disponibilité au client (web, magasin) nécessite un calcul temps réel combinant stock physique (SE), réservations en cours (GC) et réapprovisionnements planifiés (SC) | Survente (commande acceptée alors que le stock est insuffisant) ou sous-vente (stock affiché comme indisponible à tort) |
| 3 | **Orchestration retours multi-domaine** | GC, SE, FC | Un retour client traverse 3 domaines : création (GC), contrôle qualité et réintégration/destruction (SE), avoir et écriture comptable (FC). Le flux complet n'est pas décrit de bout en bout | Retour bloqué entre deux domaines, client non remboursé, stock non mis à jour |
| 4 | **Cascade de prix : achat → revient → vente → marge** | AA, SC, FC, GC | Le prix de revient (AA) intègre le transport (SC), alimente la marge (FC) et contraint le prix de vente minimum (GC). Cette chaîne traverse 4 domaines sans être formalisée | Vente à perte non détectée, marge calculée sur des données obsolètes |
| 5 | **Synchronisation multi-WMS** | SE, SC | Le CDC ne documente que Manhattan Associates (France). Les filiales internationales ont potentiellement d'autres WMS. L'intégration SE↔WMS doit être multi-connecteurs | Rupture de la visibilité stock unifiée dès qu'on sort de la France |
| 6 | **Consentement RGPD transverse** | CM, GC, RB | Un retrait de consentement en CM doit impacter immédiatement les traitements dans GC (communications) et RB (analytics). Pas de mécanisme de propagation décrit | Non-conformité RGPD avec risque d'amende (4% du CA mondial) |
| 7 | **Alimentation comptable depuis N domaines** | GC, AA, SE → FC | La comptabilité reçoit des écritures automatiques depuis au minimum 3 domaines (ventes, achats, stocks). Le mécanisme de génération est décrit dans chaque domaine source mais il n'existe pas de vue unifiée des « événements comptabilisables » | Écritures manquantes, doublons, ou incohérences de séquencement entre les sources |
| 8 | **Cohérence calendrier promotionnel** | GC, CM | Les promotions (GC) et les campagnes marketing (CM) doivent être synchronisées. Un calendrier promotionnel unifié n'est décrit dans aucun des deux domaines | Promotion en caisse sans communication marketing, ou campagne annonçant une promo non active en magasin |
