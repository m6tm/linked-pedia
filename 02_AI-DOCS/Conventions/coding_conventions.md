# Coding Conventions and Style Guide - Link-Pedia

## Overview

This document outlines the coding conventions and style guidelines for the Link-Pedia project. Following these conventions ensures consistency, readability, and maintainability across the codebase. It should be used in conjunction with the [**Design Conventions and Style Guide**](design_conventions.md:1) which details the UX/UI principles and visual standards.

## General Principles

- **Readability First:** Code should be written for humans to read and understand. Prioriser la clarté sur la concision excessive.
- **Consistency:** Follow established patterns throughout the codebase. Utiliser les linters et formatteurs pour assurer la cohérence automatique.
- **Simplicity:** Prefer simple, straightforward solutions over complex ones. Éviter la sur-ingénierie.
- **Documentation:** Document code where necessary, especially non-obvious behavior, APIs, et composants UI réutilisables.

## Naming Conventions

### Variables

- Use **camelCase** for variables and function names.
- Use descriptive names that indicate purpose.
- Avoid abbreviations unless widely understood (ex: `userId`, `API_URL`).
- Boolean variables should start with `is`, `has`, `should`, etc.

```javascript
// Good
const userProfile = {};
const isActive = true;
const hasPermission = checkPermissions();
const isLoadingData = false;

// Bad
const up = {};
const active = true;
const perm = checkPermissions();
const loading = false;
```

### Functions

- Use **camelCase** for function names.
- Use verbs for function names to indicate actions (ex: `get`, `set`, `handle`, `calculate`, `render`).
- Be specific about what the function does.

```javascript
function getUserProfile() {
  /* ... */
}
function validateInput() {
  /* ... */
}
function calculateTotal() {
  /* ... */
}
async function fetchLinkedInPosts() {
  /* ... */
}
function handleFormSubmit() {
  /* ... */
}
```

### Classes

- Use **PascalCase** for class names (si des classes sont utilisées, bien que les composants fonctionnels React soient préférés).
- Use nouns for class names.

```javascript
class UserProfileService {
  /* ... */
}
class LinkedInApiAdapter {
  /* ... */
}
```

### Constants

- Use **UPPER_SNAKE_CASE** for constants.
- Utiliser `const` pour les variables dont la valeur ne change jamais.

```javascript
const API_BASE_URL = "https://api.example.com";
const MAX_RETRY_ATTEMPTS = 3;
const DEFAULT_PAGE_SIZE = 10;
```

### Files and Directories

- Use **kebab-case** for file and directory names.
- Group related files in directories (ex: par fonctionnalité, par type de composant).
- Les composants React/Next.js doivent être dans des fichiers `PascalCase.tsx`.

```
components/
  user-profile/
    UserProfile.tsx
    user-profile.types.ts
    user-profile.test.tsx
lib/
  linkedin-api/
    linkedin-api.ts
    linkedin-api.types.ts
```

## Code Formatting

- Use 2 spaces for indentation.
- Maximum line length: 100 characters (pour une meilleure lisibilité sur des écrans larges).
- Use semicolons at the end of statements.
- Use single quotes for strings, sauf si la chaîne contient une apostrophe ou nécessite des template literals.
- Always use curly braces for control structures, even for single-line blocks.
- Ajouter une ligne vide entre les blocs logiques (déclarations de variables, boucles, conditions).

```javascript
// Good
if (condition) {
  doSomething();
}

for (let i = 0; i < count; i++) {
  processItem(i);
}

// Bad
if (condition) doSomething();
for (let i = 0; i < count; i++) processItem(i);
```

## Comments

- Use TSDoc comments for functions, classes, types, et composants React pour décrire leur objectif, leurs paramètres, leurs valeurs de retour et leurs props.
- Add comments for complex logic, workarounds, or non-obvious behavior.
- Keep comments up-to-date with code changes.
- Utiliser `// TODO:` pour marquer les tâches futures ou les sections incomplètes.

```typescript
/**
 * Calculates the total price including tax
 * @param price - The base price
 * @param taxRate - The tax rate as a decimal
 * @returns The total price including tax
 * @throws Error if price is negative
 */
function calculateTotalPrice(price: number, taxRate: number): number {
  // Handle edge cases
  if (price < 0) {
    throw new Error("Price cannot be negative");
  }

  return price * (1 + taxRate);
}

// TODO: Implement caching for this function
async function fetchUserData(userId: string) {
  /* ... */
}
```

## Component Structure (Next.js/React)

- One component per file (`PascalCase.tsx`).
- Utiliser des composants fonctionnels et des hooks React.
- Organiser la logique à l'intérieur du composant de manière logique : hooks, gestionnaires d'événements, logique de rendu, puis le JSX.
- Utiliser des props pour passer des données et des fonctions aux composants enfants. Définir les types de props avec TypeScript.

