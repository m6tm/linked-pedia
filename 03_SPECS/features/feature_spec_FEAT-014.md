# Feature Specification: Mode Assistant

## Overview

- **Feature ID:** FEAT-014
- **Priority:** Medium
- **Estimated Effort:** À déterminer (Estimation initiale : 2-3 jours, dépend de la complexité du guidage)
- **Status:** Planned

## Business Context

### Objective

Guider pas à pas les nouveaux utilisateurs ou les débutants dans l'utilisation des fonctionnalités clés de Link-Pedia et l'optimisation de leur présence LinkedIn. L'objectif est de faciliter la prise en main de l'application et d'aider les utilisateurs à obtenir rapidement de la valeur.

### User Stories

- As a new user, I want a step-by-step guide so that I can easily learn how to use the application. (Should Have)
- As a beginner, I want guidance on how to optimize my LinkedIn profile and create my first post. (Should Have)
- As a user, I want to be able to exit the assistant mode if I feel comfortable using the application. (Should Have)

### Success Criteria

- Un nouvel utilisateur se voit proposer d'activer le mode assistant.
- Le mode assistant guide l'utilisateur à travers les étapes clés (ex: lier compte LinkedIn, lancer audit, générer premier post).
- Le guidage est clair et facile à suivre.
- L'utilisateur peut désactiver le mode assistant à tout moment.
- L'application gère la progression de l'utilisateur dans le mode assistant.

## Design and UX/UI Principles for this Feature

