# Feature Specification: Gestion du Profil Utilisateur

## Overview

- **Feature ID:** FEAT-015
- **Priority:** High
- **Estimated Effort:** À déterminer (Estimation initiale : 2-3 jours)
- **Status:** Planned

## Business Context

### Objective

Permettre à l'utilisateur de renseigner des informations sur lui-même, son activité, ses préférences et ses objectifs LinkedIn. Ces informations seront utilisées par l'IA pour personnaliser les suggestions de contenu, les conseils et l'audit de profil. L'objectif est de rendre l'expérience Linked-Pedia plus pertinente et adaptée aux besoins individuels de l'utilisateur.

### User Stories

- As a user, I want to provide information about myself and my goals so that the AI suggestions are tailored to me. (Must Have)
- As a user, I want to specify my activity domain so that the content ideas are relevant. (Must Have)
- As a user, I want to set my LinkedIn objectives (e.g., personal branding, lead generation) so that the advice aligns with them. (Must Have)
- As a user, I want to update my profile information as needed. (Must Have)

### Success Criteria

- L'utilisateur peut accéder à une section de gestion de profil.
- L'utilisateur peut saisir des informations sur son activité, ses préférences et ses objectifs.
- Les informations saisies sont sauvegardées dans la base de données.
- L'utilisateur peut modifier les informations de son profil.
- Les informations du profil utilisateur sont utilisées par les fonctionnalités IA (génération, conseils, audit) pour personnaliser les résultats.

## Design and UX/UI Principles for this Feature

- **Main Objective:** L'interface doit être personnalisée et pertinente. Le formulaire de profil doit être simple et guider l'utilisateur sur les informations utiles à fournir. L'objectif "Silicon Valley / Y Combinator" implique ici de rendre la collecte d'informations utilisateur non fastidieuse et de souligner la valeur de ces données pour une expérience personnalisée.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** Créer une page dédiée ou une section dans les paramètres pour la gestion du profil. Utiliser des champs de formulaire clairs pour chaque type d'information (activité, préférences, objectifs). Expliquer brièvement pourquoi chaque information est demandée et comment elle sera utilisée par l'IA. Utiliser des composants `Input`, `Select`, `Checkbox` stylisés selon le Design System. Bouton "Sauvegarder" clair.

## Functional Requirements

### Core Functionality

1.  Fournir une interface pour afficher et modifier les informations du profil utilisateur (activité, préférences, objectifs).
2.  Récupérer les informations du profil utilisateur depuis la table `public.users` en base de données.
3.  Permettre à l'utilisateur de saisir ou sélectionner des valeurs pour les champs du profil.
4.  Valider les données saisies côté frontend et backend.
5.  Sauvegarder les informations mises à jour dans les champs `preferences`, `objectives`, `activity_info` de la table `public.users`.
6.  Rendre ces informations accessibles aux Edge Functions qui appellent les services IA (génération, conseils) et à la logique d'audit.

### User Interactions

1.  L'utilisateur navigue vers la section "Mon Profil" ou "Paramètres du Compte".
2.  L'application affiche ses informations de profil actuelles.
3.  L'utilisateur modifie les champs.
4.  L'utilisateur clique sur un bouton "Sauvegarder".
5.  L'application affiche un indicateur de chargement.
6.  L'application confirme la sauvegarde.

### Business Rules

1.  Les informations du profil utilisateur sont facultatives, mais leur fourniture améliore la personnalisation de l'IA.
2.  Les données stockées dans les champs `preferences`, `objectives`, `activity_info` doivent être structurées (ex: JSON) pour être facilement utilisables par l'IA.
3.  Les informations du profil sont liées à l'utilisateur connecté.

## Technical Specifications

### Affected Components

