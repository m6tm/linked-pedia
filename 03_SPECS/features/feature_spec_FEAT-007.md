# Feature Specification: Optimiseur de Posts

## Overview

- **Feature ID:** FEAT-007
- **Priority:** High
- **Estimated Effort:** À déterminer (Estimation initiale : 3-4 jours, dépend de l'intégration du service IA et de la complexité de l'optimisation)
- **Status:** Planned

## Business Context

### Objective

Permettre aux utilisateurs de soumettre un brouillon de post (qu'il soit généré par l'IA ou écrit par eux-mêmes) et d'obtenir 3 propositions d'améliorations optimisées pour la pertinence, l'accroche et l'engagement potentiel sur LinkedIn. L'objectif est d'aider les utilisateurs à affiner leur contenu pour de meilleures performances.

### User Stories

- As a user, I want to submit a post draft and get optimized suggestions so that I can improve its quality. (Must Have)
- As a user, I want to receive multiple optimization suggestions so that I can choose the best option. (Must Have)
- As a user, I want the suggestions to focus on improving engagement and relevance for LinkedIn. (Must Have)
- As a user, I want to easily apply an optimization suggestion to my draft. (Should Have)

### Success Criteria

- L'utilisateur peut soumettre un brouillon de post à l'optimiseur.
- L'application appelle le service IA avec le contenu du brouillon.
- Le service IA génère 3 propositions d'optimisation pour le post.
- L'application affiche les 3 propositions de manière claire, idéalement en les comparant au brouillon original.
- L'utilisateur peut examiner les propositions.
- L'utilisateur peut choisir d'appliquer une proposition à son brouillon (remplacer le contenu original par la suggestion choisie).
- L'application gère les erreurs lors de l'appel au service IA.

## Design and UX/UI Principles for this Feature

- **Main Objective:** L'interface doit être utile et intelligente. La présentation des suggestions d'optimisation doit être comparative et facile à appréhender, permettant à l'utilisateur de voir rapidement la valeur ajoutée. L'objectif "Silicon Valley / Y Combinator" implique ici de rendre l'interaction avec l'IA pour l'optimisation fluide et de présenter les résultats de manière visuellement efficace.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** Intégrer l'optimiseur directement dans l'interface d'édition de brouillon (FEAT-004, FEAT-005). Un bouton "Optimiser" clair doit être disponible. Afficher les 3 propositions côte à côte ou dans une liste comparative, en mettant potentiellement en évidence les changements par rapport à l'original. Un bouton "Appliquer cette suggestion" doit être présent pour chaque proposition. Utiliser un indicateur de chargement pendant l'optimisation.

## Functional Requirements

### Core Functionality

1.  Permettre à l'utilisateur de soumettre le contenu d'un brouillon de post à une Edge Function.
2.  Appeler un service IA externe (via Edge Function) avec le contenu du brouillon et des instructions pour générer 3 propositions d'optimisation pour LinkedIn.
3.  Recevoir les 3 propositions du service IA.
4.  Afficher les 3 propositions d'optimisation à l'utilisateur.
5.  Permettre à l'utilisateur de sélectionner une proposition et de l'appliquer à son brouillon actuel (remplacer le contenu du brouillon).
6.  Gérer les erreurs lors de l'appel au service IA.

### User Interactions

1.  L'utilisateur est en train de modifier un brouillon de post.
2.  L'utilisateur clique sur un bouton "Optimiser".
3.  L'application affiche un indicateur de chargement.
4.  L'application affiche les 3 propositions d'optimisation.
5.  L'utilisateur examine les propositions.
6.  L'utilisateur clique sur "Appliquer cette suggestion" pour l'une des propositions.
7.  Le contenu du brouillon est mis à jour avec la suggestion choisie.

### Business Rules

1.  L'optimisation s'applique à un brouillon de post existant.
2.  Le service IA doit retourner exactement 3 propositions d'optimisation.
3.  L'application doit permettre de remplacer le contenu du brouillon par la suggestion choisie.

## Technical Specifications

### Affected Components

- **Frontend:** Interface d'édition de brouillon (FEAT-004, FEAT-005), bouton "Optimiser", zone d'affichage des propositions d'optimisation, boutons "Appliquer cette suggestion", indicateur de chargement.
- **Backend:** Supabase Edge Function pour gérer l'appel au service IA d'optimisation (`/posts/optimize`), interaction avec le service IA externe/MCP.
- **Database:** Table `public.posts` (pour le contenu du brouillon).
- **External Services:** Service IA externe ou MCP capable d'optimiser du texte pour LinkedIn et de retourner plusieurs options.

### API Changes

#### New Endpoints

