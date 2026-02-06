# Exigences non-fonctionnelles

> Extraites de la section 3.2 du CDC — Performance, disponibilité, ergonomie, scalabilité.

## Performance

### Temps de réponse

| Type de transaction | Objectif | Condition |
|--------------------|----------|-----------|
| Transactions courantes (consultation, saisie, validation) | < 2 secondes (95e percentile) | Y compris en période de pic |
| Transactions complexes (calculs, gros volumes) | < 5 secondes | 5% restants |
| Affichage tableau de bord standard | < 5 secondes | - |
| État paramétré (période 1 mois) | < 30 secondes | - |
| Export volumineux (>100 000 lignes) | Mode asynchrone avec notification | - |

### Traitements batch

| Traitement | Fenêtre maximale | Contrainte |
|-----------|-----------------|------------|
| Clôture journalière comptable | < 2 heures | Écritures auto + contrôles de cohérence |
| Calcul MRP (toutes références, tous sites) | Terminé avant 6h00 | Pour traitement commandes fournisseurs dès ouverture |
| Mise à jour tarification / promotions | Temps réel, sans interruption | Activation des promos planifiées |
| Synchronisation stock (ERP ↔ caisses, e-commerce, WMS) | Latence < 5 min (normal), < 15 min (pic) | - |

### Pics d'activité

- Charge multipliée par **3** en période de pic (soldes, Black Friday, Noël)
- Pics horaires concentrés entre **12h-14h** et **18h-21h**
- Les engagements de performance s'appliquent aussi en période de pic
- Test de charge grandeur nature simulant le **Black Friday** obligatoire avant 1er déploiement

### Monitoring

