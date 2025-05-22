# Feature Specification: Conseils Personnalisés

## Overview

- **Feature ID:** FEAT-012
- **Priority:** High
- **Estimated Effort:** À déterminer (Estimation initiale : 3-4 jours, dépend de la complexité de l'analyse et de la génération de conseils)
- **Status:** Planned

## Business Context

### Objective

Fournir à l'utilisateur des recommandations et des conseils personnalisés basés sur l'analyse de ses statistiques de performance de posts (FEAT-011), de son profil (FEAT-003) et potentiellement de ses objectifs et préférences (FEAT-015). L'objectif est d'aider l'utilisateur à améliorer continuellement sa stratégie de contenu et son personal branding sur LinkedIn.

### User Stories

- As a user, I want to receive personalized tips based on my performance so that I can improve my strategy. (Must Have)
- As a user, I want the tips to be actionable and relevant to my goals. (Must Have)
- As a user, I want to see tips related to my post performance (what worked, what didn't) and profile optimization. (Must Have)
- As a user, I want to see tips related to personal branding and conversion. (Should Have)

### Success Criteria

- L'utilisateur peut accéder à la section des conseils personnalisés.
- L'application analyse les statistiques de performance des posts de l'utilisateur et les résultats de son audit de profil.
- L'application génère des conseils personnalisés basés sur cette analyse et potentiellement les objectifs/préférences de l'utilisateur.
- Les conseils sont présentés de manière claire et exploitable.
- L'application gère les cas où il n'y a pas encore assez de données pour générer des conseils pertinents.

## Design and UX/UI Principles for this Feature

- **Main Objective:** L'interface doit être intelligente et actionable. Les conseils doivent être présentés de manière à inciter l'utilisateur à agir pour améliorer sa présence LinkedIn. L'objectif "Silicon Valley / Y Combinator" implique ici de transformer des analyses de données en recommandations claires et motivantes via une interface élégante et personnalisée.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** Dédié une section ou une page aux conseils. Présenter les conseils sous forme de cartes ou de liste, potentiellement groupés par thème (ex: "Améliorer vos posts", "Optimiser votre profil", "Stratégie"). Utiliser des icônes ou des couleurs pour différencier les types de conseils. Inclure des liens directs vers les fonctionnalités pertinentes (ex: lien vers l'audit de profil, lien vers le générateur de posts). L'interface doit être réactive.

## Functional Requirements

### Core Functionality

1.  Récupérer les statistiques de performance des posts de l'utilisateur (FEAT-011) et les résultats de son dernier audit de profil (FEAT-003).
2.  Potentiellement, récupérer les objectifs et préférences de l'utilisateur (FEAT-015).
3.  Analyser ces données pour identifier les points forts, les points faibles et les opportunités d'amélioration pour l'utilisateur sur LinkedIn.
4.  Appeler un service IA externe (via Edge Function) avec l'analyse des données et les objectifs/préférences pour générer une liste de conseils personnalisés.
5.  Recevoir la liste de conseils du service IA.
6.  Afficher la liste de conseils dans l'interface utilisateur.
7.  Gérer les cas où il n'y a pas assez de données (ex: aucun post publié) pour générer des conseils pertinents (afficher un message approprié).
8.  Gérer les erreurs lors de l'analyse des données ou de l'appel au service IA.

### User Interactions

1.  L'utilisateur navigue vers la section "Conseils Personnalisés".
2.  L'application affiche un indicateur de chargement pendant l'analyse et la génération des conseils.
3.  L'application affiche la liste des conseils personnalisés.
4.  L'utilisateur examine les conseils.
5.  L'utilisateur peut cliquer sur un conseil pour obtenir plus de détails ou être redirigé vers une fonctionnalité connexe.

### Business Rules

1.  Les conseils doivent être basés sur les données spécifiques de l'utilisateur (statistiques, profil, objectifs/préférences).
2.  Les conseils doivent être exploitables et fournir des suggestions concrètes.
3.  L'application doit pouvoir gérer le cas où il n'y a pas suffisamment de données pour générer des conseils pertinents.

## Technical Specifications

### Affected Components

- **Frontend:** Page/section "Conseils Personnalisés", composant d'affichage des conseils, indicateur de chargement.
- **Backend:** Supabase Edge Function pour gérer l'analyse des données et l'appel au service IA de génération de conseils (`GET /analytics/tips`), interaction avec la base de données (tables `post_analytics`, `profile_audits`, `users`), interaction avec le service IA externe/MCP.
- **Database:** Tables `public.post_analytics`, `public.profile_audits`, `public.users` (lecture).
- **External Services:** Service IA externe ou MCP capable de générer des conseils personnalisés basés sur des données d'analyse.

### API Changes

#### New Endpoints

- **Endpoint:** `GET /analytics/tips`
  - Purpose: Analyser les données utilisateur et générer des conseils personnalisés.
  - Request: `{}` (les données utilisateur sont récupérées côté backend via l'authentification).
  - Response: `{ "tips": [{ "title": "string", "description": "string", "category": "string", "related_feature_id": "string" (optionnel) }] }` (en cas de succès) ou `{ "error": { ... } }` (en cas d'échec).

#### Modified Endpoints

Aucun.

### Database Changes

#### New Tables/Collections

Aucun pour le MVP (les conseils ne sont pas persistés, mais générés à la demande).

#### Modified Tables/Collections

Aucun.

### UI Changes

#### New Screens/Components

- **Screen:** Page "Conseils Personnalisés" (`/tips` ou `/analytics/tips`)
  - Description: Page dédiée à l'affichage des conseils personnalisés.
  - Wireframe/Mockup (Objectif YC Standard): Disposition claire présentant les conseils sous forme de liste ou de cartes. Chaque conseil doit être facile à lire et potentiellement inclure un lien vers une action connexe. Message pour l'état sans conseils.
- **Component:** Liste de Conseils Personnalisés
  - Description: Composant pour afficher la liste des conseils générés.

#### Modified Screens/Components

- **Screen:** Tableau de Bord (Optionnel)
  - Ajouter un widget ou une section pour afficher 1-2 conseils clés sur le tableau de bord.

## Implementation Plan

### Dependencies

- Supabase SDK.
- Tables `public.post_analytics`, `public.profile_audits`, `public.users`.
- Service IA externe ou MCP capable de générer des conseils.
- Fonctionnalités FEAT-003 (Audit) et FEAT-011 (Statistiques) pour les données d'entrée.
- Fonctionnalité FEAT-015 (Profil Utilisateur) pour les objectifs/préférences.

### Implementation Steps

1.  Créer l'Edge Function `GET /analytics/tips`.
2.  Dans l'Edge Function, récupérer les données nécessaires de la base de données (statistiques, audit, profil).
3.  Implémenter la logique d'analyse des données pour identifier les points clés.
4.  Appeler le service IA externe/MCP avec l'analyse des données et les objectifs/préférences pour générer les conseils.
5.  Gérer la réponse du service IA et extraire la liste de conseils.
6.  Créer la page frontend "Conseils Personnalisés".
7.  Implémenter l'appel à l'Edge Function `GET /analytics/tips` depuis le frontend.
8.  Gérer les états de chargement et d'erreur côté frontend.
9.  Afficher la liste de conseils reçus.
10. Implémenter les liens vers les fonctionnalités connexes si pertinent.
11. (Optionnel) Intégrer un widget de conseils sur le tableau de bord.

### Technical Considerations

- Choix et intégration du service IA externe/MCP le plus pertinent pour la génération de conseils personnalisés.
- Complexité de l'algorithme d'analyse des données utilisateur pour identifier les insights pertinents.
- Gestion des cas où les données sont insuffisantes (ex: nouvel utilisateur sans posts publiés).
- Coût potentiel lié à l'utilisation du service IA.

## Testing Requirements

### Unit Tests

- Tests unitaires pour la logique d'analyse des données (avec des données mockées).
- Tests unitaires pour l'Edge Function `GET /analytics/tips` (mockant la base de données et l'appel IA).

### Integration Tests

- Tester le flux complet : Données en base de données -> Appel Edge Function -> Analyse/Appel IA (mocké) -> Affichage conseils UI.
- Tester les cas avec différentes données utilisateur (profils variés, statistiques variées).
- Tester le cas où il n'y a pas assez de données.

### User Acceptance Tests

- Je navigue vers la section "Conseils Personnalisés".
- L'application affiche une liste de conseils basés sur mes statistiques et mon profil.
- Les conseils semblent pertinents et exploitables.
- Si je n'ai pas encore assez de données, l'application m'en informe.

## Acceptance Criteria

```gherkin
Feature: Conseils Personnalisés

  Scenario: Affichage réussi des conseils personnalisés
    Given je suis connecté à Linked-Pedia
    And j'ai des statistiques de posts et/ou un rapport d'audit de profil disponibles
    When je navigue vers la section "Conseils Personnalisés"
    Then l'application affiche un indicateur de chargement
    And l'application analyse mes données
    And l'application appelle le service IA pour générer des conseils
    And une liste de conseils personnalisés est affichée dans l'interface
    And les conseils sont basés sur mes données et semblent pertinents

  Scenario: Affichage d'un message si les données sont insuffisantes
    Given je suis connecté à Linked-Pedia
    And je n'ai pas encore assez de données (ex: aucun post publié)
    When je navigue vers la section "Conseils Personnalisés"
    Then l'application affiche un message m'informant que plus de données sont nécessaires pour générer des conseils personnalisés

  Scenario: Affichage d'un message d'erreur si la génération échoue
    Given je suis connecté à Linked-Pedia
    And le service IA de génération de conseils est indisponible ou retourne une erreur
    When je navigue vers la section "Conseils Personnalisés"
    Then l'application affiche un indicateur de chargement
    And l'appel au service IA échoue
    And l'application affiche un message d'erreur clair à l'utilisateur
```

## Security Considerations

- Sécuriser l'Edge Function `GET /analytics/tips`.
- Gérer les clés API du service IA de manière sécurisée.
- S'assurer que seules les données de l'utilisateur connecté sont utilisées pour générer les conseils.

## Performance Considerations

- Le temps de réponse dépendra de l'analyse des données et de l'appel IA. Viser un temps d'exécution raisonnable.

## Accessibility Requirements (Integral Part of Quality UX)

- La page des conseils est navigable au clavier.
- La liste de conseils est correctement structurée et lisible par les lecteurs d'écran.
- Les conseils sont clairs et faciles à comprendre.
- Les liens vers les fonctionnalités connexes sont accessibles.
- L'indicateur de chargement et les messages d'état sont accessibles.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

- Localiser le contenu des conseils générés par l'IA (si l'internationalisation est implémentée et si le service IA le supporte).

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour l'Edge Function `GET /analytics/tips` et les composants frontend associés.
- Documentation de l'endpoint API `/analytics/tips`.
- Documentation de l'algorithme d'analyse des données utilisateur.
- Documentation du service IA externe/MCP utilisé pour la génération de conseils.

## Open Questions

- Quel service IA externe ou MCP sera utilisé pour la génération de conseils personnalisés ?
- Comment l'algorithme d'analyse des données utilisateur sera-t-il défini précisément pour identifier les insights les plus pertinents ?
- Faut-il stocker les conseils générés pour l'utilisateur ? (Hors scope MVP, mais à considérer).

---

_Last Updated: 22/05/2025_
