# Feature Specification: Générateur d'Accroches

## Overview

- **Feature ID:** FEAT-008
- **Priority:** High
- **Estimated Effort:** À déterminer (Estimation initiale : 2-3 jours, dépend de l'intégration du service IA et de l'accès aux données de tendances)
- **Status:** Planned

## Business Context

### Objective

Proposer à l'utilisateur des suggestions d'accroches percutantes pour son post, potentiellement basées sur l'analyse des meilleures pratiques et des tendances actuelles sur LinkedIn. L'objectif est d'aider l'utilisateur à augmenter l'engagement initial sur ses publications.

### User Stories

- As a user, I want to generate catchy hooks for my posts so that I increase initial engagement. (Must Have)
- As a user, I want the hooks to be relevant to my post content. (Must Have)
- As a user, I want the hooks to be based on current LinkedIn trends or best practices. (Must Have)
- As a user, I want to easily insert a generated hook into my post draft. (Should Have)

### Success Criteria

- L'utilisateur peut accéder à la fonctionnalité de génération d'accroches depuis l'interface d'édition de brouillon.
- L'application appelle le service IA avec le contenu du brouillon (ou un résumé/mots-clés).
- Le service IA génère une liste de suggestions d'accroches.
- L'application affiche la liste de suggestions d'accroches de manière claire.
- L'utilisateur peut examiner les suggestions.
- L'utilisateur peut choisir d'insérer une suggestion dans son brouillon.
- L'application gère les erreurs lors de l'appel au service IA.

## Design and UX/UI Principles for this Feature

- **Main Objective:** L'interface doit être pertinente et basée sur les données. La présentation des accroches doit être rapide et facile à utiliser, permettant à l'utilisateur d'améliorer son post sans friction. L'objectif "Silicon Valley / Y Combinator" implique ici de fournir des suggestions intelligentes de manière élégante et intégrée à l'expérience d'édition.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** Intégrer le générateur d'accroches directement dans l'interface d'édition de brouillon (FEAT-004, FEAT-005, FEAT-007). Un bouton "Générer Accroches" clair doit être disponible. Afficher les suggestions d'accroches dans une liste ou une petite modale près de la zone de texte, avec un bouton "Insérer" pour chaque suggestion. Utiliser un indicateur de chargement pendant la génération.

## Functional Requirements

### Core Functionality

1.  Permettre à l'utilisateur de déclencher la génération d'accroches depuis l'interface d'édition de brouillon.
2.  Envoyer le contenu du brouillon (ou un résumé/mots-clés) à une Edge Function.
3.  Dans l'Edge Function, appeler un service IA externe (via Edge Function) avec le contenu pour générer une liste de suggestions d'accroches pour LinkedIn.
4.  Potentiellement, intégrer des données sur les tendances LinkedIn actuelles (si accessibles via API/MCPs) dans le prompt IA pour des suggestions plus pertinentes.
5.  Recevoir la liste de suggestions d'accroches du service IA.
6.  Afficher la liste de suggestions d'accroches à l'utilisateur.
7.  Permettre à l'utilisateur de sélectionner une suggestion et de l'insérer dans son brouillon actuel (généralement au début du post).
8.  Gérer les erreurs lors de l'appel au service IA ou à la récupération des tendances.

### User Interactions

1.  L'utilisateur est en train de modifier un brouillon de post.
2.  L'utilisateur clique sur un bouton "Générer Accroches".
3.  L'application affiche un indicateur de chargement.
4.  L'application affiche une liste de suggestions d'accroches.
5.  L'utilisateur examine les suggestions.
6.  L'utilisateur clique sur "Insérer" pour une suggestion.
7.  L'accroche choisie est insérée dans le brouillon.

### Business Rules

1.  La génération d'accroches s'applique à un brouillon de post existant.
2.  Le service IA doit retourner une liste de suggestions d'accroches pertinentes pour LinkedIn.
3.  L'application doit permettre d'insérer facilement l'accroche choisie dans le brouillon.

## Technical Specifications

### Affected Components

- **Frontend:** Interface d'édition de brouillon (FEAT-004, FEAT-005, FEAT-007), bouton "Générer Accroches", zone d'affichage des suggestions d'accroches, boutons "Insérer", indicateur de chargement.
- **Backend:** Supabase Edge Function pour gérer l'appel au service IA de génération d'accroches (`/posts/hooks`), interaction avec le service IA externe/MCP, potentiellement interaction avec l'API LinkedIn ou un MCP pour les données de tendances.
- **Database:** Table `public.posts` (pour le contenu du brouillon).
- **External Services:** Service IA externe ou MCP capable de générer des accroches. Potentiellement API LinkedIn ou MCP pour les données de tendances (ex: MCP `hdw`).

### API Changes

#### New Endpoints

