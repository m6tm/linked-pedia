# Feature Specification: Liaison Compte LinkedIn

## Overview

- **Feature ID:** FEAT-002
- **Priority:** High
- **Estimated Effort:** À déterminer (Estimation initiale : 3-4 jours, dépend de la complexité de l'API LinkedIn)
- **Status:** Planned

## Business Context

### Objective

Permettre aux utilisateurs de connecter leur profil LinkedIn à leur compte Link-Pedia via le flux d'autorisation OAuth 2.0 de LinkedIn. Cette liaison est essentielle pour permettre à Link-Pedia d'accéder aux données de profil nécessaires à l'audit, de récupérer les statistiques de posts et de publier/programmer du contenu au nom de l'utilisateur.

### User Stories

- As a user, I want to connect my LinkedIn profile so that Link-Pedia can access my data and publish on my behalf. (Must Have)
- As a user, I want to know if my LinkedIn account is successfully linked so that I can use the features that require it. (Must Have)
- As a user, I want to be able to disconnect my LinkedIn account from Link-Pedia. (Should Have)

### Success Criteria

- L'utilisateur peut initier le processus de liaison de compte LinkedIn depuis l'application Link-Pedia.
- L'utilisateur est redirigé vers la page d'autorisation de LinkedIn.
- Après avoir autorisé l'accès sur LinkedIn, l'utilisateur est redirigé vers Link-Pedia.
- Link-Pedia reçoit et traite correctement le code d'autorisation de LinkedIn pour obtenir les tokens d'accès et de rafraîchissement.
- Les tokens sont stockés de manière sécurisée dans la base de données.
- Le statut de liaison du compte LinkedIn de l'utilisateur est mis à jour dans Link-Pedia.
- L'application affiche un message de succès ou d'échec de la liaison.
- L'utilisateur peut voir le statut de son compte LinkedIn lié dans l'application.
- L'utilisateur peut déconnecter son compte LinkedIn depuis l'application, révoquant l'accès.

## Design and UX/UI Principles for this Feature

- **Main Objective:** La conception et l'expérience utilisateur de cette fonctionnalité doivent être fiables et transparentes. Le processus de liaison, bien qu'impliquant une redirection externe, doit sembler intégré et sécurisant pour l'utilisateur. L'objectif est une application de qualité "Silicon Valley / Y Combinator", ce qui implique ici une gestion fluide des redirections et un feedback clair à chaque étape.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** L'interface pour initier la liaison doit être claire (ex: un bouton "Lier mon compte LinkedIn"). Pendant la redirection vers LinkedIn et le retour, l'application doit afficher un état de chargement ou un message indiquant que le processus est en cours. Les messages de succès ou d'échec après la redirection doivent être très visibles et informatifs. L'état de liaison du compte doit être clairement affiché dans le profil utilisateur ou le tableau de bord. Le bouton/lien de déconnexion doit être facile à trouver mais suffisamment protégé contre les clics accidentels (ex: confirmation).

## Functional Requirements

### Core Functionality

1.  Initier le flux d'autorisation OAuth 2.0 de LinkedIn (rediriger l'utilisateur vers l'URL d'autorisation de LinkedIn avec les paramètres corrects : `client_id`, `redirect_uri`, `state`, `scope`).
2.  Gérer la redirection de callback de LinkedIn (`redirect_uri`).
3.  Extraire le code d'autorisation des paramètres de l'URL de callback.
4.  Envoyer une requête POST à l'endpoint de token de LinkedIn pour échanger le code d'autorisation contre des tokens d'accès et de rafraîchissement.
5.  Stocker de manière sécurisée les tokens d'accès et de rafraîchissement LinkedIn dans la base de données, associés à l'utilisateur Link-Pedia.
6.  Mettre à jour le statut de liaison du compte LinkedIn pour l'utilisateur dans la base de données.
7.  Afficher un message de succès ou d'échec de la liaison à l'utilisateur.
8.  Permettre à l'utilisateur de visualiser le statut de liaison de son compte LinkedIn.
9.  Permettre à l'utilisateur de déconnecter son compte LinkedIn (supprimer les tokens stockés et mettre à jour le statut).

