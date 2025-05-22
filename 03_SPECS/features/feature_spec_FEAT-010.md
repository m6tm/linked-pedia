# Feature Specification: Programmation de Posts

## Overview

- **Feature ID:** FEAT-010
- **Priority:** High
- **Estimated Effort:** À déterminer (Estimation initiale : 3-4 jours, dépend de la mise en place du mécanisme de déclenchement programmé)
- **Status:** Planned

## Business Context

### Objective

Permettre aux utilisateurs de planifier la publication de leurs brouillons de posts à une date et une heure futures spécifiées. L'objectif est d'aider les utilisateurs à gérer leur calendrier éditorial et à publier du contenu aux moments optimaux pour leur audience.

### User Stories

- As a user, I want to schedule my posts to be published later so that I can plan my content calendar. (Must Have)
- As a user, I want to specify the exact date and time for my post to be published. (Must Have)
- As a user, I want to see my scheduled posts in the dashboard so that I can manage them. (Must Have - lié à FEAT-009)
- As a user, I want to be able to edit or cancel a scheduled post before it's published. (Must Have - lié à FEAT-009)

### Success Criteria

- L'utilisateur peut sélectionner un brouillon de post et choisir de le programmer.
- L'utilisateur peut spécifier une date et une heure futures pour la publication.
- L'application valide que la date et l'heure sont futures.
- Le post est marqué comme 'programmé' dans la base de données avec la date et l'heure spécifiées.
- Le post programmé apparaît dans le tableau de bord (section "Programmés").
- À la date et l'heure prévues, le post est automatiquement publié sur LinkedIn via l'API.
- L'application gère les échecs de publication programmée.
- L'utilisateur peut modifier ou supprimer un post programmé depuis le tableau de bord avant sa publication.

## Design and UX/UI Principles for this Feature

- **Main Objective:** L'interface doit être fiable et automatisée. Le processus de programmation doit être simple et clair, et l'utilisateur doit avoir confiance que le post sera publié au moment voulu. L'objectif "Silicon Valley / Y Combinator" implique ici une expérience utilisateur fluide pour la planification, avec des sélecteurs de date/heure intuitifs et un feedback clair sur l'état de la programmation.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** Intégrer l'option de programmation dans l'interface d'édition de brouillon ou dans le tableau de bord des brouillons. Utiliser un sélecteur de date et d'heure intuitif et accessible. Afficher clairement la date et l'heure de programmation pour les posts programmés dans le tableau de bord. Les actions d'édition et de suppression pour les posts programmés doivent être facilement accessibles dans le tableau de bord.

## Functional Requirements

### Core Functionality

1.  Permettre à l'utilisateur de choisir de programmer un brouillon de post.
2.  Fournir une interface pour sélectionner une date et une heure futures.
3.  Valider que la date et l'heure sélectionnées sont bien futures.
4.  Mettre à jour l'enregistrement du post dans la table `public.posts` : changer le statut à 'programmé' et enregistrer la `scheduled_date`.
5.  Mettre en place un mécanisme côté backend pour surveiller les posts programmés et déclencher leur publication via l'API LinkedIn à la `scheduled_date`.
6.  Appeler l'API LinkedIn pour publier le post programmé au moment voulu.
7.  Mettre à jour le statut du post à 'publié' (ou 'échec') après la tentative de publication programmée.
8.  Permettre la modification d'un post programmé (mettre à jour le contenu et/ou la date/heure de programmation).
9.  Permettre la suppression d'un post programmé (annuler la programmation et supprimer le post).

### User Interactions

1.  L'utilisateur est en train de modifier un brouillon ou visualise un brouillon dans le tableau de bord.
2.  L'utilisateur clique sur une option "Programmer".
3.  Une interface (modale, section) apparaît pour choisir la date et l'heure.
4.  L'utilisateur sélectionne la date et l'heure et confirme.
5.  L'application confirme la programmation et le post apparaît dans la section "Programmés" du tableau de bord.
6.  À la date et l'heure prévues, le post apparaît sur son profil LinkedIn.
7.  L'utilisateur visualise un post programmé dans le tableau de bord.
8.  L'utilisateur clique sur "Modifier" ou "Supprimer" pour ce post.
9.  L'application permet la modification ou demande confirmation pour la suppression.

### Business Rules

1.  Seuls les posts en statut 'brouillon' peuvent être programmés.
2.  La date et l'heure de programmation doivent être dans le futur.
3.  Un post programmé ne peut être modifié ou supprimé que avant sa publication effective.
4.  Le mécanisme de déclenchement doit être fiable et s'exécuter à l'heure prévue.
5.  La publication programmée doit utiliser le compte LinkedIn lié de l'utilisateur propriétaire du post.

## Technical Specifications

### Affected Components

