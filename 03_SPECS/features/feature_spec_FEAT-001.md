# Feature Specification: Inscription et Connexion

## Overview

- **Feature ID:** FEAT-001
- **Priority:** High
- **Estimated Effort:** À déterminer (Estimation initiale : 2-3 jours)
- **Status:** Planned

## Business Context

### Objective

Permettre aux nouveaux utilisateurs de créer un compte sécurisé sur Linked-Pedia et aux utilisateurs existants de se connecter, afin qu'ils puissent accéder et utiliser toutes les fonctionnalités de l'application pour optimiser leur présence sur LinkedIn.

### User Stories

- As a new user, I want to create an account so that I can access Linked-Pedia's features. (Must Have)
- As a returning user, I want to log in securely so that I can continue managing my LinkedIn presence. (Must Have)

### Success Criteria

- Un nouvel utilisateur peut s'inscrire avec succès en fournissant une adresse email et un mot de passe.
- Un utilisateur existant peut se connecter avec succès en utilisant son adresse email et son mot de passe.
- L'application gère et affiche correctement les erreurs d'inscription ou de connexion (ex: email déjà utilisé, mot de passe incorrect).
- Les informations d'identification de l'utilisateur sont stockées de manière sécurisée.
- L'utilisateur est redirigé vers le tableau de bord ou la page d'onboarding après une connexion réussie.

## Design and UX/UI Principles for this Feature