- Métriques de performance exposées (temps de réponse par transaction, utilisation ressources, files d'attente)
- Intégration outils **APM** (Application Performance Monitoring) du Groupe
- Alertes automatiques de dégradation avant impact utilisateur
- Tableau de bord de performance temps réel (IT + sponsors métiers)

## Disponibilité

### SLA de disponibilité

| Plage horaire | Disponibilité cible | Indisponibilité max/an |
|--------------|--------------------|-----------------------|
| Heures critiques (6h-23h, 7j/7) | **99,9%** | ~8 heures |
| Heures non critiques (23h-6h) | 99,0% | ~25 heures |
| Périodes commerciales critiques* | **99,95%** | ~2 heures (sur la période) |

> *Périodes critiques : soldes hiver (2 sem.), soldes été (2 sem.), Black Friday (4 jours), Noël (15 nov - 31 déc)

### Continuité d'activité (PCA/PRA)

| Composant | RTO | RPO | Criticité |
|-----------|-----|-----|-----------|
| ERP Core (ventes, stocks, finance) | **4 heures** | **1 heure** | CRITIQUE |
| Interfaces temps réel (caisses, e-commerce) | 2 heures | 15 minutes | CRITIQUE |
| Reporting et BI | 8 heures | 4 heures | ÉLEVÉE |
| Environnements hors production | 24 heures | 24 heures | STANDARD |

### Architecture de résilience

- Redondance tous composants critiques : cluster actif-actif, BDD répliquée en synchrone, réseau doublé
- **Aucun SPOF** (Single Point of Failure) sur le chemin critique
- Réplication vers site de secours géographiquement distant (>100 km)
- Certifications attendues : **ISO 22301**, datacenter **Tier III** minimum

### Modes dégradés

| Contexte | Mode dégradé | Durée supportée |
|----------|-------------|-----------------|
| Magasins | Caisse autonome, transactions locales, synchronisation différée | 48 heures minimum |
| Logistique | Procédures manuelles, édition locale documents transport | Variable |
| Comptabilité | Décalage clôture de 24h max | Sans impact légal |

### Sauvegardes

- Sauvegarde complète **quotidienne** (rétention 30 jours en ligne)
- Sauvegarde incrémentale **horaire**
- Stockage sur site distant, chiffré
- Test de restauration **trimestriel** + restauration grandeur nature **annuelle** (exercice PRA)
- Point-in-time recovery supporté

### Supervision

- Health checks exposés pour monitoring du Groupe
- Intégration **Datadog** (supervision) et **PagerDuty** (alertes)
- Alertes multi-niveaux : critique (immédiat, astreinte), warning (heures ouvrées)
- Tableau de bord de disponibilité temps réel

## Ergonomie et expérience utilisateur

### Principes UX

- Interface **moderne**, cohérente avec les standards web/mobile grand public
- Navigation intuitive : **3 clics maximum** pour les fonctionnalités courantes
- Interface contextualisée par profil (vendeur, contrôleur de gestion, acheteur, etc.)
- Parcours optimisés pour les tâches les plus fréquentes
- Assistants (wizards) pour les processus complexes

### Personnalisation

- Tableaux de bord personnels (widgets, disposition)
- Colonnes configurables dans les listes (ordre, taille, visibilité)
- Filtres et recherches favoris
- Raccourcis et favoris vers transactions fréquentes
- Profil synchronisé quel que soit le poste de connexion
- Configurations par défaut définissables par profil métier (par les admins)

### Recherche

- Barre de recherche **globale** accessible depuis toutes les pages
- Recherche sur articles (réf, désignation, code-barres), clients (nom, email, N° fidélité), commandes, etc.
- Tolérance aux fautes de frappe, suggestions temps réel
- Historique des recherches récentes

### Aide et accompagnement

- Aide en ligne contextuelle par écran
- Info-bulles sur les champs (au survol)
- Base de connaissances consultable
- Système de notifications (tâches en attente, événements)

### Accessibilité

- Conformité **WCAG 2.1 niveau AA** (obligation légale France)
- Navigation complète au clavier
- Compatible lecteurs d'écran (JAWS, NVDA) et plages braille
- Contrastes couleurs : ratios 4.5:1 (texte normal), 3:1 (texte large)
- Agrandissement texte jusqu'à 200% sans perte de fonctionnalité
- Audit d'accessibilité indépendant avant mise en production

### Multi-devices (responsive)

| Support | Usage | Exigence |
|---------|-------|----------|
| Poste fixe (grand écran) | Fonctions administratives | Interface complète |
| Portable | Managers, commerciaux itinérants | Interface complète |
| Tablette | Vendeurs en surface de vente, magasiniers | Ergonomie tactile |
| Smartphone | Consultation mobilité, validations urgentes | Parcours simplifiés |

- Navigateurs supportés : Chrome, Safari, Edge, Firefox (versions récentes)
- Systèmes mobiles : iOS, Android

### Multilingue

| Langue | Pays | Utilisateurs | Priorité |
|--------|------|:------------:|----------|
| Français | France, Belgique, Suisse | 2 100 | P1 - Démarrage |
| Anglais | International, Asie | 800 | P1 - Démarrage |
| Allemand | Allemagne, Autriche, Suisse | 650 | P2 - Vague 2 |
| Néerlandais | Pays-Bas, Belgique | 280 | P2 - Vague 2 |
| Italien | Italie | 320 | P2 - Vague 2 |
| Espagnol | Espagne | 180 | P3 - Vague 3 |
| Langues scandinaves (4) | Suède, Danemark, Norvège, Finlande | 420 | P3 - Vague 3 |
| Polonais, Tchèque | Pologne, Rép. tchèque | 150 | P3 - Vague 3 |
| Thaï | Thaïlande | 100 | P4 - Vague Asie |

- Traduction de qualité professionnelle (natifs, vocabulaire retail), pas de traduction automatique non révisée
- Ajout de nouvelles langues sans développement (fichiers de ressources)
- Conventions locales respectées : formats date, séparateurs numériques, symbole monétaire, 1er jour semaine, format adresse
- Encodage **UTF-8** de bout en bout (y compris caractères non latins : thaï, et futur chinois/japonais/coréen)

## Scalabilité

### Volumétries actuelles vs cibles 2030

| Indicateur | 2025 | Cible 2030 | Facteur |
|-----------|------|-----------|---------|
| Commandes annuelles | 15,2 M | 30 M | x2 |
| Lignes de commande annuelles | 52 M | 100 M | x2 |
| Commandes/jour (pic) | 150 000 | 300 000 | x2 |
| Références produits | 218 000 | 400 000 | x1,8 |
| Fiches clients | 8,5 M | 15 M | x1,8 |
| Utilisateurs nommés | 4 500 | 8 000 | x1,8 |
| Utilisateurs concurrents (pic) | 2 500 | 5 000 | x2 |
| Points de vente | 347 | 600 | x1,7 |
| Pays | 15 | 25 | x1,7 |

### Exigences

- Montée en charge **linéaire et prévisible**, sans effet de seuil ni point de rupture
- Mécanismes de **scaling automatique** (architecture Cloud)
- Coûts qui ne croissent **pas plus vite que le volume** d'activité (économies d'échelle)
- Simulation d'évolution des coûts sur 5 ans (scénarios base, optimiste, accéléré)

## Maintenabilité

### Purge et archivage

| Type de données | Rétention en ligne | Archivage |
|----------------|-------------------|-----------|
| Données transactionnelles opérationnelles | 3 ans | Oui (accès audit) |
| Historiques analytiques | 7 ans | Pour tendances |
| Données personnelles | Selon durées RGPD | Purge après délai |
| Logs d'audit | 12 mois en ligne | Long terme (légal) |

### Tests de performance

- Tests de charge partie intégrante de la **recette**
- Scénarios représentatifs basés sur les profils d'utilisation réels
- Environnement de test dimensionné de manière représentative
- Tests renouvelés **régulièrement** en exploitation
