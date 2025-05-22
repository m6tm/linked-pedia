# Feature Specification: Générateur de Posts Texte

## Overview

- **Feature ID:** FEAT-004
- **Priority:** High
- **Estimated Effort:** À déterminer (Estimation initiale : 3-4 jours, dépend de l'intégration du service IA)
- **Status:** Planned

## Business Context

### Objective

Permettre aux utilisateurs de générer rapidement des brouillons de posts LinkedIn basés sur des thèmes, axes ou catégories qu'ils choisissent. L'objectif est de surmonter le "syndrome de la page blanche" et de fournir un point de départ efficace pour la création de contenu.

### User Stories

- As a user, I want to generate post drafts based on themes so that I can quickly start writing. (Must Have)
- As a user, I want to provide keywords or topics so that the generated posts are relevant to my needs. (Must Have)
- As a user, I want to be able to choose the style or tone of the generated post (e.g., informative,Brouillon, inspirant). (Should Have)

### Success Criteria

- L'utilisateur peut accéder à la fonctionnalité de génération de posts texte.
- L'utilisateur peut fournir des mots-clés, un thème ou choisir parmi des catégories/axes prédéfinis.
- L'application appelle le service IA avec les paramètres fournis.
- Le service IA génère un brouillon de post texte pertinent.
- L'application affiche le brouillon généré dans une interface d'édition.
- L'utilisateur peut modifier le brouillon généré.
- L'utilisateur peut sauvegarder le brouillon généré.
- L'application gère les erreurs lors de l'appel au service IA.

## Design and UX/UI Principles for this Feature

- **Main Objective:** L'interface du générateur de posts texte doit être créative et pertinente, tout en restant simple et intuitive. L'objectif "Silicon Valley / Y Combinator" implique ici de rendre l'interaction avec l'IA fluide et productive, en présentant les options de manière claire et en affichant le résultat rapidement.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** Utiliser un formulaire simple pour la saisie des paramètres (mots-clés, thème, choix de catégorie/axe). Afficher un indicateur de chargement clair pendant la génération par l'IA. Présenter le brouillon généré dans une zone de texte éditable, avec des options pour l'optimiser (lien vers FEAT-007) ou générer des accroches (lien vers FEAT-008). L'esthétique doit être épurée et mettre en valeur le contenu généré.

## Functional Requirements

### Core Functionality

1.  Fournir une interface pour la saisie des paramètres de génération (mots-clés, thème, sélection de catégories/axes).
2.  Appeler un service IA externe (via Edge Function) avec les paramètres fournis pour générer un brouillon de post texte.
3.  Recevoir le contenu généré par le service IA.
4.  Afficher le contenu généré dans une zone de texte éditable.
5.  Permettre à l'utilisateur de modifier le contenu généré.
6.  Permettre à l'utilisateur de sauvegarder le brouillon (créer un enregistrement dans la table `posts` avec le statut 'brouillon').
7.  Gérer les erreurs lors de l'appel au service IA (ex: service indisponible, contenu non généré).

### User Interactions

1.  L'utilisateur navigue vers la section "Créer un Post" et choisit "Générer Texte".
2.  L'utilisateur remplit les champs ou sélectionne les options (mots-clés, thème, catégorie/axe).
3.  L'utilisateur clique sur un bouton "Générer".
4.  L'application affiche un indicateur de chargement.
5.  Le brouillon généré apparaît dans une zone d'édition.
6.  L'utilisateur modifie le texte si nécessaire.
7.  L'utilisateur clique sur un bouton "Sauvegarder le brouillon".
8.  L'application confirme la sauvegarde.

### Business Rules

1.  La génération de post nécessite au moins un paramètre d'entrée (mots-clés, thème, ou sélection).
2.  Le contenu généré doit être un point de départ et peut être modifié par l'utilisateur.
3.  Les brouillons générés sont associés à l'utilisateur connecté.

## Technical Specifications

### Affected Components

- **Frontend:** Page/section "Générer Post Texte", formulaire de saisie des paramètres, zone de texte éditable, bouton "Générer", bouton "Sauvegarder", indicateur de chargement.
- **Backend:** Supabase Edge Function pour gérer l'appel au service IA de génération (`/posts/generate/text`), interaction avec le service IA externe/MCP, interaction avec la base de données Supabase (table `posts`).
- **Database:** Table `public.posts` (pour sauvegarder les brouillons).
- **External Services:** Service IA externe ou MCP pour la génération de texte.

### API Changes

#### New Endpoints

- **Endpoint:** `POST /posts/generate/text`
  - Purpose: Appeler le service IA pour générer un brouillon de post texte.
  - Request: `{ "keywords": "string", "theme": "string", "category": "string", "axes": "string[]", "style": "string" }` (les champs peuvent être optionnels selon l'implémentation UI, mais au moins un doit être présent).
  - Response: `{ "content": "string" }` (en cas de succès) ou `{ "error": { ... } }` (en cas d'échec).

#### Modified Endpoints

Aucun.

### Database Changes

#### New Tables/Collections

Aucun (la table `public.posts` est utilisée, potentiellement créée par une autre fonctionnalité si elle est implémentée avant).

#### Modified Tables/Collections

- **Table:** `public.posts`
  - S'assurer que la structure de la table `posts` peut stocker le contenu généré et les métadonnées associées (type, catégorie, axes).

### UI Changes

#### New Screens/Components

- **Screen:** Section "Générer Texte" (au sein de la page "Créer un Post")
  - Description: Interface pour la génération de posts texte. Contient un formulaire de saisie et une zone d'affichage/édition du résultat.
  - Wireframe/Mockup (Objectif YC Standard): Formulaire simple et épuré en haut, zone de texte éditable en dessous. Bouton "Générer" clair. Indicateur de chargement visuel. Bouton "Sauvegarder le brouillon" et potentiellement des liens vers "Optimiser" et "Générer Accroches".
- **Component:** Formulaire de Paramètres de Génération
  - Description: Composant pour saisir les mots-clés, thème, et sélectionner les catégories/axes.
- **Component:** Zone d'Édition de Brouillon
  - Description: Composant pour afficher et permettre la modification du contenu généré.

#### Modified Screens/Components

- **Screen:** Page "Créer un Post"
  - Ajouter des options pour choisir le type de génération (Texte, Fichier, Idée).

## Implementation Plan

### Dependencies

- Supabase SDK.
- Librairie de requêtes HTTP côté backend (Edge Function) pour appeler le service IA externe/MCP.
- Service IA externe ou MCP capable de générer du texte basé sur des prompts.
- Table `public.posts`.

### Implementation Steps

1.  Créer l'Edge Function `/posts/generate/text`.
2.  Dans l'Edge Function, implémenter l'appel au service IA externe/MCP en utilisant l'outil `use_mcp_tool` ou une librairie HTTP.
3.  Gérer les paramètres d'entrée et formater le prompt pour le service IA.
4.  Gérer la réponse du service IA et extraire le contenu généré.
5.  Créer la page/section frontend "Générer Texte".
6.  Implémenter le formulaire de paramètres et le bouton "Générer".
7.  Implémenter l'appel à l'Edge Function `/generate/text` depuis le frontend.
8.  Gérer les états de chargement et d'erreur côté frontend.
9.  Implémenter la zone de texte éditable pour afficher le brouillon généré.
10. Implémenter la logique de sauvegarde du brouillon dans la table `public.posts` via une autre Edge Function (ou directement via le SDK Supabase si les règles RLS le permettent).

### Technical Considerations

- Choix et intégration du service IA externe/MCP le plus pertinent et performant pour la génération de texte.
- Coût potentiel lié à l'utilisation du service IA.
- Gestion des temps de réponse du service IA (peut être plus long que les appels API classiques).
- Formatage du prompt pour l'IA pour obtenir les meilleurs résultats.
- Gestion de la longueur maximale du contenu généré par l'IA et des limites de caractères de LinkedIn.

## Testing Requirements

### Unit Tests

- Tests unitaires pour l'Edge Function `/generate/text` (mockant l'appel au service IA et la base de données).
- Tests unitaires pour les fonctions de formatage du prompt IA.

### Integration Tests

- Tester le flux complet : Saisie paramètres UI -> Appel Edge Function -> Appel service IA (mocké) -> Affichage brouillon -> Sauvegarde brouillon en base de données.
- Tester les cas d'erreur (service IA indisponible, réponse IA inattendue).

### User Acceptance Tests

- Je peux naviguer vers la section "Générer Texte".
- Je peux saisir des mots-clés/thème et cliquer sur "Générer".
- L'application affiche un indicateur de chargement.
- Un brouillon de post pertinent est généré et affiché.
- Je peux modifier le texte du brouillon.
- Je peux sauvegarder le brouillon.
- Le brouillon sauvegardé apparaît dans mon tableau de bord.

## Acceptance Criteria

```gherkin
Feature: Générateur de Posts Texte

  Scenario: Génération et sauvegarde réussie d'un brouillon de post texte
    Given je suis connecté à Linked-Pedia
    When je navigue vers la section "Créer un Post" et choisis "Générer Texte"
    And je saisis des mots-clés ou un thème
    And je clique sur le bouton "Générer"
    Then l'application affiche un indicateur de chargement
    And l'application appelle le service IA pour générer un brouillon
    And un brouillon de post texte pertinent est généré
    And le brouillon est affiché dans une zone d'édition
    When je clique sur le bouton "Sauvegarder le brouillon"
    Then le brouillon est sauvegardé dans mon tableau de bord

  Scenario: Affichage d'un message d'erreur si la génération échoue
    Given je suis connecté à Linked-Pedia
    And le service IA de génération est indisponible ou retourne une erreur
    When je navigue vers la section "Créer un Post" et choisis "Générer Texte"
    And je saisis des mots-clés ou un thème
    And je clique sur le bouton "Générer"
    Then l'application affiche un indicateur de chargement
    And l'application tente d'appeler le service IA
    And l'appel au service IA échoue
    And l'application affiche un message d'erreur clair à l'utilisateur
```

## Security Considerations

- Sécuriser l'appel à l'Edge Function `/generate/text` (authentification utilisateur).
- Gérer les clés API du service IA de manière sécurisée (variables d'environnement Supabase).
- Valider et assainir les paramètres d'entrée utilisateur avant de les envoyer au service IA.

## Performance Considerations

- Le temps de réponse de la génération dépendra du service IA. Afficher un indicateur de chargement clair est essentiel.
- Optimiser l'appel à l'Edge Function et le traitement de la réponse.

## Accessibility Requirements (Integral Part of Quality UX)

- Le formulaire de paramètres est navigable au clavier.
- La zone de texte éditable est accessible et utilisable avec un lecteur d'écran.
- L'indicateur de chargement est accessible.
- Les messages d'erreur sont clairs et annoncés aux technologies d'assistance.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

Aucune exigence spécifique pour le MVP. Les chaînes de caractères seront codées en dur en français.

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour l'Edge Function `/generate/text` et les composants frontend associés.
- Documentation de l'endpoint API `/generate/text`.
- Documentation du service IA externe/MCP utilisé et de son intégration.

## Open Questions

- Quel service IA externe ou MCP sera utilisé pour la génération de texte ? (Nécessite une décision et une intégration).
- Comment les catégories/axes de génération seront-ils définis et gérés ? (Configuration statique ou dynamique ?).
- Faut-il stocker les paramètres de génération utilisés pour un brouillon donné ?

---

_Last Updated: 22/05/2025_
