# Feature Specification: Tableau de Bord des Posts

## Overview

- **Feature ID:** FEAT-009
- **Priority:** High
- **Estimated Effort:** À déterminer (Estimation initiale : 3-4 jours, dépend de la complexité de l'interface et de la gestion des différents statuts)
- **Status:** Planned

## Business Context

### Objective

Fournir une interface centralisée où l'utilisateur peut visualiser et gérer tous ses posts créés dans Link-Pedia, qu'ils soient en brouillon, programmés ou déjà publiés. L'objectif est d'offrir une vue d'ensemble claire et un contrôle facile sur son calendrier de publication LinkedIn.

### User Stories

- As a user, I want to see all my posts (drafts, scheduled, published) in one place so that I can manage them easily. (Must Have)
- As a user, I want to filter or sort my posts by status (draft, scheduled, published) so that I can quickly find what I need. (Must Have)
- As a user, I want to see key information about each post (content preview, status, date) in the list. (Must Have)
- As a user, I want to be able to perform actions on posts directly from the dashboard (edit draft, view published, delete). (Must Have)

### Success Criteria

- L'utilisateur peut accéder au tableau de bord des posts.
- Le tableau de bord affiche une liste de tous les posts de l'utilisateur stockés dans Link-Pedia.
- La liste inclut les posts en statut 'brouillon', 'programmé' et 'publié'.
- Pour chaque post, des informations clés sont affichées (ex: début du contenu, statut, date de création/programmation/publication).
- L'utilisateur peut filtrer la liste par statut.
- L'utilisateur peut cliquer sur un post pour voir plus de détails ou effectuer des actions (édition pour brouillons, visualisation pour publiés).
- L'application gère les cas où l'utilisateur n'a pas encore de posts.

## Design and UX/UI Principles for this Feature

- **Main Objective:** L'interface doit être claire et organisée. Le tableau de bord est un point central de l'application, il doit donc être intuitif et permettre une gestion efficace. L'objectif "Silicon Valley / Y Combinator" implique ici une présentation des données (liste de posts) de manière lisible, avec des options de filtrage/tri faciles à utiliser et des actions claires pour chaque élément.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** Utiliser un composant `Table` ou une liste stylisée pour afficher les posts. Utiliser des onglets ou des boutons de filtre clairs pour basculer entre les statuts (Brouillons, Programmés, Publiés). Afficher un aperçu du contenu du post (les premières lignes). Utiliser des icônes ou des étiquettes visuelles pour indiquer le statut. Les actions disponibles pour chaque post doivent être évidentes (ex: icônes d'édition, de suppression, de visualisation). L'interface doit être réactive et bien gérer l'affichage sur différents appareils.

## Functional Requirements

### Core Functionality

1.  Récupérer la liste des posts associés à l'utilisateur connecté depuis la table `public.posts` en base de données.
2.  Afficher la liste des posts, incluant les posts en statut 'brouillon', 'programmé' et 'publié'.
3.  Pour chaque post, afficher un aperçu du contenu, le statut, et la date pertinente (création pour brouillon, programmation pour programmé, publication pour publié).
4.  Permettre de filtrer la liste des posts par statut.
5.  Permettre de trier la liste (ex: par date).
6.  Permettre de naviguer vers la page d'édition pour les posts en statut 'brouillon'.
7.  Permettre de visualiser les détails d'un post publié (et potentiellement ses statistiques - lien vers FEAT-011).
8.  Permettre de supprimer un post (brouillon ou programmé) (voir Business Rules).
9.  Afficher un message clair si l'utilisateur n'a aucun post.

### User Interactions

1.  L'utilisateur navigue vers le tableau de bord des posts.
2.  L'application affiche la liste de ses posts.
3.  L'utilisateur clique sur un filtre de statut (ex: "Programmés").
4.  La liste se met à jour pour afficher uniquement les posts programmés.
5.  L'utilisateur clique sur un post en brouillon.
6.  L'application le redirige vers la page d'édition de ce brouillon.
7.  L'utilisateur clique sur un post publié.
8.  L'application affiche les détails du post publié.
9.  L'utilisateur clique sur une icône de suppression pour un post en brouillon ou programmé.
10. L'application demande confirmation et supprime le post.

### Business Rules

1.  Seuls les posts associés à l'utilisateur connecté (ou à ses sous-comptes gérés) doivent être affichés.
2.  La suppression n'est autorisée que pour les posts en statut 'brouillon' ou 'programmé'. Les posts 'publiés' ne peuvent pas être supprimés depuis Link-Pedia (car ils existent sur LinkedIn et la suppression via API peut être limitée ou non souhaitée).
3.  Les posts sont triés par défaut par date (la plus récente en premier).