- **Endpoint:** `POST /posts/optimize`
  - Purpose: Appeler le service IA pour optimiser un contenu de post et retourner 3 suggestions.
  - Request: `{ "content": "string" }` (potentiellement avec des paramètres supplémentaires comme le style souhaité, le public cible, etc.)
  - Response: `{ "suggestions": ["string", "string", "string"] }` (en cas de succès) ou `{ "error": { ... } }` (en cas d'échec).

#### Modified Endpoints

Aucun.

### Database Changes

#### New Tables/Collections

Aucun.

#### Modified Tables/Collections

Aucun.

### UI Changes

#### New Screens/Components

- **Component:** Interface d'Optimisation
  - Description: Section ou modale affichant le bouton "Optimiser" et, après traitement, les 3 propositions d'optimisation avec des boutons pour les appliquer.
  - Wireframe/Mockup (Objectif YC Standard): Intégré à la page d'édition de brouillon. Un bouton "Optimiser" près de la zone de texte. Lorsque cliqué, une section s'ouvre ou une modale apparaît, montrant le texte original et 3 zones de texte (non éditables initialement) avec les suggestions. Un bouton "Appliquer" sous chaque suggestion.

#### Modified Screens/Components

- **Screen:** Page d'édition de brouillon (liée à FEAT-004, FEAT-005)
  - Ajouter le bouton "Optimiser" et la zone d'affichage des propositions.

## Implementation Plan

### Dependencies

- Supabase SDK.
- Librairie de requêtes HTTP côté backend (Edge Function) pour appeler le service IA externe/MCP.
- Service IA externe ou MCP capable d'optimiser du texte et de retourner plusieurs options.
- Interface d'édition de brouillon (FEAT-004, FEAT-005).

### Implementation Steps

1.  Créer l'Edge Function `/posts/optimize`.
2.  Dans l'Edge Function, implémenter l'appel au service IA externe/MCP en utilisant l'outil `use_mcp_tool` ou une librairie HTTP.
3.  Gérer le paramètre d'entrée (contenu du post) et formater le prompt pour le service IA.
4.  Gérer la réponse du service IA et extraire les 3 propositions.
5.  Créer le composant frontend pour l'interface d'optimisation.
6.  Intégrer ce composant dans la page d'édition de brouillon.
7.  Implémenter l'appel à l'Edge Function `/posts/optimize` depuis le frontend, en envoyant le contenu actuel du brouillon.
8.  Gérer les états de chargement et d'erreur côté frontend.
9.  Afficher les 3 propositions reçues.
10. Implémenter la logique pour remplacer le contenu du brouillon par la suggestion choisie lorsque l'utilisateur clique sur "Appliquer".

### Technical Considerations

- Choix et intégration du service IA externe/MCP le plus pertinent et performant pour l'optimisation de texte spécifique à LinkedIn.
- Capacité du service IA à retourner exactement 3 propositions distinctes et de qualité.
- Coût potentiel lié à l'utilisation du service IA.
- Gestion des temps de réponse du service IA.

## Testing Requirements

### Unit Tests

- Tests unitaires pour l'Edge Function `/posts/optimize` (mockant l'appel au service IA).
- Tests unitaires pour les fonctions de formatage du prompt IA.

### Integration Tests

- Tester le flux complet : Saisie/modification brouillon UI -> Appel Edge Function -> Appel service IA (mocké) -> Affichage propositions -> Application suggestion.
- Tester les cas d'erreur (service IA indisponible, réponse IA inattendue).

### User Acceptance Tests

- Je suis en train de modifier un brouillon de post.
- Je clique sur le bouton "Optimiser".
- L'application affiche un indicateur de chargement.
- L'application affiche 3 propositions d'optimisation pour mon post.
- Les propositions semblent pertinentes et différentes de l'original.
- Je peux cliquer sur "Appliquer cette suggestion" pour l'une des propositions.
- Le contenu de mon brouillon est remplacé par la suggestion choisie.

## Acceptance Criteria

```gherkin
Feature: Optimiseur de Posts

  Scenario: Optimisation réussie d'un brouillon de post
    Given je suis connecté à Linked-Pedia
    And je suis en train de modifier un brouillon de post
    When je clique sur le bouton "Optimiser"
    Then l'application affiche un indicateur de chargement
    And l'application appelle le service IA pour optimiser le post
    And le service IA retourne 3 propositions d'optimisation
    And l'application affiche les 3 propositions d'optimisation dans l'interface

  Scenario: Application d'une suggestion d'optimisation
    Given je suis sur la page d'édition de brouillon
    And 3 propositions d'optimisation ont été affichées
    When je clique sur le bouton "Appliquer cette suggestion" pour l'une des propositions
    Then le contenu de mon brouillon de post est remplacé par le contenu de la suggestion choisie

  Scenario: Affichage d'un message d'erreur si l'optimisation échoue
    Given je suis connecté à Linked-Pedia
    And le service IA d'optimisation est indisponible ou retourne une erreur
    When je suis en train de modifier un brouillon de post
    And je clique sur le bouton "Optimiser"
    Then l'application affiche un indicateur de chargement
    And l'appel au service IA échoue
    And l'application affiche un message d'erreur clair à l'utilisateur
```

## Security Considerations

- Sécuriser l'appel à l'Edge Function `/posts/optimize`.
- Gérer les clés API du service IA de manière sécurisée.
- Valider et assainir le contenu du post envoyé au service IA.

## Performance Considerations

- Le temps de réponse de l'optimisation dépendra du service IA. Afficher un indicateur de chargement clair est essentiel.

## Accessibility Requirements (Integral Part of Quality UX)

- Le bouton "Optimiser" est accessible au clavier.
- Les propositions d'optimisation sont clairement structurées et lisibles par les lecteurs d'écran.
- Les boutons "Appliquer cette suggestion" sont accessibles et leur action est claire.
- L'indicateur de chargement est accessible.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

Aucune exigence spécifique pour le MVP. Les chaînes de caractères seront codées en dur en français.

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour l'Edge Function `/posts/optimize` et les composants frontend associés.
- Documentation de l'endpoint API `/posts/optimize`.
- Documentation du service IA externe/MCP utilisé pour l'optimisation.

## Open Questions

- Quel service IA externe ou MCP sera utilisé pour l'optimisation de texte ?
- Comment s'assurer que les 3 propositions sont suffisamment distinctes et de qualité ?
- Faut-il stocker les propositions d'optimisation générées pour référence future ? (Hors scope MVP, mais à considérer).

---

_Last Updated: 22/05/2025_
