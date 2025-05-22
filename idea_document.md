# Project Idea Pre-Writing Template

_For initializing the PRD "Streamlined Agentic AI Workflow" with the Agentic Agent_

**Creation Date:** 22/05/2025
**Idea Author:** Utilisateur

## SECTION A: THE CORE IDEA

_Purpose: Capture the essence of the project for Sections 1.2 and 3.1 of the PRD_

### 1. Project Working Title

```
Linked-Pedia
```

**Example:** "Neighborhood Carpooling App"

### 2. The Idea in a Few Words (Pitch/Central Concept)

```
Une application web Next.js 15 pour aider les professionnels à optimiser leur présence sur LinkedIn grâce à des outils de création, d'édition, d'organisation et d'analyse de posts, ainsi qu'un audit de profil personnalisé.
```

**Example:** "A mobile application to facilitate spontaneous and local carpooling between neighbors for short trips (school, shopping, activities)."

### 3. Main Problem this Project Solves

```
Les professionnels ont du mal à créer du contenu LinkedIn pertinent et engageant de manière efficace, à organiser leur activité de publication et à comprendre comment optimiser leur profil pour atteindre leurs objectifs.
```

**Example:** "Difficulty organizing small shared trips without the complexity of national platforms, lack of trust for carpooling with strangers over short distances, waste of empty seats in cars for recurring neighborhood trips."

### 4. Proposed Solution (How the Project Solves the Problem)

```
Linked-Pedia offre une suite d'outils basés sur l'IA pour générer et optimiser des posts, organiser les publications, analyser les performances et fournir des recommandations personnalisées pour l'amélioration du profil et de la stratégie de contenu LinkedIn.
```

**Example:** "By creating a trust network based on locality, with a simple interface to offer/search for trips instantly, and verified profiles (optional) within a neighborhood community."

## SECTION B: KEY FEATURES (INITIAL MVP)

_Purpose: Feed Section 3.1 of the PRD_

### 1. Essential Feature #1

- **Name:** Générateur de posts LinkedIn
- **Description (what the user can do):** Générer des posts LinkedIn sur la base de différents axes, types de posts et catégories, ou à partir d'un fichier soumis (image, doc, lien).
- **Key Result for the User:** Créer rapidement des brouillons de posts pertinents.
- **Desired "Vibe"/Experience (Optional):** Efficace, intuitif.

**Example:**

- **Name:** Neighborhood Registration & Profile
- **Description:** Create an account, define your neighborhood of residence, add a photo.
- **Result:** Be identified and able to interact with neighbors.
- **Vibe:** Simple, quick, reassuring.

### 2. Essential Feature #2

- **Name:** Optimiseur de posts
- **Description:** Soumettre un post déjà écrit et obtenir 3 propositions d'améliorations optimisées pour la pertinence et l'accroche.
- **Key Result for the User:** Améliorer la qualité et l'impact de ses posts existants.
- **Desired "Vibe"/Experience (Optional):** Intelligent, utile.

### 3. Essential Feature #3 (and #4 if needed)

- **Name:** Générateur d'idées de posts
- **Description:** Générer des idées de posts à l'utilisateur sur la base d'un grand thème, couvrant différents axes.
- **Key Result for the User:** Ne jamais manquer d'inspiration pour de nouveaux contenus.
- **Desired "Vibe"/Experience (Optional):** Créatif, inspirant.

### 4. Essential Feature #4

- **Name:** Générateur d'accroches
- **Description:** Proposer des accroches LinkedIn ultra pertinentes basées sur les meilleures pratiques et les posts performants du moment, intégrant un système d'analyse des tendances en temps réel.
- **Key Result for the User:** Augmenter l'engagement initial sur ses posts.
- **Desired "Vibe"/Experience (Optional):** Pertinent, basé sur les données.

### 5. Essential Feature #5

- **Name:** Tableau de bord des posts
- **Description:** Une interface centralisée avec tous les posts de l'utilisateur (brouillon, Publié, Programmé, etc.) et la possibilité de programmer des posts directement sur LinkedIn.
- **Key Result for the User:** Gérer facilement son calendrier de publication.
- **Desired "Vibe"/Experience (Optional):** Organisé, pratique.

## SECTION C: INITIAL DESIGN & TECHNOLOGY PREFERENCES

_Purpose: Guide AI proposals for Sections 1.10, 5.1, 5.2, 5.4 of the PRD_

### 1. General "Vibe" and Desired Aesthetics