## Technical Specifications

### Affected Components

- **Frontend:** Page "Tableau de Bord des Posts", composant de liste de posts, composants de filtre/tri, composants d'affichage d'un élément de post (aperçu, statut, date, actions).
- **Backend:** Supabase Edge Function pour récupérer la liste des posts de l'utilisateur (`GET /posts`), Edge Function pour gérer la suppression d'un post (`DELETE /posts/{id}`).
- **Database:** Table `public.posts` (lecture, suppression).
- **External Services:** Aucun service externe direct pour l'affichage du tableau de bord, mais les données affichées proviennent de la base de données peuplée par d'autres fonctionnalités (génération, publication, statistiques).

### API Changes

#### New Endpoints

- **Endpoint:** `GET /posts`
  - Purpose: Récupérer la liste des posts de l'utilisateur, potentiellement filtrée par statut et triée.
  - Request: `{ "status": "string" (optionnel), "sort_by": "string" (optionnel), "sort_order": "string" (optionnel) }`
  - Response: `{ "posts": [{ ... }] }` (liste des posts) ou `{ "error": { ... } }`.
- **Endpoint:** `DELETE /posts/{id}`
  - Purpose: Supprimer un post (brouillon ou programmé) par son ID.
  - Request: Aucun (ID dans l'URL).
  - Response: `{ "success": boolean }` ou `{ "error": { ... } }`.

#### Modified Endpoints

Aucun.

### Database Changes

#### New Tables/Collections

Aucun.

#### Modified Tables/Collections

- **Table:** `public.posts`
  - S'assurer que les politiques RLS sont correctement configurées pour permettre à un utilisateur de lire et supprimer uniquement ses propres posts (ou ceux de ses sous-comptes gérés).

### UI Changes

#### New Screens/Components

- **Screen:** Page "Tableau de Bord des Posts" (`/dashboard` ou `/posts`)
  - Description: Page principale affichant la liste des posts de l'utilisateur avec des options de filtrage et de tri.
  - Wireframe/Mockup (Objectif YC Standard): Disposition claire avec des onglets ou des boutons de filtre en haut (Brouillons, Programmés, Publiés). Une liste ou un tableau en dessous affichant chaque post avec un aperçu du contenu, le statut, la date et des icônes d'action (éditer, supprimer, voir). Message pour l'état vide.
- **Component:** Liste de Posts
  - Description: Composant pour afficher la liste des posts. Chaque élément de la liste est un résumé cliquable du post.
- **Component:** Filtres/Tri des Posts
  - Description: Composants (onglets, boutons, menus déroulants) pour filtrer et trier la liste des posts.

#### Modified Screens/Components

- **Navigation :** Ajouter un lien vers le tableau de bord dans la navigation principale.

## Implementation Plan

### Dependencies

- Supabase SDK.
- Table `public.posts`.
- Logique d'autorisation (RLS).

### Implementation Steps

1.  Créer l'Edge Function `GET /posts` pour récupérer les posts de l'utilisateur avec filtrage et tri.
2.  Créer l'Edge Function `DELETE /posts/{id}` pour supprimer un post, en vérifiant le statut et l'autorisation.
3.  Créer la page frontend "Tableau de Bord des Posts".
4.  Implémenter l'appel à l'Edge Function `GET /posts` depuis le frontend au chargement de la page.
5.  Afficher la liste des posts reçus en utilisant un composant de liste.
6.  Implémenter les filtres et le tri, en appelant l'Edge Function `GET /posts` avec les paramètres appropriés.
7.  Implémenter les actions sur les posts :
    - Lien vers la page d'édition pour les brouillons.
    - Lien vers une page de visualisation pour les posts publiés (FEAT-011).
    - Appel à l'Edge Function `DELETE /posts/{id}` pour la suppression, avec confirmation utilisateur.
8.  Gérer l'état de chargement et l'état vide de la liste.

### Technical Considerations

- Performance de la requête de base de données pour récupérer potentiellement un grand nombre de posts. Utiliser la pagination si nécessaire (hors scope MVP, mais à considérer).
- Assurer que les politiques RLS sont robustes pour empêcher l'accès non autorisé aux posts d'autres utilisateurs.
- Gestion des différents statuts de posts et des actions autorisées pour chaque statut.

## Testing Requirements

### Unit Tests

- Tests unitaires pour les Edge Functions `GET /posts` et `DELETE /posts/{id}` (mockant la base de données et l'autorisation).
- Tests unitaires pour les fonctions de filtrage/tri côté frontend (si logique complexe).

### Integration Tests

- Tester la récupération des posts pour un utilisateur connecté.
- Tester le filtrage et le tri.
- Tester la suppression d'un brouillon et d'un post programmé.
- Tester la tentative de suppression d'un post publié (doit échouer).
- Tester l'affichage correct des différents statuts et actions disponibles.

### User Acceptance Tests

- Je peux naviguer vers le tableau de bord.
- Je vois la liste de tous mes posts (brouillons, programmés, publiés).
- Je peux filtrer la liste par statut (ex: voir uniquement les brouillons).
- Je peux cliquer sur un brouillon pour l'éditer.
- Je peux cliquer sur un post publié pour le voir.
- Je peux supprimer un brouillon ou un post programmé.
- Je ne peux pas supprimer un post publié.
- Si je n'ai pas de posts, un message clair s'affiche.

## Acceptance Criteria

```gherkin
Feature: Tableau de Bord des Posts

  Scenario: Affichage de tous les posts de l'utilisateur
    Given je suis connecté à Link-Pedia
    And j'ai des posts en brouillon, programmés et publiés
    When je navigue vers le tableau de bord des posts
    Then l'application affiche une liste de tous mes posts
    And chaque élément de la liste montre un aperçu du contenu, le statut et la date pertinente
    And je peux voir mes posts en brouillon, programmés et publiés dans la liste

  Scenario: Filtrage des posts par statut
    Given je suis sur le tableau de bord des posts
    And j'ai des posts de différents statuts
    When je sélectionne un filtre de statut (ex: "Programmés")
    Then la liste affiche uniquement les posts ayant le statut sélectionné

  Scenario: Navigation vers l'édition d'un brouillon
    Given je suis sur le tableau de bord des posts
    And j'ai un post en statut "brouillon"
    When je clique sur ce post en brouillon
    Then l'application me redirige vers la page d'édition de ce brouillon

  Scenario: Suppression d'un post en brouillon ou programmé
    Given je suis sur le tableau de bord des posts
    And j'ai un post en statut "brouillon" ou "programmé"
    When je clique sur l'icône de suppression pour ce post
    And je confirme la suppression
    Then le post est supprimé de ma liste de posts

  Scenario: Tentative de suppression d'un post publié
    Given je suis sur le tableau de bord des posts
    And j'ai un post en statut "publié"
    When je tente de supprimer ce post (si l'option est visible)
    Then l'application m'empêche de supprimer le post
    And potentiellement affiche un message expliquant pourquoi
```

## Security Considerations

- Implémenter des politiques RLS strictes sur la table `public.posts` pour garantir que les utilisateurs ne peuvent lire, modifier ou supprimer que leurs propres posts (ou ceux de leurs sous-comptes gérés).
- Sécuriser les Edge Functions `GET /posts` et `DELETE /posts/{id}` avec l'authentification utilisateur.

## Performance Considerations

- Optimiser la requête de base de données pour récupérer la liste des posts. Utiliser des index sur les champs `user_id`, `status`, `created_at`/`scheduled_date`/`published_date`.
- Envisager la pagination si le nombre de posts par utilisateur devient très important.

## Accessibility Requirements (Integral Part of Quality UX)

- Le tableau de bord est navigable au clavier.
- La liste des posts est correctement structurée et lisible par les lecteurs d'écran.
- Les filtres et options de tri sont accessibles.
- Les actions sur chaque post (édition, suppression, visualisation) sont accessibles et leur objectif est clair pour les technologies d'assistance.
- L'état vide est clairement communiqué.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

Aucune exigence spécifique pour le MVP. Les chaînes de caractères seront codées en dur en français.

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour les Edge Functions `GET /posts` et `DELETE /posts/{id}` et les composants frontend associés.
- Documentation des endpoints API `/posts`.
- Mise à jour du schéma de base de données pour confirmer les index et les politiques RLS sur la table `posts`.

## Open Questions

- Faut-il implémenter la pagination pour la liste des posts dans le MVP ? (Dépend du nombre attendu de posts par utilisateur).
- Comment gérer la synchronisation des posts publiés directement sur LinkedIn (en dehors de Link-Pedia) ? (Hors scope MVP, mais pertinent pour une vue complète).
- Faut-il permettre l'archivage des posts au lieu de la suppression ? (Hors scope MVP).

---

_Last Updated: 22/05/2025_
