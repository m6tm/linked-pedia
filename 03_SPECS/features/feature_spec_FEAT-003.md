# Feature Specification: Audit de Profil LinkedIn

## Overview

- **Feature ID:** FEAT-003
- **Priority:** High
- **Estimated Effort:** À déterminer (Estimation initiale : 4-5 jours, dépend de l'accès aux données de profil via l'API et de la complexité de l'algorithme d'audit)
- **Status:** Planned

## Business Context

### Objective

Analyser le profil LinkedIn de l'utilisateur (en tenant compte du SSI si possible) et fournir un rapport détaillé avec des recommandations d'optimisation personnalisées. L'objectif est d'aider l'utilisateur à comprendre les points forts et faibles de son profil et à savoir comment l'améliorer pour atteindre ses objectifs professionnels sur LinkedIn.

### User Stories

- As a user, I want to get an audit of my LinkedIn profile so that I understand how to optimize it. (Must Have)
- As a user, I want to see personalized recommendations based on my SSI so that I can improve my profile's effectiveness. (Must Have)
- As a user, I want to see the evolution of my profile score over time so that I can track my optimization efforts. (Should Have - pour futures itérations, mais la structure de données doit le permettre)

### Success Criteria

- L'utilisateur peut lancer un audit de son profil LinkedIn (si compte lié).
- L'application récupère les données nécessaires du profil LinkedIn via l'API.
- L'application calcule un score d'audit (global et/ou par section) basé sur les données du profil et les critères définis.
- L'application génère une liste de recommandations d'optimisation personnalisées.
- L'application affiche le rapport d'audit de manière claire et compréhensible.
- Le rapport inclut le score (si calculé) et les recommandations.
- L'application gère les cas où le compte LinkedIn n'est pas lié ou si la récupération des données échoue.

## Design and UX/UI Principles for this Feature

- **Main Objective:** L'interface de l'audit de profil doit être informative, actionable et inspirer confiance. L'objectif "Silicon Valley / Y Combinator" implique ici de présenter des données complexes (analyse de profil) de manière simple et visuellement agréable, avec des recommandations claires et faciles à comprendre.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** Utiliser des visualisations claires (graphiques simples, barres de progression) pour présenter les scores. Les recommandations doivent être présentées sous forme de liste facile à lire, potentiellement groupées par section du profil. Utiliser les couleurs du Design System (ex: couleur accent pour mettre en évidence les points à améliorer). L'interface doit être réactive. Fournir un indicateur de chargement clair pendant l'exécution de l'audit.

## Functional Requirements

### Core Functionality

1.  Vérifier si le compte LinkedIn de l'utilisateur est lié.
2.  Si lié, permettre à l'utilisateur de lancer un audit de profil.
3.  Appeler l'API LinkedIn pour récupérer les données du profil de l'utilisateur (informations de base, résumé, expérience, éducation, compétences, etc. - dépend des scopes API disponibles).
4.  Si le score SSI est accessible via l'API, le récupérer.
5.  Si le SSI n'est pas accessible ou en complément, implémenter un algorithme pour calculer un score d'audit basé sur les données disponibles du profil (complétude des sections, mots-clés pertinents - algorithme à définir précisément).
6.  Générer des recommandations d'optimisation personnalisées basées sur les résultats de l'analyse du profil et le score.
7.  Stocker les résultats de l'audit (score, recommandations) dans la base de données.
8.  Afficher le rapport d'audit à l'utilisateur dans une interface dédiée.
9.  Gérer le cas où le compte LinkedIn n'est pas lié (inviter l'utilisateur à le lier - voir FEAT-002).
10. Gérer les erreurs lors de la récupération des données API ou de l'exécution de l'audit.

### User Interactions

1.  L'utilisateur navigue vers la section "Audit de Profil".
2.  Si le compte LinkedIn n'est pas lié, l'application l'invite à le faire.
3.  Si le compte est lié, l'utilisateur clique sur un bouton "Lancer l'audit".
4.  L'application affiche un indicateur de chargement pendant l'exécution de l'audit.
5.  Une fois l'audit terminé, l'application affiche le rapport.
6.  L'utilisateur examine le score et les recommandations.
7.  L'utilisateur peut revenir à cette section pour consulter le dernier rapport d'audit.

### Business Rules

1.  Un audit de profil ne peut être lancé que si un compte LinkedIn principal est lié à l'utilisateur Linked-Pedia.
2.  Les recommandations doivent être personnalisées et basées sur l'analyse du profil spécifique de l'utilisateur.
3.  Les résultats de l'audit doivent être stockés pour permettre une consultation ultérieure.

## Technical Specifications

### Affected Components

