# Feature Specification: Générateur de Posts à partir de Fichier

## Overview

- **Feature ID:** FEAT-005
- **Priority:** Medium
- **Estimated Effort:** À déterminer (Estimation initiale : 4-5 jours, dépend de la complexité du traitement de fichier/lien et de l'intégration IA)
- **Status:** Planned

## Business Context

### Objective

Permettre aux utilisateurs de soumettre un fichier (document, image) ou un lien (article, page web) et de générer automatiquement un brouillon de post LinkedIn pertinent basé sur le contenu de cette ressource. L'objectif est de faciliter la transformation de contenus existants en posts LinkedIn, offrant un gain de temps et d'efficacité.

### User Stories

- As a user, I want to generate a post from a file (image, doc) so that I can easily share insights from documents or images. (Should Have)
- As a user, I want to generate a post from a link (article, web page) so that I can quickly share and comment on external content. (Should Have)
- As a user, I want the generated post to capture the main points of the file or link so that it's a relevant starting point. (Should Have)

### Success Criteria

- L'utilisateur peut accéder à la fonctionnalité de génération de posts à partir de fichier/lien.
- L'utilisateur peut télécharger un fichier ou coller un lien.
- L'application traite le fichier/lien pour en extraire le contenu pertinent.
- L'application appelle le service IA avec le contenu extrait pour générer un brouillon de post.
- Le brouillon généré est affiché dans une interface d'édition.
- L'utilisateur peut modifier et sauvegarder le brouillon.
- L'application gère les types de fichiers/liens supportés et affiche un message d'erreur pour les formats non supportés.
- L'application gère les erreurs lors du traitement du fichier/lien ou de l'appel IA.

## Design and UX/UI Principles for this Feature

- **Main Objective:** L'interface doit être intelligente et pratique. Le processus de soumission de fichier/lien et de génération doit être fluide, avec un feedback clair sur l'état du traitement. L'objectif "Silicon Valley / Y Combinator" implique ici une expérience utilisateur efficace pour transformer des ressources externes en contenu LinkedIn, avec une attention particulière aux états de chargement et aux messages d'erreur.
- **Reference:** Refer to the general design and UX/UI principles defined in the Design Conventions document ([`02_AI-DOCS/Conventions/design_conventions.md`](02_AI-DOCS/Conventions/design_conventions.md:1)).
- **Specifics for this feature:** Utiliser un composant clair pour le téléchargement de fichier (drag-and-drop, bouton de sélection) et un champ de saisie pour les liens. Afficher une barre de progression ou un indicateur de chargement détaillé pendant le téléchargement, le traitement et la génération IA. Présenter le brouillon généré dans une zone de texte éditable, similaire à FEAT-004. Utiliser des icônes claires pour les types de fichiers supportés.

## Functional Requirements

### Core Functionality

1.  Fournir une interface pour le téléchargement de fichiers et la saisie de liens.
2.  Gérer le téléchargement de fichiers vers un stockage temporaire (ex: Supabase Storage).
3.  Envoyer le fichier téléchargé ou le lien saisi à une Edge Function pour traitement.
4.  Dans l'Edge Function, analyser le fichier/lien pour en extraire le contenu textuel ou les informations clés.
5.  Appeler un service IA externe (via Edge Function) avec le contenu extrait pour générer un brouillon de post LinkedIn.
6.  Recevoir le contenu généré par le service IA.
7.  Afficher le contenu généré dans une zone de texte éditable.
8.  Permettre à l'utilisateur de modifier le contenu généré.
9.  Permettre à l'utilisateur de sauvegarder le brouillon (créer un enregistrement dans la table `posts` avec le statut 'brouillon').
10. Gérer les types de fichiers supportés (ex: PDF, DOCX, TXT, JPG, PNG) et les types de liens (articles de blog, pages web).
11. Gérer les erreurs lors du téléchargement, du traitement de fichier/lien, ou de l'appel au service IA.

### User Interactions

1.  L'utilisateur navigue vers la section "Créer un Post" et choisit "À partir de Fichier/Lien".
2.  L'utilisateur télécharge un fichier ou colle un lien.
3.  L'application affiche un indicateur de progression/chargement.
4.  L'application traite la ressource et génère un brouillon.
5.  Le brouillon généré apparaît dans une zone d'édition.
6.  L'utilisateur modifie le texte si nécessaire.
7.  L'utilisateur clique sur un bouton "Sauvegarder le brouillon".
8.  L'application confirme la sauvegarde.

### Business Rules

1.  Seuls les types de fichiers et de liens supportés peuvent être traités.
2.  La taille des fichiers téléchargés peut être limitée.
3.  Le contenu extrait du fichier/lien sert de base à la génération IA.
4.  Les brouillons générés sont associés à l'utilisateur connecté.

## Technical Specifications

### Affected Components

- **Frontend:** Page/section "À partir de Fichier/Lien", composant de téléchargement de fichier, champ de saisie de lien, indicateur de progression/chargement, zone de texte éditable, bouton "Sauvegarder".
- **Backend:** Supabase Edge Function pour gérer le téléchargement et le traitement de fichier/lien (`/posts/generate/file`), interaction avec Supabase Storage, logique d'extraction de contenu (potentiellement via une librairie ou un service externe), appel au service IA externe/MCP, interaction avec la base de données Supabase (table `posts`).
- **Database:** Table `public.posts` (pour sauvegarder les brouillons). Potentiellement Supabase Storage pour le stockage temporaire des fichiers.
- **External Services:** Service IA externe ou MCP pour la génération de texte. Potentiellement un service externe ou une librairie pour l'extraction de texte/contenu à partir de différents formats de fichiers/liens (ex: OCR pour images, parsing de PDF/DOCX, scraping pour liens). Le MCP `mcp-server-firecrawl` pourrait être exploré pour l'extraction de contenu web.

### API Changes

#### New Endpoints

- **Endpoint:** `POST /posts/generate/file`
  - Purpose: Gérer le téléchargement de fichier ou la soumission de lien, traiter la ressource et appeler le service IA pour générer un brouillon.
  - Request: `multipart/form-data` pour les fichiers, ou `application/json` pour les liens `{ "url": "string" }`.
  - Response: `{ "content": "string" }` (en cas de succès) ou `{ "error": { ... } }` (en cas d'échec).

#### Modified Endpoints

Aucun.

### Database Changes

#### New Tables/Collections

Aucun (la table `public.posts` est utilisée).

#### Modified Tables/Collections

Aucun.

### UI Changes

#### New Screens/Components

- **Screen:** Section "À partir de Fichier/Lien" (au sein de la page "Créer un Post")
  - Description: Interface pour la génération de posts à partir de ressources externes. Contient des options pour télécharger un fichier ou saisir un lien, un indicateur de progression, et une zone d'affichage/édition du résultat.
  - Wireframe/Mockup (Objectif YC Standard): Zone de dépôt de fichier ou bouton de sélection, champ de saisie de lien. Indicateur de progression visuel pendant le traitement. Zone d'édition du brouillon généré en dessous.
- **Component:** Composant de Téléchargement de Fichier
  - Description: Permet à l'utilisateur de sélectionner ou glisser-déposer un fichier. Affiche le nom du fichier et la progression du téléchargement/traitement.
- **Component:** Champ de Saisie de Lien
  - Description: Champ de texte pour coller une URL.
- **Component:** Indicateur de Progression/Chargement
  - Description: Barre de progression ou spinner affichant l'état du traitement (téléchargement, analyse, génération IA).

#### Modified Screens/Components

- **Screen:** Page "Créer un Post"
  - Ajouter des options pour choisir le type de génération (Texte, Fichier, Idée).

## Implementation Plan

### Dependencies

- Supabase SDK.
- Librairie de requêtes HTTP côté backend (Edge Function) pour appeler le service IA externe/MCP et potentiellement un service d'extraction de contenu.
- Service IA externe ou MCP capable de générer du texte.
- Potentiellement, une librairie ou un service externe/MCP pour l'extraction de contenu (parsing PDF/DOCX, OCR, scraping web).
- Supabase Storage pour le stockage temporaire des fichiers.
- Table `public.posts`.

### Implementation Steps

1.  Créer l'Edge Function `/posts/generate/file`.
2.  Dans l'Edge Function, gérer la réception du fichier téléchargé ou du lien.
3.  Implémenter la logique d'extraction de contenu :
    - Pour les fichiers : Utiliser une librairie ou appeler un service/MCP pour lire le texte ou analyser l'image.
    - Pour les liens : Utiliser une librairie ou appeler un service/MCP pour scraper le contenu de la page web.
4.  Gérer les types de fichiers/liens supportés et retourner une erreur pour les autres.
5.  Appeler le service IA externe/MCP avec le contenu extrait pour générer un brouillon de post.
6.  Gérer la réponse du service IA et extraire le contenu généré.
7.  Créer la page/section frontend "À partir de Fichier/Lien".
8.  Implémenter le composant de téléchargement de fichier et le champ de saisie de lien.
9.  Implémenter l'appel à l'Edge Function `/generate/file` depuis le frontend, en gérant l'envoi du fichier ou du lien.
10. Gérer les états de progression et de chargement côté frontend.
11. Implémenter la zone de texte éditable pour afficher le brouillon généré.
12. Implémenter la logique de sauvegarde du brouillon dans la table `public.posts`.

### Technical Considerations

- Choix et intégration du service/librairie le plus efficace pour l'extraction de contenu à partir de divers formats.
- Gestion des fichiers volumineux et des liens complexes.
- Temps de réponse potentiellement long en raison du traitement de fichier/lien et de l'appel IA.
- Coût potentiel lié aux services externes (extraction, IA).
- Sécurité des fichiers téléchargés (analyse antivirus si nécessaire, limitation des types de fichiers).

## Testing Requirements

### Unit Tests

- Tests unitaires pour la logique d'extraction de contenu (avec des fichiers/liens mockés).
- Tests unitaires pour l'Edge Function `/generate/file` (mockant l'extraction et l'appel IA).

### Integration Tests

- Tester le flux complet : Téléchargement/saisie lien UI -> Appel Edge Function -> Traitement/Extraction (mocké) -> Appel service IA (mocké) -> Affichage brouillon -> Sauvegarde brouillon.
- Tester différents types de fichiers et de liens.
- Tester les cas d'erreur (format non supporté, échec traitement, échec IA).

### User Acceptance Tests

- Je peux naviguer vers la section "À partir de Fichier/Lien".
- Je peux télécharger un fichier (ex: PDF, image).
- Je peux coller un lien (ex: URL d'article).
- L'application affiche un indicateur de progression/chargement.
- Un brouillon de post pertinent basé sur le contenu du fichier/lien est généré et affiché.
- Je peux modifier le texte du brouillon.
- Je peux sauvegarder le brouillon.
- L'application affiche un message d'erreur si le format n'est pas supporté ou si le traitement échoue.

## Acceptance Criteria

```gherkin
Feature: Générateur de Posts à partir de Fichier/Lien

  Scenario: Génération et sauvegarde réussie d'un brouillon à partir d'un fichier
    Given je suis connecté à Linked-Pedia
    When je navigue vers la section "Créer un Post" et choisis "À partir de Fichier/Lien"
    And je télécharge un fichier supporté (ex: PDF)
    Then l'application affiche un indicateur de progression/chargement
    And l'application traite le fichier et appelle le service IA
    And un brouillon de post pertinent basé sur le contenu du fichier est généré
    And le brouillon est affiché dans une zone d'édition
    When je clique sur le bouton "Sauvegarder le brouillon"
    Then le brouillon est sauvegardé dans mon tableau de bord

  Scenario: Génération et sauvegarde réussie d'un brouillon à partir d'un lien
    Given je suis connecté à Linked-Pedia
    When je navigue vers la section "Créer un Post" et choisis "À partir de Fichier/Lien"
    And je colle un lien supporté (ex: URL d'article)
    And je clique sur un bouton "Générer à partir du lien" (ou action similaire)
    Then l'application affiche un indicateur de progression/chargement
    And l'application traite le lien et appelle le service IA
    And un brouillon de post pertinent basé sur le contenu du lien est généré
    And le brouillon est affiché dans une zone d'édition
    When je clique sur le bouton "Sauvegarder le brouillon"
    Then le brouillon est sauvegardé dans mon tableau de bord

  Scenario: Affichage d'un message d'erreur pour un format non supporté
    Given je suis connecté à Linked-Pedia
    When je navigue vers la section "Créer un Post" et choisis "À partir de Fichier/Lien"
    And je télécharge un fichier non supporté (ex: .exe)
    Then l'application affiche un message d'erreur clair indiquant que le format n'est pas supporté

  Scenario: Affichage d'un message d'erreur si le traitement échoue
    Given je suis connecté à Linked-Pedia
    And le traitement du fichier/lien ou l'appel IA échoue
    When je navigue vers la section "Créer un Post" et choisis "À partir de Fichier/Lien"
    And je soumets un fichier/lien
    Then l'application affiche un indicateur de progression/chargement
    And le traitement échoue
    And l'application affiche un message d'erreur clair à l'utilisateur
```

## Security Considerations

- Validation stricte des types de fichiers et des liens soumis pour prévenir les vulnérabilités (ex: injection via fichier, SSRF via lien).
- Stockage sécurisé et temporaire des fichiers téléchargés (Supabase Storage avec politiques d'accès appropriées).
- Analyse antivirus potentielle des fichiers téléchargés (hors scope MVP, mais à considérer).
- Sécuriser l'appel à l'Edge Function `/generate/file`.

## Performance Considerations

- Le traitement de fichier/lien et la génération IA peuvent prendre du temps. Afficher un indicateur de progression détaillé est important pour l'expérience utilisateur.
- Limiter la taille des fichiers et potentiellement la longueur des pages web à scraper.

## Accessibility Requirements (Integral Part of Quality UX)

- Le composant de téléchargement de fichier et le champ de saisie de lien sont accessibles au clavier et utilisables avec un lecteur d'écran.
- L'indicateur de progression/chargement est accessible et son état est annoncé aux technologies d'assistance.
- Les messages d'erreur sont clairs et annoncés.
- La zone de texte éditable est accessible.
- _Accessibility should not be an afterthought but integrated from the design phase to ensure an inclusive and high-quality user experience for all._

## Internationalization/Localization

Aucune exigence spécifique pour le MVP. Les chaînes de caractères seront codées en dur en français.

## Rollout Plan

- **Phased Rollout:** No
- **Feature Flag:** No
- **Rollback Plan:** En cas de bug critique, rollback vers la version précédente stable.

## Documentation Requirements

- Documentation TSDoc pour l'Edge Function `/generate/file` et les composants frontend associés.
- Documentation de l'endpoint API `/generate/file`.
- Documentation des types de fichiers et de liens supportés.
- Documentation du service/librairie utilisé pour l'extraction de contenu.

## Open Questions

- Quels types de fichiers (PDF, DOCX, TXT, images, etc.) et de liens (articles, pages web) seront supportés précisément dans le MVP ?
- Quel service/librairie sera utilisé pour l'extraction de contenu à partir de ces formats ?
- Quel service IA externe ou MCP sera utilisé pour la génération de texte à partir du contenu extrait ?
- Comment gérer les contenus très longs extraits de fichiers/liens pour les adapter aux limites de longueur des posts LinkedIn ?

---

_Last Updated: 22/05/2025_