- **Frontend:** Page/section "Mon Profil", formulaire de profil, champs de saisie/sélection, bouton "Sauvegarder", indicateur de chargement.
- **Backend:** Supabase Edge Function pour récupérer les informations du profil (`GET /user/profile`), Edge Function pour mettre à jour les informations du profil (`PUT /user/profile`). Modification des Edge Functions liées à l'IA (génération, conseils) et à l'audit pour récupérer et utiliser ces informations.
- **Database:** Table `public.users` (lecture, écriture sur les champs `preferences`, `objectives`, `activity_info`).
- **External Services:** Aucun service externe direct, mais les informations sont utilisées par les services IA.

### API Changes

#### New Endpoints

- **Endpoint:** `GET /user/profile`
  - Purpose: Récupérer les informations du profil de l'utilisateur connecté.
  - Request: `{}`
  - Response: `{ "profile": { "activity_info": { ... }, "preferences": { ... }, "objectives": { ... } } }` ou `{ "error": { ... } }`.
- **Endpoint:** `PUT /user/profile`
  - Purpose: Mettre à jour les informations du profil de l'utilisateur connecté.
  - Request: `{ "activity_info": { ... }, "preferences": { ... }, "objectives": { ... } }` (envoyer les champs à mettre à jour).
  - Response: `{ "success": boolean, "profile": { ... } }` ou `{ "error": { ... } }`.

#### Modified Endpoints

- **Endpoints liés à l'IA et à l'audit :** Modifier les Edge Functions (ex: `/posts/generate/text`, `/analytics/tips`, `/analytics/profile/audit`) pour récupérer les informations du profil utilisateur (`activity_info`, `preferences`, `objectives`) depuis la DB et les inclure dans les prompts envoyés aux services IA ou dans la logique d'analyse.

### Database Changes

#### New Tables/Collections

Aucun.

#### Modified Tables/Collections

- **Table:** `public.users`
  - S'assurer que les champs `activity_info`, `preferences`, `objectives` existent et sont de type `jsonb`.
  - S'assurer que les politiques RLS permettent à l'utilisateur de lire et écrire sur ses propres champs de profil.

### UI Changes

#### New Screens/Components

- **Screen:** Page "Mon Profil" ou "Paramètres du Compte" (`/profile` ou `/settings`)
  - Description: Page contenant le formulaire pour gérer les informations du profil utilisateur.
  - Wireframe/Mockup (Objectif YC Standard): Formulaire clair et bien organisé avec des sections pour l'activité, les préférences et les objectifs. Utiliser des composants de formulaire standard stylisés selon le Design System. Inclure des textes d'aide expliquant l'utilité de chaque champ. Bouton "Sauvegarder" en bas.
- **Component:** Formulaire de Profil Utilisateur
  - Description: Composant pour afficher et permettre la modification des informations du profil.

#### Modified Screens/Components

- **Navigation :** Ajouter un lien vers la page de profil/paramètres dans la navigation principale.

## Implementation Plan

### Dependencies

- Supabase SDK.
- Table `public.users`.
- Fonctionnalités IA et Audit qui utiliseront ces informations.

### Implementation Steps

1.  S'assurer que les champs `activity_info`, `preferences`, `objectives` existent dans la table `public.users` et mettre à jour les politiques RLS.
2.  Créer l'Edge Function `GET /user/profile`.
3.  Créer l'Edge Function `PUT /user/profile`.
4.  Créer la page frontend "Mon Profil".
5.  Implémenter l'appel à l'Edge Function `GET /user/profile` au chargement de la page et afficher les données dans le formulaire.
6.  Implémenter la logique de modification des champs du formulaire.
7.  Implémenter l'appel à l'Edge Function `PUT /user/profile` lors de la soumission du formulaire de sauvegarde.
8.  Gérer les états de chargement et d'erreur côté frontend.
9.  Modifier les Edge Functions liées à l'IA et à l'audit pour récupérer et utiliser les informations du profil utilisateur.

### Technical Considerations