- **Frontend:** Page "Audit de Profil", composants pour afficher le score (graphique, barre), composants pour afficher les recommandations (liste), indicateur de chargement, message si compte non lié.
- **Backend:** Supabase Edge Function pour lancer l'audit (`/analytics/profile/audit`), Edge Function pour récupérer les données de profil via l'API LinkedIn, logique de calcul du score et de génération des recommandations dans l'Edge Function.
- **Database:** Table `public.profile_audits` pour stocker les résultats d'audit.
- **External Services:** API officielle de LinkedIn (endpoints pour récupérer les données de profil), potentiellement un service IA externe/MCP pour aider à l'analyse du contenu du profil ou à la génération de recommandations plus sophistiquées (à évaluer). Le MCP `hdw` pourrait être exploré ici.

### API Changes

#### New Endpoints

- **Endpoint:** `GET /analytics/profile/audit`
  - Purpose: Lancer un audit du profil LinkedIn de l'utilisateur connecté et retourner le rapport.
  - Request: `{}`
  - Response: `{ "score": number, "sections_scores": { ... }, "recommendations": [], "audit_date": "timestamp" }` (en cas de succès) ou `{ "error": { ... } }` (en cas d'échec).

#### Modified Endpoints

Aucun.

### Database Changes

#### New Tables/Collections

- **Table:** `public.profile_audits`
  - Purpose: Stocker les résultats des audits de profil LinkedIn.
  - Fields:
    - `id`: `uuid` (Clé primaire)
    - `user_id`: `uuid` (Clé étrangère vers `public.users.id`)
    - `linkedin_account_id`: `uuid` (Clé étrangère vers `public.linkedin_accounts.id`)
    - `audit_date`: `timestamp with time zone` (Date et heure de l'audit)
    - `linkedin_ssi_score`: `integer` (Score SSI si accessible, peut être null)
    - `calculated_score`: `integer` (Score calculé par Linked-Pedia si SSI non accessible ou en complément)
    - `sections_scores`: `jsonb` (Scores détaillés par section du profil)
    - `recommendations`: `jsonb` (Liste des recommandations d'optimisation)
    - `raw_profile_data`: `jsonb` (Données brutes du profil LinkedIn utilisées pour l'audit - à évaluer pour la confidentialité et le stockage)
    - `status`: `text` (ex: 'completed', 'failed')

#### Modified Tables/Collections

Aucun.

### UI Changes

#### New Screens/Components

- **Screen:** Page "Audit de Profil" (`/audit`)
  - Description: Page dédiée à l'audit de profil. Affiche le statut de liaison LinkedIn, un bouton pour lancer l'audit, un indicateur de chargement pendant l'audit, et la zone d'affichage du rapport d'audit.
  - Wireframe/Mockup (Objectif YC Standard): Disposition claire avec une section pour le statut/bouton d'action et une section pour le rapport. Utiliser des composants visuels pour le score (barre, cercle de progression). Présenter les recommandations sous forme de liste ou de cartes.
- **Component:** Composant Rapport d'Audit
  - Description: Composant pour afficher les résultats de l'audit (score, scores par section, recommandations).
- **Component:** Indicateur de Statut de Liaison LinkedIn
  - Description: Composant réutilisable pour afficher si le compte LinkedIn est lié ou non, avec un lien vers la page de liaison si nécessaire.

#### Modified Screens/Components

- **Screen:** Tableau de Bord
  - Ajouter un widget ou une section pour afficher un résumé du dernier audit de profil (score, date) et un lien rapide vers la page d'audit complète.

## Implementation Plan

### Dependencies

- Supabase SDK.
- Librairie de requêtes HTTP côté backend (Edge Function) pour appeler l'API LinkedIn.
- Logique de calcul du score et de génération des recommandations (algorithme à implémenter dans l'Edge Function).
- Potentiellement, intégration avec un service IA externe/MCP pour l'analyse de texte/génération de recommandations.
- Table `public.linkedin_accounts` (FEAT-002).

### Implementation Steps

1.  Créer la table `public.profile_audits` dans Supabase avec les politiques RLS appropriées.
2.  Créer l'Edge Function `/analytics/profile/audit`.
3.  Dans l'Edge Function, vérifier la liaison du compte LinkedIn.
4.  Appeler l'API LinkedIn pour récupérer les données de profil nécessaires.
5.  Implémenter la logique de calcul du score d'audit (en utilisant le SSI si disponible, sinon l'algorithme interne).
6.  Implémenter la logique de génération des recommandations personnalisées.
7.  Stocker les résultats de l'audit dans la table `public.profile_audits`.
8.  Créer la page frontend "Audit de Profil".
9.  Implémenter l'affichage du statut de liaison et du bouton "Lancer l'audit".
10. Implémenter l'appel à l'Edge Function `/audit` depuis le frontend et gérer les états de chargement/erreur.
11. Créer le composant pour afficher le rapport d'audit.
12. Intégrer un résumé de l'audit dans le tableau de bord.

### Technical Considerations

- Dépendance à la disponibilité et aux données accessibles via l'API LinkedIn. L'accès au SSI officiel peut être limité.
- La complexité et la pertinence de l'algorithme d'audit interne sont cruciales pour la valeur de cette fonctionnalité.
- Gestion du stockage des données brutes du profil LinkedIn (considérations de confidentialité et de conformité).
- Temps de réponse de l'audit (peut prendre du temps si plusieurs appels API sont nécessaires). Envisager un traitement asynchrone si l'audit est long.

## Testing Requirements

### Unit Tests

- Tests unitaires pour l'algorithme de calcul du score d'audit (avec des données de profil mockées).
- Tests unitaires pour la logique de génération des recommandations (avec des résultats d'analyse mockés).
- Tests unitaires pour l'Edge Function `/audit` (mockant les appels API LinkedIn et la base de données).

### Integration Tests

- Tester le flux complet : Lancer l'audit depuis l'UI, vérifier l'appel à l'Edge Function, la récupération des données API (mockée), le calcul, la sauvegarde en base de données et l'affichage du rapport.
- Tester les cas d'erreur (compte non lié, échec API).

### User Acceptance Tests

- Je peux naviguer vers la page "Audit de Profil".
- Si mon compte LinkedIn n'est pas lié, l'application me le dit.
- Si mon compte LinkedIn est lié, je peux cliquer sur "Lancer l'audit".
- L'application affiche un indicateur pendant l'audit.
- Après l'audit, je vois un rapport avec un score et des recommandations.
- Le rapport est clair et facile à comprendre.
- Je peux revenir plus tard pour consulter le dernier rapport d'audit.
- Un résumé de mon audit apparaît sur le tableau de bord.

## Acceptance Criteria

```gherkin
Feature: Audit de Profil LinkedIn

  Scenario: Lancement et affichage réussi de l'audit de profil
    Given je suis connecté à Linked-Pedia
    And mon compte LinkedIn est lié
    When je navigue vers la page "Audit de Profil"
    And je clique sur le bouton "Lancer l'audit"
    Then l'application affiche un indicateur de chargement
    And l'application récupère mes données de profil LinkedIn via l'API
    And l'application calcule mon score d'audit et génère des recommandations
    And l'application stocke les résultats de l'audit
    And l'application affiche le rapport d'audit complet avec mon score et mes recommandations

  Scenario: Affichage du message si le compte LinkedIn n'est pas lié
    Given je suis connecté à Linked-Pedia
    And mon compte LinkedIn n'est pas lié
    When je navigue vers la page "Audit de Profil"
    Then l'application affiche un message m'invitant à lier mon compte LinkedIn
    And le bouton "Lancer l'audit" n'est pas disponible

  Scenario: Consultation du dernier rapport d'audit
    Given je suis connecté à Linked-Pedia
    And j'ai déjà lancé un audit de profil
    When je navigue vers la page "Audit de Profil"
    Then l'application affiche automatiquement mon dernier rapport d'audit

  Scenario: Affichage d'un résumé de l'audit sur le tableau de bord
    Given je suis connecté à Linked-Pedia
    And j'ai déjà lancé un audit de profil
    When je navigue vers le tableau de bord
    Then l'application affiche un résumé de mon dernier audit de profil, incluant mon score et un lien vers le rapport complet
```

## Security Considerations

- Accès sécurisé aux données de profil LinkedIn via l'API (utilisation des tokens d'accès).
- Stockage sécurisé des résultats d'audit en base de données (RLS).
- Attention à ne pas stocker de données sensibles du profil LinkedIn plus que nécessaire pour l'audit.

## Performance Considerations

- Le temps d'exécution de l'audit dépendra du temps de réponse de l'API LinkedIn et de la complexité de l'algorithme de calcul/génération. Viser un temps d'exécution raisonnable (quelques secondes).
- Si l'audit est long, envisager un traitement en arrière-plan avec notification à l'utilisateur une fois terminé.

## Accessibility Requirements (Integral Part of Quality UX)

- La page d'audit est navigable au clavier.
- Les scores et recommandations sont clairement structurés et lisibles par les lecteurs d'écran.
- Les visualisations (graphiques) sont accessibles (fournir des alternatives textuelles si nécessaire).
- Les messages d'état (chargement, erreur) sont annoncés aux technologies d'assistance.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

Aucune exigence spécifique pour le MVP. Les chaînes de caractères seront codées en dur en français.

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour l'Edge Function `/audit` et les fonctions de calcul/génération.
- Documentation de l'endpoint API `/audit`.
- Documentation détaillée de l'algorithme de calcul du score d'audit et de génération des recommandations.
- Mise à jour du schéma de base de données pour inclure la table `profile_audits`.

## Open Questions

- Quel niveau d'accès aux données de profil l'API LinkedIn permet-elle exactement pour l'audit ? (Nécessite de consulter la documentation API).
- Comment l'algorithme de calcul du score d'audit interne sera-t-il défini précisément ? (Nécessite une conception détaillée de l'algorithme).
- Faut-il stocker les données brutes du profil LinkedIn utilisées pour l'audit, et si oui, avec quelles précautions ?

---

_Last Updated: 22/05/2025_
