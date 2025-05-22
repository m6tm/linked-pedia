# Feature Specification: Statistiques de Performance

## Overview

- **Feature ID:** FEAT-011
- **Priority:** High
- **Estimated Effort:** À déterminer (Estimation initiale : 3-4 jours, dépend de l'accès aux données via l'API LinkedIn et de la complexité de l'affichage)
- **Status:** Planned

## Business Context

### Objective

Afficher les métriques clés (impressions, interactions, taux d'engagement, etc.) pour les posts publiés par l'utilisateur via Link-Pedia. L'objectif est d'aider l'utilisateur à comprendre la performance de son contenu et à identifier ce qui résonne le mieux auprès de son audience.

### User Stories

- As a user, I want to see the performance metrics of my published posts so that I understand what works. (Must Have)
- As a user, I want to see key metrics like impressions, likes, comments, and shares for each post. (Must Have)
- As a user, I want to see the engagement rate for each post. (Must Have)
- As a user, I want to see the statistics for a specific post from the dashboard. (Must Have - lié à FEAT-009)

### Success Criteria

- L'utilisateur peut accéder à la section des statistiques de performance pour ses posts publiés.
- L'application récupère les statistiques de performance pour les posts publiés via Link-Pedia depuis l'API LinkedIn.
- L'application affiche les métriques clés (impressions, likes, commentaires, partages) pour chaque post publié.
- L'application calcule et affiche le taux d'engagement pour chaque post.
- Les statistiques sont présentées de manière claire et lisible.
- L'application gère les cas où les statistiques ne sont pas encore disponibles pour un post ou si la récupération échoue.

## Design and UX/UI Principles for this Feature

- **Main Objective:** L'interface doit être transparente et informative. La présentation des statistiques doit être visuellement claire et facile à interpréter, permettant à l'utilisateur de tirer rapidement des conclusions sur la performance de son contenu. L'objectif "Silicon Valley / Y Combinator" implique ici de transformer des données brutes en insights exploitables via une interface élégante et intuitive.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** Utiliser des tableaux ou des listes stylisées pour afficher les métriques par post. Utiliser des indicateurs visuels (couleurs, icônes) pour mettre en évidence les posts les plus performants ou les métriques clés. Potentiellement, utiliser des graphiques simples pour montrer l'évolution des métriques dans le temps (si l'API le permet et si hors scope MVP). L'interface doit être réactive.

## Functional Requirements

### Core Functionality

1.  Récupérer la liste des posts publiés par l'utilisateur via Link-Pedia depuis la base de données (`public.posts`).
2.  Pour chaque post publié, appeler l'API LinkedIn pour récupérer ses statistiques de performance (impressions, likes, commentaires, partages).
3.  Stocker les statistiques récupérées dans la base de données (table `post_analytics`).
4.  Calculer le taux d'engagement pour chaque post.
5.  Afficher la liste des posts publiés avec leurs métriques clés et leur taux d'engagement.
6.  Permettre de visualiser les statistiques détaillées pour un post spécifique (potentiellement sur une page dédiée).
7.  Mettre à jour régulièrement les statistiques des posts publiés (mécanisme de synchronisation à définir).
8.  Gérer les cas où l'API ne retourne pas de statistiques ou si la récupération échoue.

### User Interactions

1.  L'utilisateur navigue vers la section "Statistiques" ou clique sur un post publié dans le tableau de bord (FEAT-009).
2.  L'application affiche un indicateur de chargement pendant la récupération des statistiques.
3.  L'application affiche la liste des posts publiés avec leurs métriques.
4.  L'utilisateur examine les statistiques.
5.  L'utilisateur peut cliquer sur un post pour voir plus de détails (si implémenté).

### Business Rules

1.  Les statistiques ne sont disponibles que pour les posts publiés via Link-Pedia.
2.  Les statistiques doivent être récupérées via l'API LinkedIn.
3.  Les statistiques doivent être mises à jour périodiquement pour refléter les données les plus récentes.
4.  Le calcul du taux d'engagement doit suivre une formule définie (voir Logique Métier).

## Technical Specifications

### Affected Components

- **Frontend:** Page/section "Statistiques", composant de liste de posts avec métriques, affichage des métriques clés et du taux d'engagement, indicateur de chargement.
- **Backend:** Supabase Edge Function pour récupérer les statistiques des posts publiés (`GET /analytics/posts`), Edge Function pour appeler l'API LinkedIn pour les statistiques, mécanisme de synchronisation des statistiques (ex: tâche planifiée).
- **Database:** Table `public.posts` (lecture), nouvelle table `public.post_analytics` pour stocker les statistiques.
- **External Services:** API officielle de LinkedIn (endpoints pour les statistiques de posts).

### API Changes

#### New Endpoints

- **Endpoint:** `GET /analytics/posts`
  - Purpose: Récupérer la liste des posts publiés avec leurs statistiques.
  - Request: `{}` (potentiellement avec des paramètres de filtrage/tri/pagination).
  - Response: `{ "posts": [{ "id": "...", "content_preview": "...", "published_date": "...", "metrics": { "impressions": number, "likes": number, "comments": number, "shares": number, "engagement_rate": number } }] }` ou `{ "error": { ... } }`.

#### Modified Endpoints

Aucun.

### Database Changes

#### New Tables/Collections

- **Table:** `public.post_analytics`
  - Purpose: Stocker les statistiques de performance des posts publiés.
  - Fields:
    - `id`: `uuid` (Clé primaire)
    - `post_id`: `uuid` (Clé étrangère vers `public.posts.id`)
    - `impressions`: `integer`
    - `likes`: `integer`
    - `comments`: `integer`
    - `shares`: `integer`
    - `retrieved_at`: `timestamp with time zone` (Date et heure de récupération des statistiques)

#### Modified Tables/Collections

- **Table:** `public.posts`
  - Ajouter un champ `linkedin_post_id`: `text` (pour stocker l'ID du post sur LinkedIn après publication, nécessaire pour récupérer les statistiques via l'API).

### UI Changes

#### New Screens/Components

- **Screen:** Page "Statistiques" (`/analytics` ou `/posts/published`)
  - Description: Page affichant la liste des posts publiés avec leurs métriques de performance.
  - Wireframe/Mockup (Objectif YC Standard): Tableau ou liste claire affichant chaque post publié avec des colonnes pour les métriques (Impressions, Likes, Commentaires, Partages, Taux d'Engagement). Utiliser des couleurs ou des icônes pour mettre en évidence les posts les plus performants.
- **Component:** Liste de Posts avec Statistiques
  - Description: Composant pour afficher les posts publiés et leurs métriques.
- **Component:** Affichage des Métriques
  - Description: Composant pour présenter les métriques clés et le taux d'engagement pour un post.

#### Modified Screens/Components

- **Screen:** Tableau de Bord des Posts (FEAT-009)
  - Permettre de cliquer sur un post publié pour naviguer vers la page des statistiques détaillées (si implémenté).

## Implementation Plan

### Dependencies

- Supabase SDK.
- Table `public.posts` (avec `linkedin_post_id`).
- API LinkedIn (endpoints statistiques).
- Mécanisme de synchronisation des statistiques.

### Implementation Steps

1.  Ajouter le champ `linkedin_post_id` à la table `public.posts`.
2.  Créer la table `public.post_analytics` avec les politiques RLS appropriées.
3.  Modifier l'Edge Function de publication (FEAT-010) pour enregistrer le `linkedin_post_id` retourné par l'API LinkedIn.
4.  Créer l'Edge Function `GET /analytics/posts`.
5.  Dans l'Edge Function `/analytics/posts`, récupérer les posts publiés depuis la DB.
6.  Pour chaque post, récupérer les statistiques les plus récentes depuis la table `post_analytics` ou appeler l'API LinkedIn si les données sont obsolètes ou manquantes.
7.  Implémenter le calcul du taux d'engagement.
8.  Créer la page frontend "Statistiques".
9.  Implémenter l'appel à l'Edge Function `GET /analytics/posts` depuis le frontend.
10. Afficher les statistiques en utilisant les composants frontend appropriés.
11. Mettre en place un mécanisme de synchronisation côté backend (ex: tâche planifiée) pour récupérer et stocker régulièrement les statistiques des posts publiés via l'API LinkedIn.

### Technical Considerations

- Accès aux statistiques via l'API LinkedIn : vérifier les scopes nécessaires et la granularité des données disponibles.
- Fréquence de synchronisation des statistiques : à définir pour équilibrer fraîcheur des données et utilisation des quotas API.
- Gestion du stockage des statistiques historiques si l'on souhaite afficher l'évolution dans le temps (hors scope MVP, mais la structure de `post_analytics` le permet).
- Calcul correct du taux d'engagement, en gérant les cas limites (impressions nulles).

## Testing Requirements

### Unit Tests

- Tests unitaires pour la logique de calcul du taux d'engagement.
- Tests unitaires pour l'Edge Function `GET /analytics/posts` (mockant la base de données et l'API LinkedIn).
- Tests unitaires pour le mécanisme de synchronisation (mockant l'API et la DB).

### Integration Tests

- Tester le flux complet : Publication d'un post (FEAT-010) -> Récupération du `linkedin_post_id` -> Synchronisation des statistiques (manuelle ou déclenchée) -> Affichage des statistiques dans l'UI.
- Tester l'affichage correct des métriques et du taux d'engagement.
- Tester les cas où les statistiques ne sont pas encore disponibles.

### User Acceptance Tests

- Je publie un post via Link-Pedia.
- Après un certain temps, je navigue vers la section "Statistiques".
- Je vois mon post publié dans la liste avec ses métriques (impressions, likes, commentaires, partages).
- Je vois le taux d'engagement calculé pour mon post.
- Les statistiques semblent se mettre à jour périodiquement.

## Acceptance Criteria

```gherkin
Feature: Statistiques de Performance

  Scenario: Affichage des statistiques des posts publiés
    Given je suis connecté à Link-Pedia
    And j'ai publié des posts via l'application
    And les statistiques pour ces posts ont été récupérées via l'API LinkedIn
    When je navigue vers la section "Statistiques"
    Then l'application affiche une liste de mes posts publiés
    And pour chaque post, les métriques clés (impressions, likes, commentaires, partages) sont affichées
    And le taux d'engagement est calculé et affiché pour chaque post

  Scenario: Synchronisation périodique des statistiques
    Given je suis connecté à Link-Pedia
    And j'ai des posts publiés
    And de nouvelles interactions ont eu lieu sur ces posts sur LinkedIn
    When le mécanisme de synchronisation des statistiques s'exécute
    Then l'application récupère les statistiques mises à jour via l'API LinkedIn
    And les statistiques affichées dans l'interface sont mises à jour

  Scenario: Affichage d'un indicateur si les statistiques ne sont pas disponibles
    Given je suis connecté à Link-Pedia
    And j'ai publié un post très récemment
    When je navigue vers la section "Statistiques"
    Then l'application affiche mon post
    And indique que les statistiques ne sont pas encore disponibles ou sont en cours de récupération
```

## Security Considerations

- Sécuriser l'Edge Function `GET /analytics/posts`.
- Gérer les tokens LinkedIn de manière sécurisée pour la récupération des statistiques.
- Implémenter RLS sur la table `post_analytics`.

## Performance Considerations

- La récupération des statistiques via l'API LinkedIn peut prendre du temps, surtout pour de nombreux posts. Le mécanisme de synchronisation doit être efficace.
- Optimiser la requête de base de données pour récupérer les posts et leurs statistiques associées.

## Accessibility Requirements (Integral Part of Quality UX)

- La page des statistiques est navigable au clavier.
- Les tableaux ou listes de statistiques sont correctement structurés et lisibles par les lecteurs d'écran.
- Les métriques et le taux d'engagement sont clairement annoncés.
- Les indicateurs de chargement/indisponibilité des statistiques sont accessibles.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

- Localiser les noms des métriques et les formats numériques (si l'internationalisation est implémentée).

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour l'Edge Function `GET /analytics/posts`, l'Edge Function de synchronisation, et les composants frontend associés.
- Documentation de l'endpoint API `/analytics/posts`.
- Documentation du mécanisme de synchronisation des statistiques.
- Mise à jour du schéma de base de données pour inclure la table `post_analytics` et le champ `linkedin_post_id`.
- Documenter les scopes LinkedIn nécessaires pour accéder aux statistiques.

## Open Questions

- Quel est le meilleur mécanisme pour synchroniser régulièrement les statistiques (Supabase Scheduled Edge Functions, service externe) ?
- Quelle est la fréquence optimale de synchronisation pour équilibrer fraîcheur des données et utilisation des quotas API LinkedIn ?
- Faut-il stocker l'historique des statistiques pour afficher des graphiques d'évolution ? (Hors scope MVP, mais à considérer).

---

_Last Updated: 22/05/2025_
