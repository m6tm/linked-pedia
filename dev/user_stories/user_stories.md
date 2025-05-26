# User Stories - Linked-Pedia

Ce document rassemble les User Stories clés pour l'application Linked-Pedia, extraites des spécifications de fonctionnalités (FEAT-XXX). Elles décrivent les besoins et les objectifs des utilisateurs du point de vue de l'utilisateur.

## 1. User Stories par Fonctionnalité (MVP)

### FEAT-001: Inscription et Connexion

- As a new user, I want to create an account so that I can access Linked-Pedia's features. (Must Have)
- As a returning user, I want to log in securely so that I can continue managing my LinkedIn presence. (Must Have)

### FEAT-002: Liaison Compte LinkedIn

- As a user, I want to connect my LinkedIn profile so that Linked-Pedia can access my data and publish on my behalf. (Must Have)
- As a user, I want to know if my LinkedIn account is successfully linked so that I can use the features that require it. (Must Have)
- As a user, I want to be able to disconnect my LinkedIn account from Linked-Pedia. (Should Have)

### FEAT-003: Audit de Profil LinkedIn

- As a user, I want to get an audit of my LinkedIn profile so that I understand how to optimize it. (Must Have)
- As a user, I want to see personalized recommendations based on my SSI so that I can improve my profile's effectiveness. (Must Have)
- As a user, I want to see the evolution of my profile score over time so that I can track my optimization efforts. (Should Have - pour futures itérations, mais la structure de données doit le permettre)

### FEAT-004: Générateur de Posts Texte

- As a user, I want to generate post drafts based on themes so that I can quickly start writing. (Must Have)
- As a user, I want to provide keywords or topics so that the generated posts are relevant to my needs. (Must Have)
- As a user, I want to be able to choose the style or tone of the generated post (e.g., informative,Brouillon, inspirant). (Should Have)

### FEAT-005: Générateur de Posts à partir de Fichier

- As a user, I want to generate a post from a file (image, doc) so that I can easily share insights from documents or images. (Should Have)
- As a user, I want to generate a post from a link (article, web page) so that I can quickly share and comment on external content. (Should Have)
- As a user, I want the generated post to capture the main points of the file or link so that it's a relevant starting point. (Should Have)

### FEAT-006: Générateur d'Idées de Posts

- As a user, I want to get new post ideas based on a theme so that I never run out of content inspiration. (Must Have)
- As a user, I want the ideas to cover different angles or formats so that I can vary my content. (Must Have)
- As a user, I want to be able to generate more ideas if the initial ones are not suitable. (Should Have)

### FEAT-007: Optimiseur de Posts

- As a user, I want to submit a post draft and get optimized suggestions so that I can improve its quality. (Must Have)
- As a user, I want to receive multiple optimization suggestions so that I can choose the best option. (Must Have)
- As a user, I want the suggestions to focus on improving engagement and relevance for LinkedIn. (Must Have)
- As a user, I want to easily apply an optimization suggestion to my draft. (Should Have)

### FEAT-008: Générateur d'Accroches

- As a user, I want to generate catchy hooks for my posts so that I increase initial engagement. (Must Have)
- As a user, I want the hooks to be relevant to my post content. (Must Have)
- As a user, I want the hooks to be based on current LinkedIn trends or best practices. (Must Have)
- As a user, I want to easily insert a generated hook into my post draft. (Should Have)

### FEAT-009: Tableau de Bord des Posts

- As a user, I want to see all my posts (drafts, scheduled, published) in one place so that I can manage them easily. (Must Have)
- As a user, I want to filter or sort my posts by status (draft, scheduled, published) so that I can quickly find what I need. (Must Have)
- As a user, I want to see key information about each post (content preview, status, date) in the list. (Must Have)
- As a user, I want to be able to perform actions on posts directly from the dashboard (edit draft, view published, delete). (Must Have)

### FEAT-010: Programmation de Posts