```typescript
import React, { useState, useEffect } from "react";

interface UserProfileProps {
  userId: string;
}

const UserProfile: React.FC<UserProfileProps> = ({ userId }) => {
  const [profile, setProfile] = useState(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    const loadProfile = async () => {
      try {
        const data = await fetchUserData(userId);
        setProfile(data);
      } catch (error) {
        console.error("Failed to load profile:", error);
      } finally {
        setIsLoading(false);
      }
    };

    loadProfile();
  }, [userId]); // Dependency array

  const handleEditClick = () => {
    // Handle edit action
  };

  if (isLoading) {
    return <div>Loading...</div>;
  }

  if (!profile) {
    return <div>Profile not found.</div>;
  }

  return (
    <div>
      <h1>{profile.name}</h1>
      {/* ... rest of the component */}
      <button onClick={handleEditClick}>Edit</button>
    </div>
  );
};

export default UserProfile;
```

## State Management

- Keep state as local as possible using `useState` and `useReducer`.
- Use React Context API or une librairie de gestion d'état (si nécessaire pour l'état global complexe, à évaluer) for shared state across components.
- Document the shape of state objects using TypeScript interfaces.

## Error Handling

- Use try/catch blocks for error-prone operations (appels API, opérations asynchrones).
- Provide meaningful error messages to the user via l'interface UI (notifications, messages d'erreur).
- Log errors with appropriate context (utilisateur, opération, détails de l'erreur) côté serveur (Edge Functions).
- Utiliser des Error Boundaries en React pour gérer les erreurs dans l'arbre des composants UI.

```typescript
try {
  const data = await fetchUserData(userId);
  processUserData(data);
} catch (error: any) {
  // Utiliser 'any' ou un type d'erreur plus spécifique si connu
  console.error(`Failed to fetch user data for ${userId}:`, error); // Log côté client pour le débuggage
  // Côté Edge Function, utiliser un logger serveur
  showErrorNotification("Could not load user data. Please try again.");
}
```

## Testing

- Write tests for all new features, composants UI réutilisables, et logique métier complexe (Edge Functions).
- Follow the AAA pattern (Arrange, Act, Assert).
- Test both success and failure cases, ainsi que les cas limites (edge cases).
- Utiliser Jest et React Testing Library pour les tests unitaires et d'intégration frontend.
- Utiliser un framework de test adapté aux Edge Functions Supabase pour le backend.
- L'Agent IA générera des tests basés sur les critères d'acceptation (Section 6.2 du PRD).

```typescript
describe("UserProfile component", () => {
  it("should display user name after loading", async () => {
    // Arrange
    const mockUser = { name: "Jane Doe" };
    // Mock the fetchUserData function
    jest.spyOn(global, "fetch").mockImplementation(() =>
      Promise.resolve({
        json: () => Promise.resolve(mockUser),
      } as Response)
    );

    // Act
    render(<UserProfile userId="123" />);

    // Assert
    // Wait for the loading state to disappear and the user name to appear
    await screen.findByText("Jane Doe");
    expect(screen.getByText("Jane Doe")).toBeInTheDocument();

    // Clean up the mock
    jest.restoreAllMocks();
  });

  // Add more test cases for loading state, error state, etc.
});
```

## Performance Considerations

- Memoize expensive calculations or components using `useMemo` et `React.memo`.
- Use pagination or infinite scrolling for large data sets (ex: liste de posts).
- Optimize renders in React components en évitant les rendus inutiles.
- Réduire la taille du bundle JavaScript.
- Optimiser les requêtes de base de données et les appels aux services externes.

## Accessibility

- Use semantic HTML elements (`<button>`, `<nav>`, `<article>`, etc.).
- Include proper ARIA attributes when sémantique HTML n'est pas suffisant (ex: pour les widgets complexes).
- Ensure keyboard navigation works for all interactive elements (`tabindex`, gestion du focus).
- Maintain sufficient color contrast as defined in the [**Design Conventions and Style Guide**](design_conventions.md:1).
- Fournir des textes alternatifs descriptifs pour toutes les images significatives.
- Tester l'accessibilité avec des outils automatisés (ex: Axe DevTools) et manuellement.
- Refer to the [**Design Conventions and Style Guide**](design_conventions.md:1) for detailed UX/UI principles and A11Y targets.

## Design and User Experience (UX/UI) Principles

The goal is to create user interfaces (UI) and user experiences (UX) that rival the best startups (e.g., Y Combinator standard), focusing on modernity, elegance, intuitiveness, and a "pixel-perfect" aesthetic. Adhérer strictement aux principes et spécifications définis dans le [**Design Conventions and Style Guide**](design_conventions.md:1).

### Design Philosophy

- **Modernity and Elegance:** Aim for clean, contemporary, and visually appealing designs.
- **Intuitive Simplicity:** Interfaces should be easy to understand and use, even for new users.
- **Consistency:** Maintain visual and functional consistency throughout the application.
- **User Feedback:** Provide clear and immediate feedback to user actions.
- **Perceived Performance:** Optimize so the application feels fast and responsive.

### Design System

- **Use of Tailwind CSS:**
  - Follow a centralized Tailwind CSS configuration ([`tailwind.config.js`](tailwind.config.js:1) to be created/modified if needed) to define the color palette, typography, breakpoints, etc.
  - Favor composing Tailwind utility classes to create reusable components.
  - Avoid excessive custom CSS styles; rely as much as possible on Tailwind's capabilities.
  - **Utiliser les tokens définis dans le Link-Pedia Design System (voir [Design Conventions](design_conventions.md:1)) pour toutes les décisions de style.**
- **UI Component Library (Optional but recommended):**
  - Utiliser Shadcn/UI comme base pour les composants, en les personnalisant entièrement pour correspondre au Design System Link-Pedia.
  - Documenter la personnalisation et l'utilisation des composants dans Storybook.
- **Color Palette:** Utiliser la palette de couleurs définie dans le [**Design Conventions and Style Guide**](design_conventions.md:1) (Bleu, Blanc, Neutres, Accent, Sémantiques).
- **Typography:** Utiliser la hiérarchie typographique définie dans le [**Design Conventions and Style Guide**](design_conventions.md:1).
- **Spacing and Grid (Layout):** Adopter le système d'espacement et utiliser les utilitaires de grille de Tailwind selon les principes définis dans le [**Design Conventions and Style Guide**](design_conventions.md:1).
- **Iconography:** Choisir et utiliser un set d'icônes cohérent (ex: Lucide React, basé sur Feather Icons, utilisé par Shadcn/ui).
- **Micro-interactions and Animations:** Implémenter des animations subtiles et fonctionnelles selon les principes définis dans le [**Design Conventions and Style Guide**](design_conventions.md:1).
- **Responsive Design:** Implémenter une conception entièrement réactive en utilisant les utilitaires de breakpoint de Tailwind.

### Accessibility (In-depth)

In addition to the points in the "Accessibility" section:

- **Full keyboard navigation:** All interactive elements must be accessible and usable via the keyboard.
- **High contrast by default:** Aim for contrasts higher than WCAG AA minimums where possible, for increased visual comfort.
- **Alternative texts for images:** Descriptive and relevant.
- **Rigorous semantic structure:** Use appropriate HTML tags for their meaning.
- **Se référer aux objectifs spécifiques d'accessibilité définis dans le [Design Conventions and Style Guide](design_conventions.md:1).**

### Tools and Processes

- **Mockups (if available):** Si des mockups sont fournis, s'y conformer fidèlement.
- **Iteration:** Design et développement sont itératifs. Être ouvert au feedback.

## Security Best Practices

- Sanitize user inputs on the server-side (Edge Functions) before processing or storing them.
- Use parameterized queries for database operations (géré nativement par le SDK Supabase ou les Edge Functions si utilisées correctement).
- Implement proper authentication and authorization checks on the backend (Edge Functions) for all opérations sensibles, en utilisant RLS dans la base de données.
- Follow the principle of least privilege lors de la définition des permissions d'accès aux données et aux ressources.
- Gérer les secrets (clés API, tokens) de manière sécurisée via les variables d'environnement de Supabase.
- Valider les données côté serveur même si une validation côté client est présente.

## Git Workflow

- Use descriptive branch names (ex: `feat/generate-linkedin-post`, `fix/profile-audit-error`, `chore/update-dependencies`).
- Write meaningful commit messages following Conventional Commits (ex: `feat(posts): add text generation`, `fix(auth): resolve login redirect bug`).
- Keep commits focused on a single logical change.
- Squash commits before merging les branches de fonctionnalités dans la branche principale (ex: `main` ou `develop`).
- Utiliser des Pull Requests (PRs) pour les revues de code.

## Code Review Checklist

- Does the code follow the conventions in this document?
- **Has the [Design Conventions and Style Guide](design_conventions.md:1) been completed and is it being followed?**
- Is the code well-tested? (Couverture de code suffisante, tests pertinents)
- Are there any security concerns? (Validation des entrées, gestion des secrets, autorisations)
- Is the code efficient and performant? (Requêtes optimisées, rendu efficace)
- **Are the design and UX/UI compliant with the principles defined in both convention documents (modernity, intuitiveness, consistency, aesthetics)?**
- **Is the Tailwind CSS implementation clean and consistent (see [Design Conventions](design_conventions.md:1))?**
- **Are the components responsive and do they display correctly on different screen sizes (see [Design Conventions](design_conventions.md:1))?**
- **Are interactions and animations smooth and do they add value (see [Design Conventions](design_conventions.md:1))?**
- Is the code accessible? (Vérifier les points spécifiques des sections UX/UI et Accessibilité des deux documents de conventions)
- Is the documentation sufficient? (TSDoc, commentaires, Storybook si applicable)
- Le code est-il facile à comprendre et à maintenir ?
- La logique métier est-elle correctement implémentée selon le PRD et le document de logique métier ?

---

_This document, along with the [**Design Conventions and Style Guide**](design_conventions.md:1), should be reviewed and updated regularly as the project evolves._
