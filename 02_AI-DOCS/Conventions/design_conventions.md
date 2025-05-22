# Design Conventions and Style Guide - Linked-Pedia

## Overview

This document outlines the design conventions and style guidelines for the Linked-Pedia project, aiming for a professional-grade User Experience (UX) and User Interface (UI) that aligns with modern best practices, such as those seen in successful Y Combinator startups. Following these conventions ensures consistency, intuitiveness, elegance, and a "pixel-perfect" aesthetic across the application.

This document should be completed based on the project's specific needs and after reviewing the insights from "[`AI_Design_Agent_Optimization.md`](../Documentation/AI_Design_Agent_Optimization.md:1)".

## I. Core Principles of Excellent UI/UX Design

_(Refer to Section II of [`AI_Design_Agent_Optimization.md`](../Documentation/AI_Design_Agent_Optimization.md#ii-core-principles-of-excellent-uiux-design-in-2025) for detailed explanations)_

- **Clarity and Simplicity:**
  - _Project Specifics:_ L'interface doit être épurée, avec un minimum d'éléments distrayants. Utiliser des libellés clairs et concis. Les parcours utilisateurs doivent être directs et sans ambiguïté. Utiliser l'espace blanc généreusement pour séparer les éléments et améliorer la lisibilité.
- **Consistency:**
  - _Project Specifics:_ Maintenir une cohérence visuelle et interactive rigoureuse. Les styles des boutons, des champs de formulaire, de la typographie, des icônes et des éléments de navigation doivent être uniformes à travers toute l'application. Utiliser le Design System Linked-Pedia comme source unique de vérité pour les styles.
- **User Control and Freedom/Predictability:**
  - _Project Specifics:_ Fournir des mécanismes clairs pour annuler les actions (ex: annuler la modification d'un brouillon). Assurer des chemins de sortie évidents des modales ou des flux. Utiliser des dialogues de confirmation pour les actions destructrices (ex: supprimer un post). Les interactions doivent être prévisibles en se basant sur les conventions UI standard.
- **Accessibility and Inclusivity (WCAG POUR Principles):**
  - _Project Specifics:_ Cibler la conformité WCAG 2.1 AA au minimum. Assurer une navigation complète au clavier. Utiliser correctement les attributs ARIA. Fournir des textes alternatifs pour les images. Tester l'accessibilité avec des outils automatisés et manuellement.
- **Visual Hierarchy:**
  - _Project Specifics:_ Établir une hiérarchie visuelle claire en utilisant l'échelle typographique définie, les couleurs pour mettre l'accent sur les éléments importants (ex: appels à l'action, messages d'erreur), et un espacement cohérent pour grouper les éléments liés. Les titres et sous-titres doivent guider l'utilisateur à travers le contenu.
- **Feedback and Error Prevention:**
  - _Project Specifics:_ Fournir un feedback immédiat et clair pour toutes les actions utilisateur (états de chargement, messages de succès, messages d'erreur). Les messages d'erreur doivent être explicites et aider l'utilisateur à comprendre comment résoudre le problème. Valider les entrées utilisateur autant que possible côté client pour prévenir les erreurs avant soumission.
- **Efficiency:**
  - _Project Specifics:_ Optimiser les flux utilisateurs clés (génération de post, programmation) pour minimiser le nombre d'étapes. Permettre la réutilisation de contenu (ex: dupliquer un brouillon). Assurer des temps de chargement rapides pour les pages et les données.
- **Aesthetic & Functional Balance:**
  - _Project Specifics:_ L'esthétique épurée et professionnelle (couleurs bleu/blanc, typographie moderne) doit soutenir la fonctionnalité en rendant l'interface agréable à utiliser et en facilitant la concentration sur les tâches de création et d'optimisation de contenu. Le design ne doit jamais compromettre l'utilisabilité.

### Information Architecture (IA)

_(Refer to Section II, "Information Architecture (IA) as a Foundation" in [`AI_Design_Agent_Optimization.md`](../Documentation/AI_Design_Agent_Optimization.md#information-architecture-ia-as-a-foundation) for detailed explanations)_

- **Principle of Objects:**
  - _Project Specifics:_ Les objets clés sont les "Posts" (avec leurs différents statuts : brouillon, programmé, publié), les "Comptes LinkedIn" (principal et sous-comptes), les "Audits de Profil", les "Statistiques", et les "Suggestions IA". Chaque objet doit avoir une représentation claire et cohérente dans l'interface.
- **Principle of Choices:**
  - _Project Specifics:_ Limiter le nombre d'options présentées à l'utilisateur à chaque étape, en particulier dans les flux de création de post. Utiliser des menus déroulants ou des sélecteurs clairs pour les choix (ex: type de post, catégorie).
- **Principle of Disclosure:**
  - _Project Specifics:_ Utiliser la divulgation progressive pour les options avancées ou les détails moins importants (ex: afficher les statistiques détaillées d'un post uniquement sur demande, masquer les champs optionnels dans les formulaires de création de post).
- **Principle of Exemplars:**
  - _Project Specifics:_ Utiliser des icônes claires et reconnaissables pour représenter les actions ou les types de contenu. Potentiellement, montrer des exemples de posts réussis ou de recommandations d'audit pour illustrer les concepts.
- **Principle of Front Doors:**
  - _Project Specifics:_ La page d'accueil/tableau de bord doit fournir une vue d'ensemble claire de l'activité de l'utilisateur (posts récents, statistiques clés, état de l'audit de profil) et des points d'entrée évidents vers les fonctionnalités principales (créer un post, voir les statistiques, lancer un audit).
- **Principle of Multiple Classification:**
  - _Project Specifics:_ Permettre aux utilisateurs de retrouver leurs posts via différentes méthodes : liste chronologique, filtrage par statut (brouillon, programmé, publié), potentiellement par catégorie ou axe (si ces métadonnées sont implémentées).
- **Principle of Navigation:**
  - _Project Specifics:_ Utiliser une barre de navigation principale claire (latérale ou supérieure) pour accéder aux sections majeures (Tableau de Bord, Créer un Post, Audit de Profil, Statistiques, Paramètres). Utiliser des fils d'Ariane ou des titres de page clairs pour indiquer la position actuelle de l'utilisateur.
- **Principle of Growth:**
  - _Project Specifics:_ L'architecture de l'information doit être suffisamment flexible pour accueillir de nouvelles fonctionnalités (ex: analyse concurrentielle, intégrations supplémentaires) sans perturber la structure existante. Les nouvelles sections ou objets seront intégrés logiquement dans la navigation et les vues existantes.

## II. Design System & Styling (Leveraging Tailwind CSS)

_(Refer to Section III of [`AI_Design_Agent_Optimization.md`](../Documentation/AI_Design_Agent_Optimization.md#iii-mastering-tailwind-css-for-professional-ui-development) for detailed explanations)_

- **Tailwind CSS Configuration (`tailwind.config.js`):**
  - **Colors:**
    - Primary: `#0077B5` (Bleu LinkedIn ou similaire)
    - Secondary: `#FFFFFF` (Blanc)
    - Accent: `#00A0DC` (Un bleu plus clair ou une autre couleur vive professionnelle, ex: `#5E93B9` pour un bleu-gris)
    - Neutral Palette (grays, whites, blacks): `#1A1A1A` (Noir très sombre pour texte principal), `#333333`, `#666666`, `#CCCCCC`, `#EEEEEE`, `#F8F8F8`, `#FFFFFF`.
    - Semantic Colors (success, error, warning, info): Vert, Rouge, Orange, Bleu standard pour les messages d'état.
  - **Typography:**
    - Font Families (headings, body): `Inter`, `Roboto`, ou `Open Sans` (sans-serif moderne et lisible). Fallback: `sans-serif`.
    - Font Sizes (scale for h1-h6, p, small, etc.): Utiliser l'échelle par défaut de Tailwind (`text-xs` à `text-6xl`) en l'adaptant si nécessaire pour assurer une hiérarchie claire et une bonne lisibilité.
    - Font Weights: Regular (400), Medium (500), Semi-bold (600), Bold (700).
    - Line Heights: Standard Tailwind line heights (`leading-none` à `leading-loose`). Utiliser `leading-normal` ou `leading-relaxed` pour le corps du texte.
  - **Spacing Scale:** Utiliser une échelle basée sur 4px ou 8px (l'échelle par défaut de Tailwind est basée sur 4px, ce qui convient). Utiliser les classes utilitaires de Tailwind (`m-`, `p-`, `space-`).
  - **Breakpoints:** Utiliser les breakpoints par défaut de Tailwind (`sm`, `md`, `lg`, `xl`, `2xl`).
  - **Border Radii:** Utiliser l'échelle par défaut de Tailwind (`rounded-sm` à `rounded-full`). Préférer des coins légèrement arrondis pour un look moderne et doux.
  - **Shadows:** Utiliser l'échelle par défaut de Tailwind (`shadow-sm` à `shadow-2xl`, `shadow-inner`). Préférer des ombres subtiles pour la profondeur.
  - **Other Design Tokens:** Définir des tokens pour les bordures, les opacités, les transitions si nécessaire.
- **Component Architecture:**
  - _Project Specifics:_ Développer une bibliothèque de composants UI réutilisables en utilisant React et TypeScript, stylisés avec Tailwind CSS et personnalisés à partir de Shadcn/ui. Liste initiale des composants clés : `Button`, `Input`, `Checkbox`, `RadioGroup`, `Switch`, `Select`, `Card`, `Modal`/`Dialog`, `Table`, `Navigation`, `Alert`/`Toast`, `Spinner`, `Tabs`, `Form`, `Tooltip`, `Avatar`.
  - _Strategy for Abstraction:_ Encapsuler les groupes de classes utilitaires Tailwind dans des composants React pour éviter la répétition et améliorer la maintenabilité. Utiliser des props pour gérer les variantes et les états.
- **Global CSS (`global.css` or equivalent):**
  - **Base Styles:** Inclure un reset CSS (ex: basé sur Normalize.css ou un reset Tailwind minimal) et définir `box-sizing: border-box;`.
  - **Default HTML Element Styling:** Appliquer des styles de base aux éléments HTML bruts (`body`, `h1`-`h6`, `p`, `a`, `ul`, `ol`) en utilisant `@layer base` et `@apply` avec les tokens Tailwind définis.
  - **Custom Font Loading (`@font-face`):** Configurer le chargement des polices personnalisées si elles ne sont pas chargées via un CDN (ex: Google Fonts).
- **Theming (e.g., Light/Dark Mode):**
  - _Project Specifics:_ Implémenter un thème clair par défaut. Un thème sombre pourra être envisagé dans le futur. Utiliser les variables CSS et le modificateur `dark:` de Tailwind si un thème sombre est ajouté.

## III. UI Polish and Refinement

_(Refer to Section V of [`AI_Design_Agent_Optimization.md`](../Documentation/AI_Design_Agent_Optimization.md#v-achieving-beautiful-and-pro-techniques-for-ui-polish) for detailed explanations)_

- **Micro-interactions:**
  - _Project Specifics:_ Ajouter des micro-interactions subtiles pour améliorer l'expérience utilisateur : feedback visuel au clic sur les boutons (légère animation, changement de couleur), indicateurs de chargement clairs pour les opérations asynchrones (spinners, barres de progression), animations douces pour l'apparition/disparition des notifications (toasts).
- **Visual Refinement:**
  - **Typography:** Assurer une application cohérente de l'échelle typographique. Utiliser des contrastes de poids et de taille pour créer une hiérarchie claire. S'assurer que le texte est lisible sur tous les arrière-plans.
  - **Color Application:** Utiliser la couleur primaire (bleu) pour les éléments interactifs principaux et la marque. Le blanc et les neutres pour les arrière-plans et le texte principal. La couleur accent pour les appels à l'action ou les éléments à mettre en évidence. Les couleurs sémantiques pour les messages d'état (succès, erreur).
  - **Spacing (White Space):** Utiliser l'espacement de manière intentionnelle pour grouper les éléments liés et séparer les blocs de contenu. Maintenir un espacement vertical et horizontal cohérent.
- **Animations and Transitions:**
  - _Project Specifics:_ Utiliser des transitions douces pour les changements d'état (ex: apparition d'une modale, transition entre les onglets). Les animations doivent être rapides (200-300ms) et utiliser des fonctions d'easing `ease-in-out`. Éviter les animations excessives ou clignotantes.
- **Iconography:**
  - _Chosen Icon Set:_ Utiliser Lucide React (ou un set similaire de style épuré et moderne).
  - _Usage Guidelines:_ Utiliser les icônes de manière cohérente pour représenter les actions ou les concepts. S'assurer qu'elles sont alignées avec le texte ou les éléments UI associés. Utiliser les couleurs du Design System pour les icônes.
- **Imagery and Illustrations:**
  - _Style Guidelines:_ Si des images ou illustrations sont utilisées (ex: pour l'onboarding, les états vides), elles doivent correspondre à l'esthétique épurée et professionnelle de l'application. Préférer des illustrations vectorielles.
  - _Optimization:_ Optimiser les images pour le web (formats modernes comme WebP, compression appropriée) pour réduire les temps de chargement.

## IV. Responsive Design

- **Approach:** Adopter une approche "Mobile-first". Concevoir d'abord pour les petits écrans, puis utiliser les utilitaires de breakpoint de Tailwind pour adapter la mise en page aux écrans plus grands.
- **Key Breakpoints and Adaptations:**
  - `sm` (640px) : Ajustements mineurs de la mise en page, taille de texte potentiellement légèrement augmentée.
  - `md` (768px) : Adaptation de la navigation (ex: barre latérale au lieu d'un menu hamburger), ajustements de la mise en page pour les tablettes.
  - `lg` (1024px) : Mise en page desktop standard.
  - `xl` (1280px), `2xl` (1536px) : Ajustements pour les grands écrans, augmentation de l'espacement ou du nombre de colonnes.
  - Chaque composant doit être conçu pour être réactif.
- **Testing Strategy:** Tester la réactivité sur les navigateurs en redimensionnant la fenêtre et en utilisant les outils de développement mobile. Tester sur des appareils réels si possible.

## V. Accessibility (Deep Dive)

_(Refer to Section II, "Accessibility and Inclusivity" and Section V, "Visual Refinement" (Color Contrast) in [`AI_Design_Agent_Optimization.md`](../Documentation/AI_Design_Agent_Optimization.md#accessibility-and-inclusivity) for broader context)_

- **Keyboard Navigation:**
  - _Project Specifics:_ S'assurer que tous les éléments interactifs (boutons, liens, champs de formulaire, éléments sélectionnables) sont accessibles via la touche Tab. L'ordre de tabulation doit être logique. Le style de l'état de focus doit être clairement visible.
- **ARIA Attributes:**
  - _Project Specifics:_ Utiliser les attributs ARIA (`role`, `aria-*`) pour améliorer la sémantique des éléments UI complexes (ex: modales, onglets, notifications dynamiques) pour les lecteurs d'écran.
- **Semantic HTML:**
  - _Project Specifics:_ Utiliser les balises HTML5 sémantiques appropriées (`<header>`, `<nav>`, `<main>`, `<aside>`, `<article>`, `<section>`, `<footer>`, `<button>`, `<form>`, `<input>`, `<label>`, `<table>`, `<thead>`, `<tbody>`, `<tr>`, `<th>`, `<td>`, etc.) pour structurer le contenu.
- **Color Contrast:**
  - _Project Specifics:_ Mandater un contraste de couleur minimum de WCAG 2.1 AA pour le texte normal et les éléments UI importants. Viser AAA pour le texte de grande taille. Utiliser des outils comme le Color Contrast Checker ou des plugins de navigateur pour vérifier les contrastes.
- **Alternative Text for Images:**
  - _Project Specifics:_ Fournir un texte alternatif (`alt` attribute) concis et descriptif pour toutes les images qui transmettent des informations. Les images purement décoratives peuvent avoir un `alt=""`.
- **Forms:**
  - _Project Specifics:_ Associer chaque champ de formulaire à un `<label>` en utilisant l'attribut `for`. Fournir des messages d'erreur de validation clairs et accessibles, liés au champ concerné. Utiliser les types d'input HTML appropriés (`email`, `password`, `url`, etc.).

## VI. Tools and Process

- **Design Tools (if applicable):** Si des outils de conception (Figma, etc.) sont utilisés, maintenir les fichiers de conception à jour et les lier au PRD ou aux spécifications de fonctionnalités. Utiliser des outils de collaboration pour le feedback sur le design.
- **AI-Assisted UI Development (Optional but Recommended):**
  - _(Refer to Section IV of [`AI_Design_Agent_Optimization.md`](../Documentation/AI_Design_Agent_Optimization.md#iv-ai-assisted-ui-development-the-startup-superpower) for detailed strategies)_
  - _Chosen AI Tools:_ L'Agent IA peut utiliser des outils comme Vercel v0, ou des assistants de code basés sur l'IA (Cursor, Copilot) pour accélérer la génération de code UI, en s'assurant que le code généré respecte ces conventions et le Design System.
  - _Prompting Strategy:_ Lors de l'utilisation d'outils IA, fournir des prompts clairs incluant des références à ce document, au `tailwind.config.js`, et aux spécifications de composants si elles existent (Storybook).
  - _Review and Refinement Process:_ Le code UI généré par l'IA doit être rigoureusement revu, testé (unitaires, intégration, accessibilité, réactivité) et affiné manuellement pour garantir qu'il répond aux standards de qualité élevés et s'intègre parfaitement au reste du codebase et au Design System.
- **Collaboration and Handoff:** Maintenir une communication étroite entre les rôles de conception et de développement (assumés par l'Agent IA et l'utilisateur). Utiliser Storybook comme point de référence pour les composants UI.
- **Iteration and Feedback:** Intégrer le feedback utilisateur et les résultats des tests d'acceptation dans les itérations de conception et de développement.

## VII. Design Review Checklist

- **Core Principles:**
  - Is the design clear, simple, and intuitive?
  - Is consistency maintained across all elements and interactions?
  - Does the user have adequate control and are outcomes predictable?
  - Is the design accessible (WCAG compliant, keyboard navigable, sufficient contrast, ARIA used correctly)?
  - Is there a clear visual hierarchy guiding the user?
  - Is feedback provided effectively and are errors prevented/handled gracefully?
  - Is the design efficient for task completion?
  - Is there a good balance between aesthetics and functionality?
- **Information Architecture:**
  - Is content organized logically?
  - Is navigation clear and intuitive?
  - Can users easily find what they need?
- **Design System & Styling (Tailwind CSS):**
  - Does the design adhere to the defined `tailwind.config.js` (colors, typography, spacing, etc.)?
  - Are components well-abstracted and reusable?
  - Is global CSS used appropriately and integrated correctly with Tailwind?
  - **Les tokens du Linked-Pedia Design System sont-ils utilisés de manière cohérente ?**
  - **Les composants Shadcn/ui sont-ils suffisamment personnalisés pour correspondre à l'esthétique Linked-Pedia ?**
- **UI Polish:**
  - Are micro-interactions enhancing the UX without being distracting?
  - Is typography applied consistently and legibly?
  - Is color used purposefully and effectively?
  - Is spacing (white space) managed well to improve clarity and organization?
  - Are animations/transitions smooth and meaningful?
  - Is iconography consistent and clear?
- **Responsive Design:**
  - Does the design adapt correctly to all specified breakpoints?
  - Is the experience consistent and usable across different screen sizes?
- **Overall Impression:**
  - Does the design feel modern, elegant, and "pixel-perfect"?
  - Does it align with the project's brand and objectives (professionnel, épuré, moderne) ?
  - Does it meet the standard of a "Real Senior design app" / "Silicon Valley / Y Combinator standard"?

---

_This document should be reviewed and updated regularly as the project evolves and specific design decisions are made._