- Définir la structure exacte des données stockées dans les champs `jsonb` (`activity_info`, `preferences`, `objectives`) pour qu'elles soient facilement utilisables par l'IA.
- Assurer que les politiques RLS sont correctement configurées pour protéger ces données.
- Comment les informations du profil seront-elles intégrées dans les prompts IA ou les algorithmes d'analyse ? (Nécessite une coordination avec l'implémentation des fonctionnalités IA/Audit).

## Testing Requirements

### Unit Tests

- Tests unitaires pour les Edge Functions `GET /user/profile` et `PUT /user/profile` (mockant la base de données).
- Tests unitaires pour la logique frontend du formulaire de profil (validation, gestion des états).

### Integration Tests

- Tester le flux complet : Chargement page profil -> Affichage données -> Modification données -> Sauvegarde -> Vérification en base de données.
- Tester que les informations du profil sont correctement utilisées par les fonctionnalités IA (tester les fonctionnalités IA avec différents profils utilisateur mockés).

### User Acceptance Tests

- Je peux naviguer vers la section "Mon Profil".
- Je vois mes informations de profil actuelles (si déjà saisies).
- Je peux saisir ou modifier des informations sur mon activité, mes préférences et mes objectifs.
- Je clique sur "Sauvegarder" et mes informations sont enregistrées.
- Lorsque j'utilise les générateurs de posts ou que je consulte les conseils, les suggestions semblent tenir compte des informations de mon profil.

## Acceptance Criteria

```gherkin
Feature: Gestion du Profil Utilisateur

  Scenario: Affichage et modification réussis des informations de profil
    Given je suis connecté à Linked-Pedia
    When je navigue vers la section "Mon Profil"
    Then l'application affiche mes informations de profil actuelles
    When je modifie une ou plusieurs informations
    And je clique sur le bouton "Sauvegarder"
    Then l'application affiche un indicateur de chargement
    And les informations mises à jour sont sauvegardées dans mon profil utilisateur
    And l'application confirme la sauvegarde

  Scenario: Utilisation des informations de profil par l'IA
    Given je suis connecté à Linked-Pedia
    And j'ai renseigné des informations sur mon activité, mes préférences et mes objectifs dans mon profil
    When j'utilise une fonctionnalité IA (ex: Générateur de Posts, Conseils Personnalisés)
    Then les suggestions ou conseils générés sont personnalisés et pertinents en fonction des informations de mon profil
```

## Security Considerations

- Implémenter des politiques RLS strictes sur la table `public.users` pour garantir que les utilisateurs ne peuvent lire ou écrire que sur leurs propres informations de profil.
- Sécuriser les Edge Functions `GET /user/profile` et `PUT /user/profile`.

## Performance Considerations

- Le chargement et la sauvegarde du profil utilisateur doivent être rapides.
- L'impact sur la performance des fonctionnalités IA utilisant ces informations devrait être minimal.

## Accessibility Requirements (Integral Part of Quality UX)

- La page de profil est navigable au clavier.
- Les champs de formulaire ont des labels associés et sont utilisables avec un lecteur d'écran.
- Les messages de validation et de sauvegarde sont accessibles.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

- Localiser les libellés des champs et les messages d'aide (si l'internationalisation est implémentée).

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour les Edge Functions `GET /user/profile`, `PUT /user/profile` et les composants frontend associés.
- Documentation des endpoints API `/user/profile`.
- Documentation détaillée de la structure des données stockées dans les champs `jsonb` du profil utilisateur.
- Documentation de la manière dont les fonctionnalités IA utilisent ces informations.

## Open Questions

- Quelle structure de données précise utiliser pour les champs `activity_info`, `preferences`, `objectives` afin de maximiser leur utilité pour l'IA ?
- Comment les fonctionnalités IA intégreront-elles ces informations dans leurs prompts ou algorithmes ?
- Faut-il ajouter d'autres champs au profil utilisateur à l'avenir ?

---

_Last Updated: 22/05/2025_