- **Main Objective:** La conception et l'expérience utilisateur de cette fonctionnalité doivent activement contribuer à l'objectif global d'une application de qualité "Silicon Valley / Y Combinator". Cela signifie une attention méticuleuse aux détails, la modernité, l'intuitivité et l'esthétique. Le processus d'inscription et de connexion doit être perçu comme simple, rapide et sécurisé.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** L'interface doit être épurée et professionnelle, en accord avec l'esthétique générale de Linked-Pedia (couleurs bleu/blanc, typographie moderne). Les formulaires doivent être clairs et faciles à remplir sur tous les appareils (réactivité). Le feedback visuel (états de chargement, messages d'erreur) doit être immédiat et non ambigu. L'accent doit être mis sur la confiance et la sécurité perçues par l'utilisateur.

## Functional Requirements

### Core Functionality

1.  Permettre l'inscription d'un nouvel utilisateur avec email et mot de passe.
2.  Permettre la connexion d'un utilisateur existant avec email et mot de passe.
3.  Valider le format de l'adresse email.
4.  Appliquer des règles de complexité pour le mot de passe lors de l'inscription (voir Conventions de Codage).
5.  Stocker les informations d'identification de manière sécurisée (hachage du mot de passe).
6.  Générer et gérer les sessions utilisateur (tokens d'authentification).
7.  Rediriger l'utilisateur après une connexion/inscription réussie.
8.  Afficher des messages d'erreur appropriés en cas d'échec (email déjà utilisé, identifiants incorrects, etc.).

### User Interactions

1.  L'utilisateur navigue vers la page d'inscription ou de connexion.
2.  L'utilisateur remplit le formulaire (email, mot de passe, confirmation mot de passe pour inscription).
3.  L'utilisateur clique sur le bouton "S'inscrire" ou "Se connecter".
4.  L'application affiche un indicateur de chargement pendant le traitement.
5.  En cas de succès, l'utilisateur est redirigé.
6.  En cas d'échec, un message d'erreur est affiché sur le formulaire.
7.  Option "Mot de passe oublié" (hors scope MVP, mais l'interface peut inclure un lien grisé ou une note).

### Business Rules

1.  Une adresse email ne peut être associée qu'à un seul compte utilisateur.
2.  Le mot de passe doit respecter les critères de complexité définis.
3.  Les tentatives de connexion échouées répétées pour un même compte peuvent entraîner un verrouillage temporaire (à définir si nécessaire pour la sécurité).

## Technical Specifications

### Affected Components

- **Frontend:** Pages d'inscription (`/signup`), de connexion (`/login`), composants de formulaire (Input, Button), gestion de l'état d'authentification, redirection.
- **Backend:** Supabase Auth (gestion des utilisateurs, hachage des mots de passe, tokens), Supabase Edge Functions (potentiellement pour une logique d'inscription/connexion personnalisée si nécessaire, mais Supabase Auth devrait suffire pour le MVP).
- **Database:** Table `auth.users` (gérée par Supabase Auth), potentiellement une table `public.users` pour stocker des informations utilisateur supplémentaires liées à l'ID Supabase Auth.
- **External Services:** Aucun service externe direct pour l'authentification de base, mais Supabase Auth peut s'intégrer à des fournisseurs tiers (non prévu pour le MVP).

### API Changes

#### New Endpoints

- **Endpoint:** `POST /auth/signup`
  - Purpose: Enregistrer un nouvel utilisateur.
  - Request: `{ "email": "string", "password": "string" }`
  - Response: `{ "user": { ... }, "session": { ... } }` (en cas de succès) ou `{ "error": { ... } }` (en cas d'échec).
- **Endpoint:** `POST /auth/login`
  - Purpose: Connecter un utilisateur existant.
  - Request: `{ "email": "string", "password": "string" }`
  - Response: `{ "user": { ... }, "session": { ... } }` (en cas de succès) ou `{ "error": { ... } }` (en cas d'échec).

#### Modified Endpoints

Aucun.

### Database Changes

#### New Tables/Collections

- **Table:** `public.users`
  - Purpose: Stocker les informations utilisateur spécifiques à l'application Linked-Pedia, liées à l'utilisateur Supabase Auth.
  - Fields:
    - `id`: `uuid` (Clé primaire, liée à `auth.users.id`)
    - `email`: `text` (Indexé, unique, peut être copié depuis auth.users)
    - `created_at`: `timestamp with time zone`
    - `updated_at`: `timestamp with time zone`
    - `preferences`: `jsonb` (Stocke les préférences utilisateur, voir FEAT-015)
    - `objectives`: `jsonb` (Stocke les objectifs LinkedIn, voir FEAT-015)
    - `activity_info`: `jsonb` (Stocke les informations sur l'activité, voir FEAT-015)

#### Modified Tables/Collections

Aucun.

### UI Changes

#### New Screens/Components

- **Screen:** Page d'Inscription (`/signup`)
  - Description: Page contenant un formulaire pour la création de compte (champs email, mot de passe, confirmation mot de passe, bouton "S'inscrire"). Inclut un lien vers la page de connexion. Doit être épurée et professionnelle.
  - Wireframe/Mockup (Objectif YC Standard): Disposition simple et centrée. Utilisation de composants `Input` et `Button` stylisés selon le Design System Linked-Pedia. Messages d'erreur clairs sous les champs ou en haut du formulaire. Indicateur de chargement sur le bouton lors de la soumission.
- **Screen:** Page de Connexion (`/login`)
  - Description: Page contenant un formulaire pour la connexion (champs email, mot de passe, bouton "Se connecter"). Inclut un lien vers la page d'inscription et potentiellement un lien "Mot de passe oublié" (grisé si hors scope). Disposition similaire à la page d'inscription pour la cohérence.
  - Wireframe/Mockup (Objectif YC Standard): Disposition simple et centrée. Utilisation de composants `Input` et `Button` stylisés selon le Design System. Feedback clair.
- **Component:** Formulaire d'Authentification
  - Description: Composant réutilisable pour les formulaires d'inscription et de connexion, gérant la validation côté client et l'état de soumission.

#### Modified Screens/Components

Aucun.

## Implementation Plan

### Dependencies

- Supabase SDK (pour interagir avec Supabase Auth).
- Librairie de validation de formulaire (ex: React Hook Form, Zod pour la validation de schéma).
- Librairie de routage (Next.js App Router).

### Implementation Steps

1.  Mettre en place la structure des pages `/signup` et `/login` dans l'App Router de Next.js.
2.  Créer le composant de formulaire d'authentification réutilisable.
3.  Implémenter la logique de validation côté client pour les formulaires.
4.  Intégrer le SDK Supabase pour gérer l'inscription et la connexion.
5.  Implémenter la logique d'appel aux méthodes d'authentification de Supabase dans les gestionnaires de soumission de formulaire.
6.  Gérer les états de chargement et d'erreur lors des appels API.
7.  Implémenter la redirection après succès.
8.  Mettre en place la création de l'enregistrement utilisateur dans la table `public.users` lors de l'inscription (potentiellement via un trigger Supabase ou une Edge Function si une logique supplémentaire est nécessaire).
9.  Définir les politiques RLS pour la table `public.users`.

### Technical Considerations

- Gestion sécurisée des tokens de session côté client (Supabase SDK gère cela généralement via les cookies ou le stockage local sécurisé).
- Gestion des erreurs spécifiques retournées par Supabase Auth.
- Assurer la cohérence entre l'utilisateur dans `auth.users` et l'enregistrement dans `public.users`.

## Testing Requirements

### Unit Tests

- Tests unitaires pour le composant de formulaire (validation, gestion des états).
- Tests unitaires pour les fonctions utilitaires liées à l'authentification (si elles existent).

### Integration Tests

- Tester le flux complet d'inscription via l'interface utilisateur (simulée) et vérifier la création de l'utilisateur dans Supabase (en environnement de test).
- Tester le flux complet de connexion et vérifier la création d'une session valide.
- Tester les cas d'erreur (identifiants incorrects, email déjà utilisé).

### User Acceptance Tests

- Un nouvel utilisateur peut s'inscrire avec une nouvelle adresse email.
- Un utilisateur existant peut se connecter avec ses identifiants.
- Les messages d'erreur sont clairs et corrects.
- La redirection après connexion/inscription fonctionne.

## Acceptance Criteria

```gherkin
Feature: Inscription et Connexion Utilisateur

  Scenario: Inscription réussie d'un nouvel utilisateur
    Given je ne suis pas un utilisateur enregistré
    When je navigue vers la page d'inscription
    And je remplis le formulaire avec une adresse email valide et un mot de passe sécurisé
    And je soumets le formulaire
    Then mon compte est créé avec succès
    And je suis automatiquement connecté
    And je suis redirigé vers le tableau de bord

  Scenario: Connexion réussie d'un utilisateur existant
    Given je suis un utilisateur enregistré
    When je navigue vers la page de connexion
    And je remplis le formulaire avec mon adresse email et mon mot de passe corrects
    And je soumets le formulaire
    Then je suis connecté avec succès
    And je suis redirigé vers le tableau de bord

  Scenario: Échec de l'inscription avec une adresse email déjà utilisée
    Given une adresse email est déjà enregistrée
    When je navigue vers la page d'inscription
    And je remplis le formulaire avec l'adresse email existante et un mot de passe
    And je soumets le formulaire
    Then mon compte n'est pas créé
    And un message d'erreur indiquant que l'email est déjà utilisé est affiché

  Scenario: Échec de la connexion avec des identifiants incorrects
    Given je suis un utilisateur enregistré
    When je navigue vers la page de connexion
    And je remplis le formulaire avec mon adresse email et un mot de passe incorrect
    And je soumets le formulaire
    Then je ne suis pas connecté
    And un message d'erreur indiquant des identifiants invalides est affiché
```

## Security Considerations

- Utiliser exclusivement HTTPS.
- Stocker les mots de passe hachés (géré par Supabase Auth).
- Protéger les routes d'authentification contre les attaques par force brute (géré en partie par Supabase Auth, potentiellement renforcé par des mesures côté Vercel/Edge Functions si nécessaire).
- Valider et assainir toutes les entrées utilisateur côté serveur.

## Performance Considerations

- Les opérations d'inscription et de connexion doivent être rapides (temps de réponse API < 500ms).
- Le chargement des pages d'inscription/connexion doit être optimisé.

## Accessibility Requirements (Integral Part of Quality UX)

- Les formulaires sont entièrement navigables et utilisables au clavier.
- Les champs de formulaire ont des labels associés (`<label for="...">`).
- Les messages d'erreur de validation sont clairs et liés aux champs concernés.
- Le contraste des couleurs est suffisant pour le texte et les éléments interactifs.
- Utiliser des rôles ARIA appropriés si nécessaire pour les messages d'état ou les indicateurs de chargement.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

Aucune exigence spécifique pour le MVP. Les chaînes de caractères seront codées en dur en français.

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique bloquant l'inscription/connexion, rollback vers la version précédente stable via la plateforme d'hébergement.

## Documentation Requirements

- Documentation TSDoc pour les composants React et les fonctions liées à l'authentification.
- Documentation des endpoints API dans ce document de spécification.
- Mise à jour du schéma de base de données dans la documentation globale du modèle de données.

## Open Questions

- Faut-il implémenter une confirmation d'email lors de l'inscription ? (Hors scope MVP, mais à considérer pour la sécurité).
- Faut-il implémenter une fonctionnalité "Mot de passe oublié" dans le MVP ? (Actuellement hors scope).

---

_Last Updated: 22/05/2025_