- As a user, I want to schedule my posts to be published later so that I can plan my content calendar. (Must Have)
- As a user, I want to specify the exact date and time for my post to be published. (Must Have)
- As a user, I want to see my scheduled posts in the dashboard so that I can manage them. (Must Have - lié à FEAT-009)
- As a user, I want to be able to edit or cancel a scheduled post before it's published. (Must Have - lié à FEAT-009)

### FEAT-011: Statistiques de Performance

- As a user, I want to see the performance metrics of my published posts so that I understand what works. (Must Have)
- As a user, I want to see key metrics like impressions, likes, comments, and shares for each post. (Must Have)
- As a user, I want to see the engagement rate for each post. (Must Have)
- As a user, I want to see the statistics for a specific post from the dashboard. (Must Have - lié à FEAT-009)

### FEAT-012: Conseils Personnalisés

- As a user, I want to receive personalized tips based on my performance so that I can improve my strategy. (Must Have)
- As a user, I want the tips to be actionable and relevant to my goals. (Must Have)
- As a user, I want to see tips related to my post performance (what worked, what didn't) and profile optimization. (Must Have)
- As a user, I want to see tips related to personal branding and conversion. (Should Have)

### FEAT-013: Gestion des Sous-Comptes

- As a ghostwriter, I want to manage multiple LinkedIn profiles so that I can handle content for my clients efficiently. (Should Have)
- As a ghostwriter, I want to link a client's LinkedIn account to my Linked-Pedia account. (Should Have)
- As a ghostwriter, I want to switch between managing different client accounts easily. (Should Have)
- As a ghostwriter, I want to see content and analytics specific to the client account I am currently managing. (Should Have)
- As a ghostwriter, I want to unlink a client's account. (Should Have)

### FEAT-014: Mode Assistant

- As a new user, I want a step-by-step guide so that I can easily learn how to use the application. (Should Have)
- As a beginner, I want guidance on how to optimize my LinkedIn profile and create my first post. (Should Have)
- As a user, I want to be able to exit the assistant mode if I feel comfortable using the application. (Should Have)

### FEAT-015: Gestion du Profil Utilisateur

- As a user, I want to provide information about myself and my goals so that the AI suggestions are tailored to me. (Must Have)
- As a user, I want to specify my activity domain so that the content ideas are relevant. (Must Have)
- As a user, I want to set my LinkedIn objectives (e.g., personal branding, lead generation) so that the advice aligns with them. (Must Have)
- As a user, I want to update my profile information as needed. (Must Have)

## 2. User Stories par Persona

### Persona : Le Professionnel Ambitieux (Sarah Dubois)

- As a professional, I want to quickly generate relevant post ideas so that I save time on content creation.
- As a professional, I want to optimize my post drafts to increase engagement.
- As a professional, I want to see the performance of my posts so that I understand what resonates with my audience.
- As a professional, I want personalized tips to improve my LinkedIn strategy.
- As a professional, I want to understand how to optimize my profile to attract opportunities.
- As a professional, I want to manage my publishing schedule easily from a central dashboard.

### Persona : Le Ghostwriter/Agence (David Chen)

- As a ghostwriter, I want to link and manage multiple client accounts under my main account.
- As a ghostwriter, I want to easily switch between client accounts to manage their content.
- As a ghostwriter, I want to generate, optimize, and schedule posts for each client account.
- As a ghostwriter, I want to see the performance statistics and audit results specific to each client account.
- As a ghostwriter, I want a clear overview of all scheduled posts across all managed accounts (via the dashboard).

### Persona : L'Étudiant/Jeune Diplômé

- As a young professional, I want a step-by-step guide to learn how to use Linked-Pedia and optimize my LinkedIn presence. (Mode Assistant)
- As a young professional, I want to optimize my profile for job searching. (Audit de Profil)
- As a young professional, I want easy ways to create my first professional posts. (Générateurs de Posts)