- **Endpoint:** `POST /posts/hooks`
  - Purpose: Appeler le service IA pour générer des suggestions d'accroches pour un post.
  - Request: `{ "content": "string" }` (potentiellement avec des paramètres supplémentaires comme le thème, le public cible, les tendances actuelles).
  - Response: `{ "hooks": ["string", "string", ...] }` (en cas de succès) ou `{ "error": { ... } }` (en cas d'échec).

#### Modified Endpoints

Aucun.

### Database Changes

#### New Tables/Collections

Aucun.

#### Modified Tables/Collections

Aucun.

### UI Changes

#### New Screens/Components

- **Component:** Interface de Génération d'Accroches
  - Description: Section ou petite modale affichant le bouton "Générer Accroches" et, après traitement, la liste des suggestions avec des boutons pour les insérer.
  - Wireframe/Mockup (Objectif YC Standard): Intégré à la page d'édition de brouillon. Un bouton "Générer Accroches" près de la zone de texte. Lorsque cliqué, une petite liste ou modale apparaît, montrant les suggestions d'accroches. Un bouton "Insérer" à côté de chaque suggestion.

#### Modified Screens/Components

- **Screen:** Page d'édition de brouillon (liée à FEAT-004, FEAT-005, FEAT-007)
  - Ajouter le bouton "Générer Accroches" et la zone d'affichage des suggestions.

## Implementation Plan

### Dependencies

- Supabase SDK.
- Librairie de requêtes HTTP côté backend (Edge Function) pour appeler le service IA externe/MCP.
- Service IA externe ou MCP capable de générer des accroches.
- Potentiellement, accès aux données de tendances LinkedIn (API ou MCP `hdw`).
- Interface d'édition de brouillon (FEAT-004, FEAT-005, FEAT-007).

### Implementation Steps

1.  Créer l'Edge Function `/posts/hooks`.
2.  Dans l'Edge Function, implémenter l'appel au service IA externe/MCP.
3.  Potentiellement, récupérer des données de tendances LinkedIn (via API ou MCP `hdw`) et les inclure dans le prompt IA.
4.  Gérer le paramètre d'entrée (contenu du post) et formater le prompt pour le service IA.
5.  Gérer la réponse du service IA et extraire la liste d'accroches.
6.  Créer le composant frontend pour l'interface de génération d'accroches.
7.  Intégrer ce composant dans la page d'édition de brouillon.
8.  Implémenter l'appel à l'Edge Function `/posts/hooks` depuis le frontend.
9.  Gérer les états de chargement et d'erreur côté frontend.
10. Afficher la liste de suggestions reçues.
11. Implémenter la logique pour insérer l'accroche choisie dans le brouillon lorsque l'utilisateur clique sur "Insérer".

### Technical Considerations

- Choix et intégration du service IA externe/MCP le plus pertinent pour la génération d'accroches.
- Capacité à intégrer des données de tendances en temps réel (dépend de l'accès et de la fiabilité des sources).
- Coût potentiel lié à l'utilisation du service IA et à l'accès aux données de tendances.
- Gestion des temps de réponse.

## Testing Requirements

### Unit Tests

- Tests unitaires pour l'Edge Function `/posts/hooks` (mockant l'appel au service IA et la récupération des tendances).
- Tests unitaires pour les fonctions de formatage du prompt IA.

### Integration Tests

- Tester le flux complet : Édition brouillon UI -> Appel Edge Function -> Appel service IA (mocké) -> Affichage suggestions -> Insertion suggestion.
- Tester les cas d'erreur (service IA indisponible, échec récupération tendances).

### User Acceptance Tests

- Je suis en train de modifier un brouillon de post.
- Je clique sur le bouton "Générer Accroches".
- L'application affiche un indicateur de chargement.
- L'application affiche une liste de suggestions d'accroches pertinentes pour mon post.
- Je peux cliquer sur "Insérer" pour une suggestion.
- L'accroche choisie est insérée au début de mon brouillon.

## Acceptance Criteria

```gherkin
Feature: Générateur d'Accroches

  Scenario: Génération et affichage réussis d'accroches
    Given je suis connecté à Link-Pedia
    And je suis en train de modifier un brouillon de post
    When je clique sur le bouton "Générer Accroches"
    Then l'application affiche un indicateur de chargement
    And l'application appelle le service IA pour générer des accroches
    And une liste de suggestions d'accroches pertinentes est générée
    And la liste de suggestions est affichée dans l'interface

  Scenario: Insertion d'une suggestion d'accroche dans le brouillon
    Given je suis sur la page d'édition de brouillon
    And une liste de suggestions d'accroches a été affichée
    When je clique sur le bouton "Insérer" pour l'une des suggestions
    Then le texte de l'accroche choisie est inséré au début de mon brouillon de post

  Scenario: Affichage d'un message d'erreur si la génération échoue
    Given je suis connecté à Link-Pedia
    And le service IA de génération d'accroches est indisponible ou retourne une erreur
    When je suis en train de modifier un brouillon de post
    And je clique sur le bouton "Générer Accroches"
    Then l'application affiche un indicateur de chargement
    And l'appel au service IA échoue
    And l'application affiche un message d'erreur clair à l'utilisateur
```

## Security Considerations

- Sécuriser l'appel à l'Edge Function `/posts/hooks`.
- Gérer les clés API du service IA et des sources de tendances de manière sécurisée.
- Valider et assainir le contenu du post envoyé au service IA.

## Performance Considerations

- Le temps de réponse de la génération d'accroches dépendra du service IA et de la récupération des tendances. Afficher un indicateur de chargement clair est essentiel.

## Accessibility Requirements (Integral Part of Quality UX)

- Le bouton "Générer Accroches" est accessible au clavier.
- La liste de suggestions est navigable au clavier et les éléments sont correctement annoncés par les lecteurs d'écran.
- Les boutons "Insérer" sont accessibles et leur action est claire.
- L'indicateur de chargement est accessible.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

Aucune exigence spécifique pour le MVP. Les chaînes de caractères seront codées en dur en français.

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour l'Edge Function `/posts/hooks` et les composants frontend associés.
- Documentation de l'endpoint API `/posts/hooks`.
- Documentation du service IA externe/MCP utilisé pour la génération d'accroches.
- Documentation de la source des données de tendances (si utilisée).

## Open Questions

- Quel service IA externe ou MCP sera utilisé pour la génération d'accroches ?
- Comment accéder aux données de tendances LinkedIn en temps réel de manière fiable et conforme aux politiques de LinkedIn ? (Explorer l'utilisation du MCP `hdw`).
- Combien de suggestions d'accroches le service IA doit-il retourner ? (Le PRD n'en spécifie pas un nombre fixe, mais une liste est attendue).

---

_Last Updated: 22/05/2025_