```
Professionnel, épuré et moderne. L'interface doit être intuitive et facile à naviguer. Pas d'inspiration spécifique pour l'instant, mais quelque chose de propre et fonctionnel.
```

**Example:** "I want something very simple, intuitive, with a modern and reassuring design. Soft colors. Inspiration: a mix between the 'Nextdoor' app for the local aspect and 'BlaBlaCar' for the simplicity of trip proposals."

### 2. Primary Target Audience (First Intuition)

```
Professionnels cherchant à développer leur marque personnelle ou leur activité sur LinkedIn, incluant potentiellement des ghostwriters gérant plusieurs comptes clients.
```

**Example:** "Residents of the same neighborhood or small town, especially families for school/activity trips, and people looking to save money or reduce their ecological footprint for local errands."

### 3. Technology Stack (If you have strong preferences or constraints)

The Agentic PRD suggests as a default: Next.js, Supabase, Tailwind CSS. However, the final technology choices can be adapted by the user.

```
Next.js 15 est une exigence. Pour le reste, la stack par défaut (Supabase, Tailwind CSS) semble appropriée, sauf si des besoins spécifiques pour l'intégration LinkedIn ou l'IA suggèrent d'autres outils.
```

**Example:** "The default stack works perfectly for me. Perhaps explore using [Library X] for mapping if needed."

### 4. Anticipated Third-Party Integrations / MCPs (If clear ideas already exist)

```
Intégration majeure avec l'API LinkedIn pour la publication, la programmation et l'analyse des posts, ainsi que l'audit de profil. Utilisation potentielle d'outils d'IA (via MCPs) pour la génération et l'optimisation de texte, l'analyse de tendances.
```

**Example:** "Probably a mapping service to visualize trips (e.g., Mapbox or Google Maps via MCP). Push notifications for new trip proposals."

## SECTION D: INITIAL QUESTIONS FOR YOURSELF (AND FOR ROO LATER)

_Purpose: Anticipate points to explore further_

### 1. What are the biggest risks or uncertainties for this project at this stage?

```
La complexité de l'intégration avec l'API LinkedIn et les limitations potentielles de celle-ci. La précision et la pertinence des suggestions générées par l'IA. L'adoption par les utilisateurs et la monétisation.
```

### 2. How could this project generate value (for users, for you/the company)?

```
Pour les utilisateurs : Gain de temps dans la création de contenu, amélioration de l'engagement et de la visibilité sur LinkedIn, croissance du réseau et des opportunités professionnelles. Pour l'entreprise : Revenus via un modèle d'abonnement, acquisition d'utilisateurs grâce à une proposition de valeur unique.
```

### 3. Are there any direct or indirect competitors that you already know of?

```
Des outils de gestion des réseaux sociaux (Hootsuite, Buffer), des générateurs de contenu basés sur l'IA (Jasper, Copy.ai), des services d'audit de profil LinkedIn. Cependant, une solution tout-en-un spécifiquement axée sur LinkedIn avec des fonctionnalités d'IA avancées semble moins courante.
```

### 4. On a scale of 1 to 10, how clear is this idea to you (1=very vague, 10=very clear)? Which aspects are the most unclear?

```
8/10. Les aspects les plus clairs sont les fonctionnalités principales. Les aspects les moins clairs concernent les détails techniques de l'intégration LinkedIn et la mise en œuvre précise des algorithmes d'IA pour l'analyse des tendances et l'optimisation personnalisée.
```

## Instructions for the Next Step

### Saving Your Output

Once you've completed this document:

1. Save it as `idea_document.md` in your project directory
2. This file will serve as the foundation for the next phase of the workflow

### Moving to Market Research

To proceed with market research:

1. Open the prompt file in `01_AI-RUN/` that corresponds to the `02_Market_Research.md` logical step. (Ensure `00_AutoPilot.md` or your manual process calls the correct actual filename for market research).
2. Share it with your AI agent.
3. When prompted for your idea, reference this completed `idea_document.md`.

```
@MarketMaster Pro

Please analyze the market potential for my project idea. You can find the complete idea document at: `idea_document.md`

The core concept is: Une application web Next.js 15 pour aider les professionnels à optimiser leur présence sur LinkedIn grâce à des outils de création, d'édition, d'organisation et d'analyse de posts, ainsi que l'audit de profil personnalisé.
```

### What to Expect Next

In the Market Research phase, the AI will:

1. Analyze your idea for market viability
2. Research competitors and market trends
3. Identify target user segments
4. Provide pricing and go-to-market strategies
5. Deliver a comprehensive market analysis report

This market validation is crucial before proceeding to refine your core concept in the next phase.
