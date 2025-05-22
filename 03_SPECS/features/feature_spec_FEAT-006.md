# Feature Specification: Générateur d'Idées de Posts

## Overview

- **Feature ID:** FEAT-006
- **Priority:** High
- **Estimated Effort:** À déterminer (Estimation initiale : 2-3 jours, dépend de l'intégration du service IA)
- **Status:** Planned

## Business Context

### Objective

Proposer à l'utilisateur des idées de posts LinkedIn basées sur un grand thème qu'il fournit, en explorant différents angles et formats. L'objectif est d'aider l'utilisateur à trouver l'inspiration et à varier son contenu.

### User Stories

- As a user, I want to get new post ideas based on a theme so that I never run out of content inspiration. (Must Have)
- As a user, I want the ideas to cover different angles or formats so that I can vary my content. (Must Have)
- As a user, I want to be able to generate more ideas if the initial ones are not suitable. (Should Have)

### Success Criteria

- L'utilisateur peut accéder à la fonctionnalité de génération d'idées de posts.
- L'utilisateur peut fournir un thème principal.
- L'application appelle le service IA avec le thème fourni.
- Le service IA génère une liste d'idées de posts, explorant différents angles.
- L'application affiche la liste d'idées de manière claire.
- L'utilisateur peut sélectionner une idée pour potentiellement la développer (lien vers FEAT-004 ou FEAT-005).
- L'application gère les erreurs lors de l'appel au service IA.

## Design and UX/UI Principles for this Feature

- **Main Objective:** L'interface doit être inspirante et variée. L'objectif "Silicon Valley / Y Combinator" implique ici de présenter les idées de manière engageante et facile à parcourir, encourageant l'utilisateur à explorer de nouvelles pistes de contenu.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** Utiliser un champ de saisie clair pour le thème. Afficher les idées générées sous forme de liste ou de cartes, avec un titre accrocheur et une brève description de l'angle. Utiliser des icônes ou des couleurs pour différencier les types d'idées ou les angles. Fournir un bouton "Générer plus d'idées" ou similaire. L'esthétique doit être épurée et mettre en valeur les suggestions.

## Functional Requirements

### Core Functionality

1.  Fournir une interface pour la saisie du thème principal.
2.  Appeler un service IA externe (via Edge Function) avec le thème fourni pour générer une liste d'idées de posts.
3.  Recevoir la liste d'idées générées par le service IA.
4.  Afficher la liste d'idées dans l'interface utilisateur.
5.  Permettre à l'utilisateur de sélectionner une idée pour potentiellement initier la création d'un brouillon basé sur cette idée (rediriger vers FEAT-004 ou FEAT-005 avec l'idée pré-remplie).
6.  Gérer les erreurs lors de l'appel au service IA.

### User Interactions

1.  L'utilisateur navigue vers la section "Créer un Post" et choisit "Idées de Posts".
2.  L'utilisateur saisit un thème principal.
3.  L'utilisateur clique sur un bouton "Générer des idées".
4.  L'application affiche un indicateur de chargement.
5.  Une liste d'idées de posts apparaît.
6.  L'utilisateur examine les idées.
7.  L'utilisateur peut cliquer sur une idée pour la développer ou cliquer sur "Générer plus d'idées".

### Business Rules

1.  La génération d'idées nécessite un thème principal.
2.  Le service IA doit être capable de proposer des idées sous différents angles ou formats.
3.  Les idées générées ne sont pas automatiquement sauvegardées comme brouillons, l'utilisateur doit choisir de les développer.

## Technical Specifications

### Affected Components

- **Frontend:** Page/section "Idées de Posts", champ de saisie du thème, liste d'affichage des idées, bouton "Générer des idées", bouton "Générer plus d'idées", indicateur de chargement.
- **Backend:** Supabase Edge Function pour gérer l'appel au service IA de génération d'idées (`/posts/ideas`), interaction avec le service IA externe/MCP.
- **Database:** Potentiellement, une table pour stocker les idées générées si l'on souhaite permettre à l'utilisateur de les retrouver plus tard (hors scope MVP, mais à considérer).
- **External Services:** Service IA externe ou MCP capable de générer des idées de contenu basées sur un thème.

### API Changes

#### New Endpoints

- **Endpoint:** `POST /posts/ideas`
  - Purpose: Appeler le service IA pour générer une liste d'idées de posts.
  - Request: `{ "theme": "string" }`
  - Response: `{ "ideas": [{ "title": "string", "description": "string", "angle": "string" }] }` (en cas de succès) ou `{ "error": { ... } }` (en cas d'échec).

#### Modified Endpoints

Aucun.

### Database Changes

#### New Tables/Collections

Aucun pour le MVP (les idées ne sont pas persistées).

#### Modified Tables/Collections

Aucun.

### UI Changes

#### New Screens/Components

- **Screen:** Section "Idées de Posts" (au sein de la page "Créer un Post")
  - Description: Interface pour la génération d'idées de posts. Contient un champ de saisie pour le thème, un bouton de génération, et une zone d'affichage de la liste d'idées.
  - Wireframe/Mockup (Objectif YC Standard): Champ de saisie clair en haut. Liste d'idées présentée sous forme de cartes ou de liste stylisée, chaque élément montrant le titre/angle de l'idée et un bouton/lien pour "Développer cette idée". Bouton "Générer plus d'idées" en bas de la liste.
- **Component:** Liste d'Idées de Posts
  - Description: Composant pour afficher la liste des idées générées. Chaque élément de la liste doit être cliquable ou contenir une action pour développer l'idée.

#### Modified Screens/Components

- **Screen:** Page "Créer un Post"
  - Ajouter des options pour choisir le type de génération (Texte, Fichier, Idée).

## Implementation Plan

### Dependencies

- Supabase SDK.
- Librairie de requêtes HTTP côté backend (Edge Function) pour appeler le service IA externe/MCP.
- Service IA externe ou MCP capable de générer des idées de contenu.

### Implementation Steps

1.  Créer l'Edge Function `/posts/ideas`.
2.  Dans l'Edge Function, implémenter l'appel au service IA externe/MCP en utilisant l'outil `use_mcp_tool` ou une librairie HTTP.
3.  Gérer le paramètre d'entrée (thème) et formater le prompt pour le service IA.
4.  Gérer la réponse du service IA et extraire la liste d'idées.
5.  Créer la page/section frontend "Idées de Posts".
6.  Implémenter le champ de saisie du thème et le bouton "Générer des idées".
7.  Implémenter l'appel à l'Edge Function `/posts/ideas` depuis le frontend.
8.  Gérer les états de chargement et d'erreur côté frontend.
9.  Implémenter le composant pour afficher la liste d'idées.
10. Implémenter la logique pour rediriger ou pré-remplir le générateur de brouillon (FEAT-004/FEAT-005) lors de la sélection d'une idée.

### Technical Considerations

- Choix et intégration du service IA externe/MCP le plus pertinent pour la génération d'idées.
- Capacité du service IA à varier les angles et les formats d'idées.
- Coût potentiel lié à l'utilisation du service IA.
- Gestion des temps de réponse du service IA.

## Testing Requirements

### Unit Tests

- Tests unitaires pour l'Edge Function `/posts/ideas` (mockant l'appel au service IA).
- Tests unitaires pour les fonctions de formatage du prompt IA.

### Integration Tests

- Tester le flux complet : Saisie thème UI -> Appel Edge Function -> Appel service IA (mocké) -> Affichage liste idées.
- Tester la sélection d'une idée et la redirection/pré-remplissage du générateur de brouillon.
- Tester les cas d'erreur (service IA indisponible, réponse IA inattendue).

### User Acceptance Tests

- Je peux naviguer vers la section "Idées de Posts".
- Je peux saisir un thème et cliquer sur "Générer des idées".
- L'application affiche un indicateur de chargement.
- Une liste d'idées de posts pertinentes est générée et affichée.
- Les idées couvrent différents angles ou formats.
- Je peux cliquer sur une idée pour la développer dans le générateur de brouillon.
- Je peux cliquer sur "Générer plus d'idées" pour obtenir une nouvelle liste.

## Acceptance Criteria

```gherkin
Feature: Générateur d'Idées de Posts

  Scenario: Génération et affichage réussis d'idées de posts
    Given je suis connecté à Link-Pedia
    When je navigue vers la section "Créer un Post" et choisis "Idées de Posts"
    And je saisis un thème principal (ex: "Marketing Digital")
    And je clique sur le bouton "Générer des idées"
    Then l'application affiche un indicateur de chargement
    And l'application appelle le service IA pour générer des idées
    And une liste d'idées de posts pertinentes est générée
    And la liste d'idées est affichée dans l'interface utilisateur
    And les idées couvrent différents angles ou formats

  Scenario: Développement d'une idée de post
    Given je suis sur la page "Idées de Posts"
    And une liste d'idées a été générée
    When je clique sur une idée spécifique dans la liste
    Then l'application me redirige vers le générateur de brouillon (Texte ou Fichier)
    And le générateur de brouillon est pré-rempli ou basé sur l'idée sélectionnée

  Scenario: Affichage d'un message d'erreur si la génération échoue
    Given je suis connecté à Link-Pedia
    And le service IA de génération d'idées est indisponible ou retourne une erreur
    When je navigue vers la section "Créer un Post" et choisis "Idées de Posts"
    And je saisis un thème principal
    And je clique sur le bouton "Générer des idées"
    Then l'application affiche un indicateur de chargement
    And l'appel au service IA échoue
    And l'application affiche un message d'erreur clair à l'utilisateur
```

## Security Considerations

- Sécuriser l'appel à l'Edge Function `/posts/ideas`.
- Gérer les clés API du service IA de manière sécurisée.
- Valider et assainir le thème d'entrée utilisateur.

## Performance Considerations

- Le temps de réponse de la génération d'idées dépendra du service IA. Afficher un indicateur de chargement est essentiel.

## Accessibility Requirements (Integral Part of Quality UX)

- Le champ de saisie du thème et le bouton de génération sont accessibles au clavier.
- La liste d'idées est navigable au clavier et les éléments sont correctement annoncés par les lecteurs d'écran.
- Les actions pour développer une idée sont accessibles.
- L'indicateur de chargement est accessible.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

Aucune exigence spécifique pour le MVP. Les chaînes de caractères seront codées en dur en français.

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour l'Edge Function `/posts/ideas` et les composants frontend associés.
- Documentation de l'endpoint API `/posts/ideas`.
- Documentation du service IA externe/MCP utilisé pour la génération d'idées.

## Open Questions

- Quel service IA externe ou MCP sera utilisé pour la génération d'idées ?
- Comment structurer la réponse de l'IA pour qu'elle propose des idées sous différents angles/formats ?
- Faut-il stocker les idées générées pour l'utilisateur ? (Hors scope MVP, mais à considérer).

---

_Last Updated: 22/05/2025_