- **Main Objective:** L'interface doit être pédagogique et rassurante. Le mode assistant doit être perçu comme une aide utile, pas comme une contrainte. L'objectif "Silicon Valley / Y Combinator" implique ici de fournir une expérience d'onboarding fluide et efficace, en utilisant des éléments UI non intrusifs et un langage clair.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_design_conventions.md:1)).
- **Specifics for this feature:** Utiliser des éléments de guidage visuels (ex: surbrillance d'éléments, flèches) et des bulles d'information ou des panneaux latéraux pour expliquer les étapes. Le mode assistant peut être une superposition ou un panneau persistant. Utiliser un langage simple et encourageant. Fournir une option claire pour "Passer l'assistant" ou "Désactiver l'assistant".

## Functional Requirements

### Core Functionality

1.  Détecter si un utilisateur est nouveau ou a besoin d'assistance (basé sur l'état de son profil, s'il a lié son compte LinkedIn, créé des posts, etc.).
2.  Proposer d'activer le mode assistant aux utilisateurs identifiés comme débutants.
3.  Implémenter une séquence d'étapes de guidage couvrant les fonctionnalités clés (ex: liaison compte LinkedIn, audit de profil, génération/publication de post).
4.  Afficher des éléments UI de guidage (bulles d'aide, surbrillance) pour chaque étape.
5.  Suivre la progression de l'utilisateur dans le mode assistant (stocker l'étape actuelle).
6.  Permettre à l'utilisateur de marquer une étape comme terminée ou de passer à l'étape suivante.
7.  Permettre à l'utilisateur de désactiver complètement le mode assistant.
8.  Gérer la réactivation du mode assistant si l'utilisateur le souhaite.

### User Interactions

1.  Un nouvel utilisateur se connecte pour la première fois.
2.  Une invitation à activer le mode assistant apparaît.
3.  L'utilisateur accepte ou refuse.
4.  Si accepté, le mode assistant commence, guidant l'utilisateur à travers la première étape (ex: lier compte LinkedIn).
5.  L'utilisateur suit les instructions et interagit avec l'interface guidée.
6.  Une fois l'étape terminée, l'assistant passe à l'étape suivante.
7.  L'utilisateur clique sur une option "Désactiver l'assistant".
8.  Le mode assistant se ferme.

### Business Rules

1.  Le mode assistant est proposé par défaut aux nouveaux utilisateurs.
2.  La progression de l'assistant doit être sauvegardée pour que l'utilisateur puisse reprendre là où il s'est arrêté.
3.  Le mode assistant ne doit pas bloquer l'utilisation normale de l'application.

## Technical Specifications

### Affected Components

- **Frontend:** Composants de guidage (bulles d'aide, surbrillance), logique d'affichage des étapes, bouton "Désactiver l'assistant". Intégration de la logique de l'assistant dans les pages clés (Tableau de Bord, Liaison Compte, Audit, Créer un Post).
- **Backend:** Potentiellement, un champ dans la table `public.users` pour stocker l'étape actuelle de l'assistant ou un statut indiquant si l'assistant a été complété/désactivé.
- **Database:** Table `public.users` (potentiellement).
- **External Services:** Aucun service externe direct, mais le guidage concerne l'utilisation des fonctionnalités qui appellent des services externes.

### API Changes

#### New Endpoints

Aucun endpoint API backend spécifique pour le mode assistant lui-même, car il s'agit principalement d'une fonctionnalité frontend.

#### Modified Endpoints

Aucun.

### Database Changes

#### New Tables/Collections

Aucun pour le MVP.

#### Modified Tables/Collections

- **Table:** `public.users`
  - Ajouter un champ `assistant_status`: `jsonb` (Stocke l'état de l'assistant pour l'utilisateur, ex: `{ "enabled": true, "current_step": "link_linkedin" }`).

### UI Changes

#### New Screens/Components

- **Component:** Composant de Guidage (Bulle d'aide, Surbrillance)
  - Description: Composants visuels pour attirer l'attention sur des éléments spécifiques de l'interface et afficher des instructions.
  - Wireframe/Mockup (Objectif YC Standard): Bulles d'information stylisées pointant vers des éléments UI, avec un texte explicatif et des boutons "Suivant" / "Passer". Surbrillance subtile autour de l'élément ciblé.
- **Component:** Panneau/Modale d'Invitation à l'Assistant
  - Description: Interface affichée aux nouveaux utilisateurs pour leur proposer le mode assistant.
- **Component:** Bouton/Option "Désactiver l'Assistant"
  - Description: Élément UI pour quitter le mode assistant.

#### Modified Screens/Components

- **Toutes les pages clés (Tableau de Bord, Liaison Compte, Audit, Créer un Post) :** Intégrer la logique d'affichage des éléments de guidage de l'assistant sur ces pages.

## Implementation Plan

### Dependencies

- Table `public.users`.
- Fonctionnalités clés à guider (FEAT-002, FEAT-003, FEAT-004, etc.).

### Implementation Steps

1.  Ajouter le champ `assistant_status` à la table `public.users` et mettre à jour les politiques RLS.
2.  Implémenter la logique frontend pour détecter si l'utilisateur est nouveau ou si l'assistant est activé.
3.  Créer le composant d'invitation à l'assistant.
4.  Définir la séquence des étapes de guidage et le contenu textuel de chaque étape.
5.  Créer les composants de guidage (bulles, surbrillance).
6.  Implémenter la logique d'affichage des composants de guidage sur les pages clés en fonction de l'étape actuelle de l'utilisateur.
7.  Implémenter la logique de progression (mise à jour de l'étape actuelle dans la DB).
8.  Implémenter l'option de désactivation de l'assistant (mise à jour du statut dans la DB).
9.  Gérer la réactivation de l'assistant.

### Technical Considerations

- Stocker l'état de l'assistant de manière persistante en base de données.
- Assurer que le mode assistant ne crée pas de conflits avec les interactions normales de l'utilisateur.
- La logique de détection des utilisateurs "débutants" peut évoluer (ex: basée sur le nombre de posts publiés, l'utilisation des fonctionnalités).

## Testing Requirements

### Unit Tests

- Tests unitaires pour la logique frontend d'affichage des étapes et de gestion de la progression.
- Tests unitaires pour les fonctions de mise à jour du statut de l'assistant en base de données.

### Integration Tests

- Tester le flux complet : Nouvel utilisateur -> Invitation assistant -> Parcours des premières étapes -> Désactivation -> Réactivation.
- Tester que les éléments de guidage apparaissent correctement sur les pages prévues.
- Tester que la progression est sauvegardée entre les sessions.

### User Acceptance Tests

- En tant que nouvel utilisateur, je vois une invitation à utiliser le mode assistant.
- J'accepte et l'assistant me guide pas à pas.
- Le guidage est clair et m'aide à comprendre comment utiliser l'application.
- Je peux marquer une étape comme terminée ou passer à la suivante.
- Je peux désactiver l'assistant à tout moment.
- Si je me déconnecte et me reconnecte, l'assistant reprend là où je l'ai laissé (si je ne l'ai pas désactivé).

## Acceptance Criteria

```gherkin
Feature: Mode Assistant

  Scenario: Invitation et activation du mode assistant pour un nouvel utilisateur
    Given je suis un nouvel utilisateur
    When je me connecte à Link-Pedia pour la première fois
    Then l'application affiche une invitation à activer le mode assistant
    When j'accepte l'invitation
    Then le mode assistant est activé
    And l'application commence à me guider à travers la première étape

  Scenario: Progression dans les étapes du mode assistant
    Given je suis en mode assistant
    And je suis à une étape spécifique du guidage
    When je complète l'action requise pour l'étape actuelle
    Or je clique sur le bouton "Suivant" ou "Marquer comme terminé"
    Then l'application enregistre ma progression
    And l'assistant me guide vers l'étape suivante

  Scenario: Désactivation du mode assistant
    Given je suis en mode assistant
    When je clique sur l'option "Désactiver l'assistant"
    And je confirme la désactivation (si nécessaire)
    Then le mode assistant est désactivé
    And les éléments de guidage ne sont plus affichés

  Scenario: Reprise du mode assistant après déconnexion/reconnexion
    Given je suis en mode assistant
    And j'ai complété certaines étapes mais pas toutes
    And je me déconnecte
    When je me reconnecte
    Then l'application me propose de reprendre le mode assistant
    And si j'accepte, l'assistant reprend à l'étape où je l'avais laissé
```

## Security Considerations

- S'assurer que l'état de l'assistant est lié à l'utilisateur authentifié et ne peut pas être manipulé par d'autres utilisateurs.

## Performance Considerations

- Le mode assistant ne devrait pas impacter significativement la performance de l'application. L'affichage des éléments de guidage doit être rapide.

## Accessibility Requirements (Integral Part of Quality UX)

- Les éléments de guidage (bulles, surbrillance) sont accessibles et correctement annoncés par les lecteurs d'écran.
- Les boutons de navigation de l'assistant ("Suivant", "Passer", "Désactiver") sont accessibles au clavier.
- Le contenu textuel des bulles d'aide est lisible et a un contraste suffisant.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

- Localiser le contenu textuel du mode assistant (si l'internationalisation est implémentée).

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour les composants frontend du mode assistant et la logique associée.
- Documentation de la structure du champ `assistant_status` dans la table `public.users`.
- Documentation de la séquence des étapes de guidage et de leur contenu.

## Open Questions

- Comment définir précisément les critères pour identifier un utilisateur comme "débutant" et lui proposer l'assistant ?
- Faut-il permettre de réinitialiser la progression de l'assistant ?
- Le contenu du guidage sera-t-il statique ou pourra-t-il être mis à jour dynamiquement ?

---

_Last Updated: 22/05/2025_
