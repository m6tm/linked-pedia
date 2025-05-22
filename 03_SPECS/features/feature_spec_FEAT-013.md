# Feature Specification: Gestion des Sous-Comptes

## Overview

- **Feature ID:** FEAT-013
- **Priority:** Medium
- **Estimated Effort:** À déterminer (Estimation initiale : 3-4 jours, dépend de la complexité de la gestion des permissions via l'API LinkedIn et RLS)
- **Status:** Planned

## Business Context

### Objective

Permettre aux utilisateurs (spécifiquement les ghostwriters) de lier et de gérer jusqu'à 3 profils LinkedIn distincts sous leur compte Linked-Pedia principal. L'objectif est de fournir une solution centralisée et efficace pour les professionnels gérant le contenu LinkedIn pour plusieurs clients.

### User Stories

- As a ghostwriter, I want to manage multiple LinkedIn profiles so that I can handle content for my clients efficiently. (Should Have)
- As a ghostwriter, I want to link a client's LinkedIn account to my Linked-Pedia account. (Should Have)
- As a ghostwriter, I want to switch between managing different client accounts easily. (Should Have)
- As a ghostwriter, I want to see content and analytics specific to the client account I am currently managing. (Should Have)
- As a ghostwriter, I want to unlink a client's account. (Should Have)

### Success Criteria

- Un utilisateur (avec un plan payant approprié si applicable) peut accéder à la fonctionnalité de gestion des sous-comptes.
- L'utilisateur peut initier le processus de liaison d'un sous-compte LinkedIn (similaire à FEAT-002).
- L'application gère la liaison de plusieurs comptes LinkedIn sous un même utilisateur principal.
- L'application applique la limite de 3 sous-comptes par utilisateur principal.
- L'utilisateur peut sélectionner le compte LinkedIn actif qu'il souhaite gérer.
- Lorsque l'utilisateur gère un sous-compte, toutes les fonctionnalités (génération, optimisation, tableau de bord, statistiques, audit) opèrent sur ce sous-compte.
- L'application gère la déconnexion d'un sous-compte.

## Design and UX/UI Principles for this Feature

- **Main Objective:** L'interface doit être flexible et efficace pour les utilisateurs gérant plusieurs comptes. Le passage d'un compte à l'autre doit être rapide et clair. L'objectif "Silicon Valley / Y Combinator" implique ici de fournir une expérience utilisateur fluide pour la gestion multi-comptes, en évitant la confusion sur le compte actuellement géré.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** Dédié une section claire dans les paramètres ou le profil utilisateur pour la gestion des sous-comptes. Utiliser une liste pour afficher les comptes liés (principal et sous-comptes). Fournir un bouton "Ajouter un sous-compte" clair. Implémenter un sélecteur de compte actif bien visible dans l'interface (ex: en-tête, barre latérale). Utiliser des indicateurs visuels clairs pour montrer quel compte est actuellement actif. Le processus de liaison d'un sous-compte doit être similaire à la liaison du compte principal (FEAT-002).

## Functional Requirements

### Core Functionality

1.  Fournir une interface pour visualiser les comptes LinkedIn liés (principal et sous-comptes).
2.  Permettre d'initier le processus de liaison d'un nouveau sous-compte LinkedIn (réutilisation de la logique de FEAT-002, mais associant le compte lié à l'utilisateur principal en tant que sous-compte).
3.  Appliquer la limite de 3 sous-comptes par utilisateur principal (Business Rule 1).
4.  Stocker les informations des sous-comptes liés dans la base de données (table `linkedin_accounts` avec lien vers `sub_accounts`).
5.  Permettre à l'utilisateur de sélectionner le sous-compte actif qu'il souhaite gérer.
6.  Stocker l'ID du sous-compte actif dans la session utilisateur ou l'état frontend/backend.
7.  Adapter toutes les requêtes backend (récupération/création/modification de posts, audit, statistiques) pour opérer sur le sous-compte actif si un est sélectionné, sinon sur le compte principal.
8.  Permettre de déconnecter un sous-compte (supprimer la liaison et les données associées si nécessaire).

### User Interactions

1.  L'utilisateur navigue vers la section "Gestion des Sous-Comptes".
2.  L'application affiche la liste des comptes LinkedIn déjà liés.
3.  L'utilisateur clique sur "Ajouter un sous-compte".
4.  L'application initie le flux de liaison LinkedIn pour un nouveau compte.
5.  Après liaison réussie, le nouveau sous-compte apparaît dans la liste.
6.  L'utilisateur utilise un sélecteur (ex: menu déroulant dans l'en-tête) pour choisir le compte actif.
7.  L'interface se met à jour pour afficher les données (posts, audit, statistiques) du compte actif.
8.  L'utilisateur clique sur une option pour déconnecter un sous-compte.
9.  L'application demande confirmation et supprime le sous-compte.

### Business Rules

1.  Un utilisateur principal (payant) est limité à 3 sous-comptes LinkedIn liés.
2.  Chaque sous-compte doit être lié à un profil LinkedIn distinct.
3.  Les permissions d'accès aux données (posts, audits, statistiques) doivent être gérées de manière à ce que l'utilisateur principal puisse accéder aux données de ses sous-comptes, mais pas aux données d'autres utilisateurs.
4.  Les actions de création/publication/programmation doivent s'appliquer au compte LinkedIn actif.

## Technical Specifications

### Affected Components

- **Frontend:** Page/section "Gestion des Sous-Comptes", liste des comptes liés, bouton "Ajouter un sous-compte", sélecteur de compte actif (composant global), adaptation des composants affichant les données (Tableau de Bord, Audit, Statistiques) pour utiliser le compte actif.
- **Backend:** Supabase Edge Function pour gérer la création d'un sous-compte (lier un `linkedin_account` à un `user` en tant que sous-compte) (`POST /user/subaccount`), Edge Function pour lister les sous-comptes (`GET /user/subaccounts`), Edge Function pour déconnecter un sous-compte (`DELETE /user/subaccount/{id}`). Modification des Edge Functions existantes (posts, analytics) pour prendre en compte l'ID du compte actif.
- **Database:** Nouvelle table `public.sub_accounts`, modification de la table `public.linkedin_accounts` pour inclure une clé étrangère vers `sub_accounts`.
- **External Services:** API officielle de LinkedIn (pour la liaison de chaque sous-compte).

### API Changes

#### New Endpoints

- **Endpoint:** `POST /user/subaccount`
  - Purpose: Créer un enregistrement de sous-compte et initier la liaison LinkedIn pour ce sous-compte.
  - Request: `{}` (le processus de liaison est similaire à FEAT-002, potentiellement initié par cet endpoint).
  - Response: `{ "subaccount": { ... }, "redirect_url": "string" }` ou `{ "error": { ... } }`.
- **Endpoint:** `GET /user/subaccounts`
  - Purpose: Lister les sous-comptes liés à l'utilisateur principal.
  - Request: `{}`
  - Response: `{ "subaccounts": [{ ... }] }` ou `{ "error": { ... } }`.
- **Endpoint:** `DELETE /user/subaccount/{id}`
  - Purpose: Déconnecter et supprimer un sous-compte.
  - Request: Aucun (ID du sous-compte dans l'URL).
  - Response: `{ "success": boolean }` ou `{ "error": { ... } }`.

#### Modified Endpoints

- **Endpoints de gestion de posts, audit, statistiques :** Modifier les Edge Functions existantes (ex: `GET /posts`, `POST /posts`, `GET /analytics/profile/audit`, `GET /analytics/posts`) pour accepter un paramètre optionnel `linkedin_account_id` (ou le récupérer de la session/état) et filtrer/opérer sur les données de ce compte.

### Database Changes

#### New Tables/Collections

- **Table:** `public.sub_accounts`
  - Purpose: Représenter les sous-comptes gérés par un utilisateur principal.
  - Fields:
    - `id`: `uuid` (Clé primaire)
    - `user_id`: `uuid` (Clé étrangère vers `public.users.id`, l'utilisateur principal)
    - `name`: `text` (Nom donné au sous-compte par l'utilisateur principal)
    - `created_at`: `timestamp with time zone`
    - `updated_at`: `timestamp with time zone`

#### Modified Tables/Collections

- **Table:** `public.linkedin_accounts`
  - Ajouter un champ `sub_account_id`: `uuid` (Clé étrangère optionnelle vers `public.sub_accounts.id`).
  - S'assurer que les politiques RLS permettent à l'utilisateur principal d'accéder aux enregistrements `linkedin_accounts` liés à ses `sub_accounts`.
- **Table:** `public.posts`, `public.profile_audits`, `public.post_analytics`
  - S'assurer que ces tables sont liées à `public.linkedin_accounts` (via `linkedin_account_id`) et que les politiques RLS permettent l'accès via l'utilisateur principal ou le sous-compte actif.

### UI Changes

#### New Screens/Components

- **Screen:** Page "Gestion des Sous-Comptes" (`/settings/subaccounts` ou similaire)
  - Description: Page listant les sous-comptes liés et permettant d'en ajouter/supprimer.
  - Wireframe/Mockup (Objectif YC Standard): Liste claire des sous-comptes avec leur nom et le profil LinkedIn associé. Bouton "Ajouter un sous-compte". Boutons d'action (déconnecter) pour chaque sous-compte.
- **Component:** Sélecteur de Compte Actif
  - Description: Composant (ex: menu déroulant dans l'en-tête) permettant de choisir le compte LinkedIn actif (principal ou sous-compte).
  - Wireframe/Mockup (Objectif YC Standard): Un menu déroulant affichant le nom et potentiellement la photo de profil du compte actif, avec une liste des autres comptes disponibles pour sélection.
- **Component:** Élément de Liste de Sous-Compte
  - Description: Affiche les informations d'un sous-compte lié dans la liste.

#### Modified Screens/Components

- **Navigation :** Intégrer le Sélecteur de Compte Actif dans l'en-tête ou la barre latérale.
- **Toutes les pages affichant des données spécifiques à un compte LinkedIn (Tableau de Bord, Créer un Post, Audit, Statistiques) :** Adapter ces pages pour afficher les données du compte actif sélectionné.

## Implementation Plan

### Dependencies

- Supabase SDK.
- API LinkedIn (endpoints OAuth, profil).
- Tables `public.users`, `public.linkedin_accounts`, `public.posts`, `public.profile_audits`, `public.post_analytics`.
- Logique de liaison de compte LinkedIn (FEAT-002).

### Implementation Steps

1.  Créer la table `public.sub_accounts` avec les politiques RLS appropriées.
2.  Modifier la table `public.linkedin_accounts` pour ajouter le champ `sub_account_id` et mettre à jour les politiques RLS.
3.  Créer l'Edge Function `POST /user/subaccount` pour gérer la création d'un sous-compte et initier le flux de liaison (réutilisant la logique de FEAT-002).
4.  Créer l'Edge Function `GET /user/subaccounts` pour lister les sous-comptes de l'utilisateur principal.
5.  Créer l'Edge Function `DELETE /user/subaccount/{id}` pour déconnecter un sous-compte.
6.  Créer la page frontend "Gestion des Sous-Comptes".
7.  Implémenter l'affichage de la liste des sous-comptes et les actions (ajouter, déconnecter).
8.  Créer le composant frontend Sélecteur de Compte Actif.
9.  Implémenter la logique de sélection du compte actif (stockage dans l'état frontend/session).
10. Modifier les Edge Functions existantes (posts, analytics) pour filtrer/opérer sur le compte actif.
11. Modifier les composants frontend affichant les données pour utiliser les données du compte actif.
12. Implémenter la logique de limite de 3 sous-comptes.

### Technical Considerations

- Gestion complexe des permissions via RLS pour permettre à l'utilisateur principal d'accéder aux données de ses sous-comptes tout en protégeant les données des autres utilisateurs.
- Assurer que toutes les Edge Functions qui opèrent sur des données liées à un compte LinkedIn prennent en compte l'ID du compte actif.
- Le processus de liaison d'un sous-compte est un flux OAuth distinct pour chaque compte LinkedIn.
- Gestion de l'état du compte actif côté frontend et backend.

## Testing Requirements

### Unit Tests

- Tests unitaires pour les Edge Functions `POST /user/subaccount`, `GET /user/subaccounts`, `DELETE /user/subaccount/{id}` (mockant la base de données et l'API LinkedIn).
- Tests unitaires pour la logique de limite de sous-comptes.

### Integration Tests

- Tester le flux complet d'ajout d'un sous-compte (initiation UI -> liaison LinkedIn (mockée) -> création sous-compte DB -> affichage dans la liste).
- Tester l'ajout d'un 4ème sous-compte (doit échouer).
- Tester la sélection d'un sous-compte actif et vérifier que les données affichées correspondent à ce compte.
- Tester la déconnexion d'un sous-compte.
- Tester que l'utilisateur principal ne peut pas accéder aux données d'un sous-compte qui ne lui appartient pas.

### User Acceptance Tests

- Je suis connecté à Linked-Pedia.
- Je navigue vers la section "Gestion des Sous-Comptes".
- Je clique sur "Ajouter un sous-compte".
- Je lie un nouveau compte LinkedIn via le flux d'autorisation.
- Le nouveau sous-compte apparaît dans ma liste de sous-comptes.
- Je peux lier jusqu'à 3 sous-comptes.
- Je peux utiliser le sélecteur de compte pour choisir le compte actif (principal ou sous-compte).
- Lorsque je change de compte actif, les posts, audits et statistiques affichés correspondent au compte sélectionné.
- Je peux déconnecter un sous-compte.

## Acceptance Criteria

```gherkin
Feature: Gestion des Sous-Comptes

  Scenario: Ajout réussi d'un sous-compte
    Given je suis connecté à Linked-Pedia
    And j'ai moins de 3 sous-comptes liés
    When je navigue vers la section "Gestion des Sous-Comptes"
    And je clique sur le bouton "Ajouter un sous-compte"
    And je lie un nouveau compte LinkedIn via le flux d'autorisation
    Then le nouveau compte LinkedIn est ajouté en tant que sous-compte
    And le sous-compte apparaît dans ma liste de sous-comptes

  Scenario: Limite de sous-comptes appliquée
    Given je suis connecté à Linked-Pedia
    And j'ai déjà 3 sous-comptes liés
    When je navigue vers la section "Gestion des Sous-Comptes"
    And je tente d'ajouter un nouveau sous-compte
    Then l'application m'empêche d'ajouter le sous-compte
    And affiche un message indiquant que la limite de 3 sous-comptes est atteinte

  Scenario: Sélection d'un sous-compte actif
    Given je suis connecté à Linked-Pedia
    And j'ai plusieurs comptes LinkedIn liés (principal et sous-comptes)
    When j'utilise le sélecteur de compte actif pour choisir un sous-compte
    Then le sous-compte sélectionné devient le compte actif
    And toutes les données affichées (posts, audit, statistiques) correspondent à ce sous-compte

  Scenario: Déconnexion d'un sous-compte
    Given je suis connecté à Linked-Pedia
    And j'ai un sous-compte lié
    When je navigue vers la section "Gestion des Sous-Comptes"
    And je clique sur l'option de déconnexion pour ce sous-compte
    And je confirme la déconnexion
    Then le sous-compte est déconnecté
    And il n'apparaît plus dans ma liste de sous-comptes
```

## Security Considerations

- Implémenter des politiques RLS très strictes pour garantir que les utilisateurs ne peuvent accéder qu'à leurs propres sous-comptes et aux données associées.
- Sécuriser les Edge Functions liées à la gestion des sous-comptes.
- Gérer les tokens LinkedIn de chaque sous-compte de manière sécurisée et isolée.

## Performance Considerations

- La récupération des données (posts, audits, statistiques) doit être rapide même lors du filtrage par sous-compte actif. Assurer des index appropriés sur les IDs de compte LinkedIn dans les tables de données.

## Accessibility Requirements (Integral Part of Quality UX)

- La page de gestion des sous-comptes est navigable au clavier.
- La liste des sous-comptes et les actions associées sont accessibles.
- Le sélecteur de compte actif est accessible et son état actuel est clairement annoncé par les lecteurs d'écran.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

Aucune exigence spécifique pour le MVP. Les chaînes de caractères seront codées en dur en français.

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour les Edge Functions liées à la gestion des sous-comptes et les composants frontend associés.
- Documentation des endpoints API liés aux sous-comptes.
- Mise à jour du schéma de base de données pour inclure la table `sub_accounts` et les modifications sur `linkedin_accounts`.
- Documentation détaillée des politiques RLS pour la gestion multi-comptes.

## Open Questions

- Comment gérer les permissions spécifiques si l'API LinkedIn a des limitations sur les actions possibles via un sous-compte lié par OAuth ?
- Faut-il permettre à l'utilisateur principal de donner des noms personnalisés à ses sous-comptes ? (Oui, inclus dans la spécification).
- Comment gérer la suppression d'un utilisateur principal qui a des sous-comptes liés ? (Supprimer tous les sous-comptes et leurs données associées).

---

_Last Updated: 22/05/2025_