### User Interactions

1.  L'utilisateur clique sur un bouton "Lier mon compte LinkedIn".
2.  L'application redirige l'utilisateur vers LinkedIn.
3.  L'utilisateur autorise Link-Pedia sur le site de LinkedIn.
4.  LinkedIn redirige l'utilisateur vers l'URI de callback configurée dans Link-Pedia.
5.  L'application traite la réponse et affiche un message de succès/échec.
6.  L'utilisateur navigue vers une section (ex: Paramètres, Profil) pour voir le statut de liaison.
7.  L'utilisateur clique sur un bouton "Déconnecter mon compte LinkedIn".
8.  L'utilisateur confirme la déconnexion (si confirmation implémentée).
9.  L'application supprime la liaison et affiche un message de succès.

### Business Rules

1.  Un utilisateur Link-Pedia ne peut lier qu'un seul profil LinkedIn principal (bien que les sous-comptes permettent de lier d'autres profils, le mécanisme initial de liaison est pour le compte principal).
2.  Les scopes demandés lors de l'autorisation doivent correspondre aux besoins des fonctionnalités implémentées (publication, lecture profil, statistiques).
3.  Les tokens d'accès et de rafraîchissement doivent être traités comme des données sensibles.

## Technical Specifications

### Affected Components

- **Frontend:** Page/composant pour initier la liaison (ex: dans les paramètres ou l'onboarding), page/route de callback (`/api/auth/callback` ou similaire), affichage du statut de liaison, bouton de déconnexion.
- **Backend:** Supabase Edge Function pour gérer le callback OAuth (`/api/auth/callback`), Edge Function pour gérer la déconnexion, interaction avec l'API LinkedIn, interaction avec la base de données Supabase.
- **Database:** Table `public.users` (pour stocker le statut de liaison), nouvelle table `public.linkedin_accounts` pour stocker les tokens et les informations de base du compte LinkedIn lié.
- **External Services:** API officielle de LinkedIn (endpoints OAuth 2.0).

### API Changes

#### New Endpoints

- **Endpoint:** `POST /auth/linkedin/connect`
  - Purpose: Initier le flux OAuth en générant l'URL de redirection LinkedIn.
  - Request: `{}` (potentiellement avec un paramètre `redirect_url` si différent de la valeur par défaut)
  - Response: `{ "redirect_url": "string" }`
- **Endpoint:** `GET /auth/linkedin/callback` (Géré par une Edge Function)
  - Purpose: Gérer le callback de LinkedIn, échanger le code contre des tokens, sauvegarder les tokens, rediriger l'utilisateur vers le frontend.
  - Request: Paramètres dans l'URL (`code`, `state`).
  - Response: Redirection vers le frontend avec des paramètres indiquant succès/échec.
- **Endpoint:** `POST /auth/linkedin/disconnect`
  - Purpose: Supprimer la liaison du compte LinkedIn.
  - Request: `{}`
  - Response: `{ "success": boolean }`

#### Modified Endpoints

Aucun.

### Database Changes

#### New Tables/Collections

- **Table:** `public.linkedin_accounts`
  - Purpose: Stocker les informations et tokens des comptes LinkedIn liés.
  - Fields:
    - `id`: `uuid` (Clé primaire, liée à `public.users.id` ou `public.sub_accounts.id`)
    - `user_id`: `uuid` (Clé étrangère vers `public.users.id`, l'utilisateur propriétaire)
    - `linkedin_profile_id`: `text` (ID unique du profil LinkedIn, ex: URN)
    - `access_token`: `text` (Token d'accès LinkedIn, chiffré si possible)
    - `refresh_token`: `text` (Token de rafraîchissement LinkedIn, chiffré si possible)
    - `expires_at`: `timestamp with time zone` (Expiration du token d'accès)
    - `refresh_token_expires_at`: `timestamp with time zone` (Expiration du token de rafraîchissement)
    - `scopes`: `text[]` (Scopes accordés)
    - `created_at`: `timestamp with time zone`
    - `updated_at`: `timestamp with time zone`
    - `is_primary`: `boolean` (Indique si c'est le compte principal de l'utilisateur)
    - `sub_account_id`: `uuid` (Clé étrangère optionnelle vers `public.sub_accounts.id` si c'est un sous-compte)

#### Modified Tables/Collections

- **Table:** `public.users`
  - Ajouter un champ `linkedin_account_linked`: `boolean` (Indique si un compte LinkedIn principal est lié).

### UI Changes

#### New Screens/Components

- **Screen:** Page de Callback LinkedIn (`/auth/callback`)
  - Description: Page interne qui gère la redirection depuis LinkedIn. Affiche un indicateur de chargement pendant le traitement et redirige ensuite vers une autre page (ex: tableau de bord ou page de statut de liaison) avec un message de succès/échec.
  - Wireframe/Mockup (Objectif YC Standard): Page minimale avec un spinner et un message "Traitement de la liaison LinkedIn en cours...".
- **Component:** Bouton/Section "Lier Compte LinkedIn"
  - Description: Un élément UI (bouton, carte) qui initie le flux de liaison. Peut afficher le statut actuel (non lié, lié, erreur).
  - Wireframe/Mockup (Objectif YC Standard): Un bouton clair "Lier mon compte LinkedIn" dans une section dédiée des paramètres ou de l'onboarding. Une fois lié, afficher "Compte LinkedIn lié" avec l'image de profil et le nom, et un bouton "Déconnecter".

#### Modified Screens/Components

- **Screen:** Tableau de Bord / Page de Profil / Paramètres
  - Ajouter une section ou un indicateur montrant le statut de liaison du compte LinkedIn.

## Implementation Plan

### Dependencies

- Supabase SDK.
- Librairie de requêtes HTTP côté backend (Edge Function) pour appeler l'API LinkedIn (ex: `node-fetch`).
- Librairie de routage (Next.js App Router).
- Variables d'environnement pour les clés API LinkedIn et l'URI de redirection.

### Implementation Steps

1.  Créer la table `public.linkedin_accounts` dans Supabase avec les politiques RLS appropriées.
2.  Ajouter le champ `linkedin_account_linked` à la table `public.users`.
3.  Créer l'Edge Function `/api/auth/linkedin/connect` pour générer l'URL d'autorisation.
4.  Créer la page frontend pour initier la liaison, appelant l'Edge Function `/connect` et redirigeant l'utilisateur.
5.  Créer l'Edge Function `/api/auth/linkedin/callback` pour gérer la réponse de LinkedIn, échanger le code contre des tokens, sauvegarder les tokens et mettre à jour le statut utilisateur.
6.  Créer la page frontend de callback pour afficher l'état de traitement et gérer la redirection finale.
7.  Implémenter l'affichage du statut de liaison dans l'interface utilisateur.
8.  Créer l'Edge Function `/auth/linkedin/disconnect` pour supprimer les tokens et mettre à jour le statut.
9.  Implémenter le bouton/flux de déconnexion côté frontend.

### Technical Considerations

- Gestion sécurisée des tokens LinkedIn (chiffrement au repos dans la base de données si possible, accès strict via RLS et Edge Functions).
- Gestion des erreurs spécifiques retournées par l'API LinkedIn pendant le flux OAuth.
- Assurer que l'URI de redirection est correctement configurée sur LinkedIn Developer Portal et dans les variables d'environnement.
- Gérer l'état `state` dans le flux OAuth pour prévenir les attaques CSRF.

## Testing Requirements

### Unit Tests

- Tests unitaires pour les fonctions de l'Edge Function `/callback` (échange de code, sauvegarde des tokens).
- Tests unitaires pour la logique de gestion des tokens (rafraîchissement - si implémenté dans le MVP).
- Tests unitaires pour la logique de déconnexion.

### Integration Tests

- Tester le flux OAuth complet en environnement de test (nécessite une application LinkedIn Developer de test).
- Tester la sauvegarde sécurisée des tokens en base de données.
- Tester la déconnexion et la révocation de l'accès.

### User Acceptance Tests

- Je peux cliquer sur "Lier mon compte LinkedIn" et être redirigé vers LinkedIn.
- Je peux autoriser Link-Pedia sur LinkedIn et être redirigé vers l'application.
- L'application m'indique que mon compte LinkedIn est lié avec succès.
- Je peux voir le statut "Compte LinkedIn lié" dans mon profil/paramètres.
- Je peux cliquer sur "Déconnecter mon compte LinkedIn" et confirmer.
- L'application m'indique que mon compte LinkedIn est déconnecté.

## Acceptance Criteria

```gherkin
Feature: Liaison Compte LinkedIn

  Scenario: Liaison réussie d'un compte LinkedIn
    Given je suis connecté à Link-Pedia
    And mon compte LinkedIn n'est pas lié
    When je clique sur le bouton "Lier mon compte LinkedIn"
    Then je suis redirigé vers la page d'autorisation de LinkedIn
    And après avoir autorisé l'accès sur LinkedIn, je suis redirigé vers Link-Pedia
    And mon compte LinkedIn est lié avec succès
    And l'application affiche un message de succès
    And le statut de liaison de mon compte est mis à jour dans l'interface

  Scenario: Échec de la liaison d'un compte LinkedIn (autorisation refusée)
    Given je suis connecté à Link-Pedia
    And mon compte LinkedIn n'est pas lié
    When je clique sur le bouton "Lier mon compte LinkedIn"
    And je suis redirigé vers la page d'autorisation de LinkedIn
    And je refuse l'autorisation sur LinkedIn
    And je suis redirigé vers Link-Pedia
    Then mon compte LinkedIn n'est pas lié
    And l'application affiche un message d'échec

  Scenario: Déconnexion d'un compte LinkedIn
    Given je suis connecté à Link-Pedia
    And mon compte LinkedIn est lié
    When je clique sur le bouton "Déconnecter mon compte LinkedIn"
    And je confirme la déconnexion (si nécessaire)
    Then mon compte LinkedIn est déconnecté de Link-Pedia
    And l'application affiche un message de succès
    And le statut de liaison de mon compte est mis à jour dans l'interface
```

## Security Considerations

- Gestion très stricte et sécurisée des tokens d'accès et de rafraîchissement LinkedIn.
- Utilisation exclusive du flux OAuth 2.0 officiel de LinkedIn.
- Validation du paramètre `state` dans le callback OAuth pour prévenir les attaques CSRF.
- Stockage des clés API LinkedIn (Client ID, Client Secret) dans les variables d'environnement sécurisées des Edge Functions.
- S'assurer que les politiques RLS empêchent un utilisateur d'accéder aux tokens d'un autre utilisateur.

## Performance Considerations

- Le processus de redirection OAuth implique des allers-retours externes, la performance dépendra en partie de LinkedIn.
- Les Edge Functions gérant le callback et la déconnexion doivent être optimisées pour un temps de réponse rapide.

## Accessibility Requirements (Integral Part of Quality UX)

- Le bouton/lien pour initier la liaison est accessible au clavier et a un état de focus visible.
- Les messages de succès/échec après la redirection sont annoncés aux lecteurs d'écran.
- Le statut de liaison est clairement communiqué aux technologies d'assistance.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

Aucune exigence spécifique pour le MVP. Les chaînes de caractères seront codées en dur en français.

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique bloquant la liaison, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour les fonctions des Edge Functions gérant la liaison et la déconnexion.
- Documentation des endpoints API liés à la liaison dans ce document de spécification.
- Mise à jour du schéma de base de données pour inclure la table `linkedin_accounts` et le champ `linkedin_account_linked`.
- Documenter les scopes LinkedIn requis et la procédure de configuration sur LinkedIn Developer Portal (potentiellement dans le guide de déploiement ou un document séparé).

## Open Questions

- Quels scopes LinkedIn exacts sont nécessaires pour toutes les fonctionnalités du MVP (publication, statistiques, données de profil pour audit) ? (Nécessite de consulter la documentation API LinkedIn).
- Faut-il gérer le cas où l'utilisateur a déjà lié son compte à une autre application ? (Comportement standard de LinkedIn OAuth).
- Comment gérer le rafraîchissement automatique des tokens d'accès LinkedIn ? (À implémenter dans le backend).

---

_Last Updated: 22/05/2025_