- **Frontend:** Interface d'édition de brouillon, tableau de bord des posts (FEAT-009), composant de sélection de date/heure, affichage de la date de programmation, options d'édition/suppression pour posts programmés.
- **Backend:** Supabase Edge Function pour gérer la programmation (mise à jour du statut et de la date dans la DB) (`POST /posts/{id}/schedule`), mécanisme de déclenchement programmé (ex: Supabase Scheduled Edge Functions, ou service externe), Edge Function pour la publication effective via l'API LinkedIn (réutilisation de la logique de publication immédiate si possible).
- **Database:** Table `public.posts` (mise à jour du statut et de `scheduled_date`).
- **External Services:** API officielle de LinkedIn (endpoint de publication). Potentiellement un service de planification externe si Supabase ne fournit pas de mécanisme de déclenchement suffisant.

### API Changes

#### New Endpoints

- **Endpoint:** `POST /posts/{id}/schedule`
  - Purpose: Marquer un post existant comme programmé et enregistrer la date/heure.
  - Request: `{ "scheduled_date": "timestamp" }` (ID du post dans l'URL).
  - Response: `{ "success": boolean, "post": { ... } }` ou `{ "error": { ... } }`.

#### Modified Endpoints

Aucun.

### Database Changes

#### New Tables/Collections

Aucun.

#### Modified Tables/Collections

- **Table:** `public.posts`
  - S'assurer que les champs `status` et `scheduled_date` existent et sont correctement typés.
  - S'assurer que les politiques RLS permettent à l'utilisateur de mettre à jour son propre post.

### UI Changes

#### New Screens/Components

- **Component:** Sélecteur de Date et Heure
  - Description: Composant UI permettant à l'utilisateur de choisir une date et une heure précises dans le futur.
  - Wireframe/Mockup (Objectif YC Standard): Un champ de date et un champ d'heure, ou un composant combiné (date picker avec sélection d'heure). Doit être intuitif et mobile-friendly.
- **Component:** Option "Programmer"
  - Description: Bouton ou lien pour initier le processus de programmation.

#### Modified Screens/Components

- **Screen:** Page d'édition de brouillon
  - Ajouter l'option "Programmer" et intégrer le sélecteur de date/heure.
- **Screen:** Tableau de Bord des Posts (FEAT-009)
  - Afficher la date et l'heure de programmation pour les posts programmés.
  - Permettre l'édition et la suppression des posts programmés.

## Implementation Plan

### Dependencies

- Supabase SDK.
- Table `public.posts`.
- API LinkedIn (endpoint de publication).
- Mécanisme de déclenchement programmé côté backend.

### Implementation Steps

1.  Ajouter les champs `status` et `scheduled_date` à la table `public.posts` si ce n'est pas déjà fait.
2.  Créer l'Edge Function `POST /posts/{id}/schedule`.
3.  Implémenter la logique de validation de date/heure dans l'Edge Function.
4.  Implémenter la mise à jour du statut et de la date dans la DB dans l'Edge Function.
5.  Créer le composant frontend Sélecteur de Date et Heure.
6.  Intégrer l'option "Programmer" et le sélecteur dans la page d'édition de brouillon.
7.  Implémenter l'appel à l'Edge Function `/schedule` depuis le frontend.
8.  Mettre en place le mécanisme de déclenchement programmé côté backend (ex: Supabase Scheduled Edge Functions). Ce mécanisme doit interroger la DB pour trouver les posts dont la `scheduled_date` est passée et le statut est 'programmé'.
9.  Créer ou réutiliser l'Edge Function de publication (similaire à une publication immédiate) qui sera appelée par le mécanisme de déclenchement.
10. Dans l'Edge Function de publication, appeler l'API LinkedIn et mettre à jour le statut du post à 'publié' ou 'échec'.
11. Mettre à jour l'affichage dans le tableau de bord (FEAT-009) pour montrer les posts programmés et permettre leur modification/suppression.

### Technical Considerations

- Choix et fiabilité du mécanisme de déclenchement programmé. Supabase Scheduled Edge Functions est une option à explorer en priorité.
- Gestion des fuseaux horaires : la date et l'heure de programmation doivent être gérées correctement, idéalement en UTC et converties pour l'affichage côté client en fonction du fuseau horaire de l'utilisateur.
- Gestion des erreurs lors de la publication programmée (ex: token LinkedIn expiré au moment de la publication).
- Assurer que le mécanisme de déclenchement ne manque pas de posts et ne publie pas de posts en double.

## Testing Requirements

### Unit Tests

- Tests unitaires pour l'Edge Function `/schedule` (validation de date, mise à jour DB).
- Tests unitaires pour la logique du mécanisme de déclenchement (identification des posts à publier).
- Tests unitaires pour l'Edge Function de publication programmée (mockant l'appel API LinkedIn).

### Integration Tests

- Tester le flux complet de programmation : Sélection date/heure UI -> Appel Edge Function -> Mise à jour DB -> Vérification dans le tableau de bord.
- Tester le déclenchement de la publication programmée (en environnement de test avec des dates/heures proches).
- Tester la modification et la suppression d'un post programmé avant son déclenchement.
- Tester les cas d'erreur (date passée, échec publication programmée).

### User Acceptance Tests

- Je peux sélectionner un brouillon et choisir de le programmer.
- Je peux choisir une date et une heure futures.
- Le post apparaît dans la section "Programmés" de mon tableau de bord.
- La date et l'heure de programmation sont affichées correctement.
- À la date et l'heure prévues, le post apparaît sur mon profil LinkedIn.
- Je peux modifier la date/heure ou le contenu d'un post programmé avant sa publication.
- Je peux supprimer un post programmé avant sa publication.
- Si la publication programmée échoue, le statut du post est mis à jour et je suis informé (si possible).

## Acceptance Criteria

```gherkin
Feature: Programmation de Posts

  Scenario: Programmation réussie d'un brouillon de post
    Given je suis connecté à Linked-Pedia
    And j'ai un post en statut "brouillon"
    When je choisis de programmer ce post
    And je sélectionne une date et une heure futures
    And je confirme la programmation
    Then le statut du post est mis à jour à "programmé"
    And la date et l'heure de programmation sont enregistrées
    And le post apparaît dans la section "Programmés" du tableau de bord

  Scenario: Publication automatique d'un post programmé
    Given je suis connecté à Linked-Pedia
    And j'ai un post en statut "programmé" avec une date et une heure futures
    When la date et l'heure de programmation sont atteintes
    Then le mécanisme de déclenchement s'active
    And l'application appelle l'API LinkedIn pour publier le post
    And le post est publié sur mon profil LinkedIn
    And le statut du post dans Linked-Pedia est mis à jour à "publié"

  Scenario: Modification d'un post programmé avant publication
    Given je suis connecté à Linked-Pedia
    And j'ai un post en statut "programmé" dont la date de publication n'est pas encore atteinte
    When je modifie le contenu ou la date/heure de programmation de ce post
    And je sauvegarde les modifications
    Then les modifications sont enregistrées
    And le post reste en statut "programmé" avec les nouvelles informations

  Scenario: Suppression d'un post programmé avant publication
    Given je suis connecté à Linked-Pedia
    And j'ai un post en statut "programmé" dont la date de publication n'est pas encore atteinte
    When je supprime ce post programmé
    And je confirme la suppression
    Then le post est supprimé de ma liste de posts programmés

  Scenario: Échec de la programmation avec une date/heure passée
    Given je suis connecté à Linked-Pedia
    And j'ai un post en statut "brouillon"
    When je choisis de programmer ce post
    And je sélectionne une date et une heure passées
    And je confirme la programmation
    Then l'application refuse la programmation
    And affiche un message d'erreur indiquant que la date/heure doit être future
```

## Security Considerations

- Sécuriser l'Edge Function `/schedule`.
- S'assurer que le mécanisme de déclenchement programmé s'exécute dans un environnement sécurisé et ne peut pas être manipulé.
- Gérer les tokens LinkedIn de manière sécurisée lors de la publication programmée.

## Performance Considerations

- Le mécanisme de déclenchement programmé doit être efficace pour gérer un grand nombre de posts programmés.
- La publication effective via l'API LinkedIn doit être gérée de manière asynchrone pour ne pas bloquer le mécanisme de déclenchement.

## Accessibility Requirements (Integral Part of Quality UX)

- Le sélecteur de date et d'heure est accessible au clavier et utilisable avec un lecteur d'écran.
- Les informations de programmation dans le tableau de bord sont clairement annoncées.
- Les options de modification/suppression pour les posts programmés sont accessibles.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

- Gérer les formats de date et d'heure en fonction de la locale de l'utilisateur (si l'internationalisation est implémentée).
- Stocker les dates en UTC en base de données.

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique lié à la programmation ou à la publication automatique, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour l'Edge Function `/schedule`, l'Edge Function de publication programmée, et le mécanisme de déclenchement.
- Documentation de l'endpoint API `/posts/{id}/schedule`.
- Documentation du mécanisme de déclenchement programmé utilisé (configuration, surveillance).
- Mise à jour du schéma de base de données pour confirmer les champs `status` et `scheduled_date` et les index pertinents.

## Open Questions

- Quel mécanisme précis sera utilisé pour le déclenchement programmé (Supabase Scheduled Edge Functions, Cron job externe, etc.) ?
- Comment gérer les échecs de publication programmée (notifications utilisateur, retries) ?
- Faut-il permettre la modification de la date/heure de programmation après la programmation initiale ? (Oui, inclus dans les exigences).

---

_Last Updated: 22/05/2025_
